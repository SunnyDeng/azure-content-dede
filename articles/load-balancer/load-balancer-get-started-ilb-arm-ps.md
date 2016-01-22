<properties 
   pageTitle="Erstellen eines internen Lastenausgleichs im Ressourcen-Manager mithilfe von PowerShell | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie im Ressourcen-Manager mithilfe von PowerShell einen internen Lastenausgleich erstellen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines internen Lastenausgleichs mithilfe von PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [Klassisches Bereitstellungsmodell](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Die folgenden Schritte zeigen, wie Sie einen internen Lastenausgleich mit dem Azure-Ressourcen-Manager mit PowerShell erstellen. Beim Azure-Ressourcen-Manager werden die Elemente, mit denen ein interner Lastenausgleich erstellt wird, einzeln konfiguriert und dann zusammengeführt, um eine Ressource zu erstellen.

In diesem Artikel wird die Abfolge der einzelnen Aufgaben beschrieben, die zum Erstellen eines internen Lastenausgleichs durchgeführt werden müssen. Sie erfahren detailliert, welche Schritte durchgeführt werden müssen, um einen Lastenausgleich zu erstellen.


## Was ist erforderlich, um einen internen Lastenausgleich zu erstellen?


Die folgenden Elemente müssen konfiguriert werden, bevor Sie einen internen Lastenausgleich erstellen:

- Front-End-IP-Konfiguration – Dient zum Konfigurieren der privaten IP-Adresse für eingehenden Netzwerkdatenverkehr 

- Back-End-Adresspool – Dient zum Konfigurieren der Netzwerkschnittstellen, die den vom Load Balancer verteilten Datenverkehr vom Front-End-IP-Adresspool empfangen

- Lastenausgleichsregeln – Quell- und lokale Portkonfiguration für den Lastenausgleich.

- Tests – Dient zum Konfigurieren des Integritätsstatustests für die VM-Instanzen.

- Eingehende NAT-Regeln – Dient zum Konfigurieren der Portregeln, um direkt auf eine der VM-Instanzen zuzugreifen.

Unter [Unterstützung des Azure-Ressourcen-Managers für den Lastenausgleich](load-balancer-arm.md) erhalten Sie weitere Informationen zu Lastenausgleichskomponenten des Azure-Ressourcen-Managers.

Die folgenden Schritte zeigen, wie Sie einen Load Balancer zwischen zwei virtuellen Computer konfigurieren.


## Schrittweise Anleitung mit PowerShell


### Einrichten von PowerShell für die Verwendung des Ressourcen-Managers

Stellen Sie sicher, dass Sie die neueste Produktionsversion des Azure-Moduls für PowerShell verwenden und PowerShell ordnungsgemäß eingerichtet wurde, damit Sie auf Ihr Azure-Abonnement Zugriff haben.

### Schritt 1

		PS C:\> Login-AzureRmAccount



### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

		PS C:\> get-AzureRmSubscription 

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 3 

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Erstellen einer Ressourcengruppe für den Load Balancer

### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

    PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Load Balancer erstellt wird, die gleiche Ressourcengruppe verwenden.

Im oben stehenden Beispiel haben wir eine Ressourcengruppe namens "NRP RG" mit dem Standort "USA, Westen" erstellt.

## Erstellen eines virtuellen Netzwerks und einer privaten IP-Adresse für den Front-End-IP-Pool


### Schritt 1

Erstellt ein Subnetz für das virtuelle Netzwerk und weist es der Variablen "$backendSubnet" zu

	$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Erstellen Sie ein virtuelles Netzwerk:

	$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Erstellt das virtuelle Netzwerk, fügt dem virtuellen Netzwerk "NRPVNet" das Subnetz "lb-subnet-be" hinzu und weist es der Variablen "$vnet" zu



## Erstellen eines Front-End-IP-Pools und eines Back-End-Adresspools

Richten Sie einen Front-End-IP-Pool für den eingehenden Netzwerkverkehr des Load Balancers und des Back-End-Adresspools ein, um den Lastenausgleichsverkehr zu empfangen.

### Schritt 1 

