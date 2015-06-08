<properties 
	pageTitle="Beispiele für die Verwendung der Kopie-Aktivität in Azure Data Factory" 
	description="Beispiele für Usin eine Kopie Aktivität in einer Factory Azure Daten an." 
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

# Beispiele für die Verwendung der Kopie-Aktivität in Azure Data Factory
Mithilfe der **Kopieraktivität** in einer Pipeline können Sie Daten aus einer Quelle an eine Senke (Ziel) in einem Batch kopieren. Dieses Thema enthält einige Beispiele für die Verwendung der Kopie-Aktivität in einer Pipeline Data Factory. Eine ausführliche Übersicht über die Kopieraktivität und wichtige Szenarien, die davon unterstützt werden, finden Sie unter [Kopieren von Daten mit Azure Data Factory][adf-copyactivity].

## Kopieren von Daten aus einer lokalen SQL Server-Datenbank in eine Azure-blob
In diesem Beispiel werden eine Eingabe- und eine Ausgabetabelle definiert. Die Tabellen werden in einer Kopieraktivität in einer Pipeline verwendet, die Daten aus einer lokalen SQL Server-Datenbank in ein Azure-BLOB kopiert.

### Annahmen
In diesem Beispiel wird davon ausgegangen, dass Sie bereits die folgenden Azure Data Factory Artefakte:

* Ressourcengruppe namens **ADF**.
* Eine Azure Data Factory namens **CopyFactory**.
* Mit dem Namen Data Management Gateway **Mygateway** wird erstellt und online ist.  

### Erstellen Sie einen verknüpften Dienst für die lokale SQL Server-Quelldatenbank
In diesem Schritt erstellen Sie einen verknüpften Dienst namens **MyOnPremisesSQLDB** die auf einer lokalen SQL Server-Datenbank verweist.

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

- **Typ** minFreeThreads auf **OnPremisesSqlLinkedService**.
- **ConnectionString** auf die Verbindungszeichenfolge für eine SQL Server-Datenbank festgelegt ist. 
- **GatewayName** auf den Namen des Datenverwaltungsgateways auf dem lokalen Computer installiert und registriert den Azure Data Factory-Service-Portal festgelegt ist. 

