<properties      
    pageTitle="Partitionieren von Daten in DocumentDB | Microsoft Azure"
	description="Erfahren Sie, wie Sie Daten in DocumentDB partitionieren können und wann Sie eine Hash-, Bereichs- und Suchpartitionierung verwenden sollten."
	services="documentdb"
	authors="arramac"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>
<tags       
    ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="arramac"/>

# Partitionieren von Daten in DocumentDB

[Microsoft Azure DocumentDB](../../services/documentdb/) wurde entwickelt, damit Sie schnelle, vorhersagbare Leistung für Ihre Anwendung erzielen und diese nahtlos *horizontal hochskalieren* können, wenn sie wächst. DocumentDB wurde verwendet, um hoch skalierbare Produktionsdienste bei Microsoft zu betreiben, wie z. B. den Benutzer-Datenspeicher, der wiederum die MSN-Suite mit Webanwendungen und mobilen Apps betreibt.

Im Hinblick auf Speicher und Durchsatz für Ihre DocumentDB-Anwendung können Sie eine nahezu unbegrenzte Skalierung erzielen, wenn Sie Ihre Daten horizontal partitionieren. Dieses Konzept wird gemeinhin als **Sharding** bezeichnet. DocumentDB-Konten können über stapelbare Einheiten, auch bekannt als **Sammlungen**, linear zu den Kosten skaliert werden. Die beste Partitionierung Ihrer Daten zwischen Sammlungen ist vom Datenformat und den Zugriffsmustern abhängig.

Nach dem Lesen dieses Artikels können Sie die folgenden Fragen beantworten:

 - Was ist eine Hash-, Bereichs- und Suchpartitionierung?
 - Wann verwenden Sie die einzelnen Partitionierungsverfahren und warum?
 - Wie erstellen Sie eine partitionierte Anwendung in Azure DocumentDB?

Dieser Artikel stellt einige Konzepte zum Sharding vor. Wenn Sie Code zum Partitionieren von Daten mit dem DocumentDB .NET SDK schreiben möchten, lesen Sie die Informationen unter [Partitionieren von Daten mit dem DocumentDB .NET SDK](documentdb-sharding.md).

## Sammlungen = Partitionen

Bevor wir uns näher mit Datenpartitionierungsverfahren befassen, müssen Sie zunächst verstehen, was eine Sammlung genau ist und was nicht. Wie Sie vielleicht bereits wissen, ist eine Sammlung ein Container für Ihre JSON-Dokumente. Sammlungen in DocumentDB sind nicht nur *logische*, sondern auch *physische* Container. Sie stellen die Transaktionsgrenze für gespeicherte Prozeduren und Trigger sowie den Eingangspunkt für Abfragen und CRUD-Vorgänge dar. Jeder Sammlung ist eine reservierte Durchsatzmenge zugewiesen, die nicht mit anderen Sammlungen im gleichen Konto gemeinsam genutzt wird. Aus diesem Grund können Sie Ihre Anwendung im Hinblick auf Speicher und Durchsatz horizontal hochskalieren, indem Sie weitere Sammlungen hinzufügen und dann Ihre Dokumente auf diesen verteilen.

Sammlungen sind nicht identisch mit Tabellen in relationalen Datenbanken. Sammlungen erzwingen kein Schema. Daher können Sie unterschiedliche Arten von Dokumenten mit unterschiedlichen Schemas in derselben Sammlung speichern. Sie können jedoch auch Sammlungen verwenden, um wie bei Tabellen Objekte eines einzelnen Typs zu speichern. Das geeignetste Modell ist nur davon abhängig, wie die Daten zusammen in Abfragen und Transaktionen angezeigt werden.

## Partitionieren mit DocumentDB

Die am häufigsten verwendeten Verfahren zum Partitionieren von Daten mit Azure DocumentDB sind die *Bereichspartitionierung*, die *Suchpartitionierung* und die *Hashpartitionierung*. In der Regel legen Sie einen einzelnen JSON-Eigenschaftennamen im Dokument als Partitionsschlüssel fest, z. B. "timestamp" oder "userID". In einigen Fällen ist dies möglicherweise stattdessen eine interne JSON-Eigenschaft oder ein anderer Eigenschaftennamen für jeden unterschiedlichen Dokumenttyp.

Lassen Sie uns diese Verfahren einmal näher betrachten.

## Bereichspartitionierung

In einer Bereichspartitionierung werden Partitionen basierend darauf zugewiesen, ob der Partitionsschlüssel in einem bestimmten Bereich liegt. Dies wird häufig für die Partitionierungen mit *time stamp*-Eigenschaften verwendet (z. B. eventTime zwischen dem 1. Februar 2015 und dem 2. Februar 2015).

