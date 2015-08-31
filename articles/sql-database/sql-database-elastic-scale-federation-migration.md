<properties 
    pageTitle="Migration von Verbunden" 
    description="Beschreibt die Schritte zur Migration einer vorhandenen, mit dem Verbundfeature erstellten App zum elastischen Datenbankmodell." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/14/2015" 
    ms.author="sidneyh"/>

# Migration von Verbunden 

Das Verbundfeature für die Azure SQL-Datenbank wird zusammen mit den Web-/Business-Editionen im September 2015 eingestellt. Zu diesem Zeitpunkt können Anwendungen, die das Verbundfeature verwenden, nicht mehr ausgeführt werden. Für eine erfolgreiche Migration wird dringend empfohlen, die Migration so bald wie möglich zu beginnen, um ausreichend Zeit für Planung und Ausführung zu sichern.

Wenn Ihre Anwendung noch nicht ohne Verbunde verwendet werden kann, wenden Sie sich anhand der [hier](https://support.microsoft.com/kb/3087180) genannten Anweisungen an den Microsoft-Support.

Dieses Dokument liefert den Kontext, Beispiele und eine Einführung in das Dienstprogramm zur Migration von Verbunden, das zeigt, wie eine Verbunde-Anwendung erfolgreich und nahtlos in die APIS der [Clientbibliothek für elastische Datenbanken](http://go.microsoft.com/?linkid=9862592) für Sharding migriert werden kann. Das Ziel des Dokuments ist, Sie durch die vorgeschlagenen Schritte für die Migration einer Verbundanwendung zu führen, ohne Daten verschieben zu müssen.

Es gibt drei Hauptschritte für die Migration einer vorhandenen Verbundanwendung zu einer Anwendung, die Tools für elastische Datenbanken verwendet.

1. [Erstellen von Shard Map Manager von einem Verbundstamm](#create-a-shard-map-manager-from-a-federation-root) 
2. [Ändern der vorhandenen Anwendung](#modify-the-existing-application)
3. [Auswechseln vorhandener Verbundmitglieder](#switch-out-existing-federation-members)
    

### Das Beispielmigrationstool
Um diesen Prozess zu erleichtern, wurde ein [Dienstprogramm für die Migration von Verbunden](http://go.microsoft.com/?linkid=9862613) erstellt. Mit diesem Dienstprogramm können Sie die Schritte 1 und 3 durchführen.

## Erstellen von Shard Map Manager von einem Verbundstamm
Der erste Schritt der Migration einer Verbundanwendung besteht darin, die Metadaten eines Verbundstamms an die Konstrukte eines Shard Map Managers zu klonen.

![Klonen des Verbundstamms zum Shard Map Manager][1]
 
Beginnen Sie mit einer vorhandenen Verbundanwendung in einer Testumgebung.
 
Verwenden Sie das **Dienstprogramm für die Migration von Verbunden**, um die Metadaten des Verbund-Stammelements in Konstrukte des [Shard Map Managers](http://go.microsoft.com/?linkid=9862595) der Clientbibliothek für elastische Datenbanken zu klonen. Analog zu einem Verbundstamm ist die Shard Map Manager-Datenbank eine eigenständige Datenbank, die die Shard Maps (d. h. Verbünde), Referenzen zu Shards (d. h. Verbundmitgliedern) und entsprechende Bereichszuordnungen enthält.

Das Klonen des Verbundstamms zum Shard Map Manager besteht aus dem Kopieren und Übersetzen von Metadaten. Metadaten werden im Verbundstamm nicht geändert. Beachten Sie, dass das Klonen des Verbund-Stammelements mit dem Dienstprogramm für die Migration von Verbunden ein Point-in-Time-Vorgang ist und alle Änderungen an den Verbund-Stammelement oder den Shard-Maps nicht im anderen entsprechenden Datenspeicher übernommen werden. Wenn Änderungen am Verbund-Stammelement während der Testphase der neuen APIs vorgenommen werden, kann das Dienstprogramm für die Migration von Verbunden verwendet werden, um die Shard-Maps zu aktualisieren, sodass sie den aktuellen Zustand darstellen.

![Migration der vorhandenen App für die Verwendung von APIs der Tools für elastische Datenbanken][2]

## Ändern der vorhandenen Anwendung 

Mit Shard Map Manager und den beim Shard Map Manager registrierten Verbundmitgliedern und -bereichen (über das Migrationsdienstprogramm) können Sie die vorhandene Verbundanwendung so ändern, dass sie die Clientbibliothek für elastische Datenbanken verwendet. Wie in der Abbildung oben gezeigt, werden Anwendungsverbindungen über diese APIs über den Shard Map Manager an entsprechende Verbundmitglieder (die jetzt auch Shards sind) weitergeleitet. Die Zuordnung von Verbundmitgliedern zum Shard Map Manager ermöglicht, dass zwei Versionen einer Anwendung – eine, die Verbunde verwendet und eine, die die Clientbibliothek für elastische Datenbanken verwendet - nebeneinander ausgeführt werden können, um die Funktionalität zu überprüfen.

Während der Migration der Anwendung müssen zwei zentrale Änderungen an der vorhandenen Anwendung durchgeführt werden.


#### Änderung 1: Instanziieren eines Shard Map Manager-Objekts: 

Im Gegensatz zu Verbunden interagieren APIs von Tools für elastische Datenbanken mit dem Shard Map Manager über die Klasse **ShardMapManager**. Die Instanziierung eines **ShardMapManager**-Objekts und einer Shard Map kann auf folgende Weise vorgenommen werden:
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### Änderung 2: Weiterleiten von Verbindungen an den entsprechenden Shard 

Bei Verbunden wird eine Verbindung mit einem bestimmten Verbundmitglied wie folgt mit dem Befehl USE FEDERATION eingerichtet:

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Bei den APIs von Tools für elastische Datenbanken wird eine Verbindung zu einem bestimmten Shard über das [datenabhängige Routing](sql-database-elastic-scale-data-dependent-routing.md) mit der Methode **OpenConnectionForKey** für die Klasse **RangeShardMap** eingerichtet.

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

Die Schritte in diesem Abschnitt sind erforderlich, lösen aber möglicherweise nicht alle aufkommenden Migrationsszenarien. Weitere Informationen finden Sie unter [Konzeptionelle Übersicht über Tools für elastische Datenbanken](sql-database-elastic-scale-introduction.md) und [API-Referenz](http://go.microsoft.com/?linkid=9862604).

## Auswechseln vorhandener Verbundmitglieder 

![Auswechseln der Verbundmitglieder für die Shards][3]

Nachdem die Anwendung durch das Einschließen der APIs von Tools für elastische Datenbanken geändert wurde, besteht der letzte Schritt der Migration einer Verbundanwendung darin, die Verbundmitglieder per **SWITCH OUT** auszuwechseln (weitere Informationen finden Sie in der MSDN-Referenz für [ALTER FEDERATION (Azure SQL Database](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx)). Das Endergebnis der Anwendung eines **SWITCH OUT**- Befehls auf ein bestimmtes Verbundmitglied ist, dass alle Verbundbedingungen und Metadaten entfernt werden, sodass das Verbundmitglied als reguläre Azure SQL-Datenbank gerendert wird, ebenso wie jede andere Azure SQL-Datenbank.

Beachten Sie, dass die Ausgabe eines **SWITCH OUT**-Befehls für ein Verbundmitglied ein nur in eine Richtung möglicher Vorgang ist, der nicht rückgängig gemacht werden kann. Nach der Durchführung kann die resultierende Datenbank nicht wieder zu einem Verbund hinzugefügt werden, und der Befehl USE FEDERATION funktioniert für diese Datenbank nicht mehr.

Zum Durchführen des Wechsels wurde ein zusätzliches Argument zum Befehl ALTER FEDERATION hinzugefügt, um ein Verbundmitglied per SWITCH OUT zu wechseln. Zwar kann der Befehl für jedes einzelne Verbundmitglied ausgeführt werden, aber das Federations Migration Utility bietet die Funktionalität, um programmgesteuert jedes Verbundmitglied zu durchlaufen und den Wechselvorgang durchzuführen.

Nachdem der Wechsel für alle vorhandenen Verbundmitglieder durchgeführt wurde, ist die Migration der Anwendung abgeschlossen. Wenn alle Verbundmitglieder ausgewechselt wurden, wird empfohlen, einen DROP FEDERATION-Vorgang für den Stamm durchzuführen. Dies hat keine Auswirkungen auf frühere Mitglieder, ermöglicht jedoch die problemlose Migration der Stammdatenbank aus der Web und Business-Edition.
  
Das Federations Migration Utility bietet die folgenden Funktionen:

1.    Durchführen eines Klons des Verbundstamms an einen Shard Map Manager. Sie können auswählen, ob Sie den vorhandenen Shard Map Manager für eine neue Azure SQL-Datenbank (empfohlen) oder die vorhandene Verbundstammdatenbank verwenden.
2.    Geben Sie SWITCH OUT für alle Verbundmitglieder in einem Verbund aus.


## Funktionsvergleiche

Obwohl die Tools für elastische Datenbanken viele zusätzliche Funktionen bietet (z. B. [Multi-Shard-Abfragen](sql-database-elastic-scale-multishard-querying.md), [Teilen und Zusammenführen von Shards](sql-database-elastic-scale-overview-split-and-merge.md), [Shard-Elastizität](sql-database-elastic-scale-elasticity.md), [clientseitiges Zwischenspeichern](sql-database-elastic-scale-shard-map-management.md) und anderes mehr), gibt es einige beachtenswerte Verbund-Funktionen, die in den Tools für elastische Datenbanken nicht unterstützt werden.
  
- Die Verwendung von **FILTERING=ON**. Stattdessen wird die Verwendung von zeilenbasierter Sicherheit (RLS) für die Filterung von Zeilen empfohlen. Wie bei der Filterung in Verbunden, fügt RLS automatisch ein Prädikat für alle Abfragen in einer Shardtabelle hinzu. Weitere Informationen hierzu finden Sie unter [Mehrinstanzenfähige Anwendungen mit Tools für elastische Datenbanken und zeilenbasierter Sicherheit](sql-database-elastic-tools-multi-tenant-row-level-security.md). 
 
 Alternativ können Sie auch die Filterlogik in der Abfrage erstellen, die für den Shard verwendet wird: Beispiel:

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 Dies erzielt dasselbe Ergebnis wie:

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Die **Split**-Funktion der Tools für elastische Datenbanken ist nicht vollständig online. Während eines Split-Vorgangs wird jedes einzelne Shardlet für die Dauer der Verschiebung offline geschaltet.
- Für das Split-Feature ist eine manuelle Datenbankbereitstellung und Schemaverwaltung erforderlich.

## Zusätzliche Überlegungen

* Sowohl die Web- als auch die Business-Edition und Verbunde werden im Herbst 2015 eingestellt. Als Teil der Migration einer Verbundanwendung wird dringend empfohlen, Leistungstests in den Editionen Basic, Standard und Premium durchzuführen. 

* Durch die Durchführung der SWITCH OUT-Anweisung für ein Verbundmitglied kann die resultierende Datenbank alle Azure SQL-Datenbankfeatures verwenden (d. h. neue Editionen, Sicherung, PITR, Überwachung usw.)

##Benötigen Sie mehr Zeit für die Migration? 
Wenn Ihre Anwendung noch nicht ohne Verbunde verwendet werden kann, wenden Sie sich anhand der [hier](https://support.microsoft.com/kb/3087180) genannten Anweisungen an den Microsoft-Support.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!---HONumber=August15_HO8-->