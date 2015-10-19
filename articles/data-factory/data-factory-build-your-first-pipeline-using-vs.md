<properties
	pageTitle="Erstellen der ersten Azure Data Factory-Pipeline mit Visual Studio"
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mithilfe von Visual Studio."
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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Erstellen der ersten Azure Data Factory-Pipeline mit Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Ihre erste Pipeline erstellen. Dieses Tutorial umfasst die folgenden Schritte:

2.	Erstellen verknüpfter Dienste (Datenspeicher, Compute-Instanzen)
3.	Erstellen eines Datensatzes
3.	Erstellen einer Pipeline
4.	Erstellen einer Data Factory und Bereitstellen von verknüpften Diensten, des Datensatzes und der Pipeline 

Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Eine ausführliche Übersicht über den Dienst finden Sie im Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).

> [AZURE.IMPORTANT]Lesen Sie sich den Artikel [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) durch, und führen Sie die vorbereitenden Schritte aus, bevor Sie mit diesem Tutorial beginnen.

## Exemplarische Vorgehensweise: Erstellen und Bereitstellen von Data Factory-Entitäten mit Visual Studio 

### Voraussetzungen

Sie müssen Folgendes auf Ihrem Computer installiert haben: – Visual Studio 2013 – Herunterladen von Azure SDK für Visual Studio 2013. Navigieren Sie zur [Azure-Downloadseite](http://azure.microsoft.com/downloads/), und klicken Sie im Abschnitt **.NET** auf die Option für die **VS 2013-Installation**. - Aktualisieren Sie die „Visual Studio Tools für Azure Data Factory“ auf die aktuelle Version, indem Sie auf Visual Studio 2013 --> Extras --> Updates --> Visual Studio Gallery“ zugreifen und für das Plug-In „Visual Studio Tools für Azure Data Factory“ auf „Aktualisieren“ klicken.


### Erstellen des Visual Studio-Projekts 
1. Starten Sie **Visual Studio 2013**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** sollte angezeigt werden.  
2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Leeres Data Factory-Projekt**. Wenn die Vorlage DataFactory nicht angezeigt wird, schließen Sie Visual Studio, installieren Sie Azure SDK für Visual Studio 2013, und öffnen Sie Visual Studio erneut.  

	![Dialogfeld "Neues Projekt"](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Füllen Sie für das Projekt die Felder **Name**, **Speicherort** und **Lösung** aus, und klicken Sie auf**OK**.

	![Projektmappen-Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Erstellen von verknüpften Diensten
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory und erstellen dann ein Dataset, das die Ausgabedaten der Hive-Verarbeitung darstellt.


#### Erstellen des mit Azure Storage verknüpften Diensts


4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.      
5. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Mit Azure-Speicher verknüpfter Dienst** aus der Liste aus, und klicken Sie auf **Hinzufügen**. 

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
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
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

4. Speichern Sie die Datei **HDInsightOnDemandLinkedService1.json**.
 
### Erstellen des Ausgabedatasets
Jetzt erstellen Sie das Ausgabedataset, das die im Azure- Blobspeicher gespeicherten Daten darstellt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Azure-Blob** in der Liste aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code im Editor durch folgenden Code: Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobOutput**, und geben Sie die Struktur der Daten an, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer namens **data** gespeichert werden und dass der Ordner **partitioneddata** heißt. Der Abschnitt **availability** gibt an, dass das Ausgabedataset monatlich erzeugt wird.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Speichern Sie die Datei **AzureBlobLocation1.json**.


### Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Pipelines**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Hive-Transformationspipeline** in der Liste aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code durch den folgenden Ausschnitt:

	> [AZURE.IMPORTANT]Ersetzen Sie **storageaccountname** durch den Namen Ihres Speicherkontos.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
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
	
	Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens **AzureStorageLinkedService1** angegeben ist) und in einem Container namens **script** gespeichert.

	Der Abschnitt **extendedProperties** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte (z. B. ${hiveconf:PartitionedData}) an das Hive-Skript übergeben werden.

	Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

	Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Compute-Instanz ausgeführt wird, die vom verknüpften Dienst **HDInsightOnDemandLinkedService** angegeben wurde.
3. Speichern Sie die Datei **HiveActivity1.json**.

### Hinzufügen von „partitionweblogs.hql“ als Abhängigkeit 

1. Klicken Sie mit der rechten Maustaste im Fenster **Projektmappen-Explorer** auf **Abhängigkeiten**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Vorhandenes Element**.  
2. Navigieren Sie zu **C:\\ADFGettingStarted**, wählen Sie die Datei **partitionweblogs.hql** aus, und klicken Sie auf **Hinzufügen**. 

Beim Veröffentlichen der Projektmappe im nächsten Schritt wird die HQL-Datei in den Skriptcontainer im Blobspeicher hochgeladen.

### Veröffentlichen/Bereitstellen der Data Factory-Entitäten

18. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**. 
19. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
20. Das folgende Dialogfeld sollte angezeigt werden:

	![Dialogfeld „Veröffentlichen“](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Führen Sie auf der Seite zum Konfigurieren der Data Factory folgende Schritte aus:
	1. Wählen Sie die Option **Neue Data Factory erstellen**.
	2. Geben Sie **FirstPipelineUsingVS** als **Name** ein.
	3. Wählen Sie im Feld **Abonnement** das richtige Abonnement aus. 
	4. Wählen Sie die **Ressourcengruppe** für die zu erstellende Data Factory aus. 
	5. Wählen Sie die **Region** für die Data Factory aus. 
	6. Klicken Sie auf **Weiter**, um zur Seite **Elemente veröffentlichen** zu wechseln. (Drücken Sie die **TAB-TASTE**, um das Feld „Name“ zu verlassen, wenn die Schaltfläche **Weiter** deaktiviert ist.) 
23. Stellen Sie auf der Seite **Elemente veröffentlichen** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.     
24. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
25. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist. 
 

## Verwenden Sie Server-Explorer, um Data Factory-Entitäten zu überprüfen

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht** und dann auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster erst die Option **Azure** und dann **Data Factory**. Wenn **Anmelden bei Visual Studio** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen über alle Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt.

	![Server-Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Durch Klicken mit der rechten Maustaste auf eine Data Factory und Auswählen von **Data Factory in neues Projekt exportieren** können Sie anhand einer vorhandenen Data Factory ein Visual Studio-Projekt erstellen.

	![Data Factory exportieren](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Aktualisieren von Data Factory-Tools für Visual Studio

Um die Azure Data Factory-Tools für Visual Studio zu aktualisieren, führen Sie folgende Schritte aus:

1. Klicken Sie im Menü auf **Tools**, und wählen Sie**Erweiterungen und Updates** aus.
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
3. Wählen Sie **Azure Data Factory-Tools für Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools. 

Unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md) finden Sie eine Anleitung zum Überwachen der in dieser Anleitung erstellten Pipeline und Datasets über das Azure-Vorschauportal.
 

## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zur Verwendung einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Lernprogramm: Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-get-started.md).
  
## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=Oct15_HO2-->