<properties linkid="manage-services-how-to-monitor-websites" urlDisplayName="How to monitor" pageTitle="How to monitor web sites - Azure service management" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Learn how to monitor Azure web sites by using the Monitor page in the Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Web Sites" authors="" solutions="" manager="" editor="" />

Überwachen von Websites
=======================

Websites bieten Überwachungsfunktionen über die Verwaltungsseite "Monitor". Die Verwaltungsseite "Monitor" enthält Leistungsstatistiken für eine Website, die unten beschrieben sind.

Inhaltsverzeichnis
------------------

-   [Vorgehensweise: Hinzufügen von Website-Kennzahlen](#websitemetrics)
-   [Vorgehensweise: Empfangen von Warnungen von Website-Kennzahlen](#howtoreceivealerts)
-   [Vorgehensweise: Anzeigen der Nutzungskontingente für eine Website](#howtoviewusage)
-   [Vorgehensweise: Reduzieren der Ressourcennutzung](#resourceusage)
-   [Was geschieht, wenn ein Ressourcennutzungskontingent überschritten wird?](#exceeded)
-   [Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website](#howtoconfigdiagnostics)
-   [Vorgehensweise: Überwachen des Web-Endpunktstatus](#webendpointstatus)

Vorgehensweise: Hinzufügen von Website-Kennzahlen
-------------------------------------------------

1.  Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) auf den Verwaltungsseiten der Website auf die Registerkarte **Monitor**, um die Verwaltungsseite **Monitor** anzuzeigen. Standardmäßig zeigt das Diagramm auf der Seite **Monitor** dieselben Kennzahlen wie das Diagramm auf der Seite **Dashboard** an.

2.  Zum Anzeigen zusätzlicher Kennzahlen für die Website klicken Sie unten auf der Seite auf **Add Metrics**, um das Dialogfeld **Choose Metrics** anzuzeigen.

3.  Wählen Sie zusätzliche Kennzahlen zum Anzeigen auf der Seite **Monitor** durch Anklicken aus.

4.  Nach dem Auswählen der Kennzahlen, die Sie zur Seite **Monitor** hinzufügen möchten, klicken Sie auf **OK**.

5.  Nachdem Sie die Kennzahlen zur Seite **Monitor** hinzugefügt haben, klicken Sie zum Aktivieren/Deaktivieren auf die Optionsfelder neben jeder Kennzahl, um sie im Diagramm oben auf der Seite hinzuzufügen bzw. zu entfernen.

6.  Um Kennzahlen von der Seite **Monitor** zu entfernen, wählen Sie die zu entfernende Kennzahl aus und klicken auf das Symbol **Delete Metric** unten auf der Seite.

In der folgenden Liste sind die Kennzahlen beschrieben, die Sie im Diagramm auf der Seite **Monitor** anzeigen können:

-   **CPUTime** - Messung der CPU-Auslastung der Website.
-   **Requests** - Anzahl der Clientanforderungen an die Website.
-   **Data Out** - Messung der Daten, die von der Website an Clients gesendet wurden.
-   **Data In** - Messung der Daten, die auf der Website von Clients empfangen wurden.
-   **Http Client Errors** - Anzahl der gesendeten HTTP-Fehlermeldungen zu "4xx Clientfehler".
-   **Http Server Errors** - Anzahl der gesendeten HTTP-Fehlermeldungen zu "5xx Serverfehler".
-   **Http Successes** - Anzahl der gesendeten HTTP-Meldungen zu "2xx Erfolgreich".
-   **Http Redirects** - Anzahl der gesendeten HTTP-Meldungen zu "3xx Umleitung".
-   **Http 401 errors** - Anzahl der gesendeten HTTP-Meldungen zu "401 Nicht autorisiert".
-   **Http 403 errors** - Anzahl der gesendeten HTTP-Meldungen zu "403 Verboten".
-   **Http 404 errors** - Anzahl der gesendeten HTTP-Meldungen zu "404 Nicht gefunden".
-   **Http 406 errors** - Anzahl der gesendeten HTTP-Meldungen zu "406 Nicht zulässig".

Vorgehensweise: Empfangen von Warnungen von Website-Kennzahlen
--------------------------------------------------------------

Im Website-Modus **Standard** können Sie basierend auf den Website-Überwachungskennzahlen Warnungen empfangen. Zur Verwendung der Warnfunktion müssen Sie zuerst einen Web-Endpunkt zur Überwachung auswählen, was Sie im Abschnitt **Monitoring** der Seite **Configure** tun können. Auf der Seite **Settings** des Azure-Verwaltungsportals können Sie anschließend eine Regel erstellen, um eine Warnung auszulösen, wenn die von Ihnen ausgewählte Kennzahl einen festgelegten Wert erreicht. Sie können auch angeben, dass eine E-Mail gesendet wird, sobald eine Warnung ausgelöst wird. Weitere Informationen finden Sie unter [Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

Vorgehensweise: Anzeigen der Nutzungskontingente für eine Website
-----------------------------------------------------------------

Websites können auf der Verwaltungsseite **Scale** zur Ausführung entweder im **Shared**- oder im **Standard**-Website-Modus konfiguriert werden. Jedes Azure-Abonnement hat Zugriff auf einen Ressourcenpool, der für die Ausführung von bis zu 100 Websites pro Region im Website-Modus **Shared** bereitgestellt wird. Der für jedes Website-Abonnement für diesen Zweck bereitgestellte Ressourcenpool wird auch von anderen Websites in derselben geografischen Region genutzt, die für die Ausführung im **Shared**-Modus konfiguriert sind. Da diese Ressourcen von anderen Websites ebenfalls genutzt werden, verfügen alle Abonnements über begrenzte Ressourcennutzung. Die Höchstgrenzen für die Ressourcennutzung von Abonnements werden als Nutzungskontingente bezeichnet und sind im Abschnitt "Usage Overview" der Verwaltungsseite **Dashboard** auf jeder Website aufgelistet.

**Hinweis**

 Wenn eine Website zur Ausführung im **Standard**-Modus konfiguriert ist, werden ihr dedizierte Ressourcen entsprechend der Größe **Small** (Standard), **Medium** oder **Large** des virtuellen Computers zugewiesen, die in der Tabelle unter [Größen virtueller Computer und Clouddienste für Azure](http://go.microsoft.com/fwlink/?LinkID=309169) aufgelistet sind. Es gibt kein Limit, wie viele Ressourcen ein Abonnement zum Ausführen von Websites im **Standard**-Modus verwenden kann. Die Anzahl der im **Standard**-Modus erstellten Websites pro Region ist jedoch auf 500 begrenzt.

### Anzeigen der Nutzungskontingente für Websites, die für den Website-Modus Shared konfiguriert sind

Um zu bestimmen, in welchem Umfang eine Website sich auf die Ressourcennutzungskontingente auswirkt, führen Sie folgende Schritte aus:

1.  Öffnen Sie die Website-Verwaltungsseite **Dashboard**.
2.  Im Abschnitt **usage overview** werden die Nutzungskontingente für **Data Out**, **CPU Time** und **File System Storage** angezeigt. Der für jede Ressource angezeigte grüne Balken gibt an, welcher Anteil des Ressourcennutzungskontingents eines Abonnements von der aktuellen Website belegt wird, und der für jede Ressource angezeigte graue Balken gibt an, welcher Anteil des Ressourcennutzungskontingents von allen anderen Websites im Shared-Modus, die Ihrem Website-Abonnement zugeordnet sind, belegt wird.

Ressourcennutzungskontingente verhindern die Überlastung folgender Ressourcen:

-   **Data Out** - Messung der Datenmenge, die von Websites im **Shared**-Modus innerhalb des aktuellen Kontingentintervalls (24 Stunden) an ihre Clients gesendet wurde.
-   **CPU Time** - CPU-Zeit, die von Websites im **Shared**-Modus innerhalb des aktuellen Kontingentintervalls verbraucht wurde.
-   **File System Storage** - Dateisystemspeicher, der durch Websites im **Shared**-Modus belegt wird.

Wenn das Nutzungskontingent eines Abonnements überschritten wird, stoppt Azure die Überlastung der Ressourcen mit bestimmten Maßnahmen. Dies soll verhindern, dass ein Abonnent die Ressourcen zum Nachteil anderer Abonnenten erschöpft.

Vorgehensweise: Reduzieren der Ressourcennutzung
------------------------------------------------

Da Azure die Ressourcennutzungskontingente durch Messung der von Websites im Shared-Modus eines Abonnements über ein Kontingentintervall von 24 Stunden verwendeten Ressourcen berechnet, beachten Sie Folgendes:

-   Wenn sich die Anzahl der Websites, die zur Ausführung im Shared-Modus konfiguriert sind, erhöht, steigt die Wahrscheinlichkeit, dass die Ressourcennutzungskontingente für den Shared-Modus überschritten werden. Reduzieren Sie ggf. die Anzahl der Websites, die zur Ausführung im Shared-Modus konfiguriert sind, falls das Ressourcennutzungskontingent überschritten wird.
-   Genauso gilt: Wenn sich die Anzahl der Instanzen einer Website, die im Shared-Modus ausgeführt wird, erhöht, steigt die Wahrscheinlichkeit, dass die Ressourcennutzungskontingente für den Shared-Modus überschritten werden. Skalieren Sie ggf. zusätzliche Instanzen von Websites im Shared-Modus herunter, wenn die Ressourcennutzungskontingente überschritten werden.

Was geschieht, wenn ein Ressourcennutzungskontingent überschritten wird?
------------------------------------------------------------------------

Azure trifft folgende Maßnahmen, wenn das Ressourcennutzungskontingent eines Abonnements in einem Kontingentintervall (24 Stunden) überschritten wird:

-   **Data Out** - Wenn dieses Kontingent überschritten wird, stoppt Azure alle Websites für ein Abonnement, die zur Ausführung im **Shared**-Modus konfiguriert sind, für den Rest des aktuellen Kontingentintervalls. Azure startet die Websites erneut zu Beginn des nächsten Kontingentintervalls.

-   **CPU Time** - Wenn dieses Kontingent überschritten wird, stoppt Azure alle Websites für ein Abonnement, die zur Ausführung im **Shared**-Modus konfiguriert sind, für den Rest des aktuellen Kontingentintervalls. Azure startet die Websites erneut zu Beginn des nächsten Kontingentintervalls.

-   **File System Storage** - Azure verhindert die Bereitstellung von Websites für ein Abonnement, die zur Ausführung im Shared-Modus konfiguriert sind, falls die Bereitstellung dazu führt, dass das Dateisystemspeicher-Nutzungskontingent überschritten wird. Sobald die Dateisystemspeicher-Ressource die maximale für das Kontingent zulässige Größe erreicht hat, bleibt der Dateisystemspeicher zwar für Lesevorgänge verfügbar, aber alle Schreibvorgänge, auch die für die normale Website-Aktivität erforderlichen, werden blockiert. In diesem Fall können Sie eine oder mehrere Websites, die im Shared-Modus ausgeführt werden, für die Ausführung im Standard-Website-Modus konfigurieren, oder alternativ die Nutzung des Dateisystemspeichers entsprechend dem Dateisystemspeicher-Nutzungskontingent reduzieren.

Vorgehensweise: Diagnosekonfiguration und Herunterladen von Protokollen für eine Website
----------------------------------------------------------------------------------------

Diagnosen werden auf der Verwaltungsseite **Configure** für die Website aktiviert. Es gibt zwei Arten von Diagnosen: **application diagnostics** und **site diagnostics**.

#### Application Diagnostics

Der Abschnitt **application diagnostics** auf der Verwaltungsseite **Configure** steuert die Protokollierung der von der Anwendung erzeugten Informationen. Dies ist nützlich beim Protokollieren von Ereignissen, die innerhalb einer Anwendung auftreten. Wenn also ein Fehler in der Anwendung auftritt, können Sie beispielsweise einen benutzerfreundlichen Fehler anzeigen und die ausführlicheren Fehlerinformationen zur späteren Analyse in das Protokoll schreiben.

Sie können die folgenden Anwendungsdiagnose-Optionen aktivieren oder deaktivieren:

-   **Application Logging (File System)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen. Das Feld **Logging Level** bestimmt, ob Fehler, Warnmeldungen oder Informationen protokolliert werden. Sie können auch "Verbose" auswählen, um alle von der Anwendung erzeugten Informationen zu protokollieren.

    Die von dieser Einstellung erzeugten Protokolle werden im Dateisystem der Website gespeichert und können mithilfe der unter **Herunterladen von Protokolldateien für eine Website** beschriebenen Schritte heruntergeladen werden.

-   **Application Logging (Table Storage)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen, ähnlich wie die Option "Application Logging (File System)". Die Protokollinformationen werden jedoch in einer Tabelle im Azure-Speicherkonto gespeichert.

    Zur Angabe des Azure-Speicherkontos und der Tabelle aktivieren Sie **On**, wählen **Logging Level** aus und legen **Manage Table Storage** fest. Geben Sie die zu verwendende Tabelle und das Speicherkonto an, oder erstellen Sie eine neue Tabelle.

    Die in der Tabelle gespeicherten Protokollinformationen können über einen Azure-Speicherclient abgerufen werden.

-   **Application Logging (Blob Storage)** - Aktiviert die Protokollierung der von der Anwendung erzeugten Informationen, ähnlich wie die Option "Application Logging (Table Storage)". Die Protokollinformationen werden jedoch in einem Blob im Azure-Speicherkonto gespeichert.

    Zur Angabe des Azure-Speicherkontos und des Blobs aktivieren Sie **On**, wählen **Logging Level** aus und legen **Manage Blob Storage** fest. Geben Sie das Speicherkonto, den Blob-Container und den zu verwendenden Blob-Namen an, oder erstellen Sie einen neuen Container mit Blob.

Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Verwalten von Speicherkonten](https://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/).
**Hinweis**

Anwendungsprotokollierung in einer Tabelle oder einem Blob-Speicher wird nur für .NET-Anwendungen unterstützt.

Da die Anwendungsprotokollierung im Speicher die Verwendung eines Speicherclients zum Anzeigen der Protokolldaten erfordert, ist es hilfreich, wenn Sie eine Anwendung oder einen Dienst verwenden, die bzw. der Daten direkt aus der Azure-Tabelle oder dem Blob-Speicher lesen und verarbeiten kann. Bei der Protokollierung im Dateisystem werden Dateien erzeugt, die über FTP oder andere Dienstprogramme auf Ihren lokalen Computer heruntergeladen werden können, wie später in diesem Abschnitt beschrieben wird.
**Hinweis**

**Application diagnostics (file system)**, **Application diagnostics (table storage)** und **Application diagnostics (blob storage)** können parallel aktiviert werden und über individuelle Konfigurationen der Protokollierungsebene verfügen. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Speicher protokollieren, während die Dateisystemprotokollierung nach der Instrumentierung des Anwendungscodes zum Zweck der Fehlerbehebung ausführlich erfolgt.

**Hinweis**

Diagnosen können auch über die Azure PowerShell mit dem Cmdlet **Set-AzureWebsite** aktiviert werden.

Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/powershell-cmdlets/).

**Hinweis**

Die Anwendungsprotokollierung beruht auf Protokollinformationen, die von Ihrer Anwendung erzeugt werden. Die verwendete Methode zum Erzeugen von Protokollinformationen sowie das Format der Informationen hängt von der Sprache ab, in der Ihre Anwendung geschrieben ist. Erläuterungen zur sprachspezifischen Verwendung der Anwendungsprotokollierung finden Sie in den folgenden Artikeln:

-   **.NET** - [Aktivieren der Diagnoseprotokollierung für Azure-Websites](/de-de/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
-   **Node.js** - [Debuggen einer Node.js-Anwendung auf Azure-Websites](/de-de/develop/nodejs/how-to-guides/Debug-Website/)

Anwendungsprotokollierung in einer Tabelle oder einem Blob-Speicher wird nur für .NET-Anwendungen unterstützt.

#### Site Diagnostics

Der Abschnitt **site diagnostics** der Verwaltungsseite **Configure** steuert die vom Webserver durchgeführte Protokollierung, wie z. B. das Protokollieren von Webanfragen, Fehler bei der Seitenbereitstellung und wie lange es dauert, eine Seite bereitzustellen. Sie können die folgenden Optionen aktivieren oder deaktivieren:

-   **Web Server Logging** - Aktivieren Sie die Webserverprotokollierung, um Website-Protokolle im erweiterten W3C-Protokolldateiformat zu speichern. Die Webserverprotokollierung erzeugt einen Datensatz aller eingehenden Anfragen an die Website, mit Informationen wie Client-IP-Adresse, angeforderte URI, HTTP-Statuscode der Antwort sowie Benutzer-Agent-Zeichenfolge des Clients. Sie können die Protokolle auf einem Azure-Speicherkonto oder im Dateisystem speichern.

   Um die Webserverprotokolle in einem Azure-Speicherkonto zu speichern, wählen Sie **Storage** und dann **manage storage** aus, um ein Speicherkonto und einen Azure Blob-Container anzugeben, in dem die Protokolle gespeichert werden. Weitere Informationen zu Azure-Speicherkonten finden Sie unter [Verwalten von Speicherkonten](https://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/).

   Um die Webserverprotokolle im Dateisystem zu speichern, wählen Sie **File System** aus. Daraufhin wird das Feld **Quota** aktiviert, in dem Sie den maximalen Speicherplatz für die Protokolldateien angeben können. Die Mindestgröße beträgt 25 MB, das Maximum 100 MB. Die Standardgröße ist 35 MB.

   Standardmäßig werden Webserverprotokolle nie gelöscht. Wenn Sie einen Zeitraum angeben möchten, nach dem Protokolle automatisch gelöscht werden, wählen Sie **Set Retention** aus, und geben Sie unter **Retention Period** die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen. Diese Einstellung ist für die Azure Speicher- oder Dateisystem-Option verfügbar.

-   **Detailed Error Messages** - Aktiviert die detaillierte Fehlerprotokollierung, um zusätzliche Informationen über HTTP-Fehler zu protokollieren (höhere Statuscodes als 400).

-   **Failed Request Tracing** - Aktiviert die Verfolgung fehlgeschlagener Anforderungen, um Informationen zu fehlgeschlagenen Clientanforderungen zu erfassen, wie HTTP-Statuscodes der 400er-Serie. Die Verfolgung fehlgeschlagener Anforderungen erzeugt ein XML-Dokument, in dem man verfolgen kann, welche Module die Anforderung in IIS durchlaufen hat, die vom Modul zurückgegebenen Details sowie den Zeitpunkt des Modulaufrufs. Diese Informationen können verwendet werden, um zu isolieren, in welcher Komponente der Fehler aufgetreten ist.

Klicken Sie nach dem Aktivieren der Diagnose für eine Website auf das Symbol **Save** unten auf der Verwaltungsseite **Configure**, um die von Ihnen festgelegten Optionen anzuwenden.
**Wichtig**

Durch die Protokollierung und Verfolgung kann die Website erheblich belastet werden. Wir empfehlen, die Protokollierung und Verfolgung wieder zu deaktivieren, nachdem Sie die zu behebenden Probleme reproduziert haben.

### Erweiterte Konfiguration

Die Diagnose kann weiter angepasst werden, indem Sie Schlüssel/Wert-Paare im Abschnitt **app settings** der Verwaltungsseite **Configure** hinzufügen. Folgende Einstellungen können über **app settings** konfiguriert werden:

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

-   Das Verzeichnis, in dem die Anwendungsprotokolle gespeichert werden, in Bezug auf das Web-Stammverzeichnis.

-   Standardwert:: ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

-   Die maximale Puffergröße für das Erfassen von Anwendungsprotokollen. Die Informationen werden zunächst in den Puffer geschrieben, bevor sie in die Datei oder den Speicher übertragen werden. Wenn neue Informationen in den Puffer geschrieben werden, bevor dieser geleert (übertragen) wurde, können die zuvor erfassten Informationen verloren gehen. Falls Ihre Anwendung große Mengen von Protokollinformationen erzeugt, sollten Sie die Puffergröße erhöhen.

-   Standardwert: 10 MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

-   Die maximale Größe des Ordners **Application**, in welchem die in die Datei geschriebenen Anwendungsdiagnosen gespeichert werden.

-   Standardwert: 1 MB

### Herunterladen von Protokolldateien für eine Website

Protokolldateien können entweder mit FTP, Azure PowerShell oder den Azure-Befehlszeilentools heruntergeladen werden.

**FTP**

1.  Öffnen Sie die Verwaltungsseite **Dashboard** der Website, und notieren Sie die unter **Diagnostics Logs** aufgelistete FTP-Site sowie das unter **Deployment User** aufgelistete Konto. Auf der FTP-Site befinden sich die Protokolldateien, und das unter "Deployment User" aufgelistete Konto wird zur Authentifizierung bei der FTP-Site verwendet.
2.  Falls Sie noch keine Bereitstellungsanmeldeinformationen erstellt haben, wird das Konto unter **Deployment User** als **Not set** aufgeführt. In diesem Fall müssen Sie die Bereitstellungsanmeldeinformationen wie im Abschnitt "Zurücksetzen der Bereitstellungsanmeldeinformationen" des Dashboards beschrieben erstellen, da diese Anmeldeinformationen zur Authentifizierung bei der FTP-Site erforderlich sind, wo die Protokolldateien gespeichert werden. Azure unterstützt keine Authentifizierung bei der FTP-Site mittels Live ID-Anmeldung.
3.  Verwenden Sie einen FTP-Client wie [FileZilla](http://go.microsoft.com/fwlink/?LinkId=247914) für die Verbindung mit der FTP-Site. Ein FTP-Client vereinfacht die Eingabe der Anmeldeinformationen und das Anzeigen der Ordner auf der FTP-Site gegenüber der Verwendung eines Browsers.
4.  Kopieren Sie die Protokolldateien von der FTP-Site auf Ihren lokalen Computer.

**Azure PowerShell**

1.  Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie mit der rechten Maustaste auf den Eintrag **Azure PowerShell**, und wählen Sie **Run as Administrator** aus.

    **Hinweis**

    Falls **Azure PowerShell** nicht installiert ist, finden Sie Informationen zur Installation und Konfiguration unter [Erste Schritte mit Azure PowerShell-Cmdlets](http://msdn.microsoft.com/de-de/library/windowsazure/jj554332.aspx).

2.  Geben Sie an der Eingabeaufforderung von Azure PowerShell den folgenden Befehl ein, um die Protokolldateien herunterzuladen:

         Save-AzureWebSiteLog -Name websitename

    Daraufhin werden die Protokolldateien für die Website heruntergeladen, die unter **websitename** angegeben ist, und in der Datei **log.zip** im aktuellen Verzeichnis gespeichert.

    Sie können auch einen Live-Stream der Protokollereignisse mit dem folgenden Befehl anzeigen:

         Get-AzureWebSiteLog -Name websitename -Tail

    Daraufhin werden die Protokollinformationen an der Eingabeaufforderung von Azure PowerShell angezeigt, sobald sie auftreten.

**Azure-Befehlszeilentools**

Öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung, und verwenden Sie den folgenden Befehl zum Herunterladen der Protokolldateien:

    azure site log download websitename

Daraufhin werden die Protokolldateien für die Website heruntergeladen, die unter **websitename** angegeben ist, und in der Datei **log.zip** im aktuellen Verzeichnis gespeichert.

Sie können auch einen Live-Stream der Protokollereignisse mit dem folgenden Befehl anzeigen:

    azure site log tail websitename

Daraufhin werden die Protokollinformationen an der Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung angezeigt, von der der Befehl ausgeführt wird.
**Hinweis**

Falls der Befehl **azure** nicht installiert ist, finden Sie Informationen zur Installation und Konfiguration unter [Verwenden der Azure-Befehlszeilentools](http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/command-line-tools/).

### Lesen der Protokolldateien

Die Protokolldateien, die erzeugt werden, nachdem Sie Protokollierung und/oder Verfolgung für eine Website aktiviert haben, sind je nach der auf der Verwaltungsseite "Configure" für die Website eingestellten Protokollierungs-/Verfolgungsebene unterschiedlich. Nachfolgend ist das Verzeichnis der Protokolldateien und die Art der Analyse angegeben:

**Protokolldateityp: Application Logging**

-   Verzeichnis: /LogFiles/Application/. Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden. Zu den protokollierten Informationen gehören Datum und Zeit, Prozess-ID (PID) der Anwendung sowie der von der Anwendungsinstrumentierung erzeugte Wert.

-   Lesen Sie Dateien mit: Einem Texteditor oder Parser, der die von der Anwendung erzeugten Werte versteht

**Protokolldateityp: Failed Request Tracing**

-   Verzeichnis: /LogFiles/W3SVC########\#/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.

-   Lesen Sie Dateien mit: Internet Explorer

**Protokolldateityp: Detailed Error Logging**

-   Verzeichnis: /LogFiles/DetailedErrors/. Der Ordner "/LogFiles/DetailedErrors/" enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen.

-   Lesen Sie Dateien mit: Webbrowser

Die HTM-Dateien umfassen die folgenden Abschnitte:

-   **Detailed Error Information:** Enthält Informationen zum Fehler wie *Module*, *Handler*, *Error Code* und *Requested URL*.

-   **Most likely causes:** Listet verschiedene mögliche Fehlerursachen auf.

-   **Things you can try:** Listet mögliche Lösungen für das im Fehler gemeldete Problem auf.

-   **Links and More Information**: Bietet zusätzliche zusammenfassende Informationen zum Fehler sowie ggf. Links zu anderen Ressourcen wie Microsoft Knowledge Base-Artikeln.

**Protokolldateityp: Web Server Logging**

-   Verzeichnis: /LogFiles/http/RawLogs. Die in den Dateien gespeicherten Informationen sind im [erweiterten W3C-Protokollformat](http://go.microsoft.com/fwlink/?LinkID=90561) formatiert. Die Felder "s-computername", "s-ip" und "cs-version" werden von Azure-Websites nicht verwendet.

-   Lesen Sie Dateien mit: Log Parser. Dient zum Analysieren und Abfragen von IIS-Protokolldateien. Log Parser 2.2 ist im Microsoft Download Center unter <http://go.microsoft.com/fwlink/?LinkId=246619> verfügbar.

Vorgehensweise: Überwachen des Web-Endpunktstatus
-------------------------------------------------

Mit dieser Funktion, die im **Standard**-Modus verfügbar ist, können Sie bis zu 2 Endpunkte von 3 geografischen Standorten aus überwachen.

Endpunktüberwachung konfiguriert Webtests von geografisch verteilten Standorten zum Testen der Reaktionszeit und der Verfügbarkeit von Web-URLs. Der Test führt einen HTTP GET-Aufruf der Web-URL durch, um die Reaktionszeit und Verfügbarkeit von jedem Standort zu bestimmen. Jeder konfigurierte Standort führt alle fünf Minuten einen Test aus.

Die Verfügbarkeit wird mit HTTP-Antwortcodes überwacht, und die Reaktionszeit wird in Millisekunden gemessen. Eine Verfügbarkeit von 100 % ist gegeben, wenn die Reaktionszeit kürzer als 30 Sekunden ist und der HTTP-Statuscode kleiner als 400 ist. Eine Verfügbarkeit von 0 % ist gegeben, wenn die Reaktionszeit länger als 30 Sekunden ist oder der HTTP-Statuscode größer als 400 ist.

Nachdem Sie die Endpunktüberwachung konfiguriert haben, können Sie Details zu den einzelnen Endpunkten aufrufen, um die Reaktionszeit und den Verfügbarkeitsstatus für das Überwachungsintervall von jedem der Teststandorte anzuzeigen.

**So konfigurieren Sie die Endpunktüberwachung:**

1.  Öffnen Sie **Web Sites**. Klicken Sie auf den Namen der Website, die Sie konfigurieren möchten.
2.  Klicken Sie auf die Registerkarte **Configure**.
3.  Wechseln Sie zum Abschnitt **Monitoring**, um Ihre Endpunkteinstellungen einzugeben.
4.  Geben Sie einen Namen für den Endpunkt ein.
5.  Geben Sie die URL für den Dienst ein, den Sie überwachen möchten. Beispiel: <http://contoso.cloudapp.net>.
6.  Wählen Sie einen oder mehrere geografische Standorte aus der Liste aus.
7.  Sie können optional die vorherigen Schritte wiederholen, um einen zweiten Endpunkt zu erstellen.
8.  Klicken Sie auf **Speichern**. Es kann einige Zeit dauern, bis die Daten der Web-Endpunktüberwachung auf den Registerkarten **Dashboard** und **Monitor** verfügbar sind.

Weitere Informationen zur Web-Endpunktüberwachung erhalten Sie in den folgenden Videos:

-   [Scott Guthrie mit einer Einführung zu Azure-Websites und Einrichtung der Endpunktüberwachung](http://www.windowsazure.com/de-de/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

-   [Pflegen von Azure-Websites plus Endpunktüberwachung - mit Stefan Schackow](http://www.windowsazure.com/de-de/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)


