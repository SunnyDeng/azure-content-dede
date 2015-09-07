<properties 
	pageTitle="Verschieben von Daten aus DB2 | Azure Data Factory"
	description="Erfahren Sie, wie Sie Daten aus der DB2-Datenbank mithilfe von Azure Data Factory verschieben."
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
	ms.date="08/26/2015"
	ms.author="spelluru"/>

# Verschieben von Daten aus DB2 mithilfe von Azure Data Factory
Dieser Artikel beschreibt, wie Sie die Kopieraktivität in einer Azure Data Factory verwenden können, um Daten aus DB2 in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

Data Factory unterstützt das Herstellen einer Verbindung mit lokalen DB2-Datenquellen mithilfe des Datenverwaltungsgateways. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways.

**Hinweis:** Sie müssen das Gateway nutzen, um sich mit DB2 zu verbinden, auch wenn DB2 in Azure-IaaS-VMs gehostet wird. Wenn Sie versuchen, sich mit einer DB2-Instanz zu verbinden, die in der Cloud gehostet wird, können Sie auch die Gateway-Instanz in der IaaS-VM installieren.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus DB2 in andere Datenspeicher und nicht aus anderen Datenspeichern in DB2.

## Installation 

Damit sich das Datenverwaltungsgateway mit der DB2-Datenbank verbindet, müssen Sie den [IBM DB2 Data Server-Treiber](http://go.microsoft.com/fwlink/p/?LinkID=274911) auf dem System mit dem Datenverwaltungsgateway installieren.

IBM hat bekannte Probleme bei der Installation des IBM DB2 Data Server-Treibers unter Windows 8 gemeldet, die zusätzliche Installationsschritte erforderlich machen. Weitere Informationen zum IBM DB2 Data Server-Treiber für Windows 8 finden Sie unter [http://www-01.ibm.com/support/docview.wss?uid=swg21618434](http://www-01.ibm.com/support/docview.wss?uid=swg21618434).

## Beispiel: Kopieren von Daten aus DB2 in Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 
5.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet 

Das Beispiel kopiert Daten stündlich aus einem Abfrageergebnis in einer DB2-Datenbank in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) ein.

**Mit DB2 verknüpfter Dienst:**

	{
	    "name": "OnPremDb2LinkedService",
	    "properties": {
	        "type": "OnPremisesDb2",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}


**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}

**DB2-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in DB2 erstellt haben, die für Zeitreihendaten eine Spalte namens "timestamp" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird der Data Factory angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird. Beachten Sie, dass **type** auf **RelationalTable** festgelegt ist.

	{
	    "name": "Db2DataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremDb2LinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}


**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

	{
	    "name": "AzureBlobDb2DataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

**Pipeline mit Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die SQL-Abfrage für die Eigenschaft **query** wählt die Daten aus der Tabelle "Orders" aus.

	{
	    "name": "CopyDb2ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "Orders""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Db2DataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobDb2DataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "Db2ToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Eigenschaften des mit DB2 verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit DB2 verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| type | Die "type"-Eigenschaft muss auf **OnPremisesDB2** festgelegt sein. | Ja |
| server | Name des DB2-Servers. | Ja |
| database | Name der DB2-Datenbank. | Ja |
| schema | Name des Schemas in der Datenbank. | Nein |
| authenticationType | Typ der Authentifizierung für die Verbindung mit der DB2-Datenbank. Mögliche Werte: Anonymous, Basic und Windows. | Ja |
| username | Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. | Nein |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. | Nein |
| gatewayName | Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen DB2-Datenbank verwenden soll. | Ja |

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für eine lokale DB2-Datenquelle finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).


## Eigenschaften des Dataset-Typs "DB2"

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ "RelationalTable "(wozu ein DB2-Dataset gehört) hat die folgenden Eigenschaften.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| tableName | Name der Tabelle in der DB2-Datenbankinstanz, auf die der verknüpfte Dienst verweist. | Ja |

## Eigenschaften von DB2-Kopieraktivitätstyp

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität "source" den Typ **RelationalSource** hat (zu dem DB2 gehört), sind im Abschnitt "typeProperties" die folgenden Eigenschaften verfügbar:


| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------- | -------------- |
| query | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. Beispiel: select * from MyTable. | Nein |

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Typzuordnung für DB2
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in DB2 werden die folgenden Zuordnungen zwischen DB2-Typ und .NET-Typ verwendet.

Typ "DB2-Datenbank" | Typ ".NET Framework" 
----------------- | ------------------- 
SmallInt | Int16
Integer | Int32
BigInt | Int64
Real | Single
Double | Double
Float | Double
Decimal | Decimal
DecimalFloat | Decimal
Numeric | Decimal
Date | Datetime
Time | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | String
VarChar | String
LongVarChar | String
DB2DynArray | String
Binary | Byte
VarBinary | Byte
LongVarBinary | Byte
Graphic | String
VarGraphic | String
LongVarGraphic | String
Clob | String
Blob | Byte
DbClob | String
SmallInt | Int16
Integer | Int32
BigInt | Int64
Real | Single
Double | Double
Float | Double
Decimal | Decimal
DecimalFloat | Decimal
Numeric | Decimal
Date | Datetime
Time | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=August15_HO9-->