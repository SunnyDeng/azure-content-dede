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

Azure Search ist ein gehosteter Cloudsuchdienst, der Folgendes bietet: eine Suchmaschine, Suchfunktionen, die ein mit Bing oder Google vergleichbare Umgebung in Ihrer benutzerdefinierten Anwendung ermöglichen, .NET- und REST-APIs für die Integration von Azure Search mit Ihrer Web- oder Desktopanwendung und einen Datenspeicher mit durchsuchbaren Daten in Form von *Indizes*.

##Was ist ein Index?

Ein *Index* ist ein dauerhafter Speicher von Dokumenten und anderen Konstrukten, die von einem Azure Search-Dienst verwendet werden.

Genauer gesagt, enthält ein Index alle durchsuchbaren Daten, die für Folgendes verwendet werden: Verarbeiten eines Indexes, Ausführen einer Abfrage sowie Zurückgeben einer Suchergebnisliste, einer Facettennavigationsstruktur oder einer Detailseite an Ihre Anwendung. In Azure Search enthält ein Index auch nicht durchsuchbare Daten zur Verwendung in Filterausdrücken und Bewertungsprofilen (die zum Optimieren einer Abfrage oder Erhöhen einer Suchrangbewertung dienen). Außerdem enthalten sind an Ihre benutzerdefinierte Anwendung angepasste Strukturen, die in Azure Search integriert werden.

Wenn Sie mit Datenbanken vertraut sind, ist ein Index vom Konzept her einer Tabelle ähnlich, und Dokumente entsprechen ungefähr den Zeilen einer Tabelle.

Die Hauptabschnitte eines Indexes, die ausführlich in diesem Artikel beschrieben werden, sind wie folgt: – Felder, die den Textteil eines *Dokuments* definieren, mit Attributen für bestimmte Verhalten – Bewertungsprofile – Vorschläge – CORS-Optionen – Standardanalysen

Im Rahmen der Bereitstellung eines Suchdiensts für Ihre Anwendung müssen Sie einen Index erstellen. Das Erstellen eines Indexes ist eine Aufgabe, deren Schwerpunkt auf der Definition eines Schemas liegt. Mindestanforderungen sind das Definieren von Feldern und Festlegen von Attributen. Optional können Sie Bewertungsprofile und Vorschläge definieren, um die Sucherfahrung zu verbessern.

Um ein Schema zu erstellen, können Sie das Portal verwenden oder Code schreiben, der das .NET SDK oder die REST-API aufruft.

Nachdem Sie das Schema definiert und den Index erstellt haben, erfolgt dessen Auffüllen in einem getrennten Vorgang. Weitere Informationen zur Datenerfassung, nachdem der Index erstellt wurde, finden Sie unter [Importieren von Daten in Azure Search](search-what-is-data-import.md).

##Wie werden Indizes in Azure Search verwendet?

Bei allen Azure Search ausgeführten datenbezogenen Vorgängen werden Daten im Index genutzt oder zurückgegeben. Es gibt keine Ausnahmen: Sie können den Dienst nicht an andere Datenquellen neben dem Index verweisen, um in einer Antwort externe Daten zurückzugeben.

Für Anwendungen, die Daten ansammeln und Vorgänge darauf anwenden, wie z. B. Verkaufstransaktionen einer Onlinehändler-App, die Daten in einer OLTP-Datenbank speichert, stellt ein Azure Search-Index eine weitere Datenquelle in Ihrer Gesamtlösung dar. Der Index ist ein dedizierter Datenspeicher, der nur von Ihrem Suchdienst verwendet wird. Das Vorhandensein einer dedizierten Datenquelle in der Nähe Ihrer Dienste stellt Einheitlichkeit in Suchergebnissen sicher, verringert Flüchtigkeit, mindert die Anzahl von Roundtrips zwischen Ihrer Anwendung und Azure Search und verbessert die Gesamtleistung von Suchvorgängen, da es keine Ressourcen- oder Datenkonflikte gibt.

##Anleitung zum Definieren eines Schemas

Schemas werden als JSON-Strukturen erstellt. Sie müssen einen Index für jede benutzerdefinierte Lösung erstellen, die Sie mit Azure Search integrieren. Es ist nicht möglich, mehrere Indizes zu verknüpfen, doch Sie können komplexe Indizes erstellen, die für verschiedene von Ihrer Lösung benötigte Suchszenarien geeignet sind. Wenn Ihre Anwendung z. B. mehrere Sprachen unterstützt, kann die „fields“-Auflistung in Ihrem Index sprachspezifische Varianten für jedes Feld aufweisen.

Nehmen Sie sich beim Entwerfen Ihres Indexes in der Planungsphase genügend Zeit, um jede Entscheidung sorgfältig zu durchdenken. Das Ändern eines Indexes nach seiner Bereitstellung erfordert dessen Neuerstellung und das erneute Laden der Daten. Wenn Sie den Index in Code erstellen, ist dieser Schritt wesentlich einfacher, als wenn Sie ihn manuell im Portal erstellen.

Ein solides Grundwissen über die ursprünglichen Quelldaten ist für das Erstellen eines geeigneten Schemas unerlässlich. Sie müssen Datentypen entsprechend zuordnen, wissen, welches Feld als *Schlüssel* verwendet werden soll, um jedes Dokument im Index eindeutig zu identifizieren, und welche Felder in einer Suchergebnisliste oder auf einer Detailseite angezeigt werden sollen.

