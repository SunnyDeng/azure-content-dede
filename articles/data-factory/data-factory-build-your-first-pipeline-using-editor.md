<properties
	pageTitle="Erste Schritte mit Azure Data Factory (Azure-Portal)"
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
	ms.topic="hero-article" 
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Erste Schritte mit Azure Data Factory (Data Factory Edition)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)

In diesem Artikel erfahren Sie, wie Sie mithilfe des [Azure-Portals](https://portal.azure.com/) Ihre erste Azure Data Factory erstellen.

## Voraussetzungen

1. Sie **müssen** den Artikel [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) lesen und die erforderlichen Schritte abschließen, bevor Sie fortfahren.
2. Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Sie sollten den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md) lesen, um eine ausführliche Übersicht über den Dienst zu erhalten.  

## Schritt 1: Erstellen der Data Factory
Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. In diesem Schritt erstellen wir zunächst die Data Factory.

1.	Nach der Anmeldung beim [Azure-Portal](http://portal.azure.com/) gehen Sie wie folgt vor:
	1.	Klicken Sie im linken Menü auf **NEU**. 
	2.	Klicken Sie auf dem Blatt **Erstellen** auf **Datenanalyse**.
	3.	Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.

		![Blatt "Erstellen"](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Geben Sie auf dem Blatt **Neue Data Factory** unter „Name“ die Zeichenfolge **GetStartedDF** ein.

	![Blatt "Neue Data Factory"](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name „GetStartedDF“ ist nicht verfügbar** ändern Sie den Namen der Data Factory (z. B. in „IhrNameGetStartedDF“), und wiederholen Sie den Vorgang. Im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie Benennungsregeln für Data Factory-Artefakte.
	>  
	> Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.

3.	Wählen Sie das **Azure-Abonnement**, in dem die Data Factory erstellt werden soll.
4.	Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe. Erstellen Sie im Rahmen des Tutorials eine Ressourcengruppe mit dem Namen: **ADFGetStartedRG**.    
5.	Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.
6.	Sie sehen die erstellte Data Factory folgendermaßen im **Startmenü** des Azure-Portals:   

	![Erstellen des Data Factory-Status](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Glückwunsch! Sie haben erfolgreich Ihre erste Data Factory erstellt. Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie die Data Factory-Seite mit dem Inhalt der Data Factory. 	

	![Blatt "Data Factory"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Vor dem Erstellen einer Pipeline müssen Sie zunächst einige Data Factory-Entitäten erstellen. Zuerst erstellen Sie verknüpfte Dienste zum Verknüpfen von Datenspeichern/Berechnungen mit Ihrem Datenspeicher, definieren die Datasets für Ein- und Ausgabe, um Daten in verknüpften Datenspeichern darzustellen, und erstellen dann die Pipeline mit einer Aktivität, die diese Datasets verwendet.

## Schritt 2: Erstellen von verknüpften Diensten
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um das in der Aktivität der Pipeline in diesem Beispiel angegebene Hive-Skript auszuführen. Sie müssen identifizieren, welche Datenspeicher-/Computedienste in Ihrem Szenario verwendet werden, und diese Dienste mit der Data Factory verknüpfen, indem Sie verknüpfte Dienste erstellen.

### Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. Im Rahmen dieses Tutorials verwenden Sie das gleiche Azure Storage-Konto, um  
Ein-/Ausgabedaten und die HQL-Skriptdatei zu speichern.

1.	Klicken Sie auf dem Blatt **DATA FACTORY** für **GetStartedDF** auf **Erstellen und bereitstellen**. Der Data Factory-Editor wird gestartet. 
	 
	![Kachel "Erstellen und bereitstellen"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Klicken Sie auf **Neuer Datenspeicher**, und wählen Sie **Azure Storage**.
	
	![Mit Azure Storage verknüpfter Dienst](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Das JSON-Skript zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im Editor angezeigt werden. 
4. Ersetzen Sie **Kontoname** durch den Namen Ihres Azure-Speicherkontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
5. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

	![Schaltfläche "Bereitstellen"](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Nach erfolgreicher Bereitstellung des verknüpften Diensts sollte das Fenster **Draft-1** nicht mehr angezeigt werden, und Sie sehen **StorageLinkedService** in der Strukturansicht links.
        ![Mit Storage verknüpfter Dienst im Menü](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)

 
### Erstellen des mit Azure-HDInsight verknüpften Diensts
In diesem Schritt verknüpfen Sie einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Der HDInsight-Cluster wird automatisch zur Laufzeit erstellt und gelöscht, nachdem die Verarbeitung abgeschlossen und die angegebene Leerlaufzeit verstrichen ist. Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie Ihren eigenen HDInsight-Cluster verwenden. Weitere Informationen finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md).

1. Klicken Sie im **Data Factory Editor** auf der Befehlsleiste auf **New compute**, und wählen Sie im Menü **On-demand HDInsight cluster** aus.

	![Neue Compute-Instanz](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in das Fenster **Draft-1** ein. Der JSON-Codeausschnitt beschreibt die Eigenschaften, die zum Erstellen des bedarfsgesteuerten HDInsight-Clusters verwendet werden. 

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
3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen. 
4. Vergewissern Sie sich, dass in der Strukturansicht auf der linken Seite **StorageLinkedService** und **HDInsightOnDemandLinkedService** angezeigt werden.

	![Strukturansicht mit verknüpften Diensten](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## Schritt 3: Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten für die Hive-Verarbeitung darzustellen. Diese Datasets verweisen auf den **StorageLinkedService**, den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst weist auf ein Azure Storage-Konto, und Datasets geben Container, Ordner und Dateiname in dem Speicher an, der Eingabe- und Ausgabedaten enthält.

### Erstellen des Eingabedatasets

1. Klicken Sie im **Data Factory Editor** auf der Befehlsleiste auf **Neues Dataset** und dann auf **Azure-BLOB-Speicher**.

	![Neues Dataset](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in das Fenster "Draft-1" ein. Im JSON-Codeausschnitt erstellen Sie ein Dataset mit dem Namen **AzureBlobInput**, das Eingabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Eingabedaten im Blobcontainer **adfgetstarted** und im Ordner **inputdata** gespeichert werden.
		
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
	  
	
3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das neu erstellte DataSet bereitzustellen. Das Dataset sollte in der Strukturansicht auf der linken Seite angezeigt werden.


### Erstellen des Ausgabedatasets
Nun erstellen Sie das Ausgabedataset, das die im Azure-Blobspeicher gespeicherten Ausgabedaten darstellt.

1. Klicken Sie im **Data Factory Editor** auf der Befehlsleiste auf **Neues Dataset** und dann auf **Azure-BLOB-Speicher**.  
2. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in das Fenster "Draft-1" ein. Im JSON-Codeausschnitt erstellen Sie ein DataSet mit dem Namen **AzureBlobOutput**, und geben Sie die Struktur der Daten an, die vom Hive-Skript erzeugt werden. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer **adfgetstarted** und im Ordner **partitioneddata** gespeichert werden. Der Abschnitt **availability** gibt an, dass das Ausgabe-DataSet monatlich erzeugt wird.
	
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

	Im Abschnitt **Erstellen des Eingabedatasets** werden diese Eigenschaften beschrieben. Sie legen die Eigenschaft „external“ nicht für ein Ausgabedataset fest, da das Dataset vom Data Factory-Dienst erstellt wird.
3. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um das neu erstellte DataSet bereitzustellen.
4. Überprüfen Sie, ob das Dataset erfolgreich erstellt wurde.

	![Strukturansicht mit verknüpften Diensten](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Schritt 4: Erstellen Ihrer ersten Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline mit einer **HDInsightHive**-Aktivität. Beachten Sie, dass der Eingabeslice monatlich verfügbar ist („frequency“: „Month“, „interval“: „1“), der Ausgabeslice monatlich erstellt wird und die scheduler-Eigenschaft für die Aktivität auch auf monatlich (siehe unten) festgelegt ist. Die Einstellungen für das Ausgabedataset und den Aktivitätsplaner müssen übereinstimmen. Zu diesem Zeitpunkt steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe erzeugt. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. Am Ende dieses Abschnitts werden die in der folgenden JSON verwendeten Eigenschaften erläutert.

1. Klicken Sie im **Data Factory Editor** auf **(...)** und dann auf **New pipeline**.
	
	![Schaltfläche "Neue Pipeline"](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in das Fenster "Draft-1" ein.

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


3. Vergewissern Sie sich, dass Sie die Datei „input.log“ im Ordner „adfgetstarted/inputdata“ im Azure-Blobspeicher sehen, und klicken Sie in der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen. Da die Zeiten für **start** und **end** in der Vergangenheit festgelegt sind, und **isPaused** auf „false“ festgelegt ist, wird die Pipeline (Aktivität in der Pipeline) sofort nach der Bereitstellung ausgeführt.
4. Vergewissern Sie sich, dass die Pipeline in der Strukturansicht angezeigt wird.

	![Strukturansicht mit Pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Glückwunsch, Sie haben Ihre erste Pipeline erfolgreich erstellt!

## Schritt 4: Überwachen der Pipeline

6. Klicken Sie auf **X**, um die Data Factory Editor-Blätter zu schließen und zum Blatt „Data Factory“ zurückzugelangen, und klicken Sie auf **Diagramm**.
  
	![Kachel "Diagramm"](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. In der Diagrammansicht sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.
	
	![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Um alle Aktivitäten in der Pipeline anzuzeigen, klicken Sie mit der rechten Maustaste auf die Pipeline im Diagramm, und klicken Sie auf „Pipeline öffnen“. 

	![Menü "Pipeline öffnen"](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Vergewissern Sie sich, dass Sie die HDInsightHive-Aktivität in der Pipeline sehen. 
  
	![Ansicht „Pipeline öffnen“](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

	Um zur vorherigen Ansicht zurück zu navigieren, klicken Sie oben in der Breadcrumb-Leiste auf **Data Factory**. 
10. Doppelklicken Sie in der **Diagrammansicht** auf das Dataset **AzureBlobInput**. Vergewissern Sie sich, dass der Slice sich im Zustand **Bereit** befindet. Es kann ein paar Minuten dauern, bis der Slice im Status „Bereit“ angezeigt wird. Wenn dies nicht geschieht, nachdem Sie einige Zeit gewartet haben, prüfen Sie, ob Sie die Eingabedatei („input.log“) im richtigen Container („adfgetstarted“) und Ordner („inputdata“) platziert haben.

	![Eingabeslice im Status „Bereit“](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Klicken Sie auf **X**, um das Blatt **AzureBlobInput** zu schließen. 
12. Doppelklicken Sie in der **Diagrammansicht** auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Wenn die Verarbeitung abgeschlossen ist, wird der Slice mit dem Zustand **Bereit** angezeigt.
>[AZURE.IMPORTANT]Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel einige Zeit (etwa 20 Minuten).  

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)
	
10. Sobald der Slice den Status **Bereit** hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.
 
	![Ausgabedaten](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

## Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Lernprogramm: Kopieren von Daten aus einem Azure-Blob in Azure SQL](./data-factory-get-started.md).

### Referenzen
| Thema | Beschreibung |
| :---- | :---- |
| [Pipelines](data-factory-create-pipelines.md) | In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. |
| [Datasets](data-factory-create-datasets.md) | Dieser Artikel bietet Ihnen Informationen über Datasets in Azure Data Factory.
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) | In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. |
| [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md) | In diesem Artikel wird das Überwachen, Verwalten und Debuggen Ihrer Pipelines beschrieben. Ferner wird erläutert, wie Warnungen erstellt und Benachrichtigungen bei Fehlern eingerichtet werden. |


  

<!----HONumber=AcomDC_1223_2015-->