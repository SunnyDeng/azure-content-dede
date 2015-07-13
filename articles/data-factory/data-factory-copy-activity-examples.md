<properties 
	pageTitle="Beispiele für die Verwendung der Kopieraktivität in Azure Data Factory" 
	description="Beispiele für das Verwenden einer Kopieraktivität in einer Azure Data Factory" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Beispiele für die Verwendung der Kopieraktivität in Azure Data Factory
Mithilfe der **Kopieraktivität** in einer Pipeline können Sie Daten aus einer Quelle an eine Senke (Ziel) in einem Batch kopieren. Dieses Thema enthält einige Beispiele für die Verwendung der Kopieraktivität in einer Data Factory-Pipeline. Eine ausführliche Übersicht über die Kopieraktivität und wichtige Szenarien, die davon unterstützt werden, finden Sie unter [Kopieren von Daten mit Azure Data Factory][adf-copyactivity].

## Kopieren von Daten aus einer lokalen SQL Server-Datenbank in ein Azure-Blob
In diesem Beispiel werden eine Eingabe- und eine Ausgabetabelle definiert. Die Tabellen werden in einer Kopieraktivität in einer Pipeline verwendet, die Daten aus einer lokalen SQL Server-Datenbank in ein Azure-BLOB kopiert.

### Annahmen
In diesem Beispiel wird davon ausgegangen, dass Sie bereits über die folgenden Azure Data Factory-Artefakte verfügen:

* Ressourcengruppe namens **ADF**.
* Eine Azure Data Factory namens **CopyFactory**.
* Ein Gateway zur Datenverwaltung mit dem Namen **mygateway** wird erstellt und online geschaltet.  

### Erstellen eines mit der lokalen SQL Server-Quelldatenbank verknüpften Diensts
In diesem Schritt erstellen Sie einen verknüpften Dienst namens **MyOnPremisesSQLDB**, der auf eine lokale SQL Server-Datenbank verweist.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

Beachten Sie Folgendes:

- **type** ist auf **OnPremisesSqlLinkedService** festgelegt.
- **connectionString** ist auf die Verbindungszeichenfolge einer SQL Server-Datenbank festgelegt. 
- **gatewayName** ist auf den Namen des Datenverwaltungsgateways festgelegt, das Sie auf dem lokalen Computer installiert haben, und beim Azure Data Factory-Dienstportal registriert. 

