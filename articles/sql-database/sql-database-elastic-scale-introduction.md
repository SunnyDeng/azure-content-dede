<properties 
    pageTitle="Tools für elastische Datenbanken in Azure SQL-Datenbank" 
    description="Skalieren Sie auf einfache Weise Datenbankressourcen in der Cloud mithilfe der Tools für elastische Datenbanken." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/24/2015" 
    ms.author="sidneyh"/>

# Tools für elastische Datenbanken in Azure SQL-Datenbank

## Versprechen und Herausforderungen

Azure SQL-Datenbank bietet praktisch unbegrenzte Datenbankressourcen sowie Flexibilität für transaktionale Workloads. Diese Funktionalität wird erreicht durch Features wie z. B. die **Clientbibliothek für elastische Datenbanken** und das **Split-Merge-Tool**, die zusammen als **Tools für elastische Datenbanken** bezeichnet werden. Diese Komponenten dienen zur Vereinfachung der Entwicklung und Verwaltung von horizontal partitionierten Datenbanklösungen, die in diesem Artikel behandelt werden.

Das Erzielen von Elastizität und Skalierbarkeit bei Cloudanwendungen war für Compute und Blob-Speicher relativ einfach – für die statusbehaftete Datenverarbeitung in relationalen Datenbanken bleibt es jedoch eine Herausforderung. Diese Herausforderungen treten vor allem in den beiden folgenden Szenarien auf:

* Vergrößern und Verkleinern der Kapazität für den relationalen Datenbankteil der Workload.
* Verwalten von Hotspots, die Auswirkungen auf eine bestimmte Teilmenge von Daten haben können – z. B. auf besonders aktive Endkunden (Mandant).

In der Vergangenheit wurden Szenarios wie diese durch eine Investition in größere Datenbankserver zur Unterstützung der Anwendung gelöst. Diese Option ist in der Cloud jedoch beschränkt, wo für die gesamte Verarbeitung vordefinierte Standardhardware verwendet wird. Stattdessen stellt die Verteilung der Daten und der Verarbeitung auf mehrere identisch strukturierte Datenbanken (die als Sharding bezeichnete horizontale Skalierung) eine attraktive Alternative zur herkömmlichen Hochskalierung im Hinblick auf Kosten und Elastizität dar.

Die Features für elastische Datenbanken von Azure SQL-Datenbank machen das horizontale Skalieren noch einfacher und flexibler als jemals zuvor. Wenn Sie SaaS (Software as a Service)-Anwendungen entwickeln, erleichtern **Pools für elastische Datenbanken** das Erstellen einzelner Datenbanken für jeden Endkunden oder Mandanten. Außerdem ermöglichen sie das automatische dynamische Vergrößern oder Verkleinern des Speicherbedarfs von Ressourcen und eine sicherere Budgetplanung. **Elastische Datenbankaufträge** ermöglichen es Ihnen, zuverlässig Verwaltungsvorgänge über einen ganzen Satz von Datenbanken durchzuführen, indem Sie T-SQL-Skripts zum Durchführen von Schemaänderungen, zur Verwaltung von Anmeldeinformationen oder andere Wartungsvorgänge für die Datenbanken ausführen. Weitere Informationen zu elastischen Datenbankaufträgen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

Unabhängig davon, ob Sie separate Datenbanken für jeden Mandanten verwenden oder mehrere Datenbereiche in einer Sammlung von Datenbanken zusammenfassen, reduzieren die Clientbibliothek für elastische Datenbanken und das Split-Merge-Tool den Aufwand für das Erstellen und Verwalten von Anwendungen, die Sharding nutzen. Mit der Clientbibliothek können Sie sich auf die Geschäftslogik der Anwendung konzentrieren, da Sie keinen Code in das Datendokumentenlayout verschiedener Datenbanken schreiben oder Verbindungen zum richtigen Speicherort umleiten müssen.

## Horizontale und vertikale Skalierung
Die folgende Abbildung zeigt die horizontale und vertikale Skalierung.
 
