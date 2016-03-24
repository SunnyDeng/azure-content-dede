<properties
   pageTitle="Sichern einer Datenbank in SQL Data Warehouse | Microsoft Azure"
   description="Tipps für das Sichern einer Datenbank in Azure SQL Data Warehouse für die Entwicklung von Lösungen."
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

# Sichern einer Datenbank in SQL Data Warehouse

Dieser Artikel beschreibt die Grundlagen zum Sichern der Azure SQL Data Warehouse-Datenbank. Insbesondere erhalten Sie in diesem Artikel erste Informationen über Ressourcen zum Einschränken des Zugriffs, zum Schützen von Daten und zum Überwachen von Aktivitäten in einer Datenbank.

## Verbindungssicherheit

Verbindungssicherheit bezieht sich darauf, auf welche Weise Sie die Verbindungen zu Ihrer Datenbank mithilfe von Firewall-Regeln und Verbindungsverschlüsselung einschränken und sichern.

Firewall-Regeln werden vom Server und der Datenbank verwendet, um Verbindungsversuche von IP-Adressen abzuwehren, die nicht explizit der weißen Liste hinzugefügt wurden. Damit Ihre Anwendung oder die öffentliche IP-Adresse Ihres Clientcomputers eine Verbindung mit einer neuen Datenbank herstellen kann, müssen Sie zuerst über das klassische Azure-Portal, über REST-API oder PowerShell eine Firewall-Regel auf Serverebene erstellen. Eine bewährte Methode besteht darin, die von der Server-Firewall zugelassenen IP-Adressbereiche so weit wie möglich einzuschränken. Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank][].


## Authentifizierung

Authentifizierung bezieht sich darauf, auf welche Weise Sie Ihre Identität beim Herstellen der Verbindung mit der Datenbank nachweisen. SQL Data Warehouse unterstützt derzeit die SQL-Authentifizierung mit Benutzername und Kennwort.

Bei der Erstellung des logischen Servers für die Datenbank haben Sie eine "Server Admin"-Anmeldung mit Benutzername und Kennwort angegeben. Mit diesen Anmeldeinformationen können Sie sich bei jeder Datenbank auf diesem Server als Datenbankbesitzer bzw. "dbo" (database owner) authentifizieren.

Allerdings sollten die Benutzer in Ihrer Organisation als bewährte Methode ein anderes Konto für die Authentifizierung verwenden. Auf diese Weise können Sie die Berechtigungen für die Anwendung beschränken und die Risiken schädlicher Aktivitäten reduzieren, falls Ihr Anwendungscode für einen SQL-Injection-Angriff anfällig ist. So erstellen Sie einen Datenbankbenutzer auf Grundlage der Serveranmeldung:

Stellen Sie zunächst als mit den Anmeldeinformationen für den Serveradministrator eine Verbindung mit der Masterdatenbank auf dem Server her, und erstellen Sie eine neue Serveranmeldung.

```
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

Stellen Sie dann mit den Anmeldedaten für den Serveradministrator eine Verbindung mit der SQL Data Warehouse-Datenbank her, und erstellen Sie einen Datenbankbenutzer mit der gerade erstellten Serveranmeldung.

```

-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

Weitere Informationen zur Authentifizierung bei einer SQL-Datenbank finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][].


## Autorisierung

Autorisierung bezieht sich darauf, welche Aufgaben Sie in einer Azure SQL Data Warehouse-Datenbank ausführen können. Dies wird durch die Rollenmitgliedschaften und Berechtigungen Ihres Benutzerkontos gesteuert. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen. Azure SQL Data Warehouse vereinfacht die Verwaltung durch Rollen in T-SQL:

```
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Das Server-Admin-Konto, mit dem Sie eine Verbindung herstellen, ist Mitglied von db\_owner und verfügt daher über alle Berechtigungen in der Datenbank. Speichern Sie dieses Konto für die Bereitstellung von Schemaänderungen und andere Verwaltungsvorgänge. Verwenden Sie das Konto "ApplicationUser" mit eingeschränkteren Berechtigungen, um eine Verbindung von Ihrer Anwendung zur Datenbank mit den geringsten Berechtigungen herzustellen, die von Ihrer Anwendung benötigt werden.

Es gibt Möglichkeiten, Benutzerberechtigungen für die Azure SQL-Datenbank noch weiter einzuschränken:

- [Datenbankrollen][] können, mit Ausnahme von "db\_datareader" und "db\_datawriter", dazu verwendet werden, leistungsstärkere Benutzerkonten für Anwendungen oder weniger leistungsstarke Verwaltungskonten zu erstellen.
- Mithilfe von granularen [Berechtigungen][] können Sie steuern, welche Aufgaben in einzelnen Spalten, Tabellen, Ansichten, Prozeduren und anderen Objekten in der Datenbank ausgeführt werden dürfen.
- [Gespeicherten Prozeduren][] können verwendet werden, um die Aktionen zu begrenzen, die in der Datenbank ausgeführt werden können.

Die Verwaltung von Datenbanken und logischen Servern über das klassische Azure-Portal oder mit der Azure-Ressourcen-Manager-API wird durch die Rollenzuweisungen Ihres Portal-Benutzerkontos gesteuert. Weitere Informationen zu diesem Thema finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Portal][].



## Verschlüsselung

Durch [Transparent Data Encryption][] kann die Azure SQL-Datenbank zum Schutz Ihrer Daten beitragen, indem ruhende oder in Datenbankdateien und Sicherungen gespeicherte Daten verschlüsselt werden. Um Ihre Datenbank zu verschlüsseln, stellen Sie auf Ihrem Server eine Verbindung mit der Masterdatenbank her und führen Folgendes aus:


```

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

Sie können Transparent Data Encryption auch über die Datenbankeinstellungen im [klassischen Azure-Portal][] aktivieren.



## Überwachung

Die Überwachung und Nachverfolgung von Datenbankereignissen kann Sie bei der Einhaltung von gesetzlichen Bestimmungen und der Erkennung von verdächtigen Aktivitäten unterstützen. Mit SQL Data Warehouse-Überwachung können Sie Ereignisse in der Datenbank in einem Überwachungsprotokoll in Ihrem Azure-Speicherkonto aufzeichnen. SQL Data Warehouse-Überwachung kann auch in Microsoft Power BI integriert werden, um detaillierte Berichte und Analysen zu ermöglichen. Weitere Informationen finden Sie unter [Erste Schritte mit der SQL-Datenbanküberwachung][].



## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Firewall für die Azure SQL-Datenbank]: https://msdn.microsoft.com/library/ee621782.aspx
[Datenbankrollen]: https://msdn.microsoft.com/library/ms189121.aspx
[Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: https://msdn.microsoft.com/library/ee336235.aspx
[Berechtigungen]: https://msdn.microsoft.com/library/ms191291.aspx
[Gespeicherten Prozeduren]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?LinkId=526242
[Erste Schritte mit der SQL-Datenbanküberwachung]: sql-database-auditing-get-started.md
[klassischen Azure-Portal]: https://portal.azure.com/

<!--Other Web references-->
[Rollenbasierte Zugriffssteuerung im Azure-Portal]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=AcomDC_0309_2016-->