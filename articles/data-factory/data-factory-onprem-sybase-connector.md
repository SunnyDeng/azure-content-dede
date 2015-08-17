<properties 
	pageTitle="Sybase-Connector: Verschieben von Daten aus Sybase" 
	description="Informationen zum Sybase-Connector für den Data Factory-Dienst, mit dem Sie Daten aus einer Sybase-Datenbank verschieben können" 
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
	ms.date="07/27/2015" 
	ms.author="spelluru"/>


# Sybase-Connector: Verschieben von Daten aus Sybase 

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in einer Azure Data Factory verwenden können, um Daten aus Sybase in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

Der Data Factory-Dienst unterstützt das Herstellen einer Verbindung mit lokalen Sybase-Datenquellen mithilfe des Datenverwaltungsgateways. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways.

**Hinweis:** Sie müssen das Gateway nutzen, um sich mit Sybase zu verbinden, auch wenn Sybase in Azure-IaaS-VMs gehostet wird. Wenn Sie versuchen, sich mit einer Sybase-Instanz zu verbinden, die in der Cloud gehostet wird, können Sie auch die Gateway-Instanz in der IaaS-VM installieren.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus Sybase in andere Datenspeicher und nicht aus anderen Datenspeichern in Sybase.

## Installation

Damit sich das Datenverwaltungsgateway mit der Sybase-Datenbank verbindet, müssen Sie den [Datenanbieter für Sybase](http://go.microsoft.com/fwlink/?linkid=324846) auf dem System mit dem Datenverwaltungsgateway installieren.

## Beispiel: Kopieren von Daten aus Sybase in Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs "OnPremisesSybase"
2.	Einen verknüpften Dienst des Typs "AzureStorage"
3.	Ein Eingabedataset des Typs "RelationalTable"
4.	Ein Ausgabedataset des Typs "AzureBlob"
4.	Die Pipeline mit Kopieraktivität, die "RelationalSource" und "BlobSink" verwendet

Das Beispiel kopiert Daten stündlich aus einem Abfrageergebnis in einer Sybase-Datenbank in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) ein.

**Mit Sybase verknüpfter Dienst:**

	{
	    "name": "OnPremSybaseLinkedService",
	    "properties": {
	        "type": "OnPremisesSybase",
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


**Sybase-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Sybase erstellt haben, die für Zeitreihendaten eine Spalte namens "timestamp" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird der Data Factory angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird. Beachten Sie, dass **type** für den verknüpften Dienst auf **RelationalTable** festgelegt ist.
	
	{
	    "name": "SybaseDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremSybaseLinkedService",
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
	    "name": "AzureBlobSybaseDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die SQL-Abfrage für die Eigenschaft **query** wählt die Daten aus der Tabelle "DBA.Orders" in der Datenbank aus.


	{
	    "name": "CopySybaseToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from DBA.Orders"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "SybaseDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobSybaseDataSet"
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
	                "name": "SybaseToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Eigenschaften des mit Sybase verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Sybase verknüpften Dienst spezifisch sind.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Die "type"-Eigenschaft muss auf **OnPremisesSybase** festgelegt sein. | Ja
server | Name des Sybase-Servers. | Ja
database | Name der Sybase-Datenbank. | Ja 
schema | Name des Schemas in der Datenbank. | Nein
authenticationType | Typ der Authentifizierung für die Verbindung mit der Sybase-Datenbank. Mögliche Werte: Anonymous, Basic und Windows. | Ja
username | Geben Sie den Benutzernamen an, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. | Nein
passwort | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. | Nein
gatewayName | Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen Sybase-Datenbank verwenden soll. | Ja 

## Eigenschaften des Dataset-Typs "Sybase"

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset vom Typ **RelationalTable** (wozu ein Sybase-Dataset gehört) hat die folgenden Eigenschaften.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
tableName | Name der Tabelle in der Sybase-Datenbankinstanz, auf die der verknüpfte Dienst verweist. | Ja

## Eigenschaften von Sybase-Kopieraktivitätstyp 

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität "source" den Typ **RelationalSource** hat (zu dem Sybase gehört), sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
query | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. Beispiel: select \* from MyTable. | Nein

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Typzuordnung für Sybase

Wie im Artikel "Datenverschiebungsaktivitäten" beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Sybase unterstützt SQL- und T-SQL-Typen. Eine Tabelle mit der Zuordnung von SQL-Typen zu .NET- Typen finden Sie im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=August15_HO6-->