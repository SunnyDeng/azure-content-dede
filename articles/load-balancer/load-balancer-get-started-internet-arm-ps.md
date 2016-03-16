<properties 
   pageTitle="Erstellen eines Load Balancers mit Internetzugriff im Ressourcen-Manager mithilfe von PowerShell | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie im Ressourcen-Manager mithilfe von PowerShell einen Load Balancer mit Internetzugriff erstellen."
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
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff im Ressourcen-Manager unter Verwendung von PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können auch erfahren, wie Sie [mithilfe des klassischen Bereitstellungsmodells einen Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Die folgenden Schritte zeigen, wie Sie einen internen Load Balancer mit Internetzugriff mit dem Azure-Ressourcen-Manager und PowerShell erstellen. Beim Azure-Ressourcen-Manager werden die Elemente, mit denen ein Load Balancer mit Internetzugriff erstellt wird, einzeln konfiguriert und dann zusammengeführt, um eine Ressource zu erstellen.

Hier finden Sie eine ausführliche Beschreibung der einzelnen Aufgaben, die zum Erstellen eines Load Balancers durchgeführt werden müssen.

## Was ist erforderlich, um einen Load Balancer mit Internetzugriff zu erstellen?

Zum Bereitstellen eines Load Balancers müssen folgende Objekte erstellt und konfiguriert werden:

- Front-End-IP-Konfiguration: Enthält öffentliche IP-Adressen für eingehenden Netzwerkdatenverkehr. 

- Back-End-Adresspool: Enthält Netzwerkschnittstellen (NICs), die virtuellen Computern den Empfang von Netzwerkdatenverkehr des Load Balancers ermöglichen.

- Lastenausgleichsregeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port im Back-End-Adresspool.

- Eingehende NAT-Regeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool.

- Tests: Enthält Integritätstests zum Prüfen der Verfügbarkeit von VM-Instanzen im Back-End-Adresspool.

Unter [Unterstützung des Azure-Ressourcen-Managers für Load Balancer](load-balancer-arm.md) erhalten Sie weitere Informationen zu Load Balancer-Komponenten mit Azure-Ressourcen-Manager.


## Einrichten von PowerShell für die Verwendung des Ressourcen-Managers

Stellen Sie sicher, dass Sie über die neueste Produktionsversion des ARM-Moduls (Azure-Ressourcen-Manager) für PowerShell verfügen.

### Schritt 1

		PS C:\> Login-AzureRmAccount

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.<BR>

### Schritt 2

Überprüfen Sie die Abonnements für das Konto.

		PS C:\> Get-AzureRmSubscription 

### Schritt 3 

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.<BR>

		PS C:\> Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

### Schritt 4

Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)


    	PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Erstellen eines virtuellen Netzwerks und einer öffentlichen IP-Adresse für den Front-End-IP-Adresspool

### Schritt 1

Erstellen Sie ein Subnetz und ein virtuelles Netzwerk.

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Schritt 2

Erstellen Sie eine Azure-PIP-Ressource (Public IP Address, öffentliche IP-Adresse) mit dem Namen *PublicIP* zur Verwendung durch einen Front-End-IP-Pool mit dem DNS-Namen *loadbalancernrp.westus.cloudapp.azure.com*. Der folgende Befehl verwendet den statischen Zuordnungstyp.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] Der Load Balancer verwendet den Domänennamen der öffentlichen IP-Adresse als seinen vollqualifizierten Domänennamen (FQDN). Dies ist eine Abkehr vom klassischen Bereitstellungsmodell, bei dem der Clouddienst als FQDN des Load Balancers verwendet wird. In diesem Beispiel lautet der FQDN *loadbalancernrp.westus.cloudapp.azure.com*.

## Erstellen eines Front-End-IP-Adresspools und eines Back-End-IP-Adresspools

### Schritt 1 

Erstellen Sie einen Front-End-IP-Pool namens *LB-Frontend*, der die öffentliche IP-Adresse *PublicIp* verwendet.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Schritt 2 

Erstellen Sie einen Back-End-Adresspool namens *LB-backend*.

	$beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Erstellen von LB-Regeln, NAT-Regeln, Test und Load Balancer

Im folgenden Beispiel werden folgende Elemente erstellt:

- eine NAT-Regel, um sämtlichen an Port 3441 eingehenden Datenverkehr für Port 3389 zu übersetzen.
- eine NAT-Regel, um sämtlichen, bei Port 3442 eingehenden Datenverkehr für Port 3389 zu übersetzen.
- eine Load Balancer-Regel für die gleichmäßige Verteilung des gesamten an Pool 80 eingehenden Datenverkehrs an Port 80 an die Adressen im Back-End-Pool.
- eine Testregel, die den Integritätsstatus der Seite *HealthProbe.aspx* überprüft.
- einen Load Balancer, der alle oben aufgeführten Objekte verwendet.


### Schritt 1

Erstellen Sie die NAT-Regeln.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Schritt 2

Erstellen Sie eine Load Balancer-Regel.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Schritt 3

Erstellen Sie einen Integritätstest. Es gibt zwei Möglichkeiten, einen Test zu konfigurieren:
 
HTTP-Test
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
oder

TCP-Test
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Schritt 4

Erstellen Sie den Load Balancer mithilfe der oben erstellten Objekte.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Erstellen von NICs

Sie müssen Netzwerkschnittstellen erstellen (oder vorhandene ändern) und diese NAT-Regeln, Load Balancer-Regeln und Tests zuordnen.

### Schritt 1 

Rufen Sie das virtuelle Netzwerk und das zugehörige Subnetz ab, in denen die NICs erstellt werden sollen.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Schritt 2

Erstellen Sie eine Netzwerkkarte mit dem Namen *lb-nic1-be*, und ordnen Sie sie der ersten NAT-Regel und dem ersten (und einzigen) Back-End-Adresspool zu.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Schritt 3

Erstellen Sie eine NIC mit dem Namen *lb-nic2-be*, und ordnen Sie sie der zweiten NAT-Regel und dem ersten (und einzigen) Back-End-Adresspool zu.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Schritt 4

Überprüfen Sie die Netzwerkkarten.


	PS C:\> $backendnic1

Erwartete Ausgabe:

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



### Schritt 5

Weisen Sie die Netzwerkkarten mithilfe des Cmdlets `Add-AzureRmVMNetworkInterface` unterschiedlichen virtuellen Computern zu.

Eine Anleitung zum Erstellen eines virtuellen Computers und zum Zuweisen einer Netzwerkkarte finden Sie unter [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) (Option 5 im Beispiel).


Falls Sie bereits einen virtuellen Computer erstellt haben, können Sie die Netzwerkschnittstelle mit den folgenden Schritten hinzufügen:

#### Schritt 1 

Laden Sie die Lastenausgleichsressource in eine Variable (sofern nicht schon geschehen). Die verwendete Variable heißt „$lb“ und verwendet die Namen aus der oben erstellten Lastenausgleichsressource.

	$lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

#### Schritt 2 

Laden Sie die Back-End-Konfiguration in eine Variable.

	PS C:\> $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Schritt 3 

Laden Sie die bereits erstellte Netzwerkschnittstelle in eine Variable. Der Variablenname lautet „$nic“. Der Name der Netzwerkschnittstelle ist der im obigen Beispiel verwendete.

	$nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Schritt 4

Ändern Sie die Back-End-Konfiguration in der Netzwerkschnittstelle.

	PS C:\> $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Schritt 5 

Speichern Sie das Netzwerkschnittstellenobjekt.

	PS C:\> Set-AzureRmNetworkInterface -NetworkInterface $nic

Nachdem eine Netzwerkschnittstelle zum Back-End-Pool des Lastenausgleichs hinzugefügt wurde, empfängt sie Netzwerkdatenverkehr basierend auf den Lastenausgleichsregeln für diese Lastenausgleichsressouce.

## Aktualisieren eines vorhandenen Load Balancers


### Schritt 1

Weisen Sie unter Verwendung des Load Balancers aus dem vorherigen Beispiel mithilfe von „Get-AzureLoadBalancer“ der Variablen „$slb“ ein Load Balancer-Objekt zu.

	$slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Schritt 2

Im folgenden Beispiel fügen Sie einem vorhandenen Load Balancer eine neue eingehende NAT-Regel hinzu, die Port 81 in der Front-End-Anwendung und Port 8181 für den Back-End-Pool verwendet.

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP


### Schritt 3

Speichern Sie die neue Konfiguration mithilfe von „Set-AzureLoadBalancer“.

	$slb | Set-AzureRmLoadBalancer

## Entfernen eines Lastenausgleichs

Verwenden Sie den Befehl `Remove-AzureLoadBalancer`, um den zuvor erstellten Load Balancer namens „NRP-LB“ in der Ressourcengruppe „NRP-RG“ zu löschen.

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Sie können den optionalen Switch „-Force“ verwenden, um die Aufforderung zum Löschen zu vermeiden.

## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0302_2016-->