<properties 
	pageTitle="SQL Server-Aktivität ";Gespeicherte Prozedur";" 
	description="Informationen, wie Sie die SQL Server-Aktivität ";Gespeicherte Prozedur"; in einer Data Factory-Pipeline zum Aufrufen einer gespeicherten Prozedur in einer Azure SQL-Datenbank oder einem Azure SQL Data Warehouse verwenden können." 
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
	ms.date="10/12/2015" 
	ms.author="spelluru"/>

# SQL Server-Aktivität "Gespeicherte Prozedur"

Sie können die SQL Server-Aktivität "Gespeicherte Prozedur" in einer Data Factory-[Pipeline](data-factory-create-pipelines.md) zum Aufrufen einer gespeicherten Prozedur in einer **Azure SQL-Datenbank** oder einem **Azure SQL Data Warehouse** verwenden. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der einen allgemeinen Überblick über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

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
outputs | Von der Aktivität "Gespeicherte Prozedur" generierte Ausgabedatasets. Stellen Sie sicher, dass die Ausgabetabelle einen verknüpften Dienst verwendet, der eine Azure SQL-Datenbank oder ein Azure SQL Data Warehouse mit der Data Factory verknüpft. Die Ausgaben in der Aktivität "Gespeicherte Prozedur" dienen als Möglichkeit, das Ergebnis dieser Aktivität für die nachfolgende Verarbeitung zu übergeben und/oder Abhängigkeiten bei deren Verkettung zu verwalten. | Ja
storedProcedureName | Geben Sie den Namen der gespeicherten Prozedur in der Azure SQL-Datenbank oder dem Azure SQL Data Warehouse an, die bzw. das vom verknüpften Dienst dargestellt wird, den die Ausgabetabelle verwendet. | Ja
storedProcedureParameters | Geben Sie Werte für Parameter der gespeicherten Prozedur an. | Nein

## Exemplarische Vorgehensweise

### Beispiel für eine Tabelle und eine gespeicherte Prozedur
1. Erstellen Sie die folgende **Tabelle** in der Azure SQL-Datenbank mithilfe von SQL Server Management Studio oder anderen Tools, mit denen Sie vertraut sind. Die Spalte "datetimestamp" enthält das Datum und die Uhrzeit, zu der die entsprechende ID generiert wird. 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	"Id" ist der eindeutige Bezeichner, und die Spalte "datetimestamp" enthält das Datum und die Uhrzeit, zu der die entsprechende ID generiert wird. ![Beispieldaten](./media/data-factory-stored-proc-activity/sample-data.png)

2. Erstellen Sie die folgende **gespeicherte Prozedur**, mit welcher Daten in **sampletable** eingefügt werden.

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT]**Name** und **Groß- und Kleinschreibung** des Parameters ("DateTime" in diesem Beispiel) müssen mit dem Parameter übereinstimmen, der in der JSON der Pipeline/Aktivität angegeben ist. Stellen Sie bei der Definition der gespeicherten Prozedur sicher, dass **@** als Präfix für den Parameter verwendet wird.
	
