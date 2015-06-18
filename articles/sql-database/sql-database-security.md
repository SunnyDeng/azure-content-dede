<properties 
   pageTitle="Überblick über die SQL-Datenbank-Sicherheit" 
   description="Erfahren Sie mehr über die Sicherheit von Azure SQL-Datenbank und SQL Server, einschließlich der Unterschiede zwischen einem SQL-Server in der Cloud und einem SQL-Server vor Ort hinsichtlich Authentifizierung, Autorisierung, Verbindungssicherheit, Verschlüsselung und Compliance." 
   services="sql-database" 
   documentationCenter="" 
   authors="tmullaney" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="thmullan;jackr"/>


# Sichern der SQL-Datenbank

## Übersicht

Dieser Artikel beschreibt die Grundlagen zum Sichern der Datenebene einer Anwendung mit der Azure SQL-Datenbank. Insbesondere erhalten Sie in diesem Artikel erste Informationen über Ressourcen zum Einschränken des Zugriffs, zum Schützen von Daten und zum Überwachen von Aktivitäten in einer Datenbank, die Sie im Lernprogramm [Erste Schritte mit SQL-Datenbank](sql-database-get-started.md) erstellt haben. Eine vollständige Übersicht der für alle Varianten von SQL verfügbaren Sicherheitsfunktionen finden Sie unter [Sicherheitscenter für SQL Server Database Engine und Azure SQL-Datenbank](https://msdn.microsoft.com/library/bb510589).

## Verbindungssicherheit

Verbindungssicherheit bezieht sich darauf, auf welche Weise Sie die Verbindungen zu Ihrer Datenbank mithilfe von Firewall-Regeln und Verbindungsverschlüsselung einschränken und sichern.

Firewall-Regeln werden vom Server und der Datenbank verwendet, um Verbindungsversuche von IP-Adressen abzuwehren, die nicht explizit der weißen Liste hinzugefügt wurden. Damit Ihre Anwendung oder die öffentliche IP-Adresse Ihres Clientcomputers eine Verbindung mit einer neuen Datenbank herstellen kann, müssen Sie zuerst über das Azure-Verwaltungsportal, über REST-API oder PowerShell eine Firewall-Regel auf Serverebene erstellen. Eine bewährte Methode besteht darin, die von der Server-Firewall zugelassenen IP-Adressbereiche so weit wie möglich einzuschränken. Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/ee621782).

