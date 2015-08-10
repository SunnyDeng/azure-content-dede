<properties
	pageTitle="Erstellen der ersten Pipeline mit Azure Data Factory"
	description="Dieses Tutorial zeigt, wie Sie eine Beispieldatenpipeline erstellen, die Daten mithilfe von Azure HDInsight und Azure PowerShell transformiert."
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
	ms.topic="hero-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Erstellen der ersten Azure Data Factory-Pipeline mit Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell Ihre erste Pipeline erstellen. Dieses Tutorial umfasst die folgenden Schritte:

1.	Erstellen der Data Factory
2.	Erstellen der verknüpften Dienste \(Datenspeicher, Compute-Instanzen\) und Datasets
3.	Erstellen der Pipeline

Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Eine ausführliche Übersicht über den Dienst finden Sie im Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).

## Schritt 1: Erstellen der Data Factory

In diesem Schritt erstellen Sie mit Azure PowerShell eine Azure Data Factory namens **ADFTutorialDataFactoryPSH**.

1. Starten Sie **Azure PowerShell**, und führen Sie die folgenden Befehle aus. Lassen Sie Azure PowerShell bis zum Ende dieses Lernprogramms geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie diese Befehle erneut ausführen.
	- Führen Sie **Add-AzureAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung für das Azure-Vorschauportal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Vorschauportal verwendet haben. 
2. Wechseln Sie in den Modus **AzureResourceManager**, da die Azure Data Factory-Cmdlets in diesem Modus verfügbar sind.

		Switch-AzureMode AzureResourceManager 
3. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup**, indem Sie den folgenden Befehl ausführen.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens **ADFTutorialResourceGroup** verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie diese anstelle der Ressourcengruppe ADFTutorialResourceGroup verwenden. 
4. Führen Sie das Cmdlet **New-AzureDataFactory** zum Erstellen einer Data Factory mit dem Namen **DataFactoryMyFirstPipelinePSH** aus.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"


	Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name "DataFactoryMyFirstPipelinePSH" nicht verfügbar** ändern Sie den Namen \(z. B. in "IhrNameDataFactoryMyFirstPipelinePSH"\). Verwenden Sie diesen Namen beim Ausführen der Schritte in diesem Lernprogramm anstelle von "ADFTutorialDataFactoryPSH".

In den folgenden Schritten erfahren Sie, wie Sie die verknüpften Dienste, Datasets und Pipeline erstellen, die Sie in diesem Tutorial verwenden.

## Schritt 2: Erstellen von verknüpften Diensten und Datasets
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory und erstellen dann ein Dataset, das die Ausgabedaten der Hive-Verarbeitung darstellt.

### Erstellen des mit Azure Storage verknüpften Diensts
1.	Erstellen Sie in **C:\\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **StorageLinkedService.json** mit folgendem Inhalt. Erstellen Sie den Ordner "ADFGetStartedPSH", falls dieser noch nicht vorhanden ist.

		{
		    "name": "StorageLinkedService",
		    "properties": {
		        "type": "AzureStorage",
		        "description": "",
		        "typeProperties": {
		            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    }
		}

	Ersetzen Sie **Kontoname** durch den Namen Ihres Azure-Speicherkontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2.	Wechseln Sie in **Azure PowerShell** zum Ordner **ADFGetStartedPSH**.
3.	Verwenden Sie das Cmdlet **New-AzureDataFactoryLinkedService**, um einen verknüpften Dienst zu erstellen. Dieses Cmdlet und andere Data Factory-Cmdlets, die Sie in diesem Lernprogramm verwenden, erfordern das Eingeben von Werten für die Parameter **ResourceGroupName** und **DataFactoryName**. Sie können auch **Get-AzureDataFactory** verwenden, um ein "DataFactory"-Objekt abzurufen und das Objekt ohne Eingabe von "ResourceGroupName" und "DataFactoryName" bei jeder Ausführung eines Cmdlets zu übergeben. Führen Sie folgenden Befehl aus, um die Ausgabe des Cmdlets **Get-AzureDataFactory** der Variablen **$df** zuzuweisen. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	Führen Sie nun das Cmdlet **New-AzureDataFactoryLinkedService** zum Erstellen des verknüpfte Diensts **StorageLinkedService** aus.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Wenn Sie das Cmdlet **Get-AzureDataFactory** noch nicht ausgeführt und die Ausgabe nicht der Variablen **$df** zugewiesen hätten, müssten Sie für die Parameter "ResourceGroupName" und "DataFactoryName" folgende Werte angeben.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Wenn Sie Azure PowerShell mitten im Lernprogramm schließen, müssen Sie das Cmdlet "Get-AzureDataFactory" beim nächsten Start von Azure PowerShell ausführen, um das Lernprogramm abschließen zu können.

### Erstellen des mit Azure-HDInsight verknüpften Diensts
Sie erstellen nun einen verknüpften Dienst für einen bedarfsgesteuerten HDInsight-Cluster, der zum Ausführen des Hive-Skripts verwendet wird.

1.	Erstellen Sie in **C:\\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **HDInsightOnDemandLinkedService.json** mit folgendem Inhalt. 


		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:05:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:
		
	Eigenschaft | Beschreibung
	-------- | -----------
	Version | Gibt die Version des zu erstellenden HDInsight-Clusters als 3.1 an. 
	ClusterSize | Dient zum Erstellen eines HDInsight-Clusters mit einem Knoten. 
	TimeToLive | Gibt die Leerlaufzeit des HDInsight-Clusters an, bevor er gelöscht wird.
	JobsContainer | Gibt den Namen des Auftragcontainers an, der erstellt wird, um die Protokolle zu speichern, die von HDInsight generiert werden.
	linkedServiceName | Gibt das Speicherkonto an, das verwendet wird, um die Protokolle zu speichern, die von HDInsight generiert werden.
2. Führen Sie nun das Cmdlet **New-AzureDataFactoryLinkedService** zum Erstellen des verknüpfte Diensts **HDInsightOnDemandLinkedService** aus.

		New-AzureDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Erstellen des Ausgabedatasets
Jetzt erstellen Sie das Ausgabedataset, das die im Azure- Blobspeicher gespeicherten Daten darstellt.

1.	Erstellen Sie im Ordner **C:\\ADFGetStartedPSH** eine JSON-Datei mit dem Namen **OutputTable.json** mit folgendem Inhalt:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}
	Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobOutput** und geben die Struktur der Daten an, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer namens **data** gespeichert werden und dass der Ordner **partitioneddata** heißt. Der Abschnitt **Verfügbarkeit** gibt an, dass das Ausgabedataset monatlich erzeugt wird.

