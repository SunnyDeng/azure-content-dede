<properties 
	pageTitle="Neuerungen im aktuellen Update zu Azure Search | Microsoft Azure" 
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
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#Neuerungen im aktuellen Update zu Azure Search#

Azure Search ist ein in Microsoft Azure gehosteter Cloudsuchdienst. Azure Search ist allgemein verfügbar und bietet für die unterstützten Konfigurationen der [Version 2015-02-28 der API](https://msdn.microsoft.com/library/azure/dn798935.aspx) eine Vereinbarung zum Servicelevel (SLA) mit einer Verfügbarkeit von 99,9 %.

##Versionierung und Freigabe der Funktionen

Die Funktionen werden gemeinsam oder separat über die [REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx), das [.NET-SDK](http://go.microsoft.com/fwlink/?LinkId=528216) oder das Dienst-Dashboard im [Azure-Portal](https://portal.azure.com) veröffentlicht.

Sowohl die .NET-Bibliothek als auch die REST-APIs liegen in mehreren Versionen vor. Die älteren APIs funktionieren weiterhin, auch nach Einführung neuerer Features. Nähere Informationen zu unseren Versionierungsrichtlinien erhalten Sie im Artikel [Search service versioning](https://msdn.microsoft.com/library/azure/dn864560.aspx) (+++Versionsverwaltung des Suchdienstes).


##API-Version 2015-02-28-Preview
**Wiederveröffentlicht: September 2015**

Diese Version bietet neue [Unterstützung der Lucene-Abfragesyntax](https://msdn.microsoft.com/library/azure/mt589323.aspx), die für die [Preview-Version der REST-API für den Azure Search-Dienst](search-api-2015-02-28-preview.md) verwendet werden kann. Um die neue Syntax zu verwenden, müssen Sie in einem Search Documents-Vorgang den `queryType` angeben.

Außerdem sind die beiden folgenden Funktionen jetzt nicht mehr Funktionen der Vorschauversion, sondern Teil der offiziellen API in MSDN: – [Natural Language Processors](search-language-support.md) – POST in Suchvorgängen, Vorschlägen und Suchabfragen

##.NET-SDK 0.10.0-preview
**Veröffentlicht im August 2015**

Dies ist die zweite Iteration der .NET-Clientbibliothek Microsoft.Azure.Search.dll. In dieser Version wurde die Unterstützung zum Erstellen, Verwalten und Verwenden von Indexern über .NET-Klassen eingeführt. Für Azure SQL-Indexer wurde zudem die Unterstützung für die Indizierung geografischer Punkte eingeführt.

- [Indexer-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [DataSource-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET-SDK 0.9.6-preview
**Veröffentlicht am 05.03.2015**

Dies ist die erste offizielle Veröffentlichung des .NET-SDK für Azure Search. Sie enthält die Clientbibliothek Microsoft.Azure.Search.dll mit den folgenden beiden Namespaces:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

In diesem Release nicht enthalten:

- [Indexer](http://go.microsoft.com/fwlink/p/?LinkId=528173) (ab Version 0.10.0-preview ist dieses Feature enthalten)
- [Management-REST-API](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- Features von [2015-02-28-Preview](search-api-2015-02-28-Preview.md) (zurzeit nur als Preview verfügbare Features sind Microsoft Natural Language-Prozessoren und `moreLikeThis`).

Anleitungen zur Installation und Verwendung des SDK finden Sie im Artikel [How to use Azure Search in .NET](http://go.microsoft.com/fwlink/p/?LinkId=528088) (+++Verwenden von Azure Search in .NET).

##API-Version 2015-02-28-Preview
**Veröffentlicht am 22.04.2015**

- Indexer unterstützen nun auch fieldMapping-Konstrukte, die Feldzuordnungen ermöglichen, wenn die Feldnamen in der externen Datenbank und im Azure Search-Index voneinander abweichen. Die `2015-02-28-preview`-Version der Indexer-Dokumentation finden Sie unter [Indexer](search-api-indexers-2015-02-28-Preview.md).

- Außerdem unterstützen in diesem Preview-Update Indexer nun auch Feldtyptransformationen, so dass ein Zeichenfolgefeld einer SQL-Tabelle einem Zeichenfolge-Collection-Feld eines Suchindex zugeordnet werden kann, sofern das Quellfeld ein JSON-Array ist.

**Veröffentlicht am 05.03.2015**

- [Microsoft Natural Language-Prozessoren](search-api-2015-02-28-Preview.md) erweitern die Sprachunterstützung und ermöglichen eine umfassende Wortstammerkennung für alle von Office und Bing unterstützte Sprachen.

- [moreLikeThis=](search-api-2015-02-28-Preview.md) ist ein Suchparameter für einen alternativen Abfrageausführungspfad, der `search=` ausschließt. Anstelle der Volltextsuche von `search=` anhand des eingegebenen Suchbegriffs sucht `moreLikeThis=` nach Dokumenten, die einem bestimmten Dokument ähnlich sind, indem es deren durchsuchbaren Felder vergleicht.

##API-Version 2015-02-28
**Veröffentlicht am 05.03.2015**

- [Indexer](http://go.microsoft.com/fwlink/p/?LinkID=528210) sind eine neue Funktion, welche die Indizierung der Daten aus Datenquellen wie der Azure SQL-Datenbank, Azure DocumentDB und SQL Server auf Azure-VMs erheblich vereinfacht.

- [Suggester](https://msdn.microsoft.com/library/azure/dn798936.aspx) ersetzen die eher eingeschränkte Unterstützung für Typ-ahead-Abfragen der vorherigen Implementierung (bei der nur Präfixes abgeglichen wurden) durch die Unterstützung des Infix-Abgleichs. In dieser Implementierung werden Übereinstimmungen an jeder Stelle eines Begriffs gefunden und auch der Fuzzy-Match-Abgleich wird unterstützt.

- [\+++Tag-Boosting](http://go.microsoft.com/fwlink/p/?LinkId=528212) ermöglicht ein neues Szenario für Wertungsprofile. Insbesondere nutzt es persistente Daten (z. B. Warenkorb-Einstellungen), so dass die Suchergebnisse für einzelne Benutzer basierend auf deren persönlichen Informationen verbessert werden können.

Besuchen Sie auch den Artikel [Azure Search is now Generally Available](http://go.microsoft.com/fwlink/p/?LinkId=528211) (+++Azure Search jetzt allgemein verfügbar) im Azure-Blog, in dem alle diese Funktionen vorgestellt werden.

##API-Version 2014-10-20-Preview
**Veröffentlicht im November 2014 und Januar 2015**

- [Lucene-Sprachanalyseprogramme](search-api-2014-10-20-preview.md) wurden hinzugefügt, um eine mehrsprachige Unterstützung der mit Lucene verteilten benutzerdefinierten Sprachanalyseprogramme bereitzustellen. 

- Im [Azure Management Portal](https://portal.azure.com) wurde eine Toolunterstützung zum Erstellen von Indizes, einschließlich der Erstellung von Wertungsprofilen, eingeführt.

##API-Version 2014-07-31-Preview
**Veröffentlicht am 21.08.2014**

Diese Version war die öffentliche Preview-Version für Azure Search. Sie enthielt die folgenden zentralen Features:

- REST-API für Index- und Dokumentoperationen. Der größte Teil der Features dieser API-Version ist in Version 2015-02-28 nach wie vor vorhanden. Die Dokumentation für Version `2014-07-31-Preview` finden Sie unter [Azure Search Service REST API Version 2014-07-31](search-api-2014-07-31-preview.md) (+++REST-API des Azure Search-Dienstes Version 2014-07-31).

- Wertungsprofile für die Optimierung der Suchergebnisse. Ein Wertungsprofil fügt Kriterien zum Berechnen der Suchergebnisse hinzu. Die Dokumentation für dieses Feature finden Sie unter [Azure Search Service Scoring Profiles REST API Version 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md) (+++Wertungsprofil-REST-API des Azure Search-Dienstes Version 2014-07-31).

- Geospatial-Unterstützung wurde als fester Bestandteil von Azure Search seit seiner Einführung durch den Datentyp `Edm.GeographyPoint` bereitgestellt.

- Bereitstellung in der Preview-Version von [Azure Management Portal](https://portal.azure.com). Azure Search war einer der wenigen Dienste, die nur im neuen Portal verfügbar waren.

##Management-API Version 2015-08-19
**Veröffentlicht: 11. September 2015**

Die [Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn832684.aspx) enthält die folgenden Updates.

- „CheckNameAvailability“ überprüft, ob ein bestimmter Dienst bereits verwendet wird.
- Replikatsbereich war zuvor 1 bis 6 und ist jetzt 1 bis 12.
- SKU-Eigenschaft wurde aus dem Eigenschaftenbehälter in die oberste Ebene der Dienstnutzlast verschoben.
- Antworttext des Vorgangs „Search-Dienst erstellen“ wurde aktualisiert, um das Verschieben der SKU-Einstellung zu unterstützen.

##Management-API Version 2015-02-28
**Veröffentlicht am 05.03.2015**

[Management-REST-API](search-management-api-2014-02-28.md) ist die erste Version der Management-API in der allgemein verfügbaren Version von Azure Search. Es gibt keine Funktionsunterschiede zwischen dem früheren und diesem Preview.

##Management-API Version 2014-07-31-Preview
**Veröffentlicht im Oktober 2014**

Zur Unterstützung der programmgesteuerten Dienstverwaltung wurde die Preview-Version von [Management-REST-API](search-management-api-2014-07-31-preview.md) hinzugefügt. Ihre Versionierung ist unabhängig von der Versionierung der REST-API des Diensts.


 

<!---HONumber=Nov15_HO2-->