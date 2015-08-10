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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Verwenden von Pig und Hive mit Data Factory
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. Dieser Artikel beschreibt das Verwenden der HDInsight-Aktivität mit Pig/Hive-Transformation in einer Azure Data-Factory-Pipeline. Weitere Informationen zum Anwenden von MapReduce-Programmen auf einem HDInsight-Cluster über eine Azure Data Factory-Pipeline finden Sie unter [Aufrufen von MapReduce-Programmen über Data Factory][data-factory-map-reduce].

## Exemplarische Vorgehensweise: Verwenden von Hive mit Azure Data Factory
Diese exemplarische Vorgehensweise enthält schrittweise Anleitungen zum Verwenden einer HDInsight-Aktivität mit Hive-Transformation in einer Data Factory-Pipeline.

### Voraussetzungen
1. Absolvieren Sie das Lernprogramm im Artikel [Erste Schritte mit Azure Data Factory][adfgetstarted].
2. Erstellen Sie die Datei **hivequery.hql** in einem Unterordner mit dem Namen **Hive** unter **C:\\ADFGetStarted** mit dem folgenden Inhalt.
    		
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

	> [AZURE.NOTE]Um die **Tez**-Engine zum Ausführen von Hive-Abfragen in der HQL-Datei zu verwenden, fügen Sie am Anfang der Datei "\*\*set hive.execution.engine=tez\*\*;" hinzu.
		
3.  Laden Sie die Datei **hivequery.hql** in den Container **adftutorial** in Ihrem Blobspeicher hoch.


### Exemplarische Vorgehensweise

#### Erstellen der Ausgabetabelle
        
1. Klicken Sie im **Data Factory-Editor** auf **Neues Dataset** und dann auf der Befehlsleiste auf **Azure-Blobspeicher**.
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch das folgende JSON-Skript:

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

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Tabelle bereitzustellen.


### Erstellen eines verknüpften Diensts für einen HDInsight-Cluster
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Der bedarfsgesteuerte HDInsight-Cluster wird automatisch vom Azure Data Factory-Dienst zum Verarbeiten von Daten erstellt und verwaltet. Weitere Informationen zum bedarfsgesteuerten verknüpften HDInsight-Dienst finden Sie unter [Bedarfsgesteuerter verknüpfter Dienst für Azure HDInsight](https://msdn.microsoft.com/library/dn893526.aspx). Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster. Beachten Sie, dass es mehr als 15 Minuten dauert, bis der HDInsight-Cluster bei Bedarf erstellt werden kann. Ihnen wird nur die Zeit berechnet, die der HDInsight-Cluster in Betrieb ist und Aufträge ausführt.

#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster
1. Klicken Sie auf der Befehlsleiste auf **Neu -\> Compute**, und wählen Sie im Menü **On-Demand-HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus: 
	1. Geben Sie für die Eigenschaft **clusterSize** die Größe des HDInsight-Clusters an.
	2. Geben Sie für die Eigenschaft **jobsContainer** den Namen des Standardcontainers an, in dem die Clusterprotokolle gespeichert werden sollen. Geben Sie im Rahmen dieses Lernprogramms **adfjobscontainer** an.
	3. Geben Sie für die Eigenschaft **timeToLive** an, wie lange sich der Cluster im Leerlauf befinden darf, bevor er gelöscht wird. 
	4. Geben Sie für die Eigenschaft **version** die HDInsight-Version an, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Geben Sie für **linkedServiceName** den Dienst **StorageLinkedService** an, den Sie im Lernprogramm "Erste Schritte" erstellt haben. 

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

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.
   
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Klicken Sie auf der Befehlsleiste auf **Neu -\> Compute**, und wählen Sie im Menü **HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus: 
	1. Geben Sie für die **clusterUri**-Eigenschaft die URL für Ihr HDInsight ein. Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Geben Sie für die **UserName**-Eigenschaft den Benutzernamen ein, der Zugriff auf den HDInsight-Cluster hat.
	3. Geben Sie für die **Password**-Eigenschaft das Kennwort für das Benutzerkonto ein. 
	4. Geben Sie für die **LinkedServiceName**-Eigenschaft das **StorageLinkedService**-Element ein. Dies ist der verknüpfte Dienst, den Sie im Lernprogramm "Erste Schritte" erstellt haben. 

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

### Erstellen und Planen der Pipeline
   
1. Klicken Sie auf der Befehlsleiste auf **Neue Pipeline**. Wenn der Befehl nicht angezeigt wird, klicken Sie auf **... \(Auslassungspunkte\)**, um ihn anzuzeigen. 
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch das folgende JSON-Skript. Wenn Sie einen eigenen Cluster verwenden möchten und die Schritte zum Erstellen des verknüpften Diensts **HDInsightLinkedService** ausgeführt haben, ersetzen Sie im folgenden JSON-Code **HDInsightOnDemandLinkedService** durch **HDInsightLinkedService**. 


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
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"defines":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
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

	> [AZURE.NOTE]Ersetzen Sie den Wert **StartDateTime** durch den Zeitpunkt drei Tage vor dem aktuellen Tag und den Wert **EndDateTime** durch den aktuellen Tag. "StartDateTime" und "EndDateTime" müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Ausgabetabelle soll jeden Tag erstellt werden, deshalb werden drei Slices erstellt.
	> 
	> Ersetzen Sie **your storage account** im JSON-Code durch den Namen Ihres Speicherkontos.
	
	Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](http://go.microsoft.com/fwlink/?LinkId=516971).
2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.
4. Weitere Informationen finden Sie im Abschnitt [Überwachen von Datasets und der Pipeline][adfgetstartedmonitoring] des Artikels [Erste Schritte mit Data Factory][adfgetstarted]. 

	> [AZURE.NOTE]Auf dem Blatt **AKTIVITÄTSAUSFÜHRUNG – DETAILS** für einen Slice einer Ausgabetabelle \(Ausgabetabelle auswählen -\> Slice auswählen -\> Eine im Portal auszuführende Aktivität auswählen\) sehen Sie Links zu Protokollen, die vom HDInsight-Cluster erstellt wurden. Sie können diese direkt im Portal prüfen oder auf Ihren Computer herunterladen.
  

## Pig-JSON-Beispiel
Beim Definieren einer Pig- oder Hive-Aktivität in einer JSON-Pipeline muss die Eigenschaft **type** auf **HDInsightActivity** festgelegt werden.

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
			"defines":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Beachten Sie Folgendes:**
	
- Der **Aktivitätstyp** ist auf **HDInsightActivity** festgelegt.
- **linkedServiceName** ist auf **MyHDInsightLinkedService** festgelegt. Im nachstehenden Abschnitt "Mit HDInsight verknüpfter Dienst" finden Sie weitere Informationen zum Erstellen eines mit HDInsight verknüpften Diensts.
- Der **Typ** der **Transformation** ist auf **Pig** festgelegt.
- Sie können das Pig-Skript inline für die Eigenschaft **script** angeben oder Skriptdateien in einem Azure-Blobspeicher speichern und mithilfe der Eigenschaft **scriptPath**, die später in diesem Artikel erläutert wird, auf die Datei verweisen. 
- Sie geben Parameter für das Pig-Skript mithilfe von **defines** an. Weiter unten in diesem Artikel erhalten Sie weitere Details. 


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
			"defines":
			{	
				"param1": "param1Value"
            }
		}
	}

