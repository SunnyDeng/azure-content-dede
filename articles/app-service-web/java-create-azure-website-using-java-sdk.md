<properties 
	pageTitle="Erstellen einer Web-App in Azure App Service mithilfe des Azure SDK für Java"
	description="Erfahren Sie, wie Sie mithilfe des Azure SDK für Java programmgesteuert eine Web-App in Azure App Service erstellen."
	tags="azure-classic-portal"
	services="app-service\web"
	documentationCenter="Java"
	authors="donntrenton"
	manager="wpickett"
	editor="jimbe"/>

<tags 
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="v-donntr"/>


# Erstellen einer Web-App in Azure App Service mithilfe des Azure SDK für Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## Übersicht

In dieser exemplarischen Vorgehensweise wird gezeigt, wie Sie eine Azure SDK für Java-Anwendung erstellen, mit der eine Web-App in [Azure App Service][] erstellt wird. Anschließend stellen Sie eine Anwendung in der Web-App bereit. Die Vorgehensweise besteht aus zwei Teilen:

- Teil 1 veranschaulicht, wie Sie eine Java-Anwendung zur Web-App-Erstellung erstellen.
- Teil 2 zeigt, wie Sie eine einfache "Hello World"-JSP-Anwendung schreiben und anschließend mit einem FTP-Client Code in App Service bereitstellen.


## Voraussetzungen

### Softwareinstallationen

Der Anwendungscode für "AzureWebDemo" in diesem Artikel wurde mit dem Azure Java SDK 0.7.0 geschrieben, das Sie über den [Web Platform Installer][] (WebPI) installieren können. Stellen Sie außerdem sicher, dass Sie die aktuelle Version des [Azure-Toolkits für Eclipse][] verwenden. Nachdem Sie das SDK installiert haben, aktualisieren Sie die Abhängigkeiten in Ihrem Eclipse-Projekt, indem Sie in **Maven-Repositorys** die Option **Index aktualisieren** ausführen, anschließend im Fenster **Abhängigkeiten** die neueste Version von jedem Paket hinzufügen. Sie können die Version Ihrer installierten Software in Eclipse prüfen, indem Sie auf **Hilfe > Installationsdetails** klicken. Sie sollten mindestens über die folgenden Versionen verfügen:

- Paket für Microsoft Azure-Bibliotheken für Java 0.7.0.20150309
- Eclipse IDE für Java EE-Entwickler 4.4.2.20150219


### Erstellen und Konfigurieren von Cloudressourcen in Azure

Zur Durchführung dieser Anleitung müssen Sie über ein aktives Azure-Abonnement verfügen und ein standardmäßiges Active Directory-Verzeichnis (AD) in Azure eingerichtet haben.


### Erstellen eines Active Directory-Verzeichnisses in Azure

Wenn Sie noch kein Active Directory-Verzeichnis in Ihrem Azure-Abonnement eingerichtet haben, melden Sie sich mit Ihrem Microsoft-Konto beim [klassischen Azure-Portal][] an. Wenn Sie mehrere Abonnements besitzen, klicken Sie auf **Abonnements**, und wählen Sie das Standardverzeichnis für das Abonnement, das Sie für dieses Projekt verwenden möchten. Klicken Sie dann auf **Anwenden**, um dieses Abonnement anzuzeigen.

1. Wählen Sie im Menü links **Active Directory** aus. Klicken Sie auf **Neu > Verzeichnis > Benutzerdefiniert erstellen**.

2. Wählen Sie in **Verzeichnis hinzufügen** die Option **Neues Verzeichnis erstellen**.

3. Geben Sie **Name** einen Verzeichnisnamen ein.

4. Geben Sie in **Domäne** einen Domänennamen ein. Dies ist ein grundlegender Domänenname, der standardmäßig Ihr Verzeichnis umfasst. Er hat die Form `<domain_name>.onmicrosoft.com`. Sie können den Namen basierend auf dem Verzeichnisnamen oder einer anderen Domäne wählen, die Sie besitzen. Später können Sie einen weiteren Domänennamen hinzufügen, der bereits in Ihrer Organisation verwendet wird.

5. Wählen Sie in **Land oder Region** Ihr Gebietsschema aus.

Weitere Informationen zu Active Directory (AD) finden Sie unter [Was ist ein Azure AD-Verzeichnis?][].


### Erstellen eines Verwaltungszertifikats für Azure

Das Azure SDK für Java verwendet Verwaltungszertifikate für die Authentifizierung von Azure-Abonnements. Es handelt sich hierbei um X.509 v3-Zertifikate, die Sie zum Authentifizieren einer Clientanwendung nutzen, die die Dienstverwaltungs-API zur Verwaltung von Abonnementressourcen im Namen des Abonnementbesitzers verwendet.

