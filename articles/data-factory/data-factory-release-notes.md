<properties 
	pageTitle="Data Factory – Anmerkungen zu dieser Version | Microsoft Azure" 
	description="DataFactory – Anmerkungen zur Version" 
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
	ms.date="10/09/2015" 
	ms.author="spelluru"/>

# Versionshinweise für Azure Data Factory
Im Artikel zu [Data Factory – .NET-API-Änderungsprotokoll](data-factory-api-change-log.md) finden Sie Informationen zu Änderungen des Data Factory-.NET-SDK in einer bestimmten Version.

## Anmerkungen zur Version von Data Factory vom 17.7.2015
Die folgenden JSON-Änderungen wurden mit der Version vom Juli 2015 von Azure PowerShell eingeführt.

## Aktualisierung von Typen verknüpfter Dienste, Tabellen und Aktivitäten
Ressourcentyp | Aktueller Name in JSON | Neuer Name in JSON
------------- | -------------------- | ----------------
Verknüpfter Dienst (Datenquelle) | AzureSqlLinkedService | AzureSqlDatabase
Verknüpfter Dienst (Datenquelle) | AzureStorageLinkedService | AzureStorage
Verknüpfter Dienst (Datenquelle) | DocumentDbLinkedService | DocumentDb
Verknüpfter Dienst (Datenquelle) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
Verknüpfter Dienst (Datenquelle) | OnPremisesOracleLinkedService | OnPremisesOracle
Verknüpfter Dienst (Datenquelle) | OnPremisesSqlLinkedService | OnPremisesSqlServer
Verknüpfter Dienst (Datenquelle) | OnPremisesMySqlLinkedService | OnPremisesMySql
Verknüpfter Dienst (Datenquelle) | OnPremisesDb2LinkedService | OnPremisesDb2
Verknüpfter Dienst (Datenquelle) | OnPremisesTeradataLinkedService | OnPremisesTeradata
Verknüpfter Dienst (Datenquelle) | OnPremisesSybaseLinkedService | OnPremisesSybase
Verknüpfter Dienst (Datenquelle) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
Verknüpfter Dienst (Compute) | AzureMlLinkedService | AzureML
Verknüpfter Dienst (Compute) | HDInsightBYOCLinkedService | HDInsight
Verknüpfter Dienst (Compute) | HDInsightOnDemandLinkedService | HDInsightOnDemand
Verknüpfter Dienst (Compute) | AzureBatchLinkedService | AzureBatch
Datensatz | AzureBlobLocation | AzureBlob
Datensatz | AzureTableLocation | AzureTable
Datensatz | AzureSqlTableLocation | AzureSqlTable
Datensatz | DocumentDbCollectionLocation | DocumentDbCollection 
Datensatz | OnPremisesFileSystemLocation | FileShare
Datensatz | OnPremisesOracleTableLocation | OracleTable
Datensatz | OnPremisesSqlServerTableLocation | SqlServerTable
Datensatz | RelationTableLocation | RelationalTable
Aktivität | CopyActivity | Kopieren
Aktivität | HDInsightActivity (Hive-Transformation) | HDInsightHive
Aktivität | HDInsightActivity (Pig-Transformation) | HDInsightPig
Aktivität | HDInsightActivity (MapReduce-Transformation) | HDInsightMapReduce
Aktivität | HDInsightActivity (Streaming) | HDInsightHadoopStreaming
Aktivität | AzureMLBatchScoringActivity | AzureMLBatchScoring
Aktivität | StoredProcedureActivity | SqlServerStoredProcedure

## Neues typeProperties-element
Das neue **typeProperties**-Element enthält typspezifische Eigenschaften für verknüpfte Dienste/Tabellen/Aktivitäten.