Bild-, Audio- und Videoinhalte müssen an anderer Stelle gespeichert werden, solange der Index ein repräsentatives Feld mit einer URL zur Ressource enthält. Alle Inhalte, insbesondere binäre Inhalte, die nicht durchsuchbar sind, sollten an anderer Stelle gespeichert werden. Anschließend sollte ein Verweis anhand des URI in einem Feld in Ihrem Index erfolgen.

Wie Sie sich vorstellen können, muss der Index regelmäßig mit anderen in der Lösung verwendeten Datenquellen synchronisiert werden. In einem Katalog eines Onlinehändlers muss die Inventardatenbank, in der Verkaufstransaktionen erfasst werden, dieselben Artikel-, Preis- und Verfügbarkeitsinformationen wie die Daten enthalten, die in Suchergebnissen angezeigt werden. Je nachdem, wie viel Latenz für Ihre Lösung akzeptabel ist, können Sie Daten wöchentlich, täglich oder nahezu in Echtzeit mithilfe gleichzeitiger Schreibvorgänge in sowohl eine Inventardatenbank als auch einen Azure Search-Index synchronisieren. Unter [Importieren von Daten in Azure Search](search-what-is-data-import.md) werden die verfügbaren Optionen ausführlich erläutert.

##Elemente des Schemas

Indizes bestehen aus attributierten Feldern, Datenwerten und anderen Konstrukten, die den Aufbau von Abfragen und Suchergebnissen bestimmen.

- Felder definieren den Textkörper eines Dokuments. Ein Onlinehändler benötigt Dokumente für jede Artikelposition, ein Nachrichtensender braucht Dokumente für jeden Artikel und ein Infomediaportal hat Bedarf an Dokumenten für jeden Hersteller oder Webshop.
- Attribute (bzw. Eigenschaften) sind Anmerkungen zum Feld, das für Angaben zu Datentyp, Länge, Wert und Verhalten dient, die für dieses Feld zulässig sind. Sie können feldbezogen angeben, ob ein Feld durchsuchbar, abrufbar oder sortierbar ist. Sie können auch angeben, welche Sprachanalyse auf Feldebene erfolgen soll. Ein *Schlüssel* ist ein reserviertes Feld im Schema, das für jedes Dokument in der „fields“-Auflistung einen eindeutigen Bezeichner enthält.
- Bewertungsprofile sind Kriterien zum Verbessern des Rangs eines Suchtreffers, der mehrere der Merkmale aufweist, die im Profil festgelegt sind. Wenn beispielsweise ein Suchbegriff mit einem Produktnamen und einer Produktbeschreibung übereinstimmt, möchten Sie bestimmt, dass Übereinstimmungen mit dem Produktnamen höher eingestuft werden als Übereinstimmungen in einer Beschreibung.
- Vorschläge, auch automatisch vervollständigte Abfragen genannt, werden in einem Abschnitt des Indexes definiert.
- Standardsprachanalysen können auf Indexebene angegeben werden und gelten global für alle Felder.
- CORS-Optionen ermöglichen domänenübergreifende Skripts und sind ebenfalls Teil der Indexdefinition.

## Indexattribute

|**Eigenschaft**|Beschreibung|
|------------|-----------|
|**Abrufbar**|Gibt an, ob ein Feld in einem Suchergebnis zurückgegeben werden kann.|
|**Filterbar**|Ermöglicht die Verwendung des Felds in Abfragen vom Typ **$filter**.|
|**Sortierbar**|Ermöglicht die Verwendung des Felds als Sortieroption.|
|**Facettierbar**|Ermöglicht die Verwendung eines Felds in einer Facettennavigationsstruktur für selbstständiges Filtern. Repetitive Werte, mit denen sich mehrere Dokumente zu einer Gruppe zusammenfassen lassen (etwa mehrere Dokumente der gleichen Produkt- oder Dienstleistungskategorie), sind in der Regel am besten für die Verwendung als Facetten geeignet.|
|**Schlüssel**|Die eindeutige ID der einzelnen Dokumente, die für die Dokumentsuche verwendet wird. Jeder Index muss über einen Schlüssel verfügen. Als Schlüssel kann immer nur ein einzelnes Feld fungieren, und dieses Feld muss auf „Edm.String“ festgelegt sein.|
|**Durchsuchbar**|Markiert das Feld als in die Volltextsuche einbeziehbar.|

## Speicherung in der Cloud

Da Azure Search ein vollständig verwalteter Suchdienst ist, wird die Speicherung von Daten als interner Vorgang behandelt. Als Programmierer müssen Sie wissen, dass Sie die Azure Search-APIs nicht umgehen können, um sich direkt mit dem Index zu verbinden. Außerdem können Sie die Indexgröße bzw. -integrität nur mittels Portalbenachrichtigungen oder API-Aufrufen überwachen. Es gibt weder eine Möglichkeit zum Sichern oder Wiederherstellen eines Indexes noch zum Optimieren der Leistung auf Speicherebene. Hinter den Kulissen werden Daten in Blobspeicher gespeichert. Am besten stellen Sie sich jedoch die physische Speicherung und Verwaltung eines Indexes als ein Implementierungsdetail vor, das sich in Zukunft ändern kann. Einer der Hauptvorteile des Diensts ist der vollautomatisierte Ansatz zur Datenverwaltung.

Wenn sich das Abfragevolumen oder die Datenspeicheranforderungen mit der Zeit ändern, können Sie die Kapazität erhöhen oder verringern, indem Sie Replikate und Partitionen hinzufügen oder verschieben. Weitere Informationen finden Sie unter [Verwalten Ihres Suchdiensts in Azure](search-manage.md) oder [Diensteinschränkungen](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO3-->