<properties 
	pageTitle="Erstellen von Pipelines" 
	description="Informieren Sie sich über Azure Data Factory-Pipelines. Erfahren Sie, wie Sie sie erstellen, um Daten zu verschieben und zu transformieren und somit Informationen generieren, die Ihnen Einblicke verschaffen." 
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
	ms.topic="article" y
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Grundlegendes zu Pipelines und Aktivitäten
In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. In diesem Artikel wird davon ausgegangen, dass Sie die Artikel [Übersicht](data-factory-introduction.md) und [Erstellen von Datasets](data-factory-create-datasets.md) bereits gelesen haben.

## Was ist eine Pipeline?
**Pipelines sind logische Gruppierungen von Aktivitäten**. Sie dienen zum Gruppieren von Aktivitäten zu einer Einheit, die zum Ausführen einer Aufgabe verwendet wird. Um Pipelines besser zu verstehen, müssen Sie sich zunächst mit Aktivitäten befassen.

### Was ist eine Aktivität?
Aktivitäten definieren die Aktionen, die Sie auf Ihre Daten anwenden. Jede Aktivität verwendet von null bis zu mehreren [Datasets](data-factory-create-datasets.md) als Eingaben und erzeugt von ein bis zu mehreren Datasets als Ausgaben. **Eine Aktivität ist eine Einheit für die Orchestrierung in Azure Data-Factory.**

Beispielsweise können Sie eine Kopieraktivität verwenden, um das Kopieren von Daten aus einem Dataset in ein anderes zu orchestrieren. Auf ähnliche Weise können Sie eine Hive-Aktivität verwenden, die eine Hive-Abfrage auf einen Azure-HDInsight-Cluster anwendet, um Ihre Daten zu transformieren oder zu analysieren. Azure Data Factory bietet eine Vielzahl von [Aktivitäten zur Datentransformation, -analyse](data-factory-data-transformation-activities.md) und [-verschiebung](data-factory-data-movement-activities.md). Sie können auch eine benutzerdefinierte .NET-Aktivität erstellen, um eigenen Code auszuführen.

Betrachten Sie die folgenden zwei Datasets:

**Azure SQL-Dataset**

Die Tabelle "MyTable" enthält eine Spalte "timestampcolumn", die Sie beim Angeben des datetime-Werts für den Zeitpunkt unterstützt, zu dem die Daten in die Datenbank eingefügt wurden.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure-Blobdataset**

Daten werden stündlich in ein neues Blob kopiert, wobei der Pfad des Blobs jeweils Datum und Uhrzeit mit stündlicher Genauigkeit angibt.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


Die Aktivität "Copy" in der unten gezeigten Pipeline kopiert Daten von Azure SQL in den Azure-Blobspeicher. Sie verwendet stündlich die Azure SQL-Tabelle als Eingabedataset und schreibt die Daten in den Azure Blobspeicher, der durch das Dataset "AzureBlobOutput" dargestellt wird. Das Ausgabedataset wird ebenfalls stündlich generiert. Informationen zum Kopieren der Daten in Bezug auf die Zeiteinheit finden Sie im Abschnitt "Planung und Ausführung". Diese Pipeline besitzt einen aktiven Zeitraum von 3 Stunden von "2015-01-01T08:00:00" bis "2015-01-01T11:00:00".

**Pipeline:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

Nachdem wir einen kurzen Überblick über Aktivitäten gewonnen haben, kommen wir jetzt auf die Pipeline zurück.
 
Pipelines sind logische Gruppierungen von Aktivitäten. Sie dienen zum Gruppieren von Aktivitäten zu einer Einheit, die zum Ausführen einer Aufgabe verwendet wird. **Eine Pipeline ist außerdem die Einheit zur Bereitstellung und Verwaltung von Aktivitäten.** Beispielsweise möchten Sie logisch verwandte Aktivitäten als eine Pipeline zusammenstellen, sodass sie zusammen in den aktiven oder angehaltenen Status versetzt werden können.

