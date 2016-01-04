<properties 
	pageTitle="Azure Data Factory Editor" 
	description="Beschreibt Azure Data Factory Editor. Dieses Tool ermöglicht Ihnen das Erstellen, Bearbeiten und Bereitstellen von JSON-Definitionen für verknüpfte Dienste, Tabellen und Pipelines mithilfe einer einfachen webbasierten Benutzeroberfläche." 
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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# Azure Data Factory Editor
Azure Data Factory Editor ist ein einfacher Web-Editor, der im Azure-Portal enthalten ist. Damit können Sie JSON-Dateien aller Azure Data Factory-Entitäten erstellen, bearbeiten und bereitstellen. So können Sie verknüpfte Dienste, Datasets und Pipelines anhand der JSON-Vorlagen erstellen, die im Umfang des Data Factory-Diensts enthalten sind. Wenn Sie sich mit PowerShell noch nicht auskennen, müssen Sie Azure PowerShell auf diese Weise nicht installieren und einführen, um Azure Data Factorys zu erstellen.

## Starten von Azure Data Factory Editor
Um Data Factory Editor zu starten, klicken Sie auf dem Blatt **Data Factory** für Ihre Azure Data Factory auf die Kachel **Erstellen und bereitstellen**.

![Kachel "Erstellen und bereitstellen"][author-and-deploy-tile]

Data Factory Editor wird wie in der folgenden Abbildung dargestellt angezeigt:
 
![Data Factory Editor][data=factory-editor]
 
## Erstellen neuer verknüpfter Dienste, Datasets und Pipelines
Es gibt vier Schaltflächen auf der Symbolleiste, mit denen Sie Azure Data Factory-Entitäten erstellen können.
 
- **Neuer Datenspeicher** zum Erstellen eines verknüpften Datenspeicherdiensts. Wenn Sie auf diese Schaltfläche klicken, wird Ihnen ein Menü mit den folgenden Optionen angezeigt: Azure Storage, Azure SQL-Datenbank, Lokale SQL Server-Datenbank.
- **Neue Computeressource** zum Erstellen eines verknüpfter Computediensts. Wenn Sie auf diese Schaltfläche klicken, wird Ihnen ein Menü mit den folgenden Optionen angezeigt: On-Demand-HDInsight-Cluster, HDInsight-Cluster, Verknüpfter AzureML-Dienst.      
- **Neues Dataset** zum Erstellen eines Datasets. Wenn Sie auf diese Schaltfläche klicken, werden Ihnen die folgenden Optionen angezeigt: Blobtabelle, Azure SQL-Tabelle, Lokale Tabelle.  
- **Neue Pipeline** zum Erstellen einer Pipeline. Klicken Sie auf **... (drei Punkte)** auf der Symbolleiste, wenn diese Schaltfläche nicht auf der Symbolleiste angezeigt wird.
 
### So erstellen Sie einen verknüpften Speicherdienst
1. Klicken Sie auf **Neuer Datenspeicher**, und klicken Sie auf eine der Optionen im Menü.
 
	![Menü "Neuer Datenspeicher"][new-data-store-menu] 
2. Die JSON-Vorlage zum Erstellen eines verknüpften Speicherdiensts wird im **Editor-Arbeitsbereich** rechts angezeigt. Außerdem wird ein Entwurfsknoten unter **Entwürfe** angezeigt. Gehen Sie wie folgt vor:
	1. Für **Azure-Speicher**: Ersetzen Sie **<Kontoname>** und **<Kontoschlüssel>** durch den Namen und den Schlüssel Ihres Azure-Speicherkontos.
	2. Für **Azure SQL-Datenbank**: Ersetzen Sie **<Servername>** durch den Namen des Azure SQL-Servers, **<Datenbankname>** durch den Namen der Datenbank, **<Benutzername>@<Servername>** durch den Namen des Benutzers und **<Kennwort>** durch das Kennwort für das Benutzerkonto. 
	3. Für **Lokale Azure SQL-Datenbank**: Ersetzen Sie **<Servername>** durch den Namen des lokalen SQL-Servers, **<Datenbankname>** durch den Namen der Datenbank, **<Benutzername>** durch den Namen des Benutzers und **<Kennwort>** durch das Kennwort für das Benutzerkonto.
4. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen. Klicken Sie auf **Verwerfen**, um den erstellten JSON-Entwurf zu verwerfen.
 
	![Schaltfläche "Bereitstellen"][deploy-button]

1. Der Status des Bereitstellungsvorgangs wird in der Titelleiste angezeigt.

	![Nachricht über erfolgreiche Bereitstellung][deploy-success-message]
2. Wenn der Bereitstellungsvorgang erfolgreich ist, wird der erstellte verknüpfte Dienst in der Strukturansicht auf der linken Seite angezeigt.
  
	![StorageLinkedService in Strukturansicht][storagelinkedservice-in-treview]

