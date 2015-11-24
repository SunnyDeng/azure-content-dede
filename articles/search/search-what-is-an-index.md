<properties
	pageTitle="Indizes in Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Was ist ein Index in Azure Search, und wie wird er verwendet?"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Indizes in Azure Search
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Azure Search ist ein gehosteter Cloudsuchdienst, der Folgendes bietet: eine Suchmaschine, Suchfeatures, die eine mit Bing oder Google vergleichbare Umgebung in Ihrer benutzerdefinierten Anwendung ermöglichen, .NET- und REST-APIs für die Integration von Azure Search in Ihre Web- oder Desktopanwendung und einen Datenspeicher mit suchbezogenen Daten in Form von *Indizes*.

##Was ist ein Index?

Ein *Index* ist ein dauerhafter Speicher von *Dokumenten* und anderen Konstrukten, die von einem Azure Search-Dienst verwendet werden. Dokumente sind eine grundlegende Einheit durchsuchbarer Daten. Beispielsweise könnte ein Einzelhändler ein Dokument für jede SKU, ein Nachrichtenanbieter ein Dokument für jeden Artikel und ein Streaming-Medienanbieter ein Dokument für jedes Video oder jeden Musiktitel in seiner Bibliothek besitzen. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Index* entspricht etwa einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

Der Index, der Dokumente enthält, ist ein flaches Dataset – in der Regel eine Teilmenge der Daten, die während normaler Geschäftsabläufe durch computergestützte Prozesse, z. B. Verkaufstransaktionen, die Veröffentlichung von Inhalten, Erwerb von Aktivitäten erstellt oder aufgezeichnet werden – häufig in relationalen oder NoSQL-Datenbanken gespeichert. Ein Index ruft seine Dokumente ab, wenn Sie mit Push oder Pull ein Dataset mit Zeilen aus anderen Datenquellen in Ihren Index holen.

Im Rahmen der Suchmodulvorgänge enthält ein Index alle durchsuchbaren Daten, die verwendet werden, um einen Index zu verarbeiten, eine Abfrage auszuführen oder Folgendes zurückzugeben: eine Suchergebnisliste, eine facettierte Navigationsstruktur, eine Detailseite für ein einzelnes Dokument. In Azure Search kann ein Index auch nicht durchsuchbare Daten enthalten, die intern in Filterausdrücken oder in den Bewertungsprofilen verwendet werden, die Kriterien für die Höherstufung von Suchergebnissen bieten.

##Wann sollte ein Index erstellt werden ?

Im Rahmen der Bereitstellung eines Suchdiensts für Ihre Anwendung müssen Sie einen Index erstellen. Das Erstellen eines Indexes ist eine Aufgabe, deren Schwerpunkt vor allem auf der Definition eines Schemas liegt. Mindestanforderungen sind das Definieren von Feldern und Festlegen von Attributen. Optional können Sie einen Index um Bewertungsprofile, Vorschläge und benutzerdefinierte Standardwerte erweitern.

Um ein Schema zu erstellen, das einen Index definiert, können Sie das Portal verwenden oder Code schreiben, der das .NET SDK oder die REST-API aufruft.

Nachdem Sie das Schema definiert und den Index erstellt haben, erfolgt dessen Auffüllen in einem getrennten Vorgang. Weitere Informationen zur Datenerfassung, nachdem der Index erstellt wurde, finden Sie unter [Importieren von Daten in Azure Search](search-what-is-data-import.md).

##JSON-Schemaspezifikation eines Indexes

Die Hauptabschnitte eines Azure Search-Indexes, ausgedrückt im JSON-Datenaustauschformat, lauten wie folgt.

