<properties
   pageTitle="Sicherheitsrichtlinien und Einschränkungen von Azure SQL-Datenbank | Microsoft Azure"
   description="Enthält Informationen zu den Richtlinien und Einschränkungen von Microsoft Azure SQL-Datenbank, die sich auf die Sicherheit beziehen."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/20/2015"
   ms.author="rickbyh"/>

# Sicherheitsrichtlinien und Einschränkungen von Azure SQL-Datenbank

In diesem Thema werden die Richtlinien und Einschränkungen von Microsoft Azure SQL-Datenbank beschrieben, die sich auf die Sicherheit beziehen. Beachten Sie die folgenden Punkte, wenn Sie die Sicherheit Ihrer Azure SQL-Datenbanken verwalten.

## Firewall

Der Azure SQL-Datenbank-Dienst ist nur über TCP-Port 1433 verfügbar. Wenn Sie von Ihrem Computer auf eine SQL-Datenbank zugreifen möchten, sollten Sie sicherstellen, dass für Ihre Firewall die ausgehende TCP-Kommunikation über TCP-Port 1433 zugelassen ist. Im Rahmen des Verbindungsprozesses werden Verbindungen von virtuellen Azure-Computern an eine andere IP-Adresse und einen anderen Port umgeleitet, wobei diese Angaben für jede Workerrolle jeweils eindeutig sind. Die Portnummer liegt im Bereich von 11.000 bis 11.999.

