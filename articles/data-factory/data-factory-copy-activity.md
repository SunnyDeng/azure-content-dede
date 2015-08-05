<properties 
	pageTitle="Kopieren von Daten mit Azure Data Factory" 
	description="Erfahren Sie, wie Sie mithilfe der Funktion zum Kopieren von Aktivitäten in Azure Data Factory Daten aus einer Datenquelle in eine andere Datenquelle kopieren." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Kopieren von Daten mit Azure Data Factory (Kopieraktivität)
## Übersicht
Mithilfe der **Kopieraktivität** in einer Pipeline können Sie Daten aus einer Quelle an eine Senke (Ziel) in einem Batch kopieren. Die Kopieraktivität kann in den folgenden Szenarien verwendet werden:

- **Eingehend in Azure**. In diesem Szenario werden Daten aus einer lokalen Datenquelle (ex: SQL Server) auf einen Azure-Datenspeicher (ex: Azure-Blob, Azure-Tabelle oder Azure SQL-Datenbank) für die folgenden untergeordneten Szenarien kopiert:
	- Sammeln von Daten an einem zentralen Ort in Azure zur weiteren Verarbeitung.
	- Migrieren von Daten aus lokalen oder Nicht-Azure-Cloud-Plattformen in Azure.
	- Archivieren oder Sichern von Daten in Azure für kosteneffizienten mehrstufigen Speicher.
- **Ausgehend von Azure**. In diesem Szenario werden Daten von Azure (ex: Azure-Blob, Azure-Tabelle oder Azure SQL-Datenbank) auf lokale Data Marts und Data Warehouses (ex: SQL-Server) für die folgenden untergeordneten Szenarien kopiert:
	- Übertragen von Daten in lokale Data Warehouses aufgrund mangelnder Unterstützung für Cloud Data Warehouses.
	- Übertragen von Daten in lokale Lösung, um vorhandene lokale Lösung oder Berichterstellungsinfrastruktur zu nutzen.
	- Archivieren oder Sichern von Daten in lokalen mehrstufigen Speicher.
- **Azure-zu-Azure-Kopie**. In diesem Szenario werden die auf verschiedene Azure-Datenquellen verteilten Daten in einem zentralen Azure-Datenspeicher aggregiert. Beispiele: Azure-Tabelle zu Azure-Blob, Azure-Blob zu Azure-Tabelle, Azure-Tabelle zu Azure-SQL, Azure-Blob zu Azure-SQL.

Weitere Informationen finden Sie hier:

- Sehen Sie sich das Video [Einführung in die Kopieraktivität mit Azure Data Factory][copy-activity-video] an.
- Unter [Erste Schritte mit Azure Data Factory][adfgetstarted] finden Sie ein Lernprogramm, das das Kopieren von Daten aus einem Azure-Blob-Speicher mithilfe der Kopieraktivität in eine Azure-SQL-Datenbank veranschaulicht. 
- Unter [Aktivieren von Pipelines für die Arbeit mit lokalen Daten][use-onpremises-datasources] finden Sie eine exemplarische Vorgehensweise, die das Kopieren von Daten aus einer lokalen SQL Server-Datenbank mithilfe der Kopieraktivität in einen Azure-Blob-Speicher veranschaulicht.


## Unterstützte Datenquellen und Senken
Die Kopieraktivität unterstützt folgende Datenbewegungen:

| *Quelle/Senke* | Azure Blob | Azure-Tabelle | Azure SQL-Datenbank | Azure DocumentDB | SQL Server auf Azure VM | Lokaler SQL Server |
| ------------- | ---------- | ----------- | ------------------ | ---------------- | ------------------ | ------------------- |
| Azure Blob | X | X | X | X | X | X |
| Azure-Tabelle | X | X | X | X | X | X |
| Azure SQL-Datenbank | X | X | X | X | X | X |
| Azure DocumentDB | X | X | X | | | |  
| Lokaler SQL Server | X | X | X | | X | X |
| SQL Server auf Azure VM | X | X | X | | X | X |
| Lokales Dateisystem | X | X | X | | X | X |
| Lokale Oracle-Datenbank | X | X | X | | X | X |
| Lokale MySQL-Datenbank| X | X | X | | X | X |
| Lokale DB2-Datenbank | X | X | X | | X | X |
| Lokale Teradata-Datenbank | X | X | X | | X | X |
| Lokale Sybase-Datenbank | X | X | X | | X | X |
| Lokale PostgreSQL-Datenbank | X | X | X | | X | X |