Finden Sie unter [verknüpften lokalen SQL-Dienst](https://msdn.microsoft.com/library/dn893523.aspx) ausführliche Informationen zu JSON Elemente zum Definieren einer lokalen SQL Service verknüpft.
 
### Erstellen Sie einen verknüpften Dienst für das Azure-Blob-Ziel
In diesem Schritt erstellen Sie einen verknüpften Dienst namens **MyAzureStorage** Azure Blob-Speicher verweist.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Beachten Sie Folgendes:

- **Typ** minFreeThreads auf **AzureStorageLinkedService**.
- **ConnectionString** - Kontonamen und Kennwort für den Azure-Speicher.

Finden Sie unter [verknüpfte Azure-Speicherdienst](https://msdn.microsoft.com/library/dn893522.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer Azure-Speicher verknüpft Service.

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

- **Typ** minFreeThreads auf **OnPremisesSqlServerTableLocation**.
- **TableName** minFreeThreads auf **MyTable**, die die Quelldaten enthält. 
- **LinkedServiceName** minFreeThreads auf **MyOnPremisesSQLDB**, die verknüpft, den Sie für den lokalen SQL-Datenbank erstellt haben.

Finden Sie unter [lokale SQL Standorteigenschaften](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) ausführliche Informationen zu JSON-Elemente zum Definieren einer Data Factory-Tabelle, die auf einer SQL Server-Tabelle verweist.

### JSON für Ausgabetabelle
Das folgende JSON-Skript definiert eine Ausgabetabelle: **MyAzureBlob**, die auf ein Azure-Blob verweist: **Meinblob** in den Blob-Ordner: **MySubFolder** im Blob-Container: **MyContainer**.
         
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
 
- **Typ** minFreeThreads auf **AzureBlobLocation**.
- **FolderPath** minFreeThreads auf **MyContainer/MySubFolder**, enthält das Blob, das die kopierten Daten enthält. 
- **Dateiname** minFreeThreads auf **Meinblob**, das Blob, das die Ausgabedaten enthalten wird.
- **LinkedServiceName** minFreeThreads auf **MyAzureStorge**, die verknüpft, den Sie für den Azure-Speicher erstellt haben.    

Finden Sie unter [Azure-Blob-Eigenschaften für Empfangsspeicherort](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) ausführliche Informationen zu JSON-Elemente zum Definieren einer Data Factory-Tabelle, die auf ein Azure-Blob verweist.

### JSON für Pipeline (mit Kopieraktivität)
In diesem Beispiel, eine Pipeline: **CopyActivityPipeline** ist mit den folgenden Eigenschaften definiert:

- Die **type**-Eigenschaft wird auf **CopyActivity** festgelegt.
- **MyOnPremTable** wird als Eingabe angegeben (**Eingaben**-Tag).
- **MyAzureBlob** wird als Ausgabe angegeben (**Ausgaben**-Tag)
- Der Abschnitt **Transformation** enthält zwei Unterabschnitte: **Quelle** und **Senke**. Für den Typ der Quelle wird **SqlSource** und für den Typ der Senke **BlobSink** festgelegt. **sqlReaderQuery** definiert die Transformation (Projektion), die für die Quelle ausgeführt werden soll. Weitere Informationen zu allen Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

         
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

Finden Sie unter [Pipeline JSON-Verweis](https://msdn.microsoft.com/library/dn834988.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer Pipeline Data Factory und [unterstützt Ereignisquellen und-senken](https://msdn.microsoft.com/library/dn894007.aspx) für Eigenschaften von SQLSource aus (z. B.: **SqlReaderQuery **im Beispiel) und BlobSink. 


## Kopieren von Daten aus einem lokalen Dateisystem in eine Azure-blob
Copy-Aktivität können Dateien von einem lokalen Dateisystem (Windows-/Linux-Netzwerkfreigaben oder lokalen Windows-Host) in Azure-Blob zu kopieren. Der Host kann entweder Windows oder Linux mit Samba konfiguriert sein. Data Management Gateway sollte auf einem Windows-Computer installiert werden, die auf dem Host zugreifen können.

### Annahmen
In diesem Beispiel wird Folgendes vorausgesetzt:

- **Host** -Namen des Servers, auf dem das Dateisystem gehostet wird: **\contoso**.
- **Ordner** -ist der Name des Ordners, der die Eingabedateien enthält: **Marketingcampaign\regionaldata\ {Slice}, in dem Dateien in einem Ordner mit dem Namen {Slice}, wie z. B. 2014121112 (Jahr 2014, 12 Monat, Tag 11, 12 Stunden) partitioniert werden. 
### Erstellen Sie eine lokale Datei verknüpften Systemdienst
Im folgenden Beispiel JSON kann zum Erstellen von verknüpften Diensts namens **FolderDataStore** des Typs **OnPremisesFileSystemLinkedService**.

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

> [AZURE.NOTE]Denken Sie daran, das Escapezeichen verwendet "für den Host und die Ordnernamen in JSON-Dateien. Für **\Contoso**, verwenden Sie **\\Contoso**.

Finden Sie unter [lokale Datei verknüpften Systemdienst](https://msdn.microsoft.com/library/dn930836.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren von einem lokalen Dateisystem verknüpft Service.

### Erstellen Sie einen verknüpften Dienst für das Azure-Blob-Ziel
Im folgenden Beispiel JSON kann zum Erstellen von verknüpften Diensts namens **MyAzureStorage** des Typs **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Finden Sie unter [verknüpfte Azure-Speicherdienst](https://msdn.microsoft.com/library/dn893522.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer Azure-Speicher verknüpft Service.

### Erstellen Sie die Eingabetabelle
Das folgende JSON-Skript definiert eine Eingabetabelle, die auf einer lokalen Datei verknüpften Systemdienst, den Sie zuvor erstellt haben.

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

Finden Sie unter [lokalen Dateisystem Standorteigenschaften](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) ausführliche Informationen zu JSON-Elemente zum Definieren einer Data Factory-Tabelle, die auf einem lokalen Dateisystem verweist.

### Die Ausgabe kann erstellen
Das folgende JSON-Skript definiert eine Ausgabetabelle: **AzureBlobDest**, die auf ein Azure-Blob verweist: **Meinblob** in den Blob-Ordner: **MySubFolder** im Blob-Container: **MyContainer**.
         
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

Finden Sie unter [Azure-Blob-Eigenschaften für Empfangsspeicherort](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) ausführliche Informationen zu JSON-Elemente zum Definieren einer Data Factory-Tabelle, die auf ein Azure-Blob verweist.

### Erstellen der pipeline
Die folgende JSON-Pipeline definiert eine Pipeline mit einer Kopie-Aktivität, die Daten aus dem lokalen Dateisystem in das Azure-Blob-Ziel kopiert.
 
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

Die Pipeline in diesem Beispiel kopiert den Inhalt als Binär, ohne Analyse oder irgendwelche Transformationen auszuführen. Beachten Sie, die Sie nutzen können **Parallelität** Slices Dateien parallel zu kopieren. Dies ist hilfreich, wenn verschieben, dass die Segmente in der Vergangenheit bereits geschehen soll.

> [AZURE.NOTE]Parallele Kopien Aktivitäten mit dem gleichen Host über UNC-Pfad mit unterschiedlichen Benutzerkonten können zu Fehlern führen, z. B. "mehrere Verbindungen zu einem Server oder einer freigegebenen Ressource von demselben Benutzer mit mehr als einen Benutzernamen, sind nicht zulässig". Dies ist die Einschränkung des Betriebssystems aus Sicherheitsgründen. Der Kopie Aktivitäten mit verschiedenen Gateways, oder installieren Sie das Gateway in den Host, und verwenden Sie "Localhost" oder "local" anstelle der UNC-Pfad.

Finden Sie unter [Pipeline JSON-Verweis](https://msdn.microsoft.com/library/dn834988.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer Pipeline Data Factory und [unterstützt Ereignisquellen und-senken](https://msdn.microsoft.com/library/dn894007.aspx) für Eigenschaften von FileSystemSource und BlobSink.

### Zusätzliche Szenarien für die Verwendung von File System-Tabellen

#### Kopieren Sie alle Dateien in einem bestimmten Ordner
Beachten Sie, dass nur **FolderPath** in das JSON-Beispiel angegeben ist.

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
 
#### Kopieren Sie alle CSV-Dateien unter dem Ordner
Beachten Sie, dass die **FileFilter** minFreeThreads auf ***CSV**.

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
Beachten Sie, dass die **FileFiter** für eine bestimmte Datei festgelegt ist: **201501.csv**.

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

## Kopieren von Daten aus einer lokalen Oracle-Datenbank in ein Azure-blob
Kopie-Aktivität können Dateien von einer lokalen lokalen Oracle-Datenbank in ein Azure-Blob zu kopieren.

### Erstellen Sie einen verknüpften Dienst für eine lokale Oracle-Datenbank
Die folgende JSON kann verwendet werden, einen verknüpften Dienst zu erstellen, der auf einer lokalen Oracle-Datenbank verweist. Beachten Sie, dass die **Typ** minFreeThreads auf **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Finden Sie unter [verknüpften lokalen Oracle-Dienst](https://msdn.microsoft.com/library/dn948537.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer lokalen Oracle verknüpft Service.

### Erstellen Sie einen verknüpften Dienst für das Azure-Blob-Ziel
Im folgenden Beispiel JSON kann zum Erstellen von verknüpften Diensts namens **MyAzureStorage** des Typs **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Finden Sie unter [verknüpfte Azure-Speicherdienst](https://msdn.microsoft.com/library/dn893522.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer Azure-Speicher verknüpft Service.

### Erstellen Sie die Eingabetabelle
Im folgende Beispiel JSON kann verwendet werden, um ein Azure Data Factory-Tabelle zu erstellen, die auf eine Tabelle in einer lokalen Oracle-Datenbank verweist. Beachten Sie, dass die **Speicherorttyp** minFreeThreads auf **OnPremisesOracleTableLocation**.

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

Finden Sie unter [lokalen Oracle Standorteigenschaften](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) ausführliche Informationen zu JSON-Elemente zum Definieren einer Data Factory-Tabelle, die auf eine Tabelle in einer lokalen Oracle-Datenbank verweist.

### Erstellen Sie die Ausgabetabelle
Das folgende JSON-Skript definiert eine Ausgabetabelle: **MyAzureBlob**, die auf ein Azure-Blob verweist: **Meinblob** in den Blob-Ordner: **MySubFolder** im Blob-Container: **MyContainer**.
         
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

Finden Sie unter [Azure-Blob-Eigenschaften für Empfangsspeicherort](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) ausführliche Informationen zu JSON-Elemente zum Definieren einer Data Factory-Tabelle, die auf ein Azure-Blob verweist.

### Erstellen der pipeline
Die folgende Beispiel-Pipeline verfügt über eine Kopie-Aktivität, die Daten aus einer Oracle-Datenbank-Tabelle in ein Azure-Speicher-Blob kopiert.

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

Finden Sie unter [Pipeline JSON-Verweis](https://msdn.microsoft.com/library/dn834988.aspx) ausführliche Informationen zu JSON-Elemente zum Definieren einer Pipeline Data Factory und [unterstützt Ereignisquellen und-senken](https://msdn.microsoft.com/library/dn894007.aspx) für Eigenschaften von OracleSource und BlobSink.

## Siehe auch

- [Kopieren von Daten mit Azure Data Factory][adf-copyactivity]
- [Kopieraktivität: Referenz zur JSON-Skipterstellung](https://msdn.microsoft.com/library/dn835035.aspx)
- [Video: Einführung in die Kopieraktivität mit Azure Data Factory][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir-->