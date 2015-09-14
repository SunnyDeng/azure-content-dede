<properties 
	pageTitle="Pig-Aktivität"
	description="Erfahren Sie, wie Sie die Pig-Aktivität in Azure Data Factory verwenden können, um Pig-Abfragen in einem bedarfsgesteuerten/eigenen HDInsight-Cluster auszuführen."
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

# Pig-Aktivität

Die HDInsight-Pig-Aktivität in einer Data Factory-[Pipeline](data-factory-create-pipelines.md) führt Pig-Abfragen in [eigenen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-Clustern aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der einen allgemeinen Überblick über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

## Syntax

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
	        	"inputs": [
	          		{
	            		"name": "input tables"
	          		}
	        	],
	        	"outputs": [
	          		{
	            		"name": "output tables"
	          		}
        		],
	        	"linkedServiceName": "MyHDInsightLinkedService",
	        	"typeProperties": {
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## Syntaxdetails

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Name | Der Name der Aktivität | Ja
description | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Nein
Typ | HDInsightPig | Ja
inputs | Von der Pig-Aktivität genutzte Eingabe(n) | Nein
outputs | Von der Pig-Aktivität genutzte Ausgabe(n) | Ja
linkedServiceName | Verweis auf den HDInsight-Cluster, der als verknüpfter Dienst in Data Factory registriert ist. | Ja
script | Angabe des Pig-Skripts inline | Nein
Skriptpfad | Speichern Sie das Pig-Skript in einem Azure-Blobspeicher, und geben Sie den Pfad zur Datei an. Verwenden Sie die Eigenschaft "script" oder "scriptPath". Beide können nicht zusammen verwendet werden. | Nein
defines | Geben Sie Parameter als Schlüssel-Wert-Paare für Verweise innerhalb des Pig-Skripts an. | Nein

## Beispiel

Betrachten wir ein Beispiel mit Analysen von Spielprotokollen, in dem Sie die Zeit ermitteln möchten, die Benutzern mit den Spielen Ihres Unternehmens verbringen.
 
Im Folgenden finden Sie ein Beispielspielprotokoll mit Kommas (,) als Trennzeichen und den folgenden Feldern: ID, SessionStart, Duration, SrcIPAddress und GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

Das **Pig-Skript** zur Verarbeitung dieser Daten sieht folgendermaßen aus:

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Um dieses Hive-Skript in einer Data Factory-Pipeline auszuführen, müssen Sie folgende Schritte ausführen:

1. Erstellen Sie einen verknüpften Dienst, um [Ihren eigenen HDInsight-Compute-Cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) zu registrieren oder einen [bedarfsgesteuerten HDInsight-Compute-Cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) zu konfigurieren. Wir nennen diesen verknüpften Dienst "HDInsightLinkedService".
2.	Erstellen Sie einen [verknüpften Dienst](data-factory-azure-storage-connector.md), um die Verbindung mit dem Azure-Blobspeicher zu konfigurieren, in dem die Daten gehostet werden. Wir nennen diesen verknüpften Dienst "StorageLinkedService".
3.	Erstellen Sie [Datasets](data-factory-create-datasets.md), die auf die Eingabe- und die Ausgabedaten verweisen. Wir nennen das Eingabe-Dataset "PigSampleIn" und das Ausgabe-Dataset "PigSampleOut".
4.	Kopieren Sie die Pig-Abfrage als Datei in den Azure-Blobspeicher, den Sie im obigen Schritt 2 konfiguriert haben. Wenn sich der verknüpfte Dienst zum Hosten der Daten vom Dienst zum Hosten dieser Abfragedatei unterscheidet, erstellen Sie einen separaten verknüpften Azure Storage-Dienst, und verweisen Sie in der Aktivitätskonfiguration darauf. Geben Sie mit **skriptPath** den Pfad der Pig-Abfragedatei und mit **scriptLinkedService** den Azure-Speicher mit der Skriptdatei an.
	
	> [AZURE.NOTE]Sie können das Pig-Skript auch inline in der Aktivitätsdefinition mit der **script**-Eigenschaft bereitstellen, aber dies wird nicht empfohlen, da alle Sonderzeichen im Skript innerhalb des JSON-Dokuments mit Escapezeichen versehen werden müssen, was möglicherweise zu Problemen beim Debuggen führen kann. Die bewährte Methode ist, Schritt 4 auszuführen.
5. Erstellen Sie die unten aufgeführte Pipeline mit der HDInsightHive-Aktivität, um die Daten zu verarbeiten.

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. Stellen Sie die Pipeline bereit. Weitere Informationen finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). 
7. Überwachen Sie die Pipeline mithilfe der Überwachungs- und Verwaltungsansichten von Data Factory. Weitere Informationen finden Sie im Artikel [Überwachen und Verwalten von Data Factory-Pipelines](data-factory-monitor-manage-pipelines.md).

## Angeben von Parametern für ein Pig-Skript mit dem defines-Element

Betrachten Sie das Beispiel, in dem die Spielprotokolle täglich im Azure-Blobspeicher erfasst und in einem mit Datum und Uhrzeit partitionierten Ordner gespeichert werden. Sie möchten das Pig-Skript parametrisieren, den Eingabeordnerpfad dynamisch während der Laufzeit übergeben und zudem die Ausgabe partitioniert mit Datum und Uhrzeit erzeugen.
 
Gehen Sie folgendermaßen vor, um das Pig-Skript zu parametrisieren

- Legen Sie die Parameter in **defines** fest.

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- Verweisen Sie im Pig-Skript mit "**$parameterName**" wie im folgenden Beispiel gezeigt auf die Parameter:

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 


 
## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-pig-activity.md).

<!---HONumber=September15_HO1-->