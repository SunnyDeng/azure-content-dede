<properties 
   pageTitle="Erstellen eines internen Load Balancers mit der Azure Befehlszeilenschnittstelle im Ressourcen-Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie im Ressourcen-Manager mithilfe der Azure-Befehlszeilenschnittstelle einen internen Load Balancer erstellen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines internen Load Balancers mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Was ist erforderlich, um einen internen Lastenausgleich zu erstellen?

Zum Bereitstellen eines Load Balancers müssen folgende Objekte erstellt und konfiguriert werden:

- Front-End-IP-Konfiguration: Dient zum Konfigurieren der privaten IP-Adresse für eingehenden Netzwerkdatenverkehr 

- Back-End-Adresspool – Netzwerkkarten (NICs) zum Empfangen von Datenverkehr vom Load Balancer.

- Lastenausgleichsregeln: Regeln, die einen eingehenden Datenverkehr am Netzwerkport einem Port zuordnen, der Netzwerkdatenverkehr im Back-End-Pool empfängt.

- Eingehende NAT-Regeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool.

- Tests: Integritätstests zum Prüfen der Verfügbarkeit von virtuellen Computern im Back-End-Adresspool.

Unter [Unterstützung des Azure-Ressourcen-Managers für den Load Balancer](load-balancer-arm.md) erhalten Sie weitere Informationen zu Load Balancer-Komponenten des Azure-Ressourcen-Managers.

## Einrichten der Befehlszeilenschnittstelle für die Verwendung des Ressourcen-Managers

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Erwartete Ausgabe:

		info:    New mode is arm

## Schritte zum Erstellen eines internen Load Balancers 

Anhand der folgenden Schritte erstellen Sie auf der Grundlage des oben beschriebenen Szenarios einen internen Load Balancer.

### Schritt 1 