### Alter JSON-Text für verknüpfte Dienste
	{
	    "name": "StorageLinkedService",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

### Neuer JSON-Text für verknüpfte Dienste
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

Beachten Sie Folgendes:

- Die **type**-Eigenschaft wurde eine Ebene nach oben verschoben und auf **AzureStorage** festgelegt (geändert von **AzureStorageLinkedService** in **AzureStorage**) 
- Neues **typeProperties**-Element, das Eigenschaften enthält, die von dem mit Azure-Speicher verknüpften Dienst unterstützt werden (in diesem Beispiel **connectionString**).  

### Alter JSON-Text für DataSet
	{
	    "name": "EmpTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureTableLocation",
	            "tableName": "myazuretable",
	            "linkedServiceName": "MyLinkedService"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

### Neuer JSON-Text für DataSet

	{
	    "name": "EmpTable",
	    "properties": {
	        "type": "AzureTable",
	        "linkedServiceName": "MyLinkedServiceName",
	        "typeProperties": {
	            "tableName": "myazuretable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Beachten Sie Folgendes:

- Die **type**-Eigenschaft wurde um eine Ebene nach oben verschobenen, und der Name des Typs **AzureTableLocation** wurde geändert in **AzureTable**.
- Der **linkedServiceName** wurde um eine Ebene nach oben verschoben. 
- Das **location**-Element wurde entfernt, und die typspezifischen Eigenschaften wie z. B. **tableName**, die im **location**-Abschnitt angegeben wurden, werden im neuen **typeProperties**-Abschnitt festgelegt.  

### Alte JSON-Aktivität

	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "transformation":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}   

### Neue JSON-Aktivität
	
	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}

Beachten Sie Folgendes:

- Beachten Sie, dass das **transformation**-Element durch das neue **typeProperties**-Element ersetzt wurde.

## waitOnExternal-Element wurde entfernt.
Das **waitOnExternal**-Element wird durch die neuen Eigenschaften **external** und**externalData** ersetzt.

### Altes JSON
	{
	    "name": "EmpTableFromBlob",
	    "properties":
	    {
	        "location": 
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "adftutorial/",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	            "linkedServiceName": "StorageLinkedService"
	        },
	        "availability": 
	        {
	            "frequency": "hour",
	            "interval": 1,
                "waitOnExternal": 
				{
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": "3"			
				}
	        }
	    }
	} 

### Neues JSON
	{
	  "name": "EmpTableFromBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": "3"
	      }
	    }
	  }
	}

Beachten Sie Folgendes:

- Die **waitOnExternal**-Eigenschaft wurde aus dem **availability**-Abschnitt entfernt. 
- Eine neue **external**-Eigenschaft wurde eine Ebene höher hinzugefügt und wird für eine externe Tabelle auf **true** festgelegt. 
- Die Eigenschaften des **waitOnExternal**-Elements wie z. B. **retryInterval** wurden dem neuen **externalData**-Abschnitt im **Policy**-Element hinzugefügt.
- Das **externalData**-Element ist ein optionales Element. 
- Für die Verwendung des **externalData**-Elements muss die **external**-Eigenschaft auf **true** festgelegt sein. 
 

## Neue copyBehavior-Eigenschaft für BlobSink
**BlobSink** unterstützt die neue Eigenschaft **copyBehavior**. Diese Eigenschaft definiert das Verhalten beim Kopieren, wenn die Quelle **BlobSource** oder **FileSystem** ist. Es gibt drei mögliche Werte für die **copyBehavior**-Eigenschaft.

**PreserveHierarchy**: Bewahrt die Dateihierarchie im Zielordner, d. h., der relative Pfad der Quelldatei zum Quellordner ist identisch mit dem relativen Pfad der Zieldatei zum Zielordner.


**FlattenHierarchy**: Alle Dateien aus dem Quellordner werden in der ersten Ebene des Zielordners gespeichert. Für die Zieldateien wird ein automatisch ein Name erzeugt.


**MergeFiles**: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen.
 
## Neue getDebugInfo-Eigenschaft für alle HDInsight-Aktivitäten
Die HDInsight-Aktivitäten (Hive, Pig, MapReduce, Hadoop-Streaming) unterstützen die neue **getDebugInfo**-Eigenschaft. Die **getDebugInfo**-Eigenschaft ist ein optionales Element. Wenn die Eigenschaft als **Failure** festgelegt wird, werden die Protokolle nur bei Ausführungsfehlern heruntergeladen. Wenn die Eigenschaft auf **All** festgelegt wird, werden Protokolle immer heruntergeladen, unabhängig vom Ausführungsstatus. Wenn die Eigenschaft auf **None** festgelegt wird, werden keine Protokolle heruntergeladen.

  
     

## Anmerkungen zur Version von Data Factory vom 10.04.2015
Die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** befinden sich nun auf dem Blatt **TABELLE**. Diese Listen werden anhand der Aktualisierungszeit des Slices sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.

-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set-AzureRmDataFactorySliceStatus**, oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).

Klicken Sie auf den Titel der Listen oder auf **...** (Auslassungspunkte), um eine umfangreichere Liste mit Slices anzuzeigen. Klicken Sie auf der Symbolleiste auf **Filter**, um die Slices zu filtern.
 
Durch Klicken auf die Kachel **Datenslices (nach Slicezeit)** können Sie Slices weiterhin sortiert nach der Slicezeit anzeigen. Die Slices in diesen Auflistungen sind nach Slicezeit sortiert. Bei einem stündlichen Zeitplan sind die Slices beispielsweise wie folgt sortiert: - 04.04.2015 17:00 Uhr In Bearbeitung - 04.04.2015 16:00 Uhr Erfolg - 04.04.2015 15:00 Uhr Fehler

