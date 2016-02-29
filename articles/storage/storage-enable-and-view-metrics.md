<properties 
	pageTitle="Aktivieren von Speichermetriken im Azure-Portal | Microsoft Azure" 
	description="Aktivieren von Speichermetriken für die Blob-, Warteschlangen-, Tabellen- und Dateidienste" 
	services="storage" 
	documentationCenter="" 
	authors="robinsh" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/14/2016" 
	ms.author="robinsh"/>

# Aktivieren der Azure-Speichermetriken und Anzeigen von Metrikdaten

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## Übersicht

Standardmäßig sind für Ihre Speicherdienste keine Speichermetriken aktiviert. Sie können die Überwachung über das [Azure-Portal](https://portal.azure.com), über Windows PowerShell oder programmgesteuert über die Speicherclientbibliothek aktivieren.

Wenn Sie Speichermetriken aktivieren, müssen Sie einen Aufbewahrungszeitraum für die Daten auswählen: Dieser Zeitraum bestimmt, wie lange der Speicherdienst die Metriken beibehält und Speicherplatz abgerechnet wird, der für ihre Speicherung erforderlich ist. Normalerweise sollten Sie einen kürzeren Aufbewahrungszeitraum für minütliche Metriken als für stündliche Metriken auswählen, weil für minütliche Metriken eine erhebliche Menge an zusätzlichem Speicherplatz erforderlich ist. Sie sollten den Aufbewahrungszeitraum so auswählen, dass ausreichend Zeit zum Analysieren der Daten und zum Herunterladen von Metriken verfügbar ist, die Sie für die Offlineanalyse oder zur Berichterstellung verwenden möchten. Denken Sie daran, dass auch für das Herunterladen von Metrikdaten aus Ihrem Speicherkonto Kosten anfallen.

## Aktivieren von Metriken über das Azure-Portal

Gehen Sie wie folgt vor, um Metriken im [Azure-Portal](https://portal.azure.com) zu aktivieren:

1. Navigieren Sie zum Speicherkonto. 
1. Öffnen Sie das Blatt **Einstellungen** und wählen **Diagnose**.
1. Prüfen Sie, ob der **Status** auf **Ein** festgelegt ist.
1. Wählen Sie die Metriken für die Dienste, die Sie überwachen möchten.
2. Geben Sie eine Aufbewahrungsrichtlinie an, um festzulegen, wie lange Metriken und Protokolldaten beibehalten werden sollen.

Beachten Sie, dass das [Azure-Portal](https://portal.azure.com) zurzeit die Konfiguration von minütlichen Metriken in Ihrem Speicherkonto nicht unterstützt. Sie müssen minütliche Metriken mithilfe von PowerShell oder programmgesteuert aktivieren.

## Aktivieren von Metriken mithilfe von PowerShell

Sie können PowerShell auf Ihrem lokalen Computer zum Konfigurieren der Speichermetriken in Ihrem Speicherkonto verwenden, indem Sie das Azure PowerShell-Cmdlet "Get-AzureStorageServiceMetricsProperty" ausführen, um die aktuellen Einstellungen abzurufen. Mithilfe des Cmdlets "Set-AzureStorageServiceMetricsProperty" können Sie die aktuellen Einstellungen ändern.

Die Cmdlets zur Steuerung der Speichermetriken verwenden die folgenden Parameter:

- MetricsType – Mögliche Werte sind Hour und Minute.

- ServiceType – Mögliche Werte sind Blob, Queue und Table.

- MetricsLevel – Mögliche Werte sind None, Service und ServiceAndApi.

Der folgende Befehl aktiviert z. B. minütliche Metriken für den Blob-Dienst in Ihrem Standardspeicherkonto mit einem Aufbewahrungszeitraum, der auf fünf Tage festgelegt ist:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

Der folgende Befehl ruft die aktuelle stündliche Metrikstufe und die Aufbewahrungstage für den Blob-Dienst in Ihrem Standardspeicherkonto ab:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Informationen zum Konfigurieren der Azure PowerShell-Cmdlets für Ihr Azure-Abonnement sowie zum Auswählen des zu verwendenden Standardspeicherkontos finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

## Programmgesteuertes Aktivieren von Speichermetriken

Der folgende C#-Codeausschnitt zeigt, wie Metriken und Protokollierung für den Blob-Dienst mithilfe der Speicherclientbibliothek für .NET aktiviert werden:

	// Parse connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);

    
## Anzeigen von Speichermetriken

Nachdem Sie die Metriken der Speicheranalyse zum Überwachen Ihres Speicherkontos konfiguriert haben, erfasst die Speicheranalyse die Metriken in bekannten Tabellen in Ihrem Speicherkonto. Sie können Diagramme zum Anzeigen stündlicher Metriken im [Azure-Portal](https://portal.azure.com) konfigurieren:

1. Navigieren Sie zum Speicherkonto im [Azure-Portal](https://portal.azure.com).
2. Klicken Sie im Abschnitt **Überwachung** auf **Kacheln hinzufügen**, um ein neues Diagramm hinzuzufügen. Wählen Sie im **Kachelkatalog** die Metrik aus, die Sie anzeigen möchten, und ziehen Sie sie in den Abschnitt **Überwachung**.
3. Um einzustellen, welche Metriken in einem Diagramm angezeigt werden, klicken Sie auf den Link **Bearbeiten**. Sie können einzelne Metriken durch Aktivieren oder Deaktivieren hinzufügen oder entfernen.
4. Klicken Sie auf **Speichern**, wenn Sie mit dem Bearbeiten der Metriken fertig sind.

Wenn Sie die Metriken zur langfristigen Speicherung oder für eine lokale Analyse herunterladen möchten, müssen Sie ein Tool verwenden oder Code zum Lesen der Tabellen schreiben. Sie müssen die minütlichen Metriken für die Analyse herunterladen. Die Tabellen werden nicht angezeigt, wenn Sie alle Tabellen in Ihrem Speicherkonto auflisten. Sie können jedoch direkt anhand des Namens darauf zugreifen. Zahlreiche Drittanbietertools zum Durchsuchen des Speichers erkennen diese Tabellen und ermöglichen die direkte Anzeige (im Blogbeitrag [Microsoft Azure Storage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) finden Sie eine Liste der verfügbaren Tools).

### Stundenmetriken
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### Minutenmetriken
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### Kapazität
- $MetricsCapacityBlob

Die vollständigen Details der Schemas für diese Tabellen finden Sie unter [Schema der Tabellen für Speicheranalysemetriken](https://msdn.microsoft.com/library/azure/hh343264.aspx). Die Beispielzeilen unten zeigen nur eine Teilmenge der verfügbaren Spalten. Sie zeigen jedoch einige wichtige Funktionen, wie die Speichermetriken diese Metriken speichern:

| Partitionsschlüssel | Zeilenschlüssel | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Availability | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 | user;All | 2014-05-22T11:01:16.7650250Z | 7 | 7 | 4003 | 46801 | 100 | 104,4286 | 6,857143 | 100 |
| 20140522T1100 | user;QueryEntities | 2014-05-22T11:01:16.7640250Z | 5 | 5 | 2694 | 45951 | 100 | 143,8 | 7,8 | 100 |
| 20140522T1100 | user;QueryEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 538 | 633 | 100 | 3 | 3 | 100 |
| 20140522T1100 | user;UpdateEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 771 | 217 | 100 | 9 | 6 | 100 |

In diesen minütlichen Metrikbeispieldaten verwendet der Partitionsschlüssel die Auflösung "Uhrzeit zur Minute". Der Zeilenschlüssel identifiziert den Informationstyp, der in der Zeile gespeichert ist. Dieser besteht aus zwei Informationseinheiten: dem Zugriffstyp und dem Anforderungstyp:

- Der Zugriffstyp ist "user" oder "system". Dabei bezieht sich "user" auf alle Benutzeranforderungen des Speicherdiensts und "system" auf Anforderungen, die von der Speicheranalyse vorgenommen werden.

- Der Anforderungstyp ist entweder "all", in diesem Fall handelt es sich um eine Zusammenfassungszeile, oder er identifiziert die jeweilige API, z. B. "QueryEntity" oder "UpdateEntity".


Die Beispieldaten oben zeigen alle Datensätze für eine einzelne Minute (Beginn um 11:00 Uhr). Die Anzahl der QueryEntities-Anforderungen zuzüglich der Anzahl der QueryEntity-Anforderungen zuzüglich der Anzahl der UpdateEntity-Anforderungen ergibt daher den Wert 7. Dies ist die Gesamtsumme, die in der Zeile "user:All" angezeigt wird. Analog können Sie die durchschnittliche End-to-End-Latenz 104,4286 für die Zeile "user:All" ableiten, indem Sie die Berechnung ((143,8 * 5) + 3 + 9)/7 ausführen.

Sie sollten in Betracht ziehen, im [Azure-Portal](https://portal.azure.com) auf der Seite "Überwachen" Benachrichtigungen einzurichten, damit die Speichermetriken Sie automatisch bei wichtigen Änderungen im Verhalten Ihrer Speicherdienste benachrichtigen können. Wenn Sie ein Speicher-Explorer-Tool zum Herunterladen dieser Metrikdaten in einem Format mit Trennzeichen verwenden, kann Microsoft Excel zum Analysieren der Daten verwendet werden. Im Blogbeitrag [Microsoft Azure Storage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) finden Sie eine Liste der verfügbaren Speicher-Explorer-Tools.



## Programmgesteuertes Zugreifen auf Metrikdaten

Das folgende Listing zeigt C#-Beispielcode, der auf die minütlichen Metriken für einen bestimmten Zeitraum von Minuten zugreift und die Ergebnisse in einem Konsolenfenster anzeigt. Er verwendet die Azure Storage-Bibliothek, Version 4, die die Klasse "CloudAnalyticsClient" enthält, durch die der Zugriff auf Metriktabellen im Speicher vereinfacht wird.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## Welche Kosten fallen an, wenn Speichermetriken aktiviert werden?

Für Schreibanforderungen zum Erstellen von Tabellenentitäten für Metriken fallen die Standardraten an, die für alle Azure Storage-Vorgänge gelten.

Für Lese- und Löschanforderungen durch einen Client für Metrikdaten gelten ebenfalls Standardraten. Wenn Sie eine Datenbeibehaltungsrichtlinie konfiguriert haben, fallen keine Kosten an, wenn Azure Storage alte Metrikdaten löscht. Wenn Sie jedoch Analysedaten löschen, wird Ihr Konto für die Löschvorgänge belastet.

Die von den Metriktabellen verwendete Kapazität ist ebenfalls kostenpflichtig: Sie können die folgenden Angaben verwenden, um die erforderliche Kapazität zum Speichern von Metrikdaten einzuschätzen:

- Wenn ein Dienst jede Stunde alle APIs in jedem Dienst nutzt, werden ungefähr 148 KB Daten pro Stunde in den Metriktransaktionstabellen gespeichert, wenn Sie die Zusammenfassung auf Dienst- und API-Ebene aktiviert haben.

- Wenn ein Dienst jede Stunde alle APIs in jedem Dienst nutzt, werden ungefähr 12 KB Daten pro Stunde in den Metriktransaktionstabellen gespeichert, wenn Sie die Zusammenfassung nur auf Dienstebene aktiviert haben.

- In der Kapazitätstabelle für Blobs werden pro Tag zwei Zeilen hinzugefügt (wenn der Benutzer Protokolle abonniert hat): Dies bedeutet, dass die Größe dieser Tabelle pro Tag um ungefähr 300 Bytes zunimmt.

## Nächste Schritte:
[Aktivieren der Speicherprotokollierung und Zugreifen auf Protokolldaten](https://msdn.microsoft.com/library/dn782840.aspx)
 

<!---HONumber=AcomDC_0218_2016-->