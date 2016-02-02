<properties
	pageTitle="Indexer in Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erfahren Sie, wie Sie eine Datenbank per Crawler durchlaufen, um durchsuchbare Daten zu extrahieren und einen Azure Search-Index aufzufüllen."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/24/2016"
	ms.author="heidist"/>

# Indexer in Azure Search
> [AZURE.SELECTOR]
- [Overview](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [Blob Storage (preview)](search-howto-indexing-azure-blob-storage.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)

Ein **Indexer** in Azure Search ist ein sog. Crawler, mit dem durchsuchbare Daten und Metadaten aus einer externen Datenquelle extrahiert werden und ein Index basierend auf Feld-zu-Feld-Zuordnungen zwischen dem Index und Ihrer Datenquelle aufgefüllt wird. Dieser Ansatz wird auch als „Pullmodell“ bezeichnet, da der Dienst Daten abruft, ohne dass Sie Code zum Übertragen per Pushvorgang an einen Index schreiben müssen.

Sie können einen Indexer als alleiniges Mittel für die Datenerfassung verwenden, oder Sie können eine Kombination aus Verfahren nutzen, bei denen ein Indexer zum Laden eines Teils der Felder in Ihren Index verwendet wird.

Indexer können bei Bedarf oder nach einem Zeitplan für die regelmäßige Datenaktualisierung ausgeführt werden, z. B. alle 15 Minuten. Für häufigere Aktualisierungen ist ein Pushmodell erforderlich, bei dem Daten in Azure Search und Ihrer externen Datenquelle gleichzeitig aktualisiert werden.

Ein Indexer ruft Daten per Pullvorgang aus einer **Datenquelle** ab, die Informationen enthält, z. B. eine Verbindungszeichenfolge. Derzeit werden die folgenden Datenquellen unterstützt:

- [Azure SQL-Datenbank](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (oder SQL Server in einer virtuellen Azure-Maschine)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (Derzeit als Vorschauversion. Extrahiert Text aus PDF-, Office-Dokumenten, HTML-, XML-Dateien.)

Datenquellen werden unabhängig von den Indexern, die darauf zugreifen, konfiguriert und verwaltet. Dies bedeutet, dass eine Datenquelle von mehreren Indexern verwendet werden kann, um mehr als einen Index gleichzeitig zu laden.

Sowohl das [.NET-SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) als auch die [Dienst-REST-API](https://msdn.microsoft.com/library/azure/dn946891.aspx) unterstützen die Verwaltung von Indexern und Datenquellen. Alternativ hierzu können Sie einen Indexer auch im Portal konfigurieren, wenn Sie den Assistenten **Daten importieren** verwenden.

<!---HONumber=AcomDC_0128_2016-->