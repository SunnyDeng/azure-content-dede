<properties linkid="develop-net-common-tasks-diagnostics-logging-and-instrumentation" urlDisplayName="Enable diagnostic logging" pageTitle="Enable diagnostic logging - Azure Web Sites" metaKeywords="Azure diagnostics web sites, Azure Management Portal diagnostics, Azure diagnostics, web site diagnostics, web site debug" description="Learn how to enable diagnostic logging and add instrumentation to your application, as well as how to access the information logged by Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Enable diagnostic logging for Azure Web Sites" authors="larryfr" solutions="" manager="" editor="" />

Aktivieren der Diagnoseprotokollierung für Azure-Websites
=========================================================

Azure bietet integrierte Diagnosefunktionen zur Unterstützung des Debugging einer auf Azure-Websites gehosteten Anwendung. In diesem Artikel erfahren Sie, wie Sie die Diagnoseprotokollierung aktivieren und Instrumentierung zu Ihrer Anwendung hinzufügen und wie Sie die von Azure protokollierten Informationen abrufen.

> [WACOM.NOTE] In diesem Artikel wird die Verwendung von Diagnoseprotokollen mit dem Azure-Verwaltungsportal, der Azure PowerShell und der Azure plattformübergreifenden Befehlszeilenschnittstelle beschrieben. Informationen zum Arbeiten mit Diagnoseprotokollen in Visual Studio finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

Inhaltsverzeichnis
------------------

