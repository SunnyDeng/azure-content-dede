<properties pageTitle="Get Started with the Azure WebJobs SDK" metaKeywords="Azure tutorial, Azure WebJobs tutorial, Azure multi-tier tutorial, MVC tutorial, Azure blobs tutorial, Azure queues tutorial, Azure storage tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The frontend runs in a website, and the backend runs as a WebJob. The app uses Entity Framework, SQL Database, and Azure storage queues and blobs." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Get Started with the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Erste Schritte mit dem Azure WebJobs SDK

Dieses Lernprogramm enthält eine Einführung in die WebJobs SDK und beschreibt die Erstellung einer ASP.NET MVC-Anwendung mit mehreren Ebenen, die das WebJobs SDK in einer [Azure-Website][Azure-Website] verwendet. Die Anwendung verwendet eine [Azure SQL-Datenbank][Azure SQL-Datenbank], den [Azure-Blob-Dienst][Azure-Blob-Dienst] und den [Azure-Warteschlangendienst][Azure-Warteschlangendienst].

Bei dieser Beispielanwendung handelt es sich um ein Bulletin Board für Werbung. Benutzer können eine Werbung erstellen, indem sie Text eingeben und ein Bild hochladen. Die Anwendung zeigt eine Liste von Werbeeinblendungen mit Miniaturbildern an, und Benutzer können auf eine Werbung klicken, um das Bild in voller Größe anzuzeigen. Hier sehen Sie einen Screenshot:

![Werbungsliste][Werbungsliste]

Sie können das [Visual Studio-Projekt][Visual Studio-Projekt] in der MSDN Code Gallery herunterladen.

## Inhaltsverzeichnis

Dieses Lernprogramm enthält die folgenden Abschnitte:

-   [Voraussetzungen][Voraussetzungen]
-   [Sie lernen Folgendes][Sie lernen Folgendes]
-   [Das WebJobs SDK][Das WebJobs SDK]
-   [Anwendungsarchitektur][Anwendungsarchitektur]
-   [Einrichten der Entwicklungsumgebung][Einrichten der Entwicklungsumgebung]
-   [Erstellen, Ausführen und Bereitstellen der Anwendung][Erstellen, Ausführen und Bereitstellen der Anwendung]
-   [Erstellen der Anwendung von Grund auf][Erstellen der Anwendung von Grund auf]
-   [Überprüfen des Anwendungscodes][Überprüfen des Anwendungscodes]
-   [Problembehandlung][Problembehandlung]
-   [Nächste Schritte][Nächste Schritte]

## <span id="prerequisites"></span></a>Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie mit der Arbeit mit [ASP.NET MVC][ASP.NET MVC]- oder [Web Forms][Web Forms]-Projekten in Visual Studio vertraut sind. Die Beispielanwendung verwendet MVC, aber ein Großteil des Lernprogramms gilt auch für Web Forms.

Die Anweisungen im Lernprogramm funktionieren mit den folgenden beiden Produkten:

-   Visual Studio 2013
-   Visual Studio 2013 Express für das Web

Falls Sie keines dieser Produkte haben, wird Visual Studio 2013 Express für das Web zusammen mit dem Azure SDK installiert.

