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
	ms.date="10/08/2015" 
	ms.author="sstein; vinsonyu"/>

# Verwalten von Azure SQL-Datenbanken mit PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

In diesem Thema werden PowerShell-Befehle vorgestellt, mit denen viele Azure SQL-Datenbank-Aufgaben ausgeführt werden können.


> [AZURE.IMPORTANT]Ab Veröffentlichung von Azure PowerShell 1.0 Preview ist das Cmdlet "Switch-AzureMode" nicht mehr verfügbar. Außerdem wurden die Cmdlets im Modul "Azure-Ressourcen-Manager" umbenannt. In den Beispielen in diesem Artikel werden die neuen Benennungskonventionen von PowerShell 1.0 Preview verwendet. Ausführliche Informationen finden Sie unter ["Switch-AzureMode" in Azure PowerShell veraltet](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Zur Ausführung von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Aufgrund des Entfernens von "Switch-AzureMode" müssen Sie die neueste Azure PowerShell herunterladen und durch Ausführen des [Microsoft-Webplattform-Installers](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) installieren. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).



## Konfigurieren Ihrer Anmeldeinformationen

Um PowerShell-Cmdlets für Ihr Azure-Abonnement ausführen zu können, müssen Sie zunächst den Zugriff auf Ihr Azure-Konto herstellen. Führen Sie Folgendes aus. Es wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung sollten einige Informationen auf dem Bildschirm angezeigt werden, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


## Auswählen des Azure-Abonnements

Zur Auswahl des Abonnements, mit dem Sie arbeiten möchten, benötigen Sie Ihre Abonnement-ID (**-SubscriptionId**) oder den Abonnementnamen (**-SubscriptionName**). Sie können diese Informationen aus dem vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren.

Führen Sie das folgende Cmdlet mit den Informationen zu Ihrem Abonnement aus, um Ihr aktuelles Abonnement festlegen:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Die folgenden Befehle werden für das soeben ausgewählte Abonnement ausgeführt.

## Erstellen einer Ressourcengruppe

Erstellen Sie die Ressourcengruppe, die den Server enthalten soll. Sie können den nächsten Befehl für einen beliebigen gültigen Standort anpassen.

Eine Liste der gültigen Standorte für Azure SQL-Datenbankserver erhalten Sie durch Ausführen der folgenden Cmdlets:

	$AzureSQLLocations = Get-AzureRMLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

Wenn Sie bereits über eine Ressourcengruppe verfügen, können Sie mit dem Erstellen eines Servers fortfahren, oder Sie führen nach Anpassung den folgenden Befehl zum Erstellen einer neuen Ressourcengruppe aus:

	New-AzureRMResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Erstellen eines Servers 

Um einen neuen V12-Server zu erstellen, verwenden Sie das Cmdlet [New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Ersetzen Sie "server12" durch den Namen Ihres Servers. Der muss für Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Die Serverdetails und PowerShell-Eingabeaufforderung werden angezeigt, nachdem der Server erfolgreich erstellt wurde. Sie können den Befehl für einen beliebigen gültigen Speicherort anpassen.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Beim Ausführen dieses Befehls wird ein Fenster zur Eingabe von **Benutzername** und **Kennwort** geöffnet. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.

## Erstellen einer Server-Firewallregel

Um eine Firewallregel für den Zugriff auf den Server zu erstellen, verwenden Sie den Befehl [New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Führen Sie den folgenden Befehl aus, wobei Sie die Start- und End-IP-Adressen durch gültige Werte für Ihren Client ersetzen.

Wenn Ihr Server Zugriff auf andere Azure-Dienste ermöglichen muss, fügen Sie den Schalter **-AllowAllAzureIPs** hinzu, durch den eine spezielle Firewallregel hinzugefügt und alle Zugriffe für Azure-Datenverkehr auf den Server ermöglicht werden.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Erstellen einer SQL-Datenbank

Um eine Datenbank zu erstellen, verwenden Sie den Befehl [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Sie benötigen einen Server, um eine Datenbank zu erstellen. Im folgenden Beispiel wird eine SQL-Datenbank mit dem Namen "TestDB12" erstellt. Die Datenbank wird als Standard-S1-Datenbank erstellt.

	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Ändern der Leistungsebene einer SQL-Datenbank

Sie können Ihre Datenbank mit dem Befehl [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) hoch- oder herunterskalieren. Im folgenden Beispiel wird eine SQL-Datenbank mit dem Namen "TestDB12" aus der aktuellen Leistungsebene auf eine Standard-S3-Ebene hochskaliert.

	Set-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Löschen einer SQL-Datenbank

Sie können eine SQL-Datenbank mit dem Befehl [Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) löschen. Im folgenden Beispiel wird eine SQL-Datenbank mit dem Namen "TestDB12" gelöscht.

	Remove-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Löschen eines Servers

Sie können auch einen Server mit dem Befehl [Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) löschen. Im folgenden Beispiel wird ein Server mit dem Namen "server12" gelöscht.

	Remove-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Wenn Sie diese oder ähnliche Azure SQL-Ressourcen erneut erstellen, haben Sie folgende Möglichkeiten:

- Speichern als PowerShell-Skriptdatei ("*.ps1")
- Speichern als Azure Automation-Runbook im Bereich "Automation" des Azure-Verwaltungsportals 

## Nächste Schritte

Kombinieren und Automatisieren von Befehlen. Ersetzen Sie z. B. alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >, durch Ihre Werte, um einen Server, die Firewallregel und die Datenbank zu erstellen:


    New-AzureRMResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRMSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRMSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRMSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Verwandte Informationen

- [Azure SQL-Datenbank-Cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=Oct15_HO3-->