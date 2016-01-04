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
	ms.date="11/13/2015"
	ms.author="rickbyh"/>


# Konfigurieren der Firewalleinstellungen für SQL-Datenbank mit der REST-API


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL-Datenbank verwendet Firewallregeln, um Verbindungen mit Ihren Servern und Datenbanken zu erlauben. Sie können auf Ihrem Azure SQL-Datenbank-Server auf Serverebene und Datenbankebene Firewalleinstellungen für die Masterdatenbank oder eine Benutzerdatenbank definieren, um den selektiven Zugriff auf die Datenbank zu ermöglichen.

> [AZURE.IMPORTANT]Um Anwendungen von Azure die Verbindung mit dem Datenbankserver zu ermöglichen, müssen Azure-Verbindungen aktiviert sein. Weitere Informationen zu Firewallregeln und dem Aktivieren von Verbindungen aus Azure finden Sie unter [Firewall für Azure SQL-Datenbank](sql-database-firewall-configure.md). Sie müssen möglicherweise einige zusätzliche TCP-Ports öffnen, wenn Sie Verbindungen innerhalb der Grenzen der Azure-Cloud herstellen möchten. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank V12: "Außerhalb" im Vergleich zu "Innerhalb"** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Verwalten von Firewallregeln auf Serverebene über die REST-API
1. Beim Verwalten von Firewallregeln über die REST-API muss eine Authentifizierung erfolgen. Informationen hierzu finden Sie unter "Authentifizieren von Dienstverwaltungsanforderungen".
2. Regeln auf Serverebene können über die REST-API erstellt, aktualisiert oder gelöscht werden.

	Um eine Firewallregel auf Serverebene zu erstellen oder zu aktualisieren, führen Sie die POST-Methode mit folgendem Inhalt aus:
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Anforderungstext

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Um eine vorhandene Firewallregel auf Serverebene zu entfernen, führen Sie die DELETE-Methode mit folgendem Inhalt aus:
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule


## Verwalten von Firewallregeln mithilfe der Dienstverwaltungs-REST-API

* [Create Firewall Rule](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Delete Firewall Rule](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Get Firewall Rule](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [List Firewall Rules](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## Nächste Schritte

Ein Lernprogramm zum Erstellen einer Datenbank finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md). Hilfe beim Herstellen einer Verbindung mit Azure SQL-Datenbank von Open-Source-Anwendungen oder Anwendungen von Drittanbietern finden Sie unter [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx). Informationen zum Navigieren zu Datenbanken finden Sie unter [Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_1203_2015-->