Der Code in dieser Vorgehensweise verwendet ein selbstsigniertes Zertifikat zur Authentifizierung bei Azure. Für diese Vorgehensweise müssen Sie ein Zertifikat erstellen und dieses vorab in das [klassische Azure-Portal][] hochladen. Dieser Vorgang umfasst die folgenden Schritte:

- Generieren Sie eine PFX-Datei, das Ihr Clientzertifikat repräsentiert, und speichern Sie die Datei lokal.
- Generieren Sie ein Verwaltungszertifikat (CER-Datei) aus der PFX-Datei.
- Laden Sie die CER-Datei in Ihr Azure-Abonnement hoch.
- Konvertieren Sie die PFX-Datei in eine JKS-Datei, da Java dieses Format für die Zertifikatauthentifizierung verwendet.
- Schreiben Sie den Anwendungscode, der auf die lokale JKS-Datei verweist.

Wenn Sie diese Schritte ausgeführt haben, liegt das CER-Zertifikat in Ihrem Azure-Abonnement vor, und das JKS-Zertifikat befindet sich auf Ihrem lokalen Laufwerk. Weitere Informationen über Verwaltungszertifikate finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure][].


#### Erstellen eines Zertifikats

Um ein eigenes selbstsigniertes Zertifikat zu erstellen, öffnen Sie eine Befehlszeile auf Ihrem Betriebssystem, und führen Sie die folgenden Befehle aus.

> **Hinweis:** Auf dem Computer, auf dem Sie die Befehle ausführen, muss das JDK installiert sein. Darüber hinaus richtet sich der keytool-Pfad danach, wo Sie das JDK installiert haben. Weitere Informationen finden Sie unter[Schlüssel- und Zertifikatverwaltungstool (keytool)][] in der Java-Onlinedokumentation.

Erstellen der PFX-Datei:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Erstellen der CER-Datei:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

Hierbei gilt:

- `<java-install-dir>` ist der Pfad zu dem Verzeichnis, in dem Sie Java installiert haben.
- `<keystore-id>` ist der Eintragsbezeichner im Schlüsselspeicher (z. B. `AzureRemoteAccess`).
- `<cert-store-dir>` ist der Pfad zu dem Verzeichnis, in dem Sie die Zertifikate speichern möchten (z. B. `C:/Certificates`).
- `<cert-file-name>` ist der Name der Zertifikatdatei (z. B. `AzureWebDemoCert`).
- `<password>` ist das Kennwort zum Schutz des Zertifikats. Das gewählte Kennwort muss mindestens 6 Zeichen umfassen. Sie können auch kein Kennwort verwenden, dies wird jedoch nicht empfohlen.
- `<dname>` ist der X.500 Distinguished Name, der mit Aliasen verwendet wird. Dieser wird in die Felder für Aussteller und Antragsteller im selbstsignierten Zertifikat eingetragen.

Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure][].


#### Hochladen des Zertifikats

Um ein selbstsigniertes Zertifikat in Azure hochzuladen, wechseln Sie im klassischen Portal zur Seite **Einstellungen** und klicken dann auf die Registerkarte **Verwaltungszertifikate**. Klicken Sie im unteren Bereich der Seite auf **Hochladen**, und navigieren Sie zum Speicherort der CER-Datei, die Sie erstellt haben.


#### Konvertieren der PFX-Datei in eine JKS-Datei

Wechseln Sie in der Windows-Eingabeaufforderung (ausgeführt als "admin") mit dem cd-Befehl zu dem Verzeichnis, das die Zertifikate enthält. Führen Sie anschließend den folgenden Befehl aus, wobei `<java-install-dir>` das Verzeichnis angibt, in dem Sie Java auf Ihrem Computer installiert haben.

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Geben Sie bei Aufforderung das Kennwort für den Zielschlüsselspeicher ein; hierbei handelt es sich um das Kennwort für die JKS-Datei.

2. Geben Sie bei Aufforderung das Kennwort für den Quellschlüsselspeicher ein. Dies ist das Kennwort, das Sie für die PFX-Datei angegeben haben.

Die zwei Kennwörter müssen nicht übereinstimmen. Sie können auch kein Kennwort verwenden, dies wird jedoch nicht empfohlen.


## Erstellen einer Anwendung für die Web-App-Erstellung

### Erstellen des Eclipse-Arbeitsbereichs und des Maven-Projekts

In diesem Abschnitt erstellen Sie einen Arbeitsbereich und ein Maven-Projekt für die Anwendung zur Web-App-Erstellung. Diese trägt den Namen "AzureWebDemo".