Bevor Sie zum ersten Mal eine Verbindung mit dem Azure SQL-Datenbank-Server herstellen können, müssen Sie das [Azure-Portal](https://portal.azure.com) oder das [Azure Platform-Verwaltungsportal](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) verwenden, um die Firewall von Azure SQL-Datenbank zu konfigurieren. Sie müssen eine Firewalleinstellung auf Serverebene erstellen, die Verbindungsversuche von Ihrem Computer oder Azure zum Azure SQL-Datenbank-Server ermöglicht. Falls Sie den Zugriff auf bestimmte Datenbanken Ihres Azure SQL-Datenbank-Servers steuern möchten, müssen Sie für die jeweiligen Datenbanken außerdem jeweils Firewallregeln auf Datenbankebene erstellen. Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](sql-database-firewall-configure.md).

## Verbindungsverschlüsselung und Zertifikatüberprüfung

Die gesamte Kommunikation zwischen SQL-Datenbank und Ihrer Anwendung erfordert stets eine Verschlüsselung (SSL). Wenn Ihre Clientanwendung bei der Verbindungsherstellung keine Überprüfung der Zertifikate durchführt, ist die Verbindung mit SQL-Datenbank anfällig für Man-in-the-Middle-Angriffe.

Um Zertifikate mit Anwendungscode oder Tools zu überprüfen, sollten Sie explizit eine verschlüsselte Verbindung anfordern und den Serverzertifikaten nicht vertrauen. Wenn Ihr Anwendungscode oder die Tools keine verschlüsselte Verbindung anfordern, werden trotzdem verschlüsselte Verbindungen verwendet. Es kann aber sein, dass hierbei die Serverzertifikate nicht überprüft werden, sodass die Gefahr von Man-in-the-Middle-Angriffen besteht.

Legen Sie ``Encrypt=True`` und ``TrustServerCertificate=False`` in der Datenbank-Verbindungszeichenfolge fest, um Zertifikate mit ADO.NET-Anwendungscode zu überprüfen. Weitere Informationen finden Sie unter [Codebeispiel: Wiederholungslogik für das Herstellen einer Verbindung mit einer Azure SQL-Datenbank mithilfe von ADO.NET](https://msdn.microsoft.com/library/azure/ee336243.aspx).

SQL Server Management Studio unterstützt ebenfalls die Überprüfung von Zertifikaten. Klicken Sie im Dialogfeld **Mit Server verbinden** auf der Registerkarte **Verbindungseigenschaften** auf **Verbindung verschlüsseln**

> [AZURE.NOTE]SQL Server Management Studio unterstützt in den Versionen vor SQL Server 2008 R2 keine Verbindungen mit SQL-Datenbank.

SQL-Datenbank wird von SQLCMD zwar ab Version SQL Server 2008 unterstützt, aber in Versionen vor SQL Server 2008 R2 ist keine Unterstützung der Zertifikatüberprüfung vorhanden. Verwenden Sie die Befehlszeilenoption ``-N`` und nicht die Option ``-C``, um Zertifikate ab Version SQL Server 2008 R2 mit SQLCMD zu überprüfen. Mit der Option „-N“ fordert SQLCMD eine verschlüsselte Verbindung an. Indem nicht die Option ``-C`` verwendet wird, vertraut SQLCMD dem Serverzertifikat nicht implizit und ist gezwungen, das Zertifikat zu überprüfen.

Zusätzliche technische Informationen finden Sie im Artikel [Azure SQL-Datenbank – Verbindungssicherheit](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) auf der TechNet-Wiki-Website.

## Authentifizierung

Die Active Directory-Authentifizierung (integrierte Sicherheit) ist als Vorschau in SQL-Datenbank V12 verfügbar. Informationen zum Konfigurieren der AD-Authentifizierung finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md). Wenn die Vorschau nicht verwendet wird, müssen die Benutzer bei jeder Verbindung mit der SQL-Datenbank ihre Anmeldeinformationen (Benutzername und Kennwort) angeben. Weitere Informationen zur SQL Server-Authentifizierung finden Sie unter [Auswählen eines Authentifizierungsmodus](https://msdn.microsoft.com/library/ms144284.aspx) in der SQL Server-Onlinedokumentation.

Mit [SQL-Datenbank V12](sql-database-v12-whats-new.md) können sich Benutzer gegenüber der Datenbank authentifizieren, indem sie Benutzer für eigenständige Datenbanken verwenden. Weitere Informationen finden Sie unter [Eigenständige Datenbankbenutzer - machen Sie Ihre Datenbank portabel](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) und [Eigenständige Datenbanken](https://technet.microsoft.com/library/ff929071.aspx).

> [AZURE.NOTE]Microsoft empfiehlt die Verwendung von Benutzern für eigenständige Datenbanken, um die Skalierbarkeit zu verbessern.

Das Datenbankmodul schließt Verbindungen, die sich länger als 30 Minuten im Leerlauf befinden. Für die Verbindung muss dann eine erneute Anmeldung durchgeführt werden, bevor sie verwendet werden kann.

Ständig aktive Verbindungen mit SQL-Datenbank erfordern mindestens alle 10 Stunden eine erneute Autorisierung (per Datenbankmodul). Das Datenbankmodul versucht, die Autorisierung erneut durchzuführen, indem das ursprünglich übermittelte Kennwort verwendet wird. Im Erfolgsfall ist dann keine Benutzereingabe erforderlich. Wenn ein Kennwort in SQL-Datenbank zurückgesetzt wird, wird die Verbindung aus Leistungsgründen nicht erneut authentifiziert. Dies gilt auch, wenn die Verbindung aufgrund des Verbindungspoolings zurückgesetzt wird. Dies unterscheidet sich vom Verhalten einer lokalen SQL Server-Instanz. Wenn das Kennwort seit der ersten Autorisierung der Verbindung geändert wurde, muss die Verbindung beendet und mit dem neuen Kennwort eine neue Verbindung hergestellt werden. Ein Benutzer mit der Berechtigung KILL DATABASE CONNECTION kann eine Verbindung mit SQL-Datenbank explizit beenden, indem er den Befehl [KILL](https://msdn.microsoft.com/library/ms173730.aspx) verwendet.

## Anmeldungen und Benutzer

Für die Verwaltung von Anmeldungen und Benutzern in SQL-Datenbank gelten Einschränkungen.

Für die Serverebenenprinzipal-Anmeldung gelten die folgenden Einschränkungen:

- Der Datenbankbenutzer in der Masterdatenbank, der zur Serverebenenprinzipal-Anmeldung gehört, kann nicht geändert oder verworfen werden. 
- Auch wenn die Serverebenenprinzipal-Anmeldung kein Mitglied der beiden Datenbankrollen **dbmanager** und **loginmanager** in der Datenbank **master** ist, verfügt sie über alle Berechtigungen, die diesen beiden Rollen gewährt werden.

> [AZURE.NOTE]Diese Anmeldung wird während der Serverbereitstellung erstellt und ähnelt der Anmeldung **sa** in einer Instanz von SQL Server.

Für alle Anmeldungen gelten die folgenden Einschränkungen:

- US-Englisch ist die Standardsprache.
- Für das Zugreifen auf die Datenbank **master** muss jede Anmeldung einem Benutzerkonto in der Datenbank **master** zugeordnet sein. Die Datenbank **master** unterstützt keine Benutzer für eigenständige Datenbanken.
- Wenn Sie in der Verbindungszeichenfolge keine Datenbank angeben, wird standardmäßig eine Verbindung mit der Datenbank **master** hergestellt.
- Sie müssen über eine Verbindung mit der Datenbank **master** verfügen, wenn Sie die Anweisungen ``CREATE/ALTER/DROP LOGIN`` und ``CREATE/ALTER/DROP DATABASE`` ausführen. 
- Beim Ausführen der Anweisungen ``CREATE/ALTER/DROP LOGIN`` und ``CREATE/ALTER/DROP DATABASE`` in einer ADO.NET-Anwendung ist die Verwendung von parametrisierten Befehlen nicht zulässig. Weitere Informationen finden Sie unter [Befehle und Parameter](https://msdn.microsoft.com/library/ms254953.aspx).
- Beim Ausführen der Anweisungen ``CREATE/ALTER/DROP DATABASE`` und ``CREATE/ALTER/DROP LOGIN`` muss es sich jeweils um die einzige Anweisung in einem Transact-SQL-Batch handeln. Andernfalls tritt ein Fehler auf. Beispielsweise wird mit dem folgenden Transact-SQL-Code überprüft, ob die Datenbank vorhanden ist. Wenn ja, wird die Anweisung ``DROP DATABASE`` aufgerufen, um die Datenbank zu entfernen. Da die Anweisung ``DROP DATABASE`` nicht die einzige Anweisung im Batch ist, führt das Ausführen der folgenden Transact-SQL-Anweisung zu einem Fehler.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Beim Ausführen der Anweisung ``CREATE USER`` mit der Option ``FOR/FROM LOGIN`` muss sie die einzige Anweisung in einem Transact-SQL-Batch sein.
- Beim Ausführen der Anweisung ``ALTER USER`` mit der Option ``WITH LOGIN`` muss sie die einzige Anweisung in einem Transact-SQL-Batch sein.
- Nur die Serverebenenprinzipal-Anmeldung und die Mitglieder der Datenbankrolle **dbmanager** in der Datenbank **master** verfügen über die Berechtigung zum Ausführen der Anweisungen ``CREATE DATABASE`` und ``DROP DATABASE``.
- Nur die Serverebenenprinzipal-Anmeldung und die Mitglieder der Datenbankrolle **loginmanager** in der Datenbank **master** verfügen über die Berechtigung zum Ausführen der Anweisungen ``CREATE LOGIN``, ``ALTER LOGIN`` und ``DROP LOGIN``.
- Für ``CREATE/ALTER/DROP`` benötigt ein Benutzer die Berechtigung ``ALTER ANY USER`` für die Datenbank.
- Wenn der Besitzer einer Datenbankrolle versucht, einen anderen Datenbankbenutzer dieser Datenbankrolle hinzuzufügen oder ihn daraus zu entfernen, tritt ggf. ein Fehler der folgenden Art auf: **Benutzer oder Rolle "Name" ist in dieser Datenbank nicht vorhanden.** Dieser Fehler tritt auf, da der Benutzer für den Besitzer nicht sichtbar ist. Gewähren Sie dem Rollenbesitzer die Berechtigung ``VIEW DEFINITION`` für den Benutzer, um dieses Problem zu beheben. 

Weitere Informationen zu Anmeldungen und Benutzern finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](sql-database-manage-logins.md).

## Bewährte Methoden für die Sicherheit

Berücksichtigen Sie die folgenden Punkte, um Ihre Azure SQL-Datenbank-Anwendungen weniger anfällig für Sicherheitsbedrohungen zu machen:

- Verwenden der neuesten Updates: Verwenden Sie beim Herstellen einer Verbindung mit Ihrer SQL-Datenbank immer die aktuellste Version der Tools und Bibliotheken, um Sicherheitsrisiken zu vermeiden. Weitere Informationen zu den unterstützten Tools und Bibliotheken finden Sie unter [Allgemeine Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](https://msdn.microsoft.com/library/azure/ee336245.aspx).
- Blockieren eingehender Verbindungen für TCP-Port 1433: Damit Anwendungen mit SQL-Datenbank kommunizieren können, sind an TCP-Port 1433 nur ausgehende Verbindungen erforderlich. Falls die eingehende Kommunikation nicht von anderen Anwendungen auf dem Computer benötigt wird, sollten Sie sicherstellen, dass Ihre Firewall eingehende Verbindungen über TCP-Port 1433 weiterhin blockiert.
- Vermeiden von Einschleusungsrisiken: Nutzen Sie nach Möglichkeit parametrisierte Abfragen, um sicherzustellen, dass für Ihre Anwendungen keine Gefahr der Einschleusung von SQL-Befehlen besteht. Prüfen Sie Ihren Code außerdem gründlich, und führen Sie einen Penetrationstest durch, bevor Sie Ihre Anwendung bereitstellen.


## Weitere Informationen

[Firewall für die Azure SQL-Datenbank](sql-database-firewall-configure.md)

[Konfigurieren der Firewalleinstellungen (Azure SQL-Datenbank)](sql-database-configure-firewall-settings.md)

[Allgemeine Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](https://msdn.microsoft.com/library/azure/ee336245.aspx)

[Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](sql-database-manage-logins.md)

<!---HONumber=Oct15_HO3-->