<properties
	pageTitle="Behandlung von Berechtigungs- und Zugriffsproblemen mit Azure SQL-Datenbank"
	description="Kurzanleitung zum Beheben gängiger Berechtigungs-, Zugriffs-, Benutzer- und Anmeldeprobleme"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="v-shysun"/>

#Problembehandlung bei allgemeinen Berechtigungs- und Zugriffsproblemen mit Azure SQL-Datenbank
In diesem Thema finden Sie Kurzanleitungen zum Gewähren und Aufheben des Zugriffs auf eine Azure SQL-Datenbank. Ausführlichere Informationen finden Sie unter:

- [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](sql-database-manage-logins.md)
- [Schützen Ihrer SQL-Datenbank](sql-database-security)
- [Sicherheitscenter für SQL Server-Datenbankmodul und Azure SQL-Datenbank](https://msdn.microsoft.com/library/bb510589)

##So ändern Sie das Administratorkennwort für einen logischen Server
- Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL-Server**, wählen Sie in der Liste den Server aus, und klicken Sie auf **Kennwort zurücksetzen**.
##So stellen Sie sicher, dass nur autorisierte IP-Adressen auf den Server zugreifen dürfen
- Siehe [Konfigurieren der Firewalleinstellungen für Azure SQL-Datenbank](sql-database-configure-firewall-settings.md).

##So erstellen Sie eigenständige Datenbankbenutzer in der Benutzerdatenbank
- Verwenden Sie die [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx)-Anweisung, und lesen Sie [Eigenständige Datenbankbenutzer – Machen Sie Ihre Datenbank portierbar](https://msdn.microsoft.com/library/ff929188.aspx).

## So authentifizieren Sie eigenständige Datenbankbenutzer mithilfe von Azure Active Directory
- Siehe [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md).

## So erstellen Sie zusätzliche Anmeldungen für Benutzer mit hohen Berechtigungen in der virtuellen „master“-Datenbank
Verwenden Sie die [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)-Anweisung, und lesen Sie den Abschnitt „Verwalten von Anmeldungen“ in [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md), der weitere Details bietet.

<!---HONumber=AcomDC_1217_2015-->