**Beachten Sie Folgendes:**
	
- Der **Aktivitätstyp** ist auf **HDInsightActivity** festgelegt.
- **linkedServiceName** ist auf **MyHDInsightLinkedService** festgelegt. 
- Der **Typ** der **Transformation** ist auf **Hive** festgelegt.
- Sie können das Hive-Skript inline für die Eigenschaft **script** angeben oder Skriptdateien in einem Azure-Blobspeicher speichern und mithilfe der Eigenschaft **scriptPath**, die später in diesem Artikel erläutert wird, auf die Datei verweisen. 
- Sie geben Parameter für das Hive-Skript mithilfe von **defines** an. Weiter unten in diesem Artikel erhalten Sie weitere Details. 

> [AZURE.NOTE]In der [Entwicklerreferenz](http://go.microsoft.com/fwlink/?LinkId=516908) finden Sie Details zu Cmdlets, JSON-Schemas und Eigenschaften im Schema.


## Verwenden von Pig- und Hive-Skripts in einer HDInsight-Aktivität
Sie können Pig-/Hive-Skripts in einem Azure-BLOB-Speicher speichern, der mit dem HDInsight-Cluster verbunden ist, und mithilfe der folgenden Eigenschaften in JSON aus Pig-/Hive-Aktivitäten auf diese verweisen:

* **scriptPath**: Pfad zur Pig- oder Hive-Skriptdatei
* **scriptLinkedService**: Azure-Speicherkonto, das die Skriptdatei enthält

Das folgende JSON-Beispiel für eine Beispielpipeline verwendet eine Hive-Aktivität, die auf die Datei **transformdata.hql** verweist. Diese ist im Ordner **scripts** im Container **adfwalkthrough** im Azure-Blobspeicher gespeichert, der durch **StorageLinkedService** dargestellt wird.

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
						"defines":
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


> [AZURE.NOTE]Um die **Tez**-Engine zum Ausführen von Hive-Abfragen zu verwenden, führen Sie vor der Hive-Abfrage "\*\*set hive.execution.engine=tez\*\*;" aus.
> 
> In der [Entwicklerreferenz](http://go.microsoft.com/fwlink/?LinkId=516908) finden Sie Details zu Cmdlets, JSON-Schemas und Eigenschaften im Schema.

## Parametrisierte Pig- und Hive-Abfragen
Mit den Pig- und Hive-Aktivitäten in Data Factory können Sie mithilfe von **defines** Werte für Parameter angeben, die in den Pig- und Hive-Skripts verwendet werden. Der Abschnitt "defines" besteht aus dem Namen und Wert des Parameters.

Das folgende Beispiel veranschaulicht das Angeben von Parametern für ein Hive-Skript mithilfe von **defines**. Gehen Sie folgendermaßen vor, um parametrisierte Hive-Skripts zu verwenden:

1.	Legen Sie die Parameter in **defines** fest.
2.	Verweisen Sie im Inline-Hive-Skript oder in der Hive-Skriptdatei im Blobspeicher mithilfe von **${hiveconf:parameterName}** auf den Parameter.

   
    		
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
							"defines":
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
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines nahezu realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um mithilfe von Protokolldateien nützliche Einblicke zu gewinnen.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 

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
 

<!---HONumber=July15_HO5-->