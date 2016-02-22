<properties
	pageTitle="Konfigurieren von Firewalleinstellungen | Microsoft Azure"
	description="Sie erfahren, wie Sie die Firewall für IP-Adressen mit Zugriff auf Azure SQL-Datenbanken konfigurieren."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# Konfigurieren der Firewalleinstellungen für SQL-Datenbank mit PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL-Datenbank verwendet Firewallregeln, um Verbindungen mit Ihren Servern und Datenbanken zu erlauben. Sie können auf Ihrem Azure SQL-Datenbank-Server auf Serverebene und Datenbankebene Firewalleinstellungen für die Masterdatenbank oder eine Benutzerdatenbank definieren, um den selektiven Zugriff auf die Datenbank zu ermöglichen.

> [AZURE.IMPORTANT] Um Anwendungen von Azure die Verbindung mit dem Datenbankserver zu ermöglichen, müssen Azure-Verbindungen aktiviert sein. Weitere Informationen zu Firewallregeln und dem Aktivieren von Verbindungen aus Azure finden Sie unter [Firewall für Azure SQL-Datenbank](sql-database-firewall-configure.md). Sie müssen möglicherweise einige zusätzliche TCP-Ports öffnen, wenn Sie Verbindungen innerhalb der Grenzen der Azure-Cloud herstellen möchten. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank V12: "Außerhalb" im Vergleich zu "Innerhalb"** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Verwalten von Firewallregeln auf Serverebene über Azure PowerShell
1. Starten Sie Azure PowerShell.
2. Firewallregeln auf Serverebene können mit Azure PowerShell erstellt, aktualisiert und gelöscht werden. 

	Um eine neue Firewallregel auf Serverebene zu erstellen, führen Sie das Cmdlet "New-AzureSqlDatabaseServerFirewallRule" aus. Im folgenden Beispiel wird ein Bereich von IP-Adressen auf dem Server Contoso aktiviert.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Um eine neue Firewallregel auf Serverebene zu ändern, führen Sie das Cmdlet "Set-AzureSqlDatabaseServerFirewallRule" aus. Im folgenden Beispiel wird der Bereich zulässiger IP-Adressen für die Regel mit dem Namen "ContosoFirewallRule" geändert.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Um eine vorhandene Firewallregel auf Serverebene zu löschen, führen Sie das Cmdlet "Remove-AzureSqlDatabaseServerFirewallRule" aus. Im folgenden Beispiel wird die Regel mit dem Namen "ContosoFirewallRule" gelöscht.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso


## Verwalten von Firewallregeln mithilfe von PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Nächste Schritte

Ein Lernprogramm zum Erstellen einer Datenbank finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md). Hilfe beim Herstellen einer Verbindung mit Azure SQL-Datenbank von Open-Source-Anwendungen oder Anwendungen von Drittanbietern finden Sie unter [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx). Informationen zum Navigieren zu Datenbanken finden Sie unter [Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0211_2016-->