Weitere Informationen finden Sie im Thema [Unterstützte Datenquellen und Senken](https://msdn.microsoft.com/library/dn894007.aspx) in der MSDN Library.

### SQL unter IaaS (Infrastructure-as-a-Service)
SQL Server unter IaaS wird auch als Quelle und Senke unterstützt. Ein Datenverwaltungsgateway ist beim Erstellen eines verknüpften Diensts für SQL Server unter IaaS erforderlich. Sie sollten erwägen, das Datenverwaltungsgateway auf einem virtuellen Computer statt auf dem Computer mit SQL Server zu installieren. Dadurch können Leistungsabfälle vermieden werden, die entstehen, wenn SQL Server und das Gateway versuchen, die gleichen Ressourcen für sich zu beanspruchen. Weitere Informationen zum Datenverwaltungsgateway finden Sie unter [Aktivieren von Pipelines für den Zugriff auf lokale Daten][use-onpremises-datasources].

1.	VM mit öffentlichem DNS-Namen und statischem, öffentlichen Port: private Portzuordnung
2.	VM mit öffentlichem DNS-Namen ohne bereitgestellten SQL-Endpunkt
3.	Virtuelles Netzwerk
	<ol type='a'>
<li>Azure-Cloud-VPN mit folgender Topologie am Ende der Liste. </li>	
<li>VM mit lokal-zu-Cloud- und Standort-zu-Standort-VPN, das Azure Virtual Network verwendet.</li>	
</ol>![Data Factory mit Kopieraktivität][image-data-factory-copy-actvity]

## Kopieraktivität – Komponenten
Die Kopieraktivität enthält die folgenden Komponenten:

- **Eingabetabelle**. Eine Tabelle ist ein Dataset, das über ein Schema verfügt und rechteckig ist. Die Eingabetabellenkomponente beschreibt die Eingabedaten für die Aktivität, die Folgendes enthalten: Name der Tabelle, Typ der Tabelle und verknüpfter Dienst, der auf eine Datenquelle verweist, die diese Eingabedaten enthält.
- **Ausgabetabelle**. Die Ausgabetabelle beschreibt die Ausgabedaten für die Aktivität, die Folgendes enthalten: Name der Tabelle, Typ der Tabelle und verknüpfter Dienst, der auf eine Datenquelle verweist, die diese Ausgabedaten enthält.
- **Transformationsregeln**. Die Transformationsregeln geben an, wie Eingabedaten aus der Quelle extrahiert und Ausgabedaten in Senken usw. geladen werden…
 
Eine Kopieraktivität kann eine **Eingabetabelle** und eine **Ausgabetabelle** besitzen.

## <a name="CopyActivityJSONSchema"></a>JSON für Kopieraktivität
Eine Pipeline besteht aus mindestens einer Aktivität. Aktivitäten in den Pipelines werden über den Abschnitt **Aktivitäten** definiert. Der JSON-Wert für eine Pipeline sieht folgendermaßen aus:
         
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

| Tag | Beschreibung | Erforderlich |
|-----|-------------|----------|
|name|Der Name der Aktivität.|Y|
|description|Ein Text, der beschreibt, wofür die Aktivität verwendet wird.|J|
|type|Gibt den Typ der Aktivität an. Als Typ sollte **Copy** festgelegt sein. |J|
|inputs|Eingabetabellen, die von der Aktivität verwendet werden. Geben Sie für die Kopieraktivität nur eine Eingabetabelle an. | J
|outputs|Ausgabetabellen, die von der Aktivität verwendet werden. Geben Sie für die Kopieraktivität nur eine Ausgabetabelle an. | J
|transformation|Eigenschaften der Transformation sind vom Typ abhängig. Die Kopieraktivität erfordert die Angabe eines Abschnitts für die Quelle und eines für die Senke innerhalb des Abschnitts "Transformation". Weiter unten in diesem Artikel erhalten Sie weitere Details.|Y
|policy| Richtlinien, die das Laufzeitverhalten der Aktivität beeinflussen. Wenn es nicht angegeben wird, werden Standardwerte verwendet. | N

Ausführliche Informationen zu JSON-Eigenschaften/-Tags finden Sie in der [JSON-Skriptreferenz][json-script-reference].

### Quellen- und Senktypen
Die Liste der Quell- und Senktypen und den Eigenschaften, die von diesen Typen unterstützt werden, finden Sie im Thema [Unterstützte Quellen und Senken][msdn-supported-sources-sinks] in der MSDN Library.

## Kopieraktivität – Beispiel
In diesem Beispiel werden eine Eingabe- und eine Ausgabetabelle definiert. Die Tabellen werden in einer Kopieraktivität in einer Pipeline verwendet, die Daten aus einer lokalen SQL Server-Datenbank in ein Azure-BLOB kopiert.

**Annahmen:** Auf die folgenden Azure Data Factory-Artefakte wird in folgenden JSON-Beispielskripts verwiesen:

* Ressourcengruppe namens **ADF**.
* Eine Azure Data Factory namens **CopyFactory**.
* Ein verknüpfter Dienst namens **MyOnPremisesSQLDB**, der auf eine lokale SQL Server-Datenbank verweist.
* Ein verknüpfter Dienst namens **MyAzureStorage**, der auf einen Azure-Blob-Speicher verweist.

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

Der folgende Azure PowerShell-Befehl verwendet **New-AzureDataFactoryTable**, das eine JSON-Datei verwendet, die das obige Skript zum Erstellen einer Tabelle (**MyOnPremTable**) in einer Azure Data Factory enthält: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Weitere Informationen zu Data Factory-Cmdlets finden Sie in der [Cmdlet-Referenz][cmdlet-reference].

### JSON für Ausgabetabelle
Das folgende JSON-Skript definiert eine Ausgabetabelle: **MyDemoBlob**, die auf ein Azure-Blob verweist: **MyBlob** im Blob-Ordner: **MySubFolder** im Blob-Container: **MyContainer**.
         
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
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


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


 Der folgende Azure PowerShell-Befehl verwendet **New-AzureDataFactoryPipeline**, das eine JSON-Datei verwendet, die das obige Skript zum Erstellen einer Pipeline (**CopyActivityPipeline**) in einer Azure Data Factory enthält: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]Finden Sie unter [Beispiele für die Verwendung der Kopieraktivität in Azure Data Factory][copy-activity-examples] weitere Beispiele für die Verwendung der Kopieraktivität.