|Schemaelement|Beschreibung|
|--------------|-----------|
|Felderauflistung|Felder definieren ein Dokument. Ein Dataset, das Sie mit Push oder Pull in einen Index holen, muss Werte bzw. NULL-Werte für alle Felder bieten, die mit Datentyp und Feldlänge im Schema kompatibel sind.|
|[Attribute](#index-attributes)|Eigenschaften bzw. Anmerkungen zu einem Feld, die Angaben zu Datentyp, -länge, -wert und -verhalten für dieses Feld bieten. Sie können feldbezogen angeben, ob ein Feld durchsuchbar, abrufbar oder sortierbar ist. Sie können auch Sprachanalyse-Außerkraftsetzungen auf Feldebene angeben.
|[Bewertungsprofile](https://msdn.microsoft.com/library/azure/dn798928.aspx)|Kriterien zum Verbessern des Rangs eines Suchtreffers, der mehrere der Merkmale aufweist, die im Profil festgelegt sind. Wenn beispielsweise ein Suchbegriff mit einem Produktnamen und einer Produktbeschreibung übereinstimmt, möchten Sie bestimmt, dass Übereinstimmungen mit dem Produktnamen höher eingestuft werden als Übereinstimmungen in einer Beschreibung.|
|[Vorschläge](https://msdn.microsoft.com/library/azure/mt131377.aspx)|Auch automatisch vervollständigte Abfragen genannt, werden in einem Abschnitt des Indexes definiert.|
|[Standardsprachanalysen]()|Diese können Sie optional auf Indexebene angeben, und sie gelten global für alle Felder.|
|CORS-Optionen|Optional, ermöglicht die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS, Cross-Origin Resource Sharing), wobei Anforderungen für eine Ressource, die von einer Webseite verwendet werden, über eine Domänengrenze hinweg ausgegeben werden. CORS ist immer deaktiviert, sofern Sie es nicht speziell für Ihren Index aktivieren.|

<a name="index-attributes"></a>
##Indexattribute

Attribute werden für einzelne Felder festlegen, um anzugeben, wie das Feld verwendet wird. Die folgende Tabelle listet die Attribute auf, die Sie angeben können.

|**Eigenschaft**|Beschreibung|
|------------|-----------|
|**Abrufbar**|Gibt an, ob ein Feld in einem Suchergebnis zurückgegeben werden kann.|
|**Filterbar**|Ermöglicht die Verwendung des Felds in Abfragen vom Typ **$filter**.|
|**Sortierbar**|Ermöglicht die Verwendung des Felds als Sortierspalte in einem Resultset.|
|**Facettierbar**|Ermöglicht die Verwendung eines Felds in einer Facettennavigationsstruktur für benutzerdefiniertes Filtern. Repetitive Werte, mit denen sich mehrere Dokumente zu einer Gruppe zusammenfassen lassen (etwa mehrere Dokumente der gleichen Marken- oder Dienstleistungskategorie), sind in der Regel am besten für die Verwendung als Facetten geeignet.|
|**Schlüssel**|Eine Zeichenfolge, die die eindeutige ID der einzelnen Dokumente darstellt und für die Dokumentsuche verwendet wird. Jeder Index muss über einen Schlüssel verfügen. Als Schlüssel kann immer nur ein einzelnes Feld fungieren, und dieses Feld muss auf „Edm.String“ festgelegt sein.|
|**Durchsuchbar**|Markiert das Feld als in die Volltextsuche einbeziehbar.|


##Wie werden Indizes in Azure Search verwendet?

Bei allen Azure Search ausgeführten datenbezogenen Vorgängen werden Daten im Index genutzt oder zurückgegeben. Es gibt keine Ausnahmen: Sie können den Dienst nicht an andere Datenquellen neben dem Index verweisen, um in einer Antwort, die Ihr Suchdienst ausgibt, externe Daten zurückzugeben.

Für Anwendungen, die Daten ansammeln und Vorgänge darauf anwenden, wie z. B. Verkaufstransaktionen einer Onlinehändler-App, stellt ein Azure Search-Index eine weitere Datenquelle in Ihrer Gesamtlösung dar. Der Index ist ein dedizierter Datenspeicher, der nur von Ihrem Suchdienst verwendet wird. Das Vorhandensein einer dedizierten Datenquelle in der Nähe Ihrer Suchdienste stellt Einheitlichkeit in Suchergebnissen sicher, verringert Flüchtigkeit, mindert die Anzahl von Roundtrips zwischen Ihrer Anwendung und Azure Search und verbessert die Gesamtleistung von Suchvorgängen, da es keine Ressourcen- oder Datenkonflikte gibt.

##Anleitung zum Definieren eines Schemas

Schemas werden als JSON-Strukturen erstellt. Sie müssen einen Index für jede benutzerdefinierte Lösung erstellen, die Sie mit Azure Search integrieren. Es ist nicht möglich, mehrere Indizes zu verknüpfen, doch Sie können komplexe Indizes erstellen, die für verschiedene von Ihrer Lösung benötigte Suchszenarien geeignet sind. Wenn Ihre Anwendung z. B. mehrere Sprachen unterstützt, kann die „fields“-Auflistung in Ihrem Index sprachspezifische Varianten für jedes Feld aufweisen.

Nehmen Sie sich beim Entwerfen Ihres Indexes in der Planungsphase genügend Zeit, um jede Entscheidung sorgfältig zu durchdenken. Das Ändern eines Indexes nach seiner Bereitstellung erfordert dessen Neuerstellung und das erneute Laden der Daten. Wenn Sie den Index in Code erstellen, ist dieser Schritt wesentlich einfacher, als wenn Sie ihn manuell im Portal erstellen.

Ein solides Grundwissen über die ursprünglichen Quelldaten ist für das Erstellen eines geeigneten Schemas unerlässlich. Sie müssen Datentypen entsprechend zuordnen, wissen, welches Feld als *Schlüssel* verwendet werden soll, um jedes Dokument im Index eindeutig zu identifizieren, und welche Felder in einer Suchergebnisliste oder auf einer Detailseite angezeigt werden sollen.

**Starten mit relationalen Daten**

Es kann schwierig sein, relationale Daten in ein flaches Dataset umzuwandeln. Falls möglich, versuchen Sie, das ggf. verfügbare Dataware House bzw. die Berichtsdatenbank Ihres Unternehmens zu verwenden. Es ist in der Regel die bessere Wahl, da das Modell bereits denormalisiert ist. Andernfalls müssen Sie sich auf Abfragen verlassen, um das benötigte Rowset zu erhalten. Ein Beispiel und eine Erklärung zur Ausführung finden Sie in [Modellieren der AdventureWorks-Inventory-Datenbank für Azure Search](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx).

**Einbeziehen binärer Daten**

Möglicherweise soll Bild-, Audio- oder Videoinhalt in den Suchergebnissen angezeigt werden. In diesem Fall sollten die Dateien an anderer Stelle gespeichert werden, solange der Index ein repräsentatives Feld mit einer URL zur Ressource enthält. Alle Inhalte, insbesondere binäre Inhalte, die nicht durchsuchbar sind, sollten kostengünstiger gespeichert werden. Anschließend sollte ein Verweis anhand des URI in einem Feld in Ihrem Index erfolgen.

**Synchronisieren von Daten**

Wie Sie sich vorstellen können, muss der Index regelmäßig mit anderen in der Lösung verwendeten Datenquellen synchronisiert werden. In einem Katalog eines Onlinehändlers muss die Inventardatenbank, in der Verkaufstransaktionen erfasst werden, dieselben Artikel-, Preis- und Verfügbarkeitsinformationen wie die Daten enthalten, die in Suchergebnissen angezeigt werden. Je nachdem, wie viel Latenz für Ihre Lösung akzeptabel ist, können Sie Daten wöchentlich, täglich oder nahezu in Echtzeit mithilfe gleichzeitiger Schreibvorgänge in sowohl eine Inventardatenbank als auch einen Azure Search-Index synchronisieren. Unter [Importieren von Daten in Azure Search](search-what-is-data-import.md) werden die verfügbaren Optionen ausführlich erläutert.

## Speicherung in der Cloud

Da Azure Search ein vollständig verwalteter Suchdienst ist, wird die Speicherung von Daten als interner Vorgang behandelt. Als Programmierer müssen Sie wissen, dass Sie die Azure Search-APIs nicht umgehen können, um sich direkt mit dem Index zu verbinden. Außerdem können Sie die Indexgröße bzw. -integrität nur mittels Portalbenachrichtigungen oder API-Aufrufen überwachen. Es gibt weder eine Möglichkeit zum Sichern oder Wiederherstellen eines Indexes noch zum Optimieren der Leistung auf Speicherebene. Hinter den Kulissen werden Daten in Blobspeicher gespeichert. Am besten stellen Sie sich jedoch die physische Speicherung und Verwaltung eines Indexes als ein Implementierungsdetail vor, das sich in Zukunft ändern kann. Einer der Hauptvorteile des Diensts ist der automatisierte Ansatz zur physischen Verwaltung der Computeressourcen Ihrer Suchanwendung.

Wenn sich das Abfragevolumen oder die Datenspeicheranforderungen mit der Zeit ändern, können Sie die Kapazität erhöhen oder verringern, indem Sie Replikate und Partitionen hinzufügen oder verschieben. Weitere Informationen finden Sie unter [Verwalten Ihres Suchdiensts in Azure](search-manage.md) oder [Diensteinschränkungen](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO4-->