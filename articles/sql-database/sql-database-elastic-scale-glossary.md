<properties 
	pageTitle="Azure Elastic Scale - Glossar" 
	description="Begriffserläuterungen zum Elastic Scale-Feature der Azure SQL-Datenbank" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="sidneyh@microsoft.com"/>

# Elastic Scale - Glossar
Die folgenden Begriffe werden für das Elastic Scale-Feature der Azure SQL-Datenbank definiert.

![Elastic Scale terms][1]

**Datenbank**: Eine Azure SQL-Datenbank. 

**Datenabhängiges Routing**: Die Funktionen, die es einer Anwendung ermöglicht, eine Verbindung mit einer Shard unter Verwendung eines bestimmten Sharding-Schlüssels herzustellen. Vgl. **Abfragen mehrerer Shards**.

**Globale Shard-Zuordnung**: Die Gruppe von Zuordnungen zwischen Sharding-Schlüsseln und den zugehörigen Shards in einer **Shard-Gruppe**. Die globale Shard-Zuordnung wird im **Shard-Zuordnungs-Manager** gespeichert. Vgl. **lokale Shard-Zuordnung**.

**Listen-Shard-Zuordnung**: Eine Shard-Zuordnung, in der die Sharding-Schlüssel einzeln zugeordnet sind. Vgl. **Bereichs-Shard-Zuordnung**.   

**Lokale Shard-Zuordnung**: Die lokale Shard Map wird auf einem Shard gespeichert und enthält Zuordnungen für die Shardlets, die sich auf dem Shard befinden.


**Abfrage mehrerer Shards**: Die Möglichkeit, eine Abfrage für mehrere Shards auszustellen. Die Ergebnisse werden über die UNION ALL-Semantik zurückgegeben (auch als "Auffächerungsabfrage" bezeichnet). Vgl. **Datenabhängiges Routing**.

**Bereichs-Shard-Zuordnung**: Eine Shard-Zuordnung, in der die Shard-Verteilungsstrategie auf mehreren Bereichen zusammenhängender Werte basiert. 


**Verweistabellen**: Tabellen, die nicht partitioniert sind, aber über mehrere Shards hinweg repliziert werden. 

**Shard**: Eine Azure SQL-Datenbank, in der die Daten aus einem partitionierten Dataset gespeichert werden. 

**Shard-Elastizität** (SE): Die Fähigkeit, sowohl **horizontal zu skalieren** als auch **vertikal zu skalieren**.

**Shardtabellen**: Tabellen, die partitioniert sind, deren Daten also anhand ihrer Sharding-Schlüsselwerte über Shards verteilt werden. 

**Sharding-Schlüssel**: Ein Spaltenwert, der bestimmt, wie Daten über Shards hinweg verteilt werden. Folgende Werttypen sind zulässig: int, bigint, varbinary oder uniqueidentifier. 

**Shard-Gruppe**: Die Auflistung von Shards, die der gleichen Shard-Zuordnung im Shard-Zuordnungs-Manager zugeordnet sind.  

**Shardlet**: Alle Daten, die mit einem Wert eines Sharding-Schlüssel in einer Shard verknüpft sind. Ein Shardlet ist die kleinste Einheit der Datenverschiebung, die bei der erneuten Verteilung von Shard-Tabellen möglich ist. 

**Shard-Zuordnung**: Der Satz von Zuordnungen zwischen Sharding-Schlüsseln und den entsprechenden Shards.

**Shard-Zuordnungs-Manager**: Ein Verwaltungsobjekt und Datenspeicher mit den Shard Maps, Shard-Standorten und Zuordnungen für einen oder mehrere Shard-Sätze.

![Mappings][2]


## Verben

**Horizontale Skalierung**: Der Prozess des horizontalen Hochskalierens (oder Herunterskalierens) einer Sammlung von Shards, indem einer Shard Map Shards hinzugefügt oder daraus entfernt werden.

**Zusammenführen**: Der Prozess des Verschiebens von Shardlets von zwei Shards zu einem Shards und das entsprechende Aktualisieren der Shard Map.

**Shardlet-Verschiebung**: Das Verschieben einer einzelnen Shardlet in eine andere Shard. 

**Shard**: Der Prozess des horizontalen Partitionierens von identisch strukturierten Daten über mehrere Datenbanken auf der Basis eines Sharding-Schlüssels.

**Aufteilen**: Das Verschieben mehrerer Shardlets aus einer Shard in eine andere (in der Regel neue) Shard. Als Aufteilungspunkt wird ein Sharding-Schlüssel vom Benutzer bereitgestellt.

**Vertikales Skalieren**: Das Heraufskalieren (oder Herunterskalieren) der Leistungsstufe einer einzelnen Shard. Dazu zählt beispielsweise das Ändern eines Shards von Standard zu Premium (wie aus Leistungsgründen erforderlich). 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png



<!--HONumber=47-->
 