-   [Was ist: Website-Diagnose?](#whatisdiag)
-   [Vorgehensweise: Aktivieren der Diagnosefunktion](#enablediag)
-   [Vorgehensweise: Herunterladen von Protokollen](#download)
-   [Vorgehensweise: Streaming von Protokollen](#streamlogs)
-   [Vorgehensweise: Verstehen von Diagnoseprotokollen](#understandlogs)
-   [Nächste Schritte](#nextsteps)

## Was ist Website-Diagnose?

Azure-Websites bieten Diagnosefunktionen zum Protokollieren von Informationen sowohl über den Webserver als auch über die Webanwendung. Diese sind logisch in **Site-Diagnose** und **Anwendungsdiagnose** unterteilt.

### Site-Diagnose

Unter "site diagnostics" können Sie Folgendes aktivieren oder deaktivieren:

-   **Detailed Error Logging** - Protokolliert detaillierte Fehlerinformationen für HTTP-Statuscodes, die auf einen Fehler hinweisen (Statuscode 400 oder höher). Diese können Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat.
-   **Failed Request Tracing** - Protokolliert detaillierte Informationen zu fehlgeschlagenen Anforderungen, einschließlich der Verfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies kann nützlich sein, wenn Sie versuchen, die Leistung von Websites zu verbessern oder herauszufinden, warum ein bestimmter HTTP-Fehler zurückgegeben wird.
-   **Web Server Logging** - Protokolliert alle HTTP-Transaktionen einer Website im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Dieser Bericht ist nützlich, wenn Sie allgemeine Website-Kennzahlen bestimmen möchten, wie die Anzahl der verarbeiteten Anfragen oder wie viele Anforderungen von einer bestimmten IP-Adresse stammen.

### Anwendungsdiagnose

Mit der Option "Application Diagnostics" können Sie die von einer Webanwendung erzeugten Informationen erfassen. ASP.NET-Anwendungen können die Klasse [System.Diagnostics.Trace](http://msdn.microsoft.com/en-us/library/36hhw2t6.aspx) verwenden, um Informationen im Anwendungs-Diagnoseprotokoll aufzuzeichnen. Beispiel:

    System.Diagnostics.Trace.TraceError("Wenn Sie dies lesen, ist ein Fehler aufgetreten");

Anwendungsdiagnosen ermöglichen Ihnen, Fehler in der laufenden Anwendung zu ermitteln, indem Informationen ausgegeben werden, sobald bestimmte Codes verwendet werden. Dies ist besonders nützlich, wenn Sie versuchen zu bestimmen, warum ein bestimmter Pfad vom Code verwendet wurde, in der Regel wenn der Pfad einen Fehler oder anderes unerwünschtes Verhalten verursacht.

Informationen zum Arbeiten mit Anwendungsdiagnosen in Visual Studio finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](http://www.windowsazure.com/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

> [WACOM.NOTE] Im Gegensatz zum Ändern der Datei "web.config" wird durch das Aktivieren der Anwendungsdiagnose oder durch das Ändern des Diagnoseprotokollumfangs nicht die Anwendungsdomäne neu gestartet, in der die Anwendung ausgeführt wird.

Azure-Websites protokollieren ebenfalls Bereitstellungsinformationen, wenn Sie eine Anwendung auf einer Website veröffentlichen. Dies erfolgt automatisch, und es gibt keine Konfigurationseinstellung für die Bereitstellungsprotokollierung. Anhand der Bereitstellungsprotokollierung können Sie bestimmen, warum eine Bereitstellung fehlgeschlagen ist. Wenn Sie beispielsweise ein benutzerdefiniertes Bereitstellungsskript verwenden, können Sie die Bereitstellungsprotokollierung verwenden, um festzustellen, warum das Skript fehlgeschlagen ist.

## Vorgehensweise: Aktivieren der Diagnosefunktion

Diagnosen können auf der Seite **Configure** der Azure-Website im [Azure-Verwaltungsportal](https://manage.microsoft.com) aktiviert werden. Verwenden Sie auf der Seite **Configure** die Bereiche **application diagnostics** und **site diagnostics**, um die Protokollierung zu aktivieren.

Bei Aktivierung von **application diagnostics** müssen Sie auch **logging level** für die Protokollierungsebene angeben sowie **file system**, **table storage** oder **blob storage** als Protokollspeicherort. Obwohl alle drei Speicherorte dieselben grundlegenden Informationen zu protokollierten Ereignissen bieten, zeichnen **table storage** und **blob storage** zusätzliche Informationen wie Instanz-ID, Thread-ID und einen ausführlicheren Zeitstempel (Tick-Format) als die Protokollierung im **file system** auf.

Bei Aktivierung von **site diagnostics** müssen Sie **storage** oder **file system** für die Option **web server logging** auswählen. Durch Auswahl von **storage** können Sie ein Speicherkonto festlegen und dann einen Blob-Container hinzufügen, in den die Protokolle geschrieben werden. Alle anderen Protokolle für **site diagnostics** werden nur in das Dateisystem geschrieben.

> [WACOM.NOTE] In **table storage** oder **blob storage** gespeicherte Informationen können nur über einen Speicherclient oder eine Anwendung aufgerufen werden, die direkt mit diesen Speichersystemen arbeiten. Beispielsweise enthält Visual Studio 2013 einen Speicher-Explorer, mit dem Tabellen- oder Blob-Speicher erkundet werden können, und HDInsight kann auf die im Blob-Speicher gespeicherten Daten zugreifen. Sie können auch mithilfe der [Azure SDKs](http://www.windowsazure.com/en-us/downloads/#) eine Anwendung schreiben, die auf den Azure-Speicher zugreift.

Die folgenden Einstellungen sind bei der Aktivierung von **application diagnostics** verfügbar:

-   **Logging level** - Ermöglicht das Filtern der aufgezeichneten Informationen nach **informational**, **warning** oder **error**. Sie können auch **verbose** auswählen, um alle von der Anwendung erzeugten Informationen zu protokollieren. **Logging level** kann auf **file system**, **table storage** oder **blob storage** eingestellt werden.
-   **File system** - Speichert die Anwendungsdiagnoseinformationen im Dateisystem der Website. Diese Dateien können über FTP aufgerufen werden oder als ZIP-Archiv mit Azure PowerShell oder den Azure-Befehlszeilentools heruntergeladen werden.
-   **Table storage** - Speichert die Anwendungsdiagnoseinformationen im angegebenen Azure-Speicherkonto unter dem Tabellennamen.
-   **Blob storage** - Speichert die Anwendungsdiagnoseinformationen im angegebenen Azure-Speicherkonto im Blob-Container.
-   **Retention period** - Standardmäßig werden Protokolle nicht automatisch aus dem **blob storage** gelöscht. Falls die Protokolle automatisch gelöscht werden sollen, wählen Sie **set retention** aus, und geben Sie die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen.

> [WACOM.NOTE] Sie können gleichzeitig eine Kombination aus Dateisystem-, Tabellen- und Blob-Speicher aktivieren und jeweils individuelle Konfigurationen der Protokollierungsebene festlegen. Sie können beispielsweise Fehler und Warnmeldungen als langfristige Protokollierungslösung im Blob-Speicher protokollieren, während die Dateisystemprotokollierung ausführlich erfolgt.

> [WACOM.NOTE] Diagnosen können auch über die Azure PowerShell mit dem Cmdlet **Set-AzureWebsite** aktiviert werden. Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/).

## Vorgehensweise: Herunterladen von Protokollen

Im Dateisystem der Website gespeicherte Diagnoseinformationen können direkt über FTP aufgerufen werden. Außerdem können sie als ZIP-Archiv mit Azure PowerShell oder den Azure-Befehlszeilentools heruntergeladen werden.

Protokolle werden in der folgenden Verzeichnisstruktur gespeichert:

-   **Anwendungsprotokolle** - /LogFiles/Application/. Dieser Ordner enthält eine oder mehrere Textdateien mit Informationen, die bei der Anwendungsprotokollierung erzeugt wurden.

-   **Protokolle für fehlgeschlagene Anforderungen** - /LogFiles/W3SVC########\#/. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.

-   **Detaillierte Fehlerprotokolle** - /LogFiles/DetailedErrors/. Dieser Ordner enthält eine oder mehrere HTM-Dateien, die umfangreiche Informationen zu aufgetretenen HTTP-Fehlern bereitstellen.

-   **Webserverprotokolle** - /LogFiles/http/RawLogs. Dieser Ordner enthält eine oder mehrere Textdateien im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

-   **Bereitstellungsprotokolle** - /LogFiles/Git. Dieser Ordner enthält Protokolle, die von den internen Bereitstellungsprozessen der Azure-Websites erzeugt werden, sowie Protokolle für Git-Bereitstellungen.

### FTP

Um Diagnoseinformationen über FTP aufzurufen, wechseln Sie zur Seite **Dashboard** Ihrer Website im Azure-Verwaltungsportal. Im Abschnitt **quick glance** können Sie den Link **FTP Diagnostic Logs** verwenden, um die Protokolldateien über FTP aufzurufen. Der Eintrag **Deployment/FTP User** listet den Benutzernamen auf, der zum Zugriff auf die FTP-Site verwendet werden sollte.

> [WACOM.NOTE] Falls der Eintrag **Deployment/FTP User** nicht festgelegt ist oder Sie das Kennwort für diesen Benutzer vergessen haben, können Sie einen neuen Benutzer mit zugehörigem Kennwort über den Link **Reset deployment credentials** im Abschnitt **quick glance** im **Dashboard** erstellen.

### Herunterladen mit Azure PowerShell

Starten Sie zum Herunterladen der Protokolldateien eine neue Instanz von Azure PowerShell, und führen Sie den folgenden Befehl aus:

    Save-AzureWebSiteLog -Name websitename

Daraufhin werden die Protokolle für die Website, die durch den Parameter **-Name** angegeben wird, in der Datei **logs.zip** im aktuellen Verzeichnis gespeichert.

> [WACOM.NOTE] Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Herunterladen mit Azure-Befehlszeilentools

Zum Herunterladen der Protokolldateien mit den Azure-Befehlszeilentools öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung und geben den folgenden Befehl ein:

    azure site log download websitename

Daraufhin werden die Protokolle für die Website namens "websitename" in der Datei **diagnostics.zip** im aktuellen Verzeichnis gespeichert.

> [WACOM.NOTE] Wenn Sie die Azure-Befehlszeilentools nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden der Azure-Befehlszeilentools](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/).

## Vorgehensweise: Streaming von Protokollen

Beim Entwickeln einer Anwendung ist es häufig nützlich, Protokollinformationen nahezu in Echtzeit zu sehen. Dies kann durch das Streaming von Protokollinformationen in die Entwicklungsumgebung entweder mit der PowerShell oder den Azure-Befehlszeilentools erfolgen.

> [WACOM.NOTE] Einige Protokolltypen puffern die Schreibvorgänge in die Protokolldatei, was zu Störereignissen im Stream führen kann. Beispielsweise kann ein Anwendungsprotokolleintrag für den Besuch einer Seite im Stream vor dem zugehörigen HTTP-Protokolleintrag für die Seitenanforderung angezeigt werden.

> [WACOM.NOTE] Protokoll-Streaming zeigt auch Informationen an, die in eine Textdatei im Ordner **D:\\home\\LogFiles\\** geschrieben werden.

### Streaming mit Azure PowerShell

Starten Sie zum Streaming der Protokollinformationen eine neue Instanz von Azure PowerShell, und führen Sie den folgenden Befehl aus:

    Get-AzureWebSiteLog -Name websitename -Tail

Dadurch wird eine Verbindung zur Website hergestellt, die durch den Parameter **-Name** angegeben ist, und das Streaming von Informationen in das PowerShell-Fenster gestartet, sobald Protokollereignisse auf der Website auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM geschrieben werden, die sich im Verzeichnis "/LogFiles" (d:/home/logfiles) befinden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **-Message**. Beispiel:

    Get-AzureWebSiteLog -Name websitename -Tail -Message Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **-Path**. Beispiel:

    Get-AzureWebSiteLog -Name websitename -Tail -Path http

Eine Liste der verfügbaren Pfade wird mit dem Parameter "-ListPath" angezeigt.

> [WACOM.NOTE] Wenn Sie Azure PowerShell nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden von Azure PowerShell](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Streaming mit Azure-Befehlszeilentools

Zum Streaming der Protokollinformationen öffnen Sie eine neue Eingabeaufforderung, PowerShell, Bash- oder Terminalsitzung und geben den folgenden Befehl ein:

    azure site log tail websitename

Dadurch wird eine Verbindung zur Website namens "websitename" hergestellt und das Streaming von Informationen in das Fenster gestartet, sobald Protokollereignisse auf der Website auftreten. Alle Informationen, die in Dateien mit der Erweiterung TXT, LOG oder HTM geschrieben werden, die sich im Verzeichnis "/LogFiles" (d:/home/logfiles) befinden, werden an die lokale Konsole gestreamt.

Um bestimmte Ereignisse wie beispielsweise Fehler zu filtern, verwenden Sie den Parameter **--Filter**. Beispiel:

    azure site log tail websitename --filter Error

Um bestimmte Protokolltypen wie HTTP zu filtern, verwenden Sie den Parameter **--Path**. Beispiel:

    azure site log tail websitename --path http

> [WACOM.NOTE] Wenn Sie die Azure-Befehlszeilentools nicht installiert haben oder nicht zur Verwendung des Azure-Abonnements konfiguriert haben, finden Sie weitere Informationen unter [Verwenden der Azure-Befehlszeilentools](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/).

## Vorgehensweise: Verstehen von Diagnoseprotokollen

### Anwendungs-Diagnoseprotokolle

Die Anwendungsdiagnose speichert Informationen in einem bestimmten Format für .NET-Anwendungen, je nachdem, ob Sie Protokolle im Dateisystem, im Tabellenspeicher oder im Blob-Speicher speichern. Der Grunddatensatz ist für alle drei Speichertypen gleich – Datum und Zeit des Ereignisses, Prozess-ID, die das Ereignis erzeugt hat, sowie Ereignistyp (Info, Warnung, Fehler) und Ereignismeldung.

**Dateisystem**

Jede im Dateisystem protokollierte oder per Streaming empfangene Zeile hat das folgende Format:

    {Datum}  PID[{Prozess-ID}] {Ereignistyp/Ebene} {Meldung}

Beispielsweise würde ein Fehlerereignis in etwa wie folgt aussehen:

    2014-01-30T16:36:59  PID[3096] Error       Schwerwiegender Fehler auf der Seite!

Bei der Protokollierung im Dateisystem erhalten Sie nur die grundlegendsten Informationen der drei verfügbaren Methoden, nämlich Zeit, Prozess-ID, Ereignistyp und Meldung.

**Tabellenspeicher**

Bei der Protokollierung im Tabellenspeicher werden zusätzliche Eigenschaften verwendet, um die Suche nach Daten in der Tabelle zu erleichtern und ausführlichere Informationen zum Ereignis anzugeben. Die folgenden Eigenschaften (Spalten) werden für jedes Element (Zeile) in der Tabelle angegeben.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Eigenschaftenname
</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Wert/Format</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Datum/Zeit des Ereignisses im Format JJJJMMTTHH
</td>
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
<td style="border:1px solid black;vertical-align:top">Name der Website</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Ereignistyp (z. B. Info, Warnung, Fehler)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">Die Ereignis-ID<br>Bei fehlender Angabe standardmäßig 0</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instanz der Website, auf der das Ereignis auftrat</td>
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

**Blob-Speicher**

Bei der Protokollierung in einem Blob-Speicher werden die Daten im CSV-Format (durch Kommas getrennte Werte) gespeichert. Ähnlich wie beim Tabellenspeicher werden zusätzliche Felder protokolliert, um ausführlichere Informationen zum Ereignis anzugeben. Die folgenden Eigenschaften werden für jede Zeile in der CSV-Datei angegeben:

<table  style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th  style="width:45%;border:1px solid black;background-color:#0099dd">Eigenschaftenname</th>

<th  style="border:1px solid black;vertical-align:top;background-color:#0099dd">Wert/Format</th>

</tr>

</thead>

<tr>
<td  style="border:1px solid black;vertical-align:top">Date</td>

<td  style="border:1px solid black;vertical-align:top">Datum und Zeit des Auftretens des Ereignisses</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>

<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Ereignistyp (z. B. Info, Warnung, Fehler)</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top">ApplicationName</td>

<td  style="border:1px solid black;vertical-align:top">Name der Website</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>

<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instanz der Website, auf der das Ereignis auftrat</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>

<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Datum und Zeit des Auftretens des Ereignisses im Tick-Format (höhere Präzision)</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top">EventId</td>

<td  style="border:1px solid black;vertical-align:top">Die Ereignis-ID<br  />
Bei fehlender Angabe standardmäßig 0</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top">Pid</td>

<td  style="border:1px solid black;vertical-align:top">Prozess-ID</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>

<td  style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Thread-ID des Threads, der das Ereignis erzeugt hat</td>

</tr>

<tr>
<td  style="border:1px solid black;vertical-align:top">Message</td>

<td  style="border:1px solid black;vertical-align:top">Meldung zu den Ereignisdetails</td>

</tr>

</table>

In einem Blob gespeicherte Daten sehen in etwa wie folgt aus:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [WACOM.NOTE] Die erste Zeile des Protokolls enthält die Spaltentitel, wie in diesem Beispiel angegeben.

### Verfolgung fehlgeschlagener Anforderungen

Die Verfolgungsprotokolle für fehlgeschlagene Anforderungen werden in XML-Dateien mit Namen **fr######.xml** gespeichert. Um das Anzeigen der protokollierten Informationen zu erleichtern, wird ein XSL-Stylesheet namens **freb.xsl** im selben Verzeichnis wie die XML-Dateien bereitgestellt. Wenn Sie eine der XML-Dateien in Internet Explorer öffnen, wird das XSL-Stylesheet verwendet, um eine formatierte Ansicht der Verfolgungsinformationen anzuzeigen. Diese sieht in etwa wie folgt aus:

![Anzeige fehlgeschlagener Anforderungen im Browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Detaillierte Fehlerprotokolle

Detaillierte Fehlerprotokolle sind HTML-Dokumente, die ausführlichere Informationen zu aufgetretenen HTTP-Fehlern bereitstellen. Da es sich um einfache HTML-Dokumente handelt, können sie in einem Webbrowser angezeigt werden.

### Webserverprotokolle

Webserverprotokolle werden im [erweiterten W3C-Protokolldateiformat](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx) gespeichert. Die Informationen können in einem Texteditor gelesen oder mit einem Dienstprogramm wie [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619) analysiert werden.

> [WACOM.NOTE] Die von den Azure-Websites erzeugten Protokolle unterstützen nicht die Felder **s-computername**, **s-ip** und **cs-version**.

## Nächste Schritte

-   [Überwachen von Websites](/en-us/manage/services/web-sites/how-to-monitor-websites/)
-   [Lernprogramm – Problembehandlung von Websites](/en-us/develop/net/best-practices/troubleshooting-web-sites/)
-   [Problembehandlung von Azure-Websites in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
-   [Analyse von Website-Protokollen in HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

