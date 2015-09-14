<properties
	pageTitle="Erstellen der ersten Azure Data Factory-Pipeline mit dem Data Factory-Editor"
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mit dem Data Factory-Editor im Azure-Portal."
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
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Erstellen der ersten Azure Data Factory-Pipeline mit dem Data Factory-Editor (Azure-Portal)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe des [Azure-Vorschauportals](https://portal.azure.com/) Ihre erste Pipeline erstellen. Dieses Tutorial umfasst die folgenden Schritte:

1.	Erstellen der Data Factory
2.	Erstellen der verknüpften Dienste (Datenspeicher, Compute-Instanzen) und Datasets
3.	Erstellen der Pipeline

Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Eine ausführliche Übersicht über den Dienst finden Sie im Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).

## Schritt 1: Erstellen der Data Factory

1.	Nach der Anmeldung beim [Azure-Vorschauportal](http://portal.azure.com/) gehen Sie wie folgt vor:
	1.	Klicken Sie im linken Menü auf **NEU**. 
	2.	Klicken Sie auf dem Blatt **Erstellen** auf **Datenanalyse**.
	3.	Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.

		![Blatt "Erstellen"](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Geben Sie auf dem Blatt **Neue Data Factory** unter "Name" **DataFactoryMyFirstPipeline** ein.

	![Blatt "Neue Data Factory"](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factory-Namen sind global eindeutig. Sie müssen dem Namen der Data Factory Ihren Namen voranstellen, um die erfolgreiche Erstellung der Factory zu aktivieren. 
3.	Wenn Sie noch keine Ressourcengruppe erstellt haben, müssen Sie eine Ressourcengruppe erstellen. Gehen Sie dazu folgendermaßen vor:
	1.	Klicken Sie auf **RESSOURCENGRUPPENNAME**.
	2.	Wählen Sie auf dem Blatt **Ressourcengruppe** die Option **Neue Ressourcengruppe erstellen** aus.
	3.	Geben Sie auf dem Blatt **Ressourcengruppe erstellen** den Namen **ADF** für **Name** ein.
	4.	Klicken Sie auf **OK**.
	
		![Ressourcengruppe erstellen](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	Nachdem Sie die Ressourcengruppe ausgewählt haben, stellen Sie sicher, dass Sie das richtige Abonnement verwenden, in dem die Data Factory erstellt werden soll.
5.	Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.
6.	Sie sehen die Data Factory, die im **Startmenü** des Azure-Vorschauportals erstellt wurde, wie folgt:   

	![Erstellen des Data Factory-Status](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Glückwunsch! Sie haben erfolgreich Ihre erste Data Factory erstellt. Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie die Data Factory-Seite mit dem Inhalt der Data Factory. 	

	![Blatt "Data Factory"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

In den folgenden Schritten erfahren Sie, wie Sie die verknüpften Dienste, Datasets und Pipeline erstellen, die Sie in diesem Tutorial verwenden.

## Schritt 2: Erstellen von verknüpften Diensten und Datasets
In diesem Schritt verknüpfen Sie Ihr Azure-Speicherkonto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory und erstellen dann ein Dataset, das die Ausgabedaten der Hive-Verarbeitung darstellt.

### Erstellen des mit Azure Storage verknüpften Diensts
1.	Klicken Sie auf dem Blatt **DATA FACTORY** für **DataFactoryFirstPipeline** auf **Erstellen und bereitstellen**. Der Data Factory-Editor wird gestartet. 
	 
	![Kachel "Erstellen und bereitstellen"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Klicken Sie auf **Neuer Datenspeicher**, und wählen Sie **Azure-Speicher**.
	
	![Mit Azure Storage verknüpfter Dienst](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Das JSON-Skript zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im Editor angezeigt werden. 
4. Ersetzen Sie **Kontoname** durch den Namen Ihres Azure-Speicherkontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](../storage/storage-create-storage-account.md/#view-copy-and-regenerate-storage-access-keys).
5. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

	![Schaltfläche "Bereitstellen"](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Erstellen des mit Azure-HDInsight verknüpften Diensts
Sie erstellen nun einen verknüpften Dienst für einen bedarfsgesteuerten HDInsight-Cluster, der zum Ausführen des Hive-Skripts verwendet wird.

1. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neue Compute-Instanz**, und wählen Sie im Menü **Bedarfsgesteuerter HDInsight-Cluster** aus.

	![Neue Compute-Instanz](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in das Fenster "Draft-1" ein. Der JSON-Codeausschnitt beschreibt die Eigenschaften, die zum Erstellen des bedarfsgesteuerten HDInsight-Clusters verwendet werden. 

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
3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen. 
4. Vergewissern Sie sich, dass in der Strukturansicht auf der linken Seite "StorageLinkedService" und "HDInsightOnDemandLinkedService" angezeigt werden.

	![Strukturansicht mit verknüpften Diensten](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Erstellen des Ausgabedatasets
Jetzt erstellen Sie das Ausgabedataset, das die im Azure- Blobspeicher gespeicherten Daten darstellt.

1. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neues Dataset** und dann auf **Azure-Blobspeicher**.  

	![Neues Dataset](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in das Fenster "Draft-1" ein. Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobOutput**, und geben Sie die Struktur der Daten an, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer namens **data** gespeichert werden, und dass der Ordner **partitioneddata** heißt. Der Abschnitt **availability** gibt an, dass das Ausgabedataset monatlich erzeugt wird.
	
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

3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das neu erstellte Dataset bereitzustellen.
4. Überprüfen Sie, ob das Dataset erfolgreich erstellt wurde.

	![Strukturansicht mit verknüpften Diensten](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Schritt 3: Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline.

1. Klicken Sie im **Data Factory-Editor** auf **\(...\)** und dann auf **Neue Pipeline**.
	
	![Schaltfläche "Neue Pipeline"](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in das Fenster "Draft-1" ein.

	> [AZURE.IMPORTANT] Ersetzen Sie im JSON-Code **storageaccountname** durch den Namen Ihres Speicherkontos.

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
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
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
	
	Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das neben "scriptLinkedService" als **StorageLinkedService** angegeben ist) und in einem Container namens **script** gespeichert.

	Der Abschnitt **extendedProperties** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte (z. B. ${hiveconf:PartitionedData}) an das Hive-Skript übergeben werden.

	Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

	Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Compute-Instanz ausgeführt wird, die vom verknüpften Dienst **HDInsightOnDemandLinkedService** angegeben wurde.
3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.
4. Vergewissern Sie sich, dass die Pipeline in der Strukturansicht angezeigt wird.

	![Strukturansicht mit Pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Glückwunsch, Sie haben Ihre erste Pipeline erfolgreich erstellt!
6. Klicken Sie auf **X**, um die Data Factory-Editor-Blätter zu schließen. Navigieren Sie zurück zum Blatt "Data Factory", und klicken Sie auf **Diagramm**.
  
	![Kachel "Diagramm"](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. In der Diagrammansicht sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.
	
	![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Doppelklicken Sie in der Diagrammansicht auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Wenn die Verarbeitung abgeschlossen ist, wird der Slice mit dem Zustand **Bereit** angezeigt. Beachten Sie, dass die bedarfsgesteuerte Erstellung eines HDInsight-Clusters in der Regel einige Zeit dauert. 

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Sobald der Slice den Status **Bereit** hat, überprüfen Sie den Ordner **partitioneddata** im Container **data** in Ihrem Blobspeicher auf die Ausgabedaten.  
 

 

## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Tutorial: Kopieren von Daten aus einem Azure-Blob in Azure SQL](./data-factory-get-started.md).
  

## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-editor.md).

<!---HONumber=September15_HO1-->