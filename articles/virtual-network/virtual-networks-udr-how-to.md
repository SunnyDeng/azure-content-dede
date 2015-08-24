<properties 
   pageTitle="Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure"
   description="Erfahren Sie, wie Sie benutzerdefinierte Routinen (UDRs) und IP-Weiterleitung verwalten."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2015"
   ms.author="telmos" />

# Erstellen von Routen und Aktivieren der IP-Weiterleitung in Azure
Mithilfe virtueller Geräte können Sie in Azure den Datenverkehr im virtuellen Azure-Netzwerk verwalten. Allerdings müssen Sie Routen erstellen, damit die virtuellen Computer und Clouddienste in Ihrem virtuellen Netzwerk Pakete an das virtuelle Gerät anstatt an das gewünschte Ziel des Pakets senden können. Zudem müssen Sie für den virtuellen Computer die IP-Weiterleitung aktivieren, damit dieser die Pakete empfangen und weiterleiten kann, die nicht eigentlich an ihn adressiert sind.

## Verwalten von Routen
Mithilfe von PowerShell können Sie in Azure Routen hinzufügen, entfernen und ändern. Bevor Sie eine Route erstellen können, müssen Sie eine Routentabelle als Host für die Route erstellen.

### Erstellen von Routentabellen
Zum Erstellen einer Routentabelle mit dem Namen *FrontEndSubnetRouteTable*, führen Sie den folgenden PowerShell-Befehl aus:

```powershell
New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location uscentral `
	-Label "Route table for frontend subnet"
```

Die Ausgabe des Befehls oben sollte ungefähr wie folgt aussehen:

	Error          :
	HttpStatusCode : OK
	Id             : 085ac8bf-26c3-9c4c-b3ae-ebe880108c70
	Status         : Succeeded
	StatusCode     : OK
	RequestId      : a8cc03ca42d39f27adeaa9c1986c14f7

### Hinzufügen von Routen zu einer Routentabelle
Führen Sie den folgenden PowerShell-Befehl aus, um eine Route hinzuzufügen, die in der Routentabelle *10.1.1.10* als nächsten Hop für das Subnetz *10.2.0.0/16* festlegt:

```powershell
Get-AzureRouteTable FrontEndSubnetRouteTable `
	|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10
```

Die Ausgabe des Befehls oben sollte ungefähr wie folgt aussehen:

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### Zuordnen von Routen zu einem Subnetz
Einer Routentabelle muss mindestens ein zu verwendendes Subnetz zugeordnet werden. Führen Sie den folgenden PowerShell-Befehl aus, um die Routentabelle *FrontEndSubnetRouteTable* dem Subnetz *FrontEndSubnet* im virtuellen Netzwerk *ProductionVnet* zuzuordnen:

```powershell
Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable
```

### Anzeigen der geltenden Routen auf einem virtuellen Computer
Sie können Azure abfragen, um die tatsächlichen Routen für einen bestimmten virtuellen Computer oder eine Rolleninstanz anzuzeigen. Die angezeigten Routen umfassen die Azure-Standardrouten sowie die von einem VPN-Gateway angegebenen Routen. Es werden bis zu 800 Routen dargestellt.

Führen Sie den folgenden PowerShell-Befehl aus, um Routen anzuzeigen, die dem primären NIC auf dem virtuellen Computer *FWAppliance1* zugeordnet sind:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureEffectiveRouteTable
```

Die Ausgabe des Befehls oben sollte ungefähr wie folgt aussehen:

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

Führen Sie den folgenden PowerShell-Befehl aus, um Routen anzuzeigen, die dem sekundären NIC *backendnic* auf dem virtuellen Computer *FWAppliance1* zugeordnet sind:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
```

Führen Sie den folgenden PowerShell-Befehl aus, um Routen anzuzeigen, die dem primären NIC auf einer Rolleninstanz *myRole* zugeordnet sind, die Teil des Clouddiensts *ProductionVMs* ist:

```powershell
Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
	-RoleInstanceName myRole
```

## Verwalten der IP-Weiterleitung
Wie bereits erwähnt müssen Sie die IP-Weiterleitung auf einem beliebigen virtuellen Computer oder einer Rolleninstanz aktivieren, die als virtuelles Gerät fungiert.

### Aktivieren der IP-Weiterleitung
Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung auf dem virtuellen Computer *FWAppliance1* zu aktivieren:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung in der Rolleninstanz *FWAppliance* im Clouddienst *DMZService* zu aktivieren:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### Deaktivieren der IP-Weiterleitung
Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung auf dem virtuellen Computer *FWAppliance1* zu deaktivieren:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Disable
```

Führen Sie den folgenden PowerShell-Befehl aus, um die IP-Weiterleitung in der Rolleninstanz *FWAppliance* im Clouddienst *DMZService* zu deaktivieren:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### Anzeigen des Status der IP-Weiterleitung
Führen Sie den folgenden PowerShell-Befehl aus, um den Status der IP-Weiterleitung auf dem virtuellen Computer *FWAppliance1* anzuzeigen:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
``` 

<!---HONumber=August15_HO7-->