<properties title="Copy data with Azure Data Factory" pageTitle="Kopieren von Daten mit Azure Data Factory" description="Erfahren Sie, wie Sie in Azure Data Factory mit "Aktivität kopieren" Daten aus einer Datenquelle in eine andere kopieren." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Kopieren von Daten mit Azure Data Factory (Kopieraktivität)
Mithilfe der **Kopieraktivität** in einer Pipeline können Sie Daten aus einer Quelle an eine Senke (Ziel) in einem Batch kopieren. Die Kopieraktivität kann in den folgenden Szenarien verwendet werden:

- **Ingress zu Azure**. In diesem Szenario werden Daten aus einer lokalen Datenquelle (Beispiel: SQL Server) in einen Azure-Datenspeicher (Beispiel: Azure-BLOB, Azure-Tabelle oder Azure-SQL-Datenbank) für die folgenden Unterszenarien kopiert:
	- Sammeln von Daten an einem zentralen Ort in Azure zur weiteren Verarbeitung.
	- Migrieren von Daten aus lokalen oder Nicht-Azure-Cloud-Plattformen in Azure.
	- Archivieren oder Sichern von Daten in Azure für kosteneffizienten mehrstufigen Speicher.
- **Egress von Azure**. In diesem Szenario werden Daten von Azure (Beispiel: Azure-BLOB, Azure-Tabelle oder Azure-SQL-Datenbank) in lokale Data Marts und Data Warehouses (Beispiel: SQL-Server) für die folgenden Unterszenarien kopiert:
	- Übertragen von Daten in lokale Data Warehouses aufgrund mangelnder Unterstützung für Cloud Data Warehouses.
	- Übertragen von Daten in lokale Lösung, um vorhandene lokale Lösung oder Berichterstellungsinfrastruktur zu nutzen.
	- Archivieren oder Sichern von Daten in lokalen mehrstufigen Speicher.
- **Azure-zu-Azure-Kopie**. In diesem Szenario werden die auf verschiedene Azure-Datenquellen verteilten Daten in einem zentralen Azure-Datenspeicher aggregiert. Beispiele: Azure-Tabelle in Azure-BLOB, Azure-BLOB in Azure-Tabelle, Azure-Tabelle zu Azure-SQL, Azure-BLOB in Azure-SQL.

Unter [Erste Schritte mit Azure Data Factory][adfgetstarted] finden Sie ein Lernprogramm, das das Kopieren von Daten aus einem Azure-BLOB-Speicher mithilfe der Kopieraktivität in eine Azure-SQL-Datenbank veranschaulicht. Unter [Aktivieren von Pipelines für die Arbeit mit lokalen Daten][use-onpremises-datasources] finden Sie eine exemplarische Vorgehensweise, die das Kopieren von Daten aus einer lokalen SQL Server-Datenbank mithilfe der Kopieraktivität in einen Azure-BLOB-Speicher veranschaulicht.


## Kopieraktivität - Komponenten
Die Kopieraktivität enthält die folgenden Komponenten: 

- **Eingabetabelle**. Eine Tabelle ist ein Dataset, das über ein Schema verfügt und rechteckig ist. Die Komponente "Eingabetabelle" 
- beschreibt die Eingabedaten für die Aktivität, die Folgendes enthalten: Name der Tabelle, Typ der Tabelle und verknüpfter Dienst, der auf eine Datenquelle verweist, die diese Eingabedaten enthält.
- **Ausgabetabelle**. Die Ausgabetabelle beschreibt die Ausgabedaten für die Aktivität, die Folgendes enthalten: Name der Tabelle, Typ der Tabelle und verknüpfter Dienst, der auf eine Datenquelle verweist, die diese Ausgabedaten enthält.
- **Transformationsregeln**. Die Transformationsregeln geben an, wie Eingabedaten aus der Quelle extrahiert und Ausgabedaten in Senken usw. geladen werden...
 
Eine Kopieraktivität kann eine **Eingabetabelle** und eine **Ausgabetabelle** besitzen.

## JSON für Kopieraktivität
Eine Pipeline besteht aus mindestens einer Aktivität. Aktivitäten in den Pipelines werden über den Abschnitt **Aktivitäten[]** definiert. Der JSON-Wert für eine Pipeline sieht folgendermaßen aus:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Jede Aktivität innerhalb des Abschnitts **Aktivitäten** weist die folgende allgemeine Struktur auf. Die **type**-Eigenschaft sollte auf **CopyActivity** gesetzt sein. Die Kopieraktivität darf nur eine Eingabetabelle und eine Ausgabetabelle besitzen.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

