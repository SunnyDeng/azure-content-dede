<properties
    pageTitle="Übersicht über Funktionen der Tools für elastische Datenbanken | Microsoft Azure"
    description="SaaS-Entwickler (Software-as-a-Service) können mit diesen Tools problemlos elastische und skalierbare Datenbanken in der Cloud erstellen."
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="ddove;sidneyh"/>

# Übersicht über Features für elastische Datenbanken

Mit den Features für **elastische Datenbanken** können Sie nahezu unbegrenzt viele Datenbankressourcen der **Azure SQL-Datenbank** zur Erstellung von Lösungen für transaktionale Workloads und insbesondere Software-as-a-Service (SaaS)-Anwendungen verwenden. Die Features für elastische Datenbanken umfassen Folgendes:

* Tools für elastische Datenbanken: Diese beiden Tools vereinfachen die Entwicklung und Verwaltung von Sharded-Datenbanklösungen. Zur Verfügung stehen die [Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library.md) und das [Split-Merge-Tool für elastische Datenbanken](sql-database-elastic-scale-overview-split-and-merge.md). 
* [Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md) (Vorschauversion): Bei einem Pool handelt es sich um eine Sammlung von Datenbanken, der Sie jederzeit Datenbanken hinzufügen bzw. aus der Sie jederzeit Datenbanken entfernen können. Die Datenbanken im Pool teilen sich eine feste Menge an Ressourcen – sogenannten DTUs (Database Transaction Units). Sie zahlen einen festen Preis für die Ressourcen. Dadurch können Sie problemlos Ihre Kosten kalkulieren und die Leistung verwalten. 
* [Aufträge für elastische Datenbanken](sql-database-elastic-jobs-overview.md) (Vorschauversion): Verwalten Sie eine große Anzahl von Azure SQL-Datenbanken mithilfe von Aufträgen. Führen Sie mithilfe von Aufträgen ganz einfach administrative Vorgänge aus, z. B. Schemaänderungen, die Verwaltung von Anmeldeinformationen, Verweisdatenupdates, die Leistungsdatensammlung oder die Mandanten- (Kunden-) Telemetrieerfassung.
* [Abfrage für elastische Datenbanken](sql-database-elastic-query-overview.md) (Vorschauversion): Mit der Abfrage können Sie eine Transact-SQL-Abfrage für mehrere Datenbanken ausführen. Dies ermöglicht eine Verknüpfung mit Berichtstools wie Excel, PowerBI, Tableau usw.

Die folgende Grafik zeigt eine Architektur mit den **Features für elastische Datenbanken** im Kontext einer Sammlung von Datenbanken.

![Tools für elastische Datenbanken][1]

