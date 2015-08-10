<properties 
	pageTitle="Abfragen von mehreren Shards" 
	description="Führen Sie mithilfe der Clientbibliothek für elastische Datenbanken Abfragen mehrerer Shards durch." 
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
	ms.date="07/24/2015" 
	ms.author="sidneyh"/>

# Abfragen von mehreren Shards

## Übersicht

Das **Abfragen von mehreren Shards** wird für Aufgaben wie Datensammlung/Berichterstellung verwendet, für die das Ausführen einer Abfrage über mehrere Shards erforderlich ist. \(Vergleichen Sie dies mit dem [datenabhängigen Routing](sql-database-elastic-scale-data-dependent-routing.md), bei dem die gesamte Arbeit in einem einzigen Shard durchgeführt wird.\) Informationen zum Verwenden von SQL Server Management Studio finden Sie unter [Erste Schritte mit Abfragen für elastische Datenbanken](sql-database-elastic-query-getting-started.md).

In der Clientbibliothek für elastische Datenbanken wird der neue Namespace **Microsoft.Azure.SqlDatabase.ElasticScale.Query** eingeführt, der das Abfragen mehrerer Shards mithilfe einer einzelnen Abfrage und eines einzelnen Ergebnisses ermöglicht. Diese stellt eine Abfrageabstraktion über eine Sammlung von Shards bereit. Darüber hinaus werden auch alternative Ausführungsrichtlinien bereitgestellt, insbesondere Teilergebnisse zur Behandlung von Fehlern beim Abfragen über mehrere Shards hinweg.

Der Haupteinstiegspunkt in das Abfragen mehrerer Shards ist die **MultiShardConnection**-Klasse. Wie beim datenabhängigen Routing verwendet die API die vertrauten **\[System.Data.SqlClient\]\(http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)**-Klassen und -Methoden. Bei der **SqlClient**-Bibliothek besteht der erste Schritt darin, ein **SqlConnection**-Element und dann ein **SqlCommand**-Element für die Verbindung zu erstellen. Führen Sie anschließend den Befehl mithilfe einer der **Execute**-Methoden aus. **SqlDataReader** durchläuft abschließend die Ergebnissätze, die von der Befehlsausführung zurückgegeben wurden. Die APIs für das Abfragen mehrerer Shards umfassen die folgenden Schritte: 

1. Erstellen Sie ein **MultiShardConnection**-Element.
2. Erstellen Sie ein **MultiShardCommand**-Element für ein **MultiShardConnection**-Element.
3. Führen Sie den Befehl aus.
4. Verwenden Sie die Ergebnisse mit dem **MultiShardDataReader**-Element. 

Ein wichtiger Unterschied ist die Erstellung von Verbindungen mit mehreren Shards. Wenn **SqlConnection** in einer Einzeldatenbank ausgeführt wird, verwendet **MultiShardConnection** eine ***Sammlung von Shards*** als Eingabe. Die Sammlung von Shards kann aus einer Shard-Map aufgefüllt werden. Die Abfrage wird dann mithilfe der **UNION ALL**-Semantik für die Sammlung von Shards ausgeführt, um ein einziges Gesamtergebnis zu erhalten. Optional kann der Name des Shards, aus dem die Zeile stammt, mithilfe der **ExecutionOptions**-Eigenschaft im Befehl der Ausgabe hinzugefügt werden. Das folgende Codebeispiel veranschaulicht die Verwendung von Abfragen mehrerer Shards mithilfe eines bestimmten **ShardMap**-Elements mit dem Namen *myShardMap*.

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
            	{ 
                	while (sdr.Read())
                    	{ 
                        	var c1Field = sdr.GetString(0); 
                        	var c2Field = sdr.GetFieldValue<int>(1); 
                        	var c3Field = sdr.GetFieldValue<Int64>(2);
                    	} 
             	} 
           } 
    } 
 

Beachten Sie, dass **myShardMap.GetShards\(\)** aufgerufen wird. Diese Methode ruft alle Shards aus der Shard-Zuordnung ab und stellt damit eine einfache Möglichkeit zum Ausführen einer Abfrage an alle Datenbanken bereit. Die Sammlung von Shards für eine Abfrage mehrerer Shards kann durch Ausführen einer LINQ-Abfrage über die Sammlung, die von dem Aufruf von **myShardMap.GetShards\(\)** zurückgegeben wird, weiter optimiert werden. In Kombination mit der Teilergebnisrichtlinie wurde die aktuelle Funktion bei der Abfrage mehrerer Shards so entwickelt, dass diese gut mit bis zu Hunderten von Shards funktioniert. Eine Einschränkung von Abfragen mehrerer Shards ist derzeit die fehlende Überprüfung von Shards und Shardlets, die abgefragt werden. Während beim datenabhängigem Routing überprüft wird, dass ob ein bestimmtes Shard Teil der Shard-Map zum Zeitpunkt der Abfrage ist, führen Abfragen mehrerer Shards dieses Überprüfung nicht durch. Dies kann dazu führen, dass Abfragen mehrerer Shards auf Datenbanken ausgeführt werden, die seitdem aus der Shard-Zuordnung entfernt wurden.

## Abfragen mehrerer Shards und Aufteilungs-/Zusammenführungsvorgänge

Beim Abfragen mehrerer Shards wird nicht überprüft, ob Shardlets im abgefragten Shard an laufenden Aufteilungs-/Zusammenführungsvorgängen teilnehmen. Dies kann zu Inkonsistenzen führen, wenn Zeilen aus demselben Shardlet für mehrere Datenbanken in derselben Abfrage mehrerer Shards angezeigt werden. Beachten Sie diese Einschränkungen, und ziehen Sie beim Ausführen von Abfragen mehrerer Shards einen Ausgleich laufender Aufteilungs-/Zusammenführungsvorgänge und Änderungen an der Shard-Zuordnung in Betracht.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO5-->