Sofern Sie noch nicht über die neueste Version der [Azure-Befehlszeilenschnittstelle](https://azure.microsoft.com/downloads/) verfügen, laden Sie diese herunter.

### Schritt 2 

Authentifizieren Sie nach der Installation Ihr Konto.

	azure login

Beim Authentifizierungsprozess werden Ihr Benutzername und das Kennwort für Ihr Azure-Abonnement angefordert.

### Schritt 3

Wechseln Sie mit den Befehlstools in den Azure-Ressourcen-Manager-Modus.

	azure config mode arm

## Erstellen einer Ressourcengruppe

Alle Ressourcen im Azure-Ressourcen-Manager werden einer Ressourcengruppe zugeordnet. Erstellen Sie eine Ressourcengruppe, wenn Sie noch keine erstellt haben.

	azure group create <resource group name> <location>


## Erstellen Sie einen internen Load Balancer. 


### Schritt 1 

Erstellen Sie mit dem Befehl `azure network lb create` einen internen Load Balancer. Im folgenden Szenario wird die Ressourcengruppe „nrprg“ in der Region „USA, Osten“ erstellt.
 	
	azure network lb create -n nrprg -l westus

>[AZURE.NOTE]Alle Ressourcen für einen internen Load Balancer, wie z. B. das virtuelle Netzwerk und das Subnetz des virtuellen Netzwerks, müssen sich in derselben Ressourcengruppe und in derselben Region befinden.


### Schritt 2 

Erstellen Sie eine Front-End-IP-Adresse für den internen Load Balancer. Die verwendete IP-Adresse muss sich im Subnetzbereich des virtuellen Netzwerks befinden.

	
	azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

Verwendete Parameter:

**-g:** Ressourcengruppe **-l**: Name der internen Load Balancer-Gruppe **-n:** Name der Front-End-IP-Adresse **-a:** private IP-Adresse innerhalb des Subnetzbereichs **-e:** Subnetzname **-m:** Name des virtuellen Netzwerks

### Schritt 3 

Erstellen Sie den Back-End-Adresspool.

	azure network lb address-pool create -g nrprg -l ilbset -n beilb

Verwendete Parameter:

**-g:** Ressourcengruppe **-l:** Name der internen Load Balancer-Gruppe **-n:** Name des Back-End-Adresspools

Nach der Definition einer Front-End-IP-Adresse und eines Back-End-Adresspools können Sie Load Balancer-Regeln und NAT-Eingangsregeln erstellen und Integritätstests anpassen.

### Schritt 4


Erstellen Sie eine Load Balancer-Regel für den internen Load Balancer. Im oben beschriebenen Szenario erstellt der Befehl eine Load Balancer-Regel, die am Pool 1433 im Front-End-Pool lauscht und den ausgeglichenen Netzwerkdatenverkehr ebenfalls über Port 1433 zurück an den Back-End-Adresspool sendet.

	azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

Verwendete Parameter:

**-g:** Ressourcengruppe **-l:** Name der internen Load Balancer-Gruppe **-n:** Name der Load Balancer-Regel **-p:** Protokoll für die Regel **-f:** Port, an dem auf eingehenden Netzwerkdatenverkehr im Load Balancer-Front-End gelauscht wird **-b:** Port, an dem Netzwerkdatenverkehr im Back-End-Adresspool empfangen wird

### Schritt 5

Erstellen Sie eingehende NAT-Regeln. Mit eingehenden NAT-Regeln werden Endpunkte in einem Load Balancer erstellt, die für eine bestimmte virtuelle Computerinstanz gelten. Im obigen Beispiel wurden zwei NAT-Regeln für den Remotedesktopzugriff erstellt.

	azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389
	
	azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

Verwendete Parameter:

**-g:** Ressourcengruppe **-l:** Name der internen Load Balancer-Gruppe **-n:** Name der eingehenden NAT-Regel **-p**: Protokoll für die Regel **-f:** Port, an dem auf eingehenden Netzwerkdatenverkehr im Load Balancer-Front-End gelauscht wird **-b:** Port, an dem Netzwerkdatenverkehr im Back-End-Adresspool empfangen wird

### Schritt 5 

Erstellen Sie Integritätstests für den Load Balancer. Eine Integritätstest überprüft alle VM-Instanzen, um sicherzustellen, dass Netzwerkdatenverkehr gesendet werden kann. VM-Instanzen mit Fehlern beim Test werden aus dem Load Balancer entfernt, bis sie wieder online geschaltet und als fehlerfrei überprüft wurden.

	azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

**-g:** Ressourcengruppe **-l:** Name der internen Load Balancer-Gruppe **-n:** Name des Integritätstests **-p:** Protokoll für den Integritätstest **-i:** Testintervall in Sekunden **-c:** Anzahl der Prüfungen

>[AZURE.NOTE]Die Microsoft Azure-Plattform nutzt eine statische, öffenlich routingfähige IPv4-Adresse für eine Vielzahl von administrativen Szenarien. Die IP-Adresse lautet 168.63.129.16. Diese IP-Adresse sollte nicht durch Firewalls blockiert werden, da dies zu unerwartetem Verhalten führen kann. In Bezug auf den internen Lastenausgleich in Azure wird diese IP-Adresse von Überwachungstests aus dem Lastenausgleich verwendet, um den Integritätsstatus von virtuellen Computern in einer Lastenausgleichsgruppe zu bestimmen. Wenn eine Netzwerksicherheitsgruppe verwendet wird, um den Datenverkehr auf virtuellen Azure-Computern in einer internen Lastenausgleichsgruppe einzuschränken, oder wenn eine Netzwerksicherheitsgruppe einem Subnetz eines virtuellen Netzwerks zugewiesen ist, stellen Sie sicher, dass eine Netzwerksicherheitsregel hinzugefügt wird, um Datenverkehr von 168.63.129.16 zuzulassen.

## Erstellen von NICs

Sie müssen NICs erstellen (oder vorhandene ändern) und diese NAT-Regeln, Load Balancer-Regeln und Tests zuordnen.

### Schritt 1 

Erstellen Sie eine NIC mit dem Namen *lb-nic1-be*, und ordnen Sie sie der NAT-Regel *rdp1* und dem Back-End-Adresspool *beilb* zu.
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

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

### Schritt 2

Erstellen Sie eine NIC mit dem Namen *lb-nic2-be*, und ordnen Sie sie der NAT-Regel *rdp2* und dem Back-End-Adresspool *beilb* zu.

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### Schritt 3 

Erstellen Sie einen virtuellen Computer (VM) mit dem Namen *DB1*, und ordnen Sie ihn der NIC mit dem Namen *lb-nic1-be* zu. Ein Speicherkonto namens *web1nrp* wurde erstellt, bevor der folgende Befehl ausgeführt wurde.

	azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT]VMs in einem Load Balancer müssen in derselben Verfügbarkeitsgruppe enthalten sein. Erstellen Sie mit `azure availset create` eine Verfügbarkeitsgruppe.

### Schritt 4

Erstellen Sie einen virtuellen Computer (VM) mit dem Namen *DB2*, und ordnen Sie ihn der NIC mit dem Namen *lb-nic2-be* zu. Ein Speicherkonto namens *web1nrp* wurde erstellt, bevor der folgende Befehl ausgeführt wurde:

	azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Löschen eines Load Balancers 


Verwenden Sie zum Entfernen eines Load Balancers den folgenden Befehl:

	azure network lb delete -g nrprg -n ilbset 

**nrprg** steht für die Ressourcengruppe und **ilbset** für den Namen des internen Load Balancers.


## Nächste Schritte

[Konfigurieren eines Load Balancer-Verteilungsmodus mithilfe der Quell-IP-Affinität](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->