> [AZURE.TIP]Verwenden Sie die Bereichspartitionierung, wenn Ihre Abfragen des Partitionsschlüssels auf bestimmte Bereichswerte beschränkt sind.

## Suchpartitionierung

Bei einer Suchpartitionierung werden Partitionen basierend auf einer Suchzuordnung zugewiesen, die diskrete Partitionswerte bestimmten Partitionen auch bekannt als Partitions- oder Shardzuordnung zuweist. Dies wird häufig für Partitionierungen nach Region verwendet (z. B. die Partition für Skandinavien umfasst Norwegen, Dänemark und Schweden).

> [AZURE.TIP]Die Suchpartitionierung bietet das höchste Maß an Kontrolle bei der Verwaltung einer mehrinstanzenfähigen Anwendung. Sie können einer Sammlung mehrere Mandanten, einer Sammlung einen Mandanten oder sogar mehreren Sammlungen einen Mandanten zuweisen.

## Hashpartitionierung

Bei der Hashpartitionierung werden basierend auf dem Wert einer Hashfunktion Partitionen zugewiesen, sodass Sie Anforderungen und Daten gleichmäßig über eine Anzahl von Partitionen verteilen können. Dies wird häufig zur Partitionierung der Daten verwendet, die aus einer großen Anzahl von unterschiedlichen Clients hergestellt oder genutzt werden, und eignet sich zum Speichern von Benutzerprofilen, Katalogelementen und IoT-Telemetriedaten ("Internet der Dinge").

> [AZURE.TIP]Verwenden Sie die Hashpartitionierung, wenn es zu viele Entitäten gibt, um sie mit der Suchpartitionierung aufzuzählen (z. B. Benutzer oder Geräte), und wenn die Abfragerate zwischen Entitäten einigermaßen gleichmäßig ist.

## Auswählen des richtigen Partitionierungsverfahrens

Welches Partitionierungsverfahren ist für Sie geeignet? Die Wahl hängt vom Datentypen und den allgemeinen Zugriffsmustern ab. Durch die Wahl des richtigen Partitionierungsverfahrens zum Entwurfszeitpunkt können Sie technische Schulden vermeiden und das Wachstum der Datengröße und des Anfragevolumens bewältigen.

- Die **Bereichspartitionierung** wird im Allgemeinen im Datenkontext verwendet, da sie Ihnen eine einfache und natürliche Methode bietet, um Partitionen nach Zeitstempel altern zu lassen. Sie ist auch nützlich, wenn Abfragen im Allgemeinen auf einen Zeitraum beschränkt sind, da dies an den Partitionierungsgrenzen ausgerichtet ist. 
- Die **Suchpartitionierung** ermöglicht es Ihnen, unsortierte und nicht miteinander verbundene Datensätze auf natürliche Weise zu gruppieren und zu organisieren, z. B. Gruppieren von Mandanten nach Organisation oder von Staaten nach geografischer Region. Die Suche bietet auch eine präzisere Kontrolle für die Migration von Daten zwischen Sammlungen. 
- Die **Hashpartitionierung** eignet sich für den einheitlichen Lastenausgleich von Anforderungen, um den bereitgestellten Speicher und Durchsatz effektiv zu nutzen. Mithilfe von *konsistenten Hash*-Algorithmen können Sie die Datenmenge minimieren, die beim Hinzufügen oder Entfernen einer Partition verschoben werden muss.

Sie müssen sich nicht für ein Partitionierungsverfahren entscheiden. Eine *Mischung* dieser Verfahren kann je nach Szenario ebenfalls sinnvoll sein. Wenn Sie z. B. Fahrzeugtelemetriedaten speichern, wäre eine Partitionierung der Gerätetelemetriedaten nach Zeitstempelbereich ein guter Ansatz, um die Partitionen einfach verwalten zu können. Führen Sie anschließend eine Subpartitionierung nach VIN (Fahrzeugnummer) durch, um für den Durchsatz eine horizontale Skalierung durchzuführen (gemischte Bereichs-Hash-Partitionierung).

## Entwickeln einer partitionierten Anwendung
Es gibt drei wichtige Designbereiche beim Entwickeln einer partitionierten Anwendung mit DocumentDB.

- Die Weiterleitung der Erstell- und Lesevorgänge (einschließlich Abfragen) an die richtigen Auflistungen
- Das Fortbestehen und Abrufen der Partitionsauflösungskonfiguration, auch bekannt als Partitionszuordnungen
- Das Hinzufügen/Entfernen von Partitionen bei zunehmendem Daten- und Abfragevolumen

Lassen Sie uns diese Bereiche einmal näher betrachten.

## Weiterleiten von Erstellungsvorgängen und Abfragen

Das Weiterleiten von Dokumenterstellungsanfragen ist bei allen drei bislang erwähnten Verfahren relativ unkompliziert. Das Dokument wird auf der Partition aus dem Hash-, Such- oder Bereichswert erstellt, der dem Partitionsschlüssel entspricht.

