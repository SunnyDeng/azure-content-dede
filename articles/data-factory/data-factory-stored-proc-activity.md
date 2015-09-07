<properties 
	pageTitle="SQL Server-Aktivität Gespeicherte Prozedur"
	description="Informationen, wie Sie die SQL Server-Aktivität Gespeicherte Prozedur in einer Data Factory-Pipeline zum Aufrufen einer gespeicherten Prozedur in einer Azure SQL-Datenbank verwenden können."
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
	ms.date="08/04/2015"
	ms.author="spelluru"/>

# SQL Server-Aktivität "Gespeicherte Prozedur"

Sie können die SQL Server-Aktivität "Gespeicherte Prozedur" in einer Data Factory-[Pipeline](data-factory-create-pipelines.md) zum Aufrufen einer gespeicherten Prozedur in einer **Azure SQL**-Datenbank verwenden. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der einen allgemeinen Überblick über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

## Syntax
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Syntaxdetails

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Name | Der Name der Aktivität | Ja
description | Ein Text, der beschreibt, wofür die Aktivität verwendet wird. | Nein
Typ | SqlServerStoredProcedure | Ja
inputs | Eingabedatasets müssen (im Status "Bereit") verfügbar sein, damit die Aktivität "Gespeicherte Prozedur" ausgeführt wird. Die Eingaben in die Aktivität "Gespeicherte Prozedur" dienen nur zur Verwaltung von Abhängigkeiten bei deren Verkettung. Die Eingabedatasets können nicht als Parameter in der gespeicherten Prozedur genutzt werden. | Nein
outputs | Von der Aktivität "Gespeicherte Prozedur" generierte Ausgabedatasets. Stellen Sie sicher, dass die Ausgabetabelle einen verknüpften Dienst verwendet, der eine Azure SQL-Datenbank mit der Data Factory verknüpft. Die Ausgaben in der Aktivität "Gespeicherte Prozedur" dienen als Möglichkeit, das Ergebnis dieser Aktivität für die nachfolgende Verarbeitung zu übergeben und/oder Abhängigkeiten bei deren Verkettung zu verwalten. | Ja
storedProcedureName | Geben Sie den Namen der gespeicherten Prozedur in der Azure SQL-Datenbank an, die vom verknüpften Dienst dargestellt wird, den die Ausgabetabelle verwendet. | Ja
storedProcedureParameters | Geben Sie Werte für Parameter der gespeicherten Prozedur an. | Nein

## Beispiel

Nehmen wir ein Beispiel, bei dem Sie eine Tabelle in der Azure SQL-Datenbank erstellen, die zwei Spalten enthält:

Spalte | Typ
------ | ----
ID | Eindeutige ID
Datetime | Datum und Uhrzeit der Erstellung der betreffenden ID

![Beispieldaten](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

> [AZURE.NOTE]**Name** und **Groß-und Kleinschreibung** des Parameters ("DateTime" bei diesem Beispiel) müssen mit dem Parameter übereinstimmen, der in der nachstehenden JSON der Aktivität angegeben ist. Stellen Sie bei der Definition der gespeicherten Prozedur sicher, dass **@** als Präfix für den Parameter verwendet wird.

Um diese gespeicherte Prozedur in einer Data Factory-Pipeline auszuführen, müssen Sie folgende Schritte ausführen:

1.	Erstellen Sie einen [verknüpften Dienst](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) zum Registrieren der Verbindungszeichenfolge der Azure SQL-Datenbank, in der die gespeicherte Prozedur ausgeführt werden soll.
2.	Erstellen Sie ein [Dataset](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties), das auf die Ausgabetabelle in der Azure SQL-Datenbank zeigt. Lassen Sie uns dieses Dataset "sprocsampleout" nennen. Dieses Dataset muss auf den verknüpften Dienst in Schritt 1 verweisen. 
3.	Erstellen Sie die gespeicherte Prozedur in der Azure SQL-Datenbank.
4.	Erstellen Sie die nachstehende [Pipeline](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) mit der "SqlServerStoredProcedure"-Aktivität, um die gespeicherte Prozedur in der Azure SQL-Datenbank aufzurufen.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		            	"name": "SprocActivitySample",
		             	"type": " SqlServerStoredProcedure",
		             	"outputs": [ {"name": "sprocsampleout"} ],
		             	"typeProperties":
		              	{
		                	"storedProcedureName": "sp_sample",
			        		"storedProcedureParameters": 
		        			{
		            			"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        			}
						}
	            	}
		        ]
		     }
		}
5.	Stellen Sie die [Pipeline](data-factory-create-pipelines.md) bereit.
6.	[Überwachen Sie die Pipeline](data-factory-monitor-manage-pipelines.md) mithilfe der Überwachungs- und Verwaltungsansichten von Data Factory.

> [AZURE.NOTE]Im obigen Beispiel weist "SprocActivitySample" keine Eingaben auf. Wenn Sie dies mit einer vorgelagerten Aktivität verketten möchten (d. h. vor der Verarbeitung), können die Ausgaben der vorgelagerten Aktivität als Eingaben in diese Aktivität verwendet werden. In diesem Fall wird diese Aktivität erst ausgeführt, nachdem die vorgelagerte Aktivität abgeschlossen und deren Ausgaben (mit dem Status "Bereit") verfügbar sind. Die Eingaben können nicht direkt als Parameter für die Aktivität "Gespeicherte Prozedur" verwendet werden.
> 
> Namen und Groß-/Kleinschreibung der Parameter der gespeicherten Prozedur in der JSON-Datei müssen mit den Namen der Parameter der gespeicherten Prozedur in der Zieldatenbank übereinstimmen.

Lassen Sie uns nun der Tabelle eine weitere Spalte mit dem Namen "Scenario" hinzufügen, die den statischen Wert "Document sample" enthält.

![Beispieldaten 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Um dies zu erreichen, übergeben Sie den Parameter "Scenario" und den Wert aus der Aktivität "Gespeicherte Prozedur". Der Abschnitt "typeProperties" im obigen Beispiel sieht folgendermaßen aus:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO9-->