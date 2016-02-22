<properties 
	pageTitle="Neuerungen im aktuellen Update zu Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst" 
	description="Versionshinweise zu Azure Search mit einer Beschreibung der aktuellen Dienständerungen." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/10/2016" 
	ms.author="heidist"/>

#Neuerungen im aktuellen Update zu Azure Search#

Azure Search ist ein in Microsoft Azure gehosteter Cloudsuchdienst. Er ist allgemein verfügbar und bietet für die Konfigurationen der [Version 2015-02-28 der Azure Search-Dienst-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) oder des [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) eine Vereinbarung zum Servicelevel (SLA) mit einer Verfügbarkeit von 99,9 %.

##Neu ab 2016

Feature|Veröffentlicht|Status|Details
-------|--------|------|-------
[.NET SDK 1.1](https://msdn.microsoft.com/library/azure/dn951165.aspx)|Februar 2016|Allgemein verfügbar|Dies ist die erste allgemein verfügbare Version der .NET-Clientbibliothek `Microsoft.Azure.Search.dll`. In dieser Version werden wichtige Änderungen eingeführt. Anweisungen zur Migration finden Sie unter [Upgrade auf Version 1.1 des Azure Search .NET SDK](search-dotnet-sdk-migration.md).
[Unterstützung für die Lucene-Abfragesyntax](https://msdn.microsoft.com/library/azure/mt589323.aspx)|Februar 2016|[ALLGEMEIN VERFÜGBAR](search-api-2015-02-28.md)|Die Lucene-Abfragesyntax ist jetzt sowohl in der REST-API als auch im .NET SDK allgemein verfügbar. Legen Sie in der REST-API den `queryType`-Parameter auf `full` und im .NET SDK die `SearchParameters.QueryType`-Eigenschaft auf `QueryType.Full` fest, um die Lucene-Syntax zu aktivieren.
[Benutzerdefinierte Analysen](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|Januar 2016|[Vorschau](search-api-2015-02-28-preview.md)|Benutzerdefinierte Konfigurationen von Tokenizern und Tokenfiltern. Weitere Informationen finden Sie auf MSDN unter [Analyse in Azure Search](https://msdn.microsoft.com/library/azure/mt605304.aspx).
[Azure Blob Storage-Indexer](search-howto-indexing-azure-blob-storage.md)|Januar 2016|[Vorschau](search-api-2015-02-28-preview.md)|PDFs, Office-Dokumente, XML-Dateien, HTML-Dateien oder sogar Video- und Audiodateien können in einem Azure Search-Index zusammengeführt oder erfasst werden.
[Suchexplorer](search-explorer.md)|Januar 2016|[Portal](https://portal.azure.com)|Ein integriertes Abfragetool zum Ausführen von Ad-hoc-Abfragen für einen Index.
[Power BI-Inhaltspaket für Azure Search](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|Januar 2016|Tool|Visualisierung und Analyse von Dienstdaten mithilfe eines neuen Power BI-Inhaltspakets für Azure Search. Verfügbar über die Suchanalyse.
[Suchanalyse](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|Januar 2016|Portal|Ermöglicht eine Datensammlung, um Erkenntnisse über die Suchaktivitäten der Benutzer zu gewinnen.

##Neu ab 2015

Feature|Veröffentlicht|Status|Details
-------|--------|------|-------
Lucene-Sprachanalysen|Oktober 2015|Allgemein verfügbar|Dieses Feature ist nun allgemein verfügbar und kann in der Dienst-REST-API und im .NET-SDK genutzt werden.
[Microsoft NLPs (Natural Language Processors)](search-api-2015-02-28-Preview.md)|Oktober 2015|Allgemein verfügbar|Dieses Feature ist nun allgemein verfügbar und kann in der Dienst-REST-API und im .NET-SDK genutzt werden. 
[Unterstützung für die Lucene-Abfragesyntax](https://msdn.microsoft.com/library/azure/mt589323.aspx)|September 2015|[Vorschau](search-api-2015-02-28-preview.md)|Das Abfrageanalysemodul von Lucene wurde hinzugefügt. Um die neue Syntax zu verwenden, müssen Sie in einem Dokumentsuchvorgang den `queryType` angeben.
[NLPs (Natural Language Processors)](search-language-support.md)|September 2015|[Vorschau](search-api-2015-02-28-preview.md)|Die Sprachverarbeitungsmodule von Microsoft wurden hinzugefügt. Dadurch wurde die Gesamtzahl der Sprachen erhöht und eine alternative Implementierung für weitere bereitgestellt.
POST in Suchvorgängen, Vorschlägen und Nachschlageabfragen|September 2015|[Vorschau](search-api-2015-02-28-preview.md)|Gilt für die Dienst-REST-API.
[Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|September 2015|Allgemein verfügbar|Zweite Version der Verwaltungs-REST-API. Enthält checkNameAvailability-Prüfungen, mit denen geprüft wird, ob ein bestimmter Dienstname bereits verwendet wird; Replikatsbereich war zuvor 1 bis 6 und ist jetzt 1 bis 12; SKU-Eigenschaft wurde aus dem Eigenschaftenbehälter in die oberste Ebene der Dienstnutzlast verschoben; Antworttext des Vorgangs „Search-Dienst erstellen“ wurde aktualisiert, um das Verschieben der SKU-Einstellung zu unterstützen.
.NET-SDK 0.10.0-preview|August 2015|Vorschau|Dies ist die zweite Iteration der .NET-Clientbibliothek „Microsoft.Azure.Search.dll“. In dieser Version wurde die Unterstützung zum Erstellen, Verwalten und Verwenden der [DataSource-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx) und der [Indexer-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) über .NET-Klassen eingeführt. Für Azure SQL-Indexer wurde zudem die Unterstützung für die Indizierung geografischer Punkte eingeführt.
fieldMapping-Konstrukte|April 2015|Vorschau|Indexer unterstützen nun auch fieldMapping-Konstrukte, die Feldzuordnungen ermöglichen, wenn die Feldnamen in der externen Datenbank und im Azure Search-Index voneinander abweichen. Die `2015-02-28-preview`-Version der Indexer-Dokumentation finden Sie unter [Indexer](search-api-indexers-2015-02-28-Preview.md).
Feldtyptransformationen|April 2015|Vorschau|Indexer unterstützen nun Feldtyptransformationen, sodass ein Zeichenfolgefeld einer SQL-Tabelle einem Zeichenfolge-Collection-Feld eines Suchindex zugeordnet werden kann, sofern das Quellfeld ein JSON-Array ist.
[Dienst-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|März 2015|Allgemein verfügbar|Die erste allgemein verfügbare Version der Azure Search-Dienst-REST-API. Diese Version enthält die vorherigen Features. Sie enthält auch [Indexer](http://go.microsoft.com/fwlink/p/?LinkID=528210). [Suggester](https://msdn.microsoft.com/library/azure/dn798936.aspx) ersetzen die eher eingeschränkte Unterstützung für Typ-ahead-Abfragen der vorherigen Implementierung (bei der nur Präfixes abgeglichen wurden) durch die Unterstützung des Infix-Abgleichs. In dieser Implementierung werden Übereinstimmungen an jeder Stelle eines Begriffs gefunden und auch der Fuzzy-Match-Abgleich wird unterstützt. [\+++Tag-Boosting](http://go.microsoft.com/fwlink/p/?LinkId=528212) ermöglicht ein neues Szenario für Wertungsprofile. Insbesondere nutzt es persistente Daten (z. B. Warenkorb-Einstellungen), so dass die Suchergebnisse für einzelne Benutzer basierend auf deren persönlichen Informationen verbessert werden können. 
.NET-SDK 0.9.6-preview|März 2015|Vorschau|Dies ist die erste offizielle Veröffentlichung des .NET-SDK für Azure Search. Dieses enthält die Clientbibliothek „Microsoft.Azure.Search.dll“ mit den beiden Namespaces [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) und [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx).
Verwaltungs-REST-API|März 2015|Allgemein verfügbar|Die erste Version der Verwaltungs-REST-API in der allgemein verfügbaren Version von Azure Search. Es gibt keine Funktionsunterschiede zwischen der früheren Vorschau und dieser Version.
[Microsoft NLPs (Natural Language Processors)](search-api-2015-02-28-Preview.md)|März 2015|Vorschau|Erweitern die Sprachunterstützung und ermöglichen eine umfassende Wortstammerkennung für alle von Office und Bing unterstützte Sprachen.
[moreLikeThis=](search-api-2015-02-28-Preview.md)|März 2015|Vorschau|Ein Suchparameter für einen alternativen Abfrageausführungspfad, der `search=` ausschließt. Anstelle der Volltextsuche von `search=` anhand des eingegebenen Suchbegriffs sucht `moreLikeThis=` nach Dokumenten, die einem bestimmten Dokument ähnlich sind, indem es deren durchsuchbaren Felder vergleicht.

##Neu ab 2014

Feature|Veröffentlicht|Status|Details
-------|--------|------|-------
Lucene-Sprachanalysen|November 2014|Vorschau|Wurden hinzugefügt, um eine mehrsprachige Unterstützung für die mit Lucene verteilten benutzerdefinierten Sprachanalyseprogramme bereitzustellen. 
Es wurde eine Portalunterstützung zum Erstellen von Indizes eingeführt.|November 2014|[Portal](https://portal.azure.com)|Indizes, Analysen und Bewertungsprofile können im Portal erstellt werden.
Management-API Version 2014-07-31-Preview|Oktober 2014|Vorschau|Erste öffentliche Vorschauversion der Verwaltungs-REST-API. Die Dokumentation für diese API-Version ist auf Anfrage erhältlich.
Azure Search-Dienst-REST-API|August 2014|Vorschau|Erste öffentliche Vorschauversion der Dienst-REST-API (api-version-2014-07-31-Preview). Dies ist die REST-API für Index- und Dokumentvorgänge, für Bewertungsprofile zum Optimieren der Suchergebnisse und für die Unterstützung von Geodaten. Diese Version unterstützt die Dienstbereitstellung im Azure-Portal. Die Dokumentation für diese API-Version ist auf Anfrage erhältlich. Ihre Versionierung ist unabhängig von der Versionierung der REST-API des Diensts.

##Versionierung und Freigabe der Features

Die Features werden separat oder gemeinsam über die [REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx), das [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) oder das Dienstdashboard im [Azure-Portal](https://portal.azure.com) veröffentlicht. Sowohl die .NET-Bibliothek als auch die REST-APIs liegen in mehreren Versionen vor. Die älteren APIs funktionieren weiterhin, auch nach Einführung neuerer Features. Nähere Informationen zu unseren Versionierungsrichtlinien erhalten Sie im Artikel [Search service versioning](https://msdn.microsoft.com/library/azure/dn864560.aspx) (+++Versionsverwaltung des Suchdienstes).

Vorschaufeatures und allgemein verfügbare Features sind an eine API-Version der gleichen Kategorie gebunden.

- Vorschaufeatures haben einen experimentellen Status und können geändert oder sogar aufgegeben werden, bevor sie allgemein verfügbar gemacht werden. Vorschaufeatures stehen immer in der [Vorschauversion der REST-API](search-api-2015-02-28-preview.md) und manchmal im [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) zur Verfügung. In der Dokumentation zu den Features wird immer erklärt, wie auf das jeweilige Feature zugegriffen werden kann.
- Allgemein verfügbare Features sind stabil und werden in der Regel nicht geändert. Jede Änderung an einem allgemein verfügbaren Feature wird als wichtige oder tiefgreifende Änderung angekündigt.

Features, die allein auf dem Portal oder nur auf Tools aufbauen, werden üblicherweise im Laufe der Zeit geändert und lassen sich weder als allgemein verfügbare Feature noch als Vorschaufeature klassifizieren.














 

<!---HONumber=AcomDC_0211_2016-->