<properties 
   pageTitle="Azure SQL-Datenbanken – Allgemeine Einschränkungen und Leitlinien"
   description="Auf dieser Seite werden einige allgemeine Einschränkungen von Azure SQL-Datenbank sowie Aspekte der Interoperabilität und Unterstützung beschrieben."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/28/2015"
   ms.author="jroth" />

# Azure SQL-Datenbanken – Allgemeine Einschränkungen und Leitlinien

In diesem Thema werden allgemeine Einschränkungen und Leitlinien für Azure SQL-Datenbank behandelt. Um Kontingente, Ressourcenverwaltung und Unterstützung besser zu verstehen, lesen Sie den Abschnitt [Zusätzliche Ressourcen](#additional-guidelines) am Ende dieses Themas.

## Konnektivität

 - Windows-Authentifizierung wird nicht unterstützt. Siehe [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md). 

 - Microsoft Azure SQL-Datenbank unterstützt den TDS-Client (Tabular Data Stream) ab Version 7.3.

 - Nur TCP/IP-Verbindungen sind zulässig.

 - Der SQL Server 2008-Browser wird nicht unterstützt, da Microsoft Azure SQL-Datenbank keine dynamische Ports, sondern lediglich den Port 1433 hat.

## SQL Server-Agent/-Aufträge

Microsoft Azure SQL-Datenbank unterstützt nicht den SQL Server-Agent und keine SQL Server-Agent-Aufträge. Sie können jedoch den SQL Server-Agent in Ihrer lokalen SQL Server-Instanz ausführen und mit Microsoft Azure SQL-Datenbank verbinden.

## Transaktionen

Azure SQL-Datenbank unterstützt keine verteilte Transaktionen, d. h. Transaktionen, die sich auf mehrere Ressourcen auswirken. Weitere Informationen finden Sie unter [Verteilte Transaktionen (ADO.NET)](https://msdn.microsoft.com/library/ms254973.aspx). Azure SQL-Datenbank kann die nicht per Commit bestätigten Zeitstempelwerte der aktuellen Datenbank (DBTS) failoverübergreifend erhalten.

> [AZURE.NOTE]In bestimmten Situationen kann eine Transaktion automatisch zu einer verteilten Transaktion heraufgestuft werden. Weitere Informationen finden Sie unter [System.Transactions-Integration in SQL Server](https://msdn.microsoft.com/library/ms172070.aspx).

## Unterstützung der SQL Server-Sortierung

Die standardmäßige Datenbanksortierung von Microsoft Azure SQL-Datenbank ist **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, wobei **LATIN1\_GENERAL** für Englisch (USA), **CP1** für Codepage 1252, **CI** für keine Unterscheidung von Groß-/Kleinschreibung und **AS** für die Unterscheidung nach Akzent steht.

Bei Verwendung einer lokalen SQL Server-Instanz können Sie Sortierungen auf Server-, Datenbank-, Spalten- und Ausdrucksebene festlegen. Microsoft Azure SQL-Datenbank lässt sich nicht das Festlegen der Sortierung auf Serverebene zu. Wenn Sie die nicht standardmäßige Sortierung mit Microsoft Azure SQL-Datenbank verwenden möchten, legen Sie die Sortierung mit der CREATE DATABASE-Option COLLATE oder auf Spalten- oder Ausdrucksebene fest. Azure SQL-Datenbank unterstützt nicht die Option COLLATE mit dem ALTER DATABASE-Befehl. In Azure SQL-Datenbank haben temporäre Daten standardmäßig dieselbe Sortierung wie die Datenbank. Weitere Informationen zum Festlegen der Sortierung finden Sie unter [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Benennungsanforderungen

Aus Sicherheitsgründen sind bestimmte Benutzernamen nicht zulässig. Dabei handelt es sich um folgende Namen:

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

Namen für alle neuen Objekte müssen den SQL Server-Regeln für Bezeichner entsprechen. Weitere Informationen finden Sie unter [Bezeichner](https://msdn.microsoft.com/library/ms175874.aspx).

Darüber hinaus dürfen Anmelde- und Benutzernamen nicht das Zeichen „\\“ enthalten (Windows-Authentifizierung wird nicht unterstützt).

## Weitere Leitlinien

- Zusätzlich zu den allgemeinen Einschränkungen, die in diesem Artikel beschrieben werden, gelten für Azure SQL-Datenbank basierend auf Ihrer [Dienstebene](sql-database-service-tiers.md) bestimmte Ressourcenkontingente und Einschränkungen. Eine ausführliche Beschreibung der dienstebenenbezogenen Einschränkungen finden Sie unter [SQL-Datenbank – Dienstebenen](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

- Andere für SQL-Datenbank geltende Einschränkungen finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](sql-database-limits.md).

- Sicherheitsbezogene Leitlinien finden Sie unter [Sicherheitsrichtlinien und Einschränkungen von Azure SQL-Datenbank](sql-database-security-guidelines.md).

- Ein weiterer Aspekt ist die Kompatibilität von Azure SQL-Datenbank mit lokalen Versionen von SQL Server, wie z. B. SQL Server 2014. Die neueste Version von Azure SQL-Datenbank (V12 ) weist bei diesem Aspekt viele Verbesserungen auf. Weitere Informationen finden Sie unter [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md).

- Informationen zur Verfügbarkeit von Treibern und Unterstützung für SQL-Datenbank finden Sie unter [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](sql-database-libraries.md).

<!---HONumber=Nov15_HO1-->