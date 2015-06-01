<properties 
	pageTitle="End-to-End-Problembehandlung mit Azure-Speichermetriken und -Protokollen sowie AzCopy und Message Analyzer | Microsoft Azure" 
	description="Ein Lernprogramm zur End-to-End-Problembehandlung mit Azure-Speicheranalyse, AzCopy und Microsoft Message Analyzer" 
	services="storage" 
	documentationCenter="dotnet" 
	authors="tamram" 
	manager="adinah"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/10/2015" 
	ms.author="tamram"/>

# End-to-End-Problembehandlung mit Azure-Speichermetriken und -Protokollen sowie AzCopy und Message Analyzer 

## Übersicht

Die Diagnose und das Beheben von Problemen ist eine wichtige Fähigkeit für die Erstellung und den Support von Clientanwendungen mit Microsoft Azure-Speicher. Aufgrund der dezentralen Art einer Azure-Anwendung kann sich die Diagnose und Behebung von Fehlern und Leistungsproblemen als komplexer erweisen als in herkömmlichen Umgebungen.

In diesem Lernprogramm wird veranschaulicht, wie Clients bestimmte Fehler identifizieren, die sich möglicherweise auf die Leistung auswirken, und wie die End-to-End-Problembehandlung mithilfe von Tools von Microsoft und des Azure-Speichers zur Optimierung der Clientanwendung funktioniert.

Dieses Lernprogramm bietet eine praktische Beschreibung eines Szenarios der End-to-End-Problembehandlung. Ein detailliertes konzeptionelles Handbuch für die Problembehandlung von Azure-Speicheranwendungen finden Sie unter [Überwachung, Diagnose und Problembehandlung im Speicher](../articles/storage-monitoring-diagnosing-troubleshooting/).

## Tools zur Problembehandlung von Azure-Speicheranwendungen

Um Probleme in Clientanwendungen mit Microsoft Azure-Speicher zu beheben, können Sie eine Kombination verschiedener Tools verwenden, um zu ermitteln, wann ein Problem aufgetreten ist und was die Ursache des Problems sein kann. Zu diesen Tools zählen:

