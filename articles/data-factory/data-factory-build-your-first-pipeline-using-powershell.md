<properties
	pageTitle="Erste Schritte mit Azure Data Factory (Azure PowerShell)"
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mithilfe von Azure PowerShell."
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
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Erste Schritte mit Azure Data Factory (Azure PowerShell)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell Ihre erste Azure Data Factory erstellen.


## Voraussetzungen
Neben den im Thema „Tutorial – Übersicht“ aufgeführten vorausgesetzten Komponenten müssen Sie Folgendes installieren:

- Sie **müssen** den Artikel [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) lesen und die erforderlichen Schritte abschließen, bevor Sie fortfahren.
- **Azure PowerShell**. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) zum Installieren der neuesten Version von Azure PowerShell auf Ihrem Computer.
- (optional) In diesem Artikel werden nicht alle Data Factory-Cmdlets behandelt. In der [Data Factory-Cmdlet-Referenz](https://msdn.microsoft.com/library/dn820234.aspx) finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets. 

Bei Verwendung von Azure PowerShell [Version < 1.0][cmdlet-reference] müssen Sie die **hier** dokumentierten Cmdlets verwenden. Sie müssen auch die folgenden Befehle ausführen, bevor Sie die Data Factory-Cmdlets verwenden:
 
1. Starten Sie Azure PowerShell, und führen Sie die folgenden Befehle aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	1. Führen Sie **Add-AzureAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.
	2. Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	3. Führen Sie **Select-AzureSubscription** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
4. Wechseln Sie in den Modus „AzureResourceManager“, da die Azure Data Factory-Cmdlets in diesem Modus verfügbar sind: **Switch-AzureMode AzureResourceManager**.


## Schritt 1: Erstellen der Data Factory

In diesem Schritt erstellen Sie mit Azure PowerShell eine Azure Data Factory namens **FirstDataFactoryPSH**. Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. In diesem Schritt erstellen wir zunächst die Data Factory.

1. Starten Sie Azure PowerShell, und führen Sie den folgenden Befehl aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	- Führen Sie **Login-AzureRmAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription <Name of the subscription>** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
3. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup**, indem Sie den folgenden Befehl ausführen.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens ADFTutorialResourceGroup verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie diese anstelle der Ressourcengruppe ADFTutorialResourceGroup verwenden.
4. Führen Sie das Cmdlet **New-AzureRmDataFactory** zum Erstellen einer Data Factory mit dem Namen **FirstDataFactoryPSH** aus.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT]Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name „FirstDataFactoryPSH“ nicht verfügbar** ändern Sie den Namen (z. B. in „IhrNameFirstDataFactoryPSH“). Verwenden Sie diesen Namen beim Ausführen der Schritte in diesem Lernprogramm anstelle von "ADFTutorialDataFactoryPSH". Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
	> 
	> Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

Vor dem Erstellen einer Pipeline müssen Sie zunächst einige Data Factory-Entitäten erstellen. Zuerst erstellen Sie verknüpfte Dienste zum Verknüpfen von Datenspeichern/Berechnungen mit Ihrem Datenspeicher, definieren die Datasets für Ein- und Ausgabe, um Daten in verknüpften Datenspeichern darzustellen, und erstellen dann die Pipeline mit einer Aktivität, die diese Datasets verwendet.

## Schritt 2: Erstellen von verknüpften Diensten 
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um das in der Aktivität der Pipeline in diesem Beispiel angegebene Hive-Skript auszuführen. Sie müssen identifizieren, welche Datenspeicher-/Computedienste in Ihrem Szenario verwendet werden, und diese Dienste mit der Data Factory verknüpfen, indem Sie verknüpfte Dienste erstellen.

### Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. Im Rahmen dieses Tutorials verwenden Sie das gleiche Azure Storage-Konto, um  
Ein-/Ausgabedaten und die HQL-Skriptdatei zu speichern.