2. Führen Sie in Azure PowerShell den folgenden Befehl zum Erstellen der Data Factory-Tabelle aus.

		New-AzureDataFactoryTable $df -File .\OutputTable.json

## Schritt 3: Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline.

1.	Erstellen Sie eine JSON-Datei mit dem Namen **MyFirstPipelinePSH.json** im Ordner **C:\\ADFGetStartedPSH** mit dem folgenden Inhalt: 

	> [AZURE.IMPORTANT]Ersetzen Sie im JSON-Code **storageaccountname** durch den Namen Ihres Speicherkontos.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
		
	Im JSON-Codeausschnitt erstellen Sie eine Pipeline, die aus einer einzelnen Aktivität besteht. Diese nutzt Hive, um Daten in einem HDInsight-Cluster Daten zu verarbeiten.
	
	Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto \(das neben "scriptLinkedService" als **StorageLinkedService** angegeben ist\) und in einem Container namens **script** gespeichert.

	Der Abschnitt **extendedProperties** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte \(z. B. ${hiveconf:PartitionedData}\) an das Hive-Skript übergeben werden.

	Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

	Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Compute-Instanz ausgeführt wird, die vom verknüpften Dienst **HDInsightOnDemandLinkedService** angegeben wurde.
2. Führen Sie den folgenden Befehl zum Erstellen der Data Factory-Tabelle aus. 
	
		New-AzureDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Glückwunsch, Sie haben mit Azure PowerShell Ihre erste Pipeline erfolgreich erstellt!

### <a name="MonitorDataSetsAndPipeline"></a> Überwachen der Datasets und der Pipeline
In diesem Schritt verwenden Sie Azure PowerShell zur Überwachung der Aktivitäten in einer Azure Data Factory.

1.	Führen Sie **Get-AzureDataFactory** aus, und weisen Sie die Ausgabe der Variablen "$df" zu.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH
 
2.	Führen Sie **Get-AzureDataFactorySlice** aus, um Details zu allen Slices in der Tabelle **EmpSQLTable** zu erhalten. Dies ist die Ausgabetabelle der Pipeline.

		Get-AzureDataFactorySlice $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Beachten Sie, dass der "StartDateTime"-Wert, den Sie hier angeben, der im JSON-Code der Pipeline angegebenen Startzeit entspricht. Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0
	
3.	Führen Sie **Get-AzureDataFactoryRun** aus, um die Details der Aktivitätsausführungen für einen **bestimmten** Slice abzurufen.

		Get-AzureDataFactoryRun $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Sie können dieses Cmdlet weiter ausführen, bis der Slice den Status "Bereit" oder "Fehler" hat. Sobald der Slice den Status "Bereit" hat, überprüfen Sie den Ordner "partitioneddata" im Container "data" in Ihrem Blobspeicher auf die Ausgabedaten. Beachten Sie, dass die bedarfsgesteuerte Erstellung eines HDInsight-Clusters in der Regel einige Zeit dauert.

In der [Data Factory-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/dn820234.aspx) finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.

 

## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität \(HDInsight-Aktivität\) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Tutorial: Kopieren von Daten aus einem Azure-Blob in Azure SQL](./data-factory-get-started.md).
  

<!---HONumber=July15_HO5-->