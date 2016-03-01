<properties
	pageTitle="Verschieben von Daten von einem lokalen SQL Server zu SQL Azure mithilfe von Azure Data Factory | Azure"
	description="Einrichten eine ADF-Pipeline, die aus zwei Datenmigrationsaktivitäten besteht, die zusammen täglich Daten zwischen lokalen Datenbanken und in die Cloud verschieben."
	services="machine-learning"
	documentationCenter=""
	authors="fashah"
	manager="jacob.spoelstra"
	editor=""
	videoId=""
	scriptId="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="fashah;bradsev" />


# Verschieben von Daten von einem lokalen SQL Server zu SQL Azure mithilfe von Azure Data Factory

In diesem Thema wird gezeigt, wie Sie mithilfe von Azure Data Factory (ADF) Daten aus einer lokalen SQL Server-Datenbank über Azure Blob-Speicher in eine SQL Azure-Datenbank verschieben.

Das nachstehende **Menü** bietet Links zu Themen, in denen beschrieben wird, wie Daten in anderen Zielumgebungen erfasst werden, in denen die Daten während des Cortana Analytics-Prozesses (CAP) gespeichert und verarbeitet werden.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

## <a name="intro"></a>Einführung: Was ist ADF und wann sollte es für die Migration von Daten eingesetzt werden?

Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst, der das Verschieben und Transformieren von Daten orchestriert und automatisiert. Das Hauptkonzept im ADF-Modell ist die Pipeline. Eine Pipeline ist eine logische Gruppierung von Aktivitäten, von denen jede die Aktionen definiert, die mit den in Datasets enthaltenen Daten auszuführen sind. Verknüpfte Dienste werden verwendet, um die Informationen zu definieren, die Data Factory benötigt, um eine Verbindung mit den Datenressourcen herzustellen.

Mit ADF können vorhandene Datenverarbeitungsdienste zu Datenpipelines zusammengestellt werden, die hoch verfügbar sind und in der Cloud verwaltet werden. Diese Datenpipelines können geplant werden, um Daten zu erfassen, vorzubereiten, zu transformieren, zu analysieren und zu veröffentlichen, und all diese komplexen Daten und Verarbeitungsabhängigkeiten werden von ADF verwaltet und orchestriert. Lösungen lassen sich schnell in der Cloud erstellen und bereitstellen, wodurch eine wachsende Anzahl von lokalen und Clouddatenquellen miteinander verbunden werden.

