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
Die folgenden Begriffe werden für die [Tools für elastische Datenbanken](sql-database-elastic-scale-introduction.md) von Azure SQL-Datenbank definiert. Zu den Tools gehören die [Clientbibliothek](sql-database-elastic-database-client-library.md), das [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md), [elastische Pools](sql-database-elastic-pool.md) und [Abfragen](sql-database-elastic-query-overview.md).

![Begriffe zur elastischen Skalierung][1]

**Datenbank**: eine Azure SQL-Datenbank.

**Datenabhängiges Routing:** die Funktionen, die es einer Anwendung ermöglichen, eine Verbindung mit einem Shard unter Verwendung eines bestimmten Shardingschlüssels herzustellen. Siehe auch **Abfragen von mehreren Shards**.

**Globale Shardzuordnung:** die Zuordnungen zwischen Shardingschlüsseln und den zugehörigen Shards in einer **Shardgruppe**. Die globale Shardzuordnung wird im **Shard-Zuordnungs-Manager** gespeichert. Siehe auch **Lokale Shardzuordnung**.

**Listen-Shardzuordnung:** eine Shardzuordnung, in der die Shardingschlüssel einzeln zugeordnet sind. Siehe auch **Bereichs-Shardzuordnung**.

**Lokale Shardzuordnung:** Die lokale Shardzuordnung wird für einen Shard gespeichert und enthält die Zuordnungen für die Shardlets, die sich in dem Shard befinden.

**Abfrage von mehreren Shards:** die Möglichkeit, eine Abfrage für mehrere Shards durchzuführen. Die Resultsets werden über die UNION ALL-Semantik zurückgegeben (auch als „Auffächerungsabfrage“ bezeichnet). Siehe auch **Datenabhängiges Routing**.

**Bereichs-Shardzuordnung**: eine Shardzuordnung, in der die Shardverteilungsstrategie auf mehreren Bereichen zusammenhängender Werte basiert.

**Verweistabellen**: Tabellen, die nicht horizontal partitioniert sind, aber über mehrere Shards hinweg repliziert werden. Postleitzahlen können z. B. in einer Verweistabelle gespeichert werden.

**Shard**: eine Azure SQL-Datenbank, in der die Daten aus einem horizontal partitionierten DataSet gespeichert werden.

**Shardelastizität:** die Fähigkeit, sowohl **horizontal** als auch **vertikal** zu skalieren.

**Shardtabellen:** Tabellen, die horizontal partitioniert sind, deren Daten also anhand ihrer Shardingschlüsselwerte über Shards verteilt werden.

**Shardingschlüssel**: ein Spaltenwert, der bestimmt, wie Daten über Shards hinweg verteilt werden. Folgende Werttypen sind zulässig: **int**, **bigint**, **varbinary** oder **uniqueidentifier**.

**Shardgruppe:** die Auflistung von Shards, die der gleichen Shardzuordnung im Shard-Zuordnungs-Manager zugeordnet sind.

**Shardlet:** alle Daten, die mit einem Wert eines Shardingschlüssels in einem Shard verknüpft sind. Ein Shardlet ist die kleinste Einheit der Datenverschiebung, die bei der erneuten Verteilung von Shard-Tabellen möglich ist.

**Shardzuordnung**: der Satz von Zuordnungen zwischen Shardingschlüsseln und ihre jeweiligen Shards.

**Shard-Zuordnungs-Manager:** ein Verwaltungsobjekt und Datenspeicher mit den Shardzuordnungen, Shardspeicherorten und Zuordnungen für eine oder mehrere Shardgruppen.

![Zuordnungen][2]


##Verben

**Horizontales Skalieren:** das Skalieren einer Auflistung von Shards durch Hinzufügen oder Entfernen von Shards zu bzw. aus einer Shardzuordnung, wie unten dargestellt.

![Horizontale und vertikale Skalierung][3]

**Zusammenführen:** das Verschieben von Shardlets aus zwei Shards in einen Shard und das entsprechende Aktualisieren der Shardzuordnung.

**Shardletverschiebung:** das Verschieben eines einzelnen Shardlets in einen anderen Shard.

**Shard:** das horizontale Partitionieren identisch strukturierter Daten über mehrere Datenbanken anhand eines Shardingschlüssels.

**Aufteilen:** das Verschieben mehrerer Shardlets aus einem Shard in einen anderen (i. d. R. neuen) Shard. Als Aufteilungspunkt wird ein Sharding-Schlüssel vom Benutzer bereitgestellt.

**Vertikales Skalieren:** das Heraufskalieren (oder Herunterskalieren) der Leistungsstufe eines einzelnen Shards. Dies erfolgt z. B. durch Ändern eines Shards von Standard in Premium (wodurch mehr Computerressourcen zur Verfügung stehen).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png
 

<!---HONumber=Nov15_HO2-->