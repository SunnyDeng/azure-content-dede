<properties 
	pageTitle="Erweiterte Szenarien für die Verwendung der Kopieraktivität in Azure Data Factory" 
	description="Beschreibt erweiterte Szenarien für die Verwendung der Kopieraktivität in Azure Data Factory." 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Erweiterte Szenarien für die Verwendung der Kopieraktivität in Azure Data Factory 
## Übersicht
Mithilfe der **Kopieraktivität** in einer Pipeline können Sie Daten aus einer Quelle an eine Senke (Ziel) in einem Batch kopieren. Dieses Thema beschreibt die erweiterten Szenarien, die von der Kopieraktivität unterstützt werden. Eine ausführliche Übersicht über die Kopieraktivität und wichtige Szenarien, die davon unterstützt werden, finden Sie unter [Kopieren von Daten mit Azure Data Factory][adf-copyactivity].


## Strukturdefinition verwendende Spaltenfilterung
Je nach Typ der Tabelle ist es möglich, eine Teilmenge der Spalten aus der Datenquelle anzugeben, indem in der **Structure**-Definition der Tabellendefinition weniger Spalten angegeben werden als in der zugrunde liegenden Datenquelle vorhanden sind. Die folgende Tabelle enthält Informationen zur Spaltenfilterungslogik für verschiedene Arten von Tabellen.

<table>

	<tr>
		<th align="left">Tabellentyp</th>
		<th align="left">Spaltenfilterungslogik</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>Die <b>Structure</b>-Definition in der JSON-Tabelle muss der Struktur des Blobs entsprechen. Verwenden Sie zum Auswählen einer Teilmenge der Spalten das Spaltenzuordnungsfeature, das im nächsten Abschnitt beschrieben wird: Transformationsregeln – Spaltenzuordnung.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation und OnPremisesSqlServerTableLocation</td>
		<td align="left">
			Wenn die <b>SqlReaderQuery</b>-Eigenschaft im Rahmen der Definition der Kopieraktivität angegeben wird, sollte die <b>Structure</b>-Definition der Tabelle mit den in der Abfrage ausgewählten Spalten abgestimmt werden.<br/><br/>
			Wenn die <b>SqlReaderQuery</b>-Eigenschaft nicht angegeben wird, erstellt die Kopieraktivität automatisch eine SELECT-Abfrage, die auf den in der <b>Structure</b>-Definition der Tabellendefinition angegebenen Spalten basiert.
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			Der Abschnitt <b>Structure</b> in der Tabellendefinition kann den gesamten Satz oder eine Teilmenge der Spalten in der zugrunde liegenden Azure-Tabelle enthalten.
		</td>
	<tr>

</table>

## Transformationsregeln - Spaltenzuordnung
Die Spaltenzuordnung kann zur Angabe verwendet werden, wie Spalten in Quelltabellen zu Spalten in Senkentabellen zugeordnet werden. Sie unterstützt die folgenden Szenarien:

- Zuordnung aller Spalten in der Quelltabelle „Structure“ zur Zieltabelle „Structure“.
- Eine Teilmenge der Spalten in der Quelltabelle „Structure“ wird zu allen „Structure“ in der Zieltabelle zugeordnet.

Folgendes wird nicht unterstützt und löst eine Ausnahme aus:

- Entweder weniger oder mehr Spalten im Ziel.
- Doppelte Zuordnung.
- SQL-Abfrageergebnisses besitzen keinen Spaltennamen

Insbesondere würde die Kopieraktivität das Kopieren von Daten zwischen zwei Azure-Blobs hauptsächlich als eine direkte binäre Datenkopie behandeln, sofern nicht die folgenden drei Szenarien erfüllt werden:


1. Wenn die Eingabe- und Ausgabetabellen unterschiedliche Formate aufweisen, führt die Kopieraktivität eine Formatkonvertierung durch.
2. Wenn die Eingabetabelle als Ordner angegeben wird, der mehrere Dateien enthalten kann, die Ausgabetabelle hingegen als einzelne Datei angegeben wird, führt die Kopieraktivität die Dateien im Quellordner in einer einzigen Senkendatei zusammen.
3. Wenn "columnMapping" angegeben wird, führt die Kopieraktivität die entsprechende Datentransformation durch.


### Beispiel 1 – Spaltenzuordnung von SQL Server zu Azure-BLOB
In diesem Beispiel wird die **Eingabetabelle** wie folgt definiert. Die Eingabetabelle verfügt über eine Struktur und diese verweist auf eine SQL-Tabelle in einer SQL Server-Datenbank.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
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

In diesem Beispiel wird die **Ausgabetabelle** wie folgt definiert. Die Ausgabetabelle verfügt über eine Struktur und verweist auf ein BLOB in einem Azure-BLOB-Speicher.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
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

Wenn Sie keinen **fileName** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die **partitionedBy**-Eigenschaft. Im folgenden Beispiel verwendet **folderPath** die Angaben für Jahr, Monat und Tag aus SliceStart (Startzeit des zu vearbeitenden Slices) und "fileName" die Angabe für Stunde aus SliceStart. Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### Beispiel – Definieren der Spaltenzuordnung
In diesem Beispiel wird eine Aktivität in einer Pipeline wie folgt definiert. Die Spalten der Quelle werden mithilfe der **Translator**-Eigenschaft den Spalten der Senke (**columnMappings**) zugeordnet.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Spaltenzuordnung][image-data-factory-column-mapping-1]

