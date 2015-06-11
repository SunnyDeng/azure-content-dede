<properties 
	pageTitle="Verwenden von Pig und Hive mit Azure Data Factory" 
	description="Erfahren Sie, wie Sie Daten verarbeiten, indem Sie Pig und Hive-Skripts auf einem Azure HDInsight-Cluster von einer Azure Data Factory ausführen." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Verwenden von Pig und Hive mit Data Factory
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. Dieser Artikel beschreibt, mit der HDInsight-Aktivität mit Pig-Hive-Transformation in einer Pipeline Azure Data-Factory. Finden Sie unter [Aufrufen MapReduce-Programmen aus Daten Factory][data-factory-map-reduce] ausführliche Informationen zum Ausführen von MapReduce Programme auf einem HDInsight-cluster aus einem Azure Data Factory-Pipeline.

## Exemplarische Vorgehensweise: Verwenden von Hive mit Azure Data Factory
Diese exemplarische Vorgehensweise enthält schrittweise Anleitungen zum Verwenden einer HDInsight-Aktivität mit Hive-Transformation in einer Pipeline Data Factory.

### Voraussetzungen
1. Arbeiten Sie das Lernprogramm aus [Erste Schritte mit Azure Data Factory][adfgetstarted] Artikel.
2. Hochladen **emp.txt** Datei, die Sie erstellt, in der oben genannten Lernprogramm als haben **hiveinput\emp.txt** in den Adftutorial-Container im Blob-Speicher. Die **Hiveinput** Ordner wird automatisch erstellt, der **Adftutorial** Container beim Hochladen emp.txt-Datei mit der folgenden Syntax.

	> [AZURE.NOTE]Die Datei emp.txt ist nur eine dummy-Datei für diese exemplarische Vorgehensweise. Die tatsächlichen Eingabedaten stammt aus der **Hivesampletable** der auf dem HDInsight-Cluster bereits vorhanden ist. Die Pipeline verwendet überhaupt keine emp.txt-Datei.
	
2. Erstellen Sie **hivequery.hql** Datei in einem Unterordner namens **Hive** unter **C:\ADFGetStarted** mit folgendem Inhalt.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;

	> [AZURE.NOTE]Verwenden der **Tez** Engine Hive-Abfragen ausführen, in der Datei HQL, fügen "** hive.execution.engine=tez**; festlegen" am Anfang der Datei.
		
3.  Hochladen der **hivequery.hql** auf der **Adftutorial** Container im Blob-Speicher


### Exemplarische Vorgehensweise

#### Erstellen der Eingabetabelle
1. In der **DATA FACTORY** Blade für die **ADFTutorialDataFactory**, klicken Sie auf **Autor und Bereitstellen von** Data Factory-Editor zu starten.
	
	![Daten-Factory-Blade][data-factory-blade]

2. In der **Data Factory-Editor**, klicken Sie auf **Neues Dataset**, und klicken Sie dann auf **Azure Blob-Speicher** in der Befehlsleiste.
3. Ersetzen Sie das Skript JSON im rechten Bereich mit dem folgenden JSON-Skript:    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**Beachten Sie Folgendes:**
	
	- Speicherort **Typ** minFreeThreads auf **AzureBlobLocation**.
	- **LinkedServiceName** minFreeThreads auf **StorageLinkedService** Azure-Speicherkonten definiert.
	- **FolderPath** gibt der Blob-Container\folder für die Eingabedaten. 
	- **Häufigkeit = Tag** und **Intervall = 1** bedeutet, dass die Segmente täglich verfügbar sind
	- **WaitOnExternal** bedeutet, dass diese Daten nicht durch eine andere Pipeline erzeugt, es wird vielmehr erzeugt extern der Daten-Factory. 
	

	Finden Sie unter [Daten Factory-Entwicklerreferenz][developer-reference] für JSON-Eigenschaften.

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste in der Tabelle bereitstellen.
  
#### Erstellen der Ausgabetabelle
        
1. In der **Data Factory-Editor**, klicken Sie auf **Neues Dataset**, und klicken Sie dann auf **Azure Blob-Speicher** in der Befehlsleiste.
2. Ersetzen Sie das Skript JSON im rechten Bereich mit dem folgenden JSON-Skript:

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste in der Tabelle bereitstellen.


