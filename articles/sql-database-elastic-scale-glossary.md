<properties title="Azure Elastic Scale Glossary" pageTitle="Azure Elastic Scale - Glossar" description="Erklärung von Begriffen, die für das Elastic Scale-Feature der Azure SQL-Datenbank verwendet werden" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Elastic Scale - Glossar
Die folgenden Begriffe werden für das Elastic Scale-Feature der Azure SQL-Datenbank definiert.

![Elastic Scale terms][1]

**Datenbank**: Eine Azure SQL-Datenbank. 

**Datenabhängiges Routing**: Die Funktionalität, mit der eine Anwendung mit einem bestimmten Sharding-Schlüssel eine Verbindung mit einem Shard herzustellen kann. Siehe auch **Abfragen mehrerer Shards**.

**Globale Shard Map**: Der Satz von Zuordnungen zwischen Sharding-Schlüsseln und den entsprechenden Shards innerhalb eines **Shard-Satzes**. Die GSM ist wird im **Shard Map Manager** gespeichert. Siehe auch **Lokale Shard Map**.

**Listen-Shard-Map**: Eine Shard Map, in der Sharding-Schlüssel einzeln zugeordnet werden. Siehe auch **Bereichs-Shard-Map**.   

**Lokale Shard Map**: Die lokale Shard Map wird auf einem Shard gespeichert und enthält Zuordnungen für die Shardlets, die sich auf dem Shard befinden.


**Abfragen mehrerer Shards**: Die Möglichkeit, eine Abfrage für mehrere Shards auszustellen. Die Ergebnisse werden über die UNION ALL-Semantik zurückgegeben (auch als "Auffächerungsabfrage" bezeichnet). Siehe auch **Datenabhängiges Routing**.

**Bereichs-Shard-Map**: Eine Shard Map, in der die Shard-Verteilungsstrategie auf mehreren Bereichen mit zusammenhängenden Werten basiert. 


**Referenztabellen**: Tabellen, die nicht freigegeben, aber über mehrere Shards repliziert werden. 

**Shard**: Eine Azure SQL-Datenbank, in der Daten aus einem Shard-Dataset gespeichert werden. 

**Shard-Elastizität** (SE): Die Möglichkeit, sowohl eine **horizontale Skalierung** als auch eine **vertikale Skalierung** durchzuführen.

**Shard-Tabellen**: Tabellen, für die ein Shard konfiguriert wurde, d. h. deren Daten auf der Basis ihrer Sharding-Schlüsselwerte über Shards verteilt sind. 

**Sharding-Schlüssel**: Ein Spaltenwert, der bestimmt, wie Daten über Shards verteilt werden. Folgende Werttypen sind zulässig: int, bigint, varbinary oder uniqueidentifier. 

**Shard-Satz**: Die Sammlung von Shards, die derselben Shard Map im Shard Map Manager zugeordnet sind.  

**Shardlet**: Alle mit einem einzigen Wert eines Sharding-Schlüssel auf einem Shard verbundenen Daten. Ein Shardlet ist die kleinste Einheit der Datenverschiebung, die bei der erneuten Verteilung von Shard-Tabellen möglich ist. 

**Shard Map**: Der Satz von Zuordnungen zwischen Sharding-Schlüsseln und den entsprechenden Shards.

**Shard Map Manager**: Ein Verwaltungsobjekt und Datenspeicher mit den Shard Maps, Shard-Standorten und Zuordnungen für einen oder mehrere Shard-Sätze.

![Mappings][2]


##Verben

**Horizontales Skalieren**: Der Prozess des horizontalen Hochskalierens (oder Herunterskalierens) einer Sammlung von Shards, indem einer Shard Map Shards hinzugefügt oder daraus entfernt werden.

**Zusammenführen**: Der Prozess des Verschiebens von Shardlets von zwei Shards zu einem Shards und das entsprechende Aktualisieren der Shard Map.

**Shardlet verschieben**: Der Prozess des Verschiebens eines einzigen Shardlets an einen anderen Shard. 

**Shard konfigurieren**: Der Prozess des horizontalen Partitionierens von identisch strukturierten Daten über mehrere Datenbanken auf der Basis eines Sharding-Schlüssels.

**Aufteilen**: Der Prozess des Verschiebens mehrerer Shardlets von einem Shard zu einem anderen (normalerweise neuen) Shard. Als Aufteilungspunkt wird ein Sharding-Schlüssel vom Benutzer bereitgestellt.

**Vertikal skalieren**: Der Prozess des Hochskalierens (oder Herunterskalierens) der Leistungsebene eines einzelnen Shards. Dazu zählt beispielsweise das Ändern eines Shards von Standard zu Premium (wie aus Leistungsgründen erforderlich). 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png



<!--HONumber=35.1-->