### So erstellen Sie einen verknüpften Computedienst
1. Klicken Sie auf **Neue Computeressource**, und klicken Sie auf eine der Optionen im Menü.
 
	![Menü "Neue Computeressource"][new-compute-menu] 
2. Die JSON-Vorlage zum Erstellen eines verknüpften Computediensts wird im Editor-Arbeitsbereich rechts angezeigt. Gehen Sie wie folgt vor:
	1. Geben Sie für **On-Demand-HDInsight-Cluster** Werte für die folgenden Eigenschaften ein: 
		1. Für die **clusterSize**-Eigenschaft geben Sie die Größe des HDInsight-Clusters ein, den der Data Factory-Dienst zur Laufzeit erstellen soll. 
		3. Für die **timeToLive**-Eigenschaft geben Sie die zulässige Leerlaufzeit vor dem Löschen des HDInsight-Clusters an. Beispiel: 00:05:00 gibt an, dass der Cluster nach 5 Minuten Leerlaufzeit gelöscht werden soll.
		4. Für die **version**-Eigenschaft geben Sie die HDInsight-Version für den Cluster an (Standard: Version 3.1).
		5. Für die **linkedServiceName**-Eigenschaft geben Sie den verknüpften Azure-Speicherdienst an, der dem HDInsight-Cluster zugeordnet werden soll. 
	6. Geben Sie für **HDInsight-Cluster** (Bring Your Own) Werte für die folgenden Eigenschaften ein:
		1. Für die **clusterUri**-Eigenschaft geben Sie die URL für Ihren eigenen HDInsight-Cluster an. 
		2. Für die **userName**-Eigenschaft geben Sie das Benutzerkonto an, das der Data Factory-Dienst zum Herstellen einer Verbindung mit dem HDInsight-Cluster verwenden soll. 
		3. Für die **password**-Eigenschaft geben Sie das Kennwort für das Benutzerkonto ein. 
		4. Für die **linkedServiceName**-Eigenschaft geben Sie den verknüpften Azure-Speicherdienst an, der dem HDInsight-Cluster zugeordnet ist.
	5. Für **Verknüpfter AzureML-Dienst** geben Sie die folgenden Eigenschaften an:
		1. Für die **mlEndPoint**-Eigenschaft geben Sie die URL für die Azure Machine Learning-Batchbewertung an.
		2. Für die **apiKey**-Eigenschaft geben Sie den API-Schlüssel für das veröffentlichte Arbeitsbereichsmodell an.
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

> [AZURE.NOTE]Im Thema [Verknüpfte Dienste][msdn-linkedservices-reference] in der MSDN Library finden Sie Beschreibungen von JSON-Elementen, die zum Definieren eines verknüpften Azure Data Factory-Diensts verwendet werden.

### So erstellen Sie ein neues Dataset
1. Klicken Sie auf **Neues Dataset**, und klicken Sie auf eine der Optionen im Menü.
2. Die JSON-Vorlage zum Erstellen eines Datasets wird im Editor-Arbeitsbereich rechts angezeigt. Gehen Sie wie folgt vor: 
	1. Für **Blobtabelle** geben Sie Werte für die folgenden Eigenschaften an:
	2. Für **Azure SQL-Tabelle** oder **Lokale Tabelle** geben Sie Werte für die folgenden Eigenschaften: 
		1. Im Abschnitt **location**: 
			2. Für die **linkedServiceName**-Eigenschaft geben Sie den Namen des verknüpften Diensts an, der auf Ihre Azure SQL/lokale SQL Server-Datenbank verweist.
			2. Für die **tableName**-Eigenschaft geben Sie den Namen der Tabelle in der Azure SQL-Datenbank-Instanz/auf dem lokalen SQL-Server an, auf die der verknüpfte Dienst verweist.
		3. Im Abschnitt **availability**:
			1. Für die **frequency**-Eigenschaft geben Sie die Zeiteinheit für die Erstellung von Datenslices an. Folgende Häufigkeitswerte werden unterstützt: Minute, Hour, Day, Week, Month.
			2. Für die **interval**-Eigenschaft geben Sie das Intervall innerhalb der festgelegten Häufigkeit an. Wenn **frequency** auf **Hour** und **interval** auf **1** festgelegt ist, werden stündlich neue Datenslices erzeugt. 
		3. Im Abschnitt **structure**: 
			1. Geben Sie Namen und Typen von Spalten an, wie im folgenden Beispiel gezeigt wird:
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]Im Thema [Tabellen][msdn-tables-reference] in der MSDN Library finden Sie Beschreibungen von JSON-Elementen, die zum Definieren einer Azure Data Factory-Tabelle verwendet werden.
 		           
