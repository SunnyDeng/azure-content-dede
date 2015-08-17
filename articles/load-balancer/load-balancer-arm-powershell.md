<properties
   pageTitle="Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen mit dem Azure-Ressourcen-Manager | Microsoft Azure"
   description="So erstellen Sie Load-Balancer-Regeln, NAT-Regeln und Tests für den Azure-Ressourcen-Manager. Umfassende schrittweise Anleitung zum Erstellen einer Load-Balancer-Ressource."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/03/2015"
   ms.author="joaoma" />

# Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen mit dem Azure-Ressourcen-Manager


> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)


Die folgenden Schritte zeigen, wie Sie einen internen Lastenausgleich für Internetverbindungen mit dem Azure-Ressourcen-Manager mit PowerShell erstellen. Beim Azure-Ressourcen-Manager werden die Elemente, mit denen ein Lastenausgleich für Internetverbindungen erstellt wird, einzeln konfiguriert und dann zusammengeführt, um eine Ressource zu erstellen.

Auf dieser Seite wird die Abfolge der einzelnen Aufgaben beschrieben, die zum Erstellen eines Load Balancers durchgeführt werden müssen. Sie erfahren detailliert, welche Schritte durchgeführt werden müssen, um einen Load Balancer zu erstellen.


## Was ist erforderlich, um einen Lastenausgleich für Internetverbindungen zu erstellen?

Die folgenden Elemente müssen konfiguriert werden, bevor Sie einen Load Balancer erstellen:

- Konfiguration der IP-Adresse des Front-Ends – Dient zum Hinzufügen einer öffentlichen IP-Adresse zum Front-End-IP-Adresspool für einen Lastenausgleich des eingehenden Netzwerkverkehrs. 

- Back-End-Adresspool – Dient zum Konfigurieren der Netzwerkschnittstellen, die den vom Load Balancer verteilten Datenverkehr vom Front-End-IP-Adresspool empfangen.

- Lastenausgleichsregeln – Quell- und lokale Portkonfiguration für den Lastenausgleich.

- Tests – Dient zum Konfigurieren des Integritätsstatustests für die VM-Instanzen.

- Eingehende NAT-Regeln – Dient zum Konfigurieren der Portregeln, um direkt auf eine der VM-Instanzen zuzugreifen.

Unter [Unterstützung des Azure-Ressourcen-Managers für den Lastenausgleich](load-balancer-arm.md) erhalten Sie weitere Informationen zu Lastenausgleichskomponenten des Azure-Ressourcen-Managers.

Die folgenden Schritte zeigen, wie Sie einen Load Balancer konfigurieren können, um einen Lastenausgleich zwischen zwei virtuellen Computern durchzuführen.


## Schrittweise Anleitung mit PowerShell


### Erstellen einer Ressourcengruppe für den Load Balancer


### Schritt 1
Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die ARM-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Schritt 2

Melden Sie sich beim Azure-Konto an.


    PS C:\> Add-AzureAccount

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.


### Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Um eine Liste der verfügbaren Abonnements anzuzeigen, verwenden Sie das Cmdlet „Get-AzureSubscription“.


### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Load Balancer erstellt wird, die gleiche Ressourcengruppe verwenden.

Im oben stehenden Beispiel haben wir eine Ressourcengruppe namens "NRP RG" mit dem Standort "USA, Westen" erstellt.

## Erstellen eines virtuellen Netzwerks und einer öffentlichen IP-Adresse für den Front-End-IP-Pool


### Schritt 1

Erstellen Sie ein virtuelles Netzwerk:

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Erstellt ein Subnetz für das virtuelle Netzwerk und weist es $backendSubnet zu

	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Erstellt das virtuelle Netzwerk, und fügt dem virtuellen Netzwerk "NRPVNet" das Subnetz "lb-subnet-be" hinzu.

### Schritt 2

Erstellen Sie eine öffentliche IP-Adresse, die vom Front-End-IP-Pool verwendet werden soll:

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip 


## Erstellen eines Front-End-IP-Pools und eines Back-End-Adresspools

Richten Sie einen Front-End-IP-Pool für den eingehenden Netzwerkverkehr des Load Balancers und des Back-End-Adresspools ein, um den Lastenausgleichsverkehr zu empfangen.

### Schritt 1 

Erstellen Sie den Front-End-IP-Pool mit einer öffentlichen IP-Variablen ($publicIP).

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 


### Schritt 2 

Richten Sie einen Back-End-Adresspool für den Empfang des eingehenden Datenverkehrs vom Front-End-IP-Pool ein:

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Erstellen von LB-Regeln, NAT-Regeln, Tests und Load Balancer

Nachdem Sie den Front-End-IP-Pool und den Back-End-Adresspool erstellt haben, müssen Sie die Regeln für die Load-Balancer-Ressource erstellen:

### Schritt 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


Im obigen Beispiel werden die folgenden Elemente erstellt:

- NAT-Regel, durch die der gesamte eingehende Datenverkehr bei Port 3441 an Port 3389 gesendet wird
- eine zweite NAT-Regel, durch die der gesamte eingehende Datenverkehr bei Port 3442 an Port 3389 gesendet wird
- eine Load-Balancer-Regel, durch die ein Lastenausgleich des gesamten eingehenden Datenverkehrs beim öffentlichen Port 80 an den lokalen Port 80 im Back-End-Adresspool durchgeführt wird
- eine Testregel, die den Integritätsstatus für den Pfad "HealthProbe.aspx" überprüft



### Schritt 2

Erstellen Sie den Load Balancer, indem Sie alle Objekte (NAT-Regeln, Load-Balancer-Regeln, Testkonfigurationen) zusammenfügen:

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Erstellen von Netzwerkschnittstellen

Nach dem Erstellen des Load Balancers müssen Sie definieren, welche Netzwerkschnittstellen den eingehenden Lastenausgleichsnetzwerkverkehr, die NAT-Regeln und Tests empfangen sollen. Die Netzwerkschnittstelle wird in diesem Fall einzeln konfiguriert und kann später einem virtuellen Computer zugewiesen werden.


### Schritt 1 


Rufen Sie das virtuelle Netzwerk der Ressource und das Subnetz ab, um Netzwerkschnittstellen zu erstellen:

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


In diesem Schritt erstellen wir eine Netzwerkschnittstelle, die dem Back-End-Adresspool des Load Balancers angehört, und ordnen die erste NAT-Regel für RDP dieser Netzwerkschnittstelle zu:
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Schritt 2

Erstellen Sie eine zweite Netzwerkschnittstelle namens "LB-Nic2-BE":

In diesem Schritt erstellen wir eine zweite Netzwerkschnittstelle, weisen sie dem gleichen Back-End-Adresspool des Load Balancers zu und ordnen die zweite NAT-Regel zu, die für RDP erstellt wurde:

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


Das Ergebnis sieht in der Regel wie folgt aus:


PS C:\> $backendnic1


	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Schritt 3 

Verwenden Sie den Befehl "Add-AzureVMNetworkInterface", um die NIC einem virtuellen Computer zuzuweisen.

In der Dokumentation [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) finden Sie eine schrittweise Anleitung zum Erstellen eines virtuellen Computers und zum Zuweisen einer NIC.


## Siehe auch

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=August15_HO6-->