### Erstellen einer Data Factory  
4. Nach der Anmeldung beim [Azure-Portal](http://portal.azure.com/) gehen Sie wie folgt vor:
	1.	Klicken Sie im linken Menü auf **NEU**. 
	2.	Klicken Sie auf dem Blatt **Erstellen** auf **Datenanalyse**.
	3.	Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.
4.	Geben Sie auf dem Blatt **Neue Data Factory** unter "Name" die Zeichenfolge **SProcDF** ein. Azure Data Factory-Namen sind global eindeutig. Sie müssen dem Namen der Data Factory Ihren Namen voranstellen, um die erfolgreiche Erstellung der Factory zu aktivieren. 
3.	Wenn Sie noch keine Ressourcengruppe erstellt haben, müssen Sie eine Ressourcengruppe erstellen. Gehen Sie dazu folgendermaßen vor:
	1.	Klicken Sie auf **RESSOURCENGRUPPENNAME**.
	2.	Wählen Sie auf dem Blatt **Ressourcengruppe** die Option **Neue Ressourcengruppe erstellen** aus.
	3.	Geben Sie auf dem Blatt **Ressourcengruppe erstellen** den Namen **ADFTutorialResourceGroup** für **Name** ein.
	4.	Klicken Sie auf **OK**.
4.	Nachdem Sie die Ressourcengruppe ausgewählt haben, stellen Sie sicher, dass Sie das richtige Abonnement verwenden, in dem die Data Factory erstellt werden soll.
5.	Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.
6.	Sie sehen die erstellte Data Factory im **Startmenü** des Azure-Portals: Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie die Data Factory-Seite mit dem Inhalt der Data Factory.

### Erstellen eines mit Azure SQL verknüpften Diensts  
Nach dem Erstellen der Data Factory erstellen Sie einen mit Azure SQL verknüpften Dienst, der Ihre Azure SQL-Datenbank mit der Data Factory verknüpft. Dies ist die Datenbank, die die Beispieltabelle "sampletable" und die gespeicherte Prozedur "sp\_sample" enthält.

7.	Klicken Sie auf dem Blatt **DATA FACTORY** für **SProcDF** auf **Erstellen und bereitstellen**. Der Data Factory-Editor wird gestartet. 
2.	Klicken Sie in der Befehlsleiste auf **Neuer Datenspeicher**, und wählen Sie **Azure SQL**. Das JSON-Skript zum Erstellen eines mit Azure SQL verknüpften Diensts wird im Editor angezeigt. 
4. Ersetzen Sie **servername** durch den Namen Ihres Azure SQL-Datenbankservers, **databasename** durch die Datenbank, in der Sie die Tabelle und die gespeicherte Prozedur erstellt haben, ****username@servername** durch das Benutzerkonto, das Zugriff auf die Datenbank besitzt, und **password** durch das Kennwort für das Benutzerkonto.
5. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

### Erstellen eines Ausgabedatasets
6. Klicken Sie in der Befehlsleiste auf **Neues Dataset**, und wählen Sie **Azure SQL**.
7. Kopieren Sie das folgende JSON-Skript, und fügen Sie es in den JSON-Editor ein.

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. Klicken Sie in der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen. 

### Erstellen einer Pipeline mit der Aktivität "SqlServerStoredProcedure"
Erstellen wir jetzt eine Pipeline mit der Aktivität "SqlServerStoredProcedure".
 
9. Klicken Sie in der Befehlsleiste auf **...** und dann auf **Neue Pipeline**. 
9. Kopieren Sie folgenden JSON-Codeausschnitt, und fügen Sie ihn ein. **storedProcedureName** wird auf **sp\_sample** festgelegt. Name und Schreibweise des Parameters **DateTime** muss mit dem Namen und der Schreibweise des Parameters in der Definition der gespeicherten Prozedur übereinstimmen.  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.  

### Überwachen der Pipeline

6. Klicken Sie auf **X**, um die Data Factory Editor-Blätter zu schließen und zum Blatt "Data Factory" zurückzugelangen, und klicken Sie auf **Diagramm**.
7. In der Diagrammansicht sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets. 
8. Doppelklicken Sie in der Diagrammansicht auf das Dataset **sprocsampleout**. Die Slices werden im Zustand "Bereit" angezeigt. Da für jede Stunde zwischen 2015/01/02 und 2015/01/03 ein Slice erzeugt wurde, sollten 24 Slices angezeigt werden. 
10. Wenn ein Slice sich im Zustand **Bereit** befindet, führen Sie eine Abfrage **select * from sampledata** für die Azure SQL-Datenbank aus, um sicherzustellen, dass die Daten von der gespeicherten Prozedur in die Tabelle eingefügt wurden.

	![Ausgabedaten](./media/data-factory-stored-proc-activity/output.png)

	Unter [Überwachen der Pipeline](data-factory-monitor-manage-pipelines.md) finden Sie ausführliche Informationen zur Überwachung von Azure Data Factory-Pipelines.

> [AZURE.NOTE]Im obigen Beispiel weist "SprocActivitySample" keine Eingaben auf. Wenn Sie dies mit einer vorgelagerten Aktivität verketten möchten (d. h. vor der Verarbeitung), können die Ausgaben der vorgelagerten Aktivität als Eingaben in diese Aktivität verwendet werden. In diesem Fall wird diese Aktivität erst ausgeführt, nachdem die vorgelagerte Aktivität abgeschlossen und deren Ausgaben (mit dem Status "Bereit") verfügbar sind. Die Eingaben können nicht direkt als Parameter für die Aktivität "Gespeicherte Prozedur" verwendet werden.

## Übergeben eines statischen Werts 
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

<!---HONumber=AcomDC_1203_2015-->