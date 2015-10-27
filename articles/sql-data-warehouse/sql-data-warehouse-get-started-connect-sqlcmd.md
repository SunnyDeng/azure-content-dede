<properties
   pageTitle="Erste Schritte: Verbinden mit Azure SQL Data Warehouse | Microsoft Azure"
   description="Erste Schritte zum Herstellen von Verbindungen mit SQL Data Warehouse für das Ausführen von Abfragen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="twounder"/>

# Verbinden und Abfragen mit SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

In dieser exemplarischen Vorgehensweise wird das Verbinden mit und Abfragen einer Azure SQL Data Warehouse-Datenbank veranschaulicht, was mit dem Hilfsprogramm SQLCMD.EXE nur wenige Minuten dauert. In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:

+ Installieren der erforderlichen Software
+ Verbinden mit einer Datenbank, die die Beispieldatenbank "AdventureWorksDW" enthält
+ Anwenden einer Abfrage auf die Beispieldatenbank  

## Voraussetzungen

+ [SQLCMD.EXE](https://msdn.microsoft.com/library/azure/ms162773.aspx): Informationen zum Herunterladen von SQLCMD.EXE finden unter [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501).

## Abrufen des vollqualifizierten Namens des SQL Azure-Servers

Um eine Verbindung mit Ihrer Datenbank herzustellen, benötigen Sie den vollständigen Namen des Servers (****Servername**.database.windows.net*), auf dem sich die Datenbank befindet.

1. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com).
2. Navigieren Sie zu der Datenbank, mit der Sie eine Verbindung herstellen möchten.
3. Suchen Sie den vollständigen Servernamen (der in den folgenden Schritten verwendet wird):

![][1]


## Verbinden mit SQL Data Warehouse per sqlcmd

Für die Verbindung mit einer bestimmten Instanz von SQL Data Warehouse müssen Sie bei Verwendung von SQLCMD die Eingabeaufforderung öffnen und **sqlcmd** gefolgt von der Verbindungszeichenfolge für die SQL Data Warehouse-Datenbank eingeben. Die Verbindungszeichenfolge muss die folgenden Parameter enthalten:

+ **Benutzer (-U):** Serverbenutzer im Format `<`Benutzer`>`
+ **Kennwort (-P)**: Das Kennwort des Benutzers.
+ **Server (-S)**: Server in Form von `<`Servername`>`.database.windows.net
+ **Datenbank (-D)**: Datenbankname.
+ **Bezeichner in Anführungszeichen aktivieren (-I)**: Bezeichner müssen in Anführungszeichen eingeschlossen sein, um die Verbindung mit einer SQL Data Warehouse-Instanz herzustellen.

Zur Verbindung mit einer SQL Data Warehouse-Instanz geben Sie daher Folgendes ein:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Durchführen von Beispielabfragen

Nach dem Herstellen der Verbindung können Sie alle unterstützten Transact-SQL-Anweisungen für die Instanz ausgeben.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT COUNT(*) FROM dbo.FactInternetSales;
2> GO
3> QUIT
```

Weitere Informationen zu SQLCMD finden Sie in der [SQLCMD-Dokumentation](https://msdn.microsoft.com/library/azure/ms162773.aspx).


## Nächste Schritte

Nachdem Sie eine Verbindung hergestellt haben und Abfragen senden können, versuchen Sie, [eine Verbindung mit Power BI herzustellen][].

[eine Verbindung mit Power BI herzustellen]: ./sql-data-warehouse-integrate-power-bi.md
 

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=Oct15_HO4-->