Eine Druckversion dieser Grafik finden Sie in der [Übersicht zu elastischen Datenbanken](http://aka.ms/axmybc).

Die Datenbankfarbe symbolisiert Schemas. Datenbanken mit der gleichen Farbe verwenden die gleichen Schemas.

1. Eine Reihe von **Azure SQL-Datenbanken** werden von Azure unter Verwendung einer Shardingarchitektur gehostet. 
2. Die **Clientbibliothek für elastische Datenbanken** dient zum Verwalten einer Shardgruppe.
3. Eine Teilmenge der Datenbanken wird einem **Pool für elastische Datenbanken** zugewiesen. (Siehe [Verwalten des explosionsartigen Wachstums elastischer Datenbanken](sql-database-elastic-pool.md).) 
4. Ein **Auftrag für die elastische Datenbank** führt T-SQL-Skripts für alle Datenbanken aus.
5. Mit dem **Split-Merge-Tool** werden Daten zwischen Shards verschoben.
6. Mit der **Abfrage für elastische Datenbanken** können Sie eine Abfrage für alle Datenbanken in der Shardgruppe erstellen.
  
## Versprechen und Herausforderungen

Flexibilität und Skalierbarkeit für Cloudanwendungen lassen sich bei Compute und Blob-Speicher durch einfaches Hinzufügen oder Entfernen von Einheiten erreichen. Bei der zustandsbehafteten Datenverarbeitung in relationalen Datenbanken ist es jedoch eine Herausforderung geblieben. Diese Herausforderungen treten vor allem in den beiden folgenden Szenarien auf:

* Vergrößern und Verkleinern der Kapazität für den relationalen Datenbankteil der Workload.
* Verwalten von Hotspots, die Auswirkungen auf eine bestimmte Teilmenge von Daten haben können – z. B. auf besonders aktive Endkunden (Mandant).

In der Vergangenheit wurden Szenarios wie diese durch eine Investition in größere Datenbankserver zur Unterstützung der Anwendung gelöst. Diese Option ist in der Cloud jedoch beschränkt, wo für die gesamte Verarbeitung vordefinierte Standardhardware verwendet wird. Stattdessen stellt die Verteilung von Daten und Verarbeitung auf mehrere identisch strukturierte Datenbanken (die als „Sharding“ bezeichnete horizontale Skalierung) eine Alternative zur herkömmlichen Hochskalierung im Hinblick auf Kosten und Elastizität dar.

## Horizontale und vertikale Skalierung

Die folgende Abbildung zeigt die horizontalen und vertikalen Skalierungsdimensionen. Hierbei handelt es sich um die grundlegenden Skalierungsmöglichkeiten für elastische Datenbanken.

![Horizontale und vertikale Skalierung][2]

Horizontales Skalieren meint das Hinzufügen oder Entfernen von Datenbanken zur Anpassung der Kapazität oder der allgemeinen Leistung. Dies wird auch als „Ausskalieren“ bezeichnet. Sharding ist eine gängige Methode für die horizontale Skalierung, bei der Daten in einer Sammlung von identisch strukturierten Datenbanken partitioniert werden.

Vertikales Skalieren bezeichnet das Erhöhen oder Verringern der Leistungsstufe einer einzelnen Datenbank. Dies wird auch als "zentrales Hochskalieren" bezeichnet.

Die meisten Datenbankanwendungen für Clouds verwenden eine Kombination aus diesen beiden Ansätzen. Eine SaaS (Software as a Service)-Anwendung kann z. B. das horizontale Skalieren für Bereitstellungen an neue Endkunden nutzen und das vertikale Skalieren, damit die Ressourcen für die Datenbanken der einzelnen Endkunden bei entsprechendem Workload vergrößert oder verkleinert werden können.

* Die horizontale Skalierung wird mithilfe der [Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library.md) verwaltet.

* Bei der vertikalen Skalierung wird mithilfe von Azure PowerShell-Cmdlets die Dienstebene geändert, oder es werden Datenbanken einem Pool für elastische Datenbanken zugewiesen.

## Muster für einzelne oder mehrere Mandanten

Beim *Sharding* werden große Mengen identisch strukturierter Daten auf mehrere unabhängige Datenbanken verteilt. Sie ist besonders bei Cloudentwicklern beliebt, die SaaS-Angebote (Software as a Service) für Endbenutzer oder Unternehmen erstellen. Diese Endbenutzer werden häufig als „Mandanten“ bezeichnet. Sharding kann aus verschiedenen Gründen erforderlich sein:

* Die Gesamtdatenmenge ist so groß, dass sie aufgrund einschlägiger Beschränkungen nicht in einer einzelnen Datenbank gespeichert werden kann.
* Der Transaktionsdurchsatz der Workload überschreitet die Fähigkeiten einer einzelnen Datenbank.
* Mandanten erfordern u. U. eine physische Trennung von anderen Mandaten, sodass für jeden Mandanten eine eigene Datenbank erforderlich ist.
* Verschiedene Abschnitte einer Datenbank müssen sich aufgrund von Compliance- oder Leistungsanforderungen oder aus geopolitischen Gründen an verschiedenen geografischen Standorten befinden.

In anderen Szenarien, z. B. beim Erfassen von Daten von verteilten Geräten, kann das Sharding eingesetzt werden, um eine Gruppe von Datenbanken über einen Zeitraum verteilt zu füllen. Beispielsweise kann jedem Wochentag oder jeder Woche eine eigene Datenbank zugeordnet werden. In diesem Fall kann der Sharding-Schlüssel eine Ganzzahl sein, die das Datum darstellt (und in allen Zeilen der partitionierten Tabellen vorhanden ist). Abfragen zum Abruf von Informationen für einen Datumsbereich müssen von der Anwendung an die Teilmenge von Datenbanken, welche den fraglichen Bereich abdecken, weitergeleitet werden.

Das Sharding funktioniert am besten, wenn jede Transaktion in einer Anwendung auf einen einzelnen Wert eines Sharding-Schlüssels begrenzt werden kann. Damit wird sichergestellt, dass alle Transaktionen einer bestimmten Datenbank eigen sind.

Einige Anwendungen verwenden den einfachsten Ansatz und erstellen für jeden Mandanten eine eigene Datenbank. Dies ist das **Shardingmuster mit einzelnen Mandanten**, das Isolation, Sicherungs-/Wiederherstellungsfunktion und Ressourcenskalierung auf der Ebene einzelner Mandanten bietet. Beim Sharding mit einzelnen Mandanten ist jede Datenbank mit einem bestimmten Mandanten-ID-Wert (oder Kundenschlüsselwert) verknüpft, wobei der Schlüssel jedoch nicht immer in den Daten vorhanden sein muss. Es ist Aufgabe der Anwendung, die einzelnen Anforderungen an die entsprechende Datenbank weiterzuleiten – dies wird durch die Clientbibliothek vereinfacht.

![Einzelinstanzen und Mehrinstanzen][4]

In anderen Szenarien werden mehrere Mandanten in Datenbanken zusammengefasst, statt sie in getrennten Datenbanken zu isolieren. Dabei handelt es sich um ein typisches **Shardingszenario mit mehreren Mandanten**, dem die Tatsache zugrunde liegen kann, dass eine Anwendung eine große Anzahl von sehr kleinen Mandanten verwalten muss. Beim Sharding mit mehreren Mandanten sollen die Zeilen in den Datenbanktabellen einen Schlüssel enthalten, der die Mandanten-ID oder den Sharding-Schlüssel eindeutig identifiziert. Auch hier ist es Aufgabe der Anwendungsschicht, die Anforderung eines Mandanten an die entsprechende Datenbank weiterzuleiten. Dieser Vorgang kann durch die Clientbibliothek für elastische Datenbanken unterstützt werden. Darüber hinaus kann die Sicherheit auf Zeilenebene zum Filtern der Zeilen verwendet werden, auf die jeder Mandant Zugriff hat. Weitere Informationen hierzu finden Sie unter [Mehrinstanzenfähige Anwendungen mit Tools für elastische Datenbanken und zeilenbasierter Sicherheit](sql-database-elastic-tools-multi-tenant-row-level-security.md). Beim Sharding mit mehreren Mandanten ist möglicherweise eine Neuverteilung der Daten zwischen den Datenbanken erforderlich, die mit dem Split-Merge-Tool für elastische Datenbanken durchgeführt wird.

### Verschieben von Daten aus Datenbanken mit mehreren Mandanten in Datenbanken mit Einzelmandant
Wenn Sie eine SaaS-Anwendung erstellen, wird Interessenten üblicherweise eine Testversion der Software bereitgestellt. In diesem Fall ist es aus Kostengründen sinnvoll, für die Daten eine Datenbank mit mehreren Mandanten zu verwenden. Wenn aus dem Interessenten allerdings ein Kunde wird, ist eine Datenbank mit nur einem Mandanten die bessere Wahl, da diese eine bessere Leistung bietet. Falls der Kunde während des Testzeitraums Daten erstellt hat, können Sie diese mithilfe des [Split-Merge-Tools](sql-database-elastic-scale-overview-split-and-merge.md) aus der Datenbank mit mehreren Mandanten in die neue Einzelmandantendatenbank verschieben.

## Nächste Schritte

Unter [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md) finden Sie eine Beispiel-App zur Veranschaulichung der Clientbibliothek.

Zur Verwendung des Split-Merge-Tools müssen Sie die [Sicherheit konfigurieren](sql-database-elastic-scale-split-merge-security-configuration.md).

Einzelheiten zum Pool für elastische Datenbanken finden Sie unter [Überlegungen zum Preis und zur Leistung von Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md) sowie im [Tutorial](sql-database-elastic-pool-portal.md) zum Erstellen eines neuen Pools.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

### Geben Sie uns Feedback!
Was können wir besser tun? Wird das Feature in diesem Thema deutlich erklärt? Oder ist irgendein Teil verwirrend? Wir möchten Sie zufrieden stellen. Teilen Sie uns über die Abstimmschaltflächen mit, ob wir Fehler gemacht haben (oder erfolgreich waren). Wenn wir Kontakt zu Ihnen aufnehmen sollen, geben Sie in Ihrem Feedback Ihre E-Mail-Adresse an.


<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/tools.png
[2]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]: ./media/sql-database-elastic-scale-introduction/overview.png
[4]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

<!---HONumber=AcomDC_0302_2016-->