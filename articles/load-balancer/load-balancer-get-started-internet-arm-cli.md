<properties 
   pageTitle="Erstellen eines Load Balancers mit Internetzugriff in Ressourcen-Manager über die Azure-Befehlszeilenschnittstelle (CLI) | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen Load Balancer mit Internetzugriff in Ressourcen-Manager über die Azure-Befehlszeilenschnittstelle (CLI) erstellen."
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
   ms.date="02/12/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können auch erfahren, wie Sie [mithilfe klassischer Bereitstellung einen Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Hier finden Sie eine ausführliche Beschreibung der einzelnen Aufgaben, die zum Erstellen eines Load Balancers durchgeführt werden müssen.


## Was ist erforderlich, um einen Lastenausgleich für Internetverbindungen zu erstellen?

Zum Bereitstellen eines Load Balancers müssen Sie die folgenden Objekte erstellen und konfigurieren.

- Front-End-IP-Konfiguration: Enthält öffentliche IP-Adressen für eingehenden Netzwerkdatenverkehr. 

- Back-End-Adresspool: Enthält Netzwerkschnittstellen (NICs), die virtuellen Computern den Empfang von Netzwerkdatenverkehr des Load Balancers ermöglichen.

- Lastenausgleichsregeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port im Back-End-Adresspool.

- Eingehende NAT-Regeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool.

- Tests: Enthält Integritätstests zum Prüfen der Verfügbarkeit von VM-Instanzen im Back-End-Adresspool.

Unter [Unterstützung des Azure-Ressourcen-Managers für den Load Balancer](load-balancer-arm.md) erhalten Sie weitere Informationen zu Load Balancer-Komponenten des Azure-Ressourcen-Managers.

## Einrichten der Befehlszeilenschnittstelle für die Verwendung des Ressourcen-Managers

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../../articles/xplat-cli-install.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Erwartete Ausgabe:

		info:    New mode is arm

## Erstellen eines virtuellen Netzwerks und einer öffentlichen IP-Adresse für den Front-End-IP-Adresspool

### Schritt 1

Erstellen Sie ein virtuelles Netzwerk (VNET) mit dem Namen *NRPVnet* in der Region „USA, Osten“ mithilfe einer Ressourcengruppe mit dem Namen *NRPRG*.

	azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

Erstellen Sie ein Subnetz mit dem Namen *NRPVnetSubnet* mit dem CIDR-Block 10.0.0.0/24 in *NRPVnet*.

	azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

### Schritt 2

Erstellen Sie eine öffentliche IP-Adresse mit dem Namen *NRPPublicIP*, die von einem Front-End-IP-Adresspool verwendet werden soll, mit dem DNS-Namen *loadbalancernrp.eastus.cloudapp.azure.com*. Der folgende Befehl verwendet den statische Zuordnungstyp und ein Leerlaufzeitlimit von 4 Minuten.

	azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4


>[AZURE.IMPORTANT] Der Load Balancer verwendet den Domänennamen der öffentlichen IP-Adresse als seinen vollqualifizierten Domänennamen (FQDN). Dies ist eine Abkehr von der klassischen Bereitstellung, bei der der Clouddienst als FQDN des Load Balancers verwendet wird. In diesem Beispiel lautet der FQDN *loadbalancernrp.eastus.cloudapp.azure.com*.

## Einrichten eines Load Balancers

Im folgenden Beispiel wird mit dem nachstehenden Befehl der Load Balancer *NRPlb* in der Ressourcengruppe *NRPRG* in der Azure-Region *USA, Osten* erstellt.

	azure network lb create NRPRG NRPlb eastus

## Erstellen eines Front-End-IP-Adresspools und Back-End-IP-Adresspools

Im folgenden Beispiel wird der Front-End-IP-Adresspool erstellt, der den eingehenden Netzwerkdatenverkehr für den Load Balancer empfängt. Außerdem wird der Back-End-IP-Pool erstellt, an den der Front-End-Adresspool den einem Lastenausgleich unterzogenen Netzwerkdatenverkehr sendet.

### Schritt 1 

Erstellen Sie einen Front-End-IP-Adresspool, der die im vorherigen Schritt erstellte öffentliche IP-Adresse dem Load Balancer zuordnet.

	azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