Die folgende Tabelle beschreibt die Tags, die in einem Aktivitätsabschnitt verwendet werden. 

<table border="1">	
	<tr>
		<th align="left">Tag</th>
		<th align="left">Beschreibung</th>
		<th align="left">Erforderlich</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Der Name der Aktivität.</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Ein Text, der beschreibt, wofür die Aktivität verwendet wird.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Gibt den Typ der Aktivität an. <br/><br/>Der <b>type</b> sollte festgelegt werden auf <b>CopyActivity</b>.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Eingabetabellen, die von der Aktivität verwendet werden.  Geben Sie für die Kopieraktivität nur eine Eingabetabelle an.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Ausgabetabellen, die von der Aktivität verwendet werden.  Geben Sie für die Kopieraktivität nur eine Ausgabetabelle an.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Eigenschaften der Transformation sind vom Typ abhängig.  Die <b>Kopieraktivität</b> erfordert die Angabe einer <b>Quelle</b> und einer <b>Senke</b> innerhalb des Abschnitts <b>transformation</b> . Weiter unten in diesem Artikel erhalten Sie weitere Details. </td>
		<td>Y</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Wenn es nicht angegeben wird, werden Standardwerte verwendet.</td>
		<td>N</td>
	</tr>


</table>

Ausführliche Informationen zu JSON-Eigenschaften/-Tags finden Sie in der [JSON-Skriptreferenz][json-script-reference].

## Kopieraktivität - Beispiel
In diesem Beispiel werden eine Eingabe- und eine Ausgabetabelle definiert. Die Tabellen werden in einer Kopieraktivität in einer Pipeline verwendet, die Daten aus einer lokalen SQL Server-Datenbank in ein Azure-BLOB kopiert.

**Annahmen**
Auf die folgenden Azure Data Factory-Artefakte wird in folgenden JSON-Beispielskripts verwiesen:

* Ressourcengruppe namens **ADF**.
* Eine Azure Data Factory namens **CopyFactory**.
* Ein verknüpfter Dienst namens **MyOnPremisesSQLDB**, der auf eine lokale SQL Server-Datenbank verweist.
* Ein verknüpfter Dienst namens **MyAzureStorage**, der auf einen Azure-BLOB-Speicher verweist.

### JSON für Eingabetabelle
Das folgende JSON-Skript definiert eine Eingabetabelle, die auf eine SQL-Tabelle verweist: **MyTable** in einer lokalen SQL Server-Datenbank, die vom verknüpften Dienst **MyOnPremisesSQLDB** definiert wird. Beachten Sie, dass **Name** der Name der Azure Data Factory-Tabelle und **Tabellenname** der Name der SQL-Tabelle in einer SQL Server-Datenbank ist.

         
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

Der folgende Azure PowerShell-Befehl verwendet **New-AzureDataFactoryTable**, das eine JSON-Datei verwendet, die das obige Skript zum Erstellen einer Tabelle (**MyOnPremTable**) in einer Azure Data Factory enthält: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Weitere Informationen zu Data Factory-Cmdlets finden Sie in der [Cmdlet-Referenz][cmdlet-reference]. 

### JSON für Ausgabetabelle
Das folgende JSON-Skript definiert eine Ausgabetabelle: **MyDemoBlob**, das auf ein Azure-BLOB verweist: **MyBlob** im BLOB-Ordner: **MySubFolder** im BLOB-Container: **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
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

Der folgende Azure PowerShell-Befehl verwendet **New-AzureDataFactoryTable**, das eine JSON-Datei verwendet, die das obige Skript zum Erstellen einer Tabelle (**MyDemoBlob**) in einer Azure Data Factory enthält: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### JSON für Pipeline (mit Kopieraktivität)
In diesem Beispiel wird eine Pipeline: **CopyActivityPipeline** mit den folgenden Eigenschaften definiert: 

- Die **type**-Eigenschaft wird auf **CopyActivity** festgelegt.
- **MyOnPremTable** wird als Eingabe angegeben (**Eingaben**-Tag).
- **MyAzureBlob** wird als Ausgabe angegeben (**Ausgaben**-Tag)
- **Transformation** enthält zwei Unterabschnitte: **Quelle** und **Senke**. Für den Typ der Quelle wird **SqlSource** und für den Typ der Senke **BlobSink** festgelegt. **sqlReaderQuery** definiert die Transformation (Projektion), die für die Quelle ausgeführt werden soll. Weitere Informationen zu allen Eigenschaften finden Sie in der [JSON-Skriptreferenz][json-script-reference].

         
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


 Der folgende Azure PowerShell-Befehl verwendet **New-AzureDataFactoryPipeline**, das eine JSON-Datei verwendet, die das obige Skript zum Erstellen einer Pipeline (**CopyActivityPipeline**) in einer Azure Data Factory enthält: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## Unterstützte Eingaben und Ausgaben
