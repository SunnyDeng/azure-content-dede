<properties
   pageTitle="Verbinden mit SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Herstellen einer Verbindung mit SQL Data Warehouse für die Entwicklung von Lösungen"
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

# Herstellen einer Verbindung mit SQL Data Warehouse 
Zum Herstellen einer Verbindung mit SQL Data Warehouse müssen Sie zu Authentifizierungszwecken Sicherheitsanmeldeinformationen übergeben. Beim Herstellen einer Verbindung werden Sie feststellen, dass bestimmte Verbindungseinstellungen im Rahmen der Einrichtung Ihrer Abfragesitzung konfiguriert werden.

In diesem Artikel werden die folgenden Aspekte der Verbindungsherstellung für SQL Data Warehouse beschrieben:

- Authentifizierung
- Verbindungseinstellungen
- Sitzungs- und Anforderungsbezeichner


## Authentifizierung
Zum Herstellen einer Verbindung mit SQL Data Warehouse müssen Sie die folgenden Informationen angeben:

- Vollqualifizierter Servername 
- SQL-Authentifizierung
- Benutzername 
- Kennwort
- Standarddatenbank (optional)

Es ist wichtig zu beachten, dass Benutzer mithilfe der SQL-Authentifizierung authentifiziert werden müssen. Die vertrauenswürdige Authentifizierung wird derzeit nicht unterstützt.

Standardmäßig wird die Verbindung mit der Masterdatenbank hergestellt, nicht mit Ihrer Benutzerdatenbank. Sie haben zwei Möglichkeiten, um eine Verbindung mit Ihrer Benutzerdatenbank herzustellen:

1. Geben Sie die Standarddatenbank an, wenn Sie Ihren Server mit dem SQL Server-Objekt-Explorer in SSDT registrieren, oder in Ihrer Verbindungszeichenfolge für die Anwendung. Fügen Sie für eine ODBC-Verbindung beispielsweise den InitialCatalog-Parameter ein.
2. Markieren Sie zuerst die Benutzerdatenbank, bevor Sie eine Sitzung in SSDT erstellen.

> [AZURE.NOTE]Eine Anleitung zum Herstellen einer Verbindung mit SQL Data Warehouse per SSDT finden Sie im Artikel mit den ersten Schritten zum [Herstellen einer Verbindung und Durchführen von Abfragen][].

Hierbei ist es wiederum wichtig zu beachten, dass die Transact-SQL-Anweisung **USE <your DB>** zum Ändern der Datenbank für eine Verbindung nicht unterstützt wird.

## Anwendungsverbindungsprotokolle
Sie können eine Verbindung mit SQL Data Warehouse herstellen, indem Sie eines der folgenden Protokolle verwenden:

- ADO.NET
- ODBC
- PHP
- JDBC

### Beispiel-Verbindungszeichenfolge für ADO.NET

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Beispiel-Verbindungszeichenfolge für ODBC

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Beispiel-Verbindungszeichenfolge für PHP

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Beispiel-Verbindungszeichenfolge für JDBC

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Verbindungseinstellungen
In SQL Data Warehouse sind einige Einstellungen beim Herstellen der Verbindung und Erstellen von Objekten standardisiert. Diese können nicht außer Kraft gesetzt werden.

| Datenbankeinstellung | Wert |
| :----------------- | :--------------------------- |
| ANSI_NULLS | EIN |
| QUOTED_IDENTIFIERS | EIN |
| NO_COUNT | AUS |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| Datenbanksortierung | SQL_Latin1_General_CP1_CI_AS |

## Sitzungen und Anforderungen
Nachdem eine Verbindung hergestellt und eine Sitzung eingerichtet wurde, können Sie Abfragen schreiben und an SQL Data Warehouse senden.

Jede Abfrage wird mit einem oder mehreren Anforderungsbezeichnern dargestellt. Alle Abfragen, die über diese Verbindung übermittelt werden, sind Teil einer einzelnen Sitzung und werden daher durch eine einzelne Sitzungs-ID dargestellt.

Da SQL Data Warehouse aber ein verteiltes MPP-System ist, werden Sitzungs- und Anforderungsbezeichner im Vergleich zu SQL Server etwas anders verfügbar gemacht.

Sitzungen und Anforderungen werden logisch durch ihre jeweiligen Bezeichner dargestellt.
	
| Bezeichner | Beispielwert |
| :--------- | :------------ |
| Sitzungs-ID | SID123456 |
| Anforderungs-ID | QID123456 |

Beachten Sie, dass die Sitzungs-ID über das Präfix SID verfügt (Abkürzung von „Session ID“) und dass die Anforderungen über das Präfix QID verfügen (Abkürzung von „Query ID“).

Sie benötigen diese Informationen beim Identifizieren der Abfrage, wenn Sie Ihre Abfrageleistung überwachen. Sie können Ihre Abfrageleistung überwachen, indem Sie das [Azure-Portal] und die dynamischen Verwaltungssichten verwenden.

Verwenden Sie die folgende Funktion, um zu ermitteln, welche Sitzung Sie gerade nutzen:

```
SELECT SESSION_ID()
;
```

Zum Anzeigen aller Abfragen, die entweder derzeit ausgeführt werden oder vor Kurzem für Ihr Data Warehouse ausgeführt wurden, können Sie eine Abfrage wie im folgenden Beispiel verwenden:

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

Beachten Sie, dass diese Abfrage in eine Ansicht eingekapselt wurde, damit Sie sie in Ihre Lösung einbauen können. Zum Anzeigen der Ergebnisse müssen Sie die Ansicht aber erstellen und ausführen.

## Nächste Schritte
Nach dem Herstellen der Verbindung können Sie mit dem Entwerfen der Tabellen beginnen. Weitere Informationen finden Sie im Artikel [Tabellenentwurf].

<!--Image references-->

<!--Azure.com references-->
[Herstellen einer Verbindung und Durchführen von Abfragen]: sql-data-warehouse-connect-query.md
[Tabellenentwurf]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=July15_HO1-->