1. Erstellen Sie ein neues Maven-Projekt. Klicken Sie auf **Datei > Neu > Maven-Projekt**. Wählen Sie in **Neues Maven-Projekt** die Option **Einfaches Projekt erstellen** und **Standardspeicherort für Arbeitsbereich verwenden** aus.

2. Geben Sie auf der zweiten Seite von **Neues Maven-Projekt** die folgenden Informationen an:

    - Gruppen-ID: `com.<username>.azure.webdemo`
    - Artefakt-ID: AzureWebDemo
    - Version: 0.0.1-SNAPSHOT
    - Paketierung: jar
    - Name: AzureWebDemo

    Klicken Sie auf **Fertig stellen**.

3. Öffnen Sie die Datei "pom.xml" des neuen Projekts im Projekt-Explorer. Wählen Sie die Registerkarte **Abhängigkeiten**. Da es sich um ein neues Projekt handelt, werden noch keine Pakete aufgelistet.

4. Öffnen Sie die Ansicht der Maven-Repositorys. Klicken Sie auf **Fenster > Ansicht anzeigen > Andere > Maven > Maven-Repositorys**, und klicken Sie auf **OK**. Die Ansicht **Maven-Repositorys** wird im unteren Bereich der IDE angezeigt.

5. Öffnen Sie **Globale Repositorys**, klicken Sie mit der rechten Maustaste auf das Repository **central**, und wählen Sie **Index neu erstellen**.

    ![][1]
    
    Dieser Schritt kann einige Minuten in Anspruch nehmen, je nach Geschwindigkeit Ihrer Verbindung. Beim Neuerstellen des Index sollten die Microsoft Azure-Pakete im Maven-Repository **central** angezeigt werden.

6. Klicken Sie in **Abhängigkeiten** auf **Hinzufügen**. Geben Sie in **Gruppen-ID eingeben...** den Wert `azure-management` ein. Wählen Sie die Pakete für die grundlegende Verwaltung und die Verwaltung von App Service-Web-Apps.

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Hinweis:** Wenn Sie die Abhängigkeiten nach dem Veröffentlichen einer neuen Version aktualisieren, müssen Sie jede der Abhängigkeiten in dieser Liste neu hinzufügen. Nachdem Sie auf **Hinzufügen** geklickt und jede Abhängigkeit ausgewählt haben, werden diese mit der neuen Versionsnummer in der Liste **Abhängigkeiten** angezeigt.

Klicken Sie auf **OK**. Das Azure-Paket wird in der Liste **Abhängigkeiten** angezeigt.


### Schreiben von Java-Code zum Erstellen einer Web-App durch Aufrufen des Azure SDK

Als Nächstes schreiben Sie den Code, der APIs im Azure SDK für Java aufruft, um die App Service-Web-App zu erstellen.

1. Erstellen Sie eine Java-Klasse für den Code des Haupteinstiegspunkts. Klicken Sie im Projekt-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Neu > Klasse** aus:

2. Benennen Sie in **Neue Java-Klasse** die Klasse mit `WebCreator`, und aktivieren Sie das Kontrollkästchen **public static void main**. Die Auswahl sollte so aussehen:

    ![][2]

3. Klicken Sie auf **Fertig stellen**. Die Datei "WebCreator.js" wird im Projekt-Explorer angezeigt.


### Aufrufen der Azure-API zum Erstellen einer App Service-Web-App


#### Hinzufügen der erforderlichen Importe

Fügen Sie in "WebCreator.js" die folgenden Importe hinzu. Diese Importe stellen Zugriff auf Klassen in den Verwaltungsbibliotheken für die Nutzung von Azure-APIs bereit:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### Definieren der Hauptklasse für den Einstiegspunkt

Da der Zweck der Anwendung "AzureWebDemo" darin besteht, eine App Service-Web-App zu erstellen, benennen Sie die Hauptklasse für diese Anwendung mit `WebAppCreator`. Diese Klasse stellt den Code für den Haupteinstiegspunkt bereit, mit dem die Azure Service Management-API zum Erstellen einer Web-App aufgerufen wird.

Fügen Sie die folgenden Parameterdefinitionen für Web-App und Webspace hinzu. Sie müssen Ihre eigene Azure-Abonnement-ID und die Zertifikatinformationen angeben.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

Hierbei gilt:

