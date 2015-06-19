<properties 
   pageTitle="Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure"
   description="Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure
Mithilfe virtueller Geräte können Sie in Azure den Datenverkehr im virtuellen Azure-Netzwerk verwalten. Allerdings müssen Sie Routen erstellen, damit die virtuellen Computer und Clouddienste in Ihrem virtuellen Netzwerk Pakete an das virtuelle Gerät anstatt an das gewünschte Ziel des Pakets senden können. Zudem müssen Sie für den virtuellen Computer die IP-Weiterleitung aktivieren, damit dieser die Pakete empfangen und weiterleiten kann, die nicht eigentlich an ihn adressiert sind.

##Verwalten von Routen
Mithilfe von PowerShell können Sie in Azure Routen hinzufügen, entfernen und ändern. Bevor Sie eine Route erstellen können, müssen Sie eine Routentabelle als Host für die Route erstellen.

### Erstellen von Routentabellen
Zum Erstellen einer Routentabelle mit dem Namen *FrontEndSubnetRouteTable*, führen Sie den folgenden PowerShell-Befehl aus:

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### Hinzufügen von Routen zu einer Routentabelle
Führen Sie den folgenden PowerShell-Befehl aus, um eine Route hinzuzufügen, die in der Routentabelle *10.1.1.10* als nächsten Hop für das Subnetz *10.2.0.0/16* festlegt:

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### Zuordnen von Routen zu einem Subnetz
Einer Routentabelle muss mindestens ein zu verwendendes Subnetz zugeordnet werden. Führen Sie den folgenden PowerShell-Befehl aus, um die Routentabelle *FrontEndSubnetRouteTable* dem Subnetz *FrontEndSubnet* im virtuellen Netzwerk *ProductionVnet* zuzuordnen:

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### Anzeigen der geltenden Routen auf einem virtuellen Computer
Sie können Azure abfragen, um die tatsächlichen Routen für einen bestimmten virtuellen Computer oder eine Rolleninstanz anzuzeigen. Die angezeigten Routen umfassen die Azure-Standardrouten sowie die von einem VPN-Gateway angegebenen Routen. Es werden bis zu 800 Routen dargestellt.

Führen Sie den folgenden PowerShell-Befehl aus, um Routen anzuzeigen, die dem primären NIC auf dem virtuellen Computer *FirewallAppliance1* zugeordnet sind:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

Führen Sie den folgenden PowerShell-Befehl aus, um Routen anzuzeigen, die dem sekundären NIC *backendnic* auf dem virtuellen Computer *FirewallAppliance1* zugeordnet sind:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

Führen Sie den folgenden PowerShell-Befehl aus, um Routen anzuzeigen, die der primären Rolleninstanz *myRole* zugeordnet sind, die Teil des Clouddiensts *myservice* sind:

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## Verwalten der IP-Weiterleitung
Wie bereits erwähnt müssen Sie die IP-Weiterleitung auf einem beliebigen virtuellen Computer oder einer Rolleninstanz aktivieren, die als virtuelles Gerät fungiert.

### Aktivieren der IP-Weiterleitung
Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung auf dem virtuellen Computer *FirewallAppliance1* zu aktivieren:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung in der Rolleninstanz *FirewallAppliance1* im Clouddienst *myService* zu aktivieren:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### Deaktivieren der IP-Weiterleitung
Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung auf dem virtuellen Computer *FirewallAppliance1* zu deaktivieren:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung in der Rolleninstanz *FirewallAppliance1* im Clouddienst *myService* zu deaktivieren:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### Anzeigen des Status der IP-Weiterleitung
Führen Sie den folgenden PowerShell-Befehl aus, um den Status der IP-Weiterleitung auf dem virtuellen Computer *FirewallAppliance1* anzuzeigen:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## Weitere Informationen

[Übersicht über benutzerdefinierte Routen und die IP-Weiterleitung](../virtual-networks-udr-overview)

[Öffentliche IP auf Instanzebene](../virtual-networks-instance-level-public-ip)

[Virtuelle Netzwerke im Überblick](https://msdn.microsoft.com/library/azure/jj156007.aspx) 
<!--HONumber=52-->
 