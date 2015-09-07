<properties 
	pageTitle="Verschieben von Daten aus MySQL | Azure Data Factory"
	description="Erfahren Sie, wie Sie Daten aus der MySQL-Datenbank mithilfe von Azure Data Factory verschieben."
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

# Verschieben von Daten aus MySQL mithilfe von Azure Data Factory

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in einer Azure Data Factory verwenden können, um Daten aus MySQL in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

Der Data Factory-Dienst unterstützt das Herstellen einer Verbindung mit lokalen MySQL-Datenquellen mithilfe des Datenverwaltungsgateways. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways.

**Hinweis:** Sie müssen das Gateway nutzen, um sich mit MySQL zu verbinden, auch wenn MySQL in Azure-IaaS-VMs gehostet wird. Wenn Sie versuchen, sich mit einer MySQL-Instanz zu verbinden, die in der Cloud gehostet wird, können Sie auch die Gateway-Instanz in der IaaS-VM installieren.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus MySQL in andere Datenspeicher und nicht das Verschieben aus anderen Datenspeichern in MySQL.

## Installation 
Damit sich das Datenverwaltungsgateway mit der MySQL-Datenbank verbindet, müssen Sie den [MySQL Connector/Net 6.6.5 für Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) auf dem System mit dem Datenverwaltungsgateway installieren.

## Beispiel: Kopieren von Daten aus MySQL in Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Das Beispiel kopiert Daten stündlich aus einem Abfrageergebnis in einer MySQL-Datenbank in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) ein.

**Mit MySQL verknüpfter Dienst**

	{
	  "name": "OnPremMySqlLinkedService",
	  "properties": {
	    "type": "OnPremisesMySql",
	    "typeProperties": {
	      "server": "<server name>",
	      "database": "<database name>",
	      "schema": "<schema name>",
	      "authenticationType": "<authentication type>",
	      "userName": "<user name>",
	      "password": "<password>",
	      "gatewayName": "<gateway>"
	    }
	  }
	}

**Mit Azure Storage verknüpfter Dienst**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**MySQL-Eingabedataset**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in MySQL erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.
	
	{
	    "name": "MySqlDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremMySqlLinkedService",
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



**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

	{
	    "name": "AzureBlobMySqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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



**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query**-Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.
	
	{
	    "name": "CopyMySqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \'{0:yyyy-MM-ddTHH:mm:ss}\' AND timestamp < \'{1:yyyy-MM-ddTHH:mm:ss}\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "MySqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobMySqlDataSet"
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
	                "name": "MySqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Eigenschaften des mit MySQL verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit MySQL verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| Typ | Die "type"-Eigenschaft muss auf **OnPremisesMySql** festgelegt sein. | Ja |
| server | Name des MySQL-Servers. | Ja |
| database | Name der MySQL-Datenbank. | Ja | 
| schema | Name des Schemas in der Datenbank. | Nein | 
| authenticationType | Typ der Authentifizierung für die Verbindung mit der MySQL-Datenbank. Mögliche Werte: Anonymous, Basic und Windows. | Ja | 
| username | Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. | Nein | 
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. | Nein | 
| gatewayName | Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen MySQL-Datenbank verwenden soll. | Ja |

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für eine lokale MySQL-Datenquelle finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

## Eigenschaften des Dataset-Typs "MySQL"

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ **RelationalTable** (wozu ein MySQL-Dataset gehört) hat die folgenden Eigenschaften.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| tableName | Name der Tabelle in der MySQL-Datenbankinstanz, auf die der verknüpfte Dienst verweist. | Ja | 

## Eigenschaften von MySQL-Kopieraktivitätstyp

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität "source" den Typ **RelationalSource** hat (zu dem MySQL gehört), sind im Abschnitt "typeProperties" die folgenden Eigenschaften verfügbar:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| query | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. Beispiel: select * from MyTable. | Ja | 

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Typzuordnung für MySQL

Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in MySQL werden die folgenden Zuordnungen zwischen MySQL-Typ und .NET-Typ verwendet.

| Typ "MySQL-Datenbank" | Typ ".NET Framework" |
| ------------------- | ------------------- | 
| bigint (ohne Vorzeichen) | Decimal |
| bigint | Int64 |
| Bit | Decimal |
| Blob | Byte |
| bool | Boolean | 
| char | String |
| date | Datetime |
| datetime | Datetime |
| decimal | Decimal |
| double precision | Double |
| double | Double |
| enum | String |
| float | Single |
| int (ohne Vorzeichen) | Int64 |
| int | Int32 |
| integer (ohne Vorzeichen) | Int64 |
| integer | Int32 | 
| long varbinary | Byte |
| long varchar | String |
| longblob | Byte |
| longtext | String | 
| mediumblob | 	Byte | 
| mediumint (ohne Vorzeichen) | Int64 |
| mediumint | Int32 | 
| mediumtext | String |
| numeric | Decimal |
| real | Double |
| set | String |
| smallint (ohne Vorzeichen) | Int32 |
| smallint | Int16 |
| Text | String |
| time | TimeSpan |
| timestamp | Datetime |
| tinyblob | Byte |
| tinyint (ohne Vorzeichen) | Int16 | 
| tinyint | Int16 |
| tinytext | String | 
| varchar | String | 
| year | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=August15_HO9-->