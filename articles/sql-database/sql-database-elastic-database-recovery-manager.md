<properties 
	pageTitle="Verwenden von Recovery Manager zum Beheben von Shardzuordnungsproblemen | Microsoft Azure" 
	description="Verwenden der RecoveryManager-Klasse zum Beheben von Problemen mit Shardzuordnungen" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg"
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="ddove"/>

# Verwenden der RecoveryManager-Klasse zur Behebung von Problemen mit der Shard-Zuordnung

Die [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx)-Klasse bietet ADO.NET-Anwendungen die Möglichkeit, Inkonsistenzen zwischen der globalen Shardzuordnung (GSM) und der lokalen Shardzuordnung (LSM) in einer horizontal partitionierten Datenbank leicht zu erkennen und zu beheben.

Die GSM und die LSM verfolgen die Zuordnung der einzelnen Datenbanken in einer horizontal partitionierten Umgebung. Gelegentlich kommt es zu einem Bruch zwischen der GSM und der LSM. Verwenden Sie in diesem Fall die RecoveryManager-Klasse, um den Bruch zu erkennen und zu reparieren.

Die RecoveryManager-Klasse ist Teil der [Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library).


![Shardzuordnung][1]


Begriffsdefinitionen finden Sie unter [Tools für elastische Datenbanken – Glossar](sql-database-elastic-scale-glossary.md). Eine Erläuterung dazu, wie der **ShardMapManager** zum Verwalten von Daten in einer Shardlösung eingesetzt wird, finden Sie unter [Shard-Zuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md).


## Gründe für die Verwendung von Recovery Manager

In einer Umgebung mit horizontal partitionierten Datenbanken gibt es eine Reihe von Datenbankservern. Jeder Server enthält eine Anzahl von Datenbanken: eine pro Benutzer in einer mehrinstanzenfähigen Lösung. Jede Datenbank muss zugeordnet werden, sodass Aufrufe an den richtigen Server und die richtige Datenbank weitergeleitet werden können. Datenbanken werden nach einem Shardingschlüssel nachverfolgt, und jedem Server wird ein Bereich von Schlüsselwerten zugewiesen. Beispielsweise kann ein Shardingschlüssel die Kundennamen "D" bis "F" darstellen. Die Zuordnung aller Server und ihrer wichtigsten Bereiche sind in der globalen Shardzuordnung enthalten. Jeder Server enthält auch eine Zuordnung der auf dem Shard enthaltenen Datenbanken, die als lokale Shardzuordnung (Local Shard Map, LSM) bezeichnet wird. Die LSM dient zum Überprüfen zwischengespeicherter Daten. (Wenn eine App eine Verbindung mit einem Shard herstellt, wird die Zuordnung für den schnellen Abruf mit der App zwischengespeichert. Die LSM überprüft die Zuordnung.)

Mithilfe eines Tools wie z. B. der Clienttoolbibliothek für elastische Datenbanken können Sie Daten aus einem Shard in einen anderen verschieben. Tritt während der Verschiebung eine Unterbrechung auf, sind die GSM und die LSM eventuell nicht mehr synchron. Andere Gründe:

1. Eine Inkonsistenz, die durch das Löschen eines Shards entstanden ist, dessen Bereich anscheinend nicht länger genutzt wird, oder die durch das Umbenennen eines Shards verursacht wurde. Das Löschen eines Shards führt zu einer **verwaisten Shardzuordnung**. Eine umbenannte Datenbank kann auf ähnliche Weise zu einer verwaisten Shardzuordnung führen. In diesem Fall muss einfach der Shardspeicherort aktualisiert werden. 
2. Ein Geofailoverereignis tritt ein. Um fortzufahren, müssen der Servername, der Datenbankname und/oder die Shardzuordnungsdetails für sämtliche Shards in einer Shardzuordnung aktualisiert werden. Bei einem Geofailover sollte eine solche Wiederherstellungslogik innerhalb des Failoverworkflows automatisiert werden. 
3. Entweder ein Shard oder die ShardMapManager-Datenbank wird auf einen früheren Zeitpunkt wiederhergestellt. 
 