[WACOM.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <span id="learn"></span></a>Sie lernen Folgendes

Dieses Lernprogramm beschreibt die folgenden Aufgaben:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK.
-   Erstellen eines Konsolenanwendungsprojekts, das automatisch als Azure WebJob bereitgestellt wird, wenn Sie das zugehörige Webprojekt bereitstellen.
-   Lokales Testen des WebJobs SDK-Back-Ends auf dem Entwicklungscomputer.
-   Veröffentlichen einer Anwendung mit WebJobs-Back-End in einer Azure-Website.
-   Hochladen von Dateien und Speicherung der Dateien im Azure-Blob-Dienst.
-   Verwenden des Azure WebJobs SDK für die Arbeit mit Azure-Speicher-Warteschlangen und -Blobs.

## <span id="webjobssdk"></span></a>Einführung in das WebJobs SDK

[WebJobs][WebJobs] ist ein Feature von Azure-Websites, mit dem Sie Programme oder Skripts im gleichen Kontext wie Ihre Website ausführen können. Sie müssen das WebJobs SDK nicht verwenden, um Programme als WebJobs auszuführen. Das WebJobs SDK vereinfacht das Schreiben von Code für die Arbeit mit Azure-Speicher-Warteschlangen, -Blobs, -Tabellen und Servicebus-Warteschlangen.

Das WebJobs SDK enthält die folgenden Komponenten:

-   **NuGet-Pakete**. NuGet-Pakete in Visual Studio-Projekten bieten ein Framework, das Ihr Code für die Arbeit mit dem Azure-Speicherdienst oder mit Servicebus-Warteschlangen verwendet.
-   **Dashboard**. Ein Teil des WebJobs SDK ist in Azure-Websites enthalten und bietet umfassende Überwachungs- und Diagnosefunktionen für die Programme, die Sie unter Verwendung der NuGet-Pakete schreiben. Sie müssen keinen Code schreiben, um diese Überwachungs- und Diagnosefunktionen zu nutzen.

Sie können Programme, die die NuGet-Pakete des WebJobs SDK verwenden, überall ausführen. Diese Programme müssen nicht als Azure-WebJobs ausgeführt werden. Das Dashboard ist jedoch nur als Website-Erweiterung für eine Azure-Website verfügbar. Sie können ein Programm, das das WebJobs SDK verwendet, z. B. lokal auf dem Entwicklungscomputer oder in einer Azure-Clouddienst-Workerrolle ausführen. Anschließend können Sie Überwachungsinformationen abrufen, indem Sie das WebJobs SDK-Dashboard einer Azure-Website so konfigurieren, dass dieses dasselbe Speicherkonto verwendet wie Ihr WebJobs SDK-Programm.

Zum Zeitpunkt der Erstellung dieses Lernprogramms befindet sich die WebJobs SDK in der Betaphase. Das SDK wird nicht für den Einsatz in Produktionsumgebungen unterstützt.

### Typische Szenarien

Hier sehen Sie einige typische Szenarien, die Sie mit dem Azure WebJobs SDK vereinfachen können:

-   Bildbearbeitung oder andere prozessorintensive Prozesse. Ein typisches Feature von Websites ist die Möglichkeit, Bilder oder Videos hochzuladen. Häufig möchten Sie die Inhalte nach dem Hochladen manipulieren, allerdings soll der Benutzer nicht auf den Abschluss dieses Vorgangs warten müssen.
-   Warteschlangenverarbeitung. Web-Front-Ends kommunizieren häufig über Warteschlangen mit Back-End-Diensten. Wenn die Website eine Aufgabe zu erledigen hat, legt sie eine Nachricht in einer Warteschlange ab. Ein Back-End-Dienst ruft die Nachrichten aus der Warteschlange ab und erledigt die Aufgabe. Sie können Warteschlangen z. B. für die Bildbearbeitung verwenden: Nachdem ein Benutzer Dateien hochgeladen hat, legen Sie die Dateinamen in eine Warteschlangen-Nachricht, die anschließend vom Back-End für die Verarbeitung abgerufen wird. Sie können Warteschlangen auch verwenden, um die Ansprechbarkeit der Website zu verbessern. Anstatt direkt in eine SQL-Datenbank zu schreiben, können Sie in eine Warteschlange schreiben, dem Benutzer mitteilen, dass Sie fertig sind, und die Datenbankvorgänge mit höherer Latenz von einem Back-End-Dienst ausführen lassen.
-   RSS-Aggregation. Wenn Ihre Website eine Liste von RSS-Feeds pflegt, können Sie z. B. alle Artikel aus den Feeds in einem Hintergrundprozess abrufen.
-   Dateiwartung, z. B. Aggregation oder Bereinigung von Protokolldateien. Möglicherweise haben Sie Protokolldateien von verschiedenen Websites oder über verschiedene Zeitspannen, die Sie kombinieren möchten, um Analyseaufgaben ausführen zu können. Vielleicht möchten Sie auch eine wöchentliche Aufgabe planen, um alte Protokolldateien zu bereinigen.
-   Andere langwierige Aufgaben, die Sie im Hintergrund ausführen möchten, z. B. der Versand von E-Mails.

Um zu verhindern, dass Ihre Website nach einer gewissen Zeit der Inaktivität in den Schlafmodus versetzt wird und Ihre langwierigen Hintergrundaufgaben dabei unterbrochen werden, können Sie die [AlwaysOn][AlwaysOn]-Funktion von Azure-Websites verwenden.

### Codebeispiele

Der Code für typische Aufgaben im Zusammenhang mit dem Azure-Speicher ist einfach. In einer Konsolenanwendung schreiben Sie Methoden für die Hintergrundaufgaben, die Sie ausführen möchten, und ergänzen diese Methoden mit Attributen aus dem WebJobs SDK. Ihre `Main`-Methode erstellt ein `JobHost`-Objekt, das die Aufrufe an die von Ihnen geschriebenen Methoden koordiniert. Das WebJobs SDK-Framework weiß anhand der verwendeten WebJobs SDK-Attribute, wann die Methoden aufgerufen werden sollen. Beispiel:

        static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

Das `JobHost`-Objekt ist ein Container für einen Satz von Hintergrundfunktionen. Das `JobHost`-Objekt überwacht die Funktionen, wartet auf Ereignisse, die diese Funktionen auslösen, und ruft die Funktionen auf, wenn Auslöserereignisse auftreten. Sie rufen eine `JobHost`-Methode auf, um anzugeben, ob der Containerprozess im aktuellen Thread oder in einem Hintergrundthread ausgeführt werden soll. In diesem Beispiel führt die `RunAndBlock`-Methode den Prozess fortlaufend im aktuellen Thread aus.

Da die `ProcessQueueMessage`-Methode in diesem Beispiel ein `QueueTrigger`-Attribut hat, ist der Auslöser für die Funktion in diesem Fall der Empfang einer neuen Warteschlangen-Nachricht. Das `JobHost`-Objekt wartet auf neue Warteschlangen-Nachrichten in der angegebenen Warteschlange ("webjobsqueue" in diesem Beispiel) und ruft `ProcessQueueMessage` auf, wenn eine Nachricht gefunden wurde. Das `QueueTrigger`-Attribut weist das Framework außerdem an, den `inputText`-Parameter an den Wert der Warteschlangen-Nachricht zu binden:

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    [Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

Außerdem bindet das Framework ein `TextWriter`-Objekt an einen Blob mit dem Namen "blobname" in einem Container mit dem Namen "containername":

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    [Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

Die Funktion verwendet diese Parameter, um den Wert der Warteschlangen-Nachricht in den Blob zu schreiben:

        writer.WriteLine(inputText);

Wie Sie sehen können, vereinfachen die Auslöse- und Bindungsfeatures des WebJobs SDK den Code, den Sie für die Arbeit mit Azure-Speicherobjekten schreiben müssen, dramatisch. Den Low-Level-Code für die Verarbeitung von Warteschlangen und Blobs übernimmt das WebJobs SDK-Framework für Sie. Das Framework erstellt Warteschlangen, die noch nicht existieren, öffnet die Warteschlange, liest Warteschlangen-Nachrichten, löscht Warteschlangen-Nachrichten nach Abschluss der Verarbeitung, erstellt Blob-Container, die noch nicht existieren, schreibt in Blobs, und so weiter.

Das WebJobs SDK bietet verschiedene Wege für die Arbeit mit dem Azure-Speicher. Wenn der Parameter, den Sie mit dem `QueueTrigger`-Attribut ergänzen, ein Byte-Array oder einen benutzerdefinierten Typ enthält, wird dieser z. B. automatisch aus JSON deserialisiert. Außerdem können Sie ein `BlobTrigger`-Attribut verwenden, um Prozesse auszulösen, wenn ein neuer Blob in Ihrem Azure-Speicherkonto erstellt wird. (Während `QueueTrigger` neue Warteschlangen-Nachrichten innerhalb weniger Sekunden findet, kann es bis zu 20 Minuten dauern, bis `BlobTrigger` einen neuen Blob entdeckt. `BlobTrigger` sucht nach Blobs, wenn der `JobHost` gestartet wird und prüft den Azure-Speicher anschließend regelmäßig auf neue Blobs.)

## <span id="contosoads"></span></a>Anwendungsarchitektur

Die Beispielanwendung verwendet das [warteschlangenorientierte Arbeitsmuster][Azure-Warteschlangendienst], um die CPU-intensive Last der Erstellung von Miniaturbildern an einen Back-End-Prozess auszulagern.

Die Anwendung speichert Werbungen in einer SQL-Datenbank und verwendet Entity Framework Code First, um Tabellen zu erstellen und auf Daten zuzugreifen. Pro Werbung werden in der Datenbank zwei URLs gespeichert, eine für das Bild in voller Größe und eine für die Miniaturansicht.

![Ad-Tabelle][Ad-Tabelle]

Wenn ein Benutzer ein Bild hochlädt, speichert das Front-End das Bild in einem [Azure-Blob][Azure-Blob-Dienst] und speichert die Werbeinformationen in der Datenbank zusammen mit einer URL, die auf das Blob zeigt. Gleichzeitig wird eine Nachricht in eine Azure-Warteschlange geschrieben. Ein als Azure WebJob ausgeführter Back-End-Prozess verwendet das WebJobs SDK, um die Warteschlange auf neue Nachrichten zu überprüfen. Wenn eine neue Nachricht vorhanden ist, erstellt der WebJob eine Miniaturansicht für das Bild und aktualisiert das entsprechende Datenbankfeld mit der URL für diese Werbung. Das folgende Diagramm zeigt die Interaktion zwischen den verschiedenen Anwendungskomponenten:

![Contoso Ads-Architektur][Contoso Ads-Architektur]

### Alternative Architektur

WebJobs werden im Kontext einer Website ausgeführt und können nicht separat skaliert werden. Wenn Sie z. B. eine Standard-Website-Instanz haben, können Sie nur 1 Instanz Ihres Hintergrundprozesses ausführen. Dieser Prozess verwendet Serverressourcen (Prozessor, Arbeitsspeicher usw.), die andernfalls für die Auslieferung von Webinhalten zur Verfügung stehen würden.

Wenn der Datenverkehr je nach Tageszeit oder Wochentag schwankt und Ihre Back-End-Verarbeitung nicht zeitkritisch ist, können Sie die Ausführung Ihrer WebJobs in Zeiten mit niedrigem Datenverkehr planen. Wenn die Last immer noch zu hoch für diese Lösung ist, können Sie alternative Umgebungen für Ihr Back-End-Programm in Betracht ziehen:

-   Führen Sie das Programm als WebJob in einer separaten Website aus, die ausschließlich zu diesem Zweck dient. Auf diese Weise können Sie Ihre Back-End-Website unabhängig von Ihrer Front-End-Website skalieren.
-   Führen Sie das Programm in einer Azure-Clouddienst-Workerrolle aus. Mit dieser Option können Sie das Front-End entweder in einer Clouddienst-Webrolle oder in einer Website ausführen.

Dieses Lernprogramm beschreibt, wie Sie das Front-End in einer Website und das Back-End als WebJob in derselben Website ausführen können. Informationen zur Auswahl der optimalen Umgebung für Ihr Szenario finden Sie unter [Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure][Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure].

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## <span id="storage"></span></a>Erstellen eines Azure-Speicherkontos

Azure-Speicherkonten bieten Ressourcen zum Speichern von Warteschlangen- und Blobdaten in der Cloud. Das WebJobs SDK verwendet das Speicherkonto außerdem zum Speichern von Protokollierungsdaten für das Dashboard.

In einer tatsächlichen Anwendung würden Sie normalerweise separate Konten für Anwendungsdaten und Protokolldaten sowie für Test- und Produktionsdaten erstellen. In diesem Lernprogramm verwenden wir nur ein einziges Konto.

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf **Neu** - **Datendienste** - **Speicher** - **Schnellerfassung**.

2.  Geben Sie ein URL-Präfix in das **URL**-Eingabefeld ein.

    Dieses Präfix zusammen mit dem Text unterhalb des Eingabefelds ergibt die eindeutige URL Ihres Speicherkontos. Falls das eingegebene Präfix bereits von einer anderen Person verwendet wird, wählen Sie ein anderes Präfix aus.

    In der Abbildung am Ende dieses Abschnitts wird ein Speicherkonto mit der URL `contosoads.core.windows.net` erstellt.

3.  Wählen Sie in der Dropdownliste **Region** den Ihnen am nächsten gelegenen Ort aus.

    Diese Einstellung legt fest, in welchem Azure-Rechenzentrum Ihr Speicherkonto gehostet wird. Für dieses Lernprogramm macht Ihre Auswahl keinen spürbaren Unterschied. Für Produktions-Websites sollten Ihr Webserver und Ihr Speicherkonto jedoch in der gleichen Region liefen, um Latenz und Kosten für die Datenübertragung zu minimieren. Die Website (die Sie später erstellen) sollte sich möglichst nahe an den Browsern befinden, die auf die Website zugreifen, um die Latenz zu minimieren.

4.  Wählen Sie in der Dropdownliste **Replikation** den Wert **Lokal redundant** aus.

    Wenn Georeplikation für ein Speicherkonto aktiviert ist, werden dessen Inhalte an ein zweites Rechenzentrum repliziert, um im Katastrophenfall eine Failover-Instanz an diesem zweiten Standort zur Verfügung zu haben. Für die Georeplikation können zusätzliche Kosten anfallen. Für Test- und Entwicklungskonten macht es wenig Sinn, für Georeplikation zu bezahlen. Weitere Informationen finden Sie unter [How to Manage Storage Accounts][How to Manage Storage Accounts] (Verwalten von Speicherkonten, in englischer Sprache).

5.  Klicken Sie auf **Speicherkonto erstellen**.

    ![Neues Speicherkonto][Neues Speicherkonto]

## <span id="download"></span></a>Herunterladen der Anwendung

1.  Laden Sie die [abgeschlossene Lösung][Visual Studio-Projekt] herunter und entzippen Sie das Archiv.

2.  Starten Sie Visual Studio.

3.  Wählen Sie im Menü **Datei** die Option **Öffnen** **Projekt/Lösung**, navigieren Sie zu dem Ort, an dem Sie die Lösung gespeichert haben und öffnen Sie die Lösungsdatei.

4.  Drücken Sie STRG+UMSCH+B, um die Lösung zu erstellen.

    Standardmäßig stellt Visual Studio den Inhalt des NuGet-Pakets automatisch wieder her, das nicht in der *.zip*-Datei enthalten war. Wenn die Pakete nicht wiederhergestellt werden, installieren Sie diese manuell, indem Sie das Dialogfeld **NuGet-Pakete verwalten** öffnen und oben rechts auf **Wiederherstellen** klicken.

5.  Vergewissern Sie sich im **Projektmappen-Explorer**, dass **ContosoAdsWeb** als Startprojekt ausgewählt ist.

## <span id="configurestorage"></span></a>Konfigurieren der Anwendung zur Verwendung Ihres Speicherkontos

1.  Öffnen Sie die Datei *Web.config* für das ContosoAdsWeb-Projekt.

    Die Datei enthält eine SQL-Verbindungszeichenfolge und eine Azure-Speicher-Verbindungszeichenfolge für die Arbeit mit Blobs und Warteschlangen.

    Die SQL-Verbindungszeichenfolge zeigt auf eine [SQL Server Express LocalDB][SQL Server Express LocalDB]-Datenbank.

    Die Speicher-Verbindungszeichenfolge enthält Platzhalter, die Sie in den folgenden Schritten durch den Namen und den Zugriffsschlüssel Ihres Speicherkontos ersetzen.

    ``` prettyprint
    <connectionStrings>
      <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
      <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[Kontoname];AccountKey=[Zugriffsschlüssel]"/>
    </connectionStrings>
    ```

    Die Speicher-Verbindungszeichenfolge hat den Namen AzureWebJobsStorage, da dies der Name ist, den das WebJobs SDK standardmäßig verwendet. Hier wird derselbe Name verwendet, sodass Sie nur einen Verbindungszeichenfolgenwert in der Azure-Umgebung setzen müssen.

2.  Wählen Sie im [Azure-Verwaltungsportal][1] Ihr Speicherkonto aus und klicken Sie im unteren Seitenbereich auf **Zugriffsschlüssel verwalten**.

    ![Schaltfläche "Zugriffsschlüssel verwalten"][Schaltfläche "Zugriffsschlüssel verwalten"]

    ![Dialogfeld "Zugriffsschlüssel verwalten"][Dialogfeld "Zugriffsschlüssel verwalten"]

3.  Ersetzen Sie *[Kontoname]* in der Verbindungszeichenfolge durch den Wert im Feld **Speicherkontoname**.

4.  Ersetzen Sie *[Zugriffsschlüssel]* in der Verbindungszeichenfolge durch den Wert im Feld **Primärer Zugriffsschlüssel**.

5.  Öffnen Sie die Datei *App.config* für das ContosoAdsWebJob-Projekt.

    Die Datei enthält zwei Verbindungszeichenfolgen: eine für die Anwendungsdaten und eine für die Protokollierung. In diesem Lernprogramm verwenden Sie dasselbe Konto für beide Funktionen. Die Verbindungszeichenfolgen enthalten dieselben Platzhalter, die Sie bereits kennengelernt haben.

  	<pre class="prettyprint">&lt;configuration&gt;
    &lt;connectionStrings&gt;
        &lt;add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/&gt;
    &lt;/connectionStrings&gt;
        &lt;startup&gt; 
            &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /&gt;
    &lt;/startup&gt;
    &lt;/configuration&gt;</pre>

    Standardmäßig sucht das WebJobs SDK nach Verbindungszeichenfolgen mit den Namen AzureWebJobsStorage und AzureWebJobsDashboard. Alternativ können Sie beliebige Namen für die Verbindungszeichenfolgen vergeben und die Namen explizit an das `JobHost`-Objekt übergeben.

6.  Ersetzen Sie alle Vorkommnisse von *[Kontoname]* und *[Zugriffsschlüssel]* mit den Werten für Ihr Speicherkonto, wie Sie dies bereits für das Webprojekt getan haben. (Alternativ können Sie die komplette Verbindungszeichenfolge aus der Datei *Web.config* in die beiden Verbindungszeichenfolgen in der Datei *App.config* kopieren.)

7.  Speichern Sie die Änderungen.

## <span id="run"></span></a>Lokales Ausführen der Anwendung

1.  Starten Sie das Web-Front-End der Anwendung mit STRG+F5.

    Die Startseite wird in Ihrem Standardbrowser geöffnet. (Das Webprojekt wird ausgeführt, da es als Startprojekt konfiguriert ist.)

    ![Contoso Ads-Startseite][Contoso Ads-Startseite]

2.  Klicken Sie mit der rechten Maustaste auf das ContosoAdsWebJob-Projekt im **Projektmappen-Explorer** und anschließend auf **Debug** \> **Neue Instanz starten**, um das WebJobs-Back-End der Anwendung zu starten.

    Ein Konsolenfenster wird geöffnet und enthält den Text "Job host started", um anzuzeigen, dass die Anwendung läuft.

    ![Konsolenfenster mit Hinweis, dass das Back-End ausgeführt wird][Konsolenfenster mit Hinweis, dass das Back-End ausgeführt wird]

3.  Klicken Sie in Ihrem Browser auf **Werbung erstellen**.

4.  Geben Sie einige Testdaten ein, wählen Sie ein hochzuladendes Bild aus, und klicken Sie auf **Erstellen**.

    ![Seite erstellen][Seite erstellen]

    Die Anwendung wechselt zur Indexseite, zeigt jedoch noch keine Miniaturansicht für die neue Werbung an, da diese Verarbeitung noch nicht erfolgt ist.

    Nach einer kurzen Wartezeit erscheint eine Protokollierungsnachricht im Konsolenfenster und zeigt an, dass eine Warteschlangen-Nachricht empfangen und verarbeitet wurde.

    ![Konsolenfenster mit Hinweis, dass eine Warteschlangen-Nachricht verarbeitet wurde][Konsolenfenster mit Hinweis, dass eine Warteschlangen-Nachricht verarbeitet wurde]

5.  Nachdem die Protokollierungsnachricht im Konsolenfenster angezeigt wurde, können Sie die Indexseite aktualisieren, um die Miniaturansicht anzuzeigen.

    ![Indexseite][Werbungsliste]

6.  Klicken Sie auf **Details** für Ihre Werbung, um das Bild in voller Größe anzuzeigen.

    ![Detailseite][Detailseite]

Sie haben die Anwendung auf Ihrem lokalen Computer ausgeführt und eine SQL Server-Datenbank auf Ihrem Computer verwendet. Die Anwendung verwendet jedoch Warteschlangen und Blobs in der Cloud. Im folgenden Abschnitt werden Sie die Anwendung in der Cloud ausführen und eine Cloud-Datenbank sowie Cloud-Blobs und -Warteschlangen verwenden.

## <span id="runincloud"></span></a>Ausführen der Anwendung in der Cloud

Führen Sie folgende Schritte aus, um die Anwendung in der Cloud auszuführen:

-   Bereitstellen in einer Azure-Website. Visual Studio erstellt automatisch eine neue Azure-Website und eine SQL-Datenbankinstanz.
-   Konfigurieren Sie die Website zur Verwendung Ihrer Azure SQL-Datenbank und Ihres Speicherkontos.

Nachdem Sie einige Werbeeinblendungen unter Ausführung in der Cloud erstellt haben, können Sie das WebJobs SDK-Dashboard öffnen, um dessen umfassende Überwachungsfeatures zu erkunden.

### Bereitstellen in einer Azure-Website

1.  Schließen Sie Ihren Browser und das Konsolenfenster.

2.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das ContosoAdsWeb-Projektmappen-Explorer und dann auf **Veröffentlichen**.

3.  Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Azure Web Sites**.

    ![Azure-Website als Veröffentlichungsziel auswählen][Azure-Website als Veröffentlichungsziel auswählen]

4.  Klicken Sie im Feld **Vorhandene Website auswählen** auf **Anmelden**.

    ![Klicken auf Anmelden][Klicken auf Anmelden]

5.  Klicken Sie nach der Anmeldung auf "Neu".

    ![Klicken auf "Neu"][Klicken auf "Neu"]

6.  Geben Sie im Dialogfeld **Site auf Microsoft Azure erstellen** einen eindeutigen Namen in das Feld **Sitename** ein.

    Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und .azurewebsites.net (wie neben dem Textfeld **Sitename** angezeigt). Beispiel: Wenn der Websitename ContosoAds lautet, ist die URL ContosoAds.azurewebsites.net.

7.  Wählen Sie in der Dropdownliste **Region** dieselbe Region aus, die Sie für Ihr Speicherkonto ausgewählt haben.

    Mit dieser Einstellung wird das Azure-Rechenzentrum angegeben, in dem Ihre Website ausgeführt wird. Verwenden Sie dasselbe Rechenzentrum für Website und Speicherkonto, um Latenz und Datenübertragungskosten zu minimieren.

8.  Wählen Sie in der Dropdownliste **Datenbankserver** die Option **Neuen Server erstellen** aus.

    Falls Ihr Konto bereits einen Server enthält, können Sie auch diesen Server aus der Dropdownliste auswählen.

9.  Geben Sie unter **Datenbank-Benutzername** und **Datenbank-Kennwort** die Anmeldedaten eines Administrators ein.

    Wenn Sie **New SQL Database server** ausgewählt haben, geben Sie hier keinen vorhandenen Namen und kein vorhandenes Kennwort ein. Stattdessen definieren Sie jetzt einen neuen Namen und ein neues Kennwort zur späteren Verwendung beim Datenbankzugriff. Wenn Sie einen zuvor erstellten Server ausgewählt haben, werden Sie aufgefordert, das Kennwort für den zuvor erstellten Administratorbenutzer einzugeben.

10. Klicken Sie auf **Erstellen**.

    ![Dialogfeld "Website in Azure erstellen"][Dialogfeld "Website in Azure erstellen"]

    Visual Studio erstellt die Lösung, das Webprojekt, die Azure-Website und die Azure SQL-Datenbankinstanz.

11. Klicken Sie im Schritt **Verbindung** des Assistent **Web veröffentlichen** auf **Weiter**.

    ![Schritt "Verbindung"][Schritt "Verbindung"]

12. Entfernen Sie im Schritt **Einstellungen** das Häkchen in **Diese Verbindungszeichenfolge zur Laufzeit verwenden** und klicken Sie auf **Weiter**.

    ![Schritt "Einstellungen"][Schritt "Einstellungen"]

    Sie müssen die SQL-Verbindungszeichenfolge im Veröffentlichungsdialog nicht eingeben, da Sie diesen Wert später in der Azure-Umgebung setzen werden.

    Sie können die Warnungen auf dieser Seite ignorieren.

    -   Normalerweise verwenden Sie für die Ausführung in Azure ein anderes Speicherkonto als bei der lokalen Ausführung. Dieses Lernprogramm verwendet jedoch dasselbe Speicherkonto für beide Umgebungen. Die AzureWebJobsStorage-Verbindungszeichenfolge muss daher nicht geändert werden. Selbst wenn Sie ein anderes Speicherkonto in der Cloud verwenden wollten, müssen Sie diese Verbindungszeichenfolge nicht ändern, da die Anwendung bei der Ausführung in Azure eine Azure-Umgebungseinstellung verwendet. Dieser Punkt wird später in diesem Lernprogramm behandelt.

    -   Für dieses Lernprogramm nehmen Sie keine Änderungen am Datenmodell für die ContosoAdsContext-Datenbank vor und müssen daher keine Entity Framework Code First-Migrationen für die Bereitstellung verwenden. Code First erstellt automatisch eine neue Datenbank, wenn die Anwendung zum ersten Mal versucht, auf SQL-Daten zuzugreifen.

    Für dieses Lernprogramm können Sie die Standardwerte für die Optionen unter **Dateiveröffentlichungsoptionen** verwenden.

13. Klicken Sie im Schritt **Vorschau** auf **Vorschau starten**.

    ![Klicken auf "Vorschau starten"][Klicken auf "Vorschau starten"]

    Sie können die Warnung ignorieren, dass keine Datenbanken veröffentlicht werden. Die Datenbank wird von Entity Framework Code First erstellt und muss daher nicht veröffentlicht werden.

    Im Vorschaufenster wird angezeigt, dass Binär- und Konfigurationsdateien aus dem WebJob-Projekt in den *app\_data\\jobs\\continuous*-Ordner der Website kopiert werden.

    ![WebJobs-Dateien im Vorschaufenster][WebJobs-Dateien im Vorschaufenster]

14. Klicken Sie auf **Veröffentlichen**.

    Visual Studio stellt die Anwendung bereit und öffnet die URL der Startseite im Browser.

    Sie können die Website jedoch erst verwenden, nachdem Sie die Verbindungszeichenfolgen für die Azure-Umgebung im nächsten Abschnitt konfiguriert haben. Je nach den Website- und Datenbankerstellungsoptionen, die Sie zuvor ausgewählt haben, wird entweder eine Fehlerseite oder die Startseite angezeigt.

### Konfigurieren Sie die Website zur Verwendung Ihrer Azure SQL-Datenbank und Ihres Speicherkontos.

Sie sollten es vermeiden [sensible Daten wie Verbindungszeichenfolgen in Dateien zu lagern, die in Quellcode-Repositorys gespeichert werden][sensible Daten wie Verbindungszeichenfolgen in Dateien zu lagern, die in Quellcode-Repositorys gespeichert werden]. Azure bietet eine Möglichkeit hierzu: Sie können die Verbindungszeichenfolge und andere Einstellungswerte in der Azure-Umgebung setzen, und die Konfigurations-APIs von ASP.NET verwenden automatisch diese Werte, wenn die Anwendung in Azure ausgeführt wird. In diesem Abschnitt setzen Sie die Verbindungszeichenfolgen in Azure.

1.  Öffnen Sie das Azure-Verwaltungsportal in Ihrem Browser und wählen Sie die Website aus, in der Sie die Contoso Ads-Anwendung bereitgestellt haben.

2.  Klicken Sie auf die Registerkarte **Konfigurieren** und blättern Sie nach unten zum Abschnitt **Verbindungszeichenfolgen**.

3.  Ändern Sie den Namen der DefaultConnection-Verbindungszeichenfolge zu ContosoAdsContext.

    Azure hat diese Verbindungszeichenfolge automatisch erstellt, als Sie die Website mit zugehöriger Datenbank erstellt haben, und die Verbindungszeichenfolge enthält bereits den korrekten Wert. Sie ändern also nur den Namen, nach dem Ihr Code suchen soll.

4.  Fügen Sie zwei neue Verbindungszeichenfolgen mit den Namen AzureWebJobsStorage und AzureWebJobsDashboard hinzu. Setzen Sie deren Typ auf "Benutzerdefiniert" und setzen Sie die Verbindungszeichenfolge auf denselben Wert, den Sie zuvor in den Dateien *Web.config* und *App.config* verwendet haben. (Verwenden Sie unbedingt die komplette Verbindungszeichenfolge und nicht nur den Zugriffsschlüssel, und verwenden Sie keine Anführungszeichen.)

    Diese Verbindungszeichenfolgen werden vom WebJobs SDK verwendet, je eine für die Anwendungsdaten und für die Protokollierung. Wie Sie bereits gesehen haben, wird die Verbindungszeichenfolge für die Anwendungsdaten auch vom Code des Web-Front-Ends verwendet.

5.  Klicken Sie auf **Speichern**.

    ![Verbindungszeichenfolgen im Verwaltungsportal][Verbindungszeichenfolgen im Verwaltungsportal]

6.  Klicken Sie auf die Registerkarte **Dashboard** und dann auf **Neu starten**.

    Der WebJob wird bei der Veröffentlichung automatisch gestartet, wird jedoch bei Änderungen an der Konfiguration angehalten. Sie können entweder die Website oder den WebJob neu starten, um dies zu beheben. Nach einer Konfigurationsänderung sollten Sie jedoch im Allgemeinen die Website neu starten.

7.  Aktualisieren Sie das Browserfenster mit der Website-URL in der Adressleiste.

    Die Startseite wird angezeigt.

8.  Erstellen Sie eine Werbung wie bereits zuvor bei der lokalen Ausführung der Anwendung.

    Die Indexseite wird zunächst ohne Miniaturansicht angezeigt.

9.  Aktualisieren Sie die Seite nach wenigen Sekunden, und die Miniaturansicht erscheint.

    Wenn die Miniaturansicht nicht angezeigt wird, kann es sein, dass der WebJob nicht automatisch gestartet wurde. Öffnen Sie in diesem Fall die WebJobs-Registerkarte im

### Anzeigen des WebJobs SDK-Dashboards

1.  Wählen Sie im Azure-Verwaltungsportal Ihre Website aus.

2.  Klicken Sie auf die Registerkarte **WebJobs**.

3.  Klicken Sie auf die URL in der "Protokolle"-Spalte für Ihren WebJob.

    ![Registerkarte WebJobs][Registerkarte WebJobs]
    Das WebJobs SDK-Dashboard wird in einer neuen Browser-Registerkarte geöffnet. Das Dashboard zeigt an, dass der WebJob ausgeführt wird, und enthält außerdem eine Liste der Funktionen in Ihrem Code, die vom WebJobs SDK ausgelöst wurden.

4.  Klicken Sie auf eine der Funktionen, um Details zu deren Ausführung anzuzeigen

    ![WebJobs SDK-Dashboard][WebJobs SDK-Dashboard]

    ![WebJobs SDK-Dashboard][2]

    Mit der Schaltfläche **Funktion abspielen** auf dieser Seite können Sie veranlassen, dass das WebJobs SDK-Framework die Funktion erneut aufruft, und können die an die Funktion übergebenen Daten zuvor ändern.

> [WACOM.NOTE] Nach Abschluss der Tests sollten Sie die Website und die SQL-Datenbankinstanz löschen. Die Website ist kostenlos, aber für die SQL-Datenbankinstanz und das Speicherkonto fallen Kosten an (minimal aufgrund der kleinen Größe). Wenn Sie die Website laufen lassen, kann außerdem jeder, der die URL findet, Werbungen erstellen und anzeigen. Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Dashboard** für Ihre Website, und klicken Sie am unteren Seitenrand auf **Löschen**. Dort finden Sie auch ein Kontrollkästchen, um die SQL-Datenbankinstanz ebenfalls zu löschen. Klicken Sie stattdessen auf **Anhalten**, um vorübergehend zu verhindern, dass andere Personen auf die Website zugreifen. In diesem Fall entstehen weiterhin Kosten für SQL-Datenbank und Speicherkonto. Sie können die SQL-Datenbank und das Speicherkonto auf ähnliche Weise löschen, wenn Sie diese nicht mehr benötigen.

## <span id="create"></span></a>Erstellen der Anwendung von Grund auf

In diesem Abschnitt werden Sie die folgenden Aufgaben ausführen:

-   Erstellen Sie eine Visual Studio-Lösung mit einem Webprojekt.
-   Fügen Sie ein Klassenbibliotheks-Projekt für die Datenzugriffsebene hinzu, das von Front-End und Back-End gemeinsam genutzt wird.
-   Fügen Sie ein Konsolenanwendungsprojekt für das Back-End hinzu und aktivieren Sie die WebJobs-Bereitstellung.
-   NuGet-Pakete hinzufügen.
-   Projektverweise setzen.
-   Kopieren Sie den Anwendungscode und die Konfigurationsdateien aus der heruntergeladenen Anwendung, mit der Sie im vorherigen Abschnitt dieses Lernprogramms gearbeitet haben.
-   Prüfen Sie die Codeabschnitte, die Azure-Blobs, Warteschlangen und das WebJobs SDK verwenden.

### Erstellen einer Visual Studio-Lösung mit Webprojekt und Klassenbibliotheks-Projekt

1.  Klicken Sie in Visual Studio auf **Neu** \> **Projekt** im Menü **Datei**.

2.  Klicken Sie im Dialogfeld **Neues Projekt** auf **Visual C#** \> **Web** \> **ASP.NET-Webanwendung**.

3.  Nennen Sie das Projekt ContosoAdsWeb und die Lösung ContosoAdsWebJobsSDK (ändern Sie den Namen der Lösung, falls Sie diese im gleichen Ordner wie die heruntergeladene Lösung ablegen), und klicken Sie auf **OK**.

    ![Neues Projekt][Neues Projekt]

4.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die MVC-Vorlage aus und entfernen Sie das Häkchen im Kontrollkästchen **In der Cloud hosten** unter **Microsoft Azure**.

    Wenn Sie **In der Cloud hosten** auswählen, erstellt Visual Studio automatisch eine neue Azure-Website und eine SQL-Datenbank. Sie haben diese Komponenten bereits zuvor erstellt und müssen dies bei der Projekterstellung daher nicht erneut tun. Markieren Sie das Kontrollkästchen, um die Komponenten neu zu erstellen. Anschließend können Sie die neue Website und die SQL-Datenbank auf dieselbe Weise konfigurieren, wie Sie dies zuvor beim Bereitstellen der Anwendung getan haben.

5.  Klicken Sie auf **Authentifizierung ändern**.

    ![Authentifizierung ändern][Authentifizierung ändern]

6.  Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

    ![Keine Authentifizierung][Keine Authentifizierung]

7.  Klicken Sie im Dialogfeld **Neues ASP.Net-Projekt** auf **OK**.

    Visual Studio erstellt die Lösung und das Webprojekt.

8.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Lösung (nicht auf das Projekt) und klicken Sie auf **Hinzufügen** \> **Neues Projekt**.

9.  Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **Visual C#** \> **Windows Desktop** \> **Klassenbibliothek** aus.

10. Benennen Sie das Projekt *ContosoAdsCommon* und klicken Sie auf **OK**.

    Das Projekt wird den Entity Framework-Kontext und das Datenmodell enthalten, die sowohl vom Front-End als auch vom Back-End verwendet werden. Alternativ könnten Sie die EF-verwandten Klassen im Webprojekt definieren und im WebJob-Projekt auf dieses Projekt verweisen. In diesem Fall würde Ihr WebJob-Projekt jedoch auf Web-Assemblys verweisen, die es nicht benötigt.

### Hinzufügen eines Konsolenanwendungsprojekts und Aktivieren der WebJobs-Bereitstellung

1.  Klicken Sie mit der rechten Maustaste auf das Webprojekt (nicht auf die Lösung oder das Klassenbibliotheks-Projekt). Klicken Sie auf **Hinzufügen** \> **Neues Azure-WebJob-Projekt**.

    ![Menüauswahl "Neues Azure-WebJob-Projekt"][Menüauswahl "Neues Azure-WebJob-Projekt"]

2.  Geben Sie im Dialogfeld **Azure-WebJob hinzufügen** unter **Projektname** und **WebJob-Name** den Wert ContosoAdsWebJob ein. Behalten Sie unter **WebJob-Ausführungsmodus** den Wert **Fortlaufend ausführen** bei.

3.  Klicken Sie auf **OK**.

    Visual Studio erstellt eine Konsolenanwendung, die automatisch als Azure WebJob bereitgestellt wird, wenn Sie das Webprojekt bereitstellen. Dazu werden die folgenden Schritte nach der Projekterstellung ausgeführt:

    -   Hinzufügen einer *webjob-publish-settings.json*-Datei im Eigenschaftenordner des WebJob-Projekts.
    -   Hinzufügen einer *webjobs-list.json*-Datei im Eigenschaftenordner des Webprojekts.
    -   Installieren des NuGet-Pakets "Microsoft.Web.WebJobs.Publish" im WebJob-Projekt.

    Weitere Informationen zu diesen Änderungen finden Sie unter [Bereitstellen von WebJobs in Visual Studio][Bereitstellen von WebJobs in Visual Studio].

### NuGet-Pakete hinzufügen

Installieren Sie zunächst das WebJobs SDK im WebJob-Projekt.

1.  Öffnen Sie das Dialogfeld **NuGet-Pakete verwalten** für die Lösung.

2.  Klicken Sie im linken Bereich auf **Online**.

3.  Ändern Sie **Nur stabile Versionen** zu **Vorabversion einschließen**.

4.  Suchen Sie das NuGet-Paket *Microsoft.Azure.WebJobs* und installieren Sie es im ContosoAdsWebJob-Projekt.

    ![Suchen des WebJobs SDK-Pakets][Suchen des WebJobs SDK-Pakets]

    ![Installieren des WebJobs SDK-Pakets nur im WebJob-Projekt][Installieren des WebJobs SDK-Pakets nur im WebJob-Projekt]

    Dabei werden andere abhängige Pakete installiert, darunter ein weiteres WebJobs SDK-Paket, *Microsoft.Jobs.Core*. (Sie erstellen das zweite WebJobs SDK-Paket nur, wenn Sie Ihre Benutzerfunktionen in einer separaten DLL bereitstellen. Für dieses Lernprogramm schreiben Sie Ihren gesamten Code als Konsolenanwendung.) Sie benötigen die Azure-Speicherclient-Bibliothek (SCL) für die Arbeit mit Warteschlangen und Blobs im Webprojekt und im WebJob-Projekt.

Das Paket für die Azure-Speicherclient-Bibliothek (SCL) wird automatisch im WebJob-Projekt als Abhängigkeit des WebJobs SDK installiert. Sie benötigen dieses Paket jedoch auch für die Arbeit mit Blobs und Warteschlangen im Webprojekt.

1.  Klicken Sie im linken Bereich auf **Installierte Pakete**.

2.  Wählen Sie das Paket *Azure-Speicher* aus und klicken Sie auf **Verwalten**.

3.  Markieren Sie im Dialogfeld **Projekte auswählen** das Kontrollkästchen **ContosoAdsWeb** und klicken Sie auf **OK**.

Alle drei Projekte verwenden das Entity Framework für die Arbeit mit Daten in SQL-Datenbanken.

1.  Klicken Sie im linken Bereich auf **Online**.

2.  Ändern Sie **Vorabversionen einschließen** zu **Nur stabile Versionen**.

3.  Suchen Sie das NuGet-Paket *EntityFramework* und installieren Sie es in allen drei Projekten.

### Setzen von Projektverweisen

Sowohl das Web- als auch das WebJob-Projekt verwenden die SQL-Datenbank. Daher benötigen beide Projekte einen Verweis auf das ContosoAdsCommon-Projekt.

1.  Fügen Sie im Projekt ContosoAdsWeb einen Verweis auf das Projekt ContosoAdsCommon ein. Klicken Sie mit der rechten Maustaste auf das ContosoAdsWeb-Projekt und klicken Sie auf **Hinzufügen** \> **Verweis**. Wählen Sie im Dialogfeld **Verweis-Manager** im linken Bereich **Lösung** \> **Projekte** \> **ContosoAdsCommon** aus und klicken Sie auf **OK**.)

2.  Fügen Sie im Projekt ContosoAdsWebJob einen Verweis auf das Projekt ContosoAdsCommon ein.

Das WebJob-Projekt benötigt Verweise für die Arbeit mit Bildern und für den Zugriff auf Verbindungszeichenfolgen.

1.  Fügen Sie im Projekt ContosoAdsWebJob Verweise auf `System.Drawing` und `System.Configuration` ein.

### Hinzufügen von Code und Konfigurationsdateien

Dieses Lernprogramm befasst sich nicht mit der [Erstellung von MVC-Controller und -Ansichten mithilfe eines Gerüsts][ASP.NET MVC], dem [Entity Framework-Code für die Arbeit mit SQL Server-Datenbanken][Entity Framework-Code für die Arbeit mit SQL Server-Datenbanken] und den [Grundsätzen asynchroner Programmierung in ASP.NET 4.5][Grundsätzen asynchroner Programmierung in ASP.NET 4.5]. Daher müssen Sie nur noch den Code und die Konfigurationsdateien aus der heruntergeladenen Lösung in die neue Lösung kopieren. Anschließend finden Sie in den folgenden Abschnitten Hinweise zu den wichtigsten Bereichen des Codes.

Um Dateien zu einem Projekt oder einem Ordner hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Projekt bzw. den Ordner und klicken dann auf **Hinzufügen** \> **Vorhandenes Element**. Wählen Sie die gewünschten Dateien aus und klicken Sie auf **Hinzufügen**. Klicken Sie auf **Ja**, wenn Sie gefragt werden, ob Sie vorhandene Dateien ersetzen möchten.

1.  Löschen Sie im Projekt ContosoAdsCommon die Datei *Class1.cs* und fügen Sie an deren Stelle die folgenden Dateien aus dem heruntergeladenen Projekt hinzu.

    -   *Ad.cs*
    -   *ContosoAdscontext.cs*
    -   *BlobInformation.cs*

2.  Fügen Sie im Projekt ContosoAdsWeb die folgenden Dateien aus dem heruntergeladenen Projekt hinzu.

    -   *Web.config*
    -   *Global.asax.cs*
    -   Im Ordner *Controllers*: *AdController.cs*
    -   Im Ordner *Views\\Shared*: Datei *\_Layout.cshtml*.
    -   Im Ordner *Views\\Home*: *Index.cshtml*.
    -   Im Ordner *Views\\Ad* (erstellen Sie den Ordner zunächst): fünf *.cshtml*-Dateien.

3.  Fügen Sie im Projekt ContosoAdsWebJob die folgenden Dateien aus dem heruntergeladenen Projekt hinzu.

    -   *App.config* (ändern Sie den Dateityp-Filter zu **Alle Dateien**)
    -   *Program.cs*

Sie können die Anwendung nun anhand der zuvor in diesem Lernprogramm beschriebenen Prozedur erstellen, ausführen und bereitstellen. Zuvor müssen Sie jedoch den WebJob anhalten, der immer noch in der ersten Website ausgeführt wird, in der Sie ihn bereitgestellt haben. Andernfalls wird dieser WebJob Warteschlangen-Nachrichten verarbeiten, die lokal oder von der Anwendung in einer neuen Website erstellt wurden, da alle Komponenten dasselbe Speicherkonto verwenden.

## <span id="code"></span></a>Überprüfen des Anwendungscodes

Die folgenden Abschnitte beschreiben den Code für die Arbeit mit dem WebJobs SDK und Azure-Speicher-Blobs und Warteschlangen. Den speziellen Code für das WebJobs SDK finden Sie im Abschnitt zur Datei [Program.cs][Program.cs].

### ContosoAdsCommon - Ad.cs

Die Datei Ad.cs definiert eine Enumeration für Werbekategorien und eine POCO-Entitätsklasse für Werbeinformationen.

        public enum Category
        {
            Cars,
            [Display(Name="Real Estate")]
            RealEstate,
            [Display(Name = "Free Stuff")]
            FreeStuff
        }

        public class Ad
        {
            public int AdId { get; set; }

            [StringLength(100)]
            public string Title { get; set; }

            public int Price { get; set; }

            [StringLength(1000)]
            [DataType(DataType.MultilineText)]
            public string Description { get; set; }

            [StringLength(1000)]
            [DisplayName("Full-size Image")]
            public string ImageURL { get; set; }

            [StringLength(1000)]
            [DisplayName("Thumbnail")]
            public string ThumbnailURL { get; set; }

            [DataType(DataType.Date)]
            [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
            public DateTime PostedDate { get; set; }

            public Category? Category { get; set; }
            [StringLength(12)]
            public string Phone { get; set; }
        }

### ContosoAdsCommon - ContosoAdsContext.cs

Die Klasse ContosoAdsContext gibt an, dass die Ad-Klasse in einer DbSet-Sammlung verwendet wird, die von Entity Framework in einer SQL-Datenbank gespeichert wird.

        public class ContosoAdsContext : DbContext
        {
            public ContosoAdsContext() : base("name=ContosoAdsContext")
            {
            }
            public ContosoAdsContext(string connString)
                : base(connString)
            {
            }
            public System.Data.Entity.DbSet<Ad> Ads { get; set; }
        }

Die Klasse besitzt zwei Konstruktoren. Der erste Konstruktor wird vom Webprojekt verwendet und gibt den Namen einer Verbindungszeichenfolge an, die in der Datei Web.config oder in der Azure-Laufzeitumgebung gespeichert ist. Mit dem zweiten Konstruktor können Sie die eigentliche Verbindungszeichenfolge übergeben. Diese wird vom WebJob-Projekt benötigt, da dieses keine eigene Web.config-Datei hat. Sie haben weiter oben gesehen, wo diese Verbindungszeichenfolge gespeichert wird, und weiter unten werden Sie sehen, wie der Code die Verbindungszeichenfolge beim Instanziieren der DbContext-Klasse abruft.

### ContosoAdsCommon - BlobInformation.cs

Die `BlobInformation`-Klasse speichert Informationen über ein Bild-Blob in einer Warteschlangen-Nachricht.

        public class BlobInformation
        {
            public Uri BlobUri { get; set; }
            
            public string BlobName
            {
                get
                {
                    return BlobUri.Segments[BlobUri.Segments.Length - 1];
                }
            }
            public string BlobNameWithoutExtension
            {
                get
                {
                    return Path.GetFileNameWithoutExtension(BlobName);
                }
            }
            public int AdId { get; set; }
        }

### ContosoAdsWeb - Global.asax.cs

Der aus der `Application_Start`-Methode aufgerufene Code erstellt einen *images*-Blob-Container und eine *images*-Warteschlange, falls diese noch nicht existieren. Damit wird sichergestellt, dass Blob-Container und Warteschlange immer automatisch erstellt werden, wenn Sie ein neues Speicherkonto verwenden.

Der Code verwendet die Speicher-Verbindungszeichenfolge aus der *Web.config*-Datei oder aus der Azure-Laufzeitumgebung für den Zugriff auf das Speicherkonto.

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Anschließend ruft er einen Verweis auf den *images*-Blob-Container ab, erstellt den Container, falls dieser noch nicht existiert, und setzt die Zugriffsberechtigungen für den neuen Container. Standardmäßig erlauben neue Container nur Clients mit Speicherkonto-Anmeldeinformationen den Zugriff auf Blobs. Für die Website müssen die Blobs öffentlich sein, um die Bilder mithilfe der URLs anzuzeigen, die auf die Bild-Blobs zeigen.

        var blobClient = storageAccount.CreateCloudBlobClient();
        var imagesBlobContainer = blobClient.GetContainerReference("images");
        if (imagesBlobContainer.CreateIfNotExists())
        {
            imagesBlobContainer.SetPermissions(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
        }

Ein ähnlicher Codeabschnitt ruft einen Verweis auf die *blobnamerequest*-Warteschlange ab und erstellt eine neue Warteschlange. In diesem Fall sind keine Berechtigungsänderungen erforderlich. Der Abschnitt [ResolveBlobName][ResolveBlobName] in diesem Lernprogramm erläutert, warum die Warteschlange, in die die Webanwendung schreibt, nur zum Abrufen der Blobnamen verwendet wird, und nicht für die Generierung der Miniaturansichten.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
        imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

Die *\_Layout.cshtml*-Datei setzt den App-Namen in Kopf- und Fußzeile und erstellt einen "Ads"-Menüeintrag.

### ContosoAdsWeb - Views\\Home\\Index.cshtml

Die *Views\\Home\\Index.cshtml*-Datei zeigt Links zu den Kategorien auf der Startseite an. Die Links übergeben den ganzzahligen Wert aus der `Category`-Enumeration in einer querystring-Variable an die Ads-Indexseite.

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

In der *AdController.cs*-Datei ruft der Konstruktor die `InitializeStorage`-Methode auf, um Objekte der Azure-Speicherclient-Bibliothek zu erstellen, die eine API für die Arbeit mit Blobs und Warteschlangen bereitstellen.

Anschließend ruft der Code einen Verweis auf den *images*-Blob-Container ab, wie weiter oben in *Global.asax.cs* gezeigt. Gleichzeitig wird eine angemessene Standard-[Wiederholungs-Richtlinie][Wiederholungs-Richtlinie] für eine Webanwendung gesetzt. Mit der Standardrichtlinie (exponentiell ansteigende Wartezeiten) kann es passieren, dass die Webanwendung im Fall eines vorübergehenden Fehlers über eine Minute lang stehen bleibt. Die hier gezeigte Richtlinie wartet 3 Sekunden nach jedem Versuch für bis zu 3 Versuche.

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

Ein ähnlicher Codeteil ruft einen Verweis auf die *images*-Warteschlange ab.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

Ein Großteil des Controller-Codes ist typisch für die Arbeit mit einem Entity Framework-Datenmodell und einer DbContext-Klasse. Eine Ausnahme ist die HttpPost `Create`-Methode, die eine Datei hochlädt und im Blobspeicher ablegt. Die Modellbindung stellt ein [HttpPostedFileBase][HttpPostedFileBase]-Objekt für die Methode bereit.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(
            [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
            HttpPostedFileBase imageFile)

Wenn ein Benutzer eine hochzuladende Datei auswählt, lädt der Code die Datei hoch, speichert sie in einem Blob und aktualisiert den Ad-Datenbankeintrag mit einer URL, die auf den Blob zeigt.

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            blob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = blob.Uri.ToString();
        }

Der Code für den Upload befindet sich in der `UploadAndSaveBlobAsync`-Methode. Er erstellt einen GUID-Namen für den Blob, lädt die Datei hoch und speichert sie, und gibt einen Verweis auf den gespeicherten Blob zurück.

        private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
        {
            string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
            CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
            using (var fileStream = imageFile.InputStream)
            {
                await imageBlob.UploadFromStreamAsync(fileStream);
            }
            return imageBlob;
        }

Nachdem die HttpPost `Create`-Methode einen Blob hochgeladen und die Datenbank aktualisiert hat, wird eine Nachricht in der Warteschlange erstellt, um den Back-End-Prozess darüber zu informieren, dass ein Bild für die Konvertierung zu einer Miniaturansicht vorliegt.

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

Der Code für die HttpPost `Edit`-Methode ist ähnlich, nur müssen hier alle vorhandenen Blobs für diese Werbung gelöscht werden, wenn der Benutzer eine neue Bilddatei auswählt.

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            await DeleteAdBlobsAsync(ad);
            imageBlob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = imageBlob.Uri.ToString();
        }

Hier sehen Sie den Code, der Blobs löscht, wenn Sie eine Werbung löschen:

        private async Task DeleteAdBlobsAsync(Ad ad)
        {
            if (!string.IsNullOrWhiteSpace(ad.ImageURL))
            {
                Uri blobUri = new Uri(ad.ImageURL);
                await DeleteAdBlobAsync(blobUri);
            }
            if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
            {
                Uri blobUri = new Uri(ad.ThumbnailURL);
                await DeleteAdBlobAsync(blobUri);
            }
        }
        private static async Task DeleteAdBlobAsync(Uri blobUri)
        {
            string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
            CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
            await blobToDelete.DeleteAsync();
        }

### ContosoAdsWeb - Views\\Ad\\Index.cshtml und Details.cshtml

Die Datei *Index.cshtml* zeigt Miniaturansichten zusammen mit den restlichen Werbedaten an:

        <img  src="@Html.Raw(item.ThumbnailURL)" />

Die Datei *Details.cshtml* zeigt das Bild in voller Größe an:

        <img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\\Ad\\Create.cshtml und Edit.cshtml

Die Dateien *Create.cshtml* und *Edit.cshtml* geben die Formularcodierung an, mit der der Controller das `HttpPostedFileBase`-Objekt abruft.

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Ein `<input>`-Element weist den Browser an, ein Dateiauswahl-Dialogfeld zu öffnen.

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <span id="programcs"></span></a>ContosoAdsWebJob - Program.cs - Main- und Initialisierungsmethoden

Wenn der WebJob gestartet wird, ruft die `Main`-Methode `Initialize` auf, um den Entity Framework-Datenbankkontext zu instanziieren. Anschließend wird die `JobHost.RunAndBlock`-Methode im WebJobs SDK aufgerufen, um mit der Singlethread-Ausführung der ausgelösten Funktionen im aktuellen Thread zu beginnen.

        static void Main(string[] args)
        {
            Initialize();
        
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
        private static void Initialize()
        {
            db = new ContosoAdsContext();
        }

### <span id="generatethumbnail"></span></a>ContosoAdsWebJob - Program.cs - GenerateThumbnail-Methode

Das WebJobs SDK ruft diese Methode auf, wenn eine Warteschlangen-Nachricht empfangen wird. Die Methode gibt eine Miniaturansicht zurück und legt die URL der Miniaturansicht in der Datenbank ab.

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }
        
            var id = blobInfo.AdId;
            Ad ad = db.Ads.Find(id);
            if (ad == null)
            {
                throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
            }
            ad.ThumbnailURL = outputBlob.Uri.ToString();
            db.SaveChanges();
        }

-   Das `QueueTrigger`-Attribut weist das WebJobs SDK an, diese Methode aufzurufen, wenn eine neue Nachricht in der thumbnailrequest-Warteschlange empfangen wird.

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    Das `BlobInformation`-Objekt in der Warteschlangen-Nachricht wird automatisch in den `blobInfo`-Parameter deserialisiert. Nach Abschluss der Methode wird die Warteschlangen-Nachricht gelöscht. Wenn vor Abschluss der Methode ein Fehler auftritt, wird die Warteschlangen-Nachricht nicht gelöscht. Nachdem eine 10-minütige Sperre abläuft, wird die Nachricht freigegeben und kann erneut abgerufen und verarbeitet werden. Diese Sequenz wird nicht unbegrenzt wiederholt, wenn eine Nachricht immer eine Ausnahme auslöst. Nach 5 fehlgeschlagenen Versuchen, eine Nachricht zu verarbeiten, wird die Nachricht in eine Warteschlange mit dem Namen "{Warteschlangenname}-poison" verschoben. Die maximale Anzahl möglicher Versuche ist konfigurierbar.

-   Die beiden `Blob`-Attribute enthalten Objekte, die an Blobs gebunden werden: Ein Objekt für den vorhandenen Bild-Blob und ein Objekt für den neuen Miniaturansicht-Blob, der von der Methode erstellt wird.

        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Die Blobnamen stammen aus den Eigenschaften des `BlobInformation`-Objekts, das in der Warteschlangen-Nachricht empfangen wurde (`BlobName` und `BlobNameWithoutExtension`). Sie können die `CloudBlockBlob`-Klasse für die Arbeit mit Blobs verwenden, um den kompletten Funktionsumfang der Speicherclient-Bibliothek zu nutzen. Falls Sie Code wiederverwenden möchten, der für die Arbeit mit `Stream`-Objekten geschrieben wurde, können Sie die `Stream`-Klasse verwenden.

> [WACOM.NOTE]
>
> -   Falls Ihre Website auf mehreren VMs läuft, wird dieses Programm auf allen Computern ausgeführt, und jeder Computer wartet auf Auslöser und versucht, Funktionen auszuführen. In manchen Szenarien kann dies dazu führen, dass manche Funktionen dieselben Daten doppelt verarbeiten. Die Funktionen sollten daher "idempotent" geschrieben sein, d. h. beim wiederholten Aufrufen mit denselben Eingangsdaten dürfen keine duplizierten Resultate entstehen.
> -   Weitere Informationen zum ordnungsgemäßen Herunterfahren finden Sie in der [WebJobs SDK 0.3.0 Beta-Ankündigung][WebJobs SDK 0.3.0 Beta-Ankündigung].
> -   Der Code in der `ConvertImageToThumbnailJPG`-Methode (nicht gezeigt) verwendet Klassen aus dem `System.Drawing`-Namespace zur Vereinfachung. Die Klassen in diesem Namespace wurden jedoch für den Einsatz mit Windows Forms entwickelt. Sie werden nicht für die Verwendung in einem Windows- oder ASP.NET-Dienst unterstützt.

### Vergleich: WebJobs und Clouddienst-Workerrollen

Wenn Sie die Menge an Code in der `GenerateThumbnails`-Methode in dieser Beispielanwendung mit dem Workerrollen-Code in der [Clouddienst-Version der Anwendung][Clouddienst-Version der Anwendung] vergleichen, können Sie erkennen, wie viel Arbeit das WebJobs SDK Ihnen tatsächlich abnimmt. Der tatsächliche Vorteil ist sogar noch größer, da der Code der Clouddienst-Beispielanwendung nicht alle Aufgaben erledigt (z. B. die Behandlung nicht verarbeitbarer Nachrichten), die sie in einer Produktionsanwendung implementieren würden, während das WebJobs SDK diese Aufgaben ausführt.

Die Clouddienst-Version der Beispielanwendung enthält eine `ProcessQueueMessage`-Methode, die die folgenden Aufgaben ausführt:

-   Abrufen der ID des Datenbankeintrags aus der Warteschlangen-Nachricht.
-   Lesen der Blob-URL aus der Datenbank.
-   Konvertieren des Bild zu einer Miniaturansicht und Speichern der Miniaturansicht in einem neuen Blob.
-   Aktualisieren des Datenbankeintrags durch Hinzufügen der URL des Miniaturansicht-Blobs.

In der Clouddienst-Version der Anwendung ist die Eintrags-ID die einzige Information in der Warteschlangen-Nachricht, und der Hintergrundprozess ruft die Bild-URL aus der Datenbank ab. In der WebJobs SDK-Version der Anwendung enthält die Warteschlangen-Nachricht die Bild-URL, die an die `Blob`-Attribute weitergegeben werden kann. Ohne die Blob-URL in der Warteschlangen-Nachricht müssten Sie Code in der Methode schreiben, die die Blob-URL abruft, und dann müssten Sie Code schreiben, um Objekte an Ein- und Ausgabe-Blobs zu binden, anstelle diese Aufgaben vom WebJobs SDK erledigen zu lassen.

### Verwenden des WebJobs SDK in einer Workerrolle

Sie haben gesehen, dass Programme, die das WebJobs SDK verwenden, nicht in einem WebJob in Azure ausgeführt werden müssen. Diese Programme können lokal oder auch in einer Workerrolle ausgeführt werden. Sie erreichen das WebJobs SDK-Dashboard jedoch nur über eine Azure-Website. Um das Dashboard zu verwenden, müssen Sie die Website mit dem Speicherkonto verbinden, indem Sie "AzureWebJobsDashboard"-Verbindungszeichenfolge in der Registerkarte "Konfigurieren" im Verwaltungsportal setzen. Anschließend finden Sie das Dashboard unter der URL [https://{Websitename}.scm.azurewebsites.net/azurejobs/\#/functions][https://{Websitename}.scm.azurewebsites.net/azurejobs/\#/functions]. Weitere Informationen finden Sie unter [Einrichten eines Dashboards für die lokale Entwicklung mit dem WebJobs SDK][Einrichten eines Dashboards für die lokale Entwicklung mit dem WebJobs SDK]. Dieser Artikel enthält jedoch einen veralteten Namen für die Verbindungszeichenfolge.

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie eine einfache Anwendung mit mehreren Ebenen erstellen können, die das WebJobs SDK für die Hintergrundverarbeitung verwendet. Die Anwendung wurde für dieses Lernprogramm bewusst einfach gehalten. Sie implementiert z. B. keine [Abhängigkeitsinjektion][Abhängigkeitsinjektion] oder [Repository und Arbeitseinheit][Repository und Arbeitseinheit], verwendet keine [Schnittstelle für die Protokollierung][Schnittstelle für die Protokollierung] und keine [EF Code First-Migrationen][EF Code First-Migrationen] zur Verwaltung von Datenmodellen oder [EF-Verbindungsstabilität][EF-Verbindungsstabilität] für vorübergehende Verbindungsfehler, usw.

Weitere Beispiele für die Verwendung des WebJobs SDK in anderen Szenarien finden Sie im [AzureWebJobs][AzureWebJobs]-Ordner im ASP.NET CodePlex-Projekt.

Wenn WebJobs in einer Website ausgeführt werden, können Sie die Problembehandlung im Debugmodus aus der Ferne exakt wie für Ihre Websites ausführen. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio][Problembehandlung von Azure-Websites in Visual Studio]. Sie müssen den WebJob-Prozess manuell einbinden. Weitere Informationen hierzu finden Sie in den Visual Studio 2012-Anweisungen im Lernprogramm.

Weitere Informationen finden Sie unter [Azure WebJobs - Empfohlene Ressourcen][Azure WebJobs - Empfohlene Ressourcen].

  [Azure-Website]: /de-de/documentation/articles/fundamentals-application-models/#WebSites
  [Azure SQL-Datenbank]: http://msdn.microsoft.com/library/azure/ee336279
  [Azure-Blob-Dienst]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Azure-Warteschlangendienst]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Werbungsliste]: ./media/websites-dotnet-webjobs-sdk-get-started/list.png
  [Visual Studio-Projekt]: http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb
  [Voraussetzungen]: #prerequisites
  [Sie lernen Folgendes]: #learn
  [Das WebJobs SDK]: #webjobssdk
  [Anwendungsarchitektur]: #contosoads
  [Einrichten der Entwicklungsumgebung]: #setupdevenv
  [Erstellen, Ausführen und Bereitstellen der Anwendung]: #storage
  [Erstellen der Anwendung von Grund auf]: #create
  [Überprüfen des Anwendungscodes]: #code
  [Problembehandlung]: #troubleshoot
  [Nächste Schritte]: #next-steps
  [ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Web Forms]: http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview
  [free-trial-note]: ../includes/free-trial-note.md
  [WebJobs]: /de-de/documentation/articles/web-sites-create-web-jobs/
  [AlwaysOn]: http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx
  [Ad-Tabelle]: ./media/websites-dotnet-webjobs-sdk-get-started/adtable.png
  [Contoso Ads-Architektur]: ./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png
  [Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure]: /de-de/documentation/articles/choose-web-site-cloud-service-vm/
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [How to Manage Storage Accounts]: /de-de/documentation/articles/storage-manage-storage-account/
  [Neues Speicherkonto]: ./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png
  [SQL Server Express LocalDB]: http://msdn.microsoft.com/de-de/library/hh510202.aspx
  [1]: http://manage.windowsazure.com/
  [Schaltfläche "Zugriffsschlüssel verwalten"]: ./media/websites-dotnet-webjobs-sdk-get-started/mak.png
  [Dialogfeld "Zugriffsschlüssel verwalten"]: ./media/websites-dotnet-webjobs-sdk-get-started/cpak.png
  [Contoso Ads-Startseite]: ./media/websites-dotnet-webjobs-sdk-get-started/home.png
  [Konsolenfenster mit Hinweis, dass das Back-End ausgeführt wird]: ./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png
  [Seite erstellen]: ./media/websites-dotnet-webjobs-sdk-get-started/create.png
  [Konsolenfenster mit Hinweis, dass eine Warteschlangen-Nachricht verarbeitet wurde]: ./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png
  [Detailseite]: ./media/websites-dotnet-webjobs-sdk-get-started/details.png
  [Azure-Website als Veröffentlichungsziel auswählen]: ./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png
  [Klicken auf Anmelden]: ./media/websites-dotnet-webjobs-sdk-get-started/signin.png
  [Klicken auf "Neu"]: ./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png
  [Dialogfeld "Website in Azure erstellen"]: ./media/websites-dotnet-webjobs-sdk-get-started/newdb.png
  [Schritt "Verbindung"]: ./media/websites-dotnet-webjobs-sdk-get-started/connstep.png
  [Schritt "Einstellungen"]: ./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png
  [Klicken auf "Vorschau starten"]: ./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png
  [WebJobs-Dateien im Vorschaufenster]: ./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png
  [sensible Daten wie Verbindungszeichenfolgen in Dateien zu lagern, die in Quellcode-Repositorys gespeichert werden]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets
  [Verbindungszeichenfolgen im Verwaltungsportal]: ./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png
  [Registerkarte WebJobs]: ./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png
  [WebJobs SDK-Dashboard]: ./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png
  [2]: ./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png
  [Neues Projekt]: ./media/websites-dotnet-webjobs-sdk-get-started/newproject.png
  [Authentifizierung ändern]: ./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png
  [Keine Authentifizierung]: ./media/websites-dotnet-webjobs-sdk-get-started/noauth.png
  [Menüauswahl "Neues Azure-WebJob-Projekt"]: ./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png
  [Bereitstellen von WebJobs in Visual Studio]: /de-de/documentation/articles/websites-dotnet-deploy-webjobs/
  [Suchen des WebJobs SDK-Pakets]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg.png
  [Installieren des WebJobs SDK-Pakets nur im WebJob-Projekt]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg2.png
  [Entity Framework-Code für die Arbeit mit SQL Server-Datenbanken]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc
  [Grundsätzen asynchroner Programmierung in ASP.NET 4.5]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [Program.cs]: #programcs
  [ResolveBlobName]: #resolveblobname
  [Wiederholungs-Richtlinie]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
  [HttpPostedFileBase]: http://msdn.microsoft.com/de-de/library/system.web.httppostedfilebase.aspx
  [WebJobs SDK 0.3.0 Beta-Ankündigung]: http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/
  [Clouddienst-Version der Anwendung]: /de-de/documentation/articles/cloud-services-dotnet-get-started/
  [Einrichten eines Dashboards für die lokale Entwicklung mit dem WebJobs SDK]: http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx
  [Abhängigkeitsinjektion]: http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection
  [Repository und Arbeitseinheit]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo
  [Schnittstelle für die Protokollierung]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log
  [EF Code First-Migrationen]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
  [EF-Verbindungsstabilität]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
  [AzureWebJobs]: http://aspnet.codeplex.com/SourceControl/latest#Samples/AzureWebJobs/ReadMe.txt
  [Problembehandlung von Azure-Websites in Visual Studio]: /de-de/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
  [Azure WebJobs - Empfohlene Ressourcen]: http://go.microsoft.com/fwlink/?LinkId=390226
