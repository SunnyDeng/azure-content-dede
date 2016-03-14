<properties
	pageTitle="Verwalten von Stretch-Datenbank und Behandeln von Problemen | Microsoft Azure"
	description="Erfahren Sie, wie Sie Stretch-Datenbank verwalten und Probleme behandeln können."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Verwalten von Stretch-Datenbank und Behandeln von Problemen

Verwenden Sie zum Verwalten von Stretch-Datenbank und zur Behandlung von Problemen, die in diesem Thema beschriebenen Tools und Methoden.

## <a name="LocalInfo"></a>Abrufen von Informationen zu lokalen Datenbanken und Tabellen, die für Stretch-Datenbank aktiviert sind
Öffnen Sie die Katalogsichten **sys.databases** und **sys.tables**, um Informationen über Stretch-fähige SQL Server-Datenbanken und -Tabellen anzuzeigen. Weitere Informationen finden Sie unter [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) und [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

## <a name="RemoteInfo"></a>Abrufen von Informationen zu lokalen Datenbanken und Tabellen, die von Stretch-Datenbank verwendet werden
Öffnen Sie die Katalogsichten **sys.remote\_data\_archive\_databases** und **sys.remote\_data\_archive\_tables**, um Informationen über die Remotedatenbanken und -tabellen anzuzeigen, in denen migrierte Daten gespeichert werden. Weitere Informationen finden Sie unter [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) und [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Überprüfen des auf eine Tabelle angewendeten Filterprädikats
Öffnen Sie die Katalogsicht **sys.remote\_data\_archive\_tables**, und überprüfen Sie den Wert der Spalte **filter\_predicate**. Wenn der Wert null ist, ist die gesamte Tabelle für eine Migration berechtigt. Weitere Informationen finden Sie unter [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="Migration"></a>Überprüfen des Status der Datenmigration
Wählen Sie **Aufgaben | Stretch | Überwachung** für eine Datenbank in SQL Server Management Studio, um die Datenmigration im Stretch Database Monitor zu überwachen. Weitere Informationen finden Sie unter [Überwachen und Behandeln von Problemen der Datenmigration (Stretch-Datenbank)](sql-server-stretch-database-monitor.md).

Öffnen Sie die dynamische Verwaltungssicht **sys.dm\_db\_rda\_migration\_status**, um zu ermitteln, wie viele Batches und Zeilen mit Daten migriert wurden.

## Steigern der Azure-Leistung für ressourcenintensive Vorgänge wie z. B. die Indizierung
Beim Erstellen, erneuten Erstellen oder Neuorganisieren eines Indexes für eine große Tabelle, die für Stretch-Datenbank konfiguriert ist, sollten Sie die Steigerung der Leistung der entsprechenden Remotedatenbank für die Dauer des Vorgangs in Betracht ziehen.

## Schema der Remotetabelle nicht ändern
Ändern Sie nicht das Schema einer Azure-Remotetabelle, die mit einer SQL Server-Tabelle verknüpft ist, die für Stretch-Datenbank konfiguriert wurde. Ändern Sie insbesondere nicht den Namen oder den Datentyp einer Spalte. Für die Stretch-Datenbank-Funktion gelten verschiedene Annahmen hinsichtlich des Schemas einer Remotetabelle in Bezug auf das Schema der SQL Server-Tabelle. Wenn Sie das Remoteschema ändern, funktioniert Stretch-Datenbank nicht mehr für die geänderte Tabelle.

## <a name="Firewall"></a>Beheben von Problemen bei der Datenmigration
Empfehlungen zur Problembehandlung finden Sie unter [Überwachen und Behandeln von Problemen der Datenmigration (Stretch-Datenbank)](sql-server-stretch-database-monitor.md).

## Beheben von Problemen mit der Abfrageleistung
**Abfragen mit Stretch-fähigen Tabellen sind langsam.** Abfragen mit Stretch-fähigen Tabellen werden erwartungsgemäß langsamer, nachdem die Tabellen für Stretch aktiviert wurden. Wenn die Abfrageleistung maßgeblich sinkt, überprüfen Sie die folgenden möglichen Problemursachen.

-   Befindet sich der Azure-Server in einer anderen geografischen Region als Ihr SQL Server? Konfigurieren Sie Ihren Azure-Server so, dass er sich in der gleichen geografischen Region wie Ihr SQL Server befindet, um die Netzwerklatenz zu reduzieren.

-   Möglicherweise haben sich die Netzwerkbedingungen verschlechtert. Wenden Sie sich an Ihren Netzwerkadministrator, um Informationen über aktuelle Probleme oder Ausfälle zu erhalten.

## Weitere Informationen
[Überwachen von Stretch-Datenbank](sql-server-stretch-database-monitor.md) [Sichern und Wiederherstellen von Stretch-fähigen Datenbanken](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0302_2016-->