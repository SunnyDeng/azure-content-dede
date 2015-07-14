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
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Sichern einer Datenbank in SQL Data Warehouse

Dieser Artikel beschreibt die Grundlagen zum Sichern der Azure SQL Data Warehouse-Datenbank. Insbesondere erhalten Sie in diesem Artikel erste Informationen über Ressourcen zum Einschränken des Zugriffs, zum Schützen von Daten und zum Überwachen von Aktivitäten in einer Datenbank.

## Verbindungssicherheit

Verbindungssicherheit bezieht sich darauf, auf welche Weise Sie die Verbindungen zu Ihrer Datenbank mithilfe von Firewall-Regeln und Verbindungsverschlüsselung einschränken und sichern.

Firewall-Regeln werden vom Server und der Datenbank verwendet, um Verbindungsversuche von IP-Adressen abzuwehren, die nicht explizit der weißen Liste hinzugefügt wurden. Damit Ihre Anwendung oder die öffentliche IP-Adresse Ihres Clientcomputers eine Verbindung mit einer neuen Datenbank herstellen kann, müssen Sie zuerst über das Azure-Verwaltungsportal, über REST-API oder PowerShell eine Firewall-Regel auf Serverebene erstellen. Eine bewährte Methode besteht darin, die von der Server-Firewall zugelassenen IP-Adressbereiche so weit wie möglich einzuschränken. Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank][].


## Authentifizierung

Authentifizierung bezieht sich darauf, auf welche Weise Sie Ihre Identität beim Herstellen der Verbindung mit der Datenbank nachweisen. SQL Data Warehouse unterstützt derzeit die SQL-Authentifizierung mit Benutzername und Kennwort.

Bei der Erstellung des logischen Servers für die Datenbank haben Sie eine "Server Admin"-Anmeldung mit Benutzername und Kennwort angegeben. Mit diesen Anmeldeinformationen können Sie sich bei jeder Datenbank auf diesem Server als Datenbankbesitzer bzw. "dbo" (database owner) authentifizieren.

Entsprechend einer bewährten Methode sollten die Benutzer Ihrer Organisation jedoch ein anderes Konto zur Authentifizierung verwenden. Auf diese Weise können Sie die Berechtigungen für die Anwendung beschränken und die Risiken schädlicher Aktivitäten reduzieren, falls Ihr Anwendungscode für einen SQL-Injection-Angriff anfällig ist. Der empfohlene Ansatz besteht darin, einen eigenständigen Datenbankbenutzer zu erstellen. Dadurch kann sich Ihre App direkt bei einer Datenbank mit Benutzername und Kennwort authentifizieren. Sie können einen eigenständigen Datenbankbenutzer erstellen, indem Sie das folgende T-SQL ausführen, während Sie mit Ihrer Benutzerdatenbank über Ihre Server Admin-Anmeldung verbunden sind:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

Weitere Informationen zur Authentifizierung bei einer SQL-Datenbank finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][].


## Autorisierung

Autorisierung bezieht sich darauf, welche Aufgaben Sie in einer Azure SQL Data Warehouse-Datenbank ausführen können. Dies wird durch die Rollenmitgliedschaften und Berechtigungen Ihres Benutzerkontos gesteuert. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen. Azure SQL Data Warehouse vereinfacht die Verwaltung durch Rollen in T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Das Server-Admin-Konto, mit dem Sie eine Verbindung herstellen, ist Mitglied von db_owner und verfügt daher über alle Berechtigungen in der Datenbank. Speichern Sie dieses Konto für die Bereitstellung von Schemaänderungen und andere Verwaltungsvorgänge. Verwenden Sie das Konto "ApplicationUser" mit eingeschränkteren Berechtigungen, um eine Verbindung von Ihrer Anwendung zur Datenbank mit den geringsten Berechtigungen herzustellen, die von Ihrer Anwendung benötigt werden.

Es gibt Möglichkeiten, weiter einzuschränken, was für einen Benutzer mit einer Azure SQL-Datenbank möglich ist: – [Datenbankrollen][] außer db_datareader und db_datawriter können verwendet werden, um leistungsfähigere Anwendungsbenutzerkonten oder weniger leistungsfähige Verwaltungskonten zu erstellen. – Mit präzisen [Berechtigungen][] können Sie steuern, welche Vorgänge Sie für einzelne Spalten, Tabellen, Sichten, Prozeduren und andere Objekte in der Datenbank durchführen können. – [Gespeicherte Prozeduren][] können verwendet werden, um die Aktionen einzuschränken, die in der Datenbank ausgeführt werden können.

Die Verwaltung von Datenbanken und logischen Servern über das Azure-Verwaltungsportal oder mit der Azure-Ressourcen-Manager-API wird durch die Rollenzuweisungen Ihres Portal-Benutzerkontos gesteuert. Weitere Informationen zu diesem Thema finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal][].


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
[Gespeicherte Prozeduren]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?linkid=526242&clcid=0x409

<!--Other Web references-->
[Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=July15_HO1-->