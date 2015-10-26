## Aufrufen einer gespeicherten Prozedur für die SQL-Senke

Beim Kopieren von Daten in SQL Server oder eine Azure SQL-/SQL Server-Datenbank konnte eine vom Benutzer angegebene gespeicherte Prozedur konfiguriert und mit zusätzlichen Parametern aufgerufen werden.

Eine gespeicherte Prozedur kann genutzt werden, wenn integrierte Kopiermechanismen den Zweck nicht erfüllen. Sie werden in der Regel genutzt, wenn eine zusätzliche Verarbeitung (Zusammenführen von Spalten, Suchen nach zusätzlichen Werten, Einfügen in mehrere Tabellen usw.) vor dem endgültigen Einfügen von Quelldaten in die Zieltabelle durchgeführt werden muss.

Sie können eine beliebige gespeicherte Prozedur aufrufen. Das folgende Beispiel zeigt, wie Sie eine gespeicherte Prozedur verwenden, um einen einfachen Einfügevorgang für eine Tabelle in der Datenbank auszuführen.

**Ausgabedataset**

In diesem Beispiel ist "type" auf "SqlServerTable" festgelegt. Legen Sie "type" auf "AzureSqlTable" fest, um eine Azure SQL-Datenbank zu verwenden.

	{
	  "name": "SqlOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlLinkedService",
	    "typeProperties": {
	      "tableName": "Marketing"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}
	
Definieren Sie den Abschnitt "SqlSink" in der JSON der Kopieraktivität wie folgt. Zum Aufrufen einer gespeicherten Prozedur beim Einfügen von Daten sind die Eigenschaften "SqlWriterStoredProcedureName" und "SqlWriterTableType" erforderlich.

	"sink":
	{
	    "type": "SqlSink",
	    "SqlWriterTableType": "MarketingType",
	    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
	    "storedProcedureParameters":
	            {
	                "stringData": 
	                {
	                    "value": "str1"     
	                }
	            }
	}

Definieren Sie die gespeicherte Prozedur in der Datenbank mit demselben Namen wie "SqlWriterStoredProcedureName". Sie behandelt die Eingabedaten aus der angegebenen Quelle und fügt sie in die Ausgabetabelle ein. Beachten Sie, dass der Parametername der gespeicherten Prozedur mit dem "tableName" identisch sein sollte, der in der JSON-Datei der Tabelle definiert ist.

	CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
	AS
	BEGIN
	    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
	    INSERT [dbo].[Marketing](ProfileID, State)
	    SELECT * FROM @Marketing
	END

Definieren Sie in der Datenbank den Tabellentyp mit demselben Namen wie "SqlWriterTableType". Beachten Sie, dass das Schema des Tabellentyps mit dem Schema übereinstimmen sollte, das von den Eingabedaten zurückgegeben wird.

	CREATE TYPE [dbo].[MarketingType] AS TABLE(
	    [ProfileID] [varchar](256) NOT NULL,
	    [State] [varchar](256) NOT NULL,
	)

Das Feature der gespeicherten Prozedur nutzt [Tabellenwertparameter](https://msdn.microsoft.com/library/bb675163.aspx).

<!---HONumber=Oct15_HO3-->