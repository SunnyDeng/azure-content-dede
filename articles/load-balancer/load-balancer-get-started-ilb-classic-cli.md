<properties 
   pageTitle="Erstellen eines internen Load Balancers im klassischen Bereitstellungsmodell mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="Erfahren Sie, wie Sie einen internen Load Balancer im klassischen Bereitstellungsmodell mithilfe der Azure-Befehlszeilenschnittstelle erstellen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines internen Load Balancers (klassisch) mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Erstellen einer internen Load Balancer-Gruppe für virtuelle Computer

Zum Erstellen einer internen Load Balancer-Gruppe und der Server, die den Datenverkehr an diese Gruppe senden, müssen Sie Folgendes ausführen:

1. Erstellen Sie eine ILB-Instanz, die als Endpunkt für den eingehenden Datenverkehr dient, für den Lastenausgleich zwischen den Servern einer Lastenausgleichsgruppe durchgeführt wird.

1. Fügen Sie Endpunkte für die virtuellen Computer hinzu, die den eingehenden Datenverkehr empfangen.

1. Konfigurieren Sie die Server, die den Datenverkehr für den Lastenausgleich senden, so, dass der Datenverkehr an die virtuelle IP-Adresse (VIP) der ILB-Instanz gesendet wird.

## Schritte zum Erstellen eines internen Load Balancers mit der Befehlszeilenschnittstelle

Anhand der folgenden Anleitung erstellen Sie auf der Grundlage des oben beschriebenen Szenarios einen internen Load Balancer.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

2. Führen Sie den Befehl **azure config mode** aus, um in den klassischen Modus zu wechseln, wie unten dargestellt.

		azure config mode asm

	Erwartete Ausgabe:

		info:    New mode is asm


## Erstellen eines Endpunkts und einer Load Balancer-Gruppe 

Das Szenario setzt die virtuellen Computer „DB1“ und „DB2“ in einem Clouddienst namens „mytestcloud“ voraus. Beide virtuelle Computer verwenden ein virtuelles Netzwerk namens „my testvnet“ mit dem Subnetz „subnet-1“.

Dieses Handbuch erstellt eine interne Load Balancer-Gruppe, die Port 1433 als privaten Port und Port 1433 als lokalen Port verwendet.

Dies ist ein häufiges Szenario, bei dem sich die virtuellen SQL-Computer im Back-End befinden und über einen internen Load Balancer sichergestellt wird, dass die Datenbankserver nicht direkt über eine öffentliche IP-Adresse offen gelegt werden.


### Schritt 1 

Erstellen Sie einen internen Load Balancer mit `azure network service internal-load-balancer add`.

	 azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Verwendete Parameter:

**-r:** Clouddienstname<BR> **-n:** interner Load Balancer-Name<BR> **-t:** Subnetzname (dasselbe Subnetz wie für die dem Load Balancer hinzugefügten virtuellen Computer)<BR> **-a:** (optional) statische private IP-Adresse<BR>

Weitere Informationen finden Sie unter `azure service internal-load-balancer --help`.
 
Sie können die Eigenschaften des internen Load Balancers mithilfe des Befehls `azure service internal-load-balancer list` *Clouddienstname* überprüfen.

Dies ist ein Beispiel für die Ausgabe:

	azure service internal-load-balancer list my-testcloud
	info:    Executing command service internal-load-balancer list
	+ Getting cloud service deployment
	data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
	data:    ------  -------  ----------  -----------------------------
	data:    ilbset  Private  subnet-1    192.168.2.7
	info:    service internal-load-balancer list command OK


## Schritt 2 

Sie konfigurieren die interne Load Balancer-Gruppe, wenn Sie den ersten Endpunkt hinzufügen. Sie ordnen den Endpunkt, den virtuellen Computer und den Testport der internen Load Balancer-Gruppe in diesem Schritt zu.

	azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Verwendete Parameter:

**-k:** Port des lokalen virtuellen Computers<BR> **-t:** Testport<BR> **-r:** Testprotokoll <BR> **-e:** Testintervall in Sekunden <BR> **-f:** Timeoutintervall in Sekunden <BR> **-i:** Name des internen Load Balancers <BR>


## Schritt 3 

Überprüfen Sie die Load Balancer-Konfiguration mithilfe von `azure vm show` *Name des virtuellen Computers*.

	azure vm show DB1 

Ausgabe:

		azure vm show DB1
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "mytestcloud.cloudapp.net"
	data:    Location "East US 2"
	data:    VMName "DB1"
	data:    IPAddress "192.168.2.4"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "db1-DB1-0-201511120457370846"
	data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "137.116.64.107"
	data:    VirtualIPAddresses 0 name "db1ContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    VirtualIPAddresses 1 address "192.168.2.7"
	data:    VirtualIPAddresses 1 name "ilbset"
	data:    Network Endpoints 0 localPort 5986
	data:    Network Endpoints 0 name "PowerShell"
	data:    Network Endpoints 0 port 5986
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"	
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 3389
	data:    Network Endpoints 1 name "Remote Desktop"
	data:    Network Endpoints 1 port 60173
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 1433
	data:    Network Endpoints 2 name "tcp-1433-1433"
	data:    Network Endpoints 2 port 1433
	data:    Network Endpoints 2 loadBalancerProbe port 1433
	data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
	data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
	data:    Network Endpoints 2 protocol "tcp"
	data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
	data:    Network Endpoints 2 enableDirectServerReturn false
	data:    Network Endpoints 2 loadBalancerName "ilbset"
	info:    vm show command OK


## Erstellen eines Remotedesktopendpunkts für einen virtuellen Computer

Sie können mithilfe von `azure vm endpoint create` einen Remotedesktop-Endpunkt erstellen, um für einen bestimmten virtuellen Computer Netzwerkdatenverkehr von einem öffentlichen Port an einen lokalen Port weiterzuleiten.

	azure vm endpoint create web1 54580 -k 3389 


## Entfernen eines virtuellen Computers aus einem Load Balancer

Sie können einen virtuellen Computer aus einer internen Load Balancer-Gruppe entfernen, indem Sie den zugehörigen Endpunkt löschen. Sobald der Endpunkt entfernt wurde, gehört der virtuelle Computer nicht mehr zu der Load Balancer-Gruppe.

 Anhand des oben genannten Beispiels können Sie mithilfe des Befehls `azure vm endpoint delete` den für den virtuellen Computer „DB1“ erstellten Endpunkt aus dem internen Load Balancer „ilbset“ entfernen.

	azure vm endpoint delete DB1 tcp-1433-1433


Weitere Informationen finden Sie unter `azure vm endpoint --help`.


## Nächste Schritte

[Konfigurieren eines Load Balancer-Verteilungsmodus mithilfe der Quell-IP-Affinität](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->