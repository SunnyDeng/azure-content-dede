<properties
	pageTitle="Überwachen und Behandeln von Problemen der Datenmigration (Stretch Database) | Microsoft Azure"
	description="Erfahren Sie Einzelheiten über das Überwachen des Status der Datenmigration."
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

# Überwachen und Behandeln von Problemen der Datenmigration (Stretch-Datenbank)

Wählen Sie **Aufgaben | Stretch | Überwachung** für eine Datenbank in SQL Server Management Studio aus, um die Datenmigration im Stretch Database Monitor zu überwachen.

## Prüfen des Status der Datenmigration in Stretch Database Monitor
Wählen Sie **Aufgaben | Stretch | Überwachung** für eine Datenbank in SQL Server Management Studio aus, um Stretch Database Monitor zu öffnen und die Datenmigration zu überwachen.

-   Der obere Teil der Monitors zeigt allgemeine Informationen über die Stretch-fähigen SQL Server-Datenbank und die Azure-Remotedatenbank an.

-   Im unteren Teil der Monitors wird der Status der Datenmigration für jede Stretch-fähige Tabelle in der Datenbank angezeigt.

![Stretch Database Monitor][StretchMonitorImage1]

## <a name="Migration"></a>Prüfen des Status der Datenmigration in einer dynamischen Verwaltungsansicht
Öffnen Sie die dynamische Verwaltungssicht **sys.dm\_db\_rda\_migration\_status**, um zu ermitteln, wie viele Batches und Zeilen mit Daten migriert wurden. Weitere Informationen finden Sie unter [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Beheben von Problemen bei der Datenmigration
Es gibt mehrere Probleme, die eine Migration beeinflussen können. Überprüfen Sie die folgenden Punkte.

-   Überprüfen Sie die Netzwerkkonnektivität für den SQL Server-Computer.

-   Überprüfen Sie die dynamische Verwaltungsansicht **sys.dm\_db\_rda\_migration\_status** für den Status des aktuellen Batchs. Wenn ein Fehler aufgetreten ist, überprüfen Sie die Werte error\_number, error\_state und error\_severity für den Batch.

    -   Weitere Informationen über die Ansicht finden Sie unter [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Weitere Informationen zum Inhalt von SQL Server-Fehlermeldungen finden Sie unter [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

## Siehe auch
[Verwalten von Stretch Database und Behandeln von Problemen ](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0302_2016-->