- **Azure-Speicheranalyse**. [Azure-Speicheranalyse](http://msdn.microsoft.com/library/azure/hh343270.aspx) bietet Metriken und Protokolle für den Azure-Speicher.
	- **Speichermetriken** zeichnet Transaktions- und Kapazitätsmetriken für das Speicherkonto auf. Mithilfe von Metriken können Sie die Leistung Ihrer Anwendung anhand einer Vielzahl verschiedener Messwerte bestimmen. Weitere Informationen zu den Arten der von der Speicheranalyse erfassten Metriken finden Sie unter [Schema der Tabellen für Speicheranalysemetriken](http://msdn.microsoft.com/library/azure/hh343264.aspx). 

	- **Speicherprotokollierung** erfasst jede Anfrage an die Azure-Speicherdienste in einem serverseitigen Protokoll. Das Protokoll erfasst ausführliche Daten für jede Anfrage, einschließlich des ausgeführten Vorgangs, des Status des Vorgangs und der Latenzinformationen. Weitere Informationen zu den Anfrage- und Antwortdaten, die von der Speicheranalyse in die Protokolle geschrieben werden, finden Sie unter [Protokollformat der Speicheranalyse](http://msdn.microsoft.com/library/azure/hh343259.aspx).

- **Das Azure-Verwaltungsportal**. Sie können die Protokollierung und Metriken für das Speicherkonto im Portal konfigurieren. Sie können auch Diagramme und Grafiken anzeigen, die darstellen, welche Leistung die Anwendung über einen gewissen Zeitraum hinweg erbringt, und Warnungen im Portal konfigurieren, die Sie benachrichtigen, wenn die Leistung bezüglich einer bestimmten Metrik von den Erwartungen abweicht.
	
	In diesem Lernprogramm wird gezeigt, wie Sie Ihr Speicherkonto mithilfe des [Azure-Verwaltungsportals](https://manage.windowsazure.com/) überwachen können. Informationen zum Konfigurieren der Überwachung im Portal finden Sie unter [Überwachen eines Speicherkontos](storage-monitor-storage-account.md).

	Sie können auch das [Azure-Vorschauportal](https://portal.azure.com/) für die neueste Funktionalität verwenden, jedoch handelt es sich hierbei noch um eine Vorschauversion.

- **AzCopy**. Serverprotokolle für den Azure-Speicher werden als Blobs gespeichert, damit Sie AzCopy verwenden können, um die Protokoll-Blobs für die Analyse mithilfe von Microsoft Message Analyzer in ein lokales Verzeichnis zu kopieren. Weitere Informationen zu AzCopy finden Sie unter [Verwenden von AzCopy mit Microsoft Azure Storage](storage-use-azcopy.md).

- **Microsoft Message Analyzer**. Message Analyzer ist ein Tool, das Protokolldateien in einem grafischen Format anzeigt, das es Ihnen erleichtert, Protokolldaten zu filtern, zu durchsuchen und zu gruppieren und so nützliche Zusammenstellungen für die Analyse von Fehlern und Leistungsproblemen zu erhalten. Weitere Informationen zu Message Analyzer finden Sie unter [Microsoft Message Analyzer Operating Guide](http://technet.microsoft.com/library/jj649776.aspx) (in englischer Sprache).

## Informationen zum Beispielszenario

Für dieses Lernprogramm untersuchen wir ein Szenario, in denen Metriken des Azure-Speichers eine niedrige prozentuale Erfolgsrate für eine Anwendung anzeigt, die den Azure-Speicher aufruft. Die Metrik zur niedrigen prozentualen Erfolgsrate (im Azure-Portal und den Metrikentabellen als **PercentSuccess** angezeigt) erfasst Vorgänge, die zwar erfolgreich waren, aber einen HTTP-Statuscode über 299 zurückgeben. In den serverseitigen Speicher-Protokollierungsdateien sind diese Vorgänge mit dem Transaktionsstatus **ClientOtherErrors** erfasst. Ausführliche Informationen zur Metrik zur niedrigen prozentualen Erfolgsrate finden Sie unter [Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokolleinträge enthalten Vorgänge mit Transaktionsstatus "ClientOtherErrors"](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure-Speichervorgänge geben möglicherweise als Teil ihrer normalen Funktionalität HTTP-Statuscodes zurück, die höher sind als 299. Diese Fehler zeigen jedoch in einigen Fällen an, dass Sie Ihre Clientanwendung optimieren können, um eine bessere Leistung zu erzielen.

In diesem Szenario gehen wir davon aus, dass es sich bei Werten unter 100 % um eine niedrige prozentuale Erfolgsrate handelt. Sie können jedoch Ihren Anforderungen entsprechend eine andere Metrikgrenze auswählen. Es wird empfohlen, dass Sie beim Testen der Anwendung eine grundlegende Toleranz für die wichtigen Leistungsmetriken festlegen. Beispielsweise können Sie basierend auf Tests bestimmen, dass Ihre Anwendung eine gleichbleibende prozentuale Erfolgsrate von 90 oder 85 % erzielen sollte. Wenn Ihre Metrikdaten anzeigen, dass die Anwendung von dieser Zahl abweicht, können Sie die Ursache der Abweichung untersuchen.

Nachdem wir in unserem Beispielszenario eingerichtet haben, dass die Metrik für die prozentuale Erfolgsrate unter 100 % liegt, untersuchen wir die Protokolle, um die Fehler zu finden, die mit diesen Metriken zusammenhängen, und nutzen sie, um die Ursache der geringeren prozentualen Erfolgsrate zu finden. Insbesondere konzentrieren wir uns auf Fehler im Bereich von 400. Daraufhin untersuchen wir Fehler 404 (nicht gefunden) genauer.

### Einige Ursachen von Fehlern im Bereich von 400

Die Beispiele unten zeigen Stichproben einiger Fehler im Bereich von 400 für Anfragen an den Azure-Blobspeicher sowie ihre möglichen Ursachen. Jeder dieser Fehler sowie Fehler im Bereich von 300 und 500 können die geringere prozentuale Erfolgsrate verursachen.

Beachten Sie, dass die folgenden Listen nicht vollständig sind. Informationen zu allgemeinen Azure-Speicherfehlern und Fehlern der einzelnen Speicherdienste finden Sie unter [Status- und Fehlercodes](http://msdn.microsoft.com/library/azure/dd179382.aspx) auf MSDN.

**Beispiele für Statuscode 404 (nicht gefunden)**

Tritt auf, wenn ein Lesevorgang für einen Container oder einen Blob fehlschlägt, weil der Blob oder Container nicht gefunden wurde.

- Tritt auf, wenn ein Container oder Blob vor dieser Anfrage von einem anderen Client gelöscht wurde. 
- Tritt auf, wenn Sie einen API-Aufruf verwenden, der den Container oder Blob erstellt, nachdem überprüft wurde, ob er vorhanden ist. Die APIs CreateIfNotExists führen zunächst einen HEAD-Aufruf durch, um das Vorhandensein des Containers oder Blobs zu überprüfen. Wenn hierbei festgestellt wird, dass er nicht vorhanden ist, wird ein Fehler 404 zurückgegeben und ein zweiter PUT-Aufruf durchgeführt, um den Container oder Blob zu erstellen.

**Beispiele für Statuscode 409 (Konflikt)**

- Tritt auf, wenn Sie eine Create-API verwenden, um einen neuen Container oder Blob zu erstellen, ohne zunächst sein Vorhandensein zu überprüfen, sodass ein Container oder Blob mit einem bereits vorhandenen Namen erstellt wird. 
- Tritt auf, wenn ein Container gelöscht wird und Sie versuchen, einen neuen Container mit demselben Namen zu erstellen, bevor der Löschvorgang abgeschlossen ist.
- Tritt auf, wenn Sie eine Lease für einen Container oder Blob angeben, obwohl bereits eine Lease vorhanden ist.
 
**Beispiele für Statuscode 412 (Vorbedingung nicht erfüllt)**

- Tritt auf, wenn die von einem Bedingungsheader angegebene Bedingung nicht erfüllt wurde.
- Tritt auf, wenn die angegebene Lease-ID nicht mit der Lease-ID des Containers oder Blobs übereinstimmt.

## Generieren von Protokolldateien für die Analyse

In diesem Lernprogramm verwenden wir Message Analyzer, um mit drei verschiedenen Typen von Protokolldateien zu arbeiten. Sie können jedoch mit jeder der folgenden Dateien arbeiten:

- Das **Serverprotokoll**, das bei Aktivierung der Azure-Speicherprotokollierung erstellt wird. Das Serverprotokoll enthält Daten zu jedem Vorgang, der für einen der Azure-Speicherdienste aufgerufen wird – Blob, Warteschlange, Tabelle und Datei. Das Serverprotokoll gibt an, welcher Vorgang aufgerufen und welcher Statuscode zurückgegeben wurde, und enthält weitere Details zur Anfrage und Antwort.
- Das **.NET-Clientprotokoll**, das bei Aktivierung der clientseitigen Protokollierung innerhalb Ihrer .NET-Anwendung erstellt wird. Das Clientprotokoll enthält detaillierte Informationen darüber, wie der Client die Anfrage vorbereitet und die Antwort empfängt und verarbeitet. 
- Das **HTTP-Netzwerk-Ablaufverfolgungsprotokoll**, das Daten zur HTTP/HTTPS-Anfrage und zur Antwort sammelt, einschließlich der Vorgänge im Azure-Speicher. In diesem Lernprogramm generieren wir die Netzwerkablaufverfolgung über Message Analyzer.

### Konfigurieren der serverseitigen Protokollierung und Metriken

Zunächst müssen wir die Azure-Speicherprotokollierung und -metriken konfigurieren, damit uns Daten von der Clientanwendung zur Analyse vorliegen. Sie können die Protokollierung und die Metriken auf verschiedene Arten konfigurieren: über das Azure-Verwaltungsportal, mithilfe von PowerShell oder programmgesteuert. Weitere Informationen zum Konfigurieren der Protokollierung und der Metriken finden Sie unter [Aktivieren der Speichermetriken und Anzeigen von Metrikdaten](http://msdn.microsoft.com/library/azure/dn782843.aspx) und [Aktivieren der Speicherprotokollierung und Zugreifen auf Protokolldaten](http://msdn.microsoft.com/library/azure/dn782840.aspx) auf MSDN.

**Über das Verwaltungsportal**

Folgen Sie zum Konfigurieren der Protokollierung und der Metriken für das Speicherkonto den Anweisungen unter [Überwachen eines Speicherkontos](storage-monitor-storage-account.md).

> [AZURE.NOTE]Es ist nicht möglich, mithilfe des Azure-Verwaltungsportals Minutenmetriken festzulegen. Allerdings empfiehlt es sich für dieses Lernprogramm sowie für die Untersuchung von Leistungsproblemen mit Ihrer Anwendung, diese Metriken festzulegen. Sie können die Minutenmetriken wie unten beschrieben mithilfe von PowerShell, programmgesteuert oder über das Azure-Vorschauportal festlegen.
>
> Beachten Sie, dass das Azure-Verwaltungsportal keine Minutenmetriken, sondern nur Stundenmetriken anzeigen kann.

**Über PowerShell**

Informationen zum Einstieg in PowerShell für Azure finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md).

1. Verwenden Sie das [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx)-Cmdlet, um Ihr Azure-Benutzerkonto dem PowerShell-Fenster hinzuzufügen:

	```
	Add-AzureAccount
	```

2. Geben Sie im Fenster **Bei Windows Azure anmelden** die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.
3. Legen Sie als Standardspeicherkonto das Speicherkonto fest, das Sie für das Lernprogramm verwenden, indem Sie die folgenden Befehle im PowerShell-Fenster ausführen:

	```
	$SubscriptionName = 'Your subscription name'
	$StorageAccountName = 'yourstorageaccount' 
	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 
	```

4. Aktivieren Sie die Speicherprotokollierung für den Blobdienst:
 
	```
	Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0 
	```
5. Aktivieren Sie die Speichermetriken für den Blobdienst, und stellen Sie dabei sicher, dass für **-MetricsType** `Minute` festgelegt ist:

	```
	Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0 
	```

### Konfigurieren der clientseitigen .NET-Protokollierung

Aktivieren Sie zum Konfigurieren der clientseitigen Protokollierung für eine .NET-Anwendung die .NET-Diagnose in der Konfigurationsdatei der Anwendung (web.config oder app.config). Weitere Informationen finden Sie unter [Clientseitige Protokollierung mit der .NET-Speicherclientbibliothek](http://msdn.microsoft.com/library/azure/dn782839.aspx) und [Clientseitige Protokollierung mit dem Microsoft Azure Storage SDK für Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) auf MSDN.

Das clientseitige Protokoll enthält detaillierte Informationen darüber, wie der Client die Anfrage vorbereitet und die Antwort empfängt und verarbeitet.

Die clientseitige Protokollfunktion wird in der Datei app.config oder web.config in der Anwendung konfiguriert. Weitere Informationen finden Sie unter [Clientseitige Protokollierung mit der .NET-Speicherclientbibliothek](http://msdn.microsoft.com/library/azure/dn782839.aspx) auf MSDN.

Die Speicherclientbibliothek speichert clientseitige Protokolldaten an dem in der Konfigurationsdatei der Anwendung (web.config oder app.config) festgelegten Speicherort.

### Erfassen einer Netzwerkablaufverfolgung

Sie können Message Analyzer verwenden, um eine HTTP-/HTTPS-Ablaufverfolgung zu erfassen, während Ihre Clientanwendung ausgeführt wird. Message Analyzer verwendet [Fiddler](http://www.telerik.com/fiddler) am Back-End. Bevor Sie die Netzwerkablaufverfolgung erfassen, empfehlen wir, Fiddler zum Aufzeichnen von unverschlüsseltem HTTPS-Datenverkehr zu konfigurieren:

1. Installieren Sie [Fiddler](http://www.telerik.com/download/fiddler).
2. Starten Sie Fiddler.
2. Wählen Sie **Tools | Fiddler Options**.
3. Stellen Sie sicher, dass im Dialogfeld "Options" die Optionen **Capture HTTPS CONNECTs** und **Decrypt HTTPS Traffic** wie unten dargestellt aktiviert sind.

![Konfigurieren von Fiddler-Optionen](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Erfassen und speichern Sie für das Lernprogramm zunächst eine Netzwerkablaufverfolgung in Message Analyzer, und erstellen Sie daraufhin eine Analysesitzung, um die Ablaufverfolgung und die Protokolle zu analysieren. So erfassen Sie eine Netzwerkablaufverfolgung in Message Analyzer:

1. Wählen Sie in Message Analyzer **File | Quick Trace | Unencrypted HTTPS** aus.
2. Die Ablaufverfolgung beginnt sofort. Wählen Sie **Stop** aus, um die Ablaufverfolgung anzuhalten, sodass sie für die ausschließliche Verfolgung des Speicherverkehrs konfiguriert werden kann.
3. Wählen Sie **Edit** aus, um die Ablaufverfolgungssitzung zu bearbeiten.
4. Wählen Sie **Configure** rechts neben dem ETW-Anbieter **Microsoft-Pef-WebProxy** aus.
5. Klicken Sie im Dialogfeld **Advanced Settings** auf die Registerkarte **Provider**.
6. Geben Sie im Feld **Hostname Filter** die Speicherendpunkte an (getrennt durch Leerzeichen). Sie können beispielsweise Ihre Endpunkte wie folgt angeben (ändern Sie hierbei `storagesample` in den Namen Ihres Speicherkontos):
	
	```	
	storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net 
	```

7. Schließen Sie das Dialogfeld, und klicken Sie auf **Restart**, um mit dem Erfassen der Ablaufverfolgung mit dem Hostnamensfilter zu beginnen, sodass nur der Azure-Speichernetzwerkverkehr in die Ablaufverfolgung einbezogen wird.

>[AZURE.NOTE]Nach dem Erfassen der Netzwerkablaufverfolgung wird dringend empfohlen, dass Sie die Einstellungen wiederherstellen, die Sie in Fiddler bezüglich der Entschlüsselung von HTTPS-Datenverkehr geändert haben. Deaktivieren Sie im Dialogfeld "Fiddler Options" die Kontrollkästchen **Capture HTTPS CONNECTs** und **Decrypt HTTPS Traffic**.

Weitere Informationen finden Sie unter [Using the Network Tracing Features](http://technet.microsoft.com/library/jj674819.aspx) (in englischer Sprache) auf Technet.

## Überprüfen der Metrikdaten im Portal

Sobald die Anwendung über einen bestimmten Zeitraum hinweg ausgeführt wurde, können Sie die Metrikdiagramme überprüfen, die im Portal zur Untersuchung der Dienstleistung angezeigt werden. Zunächst fügen wir die Metrik **Prozentsatz erfolgreich** auf der Seite "Überwachen" hinzu:

1. Navigieren Sie im Verwaltungsportal zum Dashboard für das Speicherkonto, und wählen Sie "Überwachen" aus, um die Überwachungsseite anzuzeigen.
2. Klicken Sie auf **Metriken hinzufügen** zum Anzeigen des Dialogfelds **Metriken auswählen**.
3. Scrollen Sie nach unten zur Gruppe **Prozentsatz erfolgreich**, erweitern Sie sie, und wählen Sie dann wie in der folgenden Abbildung dargestellt **Aggregate** aus. Diese Metrik führt Daten zum erfolgreichen Prozentsatz aus allen Blobvorgängen zusammen.

![Auswählen von Metriken](./media/storage-e2e-troubleshooting/choose-metrics-portal-1.png)

Im Portal wird nun **Prozentsatz erfolgreich** im Überwachungsdiagramm neben sämtlichen anderen Metriken angezeigt, die Sie möglicherweise hinzugefügt haben (bis zu sechs Metriken können gleichzeitig im Diagramm angezeigt werden). Die folgende Abbildung zeigt, dass die prozentuale Erfolgsrate unter 100 % liegt. Dies ist das Szenario, das wir im Folgenden durch die Analyse der Protokolle in Message Analyzer untersuchen:

![Metrikdiagramm im Verwaltungsportal](./media/storage-e2e-troubleshooting/portal-metrics-chart-1.png)

Weitere Informationen zum Hinzufügen von Metriken auf der Seite "Überwachen" finden Sie unter [Hinzufügen von Metriken zur Metrikentabelle](storage-monitor-storage-account.md#addmonitoringmetrics).

> [AZURE.NOTE]Es dauert einige Zeit, bis die Metrikdaten im Portal angezeigt werden, nachdem Sie die Speichermetriken aktiviert haben. Dies liegt daran, dass stündliche Metriken für die vergangene Stunde erst angezeigt werden, wenn die Stunde vergangen ist. Darüber hinaus werden Minutenmetriken derzeit nicht im Portal angezeigt. Je nach Zeitpunkt der Metrikaktivierung kann es also bis zu zwei Stunden dauern, bis die Metrikdaten angezeigt werden.

## Verwenden von AzCopy zum Kopieren von Serverprotokollen in ein lokales Verzeichnis

Azure Storage schreibt Serverprotokolldaten in Blobs, während Metriken in Tabellen geschrieben werden. Protokoll-Blobs finden Sie im wohlbekannten Container `$logs` für Ihr Speicherkonto. Protokoll-Blobs werden hierarchisch nach Jahr, Monat, Tag und Stunde benannt, damit Sie problemlos auf den Zeitbereich zugreifen können, den Sie untersuchen möchten. Der Container für die Protokoll-Blobs im Konto `storagesample` für den 02.01.2015 von 8:00 bis 9:00 Uhr lautet beispielsweise `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Die einzelnen Blobs in diesem Container werden sequenziell benannt, beginnend mit `000000.log`.

Sie können das AzCopy-Befehlszeilentool verwenden, um diese serverseitigen Protokolldateien an einen beliebigen Speicherort auf dem lokalen Computer herunterzuladen. Sie können z. B. den folgenden Befehl zum Herunterladen von Protokolldateien für Blobvorgänge verwenden, die am 2. Januar 2015 in den Ordner `C:\Temp\Logs\Server` stattgefunden haben. Ersetzen Sie hierbei `<storageaccountname>` durch den Namen Ihres Speicherkontos und `<storageaccountkey>` durch Ihren Zugriffsschlüssel:

	AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy steht zum Download auf der Seite [Azure-Downloads](http://azure.microsoft.com/downloads/) zur Verfügung. Ausführliche Informationen zum Verwenden von AzCopy finden Sie unter [Verwenden von AzCopy mit Microsoft Azure Storage](storage-use-azcopy.md).

Weitere Informationen zum Herunterladen serverseitiger Protokolle finden Sie unter [Herunterladen von Protokolldaten der Speicherprotokollierung](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## Analysieren von Protokolldaten mithilfe von Microsoft Message Analyzer

Microsoft Message Analyzer ist ein Tool zum Erfassen, Anzeigen und Analysieren von Messagingverkehr, -ereignissen und anderen System- oder Anwendungsnachrichten in Problembehandlungs- und Diagnoseszenarien. Message Analyzer ermöglicht außerdem das Laden, Zusammenfassen und Analysieren von Daten aus dem Protokoll und den gespeicherten Ablaufverfolgungsdateien. Weitere Informationen zu Message Analyzer finden Sie unter [Microsoft Message Analyzer Operating Guide](http://technet.microsoft.com/library/jj649776.aspx) (in englischer Sprache).

Message Analyzer enthält Ressourcen für Azure Storage, mit denen Sie Server-, Client- und Netzwerkprotokolle analysieren können. In diesem Abschnitt beschreiben wir die Verwendung dieser Tools zum Umgang mit Problemen niedriger prozentualer Erfolgsraten in den Speicherprotokollen.

### Herunterladen und Installieren von Message Analyzer und der Azure-Speicherressourcen

1. Laden Sie [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) aus dem Microsoft Download Center herunter, und führen Sie das Installationsprogramm aus.
2. Starten Sie Message Analyzer.
3. Navigieren Sie auf der Seite **Start** zu **Downloads**, und filtern Sie die Anzeige nach **Azure Storage**. Die Azure-Speicherressourcen werden angezeigt, wie in der folgenden Abbildung dargestellt.
4. Klicken Sie auf **Sync All Displayed Items**, um die Azure-Speicherressourcen zu installieren. Die verfügbaren Ressourcen umfassen: 
	- **Azure Storage Color Rules:** Die Azure-Speicherfarbregeln ermöglichen Ihnen die Angabe bestimmter Filter, die mithilfe von Farbe, Text und Schriftarten Nachrichten hervorheben, die bestimmte Informationen einer Nachverfolgung enthalten.
	- **Azure Storage Charts:** Azure-Speicherdiagramme sind vordefinierte Diagramme, die Server-Protokolldaten grafisch darstellen. Beachten Sie, dass für die Verwendung der Azure-Speicherdiagramme zum jetzigen Zeitpunkt das Serverprotokoll nur in das Analyseraster geladen werden kann.
	- **Azure Storage Parsers:** Die Azure-Speicherparser analysieren die Client-, Server- und HTTP-Protokolle des Azure-Speichers, um sie im Analyseraster anzuzeigen.
	- **Azure Storage Filters:** Azure-Speicherfilter sind vordefinierte Kriterien, die Sie für die Abfrage der Daten im Analyseraster verwenden können.
	- **Azure Storage View Layouts:** Azure-Speicheransichtslayouts sind vordefinierte Spaltenlayouts und -gruppierungen im Analyseraster.
4. Starten Sie Message Analyzer nach der Installation der Ressourcen neu.

![Message Analyzer-Startseite](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE]Installieren Sie für dieses Lernprogramm alle angezeigten Azure-Speicherressourcen.

### Importieren der Protokolldateien in Message Analyzer

Sie können alle Ihre gespeicherten Protokolldateien (serverseitig, clientseitig und Netzwerk) in einer einzigen Sitzung in Microsoft Message Analyzer für die Analyse importieren.

1. Klicken Sie im Menü **File** von Microsoft Message Analyzer auf **New Session**, und klicken Sie dann auf **Blank Session**. Geben Sie im Dialogfeld **New Session** einen Namen für Ihre Analysesitzung ein. Klicken Sie im Bereich **Session Details** auf die Schaltfläche **Files**. 
1. Um die von Message Analyzer generierten Netzwerkablaufverfolgungs-Daten zu laden, klicken Sie auf **Add Files**, navigieren Sie zum Speicherort, an dem Sie die MATP-Datei der Web-Protokollierungssitzung gespeichert haben, wählen Sie die MATP-Datei aus, und klicken Sie auf **Open**. 
1. Klicken Sie zum Laden der serverseitigen Protokolldaten auf **Add Files**, navigieren Sie zum Speicherort, an den Sie die serverseitigen Protokolle heruntergeladen haben, wählen Sie die Protokolldateien für den zu analysierenden Zeitraum aus, und klicken Sie auf **Open**. Legen Sie im Dropdownmenü **Text Log Configuration** jeder serverseitigen Protokolldatei im Bereich **Session Details** die Option **AzureStorageLog** fest, um sicherzustellen, dass Microsoft Message Analyzer die Protokolldatei ordnungsgemäß analysieren kann.
1. Klicken Sie zum Laden der clientseitigen Protokolldaten auf **Add Files**, navigieren Sie zum Speicherort, an dem Sie die clientseitigen Protokolle gespeichert haben, wählen Sie die zu analysierenden Protokolldateien aus, und klicken Sie auf **Open**. Legen Sie im Dropdownmenü **Text Log Configuration** jeder clientseitigen Protokolldatei im Bereich **Session Details** die Option **AzureStorageClientDotNetV4** fest, um sicherzustellen, dass Microsoft Message Analyzer die Protokolldatei ordnungsgemäß analysieren kann.
1. Klicken Sie im Dialogfeld **New Session** auf **Start**, um die Daten zu laden und zu analysieren. Die Protokolldaten werden im Analyseraster von Message Analyzer angezeigt.

Die folgende Abbildung zeigt eine Beispielsitzung, die mit Server-, Client- und Netzwerk-Ablaufverfolgungsprotokoll-Dateien konfiguriert ist.

![Konfigurieren der Message Analyzer-Sitzung](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Beachten Sie, dass Message Analyzer Protokolldateien in den Arbeitsspeicher lädt. Wenn Sie über eine große Menge von Protokolldaten verfügen, sollten Sie den Filter verwenden, um die Leistung von Message Analyzer zu optimieren.

Bestimmen Sie zunächst den Zeitraum, den Sie überprüfen möchten, und halten Sie diesen so kurz wie möglich. In vielen Fällen sollten Sie einen Zeitraum von Minuten oder höchstens Stunden überprüfen. Importieren Sie den kleinsten Satz an Protokollen, der Ihre Anforderungen erfüllt.

Wenn Sie dennoch über eine große Menge von Protokolldaten verfügen, empfiehlt es sich möglicherweise, einen Sitzungsfilter festzulegen, der die Protokolldaten vor dem Laden filtert. Klicken Sie im Feld **Session Filter** auf die Schaltfläche **Library**, um einen vordefinierten Filter auszuwählen. Wählen Sie beispielsweise **Global Time Filter I** in den Azure-Speicherfiltern aus, um nach einem Zeitraum zu filtern. Anschließend können Sie die Filterkriterien bearbeiten, um das Start- und Enddatum des gewünschten Zeitraums festzulegen. Sie können auch nach bestimmten Status filtern: Sie können z. B. auswählen, dass nur Protokolleinträge mit dem Statuscode 404 geladen werden.

Weitere Informationen zum Importieren von Daten in Microsoft Message Analyzer finden Sie unter [Retrieving Message Data](http://technet.microsoft.com/library/dn772437.aspx) (in englischer Sprache) auf TechNet.

### Verwenden der Client-ID zum Zuordnen von Protokolldaten

Die Azure-Speicherclientbibliothek erzeugt automatisch eine eindeutige Clientanfragen-ID für jede Anfrage. Dieser Wert wird in das Clientprotokoll, das Serverprotokoll und die Netzwerkablaufverfolgung geschrieben, damit Sie sie zum Zuordnen der Daten zu den drei Protokollen in Message Analyzer verwenden können. Weitere Informationen zur Clientanfragen-ID finden Sie unter [Clientanfrage-ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id).

In den folgenden Abschnitten wird die Verwendung vorkonfigurierter Layouts und benutzerdefinierter Ansichten zum Zuordnen und Gruppieren von Daten basierend auf der Clientanfragen-ID beschrieben.

### Auswählen eines Ansichtslayouts zur Anzeige im Analyseraster

Die Speicherressourcen für Message Analyzer umfassen Azure-Speicheransichtslayouts, bei denen es sich um vorkonfigurierte Ansichten handelt, die Sie zum Anzeigen Ihrer Daten in nützlichen Gruppen und Spalten für verschiedene Szenarien verwenden können. Sie können auch benutzerdefinierte Ansichtslayouts erstellen und zur Wiederverwendung speichern.

Die Abbildung unten zeigt das Menü **View Layout** das über die Schaltfläche **View Layout** auf der Symbolleiste verfügbar ist. Die Ansichtslayouts für Azure-Speicher werden in dem Menü unter dem Knoten **Azure Storage** zusammengefasst. Sie können im Suchfeld nach `Azure Storage` suchen, um nur Azure-Speicheransichtslayouts anzuzeigen. Sie können auch das Sternsymbol neben einem Ansichtslayout auswählen, um es als Favorit zu markieren und oben im Menü anzuzeigen.

![Menü "Layout anzeigen"](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Wählen Sie zunächst **Grouped by ClientRequestID and Module** aus. Dieses Ansichtslayout gruppiert Protokolldaten aus allen drei Protokollen nach Clientanfragen-ID und dann nach Quellprotokolldatei (oder **Module** in Message Analyzer). Mit dieser Ansicht können Sie eine bestimmte Clientanfragen-ID überprüfen und die Daten aller drei Protokolldateien für diese Clientanfragen-ID anzeigen.

Die Abbildung unten zeigt die Anwendung dieses Ansichtslayouts auf die Beispielprotokolldaten mit einem angezeigten Teilsatz der Spalten. Sie können erkennen, dass das Analyseraster für eine bestimmte Clientanfragen-ID Daten aus dem Clientprotokoll, dem Serverprotokoll und der Netzwerkablaufverfolgung anzeigt.

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE]Verschiedene Protokolldateien verfügen über unterschiedliche Spalten, sodass bei der Anzeige der Daten aus verschiedenen Protokolldateien im Analyseraster einige Spalten für bestimmte Zeilen möglicherweise keine Daten enthalten. die Clientprotokollzeilen keine Daten für die Spalten **Timestamp**, **TimeElapsed**, **Source** und **Destination**, da diese Spalten nicht im Clientprotokoll, jedoch in der Netzwerkablaufverfolgung vorhanden sind. Auf ähnliche Weise zeigt die Spalte **Timestamp** Zeitdaten aus dem Serverprotokoll an, jedoch werden keine Daten für die Spalten **TimeElapsed**, **Source** und **Destination** angezeigt, da diese nicht Teil des Serverprotokolls sind.

Zusätzlich zur Verwendung der Azure-Speicheransichtslayouts können Sie auch eigene Ansichtslayouts speichern. Sie können andere gewünschte Felder zum Gruppieren von Daten auswählen und die Gruppierung als Teil des benutzerdefinierten Layouts speichern.

### Anwenden von Farbregeln auf das Analyseraster

Die Speicherressourcen umfassen auch Farbregeln, die ein visuelles Mittel zum Identifizieren der verschiedenen Arten von Fehlern im Analyseraster bieten. Die vordefinierten Farbregeln gelten für HTTP-Fehler, sodass sie nur für das Serverprotokoll und die Netzwerkablaufverfolgung angezeigt werden.

Um die Farbregeln anzuwenden, wählen Sie auf der Symbolleiste die Option **Color Rules** aus. Daraufhin werden die Azure-Speicherfarbregeln im Menü angezeigt. Wählen Sie für das Lernprogramm **Client Errors (StatusCode between 400 and 499)** aus, wie in der folgenden Abbildung dargestellt.

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Zusätzlich zur Verwendung der Azure-Speicherfarbregeln können Sie auch eigene Farbregeln speichern.

### Gruppieren und Filtern von Protokolldaten zum Suchen von Fehlern im Bereich von 400

Als Nächstes gruppieren und filtern wir die Protokolldaten, um alle Fehler im Bereich von 400 zu finden.

1. Suchen Sie die Spalte **StatusCode** im Analyseraster, klicken Sie mit der rechten Maustaste auf die Spaltenüberschrift, und wählen Sie **Group** aus.
2. Gruppieren Sie daraufhin die Spalte **ClientRequestId**. Die Daten im Analyseraster werden daraufhin nach Statuscode und Clientanfragen-ID sortiert.
1. Öffnen Sie das Toolfenster "View Window", wenn es nicht bereits angezeigt wird. Wählen Sie auf der Symbolleiste **Tool Windows** und dann **View Filter** aus.
2. Um die Protokolldaten zur Anzeige der Fehler im Bereich 400 zu filtern, fügen Sie die folgenden Filterkriterien im Fenster **View Filter** ein, und klicken Sie auf **Apply**:

		(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

Die folgende Abbildung zeigt die Ergebnisse dieser Gruppierung und Filterung. Durch Erweitern des Felds **ClientRequestID** unter der Gruppierung für den Statuscode 409 wird beispielsweise der Vorgang angezeigt, der diesen Statuscode verursacht hat.

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Nach dem Anwenden dieses Filters werden die Zeilen aus dem Clientprotokoll ausgeschlossen, da das Clientprotokoll keine **StatusCode**-Spalte enthält. Zunächst überprüfen wir die Server- und Netzwerkablaufverfolgungs-Protokolle, um die 404-Fehler zu finden, und kehren dann zum Clientprotokoll zurück, um die Clientvorgänge zu untersuchen, die die Fehler verursacht haben.

>[AZURE.NOTE]Sie können die Spalte **StatusCode** filtern und dennoch die Daten aller drei Protokolle anzeigen, einschließlich des Clientprotokolls, indem Sie dem Filter einen Ausdruck hinzufügen, der Protokolleinträge mit dem Statuscode null einschließt. Verwenden Sie für das Erstellen dieses Filters folgende Elemente:
>
> <code>&#42;StatusCode >= 400 oder !&#42;StatusCode</code>
>
> Dieser Filter gibt alle Zeilen aus dem Clientprotokoll sowie die Zeilen des Serverprotokolls und des HTTP-Protokolls mit einem Statuscode über 400 zurück. Wenn Sie den Filter auf das Ansichtslayout anwenden, das nach Clientanfragen-ID und -modul gruppiert ist, können Sie die Protokolleinträge durchsuchen, um diejenigen zu finden, in denen alle drei Protokolle dargestellt werden.

### Filtern von Protokolldaten, um 404-Fehler zu finden

Die Speicherressourcen enthalten vordefinierte Filter, die Sie verwenden können, um Protokolldaten zu filtern und so die gesuchten Fehler oder Trends zu finden. Als Nächstes wenden wir zwei vordefinierte Filter an: einer, der die Server- und Netzwerkablaufverfolgungs-Protokolle nach 404-Fehlern filtert, und einen anderen, der die Daten nach einem angegebenen Zeitraum filtert.

1. Öffnen Sie das Toolfenster "View Window", wenn es nicht bereits angezeigt wird. Wählen Sie auf der Symbolleiste die Option **Tool Windows** und dann **View Filter** aus.
2. Wählen Sie im Fenster "View Filter" die Option **Library** aus, und suchen Sie in `Azure Storage`, um die Azure-Speicherfilter zu finden. Wählen Sie den Filter **404 (Not Found) messages in all logs** aus.
3. Öffnen Sie das Menü **Library** erneut, und wählen Sie den **Global Time Filter** aus.
4. Ändern Sie die im Filter angezeigten Zeitstempel zu dem gewünschten Zeitraum. So können Sie den Zeitraum der zu analysierenden Daten eingrenzen.
5. Der Filter wird ähnlich wie im folgenden Beispiel angezeigt. Klicken Sie auf **Apply**, um den Filter auf das Analyseraster anzuwenden.

		((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And 
		(#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Layout: Azure-Speicheransicht](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### Analysieren der Protokolldaten

Nach der Gruppierung und Filterung der Daten können Sie die Detailinformationen der einzelnen Anfragen untersuchen, die 404-Fehler verursacht haben. In dem aktuellen Ansichtslayout werden die Daten nach Kundenanfragen-ID und dann nach Protokollquelle gruppiert. Da wir die Anfragen danach filtern, ob das Feld "StatusCode" den Wert "404" enthält, werden nur die Server- und Netzwerkablaufverfolgungs-Daten, aber nicht die Clientprotokolldaten angezeigt.

Die folgende Abbildung zeigt eine bestimmte Anfrage, bei der der Vorgang "Get Blob" einen 404-Fehler zurückgegeben hat, da der Blob nicht vorhanden war. Beachten Sie, dass einige Spalten in der Standardansicht entfernt wurden, um die relevanten Daten anzuzeigen.

![Gefilterte Server- und Netzwerk-Ablaufverfolgungsprotokolle](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Als Nächstes müssen wir diese Clientanfragen-ID den Clientprotokolldaten zuordnen, um anzuzeigen, welche Aktionen der Client ausgeführt hat, als der Fehler aufgetreten ist. Sie können eine neue Analyserasteransicht für diese Sitzung öffnen, um die Clientprotokolldaten in einer zweiten Registerkarte anzuzeigen:

1. Kopieren Sie zunächst den Wert des Felds **ClientRequestId** in die Zwischenablage. Sie können dazu eine beliebige Zeile auswählen, das Feld **ClientRequestId** suchen, mit der rechten Maustaste auf den Datenwert klicken und **Copy 'ClientRequestId'** auswählen. 
1. Wählen Sie in der Symbolleiste **New Viewer** und dann **Analysis Grid** aus, um eine neue Registerkarte zu öffnen. Die neue Registerkarte zeigt alle Daten in den Protokolldateien ohne Gruppierung, Filterung oder Farbregeln an. 
2. Wählen Sie auf der Symbolleiste die Option **View Layout** und dann **All .NET Client Columns** im Bereich **Azure Storage** aus. Dieses Ansichtslayout zeigt Daten aus dem Clientprotokoll sowie den Server- und Netzwerkablaufverfolgungs-Protokollen an. Standardmäßig wird die Anzeige nach der Spalte **MessageNumber** sortiert.
3. Als Nächstes durchsuchen wir das Clientprotokoll nach der Clientanfragen-ID. Wählen Sie auf der Symbolleiste die Option **Find Messages** aus, und legen Sie dann einen benutzerdefinierten Filter für die Clientanfragen-ID im Feld **Find** fest. Verwenden Sie folgende Syntax für den Filter unter Angabe Ihrer eigenen Clientanfragen-ID:

		*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Message Analyzer sucht den ersten Protokolleintrag, bei dem die Clientanfragen-ID den Suchkriterien entspricht, und wählt ihn aus. Im Clientprotokoll sind mehrere Einträge für jede Clientanfragen-ID vorhanden, Sie sollten sie also nach dem Feld **ClientRequestId** gruppieren, um die Anzeige aller entsprechenden Elemente zu erleichtern. Die folgende Abbildung zeigt alle Nachrichten im Clientprotokoll für die angegebene Clientanfragen-ID.

![Clientprotokoll mit 404-Fehler](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Durch Verwenden der in den Ansichtslayouts auf diesen beiden Registerkarten angezeigten Daten können Sie die Anfragedaten analysieren, um zu bestimmen, was den Fehler verursacht hat. Sie können auch Anfragen anzeigen, die der aktuellen Anfrage vorangegangen sind, um zu ermitteln, ob ein vorheriges Ereignis den 404-Fehler verursacht hat. Sie können beispielsweise die Clientprotokolleinträge vor dieser Clientanfragen-ID überprüfen, um zu bestimmen, ob der Blob möglicherweise gelöscht wurde oder ob der Fehler verursacht wurde, weil die Clientanwendung eine CreateIfNotExists-API für einen Container oder Blob aufgerufen hat. Im Clientprotokoll finden Sie die Blobadresse im Feld **Description**. In den Server- und Netzwerkablaufverfolgungs-Protokollen wird diese Information im Feld **Summary** angezeigt.

Wenn Sie die Adresse des Blobs kennen, der den 404-Fehler verursacht hat, können Sie weitere Untersuchungen anstellen. Wenn Sie die Protokolleinträge für andere Nachrichten durchsuchen möchten, die mit den Vorgängen desselben Blobs zusammenhängen, können Sie überprüfen, ob der Client die Entität zuvor gelöscht hat.

## Analysieren anderer Arten von Speicherfehlern

Nachdem Sie nun mit der Verwendung von Message Analyzer zum Analysieren Ihrer Daten vertraut sind, können Sie andere Arten von Fehlern mithilfe von Ansichtslayouts, Farbregeln und Such-/Filterfunktionen analysieren. Einige mögliche Probleme und die erforderlichen Filterkriterien zum Finden dieser Probleme sind in den folgenden Tabellen aufgeführt. Weitere Informationen zum Erstellen von Filtern und zur Filtersyntax von Message Analyzer finden Sie unter [Filtering Message Data](http://technet.microsoft.com/library/jj819365.aspx) (in englischer Sprache).

| Zum Untersuchen von... | Verwenden Sie folgenden Filterausdruck... | Ausdruck gilt für Protokoll (Client, Server, Netzwerk, Alle) |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| Unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange | AzureStorageClientDotNetV4.Description contains "Retrying failed operation." | Client |
| HTTP-Zunahme von PercentThrottlingError | HTTP.Response.StatusCode == 500 HTTP.Response.StatusCode == 503 | Netzwerk |
| Zunahme von PercentTimeoutError | HTTP.Response.StatusCode == 500 | Netzwerk |
| Zunahme von PercentTimeoutError (alle) | *StatusCode == 500 | All | 
| Zunahme von PercentNetworkError | AzureStorageClientDotNetV4.EventLogEntry.Level < 2 | Client | 
| HTTP 403 (Forbidden)-Meldungen | HTTP.Response.StatusCode == 403 | Network | 
| HTTP 404 (Not found)-Meldungen | HTTP.Response.StatusCode == 404 | Network | 
| 404 (Alle) | *StatusCode == 404 | All | 
| Autorisierungsproblem für Shared Access Signature (SAS) | AzureStorageLog.RequestStatus == "SASAuthorizationError" | Network | 
| HTTP 409 (Conflict)-Meldungen | HTTP.Response.StatusCode == 409 | Network | 
| 409 (Alle) | *StatusCode == 409 | All | 
| "Low PercentSuccess" oder Analyseprotokolleinträge verfügen über Vorgänge mit Transaktionsstatus "ClientOtherErrors" | AzureStorageLog.RequestStatus == "ClientOtherError" | Server | 
| Nagle-Warnung| ((AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) and (AzureStorageLog.RequestPacketSize <1460) and (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS >= 200) | Server | 
| Zeitraum in Server- und Netzwerkprotokollen| #Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39 | Server, Network | 
| Zeitraum in Serverprotokollen| AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 and AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 | Server |


## Nächste Schritte

Weitere Informationen zur Problembehandlung in End-to-End-Szenarien im Azure-Speicher finden Sie hier:

- [Monitor, diagnose, and troubleshoot Storage](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)(in englischer Sprache)
- [Speicheranalyse](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Überwachen eines Speicherkontos](storage-monitor-storage-account.md)
- [Verwenden von AzCopy mit Microsoft Azure Storage](storage-use-azcopy.md)
- [Microsoft Message Analyzer Operating Guide](http://technet.microsoft.com/library/jj649776.aspx)(in englischer Sprache)
 

<!--HONumber=52-->