Im obigen Beispiel wird im Abschnitt "Transformation" "SqlSource" als Quelle und "BlobSink" als Senke verwendet. In der folgende Tabelle werden die Quellen und Senken aufgeführt, die von der Kopieraktivität unterstützt werden. 

<table border="1">	
	<tr>
		<th><i>Senke/Quelle<i></th>
		<th>Azure Blob</th>
		<th>Azure-Tabelle</th>
		<th>Azure SQL-Datenbank</th>
		<th>Lokaler SQL Server</th>
		<th>SQL Server unter IaaS</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Azure-Tabelle</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Azure SQL-Datenbank</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>Lokaler SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server unter IaaS</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


In der folgende Tabelle werden Quellen- und Senkentypen aufgeführt, die in einer JSON-Datei für eine Pipeline verwendet werden können, die eine Kopieraktivität enthält.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">Quellentyp</th>
		<th align="left">Senkentyp</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Azure-Tabelle</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Azure SQL-Datenbank</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>Lokaler SQL Server</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL unter IaaS</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

In der folgende Tabelle sind die Eigenschaften aufgeführt, die von diesen Quellen und Senken unterstützt werden.

<table border="1">

	<tr>
	<th align="left">Quelle/Senke</th>
	<th align="left">Unterstützte Eigenschaft</th>
	<th align="left">Beschreibung</th>
	<th align="left">Zulässige Werte</th>
	<th align="left">Erforderlich</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Gibt an, ob Null oder eine leere Zeichenfolge als NULL-Wert behandelt wird.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>Gibt an, wie viele Zeilen übersprungen werden müssen.</td>
		<td>Ganzzahl zwischen "0" und "Max".</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten.</td>
		<td>Abfragezeichenfolge für Azure-Tabelle.<br/>Beispiel: "ColumnA Eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Gibt an, ob die Ausnahme ignoriert wird: "Tabelle ist nicht vorhanden".</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten.</td>
		<td>SQL-Abfragezeichenfolge.<br/><br/> Beispiel: "Select * from MyTable".<br/><br/> Wenn diese nicht angegeben ist, wählen Sie "<In Struktur definierte Spalten> from MyTable".</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>Standardmäßiger Partitionsschlüsselwert, der von der Senke verwendet werden kann.</td>
		<td>Ein Zeichenfolgenwert.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>Vom Benutzer angegebener Spaltenname, dessen Spaltenwerte als Partitionsschlüssel verwendet werden.<br/><br/> Wenn dieser nicht angegeben ist, wird "AzureTableDefaultPartitionKeyValue" als Partitionsschlüssel verwendet.</td>
		<td>Ein Spaltenname.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>Spaltenwerte des angegebenen Spaltennamens, die als Zeilenschlüssel verwendet werden.<br/><br/>Wenn diese nicht angegeben sind, wird für jede Zeile eine GUID verwendet.</td>
		<td>Ein Spaltenname.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>Der Modus zum Einfügen von Daten in die Azure-Tabelle.</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>Fügt Daten in die Azure-Tabelle ein, wenn "writeBatchSize" oder "writeBatchTimeout" erreicht wird.</td>
		<td>Ganzzahl zwischen 1 und 100 (Einheit = Zeilenanzahl)</td>
		<td>N<br/><br/>(Standard = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Fügt Daten in die Azure-Tabelle ein, wenn "writeBatchSize" oder "writeBatchTimeout" erreicht wird.</td>
		<td>(Einheit = Zeitspanne)<br/><br/>Beispiel: "00:20:00" (20 Minuten)</td>
		<td>N<br/><br/>(Standardeinstellung für Standardtimeoutwert von 90 Sekunden für Speicherclient)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>Gibt den Namen der gespeicherten Prozedur zum Aktualisieren/Einfügen (upsert) von Daten in die Zieltabelle an.</td>
		<td>Der Name einer gespeicherten Prozedur.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>Gibt den Tabellentyp an, der in der obigen gespeicherten Prozedur verwendet wird.</td>
		<td>Ein Tabellentypname.</td>
		<td>N</td>
	</tr>
</table>

### SQL unter IaaS (Infrastructure-as-a-Service)
Für SQL unter IaaS wird Azure als IaaS-Anbieter unterstützt. Die folgenden Netzwerk- und VPN-Topologien werden unterstützt. Beachten Sie, dass für Fall 2 und 3 das Datenverwaltungsgateway erforderlich ist, während es für den Fall 1 nicht erforderlich ist. Weitere Informationen zum Datenverwaltungsgateway finden Sie unter [Aktivieren von Pipelines für den Zugriff auf lokale Daten][use-onpremises-datasources].

1.	VM mit öffentlichem DNS-Namen und statischem, öffentlichen Port: private Portzuordnung
2.	VM mit öffentlichem DNS-Namen ohne bereitgestellten SQL-Endpunkt
3.	Virtuelles Netzwerk
	<ol type='a'>
	<li>Azure Cloud VPN with following topology at the end of the list. </li>	
	<li>VM with onpremises-to-cloud site-to-site VPN using Azure Virtual Network.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Column filtering using structure definition
Depending on the type of Table, it is possible to specify a subset of the columns from the source by specifying fewer columns in the **Structure** definition of the table definition than the ones that exist in the underlying data source. The following table provides information about column filtering logic for different types of table. 

<table>

	<tr>
		<th align="left">Tabellentyp</th>
		<th align="left">Spaltenfilterungslogik</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>Die <b>Strukturdefinition</b> in der JSON-Tabelle muss der Struktur des Blob entsprechen.  Verwenden Sie zum Auswählen einer Teilmenge der Spalten die Spaltenzuordnungsfunktion, die im nächsten Abschnitt beschrieben wird: Transformationsregeln - Spaltenzuordnung.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation und OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Wenn die Eigenschaft <b>SqlReaderQuery</b> als Teil der Definition der Kopieraktivität angegeben wird, muss die <b>Strukturdefinition</b> der Tabelle mit den in der Abfrage ausgewählten Spalten ausgerichtet werden.</p>
			<p>Wenn die Eigenschaft <b>SqlReaderQuery</b> nicht angegeben ist, konstruiert die Kopieraktivität automatisch eine SELECT-Abfrage, die auf den in der <b>Strukturdefinition</b> der Tabellendefinition angegebenen Spalten basiert.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			Der Abschnitt <b>Struktur</b> in der Tabellendefinition kann den gesamten Satz oder eine Teilmenge der Spalten in der zugrunde liegenden Azure-Tabelle enthalten.
		</td>
	<tr>

</table> 

## Transformationsregeln - Spaltenzuordnung
Die Spaltenzuordnung kann zur Angabe verwendet werden, wie Spalten in Quelltabellen zu Spalten in Senkentabellen zugeordnet werden. Sie unterstützt die folgenden Szenarien:

- Zuordnung aller Spalten in der Quelltabelle "Structure" zur Zieltabelle "Structure".
- Eine Teilmenge der Spalten in der Quelltabelle "Structure" wird zu allen "Structure" in der Zieltabelle zugeordnet.

Folgendes wird nicht unterstützt und löst eine Ausnahme aus: 

- Entweder weniger oder mehr Spalten im Ziel.
- Doppelte Zuordnung.
- SQL-Abfrageergebnisses besitzen keinen Spaltennamen

#### Beispiel 1 - Spaltenzuordnung von SQL Server zu Azure-BLOB
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

Wenn Sie keinen Dateinamen **fileName** für eine **Eingabetabelle** angeben, werden alle Dateien/Blobs aus dem Eingabeordner (**folderPath**) als Eingaben angesehen. Wenn Sie einen Dateinamen in der JSON-Datei angeben, wird nur die angegebene Datei/der angegebene Blob als Eingabe betrachtet. Beispiele finden Sie in den Beispieldateien des [Lernprogramms][adf-tutorial].

Wenn Sie keinen **Dateinamen** für eine **Ausgabetabelle** angeben, werden die generierten Dateien in **folderPath** im folgenden Format benannt: Data.<Guid>.txt (Beispiel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

Um **folderPath** und **fileName** dynamisch basierend auf der **SliceStart**-Zeit festzulegen, verwenden Sie die **partitionedBy**-Eigenschaft. Im folgenden Beispiel verwendet **folderPath** die Angaben für Jahr, Monat und Tag aus "SliceStart" (Startzeit des zu verarbeitenden Slices) und "fileName" die Angabe für Stunde aus "SliceStart". Wenn beispielsweise ein Slice für den Zeitpunkt "2014-10-20T08:00:00" erzeugt wird, wird "folderName" auf "wikidatagateway/wikisampledataout/2014/10/20" und "filName" auf "08.csv" festgelegt. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

In diesem Beispiel wird eine Aktivität in einer Pipeline wie folgt definiert. Die Spalten der Quelle werden (**columnMappings**) mithilfe der **Translator**-Eigenschaft zu Spalten der Senke zugeordnet.

##### Beispiel - Definieren der Spaltenzuordnung

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

![Column Mapping][image-data-factory-column-mapping-1]

##### Beispiel 2 - Spaltenzuordnung mit SQL-Abfrage von SQL Server zu Azure-BLOB
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
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

![Column Mapping 2][image-data-factory-column-mapping-2]

#### Datentypbehandlung durch die Kopieraktivität

Die im Abschnitt "Structure" der Tabellendefinition angegebenen Datentypen werden nur für **BlobSource** berücksichtigt.  In der folgenden Tabelle wird beschrieben, wie die Datentypen für andere Typen von Quellen und Senken behandelt werden.

<table>	
	<tr>
		<th align="left">Quelle/Senke</th>
		<th align="left">Logik zur Datentypbehandlung</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Im Abschnitt <b>Struktur</b> der Tabellendefinition definierte Datentypen werden ignoriert.  Auf der zugrunde liegenden SQL-Datenbank definierte Datentypen werden während der Kopieraktivität zum Extrahieren der Daten verwendet.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Im Abschnitt <b>Struktur</b> der Tabellendefinition definierte Datentypen werden ignoriert.  Die Datentypen für die zugrunde liegende Quelle und für das Ziel werden verglichen, und es wird eine implizite Typkonvertierung durchgeführt, falls Typkonflikte auftreten.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>Beim Übertragen von <b>BlobSource</b> zu <b>BlobSink</b>erfolgt keine Typumwandlung. Im Abschnitt <b>Struktur</b> der Tabellendefinition definierte Datentypen werden ignoriert.  Für andere Ziele als <b>BlobSink</b> werden die im Abschnitt <b>Struktur</b> der Tabellendefinition definierten Datentypen berücksichtigt.</p>
		<p>
		Wenn die <b>Struktur</b> in der Tabellendefinition nicht angegeben ist, hängt die Typbehandlung von der Eigenschaft <b>Format</b> von <b>BlobSink</b> ab:
		</p>
		<ul>
			<li> <b>TextFormat:</b> Alle Spaltentypen werden als Zeichenfolge behandelt und für alle Spaltennamen wird "Prop_<0-N>" festgelegt.</li> 
			<li><b>AvroFormat:</b> Verwendet die integrierten Spaltentypen und -namen in der Avro-Datei.</li> 
			<li><b>JsonFormat:</b> Alle Spaltentypen werden als Zeichenfolge behandelt und die integrierten Spaltennamen in der JSON-Datei verwendet.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Im Abschnitt <b>Struktur</b> der Eingabetabellendefinition definierte Datentypen werden ignoriert.  Auf den zugrunde liegenden Eingabedaten definierte Datentypen werden verwendet.  Für Spalten wird angegeben, dass sie für die Avro-Serialisierung NULL-Werte zulassen.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Im Abschnitt <b>Struktur</b> der Tabellendefinition definierte Datentypen werden ignoriert.  Auf der zugrunde liegenden Azure-Tabelle definierte Datentypen werden verwendet.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Im Abschnitt <b>Struktur</b> der Tabellendefinition definierte Datentypen werden ignoriert.  Auf den zugrunde liegenden Eingabedaten definierte Datentypen werden verwendet.</td>
	</tr>

</table>


## Exemplarische Vorgehensweisen
Unter [Erste Schritte mit Azure Data Factory][adfgetstarted] finden Sie ein Lernprogramm, das das Kopieren von Daten aus einem Azure-BLOB-Speicher mithilfe der Kopieraktivität in eine Azure-SQL-Datenbank veranschaulicht.
 
Unter [Aktivieren von Pipelines für die Arbeit mit lokalen Daten][use-onpremises-datasources] finden Sie eine exemplarische Vorgehensweise, die das Kopieren von Daten aus einer lokalen SQL Server-Datenbank mithilfe der Kopieraktivität in einen Azure-BLOB-Speicher veranschaulicht.



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=35.2-->