- `<subscription-id>` ist die Azure-Abonnement-ID, in der Sie die Ressource erstellen möchten.
- `<certificate-store-path>` gibt Pfad und Dateiname der JKS-Datei in Ihrem lokalen Verzeichnis für den Zertifikatspeicher an. Dies ist beispielsweise `C:/Certificates/CertificateName.jks` für Linux und `C:\Certificates\CertificateName.jks` für Windows.
- `<certificate-password>` ist das Kennwort, das Sie beim Erstellen Ihres JKS-Zertifikats angegeben haben.
- `webAppName` kann ein beliebiger Name sein; in dieser Vorgehensweise wird `WebDemoWebApp` verwendet. Der Domänenname setzt sich aus `webAppName` und dem Suffix `domainName` zusammen, sodass der vollständige Domänenname `webdemowebapp.azurewebsites.net` lautet.
- `domainName` sollte wie oben gezeigt angegeben werden.
- `webSpaceName` sollte einer der Werte sein, die in der Klasse [WebSpaceNames][] definiert werden.
- `appServicePlanName` sollte wie oben gezeigt angegeben werden.

> **Hinweis:** Bei jeder Ausführung dieser Anwendung müssen Sie den Wert von `webAppName` und `appServicePlanName` ändern (oder die Web-App im Azure-Portal löschen), bevor Sie die Anwendung erneut ausführen können. Andernfalls tritt ein Fehler bei der Ausführung auf, da dieselbe Ressource bereits in Azure vorhanden ist.


#### Definieren der Methode für die Web-App-Erstellung

Als Nächstes definieren Sie eine Methode zum Erstellen der Web-App. Diese Methode, `createWebApp`, gibt die Parameter für Web-App und Webspace an. Es wird außerdem der Verwaltungsclient für die App Service-Web-Apps erstellt und konfiguriert, der durch das Objekt [WebSiteManagementClient][] definiert wird. Der Verwaltungsclient ist entscheidend für das Erstellen von Web-Apps. Er stellt RESTful-Webdienste bereit, die Anwendungen das Verwalten von Web-Apps ermöglichen (Ausführen von Vorgängen wie Erstellung, Aktualisierung und Löschung), indem die Dienstverwaltungs-API aufgerufen wird.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Der Code gibt den HTTP-Status der Antwort mit Hinweis auf Erfolg oder Fehler aus. Bei erfolgreicher Ausführung wird der Name der erstellten Web-App ausgegeben.


#### Definieren der main()-Methode

Stellen Sie den Code der main()-Methode bereit, die createWebApp() zum Erstellen der Web-App aufruft.

Rufen Sie schließlich `createWebApp` aus `main` auf:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### Ausführen der Anwendung und Überprüfen der Web-App-Erstellung

Klicken Sie auf **Ausführen > Ausführen**, um sicherzustellen, dass Ihre Anwendung ausgeführt wird. Nach Abschluss der Anwendungsausführung sollten Sie die folgende Ausgabe in der Eclipse-Konsole sehen:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Melden Sie sich beim klassischen Azure-Portal an, und klicken Sie auf **Web-Apps**. Die neue Web-App sollte innerhalb von wenigen Minuten in der Web-Apps-Liste angezeigt werden.


## Bereitstellen einer Anwendung für die Web-App

Nachdem Sie "AzureWebDemo" ausgeführt und die neue Web-App erstellt haben, melden Sie sich beim klassischen Portal an, klicken Sie auf **Web-Apps**, und wählen Sie in der Liste **Web-Apps** den Eintrag **WebAppDemo**. Klicken Sie im Web-App-Dashboard auf **Durchsuchen** (oder klicken Sie auf die URL, `webdemowebapp.azurewebsites.net`), um zur Web-App zu navigieren. Sie sehen eine leere Platzhalterseite, da in der Web-App noch keine Inhalte bereitgestellt wurden.

Als Nächstes erstellen Sie eine "Hello World"-Anwendung und stellen diese in der Web-App bereit.


### Erstellen einer JSP-Hello World-Anwendung

#### Erstellen der Anwendung

Um zu veranschaulichen, wie eine Anwendung im Web bereitgestellt wird, zeigt die folgende Vorgehensweise das Erstellen einer einfachen "Hello World"-Java-Anwendung und das Hochladen dieser Anwendung in die App Service-Web-App, die Ihre Anwendung erstellt hat.

1. Klicken Sie auf **Datei > Neu > Dynamisches Webprojekt**. Geben Sie dem Projekt den Namen `JSPHello`. Sie müssen keine der weiteren Einstellungen im Dialogfeld ändern. Klicken Sie auf **Fertig stellen**.

    ![][3]

2. Erweitern Sie im Projekt-Explorer das Projekt **JSPHello**, klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **Neu > JSP-Datei**. Geben Sie im Dialogfeld für die neue JSP-Datei den Namen `index.jsp` für die neue Datei ein. Klicken Sie auf **Weiter**.

3. Wählen Sie im Dialogfeld **JSP-Vorlage auswählen** die Option **Neue JSP-Datei (HTML)**, und klicken Sie auf **Fertig stellen**.

4. Fügen Sie in "index.jsp" den folgenden Code in die Tagabschnitte `<head>` und `<body>` ein:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### Ausführen der Hello World-Anwendung in localhost

