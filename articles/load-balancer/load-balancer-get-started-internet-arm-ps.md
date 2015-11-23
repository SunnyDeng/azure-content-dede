<properties 
   pageTitle="Erstellen eines Load Balancers mit Internetzugriff in Ressourcen-Manager mithilfe von PowerShell | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen Load Balancer mit Internetzugriff in Ressourcen-Manager mithilfe von PowerShell erstellen."
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
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers für Internetzugriff im Ressourcen-Manager mithilfe von PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Informationen zum klassischen Azure-Bereitstellungsmodell finden Sie unter [Erste Schritte zum Erstellen eines Load Balancers für Internetzugriff in einem klassischen Bereitstellungsmodell](load-balancer-get-started-internet-classic-portal.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Die folgenden Schritte zeigen, wie Sie einen internen Load Balancer für Internetverbindungen mit dem Azure-Ressourcen-Manager mit PowerShell erstellen. Beim Azure-Ressourcen-Manager werden die Elemente, mit denen ein Lastenausgleich für Internetverbindungen erstellt wird, einzeln konfiguriert und dann zusammengeführt, um eine Ressource zu erstellen.

Auf dieser Seite wird die Abfolge der einzelnen Aufgaben beschrieben, die zum Erstellen eines Load Balancers durchgeführt werden müssen. Sie erfahren detailliert, welche Schritte durchgeführt werden müssen, um einen Load Balancer zu erstellen.

## Was ist erforderlich, um einen Lastenausgleich für Internetverbindungen zu erstellen?

Zum Bereitstellen eines Lastenausgleich müssen Sie die folgenden Objekte erstellen und konfigurieren:

- Front-End-IP-Adresskonfiguration – Öffentliche IP-Adressen für eingehenden Netzwerkdatenverkehr. 

- Back-End-Adresspool – Netzwerkkarten (NICs) zum Empfangen von Datenverkehr vom Load Balancer.

- Lastenausgleichsregeln – Regeln für das Zuordnen eines öffentlichen Ports im Load Balancer zu Ports auf den NICs im Back-End-Adresspool.

- Eingehende NAT-Regeln – Regeln für das Zuordnen eines öffentlichen Ports im Load Balancer zu einem Port einer einzelnen NIC im Back-End-Adresspool.

- Tests – Integritätstests zum Prüfen der Verfügbarkeit von VMs, die mit den NICs im Back-End-Adresspool verknüpft sind.

Unter [Unterstützung des Azure-Ressourcen-Managers für den Lastenausgleich](load-balancer-arm.md) erhalten Sie weitere Informationen zu Lastenausgleichskomponenten des Azure-Ressourcen-Managers.


## Einrichten von PowerShell für die Verwendung des Ressourcen-Managers
Stellen Sie sicher, dass Sie die neueste Produktionsversion des Azure-Moduls für PowerShell verwenden und PowerShell ordnungsgemäß eingerichtet wurde, damit Sie auf Ihr Azure-Abonnement Zugriff haben.

### Schritt 1

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Führen Sie an einer Azure PowerShell-Eingabeaufforderung das Cmdlet **Switch-AzureMode** aus, um zum Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		Switch-AzureMode AzureResourceManager
	
	Erwartete Ausgabe:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]Das Cmdlet "Switch-AzureMode" ist demnächst veraltet. In diesem Fall werden alle Ressourcen-Manager-Cmdlets umbenannt.



### Schritt 2

Melden Sie sich beim Azure-Konto an.


    PS C:\> Add-AzureAccount

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.


### Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Um eine Liste der verfügbaren Abonnements anzuzeigen, verwenden Sie das Cmdlet „Get-AzureSubscription“.

## Erstellen einer Ressourcengruppe

Erstellen Sie eine neue Ressourcengruppe mit dem Namen *NRP-RG* in der Azure-Region *USA, Westen*.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Erstellen eines virtuellen Netzwerks und einer öffentlichen IP-Adresse für den Front-End-IP-Adresspool

### Schritt 1

Erstellen Sie ein Subnetz und ein virtuelles Netzwerk.

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Schritt 2

Erstellen Sie eine öffentliche IP-Adresse mit dem Namen *PublicIP*, die von einem Front-End-IP-Adresspool mit dem DNS-Namen *loadbalancernrp.westus.cloudapp.azure.com* verwendet werden soll. Der folgende Befehl verwendet den statischen Zuordnungstyp.

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]Der Load Balancer verwendet den Domänennamen der öffentlichen IP-Adresse als seinen vollqualifizierten Domänennamen (FQDN). Dies ist eine Abkehr vom klassischen Bereitstellungsmodell, bei dem der Clouddienst als FQDN des Load Balancers verwendet wird. In diesem Beispiel lautet der FQDN *loadbalancernrp.westus.cloudapp.azure.com*.

