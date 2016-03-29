<properties 
   pageTitle="Datenszenarien in Zusammenhang mit dem Data Lake-Speicher | Microsoft Azure" 
   description="Kennen lernen der verschiedenen Szenarien und Tools, mit denen Daten, die in einem Data Lake-Speicher gespeichert sind, erfasst, verarbeitet, heruntergeladen und visualisiert werden können" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/09/2016"
   ms.author="nitinme"/>

# Datenszenarien im Zusammenhang mit dem Azure Data Lake-Speicher

Es gibt vier wichtige Phasen in der Big Data-Verarbeitung:

* Erfassung von großen Datenmengen in einem Datenspeicher, in Echtzeit oder in Batches
* Verarbeiten der Daten
* Herunterladen der Daten
* Visualisieren der Daten


In diesem Artikel betrachten wir diese Phasen in Bezug auf den Azure Data Lake-Speicher, um die Optionen und Tools kennen zu lernen, die zur Erfüllung Ihrer Big Data-Anforderungen verfügbar sind.

## Erfassen von Daten im Data Lake-Speicher

In diesem Abschnitt werden die unterschiedlichen Quellen von Daten hervorgehoben, sowie die verschiedenen Methoden, mit denen Daten in einem Data Lake-Speicherkonto erfasst werden können.

![Erfassen von Daten im Data Lake-Speicher](./media/data-lake-store-data-scenarios/ingest-data.png "Erfassen von Daten im Data Lake-Speicher")

### Ad-hoc-Daten:

Dies steht für kleinere Datasets, die zum Erstellen von Prototypen einer Big Data-Anwendung verwendet werden. Es gibt, abhängig von der Quelle der Daten, verschiedene Möglichkeiten zum Erfassen von Ad-hoc-Daten.

| Datenquelle | Erfassen Sie mit |
|--------------------|----------------------------------------------------------------------------------------|
| Lokalem Computer | <ul> <li>[Azure-Portal](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Plattformübergreifende Azure-CLI](data-lake-store-get-started-cli.md)</li> <li>[Verwendung von Data Lake-Tools für Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure Storage-Blob | <ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[AdlCopy-Tool](data-lake-store-copy-data-azure-storage-blob.md)</li> </ul> |

 
### Streamingdaten

Diese Daten können von verschiedenen Quellen wie Anwendungen, Geräten, Sensoren etc. generiert werden. Diese Daten können in einem Data Lake-Speicher von verschiedenen Tools erfasst werden. Diese Tools erfassen und verarbeiten die Daten in der Regel Ereignis für Ereignis in Echtzeit und schreiben dann die Ereignisse stapelweise in den Data Lake-Speicher, damit sie weiterverarbeitet werden können.

Folgende Tools können Sie verwenden:
 
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md). Sie können Daten aus dem Storm-Cluster direkt in den Data Lake-Speicher schreiben.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – Sie können Ereignisse von Event Hubs empfangen und dann mit dem [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md) in den Data Lake-Speicher schreiben.

### Relationale Daten

Sie können auch Daten aus relationalen Datenbanken erfassen. Relationale Datenbanken sammeln über einen Zeitraum hinweg umfangreiche Datenmengen, die bei Verarbeitung durch eine Big Data-Pipeline wichtige Einblicke bieten können. Mit den folgenden Tools können Sie solche Daten in den Data Lake-Speicher verschieben.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

### Webserverprotokolldaten (mit benutzerdefinierten Anwendungen hochladen)

Auf diesen Datasettyp wird besonders hingewiesen, da die Analyse von Webserverprotokolldaten eine gängige Praxis für Big Data-Anwendungen ist und voraussetzt, dass zahlreiche Protokolldateien in den Data Lake-Speicher hochgeladen werden. Sie können jedes der folgenden Tools nutzen, um eigene Skripts oder Anwendungen zum Hochladen solcher Daten zu schreiben.

