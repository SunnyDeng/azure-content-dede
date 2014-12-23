<properties title="Multi-Shard Querying" pageTitle="Abfragen von mehreren Shards" description="Run queries across shards using Elastic Scale APIs." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale, multi-shard, multishard, querying" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Abfragen von mehreren Shards
**Das Abfragen von mehreren Shards** wird für Aufgaben wie Datensammlung/Berichterstellung verwendet, für die das Ausführen einer Abfrage erforderlich ist, die sich über mehrere Shards erstreckt. (Vergleichen Sie dies mit dem [datenabhängigen Routing](./sql-database-elastic-scale-data-dependent-routing.md), bei dem die gesamte Arbeit in einem einzigen Shard durchgeführt wird.) 

In der Elastic Scale-Clientbibliothek wird ein neuer Namespace mit dem Namen **Microsoft.Azure.SqlDatabase.ElasticScale.Query** eingeführt, der das Abfragen mehrerer Shards mithilfe einer einzelnen Abfrage und eines einzelnen Ergebnisses ermöglicht. Diese stellt eine Abfrageabstraktion über eine Sammlung von Shards bereit. Darüber hinaus werden auch alternative Ausführungsrichtlinien bereitgestellt, insbesondere Teilergebnisse zur Behandlung von Fehlern beim Abfragen über mehrere Shards hinweg.  

Der Haupteinstiegspunkt in das Abfragen mehrerer Shards ist die **MultiShardConnection**-Klasse. Wie beim datenabhängigen Routing verwendet die API die vertrauten **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)**-Klassen und -Methoden. Bei der **SqlClient**-Bibliothek besteht der erste Schritt darin, eine **SqlConnection** und dann einen **SqlCommand** für die Verbindung zu erstellen. Führen Sie dann den Befehl mithilfe einer der **Execute**-Methoden aus. **SqlDataReader** durchläuft abschließend die Resultsets, die von der Befehlausführung zurückgegeben wurden. Die APIs für das Abfragen mehrerer Shards umfassen die folgenden Schritte: 

1. Erstellen Sie eine **MultiShardConnection**.
2. Erstellen Sie einen **MultiShardCommand** für eine **MultiShardConnection**.
3. Führen Sie den Befehl aus.
4. Verwenden Sie die Ergebnisse des **MultiShardDataReader**. 

Ein wichtiger Unterschied ist die Erstellung von Verbindungen mit mehreren Shards. Wenn **SqlConnection** in einer einzigen Datenbank ausgeführt wird, verwendet **MultiShardConnection** eine ***Sammlung von Shards*** als Eingabe. Die Sammlung von Shards kann aus einer Shard-Map aufgefüllt werden. Die Abfrage wird dann mithilfe der **UNION ALL**-Semantik für die Sammlung von Shards ausgeführt, um ein einziges Gesamtergebnis zu erhalten. Optional kann der Name des Shards, aus dem die Zeile stammt, mithilfe der **ExecutionOptions**-Eigenschaft der Ausgabe auf Befehl hinzugefügt werden. Das folgende Codebeispiel veranschaulicht die Verwendung von Abfragen mehrerer Shards mithilfe eines bestimmten **ShardMap** mit dem Namen *myShardMap*. 

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
 

Beachten Sie, dass **myShardMap.GetShards()** aufgerufen wird. Diese Methode ruft alle Shards aus der Shard-Map ab und stellt eine einfache Möglichkeit zum Ausführen einer Abfrage über alle Shards hinweg aus dieser Shard-Map bereit. Die Sammlung von Shards für eine Abfrage mehrerer Shards kann durch Ausführen einer LINQ-Abfrage über die Sammlung, die von dem Aufruf von **myShardMap.GetShards()** zurückgegeben wird, weiter optimiert werden. In Kombination mit der Teilergebnisrichtlinie wurde die aktuelle Funktion bei der Abfrage mehrerer Shards so entwickelt, dass diese gut mit bis zu Hunderten von Shards funktioniert.
Eine Einschränkung von Abfragen mehrerer Shards ist derzeit die fehlende Überprüfung von Shards und Shardlets, die abgefragt werden. Während beim datenabhängigem Routing überprüft wird, dass ob ein bestimmtes Shard Teil der Shard-Map zum Zeitpunkt der Abfrage ist, führen Abfragen mehrerer Shards dieses Überprüfung nicht durch. Dies kann dazu führen, dass Abfragen mehrerer Shards auf Shards ausgeführt werden, die seitdem aus der Shard-Map entfernt wurden.

###Abfragen von mehreren Shards und Teilungs-/Zusammenführungsvorgänge

Beim Abfragen von mehreren Shards wird nicht überprüft, ob Shardlets in dem abgefragten Shard an laufenden Teilungs-/Zusammenführungsvorgängen teilnehmen. Dies kann zu Inkonsistenzen führen, wenn Zeilen aus demselben Shardlet für mehrere Shards in derselben Abfrage mehrerer Shards angezeigt werden. Beachten Sie diese Einschränkungen, und ziehen Sie beim Ausführen von Abfragen mehrerer Shards einen Ausgleich laufender Teilungs-/Zusammenführungsvorgänge und Änderungen an der Shard-Map in Betracht.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
