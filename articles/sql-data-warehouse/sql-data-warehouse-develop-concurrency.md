<properties
   pageTitle="Parallelitäts- und Workloadverwaltung in SQL Data Warehouse | Microsoft Azure"
   description="Grundlagen der Parallelitäts- und Workloadverwaltung in Azure SQL Data Warehouse zum Entwickeln von Lösungen"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Parallelitäts- und Workloadverwaltung in SQL Data Warehouse
SQL Data Warehouse implementiert Verfahren zum Verwalten der Workloadparallelität und der Zuweisung von Rechnerressourcen, um bei jeder Skalierung für eine vorhersagbare Leistung zu sorgen.

In diesem Artikel werden die Konzepte der Parallelitäts- und Workloadverwaltung vorgestellt. Es wird beschrieben, wie beide Features implementiert wurden und wie Sie diese in Ihrem Data Warehouse steuern können.

## Parallelität
Es ist wichtig zu verstehen, dass die Parallelität in SQL Data Warehouse auf zwei Konzepten beruht: **gleichzeitige Abfragen** und **Parallelitätsslots**.

Gleichzeitige Abfragen stehen für die Anzahl von gleichzeitig ausgeführten Abfragen. SQL Data Warehouse unterstützt bis zu 32 **gleichzeitige Abfragen**. Jede Ausführung einer Abfrage zählt unabhängig davon als einzelne Abfrage, ob es sich um eine serielle Abfrage (Singlethread) oder eine parallele Abfrage (Multithread) handelt. Dies ist eine feste Obergrenze, die für alle Servicelevel gilt.

Bei Parallelitätsslots handelt es sich um ein dynamischeres Konzept, das sich auf das Servicelevelziel für die Data Warehouse-Einheit (DWU) Ihres Data Warehouse bezieht. Wenn Sie die Anzahl von DWUs erhöhen, die SQL Data Warehouse zugeordnet sind, werden mehr Serverressourcen zugewiesen. Bei einer Erhöhung der DWUs wird aber auch die Anzahl von verfügbaren **Parallelitätsslots** erhöht.

SQL Data Warehouse muss unter Beachtung beider Schwellenwerte betrieben werden. Wenn mehr als 32 gleichzeitige Abfragen vorhanden sind oder wenn Sie die zulässige Anzahl von Parallelitätsslots überschreiten, wird die Abfrage in die Warteschlange eingereiht, bis beide Schwellenwerte eingehalten werden können.

Jede gleichzeitig ausgeführte Abfrage verbraucht einen oder mehrere Parallelitätsslots. Die genaue Anzahl der Slots hängt von zwei Faktoren ab:

1. DWU-Einstellung für SQL Data Warehouse
2. **Ressourcenklasse**, der der Benutzer angehört 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| Verbrauch von Parallelitätsslots | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| Max. Anzahl gleichzeitiger Abfragen | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| Max. Parallelitätsslots | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

Ressourcenklassen sind ein wesentlicher Bestandteil der SQL Data Warehouse-Workloadverwaltung, da hiermit auch die Serverressourcen gesteuert werden, die der Abfrage zugeordnet sind. Diese werden unten im Abschnitt zur Workloadverwaltung beschrieben.

## Workloadverwaltung

SQL Data Warehouse macht im Rahmen der Implementierung der Workloadverwaltung vier verschiedene Ressourcenklassen in Form von **Datenbankrollen** verfügbar.

Die Rollen lauten:

- smallrc
- mediumrc
- largerc
- xlargerc

Standardmäßig ist jeder Benutzer Mitglied der kleinen Ressourcenklasse (smallrc). Aber jeder Benutzer kann einer oder mehreren höheren Ressourcenklassen hinzugefügt werden. SQL Data Warehouse verwendet für die Ausführung der Abfrage die höchste Rollenmitgliedschaft. Wenn ein Benutzer einer höheren Ressourcenklasse hinzugefügt wird, wird die Anzahl der Ressourcen für den Benutzer erhöht, aber gleichzeitig werden auch größere Parallelitätsslots verbraucht. Dies kann zu einer Einschränkung der Parallelität führen. Dies liegt daran, dass der Ressourcenverbrauch anderer Benutzer begrenzt werden muss, wenn einer Abfrage mehr Ressourcen zugeordnet werden. Es werden quasi keine Almosen verteilt.

Die wichtigste Ressource, die von der höheren Ressourcenklasse gesteuert wird, ist Arbeitsspeicher. Für die meisten Data Warehouse-Tabellen mit einer nennenswerten Größe werden ColumnStore-Clusterindizes verwendet. Dies ergibt für Data Warehouse-Workloads zwar normalerweise die beste Leistung, aber die Verwaltung ist ein speicherintensiver Vorgang. Häufig ist es sehr vorteilhaft, die höheren Ressourcenklassen für Datenverwaltungsvorgänge zu nutzen, z. B. Indexneuerstellungen.

