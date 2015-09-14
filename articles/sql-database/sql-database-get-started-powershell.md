<properties 
    pageTitle="Erstellen einer Azure SQL-Datenbank mithilfe von PowerShell"
	description="Erstellen einer Azure SQL-Datenbank mithilfe von PowerShell"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
    ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="data-management"
	ms.date="09/01/2015"
	ms.author="sstein"/>

# Erstellen einer SQL-Datenbank mithilfe von PowerShell

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## Übersicht

In diesem Artikel wird das Erstellen einer SQL-Datenbank mithilfe von PowerShell veranschaulicht.


Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Azure PowerShell. Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) ausführen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

Die Cmdlets zum Erstellen und Verwalten von Azure SQL-Datenbanken befinden sich im Azure-Ressourcen-Manager-Modul. Wenn Sie Azure PowerShell starten, werden die Cmdlets im Azure-Modul standardmäßig importiert. Um zum Azure-Ressourcen-Manager-Modul zu wechseln, verwenden Sie das Cmdlet "Switch-AzureMode".

	Switch-AzureMode -Name AzureResourceManager

Wenn beim Ausführen von **Switch-AzureMode** die Warnung „Das Cmdlet *Switch-AzureMode* ist veraltet und wird in zukünftigen Versionen nicht mehr enthalten sein“ angezeigt wird, liegt kein Problem vor. Fahren Sie einfach mit dem nächsten Schritt fort, um Ihre Anmeldeinformationen zu konfigurieren.

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).


## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Da jetzt das Azure-Ressourcen-Manager-Modul ausgeführt wird, haben Sie Zugriff auf alle erforderlichen Cmdlets zum Erstellen einer SQL-Datenbank.

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Führen Sie dazu das folgende Cmdlet aus. Daraufhin wird ein Anmeldebildschirm zum Eingeben der Anmeldeinformationen angezeigt. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zum Auswählen des Abonnements benötigen Sie Ihre Abonnement-ID. Sie können diese Informationen aus dem vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren. Wenn Sie Ihre Abonnementinformationen haben, führen Sie das folgende Cmdlet aus:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Nach dem erfolgreichen Ausführen von **Select-AzureSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück. Wenn Sie über mehrere Abonnements verfügen, können Sie **Get-AzureSubscription** ausführen und überprüfen, ob das gewünschte Abonnement den Wert **IsCurrent: True** aufweist.

## Erstellen von Ressourcengruppe, Server und Firewallregel

Nachdem Sie Zugriff auf Cmdlets für Ihr ausgewähltes Azure-Abonnement haben, können Sie im nächsten Schritt die Ressourcengruppe einrichten, die den Server enthält, auf dem die Datenbank erstellt wird. Sie können den nächsten Befehl für einen beliebigen gültigen Speicherort anpassen. Führen Sie **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** aus, um eine Liste der gültigen Speicherorte abzurufen.

Führen Sie den folgenden Befehl aus, um eine neue Ressourcengruppe zu erstellen:

	New-AzureResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Nachdem Sie die neue Ressourcengruppe erstellt haben, werden auf dem Bildschirm Informationen angezeigt, darunter die Meldung **ProvisioningState: erfolgreich**.


### Erstellen eines Servers 

SQL-Datenbanken werden innerhalb von Azure SQL-Datenbankservern erstellt. Führen Sie **New-AzureSqlServer** aus, um einen neuen Server zu erstellen. Ersetzen Sie "ServerName" durch den Namen des Servers. Der muss für alle Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Sie können den Befehl bearbeiten und einen beliebigen gültigen Speicherort auswählen. Sie sollten jedoch denselben Speicherort verwenden wie für die im vorhergehenden Schritt erstellte Ressourcengruppe.

	New-AzureSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Beim Ausführen dieses Befehls wird ein Fenster zur Eingabe von **Benutzername** und **Kennwort** geöffnet. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.

Die Serverdetails werden angezeigt, nachdem der Server erfolgreich erstellt wurde.

### Konfigurieren einer Serverfirewallregel für den Zugriff auf den Server

Richten Sie eine Firewallregel für den Zugriff auf den Server ein. Führen Sie den folgenden Befehl aus, der die Start- und End-IP-Adressen durch gültige Werte für Ihren Computer ersetzt.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Die Details der Firewallregel werden angezeigt, nachdem die Regel erfolgreich erstellt wurde.

Damit andere Azure-Dienste auf den Server zugreifen können, fügen Sie eine Firewallregel hinzu, und setzen Sie sowohl „StartIpAddress“ als auch „EndIpAddress“ auf „0.0.0.0“. Beachten Sie, dass dadurch Azure-Datenverkehr aus jedem Azure-Abonnement auf den Server zugreifen kann.

Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Erstellen einer SQL-Datenbank

Sie haben jetzt eine Ressourcengruppe, einen Server und eine Firewallregel konfiguriert, sodass Sie auf den Server zugreifen können.

Der folgende Befehl erstellt eine neue (leere) SQL-Datenbank auf der Standard-Dienstebene mit der Leistungsstufe S1:


	New-AzureSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Die Datenbankdetails werden angezeigt, nachdem die Datenbank erfolgreich erstellt wurde.

## Erstellen eines SQL-Datenbank-PowerShell-Skripts

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Nächste Schritte

- [Herstellen einer Verbindung mit SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)


## Zusätzliche Ressourcen

- [Azure SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=September15_HO1-->