Sie sollten die Verwendung von ADF in Betracht ziehen, wenn Daten in einem Hybridszenario kontinuierlich migriert werden müssen, das sowohl auf lokale als auch Cloudressourcen zugreift, und wenn die Daten Transaktionen unterworfen werden oder geändert werden müssen, oder wenn ihnen im Rahmen der Migration eine Geschäftslogik hinzugefügt wird. ADF gestatte die Planung und Überwachung von Aufträgen mithilfe einfacher JSON-Skripts, die das Verschieben von Daten in regelmäßigen Abständen verwalten. ADF verfügt außerdem über weitere Funktionen wie Unterstützung für komplexe Vorgänge. Weitere Informationen zu ADF finden Sie in der Dokumentation zu [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Das Szenario

Wir richten eine ADF-Pipeline ein, die aus zwei Datenmigrationsaktivitäten besteht, die zusammen täglich Daten zwischen einer lokalen SQL-Datenbank und einer Azure SQL-Datenbank in der Cloud verschieben. Die zwei Aktivitäten sind:

* Kopieren von Daten aus einer lokalen SQL Server-Datenbank in ein Azure Blob-Speicherkonto
* Kopieren von Daten aus dem Azure Blob-Speicherkonto in eine Azure SQL-Datenbank

**Referenz**: Die hier gezeigten Schritte wurden dem detaillierteren Tutorial [Aktivieren von Pipelines zum Arbeiten mit lokalen Daten](data-factory-use-onpremises-datasources.md) entnommen und angepasst, das vom ADF-Team bereitgestellt wird. Verweise auf die entsprechenden Abschnitte dieses Themas werden an geeigneten Stellen bereitgestellt .


## <a name="prereqs"></a>Voraussetzungen
In diesem Tutorial wird Folgendes vorausgesetzt:

* Ein **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein **Azure-Speicherkonto**. Sie benötigen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](storage-create-storage-account.md#create-a-storage-account). Nachdem Sie das Speicherkonto erstellt haben, müssen Sie den Kontoschlüssel für den Zugriff auf den Speicher abrufen. Siehe [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Zugriff auf eine **Azure SQL-Datenbank**. Wenn Sie eine Azure SQL-Datenbank einrichten müssen, finden Sie in [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md) Informationen dazu, wie Sie eine neue Instanz einer Azure SQL-Datenbank bereitstellen.
* Lokal installierte und konfigurierte **Azure PowerShell**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

> [AZURE.NOTE] In diesem Verfahren wird das [Azure-Portal](https://ms.portal.azure.com/) verwendet.

##<a name="upload-data"></a> Hochladen der Daten auf Ihren lokalen SQL Server

Wir verwenden das [NYC Taxi-Dataset](http://chriswhong.com/open-data/foil_nyc_taxi/), um den Migrationsprozess zu demonstrieren. Das NYC Taxi-Dataset ist, wie in diesem Beitrag erwähnt, in Azure Blob Storage unter [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/) verfügbar. Die Daten umfassen zwei Dateien, die Datei "trip\_data.csv", die Details zu den einzelnen Fahrten enthält, und die Datei "trip\_far.csv", die Details zu den pro Fahrt bezahlten Fahrpreisen enthält. Ein Beispiel und eine Beschreibung dieser Dateien finden Sie unter [Beschreibung des NYC Taxi Trips-Datasets](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Sie können entweder das hier beschriebene Verfahren auf einen Satz Ihrer eigenen Daten anpassen oder die Schritte wie beschrieben unter Verwendung des NYC Taxi-Datasets durchführen. Um das NYC Taxi-Dataset in Ihre lokale SQL Server-Datenbank hochzuladen, befolgen Sie das in [Massenimport von Daten in eine SQL Server-Datenbank](machine-learning-data-science-process-sql-walkthrough.md#dbload) beschriebene Verfahren. Diese Anleitungen gelten für einen SQL Server auf einem virtuellen Azure-Computer, aber das Verfahren zum Hochladen in auf den lokalen SQL Server ist identisch.

##<a name="create-adf"></a> Erstellen einer Azure Data Factory

Die Schritte zum Erstellen einer neuen Azure Data Factory und einer Ressourcengruppe im [Azure-Portal](https://ms.portal.azure.com/) finden Sie unter [Create an Azure Data Factory](data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Nennen Sie die neue ADF-Instanz *adfdsp*, und nennen Sie die erstellte Ressourcengruppe *adfdsprg*.

## Installieren und Konfigurieren das Datenverwaltungsgateways

Damit Ihre Pipelines in einer Azure Data Factory mit einem lokalen SQL Server zusammenarbeiten können, müssen Sie diesen der Data Factory als verknüpften Dienst hinzufügen. Um einen verknüpften Dienst für den lokalen SQL Server zu erstellen, müssen Sie zunächst das Microsoft-Datenverwaltungsgateway auf den lokalen Computer herunterladen und dort installieren und den verknüpften Dienst für die lokale Datenquelle zur Verwendung des Gateways konfigurieren. Das Datenverwaltungsgateway serialisiert und deserialisiert die Quell- und Senkendaten auf dem Computer, auf dem sie gehostet werden.

Anleitungen zur Einrichtung und Details zum Datenverwaltungsgateway finden Sie unter [Aktivieren von Pipelines zum Arbeiten mit lokalen Daten](data-factory-use-onpremises-datasources.md).


## <a name="adflinkedservices"></a>Erstellen von verknüpften Diensten zum Herstellen einer Verbindung mit den Datenressourcen

Ein verknüpfter Dienst definiert die Informationen, die Azure Data Factory für das Herstellen einer Verbindung mit einer Datenquelle benötigt. Die schrittweise Anleitung zum Erstellen von verknüpften Diensten finden Sie unter [Erstellen von verknüpften Diensten](data-factory-use-onpremises-datasources.md#step-2-create-linked-services).

Wir haben drei Ressourcen in diesem Szenario, für die verknüpfte Dienste erforderlich sind.

1. [Verknüpfter Dienst für lokalen SQL Server](#adf-linked-service-onprem-sql)
2. [Verknüpfter Dienst für Azure-Blob-Speicher](#adf-linked-service-blob-store)
3. [Verknüpfter Dienst für Azure SQL-Datenbank](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Verknüpfter Dienst für lokale SQL Server-Datenbank
Um den verknüpften Dienst für den lokalen SQL Server zu erstellen, klicken Sie im klassischen Azure-Portal auf der ADF-Startseite auf **Datenspeicher**, wählen Sie *SQL* aus, und geben Sie die Anmeldeinformationen für *Benutzername* und *Kennwort* für den lokalen SQL Server ein. Sie müssen den Servernamen in der Form **vollqualifizierter Servername umgekehrter Schrägstrich Instanzname (Servername\\Instanzname)** eingeben. Nennen Sie den verknüpften Dienst *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Verknüpfter Dienst für Blob
Um den verknüpften Dienst für das Azure Blob-Speicherkonto zu erstellen, klicken Sie im klassischen Azure-Portal auf der ADF-Startseite auf **Datenspeicher**, wählen Sie *Azure-Speicherkonto* aus, und geben Sie den Azure Blob-Speicherkontoschlüssel und den Containernamen ein. Nennen Sie den Dienst Link *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Verknüpfter Dienst für Azure SQL-Datenbank
Um den verknüpften Dienst für die Azure SQL-Datenbank zu erstellen, klicken Sie im klassischen Azure-Portal auf der ADF-Startseite auf **Datenspeicher**, wählen Sie *Azure SQL* aus, und geben Sie die Anmeldeinformationen für *Benutzername* und *Kennwort* für die Azure SQL-Datenbank ein. Der *Benutzername* muss im Format "*user@servername*" angegeben werden.


##<a name="adf-tables"></a>Definieren und Erstellen von Tabellen, um die Art des Zugriffs auf Datasets anzugeben

Erstellen Sie Tabellen, in denen die Struktur, der Speicherort und die Verfügbarkeit der Datasets angegeben werden, mit den folgenden skriptbasierten Verfahren. Zum Definieren der Tabellen werden JSON-Dateien verwendet. Weitere Informationen zur Struktur dieser Dateien finden Sie unter [Datasets](data-factory-create-datasets.md).

> [AZURE.NOTE]  Sie sollten das `Add-AzureAccount`-Cmdlet ausführen, bevor Sie das [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx)-Cmdlet ausführen, um sicherzustellen, dass das richtige Azure-Abonnement für die Ausführung der Befehle ausgewählt ist. Die Dokumentation zu diesem Cmdlet finden Sie unter [Add-AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

Die JSON-basierten Definitionen in den Tabellen verwenden die folgenden Namen:

* Der **Tabellenname** im lokalen SQL Server lautet *nyctaxi\_data*.
* Der **Containername** im Azure Blob-Speicherkonto lautet *containername*.  

Für diese ADF-Pipeline sind drei Tabellendefinitionen erforderlich:

1. [Lokale SQL-Tabelle](#adf-table-onprem-sql)
2. [Blob-Tabelle](#adf-table-blob-store)
3. [SQL Azure-Tabelle](#adf-table-azure-sql)

> [AZURE.NOTE]  Die folgenden Verfahren verwenden Azure PowerShell, um die ADF-Aktivitäten zu definieren und zu erstellen. Diese Aufgaben können Sie allerdings auch über das Azure-Portal ausführen. Detaillierte Informationen hierzu finden Sie unter [Erstellen von Datasets für Eingabe und Ausgabe](data-factory-use-onpremises-datasources.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Lokale SQL-Tabelle

Die Tabellendefinition für den lokalen SQL Server wird in der folgenden JSON-Datei angegeben.

    	{
	    	"name": "OnPremSQLTable",
	    	"properties":
	    	{
		    	"location":
		    	{
		    	"type": "OnPremisesSqlServerTableLocation",
		    	"tableName": "nyctaxi_data",
		    	"linkedServiceName": "adfonpremsql"
		    	},
		    	"availability":
		    	{
		    	"frequency": "Day",
		    	"interval": 1,   
		    	"waitOnExternal":
		    	{
		    	"retryInterval": "00:01:00",
		    	"retryTimeout": "00:10:00",
		    	"maximumRetry": 3
		    	}

		    	}
	    	}
    	}
Beachten Sie, dass die Spaltennamen hier nicht enthalten sind. Sie können eine Unterauswahl in den Spalten treffen, indem Sie sie hier aufnehmen (Details finden Sie in der [ADF-Dokumentation](data-factory-data-movement-activities.md)).

Kopieren Sie die JSON-Definition der Tabelle in eine Datei namens *onpremtabledef.json*, und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\\temp\\onpremtabledef.json* vorausgesetzt). Erstellen Sie die Tabelle in ADF mit dem folgenden Azure PowerShell-Cmdlet.

	New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Blob-Tabelle
Die Tabellendefinition für den Blob-Speicherort der Ausgabe finden Sie im Folgenden (hierbei werden die erfassten lokalen Daten dem Azure-Blob zugeordnet):

	    {
		    "name": "OutputBlobTable",
		    "properties":
		    {
			    "location":
			    {
			    "type": "AzureBlobLocation",
			    "folderPath": "containername",
			    "format":
			    {
			    "type": "TextFormat",
			    "columnDelimiter": "\t"
			    },
			    "linkedServiceName": "adfds"
			    },
			    "availability":
			    {
			    "frequency": "Day",
			    "interval": 1
			    }
		    }
	    }

Kopieren Sie die JSON-Definition der Tabelle in eine Datei namens *bloboutputtabledef.json*, und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\\temp\\bloboutputtabledef.json* vorausgesetzt). Erstellen Sie die Tabelle in ADF mit dem folgenden Azure PowerShell-Cmdlet.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure-Tabelle
Die Tabellendefinition für die SQL Azure-Ausgabe finden Sie im Folgenden (dieses Schema ordnet die vom Blob stammenden Daten zu):

	{
	    "name": "OutputSQLAzureTable",
	    "properties":
	    {
	        "structure":
	        [
				{ "name": "column1", type": "String"},
				{ "name": "column2", type": "String"}                
	        ],
	        "location":
	        {
	            "type": "AzureSqlTableLocation",
	            "tableName": "your_db_name",
	            "linkedServiceName": "adfdssqlazure_linked_servicename"
	        },
	        "availability":
	        {
	            "frequency": "Day",
	            "interval": 1            
	        }
	    }
	}

Kopieren Sie die JSON-Definition der Tabelle in eine Datei namens *AzureSqlTable.json*, und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\\temp\\AzureSqlTable.json* vorausgesetzt). Erstellen Sie die Tabelle in ADF mit dem folgenden Azure PowerShell-Cmdlet.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  

##<a name="adf-pipeline"></a>Definieren und Erstellen der Pipeline

Geben Sie die Aktivitäten an, die zu der Pipeline gehören, und erstellen Sie die Pipeline mit den folgenden skriptbasierten Verfahren. Zum Definieren der Pipeline-Eigenschaften wird eine JSON-Datei verwendet.

* Das Skript setzt voraus, dass der **Pipelinename** *AMLDSProcessPipeline* lautet.
* Beachten Sie außerdem, dass wir die Häufigkeit auf eine tägliche Ausführung festgelegt haben und die Standardausführungszeit des Auftrags verwenden (12 Uhr UTC).

> [AZURE.NOTE]  Die folgenden Verfahren verwenden Azure PowerShell zum Definieren und Erstellen der ADF-Pipeline. Diese Aufgabe können Sie allerdings auch über das Azure-Portal ausführen. Details finden Sie unter [Erstellen und Ausführen einer Pipeline](../data-factory/data-factory-use-onpremises-datasources.md#step-4-create-and-run-a-pipeline).

Unter Verwendung der oben angegebenen Tabellendefinitionen wird die Pipeline-Definition für die ADF wie folgt angegeben:

		{
		    "name": "AMLDSProcessPipeline",
		    "properties":
		    {
		        "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
		         "activities":
		        [
		            {
		                "name": "CopyFromSQLtoBlob",
		                "description": "Copy data from on-premise SQL server to blob",     
		                "type": "CopyActivity",
		                "inputs": [ {"name": "OnPremSQLTable"} ],
		                "outputs": [ {"name": "OutputBlobTable"} ],
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "SqlSource",
		                        "sqlReaderQuery": "select * from nyctaxi_data"
		                    },
		                    "sink":
		                    {
		                        "type": "BlobSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 3,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       

		             },

					{
						"name": "CopyFromBlobtoSQLAzure",
						"description": "Push data to Sql Azure",		
						"type": "CopyActivity",
						"inputs": [ {"name": "OutputBlobTable"} ],
						"outputs": [ {"name": "OutputSQLAzureTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink",
								"WriteBatchTimeout": "00:5:00",				
							}			
						},
						"Policy":
						{
							"concurrency": 3,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 2,
							"timeout": "02:00:00"
						}
				     }
		        ]
		    }
		}

Kopieren Sie diese JSON-Definition der Pipeline in eine Datei namens *pipelinedef.json*, und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\\temp\\pipelinedef.json* vorausgesetzt). Erstellen Sie die Pipeline in ADF mit dem folgenden Azure PowerShell-Cmdlet.

	New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Vergewissern Sie sich, dass die Pipeline im klassischen Azure-Portal auf der ADF wie folgt angezeigt wird (wenn Sie auf das Diagramm klicken).

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)

##<a name="adf-pipeline-start"></a>Starten der Pipeline
Die Pipeline kann jetzt mithilfe der folgenden Befehle ausgeführt werden:

	Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Die Parameterwerte *startdate* und *enddate* müssen durch die tatsächlichen Daten ersetzt werden, zwischen denen die Pipeline ausgeführt werden soll.

Sobald die Pipeline ausgeführt wird, sollten Sie die Daten sehen können, die im für den Blob ausgewählten Container angezeigt werden – immer eine Datei pro Tag.

Beachten Sie, dass wir nicht die Funktionalität von ADF zum inkrementellen Übertragen von Daten per Pipe genutzt haben. Weitere Detailinformationen zur Vorgehensweise sowie zu weiteren von ADF bereitgestellten Funktionen finden Sie in der [ADF-Dokumentation](https://azure.microsoft.com/services/data-factory/).

<!----HONumber=AcomDC_0211_2016-->