Fügen Sie Ihren Datenbankbenutzer einfach einer der oben erwähnten Rollen/Ressourcenklassen hinzu, um Ihren Speicher zu erhöhen.

Sie können sich selbst der Datenbankrolle für die Workloadverwaltung hinzufügen, indem Sie die Prozeduren `sp_addrolemember` und `sp_droprolemember` verwenden. Beachten Sie, dass Sie hierfür die Berechtigung `ALTER ROLE` benötigen. Es ist nicht möglich, die ALTER ROLE DDL-Syntax zu verwenden. Sie müssen die oben erwähnten gespeicherten Prozeduren verwenden.

> [AZURE.NOTE]Anstatt einen Benutzer einer Workloadverwaltungsgruppe hinzuzufügen und daraus zu entfernen, ist es häufig einfacher, diese intensiveren Vorgänge über eine separate Anmeldung bzw. einen separaten Benutzer zu initiieren, die bzw. der der höheren Ressourcenklasse dauerhaft zugewiesen ist.

Die folgende Tabelle enthält die Details zur Erhöhung des Speichers, der für eine Abfrage jeweils verfügbar ist. Dies hängt von der Ressourcenklasse ab, die dem ausführenden Benutzer zugeordnet ist:

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| Verfügbarer Speicher (pro Dist.) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1.600 MB |
| largerc (l) | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1.600 MB | 1.600 MB | 1.600 MB | 3.200 MB |
| xlargerc (xl) | 400 MB | 800 MB | 800 MB | 1.600 MB | 1.600 MB | 1.600 MB | 3.200 MB | 3.200 MB | 3.200 MB | 6.400 MB |

Außerdem gilt Folgendes, wie oben bereits erwähnt: Je höher die dem Benutzer zugewiesene Ressourcenklasse ist, desto höher ist der Verbrauch von Parallelitätsslots. In der folgenden Tabelle ist der Verbrauch von Parallelitätsslots von Abfragen einer Ressourcenklasse dokumentiert.

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Verbrauch von Parallelitätsslots | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| Max. Anzahl gleichzeitiger Abfragen | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| Max. Parallelitätsslots | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

Es ist wichtig zu beachten, dass die aktive Abfrageworkload sowohl den Schwellenwert für gleichzeitige Abfragen als auch den Schwellenwert für Parallelitätsslots einhalten muss. Falls einer der Schwellenwerte überschritten wird, werden Abfragen in die Warteschlange eingereiht. Abfragen in der Warteschlange werden anhand der Prioritätsreihenfolge gefolgt vom Übermittlungszeitpunkt abgearbeitet.

Hinter den Kulissen sind die Dinge ein wenig komplizierter. Die Ressourcenklassen werden dynamisch einem generischen Satz von Workload-Verwaltungsgruppen innerhalb der Ressourcenkontrolle zugeordnet. Die verwendeten Gruppen sind abhängig vom DWU-Wert für das Warehouse. Es gibt jedoch insgesamt acht Workloadgruppen, die von SQL Data Warehouse verwendet werden. Sie lauten wie folgt:

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

Diese 8 Gruppen werden über den Verbrauch von Parallelitätsslots zugeordnet.

| Workloadgruppe | Zuordnung von Parallelitätsslots | Prioritätszuordnung |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | Mittel |
| SloDWGroupC01 | 2 | Mittel |
| SloDWGroupC02 | 4 | Mittel |
| SloDWGroupC03 | 8 | Mittel |
| SloDWGroupC04 | 16 | Hoch |
| SloDWGroupC05 | 32 | Hoch |
| SloDWGroupC06 | 64 | Hoch |
| SloDWGroupC07 | 128 | Hoch |

Wenn beispielsweise DW500 die aktuelle Einstellung der DWU für Ihr SQL-Data Warehouse ist, dann werden die aktiven Workloadgruppen wie folgt den Ressourcenklassen zugeordnet:

| Ressourcenklasse | Workloadgruppe | Verwendete Parallelitätsslots | Priorität |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Mittel |
| mediumrc | SloDWGroupC02 | 4 | Mittel |
| largerc | SloDWGroupC03 | 8 | Mittel |
| xlargerc | SloDWGroupC04 | 16 | Hoch |

Verwenden Sie zum detaillierten Betrachten der Unterschiede bei der Zuweisung von Speicherressourcen aus der Perspektive der Ressourcenkontrolle die folgende Abfrage:

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
	WHERE   wg.name like 'SloDWGroup%'
	AND     rp.name = 'SloDWPool'
) 
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY 
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

> [AZURE.NOTE]Die obige Abfrage kann auch verwendet werden, um bei der Problembehandlung die aktive und historische Verwendung von Workloadgruppen zu analysieren.

## Beispiele für die Workloadverwaltung