Die Automatisierung von Wiederherstellungsaktionen gewährleistet eine reibungslose Verwaltbarkeit für geofähige Datenbanken und vermeidet manuelle, von Personen durchzuführende Aktionen. Sie ist auch hilfreich bei Wiederherstellungsszenarien, in denen Daten versehentlich gelöscht wurden.

Weitere Informationen zu den Azure SQL-Datenbanktools für elastische Datenbanken und zur Georeplikation und Wiederherstellung finden Sie in folgenden Artikeln:

* [Features für elastische Datenbanken in Azure SQL-Datenbank](sql-database-elastic-scale-introduction.md) 
* [Geschäftskontinuität in Azure SQL-Datenbank](sql-database-business-continuity.md) 
* [Erste Schritte mit Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md)  
* [Shardzuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md)

## Abrufen von RecoveryManager aus einem ShardMapManager 

Der erste Schritt ist die Erstellung eine RecoveryManager-Instanz. Die [GetRecoveryManager-Methode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) gibt den Recovery Manager für die aktuelle [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)-Instanz zurück. Zur Behebung von Inkonsistenzen in der Shardzuordnung müssen Sie zuerst den RecoveryManager für die jeweilige Shardzuordnung abrufen.

	ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

In diesem Beispiel wird der RecoveryManager vom ShardMapManager aus initialisiert. Der ShardMapManager, der eine ShardMap enthält, ist ebenfalls bereits initialisiert.

Da dieser Anwendungscode die Shardzuordnung selbst verändert, sollten die in der Factorymethode verwendeten Anmeldeinformationen (im obigen Beispiel „smmConnectionString“) nur schreibgeschützte Berechtigungen für die GSM-Datenbank besitzen, auf die durch die Verbindungszeichenfolge verwiesen wird. Diese Anmeldeinformationen unterscheiden sich in der Regel von Anmeldeinformationen, die für das Öffnen von Verbindungen zum datenabhängigen Routing verwendet werden. Weitere Informationen hierzu finden Sie unter [Verwalten von Anmeldeinformationen in der Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-manage-credentials.md).

## Entfernen eines gelöschten Shards aus der ShardMap

