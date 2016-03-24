<properties
   pageTitle="Überwachen Ihres Workloads mit dynamischen Verwaltungssichten | Microsoft Azure"
   description="Informationen zum Überwachen Ihres Workloads mit dynamischen Verwaltungssichten."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Überwachen Ihres Workloads mit dynamischen Verwaltungssichten

Dieser Artikel beschreibt, wie Sie mit dynamischen Verwaltungssichten Ihren Workload überwachen und die Ausführung von Abfragen in Azure SQL Data Warehouse untersuchen.



## Überwachen von Verbindungen

Sie können die Sicht *sys.dm\_pdw\_nodes\_exec\_connections* zum Abrufen von Informationen zu den Verbindungen nutzen, die mit Ihrer Azure SQL Data Warehouse-Datenbank hergestellt wurden. Darüber hinaus ist die Sicht *sys.dm\_exec\_sessions* hilfreich beim Abrufen von Informationen zu allen aktiven Benutzerverbindungen.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


Verwenden Sie die folgende Abfrage zum Abrufen der Informationen zur aktuellen Verbindung.

```

SELECT *
FROM sys.dm_pdw_nodes_exec_connections AS c
   JOIN sys.dm_pdw_nodes_exec_sessions AS s
   ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;

```





## Untersuchen der Ausführung von Abfragen
Es gibt möglicherweise Situationen, in denen die Abfrage nicht abgeschlossen oder länger als erwartet ausgeführt wird. In solchen Fällen können Sie die folgenden Schritte zum Sammeln von Daten und Eingrenzen des Problems befolgen.



### SCHRITT 1: Suchen der zu untersuchenden Abfrage

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

Speichern Sie die Anforderungs-ID der Abfrage.



### SCHRITT 2: Überprüfen, ob die Abfrage auf Ressourcen wartet

```

-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;

```


Die Ergebnisse der obigen Abfrage liefern Ihnen den Wartezustand Ihrer Anforderung.

- Wenn die Abfrage auf Ressourcen einer anderen Abfrage wartet, lautet der Status **AcquireResources**.
- Wenn die Abfrage über alle erforderlichen Ressourcen verfügt und nicht wartet, ist der Status **Granted**. Untersuchen Sie in diesem Fall die Abfrageschritte.




### SCHRITT 3: Bestimmen des am längsten dauernden Schritts der Abfrage

Verwenden Sie die Anforderungs-ID, um eine Liste aller verteilten Abfrageschritte abzurufen. Untersuchen Sie die insgesamt verstrichene Zeit, um den am längsten dauernden Schritt zu finden.

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

Speichern Sie den Schrittindex des lang dauernden Schritts.

Überprüfen Sie die Spalte *operation\_type* des Schritts für die Abfrage mit langer Laufzeit:

- Fahren Sie mit Schritt 4a für **SQL-Vorgänge** fort: OnOperation, RemoteOperation, ReturnOperation.
- Fahren Sie mit Schritt 4b für **Datenverschiebungsvorgänge** fort: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.




### SCHRITT 4a: Bestimmen des Ausführungsstatus eines SQL-Schritts

Verwenden Sie die Anforderungs-ID und den Schrittindex zum Abrufen von Informationen zur Verteilung von SQL Server-Abfragen als Teil des SQL-Schritts in der Abfrage. Speichern Sie die Verteilungs-ID und die SPID.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


Verwenden Sie die folgende Abfrage, um den SQL Server-Ausführungsplan für den SQL-Schritt auf einem bestimmten Knoten abzurufen.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### SCHRITT 4b: Bestimmen des Ausführungsstatus eines Datenverschiebungsschritts

Verwenden Sie die Anforderungs-ID und den Schrittindex zum Abrufen von Informationen zum Datenverschiebungsschritts, der für jede Verteilung ausgeführt wird.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Überprüfen Sie die Spalte *total\_elapsed\_time*, um festzustellen, ob eine bestimmte Verteilung für das Verschieben von Daten erheblich länger als andere dauert.
- Überprüfen Sie für die Verteilung mit langer Laufzeit die Spalte *rows\_processed*, um festzustellen, ob die Anzahl der Zeilen, die von dieser Verteilung verschoben werden, beträchtlich größer als bei den anderen ist. Dies zeigt, dass Ihre Abfrage eine Datenschiefe aufweist.





## Untersuchen der Datenschiefe

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


Das Ergebnis dieser Abfrage zeigt Ihnen die Anzahl der Tabellenzeilen, die in jeder der 60 Verteilungen Ihrer Datenbank gespeichert sind. Für eine optimale Leistung sollten die Zeilen in der verteilten Tabelle gleichmäßig auf alle Verteilungen verteilt sein. Weitere Informationen finden Sie unter [Tabellenentwurf][].



## Nächste Schritte
Weitere Tipps zur Verwaltung von SQL Data Warehouse finden Sie unter [Verwaltungstools für SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Verwaltungstools für SQL Data Warehouse]: sql-data-warehouse-overview-manage.md
[Tabellenentwurf]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=AcomDC_0309_2016-->