Abfragen und Lesevorgänge sollten in der Regel auf einen einzelnen Partitionsschlüssel begrenzt werden, damit Abfragen nur auf die entsprechenden Partitionen verteilt werden können. Bei Abfragen für alle Daten müssen Sie jedoch die Abfrage über mehrere Partitionen*verteilen* und dann die Ergebnisse zusammenführen. Beachten Sie, dass einige Abfragen eine benutzerdefinierte Logik durchführen müssen, um die Ergebnisse zusammenzuführen, z. B. beim Abrufen der ersten n Ergebnisse.

## Verwalten der Partitionszuordnung

Außerdem müssen Sie entscheiden, wie Sie die Partitionszuordnung speichern möchten, wie Ihre Clients diese laden sollen und bei Änderungen Updates empfangen sollen, und wie sie für mehrere Clients freigegeben wird. Wenn die Partitionszuordnung sich nicht häufig ändert, können Sie sie einfach in der Anwendungskonfigurationsdatei speichern.

Wenn dies nicht der Fall ist, können Sie sie in einem persistenten Speicher speichern. Ein häufiges Entwurfsmuster in der Produktion ist die Serialisierung von Partitionszuordnungen als JSON und auch deren Speicherung in DocumentDB-Sammlungen. Clients können dann die Zuordnung zwischenspeichern, um zusätzliche Roundtrips zu vermeiden, und dann in regelmäßigen Abständen auf Änderungen hin zu prüfen. Wenn die Clients die Shardzuordnung ändern können, stellen Sie sicher, dass sie ein konsistentes Namensgebungsschema und eine optimistische Parallelität (eTags) verwenden, um einheitliche Updates der Partitionszuordnung zu ermöglichen.

## Hinzufügen und Entfernen von Partitionen

Mit DocumentDB können Sie jederzeit Sammlungen hinzufügen und entfernen und sie verwenden, um neue eingehende Daten zu speichern oder in vorhandenen Sammlungen verfügbare Daten neu auszugleichen. Die Anzahl der Sammlungen finden Sie auf der Seite [Grenzen](documentdb-limits.md). Sie können sich jederzeit an uns wenden, um diese Grenzen anzuheben.

Das Hinzufügen und Entfernen einer neuen Partition mithilfe der Such- und Bereichspartitionierung ist einfach. Zum Hinzufügen einer neuen geografischen Region oder eines neuen Zeitbereichs für aktuelle Daten müssen Sie beispielsweise einfach die neuen Partitionen an die Partitionszuordnung anhängen. Für das Aufteilen einer vorhandenen Partition in mehrere Partitionen oder das Zusammenführen von zwei Partitionen ist ein wenig mehr Aufwand erforderlich. Dazu müssen Sie eine der folgenden beiden Aufgaben durchführen:

- Offlineschalten des Shards während der Lesevorgänge
- Weiterleiten der Lesevorgänge an beide Partitionen mithilfe der alten Partitionierungskonfiguration sowie der neuen Partitionskonfiguration während der Migration Beachten Sie, dass bis zum Abschluss der Migration keine Transaktions- oder Konsistenzgarantien verfügbar sein werden.

Beim Hashing ist das Hinzufügen und Entfernen von Partitionen etwas komplizierter. Einfache Hashingverfahren führen zu einer zufälligen Anordnung, sodass die meisten Daten verschoben werden müssen. Durch **konsistentes Hashing** können Sie sicherstellen, dass nur ein Teil der Daten verschoben werden muss.

Eine relativ einfache Möglichkeit, neue Partitionen hinzufügen, ohne Daten verschieben zu müssen, besteht darin, Ihre Daten in eine neue Sammlung zu übertragen und die Abfragen dann auf die alte und die neue Sammlung zu verteilen. Dieser Ansatz sollte jedoch nur in seltenen Fällen verwendet werden (z. B. Übertragungen zu Spitzenauslastungszeiten und vorübergehendes Speichern der Daten, bis sie verschoben werden können).

## Nächste Schritte
In diesem Artikel haben wir einige gängige Verfahren der Datenpartitionierung mit DocumentDB vorgestellt und erläutert, wann welche Verfahren oder wann welche Verfahrenskombination verwendet werden sollen.

-   Lesen Sie als Nächstes diesen [Artikel](documentdb-sharding.md), in dem erläutert wird, wie Sie Daten mithilfe von Partitionsresolvern wie dem DocumentDB SDK partitionieren. 
-   Laden Sie eines der [unterstützten SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) herunter.
-   Wenden Sie sich über die [MSDN-Supportforen](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) an uns, wenn Sie Fragen haben.
   


 

<!---HONumber=August15_HO9-->