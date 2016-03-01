<properties
	pageTitle="Erste Schritte mit Azure Data Factory (Visual Studio)"
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
	ms.topic="hero-article" 
	ms.date="02/16/2016"
	ms.author="spelluru"/>

# Erste Schritte mit Azure Data Factory (Visual Studio)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe von Microsoft Visual Studio Ihre erste Azure Data Factory erstellen.

## Voraussetzungen

1. Sie **müssen** den Artikel [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) lesen und die erforderlichen Schritte abschließen, bevor Sie fortfahren.
2. Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Sie sollten den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md) lesen, um eine ausführliche Übersicht über den Dienst zu erhalten.  

## Exemplarische Vorgehensweise: Erstellen und Bereitstellen von Data Factory-Entitäten mit Visual Studio 

### Voraussetzungen

Folgendes muss auf Ihrem Computer installiert sein:

- Visual Studio 2013 oder Visual Studio 2015
- Laden Sie das Azure-SDK für Visual Studio 2013 oder Visual Studio 2015 herunter. Navigieren Sie zur [Azure-Downloadseite](https://azure.microsoft.com/downloads/), und klicken Sie auf **VS 2013** oder**VS 2015** im Abschnitt **.NET**.
- Laden Sie das neueste Azure Data Factory-Plug-In für Visual Studio herunter: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) oder [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Bei Verwendung von Visual Studio 2013 können Sie das Plug-In auch wie folgt aktualisieren: Klicken Sie im Menü auf **Tools** -> **Erweiterungen und Updates** -> **Online** -> **Visual Studio-Katalog** -> **Microsoft Azure Data Factory-Tools für Visual Studio** -> **Aktualisieren**. 
	
	

### Erstellen des Visual Studio-Projekts 
1. Starten Sie **Visual Studio 2013** oder **Visual Studio 2015**. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**. Das Dialogfeld **Neues Projekt** sollte angezeigt werden.  
2. Wählen Sie im Dialogfeld **Neues Projekt** die Vorlage **DataFactory** aus, und klicken Sie auf **Leeres Data Factory-Projekt**.   

	![Dialogfeld "Neues Projekt"](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Füllen Sie für das Projekt die Felder **Name**, **Speicherort** und **Lösung** aus, und klicken Sie auf**OK**.

	![Projektmappen-Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Erstellen von verknüpften Diensten
Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. Sie werden den Namen und die Einstellungen für die Data Factory später angeben, wenn Sie Ihre Data Factory-Lösung veröffentlichen.

In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um das in der Aktivität der Pipeline in diesem Beispiel angegebene Hive-Skript auszuführen. Sie müssen identifizieren, welche Datenspeicher-/Computedienste in Ihrem Szenario verwendet werden, und diese Dienste mit der Data Factory verknüpfen, indem Sie verknüpfte Dienste erstellen.

#### Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. Im Rahmen dieses Tutorials verwenden Sie das gleiche Azure Storage-Konto, um  
Ein-/Ausgabedaten und die HQL-Skriptdatei zu speichern.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.      
5. Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Option **Mit Azure-Speicher verknüpfter Dienst** aus der Liste aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie **accountname** und **accountkey** durch den Namen Ihres Azure-Speicherkontos bzw. den entsprechenden Schlüssel. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	![Mit Azure-Speicher verknüpfter Dienst](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Speichern Sie die Datei **AzureStorageLinkedService1.json**.

#### Erstellen des mit Azure-HDInsight verknüpften Diensts
In diesem Schritt verknüpfen Sie einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Der HDInsight-Cluster wird automatisch zur Laufzeit erstellt und gelöscht, nachdem die Verarbeitung abgeschlossen und die angegebene Leerlaufzeit verstrichen ist. Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie Ihren eigenen HDInsight-Cluster verwenden. Weitere Informationen finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verknüpfte Dienste**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**.
2. Wählen Sie **Bedarfsgesteuerter verknüpfter HDInsight-Dienst**, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code durch den folgenden Code:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
		  }
		}
	
	Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:
	
	Eigenschaft | Beschreibung
	-------- | -----------
	Version | Gibt die Version des zu erstellenden HDInsight-Clusters als 3.2 an. 
	ClusterSize | Dient zum Erstellen eines HDInsight-Clusters mit einem Knoten. 
	TimeToLive | Gibt die Leerlaufzeit des HDInsight-Clusters an, bevor er gelöscht wird.
	linkedServiceName | Gibt das Speicherkonto an, das verwendet wird, um die Protokolle zu speichern, die von HDInsight generiert werden.

	Beachten Sie Folgendes:
	
	- Die Data Factory erstellt mit dem obigen JSON-Code einen **Windows-basierten** HDInsight-Cluster für Sie. Sie können sich auch für die Erstellung eines **Linux-basierten** HDInsight-Clusters entscheiden. Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
	- Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie **Ihren eigenen HDInsight-Cluster** verwenden. Ausführliche Informationen finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
	- Der HDInsight-Cluster erstellt einen **Standardcontainer** in der Blob Storage-Instaz, die Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dies ist beabsichtigt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss – es sei denn, ein aktiver Cluster (**timeToLive**) ist vorhanden und wird gelöscht, nachdem die Verarbeitung abgeschlossen ist.
	
		Wenn immer mehr Slices verarbeitet werden, enthält Azure Blob Storage viele Container. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Der Name dieser Container basiert auf dem folgenden Muster: adf**ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel. Verwenden Sie Tools wie [Microsoft Storage-Explorer](http://storageexplorer.com/), um Container in Ihrer Azure Blob Storage-Instanz zu löschen.

	Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
4. Speichern Sie die Datei **HDInsightOnDemandLinkedService1.json**.

### Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten für die Hive-Verarbeitung darzustellen. Diese Datasets verweisen auf den **AzureStorageLinkedService1**, den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst weist auf ein Azure Storage-Konto, und Datasets geben Container, Ordner und Dateiname in dem Speicher an, der Eingabe- und Ausgabedaten enthält.

#### Erstellen des Eingabedatasets

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Tabellen**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Azure-Blob** in der Liste aus, ändern Sie den Namen der Datei in **OutputDataset.json**, und klicken Sie auf **Hinzufügen**.
3. Ersetzen Sie das **JSON**-Skript im Editor durch folgenden Text: 

	Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobInput**, das Eingabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Eingabedaten im Blobcontainer **adfgetstarted** und im Ordner **inputdata** gespeichert werden.
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService1",
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

	Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

	| Eigenschaft | Beschreibung |
	| :------- | :---------- |
	| Typ | Die Type-Eigenschaft wird auf „AzureBlob“ festgelegt, da sich Daten im Azure-Blobspeicher befinden. |  
	| linkedServiceName | verweist auf den „AzureStorageLinkedService1“, den Sie zuvor erstellt haben. |
	| fileName | Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Fall wird nur „input.log“ verarbeitet. |
	| Typ | Die Protokolldateien liegen im Textformat vor, daher wird „TextFormat“ verwendet. | 
	| columnDelimiter | Spalten in den Protokolldateien werden mit „,“ (Komma) getrennt. |
	| frequency/interval | „frequency“ wird auf „Month“ und „interval“ auf „1“ festgelegt, was bedeutet, dass die Eingabeslices monatlich verfügbar sind. | 
	| external | Diese Eigenschaft wird auf „true“ festgelegt, wenn die Eingabedaten nicht vom Data Factory-Dienst generiert werden. | 
	  
	
3. Speichern Sie die Datei **InputDataset.json**.

 
#### Erstellen des Ausgabedatasets
Nun erstellen Sie das Ausgabedataset, das die im Azure-Blobspeicher gespeicherten Ausgabedaten darstellt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Tabellen**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Azure-Blob** in der Liste aus, ändern Sie den Namen der Datei in **OutputDataset.json**, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie das **JSON**-Skript im Editor durch folgenden Text: 

	Im JSON-Codeausschnitt erstellen Sie ein DataSet mit dem Namen **AzureBlobOutput**, und geben Sie die Struktur der Daten an, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer **adfgetstarted** und im Ordner **partitioneddata** gespeichert werden. Der Abschnitt **availability** gibt an, dass das Ausgabe-DataSet monatlich erzeugt wird.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

	Im Abschnitt **Erstellen des Eingabedatasets** werden diese Eigenschaften beschrieben. Sie legen die Eigenschaft „external“ nicht für ein Ausgabedataset fest, da das Dataset vom Data Factory-Dienst erstellt wird.

4. Speichern Sie die Datei **OutputDataset.json**.


### Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline mit einer **HDInsightHive**-Aktivität. Beachten Sie, dass der Eingabeslice monatlich verfügbar ist („frequency“: „Month“, „interval“: „1“), der Ausgabeslice monatlich erstellt wird und die scheduler-Eigenschaft für die Aktivität auch auf monatlich (siehe unten) festgelegt ist. Die Einstellungen für das Ausgabedataset und den Aktivitätsplaner müssen übereinstimmen. Zu diesem Zeitpunkt steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe erzeugt. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. Am Ende dieses Abschnitts werden die in der folgenden JSON verwendeten Eigenschaften erläutert.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Pipelines**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neues Element**. 
2. Wählen Sie **Hive-Transformationspipeline** in der Liste aus, und klicken Sie auf **Hinzufügen**. 
3. Ersetzen Sie den **JSON**-Code durch den folgenden Ausschnitt:

	> [AZURE.IMPORTANT] Ersetzen Sie **storageaccountname** durch den Namen Ihres Speicherkontos.

		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService1",
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
	
	Im JSON-Codeausschnitt erstellen Sie eine Pipeline, die aus einer einzelnen Aktivität besteht. Diese nutzt Hive, um Daten in einem HDInsight-Cluster Daten zu verarbeiten.
	
	Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens **AzureStorageLinkedService1** angegeben ist) und im Ordner **script** im Container **adfgetstarted** gespeichert.

	Der Abschnitt **defines** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte (z. B. „${hiveconf:inputtable}“, „${hiveconf:partitionedtable}“) an das Hive-Skript übergeben werden.

	Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

	Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Computeinstanz ausgeführt wird, die vom **linkedServiceName** – **HDInsightOnDemandLinkedService** angegeben wurde.

3. Speichern Sie die Datei **HiveActivity1.json**.

### Hinzufügen von „partitionweblogs.hql“ und „input.log“ als Abhängigkeit 

1. Klicken Sie mit der rechten Maustaste im Fenster **Projektmappen-Explorer** auf **Abhängigkeiten**, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Vorhandenes Element**.  
2. Navigieren Sie zu **C:\\ADFGettingStarted**, wählen Sie die Dateien **partitionweblogs.hql** und **input.log** aus, und klicken Sie auf **Hinzufügen**. Sie hätten diese beiden Dateien als Teil der erforderlichen Komponenten aus der [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) erstellt.

Beim Veröffentlichen der Lösung im nächsten Schritt wird die Datei **partitionweblogs.hql** in den Skriptordner **adfgetstarted** im Blobcontainer hochgeladen.

### Veröffentlichen/Bereitstellen der Data Factory-Entitäten

18. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**. 
19. Wenn das Dialogfeld **Melden Sie sich bei Ihrem Microsoft-Konto an** angezeigt wird, geben Sie Ihre Anmeldeinformationen für das Konto mit dem Azure-Abonnement ein, und klicken Sie auf **Anmelden**.
20. Das folgende Dialogfeld sollte angezeigt werden:

	![Dialogfeld „Veröffentlichen“](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Führen Sie auf der Seite zum Konfigurieren der Data Factory folgende Schritte aus:
	1. Wählen Sie die Option **Neue Data Factory erstellen**.
	2. Geben Sie **FirstDataFactoryUsingVS** für **Name** ein. 
	
		> [AZURE.IMPORTANT] Der Name der Azure Data Factory muss global eindeutig sein. Wenn die Fehlermeldung **Data Factory-Name „FirstDataFactoryUsingVS“ nicht verfügbar** beim Veröffentlichen angezeigt wird, ändern Sie den Namen (z. B. in „IhrNameFirstDataFactoryUsingVS“). Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
		> 
		> Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
	3. Wählen Sie im Feld **Abonnement** das richtige Abonnement aus. 
	4. Wählen Sie die **Ressourcengruppe** für die zu erstellende Data Factory aus. 
	5. Wählen Sie die **Region** für die Data Factory aus. 
	6. Klicken Sie auf **Weiter**, um zur Seite **Publish Items** zu wechseln. (Drücken Sie die **TAB-TASTE**, um das Feld „Name“ zu verlassen, wenn die Schaltfläche **Weiter** deaktiviert ist.) 
23. Stellen Sie auf der Seite **Publish Items** sicher, dass alle Data Factory-Entitäten ausgewählt sind, und klicken Sie auf **Weiter**, um zur Seite **Zusammenfassung** zu wechseln.     
24. Prüfen Sie die Zusammenfassung, und klicken Sie auf **Weiter**, um den Bereitstellungsprozess zu starten und den **Bereitstellungsstatus** anzuzeigen.
25. Auf der Seite **Bereitstellungsstatus** sollte der Status des Bereitstellungsprozesses angezeigt werden. Klicken Sie auf „Fertig stellen“, nachdem die Bereitstellung abgeschlossen ist. 
 
## Schritt 4: Überwachen der Pipeline

6. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und gehen Sie wie folgt vor:
	1. Klicken Sie auf **Durchsuchen**, und wählen Sie **Data Factorys** aus. ![Durchsuchen von Data Factorys](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
	2. Wählen Sie **FirstDataFactoryUsingVS** in der Liste der Data Factorys aus. 
7. Klicken Sie auf der Startseite Ihrer Data Factory auf **Diagramm**.
  
	![Kachel "Diagramm"](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. In der Diagrammansicht sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.
	
	![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Um alle Aktivitäten in der Pipeline anzuzeigen, klicken Sie mit der rechten Maustaste auf die Pipeline im Diagramm, und klicken Sie auf „Pipeline öffnen“. 

	![Menü "Pipeline öffnen"](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Vergewissern Sie sich, dass Sie die HDInsightHive-Aktivität in der Pipeline sehen. 
  
	![Ansicht „Pipeline öffnen“](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

	Um zur vorherigen Ansicht zurück zu navigieren, klicken Sie oben in der Breadcrumb-Leiste auf **Data Factory**. 
10. Doppelklicken Sie in der **Diagrammansicht** auf das Dataset **AzureBlobInput**. Vergewissern Sie sich, dass der Slice sich im Zustand **Bereit** befindet. Es kann ein paar Minuten dauern, bis der Slice im Status „Bereit“ angezeigt wird. Wenn dies nicht geschieht, nachdem Sie einige Zeit gewartet haben, prüfen Sie, ob Sie die Eingabedatei („input.log“) im richtigen Container („adfgetstarted“) und Ordner („inputdata“) platziert haben.

	![Eingabeslice im Status „Bereit“](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Klicken Sie auf **X**, um das Blatt **AzureBlobInput** zu schließen. 
12. Doppelklicken Sie in der **Diagrammansicht** auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Wenn die Verarbeitung abgeschlossen ist, wird der Slice mit dem Zustand **Bereit** angezeigt.

	>[AZURE.IMPORTANT]Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel einige Zeit (etwa 20 Minuten).  

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)
	
10. Sobald der Slice den Status **Bereit** hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.
 
	![Ausgabedaten](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

Unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md) finden Sie eine Anleitung zum Überwachen der in diesem Tutorial erstellten Pipeline und Datasets über das Azure-Portal.

## Verwenden Sie Server-Explorer, um Data Factory-Entitäten zu überprüfen

1. Klicken Sie in **Visual Studio** im Menü auf **Ansicht** und dann auf **Server-Explorer**.
2. Erweitern Sie im Server-Explorer-Fenster erst die Option **Azure** und dann **Data Factory**. Wenn **Anmelden bei Visual Studio** angezeigt wird, geben Sie das mit Ihrem Azure-Abonnement verknüpfte **Konto** ein, und klicken Sie auf **Weiter**. Geben Sie Ihr **Kennwort** ein, und klicken Sie auf **Anmelden**. Visual Studio versucht, Informationen über alle Azure Data Factorys abzurufen, die in Ihrem Abonnement enthalten sind. Der Status dieses Vorgangs wird im Fenster **Data Factory-Aufgabenliste** angezeigt.

	![Server-Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Durch Klicken mit der rechten Maustaste auf eine Data Factory und Auswählen von **Export Data Factory to New Project** können Sie anhand einer vorhandenen Data Factory ein Visual Studio-Projekt erstellen.

	![Data Factory exportieren](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Aktualisieren von Data Factory-Tools für Visual Studio

Um die Azure Data Factory-Tools für Visual Studio zu aktualisieren, führen Sie folgende Schritte aus:

1. Klicken Sie im Menü auf **Extras**, und wählen Sie**Erweiterungen und Updates** aus.
2. Wählen Sie im linken Bereich **Updates** und dann **Visual Studio Gallery** aus.
3. Wählen Sie **Azure Data Factory tools for Visual Studio** aus, und klicken Sie auf **Update**. Wenn dieser Eintrag nicht angezeigt wird, verfügen Sie bereits über die neueste Version der Tools. 

## Verwenden von Konfigurationsdateien
Sie können Konfigurationsdateien in Visual Studio verwenden, um Eigenschaften für verknüpfte Dienste/Tabellen/Pipelines für jede Umgebung unterschiedlich zu konfigurieren.

Sehen Sie sich die folgende JSON-Definition für einen verknüpften Azure Storage-Dienst an. Sie wird zum Angeben von **connectionString** mit unterschiedlichen Werten für accountname und accountkey basierend auf der Umgebung (Entwicklung/Test/Produktion) verwendet, in der Sie Data Factory-Entitäten bereitstellen. Dies erreichen Sie, indem Sie für jede Umgebung eine separate Konfigurationsdatei nutzen.

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

### Hinzufügen einer Konfigurationsdatei
Führen Sie die folgenden Schritte aus, um eine Konfigurationsdatei für jede Umgebung hinzuzufügen:

1. Klicken Sie mit der rechten Maustaste auf das Data Factory-Projekt in Ihrer Visual Studio-Projektmappe, zeigen Sie auf **Hinzufügen**, und klicken Sie auf **Neuer Eintrag**.
2. Wählen Sie links in der Liste mit den installierten Vorlagen die Option **Config**, klicken Sie auf **Konfigurationsdatei**, geben Sie einen **Namen** für die Konfigurationsdatei ein, und klicken Sie auf **Hinzufügen**.

	![Konfigurationsdatei hinzufügen](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Fügen Sie Konfigurationsparameter und deren Werte im unten angegebenen Format hinzu:

		{
		    "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
		    "AzureStorageLinkedService1": [
		        {
		            "name": "$.properties.typeProperties.connectionString",
		            "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    ],
		    "AzureSqlLinkedService1": [
		        {
		            "name": "$.properties.typeProperties.connectionString",
		            "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		        }
		    ]
		}

	In diesem Beispiel wird die connectionString-Eigenschaft eines verknüpften Azure Storage-Diensts und eines verknüpften Azure SQL-Diensts konfiguriert. Beachten Sie, dass die Syntax zum Angeben des Namens [JsonPath](http://goessner.net/articles/JsonPath/) lautet.

	Falls JSON wie unten gezeigt über eine Eigenschaft mit einem Array von Werten verfügt:

		"structure": [
	  		{
	  			"name": "FirstName",
	    		"type": "String"
	  		},
	  		{
	    		"name": "LastName",
	    	    "type": "String"
			}
		],
	
	In diesem Fall müssen Sie dies in der Konfigurationsdatei wie folgt konfigurieren (nullbasierte Indizierung verwenden):
		
		{
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### Eigenschaftennamen mit Leerzeichen
Wenn ein Eigenschaftenname ein Leerzeichen enthält, verwenden Sie eckige Klammern wie im folgenden Beispiel (Datenbankservername):

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### Bereitstellen der Projektmappe mit einer Konfiguration
Wenn Sie Azure Data Factory-Entitäten in VS veröffentlichen, können Sie die Konfiguration angeben, die Sie für diesen Veröffentlichungsvorgang verwenden möchten.

Gehen Sie wie folgt vor, um Entitäten in einem Azure Data Factory-Projekt mit der Konfigurationsdatei zu veröffentlichen:

1. Klicken Sie mit der rechten Maustaste auf das Data Factory-Projekt, und klicken Sie dann auf **Veröffentlichen**, um das Dialogfeld **Elemente veröffentlichen** anzuzeigen. 
2. Wählen Sie eine vorhandene Data Factory aus, oder geben Sie Werte zum Erstellen einer neuen Data Factory auf der Seite **Data Factory konfigurieren** an, und klicken Sie auf **Weiter**.   
3. Auf der Seite **Elemente veröffentlichen** wird eine Dropdownliste mit verfügbaren Konfigurationen für das Feld **Bereitstellungskonfiguration auswählen** angezeigt.

	![Konfigurationsdatei auswählen](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Wählen Sie die **Konfigurationsdatei** aus, die Sie verwenden möchten, und klicken Sie auf **Weiter**.
5. Vergewissern Sie sich, dass der Name der JSON-Datei auf der Seite **Zusammenfassung** angezeigt wird, und klicken Sie auf **Weiter**. 
6. Klicken Sie auf **Fertig stellen**, nachdem der Bereitstellungsvorgang abgeschlossen ist. 

Bei der Bereitstellung werden die Werte aus der Konfigurationsdatei zum Festlegen der Werte für Eigenschaften in den JSON-Dateien für Data Factory-Entitäten (verknüpfte Dienste, Tabellen oder Pipelines) verwendet, bevor die Entitäten für den Azure Data Factory-Dienst bereitgestellt werden.

## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Lernprogramm: Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-get-started.md).
  

<!---HONumber=AcomDC_0224_2016-->