### So erstellen und aktivieren Sie eine Pipeline 
1. Klicken Sie in der Symbolleiste auf **Neue Pipeline**. Wenn die Schaltfläche **Neue Pipeline** nicht angezeigt wird, klicken Sie auf **... (drei Punkte)**, um sie anzuzeigen.   
2. Die JSON-Vorlage zum Erstellen einer Pipeline wird im Editor-Arbeitsbereich rechts angezeigt. Gehen Sie wie folgt vor: 
	1. Für die **description**-Eigenschaft geben Sie eine Beschreibung für die Pipeline an.
	2. Für den Abschnitt **activities** fügen Sie der Pipeline Aktivitäten hinzu. Beispiel:
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
					"type": "CopyActivity",
					"inputs": [ {"name": "EmpTableFromBlob"} ],
					"outputs": [ {"name": "EmpSQLTable"} ],		
					"transformation":
					{
						"source":
						{                               
							"type": "BlobSource"
						},
						"sink":
						{
							"type": "SqlSink"
						}	
					},
					"Policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"style": "StartOfInterval",
						"retry": 0,
						"timeout": "01:00:00"
					}		
				}
    		]
	3. Für die **start**-Eigenschaft geben Sie an, wann die Datenverarbeitung gestartet wird oder die Datenslices verarbeitet werden. Beispiel: 2014-05-01T00:00:00Z.
	4. Für die **end**-Eigenschaft geben Sie an, wann die Datenverarbeitung endet. Beispiel: 2014-05-01T00:00:00Z.       

> [AZURE.NOTE]Im Thema [Pipelines und Aktivitäten][msdn-pipelines-reference] in der MSDN Library finden Sie Beschreibungen von JSON-Elementen, die zum Definieren einer Azure Data Factory-Pipeline verwendet werden.

## So fügen Sie einer Pipeline-JSON eine Aktivitätsdefinition hinzu
Die Aktivitätsdefinition kann einer JSON-Pipeline hinzugefügt werden, indem Sie in der Symbolleiste auf **Aktivität hinzufügen** klicken. Wenn Sie auf diese Schaltfläche klicken, wählen Sie den Typ der Aktivität aus, die der Pipeline hinzugefügt werden soll.

![Optionen zu "Aktivität hinzufügen"][add-activity-options]

Wenn Sie Daten aus einer Azure SQL-Datenbank in Azure Blobspeicher kopieren und die Daten im Blobspeicher über Pig-Skript auf einem HDInsight-Cluster verarbeiten möchten, fügen Sie der Pipeline zuerst eine **Kopieraktivität** und dann eine **Pig-Aktivität** hinzu. Hierdurch werden zwei Abschnitte im Aktivitätsabschnitt der Pipeline-JSON erstellt. Bei Pig-Aktivität handelt es sich lediglich um HDInsight-Aktivität mit Pig-Transformation.

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## Starten einer Pipeline
Sie können das Datum und Uhrzeit für den Start und das Ende einer Pipeline angeben, indem Sie Werte für die Start- und End-Eigenschaften in JSON festlegen.

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## Entwürfe im Editor
Entwürfe erlauben Ihnen das vorübergehende Speichern Ihrer Arbeit, wenn Sie den Kontext wechseln oder zu einer anderen Entität in der Data Factory navigieren. Die Lebensdauer der Entwürfe ist an die Browsersitzung geknüpft. Wenn Sie den Browser schließen oder einen anderen Computer verwenden, stehen die Entwürfe nicht zur Verfügung.

## So verwerfen Sie einen JSON-Entwurf einer Data Factory-Entität
Sie können die JSON-Definition einer Azure Data Factory-Entität verwerfen, indem Sie in der Symbolleiste auf **Verwerfen** klicken.

## So klonen Sie eine Data Factory-Entität
Sie können eine vorhandene Azure Data Factory-Entität (verknüpfter Dienst, Tabelle oder Pipeline) klonen, indem Sie die Entität in der Strukturansicht auswählen und in der Symbolleiste auf die Schaltfläche **Klonen** klicken.

![Data Factory-Entität klonen][clone-datafactory-entity]

In der Strukturansicht wird im Knoten **Entwürfe** ein neuer Entwurf erstellt.

## So löschen Sie eine Data Factory-Entität
Sie können eine Azure Data Factory-Entität (verknüpfter Dienst, Tabelle oder Pipeline) löschen, indem Sie die Entität in der Strukturansicht auswählen und in der Symbolleiste auf **Löschen** klicken (oder) mit der rechten Maustaste auf die Entität und dann auf **Löschen** klicken.

![Data Factory-Entität löschen][delete-datafactory-entity]

## Weitere Informationen
Schrittanleitungen zum Erstellen einer Azure Data Factory mit Data Factory Editor finden Sie im Thema [Erste Schritte mit Azure Data Factory][data-factory-get-started].

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=AcomDC_1203_2015-->