<properties 
   pageTitle="Erstellen von NSGs im klassischen Modus über die Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie NSGs im klassischen Modus mithilfe der Azure-Befehlszeilenschnittstelle erstellen und bereitstellen."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/15/2015"
   ms.author="telmos" />

# Erstellen von NSGs (klassisch) in der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können [NSGs auch im Ressourcen-Manager-Bereitstellungsmodell erstellen](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Die folgenden Beispielbefehle für die Azure-Befehlszeilenschnittstelle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem obigen Szenario basiert. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, indem Sie [ein VNet erstellen](virtual-networks-create-vnet-classic-cli).

## Erstellen der NSG für das Front-End-Subnetz
Führen Sie zum Erstellen einer NSG mit dem Namen *NSG-FrontEnd* basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus:

1. Wenn Sie die Azure-Befehlszeilenschnittstelle zuvor noch nicht verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

2. Führen Sie den Befehl **azure config mode** aus, um in den klassischen Modus zu wechseln, wie unten dargestellt.

		azure config mode asm

	Erwartete Ausgabe:

		info:    New mode is asm

3. Führen Sie den Befehl **azure network nsg create** aus, um eine NSG zu erstellen.

		azure network nsg create -l uswest -n NSG-FrontEnd

	Erwartete Ausgabe:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : NSG-FrontEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Parameter:

	- **-l (oder --location)**. Azure-Region, in der die neue NSG erstellt wird. In diesem Szenario *westus*.
	- **-n (oder --name)**. Name der neuen NSG. In diesem Szenario *NSG-FrontEnd*.

4. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf Port 3389 (RDP) über das Internet zulässt.

		azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : rdp-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 3389
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Parameter:

	- **-a (oder --nsg-name)**. Name der NSG, in der die Regel erstellt wird. In diesem Szenario *NSG-FrontEnd*.
	- **-n (oder --name)**. Name der neuen Regel. In diesem Szenario *rdp-rule*.
	- **-c (oder --action)**. Zugriffsebene für die Regel (Deny oder Allow).
	- **-p (oder --protocol)**. Protokoll (Tcp, Udp oder *) für die Regel.
	- **-r (oder --type)**. Richtung der Verbindung (Inbound oder Outbound).
	- **-y (oder --priority)**. Priorität für die Regel.
	- **-f (oder --source-address-prefix)**. Quelladresspräfix in CIDR oder Verwendung von Standardtags.
	- **-o (oder --source-port-range)**. Quellport oder Portbereich.
	- **-e (oder --destination-address-prefix)**. Zieladresspräfix in CIDR oder Verwendung von Standardtags.
	- **-u (oder --destination-port-range)**. Zielport oder Portbereich.	

5. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf Port 80 (HTTP) über das Internet zulässt.

		azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Führen Sie den Befehl **azure network nsg subnet add** aus, um die NSG mit dem Front-End-Subnetz zu verbinden.

		azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd 

	Erwartete Ausgabe:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-FrontEnd"
		info:    network nsg subnet add command OK

## Erstellen der NSG für das Back-End-Subnetz
Führen Sie zum Erstellen einer NSG mit dem Namen *NSG-BackEnd* basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

3. Führen Sie den Befehl **azure network nsg create** aus, um eine NSG zu erstellen.

		azure network nsg create -l uswest -n NSG-BackEnd

	Erwartete Ausgabe:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : NSG-BackEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Parameter:

	- **-l (oder --location)**. Azure-Region, in der die neue NSG erstellt wird. In diesem Szenario *westus*.
	- **-n (oder --name)**. Name der neuen NSG. In diesem Szenario *NSG-FrontEnd*.

4. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf Port 1433 (SQL) über das Front-End-Subnetz zulässt.

		azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : sql-rule
		data:    Source address prefix           : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 1433
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK


5. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf das Internet verweigert.

		azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : *
		data:    Source Port                     : *
		data:    Destination address prefix      : INTERNET
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Outbound
		data:    Action                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Führen Sie den Befehl **azure network nsg subnet add** aus, um die NSG mit dem Back-End-Subnetz zu verbinden.

		azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd 

	Erwartete Ausgabe:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-BackEndX"
		info:    Looking up the subnet "BackEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-BackEndX"
		info:    network nsg subnet add command OK

<!----HONumber=Sept15_HO4-->