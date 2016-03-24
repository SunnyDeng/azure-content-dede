<properties 
	pageTitle="Verschieben von Daten in/aus DocumentDB | Microsoft Azure" 
	description="Erfahren Sie, wie Daten mithilfe von Azure Data Factory in und aus Azure DocumentDB verschoben werden." 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten in und aus DocumentDB mithilfe von Azure Data Factory

Dieser Artikel beschreibt die Verwendung der Kopieraktivität in einer Azure Data Factory, um Daten aus einem anderen Datenspeicher in Azure DocumentDB und aus Azure DocumentDB in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

In den folgenden Beispielen wird veranschaulicht, wie Sie Daten in und aus Azure DocumentDB und Azure-BLOB-Speicher kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.


## Beispiel: Kopieren von Daten aus DocumentDB in ein Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [DocumentDb](#azure-documentdb-linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Im Beispiel werden die Daten aus Azure DocumentDB in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure DocumentDB verknüpfter Dienst:**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure DocumentDB-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass eine Sammlung mit dem Namen **Person** in einer Azure DocumentDB-Datenbank vorhanden ist.
 
Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Azure Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob kopiert, wobei der Pfad des Blobs jeweils Datum und Uhrzeit mit stündlicher Genauigkeit angibt.

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

JSON-Beispieldokument in der Sammlung "Person" in einer DocumentDB-Datenbank:

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB unterstützt das Abfragen von Dokumenten mittels einer SQL-ähnlichen Syntax über hierarchische JSON-Dokumente.

Beispiel: "SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person"

Die folgende Pipeline kopiert Daten aus der Sammlung "Person" in der DocumentDB-Datenbank in ein Azure-Blob. Als Teil der Kopieraktivität wurden Eingabe- und Ausgabedataset angegeben.
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure DocumentDB

Das nachstehende Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [DocumentDb](#azure-documentdb-linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 
4. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) und [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties) verwendet


Im Beispiel werden Daten aus dem Azure-Blob in Azure DocumentDB kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure-Blobspeicher verknüpfter Dienst:**
	
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Mit Azure DocumentDB verknüpfter Dienst:**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Azure-Blob-Eingabedataset:**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Azure DocumentDB-Ausgabedataset:**

Im Beispiel werden Daten in eine Sammlung namens "Person" kopiert.

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

Die folgende Pipeline kopiert Daten aus dem Azure-Blob in die Sammlung "Person" in der DocumentDB-Datenbank. Als Teil der Kopieraktivität wurden Eingabe- und Ausgabedataset angegeben.
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
Wenn die Beispielblobeingabe folgendermaßen lautet:

	1,John,,Doe

Dann sieht die Ausgabe-JSON in "DocumentDB" so aus:

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
"DocumentDB" ist ein NoSQL-Speicher für JSON-Dokumente, in denen geschachtelte Strukturen zulässig sind. Azure Data Factory ermöglicht dem Benutzer, über einen **nestingSeparator**, in diesem Beispiel ".", eine Hierarchie anzugeben. Mit dem Trennzeichen generiert die Kopieraktivität das Objekt "Name" mit den drei untergeordneten Elementen "First", "Middle" und "Last" gemäß "Name.First", "Name.Middle" und "Name.Last" in der Tabellendefinition.

## Eigenschaften des mit Azure DocumentDB verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure DocumentDB verknüpften Dienst spezifisch sind.

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| -------- | ----------- | --------- |
| Typ | Die type-Eigenschaft muss auf **DocumentDb** festgelegt sein. | Ja |
| connectionString | Geben Sie die zum Verbinden mit der Azure DocumentDB-Datenbank erforderlichen Informationen ein. | Ja |

## Typeigenschaften des Azure DocumentDB-Datasets

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.
 
Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset des Typs **DocumentDbCollection** hat die folgenden Eigenschaften.

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| -------- | ----------- | -------- |
| collectionName | Der Name der DocumentDB-Dokumentsammlung. | Ja |


Beispiel:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

### Schema per Data Factory
Bei schemafreien Datenspeichern, z. B. DocumentDB, leitet der Data Factory-Dienst das Schema auf eine der folgenden Weisen ab:

1.	Wenn Sie die Struktur der Daten mithilfe der **structure**-Eigenschaft in der Datasetdefinition angeben, berücksichtigt der Data Factory-Dienst diese Struktur als das Schema. Wenn in diesem Fall eine Zeile keinen Wert für eine Spalte enthält, wird ein NULL-Wert für sie angegeben.
2.	Wenn Sie die Struktur der Daten nicht mithilfe der **structure**-Eigenschaft in der Datasetdefinition angeben, leitet der Data Factory-Dienst das Schema unter Verwendung der ersten Zeile in den Daten ab. Wenn in diesem Fall die erste Zeile nicht das vollständige Schema enthält, fehlen im Ergebnis des Kopiervorgangs einige Spalten.