Durch das Definieren von Abhängigkeiten zwischen Aktivitäten kann ein Ausgabedataset aus einer Aktivität in einer Pipeline als Eingabedataset für eine andere Aktivität in derselben/einer anderen Pipeline dienen. Dies wird im Abschnitt [Planung und Ausführung](#scheduling-and-execution) im Detail behandelt.

In der Regel werden beim Erstellen einer Pipeline in Azure Data Factory folgende Schritte ausgeführt:

1.	Erstellen Sie eine Data Factory (falls noch nicht erstellt).
2.	Erstellen Sie einen verknüpften Dienst für jeden Datenspeicher oder Computedienst.
3.	Erstellen Sie Eingabe- und Ausgabedatasets.
4.	Erstellen Sie eine Pipeline mit Aktivitäten, die auf die oben definierten Datasets angewendet werden.

![Data Factory-Entitäten](./media/data-factory-create-pipelines/entities.png)

Sehen wir uns an, wie eine Pipeline definiert wird.

## Anatomie einer Pipeline  

Die generische Struktur für eine Pipeline sieht wie folgt aus:

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

Im Abschnitt **activities** kann mindestens eine Aktivität definiert werden. Jede Aktivität besitzt auf oberster Ebene die folgende Struktur:

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

In der folgenden Tabelle werden die Eigenschaften in der Aktivität und den Pipeline-JSON-Definitionen beschrieben:

Tag | Beschreibung | Erforderlich
--- | ----------- | --------
Name | Der Name der Aktivität oder der Pipeline. Geben Sie einen Namen an, der die Aktion darstellt, für deren Durchführung die Aktivität oder die Pipeline konfiguriert ist.<br/><ul><li>Maximale Anzahl von Zeichen: 260</li><li>Muss mit einem Buchstaben, einer Zahl oder einem Unterstrich (\_) beginnen</li><li>Folgende Zeichen sind nicht zulässig: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":" , "\"</li></ul> | Ja
description | Beschreibung des Verwendungszwecks der Aktivität oder der Pipeline | Ja
type | Gibt den Typ der Aktivität an. Verschiedene Typen von Aktivitäten finden Sie in den Artikeln [Aktivitäten der Datenverschiebung](data-factory-data-movement-activities.md) und [Aktivitäten der Datentransformation](data-factory-data-transformation-activities.md). | Ja
inputs | Von der Aktivität verwendete Eingabetabellen<p>// eine Eingabetabelle<br/>"inputs":  [ { "name": "inputtable1" } ],</p><p>// zwei Eingabetabellen <br/>"inputs":  [ { "name": "inputtable1" }, { "name": "inputtable2" } ],</p> | Ja
outputs | Von der Aktivität verwendete Ausgabetabellen<p>// eine Ausgabetabelle<br/>"outputs":  [ { "name": “outputtable1” } ],</p><p>//two output tables<br/>"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” } ],</p> | Ja
linkedServiceName | Name des verknüpften Diensts, der von der Aktivität verwendet wird. <p>Für eine Aktivität kann es erforderlich sein, den verknüpften Dienst anzugeben, der mit der erforderlichen Computeumgebung verknüpft ist.</p> | Ja für die HDInsight-Aktivität und die Azure Machine Learning-Aktivität zur Batchbewertung <p>Nein für alle übrigen</p>
typeProperties | Eigenschaften im Abschnitt "typeProperties" sind abhängig vom Typ der Aktivität. Weitere Informationen finden Sie im Artikel zu den einzelnen Aktivitäten | Nein
policy | Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Falls dies nicht angegeben wird, werden Standardrichtlinien verwendet. Weitere Informationen finden Sie unten | Nein
start | Startdatum-uhrzeit für die Pipeline. Muss im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Eigenschaften "start" und "end" geben zusammen den aktiven Zeitraum der Pipeline an. Ausgabeslices werden nur in diesem aktiven Zeitraum erzeugt. | Nein<p>Der aktive Zeitraum für eine Pipeline kann auch mithilfe des Cmdlets "Set-AzureDataFactoryPipelineActivePeriod" festgelegt werden.</p>
end | Enddatum-/uhrzeit für die Pipeline. Muss, falls gewünscht, im ISO-Format angegeben werden. Beispiel: 2014-10-14T17:32:41Z <p>Falls nicht angegeben, wird dieser Wert als "Start + 48 Stunden" berechnet. Geben Sie zur unendlichen Ausführung der Pipeline "9999-09-09" als Wert für die end-Eigenschaft an.</p>| Nein
isPaused | Wenn der Parameter auf "true" gesetzt ist, wird die Pipeline nicht ausgeführt. Standardwert = false. Sie können diese Eigenschaft zum Aktivieren oder Deaktivieren verwenden. | Nein
scheduler | Die Eigenschaft "scheduler" wird verwendet, um die gewünschte Planung für die Aktivität zu definieren. Die untergeordneten Eigenschaften sind identisch mit denen der [availability-Eigenschaft in einem Dataset](data-factory-create-datasets.md#Availability). | Nein |   

### Aktivitätstypen
Azure Data Factory bietet eine Vielzahl von Aktivitäten zur [Datenverschiebung](data-factory-data-movement-activities.md) und [Datentransformation](data-factory-data-transformation-activities.md).

### Richtlinien
Richtlinien beeinflussen das Laufzeitverhalten einer Aktivität, besonders dann, wenn der Slice einer Tabelle verarbeitet wird. Die Details finden Sie in der folgenden Tabelle.

Eigenschaft | Zulässige Werte | Standardwert | Beschreibung
-------- | ----------- | -------------- | ---------------
Parallelität | Ganze Zahl <p>Maximalwert: 10</p> | 1 | Anzahl der gleichzeitigen Ausführungen der Aktivität.<p>Legt die Anzahl der parallelen Ausführungen einer Aktivität fest, die an verschiedenen Slices stattfinden können. Wenn eine Aktivität beispielsweise eine große Menge verfügbarer Daten durchlaufen muss, kann die Datenverarbeitung durch eine höhere Anzahl gleichzeitiger Ausführungen beschleunigt werden.</p> 
executionPriorityOrder | NewestFirst<p>OldestFirst</p> | OldestFirst | Bestimmt die Reihenfolge der Datenslices, die verarbeitet werden.<p>Nehmen Sie beispielsweise an, Sie haben zwei Slices (einen um 16 Uhr und einen weiteren um 17 Uhr), und beide warten auf ihre Ausführung. Wenn Sie "executionPriorityOrder" auf "NewestFirst" setzen, wird der Slice von 17 Uhr zuerst verarbeitet. Wenn Sie „executionPriorityOrder“ auf „OldestFirst“ festlegen, wird der Slice von 16 Uhr zuerst verarbeitet.</p> 
retry | Ganze Zahl<p>Maximalwert: 10</p> | 3 | Anzahl der Wiederholungsversuche, bevor die Datenverarbeitung für den Slice als Fehler markiert wird. Die Ausführung der Aktivitäten für einen Datenslice wird bis zur angegebenen Anzahl der Wiederholungsversuche wiederholt. Die Wiederholung erfolgt so bald wie möglich nach dem Fehler.
timeout | TimeSpan | 00:00:00 | Timeout für die Aktivität. Beispiel: 00:10:00 (Timeout nach 10 Minuten)<p>Wenn ein Wert nicht angegeben wird oder 0 lautet, ist das Zeitlimit unendlich.</p><p>Wenn die Datenverarbeitungszeit für einen Slice den Timeoutwert überschreitet, wird der Vorgang abgebrochen, und das System versucht, die Verarbeitung zu wiederholen. Die Anzahl der Wiederholungsversuche hängt von der Eigenschaft "retry" ab. Wenn ein Timeout auftritt, lautet der Status „TimedOut“.</p>
delay | TimeSpan | 00:00:00 | Geben Sie die Verzögerung an, mit der die Datenverarbeitung des Slice beginnt.<p>Die Ausführung der Aktivität für einen Datenslice wird gestartet, nachdem die Verzögerung die erwartete Ausführungszeit überschreitet.</p><p>Beispiel: 00:10:00 (Verzögerung von 10 Minuten)</p>
longRetry | Ganze Zahl<p>Maximalwert: 10</p> | 1 | Die Anzahl langer Wiederholungsversuche, bevor die Sliceausführung einen Fehler verursacht.<p>longRetry-Versuche werden durch „longRetryInterval“ über einen Zeitraum verteilt. Wenn Sie eine Zeit zwischen den Wiederholungsversuchen angeben müssen, verwenden Sie "longRetry". Wenn sowohl „retry“ als auch „longRetry“ angegeben werden, umfasst jeder „longRetry“-Versuch „retry“-Versuche, und die maximale Anzahl der Versuche errechnet sich aus „retry * longRetry“.</p><p>Die Richtlinie für die Aktivität enthält z. B. Folgendes:<br/>retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/></p><p>Nehmen Sie an, dass nur ein Slice auszuführen ist (Status: „PendingExecution“) und dass die Ausführung der Aktivität jedes Mal einen Fehler verursacht. Zunächst würden drei aufeinander folgende Ausführungsversuche durchgeführt. Nach jedem Versuch wäre der Slicestatus "Retry". Nachdem die ersten drei Versuche ausgeführt wurden, lautet der Slicestatus „LongRetry“.</p><p>Nach einer Stunde (Wert von „longRetryInterval“) würden drei weitere aufeinander folgende Ausführungsversuche unternommen. Danach würde der Slicestatus "Failed" lauten, und es fänden keine weiteren Versuche statt. Somit wurden insgesamt sechs Versuche unternommen.</p><p>Hinweis: Bei einer erfolgreichen Ausführung lautet der Slicestatus „Ready“, und es werden keine weiteren Versuche durchgeführt.</p><p>„longRetry“ kann in Situationen verwendet werden, bei denen abhängige Daten zu nicht festgelegten Zeiten eingehen oder die gesamte Umgebung, in der die Datenverarbeitung erfolgt, relativ unzuverlässig ist. In solchen Fällen ist die Durchführung von aufeinanderfolgenden Wiederholungen möglicherweise nicht hilfreich, und die Wiederholung nach einem bestimmten Zeitraum führt vielleicht zur gewünschten Ausgabe.</p><p>Vorsicht: Legen Sie für „longRetry“ und „longRetryInterval“ keine hohen Werte fest. In der Regel weisen höhere Werte auf andere Systemprobleme hin.</p> 
longRetryInterval | TimeSpan | 00:00:00 | Die Verzögerung zwischen langen Wiederholungsversuchen 

## Erstellen und Verwalten einer Pipeline
Azure Data Factory bietet verschiedene Mechanismen zum Erstellen und Bereitstellen von Pipelines (die wiederum eine oder mehrere Aktivitäten enthalten).

### Verwenden des Azure-Vorschauportals

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2. Wechseln Sie zu Ihrer Azure Data Factory-Instanz, in der Sie eine Pipeline erstellen möchten.
3. Klicken Sie im Fokus **Zusammenfassung** auf die Kachel **Author and Deploy**. 
 
	![Kachel "Erstellen und bereitstellen"](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Klicken Sie auf der Befehlsleiste auf **Neue Pipeline**.

	![Schaltfläche "Neue Pipeline"](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. Das Editor-Fenster wird mit der JSON-Pipelinevorlage angezeigt.

	![Pipeline-Editor](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Nach dem Erstellen der Pipeline klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.

	**Hinweis:** Während der Bereitstellung führt der Azure Data Factory-Dienst einige Überprüfungen durch, um häufig auftretende Probleme zu beheben. Für den Fall, dass ein Fehler vorliegt, werden die entsprechenden Informationen angezeigt. Korrigieren Sie den Fehler, und stellen Sie die erstellte Pipeline erneut bereit. Mithilfe des Editor können Sie eine Pipeline aktualisieren und löschen.

### Verwenden des Visual Studio-Plug-Ins
Sie können Visual Studio verwenden, um Pipelines zu erstellen und in Azure Data Factory bereitzustellen. Weitere Informationen hierzu finden Sie unter [Lernprogramm: Kopieren von Daten aus Azure Storage in Azure SQL (Visual Studio)](data-factory-get-started-using-vs.md).

### Verwenden von Azure PowerShell
Mit der Azure PowerShell können Sie Pipelines in Azure Data Factory erstellen. Angenommen, Sie haben die Pipeline "JSON" in einer Datei unter "c:\\DPWikisample.json" definiert. Sie können sie in Ihre Azure Data Factory-Instanz, wie im folgenden Beispiel gezeigt, hochladen.

	New-AzureDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Weitere Informationen zu diesem Cmdlet finden Sie unter dem Cmdlet [„New-AzureDataFactoryPipeline“](https://msdn.microsoft.com/library/dn820227.aspx).

### Verwenden der REST-API
Sie können die Pipeline auch mit REST-APIs erstellen und bereitstellen. Dieser Mechanismus kann zum programmgesteuerten Erstellen von Pipelines genutzt werden. Weitere Informationen dazu finden Sie unter [Erstellen oder Aktualisieren einer Pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx).

### Verwenden des .NET SDK
Sie können die Pipeline auch mit dem .NET SDK erstellen und bereitstellen. Dieser Mechanismus kann zum programmgesteuerten Erstellen von Pipelines genutzt werden. Weitere Informationen hierzu finden Sie unter [Programmgesteuertes Erstellen, Verwalten und Überwachen von Data Factorys](data-factory-create-data-factories-programmatically.md).


## Planung und Ausführung
Sie wissen jetzt, was Pipelines und Aktivitäten sind. Sie haben sich auch angesehen, wie sie definiert werden, und besitzen einen allgemeinen Überblick über die Aktivitäten in Azure Data Factory. Sehen wir uns jetzt an, wie sie ausgeführt werden.

Eine Pipeline ist nur zwischen ihrer Start- und ihrer Endzeit aktiv. Sie wird weder vor der Startzeit noch nach der Endzeit ausgeführt. Wenn die Pipeline angehalten wird, wird sie unabhängig von Start- und Endzeit nicht ausgeführt. Wenn eine Pipeline ausgeführt werden soll, darf sie nicht angehalten werden.

Eigentlich ist es nicht die Pipeline, die ausgeführt wird. Es sind vielmehr die Aktivitäten in der Pipeline, die ausgeführt werden. Dies geschieht jedoch im Gesamtkontext der Pipeline. Informationen zur Planung und Ausführung in Azure Data Factory finden Sie unter [Planung und Ausführung](data-factory-scheduling-and-execution.md).

## Verwalten und Überwachen  
Sobald eine Pipeline bereitgestellt wird, können Sie Ihre Pipelines, Slices und Ausführungen verwalten und überwachen. Weitere Informationen finden Sie unter [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md).

## Nächste Schritte

- Erfahren Sie mehr über die [Planung und Ausführung in Azure Data Factory](data-factory-scheduling-and-execution.md).  
- Informieren Sie sich über die Möglichkeiten zur [Datenverschiebung](data-factory-data-movement-activities.md) und [Datentransformation](data-factory-data-transformation-activities.md) in Azure Data Factory.
- Erfahren Sie mehr über das [Verwalten und Überwachen in Azure Data Factory](data-factory-monitor-manage-pipelines.md).
- [Erstellen Sie Ihre erste Pipeline, und stellen Sie sie bereit](data-factory-build-your-first-pipeline.md). 


 

   













 
 


 

 

<!---HONumber=Nov15_HO3-->