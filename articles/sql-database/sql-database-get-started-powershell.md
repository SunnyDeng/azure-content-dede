<properties 
    pageTitle="Einrichten einer neuen SQL-Datenbank mit PowerShell | Microsoft Azure" 
    description="Erfahren Sie, wie Sie eine neue SQL-Datenbank mithilfe von PowerShell erstellen. Allgemeine Datenbankeinrichtungsaufgaben können mithilfe von PowerShell-Cmdlets verwaltet werden." 
    keywords="Erstellen einer neuen SQL-Datenbank, Datenbankeinrichtung"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# Erstellen einer neue SQL-Datenbank und Ausführen gängiger Datenbankeinrichtungsaufgaben mithilfe von PowerShell-Cmdlets 

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


Erfahren Sie, wie Sie eine neue SQL-Datenbank erstellen und gängige Datenbankeinrichtungsaufgaben mithilfe von PowerShell-Cmdlets ausführen.


Zum Ausführen von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

- Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.


## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Da jetzt das Azure-Ressourcen-Manager-Modul ausgeführt wird, haben Sie Zugriff auf alle erforderlichen Cmdlets zum Erstellen einer SQL-Datenbank.

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Führen Sie dazu das folgende Cmdlet aus. Daraufhin wird ein Anmeldebildschirm zum Eingeben der Anmeldeinformationen angezeigt. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

	Add-AzureRmAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zum Auswählen des Abonnements benötigen Sie Ihre Abonnement-ID. Sie können diese Informationen aus dem vorherigen Schritt kopieren. Wenn Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureRmSubscription abrufen** und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Wenn Sie Ihre Abonnementinformationen haben, führen Sie das folgende Cmdlet aus:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Nach dem erfolgreichen Ausführen von **Select-AzureRmSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück. Wenn Sie über mehrere Abonnements verfügen, können Sie **Get-AzureRmSubscription** ausführen und überprüfen, ob das gewünschte Abonnement den Wert **IsCurrent: True** aufweist.

## Datenbankeinrichtung: Erstellen einer Ressourcengruppe, eines Servers und einer Firewallregel

Nachdem Sie Zugriff auf Cmdlets für Ihr ausgewähltes Azure-Abonnement haben, können Sie im nächsten Schritt die Ressourcengruppe einrichten, die den Server enthält, auf dem die Datenbank erstellt wird. Sie können den nächsten Befehl für einen beliebigen gültigen Speicherort anpassen. Führen Sie **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** aus, um eine Liste der gültigen Speicherorte abzurufen.

Führen Sie den folgenden Befehl aus, um eine neue Ressourcengruppe zu erstellen:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Nachdem Sie die neue Ressourcengruppe erstellt haben, werden auf dem Bildschirm Informationen angezeigt, darunter die Meldung **ProvisioningState: erfolgreich**.


### Erstellen eines Servers 

SQL-Datenbanken werden innerhalb von Azure SQL-Datenbankservern erstellt. Führen Sie **New-AzureRmSqlServer** aus, um einen neuen Server zu erstellen. Ersetzen Sie "ServerName" durch den Namen des Servers. Der muss für alle Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Sie können den Befehl bearbeiten und einen beliebigen gültigen Speicherort auswählen. Sie sollten jedoch denselben Speicherort verwenden wie für die im vorhergehenden Schritt erstellte Ressourcengruppe.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Beim Ausführen dieses Befehls wird ein Fenster zur Eingabe von **Benutzername** und **Kennwort** geöffnet. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.

Die Serverdetails werden angezeigt, nachdem der Server erfolgreich erstellt wurde.

### Konfigurieren einer Serverfirewallregel für den Zugriff auf den Server

Richten Sie eine Firewallregel für den Zugriff auf den Server ein. Führen Sie den folgenden Befehl aus, der die Start- und End-IP-Adressen durch gültige Werte für Ihren Computer ersetzt.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Die Details der Firewallregel werden angezeigt, nachdem die Regel erfolgreich erstellt wurde.

Damit andere Azure-Dienste auf den Server zugreifen können, fügen Sie eine Firewallregel hinzu, und setzen Sie sowohl „StartIpAddress“ als auch „EndIpAddress“ auf „0.0.0.0“. Beachten Sie, dass dadurch Azure-Datenverkehr aus jedem Azure-Abonnement auf den Server zugreifen kann.

Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](sql-database-firewall-configure.md).


## Erstellen einer neuen SQL-Datenbank

Sie haben jetzt eine Ressourcengruppe, einen Server und eine Firewallregel konfiguriert, sodass Sie auf den Server zugreifen können.

Der folgende Befehl erstellt eine neue (leere) SQL-Datenbank auf der Standard-Dienstebene mit der Leistungsstufe S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Die Datenbankdetails werden angezeigt, nachdem die Datenbank erfolgreich erstellt wurde.

## Erstellen einer neuen SQL-Datenbank mit einem PowerShell-Skript

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
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Nächste Schritte
Nachdem Sie eine neue SQL-Datenbank erstellt und grundlegende Datenbankeinrichtungsaufgaben ausgeführt haben, sind Sie für Folgendes bereit:

- [Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit SQL Server Management Studio und Ausführen einer T-SQL-Beispielabfrage](sql-database-connect-query-ssms.md)


## Weitere Ressourcen

- [Azure SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0302_2016-->