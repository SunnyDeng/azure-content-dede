<properties
	pageTitle="Erstellen der ersten Pipeline mit Azure Data Factory"
	description="Dieses Tutorial zeigt, wie Sie eine Beispieldatenpipeline erstellen, die Daten mithilfe von Azure HDInsight und Visual Studio transformiert."
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
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>


# Erstellen der ersten Pipeline mit Azure Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Ihre erste Pipeline erstellen. Dieses Tutorial umfasst die folgenden Schritte:

1.	Erstellen der Data Factory
2.	Erstellen der verknüpften Dienste (Datenspeicher, Compute-Instanzen) und Datasets
3.	Erstellen der Pipeline

Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Eine ausführliche Übersicht über den Dienst finden Sie im Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).

## Schritt 1: Erstellen der Data Factory

1.	Nach der Anmeldung beim [Azure-Vorschauportal](http://portal.azure.com/) gehen Sie wie folgt vor:
	1.	Klicken Sie links unten auf **NEU**. 
	2.	Klicken Sie auf dem Blatt **Erstellen** auf **Datenanalyse**.
	3.	Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.

		![Blatt "Erstellen"](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	Geben Sie auf dem Blatt **Neue Data Factory** unter "Name" **DataFactoryMyFirstPipeline** ein.

	![Blatt "Neue Data Factory"](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factory-Namen sind global eindeutig. Sie müssen dem Namen der Data Factory Ihren Namen voranstellen, um die erfolgreiche Erstellung der Factory zu aktivieren. 
3.	Wenn Sie noch keine Ressourcengruppe erstellt haben, müssen Sie eine Ressourcengruppe erstellen. Gehen Sie dazu folgendermaßen vor:
	1.	Klicken Sie auf **RESSOURCENGRUPPENNAME**.
	2.	Wählen Sie auf dem Blatt **Ressourcengruppe** die Option **Neue Ressourcengruppe erstellen** aus.
	3.	Geben Sie auf dem Blatt **Ressourcengruppe erstellen** den Namen **ADF** für **Name** ein.
	4.	Klicken Sie auf **OK**.
	
		![Ressourcengruppe erstellen](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	Nachdem Sie die Ressourcengruppe ausgewählt haben, stellen Sie sicher, dass Sie das richtige Abonnement verwenden, in dem die Data Factory erstellt werden soll.
5.	Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.
6.	Sie sehen die Data Factory, die im **Startmenü** des Azure-Vorschauportals erstellt wurde, wie folgt:   

	![Erstellen des Data Factory-Status](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. Glückwunsch! Sie haben erfolgreich Ihre erste Data Factory erstellt. Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie die Data Factory-Seite mit dem Inhalt der Data Factory. 	

	![Blatt "Data Factory"](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

In den folgenden Schritten erfahren Sie, wie Sie die verknüpften Dienste, Datasets und Pipeline erstellen, die Sie in diesem Tutorial verwenden.

## Exemplarische Vorgehensweise: Erstellen und Bereitstellen von Data Factory-Entitäten mit Visual Studio 

### Voraussetzungen

Sie müssen Folgendes auf Ihrem Computer installiert haben: – Visual Studio 2013 – Herunterladen von Azure SDK für Visual Studio 2013. Navigieren Sie zur [Azure-Downloadseite](http://azure.microsoft.com/downloads/), und klicken Sie im Abschnitt **.NET** auf **VS 2013 installieren**.


### Erstellen des Visual Studio-Projekts 
1. Starten Sie **Visual Studio 2013**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** wird angezeigt.  
2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Leeres Data Factory-Projekt**. Wenn die Vorlage DataFactory nicht angezeigt wird, schließen Sie Visual Studio, installieren Sie Azure SDK für Visual Studio 2013, und öffnen Sie Visual Studio erneut.  

	![Dialogfeld "Neues Projekt"](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Füllen Sie für das Projekt die Felder **Name**, **Speicherort** und **Lösung** aus, und klicken Sie auf **OK**.

	![Projektmappen-Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Erstellen von verknüpften Diensten
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory und erstellen dann ein Dataset, das die Ausgabedaten der Hive-Verarbeitung darstellt.


#### Erstellen des mit Azure Storage verknüpften Diensts


4. Klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.      
5. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Mit Azure Storage verknüpfter Dienst** aus der Liste aus, und klicken Sie auf **Hinzufügen**. 

	![Neuer verknüpfter Dienst](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Ersetzen Sie **accountname** und **accountkey** durch den Namen Ihres Azure-Speicherkontos und den entsprechenden Schlüssel.

	![Mit Azure-Speicher verknüpfter Dienst](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Speichern Sie die Datei **AzureStorageLinkedService1.json**.

#### Erstellen des mit Azure-HDInsight verknüpften Diensts
Sie erstellen nun einen verknüpften Dienst für einen bedarfsgesteuerten HDInsight-Cluster, der zum Ausführen des Hive-Skripts verwendet wird.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
2. Wählen Sie **Bedarfsgesteuerter verknüpfter HDInsight-Dienst**, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code durch den folgenden Code:

		{
		    "name": "HDInsightOnDemandLinkedService",
		    "properties": {
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
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

4. Speichern Sie die Datei **HDInsightOnDemandLinkedService1.json**.
 
### Erstellen des Ausgabedatasets
Jetzt erstellen Sie das Ausgabedataset, das die im Azure- Blobspeicher gespeicherten Daten darstellt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Azure-Blob** in der Liste aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code im Editor durch folgenden Code: Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobOutput**, und geben Sie die Struktur der Daten an, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer namens **data** gespeichert werden, und dass der Ordner **partitioneddata** heißt. Der Abschnitt **availability** gibt an, dass das Ausgabedataset monatlich erzeugt wird.
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        }
		    }
		}

4. Speichern Sie die Datei **AzureBlobLocation1.json**.


### Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Pipelines**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Hive-Transformationspipeline** in der Liste aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code durch den folgenden Codeausschnitt und **storageaccountname** durch den Namen Ihres Speicherkontos.

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
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
3. Speichern Sie die Datei **HiveActivity1.json**. 

### Veröffentlichen/Bereitstellen der Data Factory-Entitäten
  
1. Wählen Sie im Symbolleistenbereich durch Klicken mit der rechten Maustaste die Option **Data Factory** aus, um die Symbolleiste "Data Factory" zu aktivieren, falls diese noch nicht aktiviert ist. 
19. Klicken Sie in der Symbolleiste **Data Factory** auf das **Dropdownlistenfeld**, um alle Data Factorys in Ihrem Azure-Abonnement anzuzeigen. Wenn das Dialogfeld **Anmelden bei Visual Studio** angezeigt wird: 
	20. Geben Sie das **E-Mail-Konto** und das **Kennwort** für das Azure-Abonnement ein, in dem die Data Factory erstellt werden soll, und klicken Sie auf **Anmelden**.
	21. Nachdem die Anmeldung abgeschlossen wurde, sollten alle Data Factorys des betreffenden Azure-Abonnements angezeigt werden. In diesem Lernprogramm erstellen Sie eine neue Data Factory.       
22. Wählen Sie in der Dropdownliste die Option **DataFactoryMyFirstPipeline** aus, und klicken Sie auf die Schaltfläche **Veröffentlichen**, um die verknüpften Dienste, Datasets und Pipeline bereitzustellen bzw. zu veröffentlichen.    

	![Schaltfläche "Veröffentlichen"](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. Der Status der Veröffentlichung sollte im oben gezeigten Fenster **Data Factory-Aufgabenliste** angezeigt werden. Vergewissern Sie sich, dass die Veröffentlichung erfolgreich war.


## Verwenden Sie Server-Explorer, um Data Factory-Entitäten zu überprüfen

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht**, und klicken Sie auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster die Option **Azure**, und erweitern Sie dann **Data Factory**. Wenn **Anmelden bei Visual Studio** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen über alle Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt.

	![Server-Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Durch Klicken mit der rechten Maustaste auf eine Data Factory und Auswählen von **Data Factory in neues Projekt exportieren** können Sie anhand einer vorhandenen Data Factory ein Visual Studio-Projekt erstellen.

	![Data Factory exportieren](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Aktualisieren von Data Factory-Tools für Visual Studio

Um die Azure Data Factory-Tools für Visual Studio zu aktualisieren, führen Sie folgende Schritte aus:

1. Klicken Sie im Menü auf **Tools**, und wählen Sie **Erweiterungen und Updates** aus.
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
3. Wählen Sie **Azure Data Factory-Tools für Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools. 

Unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md) finden Sie eine Anleitung zum Überwachen der in diesem Tutorial erstellten Pipeline und Datasets über das Azure-Vorschauportal.
 

## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Tutorial: Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-get-started.md).
  

<!---HONumber=August15_HO6-->