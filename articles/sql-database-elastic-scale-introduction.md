<properties 
	pageTitle="Elastic Scale für Azure SQL-Datenbank" 
	description="Skalieren Sie auf einfache Weise Datenbankressourcen in der Cloud mithilfe des Elastic Scale-Features von Azure SQL-Datenbank." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Übersicht über Elastic Scale für Azure SQL-Datenbank 
Willkommen bei der öffentlichen Vorschauversion von Elastic Scale für Azure SQL-Datenbank! 

## Versprechen und Herausforderungen
Azure SQL Database Elastic Scale erfüllt die Erwartungen, die an Cloud-Computing gestellt werden, und ermöglicht praktisch unbegrenzte Kapazität sowie die Flexibilität der Plattform Azure SQL-Datenbank-Plattform. Bisher konnten Cloud-Dienstanbieter die meisten Aspekte hinsichtlich unbegrenzter Kapazität von Rechen- und Blob-Speicher bereitstellen. Die Elastizität bleibt jedoch weiterhin eine Herausforderung bei der statusbehafteten Datenverarbeitung in der Cloud, insbesondere bei der Verwaltung von relationalen Datenbanken. Diese Herausforderungen treten vor allem in den beiden folgenden Szenarien auf: 

* Vergrößern und Verkleinern der Kapazität für den relationalen Datenbankteil der Arbeitsauslastung.
* Verwaltung der Nutzung von Hotspots für statusbehaftete Datenbank-Arbeitsauslastungen und die zugehörigen Daten.

In der Vergangenheit wurde auf diese Szenarien durch den Kauf von weiterer Hardware eingegangen, die die Datenebene der Anwendung hostet. Diese Option ist in der Cloud jedoch beschränkt, wo für die gesamte Verarbeitung vordefinierte Standardhardware verwendet wird. Sharding oder die Verteilung der Daten und der Verarbeitung über mehrere Skalierungseinheiten aus Kapazitätsgründen stellt eine attraktive Alternative zu herkömmlichen Skalierungsansätzen im Hinblick auf Kosten und Flexibilität dar. Im Laufe der Jahre stellten wir fest, dass die Kunden ihre eigenen Ansätze für das horizontale Skalieren für die Azure SQL-Datenbank implementieren müssen, um Sharding erfolgreich einsetzen zu können. Für einige bedeutete dies, dass sie Hunderte oder Tausende von Azure SQL-Datenbanken verwalten mussten. Das bedeutete viel Code auf der Datenebene, der auf die Feinheiten des Sharding eingeht statt auf die Geschäftslogik der Anwendung. 

In der direkten Zusammenarbeit mit Kunden haben wir im Lauf der Jahre in diesen Projekten das Entstehen einiger Sharding-Muster beobachtet. Azure SQL Database Elastic Scale stellt Clientbibliotheken und Dienstangebote bereit, die auf diese Muster ausgerichtet sind. Elastic Scale ermöglicht es Ihnen, die statusbehaftete Datenebene Ihrer Azure-Anwendungen einfacher zu entwickeln, zu skalieren und zu verwalten.

Sie können dann auf die Geschäftslogik Ihrer Anwendung statt auf den Aufbau einer Infrastruktur für das Sharding konzentrieren.
## Horizontale und vertikale Skalierung
Die folgende Abbildung zeigt den Unterschied zwischen der horizontalen und vertikalen Skalierung. Dies sind die beiden Möglichkeiten zum Skalieren einer Shardinglösung. 
![Horizontal versus Vertical Scaleout][4]

Verwenden Sie die horizontale Skalierung, um die Kapazität zu erhöhen. Wenn der Abgabetermin für eine Steuererklärung näher rückt, erhöhen Sie z. B. Speicherplatz für eingehende Dokumente.

Verwenden Sie zum Erhöhen der Leistung eines Shards die vertikale Skalierung. Dieser Fall kann eintreten, wenn große Datenmengen verarbeitet werden und der Shard dadurch zu einem Hotspot im System wird. Beispielsweise können Sie einen neuen Shard erstellen, um den massiven Datenzufluss am Monatsende zu bewältigen. Solange neue Daten eingehen, wird der Shard skaliert. Wenn der Zufluss von Daten dann nachlässt, wird die Skalierung wieder zurückgenommen.

Weitere Informationen zu Skalierungsszenarien finden Sie unter [Aufteilen und Zusammenführen mit Elastic Scale](./sql-database-elastic-scale-overview-split-and-merge.md).


## Funktionen 

