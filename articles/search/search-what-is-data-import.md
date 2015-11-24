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

In Azure Search arbeitet der Dienst mit dauerhaft gespeicherten Daten (einem Index), der Dokumente und Informationen bereitstellt, die zum Verarbeiten eines Indexes, Ausführen von Abfragen oder Formulieren von Suchergebnissen dienen. Um einen Index zu füllen, können Sie ein Push- oder Pull-Modell zum Laden von Daten verwenden.

Vor dem Importieren muss der Index bereits vorhanden sein. Weitere Informationen finden Sie unter [Indizes in Azure Search](search-what-is-an-index.md).

##Push-Übertragung von Daten in einen Index

Dieser Ansatz bezieht sich darauf, dass ein vorhandenes Dataset, das dem Indexschema entspricht, und Ihrem Suchdienst zur Verfügung gestellt wird. Bei Anwendungen mit sehr niedrigen Latenzanforderungen (wenn z. B. Suchvorgänge mit Inventardatenbanken synchronisiert werden müssen), ist ein Push-Modell Ihre einzige Option.

Sie können die REST-API oder .NET SDK zur Push-Übertragung von Daten in einen Index verwenden. Es gibt derzeit keine Toolunterstützung für die Push-Übertragung von Daten über das Portal.

Dieser Ansatz ist flexibler als ein Pull-Modell, da Sie Dokumente einzeln oder in Batches hochladen (bis zu 1.000 pro Batch oder 16 MB, je nachdem, welches Limit zuerst erreicht wird).

##Pull-Übertragung von Daten (Durchforstung) 

Ein Pull-Modell durchforstet eine unterstützte Datenquelle und lädt den Index für Sie. Diese Funktion wird in Azure Search über *Indexer* implementiert, die derzeit für Azure SQL-Datenbank, DocumentDB und SQL Server auf virtuellen Azure-Computern verfügbar sind. Weitere Informationen zum Hochladen von Azure SQL-Daten finden Sie unter [Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

Sie können das Portal, REST-API oder .NET SDK zur Pull-Übertragung von Daten in einen Index verwenden.

##Datasetanforderungen

Es gibt keine Einschränkung für den Typ der Daten, die Sie hochladen, solange das Schema und die Datasets als JSON-Strukturen formuliert sind.

Die Daten sollten aus einer beliebigen Datenbank oder Datenquelle stammen, die Ihre benutzerdefinierte Anwendung erstellt oder verwendet. Wenn Ihre Anwendung beispielsweise ein Onlinekatalog ist, muss der Index, den Sie für Azure Search erstellen, Daten dem Produktbestand oder den Vertriebsdatenbanken entnehmen, die Ihre Anwendung unterstützen.

Das Dataset sollte aus einer einzelnen Tabelle, Sicht, einem einzelnen Blobcontainer oder Ähnlichem stammen. Sie müssen möglicherweise eine Datenstruktur in der Datenbank oder NoSQL-Anwendung erstellen, die Azure Search Daten bereitstellt. Alternativ können Sie für bestimmte Datenquellen wie Azure SQL-Datenbank oder DocumentDB einen Indexer erstellen, der eine externen Tabelle, Sicht oder einen Blobcontainer auf Daten durchforstet, die in Azure Search hochgeladen werden sollen.

##Auswählen eines Datenimportansatzes

|Kriterien|Empfohlener Ansatz|
|------------|---------------|
|Datensynchronisierung nahezu in Echtzeit|Programmieren Sie entweder die .NET- oder REST-API für Pushübertragungen von Aktualisierungen in einen Index. Ein Pullansatz für die Erfassung von Daten ist ein geplanter Vorgang, der nicht schnell genug ausgeführt werden kann, um mit den schnellen Änderungen in einer primären Datenquelle Schritt zu halten.|
|Azure SQL-Datenbank, DocumentDB oder SQL Server auf virtuellen Azure-Computern|Indexer sind an bestimmte Datenquellentypen gekoppelt. Wenn primäre Datenquellen als Datenquellentyp unterstützt werden, ist ein Indexer die einfachste Möglichkeit, einen Index zu laden. Die maximal planbare Datenaktualisierungsfrequenz sind stündliche Intervalle. Sie können einen Indexer im Portal oder in Code konfigurieren.|
|Geplante Datenaktualisierung|Verwenden Sie einen Indexer (siehe oben).|
|Codefreie Erstellung von Prototypen oder Bearbeitung|Das Portal enthält einen Assistenten zum Importieren von Daten, der einen Indexer konfiguriert. Dabei wird mitunter ein vorläufiges Schema generiert, wenn in der primären Datenbank genügend Informationen dafür enthalten sind. Der Assistent bietet Optionen zum Einrichten geplanter Datenaktualisierungen. Optional können Sie Sprachanalysen oder CORS-Optionen hinzufügen. Es gibt einige Nachteile: Sie können weder Bewertungsprofile hinzufügen noch ein im Portal erstelltes Schema in eine JSON-Datei zur Verwendung in Code exportieren.| 

<!---HONumber=Nov15_HO4-->