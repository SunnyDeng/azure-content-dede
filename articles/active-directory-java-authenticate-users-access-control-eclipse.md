<properties 
	pageTitle="Verwenden von Access Control (Java) – Azure-Featureleitfadem" 
	description="Erfahren Sie, wie Sie Access Control mit Java in Azure entwickeln und verwenden." 
	services="active-directory" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Authentifizieren von Webbenutzern mit Azure Access Control Service über Eclipse

Diese Anleitung zeigt, wie der Azure Access Control Service (ACS) im Azure-Plug-In für Eclipse mit Java (von Microsoft Open Technologies) verwendet wird. Weitere Informationen zum ACS finden Sie im Abschnitt [Nächste Schritte](#next_steps).

> [WACOM.NOTE]
> Der Azure Access Control Service-Filter (von Microsoft Open Technologies) ist eine Technologievorschau für die Community. Als Vorabversion wird diese Software von Microsoft Open Technologies oder Microsoft nicht offiziell unterstützt.

## Inhaltsverzeichnis

-   [Was ist der ACS?][]
-   [Konzepte][]
-   [Voraussetzungen][]
-   [Erstellen eines ACS-Namespace][]
-   [Hinzufügen von Identitätsanbietern][]
-   [Hinzufügen einer Anwendung der vertrauenden Seite][]
-   [Erstellen von Regeln][]
-   [Hochladen eines Zertifikats in Ihren ACS-Namespace][]
-   [Prüfen der Seite "Anwendungsintegration"][]
-   [Erstellen einer Java-Webanwendung][]
-   [Hinzufügen der ACS-Filterbibliothek zu Ihrer Anwendung][]
-   [Bereitstellen auf dem Serveremulator][]
-   [Bereitstellen in Azure][]
-   [Nächste Schritte][]

## <a name="what-is"></a>Was ist der ACS?

Die meisten Entwickler sind keine Identitätsexperten und möchten in der Regel
keine Zeit mit dem Entwickeln von Authentifizierungs- und Autorisierungsmechanismen für
Ihre Anwendungen und Dienste verbringen. ACS ist ein Azure-Dienst, der
eine einfache Möglichkeit für die Authentifizierung von Benutzern bereitstellt, die auf Ihre Webanwendungen
und Dienste ohne komplexe Authentifizierungslogik im Code
zugreifen müssen.

Folgende Funktionen stehen im ACS zur Verfügung:

-   Integration mit Windows Identity Foundation (WIF).
-   Unterstützung beliebter Anbieter von Webidentitäten (IPs), wie beispielsweise Windows Live ID, Google, Yahoo und Facebook.
-   Unterstützung von Active Directory Federation Services (AD FS) 2.0.
-   Ein auf dem Open Data Protocol (OData) basierender Verwaltungsdienst, der den programmgesteuerten Zugriff auf die ACS-Einstellungen ermöglicht.
-   Ein Verwaltungsportal, über das Administratoren auf die ACS-Einstellungen zugreifen können.

Weitere Informationen zu ACS finden Sie unter [Access Control Service 2.0][].

## <a name="concepts"></a>Konzepte

Azure ACS basiert auf den Prinzipien der anspruchsbasierten Identität -
ein einheitlicher Ansatz für die Erstellung von Authentifizierungsmechanismen für
Anwendungen, die lokal oder in der Cloud ausgeführt werden. Anspruchsbasierte Identität
bietet eine allgemeine Methode für Anwendungen und Dienste zum Abrufen der
Identitätsinformationen, die sie zu Benutzern in ihrer Organisation, in
anderen Organisationen und im Internet benötigen.

Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, sollten Sie die folgenden
Konzepte verstehen:

**Client** - im Kontext dieses Leitfadens, ist dies ein Browser, der
versucht, auf Ihre Webanwendung zuzugreifen.

**Anwendung der vertrauenden Seite (RP)** - Eine RP-Anwendung ist eine Website oder
ein Dienst, die bzw. der Authentifizierung an eine externe Stelle auslagert. Im
Identitätskontext sagt man, dass die RP der Authentifizierungsstelle vertraut. Dieses Handbuch
erklärt, wie Sie eine Anwendung so konfigurieren, dass sie dem ACS vertraut.

**Token** - Ein Token ist eine Sammlung von Sicherheitsdaten, die in der Regel
bei der erfolgreichen Authentifizierung eines Benutzers ausgegeben wird. Es enthält einen Satz von *Ansprüchen*. Dies sind Attribute des authentifizierten Benutzers. Ein Anspruch kann einen
Benutzernamen, eine ID für eine Rolle, der ein Benutzer angehört, das Alter eines Benutzers
und so weiter sein. Ein Token ist normalerweise digital signiert, d. h. es kann
immer wieder zum Aussteller zurückverfolgt werden, und sein Inhalt kann nicht geändert werden.
 Ein Benutzer erlangt Zugriff auf eine RP-Anwendung, indem er ein gültiges
Token bereitstellt, das von einer Zertifizierungsstelle ausgestellt wurde, der die RP-Anwendung vertraut.

**Identitätsanbieter (IP)** - Ein IP (Identity Provider) ist eine Stelle, die
Benutzer-Identitäten authentifiziert und Sicherheitstoken ausgibt. Die eigentliche Arbeit des Ausgebens
von Token wird über einen speziellen Dienst namens Sicherheitstokendienst
(STS)implementiert. Typische Beispiele für IPs sind Windows Live ID,
Facebook, geschäftliche Benutzerrepositorys (z. B. Active Directory) und so weiter.
Wenn ACS konfiguriert wurde, um einem IP zu vertrauen, akzeptiert und
überprüft das System Token, die von diesem IP ausgegeben wurden. ACS kann mehreren IPs gleichzeitig vertrauen,
was bedeutet, dass, wenn Ihre Anwendung ACS vertraut, Sie sofort
Ihre Anwendung allen authentifizierten Benutzern von allen IPs anbieten können,
denen ACS in Ihrem Auftrag vertraut.

**Verbundanbieter (Federation Provider, FP)** - IPs haben direktes Wissen über Benutzer
und authentifizieren Sie mit ihren Anmeldeinformationen und geben Ansprüche darüber aus, was
sie von ihnen wissen. Ein Verbundanbieter (FP) ist eine andere Art von
Authentifizierungsstelle: Anstatt Benutzer direkt zu authentifizieren, fungiert sie als
vermittelnde und verwaltende Authentifizierung zwischen einem RP und einem oder mehreren
IPs. IPs und FPs geben Sicherheitstokens aus, daher verwenden sie beide
den Sicherheitstokendienst (STS). Der ACS ist ein FP.

**ACS-Regelmodul** - Die Logik, die verwendet wird, um eingehende Tokens von
vertrauenswürdige IPs in Tokens umzuwandeln, die vom RP verwendet werden sollen, wird in Form von
einfachen Anspruchstransformationsregeln codiert. ACS bietet ein Regelmodul, das
das Anwenden einer beliebigen Transformationslogik, die Sie für Ihren RP angegeben haben,
übernimmt.

**ACS-Namespace** - Ein Namespace ist eine Partition oberster Ebene von ACS, den Sie
für das Organisieren Ihrer Einstellungen verwenden. Ein Namespace enthält eine Liste der IPs, denen Sie
vertrauen, der RP-Anwendungen, die Sie bereitstellen möchten, der Regeln, die das
Regelmodul voraussichtlich zum Verarbeiten eingehender Token verwendet, und so weiter. Ein namespace
weist verschiedene Endpunkte auf, die von der Anwendung und vom Entwickler verwendet werden,
damit ACS seine Funktion ausführen kann.

Die folgende Abbildung zeigt, wie die ACS-Authentifizierung bei einer:
Webanwendung abläuft:

![ACS flow diagram][acs_flow]

1.  Der Client (in diesem Fall ein Browser) fordert von der RP eine Seite an.
2.  Da die Anforderung noch nicht authentifiziert ist, verweist die RP den Benutzer an die Stelle, der sie vertraut, also an den ACS. Der ACS übermittelt dem Benutzer verschiedene IPs, die für diese RP definiert wurden. Der Benutzer wählt einen passenden IP aus.
3.  Der Client geht auf die Authentifizierungsseite des IPs und fordert den Benutzer auf, sich anzumelden.
4.  Wenn der Client authentifiziert ist (beispielsweise die Anmeldeinformationen eingegeben wurden), gibt der IP ein Sicherheitstoken aus.
5.  Nach der Ausgabe des Sicherheitstokens leitet der IP den Client an den ACS weiter und sendet das vom IP ausgegebene Sicherheitstoken an den ACS.
6.  Der ACS überprüft das vom IP ausgegebene Token, gibt die in diesem Token enthaltenen Identitätsansprüche in das ACS-Regelmodul ein, berechnet die auszugebenden Identitätsansprüche und gibt ein neues Sicherheitstoken aus, das diese Ansprüche enthält.
7.  Der ACS leitet den Client an die RP weiter. Der Client sendet das vom ACS ausgegebene neue Sicherheitstoken an die RP. Die RP validiert die Signatur auf dem vom ACS ausgegebenen Sicherheitstoken, validiert die Ansprüche in diesem Token und gibt die ursprünglich angeforderte Seite zurück.

## <a name="pre"></a>Voraussetzungen

Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, brauchen Sie Folgendes:

- Ein Java Developer Kit (JDK), Version 1.6 oder höher.
- Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann unter <http://www.eclipse.org/downloads/> heruntergeladen werden. 
- Eine Distribution eines Java-basierten Webservers oder Anwendungsservers wie Apache Tomcat, GlassFish, JBoss Application Server oder Jetty.
- Ein Azure-Abonnement, das Sie unter <http://www.microsoft.com/windowsazure/offers/> erhalten.
- Das Azure-Plug-In für Eclipse mit Java (von Microsoft Open Technologies) - Release vom April 2014. Weitere Informationen finden Sie unter [Installieren des Azure-Plug-Ins für Eclipse mit Java (von Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Ein X.509-Zertifikat, das von Ihrer Anwendung verwendet werden kann. Dieses Zertifikat wird sowohl als öffentliches Zertifikat (.cer) als auch im Format Personal Information Exchange (.PFX) benötigt. (Optionen zur Erstellung dieses Zertifikats finden Sie weiter unten in diesem Lernprogramm).
- Außerdem sollten Sie mit dem Azure-Serveremulator und den Einsatztechniken vertraut sein, die unter [Creating a Hello World Application for Windows Azure in Eclipse (Erstellen einer Hallo-Welt-Anwendung für Azure in Eclipse, in englischer Sprache)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) diskutiert werden.

## <a name="create-namespace"></a>Erstellen eines ACS-Namespace

Um mit der Verwendung von Azure Access Control Service (ACS) in Azure zu beginnen, müssen Sie
einen ACS-Namespace erstellen. Der Namespace stellt einen eindeutigen Bereich für
die Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung bereit.

1. Melden Sie sich beim [Azure-Verwaltungsportal][] an.
2. Klicken Sie auf **Active Directory**. 
3. Klicken Sie zum Erstellen eines neuen Access Control-Namespace auf **Neu** und dann auf **App-Dienste**, auf **Access Control** sowie auf **Schnellerstellung**. 
4. Geben Sie einen Namen für den Namespace ein. Azure prüft, ob der Name eindeutig ist.
5. Wählen Sie die Region aus, in der der Namespace verwendet wird. Verwenden Sie für die beste Leistung die Region, in der Sie ihre Anwendung einsetzen.
6. Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie für den ACS-Namespace verwenden möchten.
7. Klicken Sie auf **Erstellen**.

Azure erstellt und aktiviert den Namespace. Warten Sie, bis der Status des neuen Namespace auf **Aktiv** festgelegt wird, bevor Sie fortfahren. 

## <a name="add-IP"></a>Hinzufügen von Identitätsanbietern

In dieser Aufgabe fügen Sie IPs hinzu, die mit Ihrer RP-Anwendung zur
Authentifizierung verwendet werden. Zu Demonstrationszwecken veranschaulicht diese Aufgabe das Hinzufügen
von Windows Live als IP, aber Sie können alle im ACS-Verwaltungsportal aufgeführten IPs
verwenden.


1.  Klicken Sie im [Azure-Verwaltungsportal][] auf **Active Directory**, wählen Sie einen Access Control-Namespace aus, und klicken Sie dann auf **Verwalten**. Das ACS-Verwaltungsportal wird geöffnet.
2.  Klicken Sie im linken Navigationsbereich des ACS-Verwaltungsportals auf **Identitätsanbieter**.
3.  Windows Live ID wird standardmäßig aktiviert und kann nicht gelöscht werden. Für die Zwecke dieses Lernprogramms wird nur Windows Live ID verwendet. Auf diesem Bildschirm können Sie allerdings noch andere IPs hinzufügen, indem Sie auf die Schaltfläche **Hinzufügen** klicken.

Windows Live ID wird nun als IP für Ihren ACS-Namespace aktiviert. Anschließend geben Sie
Ihre Java-Webanwendung (die später erstellt wird) als RP an.

## <a name="add-RP"></a>Hinzufügen einer Anwendung der vertrauenden Seite

In dieser Aufgabe Konfigurieren Sie den ACS zur Erkennung Ihrer Java-
Webanwendung als gültige RP-Anwendung.

1.  Klicken Sie im ACS-Verwaltungsportal auf **Relying party applications**.
2.  Klicken Sie auf der Seite **Relying Party Applications** auf **Hinzufügen**.
3.  Führen Sie auf der Seite **Add Relying Party Application** die folgenden Aktionen aus:
    1.  Geben Sie unter **Name** einen Namen für die RP ein. Geben Sie für dieses Lernprogramm **Azure Web
        App** ein.
    2.  Wählen Sie unter **Modus** die Option **Enter settings manually** aus.
    3.  Geben Sie unter **Bereich** den URI ein, für den das vom ACS ausgegebene Sicherheitstoken gilt. Geben Sie für diese Aufgabe **http://localhost:8080/** ein.

        ![Relying party realm for use in compute emulator][relying_party_realm_emulator]

    4.  Geben Sie unter **Rückgabe-URL** die URL ein, an die der ACS das Sicherheitstoken zurückgibt. Geben Sie für diese Aufgabe **http://localhost:8080/** ein.

        ![Relying party return URL for use in compute emulator][relying_party_return_url_emulator]

    5.  Übernehmen Sie in den restlichen Feldern die Standardwerte.

4.  Klicken Sie auf **Speichern**.

Sie haben nun erfolgreich Ihre Java-Webanwendung konfiguriert, wenn sie im Azure-Serveremulator (unter
http://localhost: 8080/) als RP in Ihrem ACS-Namespace ausgeführt wird. Als Nächstes erstellen Sie
die Regeln, die der ACS zur Verarbeitung von Ansprüchen für die RP verwendet.

## <a name="create-rules"></a>Erstellen von Regeln

In dieser Aufgabe definieren Sie Regeln, die steuern, wie Ansprüche von
IPs an die RP übergeben werden. In diesem Leitfaden konfigurieren wir einfach
ACS zum Kopieren Sie die Eingabeanspruchstypen und -werte direkt im
Ausgabetoken, ohne sie zu filtern oder zu ändern.

1.  Klicken Sie auf der Hauptseite des ACS-Verwaltungsportals auf **Regelgruppen**.
2.  Klicken Sie auf der Seite **Regelgruppen** auf **Default Rule Group for Azure Web App**.
3.  Klicken Sie auf der Seite **Regelgruppe bearbeiten** auf **Generieren**.
4.  Aktivieren Sie auf der Seite **Generate Rules: Default Rule Group for Azure Web App** Windows Live ID, und klicken Sie dann auf **Generieren**.	
5.  Klicken Sie auf der Seite **Regelgruppe bearbeiten** auf **Speichern**.

## <a name="upload-certificate"></a>Hochladen eines Zertifikats in Ihren ACS-Namespace

In dieser Aufgabe laden Sie ein .PFX-Zertifikat hoch, das verwendet wird, um von Ihrem ACS-Namespace erstellte Tokenanforderungen zu signieren.

1.  Klicken Sie auf der Hauptseite des ACS-Verwaltungsportals auf **Certificates and Keys**.
2.  Klicken Sie auf der Seite **Certificates and Keys** über **Token Signing** auf **Hinzufügen**.
3.  Führen Sie auf der Seite **Add Token-Signing Certificate or Key** folgende Aktionen aus:
    1. Klicken Sie im Abschnitt **Used for** auf **Relying Party Application**, und wählen Sie **Azure Web App** aus (dies wurde zuvor als Name Ihrer Anwendung der vertrauenden Seite festgelegt).
    2. Wählen Sie im Abschnitt **Typ** die Option **X.509-Zertifikat** aus.
    3. Klicken Sie im Abschnitt **Zertifikat** auf die Schaltfläche "Durchsuchen", und navigieren Sie zur X.509 Zertifikat-Datei, die Sie verwenden möchten. Dies ist eine PFX-Datei. Wählen Sie die Datei aus, klicken Sie auf **Öffnen**,   und geben Sie im Textfeld   Kennwort** das Zertifikatkennwort ein. Bitte beachten Sie, dass Sie zu Testzwecken ein selbstsigniertes Zertifikat verwenden können. Klicken Sie zum Erstellen eines selbstsignierten Zertifikats im Dialogfeld **ACS Filter Library** (dieses wird später beschrieben) auf die Schaltfläche **Neu**, oder verwenden Sie das Hilfsprogramm **encutil.exe** auf der [Projektwebsite][] des Azure-Starterkits für Java (von Microsoft Open Technologies).
    4. Stellen Sie sicher, dass **Make Primary** aktiviert ist. Die Seite **Add Token-Signing Certificate or Key** sollte nun in etwas wie folgt aussehen.

        ![Add token-signing certificate][add_token_signing_cert]

    5. Klicken Sie auf **Speichern**, um Ihre Einstellungen zu speichern, und schließen Sie die Seite **Add Token-Signing Certificate or Key**.

Jetzt überprüfen Sie die Informationen auf der Seite "Anwendungsintegration" und
kopieren den URI, die Sie benötigen, um Ihre Java-
Anwendung für die Verwendung von ACS zu konfigurieren.

## <a name="review-app-int"></a>Überprüfen der Seite zur Anwendungsintegration

Sie finden alle Informationen und den Code, die bzw. den Sie zum Konfigurieren Ihrer
Java-Webanwendung (der RP-Anwendung) benötigen, um mit ACS zu arbeiten, auf
der Seite zur Anwendungsintegration des ACS-Verwaltungsportals. Sie
benötigen diese Informationen bei der Konfiguration Ihrer Java-Webanwendung für
die Verbundauthentifizierung.

1.  Klicken Sie im ACS-Verwaltungsportal auf **Application Integration**.  
2.  Klicken Sie auf der Seite **Application Integration** auf **Login Pages**.
3.  Klicken Sie auf der Seite **Login Page Integration** auf **Azure Web App**.

Auf der Seite **Login Page Integration: Azure Web App** wird die URL, die unter **Option 1: Link to an ACS-hosted login page** aufgeführt wird, in Ihrer Java-Webanwendung verwendet. Sie benötigen diesen Wert, wenn Sie die Bibliothek für Azure Access Control Service Filter zu ihrer Java-Anwendung hinzufügen.

## <a name="create-java-app"></a>Erstellen einer Java-Webanwendung
1. Klicken Sie im Eclipse-Menü auf **Datei**, auf **Neu** und anschließend auf **Dynamic Web Project**. Wenn **Dynamic Web Project** nach dem Klicken auf **Datei**, **Neu** nicht aufgelistet ist, gehen Sie folgendermaßen vor: Klicken Sie auf **Datei**, auf **Neu** und auf **Projekt**, erweitern Sie **Web**, und klicken Sie dann auf**Dynamic Web Project** sowie auf **Weiter**.) Nennen Sie das Projekt für die Zwecke dieses Lernprogramms **MyACSHelloWorld**. (Verwenden Sie diesen Namen unbedingt, da in den weiteren Schritten dieses Lernprogramms erwartet wird, dass Ihre WAR-Datei MyACSHelloWorld heißt).. Ihr Bildschirm sieht dann in etwa wie folgt aus:

    ![Create a Hello World project for ACS exampple][create_acs_hello_world]

    Klicken Sie auf **Fertig stellen**.
2. Erweitern Sie in der Projektexplorer-Ansicht von Eclipse die Option **MyACSHelloWorld**. Klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **Neu** sowie auf **JSP-Datei**.
3. Geben Sie der Datei im Dialogfeld **Neue JSP-Datei** den Namen **index.jsp**. Nennen Sie den übergeordneten Ordner, wie im Folgenden gezeigt, weiter "MyACSHelloWorld/WebContent":

    ![Add a JSP file for ACS example][add_jsp_file_acs]

    Klicken Sie auf **Weiter**.

4. Wählen Sie im Dialogfeld **JSP-Vorlage auswählen** die Option **Neue JSP-Datei (HTML)**, und klicken Sie auf **Fertig stellen**.
5. Wenn in Eclipse die Datei "index.jsp" geöffnet wird, geben Sie den Text **Hello ACS World!** ein, damit im vorhandenen Element `<body>` angezeigt wird. Der aktualisierte `<body>`-Inhalt sollte wie folgt aussehen:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Speichern Sie die Datei index.jsp.
  
## <a name="add_acs_filter_library"></a>Hinzufügen der ACS-Filterbibliothek zu Ihrer Anwendung

1. Klicken Sie im Eclipse-Projektexplorer mit der rechten Maustaste auf **MyACSHelloWorld**, klicken Sie auf **Buildpfad** und anschließend auf **Buildpfad konfigurieren**.
2. Klicken Sie im Dialogfeld 2. Java-Buildpfad** auf die Registerkarte **Bibliotheken**.
3. Klicken Sie auf **Bibliothek hinzufügen**.
4. Klicken Sie auf **Azure Access Control Services Filter (von MS Open Tech)** und anschließend auf **Weiter**. Das Dialogfeld **Azure Access Control Services Filter** wird angezeigt.  (Das Feld **Speicherort** möglicherweise einen anderen Pfad, je nachdem, wo Sie Eclipse installiert haben. Auch die Versionsnummer kann je nach Softwareupdates variieren).

    ![Add ACS Filter library][add_acs_filter_lib]

5. Öffnen Sie in einem Browser die Seite **Login Page Integration** des Verwaltungsportals, kopieren Sie die URL im Feld **Option 1: Link to an ACS-hosted login page**, und geben Sie sie in das Feld **ACS Authentication Endpoint** des Eclipse-Dialogfelds ein.
6. Öffnen Sie mit einem Browser die Seite **Edit Relying Party Application** des Verwaltungsportals, kopieren Sie die URL im Feld **Bereich**, und kopieren Sie sie in das Feld **Relying Party Realm** des Eclipse-Dialogfelds.
7. Wenn Sie ein vorhandenes Zertifikat verwenden möchten, klicken Sie im Abschnitt **Sicherheit** des Eclipse-Dialogfelds auf **Durchsuchen**, navigieren Sie zum Zertifikat, das Sie verwenden möchten, und klicken Sie auf **Öffnen**. Wenn Sie ein neues Zertifikat erstellen möchten, klicken Sie auf **Neu**, um das Dialogfeld **Neues Zertifikat** anzuzeigen, und legen Sie dann das Kennwort, den Namen der .cer-Datei und den Namen der .pfx-Datei für das neue Zertifikat fest.
8. Aktivieren Sie **Embed the certificate in the WAR file**. Wenn Sie das Zertifikat auf diese Weise einbetten, wird es in Ihre Bereitstellung aufgenommen, ohne dass Sie es manuell als Komponente hinzufügen müssen. (Wenn Sie Ihr Zertifikat stattdessen extern aus Ihrer WAR-Datei übernehmen möchten, können Sie es als Rollenkomponente hinzufügen und **Embed the certificate in the WAR file** deaktivieren.)
9. [Optional] Aktivieren Sie **Require HTTPS connections**. Wenn Sie diese Option aktivieren, müssen Sie über das HTTPS-Protokoll auf Ihre Anwendung zugreifen. Wenn Sie nicht möchten, dass HTTPS-Verbindungen erforderlich sind, deaktivieren Sie diese Option.
10. Wenn die Bereitstellung auf dem Serveremulator erfolgt, sehen Ihre Einstellungen für **Azure-ACS-Filter** in etwa wie folgt aus.

    ![Azure ACS Filter settings for a deployment to the compute emulator][add_acs_filter_lib_emulator]

11. Klicken Sie auf **Fertig stellen**.
12. Wenn die Meldung eingeblendet wird, dass eine web.xml-Datei erstellt wird, klicken Sie auf **Ja**.
13. Klicken Sie auf **OK**, um das Dialogfeld **Java-Buildpfad** zu schließen.

## <a name="deploy_compute_emulator"></a>Bereitstellen auf dem Serveremulator

1. Klicken Sie im Eclipse-Projektexplorer mit der rechten Maustaste auf **MyACSHelloWorld**, klicken Sie auf **Azure** und anschließend auf **Paket für Azure**.
2. Geben Sie unter **Projektname** den Text **MyAzureACSProject** ein, und klicken Sie auf **Weiter**.
3. Wählen sie ein JDK und einen Anwendungsserver aus. (Diese Schritte werden detailliert im Lernprogramm [Creating a Hello World Application for Windows Azure in Eclipse (Erstellen einer Hallo-Welt-Anwendung für Azure in Eclipse, in englischer Sprache)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) beschrieben).
4. Klicken Sie auf **Fertig stellen**.
5. Klicken Sie auf die Schaltfläche **Run in Azure Emulator**.
6. Schließen Sie nach dem Start Ihrer Java-Anwendung im Serveremulator alle Instanzen Ihres Browsers, damit andere laufende Browsersitzungen keine Konflikte mit Ihrem ACS-Anmeldungstest auslösen.
7. Führen Sie die Anwendung aus, indem Sie in Ihrem Browser <http://localhost:8080/MyACSHelloWorld/> öffnen(oder <https://localhost:8080/MyACSHelloWorld/>, wenn Sie **Require HTTPS connections** aktiviert haben). Sie sollten nun zur Anmeldung bei Windows Live ID aufgefordert und dann zur Rückgabe-URL weitergeleitet werden, die Sie für die Anwendung der vertrauenden Seite angegeben haben.
99.  Wenn Sie die Anzeige Ihrer Anwendung beendet haben, klicken Sie auf die Schaltfläche **Reset Azure Emulator**.

## <a name="deploy_azure"></a>Bereitstellen in Azure

Für eine Bereitstellung in Azure müssen Sie den RP-Bereich und die Rückgabe-URL für Ihren ACS-Namespace ändern.

1. Ändern Sie im Azure-Verwaltungsportal auf der Seite **Edit Relying Party Application** die Option **Bereich** in die URL Ihrer bereitgestellten Website. Ersetzen Sie **example** durch den DNS-Namen, den Sie für Ihre Bereitstellung angegeben haben.

    ![Relying party realm for use in production][relying_party_realm_production]

2. Ändern Sie 2. Rückgabe-URL** in die URL Ihrer Anwendung. Ersetzen Sie **example** durch den DNS-Namen, den Sie für Ihre Bereitstellung angegeben haben.

    ![Relying party return URL for use in production][relying_party_return_url_production]

3. Klicken Sie auf **Speichern**, um Ihren aktualisierten RP-Bereich und die geänderte Rückgabe-URL zu speichern.
4. Halten Sie die Seite **Login Page Integration** in Ihrem Browser geöffnet, da Sie in Kürze etwas daraus kopieren müssen.
5. Klicken Sie im Eclipse-Projektexplorer mit der rechten Maustaste auf **MyACSHelloWorld**, klicken Sie auf **Buildpfad** und anschließend auf **Buildpfad konfigurieren**.
6. Klicken Sie auf die Registerkarte **Bibliotheken**, auf **Azure Access Control Services Filter** und anschließend auf **Bearbeiten**.
7. Öffnen Sie in einem Browser die Seite **Login Page Integration** des Verwaltungsportals, kopieren Sie die URL im Feld **Option 1: Link to an ACS-hosted login page**, und geben Sie sie in das Feld **ACS Authentication Endpoint** des Eclipse-Dialogfelds ein.
8. Öffnen Sie mit einem Browser die Seite **Edit Relying Party Application** des Verwaltungsportals, kopieren Sie die URL im Feld **Bereich**, und kopieren Sie sie in das Feld **Relying Party Realm** des Eclipse-Dialogfelds.
9. Wenn Sie ein vorhandenes Zertifikat verwenden möchten, klicken Sie im Abschnitt **Sicherheit** des Eclipse-Dialogfelds auf **Durchsuchen**, navigieren Sie zum Zertifikat, das Sie verwenden möchten, und klicken Sie auf **Öffnen**. Wenn Sie ein neues Zertifikat erstellen möchten, klicken Sie auf **Neu**, um das Dialogfeld **Neues Zertifikat** anzuzeigen, und legen Sie dann das Kennwort, den Namen der .cer-Datei und den Namen der .pfx-Datei für das neue Zertifikat fest.
10. Behalten Sie die Aktivierung von **Embed the certificate in the WAR file** bei, da anzunehmen ist, dass Sie das Zertifikat in die WAR-Datei einbetten möchten.
11. [Optional] Aktivieren Sie **Require HTTPS connections**. Wenn Sie diese Option aktivieren, müssen Sie über das HTTPS-Protokoll auf Ihre Anwendung zugreifen. Wenn Sie nicht möchten, dass HTTPS-Verbindungen erforderlich sind, deaktivieren Sie diese Option.
12. Wenn die Bereitstellung in Azure erfolgt, sehen Ihre Einstellungen für Azure-ACS-Filter in etwa wie folgt aus:

    ![Azure ACS Filter settings for a production deployment][add_acs_filter_lib_production]

13. Klicken Sie auf **Fertig stellen**, um das Dialogfeld **Bibliothek bearbeiten** zu schließen.
14. Klicken Sie auf **OK**, um das Dialogfeld **Properties for MyACSHelloWorld** zu schließen.
15. Klicken Sie in Eclipse auf die Schaltfläche **Publish to Azure Cloud**. Befolgen Sie die Anweisungen ähnlich wie im Abschnitt **Bereitstellen der Anwendung in Azure** des Themas [Creating a Hello World Application for Windows Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). 

Schließen Sie nach der Bereitstellung Ihrer Webanwendung alle offenen Browser-Sitzungen, und führen Sie Ihre Webanwendung aus. Sie sollten nun die Aufforderung erhalten, sich mit Ihren Anmeldeinformationen für Windows Live ID anzumelden, und danach zur Rückgabe-URL Ihrer RP-Anwendung weitergeleitet werden.

Wenn Sie Ihre ACS-Hallo-Welt-Anwendung nicht mehr benötigen, denken Sie daran, die Bereitstellung zu löschen (Informationen dazu finden Sie im Thema [Creating a Hello World Application for Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).


## <a name="next_steps"></a>Nächste Schritte

Informationen zur vom ACS an Ihre Anwendung zurückgegebenen Security Assertion Markup Language (SAML) finden Sie unter [How to view SAML returned by the Azure Access Control Service][]. Wenn Sie die ACS-Funktionalität genauer erforschen und mit anspruchsvolleren Szenarien experimentieren möchten, finden Sie weitere Informationen unter [Access Control Service 2.0][].

In diesem Beispiel wurde auch die Option **Embed the certificate in the WAR file** verwendet. Diese Option vereinfacht die Bereitstellung des Zertifikats. Wenn Sie stattdessen Ihr Signaturzertifikat von Ihrer WAR-Datei getrennt halten möchten, gehen Sie folgendermaßen vor:

1. Geben Sie im Abschnitt 1. Sicherheit** des Dialogfelds **Azure Access Control Services Filter** die Zeichenfolge **${env.JAVA_HOME}/mycert.cer** ein, und deaktivieren Sie **Embed the certificate in the WAR file**. (Wenn Ihre Zertifikatdatei einen anderen Namen hat, ersetzen Sie mycert.cer durch diesen.) Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen.
2. Kopieren Sie das Zertifikat als Komponente in Ihre Bereitstellung: Erweitern Sie im Eclipse-Projektexplorer **MyAzureACSProject**, klicken Sie mit der rechten Maustaste auf **WorkerRole1**, klicken Sie auf **Eigenschaften**, erweitern Sie **Azure Role**, und klicken Sie auf **Komponenten**.
3. Klicken Sie auf **Hinzufügen**.
4. Im Dialogfeld **Komponente hinzufügen**:
    1. Im Abschnitt **Importieren**:
        1. Navigieren Sie mithilfe der Schaltfläche **Datei** zu dem Zertifikat, das Sie verwenden möchten. 
        2. Wählen Sie für **Methode** die Option **Kopieren** aus.
    2. Klicken Sie unter **Als Name** auf das Textfeld, und übernehmen Sie den Standardnamen.
    3. Im Abschnitt **Bereitstellen**:
        1. Wählen Sie für **Methode** die Option **Kopieren** aus.
        2. Geben Sie unter **To directory** die Zeichenfolge **%JAVA_HOME%** ein.
    4. Das Dialogfeld **Komponente hinzufügen** sollte nun etwa wie folgt aussehen.

        ![Add certificate component][add_cert_component]

    5. Klicken Sie auf **OK**.

An diesem Punkt wird Ihr Zertifikat in Ihre Bereitstellung aufgenommen. Bitte beachten sie: Unabhängig davon, ob Sie das Zertifikat in die WAR-Datei einbetten oder als Komponente in Ihre Bereitstellung einfügen, müssen Sie das Zertifikat wie im Abschnitt [Hochladen eines Zertifikats in Ihren ACS-Namespace][] beschrieben in Ihren Namespace hochladen.

[Was ist der ACS?]: #what-is
[Konzepte]: #concepts
[Voraussetzungen]: #pre
[Erstellen einer Java-Webanwendung]: #create-java-app
[Erstellen eines ACS-Namespace]: #create-namespace
[Hinzufügen von Identitätsanbietern]: #add-IP
[Hinzufügen einer Anwendung der vertrauenden Seite]: #add-RP
[Erstellen von Regeln]: #create-rules
[Hochladen eines Zertifikats in Ihren ACS-Namespace]: #upload-certificate
[Prüfen der Seite "Anwendungsintegration"]: #review-app-int
[Konfigurieren der Vertrauensstellung zwischen ACS und Ihrer ASP.NET-Webanwendung]: #config-trust
[Hinzufügen der ACS-Filterbibliothek zu Ihrer Anwendung]: #add_acs_filter_library
[Bereitstellen auf dem Serveremulator]: #deploy_compute_emulator
[Bereitstellen in Azure]: #deploy_azure
[Nächste Schritte]: #next_steps
[Projektwebsite]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: /de-de/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

<!--HONumber=46--> 
