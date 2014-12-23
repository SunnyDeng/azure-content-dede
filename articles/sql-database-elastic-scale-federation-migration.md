<properties title="Federations Migration" pageTitle="Migration von Verbunden" description="Outlines the steps to migrate an existing app built with Federations feature to Elastic Scale model." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

#Migration von Verbunden 

Die Funktion Azure SQL-Datenbankverbunde wird zusammen mit den Web-/Business-Editionen im September 2015 eingestellt. Ab diesem Zeitpunkt werden Anwendungen, die die Verbunde-Funktion nutzen, nicht mehr ausgeführt. Um eine erfolgreiche Migration sicherzustellen, wird dringend dazu aufgefordert, so bald wie möglich mit der Migration zu beginnen, um ausreichend Zeit für Planung und Ausführung zu haben. Dieses Dokument liefert den Kontext, Beispiele und eine Einführung in das Dienstprogramm zur Migration von Verbunden, das zeigt, wie eine Verbunde-Anwendung erfolgreich in die [Azure SQL DB Elastic Scale Vorschau-APIs](http://go.microsoft.com/?linkid=9862592) migriert werden kann. Dieses Dokument soll Sie Schritt für Schritt durch die empfohlenen Schritte zum Migrieren einer Verbunde-Anwendung Verschieben von Daten führen.

Es gibt drei wichtige Schritte zum Migrieren einer vorhandenen Verbunde-Anwendung in eine Anwendung, die Elastic Scale-APIs verwendet.

1. [Erstellen von Shard Map-Manager aus einem Verbund-Stammelement] 
2. [Ändern der vorhandenen Anwendung]
3. [Auslagern der vorhandenen Verbund-Elemente]
    

### Das Beispiel-Migrationstool
Um diesen Prozess zu erleichtern, wurde ein [Dienstprogramm für die Migration von Verbunden](http://go.microsoft.com/?linkid=9862613) geschaffen. Dieses Dienstprogramm führt die Schritte 1 und 3 durch. 

## <a name="create-shard-map-manager"></a>Erstellen von Shard Map-Manager von einem Verbund-Stammelement
Der erste Schritt beim Migrieren einer Verbund-Anwendung besteht darin, die Metadaten eines Verbund-Stammelements mit den Konstrukten des Shard Map-Managers zu klonen. 

![Clone the federation root to the shard map manager][1]
 
Beginnen Sie mit einer vorhandenen Verbunde-Anwendung in einer Testumgebung.
 
Verwenden Sie das **Dienstprogramm für die Migration von Verbunden**, um die Metadaten des Verbund-Stammelements in Konstrukte des Elastic Scale [Shard Map-Managers](http://go.microsoft.com/?linkid=9862595) zu klonen. Analog zu einem Verbund-Stammelement ist die Shard Map-Manager-Datenbank eine eigenständige Datenbank, die die Shard-Maps(d. h. Verbunde), Verweise auf Shards (d. h. Verbundmitglieder) und die jeweiligen Bereichszuordnungen enthält. 

Das Klonen des Verbund-Stammelements zum Shard Map-Manager besteht darin, die Metadaten zu kopieren und zu übersetzen. Es werden keine Metadaten im Verbund-Stammelement geändert. Beachten Sie, dass das Klonen des Verbund-Stammelements mit dem Dienstprogramm für die Migration von Verbunden ein Point-in-Time-Vorgang ist und alle Änderungen an den Verbund-Stammelement oder den Shard-Maps nicht im anderen entsprechenden Datenspeicher übernommen werden. Wenn Änderungen am Verbund-Stammelement während der Testphase der neuen APIs vorgenommen werden, kann das Dienstprogramm für die Migration von Verbunden verwendet werden, um die Shard-Maps zu aktualisieren, sodass sie den aktuellen Zustand darstellen. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## Ändern der vorhandenen Anwendung 

Wenn der Shard Map-Manager vorhanden ist und die Verbundmitglieder und -bereiche für den Shard Map-Manager registriert sind (erfolgt über das Dienstprogramm zur Migration), kann die vorhandene Verbundanwendung so abgeändert werden, dass sie Elastic Scale APIs verwendet. Wie in der Abbildung oben dargestellt, werden die Verbindungen der Anwendung über die Elastic Scale APIs durch den Shard Map-Manager an die entsprechenden Verbundmitglieder weitergeleitet (jetzt auch ein Shard). Das Zuordnen von Verbundmitgliedern zum Shard Map-Manager ermöglicht es, zwei Versionen einer Anwendung - Version, die Verbunde verwendet, und eine Version, die Elastic Scale verwendet - nebeneinander auszuführen, um die Funktionalität zu überprüfen.   

Während der Migration der Anwendung müssen zwei Core-Änderungen an der vorhandenen Anwendung vorgenommen werden.


#### Änderung 1: Instanziieren eines Shard Map-Manager-Objekts: 

Im Unterschied zu Verbunden interagieren Elastic Scale APIs mit dem Shard Map-Manager über die Klasse **ShardMapManager**. Die Instanziierung eines **ShardMapManager**-Objekts und einer Shard Map kann auf folgende Weise vorgenommen werden:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Änderung 2: Routen von Verbindungen zum entsprechenden Shard 

Bei Verbunden wird eine Verbindung mit einem bestimmten Verbundmitglied über den USE FEDERATION-Befehl wie folgt hergestellt:  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Bei den Elastic Scale APIs wird eine Verbindung zu einem bestimmten Shard über das [datenabhängige Routing](./sql-database-elastic-scale-data-dependent-routing.md) mit der Methode **OpenConnectionForKey** für die Klasse **RangeShardMap** eingerichtet. 

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

Die Schritte in diesem Abschnitt sind erforderlich, aber behandeln möglicherweise nicht alle Migrationsszenarien, die auftreten können. Weitere Informationen finden Sie in der [Übersicht über Elastic Scale](./sql-database-elastic-scale-introduction.md) und in der [API-Referenz](http://go.microsoft.com/?linkid=9862604).

## Auslagern der vorhandenen Verbundelemente 

![Switch out the federation members for the shards][3]

Nachdem die Anwendung durch die Aufnahme der Elastic Scale APIs geändert wurde, besteht der letzte Schritt in der Migration einer Verbunde-Anwendung darin, die Verbundmitglieder auszulagern, **SWITCH OUT** (weitere Informationen dazu finden Sie in der MSDN-Referenz zu [ALTER FEDERATION (Azure SQL-Datenbank](http://msdn.microsoft.com/en-us/library/azure/hh597475.aspx)). Das Endergebnis der Anwendung eines Befehls **SWITCH OUT** auf ein bestimmtes Verbundmitglied ist es, dass alle Verbundbedingungen und Metadaten entfernt werden, sodass das Verbundmitglied als reguläre Azure SQL-Datenbank gerendert wird, ebenso wie jede andere Azure SQL-Datenbank. 

Beachten Sie, dass die Ausgabe eines Befehls **SWITCH OUT** für ein Verbundmitglied ein nur in eine Richtung möglicher Vorgang ist, der nicht rückgängig gemacht werden kann. Nachdem der Befehl ausgeführt wurde, kann die daraus resultierende Datenbank nicht mehr wieder einem Verbund hinzugefügt werden, und die USE FEDERATION-Befehle funktionieren nicht mehr für diese Datenbank. 

Zum Ausführen des Wechsels wurde ein zusätzliches Argument dem Befehl ALTER FEDERATION hinzugefügt wechseln, um ein Verbundmitglied auszulagern (SWITCH OUT).  Der Befehl kann zwar für einzelne Verbundmitglieder ausgegeben werden, aber das Dienstprogramm für die Migration von Verbunden liefert Funktionen, mit denen jedes Verbundmitglied programmgesteuert durchlaufen und der Auslagerungsvorgang dafür durchgeführt wird. 

Nachdem der Wechsel für alle vorhandenen Verbundmitglieder durchgeführt wurde, ist die Migration der Anwendung abgeschlossen.  
Das Dienstprogramm für die Migration von Verbunden ermöglicht Folgendes 

1.    Durchführen einer Klonung des Verbund-Stammelements zu einem Shard Map-Manager.  Es kann festgelegt werden, ob der vorhandene Shard Map-Manager in einer neuen Azure SQL-Datenbank (empfohlen) oder in der vorhandenen Verbund-Stammdatenbank abgelegt werden soll.
2.    Ausgeben des Befehls SWITCH OUT für alle Verbundmitglieder in einem Verbund.


##Funktionsvergleiche  
Obwohl die Elastic Scale viele zusätzliche Funktionen bietet (z. B. [Multi-Shard-Abfragen](./sql-database-elastic-scale-multishard-querying.md), [Teilen und Zusammenführen von Shards](./sql-database-elastic-scale-overview-split-and-merge.md), [Shard-Elastizität](./sql-database-elastic-scale-elasticity.md), [clientseitiges Zwischenspeichern](./sql-database-elastic-scale-shard-map-management.md) und anderes mehr), gibt es einige beachtenswerte Verbund-Funktionen, die Elastic Scale nicht unterstützt.
  

- Die Verwendung von **FILTERING=ON**. Elastic Scale unterstützt derzeit auf Zeilenebene keine Filterung. Eine Gegenmaßnahme besteht darin, die Filterlogik in die Abfrage für den Shard wie folgt einzubauen: 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

Führt zum gleiche Ergebnis wie:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Die Elastic Scale-Funktion **Split** ist nicht vollständig online. Bei einem Split-Vorgang wird jeder einzelne Shardlet während der Dauer der Verschiebung offline geschaltet.
- Die Elastic Scale-Funktion Split erfordert eine manuelle Datenbank-Bereitstellung und Schemaverwaltung.

## Weitere Überlegungen

* Die Web- und Business Edition und Verbunde werden im Herbst 2015 eingestellt. Im Rahmen der Migration einer Verbund-Anwendung empfiehlt es sich dringend, Leistungstests für Basic-, Standard- und Premium-Editionen durchzuführen. 

* Das Ausführen der Anweisung SWITCH OUT auf ein Verbundelement befähigt die resultierende Datenbank dazu, alle Azure SQL-Datenbank-Funktionen zu nutzen (d. h. neue Editionen, Sicherung, PITR, Überwachung usw.) 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[Erstellen von Shard Map-Manager von einem Verbund-Stammelement aus]:#create-shard-map-manager
[Ändern der vorhandenen Anwendung]:#Modify-the-Existing-Application
[Auslagern der vorhandenen Verbundelemente]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