1. Erstellen Sie im Ordner „C:\\ADFGetStarted“ eine JSON-Datei mit dem Namen „StorageLinkedService.json“ mit folgendem Inhalt: Erstellen Sie den Ordner „ADFGetStarted“, falls dieser noch nicht vorhanden ist.

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

	Ersetzen Sie **Kontoname** durch den Namen Ihres Azure-Speicherkontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Informationen zu Azure-Speicherkonten](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. Wechseln Sie in Azure PowerShell zum Ordner „ADFGetStarted“.
3. Verwenden Sie das Cmdlet **New-AzureRmDataFactoryLinkedService**, um einen verknüpften Dienst zu erstellen. Dieses Cmdlet und andere Data Factory-Cmdlets, die Sie in diesem Lernprogramm verwenden, erfordern das Eingeben von Werten für die Parameter *ResourceGroupName* und *DataFactoryName*. Sie können auch **Get-AzureRmDataFactory** verwenden, um ein **DataFactory**-Objekt abzurufen und das Objekt ohne Eingabe von *ResourceGroupName* und *DataFactoryName* bei jeder Ausführung eines Cmdlets zu übergeben. Führen Sie folgenden Befehl aus, um die Ausgabe des Cmdlets **Get-AzureRmDataFactory** der Variablen **$df** zuzuweisen.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Führen Sie nun das Cmdlet **New-AzureRmDataFactoryLinkedService** zum Erstellen des verknüpften Diensts **StorageLinkedService** aus.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Wenn Sie das Cmdlet **Get-AzureRmDataFactory** noch nicht ausgeführt und die Ausgabe nicht der Variablen **$df** zugewiesen hätten, müssten Sie für die Parameter *ResourceGroupName* und *DataFactoryName* folgende Werte angeben.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	Wenn Sie Azure PowerShell mitten im Tutorial schließen, müssen Sie das Cmdlet **Get-AzureRmDataFactory** beim nächsten Start von Azure PowerShell ausführen, um das Tutorial abschließen zu können.

### Erstellen des mit Azure-HDInsight verknüpften Diensts
In diesem Schritt verknüpfen Sie einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Der HDInsight-Cluster wird automatisch zur Laufzeit erstellt und gelöscht, nachdem die Verarbeitung abgeschlossen und die angegebene Leerlaufzeit verstrichen ist. Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie Ihren eigenen HDInsight-Cluster verwenden. Weitere Informationen finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md).

1. Erstellen Sie im Ordner **C:\\ADFGetStarted** eine JSON-Datei mit dem Namen **HDInsightOnDemandLinkedService**.json mit folgendem Inhalt.

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

	| Eigenschaft | Beschreibung |
	| :------- | :---------- |
	| Version | Gibt die Version des zu erstellenden HDInsight-Clusters als 3.2 an. | 
	| ClusterSize | Dient zum Erstellen eines HDInsight-Clusters mit einem Knoten. | 
	| TimeToLive | Gibt die Leerlaufzeit des HDInsight-Clusters an, bevor er gelöscht wird. |
	| linkedServiceName | Gibt das Speicherkonto an, das verwendet wird, um die Protokolle zu speichern, die von HDInsight generiert werden. |

2. Führen Sie nun das Cmdlet **New-AzureRmDataFactoryLinkedService** zum Erstellen des verknüpften Diensts „HDInsightOnDemandLinkedService“ aus.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Schritt 3: Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten für die Hive-Verarbeitung darzustellen. Diese Datasets verweisen auf den **StorageLinkedService**, den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst weist auf ein Azure Storage-Konto, und Datasets geben Container, Ordner und Dateiname in dem Speicher an, der Eingabe- und Ausgabedaten enthält.

### Erstellen des Eingabedatasets
1. Erstellen Sie im Ordner **C:\\ADFGetStarted** eine JSON-Datei mit dem Namen **InputTable.json** mit folgendem Inhalt:

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	Im oben dargestellten JSON-Code wird ein Dataset mit dem Namen **AzureBlobInput** definiert, das Eingabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Eingabedaten im Blobcontainer **adfgetstarted** und im Ordner **inputdata** gespeichert werden.

	Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

	| Eigenschaft | Beschreibung |
	| :------- | :---------- |
	| Typ | Die Type-Eigenschaft wird auf „AzureBlob“ festgelegt, da sich Daten im Azure-Blobspeicher befinden. |  
	| linkedServiceName | verweist auf den „StorageLinkedService“, den Sie zuvor erstellt haben. |
	| fileName | Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Fall wird nur „input.log“ verarbeitet. |
	| Typ | Die Protokolldateien liegen im Textformat vor, daher wird „TextFormat“ verwendet. | 
	| columnDelimiter | Spalten in den Protokolldateien werden mit „,“ (Komma) getrennt. |
	| frequency/interval | „frequency“ wird auf „Month“ und „interval“ auf „1“ festgelegt, was bedeutet, dass die Eingabeslices monatlich verfügbar sind. | 
	| external | Diese Eigenschaft wird auf „true“ festgelegt, wenn die Eingabedaten nicht vom Data Factory-Dienst generiert werden. | 

2. Führen Sie in Azure PowerShell den folgenden Befehl zum Erstellen des Data Factory-Datensatzes aus.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Erstellen des Ausgabedatasets
Nun erstellen Sie das Ausgabedataset, das die im Azure-Blobspeicher gespeicherten Ausgabedaten darstellt.

1. Erstellen Sie im Ordner **C:\\ADFGetStarted** eine JSON-Datei mit dem Namen **OutputTable.json** mit folgendem Inhalt:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
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

	Im oben dargestellten JSON-Code wird ein Dataset mit dem Namen **AzureBlobOutput** definiert, das Ausgabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer **adfgetstarted** und im Ordner **partitioneddata** gespeichert werden. Der Abschnitt **availability** gibt an, dass das Ausgabe-DataSet monatlich erzeugt wird.

