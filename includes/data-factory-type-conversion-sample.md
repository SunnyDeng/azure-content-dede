### Beispiel für eine Typkonvertierung
Im folgenden Beispiel wird das Kopieren von Daten aus einem Blob in Azure SQL mit Typkonvertierungen gezeigt.

Es wird vorausgesetzt, dass das Blobdataset im CSV-Format vorliegt und drei Spalten enthält. Eine davon ist eine datetime-Spalte mit einem benutzerdefinierten datetime-Format mit abgekürzten französischen Namen für die Wochentage.

Sie definieren das Blob-Quelldataset wie folgt zusammen mit Typdefinitionen für die Spalten.

	{
	    "name": " AzureBlobTypeSystemInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
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

Unter Berücksichtigung der obigen Tabelle mit der Zuordnung des SQL-Typs zum .NET-Typ würden Sie die Azure SQL-Tabelle mit dem folgenden Schema definieren.

| Spaltenname | SQL-Typ |
| ----------- | -------- |
| userid | bigint |
| Name | Text |
| lastlogindate | datetime |

Als Nächstes definieren Sie das Azure SQL-Dataset wie folgt. Hinweis: Sie müssen keinen Abschnitt "structure" mit den Typinformationen angeben, da die Typinformationen bereits im zugrunde liegenden Datenspeicher angegeben sind.

	{
	    "name": "AzureSQLOutput",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1,
	        }
	    }
	}

In diesem Fall führt Data Factory die Typkonvertierungen automatisch einschließlich des Datetime-Felds mit dem benutzerdefinierten datetime-Format aus. Dabei wird die Kultur "fr-fr" beim Verschieben von Daten aus dem Blob in Azure SQL verwendet.

<!---HONumber=August15_HO6-->