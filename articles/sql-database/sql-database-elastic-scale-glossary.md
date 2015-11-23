<properties 
    pageTitle="Tools für elastische Datenbanken – Glossar | Microsoft Azure" 
    description="Erläuterung von Begriffen, die für die Tools für flexible Datenbanken verwendet werden" 
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
    ms.date="11/04/2015" 
    ms.author="ddove;sidneyh"/>

# Tools für elastische Datenbanken – Glossar
Die folgenden Begriffe werden für die [Tools für elastische Datenbanken](sql-database-elastic-scale-introduction.md) von Azure SQL-Datenbank definiert. Die Tools werden zur Verwaltung von [Shard-Zuordnungen](sql-database-elastic-scale-shard-map-management.md) verwendet. Sie beinhalten die [Clientbibliothek](sql-database-elastic-database-client-library.md), das [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md), [elastische Pools](sql-database-elastic-pool.md) und [Abfragen](sql-database-elastic-query-overview.md).

Diese Elemente werden in [Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken](sql-database-elastic-scale-add-a-shard.md) und [Beheben von Problemen bei der Shard-Zuordnung mithilfe der Wiederherstellungs-Manager-Klasse](sql-database-elastic-database-recovery-manager.md).

![Begriffe zur elastischen Skalierung][1]

**Datenbank:** eine Azure SQL-Datenbank.

**Datenabhängiges Routing:** die Funktionen, die es einer Anwendung ermöglicht, eine Verbindung mit einem Shard unter Verwendung eines bestimmten Sharding-Schlüssels herzustellen. Vgl. **Abfragen mehrerer Shards**.

**Globale Shard-Zuordnung:** die Zuordnungen zwischen Sharding-Schlüsseln und den zugehörigen Shards in einer **Shard-Gruppe**. Die globale Shard-Zuordnung wird im **Shard-Zuordnungs-Manager** gespeichert. Vgl. **lokale Shard-Zuordnung**.

**Listen-Shard-Zuordnung:** eine Shard-Zuordnung, in der die Sharding-Schlüssel einzeln zugeordnet sind. Vgl. **Bereichs-Shard-Zuordnung**.

**Lokale Shard-Zuordnung:** wird für einen Shard gespeichert und enthält die Zuordnungen für die Shardlets, die sich in dem Shard befinden.

**Abfrage mehrerer Shards:** die Möglichkeit, eine Abfrage für mehrere Shards durchzuführen. Die Resultsets werden über die UNION ALL-Semantik zurückgegeben (auch als "Auffächerungsabfrage" bezeichnet). Vgl. **Datenabhängiges Routing**.

**Bereichs-Shard-Zuordnung:** eine Shard-Zuordnung, in der die Shard-Verteilungsstrategie auf mehreren Bereichen zusammenhängender Werte basiert.

**Verweistabellen:** Tabellen, die nicht partitioniert sind, aber über mehrere Shards hinweg repliziert werden. Postleitzahlen können z. B. in einer Verweistabelle gespeichert werden.

**Shard:** eine Azure SQL-Datenbank, in der die Daten aus einem horizontal partitionierten DataSet gespeichert werden.

**Shard-Elastizität:** die Fähigkeit, sowohl **horizontal** als auch **vertikal** zu skalieren.

**Shard-Tabellen:** Tabellen, die horizontal partitioniert sind, deren Daten also anhand ihrer Sharding-Schlüsselwerte über Shards verteilt werden.

**Sharding-Schlüssel:** ein Spaltenwert, der bestimmt, wie Daten über Shards hinweg verteilt werden. Folgende Werttypen sind zulässig: **int**, **bigint**, **varbinary** oder **uniqueidentifier**.

**Shard-Gruppe:** die Auflistung von Shards, die der gleichen Shard-Zuordnung im Shard-Zuordnungs-Manager zugeordnet sind.

**Shardlet:** alle Daten, die mit einem Wert eines Sharding-Schlüssels in einem Shard verknüpft sind. Ein Shardlet ist die kleinste Einheit der Datenverschiebung, die bei der erneuten Verteilung von Shard-Tabellen möglich ist.

**Shard-Zuordnung:** der Satz von Zuordnungen zwischen Sharding-Schlüsseln und den entsprechenden Shards.

**Shard-Zuordnungs-Manager:** ein Verwaltungsobjekt und Datenspeicher mit den Shard-Zuordnungen, Shard-Speicherorten und Zuordnungen für eine oder mehrere Shard-Gruppen.

![Zuordnungen][2]


##Verben

**Horizontales Skalieren:** das Skalieren einer Auflistung von Shards durch Hinzufügen oder Entfernen von Shards zu bzw. aus einer Shard-Zuordnung, wie unten dargestellt.

![Horizontale und vertikale Skalierung][3]

**Zusammenführen:** das Verschieben von Shardlets aus zwei Shards in einen Shard und das entsprechende Aktualisieren der Shard-Zuordnung.

**Shardlet-Verschiebung:** das Verschieben eines einzelnen Shardlets in einen anderen Shard.

**Shard:** das horizontale Partitionieren identisch strukturierter Daten über mehrere Datenbanken anhand eines Sharding-Schlüssels.

**Aufteilen:** das Verschieben mehrerer Shardlets aus einem Shard in einen anderen (i. d. R. neuen) Shard. Als Aufteilungspunkt wird ein Sharding-Schlüssel vom Benutzer bereitgestellt.

**Vertikales Skalieren:** das Heraufskalieren (oder Herunterskalieren) der Leistungsstufe eines einzelnen Shards. Dies erfolgt z. B. durch Ändern eines Shards von Standard in Premium (wodurch mehr Computerressourcen zur Verfügung stehen).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png
 

<!---HONumber=Nov15_HO3-->