### Schritt 2 

Richten Sie einen Back-End-Adresspool für den Empfang des eingehenden Datenverkehrs vom Front-End-IP-Adresspool ein.

	azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## Erstellen von Lastenausgleichs- und NAT-Regeln sowie Tests

Im folgenden Beispiel werden die folgenden Elemente erstellt:

- eine NAT-Regel, um sämtlichen an Port 21 eingehenden Datenverkehr für Port 22<sup>1</sup> zu übersetzen.
- eine NAT-Regel, um sämtlichen, bei Port 23 eingehenden Datenverkehr für Port 22 zu übersetzen.
- eine Load Balancer-Regel für die gleichmäßige Verteilung des gesamten an Pool 80 eingehenden Datenverkehrs an Port 80 an die Adressen im Back-End-Pool.
- eine Testregel, die den Integritätsstatus der Seite *HealthProbe.aspx* überprüft

<sup>1</sup> NAT-Regeln sind einer bestimmten Instanz eines virtuellen Computers hinter dem Load Balancer zugeordnet. Im folgenden Beispiel wird der an Port 21 eingehende Netzwerkdatenverkehr an einen speziellen virtuellen Computer an Port 22 gesendet, der mit einer NAT-Regel verknüpft ist. Sie müssen für die NAT-Regel als Protokoll UDP oder TCP auswählen. Einem Port kann jeweils nur eines der beiden Protokolle zugewiesen werden.

### Schritt 1

Erstellen Sie die NAT-Regeln.

	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh1 -p tcp -f 21 -b 22
	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh2 -p tcp -f 23 -b 22

Parameter:

- **-g**: Ressourcengruppenname
- **-l**: Name des Load Balancers 
- **-n**: Name der Ressource (NAT-, Test- oder Load Balancer-Regel)
- **-p**: Protokoll (TCP oder UDP)  
- **-f**: Zu verwendender Front-End-Port (der „probe“-Befehl verwendet „-f“ zum Definieren des Testpfads)
- **-b**: Zu verwendender Back-End-Port

### Schritt 2

Erstellen Sie eine Load Balancer-Regel.

	azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool
### Schritt 3

Erstellen Sie einen Integritätstest.

	azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

	
	

**-g**: Ressourcengruppe **-l**: Name des Load Balancer-Satzes **-n**: Name der Integritätsprüfung **-p**: Protokoll für die Integritätsprüfung **-i**: Testintervall in Sekunden **-c**: Anzahl der Prüfungen

### Schritt 4

Überprüfen Sie Ihre Einstellungen.

	azure network lb show nrprg nrplb

Erwartete Ausgabe:

	info:    Executing command network lb show
	+ Looking up the load balancer "nrplb"
	+ Looking up the public ip "NRPPublicIP"	
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
	data:    Name                            : nrplb
	data:    Type                            : Microsoft.Network/loadBalancers
	data:    Location                        : eastus
	data:    Provisioning State              : Succeeded
	data:    Frontend IP configurations:
	data:      Name                          : NRPfrontendpool
	data:      Provisioning state            : Succeeded
	data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
	data:      Public IP allocation method   : Static
	data:      Public IP address             : 40.114.13.145
	data:
	data:    Backend address pools:
	data:      Name                          : NRPbackendpool
	data:      Provisioning state            : Succeeded
	data:
	data:    Load balancing rules:
	data:      Name                          : HTTP
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 80
	data:      Backend port                  : 80
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:
	data:    Inbound NAT rules:
	data:      Name                          : ssh1
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 21
	data:      Backend port                  : 22
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:      Name                          : ssh2
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 23
	data:      Backend port                  : 22
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:    Probes:
	data:      Name                          : healthprobe
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Http
	data:      Port                          : 80
	data:      Interval in seconds           : 15
	data:      Number of probes              : 4
	data:
	info:    network lb show command OK

## Erstellen von NICs

Sie müssen NICs erstellen (oder vorhandene ändern) und diese NAT-Regeln, Load Balancer-Regeln und Tests zuordnen.

### Schritt 1 

Erstellen Sie eine NIC mit dem Namen *lb-nic1-be*, und ordnen Sie sie der NAT-Regel *rdp1* und dem Back-End-Adresspool *NRPbackendpool* zu.
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

