<properties 
	pageTitle="Data Factory – .NET API-Änderungsprotokoll | Microsoft Azure" 
	description="Beschreibt Änderungen, hinzugefügte Features und Fehlerbehebungen usw. in einer bestimmten Version der .NET-API für Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/13/2015" 
	ms.author="spelluru"/>

# Azure Data Factory – Änderungsprotokoll für das .NET SDK 
Dieser Artikel enthält Informationen zu Änderungen am Azure Data Factory SDK einer bestimmten Version. [Hier](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) finden Sie das neueste NuGet-Paket für Azure Data Factory.

## Version 4.0.1
Veröffentlichungsdatum: 13.10.2015

### Wichtige Änderungen
Die folgenden Klassen wurden umbenannt. Die neuen Namen waren die ursprünglichen Namen von Klassen vor Version 4.0.0.
 
Name in 4.0.0 | Name in 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Version 4.0.0
Veröffentlichungsdatum: 02.10.2015

### Wichtige Änderungen



- Die folgenden Klassen/Schnittstellen wurden umbenannt.

| Alter Name | Neuer Name |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabelle | [Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- Die **API-Version** für diese Version ist: **2015-10-01**.

- Die **List-**-Methoden geben jetzt ausgelagerte Ergebnisse zurück. Wenn die Antwort eine nicht leere **NextLink**-Eigenschaft enthält, muss die Clientanwendung das Abrufen der nächsten Seite fortsetzen, bis alle Seiten zurückgegeben wurden. Beispiel:

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- Die **List**-Pipeline-API gibt nur die Zusammenfassung einer Pipeline anstatt alle Details zurück. Beispielsweise enthalten Aktivitäten in einer Pipelinezusammenfassung nur Name und Typ.

### Hinzugefügte Features
- Die [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx)-Klasse unterstützt die beiden neuen Eigenschaften **SliceIdentifierColumnName** und **SqlWriterCleanupScript** zur Unterstützung einer idempotenten Kopie in Azure SQL Data Warehouse. Im Artikel [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) finden Sie insbesondere in den Abschnitten [Mechanismus 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) und [Mechanismus 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) weitere Informationen zu diesen Eigenschaften.

- Wir unterstützen jetzt im Rahmen der Kopieraktivität das Ausführen einer gespeicherten Prozedur für Azure SQL-Datenbank- und Azure SQL Data Warehouse-Datenquellen. Die Klassen [SQLSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) und [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) verfügen über die folgenden Eigenschaften, um dies zu unterstützen: **SqlReaderStoredProcedureName** und **StoredProcedureParameters**. In den Artikeln [Azure SQL-Datenbank](data-factory-azure-sql-connector.md#sqlsource) und [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) auf Azure.com finden Sie ausführliche Informationen zu diesen Eigenschaften.

<!---HONumber=Oct15_HO3-->