Bevor Sie diese Anwendung ausführen, müssen Sie einige Eigenschaften konfigurieren.

1. Klicken Sie mit der rechten Maustaste auf das Projekt **JSPHello**, und wählen Sie **Eigenschaften** aus.

2. Im Dialogfeld **Eigenschaften**: Wählen Sie **Java-Buildpfad**, wählen Sie die Registerkarte **Reihenfolge und Export**, aktivieren Sie die Option **JRE-Systembibliothek**, und klicken Sie dann auf **Nach oben**, um den Eintrag an den Anfang der Liste zu verschieben.

    ![][4]

3. Ebenfalls im Dialogfeld **Eigenschaften**: Wählen Sie **Ziellaufzeiten**, und klicken Sie auf **Neu**.

4. Wählen Sie im Dialogfeld **Neue Serverlaufzeitumgebung** einen Server wie beispielsweise **Apache Tomcat v7.0** aus, und klicken Sie auf **Weiter**. Legen Sie im Dialogfeld **Tomcat-Server** den Wert für **Name** auf `Apache Tomcat v7.0` fest, und geben Sie als **Tomcat-Installationsverzeichnis** das Verzeichnis an, in dem Sie den zu verwendenden Tomcat-Server installiert haben.

    ![][5]

    Klicken Sie auf **Fertig stellen**.

5. Es wird wieder die Seite **Ziellaufzeiten** des Dialogfelds **Eigenschaften** angezeigt. Wählen Sie **Apache Tomcat v7.0**, und klicken Sie auf **OK**.

    ![][6]

6. Klicken Sie im Eclipse-Menü **Ausführen** auf **Ausführen**. Wählen Sie im Dialogfeld **Ausführen als** die Option **Auf Server ausführen**. Wählen Sie im Dialogfeld **Auf Server ausführen** die Option **Tomcat v7.0 Server**:

    ![][7]

    Klicken Sie auf **Fertig stellen**.

7. Beim Ausführen der Anwendung sollte die Seite **JSPHello** in einem localhost-Fenster in Eclipse (`http://localhost:8080/JSPHello/`) angezeigt werden, zusammen mit dieser Meldung:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Exportieren der Anwendung als WAR-Datei

Exportieren Sie die Webprojektdatei als Webarchivdatei (WAR), damit Sie sie in der Web-App bereitstellen können. Die folgende Webprojektdatei befindet sich im Ordner "WebContent":

    META-INF
    WEB-INF
    index.jsp

1. Klicken Sie mit der rechten Maustaste auf den Ordner "WebContent", und wählen Sie **Export** aus.

2. Klicken Sie im Dialogfeld **Export auswählen** auf **Web > WAR**, und klicken Sie dann auf **Weiter**.

3. Wählen Sie im Dialogfeld **WAR-Export** das scr-Verzeichnis im aktuellen Projekt aus, und fügen Sie am Ende den Namen der WAR-Datei ein. Beispiel:

    `<project-path>/JSPHello/src/JSPHello.war`

Weitere Informationen zum Bereitstellen von WAR-Dateien finden Sie unter [Hinzufügen einer Java-Anwendung zu Azure App Service-Web-Apps](web-sites-java-add-app.md).


### Bereitstellen der Hello World-Anwendung mithilfe von FTP

Wählen Sie einen Drittanbieter-FTP-Client zum Veröffentlichen der Anwendung aus. In dieser Vorgehensweise werden zwei Optionen beschrieben: die in Azure integrierte Kudu-Konsole und FileZilla, ein beliebtes Tool mit einer benutzerfreundlichen grafischen Oberfläche.

> **Hinweis:** Das Azure-Plug-In für Eclipse mit Java 2.4 unterstützt die Bereitstellung in Speicherkonten und Clouddiensten, eine Bereitstellung in Web-Apps wird derzeit jedoch nicht unterstützt. Sie können mithilfe eines Azure-Bereitstellungsprojekts, wie in [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx) beschrieben, eine Bereitstellung in Speicherkonten und Clouddiensten durchführen. Eine Bereitstellung in Web-Apps ist jedoch nicht möglich. Verwenden Sie andere Methoden, z. B. FTP oder GitHub, um Ihre Dateien in die Web-App zu übertragen.

> **Hinweis:** Es wird nicht empfohlen, FTP über die Windows-Eingabeaufforderung zu verwenden (das Befehlszeilenprogramm FTP.EXE, das im Lieferumfang von Windows enthalten ist). FTP-Clients, die aktive FTP-Verbindungen nutzen, wie beispielsweise FTP.EXE, funktionieren häufig nicht, wenn Firewalls vorhanden sind. Beim aktiven FTP wird eine interne LAN-basierte Adresse angegeben, mit der ein FTP-Server wahrscheinlich keine Verbindung herstellen kann.