Daher empfiehlt es sich bei schemafreien Datenquellen, die Struktur der Daten mithilfe der **structure**-Eigenschaft anzugeben.

## Typeigenschaften der Azure DocumentDB-Kopieraktivität

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.
 
**Hinweis**: Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität "source" den Typ **DocumentDbCollectionSource** hat, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| **Eigenschaft** | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Geben Sie die Abfrage an, um Daten zu lesen. | Die Abfragezeichenfolge wird durch DocumentDB unterstützt. <br/>Beispiel: SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"<br/> | Nein <br/><br/>Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: "select <columns defined in structure> from mycollection". 
| nestingSeparator | Sonderzeichen, um anzugeben, dass das Dokument geschachtelt ist. | Beliebiges Zeichen. <br/><br/>DocumentDB ist ein NoSQL-Speicher für JSON-Dokumente, in denen geschachtelte Strukturen zulässig sind. Azure Data Factory ermöglicht dem Benutzer, über einen "nestingSeparator", in den obigen Beispielen ".", eine Hierarchie anzugeben. Mit dem Trennzeichen generiert die Kopieraktivität das Objekt "Name" mit den drei untergeordneten Elementen "First", "Middle" und "Last" gemäß "Name.First", "Name.Middle" und "Name.Last" in der Tabellendefinition. | Nein

**DocumentDbCollectionSink** unterstützt die folgenden Eigenschaften:

| **Eigenschaft** | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Ein Sonderzeichen im Quellspaltennamen, um anzuzeigen, dass das geschachtelte Dokument erforderlich ist. <br/><br/>Zum Beispiel oben: "Name.First" in der Ausgabetabelle erzeugt die folgende JSON-Struktur im DocumentDB-Dokument:<br/><br/>"Name": {<br/> "First": "John"<br/>}, | Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird.<br/><br/>Standardwert ist "." (Punkt). | Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird. <br/><br/>Standardwert ist "." (Punkt). | Nein | 
| writeBatchSize | Anzahl der parallelen Anforderungen an den DocumentDB-Dienst zum Erstellen von Dokumenten.<br/><br/>Sie können die Leistung beim Kopieren von Daten in/aus DocumentDB mithilfe dieser Eigenschaft optimieren. Sie können eine bessere Leistung erwarten, wenn Sie "writeBatchSize" heraufsetzen, da mehr parallele Anforderungen an DocumentDB gesendet werden. Sie müssen jedoch eine Drosselung vermeiden, die zur Ausgabe einer Fehlermeldung führen kann: "Anforderungsrate ist hoch".<br/><br/>Die Drosselung hängt von einer Reihe von Faktoren ab, einschließlich Größe der Dokumente, Anzahl von Begriffen in Dokumenten, Indizierung der Richtlinie der Zielsammlung usw. Für Kopiervorgänge können Sie eine bessere Sammlung (z. B. S3) verwenden, um den optimalen verfügbaren Durchsatz zu erhalten (2.500 Anforderungseinheiten/Sekunde). | Ganzzahlwert | Nein |
| writeBatchTimeout | Die Wartezeit für den Abschluss des Vorgangs. | (Einheit = Zeitspanne) Beispiel: "00:30:00" (30 Minuten). | Nein |
 
## Anhang
1. **Frage:** Unterstützt die Kopieraktivität das Aktualisieren von vorhandenen Datensätzen?

	**Antwort:** Nein.

2. **Frage:** Wie wird bei einer Wiederholung eines Kopiervorgangs nach "DocumentDB" mit bereits kopierten Datensätzen umgegangen?

	**Antwort:** Wenn Datensätze über ein ID-Feld verfügen und beim Kopiervorgang versucht wird, einen Datensatz mit der gleichen ID einzufügen, löst der Kopiervorgang einen Fehler aus.
 
3. **Frage:** Unterstützt Data Factory die [bereichs- oder hashbasierte Datenpartitionierung]( https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)?

	**Antwort:** Nein. 
4. **Frage**: Können mehrere DocumentDB-Auflistungen für eine Tabelle angegeben werden?
	
	**Antwort:** Nein. Zurzeit kann nur eine Auflistung angegeben werden.
     

<!---HONumber=AcomDC_0309_2016-->