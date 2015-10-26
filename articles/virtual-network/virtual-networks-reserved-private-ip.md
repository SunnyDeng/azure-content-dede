<properties 
   pageTitle="Festlegen einer statischen internen privaten IP-Adresse"
   description="Grundlegendes zu statischen internen IP-Adressen (DIPs) und zu deren Verwaltung"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2015"
   ms.author="telmos" />

# Festlegen einer statischen internen privaten IP-Adresse
In den meisten Fällen muss für Ihren virtuellen Computer keine statische interne IP-Adresse angegeben werden. Virtuelle Computer in einem virtuellen Netzwerk erhalten automatisch eine interne IP-Adresse aus einem von Ihnen angegebenen Bereich. In bestimmten Fällen ist die Angabe einer statischen IP-Adresse für einen bestimmten virtuellen Computer allerdings sinnvoll – beispielsweise, wenn auf Ihrem virtuellen Computer DNS ausgeführt werden oder der virtuelle Computer als Domänencontroller fungieren soll.

>[AZURE.NOTE]Eine statische interne IP-Adresse bleibt erhalten, auch wenn der virtuelle Computer beendet oder seine Bereitstellung aufgehoben wird.

## Überprüfen der Verfügbarkeit einer bestimmten IP-Adresse
Wenn Sie beispielsweise überprüfen möchten, ob die IP-Adresse *10.0.0.7* in einem VNet namens *TestVnet* verfügbar ist, führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich den Wert für *IsAvailable* an:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE]Wenn Sie den obigen Befehl in einer sicheren Umgebung testen möchten, erstellen Sie unter Berücksichtigung der Richtlinien in [Erstellen eines virtuellen Netzwerks](../virtual-network/virtual-networks-create-vnet.md) ein VNet namens *TestVnet*, und stellen Sie dabei sicher, dass es den Adressraum *10.0.0.0/8* verwendet.

## Angeben einer statischen internen IP-Adresse beim Erstellen eines virtuellen Computers
Das folgende PowerShell-Skript erstellt einen neuen Clouddienst namens *TestService*, ruft ein Image aus Azure ab, erstellt auf dessen Grundlage in dem neuen Clouddienst einen virtuellen Computer namens *TestVM*, platziert den virtuellen Computer in einem Subnetz namens *Subnet-1* und legt *10.0.0.7* als statische interne IP-Adresse für den virtuellen Computer fest:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## Abrufen der Informationen zur statischen internen IP-Adresse für einen virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich die Werte für *IpAddress* an, um Informationen zur statischen internen IP-Adresse des virtuellen Computers zu erhalten, der mit dem obigen Skript erstellt wurde:

	Get-AzureVM -Name TestVM -ServiceName TestService

	DeploymentName              : TestService
	Name                        : TestVM
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 10.0.0.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : TestVM
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## Entfernen einer statischen internen IP-Adresse von einem virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, um die statische interne IP-Adresse zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## Hinzufügen einer statischen internen IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie den folgenden Befehl aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische interne IP-Adresse hinzuzufügen:

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## Nächste Schritte

[Reservierte IP](../virtual-networks-reserved-public-ip)

[Öffentliche IP auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip)

[Reservierte IP-REST-APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=Oct15_HO3-->