Die [DetachShard-Methode](https://msdn.microsoft.com/library/azure/dn842083.aspx) trennt den angegebenen Shard aus der Shardzuordnung und löscht die Zuordnungen, die dem Shard zugeordnet sind.

* Der location-Parameter ist der Speicherort des Shards, der getrennt wird, insbesondere dessen Servername und Datenbankname. 
* Der shardMapName-Parameter ist der Shardzuordnungsname. Dieser ist nur erforderlich, wenn mehrere Shardzuordnungen von demselben Shardzuordnungs-Manager verwaltet werden. Optional. 

**Wichtig:** Verwenden Sie dieses Verfahren nur, wenn Sie sicher sind, dass der Bereich für die aktualisierte Zuordnung leer ist. Durch die oben genannten Methoden werden keine Daten für den verschobenen Bereich überprüft. Deshalb empfiehlt es sich, Prüfroutinen im Code zu implementieren.

Im folgenden Beispiel wird der RecoveryManager zum Entfernen von Shards aus der Shardzuordnung eingesetzt. Die Shardzuordnung gibt den Shardspeicherort in der GSM vor dem Löschen des Shards an. Da der Shard gelöscht wurde, wird davon ausgegangen, dass dies absichtlich geschehen ist und der Shardingschlüsselbereich nicht mehr verwendet wird. Wenn dies nicht der Fall ist, können Sie eine Point-in-Time-Wiederherstellung durchführen, um den Shard aus einem früheren Zeitpunkt wiederherzustellen. (Lesen Sie in diesem Fall den folgenden Abschnitt, um Shardinkonsistenzen zu ermitteln.) Da davon ausgegangen wird, dass die Datenbank absichtlich gelöscht wurde, besteht die letzte administrative Bereinigungsaktion darin, den Eintrag für den Shard im Shardzuordnungs-Manager zu löschen. Dies verhindert, dass die Anwendung versehentlich Informationen in einen unerwarteten Bereich schreibt.
	
	rm.DetachShard(s.Location, customerMap); 

## So erkennen Sie Zuordnungsunterschiede 

Die [DetectMappingDifferences-Methode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) wählt eine der Shardzuordnungen (entweder lokal oder global) aus, gibt sie als gültige Quelle zurück und stimmt Zuordnungen in beiden Shardzuordnungen (GSM und LSM) darauf ab.

	rm.DetectMappingDifferences(location, shardMapName);

* Der *location*-Parameter ist der Speicherort des Shards, insbesondere dessen Servername und Datenbankname. 
* Der *shardMapName*-Parameter ist der Shardzuordnungsname. Dieser ist nur erforderlich, wenn mehrere Shardzuordnungen von demselben Shardzuordnungs-Manager verwaltet werden. Optional. 

## So lösen Sie Zuordnungsunterschiede auf

Die [ResolveMappingDifferences-Methode](https://msdn.microsoft.com/de-DE/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) wählt eine der Shardzuordnungen (entweder lokal oder global) als gültige Quelle aus und stimmt Zuordnungen in beiden Shardzuordnungen (GSM und LSM) darauf ab.

	ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution);
   
* Der *RecoveryToken*-Parameter listet die Unterschiede in den Zuordnungen zwischen GSM und LSM für den jeweiligen Shard auf. 

* Mit der [MappingDifferenceResolution-Enumeration](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) wird die Methode angegeben, die zum Beheben des Unterschieds zwischen den Shardzuordnungen verwendet werden soll. **MappingDifferenceResolution.KeepShardMapping** wird für den Fall empfohlen, dass die LSM die genaue Zuordnung enthält und daher die Zuordnung im Shard verwendet werden soll. Dies ist normalerweise bei einem Failover der Fall: Der Shard befindet sich jetzt auf einem neuen Server. Da der Shard zuerst (über die RecoveryManager.DetachShard-Methode) aus der GSM entfernt werden muss, ist auf der GSM keine Zuordnung mehr vorhanden. Aus diesem Grund muss die LSM verwendet werden, um die Shardzuordnung wiederherzustellen.

## Anfügen eines Shards an die ShardMap nach dem Wiederherstellen eines Shards 

Die [AttachShard-Methode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) fügt den angegebenen Shard an die Shardzuordnung an. Anschließend erkennt es eventuelle Inkonsistenzen in der Shardzuordnung und aktualisiert die Zuordnungen so, dass sie dem Shard zum Zeitpunkt der Wiederherstellung entsprechen. Es wird davon ausgegangen, dass die Datenbank auf den ursprünglichen Datenbanknamen (vor der Wiederherstellung des Shards) umbenannt wird, da die Point-in-Time-Wiederherstellung standardmäßig eine neue Datenbank mit angefügtem Zeitstempel verwendet.

	rm.AttachShard(location, shardMapName) 

* Der *location*-Parameter entspricht dem Servernamen und dem Datenbanknamen des angefügten Shards. 

* Der *shardMapName*-Parameter ist der Shardzuordnungsname. Dieser ist nur erforderlich, wenn mehrere Shardzuordnungen von demselben Shardzuordnungs-Manager verwaltet werden. Optional.

In diesem Beispiel wird der Shardzuordnung ein Shard hinzugefügt, der vor kurzem von einem früheren Zeitpunkt wiederhergestellt wurde. Da der Shard (d. h. die Zuordnung für den Shard in der LSM) wiederhergestellt wurde, ist er möglicherweise inkonsistent mit dem Shardeintrag in der GSM. Außerhalb dieses Beispielcodes wurde der Shard wiederhergestellt und auf den ursprünglichen Namen der Datenbank umbenannt. Da er wiederhergestellt wurde, wird davon ausgegangen, dass die Zuordnung in der LSM die vertrauenswürdige Zuordnung ist.

	rm.AttachShard(s.Location, customerMap); 
	var gs = rm.DetectMappingDifferences(s.Location); 
	  foreach (RecoveryToken g in gs) 
	   { 
	   rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
	   } 

## Aktualisieren von Shardspeicherorten nach einem Geofailover (Wiederherstellung) der Shards

Bei einem Geofailover wird die sekundäre Datenbank für den Schreibzugriff freigegeben und als neue primäre Datenbank festgelegt. Der Name des Servers und möglicherweise der der Datenbank (je nach Konfiguration) können von den ursprünglichen primären Namen abweichen. Daher müssen die Zuordnungseinträge für den Shard in der GSM und in der LSM korrigiert werden. Wenn die Datenbank auf einen anderen Namen oder Speicherort oder auf einen früheren Zeitpunkt wiederhergestellt wird, kann dies ebenso zu Inkonsistenzen in den Shardzuordnungen führen. Der Shardzuordnungs-Manager verarbeitet die Verteilung der geöffneten Verbindungen an die richtige Datenbank. Die Verteilung basiert auf den Daten in der Shardzuordnung und auf dem Wert des Shardingschlüssels, an den die Anwendungsanforderung gerichtet ist. Nach einem Geofailover müssen diese Informationen auf den richtigen Servernamen, den richtigen Datenbanknamen und die richtige Shardzuordnung der wiederhergestellten Datenbank aktualisiert werden.

## Bewährte Methoden

Geofailover und Wiederherstellung sind Vorgänge, die in der Regel von einem Cloudadministrator der Anwendung verwaltet werden, der bewusst eine der Geschäftskontinuitätsfunktionen von Azure SQL-Datenbank einsetzt. Die Planung der Geschäftskontinuität erfordert Prozesse, Verfahren und Maßnahmen, um zu gewährleisten, dass der Geschäftsbetrieb ohne Unterbrechung fortgesetzt werden kann. Die in der RecoveryManager-Klasse verfügbaren Methoden sollten in diesem Workflow verwendet werden, um sicherzustellen, dass die GSM und die LSM basierend auf der durchgeführten Wiederherstellungsaktion auf dem neuesten Stand gehalten werden. Es gibt fünf grundlegende Schritte, um ordnungsgemäß sicherzustellen, dass die GSM und die LSM nach einem Failoverereignis die genauen Informationen enthalten. Der Anwendungscode zum Ausführen dieser Schritte kann in vorhandene Tools und Workflows integriert werden.

1. Rufen Sie den Recovery Manager aus dem ShardMapManager ab. 
2. Trennen Sie den alten Shard von der Shardzuordnung.
3. Fügen Sie den neuen Shard an die Shardzuordnung an, einschließlich des neuen Shardspeicherorts.
4. Ermitteln Sie Inkonsistenzen in der Zuordnung zwischen GSM und LSM. 
5. Lösen Sie Unterschiede zwischen der GSM und der LSM auf, indem Sie die LSM als vertrauenswürdig einstufen. 

In diesem Beispiel werden die folgende Schritte ausgeführt: 1. Entfernen von Shards aus der Shardzuordnung, die Shardspeicherorte von vor dem Failover angeben. 2. Anfügen von Shards an die Shardzuordnung, die die neuen Shardspeicherorte angeben. (Der Parameter „Configuration.SecondaryServer“ ist der neue Servername, aber derselbe Datenbankname.) 3. Abrufen der Wiederherstellungstoken durch das Erkennen von Zuordnungsunterschieden zwischen der GSM und der LSM für die einzelnen Shards. 4. Auflösen der Inkonsistenzen durch Verwenden der LSM-Zuordnung als vertrauenswürdige Zuordnung für die einzelnen Shards.

	var shards = smm.GetShards(); 
	foreach (shard s in shards) 
	{ 
	 if (s.Location.Server == Configuration.PrimaryServer) 
		 { 
		  ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database); 
		
		  rm.DetachShard(s.Location); 
		
		  rm.AttachShard(slNew); 
		
		  var gs = rm.DetectMappingDifferences(slNew); 
	
		  foreach (RecoveryToken g in gs) 
			{ 
			   rm.ResolveMappingDifferences(g, 						MappingDifferenceResolution.KeepShardMapping); 
			} 
		} 
	} 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 

<!---HONumber=Nov15_HO3-->