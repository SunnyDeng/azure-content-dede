<properties 
	pageTitle="„Datenverkehrsanalyse durchsuchen“ für Azure Search | Microsoft Azure" 
	description="Aktivieren Sie „Datenverkehrsanalyse durchsuchen“ für Azure Search, einen cloudgehosteten Suchdienst für Microsoft Azure, um Erkenntnisse über Ihre Benutzer und Daten zu gewinnen." 
	services="search" 
	documentationCenter="" 
	authors="bernitorres" 
	manager="pablocas" 
	editor=""
/>

<tags 
	ms.service="search" 
	ms.devlang="multiple" 
	ms.workload="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="12/11/2015" 
	ms.author="betorres"
/>


# Aktivieren und Verwenden von „Datenverkehrsanalyse durchsuchen“ #

„Datenverkehrsanalyse durchsuchen“ ist ein Azure Search-Feature, mit dem Sie Einblick in Ihren Suchdienst und Erkenntnisse über Ihre Benutzer und deren Verhalten erhalten. Wenn Sie dieses Feature aktivieren, werden Ihre Suchdienstdaten auf ein Speicherkonto Ihrer Wahl kopiert. Diese Daten umfassen die Suchdienstprotokolle und die aggregierten operativen Metriken. Dort können Sie die Nutzungsdaten in beliebiger Weise bearbeiten.


## Aktivieren von „Datenverkehrsanalyse durchsuchen“ ##