### Beispiel 2 – Spaltenzuordnung mit SQL-Abfrage von SQL Server zu Azure-BLOB
In diesem Beispiel wird eine SQL-Abfrage (im Vergleich zur Tabelle im vorherigen Beispiel) zum Extrahieren von Daten aus einem lokalen SQL Server verwendet, und Spalten aus Abfrageergebnissen werden zu Quellartefakten und dann zu Zielartefakten zugeordnet. Für dieses Beispiel gibt die Abfrage fünf Spalten zurück.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = '{0:yyyyMMdd-HH}'', SliceStart)"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Spaltenzuordnung 2][image-data-factory-column-mapping-2]

## Datentypbehandlung durch die Kopieraktivität

Die im Abschnitt "Structure" der Tabellendefinition angegebenen Datentypen werden nur für **BlobSource** berücksichtigt. In der folgenden Tabelle wird beschrieben, wie die Datentypen für andere Typen von Quellen und Senken behandelt werden.

<table>	
	<tr>
		<th align="left">Quelle/Senke</th>
		<th align="left">Logik zur Datentypbehandlung</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert sind, werden ignoriert. Auf der zugrunde liegenden SQL-Datenbank definierte Datentypen werden während der Kopieraktivität zum Extrahieren der Daten verwendet.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert sind, werden ignoriert. Die Datentypen für die zugrunde liegende Quelle und für das Ziel werden verglichen, und es wird eine implizite Typkonvertierung durchgeführt, falls Typkonflikte auftreten.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td>Beim Übertragen von <b>BlobSource</b> zu <b>BlobSink</b> findet keine Typentransformation statt. Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert sind, werden ignoriert. Für andere Ziele als <b>BlobSink</b> werden Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert wurden, berücksichtigt.<br/><br/>
		Wenn <b>Structure</b> in der Tabellendefinition nicht angegeben wird, hängt die Typbehandlung von der <b>format</b>-Eigenschaft von der <b>BlobSink</b>-Tabelle ab:
		<ul>
			<li> <b>TextFormat:</b> Alle Spaltentypen werden als Zeichenfolge behandelt, und für alle Spaltennamen wird "Prop_&lt;0-N>" festgelegt.</li> 
			<li><b>AvroFormat:</b> Verwendet die integrierten Spaltentypen und -namen in der Avro-Datei.</li> 
			<li><b>JsonFormat:</b> Alle Spaltentypen werden als Zeichenfolge behandelt und verwenden die integrierten Spaltennamen in der JSON-Datei.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert sind, werden ignoriert. Auf den zugrunde liegenden Eingabedaten definierte Datentypen werden verwendet. Für Spalten wird angegeben, dass sie für die Avro-Serialisierung NULL-Werte zulassen.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert sind, werden ignoriert. Auf der zugrunde liegenden Azure-Tabelle definierte Datentypen werden verwendet.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Datentypen, die im Abschnitt <b>Structure</b> der Tabellendefinition definiert sind, werden ignoriert. Auf den zugrunde liegenden Eingabedaten definierte Datentypen werden verwendet.</td>
	</tr>

</table>

**Hinweis:** Azure-Tabellen unterstützen nur eine begrenzte Anzahl von Datentypen. Informationen hierzu finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell][azure-table-data-type].

## Aufrufen der gespeicherten Prozedur für SQL-Senke
Beim Kopieren von Daten in SQL Server oder Azure SQL-Datenbank konnte eine vom Benutzer angegebene gespeicherte Prozedur konfiguriert und mit zusätzlichen Parametern aufgerufen werden.
### Beispiel
1. Definieren Sie die JSON der Ausgabetabelle wie folgt (verwenden Sie die Tabelle aus Azure SQL-Datenbank als Beispiel):

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. Definieren Sie den Abschnitt **SqlSink** in der JSON der Kopieraktivität wie folgt. Zum Aufrufen einer gespeicherten Prozedur beim Einfügen von Daten sind sowohl die **SqlWriterStoredProcedureName**- als auch die **SqlWriterTableType**-Eigenschaft erforderlich.

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

3. Definieren Sie die gespeicherte Prozedur in der Datenbank mit demselben Namen wie **SqlWriterStoredProcedureName**. Sie behandelt die Eingabedaten aus der angegebenen Quelle und fügt sie in die Ausgabetabelle ein. Beachten Sie, dass der Parametername der gespeicherten Prozedur mit dem **tableName** identisch sein sollte, der in der JSON-Datei der Tabelle definiert ist.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. Definieren Sie in der Datenbank den Tabellentyp mit demselben Namen wie **SqlWriterTableType**. Beachten Sie, dass das Schema des Tabellentyps mit dem Schema übereinstimmen sollte, das von den Eingabedaten zurückgegeben wird.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

Das Feature der gespeicherten Prozedur nutzt [Tabellenwertparameter][table-valued-parameters].

## Angeben der Codierung für Textdateien
Obwohl die UTF-8-Codierung sehr beliebt ist, verwenden Textdateien in Azure-Blob aus historischen Gründen häufig andere Codierungen. Anhand der **encodingName**-Eigenschaft können Sie die Codierung für Tabellen vom Typ "TextFormat" nach Codeseitennamen angeben. Eine Liste der gültigen Codierungsnamen finden Sie unter "Encoding.EncodingName-Eigenschaft". Beispiel: Windows-1250 oder Shift-JIS. Der Standardwert lautet "UTF-8". Gültige Codierungsnamen finden Sie in [Codierungsklasse](https://msdn.microsoft.com/library/system.text.encoding(v=vs.110).aspx).

## Weitere Informationen

- [Beispiele für die Verwendung der Kopieraktivität][copy-activity-examples]
- [Kopieren von Daten mit Azure Data Factory][adf-copyactivity]
- [Kopieraktivität: Referenz zur JSON-Skipterstellung](https://msdn.microsoft.com/library/dn835035.aspx)
- [Video: Einführung in die Kopieraktivität mit Azure Data Factory][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/en-us/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!---HONumber=GIT-SubDir-->