Die Entwicklung, Skalierung und Verwaltung von skalierten Anwendungen mithilfe von Sharding stellt sowohl für den Entwickler als auch für den Administrator eine Herausforderungen dar. Elastic Scale für Azure SQL-Datenbank erleichtert beiden Rollen die Arbeit. Die Zahlen in der Grafik kennzeichnen die wichtigsten Funktionen dieser öffentlichen Vorschauversion. 
Der untere Teil zeigt die Datenebene der Anwendung und die Verteilung der Daten über mehrere Datenbanken, die Shards genannt werden. Angenommen, in mehrere Datenbanken werden die Daten für mehrere Shards gespeichert. 

Definitionen der hier verwendeten Begriffe finden Sie unter [Elastic Scale-Glossar](./sql-database-elastic-scale-glossary.md).

## Flexible Skalierung mit Sharding 
Die **Shard-Elastizität** ermöglicht es Administratoren, die vertikale Skalierung (das wahlweise Hoch- und Herunterstufen der Edition eines einzelnen Shards) und die horizontale Skalierung (das Hinzufügen oder Entfernen von Shards aus einer Shard Map) in ihrer Shardingumgebung über PowerShell-Skripts und mithilfe des Azure Automation-Diensts zu automatisieren. Details finden Sie unter [Shard-Elastizität](./sql-database-elastic-scale-elasticity.md).

In der Abbildung unten ist der Entwickler links und der Administrator rechts dargestellt. Bei der Übermittlung lokaler Shard-Vorgänge steht den Endbenutzern die volle T-SQL-Funktionalität zur Verfügung, anders als bei shard-übergreifenden Vorgängen, die eine eigene Semantik haben. 
Die öffentliche Vorschauversion von Elastic Scale für Azure SQL-Datenbank erleichtert die Entwicklung partitionierter Anwendungen für Azure SQL-Datenbank durch die folgenden spezifischen Funktionen: 

![Elastic Scale Capabilities][1]

1.  **Shard-Zuordnungsverwaltung**: Die Verwaltung von Shard Maps ist die Fähigkeit einer Anwendung, verschiedene Metadaten über die Shards zu verwalten. Die Shard-Zuordnungsverwaltung ist ein Feature der Elastic Scale-Clientbibliothek. Entwickler können diese Funktion verwenden, um Shards zu registrieren, Zuordnungen einzelner Sharding-Schlüssel oder Schlüsselbereiche zu Shards zu beschreiben und diese Metadaten zu verwalten, während sich das Layout des Shards in der Datenebene weiter entwickelt, um Kapazitätsänderungen widerzuspiegeln. Die Shard-Zuordnungsverwaltung stellt einen Großteil der Codebausteine dar, die Kunden in ihren Anwendungen schreiben mussten, wenn sie Sharding selbst implementierten. Details finden Sie unter [Shard-Zuordnungsverwaltung](./sql-database-elastic-scale-shard-map-management.md)
 
* **Datenabhängiges Routing**: Angenommen, eine Anforderung geht bei der Anwendung ein. Anhand des Shardingschlüsselwerts der Anforderung muss die Anwendung den richtigen Shard bestimmen, der die Daten für diesen Shardingschlüsselwert enthält, und eine Verbindung mit diesem Shard zum Verarbeiten der Anforderung herstellen. Das datenabhängige Routing bietet die Möglichkeit, Verbindungen durch einen einfachen Aufruf über die Shard-Zuordnung der Anwendung zu öffnen. Das datenabhängige Routing ist anderer Bereich des Infrastrukturcodes, der jetzt durch Funktionen der Elastic Scale-Clientbibliothek abgedeckt wird. Details finden Sie unter [Datenabhängiges Routing](./sql-database-elastic-scale-data-dependent-routing.md)

* **Abfragen mehrerer Shards (MSQ, Multi-Shard Queries)**: Das Abfragen mit mehreren Shard setzt ein, wenn eine Anforderung mehrere (oder alle) Shards umfasst. In einer Abfrage mehrerer Shards wird derselbe T-SQL-Code für alle Shards oder eine Gruppe von Shards ausgeführt. Die Ergebnisse der beteiligten Shards werden unter Verwendung der UNION ALL-Semantik in einem Gesamtergebnis zusammengeführt. Die Funktionalität wird über die Clientbibliothek verfügbar gemacht. Sie verarbeitet u. a. Aufgaben wie: Verbindungsverwaltung, Threadverwaltung, Fehlerbehandlung und Verarbeiten von Zwischenergebnissen. MSQ kann Hunderte von Shards abfragen. Weitere Einzelheiten finden Sie unter [Abfragen mehrerer Shards](./sql-database-elastic-scale-multishard-querying.md).