### 1\. Verwenden des Portals ###
Öffnen Sie Ihren Azure Search-Dienst im [Azure-Portal](http://portal.azure.com). Unter „Einstellungen“ finden Sie die Option „Datenverkehrsanalyse durchsuchen“.

![][1]

Wählen Sie diese Option aus, und ein neues Blatt wird geöffnet. Ändern Sie den Status in **Ein**, wählen Sie das Azure Storage-Konto, in das die Daten kopiert werden, und wählen Sie die Daten aus, die Sie kopieren möchten: Protokolle, Metriken oder beides. Sie sollten Protokolle und Metriken kopieren.

![][2]


> [AZURE.IMPORTANT]Das Speicherkonto muss sich in der gleichen Region und dem gleichen Abonnement wie Ihr Suchdienst befinden.
> 
> Für dieses Speicherkonto fallen Standardgebühren an.

### 2\. Mithilfe von PowerShell ###

Sie können dieses Feature auch aktivieren, indem Sie die folgenden PowerShell-Cmdlets ausführen.

```PowerShell
Login-AzureRmAccount
Set-AzureRmDiagnosticSetting -ResourceId <SearchService ResourceId> StorageAccountId <StorageAccount ResourceId> -Enabled $true
```

-   **SearchService ResourceId**: ```
/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Search/searchServices/<searchServiceName>
```

 
-  **StorageAccount ResourceId**: Sie finden es im Portal in „Einstellungen -> Eigenschaften -> ResourceId“. ```
New: /subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>
OR
Classic: /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ClassicStorage/storageAccounts/<storageAccountName>
```

----------

Nach der Aktivierung beginnt innerhalb von 5 bis 10 Minuten der Datenfluss auf Ihr Speicherkonto. Zwei neue Container finden Sie in Ihrem Blobspeicher:

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


## Grundlegendes zu den Daten ##

Die Daten werden in JSON-formatierten Azure Storage-Blobs gespeichert.

Es gibt einen Blob pro Stunde pro Container.
  
Beispiel-Pfad: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### Logs ###

Die Protokollblobs enthalten die Datenverkehrprotokolle des Suchdiensts.

Jeder Blob hat ein Stammobjekt namens **records**, das ein Array von Protokollobjekten enthält.

####Protokollschema####

Name |Typ |Beispiel |Hinweise 
------|-----|----|-----
in |datetime |„2015-12-07T00:00:43.6872559Z“ |Zeitstempel des Vorgangs
Ressourcen-ID |Zeichenfolge |„/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE“ |Ihre Ressourcen-ID
operationName |Zeichenfolge |„Query.Search“ |Der Name des Vorgangs
operationVersion |Zeichenfolge |„2015-02-28“|Die verwendete API-Version
category |Zeichenfolge |„OperationLogs“ |Konstante 
resultType |Zeichenfolge |„Success“ |Mögliche Werte: „Success“ oder „Failure“ 
resultSignature |int |200 |HTTP-Ergebniscode 
durationMS |int |50 |Dauer des Vorgangs in Millisekunden 
Eigenschaften |Objekt |siehe unten |Objekt, das vorgangsspezifische Daten enthält

####Eigenschaftsschema####

|Name |Typ |Beispiel |Hinweise|
|------|-----|----|-----|
|Beschreibung|Zeichenfolge |„GET-/indexes('content')/docs“ |Endpunkt des Vorgangs |
|Abfragen |Zeichenfolge |„?search=AzureSearch&$count=true&api-version=2015-02-28“ |Die Abfrageparameter |
|Dokumente |int |42 |Anzahl von verarbeiteten Dokumenten|
|IndexName |Zeichenfolge |„testindex“|Name des Indexes, der dem Vorgang zugeordnet ist |

### Metriken ###

Die Metrikblobs enthalten aggregierte Werte für Ihren Suchdienst. Jede Datei hat ein Stammobjekt namens **records**, das ein Array von Metrikobjekten enthält.

Verfügbare Metriken:

- Latenz

####Metrikenschema####

|Name |Typ |Beispiel |Hinweise|
|------|-----|----|-----|
|Ressourcen-ID |Zeichenfolge |„/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE“ |Ihre Ressourcen-ID |
|metricName |Zeichenfolge |„Latency“ |Der Name der Metrik |
|in|datetime |„2015-12-07T00:00:43.6872559Z“ |Der Zeitstempel des Vorgangs |
|average |int |64|Der Durchschnittswert der unformatierten Beispiele im Metrikzeitintervall |
|minimum |int |37 |Der Mindestwert der unformatierten Beispiele im Metrikzeitintervall |
|maximum |int |78 |Der Höchstwert der unformatierten Beispiele im Metrikzeitintervall |
|total |int |258 |Der Gesamtwert der unformatierten Beispiele im Metrikzeitintervall |
|count |int |4 |Die Anzahl der unformatierten Beispiele, die zum Generieren der Metrik verwendet werden |
|timegrain |Zeichenfolge |„PT1M“ |Das Aggregationsintervall der Metrik in ISO 8601|

## Analysieren Ihrer Daten ##

Die Daten befinden sich in Ihrem eigenen Speicherkonto, und Sie sollten diese Daten in der für Sie idealen Art und Weise untersuchen.

Zunächst sollten Sie Ihre Daten mit [Power BI Desktop](https://powerbi.microsoft.com/de-DE/desktop) untersuchen und visualisieren. Sie können sich ganz einfach mit Ihrem Azure Storage-Konto verbinden und schnell mit der Analyse Ihrer Daten beginnen.

Sehen Sie sich die folgende Beispielabfrage an, mit der Sie Ihre eigenen Berichte in Power BI Desktop erstellen.

### Anleitung ###

1. Öffnen eines neuen Power BI Desktop-Berichts
2. Wählen Sie „Daten abrufen -> Mehr...“

	![][3]

3. Wählen Sie „Microsoft Azure-Blobspeicher“ und „Verbinden“.

	![][4]

4. Geben Sie Namen und Kontoschlüssel des Speicherkontos ein.
5. Klicken Sie mit der rechten Maustaste auf „insight-logs-operationlogs“, und wählen Sie „Laden“.
6. Der Abfrage-Editor wird geöffnet. Öffnen Sie jetzt den erweiterten Editor durch Auswahl von „Ansicht -> Erweiterter Editor“.

	![][5]

7. Behalten Sie die ersten beiden Zeilen bei, und ersetzen Sie den Rest durch die folgende Abfrage:

	>     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",288),
	>     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
	>     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
	>     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
	>     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
	>     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
	>     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
	>     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
	>     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
	>     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
	>     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
	>     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
	>     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
	>     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
	>     in
	>     #"Changed Type2"

8. Klicken Sie auf „Fertig“, und wählen Sie dann auf der Registerkarte „Startseite“ „Schließen und Anwenden“.

9. Ihre Daten können jetzt genutzt werden. Fahren Sie fort, und erstellen Sie einige [Visualisierungen](https://powerbi.microsoft.com/de-DE/documentation/powerbi-desktop-report-view/).

## Nächste Schritte ##

Erhalten Sie weitere Informationen zu Syntax und Abfrageparametern. Details finden Sie unter [Suchen von Dokumenten (REST-API für den Azure Search-Dienst)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

Erfahren Sie hier mehr über das Erstellen erstaunlicher Berichte. Weitere Informationen finden Sie unter [Erste Schritte mit Power BI Desktop](https://powerbi.microsoft.com/de-DE/documentation/powerbi-desktop-getting-started/).

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/GetData.png
[4]: ./media/search-traffic-analytics/BlobStorage.png
[5]: ./media/search-traffic-analytics/QueryEditor.png

<!---HONumber=AcomDC_1217_2015-->