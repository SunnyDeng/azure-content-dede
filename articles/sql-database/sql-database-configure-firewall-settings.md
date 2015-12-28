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
	ms.date="12/16/2015"
	ms.author="rickbyh"/>


# Konfigurieren der Firewalleinstellungen für SQL-Datenbank mit dem Azure-Portal


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


SQL-Datenbank verwendet Firewallregeln, um Verbindungen mit Ihren Servern und Datenbanken zu erlauben. Sie können auf Ihrem logischen SQL-Datenbank-Server auf Serverebene und Datenbankebene Firewalleinstellungen für die Masterdatenbank oder eine Benutzerdatenbank definieren, um den selektiven Zugriff auf die Datenbank zu ermöglichen.

> [AZURE.IMPORTANT]Um Anwendungen von Azure die Verbindung mit dem Datenbankserver zu ermöglichen, müssen Azure-Verbindungen aktiviert sein. Weitere Informationen zu Firewallregeln und dem Aktivieren von Verbindungen aus Azure finden Sie unter [Firewall für Azure SQL-Datenbank](sql-database-firewall-configure.md). Sie müssen möglicherweise einige zusätzliche TCP-Ports öffnen, wenn Sie Verbindungen innerhalb der Grenzen der Azure-Cloud herstellen möchten. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank V12: "Außerhalb" im Vergleich zu "Innerhalb"** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md)


### Verwalten von Firewallregeln auf Serverebene über das Azure-Portal


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Verwalten von Firewallregeln auf Serverebene 

1. Klicken Sie im Azure-Portal auf **SQL-Datenbanken**. Alle Datenbanken und ihre entsprechenden Server werden hier aufgelistet.
2. Klicken Sie oben auf der Seite auf **Servers**.
3. Klicken Sie auf den Pfeil neben dem Server, für den Sie die Firewallregeln verwalten möchten.
4. Klicken Sie oben auf der Seite auf **Konfigurieren**.

	*  Um den aktuellen Computer hinzuzufügen, klicken Sie auf "Zu den zulässigen IP-Adressen hinzufügen".
	*  Um zusätzliche IP-Adressen hinzuzufügen, geben Sie den Regelnamen, die Start-IP-Adresse und die End-IP-Adresse ein.
	*  Um eine vorhandene Regel zu ändern, klicken Sie auf eines der Felder in der Regel, und ändern Sie den betreffenden Wert.
	*  Um eine vorhandene Regel zu löschen, zeigen Sie mit Maus auf die Regel, bis ein Kreuz am Ende der Zeile angezeigt wird. Klicken Sie auf das Kreuz, um die Regel zu entfernen.
5. Klicken Sie unten auf der Seite auf **Speichern**, um die Änderungen zu speichern.


## Nächste Schritte

Ein Lernprogramm zum Erstellen einer Datenbank finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md). Hilfe beim Herstellen einer Verbindung mit Azure SQL-Datenbank von Open-Source-Anwendungen oder Anwendungen von Drittanbietern finden Sie unter [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx). Informationen zum Navigieren zu Datenbanken finden Sie unter [Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_1217_2015-->