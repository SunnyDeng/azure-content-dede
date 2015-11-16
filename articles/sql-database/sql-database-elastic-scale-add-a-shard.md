<properties 
	pageTitle="Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken | Microsoft Azure" 
	description="Erfahren Sie, wie Sie einem Shard-Satz mithilfe der Elastic Scale-APIs neue Shards hinzufügen." 
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

# Hinzufügen eines Shards mithilfe der Tools für elastische Datenbanken

## So fügen Sie einen Shard für einen neuen Bereich oder Schlüssel hinzu  

Anwendungen müssen häufig einfach neue Shards hinzufügen, um Daten zu verwalten, die von neuen Schlüsseln oder Schlüsselbereichen für eine Shard Map erwartet werden, welche bereits vorhanden ist. Eine Anwendung beispielsweise, bei der Sharding über die Mandanten-ID durchgeführt wird, muss unter Umständen einen neuen Shard für einen neuen Mandanten bereitstellen, oder Daten, bei denen das Sharding monatlich durchgeführt wird, benötigen möglicherweise einen neuen Shard, der vor dem Start eines jeweils neuen Monats bereitgestellt wird.

Wenn der neue Schlüsselwertbereich nicht bereits Teil einer vorhandenen Zuordnung (Mapping) ist, ist es sehr einfach, den neuen Shard hinzuzufügen und den neuen Schlüssel oder Bereich mit dem Shard zu verknüpfen.

### Beispiel: Hinzufügen eines Shards und seines Bereichs zu einer vorhandenen Shard-Zuordnung
Im Beispiel unten wurden eine Datenbank namens **sample\_shard\_2** mit allen erforderlichen, darin enthaltenen Schemaobjekten so erstellt, dass sie den Bereich [300, 400) abdecken.

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Alternativ können Sie PowerShell zur Erstellung eines neuen Shard-Zuordnungs-Managers nutzen. Ein Beispiel ist [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) verfügbar.
## So fügen Sie einen Shard für einen leeren Teil eines vorhandenen Bereichs hinzu  

In einigen Fällen haben Sie einem Shard vielleicht bereits einen Bereich zugeordnet und diesen teilweise mit Daten gefüllt. Jetzt möchten Sie aber weitere Daten an einen anderen Shard umleiten. Angenommen, Sie führen das Sharding nach einem Bereich von Tagen durch und haben einem Shard bereits 50 Tage zugeordnet. Am 24. Tag sollen zukünftige Daten jedoch an einen anderen Shard weitergeleitet werden. Das [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md) für elastische Datenbanken kann diesen Vorgang durchführen. Wenn jedoch keine Datenverschiebung erforderlich ist (weil noch keine Daten für den Bereich der Tage [25, 50), d. h. einschließlich Tag 25 bis ausschließlich Tag 50, vorliegen), können Sie den ganzen Vorgang direkt mithilfe der Shard-Zuordnungs-Verwaltungs-APIs durchführen.

### Beispiel: Aufteilen eines Bereichs und Zuweisen des leeren Teils zu einem neu hinzugefügten Shard

Eine Datenbank namens „sample\_shard\_2“ sowie alle erforderlichen, darin enthaltenen Schemaobjekte wurden erstellt.

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Wichtig:** Verwenden Sie dieses Verfahren nur, wenn Sie sicher sind, dass der Bereich für die aktualisierte Zuordnung leer ist. Durch die oben genannten Methoden werden keine Daten für den verschobenen Bereich überprüft. Deshalb empfiehlt es sich, Prüfroutinen im Code zu implementieren. Wenn der verschobene Bereich Zeilen enthält, stimmt die tatsächliche Datenverteilung nicht mit der aktualisierten Shard Map überein. Verwenden Sie das [Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md), um den Vorgang durchzuführen.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=Nov15_HO2-->