![Horizontale und vertikale Skalierung][4]

Horizontales Skalieren meint das Hinzufügen oder Entfernen von Datenbanken zur Anpassung der Kapazität oder der allgemeinen Leistung. Dies wird auch als "Skalierung in der Breite" bezeichnet. Sharding ist eine gängige Methode zum horizontalen Skalieren, bei der Daten in einer Sammlung von identisch strukturierte Datenbanken partitioniert werden.

Vertikales Skalieren bezeichnet das Erhöhen oder Verringern der Leistungsstufe einer einzelnen Datenbank – auch bekannt als "Hochskalieren".

Die meisten Datenbankanwendungen für Clouds verwenden eine Kombination aus diesen beiden Ansätzen. Eine SaaS (Software as a Service)-Anwendung kann z. B. das horizontale Skalieren für Bereitstellungen an neue Endkunden nutzen und das vertikale Skalieren, damit die Ressourcen für die Datenbanken der einzelnen Endkunden bei entsprechendem Workload vergrößert oder verkleinert werden können.

Die Tools für elastische Datenbanken vereinfachen das Erstellen von Anwendungen, die Sharding erfordern – da sie die horizontale Skalierung der Infrastruktur übernehmen. Wenn Sie einen Pool für elastische Datenbanken für Ihre Datenbanken verwenden, wird die vertikale Skalierung automatisch durch das System durchgeführt. Mit Pools sind Sie verantwortlich für die Festlegung von Grenzwerten für das gesamte System und von Abweichungen, die Sie pro Datenbank zulassen möchten. Sie können auch manuell die Datenbankedition oder die Ressourcenebenen für Datenbanken ändern, die nicht im Pool für elastische Datenbanken enthalten sind. Sie können z. B. einen neuen Shard erstellen, um den massiven Datenzufluss am Monatsende zu bewältigen. Solange neue Daten eingehen, wird der Shard skaliert. Wenn der Zufluss von Daten dann nachlässt, wird die Skalierung wieder zurückgenommen.

Weitere Informationen zu Pools für elastische Datenbanken finden Sie unter [Übersicht über Pools für elastische Datenbanken](sql-database-elastic-pool.md).

## Funktionen der Tools für elastische Datenbanken 

Die Entwicklung, Skalierung und Verwaltung von skalierten Anwendungen mithilfe von Sharding stellt sowohl für den Entwickler als auch für den Administrator eine Herausforderungen dar. Die Tools für elastische Datenbanken vereinfachen die Arbeit für diese beiden Rollen. Die Zahlen in der Abbildung unten umreißen die Hauptfunktionen der Clientbibliothek für elastische Datenbanken und des Split-Merge-Tools. Die Abbildung zeigt eine Umgebung mit vielen Datenbanken, bei der jede Datenbank einem Shard entspricht. Die Tools vereinfachen die Entwicklung von Anwendungen mit Sharding für Azure SQL-Datenbank über die folgenden spezifischen Funktionen:

Definitionen der hier verwendeten Begriffe finden Sie unter [Tools für elastische Datenbanken – Glossar](sql-database-elastic-scale-glossary.md).

![Funktionen zur elastischen Skalierung][1]

1.  **Shard-Zuordnungsverwaltung:** Die Verwaltung von Shard-Zuordnungen ist die Fähigkeit einer Anwendung, verschiedene Metadaten über die Shards zu verwalten. Die Shard-Zuordnungsverwaltung ist ein Feature der Clientbibliothek für elastische Datenbanken. Entwickler können diese Funktion verwenden, um Datenbanken als Shards zu registrieren, Zuordnungen einzelner Sharding-Schlüssel oder -Schlüsselbereiche zu diesen Datenbanken zu beschreiben und diese Metadaten zu verwalten, während sich die Anzahl und Zusammensetzung der Datenbanken gemäß den Kapazitätsänderungen weiter entwickelt. Die Shard-Zuordnungsverwaltung stellt einen Großteil der Codebausteine dar, die Kunden in ihren Anwendungen schreiben mussten, wenn sie Sharding selbst implementierten. Details finden Sie unter [Shard-Zuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md).
 
