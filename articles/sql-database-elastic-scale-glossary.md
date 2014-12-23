<properties title="Azure Elastic Scale Glossary" pageTitle="Azure Elastic Scale - Glossar" description="Explanation of terms used for Elastic Scale feature of Azure SQL Database" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Elastic Scale - Glossar
Die folgenden Begriffe werden für die Elastic Scale-Funktion von Azure SQL-Datenbank definiert.

![Elastic Scale terms][1]

**Datenbank**: Eine Azure SQL-Datenbank. 

**Datenabhängiges Routing**: Die Funktionen, die es einer Anwendung ermöglicht, eine Verbindung mit einer Shard unter Verwendung eines bestimmten Sharding-Schlüssels herzustellen. Vgl. **Abfragen mehrerer Shards**.

**Globale Shard-Zuordnung**: Die Gruppe von Zuordnungen zwischen Sharding-Schlüsseln und den zugehörigen Shards in einer **Shard-Gruppe**. Die globale Shard-Zuordnung wird im **Shard-Zuordnungs-Manager** gespeichert. Vgl. **lokale Shard-Zuordnung**.

**Listen-Shard-Zuordnung**: Eine Shard-Zuordnung, in der die Sharding-Schlüssel einzeln zugeordnet sind. Vgl. **Bereichs-Shard-Zuordnung**.   

**Lokale Shard-Zuordnung**: Die lokale Shard-Zuordnung wird für eine Shard gespeichert und enthält die Zuordnungen für die Shardlets, die sich in der Shard befinden.

**Abfragen mehrerer Shards**: Die Fähigkeit, eine Abfrage für mehrere Shards auszuführen, die Ergebnismengen werden mithilfe der UNION ALL-Semantik zurückgegeben (auch Auffächerungsabfrage genannt). Vgl. **Datenabhängiges Routing**.

**Bereichs-Shard-Zuordnung**: Eine Shard-Zuordnung, in der die Shard-Verteilungsstrategie auf mehreren Bereichen zusammenhängender Werte basiert. 

**Verweistabellen**: Tabellen, die nicht partitioniert sind, aber über mehrere Shards hinweg repliziert werden. 

**Shard**: Eine Azure SQL-Datenbank, in der die Daten aus einem partitionierten Dataset gespeichert werden. 

**Shard-Elastizität** (SE): Die Fähigkeit, sowohl **horizontal zu skalieren**als auch **vertikal zu skalieren**.

**Partitionierte Tabellen**: Tabellen, die partitioniert sind, deren Daten also anhand ihrer Sharding-Schlüsselwerte über Shards verteilt werden. 

**Sharding-Schlüssel**: Ein Spaltenwert, der bestimmt, wie Daten über Shards hinweg verteilt werden. Der Wert kann von einem der folgenden Typen sein: int, bigint, varbinary oder uniqueidentifier. 

**Shard-Gruppe**: Die Auflistung von Shards, die der gleichen Shard-Zuordnung im Shard-Zuordnungs-Manager zugeordnet sind.  

**Shardlet**: Alle Daten, die mit einem Wert eines Sharding-Schlüssel in einer Shard verknüpft sind. Ein Shardlet stellt die kleinste Einheit einer möglichen Datenverschiebung dar, wenn Sharding-Tabellen neu verteilt werden. 

**Shard-Zuordnung**: Eine Gruppe von Zuordnungen zwischen Sharding-Schlüsseln und den zugehörigen Shards.

**Shard-Zuordnungs-Manager**: Ein Verwaltungsobjekt und Datenspeicher mit den Shard-Zuordnungen, Shard-Speicherorten und Zuordnungen für eine oder mehrere Shard-Gruppen.


##Verben

**Horizontale Skalierung**: Das Skalieren einer Auflistung von Shards durch Hinzufügen oder Entfernen von Shards zu bzw. aus einer Shard-Zuordnung.

**Zusammenführen**: Das Verschieben von Shardlets aus zwei Shards in eine Shards und das entsprechende Aktualisieren der Shard-Zuordnung.

**Shardlet-Verschiebung**: Das Verschieben einers einzelnen Shardlet in eine andere Shard. 

**Shard**: Das horizontale Partitionieren identisch strukturierter Daten über mehrere Datenbanken anhand eines Sharding-Schlüssels.

**Aufteilen**: Das Verschieben mehrerer Shardlets aus einer Shard in eine andere (in der Regel neue) Shard. Der Benutzer gibt als Aufteilungspunkt einen Sharding-Schlüssel an.

**Vertikales Skalieren**: Das Heraufskalieren (oder Herunterskalieren) der Leistungsstufe einer einzelnen Shard. Beispielsweise die Änderung der Leistungsstufe einer Shard von Standard in Premium (wenn die Leistungsanforderungen dies erforderlich machen). 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