Erstellen Sie einen Front-End-IP-Adresspool mit der privaten IP-Adresse 10.0.2.5 für das Subnetz 10.0.2.0/24, das der Endpunkt für eingehenden Netzwerkdatenverkehr ist.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### Schritt 2 

Richten Sie einen Back-End-Adresspool für den Empfang des eingehenden Datenverkehrs vom Front-End-IP-Pool ein:

	$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Erstellen von LB-Regeln, NAT-Regeln, Tests und Load Balancer

Nachdem Sie den Front-End-IP-Pool und den Back-End-Adresspool erstellt haben, müssen Sie die Regeln für die Load-Balancer-Ressource erstellen:

### Schritt 1

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


Im obigen Beispiel werden die folgenden Elemente erstellt:

- NAT-Regel, durch die der gesamte eingehende Datenverkehr bei Port 3441 an Port 3389 gesendet wird
- eine zweite NAT-Regel, durch die der gesamte eingehende Datenverkehr bei Port 3442 an Port 3389 gesendet wird
- eine Load-Balancer-Regel, durch die ein Lastenausgleich des gesamten eingehenden Datenverkehrs beim öffentlichen Port 80 an den lokalen Port 80 im Back-End-Adresspool durchgeführt wird
- eine Testregel, die den Integritätsstatus für den Pfad "HealthProbe.aspx" überprüft



### Schritt 2

Erstellen Sie den Load Balancer, indem Sie alle Objekte (NAT-Regeln, Load-Balancer-Regeln, Testkonfigurationen) zusammenfügen:

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Erstellen von Netzwerkschnittstellen

Nach dem Erstellen des internen Lastenausgleichs müssen Sie definieren, welche Netzwerkschnittstellen den eingehenden Lastenausgleichsnetzwerkverkehr, die NAT-Regeln und Tests empfangen sollen. Die Netzwerkschnittstelle wird in diesem Fall einzeln konfiguriert und kann später einem virtuellen Computer zugewiesen werden.


### Schritt 1 


Rufen Sie das virtuelle Netzwerk der Ressource und das Subnetz ab, um Netzwerkschnittstellen zu erstellen:

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


In diesem Schritt erstellen wir eine Netzwerkschnittstelle, die dem Back-End-Adresspool des Load Balancers angehört, und ordnen die erste NAT-Regel für RDP dieser Netzwerkschnittstelle zu:
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Schritt 2

Erstellen Sie eine zweite Netzwerkschnittstelle namens "LB-Nic2-BE":

In diesem Schritt erstellen wir eine zweite Netzwerkschnittstelle, weisen sie dem gleichen Back-End-Adresspool des Load Balancers zu und ordnen die zweite NAT-Regel zu, die für RDP erstellt wurde:

 	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


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

Verwenden Sie den Befehl Add-AzureRmVMNetworkInterface, um die NIC einem virtuellen Computer zuzuweisen.

In Option 4 oder 5 der Dokumentation [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) finden Sie eine Schritt-für-Schritt-Anleitung zum Erstellen eines virtuellen Computers und zum Zuweisen einer NIC.


## Aktualisieren eines vorhandenen Load Balancers


### Schritt 1

Weisen Sie unter Verwendung des Load Balancers aus dem vorherigen Beispiel mithilfe von „Get-AzureRmLoadBalancer“ der Variablen „$slb“ ein Load Balancer-Objekt zu.

	$slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Schritt 2

Im folgenden Beispiel fügen Sie einem vorhandenen Load Balancer eine neue eingehende NAT-Regel hinzu, die Port 81 in der Front-End-Anwendung und Port 8181 für den Back-End-Pool verwendet.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Schritt 3

Speichern Sie die neue Konfiguration mithilfe von „Set-AzureLoadBalancer“.

	$slb | Set-AzureRmLoadBalancer

## Entfernen eines Load Balancers

Verwenden Sie den Befehl „Remove-AzureRmLoadBalancer“, um den zuvor erstellten Load Balancer namens „NRP-LB“ in der Ressourcengruppe „NRP-RG“ zu löschen.

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Sie können den optionalen Switch „-Force“ verwenden, um die Aufforderung zum Löschen zu vermeiden.



## Nächste Schritte

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_0107_2016-->