Parameter:

- **-g:** Ressourcengruppenname
- **-n:** Name der NIC-Ressource
- **--subnet-name**: Name des Subnetzes 
- **--subnet-vnet-name**: Name des virtuellen Netzwerks
- **-d**: ID der Back-End-Poolressource (beginnt mit /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool>) 
- **-e**: ID der NAT-Regel, die der NIC-Ressource zugeordnet wird (beginnt mit /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>)


Erwartete Ausgabe:

	info:    Executing command network nic create
	+ Looking up the network interface "lb-nic1-be"
	+ Looking up the subnet "nrpvnetsubnet"
	+ Creating network interface "lb-nic1-be"
	+ Looking up the network interface "lb-nic1-be"
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	data:    Name                            : lb-nic1-be
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : eastus
	data:    Provisioning state              : Succeeded
	data:    Enable IP forwarding            : false
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 10.0.0.4
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
	data:      Load balancer backend address pools
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:      Load balancer inbound NAT rules:
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
	data:
	info:    network nic create command OK

### Schritt 2

Erstellen Sie eine NIC mit dem Namen *lb-nic2-be*, und ordnen Sie sie der NAT-Regel *rdp2* und dem Back-End-Adresspool *NRPbackendpool* zu.

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### Schritt 3 

Erstellen Sie einen virtuellen Computer (VM) mit dem Namen *web1*, und ordnen Sie ihn der NIC mit dem Namen *lb-nic1-be* zu. Ein Speicherkonto namens *web1nrp* wurde erstellt, bevor der folgende Befehl ausgeführt wurde:

	azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT] VMs in einem Load Balancer müssen in derselben Verfügbarkeitsgruppe enthalten sein. Erstellen Sie mit `azure availset create` eine Verfügbarkeitsgruppe.

Die Ausgabe sieht dann wie folgt aus:

	info:    Executing command vm create
	+ Looking up the VM "web1"
	Enter username: azureuser
	Enter password for azureuser: *********
	Confirm password: *********
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Looking up the storage account web1nrp
	+ Looking up the availability set "nrp-avset"
	info:    Found an Availability set "nrp-avset"
	+ Looking up the NIC "lb-nic1-be"
	info:    Found an existing NIC "lb-nic1-be"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
	info:    This is a NIC without publicIP configured
	+ Creating VM "web1"
	info:    vm create command OK

>[AZURE.NOTE] Die Informationsmeldung **Dies ist eine NIC ohne konfigurierte öffentliche IP-Adresse** wird erwartet, da sich die für den Load Balancer erstellte NIC über die öffentliche IP-Adresse des Load Balancers mit dem Internet verbindet.

Da die NIC *lb-nic1-be* der NAT-Regel *rdp1* zugeordnet ist, können Sie über RDP an Port 3441 auf dem Load Balancer eine Verbindung mit *web1* herstellen.

### Schritt 4

Erstellen Sie einen virtuellen Computer (VM) mit dem Namen *web2*, und ordnen Sie ihn der NIC mit dem Namen *lb-nic2-be* zu. Ein Speicherkonto namens *web1nrp* wurde erstellt, bevor der folgende Befehl ausgeführt wurde:

	azure vm create --resource-group nrprg --name web2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Aktualisieren eines vorhandenen Load Balancers

Sie können Regeln hinzufügen, die auf einen vorhandenen Load Balancer verweisen. Im folgenden Beispiel wird dem vorhandenen Load Balancer **NRPlb** eine neue Load Balancer-Regel hinzugefügt.

	azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

Parameter:

**-g**: Ressourcengruppenname<br> **-l**: Name des Load Balancers<BR> **-n**: Name der Load Balancer-Regel<BR> **-p**: Protokoll<BR> **-f**: Front-End-Port<BR> **-b**: Back-End-Port<BR> **-t**: Name des Front-End-Pools<BR> **-b**: Name des Back-End-Pools<BR>

## Löschen eines Load Balancers 


Verwenden Sie zum Entfernen eines Load Balancers den folgenden Befehl:

	azure network lb delete -g nrprg -n nrplb 

**nrprg** steht für die Ressourcengruppe und **nrplb** für den Namen des Load Balancers.

## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0224_2016-->