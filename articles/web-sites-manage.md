<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage web sites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal web site management pages in Microsoft Azure. Details are provided for each web site management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Web Sites" authors="timamm" solutions="" writer="timamm" manager="" editor="" />

Verwalten von Websites
======================

Sie können Websites im Azure-Portal mithilfe von mehreren Seiten oder "Registerkarten" verwalten. Alle Website-Verwaltungsseiten werden unten beschrieben.

QuickStart
----------

Die Verwaltungsseite **QuickStart** umfasst die folgenden Abschnitte:

-   **Get the tools** - Bietet Links zur [Installation von WebMatrix](http://go.microsoft.com/fwlink/?LinkID=226244) und zum [Microsoft Azure SDK](http://go.microsoft.com/fwlink/?LinkId=246928).
-   **Publish your app** - Bietet Links zum Herunterladen des Website-Veröffentlichungsprofils, zum Zurücksetzen der Bereitstellungsanmeldeinformationen für die Website, Hinzufügen eines phasenweisen Bereitstellungs-Steckplatzes auf einer Website ohne Staging sowie Informationen zum phasenweisen Veröffentlichen (Staging).
-   **Integrate source control** - Bereitstellung von einem Quellcode-Verwaltungstool oder Websites wie TFS, CodePlex, GitHub, Dropbox, Bitbucket oder lokales Git einrichten und verwalten.

Dashboard
---------

Die Verwaltungsseite **Dashboard** umfasst die folgenden Abschnitte:

-   Ein Diagramm mit einer Zusammenfassung der Website-Nutzung zur Messung bestimmter Kennzahlen.
 - **CPU Time** - Messung der CPU-Auslastung der Website.
 - **Data In** - Messung der Daten, die auf der Website von Clients empfangen wurden.
 - **Data Out** - Messung der Daten, die von der Website an Clients gesendet wurden.
 - **HTTP Server Errors** - Anzahl der gesendeten Fehlermeldungen zu HTTP "5xx Serverfehler".
 - **Requests** - Anzahl aller Clientanforderungen an die Website.
 <br />**Hinweis:** Sie können zusätzliche Leistungskennzahlen auf der Verwaltungsseite **Monitor** hinzufügen, indem Sie unten auf der Seite auf **Add Metrics** klicken. Weitere Informationen finden Sie unter [Überwachen von Websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

-   **Web Endpoint Status** - Eine Liste von Web-Endpunkten, die zur Überwachung konfiguriert wurden. Falls keine Endpunkte konfiguriert wurden, klicken Sie auf **Configure Web Endpoint Monitoring**, und wechseln Sie zum Abschnitt **Monitoring** der Verwaltungsseite **Configure**. Weitere Informationen finden Sie unter [Überwachen von Websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

-   **Autoscale Status** - Im Standardmodus können Sie automatisch Ihre Ressourcen skalieren, sodass Sie nur soviel wie nötig ausgeben. Um die automatische Skalierung zu aktivieren, verwenden Sie **Configure Autoscale**, woraufhin Sie zur Seite **Scale** gelangen. Falls sich Ihre Website derzeit im Free- oder Shared-Modus befindet, müssen Sie zum Standardmodus wechseln (auf der Seite **Scale**), bevor Sie automatische Skalierung konfigurieren können. Über **Autoscale Operation Logs** gelangen Sie zum Portal **Management Services**, wo Sie den Verlauf der automatischen Skalierung Ihrer Website anzeigen können. Die Standardabfrage erfolgt für die letzten 24 Stunden, Sie können die Abfrage jedoch anpassen.

-   **Usage Overview** - Dieser Abschnitt zeigt die Statistiken für die CPU-, Dateisystem- und Speichernutzung der Website an.
-   **Linked Resources** - Dieser Abschnitt enthält eine Liste der Ressourcen wie SQL- oder MySQL-Datenbanken oder ein Microsoft Azure-Speicherkonto, die mit Ihrer Website verbunden sind. Klicken Sie auf den Namen der Ressource, um sie zu verwalten. Falls Sie über eine MySQL-Datenbank verfügen, gelangen Sie zur ClearDB-Verwaltungsseite, sobald Sie auf den Namen klicken. Dort können Sie die Leistungskennzahlen anzeigen oder das ClearDB-Dashboard aufrufen, wo Sie die MySQL-Datenbank bei Bedarf aktualisieren können. Sind keine Ressourcen aufgelistet, klicken Sie auf **Manage Linked Resources**, um zur Seite **Linked Resources** zu gelangen, wo Sie einen Link zu einer Ressource für Ihre Website hinzufügen können.
-   Der Abschnitt **Quick Glance** enthält die folgenden zusammenfassenden Informationen und Links (abhängig von Ihren Einstellungen sind einige der unten aufgelisteten Optionen möglicherweise nicht vorhanden):
 - **View Applicable Add-ons** - Öffnet das Dialogfeld **Purchase from Store**, wo Sie Add-Ons erwerben können, die zusätzliche Funktionen für die Website bieten. Einige Add-Ons sind möglicherweise nicht in Ihrer Region oder für Ihre Umgebung verfügbar.
 - **View connection strings** - Zeigt die Datenbank-Verbindungszeichenfolgen Ihrer Website an.
 - **Download the publish profile** - Klicken Sie auf diesen Link, um das Veröffentlichungsprofil für Ihre Website herunterzuladen. Das Veröffentlichungsprofil enthält Ihre Anmeldeinformationen (Benutzername und Kennwort) sowie die URLs zum Hochladen des Inhalts auf Ihre Website mit FTP und Git. Die Profildatei liegt im XML-Format vor und kann in einem Texteditor angezeigt werden.
 - **Set up deployment credentials** - Klicken Sie auf diese Option, um einen Benutzernamen und das Kennwort zum Hochladen von Inhalt auf Ihre Website mit FTP oder Git festzulegen. Nachdem Sie die FTP/Git-Bereitstellungsanmeldeinformationen eingerichtet haben, können Sie diese verwenden, um Inhalt auf eine von Ihnen abonnierte Website per FTP oder Git zu übertragen. Um die Anmeldeinformationen nach dem Erstellen anzuzeigen, klicken Sie auf **Download the publish profile**. Das heruntergeladene Veröffentlichungsprofil ist eine Textdatei im XML-Format und kann in einem Texteditor angezeigt werden. **Hinweis**: Authentifizierung beim FTP-Host oder dem Git-Repository mittels Microsoft-Konto-Anmeldung (Live ID) wird nicht unterstützt.
 - **Reset your publish profile credentials** - Setzt das Veröffentlichungsprofil für Ihre Website zurück. Zuvor heruntergeladene Veröffentlichungsprofile werden ungültig.
 - **Set up deployment from source control** - Öffnet ein Dialogfeld, in dem Sie kontinuierliche Veröffentlichung von Team Foundation Service, CodePlex, GitHub, Dropbox, Bitbucket oder lokalem Git einrichten können.
 - **Add a new deployment slot** - Verwenden Sie diese Funktion für Websites im Standardmodus, um einen Staging-Steckplatz für die Website zu erstellen. Durch den Staging-Steckplatz (vor der Bereitstellungsphase) können Sie den Inhalt und die Konfiguration der Website prüfen, bevor sie in Produktion geht. Sie können die Staging-Version der Website auch verwenden, um Inhaltsaktualisierungen nach und nach hinzuzufügen, und dann die Website in die Produktion zu übernehmen, wenn alle Aktualisierungen im Staging-Steckplatz komplett sind. (Sie können keinen Steckplatz zu einer Website hinzufügen, die sich bereits in der Staging-Phase befindet.)
 - **Edit in Visual Studio Online** - Klicken Sie auf diesen Link, um Ihre Website direkt online unter Verwendung von Visual Studio Online im Microsoft Azure-Portal zu bearbeiten. Diese Option ist nur verfügbar, wenn Sie sie auf der Seite **Configure** aktivieren.
 - **Disconnect from Dropbox** - Falls Sie für die Bereitstellung eine Verbindung mit Dropbox eingerichtet haben, können Sie die Verbindung über diesen Link trennen.
 - **Delete Git repository** - Falls Sie ein Git-Repository eingerichtet haben, können Sie es über diesen Link löschen.
 - **Status** - Gibt an, ob die Website ausgeführt wird.
 - **Management Services** - Klicken Sie auf den Link **Operation Logs**, um die Vorgangsprotokolle für Ihre Website aus dem Microsoft Azure-Verwaltungsdienstportal anzuzeigen.
 - **Virtual IP Address** - Zeigt die virtuelle IP-Adresse der Website an, wenn Sie im Abschnitt **SSL Bindings** der Registerkarte **Configure** eine IP-basierte SSL-Bindung für die Website konfiguriert haben.
 - **Site URL** - Gibt die öffentliche zugängliche Adresse der Website im Internet an.
 - **Compute Mode** - Gibt an, ob die Website im Free-, Shared-, Basic- oder Standardmodus ausgeführt wird. Weitere Informationen zum Webskalierungsgruppen-Modus finden Sie unter [Skalieren von Websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites).
 - **FTP Hostname** - Gibt die zu verwendende URL an, wenn die Veröffentlichung auf der Website über FTP erfolgt.
 - **FTPS Hostname** - Gibt die zu verwendende URL an, wenn die Veröffentlichung auf der Website über FTPS erfolgt.
 - **Deployment User / FTP User** - Gibt das zu verwendende Konto an, wenn die Bereitstellung der Website auf Microsoft Azure über FTP oder Git erfolgt.
 - **FTP Diagnostic Logs** - Gibt das FTP-Verzeichnis der Diagnoseprotokolle der Website an, wenn Diagnoseprotokollierung auf der Verwaltungsseite **Configure** aktiviert wurde.
 - **FTPS Diagnostic Logs** - Gibt das FTPS-Verzeichnis der Diagnoseprotokolle der Website an, wenn Diagnoseprotokollierung auf der Verwaltungsseite **Configure** aktiviert wurde.
 - **Location** - Gibt die Region des Datencenters an, das die Website hostet.
 - **Subscription Name** - Gibt den Abonnementnamen an, dem die Website zugeordnet ist.
 - **Subscription ID** - Gibt die eindeutige Abonnement-ID (GUID) an, der die Website zugeordnet ist.

Deployments
-----------

Diese Registerkarte wird nur angezeigt, wenn Sie die Bereitstellung über die Quellcodeverwaltung eingerichtet haben. Auf der Verwaltungsseite **Deployments** finden Sie eine Zusammenfassung aller Bereitstellungen, die mit Ihrer ausgewählten Veröffentlichungsmethode auf der Website erfolgt sind. Wenn Git-Veröffentlichung für die Website konfiguriert wurde, aber keine Bereitstellungen durchgeführt wurden, enthält die Verwaltungsseite **Deployments** Informationen, wie Sie unter Verwendung von GIT eine Webanwendung auf der Website bereitstellen.

Monitor
-------

Die Verwaltungsseite **Monitor** enthält ein Diagramm mit den Nutzungsinformationen der Website. Standardmäßig zeigt dieses Diagramm dieselben Kennzahlen wie das Diagramm im **Dashboard** an, das oben im Abschnitt "Dashboard" beschrieben ist. Das Diagramm kann auch zur Anzeige der Kennzahlen für HTTP-Erfolge, HTTP-Umleitungen, HTTP 401-Fehler, HTTP 403-Fehler, HTTP 404-Fehler und HTTP 406-Fehler konfiguriert werden. Weitere Informationen zu diesen Kennzahlen finden Sie unter [Überwachen von Websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/).

WebJobs
-------

Auf der Verwaltungsseite für WebJobs können Sie Aufgaben für Ihre Website bei Bedarf, geplant oder kontinuierlich ausführen lassen. Weitere Informationen finden Sie unter [Verwenden der WebJobs-Funktion auf Microsoft Azure-Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-create-web-jobs/).

Configure
---------

Die Verwaltungsseite **Configure** dient zum Festlegen von anwendungsspezifischen Einstellungen, darunter:

-   **General** - Legen Sie die für die Webanwendung erforderliche .NET Framework-, PHP-, Python- oder Java-Version fest. Für Websites im Basic- oder Standardmodus können Sie eine 64-Bit-Plattform auswählen. Legen Sie **Managed Pipeline Mode** nur dann auf **Classic** fest, wenn Sie über ältere Websites verfügen, die nur auf älteren Versionen von IIS ausgeführt werden können (**Integrated** ist der Standard.) Um auf der Website Echtzeit-Anfragemusteranwendungen wie Chats zu ermöglichen, können Sie **Web Sockets** auf **On** setzen. Wenn Sie die Website direkt online bearbeiten möchten, setzen Sie **Edit in Visual Studio Online** auf **On**.
-   **Certificates** - Laden Sie ein SSL-Zertifikat für eine benutzerdefinierte Domäne hoch. SSL-Zertifikate können nur im Basic- oder Standardmodus hochgeladen werden. Die von Ihnen hochgeladenen Zertifikate sind hier aufgelistet und können jeder Website in Ihrem Abonnement bzw. an Ihrem Standort zugewiesen werden. Platzhalterzertifikate (mit einem Sternchen markierte Zertifikate) werden unterstützt.
-   **Domain Names** - Sie können zusätzliche benutzerdefinierte Domänennamen für eine Website anzeigen oder hinzufügen. Benutzerdefinierte Domänennamen sind nur im Shared-, Basic- oder Standardmodus verfügbar.
-   **SSL Bindings** - SSL-Bindungen für benutzerdefinierte Domänen sind nur im Basic- oder Standardmodus verfügbar. Wählen Sie einen SSL-Modus (**SNI**, **IP** oder **No SSL**) für einen bestimmten Domänennamen aus. Bei Auswahl von SNI oder IP können Sie ein Zertifikat für die Domäne aus den hochgeladenen Zertifikaten angeben.
-   **Deployments** - Dieser Abschnitt wird nur angezeigt, wenn Sie die Bereitstellung über die Quellcodeverwaltung aktiviert haben. Verwenden Sie diese Einstellungen zum Konfigurieren der Bereitstellungen.
-   **Application Diagnostics** - Legen Sie Optionen für das Sammeln von Diagnoseinformationen für eine Webanwendung, die Protokollierung unterstützt, fest. Sie können im Dateisystem oder auf einem Microsoft Azure-Speicherkonto protokollieren und außerdem die Protokollierungsebene angeben, um die Menge der gesammelten Informationen zu steuern.
-   **Site Diagnostics** - Legen Sie Protokollierungsoptionen zum Erfassen von Diagnoseinformationen für Ihre Website fest, oder aktivieren Sie Visual Studio 2012 bzw. Visual Studio 2013, um die Website remote für maximal 48 Stunden zu debuggen.
-   **Monitoring** - Testen Sie für Websites im Standardmodus die Verfügbarkeit von HTTP- oder HTTPS-Endpunkten für geografische verteilte Standorte.
-   **Developer Analytics** - Die Analyse überwacht die Leistung Ihrer Webanwendung. Wählen Sie ein Analyse-Add-On aus dem Microsoft Azure Store aus, oder legen Sie einen benutzerdefinierten Analyseanbieter wie New Relic fest.
-   **App Settings** - Geben Sie Name-Wert-Paare an, die beim Start von der Webanwendung geladen werden. Bei .NET-Websites werden diese Einstellungen zur Laufzeit in die AppSettings-Einstellung der .NET-Konfiguration der Website eingeführt, wobei vorhandene Einstellungen überschrieben werden. Bei PHP- und Node-Websites sind diese Einstellungen als Umgebungsvariablen zur Laufzeit verfügbar.
-   **Connection Strings** - Zeigen Sie die Verbindungszeichenfolgen zu verknüpften Ressourcen an. Bei .NET-Websites werden diese Verbindungszeichenfolgen zur Laufzeit in die connectionStrings-Einstellung der .NET-Konfiguration der Website eingeführt, wobei vorhandene Einträge überschrieben werden, wenn der Schlüssel dem verknüpften Datenbanknamen entspricht. Bei PHP- und Node-Websites sind diese Einstellungen als Umgebungsvariablen zur Laufzeit verfügbar.
-   **Default Documents** - Fügen Sie das Standarddokument Ihrer Website dieser Liste hinzu, wenn es noch nicht darauf vorhanden ist. Falls die Website mehrere Dateien in der Liste enthält, stellen Sie sicher, dass das Standarddokument Ihrer Website oben in der Liste steht (ändern Sie dazu die Reihenfolge der Dateien in der Liste).
-   **Handler Mappings** - Fügen Sie benutzerdefinierte Skriptprozessoren hinzu, die Anforderungen für bestimmte Dateitypen verarbeiten (z. B. \*.php).
-   **Virtual Applications and Directories** - Konfigurieren Sie die mit Ihrer Website verknüpften virtuellen Anwendungen und Verzeichnisse. Sie haben außerdem die Möglichkeit, ein virtuelles Verzeichnis als Anwendung in der Website-Konfiguration zu markieren.

Weitere Informationen zur Konfiguration einer Website finden Sie unter [Konfigurieren von Websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites).

Scale
-----

Auf der Verwaltungsseite **Scale** können Sie den Webskalierungsgruppen-Modus (**Free**, **Shared**, **Basic** oder **Standard**) festlegen. **Shared**, **Basic** und **Standard** bieten höheren Durchsatz und bessere Leistung. In den Modi **Shared**, **Basic** und **Standard** können Sie die Anzahl unter **Instance Count** erhöhen. Dies ist die Anzahl der virtuellen Computer, die von Ihrer Website und von anderen Websites in derselben Webskalierungsgruppe verwendet werden.

Im **Standard**-Modus können Sie außerdem die Kernanzahl und die Speicherkapazität jeder Instanz erhöhen, indem Sie **Instance Size** ändern. Für bessere Kosteneffektivität sollten Sie die Option **Autoscale** verwenden, damit Microsoft Azure die Ressourcen für Ihre Website dynamisch zuweist.

Weitere Informationen zur Konfiguration von Skalierungsoptionen für eine Website finden Sie unter [Skalieren von Websites](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites).

Verknüpfte Ressourcen
---------------------

Die Verwaltungsseite **Linked Resources** enthält eine Liste der Microsoft Azure-Ressourcen, die Ihre Website verwendet, darunter SQL-Datenbanken, MySQL-Datenbanken und Microsoft Azure-Speicherkonten. Klicken Sie auf den Namen der Ressource, um sie zu verwalten.

Backups
-------

Auf der Verwaltungsseite **Backups** können Sie automatisierte oder manuelle Sicherungen der Website erstellen, die Website in einem vorherigen Zustand wiederherstellen oder eine neue Website basierend auf einer Sicherung erstellen. Weitere Informationen finden Sie unter [Sicherungen von Microsoft Azure-Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-backup/) und unter [Wiederherstellen einer Microsoft Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-restore/).

Symbole auf der Verwaltungsseite
--------------------------------

Symbole werden unten auf jeder Verwaltungsseite der Website angezeigt. Verschiedene dieser Symbole sind auf mehreren Seiten vorhanden, und einige Symbole werden nur auf bestimmten Seiten angezeigt. Die folgenden Symbole werden unten auf der Verwaltungsseite **Dashboard** angezeigt:

-   **Browse** - Öffnet die Standardseite für die Website.
-   **Stop** - Hält die Website an.
-   **Restart** - Startet die Website neu.
-   **Manage Domains** - Ordnet dieser Website eine Domäne zu. Ist im Skalierungsmodus **Free** nicht für Websites verfügbar.
-   **Delete** - Löscht die Website.
-   **WebMatrix** - Öffnet unterstützte Websites in WebMatrix, sodass Sie Änderungen an der Website vornehmen können und die Änderungen auf der Website auf Microsoft Azure veröffentlichen können.

Die folgenden Symbole werden nicht unten auf der Verwaltungsseite **Dashboard** angezeigt, aber auf anderen Verwaltungsseiten, um bestimmte Aufgaben auszuführen:

-   **Add Metrics** - Unten auf der Verwaltungsseite **Monitor** können Sie mit diesem Symbol Kennzahlen zum Diagramm, das auf der Überwachungsseite angezeigt wird, hinzufügen.
-   **Link** - Unten auf der Verwaltungsseite **Linked Resources** können Sie mit diesem Symbol Verwaltungslinks zu anderen Microsoft Azure-Ressourcen erstellen.
-   Wenn Ihre Website beispielsweise auf eine SQL-Datenbank zugreift, können Sie einen Verwaltungslink zur Datenbankressource herstellen, indem Sie auf **Link** klicken.

