<properties 
	pageTitle="Verwalten von Azure SQL-Datenbanken mit PowerShell" 
	description="Verwalten von Azure SQL-Datenbanken mit PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="sstein"/>

# Verwalten von Azure SQL-Datenbanken mit PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

In diesem Thema werden PowerShell-Befehle vorgestellt, mit denen viele Azure SQL-Datenbank-Aufgaben ausgeführt werden können.


Zum Ausführen von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).



## Konfigurieren Ihrer Anmeldeinformationen

Um PowerShell-Cmdlets für Ihr Azure-Abonnement ausführen zu können, müssen Sie zunächst den Zugriff auf Ihr Azure-Konto herstellen. Führen Sie Folgendes aus. Es wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim klassischen Azure-Portal.

	Login-AzureRmAccount

Nach der erfolgreichen Anmeldung sollten einige Informationen auf dem Bildschirm angezeigt werden, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


## Auswählen des Azure-Abonnements

Zur Auswahl des Abonnements, mit dem Sie arbeiten möchten, benötigen Sie Ihre Abonnement-ID (**-SubscriptionId**) oder den Abonnementnamen (**-SubscriptionName**). Sie können diese Informationen aus dem vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren.

Führen Sie das folgende Cmdlet mit den Informationen zu Ihrem Abonnement aus, um Ihr aktuelles Abonnement festlegen:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Die folgenden Befehle werden für das soeben ausgewählte Abonnement ausgeführt.

## Erstellen einer Ressourcengruppe

Erstellen Sie die Ressourcengruppe, die den Server enthalten soll. Sie können den nächsten Befehl für einen beliebigen gültigen Standort anpassen.

Eine Liste der gültigen Standorte für Azure SQL-Datenbankserver erhalten Sie durch Ausführen des folgenden Cmdlets:

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

Wenn Sie bereits über eine Ressourcengruppe verfügen, können Sie mit dem Erstellen eines Servers fortfahren, oder Sie führen nach Anpassung den folgenden Befehl zum Erstellen einer neuen Ressourcengruppe aus:

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Erstellen eines Servers 

Um einen neuen V12-Server zu erstellen, verwenden Sie das Cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Ersetzen Sie "server12" durch den Namen Ihres Servers. Der muss für Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Die Serverdetails und PowerShell-Eingabeaufforderung werden angezeigt, nachdem der Server erfolgreich erstellt wurde. Sie können den Befehl für einen beliebigen gültigen Speicherort anpassen.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Beim Ausführen dieses Befehls wird ein Fenster zur Eingabe von **Benutzername** und **Kennwort** geöffnet. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.

## Erstellen einer Server-Firewallregel

Um eine Firewallregel für den Zugriff auf den Server zu erstellen, verwenden Sie den Befehl [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Führen Sie den folgenden Befehl aus, wobei Sie die Start- und End-IP-Adressen durch gültige Werte für Ihren Client ersetzen.

Wenn Ihr Server Zugriff auf andere Azure-Dienste ermöglichen muss, fügen Sie den Schalter **-AllowAllAzureIPs** hinzu, durch den eine spezielle Firewallregel hinzugefügt und alle Zugriffe für Azure-Datenverkehr auf den Server ermöglicht werden.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Erstellen einer SQL-Datenbank

Um eine Datenbank zu erstellen, verwenden Sie den Befehl [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Sie benötigen einen Server, um eine Datenbank zu erstellen. Im folgenden Beispiel wird eine SQL-Datenbank mit dem Namen "TestDB12" erstellt. Die Datenbank wird als Standard-S1-Datenbank erstellt.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Ändern der Leistungsebene einer SQL-Datenbank

Sie können Ihre Datenbank mit dem Befehl [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) hoch- oder herunterskalieren. Im folgenden Beispiel wird eine SQL-Datenbank mit dem Namen "TestDB12" aus der aktuellen Leistungsebene auf eine Standard-S3-Ebene hochskaliert.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Löschen einer SQL-Datenbank

Sie können eine SQL-Datenbank mit dem Befehl [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) löschen. Im folgenden Beispiel wird eine SQL-Datenbank mit dem Namen "TestDB12" gelöscht.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Löschen eines Servers

Sie können auch einen Server mit dem Befehl [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) löschen. Im folgenden Beispiel wird ein Server mit dem Namen "server12" gelöscht.

	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Wenn Sie diese oder ähnliche Azure SQL-Ressourcen erneut erstellen, haben Sie folgende Möglichkeiten:

- Speichern als PowerShell-Skriptdatei ("*.ps1")
- Speichern als Azure Automation-Runbook im Bereich „Automation“ des klassischen Azure-Portals 

## Nächste Schritte

Kombinieren und Automatisieren von Befehlen. Ersetzen Sie z. B. alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >, durch Ihre Werte, um einen Server, die Firewallregel und die Datenbank zu erstellen:


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Verwandte Informationen

- [Azure SQL-Datenbank-Cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0224_2016-->