### Erstellen eines verknüpften Diensts für einen HDInsight-Cluster
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung Ihres eigenen HDInsight-Clusters kann der Cluster den Slice jedoch sofort verarbeiten. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster.

#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster
1. Klicken Sie auf **neue Compute** aus, und wählen Sie die Befehlsleiste **On-Demand-HDInsight-Cluster** aus dem Menü.
2. Gehen Sie in das JSON-Skript: 
	1. Für die **ClusterSize** -Eigenschaft, geben Sie die Größe des HDInsight-Clusters.
	2. Für die **JobsContainer** -Eigenschaft, geben Sie den Namen der Standardcontainer, in denen die Clusterprotokolle gespeichert werden. Geben Sie im Rahmen dieses Lernprogramms **Adfjobscontainer**.
	3. Für die **TimeToLive** -Eigenschaft angeben, wie lange der Cluster im Leerlauf befinden darf, bevor es gelöscht wird. 
	4. Für die **Version** -Eigenschaft, geben Sie die HDInsight-Version, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Für die **LinkedServiceName**, geben Sie **StorageLinkedService** dass Sie erhalten erstellt hätten-Schritte-Lernprogramm. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste, um den verknüpften Dienst bereitzustellen.
   
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Klicken Sie auf **neue Compute** aus, und wählen Sie die Befehlsleiste **HDInsight-Cluster** aus dem Menü.
2. Gehen Sie in das JSON-Skript: 
	1. Für die **ClusterUri** -Eigenschaft, geben Sie die URL für Ihre HDInsight. Zum Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Für die **Benutzername** -Eigenschaft, geben Sie den Benutzernamen, der Zugriff auf den HDInsight-Cluster verfügt.
	3. Für die **Kennwort** -Eigenschaft, geben Sie das Kennwort für den Benutzer. 
	4. Für die **LinkedServiceName** -Eigenschaft, geben Sie **StorageLinkedService**. Dies ist die verknüpfte Dienst, den Sie in Get-Schritte-Lernprogramm erstellt haben. 

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste, um den verknüpften Dienst bereitzustellen.

### Erstellen und Planen der Pipeline
   
1. Klicken Sie auf **neue Pipeline** auf der Befehlsleiste. Wenn Sie den Befehl nicht angezeigt werden, klicken Sie auf **... (Mit den drei Punkten)** um es anzuzeigen. 
2. Ersetzen Sie die JSON im rechten Bereich mit folgendem Skript JSON. Wenn Sie Ihre eigenen Cluster verwenden möchten, und die Schritte zum Erstellen der **HDInsightLinkedService** -Dienst verknüpft ersetzen **HDInsightOnDemandLinkedService** mit **HDInsightLinkedService** in den folgenden JSON. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]Ersetzen Sie **StartDateTime** -Wert mit den drei Tage vor dem aktuellen Tag und **EndDateTime** Wert mit dem aktuellen Tag. Ist das StartDateTime und EndDateTime [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601). Zum Beispiel: 2014-10-14T16:32:41Z. Die Ausgabetabelle soll jeden Tag erstellt werden, deshalb werden drei Slices erstellt.
	
	> [AZURE.NOTE]Ersetzen Sie **Ihres Speicherkontos** in JSON mit dem Namen Ihres Speicherkontos.
	
	Finden Sie unter [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) ausführliche Informationen zu JSON-Eigenschaften.
2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste auf die Pipeline bereitstellen.
4. Finden Sie unter [überwachen, Datasets und Pipeline][adfgetstartedmonitoring] Abschnitt [Erste Schritte mit Daten Factory][adfgetstarted] Artikel. 

	> [AZURE.NOTE]In der **AKTIVITÄTSDETAILS ausführen** Blade für ein Segment einer Ausgabetabelle (Ausgabetabelle auswählen -> Select Slice -> Wählen Sie eine Aktivität, die im Portal ausführen), sehen Sie Links zu den Protokollen, die von der HDInsight-Cluster erstellt. Sie können prüfen Sie sie in das Portal selbst oder auf Ihren Computer herunterladen.
  

## Pig-JSON-Beispiel
Beim Definieren einer Aktivität Pig oder Hive in einer Pipeline JSON, die **Typ** Eigenschaft muss festgelegt werden, um: **HDInsightActivity**.

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Beachten Sie Folgendes:**
	
