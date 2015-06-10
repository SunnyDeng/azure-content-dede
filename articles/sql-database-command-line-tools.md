<properties 
	pageTitle="Verwalten von Azure SQL-Datenbank-Ressourcen mit PowerShell" 
	description="Verwalten von Azure SQL-Datenbank mit einer Befehlszeile" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# Verwalten von Azure SQL-Datenbank-Ressourcen mit PowerShell


In diesem Thema verwenden Sie ein PowerShell-Skript, um einen logischen Azure SQL-Datenbank-Server, eine Datenbank und eine Firewallregel zu erstellen.

## Schritt 1: Installieren von Azure SDK

Wenn dies noch nicht geschehen ist, gehen Sie anhand der Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md) vor, um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie dann eine Azure PowerShell-Eingabeaufforderung.


## Schritt 2: Konfigurieren von PowerShell-Skripts
Dieses PowerShell-Skript erstellt einen Server, eine Datenbank und eine Firewallregel für den Server.


1. Kopieren Sie den nachstehenden Block von PowerShell-Cmdlets in Ihren Text-Editor.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. Ersetzen Sie alles innerhalb von < > durch die gewünschten Werte. Eine Liste der gültigen Azure SQL-Datenbank-Serverspeicherorte erhalten Sie durch Ausführen der folgenden Cmdlets in der Azure PowerShell-Eingabeaufforderung.

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##Schritt 3: Ausführen des PowerShell-Skripts

Überprüfen Sie die Azure PowerShell-Cmdlets, die Sie zusammengestellt haben.

Kopieren Sie die konfigurierten PowerShell-Cmdlets aus Schritt 2, und fügen Sie sie in Ihre Azure PowerShell-Eingabeaufforderung ein. Dadurch werden die Cmdlets als eine Reihe von PowerShell-Befehlen ausgegeben und Ihr Server, Ihre Datenbank und Ihre Firewallregel für Azure SQL erstellt.

Wenn Sie diese oder ähnliche Azure SQL-Ressourcen erneut erstellen, haben Sie folgende Möglichkeiten:

- Speichern als PowerShell-Skriptdatei ("*.ps1")
- Speichern als Azure Automation-Runbook im Bereich "Automation" des Azure-Verwaltungsportals 

##Beispiele

Dieses PowerShell-Skript erstellt die Ressourcen für den Westen der USA.

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##Ressourcen

Weitere Informationen zu Azure SQL-PowerShell-Cmdlets erhalten Sie [hier](https://msdn.microsoft.com/library/dn546726.aspx).

<!---HONumber=58-->