Wenn jedoch ein älterer Slice erneut ausgeführt wird, wird er nicht oben in dieser Liste angezeigt, obwohl dies vermutlich für den Benutzer interessant wäre.

## Anmerkungen zur Version von Data Factory vom 31.3.2015
- Das aktualisierte Installationspaket für das **Datenverwaltungsgateway** wurde im [Microsoft Download Center][adf-gateway-download] bereitgestellt.
- Das Kopieren aus dem **lokalen Dateisystem in einen Azure-Blob** wird jetzt unterstützt. Weitere Informationen finden Sie in den folgenden Themen.
	-  [Mit lokalem Dateisystem verknüpfter Dienst](https://msdn.microsoft.com/library/dn930836.aspx)
	-  ["OnPremisesFileSystemLocation"-Eigenschaften im JSON-Code einer Tabelle](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx). Siehe die aktualisierte Kopiermatrix und die aktualisierten Eigenschaften **FileSystemSource**. 
-  Das Kopieren aus einer **lokalen Oracle-Datenbank in einen Azure-Blob** wird jetzt unterstützt. Weitere Informationen finden Sie in den folgenden Themen: 
	-  [Mit lokaler Oracle-Datenbank verknüpfter Dienst](https://msdn.microsoft.com/library/dn948537.aspx)
	-  ["OnPremisesOracleTableLocation-Eigenschaften" im JSON-Code einer Tabelle](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx). Siehe die aktualisierte Kopiermatrix und die aktualisierten Eigenschaften **OracleSource**.
-  Sie können die Codierung für Textdateien in einem Azure-Blob angeben. Siehe die neue Eigenschaft [encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Beim Kopieren in die SQL-Senke können Sie eine gespeicherte Prozedur mit zusätzlichen Parametern aufrufen.    

Siehe den Blogbeitrag [Azure Data Factory-Update – Neue Datenspeicher](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/), der weitere Informationen und Beispiele bietet.

## Anmerkungen zur Version von Data Factory vom 27.2.2015

### Neue Verbesserungen
- **Azure Data Factory-Editor**: Der Data Factory-Editor, der zum Azure-Portal gehört, dient zum Erstellen, Bearbeiten und Bereitstellen von JSON-Dateien, die verknüpfte Dienste, Datasets und Pipelines definieren. Hauptaufgabe des Editors ist es, Ihnen eine schnelle und schlanke Benutzeroberfläche zum Erstellen von Azure Data Factory-Artefakten zu bieten, ohne dass Sie Azure PowerShell installieren und PowerShell-Cmdlets einsetzen müssen. Im Blogbeitrag [Azure Data Factory-Editor – ein schlanker Web-Editor][adf-editor-blog] finden Sie eine Kurzübersicht sowie ein Video zum Data Factory-Editor. Eine ausführliche Übersicht über den Editor finden Sie im Artikel [Data Factory-Editor][adf-editor].          

### Änderungen

## Anmerkungen zur Version von Data Factory vom 26.1.2015 ##

### Änderungen
- Das aktualisierte Installationspaket für das **Datenverwaltungsgateway** wurde im [Microsoft Download Center][adf-gateway-download] bereitgestellt. Ab dieser Version finden Sie an dieser Downloadadresse das neueste Datenverwaltungsgateway für den Einsatz mit Azure Data Factory. Dieses Installationspaket umfasst sowohl Azure Data Factory als auch Power BI für Office 365-Dienste. Wenn Sie beide Dienste nutzen, beachten Sie, dass Gateways für Data Factory und Power BI auf verschiedenen Computern installiert und gemäß den Anleitungen in der Data Factory- bzw. Power BI-Dokumentation unterschiedlich konfiguriert werden müssen.
- Die **Kopieraktivität** unterstützt jetzt das Kopieren von Daten zwischen einer lokalen SQL Server-Datenbank und einer Azure SQL-Datenbank. 
- **SqlSink** unterstützt die neue Eigenschaft **WriteBatchTimeout**. Mit dieser Eigenschaft können Sie flexibel festlegen, wie lange auf den Abschluss des Batcheinfügevorgangs gewartet werden soll, bis ein Timeout erfolgt. Für einen Hybridkopiervorgang (der eine lokale und eine Clouddatenquelle umfasst) benötigen Sie Version 1.4 oder höher des Gateways, um diese Eigenschaft verwenden zu können. 
- Der **mit SQL Server verknüpfte Dienst** unterstützt jetzt die **Windows-Authentifizierung**. 
	- Beim Erstellen eines mit SQL Server verknüpften Diensts im Portal können Sie jetzt die Windows-Authentifizierung wählen und entsprechende Anmeldeinformationen festlegen. Dazu müssen Sie Version 1.4 oder höher des Gateways verwenden. 
	- Beim Erstellen eines mit SQL Server verknüpften Diensts mit Azure PowerShell können Sie Verbindungsinformationen unverschlüsselt angeben oder die Verbindung mit dem aktualisierten Cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) verschlüsseln. Anschließend verwenden Sie die verschlüsselte Zeichenfolge für die Eigenschaft mit der Verbindungszeichenfolge in der JSON-Nutzlast des verknüpften Diensts. Die Verschlüsselungsfunktion wird noch nicht vom Cmdlet „New-AzureRmDataFactoryEncryptValue“ unterstützt. 

## Anmerkungen zur Version von Data Factory vom 12.11.2014 ##

### Neue Verbesserungen

- Azure Machine Learning-Integration
	- Mit dieser Version des Azure Data Factory-Diensts können Sie Azure Data Factory mit Azure Machine Learning (ML) mithilfe von **AzureMLLinkedService** und **AzureMLBatchScoringActivity** integrieren. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Data Factory und Azure Machine Learning][adf-azure-ml]. 
- Der Status der Gatewayversion wird bereitgestellt.
	- Der Status „NewVersionAvailable“ wird im Azure-Portal und in der Ausgabe des Cmdlets „Get-AzureRmDataFactoryGateway“ angezeigt, wenn eine neuere Version des Gateways als die derzeit installierte Version verfügbar ist. Sie können dann den Portalanweisungen folgen, um die neue Installationsdatei (MSI) herunterzuladen und auszuführen, um das neueste Gateway zu installieren. Es ist keine zusätzliche Konfiguration erforderlich.

### Änderungen

- "JobsContainer" in "HdInsightOnDemandLinkedService" wird entfernt.
	- In der JSON-Definition für "HDInsightOnDemandLinkedService" müssen Sie die Eigenschaft **jobsContainer** nicht länger angeben. Wenn Sie die Eigenschaft für einen verknüpften, bedarfsgesteuerten Dienst angegeben haben, wird die Eigenschaft ignoriert. Sie können die Eigenschaft aus der JSON-Definition für den verknüpften Dienst entfernen und die Definition des verknüpften Diensts mithilfe des Cmdlets „New-AzureRmDataFactoryLinkedService“ aktualisieren.
- Optionale Konfigurationsparameter für "HDInsightOnDemandLinkedService"
	- Diese Version führt die Unterstützung für einige optionale Konfigurationsparameter für "HDInsightOnDemandLinked" (bedarfsgesteuerter HDInsight-Cluster) ein. Weitere Informationen finden Sie unter [ClusterCreateParameters-Eigenschaften][on-demand-hdi-parameters].
- Speicherort für Gateway wird entfernt.
	- Wenn Sie ein Azure Data Factory-Gateway über das Portal oder PowerShell (New-AzureRmDataFactoryGateway) erstellen, müssen Sie den Speicherort für das Gateway nicht länger angeben. Die Region für die Data Factory wird übernommen. Ebenso wird zum Konfigurieren eines verknüpften SQL Server-Diensts mithilfe von JSON die Eigenschaft "gatewayLocation" nicht länger benötigt. Das Data Factory .NET SDK wird ebenfalls aktualisiert, um diese Änderungen widerzuspiegeln.
	- Wenn Sie eine ältere Version des SDK und von Azure PowerShell verwenden, müssen Sie weiterhin die Standorteinstellung bereitstellen.
 
     

#### Wichtige Änderungen
	
- CustomActivity zu DotNetActivity
	- Die Schnittstelle **ICustomActivity** wurde in **IDotNetActivity** umbenannt. Sie müssen Data Factory NuGet-Pakete aktualisieren und "ICustomActivity" im Quellcode für Ihre benutzerdefinierte Aktivität in "IDotNetActivity" ändern.  
	- Der Typ der benutzerdefinierten Aktivität muss in deren JSON-Definition von **CustomActivity** in **DotNetActivity** geändert werden. 
	- Die Klassen **CustomActivity** und **CustomActivityProperties** wurden mit dem gleichen Eigenschaftensatz in **DotNetActivity** und **DotNetActivityProperties** umbenannt.

		Wenn Sie die ältere Version des SDK und von Azure PowerShell verwenden, können Sie weiterhin "CustomActivity" statt "DotNetActivity" verwenden.
    
  		Eine exemplarische Vorgehensweise zum Erstellen einer benutzerdefinierten Aktivität und zu deren Verwendung in einer Azure Data Factory-Pipeline finden Sie unter [Verwenden benutzerdefinierter Aktivitäten in einer Azure Data Factory-Pipeline][adf-custom-activities].

[adf-azure-ml]: data-factory-azure-ml-batch-execution-activity.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-editor]: data-factory-editor.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=AcomDC_0107_2016-->