- Aktivität **Typ** minFreeThreads auf **HDInsightActivity**.
- **LinkedServiceName** minFreeThreads auf **MyHDInsightLinkedService**. Finden Sie im Abschnitt Service verknüpft HDInsight unten Weitere Informationen zum Erstellen eines verknüpften HDInsight-Diensts.
- Die **Typ** von der **Transformation** minFreeThreads auf **Pig**.
- Können Sie angeben, Pig Skript Inline für den **Skript** Eigenschaft oder Store Skriptdateien in einem von Azure blob-Speicher und finden Sie in der Datei mit **Skriptpfad** -Eigenschaft, die weiter unten in diesem Artikel erläutert wird. 
- Geben Sie Parameter für das Skript Pig mithilfe der **ExtendedProperties**. Weiter unten in diesem Artikel erhalten Sie weitere Details. 


## Hive-JSON-Beispiel


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Beachten Sie Folgendes:**
	
- Aktivität **Typ** minFreeThreads auf **HDInsightActivity**.
- **LinkedServiceName** minFreeThreads auf **MyHDInsightLinkedService**. 
- Die **Typ** von der **Transformation** minFreeThreads auf **Hive**.
- Sie können angeben, dass Inline der Hive-Skript für die **Skript** Eigenschaft oder Store Skriptdateien in einem von Azure blob-Speicher und finden Sie in der Datei mit **Skriptpfad** -Eigenschaft, die weiter unten in diesem Artikel erläutert wird. 
- Geben Sie Parameter für das Hive-Skript mithilfe der **ExtendedProperties**. Weiter unten in diesem Artikel erhalten Sie weitere Details. 

> [AZURE.NOTE]Finden Sie unter [Entwicklerreferenz](http://go.microsoft.com/fwlink/?LinkId=516908) ausführliche Informationen zu Cmdlets, JSON-Schemas und Eigenschaften im Schema.


## Verwenden von Pig und Hive-Skripts in HDInsight-Aktivität
Sie können Pig-/Hive-Skripts in einem Azure-BLOB-Speicher speichern, der mit dem HDInsight-Cluster verbunden ist, und mithilfe der folgenden Eigenschaften in JSON aus Pig-/Hive-Aktivitäten auf diese verweisen:

* **Skriptpfad** – Pfad zur Skriptdatei Pig oder Hive
* **ScriptLinkedService** – Azure Storage-Konto, das die Skriptdatei enthält

Das folgende JSON-Beispiel für eine Beispiel-Pipeline verwendet eine Hive-Aktivität, die auf **transformdata.hql** in gespeicherte Datei **Skripts** Ordner in der **Adfwalkthrough** Container im Azure Blob-Speicher, dargestellt durch die **StorageLinkedService**.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}


> [AZURE.NOTE]Verwenden der **Tez** -Modul eine Hive-Abfrage ausführen, führen Sie "** hive.execution.engine=tez**; festlegen" vor dem Ausführen der Hive-Abfrage.
> 
> Finden Sie unter [Entwicklerreferenz](http://go.microsoft.com/fwlink/?LinkId=516908) ausführliche Informationen zu Cmdlets, JSON-Schemas und Eigenschaften im Schema.

## Parametrisierte Pig- und Hive-Abfragen
Die Daten Factory Pig und Hive-Aktivitäten ermöglichen es Ihnen, geben Sie Werte für Parameter, die in den Pig und Hive-Skripts verwendet werden, mit **ExtendedProperties**. Der Abschnitt "extendedProperties" besteht aus dem Namen des Parameters und dem Wert des Parameters.

Im folgenden Beispiel für die Angabe von Parametern für eine Hive-Skript mit **ExtendedProperties**. Gehen Sie folgendermaßen vor, um parametrisierte Hive-Skripts zu verwenden:

1.	Definieren Sie die Parameter in **ExtendedProperties**.
2.	In der Inline-Hive-Skript oder Hive-Skriptdatei, die in den BLOB-Speicher gespeichert, beziehen sich auf die Verwendung des Parameters **${Hiveconf:parameterName}**.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien, die mit Daten-Factory][adf-tutorial] | Dieser Artikel bietet eine End-to-End-exemplarische zum Implementieren von near Real World-Szenario mit Azure Data Factory zum Transformieren von Daten aus den Protokolldateien Einblicke in.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Entwicklerreferenz hat die umfassendes Referenzmaterial für die Cmdlets, JSON-Skripts, Funktionen usw.... 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir--> 