* **Split-Merge-Dienst**: Wenn die Kapazität der Geschäftsdynamik entsprechend variabel sein muss, müssen Anwendungen in der Lage sein, Daten flexibel über eine Reihe von Datenbanken neu zu verteilen. Elastic Scale stellen einen vom Kunden gehosteten Dienst zur Erweitern und Reduzieren der Kapazität auf Datenebene bereit und zum Verwalten von Hotspots für partitionierte Anwendungen in Situationen, in denen Daten verschoben werden müssen. Dazu werden eine zugrunde liegende Funktion zum Verschieben von Shardlets zwischen verschiedenen Shards bei Bedarf und die Shard-Zuordnungsverwaltung genutzt, um konsistente Zuordnungen und präzise datenabhängige Routingverbindungen sicherzustellen. Einzelheiten finden Sie unter [Aufteilen und Zusammenführen mit Elastic Scale](./sql-database-elastic-scale-overview-split-and-merge.md)

## Allgemeine Sharding-Muster

**Sharding** ist eine Technik zur Verteilung großer Mengen identisch strukturierter Daten über eine Reihe voneinander unabhängiger Datenbanken. Sie ist besonders bei Cloudentwicklern beliebt, die SaaS-Angebote (Software as a Service) für Endbenutzer oder Unternehmen erstellen. Diese Endbenutzer werden häufig als "Mandanten‟ bezeichnet. Sharding kann aus verschiedenen Gründen erforderlich sein: 

* Die Gesamtdatenmenge ist so groß, dass sie aufgrund einschlägiger Beschränkungen nicht in einer einzelnen Datenbank gespeichert werden kann. 
* Der Transaktionsdurchsatz der Arbeitsauslastung überschreitet die Fähigkeiten einer einzelnen Datenbank. 
* Mandanten erfordern u. U. eine physische Trennung von anderen Mandaten, sodass für jeden Mandanten eine eigene Datenbank erforderlich ist. 
* Verschiedene Abschnitte einer Datenbank müssen sich aufgrund von Compliance- oder Leistungsanforderungen oder aus geopolitischen Gründen an verschiedenen geografischen Standorten befinden. 

In anderen Szenarien, z. B. beim Erfassen von Daten von verteilten Geräten, kann das Sharding eingesetzt werden, um eine Gruppe von Datenbanken über einen Zeitraum verteilt zu füllen. Beispielsweise kann jedem Wochentag oder jeder Woche eine eigene Datenbank zugeordnet werden. In diesem Fall kann der Sharding-Schlüssel eine Ganzzahl sein, die das Datum darstellt (und in allen Zeilen der partitionierten Tabellen vorhanden ist). Abfragen zum Abruf von Informationen für einen Datumsbereich müssen von der Anwendung an die Teilmenge von Datenbanken, welche den fraglichen Bereich abdecken, weitergeleitet werden.
 
Das Sharding funktioniert am besten, wenn jede Transaktion in einer Anwendung auf einen einzelnen Wert eines Sharding-Schlüssels begrenzt werden kann. Damit wird sichergestellt, dass alle Transaktionen einer bestimmten Datenbank eigen sind. 

Einige Anwendungen verwenden den einfachsten Ansatz und erstellen für jeden Mandanten eine eigene Datenbank. Dies ist das **Sharding-Muster mit einzelnen Mandanten**, das Isolation, Sicherungs-/Wiederherstellungsfunktion und Ressourcenskalierung auf der Ebene einzelner Mandanten bietet. Beim Sharding mit einzelnen Mandanten ist jede Datenbank mit einem bestimmten Mandanten-ID-Wert (oder Kundenschlüsselwert) verknüpft, wobei der Schlüssel jedoch nicht immer in den Daten vorhanden sein muss. Es ist Aufgabe der Anwendung, die einzelnen Anforderungen an die entsprechende Datenbank weiterzuleiten. 

![Single tenant versus multi-tenant][3]

In anderen Szenarien werden mehrere Mandanten in Datenbanken zusammengefasst, statt sie in getrennten Datenbanken zu isolieren. Dabei handelt es sich um ein typisches **Sharding-Szenario mit mehreren Mandanten**, dem Überlegungen hinsichtlich der Kosten oder der Effizienz oder die Tatsache zugrunde liegen können, dass eine Anwendung eine große Anzahl von sehr kleinen Mandanten verwalten muss. Beim Sharding mit mehreren Mandanten sollen die Zeilen in den Datenbanktabellen einen Schlüssel enthalten, der die Mandanten-ID oder den Sharding-Schlüssel eindeutig identifiziert. Auch hier ist es Aufgabe der Anwendungsschicht, die Anforderung eines Mandanten an die entsprechende Datenbank weiterzuleiten. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-intro/overview.png
[2]:./media/sql-database-elastic-scale-intro/tenancy.png
[3]:./media/sql-database-elastic-scale-intro/single_v_multi_tenant.png
[4]:./media/sql-database-elastic-scale-intro/h_versus_vert.png

<!--HONumber=47-->