## Sicherheit
Dieser Abschnitt enthält allgemeine Richtlinien und bewährte Methoden, die dazu beitragen, sicheren Zugriff auf Datenspeicher für die Kopieraktivität herzustellen.

Für Datenspeicher mit HTTPS-Verbindung wählen Sie die HTTPS-Verbindung für die Kopieraktivität, um eine sichere Kommunikation über das Netzwerk herzustellen. Für den **Azure Storage** verwenden Sie beispielsweise **DefaultEndpointsProtocol = Https** in der Verbindungszeichenfolge.

Für die **Azure SQL-Datenbank** fordern Sie explizit eine verschlüsselte Verbindung an und vertrauen nicht den Serverzertifikaten, um einen Man-in-the-Middle-Angriff zu vermeiden. Um dies zu erreichen, verwenden Sie **Encrypt = True** und **TrustServerCertificate = False** in der Verbindungszeichenfolge. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](https://msdn.microsoft.com/library/azure/ff394108.aspx)

Für traditionelle Datenbanken, wie z. B. **SQL Server**, insbesondere wenn die Instanzen in einer Azure Virtual Machine sind, ist die verschlüsselte Verbindungsoption durch Konfigurieren eines signierten Zertifikats möglich, mit **Encrypt = True** und **TrustServerCertificate = False** in der Verbindungszeichenfolge. Weitere Informationen finden Sie unter [Aktivieren von verschlüsselten Verbindungen zum Datenbankmodul](https://msdn.microsoft.com/library/ms191192(v=sql.110)) und [Verbindungszeichenfolgensyntax](https://msdn.microsoft.com/library/ms254500.aspx).

## Erweiterte Szenarien
- **Strukturdefinition verwendende Spaltenfilterung**. Je nach Typ der Tabelle ist es möglich, eine Teilmenge der Spalten aus der Datenquelle anzugeben, indem in der **Structure**-Definition der Tabellendefinition weniger Spalten angegeben werden, als in der zugrunde liegenden Datenquelle vorhanden sind.
- **Transformationsregeln - Spaltenzuordnung**. Die Spaltenzuordnung kann zur Angabe verwendet werden, wie Spalten in Quelltabellen zu Spalten in Senkentabellen zugeordnet werden.
- **Datentypbehandlung durch die Kopieraktivität**. Erklärt, in welchem Fall die im Abschnitt "Struktur" angegebenen Datentypen der Tabellendefinition berücksichtigt/ignoriert werden.
- **Aufrufen der gespeicherten Prozedur für SQL-Senke**. Beim Kopieren von Daten in den SQL Server oder die Azure SQL-Datenbank konnte eine benutzerspezifische gespeicherte Prozedur konfiguriert und aufgerufen werden.

Im Artikel [Erweiterte Szenarien für die Verwendung der Kopieraktivität mit Azure Data Factory][copy-activity-advanced] finden Sie weitere Informationen zu diesen Szenarien.

## Exemplarische Vorgehensweisen
Unter [Erste Schritte mit Azure Data Factory][adfgetstarted] finden Sie ein Lernprogramm, das das Kopieren von Daten aus einem Azure-Blob-Speicher mithilfe der Kopieraktivität in eine Azure-SQL-Datenbank veranschaulicht.
 
Unter [Aktivieren von Pipelines für die Arbeit mit lokalen Daten][use-onpremises-datasources] finden Sie eine exemplarische Vorgehensweise, die das Kopieren von Daten aus einer lokalen SQL Server-Datenbank mithilfe der Kopieraktivität in einen Azure-Blob-Speicher veranschaulicht.

## Weitere Informationen
- [Kopieraktivität – Beispiele][copy-activity-examples]
- Video: [Einführung in die Kopieraktivität mit Azure Data Factory][copy-activity-video]
- [Thema "Kopieraktivität" in der MSDN Library][msdn-copy-activity]
- [Erweiterte Szenarien für die Verwendung der Kopieraktivität mit Azure Data Factory][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
 

<!---HONumber=July15_HO4-->