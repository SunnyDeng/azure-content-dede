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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Verwenden von Pig und Hive mit Data Factory
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten durch Verwendung von verknüpften Serverdiensten. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. 

- Eine **Kopieraktivität** kopiert Daten von einem Quellspeicher in einen Zielspeicher. Weitere Informationen zur Kopieraktivität finden Sie unter [Kopieren von Daten mit Data Factory][data-factory-copy-activity]. 
- Die **HDInsight-Aktivität** verarbeitet Daten durch Ausführen von Hive/Pig-Skripts oder MapReduce-Programmen auf einem HDInsight-Cluster. Die HDInsight-Aktivität unterstützt drei Transformationen: **Hive**, **Pig** und **MapReduce**. Die HDInsight-Aktivität kann mindestens eine Eingabe verwenden und mindestens eine Ausgabe produzieren.
 
Weitere Informationen zum Ausführen von MapReduce-Programmen auf HDInsight-Clustern über eine Azure Data Factory-Pipeline mithilfe von MapReduce-Transformationen der HDInsight-Aktivität finden Sie unter [Aufrufen von MapReduce-Programmen über Data Factory][data-factory-map-reduce]. In diesem Artikel wird die Verwendung der Pig/Hive-Transformation der HDInsight-Aktivität beschrieben.

## Themen in diesem Artikel