Für alle Verbindungen zur Azure SQL-Datenbank ist eine Verschlüsselung (SSL/TLS) erforderlich, und zwar zu jedem Zeitpunkt während der Übertragung von Daten in und aus der Datenbank. Sie müssen in der Verbindungszeichenfolge Ihrer Anwendung Parameter zum Verschlüsseln der Verbindung angeben und dürfen dem Serverzertifikat *nicht* vertrauen (dies wird für Sie erledigt, wenn Sie die Verbindungszeichenfolge aus dem Azure-Verwaltungsportal kopieren). Andernfalls wird die Identität des Servers von der Verbindung nicht überprüft und diese ist anfällig für "Man-in-the-Middle"-Angriffe. Für ADO.NET-Treiber beispielsweise lauten diese Parameter für Verbindungszeichenfolgen **Encrypt=True** und **TrustServerCertificate=False**. Weitere Informationen finden Sie unter [Azure SQL-Datenbank – Verbindungsverschlüsselung und Zertifikatüberprüfung](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## Authentifizierung

Authentifizierung bezieht sich darauf, auf welche Weise Sie Ihre Identität beim Herstellen der Verbindung mit der Datenbank nachweisen. Die SQL-Datenbank unterstützt derzeit die SQL-Authentifizierung mit Benutzername und Kennwort.

Bei der Erstellung des logischen Servers für die Datenbank haben Sie eine "Server Admin"-Anmeldung mit Benutzername und Kennwort angegeben. Mit diesen Anmeldeinformationen können Sie sich bei jeder Datenbank auf diesem Server als Datenbankbesitzer bzw. "dbo" (database owner) authentifizieren.

Entsprechend einer bewährten Methode sollte Ihre Anwendung jedoch ein anderes Konto zur Authentifizierung verwenden. Auf diese Weise können Sie die Berechtigungen für die Anwendung beschränken und die Risiken schädlicher Aktivitäten reduzieren, falls Ihr Anwendungscode für einen SQL-Injection-Angriff anfällig ist. Der empfohlene Ansatz besteht darin, einen [eigenständigen Datenbankbenutzer](https://msdn.microsoft.com/library/ff929188) zu erstellen. Dadurch kann sich Ihre App direkt bei einer Datenbank mit Benutzername und Kennwort authentifizieren. Sie können einen eigenständigen Datenbankbenutzer erstellen, indem Sie das folgende T-SQL ausführen, während Sie mit Ihrer Benutzerdatenbank über Ihre Server Admin-Anmeldung verbunden sind:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

Für die Verbindung mit der Datenbank sollte die Verbindungszeichenfolge Ihrer Anwendung diesen Benutzernamen und das Kennwort anstelle der Server Admin-Anmeldung angeben.

Weitere Informationen zur Authentifizierung bei einer SQL-Datenbank finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](https://msdn.microsoft.com/library/ee336235).


## Autorisierung
Autorisierung bezieht sich darauf, welche Aufgaben Sie in einer Azure SQL-Datenbank ausführen können. Dies wird durch die Rollenmitgliedschaften und Berechtigungen Ihres Benutzerkontos gesteuert. Als bewährte Methode sollten Sie Benutzern nur die minimal erforderlichen Berechtigungen erteilen. Azure SQL-Datenbank vereinfacht die Verwaltung durch Rollen in T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Das Server-Admin-Konto, mit dem Sie eine Verbindung herstellen, ist Mitglied von db_owner und verfügt daher über alle Berechtigungen in der Datenbank. Speichern Sie dieses Konto für die Bereitstellung von Schemaänderungen und andere Verwaltungsvorgänge. Verwenden Sie das Konto "ApplicationUser" mit eingeschränkteren Berechtigungen, um eine Verbindung von Ihrer Anwendung zur Datenbank mit den geringsten Berechtigungen herzustellen, die von Ihrer Anwendung benötigt werden.

Es gibt Möglichkeiten, Benutzerberechtigungen für die Azure SQL-Datenbank noch weiter einzuschränken:

* [Datenbankrollen](https://msdn.microsoft.com/library/ms189121) können, mit Ausnahme von "db_datareader" und "db_datawriter", dazu verwendet werden, leistungsstärkere Benutzerkonten für Anwendungen oder weniger leistungsstarke Verwaltungskonten zu erstellen.
* Mithilfe von granularen [Berechtigungen](https://msdn.microsoft.com/library/ms191291) können Sie steuern, welche Aufgaben in einzelnen Spalten, Tabellen, Ansichten, Prozeduren und anderen Objekten in der Datenbank ausgeführt werden dürfen.
* Mithilfe von [Identitätswechsel](https://msdn.microsoft.com/library/vstudio/bb669087) und [Modulsignierung](https://msdn.microsoft.com/library/bb669102) können Berechtigungen ohne Sicherheitsbedenken vorübergehend erhöht werden.
* Mithilfe von [zeilenbasierter Sicherheit](https://msdn.microsoft.com/library/dn765131) können Sie steuern, welche Zeilen ein Benutzer sehen kann.
* [Daten Masking](sql-database-dynamic-data-masking-get-started.md) kann zum Schutz vor unautorisierter Offenlegung von sensiblen Daten verwendet werden.
* [Gespeicherten Prozeduren](https://msdn.microsoft.com/library/ms190782) können verwendet werden, um die Aktionen zu begrenzen, die in der Datenbank ausgeführt werden können.

Die Verwaltung von Datenbanken und logischen Servern über das Azure-Verwaltungsportal oder mit der Azure-Ressourcen-Manager-API wird durch die Rollenzuweisungen Ihres Portal-Benutzerkontos gesteuert. Weitere Informationen zu diesem Thema finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Vorschauportal](../role-based-access-control-configure.md).


## Verschlüsselung

Durch [transparente Datenverschlüsselung](http://go.microsoft.com/fwlink/?LinkId=526242) kann die Azure SQL-Datenbank helfen, Ihre Daten zu schützen, indem ruhende oder in Datenbankdateien und Sicherungen gespeicherte Daten verschlüsselt werden. Um Ihre Datenbank zu verschlüsseln, stellen Sie als Datenbankbesitzer eine Verbindung her und führen Folgendes aus:

```
CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_256 
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;
   
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Es gibt noch weitere Möglichkeiten zum Verschlüsseln von sensiblen Daten:

* Bei der [Verschlüsselung auf Zellenebene](https://msdn.microsoft.com/library/ms179331.aspx) werden bestimmte Spalten oder sogar Zellen, die Daten enthalten, mit unterschiedlichen Verschlüsselungsschlüsseln verschlüsselt.
* Wenn Sie ein Hardwaresicherheitsmodul oder eine zentrale Verwaltung Ihrer Verschlüsselungsschlüsselhierarchie benötigen, können Sie den [Azure-Schlüsseltresor mit SQL Server in einer Azure-VM](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx) verwenden.


## Überwachung

Die Überwachung und Nachverfolgung von Datenbankereignissen kann Sie bei der Einhaltung von gesetzlichen Bestimmungen und der Erkennung von verdächtigen Aktivitäten unterstützen. Mit SQL-Datenbanküberwachung können Sie Ereignisse in der Datenbank in einem Überwachungsprotokoll in Ihrem Azure-Speicherkonto aufzeichnen. SQL-Datenbanküberwachung kann auch in Microsoft Power BI integriert werden, um detaillierte Berichte und Analysen leichter zu ermöglichen. Weitere Informationen finden Sie unter [Erste Schritte mit der SQL-Datenbanküberwachung](sql-database-auditing-get-started.md).

## Compliance

Zusätzlich zu den oben aufgeführten Features und Funktionalitäten, mit denen Ihre Anwendung eine Reihe von Security Compliance-Anforderungen erfüllen kann, wird Azure SQL-Datenbank außerdem regelmäßigen Überprüfungen unterzogen und ist für eine Reihe von Compliance-Standards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/), wo die aktuellste Liste von [Compliance-Zertifizierungen für SQL-Datenbank](http://azure.microsoft.com/support/trust-center/services/) angezeigt wird.

<!---HONumber=58--> 