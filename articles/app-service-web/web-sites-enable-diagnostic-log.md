<properties
	pageTitle="Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service"
	description="Erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Instrumentierung zu Ihrer Anwendung hinzufügen und wie Sie auf die von Azure protokollierten Informationen zugreifen."
	services="app-service\web"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="cephalin"/>

# Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service

## Übersicht

Azure bietet integrierte Diagnosefunktionen zur Unterstützung beim Debuggen einer [App Service-Web-App](http://go.microsoft.com/fwlink/?LinkId=529714). In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Ihrer Anwendung Instrumentierung hinzufügen sowie wie Sie die von Azure protokollierten Informationen abrufen.

> [AZURE.NOTE]In diesem Artikel wird die Verwendung von Diagnoseprotokollen mit dem [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715), Azure PowerShell und der Azure-Befehlszeilenschnittstelle (Azure-CLI) beschrieben. Informationen zum Arbeiten mit Diagnoseprotokollen in Visual Studio finden Sie unter [Problembehandlung von Azure in Visual Studio](troubleshoot-web-sites-in-visual-studio.md).

## <a name="whatisdiag"></a>Webserver- und Anwendungsdiagnose

App Service-Web-Apps bieten Diagnosefunktionen zum Protokollieren von Informationen sowohl über den Webserver als auch über die Webanwendung. Diese sind logisch in **Webserverdiagnose** und **Anwendungsdiagnose** unterteilt.

### Webserverdiagnose

Sie können die folgenden Protokollarten aktivieren oder deaktivieren:

- **Detaillierte Fehlerprotokollierung** - Detaillierte Fehlerinformationen für HTTP-Statuscodes, die auf einen Fehler hinweisen (Statuscode 400 oder höher). Diese können Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat.
- **Ablaufverfolgung von Anforderungsfehlern** - Detaillierte Informationen zu fehlgeschlagenen Anforderungen, einschließlich der Verfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies kann nützlich sein, wenn Sie versuchen, die Leistung von Websites zu verbessern oder herauszufinden, warum ein bestimmter HTTP-Fehler zurückgegeben wird.
- **Webserverprotokollierung** - Informationen über HTTP-Transaktionen im erweiterten [W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Dies ist hilfreich, wenn Sie allgemeine Website-Kennzahlen ermitteln möchten, wie die Anzahl der verarbeiteten Anfragen oder wie viele Anforderungen von einer bestimmten IP-Adresse stammen.

### Anwendungsdiagnose

Mit der Option "Application Diagnostics" können Sie die von einer Webanwendung erzeugten Informationen erfassen. ASP.NET-Anwendungen können die Klasse [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) verwenden, um Informationen im Anwendungs-Diagnoseprotokoll aufzuzeichnen. Zum Beispiel:

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Sie können diese Protokolle zur Laufzeit abrufen und bei der Problembehandlung nutzen. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Web-Apps in Visual Studio](../troubleshoot-web-sites-in-visual-studio.md).

App Service-Web-Apps protokollieren auch Bereitstellungsinformationen, wenn Sie Inhalte in einer Web-App veröffentlichen. Dies erfolgt automatisch, und es gibt keine Konfigurationseinstellung für die Bereitstellungsprotokollierung. Anhand der Bereitstellungsprotokollierung können Sie bestimmen, warum eine Bereitstellung fehlgeschlagen ist. Wenn Sie beispielsweise ein benutzerdefiniertes Bereitstellungsskript verwenden, können Sie die Bereitstellungsprotokollierung verwenden, um festzustellen, warum das Skript fehlgeschlagen ist.

## <a name="enablediag"></a>Aktivieren der Diagnose

Zum Aktivieren der Diagnose im [Azure-Vorschauportal](https://portal.azure.com) navigieren Sie zum Blatt für Ihre Web-App und klicken auf **Einstellungen > Diagnoseprotokolle**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Protokolle Teil](./media/web-sites-enable-diagnostic-log/logspart.png)

Wenn Sie die **Anwendungsdiagnose** aktivieren, können Sie auch die **Ebene** auswählen. Diese Einstellung ermöglicht das Filtern der aufgezeichneten Informationen nach **informational**, **warning** oder **error**. Sie können auch **verbose** auswählen, um alle von der Anwendung erzeugten Informationen zu protokollieren.

> [AZURE.NOTE]Im Gegensatz zum Ändern der Datei "web.config" wird durch das Aktivieren der Anwendungsdiagnose oder durch das Ändern des Diagnoseprotokollumfangs nicht die Anwendungsdomäne neu gestartet, in der die Anwendung ausgeführt wird.

Im [Azure-Portal](https://manage.windowsazure.com) der Web-App auf der Registerkarte **Konfigurieren** können Sie **Speicher** oder **Dateisystem** für **Webserverprotokollierung** auswählen. Durch Auswahl von **storage** können Sie ein Speicherkonto festlegen und dann einen Blob-Container hinzufügen, in den die Protokolle geschrieben werden. Alle anderen Protokolle für **site diagnostics** werden nur in das Dateisystem geschrieben.

Die Registerkarte **Konfigurieren** im [Azure-Portal](https://manage.windowsazure.com) der Web-App verfügt außerdem über zusätzliche Einstellungen für die Anwendungsdiagnose:

* **Dateisystem** - Speichert die Anwendungsdiagnoseinformationen im Dateisystem der Web-App. Diese Dateien können über FTP aufgerufen werden oder als ZIP-Archiv mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle (Azure-CLI) heruntergeladen werden.
* **Table storage** - Speichert die Anwendungsdiagnoseinformationen im angegebenen Azure-Speicherkonto unter dem Tabellennamen.
* **Blob storage** - Speichert die Anwendungsdiagnoseinformationen im angegebenen Azure-Speicherkonto im Blob-Container.
* **Retention period** - Standardmäßig werden Protokolle nicht automatisch aus dem **blob storage** gelöscht. Falls die Protokolle automatisch gelöscht werden sollen, wählen Sie **set retention** aus, und geben Sie die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen.

Sie können gleichzeitig eine Kombination aus Dateisystem-, Tabellen- und BLOB-Speicher aktivieren und jeweils individuelle Konfigurationen der Protokollierungsebene festlegen. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Blob-Speicher protokollieren, während die Dateisystemprotokollierung ausführlich erfolgt.

Obwohl alle drei Speicherorte dieselben grundlegenden Informationen zu protokollierten Ereignissen bieten, zeichnen **table storage** und **blob storage** zusätzliche Informationen wie Instanz-ID, Thread-ID und einen ausführlicheren Zeitstempel (Tick-Format) als die Protokollierung im **file system** auf.

> [AZURE.NOTE]Im **Tabellenspeicher** oder **Blob-Speicher** gespeicherte Informationen können nur über Speicherclients oder Anwendungen aufgerufen werden, die direkt mit diesen Speichersystemen arbeiten. Beispielsweise enthält Visual Studio 2013 einen Speicher-Explorer, mit dem Tabellen- oder Blob-Speicher erkundet werden können, und HDInsight kann auf die im Blob-Speicher gespeicherten Daten zugreifen. Sie können auch mithilfe der [Azure SDKs](/downloads/#) eine Anwendung schreiben, die auf den Azure-Speicher zugreift.

> [AZURE.NOTE]Diagnosen können auch über die Azure PowerShell mit dem Cmdlet **Set-AzureWebsite** aktiviert werden. Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a> Vorgehensweise: Herunterladen von Blobs

Im Dateisystem der Web-App gespeicherte Diagnoseinformationen können direkt über FTP aufgerufen werden. Außerdem können sie als ZIP-Archiv mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle (Azure-CLI) heruntergeladen werden.

Protokolle werden in der folgenden Verzeichnisstruktur gespeichert:

* **Anwendungsprotokolle** - /LogFiles/Application/. Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden.

* **Protokolle für fehlgeschlagene Anforderungen** - /LogFiles/W3SVC#########/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.

* **Detaillierte Fehlerprotokolle** - /LogFiles/DetailedErrors/. Dieser Ordner enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen.

* **Webserverprotokolle** - /LogFiles/http/RawLogs. Dieser Ordner enthält eine oder mehrere Textdateien im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Bereitstellungsprotokolle** - /LogFiles/Git. Dieser Ordner enthält Protokolle, die von den internen Bereitstellungsprozessen der Azure-Web-Apps erzeugt werden, sowie Protokolle für Git-Bereitstellungen.

### FTP

Um Diagnoseinformationen über FTP aufzurufen, wechseln Sie im [Azure-Portal](https://manage.windowsazure.com) zur Seite **Dashboard** Ihrer Web-App. Im Abschnitt **quick glance** können Sie den Link **FTP Diagnostic Logs** verwenden, um die Protokolldateien über FTP aufzurufen. Der Eintrag **Deployment/FTP User** listet den Benutzernamen auf, der zum Zugriff auf die FTP-Site verwendet werden sollte.

> [AZURE.NOTE]Falls der Eintrag **Bereitstellung/FTP-Benutzer** nicht festgelegt ist oder Sie das Kennwort für diesen Benutzer vergessen haben, können Sie einen neuen Benutzer mit zugehörigem Kennwort über den Link **Anmeldeinformationen für die Bereitstellung zurücksetzen** im Abschnitt **Auf einen Blick** im **Dashboard** erstellen.

### Herunterladen mit Azure PowerShell

Starten Sie zum Herunterladen der Protokolldateien eine neue Instanz von Azure PowerShell, und führen Sie den folgenden Befehl aus:

	Save-AzureWebSiteLog -Name webappname

Daraufhin werden die Protokolle für die Web-App, die durch den Parameter **-Name** angegeben wird, in der Datei**logs.zip** im aktuellen Verzeichnis gespeichert.

> [AZURE.NOTE]Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Herunterladen mit der Azure-Befehlszeilenschnittstelle

Zum Herunterladen der Protokolldateien mit der Azure-Befehlszeilenschnittstelle öffnen Sie eine neue Eingabeaufforderung, PowerShell-, Bash- oder Terminalsitzung, und geben Sie den folgenden Befehl ein:

	azure site log download webappname

Damit werden die Protokolle für die Web-App namens 'webappname' in der Datei **diagnostics.zip** im aktuellen Verzeichnis gespeichert.

> [AZURE.NOTE]Wenn Sie die Azure-Befehlszeilenschnittstelle (Azure-CLI) nicht installiert oder nicht für Ihr Azure-Abonnement konfiguriert haben, lesen Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle](../xplat-cli.md) weiter.

## Vorgehensweise: Anzeigen von Protokollen in Application Insights

Visual Studio Application Insights bietet Tools zum Filtern und Suchen von Protokollen sowie zu Korrelation der Protokolle mit Anforderungen und anderen Ereignissen.

1. Fügen Sie Ihrem Projekt in Visual Studio Application Insights SDK hinzu.
 * Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen" aus. Sie werden durch Schritte geführt, zu denen auch die Erstellung einer Application Insights-Ressource gehört. [Weitere Informationen](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. Fügen Sie Ihrem Projekt das Ablaufverfolgungs-Listener-Paket hinzu.
 * Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie "NuGet-Pakete verwalten" aus. Wählen Sie `Microsoft.ApplicationInsights.TraceListener` [Weitere Informationen](../application-insights/app-insights-asp-net-trace-logs.md) aus
3. Laden Sie das Projekt hoch, und führen sie es zum Generieren von Protokolldaten aus.
4. Navigieren Sie im [Azure-Vorschauportal](http://portal.azure.com/) zu der neuen Application Insights-Ressource, und öffnen Sie **Suche**. Ihre Protokolldaten werden zusammen mit der Anforderung, Nutzung und sonstiger Telemetrie angezeigt. Einige Telemetrie kann einige Minuten in Anspruch nehmen, bis sie angezeigt wird: Klicken Sie auf "Aktualisieren". [Weitere Informationen](../application-insights/app-insights-diagnostic-search.md)

[Erfahren Sie mehr über die Leistungsüberwachung mit Application Insights](../insights-perf-analytics.md)

##<a name="streamlogs"></a>Vorgehensweise: Streaming von Protokollen

Beim Entwickeln einer Anwendung ist es häufig nützlich, Protokollinformationen nahezu in Echtzeit zu sehen. Dies kann durch das Streaming von Protokollinformationen in die Entwicklungsumgebung entweder mit Azure PowerShell oder mit der Azure-Befehlszeilenschnittstelle erfolgen.

> [AZURE.NOTE]Einige Protokolltypen puffern die Schreibvorgänge in die Protokolldatei, was zu Störereignissen im Stream führen kann. Beispielsweise kann ein Anwendungsprotokolleintrag für den Besuch einer Seite im Stream vor dem zugehörigen HTTP-Protokolleintrag für die Seitenanforderung angezeigt werden.

> [AZURE.NOTE]Protokoll-Streaming zeigt auch Informationen an, die in eine Textdatei im Ordner **D:\home\LogFiles\** geschrieben werden.

### Streaming mit Azure PowerShell

Starten Sie zum Streaming der Protokollinformationen eine neue Instanz von Azure PowerShell, und führen Sie den folgenden Befehl aus:

	Get-AzureWebSiteLog -Name webappname -Tail

Dadurch wird eine Verbindung zu der Web-App hergestellt, die durch den Parameter **-Name** angegeben ist, und das Streaming von Informationen in das PowerShell-Fenster wird gestartet, sobald Protokollereignisse in der Web-App auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM geschrieben werden, die sich im Verzeichnis "/LogFiles" (d:/home/logfiles) befinden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **-Message**. Beispiel:

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **-Path**. Beispiel:

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

Eine Liste der verfügbaren Pfade wird mit dem Parameter "-ListPath" angezeigt.

> [AZURE.NOTE]Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Streaming mit der Azure-Befehlszeilenschnittstelle

Zum Streaming der Protokollinformationen öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung und geben den folgenden Befehl ein:

	azure site log tail webappname

Dadurch wird eine Verbindung zu der Web-App namens 'webappname' hergestellt, und das Streaming von Informationen in das Fenster wird gestartet, sobald Protokollereignisse in der Web-App auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM geschrieben werden, die sich im Verzeichnis "/LogFiles" (d:/home/logfiles) befinden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **--Filter**. Beispiel:

	azure site log tail webappname --filter Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **--Path**. Beispiel:

	azure site log tail webappname --path http

> [AZURE.NOTE]Wenn Sie die Azure-Befehlszeilenschnittstelle nicht installiert oder nicht für Ihr Azure-Abonnement konfiguriert haben, lesen Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle](../xplat-cli.md) weiter.

##<a name="understandlogs"></a> Vorgehensweise: Verstehen von Diagnoseprotokollen

### Anwendungs-Diagnoseprotokolle

Die Anwendungsdiagnose speichert Informationen in einem bestimmten Format für .NET-Anwendungen, je nachdem, ob Sie Protokolle im Dateisystem, im Tabellenspeicher oder im Blob-Speicher speichern. Der Grunddatensatz ist für alle drei Speichertypen gleich – Datum und Zeit des Ereignisses, Prozess-ID, die das Ereignis erzeugt hat, sowie Ereignistyp (Info, Warnung, Fehler) und Ereignismeldung.

__Dateisystem__

Jede im Dateisystem protokollierte oder per Streaming empfangene Zeile hat das folgende Format:

	{Date}  PID[{process id}] {event type/level} {message}

Beispielsweise würde ein Fehlerereignis in etwa wie folgt aussehen:

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Bei der Protokollierung im Dateisystem erhalten Sie nur die grundlegendsten Informationen der drei verfügbaren Methoden, nämlich Zeit, Prozess-ID, Ereignistyp und Meldung.

__Tabellenspeicherung__

Bei der Protokollierung im Tabellenspeicher werden zusätzliche Eigenschaften verwendet, um die Suche nach Daten in der Tabelle zu erleichtern und ausführlichere Informationen zum Ereignis anzugeben. Die folgenden Eigenschaften (Spalten) werden für jedes Element (Zeile) in der Tabelle angegeben.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Eigenschaftenname</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Wert/Format</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Datum/Zeit des Ereignisses im Format JJJJMMTTHH</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Ein GUID-Wert, der das Element eindeutig identifiziert</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">Datum und Zeit des Auftretens des Ereignisses</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Datum und Zeit des Auftretens des Ereignisses im Tick-Format (höhere Präzision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Der Name der Web-App</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Ebene</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Ereignistyp (z. B. Info, Warnung, Fehler)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">Die Ereignis-ID dieses Ereignisses<br>Der Standardwert ist 0, wenn keine andere Angabe erfolgt.</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instanz der Web-App, in der das Ereignis auftrat</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">Prozess-ID</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Thread-ID des Threads, der das Ereignis erzeugt hat</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Meldung zu den Ereignisdetails</td>
</tr>
</table>

__Blob-Speicher__

Bei der Protokollierung in einem Blob-Speicher werden die Daten im CSV-Format (durch Kommas getrennte Werte) gespeichert. Ähnlich wie beim Tabellenspeicher werden zusätzliche Felder protokolliert, um ausführlichere Informationen zum Ereignis anzugeben. Die folgenden Eigenschaften werden für jede Zeile in der CSV-Datei angegeben:

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Eigenschaftenname</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Wert/Format</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Date</td>
<td style="border:1px solid black;vertical-align:top">Datum und Zeit des Auftretens des Ereignisses</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Ereignistyp (z. B. Info, Warnung, Fehler)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Der Name der Web-App</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instanz der Web-App, in der das Ereignis auftrat</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Datum und Zeit des Auftretens des Ereignisses im Tick-Format (höhere Präzision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">Die Ereignis-ID dieses Ereignisses<br>Der Standardwert ist 0, wenn keine andere Angabe erfolgt.</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">Prozess-ID</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Thread-ID des Threads, der das Ereignis erzeugt hat</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Meldung zu den Ereignisdetails</td>
</tr>
</table>

In einem Blob gespeicherte Daten sehen in etwa wie folgt aus:

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE]Die erste Zeile des Protokolls enthält die Spaltentitel, wie in diesem Beispiel angegeben.

### Verfolgung fehlgeschlagener Anforderungen

Die Verfolgungsprotokolle für fehlgeschlagene Anforderungen werden in XML-Dateien mit Namen __fr######.xml__ gespeichert. Um das Anzeigen der protokollierten Informationen zu erleichtern, wird ein XSL-Stylesheet namens __freb.xsl__ im selben Verzeichnis wie die XML-Dateien bereitgestellt. Wenn Sie eine der XML-Dateien in Internet Explorer öffnen, wird das XSL-Stylesheet verwendet, um eine formatierte Ansicht der Verfolgungsinformationen anzuzeigen. Diese sieht in etwa wie folgt aus:

![Anzeige fehlgeschlagener Anforderungen im Browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Detaillierte Fehlerprotokolle

Detaillierte Fehlerprotokolle sind HTML-Dokumente, die ausführlichere Informationen zu aufgetretenen HTTP-Fehlern bereitstellen. Da es sich um einfache HTML-Dokumente handelt, können sie in einem Webbrowser angezeigt werden.

### Webserverprotokolle

Webserverprotokolle werden im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx) gespeichert. Die Informationen können in einem Texteditor gelesen oder mit einem Dienstprogramm wie [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619) analysiert werden.

> [AZURE.NOTE]Die von den Azure-Web-Apps erzeugten Protokolle unterstützen nicht die Felder __s-computername__, __s-ip__ oder __cs-version__.

##<a name="nextsteps"></a> Nächste Schritte

- [Überwachen von Web-Apps](/de-de/manage/services/web-sites/how-to-monitor-websites/)
- [Problembehandlung von Azure-Web-Apps in Visual Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [Analyse von Web-App-Protokollen in HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO4-->