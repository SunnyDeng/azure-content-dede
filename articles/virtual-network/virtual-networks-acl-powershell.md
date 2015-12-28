<properties 
   pageTitle="Verwalten von Zugriffssteuerungslisten (ACLs) für Endpunkte mithilfe von PowerShell"
   description="Hier erfahren Sie, wie Sie ACLs mit PowerShell verwalten."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Verwalten von Zugriffssteuerungslisten (ACLs) für Endpunkte mithilfe von PowerShell

Sie können mit Azure PowerShell oder im Verwaltungsportal Netzwerk-Zugriffssteuerungslisten (Access Control Lists, ACLs) für Endpunkte erstellen und verwalten. In diesem Thema finden Sie Verfahren für häufige Aufgaben in Zusammenhang mit ACLs, die Sie mit PowerShell durchführen können. Die Liste der Azure PowerShell-Cmdlets, finden Sie unter [Azure-Verwaltungs-Cmdlets](http://go.microsoft.com/fwlink/?LinkId=317721). Weitere Informationen zu ACLs finden Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste (ACL)?](../virtual-networks-acl). Informationen zum Verwalten der ACLs mithilfe des Verwaltungsportals finden Sie unter [Einrichten von Endpunkten für einen virtuelle Computer](../virtual-machines-set-up-endpoints/).

## Verwalten von Netzwerk-ACLs mithilfe von Azure PowerShell

Sie können Azure PowerShell-Cmdlets zum Erstellen, Entfernen und Konfigurieren (Festlegen) von Netzwerk-Zugriffssteuerungslisten (ACLs) verwenden. Im Folgenden finden Sie einige Beispiele dafür, wie Sie eine ACL mit PowerShell konfigurieren können.

Eine vollständige Liste der PowerShell-Cmdlets für ACLs können Sie mit einem der folgenden Befehle abrufen:

	Get-Help *AzureACL*
	Get-Command -Noun AzureACLConfig

### Erstellen einer Netzwerk-ACL mit Regeln, die den Zugriff von einem Remotesubnetz zulassen

Das folgende Beispiel veranschaulicht eine Möglichkeit zum Erstellen einer neuen ACL, die Regeln enthält. Diese ACL wird dann auf den Endpunkt eines virtuellen Computers angewendet. Die ACL-Regeln im folgenden Beispiel lassen den Zugriff von einem Remotesubnetz zu. Um eine neue Netzwerk-ACL mit Zulassungsregeln für ein Remotesubnetz zu erstellen, öffnen Sie eine Azure PowerShell ISE. Kopieren Sie das unten stehende Skript, fügen Sie es ein, konfigurieren Sie es mit Ihren eigenen Werten, und führen Sie es anschließend aus.

1. Erstellen Sie das neue Netzwerk-ACL-Objekt.

		$acl1 = New-AzureAclConfig

1. Legen Sie eine Regel fest, die den Zugriff von einem Remotesubnetz zulässt. Im folgenden Beispiel legen Sie die Regel *100* fest (diese hat Priorität vor der Regel 200 und höher), um dem Remotesubnetz *10.0.0.0/8* den Zugriff auf den Endpunkt des virtuellen Computers zu ermöglichen. Ersetzen Sie die Werte durch Ihre eigenen Konfigurationswerte. Der Name „SharePoint ACL config“ sollte durch den Anzeigenamen ersetzt werden, den Sie für diese Regel verwenden möchten.

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "SharePoint ACL config"

1. Wiederholen Sie das Cmdlet zum Festlegen weiterer Regeln, und ersetzen Sie dabei die Werte entsprechend Ihren Konfigurationsanforderungen. Denken Sie daran, die Reihenfolge der Regelnummern entsprechend der gewünschten Anwendungsreihenfolge der Regeln zu ändern. Niedrigere Nummern haben Vorrang vor höheren Nummern.

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"

1. Als Nächstes können Sie entweder einen neuen Endpunkt erstellen (hinzufügen) oder die ACL für einen vorhandenen Endpunkt festlegen (einstellen). In diesem Beispiel fügen wir einen neuen Endpunkt des virtuellen Computers mit dem Namen „Web“ hinzu und aktualisieren den Endpunkt des virtuellen Computers mit den ACL-Einstellungen.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		| Update-AzureVM

1. Danach kombinieren Sie die Cmdlets und führen das Skript aus. In diesem Beispiel würden die kombinierten Cmdlets wie folgt aussehen:

		$acl1 = New-AzureAclConfig
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "Sharepoint ACL config"
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"
		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		|Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		|Update-AzureVM

### Entfernen einer Netzwerk-ACL-Regel, die den Zugriff von einem Remotesubnetz zulässt

Das folgende Beispiel veranschaulicht eine Möglichkeit zum Entfernen einer Netzwerk-ACL-Regel. Um eine Netzwerk-ACL -Regel mit Zulassungsregeln für ein Remotesubnetz zu entfernen, öffnen Sie eine Azure PowerShell ISE. Kopieren Sie das unten stehende Skript, fügen Sie es ein, konfigurieren Sie es mit Ihren eigenen Werten, und führen Sie es anschließend aus.

1. Als Erstes rufen Sie das Netzwerk-ACL-Objekt für den Endpunkt des virtuellen Computers ab. Anschließend entfernen Sie die ACL-Regel. In diesem Fall entfernen wir die Regel anhand der Regel-ID. Dadurch wird nur die Regel-ID 0 aus der ACL entfernt. Das ACL-Objekt wird nicht aus dem Endpunkt des virtuellen Computers entfernt. 

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Get-AzureAclConfig –EndpointName "web" `
		| Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Als Nächstes müssen Sie das Netzwerk-ACL-Objekt auf den Endpunkt des virtuellen Computers anwenden und den virtuellen Computer aktualisieren.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Set-AzureEndpoint –ACL $acl1 –Name "web" `
		| Update-AzureVM

### Entfernen einer Netzwerk-ACL aus einem Endpunkt des virtuellen Computers

In bestimmten Szenarien ist es erforderlich, ein Netzwerk-ACL-Objekt aus einem Endpunkt des virtuellen Computers zu entfernen. Dazu öffnen Sie eine Azure PowerShell ISE. Kopieren Sie das unten stehende Skript, fügen Sie es ein, konfigurieren Sie es mit Ihren eigenen Werten, und führen Sie es anschließend aus.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Remove-AzureAclConfig –EndpointName "web" `
		| Update-AzureVM

## Weitere Informationen

[Was ist eine Netzwerk-Zugriffssteuerungsliste (Access Control List, ACL)?](../virtual-networks-acl)

[Einrichten der Kommunikation mit einem virtuellen Computer](http://go.microsoft.com/fwlink/?LinkId=303938)

<!---HONumber=AcomDC_1217_2015-->