Weitere Informationen über die Bereitstellung in einer App Service-Web-App mithilfe von FTP finden Sie in den folgenden Themen:

- [Bereitstellen über ein FTP-Dienstprogramm](web-sites-deploy.md)


#### Einrichten der Anmeldeinformationen für die Bereitstellung

Stellen Sie sicher, dass Sie die Anwendung **AzureWebDemo** zum Erstellen einer Web-App ausgeführt haben. Sie übertragen Dateien an diesen Speicherort.

1. Melden Sie sich beim klassischen Azure-Portal an, und klicken Sie auf **Web-Apps**. Vergewissern Sie sich, dass **WebDemoWebApp** in der Liste der Web-Apps angezeigt und ausgeführt wird. Klicken Sie auf **WebDemoWebApp**, um die zugehörige Seite **Dashboard** anzuzeigen.

2. Klicken Sie auf der Seite **Dashboard** unterhalb von **Auf einen Blick** auf **Anmeldeinformationen für die Bereitstellung einrichten** (wenn diese bereits vorhanden sind, lautet diese Option **Anmeldeinformationen für die Bereitstellung zurücksetzen**).

    Anmeldeinformationen für die Bereitstellung gehören zu einem Microsoft-Konto. Sie müssen einen Benutzernamen und ein Kennwort eingeben, das Sie zur Bereitstellung mit Git und FTP verwenden können. Sie können diese Anmeldeinformationen zum Bereitstellen einer beliebigen Web-App in allen Azure-Abonnements nutzen, die Ihrem Microsoft-Konto zugeordnet sind. Stellen Sie im Dialogfeld Anmeldeinformationen für die Bereitstellen mit Git und FTP bereit, und notieren Sie Benutzername und Kennwort für die zukünftige Verwendung.


#### Abrufen der FTP-Verbindungsinformationen

Um FTP für die Bereitstellung von Anwendungsdateien in der neu erstellen Web-App zu verwenden, müssen Sie die Verbindungsinformationen abrufen. Es gibt zwei Möglichkeiten für den Abruf der Verbindungsinformationen. Eine Möglichkeit besteht darin, die Seite **Dashboard** für die Web-App anzuzeigen, die andere besteht darin, das Veröffentlichungsprofil der Web-App herunterzuladen. Das Veröffentlichungsprofil ist eine XML-Datei, die Informationen wie FTP-Hostname und Anmeldeinformationen für Ihre Web-Apps in Azure App Service bereitstellt. Sie können diesen Benutzernamen und das Kennwort zum Bereitstellen einer beliebigen Web-App in allen Abonnements verwenden, die dem Azure-Konto zugeordnet sind – nicht lediglich in diesem Abonnement.

So rufen Sie FTP-Verbindungsinformationen aus dem Web-App-Blatt im [Azure-Portal][] ab:

