<properties
    pageTitle="Hochladen von Daten in Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Erfahren Sie, wie Sie Daten in einen Index in Azure Search hochladen können."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"/>

# Hochladen von Daten in Azure Search
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)


## Modelle zum Hochladen von Daten in Azure Search
Es gibt zwei Möglichkeiten zum Auffüllen Ihres Azure Search-Indexes mit Ihren Daten. Die erste Option ist die manuelle Push-Übertragung Ihrer Daten mithilfe der Azure Search [REST-API](search-import-data-rest-api.md) oder [.NET SDK](search-import-data-dotnet.md) in den Index. Die zweite Option besteht darin, [eine unterstützte Datenquelle auf Ihren Azure Search-Index zu verweisen](search-indexer-overview.md), damit Azure Search die Daten automatisch mithilfe von Pull in den Suchdienst abruft.

Dieses Handbuch behandelt nur die Verwendung des Push-Modells zum Hochladen von Daten (das nur in der [REST-API](search-import-data-rest-api.md) und [.NET SDK](search-import-data-dotnet.md) unterstützt wird), weiter unten finden Sie aber dennoch weitere Informationen zum Pull-Modell.

### Push-Übertragung von Daten in einen Index

Dieser Ansatz bezieht sich auf die programmgesteuerte Übertragung Ihrer Daten an Azure Search, damit diese für die Suche verfügbar sind. Bei Anwendungen mit sehr niedrigen Latenzanforderungen (wenn z. B. Suchvorgänge mit dynamischen Inventardatenbanken synchronisiert werden müssen) ist das Push-Modell Ihre einzige Option.

Sie können die [REST-API](https://msdn.microsoft.com/library/azure/dn798930.aspx) oder [.NET SDK](search-import-data-dotnet.md) zur Push-Übertragung von Daten in einen Index verwenden. Es gibt derzeit keine Toolunterstützung für die Push-Übertragung von Daten über das Portal.

Dieser Ansatz ist flexibler als ein Pull-Modell, da Sie Dokumente einzeln oder in Batches hochladen können (bis zu 1000 pro Batch oder 16 MB, je nachdem, welches Limit zuerst erreicht wird). Das Push-Modell ermöglicht Ihnen auch das Hochladen von Dokumenten in Azure Search, unabhängig davon, wo sich Ihre Daten befinden.

### Abrufen von Daten in einen Index mithilfe von Pull

Das Pull-Modell durchsucht eine unterstützte Datenquelle und lädt die Daten automatisch für Sie in den Azure Search-Index hoch. Da Änderungen und Löschvorgänge in vorhandenen Dokumenten nachverfolgt und neue Dokumente erkannt werden, ist es mithilfe von Indexern nicht mehr erforderlich, die Daten in Ihrem Index aktiv zu verwalten.

Diese Funktion wird in Azure Search über *Indexer* implementiert, die derzeit für [Blob Storage (Vorschau)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Azure SQL-Datenbank und SQL Server auf virtuellen Azure-Computern](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) verfügbar sind.

Die Indexer-Funktion steht sowohl im [Azure-Portal](search-import-data-portal.md) als auch in der [REST-API](https://msdn.microsoft.com/library/azure/dn946891.aspx) zur Verfügung.

<!---HONumber=AcomDC_0316_2016-->