2. Führen Sie in Azure PowerShell den folgenden Befehl zum Erstellen des Data Factory-Datensatzes aus.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Schritt 3: Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline mit einer **HDInsightHive**-Aktivität. Beachten Sie, dass der Eingabeslice monatlich verfügbar ist („frequency“: „Month“, „interval“: „1“), der Ausgabeslice monatlich erstellt wird und die scheduler-Eigenschaft für die Aktivität auch auf monatlich (siehe unten) festgelegt ist. Die Einstellungen für das Ausgabedataset und den Aktivitätsplaner müssen übereinstimmen. Zu diesem Zeitpunkt steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe erzeugt. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. Am Ende dieses Abschnitts werden die in der folgenden JSON verwendeten Eigenschaften erläutert.


1. Erstellen Sie im Ordner „C:\\ADFGetStarted“ eine JSON-Datei mit dem Namen „MyFirstPipelinePSH.json“ mit folgendem Inhalt:

	> [AZURE.IMPORTANT]Ersetzen Sie im JSON-Code **storageaccountname** durch den Namen Ihres Speicherkontos.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	Im JSON-Codeausschnitt erstellen Sie eine Pipeline, die aus einer einzelnen Aktivität besteht. Diese nutzt Hive, um Daten in einem HDInsight-Cluster Daten zu verarbeiten.
	
	Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens **StorageLinkedService** angegeben ist) und im Ordner **script** im Container **adfgetstarted** gespeichert.

	Der Abschnitt **defines** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte (z. B. „${hiveconf:inputtable}“, „${hiveconf:partitionedtable}“) an das Hive-Skript übergeben werden.

	Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

	Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Computeinstanz ausgeführt wird, die vom **linkedServiceName** – **HDInsightOnDemandLinkedService** angegeben wurde.
2.  Vergewissern Sie sich, dass Sie die Datei **input.log** im Ordner **adfgetstarted/inputdata** im Azure-Blobspeicher sehen, und führen Sie den folgenden Befehl aus, um die Pipeline bereitzustellen. Da die Zeiten für **start** und **end** in der Vergangenheit festgelegt sind, und **isPaused** auf „false“ festgelegt ist, wird die Pipeline (Aktivität in der Pipeline) sofort nach der Bereitstellung ausgeführt. 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Glückwunsch, Sie haben mit Azure PowerShell Ihre erste Pipeline erfolgreich erstellt!

### <a name="MonitorDataSetsAndPipeline"></a> Überwachen der DataSets und der Pipeline
In diesem Schritt verwenden Sie Azure PowerShell zur Überwachung der Aktivitäten in einer Azure Data Factory.

1. Führen Sie **Get-AzureRmDataFactory** aus, und weisen Sie die Ausgabe der Variablen **$df** zu.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Führen Sie **Get-AzureRmDataFactorySlice** aus, um Details zu allen Slices in der Tabelle **EmpSQLTable** zu erhalten. Dies ist die Ausgabetabelle der Pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Beachten Sie, dass der "StartDateTime"-Wert, den Sie hier angeben, der im JSON-Code der Pipeline angegebenen Startzeit entspricht. Eine Ausgabe ähnlich der folgenden sollte angezeigt werden.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. Führen Sie **Get-AzureRmDataFactoryRun** aus, um die Details der Aktivitätsausführungen für einen bestimmten Slice abzurufen.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden.
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Sie können dieses Cmdlet weiter ausführen, bis der Slice den Status **Bereit** oder **Fehler** hat. Sobald der Slice den Status „Bereit“ hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden. Beachten Sie, dass die bedarfsgesteuerte Erstellung eines HDInsight-Clusters in der Regel einige Zeit dauert.

	![Ausgabedaten](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten Azure HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Lernprogramm: Kopieren von Daten aus einem Azure-Blob in Azure SQL](./data-factory-get-started.md).

### Referenzen
| Thema | Beschreibung |
| :---- | :---- |
| [Cmdlet-Referenz zu Data Factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) | Umfassende Dokumentation zu Data Factory-Cmdlets |
| [Pipelines](data-factory-create-pipelines.md) | In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. |
| [Datasets](data-factory-create-datasets.md) | Dieser Artikel bietet Ihnen Informationen über Datasets in Azure Data Factory.
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) | In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. |
| [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md) | In diesem Artikel wird das Überwachen, Verwalten und Debuggen Ihrer Pipelines beschrieben. Ferner wird erläutert, wie Warnungen erstellt und Benachrichtigungen bei Fehlern eingerichtet werden. |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_0114_2016-->