Unter [Lokaler verknüpfter SQL-Dienst](https://msdn.microsoft.com/library/dn893523.aspx) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren eines lokalen verknüpften SQL-Diensts.
 
### Erstellen eines verknüpften Dienst für das Azure-Ziel-Blob
In diesem Schritt erstellen Sie einen verknüpften Dienst namens **MyAzureStorage**, der auf einen Azure-Blob-Speicher verweist.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Beachten Sie Folgendes:

- **type** ist auf **AzureStorageLinkedService** festgelegt.
- **connectionString** – Geben Sie den Kontonamen und -schlüssel für den Azure-Speicher an.

Unter [Mit Azure-Speicher verknüpfter Dienst](https://msdn.microsoft.com/library/dn893522.aspx) finden Sie Details zu JSON-Elementen zum Definieren eines mit Azure-Speicher verknüpften Diensts.

### JSON für Eingabetabelle
Das folgende JSON-Skript definiert eine Eingabetabelle, die auf eine SQL-Tabelle verweist: **MyTable** in einer lokalen SQL Server-Datenbank, die den verknüpften Dienst **MyOnPremisesSQLDB** definiert. Beachten Sie, dass **Name** der Name der Azure Data Factory-Tabelle und **Tabellenname** der Name der SQL-Tabelle in einer SQL Server-Datenbank ist.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Beachten Sie Folgendes:

- **type** ist auf **OnPremisesSqlServerTableLocation** festgelegt.
- **tableName** ist auf **MyTable** festgelegt, die die Quelldaten enthält. 
- **linkedServiceName** ist auf **MyOnPremisesSQLDB** festgelegt. Dies ist der verknüpfte Dienst, den Sie für die lokale SQL-Datenbank erstellt haben.

Unter [Eigenschaften des lokalen SQL-Speicherorts](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Tabelle, die auf eine SQL Server-Tabelle verweist.

### JSON für Ausgabetabelle
Das folgende JSON-Skript definiert die Ausgabetabelle **MyAzureBlob**, die auf das Azure-Blob **MyBlob** im Blob-Ordner **MySubFolder** im Blob-Container **MyContainer** verweist.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Beachten Sie Folgendes:
 
- **type** ist auf **AzureBlobLocation** festgelegt.
- **folderPath** ist auf **MyContainer/MySubFolder** festgelegt, den Speicherort des Blobs mit den kopierten Daten. 
- **fileName** ist auf **MyBlob** festgelegt, das Blob mit den Ausgabedaten.
- **linkedServiceName** ist auf **MyAzureStorge** festgelegt, den verknüpften Dienst, den Sie für den Azure-Speicher erstellt haben.    

Unter [Eigenschaften des Azure-Blob-Speicherorts](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Tabelle, die auf ein Azure-Blob verweist.

### JSON für Pipeline (mit Kopieraktivität)
In diesem Beispiel, eine Pipeline: **CopyActivityPipeline** ist mit den folgenden Eigenschaften definiert:

- Die **type**-Eigenschaft wird auf **CopyActivity** festgelegt.
- **MyOnPremTable** wird als Eingabe angegeben (**Eingaben**-Tag).
- **MyAzureBlob** wird als Ausgabe angegeben (**Ausgaben**-Tag)
- Der Abschnitt **Transformation** enthält zwei Unterabschnitte: **Quelle** und **Senke**. Für den Typ der Quelle wird **SqlSource** und für den Typ der Senke **BlobSink** festgelegt. **sqlReaderQuery** definiert die Transformation (Projektion), die für die Quelle ausgeführt werden soll. Ausführliche Informationen zu allen Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

Unter [Pipelines und Aktivitäten](https://msdn.microsoft.com/library/dn834988.aspx) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Pipeline. Unter [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx) finden Sie Eigenschaften von "SqlSource" (z. B. **sqlReaderQuery**) und "BlobSink".


## Kopieren von Daten aus einem lokalen Dateisystem in ein Azure-Blob
Mithilfe der Kopieraktivität können Sie Dateien aus einem lokalen Dateisystem (Windows-/Linux-Netzwerkfreigaben oder lokaler Windows-Host) in ein Azure-Blob kopieren. Der Host kann entweder ein Windows- oder Linux-Host sein, für den Samba konfiguriert ist. Das Datenverwaltungsgateway muss auf einem Windows-Computer installiert werden, der eine Verbindung mit dem Host herstellen kann.

### Annahmen
Bei diesem Beispiel wird Folgendes vorausgesetzt:

- **Host** – Name des Servers, auf dem das Dateisystem gehostet wird: **\contoso**.
- **Ordner** – Name des Ordners mit den Eingabedateien: **"marketingcampaign\regionaldata\{slice}". Hier sind Dateien in einem Ordner mit dem Namen "{slice}" aufgeteilt, wie z. B. 2014121112 (2014, 12. Monat, 11. Tag , 12. Stunde). 
### Erstellen eines mit dem lokalen Dateisystem verknüpften Diensts
Im folgenden Beispiel kann JSON zum Erstellen eines verknüpften Diensts namens **FolderDataStore** vom Typ **OnPremisesFileSystemLinkedService** verwendet werden.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]Denken Sie daran, für Host- und Ordnernamen in JSON-Dateien das Escapezeichen "\" zu verwenden. Für **\Contoso** verwenden Sie **\\Contoso**.

Unter [Mit dem lokalen Dateisystem verknüpfter Dienst](https://msdn.microsoft.com/library/dn930836.aspx) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren eines mit dem lokalen Dateisystem verknüpften Diensts.

### Erstellen eines verknüpften Dienst für das Azure-Ziel-Blob
Im folgenden Beispiel kann JSON zum Erstellen eines verknüpften Diensts namens **MyAzureStorage** mit dem Typ **AzureStorageLinkedService** verwendet werden.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Unter [Mit Azure-Speicher verknüpfter Dienst](https://msdn.microsoft.com/library/dn893522.aspx) finden Sie Details zu JSON-Elementen zum Definieren eines mit Azure-Speicher verknüpften Diensts.

### Erstellen der Eingabetabelle
Das folgende JSON-Skript definiert eine Eingabetabelle, die auf einen mit dem lokalen Dateisystem verknüpften Dienst verweist, den Sie zuvor erstellt haben.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

Unter [Eigenschaften des Speicherorts des lokalen Dateisystems](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Tabelle, die auf ein lokales Dateisystem verweist.

### Erstellen der Ausgabetabelle
Das folgende JSON-Skript definiert die Ausgabetabelle **AzureBlobDest**, die auf das Azure-Blob **MyBlob** im Blob-Ordner **MySubFolder** im Blob-Container **MyContainer** verweist.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Unter [Eigenschaften des Azure-Blob-Speicherorts](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Tabelle, die auf ein Azure-Blob verweist.

### Erstellen der Pipeline
Die folgende JSON-Pipeline definiert eine Pipeline mit einer Kopieraktivität, die Daten aus dem lokalen Dateisystem in das Azure-Ziel-Blob kopiert.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

Die Pipeline in diesem Beispiel kopiert den Inhalt im Binärformat, ohne dass Analysen oder Transformationen erfolgen. Beachten Sie, dass Sie die **Parallelität** nutzen können, um Slices von Dateien parallel zu kopieren. Dies ist hilfreich, wenn Sie die Slices verschieben möchten, die bereits in der Vergangenheit liegen.

> [AZURE.NOTE]Parallele Kopieraktivitäten mit dem gleichen Host über einen UNC-Pfad mit unterschiedlichen Benutzerkonten können zur folgenden Fehlermeldungen führen: "Mehrfache Verbindungen zu einem Server oder einer freigegebenen Ressource von demselben Benutzer unter Verwendung mehrerer Benutzernamen sind nicht zulässig". Diese Einschränkung des Betriebssystems gilt aus Sicherheitsgründen. Planen Sie die Kopieraktivitäten mit verschiedenen Gateways, oder installieren Sie das Gateway im Host, und verwenden Sie "localhost" oder "local" anstelle des UNC-Pfads.

Unter [Pipelines und Aktivitäten](https://msdn.microsoft.com/library/dn834988.aspx) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Pipeline. Unter [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx) finden Sie Eigenschaften von "FileSystemSource" und "BlobSink".

### Weitere Szenarien für die Verwendung von Dateisystemtabellen

#### Kopieren aller Dateien in einen bestimmten Ordner
Beachten Sie, dass nur **folderPath** im JSON-Beispiel angegeben ist.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Kopieren aller CSV-Dateien in einem bestimmten Ordner
Beachten Sie, dass **fileFilter** auf ***.csv** festgelegt ist.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Kopieren einer bestimmten Datei
Beachten Sie, dass **fileFiter** auf eine bestimmte Datei festgelegt ist: **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Kopieren von Daten aus einer lokalen Oracle-Datenbank in ein Azure-Blob
Mithilfe der Kopieraktivität können Sie Dateien aus einer lokalen Oracle-Datenbank in ein Azure-Blob kopieren.

### Erstellen eines verknüpften Diensts für eine lokale Oracle-Datenbank
Der folgende JSON-Code kann verwendet werden, um einen verknüpften Dienst zu erstellen, der auf eine lokale Oracle-Datenbank verweist. Beachten Sie, dass **type** auf **OnPremisesOracleLinkedService** festgelegt ist.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Unter [Mit lokaler Oracle-Datenbank verknüpfter Dienst](https://msdn.microsoft.com/library/dn948537.aspx) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren eines mit einer lokalen Oracle-Datenbank verknüpften Diensts.

### Erstellen eines verknüpften Dienst für das Azure-Ziel-Blob
Im folgenden Beispiel kann JSON zum Erstellen eines verknüpften Diensts namens **MyAzureStorage** mit dem Typ **AzureStorageLinkedService** verwendet werden.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Unter [Mit Azure-Speicher verknüpfter Dienst](https://msdn.microsoft.com/library/dn893522.aspx) finden Sie Details zu JSON-Elementen zum Definieren eines mit Azure-Speicher verknüpften Diensts.

### Erstellen der Eingabetabelle
Im folgende Beispiel kann JSON zum Erstellen einer Azure Data Factory-Tabelle verwendet werden, die auf eine Tabelle in einer lokalen Oracle-Datenbank verweist. Beachten Sie, dass **location: type** auf **OnPremisesOracleTableLocation** festgelegt ist.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

Unter [Eigenschaften des Speicherorts der lokalen Oracle-Datenbank](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Tabelle, die auf eine lokale Oracle-Datenbank verweist.

### Erstellen der Ausgabetabelle
Das folgende JSON-Skript definiert die Ausgabetabelle **MyAzureBlob**, die auf das Azure-Blob **MyBlob** im Blob-Ordner **MySubFolder** im Blob-Container **MyContainer** verweist.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Unter [Eigenschaften des Azure-Blob-Speicherorts](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Tabelle, die auf ein Azure-Blob verweist.

### Erstellen der Pipeline
Die folgende Beispielpipeline enthält eine Kopieraktivität, die Daten aus einer Oracle-Datenbanktabelle in ein Azure-Speicher-Blob kopiert.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

Unter [Pipelines und Aktivitäten](https://msdn.microsoft.com/library/dn834988.aspx) finden Sie ausführliche Informationen zu JSON-Elementen zum Definieren einer Data Factory-Pipeline. Unter [Unterstützte Quellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx) finden Sie Eigenschaften von "OracleSource" und "BlobSink".

## Siehe auch

- [Kopieren von Daten mit Azure Data Factory][adf-copyactivity]
- [Kopieraktivität: Referenz zur JSON-Skripterstellung](https://msdn.microsoft.com/library/dn835035.aspx)
- [Video: Einführung in die Kopieraktivität mit Azure Data Factory][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=62-->