Um einem Benutzer Zugriff auf das SQL-Data Warehouse zu gewähren, muss er sich zunächst anmelden.

Öffnen Sie eine Verbindung mit der Masterdatenbank für Ihr SQL-Data Warehouse, und führen Sie die folgenden Befehle aus:

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

[AZURE.NOTE]Es ist eine gute Idee, bei der Arbeit mit Azure SQL-Datenbank und SQL Data Warehouse gleichzeitig in der Masterdatenbank Benutzer für die Anmeldungen zu erstellen. Auf dieser Ebene sind zwei Serverrollen verfügbar, die eine Anmeldung dafür erfordern, dass ein Benutzer in der Masterdatenbank ist, um die Mitgliedschaft erteilen zu können. Die Rollen lauten `Loginmanager` und `dbmanager`. Sowohl in der Azure SQL-Datenbank als auch im SQL Data Warehouse erteilen diese Rollen Berechtigungen zum Verwalten von Anmeldungen und zum Erstellen von Datenbanken. Dies unterscheidet sich bei SQL Server. Weitere Informationen finden Sie im Artikel [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank].
 
Nachdem die Anmeldung abgeschlossen ist, muss nun ein Benutzerkonto hinzugefügt werden.

Öffnen Sie eine Verbindung mit der SQL Data Warehouse-Datenbank, und führen Sie den folgenden Befehl aus:

```
CREATE USER newperson FOR LOGIN newperson
```

Sobald dieser abgeschlossen ist, müssen dem Benutzer Berechtigungen erteilt werden. Im Beispiel unten erteilt `CONTROL` Berechtigungen in der SQL Data Warehouse-Datenbank. `CONTROL` auf Ebene der Datenbank entspricht "db_owner" in SQL Server.

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

Verwenden Sie die folgende Abfrage, um die Rollen in der Workloadverwaltung zu sehen:

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Verwenden Sie die folgende Abfrage, um einen Benutzer zu einer erhöhten Workload-Verwaltungsrolle hinzuzufügen:

``` 
EXEC sp_addrolemember 'largerc', 'newperson' 
```

Verwenden Sie die folgende Abfrage, um einen Benutzer aus einer Workload-Verwaltungsrolle zu entfernen:

``` 
EXEC sp_droprolemember 'largerc', 'newperson' 
```
> [AZURE.NOTE]Es ist nicht möglich, einen Benutzer aus „smallrc“ zu entfernen.

Verwenden Sie die folgende Abfrage, um festzustellen, welche Benutzer Mitglieder einer bestimmten Rolle sind:```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

## Erkennung von Abfragen in der Warteschlange
Zum Identifizieren von Abfragen, die sich in einer Parallelitätswarteschlange befinden, können Sie immer die DMV `sys.dm_pdw_exec_requests` verwenden.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

SQL Data Warehouse verfügt über spezielle Wartetypen zum Messen der Parallelität.

Sie lauten wie folgt:
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType bezieht sich auf Abfragen, die sich außerhalb des Parallelitätsslot-Frameworks befinden. DMV-Abfragen und Systemfunktionen wie `SELECT @@VERSION` sind Beispiele für lokale Abfragen.

UserConcurrencyResourceType bezieht sich auf Abfragen, die sich innerhalb des Parallelitätsslot-Frameworks befinden. Abfragen für Endbenutzertabellen sind ein Beispiel für die Verwendung dieses Ressourcentyps.

DmsConcurrencyResourceType bezieht sich auf Wartezeiten, die sich aufgrund von Datenverschiebungen ergeben.

BackupConcurrencyResourceType tritt auf, wenn eine Datenbank gesichert wird. Der maximale Wert für diesen Ressourcentyp ist 1. Wenn mehrere Sicherungen gleichzeitig angefordert werden, werden die restlichen in die Warteschlange eingereiht.


Verwenden Sie die DMV `sys.dm_pdw_waits`, wenn Sie mit einer Analyse der derzeit in der Warteschlange befindlichen Abfragen ermitteln möchten, auf welche Ressourcen eine Abfrage wartet.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

Sie können die DMV `sys.dm_pdw_resource_waits` verwenden, um nur die Ressourcenwartezustände anzuzeigen, die von einer bestimmten Abfrage verbraucht werden. Mit der Ressourcenwartezeit wird nur gemessen, wie lange auf die Bereitstellung von Ressourcen gewartet wird. Bei der Signalwartezeit (Signal Wait Time) wird dagegen gemessen, wie lange die zugrunde liegende SQL Server-Komponente zum Planen der Abfrage in der CPU benötigt.

```
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID()
;
```

Für Verlaufsanalysen zum Trend von Wartezeiträumen wird von SQL Data Warehouse die DMV `sys.dm_pdw_wait_stats` bereitgestellt.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: https://msdn.microsoft.com/de-de/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=July15_HO4-->