* [Plattformübergreifende Azure-CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK für den Azure Data Lake-Speicher](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

Für das Hochladen von Webserverprotokolldaten und auch anderer Arten von Daten (z. B. soziale Stellungnahmen) ist es ein guter Ansatz, wenn Sie Ihre eigenen benutzerdefinierten Skripts/Anwendungen schreiben, denn dies gibt Ihnen die Flexibilität, Ihre Datenhochladekomponente als Teil in Ihre Big Data-Anwendung einzubeziehen. In einigen Fällen kann dieser Code die Form eines Skripts oder eines einfachen Befehlszeilenprogramms annehmen. In anderen Fällen kann der Code verwendet werden, die Big Data-Verarbeitung in eine Geschäftsanwendung oder -lösung zu integrieren.


### Mit Azure HDInsight-Clustern verknüpfte Daten

Die meisten HDInsight-Clustertypen (Hadoop, HBase, Storm) unterstützen den Data Lake-Speicher als Datenspeicherrepository. HDInsight-Cluster greifen aus Azure Storage-Blobs (WASB) auf Daten zu. Zur Verbesserung der Leistung können Sie die Daten aus dem WASB in ein mit dem Cluster verknüpftes Data Lake-Speicherkonto kopieren. Mit den folgenden Tools können Sie die Daten kopieren.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy-Dienst](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### Sehr große Datasets

Das Hochladen von Datasets im Bereich mehrerer Terabyte kann mithilfe der oben beschriebenen Methoden manchmal langsam und kostspielig sein. In solchen Fällen können Sie die folgenden Optionen verwenden.

* **„Offline“-Hochladen von Daten**. Sie können mit dem [Azure Import/Export-Dienst](../storage/storage-import-export-service.md) Festplatten mit Ihren Daten an ein Azure-Rechenzentrum verschicken, und dort werden die Daten dann in einen Azure Storage-Blob hochgeladen. Anschließend können Sie mit [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) oder dem [AdlCopy-Tool](data-lake-store-copy-data-azure-storage-blob.md) Daten von Azure Storage-Blobs in den Data Lake-Speicher verschieben.

	>[AZURE.NOTE] Wenn Sie den Import-/Export-Dienst verwenden, sollte die Größe der Dateien auf den Datenträgern, die Sie an das Azure-Rechenzentrum senden, nicht größer als 200 GB sein.

* **Verwenden von Azure ExpressRoute** Azure ExpressRoute ermöglicht Ihnen, private Verbindungen zwischen Azure-Rechenzentren und Ihrer lokalen Infrastruktur zu erstellen. Dies ist eine zuverlässige Option zur Übertragung großer Datenmengen. Weitere Informationen finden Sie unter [ExpressRoute - Technische Übersicht](../expressroute/expressroute-introduction.md).

## Verarbeiten von Daten, die im Data Lake-Speicher gespeichert sind

Sobald die Daten im Data Lake-Speicher verfügbar sind, können Sie mit den unterstützten Big Data-Anwendungen eine Analyse dieser Daten ausführen. Derzeit können Sie mit Azure HDInsight und Azure Data Lake Analytics Datenanalyseaufträge ausführen, die sich auf Daten im Data Lake-Speicher beziehen.

![Analysieren von Daten im Data Lake-Speicher](./media/data-lake-store-data-scenarios/analyze-data.png "Analysieren von Daten im Data Lake-Speicher")

Betrachten Sie die folgenden Beispiele.

* [Erstellen eines HDInsight-Clusters mit Data Lake-Speicher mithilfe des Azure-Portals](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## Herunterladen von Daten aus dem Data Lake-Speicher

Vielleicht möchten Sie auch für Szenarien wie die folgenden Daten aus dem Azure Data Lake-Speicher herunterladen oder verschieben:

* Verschieben von Daten aus anderen Repositorys zur Verbindung mit Ihren vorhandenen Datenverarbeitungspipelines. Sie möchten z. B. Daten aus dem Data Lake-Speicher in die Azure SQL-Datenbank oder auf einen lokalen SQL Server verschieben.
* Herunterladen von Daten auf Ihren lokalen Computer für die Verarbeitung in IDE-Umgebungen beim Erstellen von Anwendungsprototypen.

![Ausgehen von Daten aus dem Data Lake-Speicher](./media/data-lake-store-data-scenarios/egress-data.png "Ausgehen von Daten aus dem Data Lake-Speicher")

In solchen Fällen können Sie eine der folgenden Optionen verwenden:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Sie können auch mit den folgenden Methoden Ihr eigenes Skript/Ihre eigene Anwendung zum Herunterladen von Daten aus dem Data Lake-Speicher schreiben.

* [Plattformübergreifende Azure-CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [.NET SDK für den Azure Data Lake-Speicher](data-lake-store-get-started-net-sdk.md)

## Visualisieren von Daten im Data Lake-Speicher

Sie können eine Kombination von Diensten verwenden, um visuelle Darstellungen der Daten zu erstellen, die im Data Lake-Speicher gespeichert sind.

![Visualisieren von Daten im Data Lake-Speicher](./media/data-lake-store-data-scenarios/visualize-data.png "Visualisieren von Daten im Data Lake-Speicher")

* Sie können beginnen, indem Sie [Azure Data Factory verwenden, um Daten aus dem Data Lake-Speicher in Azure SQL Data Warehouse zu verschieben](../data-factory/data-factory-data-movement-activities.md#supported-data-stores).
* Danach können Sie [Power BI in Azure SQL Data Warehouse integrieren](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi), um eine visuelle Darstellung der Daten zu erstellen.

<!---HONumber=AcomDC_0316_2016-->