Abschnitt | Beschreibung
------- | -----------
[Pig-JSON-Beispiel](#PigJSON) | Dieser Abschnitt enthält ein JSON-Schema zur Definition einer HDInsight-Aktivität, die eine Pig-Transformation verwendet. 
[Hive-JSON-Beispiel](#HiveJSON) | Dieser Abschnitt enthält ein JSON-Schema zur Definition einer HDInsight-Aktivität, die eine Hive-Transformation verwendet. 
[Verwenden von Pig- und Hive-Skripts, die in Azure-BLOB-Speicher gespeichert sind](#ScriptInBlob) | Beschreibt das Verweisen auf Pig/Hive-Skripts, die von einer HDInsight-Aktivität mithilfe der Pig/Hive-Transformation in einem Azure Blob-Speicher gespeichert werden.
[Parametrisierte Pig- und Hive-Abfragen](#ParameterizeQueries) | Beschreibt, wie mithilfe der **extendedProperties**-Eigenschaft in JSON Werte für Parameter angegeben werden, die in Pig- und Hive-Skripts verwendet werden.
[Exemplarische Vorgehensweise: Verwenden von Hive mit Azure Data Factory](#Waltkthrough) | Bietet schrittweise Anweisungen zum Erstellen einer Pipeline, die Hive verwendet, um Daten zu verarbeiten.  



Beim Definieren einer Pig- oder Hive-Aktivität in einer JSON-Pipeline sollte die **type**-Eigenschaft folgendermaßen festgelegt werden: **HDInsightActivity**.

## <a name="PigJSON"></a> Pig-JSON-Beispiel

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
	
- Der Aktivitäts**typ** ist auf **HDInsightActivity** festgelegt.
- **linkedServiceName** ist auf **MyHDInsightLinkedService** festgelegt. 
- Der **Typ** der **Transformation** ist auf **Pig** festgelegt.
- Sie können das Pig-Skript inline für die **script**-Eigenschaft angeben oder Skriptdateien in einem Azure-BLOB-Speicher speichern und mithilfe der **scriptPath**-Eigenschaft, die später in diesem Artikel erläutert wird, auf die Datei verweisen. 
- Sie können Parameter für das Pig-Skript mithilfe von **extendedProperties** angeben. Weiter unten in diesem Artikel erhalten Sie weitere Details. 


## <a name="HiveJSON"></a> ## Hive-JSON-Beispiel


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
	
- Der Aktivitäts**typ** ist auf **HDInsightActivity** festgelegt.
- **linkedServiceName** ist auf **MyHDInsightLinkedService** festgelegt. 
- Der **Typ** der **Transformation** ist auf **Hive** festgelegt.
- Sie können das Hive-Skript inline für die **script**-Eigenschaft angeben oder Skriptdateien in einem Azure BLOB-Speicher speichern und mithilfe der **scriptPath**-Eigenschaft, die später in diesem Artikel erläutert wird, auf die Datei verweisen. 
- Sie können Parameter für das Hive-Skript mithilfe von **extendedProperties** angeben. Weiter unten in diesem Artikel erhalten Sie weitere Details. 

> [WACOM.NOTE] In der [Entwicklerreferenz](http://go.microsoft.com/fwlink/?LinkId=516908) finden Sie Details zu Cmdlets, JSON-Schemas und Eigenschaften in dem Schema. 


## <a name="ScriptInBlob"></a>Verwenden von Pig- und Hive-Skripts, die in Azure-BLOB-Speicher gespeichert sind
Sie können Pig-/Hive-Skripts in einem Azure-BLOB-Speicher speichern, der mit dem HDInsight-Cluster verbunden ist, und mithilfe der folgenden Eigenschaften in JSON aus Pig-/Hive-Aktivitäten auf diese verweisen: 

* **scriptPath** - Pfad zur Pig- oder Hive-Skriptdatei
* **scriptLinkedService** - Azure-Speicherkonto, das die Skriptdatei enthält

Das folgende JSON-Beispiel für eine Beispiel-Pipeline verwendet eine Hive-Aktivität, die auf die Datei **transformdata.hql** verweist, die im Ordner **Skripte** im **adfwalkthrough**-Container im Azure BLOB-Speicher gespeichert ist, der durch **StorageLinkedService** dargestellt wird.

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
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
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

  

> [WACOM.NOTE] In der [Entwicklerreferenz](http://go.microsoft.com/fwlink/?LinkId=516908) finden Sie Details zu Cmdlets, JSON-Schemas und Eigenschaften in dem Schema.

## <a name="ParameterizeQueries"></a>Parametrisierte Pig- und Hive-Abfragen
Mithilfe der Pig- und Hive-Aktivitäten in Data Factory können Sie mithilfe von **extendedProperties** Werte für Parameter angeben, die in den Pig- und Hive-Skripts verwendet werden. Der Abschnitt "extendedProperties" besteht aus dem Namen des Parameters und dem Wert des Parameters.

Das folgende Beispiel veranschaulicht das Angeben von Parametern für ein Hive-Skript mithilfe von **extendedProperties**. Gehen Sie folgendermaßen vor, um parametrisierte Hive-Skripts zu verwenden:

1.	Definieren Sie die Parameter in **extendedProperties**.
2.	Verweisen Sie in dem Inline-Hive-Skript oder der Hive-Skriptdatei, die im BLOB-Speicher gespeichert ist, mithilfe von **${hiveconf:parameterName}** auf den Parameter.

   
    		
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


-  

## <a name="Walkthrough"></a>Exemplarische Vorgehensweise: Verwenden von Hive mit Azure Data Factory
### Voraussetzungen
1. Absolvieren Sie das Lernprogramm aus dem Artikel [Erste Schritte mit Azure Data Factory][adfgetstarted].
2. Laden Sie die Datei**emp.txt**, die Sie in dem Lernprogramm oben erstellt haben als **hiveinput\emp.txt** in den Container "adftutorial" im BLOB-Speicher hoch. Der Ordner **hiveinput** wird automatisch im Container **adftutorial** erstellt, wenn Sie die Datei "emp.txt" mit dieser Syntax hochladen. 
2. Erstellen Sie die Datei **hivequery.hql** in einem Unterordner mit dem Namen **Hive** unter **C:\ADFGetStarted** mit dem folgenden Inhalt.
    		
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
		
3.  Laden Sie die Datei **hivequery.hql** in den Container **adftutorial** in Ihren BLOB-Speicher hoch.


### Exemplarische Vorgehensweise

#### Erstellen der Eingabetabelle
1. Erstellen Sie eine JSON-Datei mit dem Namen **HiveInputBlobTable.json** im Ordner **C:\ADFGetStarted\Hive** mit dem folgenden Inhalt.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
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
	
	- **location type** ist auf **AzureBlobLocation** festgelegt.
	- **linkedServiceName** ist auf **MyBlobStore** festgelegt, der ein Azure-Speicherkonto definiert.
	- **folderPath** gibt den Blob-Container\Ordner für die Eingabedaten an.
	- **frequency=Day** und **interval=1** bedeutet, dass die Slices täglich verfügbar sind.
	- **waitOnExternal** bedeutet, dass diese Daten nicht von einer anderen Pipeline produziert werden, sondern außerhalb der Data Factory.
	

	In der [Entwicklerreferenz zu Data Factory][developer-reference] finden Sie Beschreibungen von JSON-Eigenschaften.  

2. Starten Sie **Azure PowerShell**, und wechseln Sie in den **AzureResourceManager**-Modus, falls erforderlich.
    		
    	Switch-AzureMode AzureResourceManager

5. Wechseln Sie zum Ordner: **C:\ADFGetStarted\Hive**.
6. Führen Sie den folgenden Befehl aus, um die Eingabetabelle in der **ADFTutorialDataFactory** zu erstellen.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	In der [Cmdlet-Referenz zu Data Factory][cmdlet-reference] finden Sie eine detaillierte Übersicht zu Data Factory-Cmdlets. 
#### Erstellen der Ausgabetabelle
        
1. Erstellen Sie eine JSON-Datei mit dem Namen **HiveOutputBlobTable.json** mit dem folgenden Inhalt, und speichern Sie sie im Ordner **C:\ADFGetStarted\Hive**.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Führen Sie den folgenden Befehl aus, um die Ausgabetabelle in der **ADFTutorialDataFactory** zu erstellen.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### Erstellen eines verknüpften Diensts für einen HDInsight-Cluster
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung Ihres eigenen HDInsight-Clusters kann der Cluster den Slice jedoch sofort verarbeiten. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster. 

#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster
1. Erstellen Sie eine JSON-Datei mit dem Namen **HDInsightOnDemandCluster.json** mit dem folgenden Inhalt, und speichern Sie sie im Ordner **C:\ADFGetStarted\Hive**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Starten Sie **Azure PowerShell**, und führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager**-Modus verfügbar.

         switch-azuremode AzureResourceManager
		

3. Wechseln Sie zum Ordner **C:\ADFGetstarted\Hive**.
4. Führen Sie den folgenden Befehl zum Erstellen des verknüpften Diensts für den bedarfsgesteuerten HDInsight-Cluster aus.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. Die Tabellen und verknüpften Dienste sollten im Fenster **Data Factory** im **Azure-Vorschauportal** angezeigt werden.    
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Erstellen Sie eine JSON-Datei mit dem Namen **MyHDInsightCluster.json** mit dem folgenden Inhalt, und speichern Sie sie im Ordner **C:\ADFGetStarted\Hive**. Ersetzen Sie den Clusternamen, den Benutzernamen und das Kennwort durch geeignete Werte, bevor Sie die JSON-Datei speichern.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Starten Sie **Azure PowerShell**, und führen Sie den folgenden Befehl aus, um zum Modus **AzureResourceManager** zu wechseln. Die Cmdlets von Azure Data Factory sind im Modus **AzureResourceManager** verfügbar.

         switch-azuremode AzureResourceManager
		

3. Wechseln Sie zum Ordner **C:\ADFGetstarted\Hive**.
4. Führen Sie den folgenden Befehl aus, um den verknüpften Dienst für Ihren eigenen HDInsight-Cluster zu erstellen.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### Erstellen und Planen der Pipeline
   
1. Erstellen Sie eine JSON-Datei mit dem Namen **ADFTutorialHivePipeline.json** mit dem folgenden Inhalt, und speichern Sie sie im Ordner **C:\ADFGetStarted\Hive**. Wenn Sie Ihren eigenen Cluster verwenden möchten und die Schritte zum Erstellen des verknüpften Diensts **MyHDInsightCluster** ausgeführt haben, ersetzen Sie in der folgenden JSON **HDInsightOnDemandCluster** durch **MyHDInsightCluster**. 


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
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
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

2. Führen Sie den folgenden Befehl aus, um die Pipeline zu erstellen.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. Planen Sie die Pipeline.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] Ersetzen Sie den **StartDateTime**-Wert durch die drei Tage vor dem aktuellen Tag und den **EndDateTime-Wert ** durch den aktuellen Tag. "StartDateTime" und "EndDateTime" sind UTC-Zeiten und müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. 
	> Wenn Sie keinen **EndDateTime**-Wert angeben, wird er als "**StartDateTime + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie **9/9/9999** als **EndDateTime** an.
  	
	Die Ausgabetabelle soll jeden Tag erstellt werden, deshalb werden drei Slices erstellt. 

4. Weitere Informationen finden Sie im Abschnitt [Überwachen von Datasets und der Pipeline][adfgetstartedmonitoring] des Artikels [Erste Schritte mit Data Factory][adfgetstarted].   

## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Einführung in Azure Data Factory][data-factory-introduction] | In diesem Artikel werden der Azure Data Factory-Dienst, Azure Data Factory-Konzepte, der bereitgestellte Nutzen und unterstützte Szenarien erläutert.
[Erste Schritte mit Azure Data Factory][adf-getstarted] | Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline.
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Probleme in Azure Data Factory behoben werden.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Entwicklerreferenz]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure-Portal]: http://portal.azure.com

<!--HONumber=35.2-->

<!--HONumber=46--> 