* **Datenabhängiges Routing:** Angenommen, eine Anforderung geht bei der Anwendung ein. Anhand des Shardingschlüsselwerts der Anforderung muss die Anwendung die richtige Datenbank bestimmen, die die Daten für diesen Schlüsselwert enthält, und eine Verbindung mit dieser zum Verarbeiten der Anforderung herstellen. Das datenabhängige Routing bietet die Möglichkeit, Verbindungen durch einen einfachen Aufruf über die Shard-Zuordnung der Anwendung zu öffnen. Das datenabhängige Routing ist weiterer Bereich des Infrastrukturcodes, der jetzt durch Funktionen der Clientbibliothek für elastische Datenbanken abgedeckt wird. Details finden Sie unter [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md).

* **Abfragen von mehreren Shards:** Das Abfragen mehrerer Shards setzt ein, wenn eine Anforderung mehrere (oder alle) Shards umfasst. In einer Abfrage mehrerer Shards wird derselbe T-SQL-Code für alle Shards oder eine Gruppe von Shards ausgeführt. Die Ergebnisse der beteiligten Shards werden unter Verwendung der UNION ALL-Semantik in einem Gesamtergebnis zusammengeführt. Die Funktionalität wird über die Clientbibliothek verfügbar gemacht. Sie verarbeitet u. a. Aufgaben wie Verbindungsverwaltung, Threadverwaltung, Fehlerbehandlung und Verarbeiten von Zwischenergebnissen. MSQ kann Hunderte von Shards abfragen. Weitere Einzelheiten finden Sie unter [Abfragen mehrerer Shards](sql-database-elastic-scale-multishard-querying.md).


* **Split-Merge-Tool:** Wenn Sie sich für das einfache Modell der Zuweisung separater Datenbanken in einem Pool für elastische Datenbanken für jedes Shardlet (Mandant) Ihrer Anwendung entscheiden, müssen Sie ggf. die Daten flexibel zwischen den Datenbanken verteilen, wenn die Kapazität zusammen mit der Geschäftsentwicklung fluktuiert. Die Tools für elastische Datenbanken umfassen ein auf Kundenseite gehostetes Split-Merge-Tool zum Neuausgleich der Datenverteilung und zum Verwalten von Hotspots für Sharding-Anwendungen in Situationen, in denen auch Daten verschoben werden. Dazu werden eine zugrunde liegende Funktion zum Verschieben von Shardlets zwischen verschiedenen Datenbanken bei Bedarf und die Shard-Zuordnungsverwaltung genutzt, um konsistente Zuordnungen und präzise datenabhängige Routingverbindungen sicherzustellen. Einzelheiten finden Sie unter [Aufteilen und Zusammenführen mit den Tools für elastische Datenbanken](sql-database-elastic-scale-overview-split-and-merge.md).

Im Allgemeinen steht den Endkunden mit den Tools für elastische Datenbanken bei der Übermittlung lokaler Shard-Vorgänge die volle T-SQL-Funktionalität zur Verfügung, anders als bei Shard-übergreifenden Vorgängen, die eine eigene Semantik haben.

## Allgemeine Sharding-Muster

**Sharding** ist eine Technik zur Verteilung großer Mengen identisch strukturierter Daten über eine Reihe voneinander unabhängiger Datenbanken. Sie ist besonders bei Cloudentwicklern beliebt, die SaaS-Angebote (Software as a Service) für Endbenutzer oder Unternehmen erstellen. Diese Endbenutzer werden häufig als „Mandanten‟ bezeichnet. Sharding kann aus verschiedenen Gründen erforderlich sein:

* Die Gesamtdatenmenge ist so groß, dass sie aufgrund einschlägiger Beschränkungen nicht in einer einzelnen Datenbank gespeichert werden kann. 
* Der Transaktionsdurchsatz der Workload überschreitet die Fähigkeiten einer einzelnen Datenbank. 
* Mandanten erfordern u. U. eine physische Trennung von anderen Mandaten, sodass für jeden Mandanten eine eigene Datenbank erforderlich ist. 
* Verschiedene Abschnitte einer Datenbank müssen sich aufgrund von Compliance- oder Leistungsanforderungen oder aus geopolitischen Gründen an verschiedenen geografischen Standorten befinden. 

In anderen Szenarien, z. B. beim Erfassen von Daten von verteilten Geräten, kann das Sharding eingesetzt werden, um eine Gruppe von Datenbanken über einen Zeitraum verteilt zu füllen. Beispielsweise kann jedem Wochentag oder jeder Woche eine eigene Datenbank zugeordnet werden. In diesem Fall kann der Sharding-Schlüssel eine Ganzzahl sein, die das Datum darstellt (und in allen Zeilen der partitionierten Tabellen vorhanden ist). Abfragen zum Abruf von Informationen für einen Datumsbereich müssen von der Anwendung an die Teilmenge von Datenbanken, welche den fraglichen Bereich abdecken, weitergeleitet werden.
 
Das Sharding funktioniert am besten, wenn jede Transaktion in einer Anwendung auf einen einzelnen Wert eines Sharding-Schlüssels begrenzt werden kann. Damit wird sichergestellt, dass alle Transaktionen einer bestimmten Datenbank eigen sind.

Einige Anwendungen verwenden den einfachsten Ansatz und erstellen für jeden Mandanten eine eigene Datenbank. Dies ist das **Sharding-Muster mit einzelnen Mandanten**, das Isolation, Sicherungs-/Wiederherstellungsfunktion und Ressourcenskalierung auf der Ebene einzelner Mandanten bietet. Beim Sharding mit einzelnen Mandanten ist jede Datenbank mit einem bestimmten Mandanten-ID-Wert (oder Kundenschlüsselwert) verknüpft, wobei der Schlüssel jedoch nicht immer in den Daten vorhanden sein muss. Es ist Aufgabe der Anwendung, die einzelnen Anforderungen an die entsprechende Datenbank weiterzuleiten – dies wird durch die Clientbibliothek für elastische Datenbanken vereinfacht.

![Einzelinstanzen und Mehrinstanzen][3]

In anderen Szenarien werden mehrere Mandanten in Datenbanken zusammengefasst, statt sie in getrennten Datenbanken zu isolieren. Dabei handelt es sich um ein typisches **Sharding-Szenario mit mehreren Mandanten**, dem die Tatsache zugrunde liegen kann, dass eine Anwendung eine große Anzahl von sehr kleinen Mandanten verwalten muss. Beim Sharding mit mehreren Mandanten sollen die Zeilen in den Datenbanktabellen einen Schlüssel enthalten, der die Mandanten-ID oder den Sharding-Schlüssel eindeutig identifiziert. Auch hier ist es Aufgabe der Anwendungsschicht, die Anforderung eines Mandanten an die entsprechende Datenbank weiterzuleiten. Dieser Vorgang kann durch die Clientbibliothek für elastische Datenbanken unterstützt werden. Darüber hinaus kann die zeilenbasierte Sicherheit zum Filtern der Zeilen verwendet werden, auf die jeder Mandant Zugriff hat. Weitere Informationen hierzu finden Sie unter [Mehrinstanzenfähige Anwendungen mit Tools für elastische Datenbanken und zeilenbasierter Sicherheit](sql-database-elastic-tools-multi-tenant-row-level-security.md). Beim Sharding mit mehreren Mandanten ist möglicherweise eine Neuverteilung der Daten zwischen den Datenbanken erforderlich, die mit dem Split-Merge-Tool für elastische Datenbanken durchgeführt wird.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/overview.png
[2]: ./media/sql-database-elastic-scale-intro/tenancy.png
[3]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png
[4]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
 

<!---HONumber=July15_HO4-->