## Erstellen eines Front-End-IP-Adresspools und Back-End-IP-Adresspools

### Schritt 1 

Erstellen Sie einen Front-End-IP-Adresspool mit dem Namen *LB-Frontend*, der die öffentliche IP-Adresse *PublicIp* verwendet.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Schritt 2 

Erstellen Sie einen Back-End-Adresspool mit dem Namen *LB-backend*.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Erstellen von LB-Regeln, NAT-Regeln, einem Test und einem Load Balancer

Im folgenden Beispiel werden die folgenden Elemente erstellt:

- eine NAT-Regel, um sämtlichen an Port 3441 eingehenden Datenverkehr für Port 3389<sup>1</sup> zu übersetzen.
- eine NAT-Regel, um sämtlichen an Port 3442 eingehenden Datenverkehr für Port 3389 zu übersetzen.
- eine Load Balancer-Regel für die gleichmäßige Verteilung des gesamten an Pool 80 eingehenden Datenverkehrs an Port 80 an die Adressen im Back-End-Pool.
- eine Testregel, die den Integritätsstatus der Seite *HealthProbe.aspx* überprüft.
- einen Load Balancer, der alle oben aufgeführten Objekte verwendet.


<sup>1</sup> NAT-Regeln sind mit einer bestimmten Instanz eines virtuellen Computers hinter dem Lastenausgleich zugeordnet. Im folgenden Beispiel wird der an Port 3341 eingehende Netzwerkdatenverkehr an einen speziellen virtuellen Computer an Port 3389 gesendet, der mit einer NAT-Regel verknüpft ist. Sie müssen für die NAT-Regel als Protokoll UDP oder TCP auswählen. Einem Port kann jeweils nur eines der beiden Protokolle zugewiesen werden.

### Schritt 1

Erstellen Sie die NAT-Regeln.

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Schritt 2

Erstellen Sie eine Load Balancer-Regel.

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Schritt 3

Erstellen Sie einen Integritätstest.

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Schritt 4

Erstellen Sie den Load Balancer mithilfe der oben erstellten Objekte.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Erstellen von NICs

Sie müssen NICs erstellen (oder vorhandene ändern) und diesen NAT-Regeln, Load Balancer-Regeln und Tests zuordnen.

### Schritt 1 

Rufen Sie das VNet und das Subnetz ab, in dem die Netzwerkkarten erstellt werden sollen.

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Schritt 2

Erstellen Sie eine Netzwerkkarte mit dem Namen *lb-nic1-be*, und ordnen Sie diese der ersten NAT-Regel und dem ersten (und einzigen) Back-End-IP-Adresspool zu.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Schritt 3

Erstellen Sie eine Netzwerkkarte mit dem Namen *lb-nic2-be*, und ordnen Sie diese der zweiten NAT-Regel und dem ersten (und einzigen) Back-End-IP-Adresspool zu.

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Schritt 4

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

Weisen Sie die Netzwerkkarten mithilfe des Cmdlets `Add-AzureVMNetworkInterface` den verschiedenen virtuellen Computern zu.

Eine Anleitung zum Erstellen eines virtuellen Computers und zum Zuweisen einer Netzwerkkarte finden Sie unter [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) unter Option 5 im Beispiel.

## Aktualisieren eines vorhandenen Lastenausgleichsmoduls


### Schritt 1

Weisen Sie unter Verwendung des Lastenausgleichsmoduls aus dem vorherigen Beispiel mithilfe von „Get-AzureLoadBalancer“ der Variablen „$slb“ ein Lastenausgleichsmodul-Objekt zu.

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Schritt 2

Im folgenden Beispiel fügen Sie einem vorhandenen Lastenausgleichsmodul eine neue eingehende NAT-Regel hinzu, die Port 81 in der Front-End-Anwendung und Port 8181 für den Back-End-Pool verwendet.

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Schritt 3

Speichern Sie die neue Konfiguration mithilfe von „Set-AzureLoadBalancer“.

	$slb | Set-AzureLoadBalancer

## Entfernen eines Lastenausgleichsmoduls

Verwenden Sie den Befehl „Remove-AzureLoadBalancer“, um das zuvor erstellte Lastenausgleichsmodul namens „NRP-LB“ in der Ressourcengruppe „NRP-RG“ zu löschen.

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Sie können die optionale Option „-Force“ verwenden, um die Aufforderung zum Löschen zu umgehen.

## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-internal-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Load Balancer](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->