1. Suchen Sie unter **Grundlegende Informationen** nach **FTP-Hostname**, und kopieren Sie diesen Wert. Es handelt sich um einen URI ähnlich wie `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Suchen Sie unter **Grundlegende Informationen** nach **FTP/Benutzername für Bereitstellung**, und kopieren Sie diesen Wert. Dieser hat die Form *Name\_Web-App\\Benutzername\_Bereitstellung*, beispielsweise `WebDemoWebApp\deployer77`.

So rufen Sie FTP-Verbindungsinformationen aus dem Veröffentlichungsprofil ab

1. Klicken Sie im Blatt für die Web-App auf **Veröffentlichungsprofil abrufen**. Es wird eine .publishsettings-Datei von Ihrem lokalen Laufwerk abgerufen.

2. Öffnen Sie die .publishsettings-Datei in einem XML- oder Text-Editor, und suchen Sie das `<publishProfile>`-Element mit `publishMethod="FTP"`. Die Anzeige sollte folgendermaßen aussehen:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Beachten Sie, dass die `publishProfile`-Einstellungen der Web-App den Einstellungen im FileZilla-Servermanager entsprechen:

- `publishUrl` stimmt mit **FTP-Hostname** überein. Dies ist der Wert, den Sie in **Host** festgelegt haben.
- `publishMethod="FTP"` bedeutet, dass Sie **Protokoll** auf **FTP – File Transfer Protocol** und **Verschlüsselung** auf **Unverschlüsseltes FTP verwenden** festgelegt haben.
- `userName` und `userPWD` sind Schlüssel für die tatsächlichen Benutzernamen und Kennwörter, die Sie beim Zurücksetzen der Anmeldeinformationen für die Bereitstellung angegeben haben. `userName` stimmen mit **Bereitstellung / FTP-Benutzer** überein. Diese Werte entsprechen **Benutzer** und **Kennwort** in FileZilla.
- `ftpPassiveMode="True"` bedeutet, dass eine passive FTP-Übertragung verwendet wird. Wählen Sie auf der Registerkarte **Übertragungs-Einstellungen** die Option **Passiv**.


#### Konfigurieren der Web-App zum Hosten einer Java-Anwendung

Bevor Sie die Anwendung veröffentlichen, müssen Sie einige Konfigurationseinstellungen ändern, damit die Web-App eine Java-Anwendung hosten kann.

1. Wechseln Sie im klassischen Portal zur Seite **Dashboard** der Web-App, und klicken Sie auf **Konfigurieren**. Geben Sie auf der Seite **Konfigurieren** die folgenden Einstellungen an.

2. Für **Java-Version** lautet die Standardeinstellung **Aus**. Wählen Sie die Java-Version für Ihre Anwendungsziele, beispielsweise 1.7.0\_51. Stellen Sie anschließend sicher, dass **Webcontainer** auf eine Tomcat-Serverversion festgelegt ist.

3. Fügen Sie in **Standarddokumente** die Datei "index.jsp" hinzu, und verschieben Sie die Datei an den Anfang der Liste. (Die Standarddatei für Web-Apps ist "hostingstart.html".)

4. Klicken Sie auf **Speichern**.


#### Veröffentlichen der Anwendung mit Kudu

Eine Möglichkeit zum Veröffentlichen der Anwendung besteht darin, die in Azure integrierte Kudu-Debugkonsole zu verwenden. Kudu arbeitet stabil und konsistent mit App Service-Web-Apps und Tomcat Server zusammen. Sie greifen über eine URL mit dem folgenden Format auf die Konsole für die Web-App zu:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. In dieser Vorgehensweise verwendet die Kudu-Konsole die folgende URL. Wechseln Sie zu diesem Standort:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. Wählen Sie im obersten Menü **Debugkonsole > CMD**.

3. Navigieren Sie in der Konsolenbefehlszeile zu `/site/wwwroot` (oder klicken Sie auf `site` und dann in der Verzeichnisansicht am Anfang der Seite auf `wwwroot`):

    `cd /site/wwwroot`

4. Nach Angabe der **Java-Version** sollte der Tomcat-Server ein Verzeichnis namens "webapps" erstellen. Navigieren Sie in der Konsolenbefehlszeile zum Verzeichnis "webapps":

    `mkdir webapps`

    `cd webapps`

5. Ziehen Sie die Datei "JSPHello.war" aus `<project-path>/JSPHello/src/`, und legen Sie sie in der Kudu-Verzeichnisansicht unter `/site/wwwroot/webapps` ab. Ziehen Sie sie nicht in den Bereich "Hier ablegen zum Hochladen und Zippen", da Tomcat die Datei entzippt.

  ![][8]

Zunächst wird nur die Datei "JSPHello.war" im Verzeichnisbereich angezeigt:

  ![][9]

Nach kurzer Zeit (üblicherweise weniger als 5 Minuten) entzippt Tomcat Server die WAR-Datei in ein JSPHello-Verzeichnis. Klicken Sie auf das ROOT-Verzeichnis um anzuzeigen, ob die Datei "index.jsp" entzippt und in das Verzeichnis kopiert wurde. Wenn dies der Fall ist, wechseln Sie zurück zum webapps-Verzeichnis, und prüfen Sie, ob das entpackte JSPHello-Verzeichnis erstellt wurde. Falls diese Elemente nicht angezeigt werden, warten Sie, und wiederholen Sie den Vorgang.

  ![][10]


#### Veröffentlichen der Anwendung mit FileZilla (optional)

Eine zweite Möglichkeit zum Veröffentlichen der Anwendung besteht in der Verwendung von FileZilla. Hierbei handelt es sich um einen beliebten Drittanbieter-FTP-Client mit einer benutzerfreundlichen grafischen Oberfläche. Falls noch nicht geschehen, können Sie FileZilla über [http://filezilla-project.org/](http://filezilla-project.org/) herunterladen und installieren. Weitere Informationen zur Verwendung dieses FTP-Clients finden Sie in der [FileZilla-Dokumentation](https://wiki.filezilla-project.org/Documentation) und in diesem Blogeintrag: [FTP Clients – Part 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. Klicken Sie in FileZilla auf **Datei > Servermanager**.
2. Klicken Sie im **Servermanager** auf **Neuer Server**. Unter **Eintrag auswählen** wird ein neuer, leerer FTP-Server angezeigt, für den Sie einen Namen eingeben müssen. Verwenden Sie in dieser Vorgehensweise `AzureWebDemo-FTP`.

    Legen Sie auf der Registerkarte **Allgemein** die folgenden Einstellungen fest: – **Host:** Geben Sie den Wert für **FTP-Hostname** ein, den Sie aus dem Dashboard kopiert haben. – **Port:** (Lassen Sie dieses Feld leer, da es sich um eine passive Übertragung handelt und der Server den zu verwendenden Port ermittelt.) – **Protokoll:** FTP File Transfer Protocol - **Verschlüsselung:** Unverschlüsseltes FTP verwenden – **Verbindungsart:** Normal - **Benutzer:** Geben Sie den Benutzer für Bereitstellung/FTP ein, den Sie aus dem Dashboard kopiert haben. Dies ist der vollständige FTP-Benutzername in der Form *Name\_Web-App\\Benutzername*. – **Kennwort:** Geben Sie das Kennwort ein, das Sie beim Festlegen der Anmeldeinformationen für die Bereitstellung angegeben haben.

    Wählen Sie auf der Registerkarte **Übertragungs-Einstellungen** die Option **Passiv**.

3. Klicken Sie auf **Verbinden**. Bei erfolgreicher Verbindung zeigt die FileZilla-Konsole eine `Status: Connected`-Meldung an und gibt einen `LIST`-Befehl zum Auflisten der Verzeichnisinhalte aus.

4. Wählen Sie im Bereich **Lokal** das Quellverzeichnis aus, in dem sich die Datei "JSPHello.war" befindet. Der Pfad ähnelt dem folgenden:

    `<project-path>/JSPHello/src/`

5. Wählen Sie im Bereich **Remote** den Zielordner aus. Sie stellen die WAR-Datei im Verzeichnis `webapps` unterhalb des Web-App-Stammverzeichnisses bereit. Navigieren Sie zu `/site/wwwroot`, klicken Sie mit der rechten Maustaste auf `wwwroot`, und wählen Sie **Verzeichnis erstellen**. Geben Sie dem Verzeichnis den Namen `webapps`, und betreten Sie das Verzeichnis.

6. Übertragen Sie die Datei "JSPHello.war" in das Verzeichnis `/site/wwwroot/webapps`. Wählen Sie "JSPHello.war" in der Dateiliste **Lokal** aus, klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Hochladen**. Sie sollten die Datei in `/site/wwwroot/webapps` sehen.

7. Nachdem Sie die Datei "JSPHello.war" in das Verzeichnis "webapps" kopiert haben, entzippt Tomcat Server automatisch die Dateien in der WAR-Datei. Wenngleich Tomcat Server beinahe sofort mit dem Entpacken der Dateien beginnt, kann es lange dauern (möglicherweise Stunden), bis die Dateien im FTP-Client angezeigt werden.


#### Ausführen der Hello World-Anwendung in der Web-App

1. Nachdem Sie die WAR-Datei hochgeladen und sichergestellt haben, dass Tomcat Server ein entpacktes Verzeichnis `JSPHello` erstellt hat, wechseln Sie zu `http://webdemowebapp.azurewebsites.net/JSPHello`, um die Anwendung auszuführen.

    > **Hinweis:** Wenn Sie aus dem klassischen Portal auf **Durchsuchen** klicken, gelangen Sie möglicherweise zur Standardwebseite, die folgende Meldung anzeigt: "Diese Java-basierte Webanwendung wurde erfolgreich erstellt." Sie müssen die Webseite möglicherweise aktualisieren, damit anstelle der Standardwebseite die Anwendungsausgabe angezeigt wird.

2. Wenn die Anwendung ausgeführt wird, sollten Sie eine Webseite mit der folgenden Ausgabe sehen:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Bereinigen von Azure-Ressourcen

In dieser Vorgehensweise wird eine App Service-Web-App erstellt. Die Ressource wird in Rechnung gestellt, solange sie vorhanden ist. Sofern Sie nicht planen, die Web-App für Tests oder Entwicklung einzusetzen, sollten Sie sie beenden oder löschen. Für eine beendete Web-App fällt weiterhin eine geringe Gebühr an, aber dafür können Sie sie jederzeit erneut starten. Beim Löschen einer Web-App werden alle Daten entfernt, die Sie hochgeladen haben.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[Azure-Toolkits für Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[klassische Azure-Portal]: https://manage.windowsazure.com
[klassischen Azure-Portal]: https://manage.windowsazure.com
[Was ist ein Azure AD-Verzeichnis?]: http://technet.microsoft.com/library/jj573650.aspx
[Erstellen und Hochladen eines Verwaltungszertifikats für Azure]: http://msdn.microsoft.com/library/azure/gg551722.aspx
[Schlüssel- und Zertifikatverwaltungstool (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Azure-Portal]: https://portal.azure.com

<!---HONumber=September15_HO1-->