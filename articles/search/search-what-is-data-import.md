<properties
	pageTitle="Datenimport in Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Hochladen von Daten in einen Index in Azure Search"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importieren von Daten in Azure Search
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Azure Search ist ein gehosteter Cloudsuchdienst, der eine Suchmaschine, Suchfunktionen und Datenspeicherung bietet. Der Dienst arbeitet mit dauerhaft gespeicherten Daten (einem Index), der Dokumente und Informationen bereitstellt, die zum Verarbeiten eines Indexes, Ausführen von Abfragen oder Formulieren von Suchergebnissen dienen. Unter diesen allgemeinen Vorgängen befinden sich untergeordnete Vorgänge für die Textanalyse, Rangfolge, Filterung, Sortierung, Facettierung und andere Funktionen, die zum Berechnen oder Anzeigen von Suchergebnissen dienen.

Die Relevanz der Suchumgebung hängt von der Qualität der Daten, die Sie hochladen, und davon ab, wie oft sie aktualisiert werden müssen. In diesem Artikel stellen wir den Push- und Pullansatz (Durchforstung) für das Importieren von Daten und Aktualisieren eines Indexes vor.

Bevor Sie Daten importieren können, muss der Index bereits vorhanden sein, und die Daten, die Sie hochladen, müssen dem Schema entsprechen. Weitere Informationen finden Sie unter [Indizes in Azure Search](search-what-is-an-index.md).

##Überlegungen zu Datasets

Es gibt keine Einschränkung für den Typ der Daten, die Sie hochladen, solange das Schema und die Datasets als JSON-Strukturen formuliert sind.

Die Daten, die Sie laden, müssen aus einer beliebigen Datenbank oder Datenquelle stammen, die Ihre benutzerdefinierte Anwendung erstellt oder verwendet. Wenn Ihre Anwendung beispielsweise ein Onlinekatalog ist, muss der Index, den Sie für Azure Search erstellen, Daten dem Produktbestand oder den Vertriebsdatenbanken entnehmen, die Ihre Anwendung unterstützen.

Ein Index in Azure Search ruft Daten aus einer einzelnen Tabelle, Sicht, einem Blobcontainer o. ä. ab. Sie müssen möglicherweise eine Datenstruktur in der Datenbank oder NoSQL-Anwendung erstellen, die Azure Search Daten bereitstellt. Alternativ können Sie für bestimmte Datenquellen wie Azure SQL-Datenbank oder DocumentDB einen Indexer erstellen, der eine externen Tabelle, Sicht oder einen Blobcontainer auf Daten durchforstet, die in Azure Search hochgeladen werden sollen.

##Anforderungen an die Latenz und Datensynchronisierung

In der folgende Tabelle finden Sie eine Zusammenfassung gängiger Anforderungen und Empfehlungen zu ihrer Erfüllung.

|Anforderungen|Empfehlungen|
|------------|---------------|
|Datensynchronisierung nahezu in Echtzeit|Programmieren Sie entweder die .NET- oder REST-API für Pushübertragungen von Aktualisierungen in einen Index. Ein Pullansatz für die Erfassung von Daten ist ein geplanter Vorgang, der nicht schnell genug ausgeführt werden kann, um mit den schnellen Änderungen in einer primären Datenquelle Schritt zu halten.|
|Azure SQL-Datenbank, DocumentDB oder SQL Server in virtuellen Azure-Computern|Indexer sind an bestimmte Datenquellentypen gekoppelt. Wenn primäre Datenquellen sich in einer unterstützten Datenquelle befinden, können Sie einen Indexer zum Durchforsten der Datenquelle und Planen von Datenaktualisierungen in stündlichen Intervallen nutzen. Sie können einen Indexer im Portal oder in Code konfigurieren.|
|Geplante Datenaktualisierung|Verwenden Sie einen Indexer (siehe oben).|
|Crawler|Verwenden Sie einen Indexer (siehe oben).|
|Codefreie Erstellung von Prototypen oder Bearbeitung|Das Portal enthält einen Assistenten zum Importieren von Daten, der einen Indexer konfiguriert. Dabei wird mitunter ein vorläufiges Schema generiert, wenn in der primären Datenbank genügend Informationen dafür enthalten sind. Der Assistent bietet Optionen zum Einrichten geplanter Datenaktualisierungen. Optional können Sie Sprachanalysen oder CORS-Optionen hinzufügen. Es gibt einige Nachteile: Sie können weder Bewertungsprofile hinzufügen noch ein im Portal erstelltes Schema in eine JSON-Datei zur Verwendung in Code exportieren.| 

<!---HONumber=Nov15_HO3-->