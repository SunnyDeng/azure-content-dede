<properties 
   pageTitle="Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten"
   description="Weitere Informationen zum Erstellen und Konfigurieren von virtuellen Computern mit mehreren Netzwerkkarten"
   services="virtual-network, virtual-machines"
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
   ms.date="08/02/2015"
   ms.author="telmos" />

# Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten

Mit dem Multi-NIC-Fueature können Sie mehrere virtuelle Netzwerkschnittstellenkarten (NIC) in Ihren virtuellen Azure-Computern (VMs) erstellen und verwalten. Viele virtuelle Netzwerkanwendungen, z. B. Lösungen für die Anwendungszustellung und WAN-Optimierung, erfordern mehrere NICs. Das Multi-NIC-Feature bietet zudem weitere Funktionen zur Verwaltung des Netzwerkdatenverkehrs, darunter die Isolation des Datenverkehrs zwischen einer Front-End-NIC und Back-End-NIC(s) oder die Trennung des Datenverkehrs auf der Datenverkehrsebene vom Datenverkehr auf der Verwaltungsebene.

![Multi-NIC für virtuelle Computer](./media/virtual-networks-multiple-nics/IC757773.png)

Die obige Abbildung zeigt einen virtuellen Computer mit drei Netzwerkkarten, die jeweils mit einem anderen Subnetz verbunden sind.

## Anforderungen und Einschränkungen

Derzeit gelten für Multi-NIC die folgenden Anforderungen und Einschränkungen:

- Multi-NIC-VMs müssen in virtuellen Azure-Netzwerken (VNets) erstellt werden. Virtuelle Computer, die nicht in ein virtuelles Netzwerk eingebunden sind, werden nicht unterstützt. 
- In einem gegebenen Clouddienst sind nur die folgenden Einstellungen zulässig: 
	- Alle virtuellen Computer in diesem Clouddienst müssen Multi-NIC-aktiviert sein, oder 
	- alle virtuellen Computer in diesem Clouddienst müssen jeweils eine einzelne NIC haben. 

>[AZURE.IMPORTANT]Wenn Sie versuchen, eine Multi-NIC-VM zu einer Bereitstellung (Clouddienst) hinzuzufügen, die bereits eine VM mit einer NIC enthält (oder umgekehrt eine VM mit einer NIC zu einer Bereitstellung mit Multi-NIC-VMs), dann wird ein Fehler angezeigt, der besagt, dass virtuelle Computer mit sekundären Netzwerkschnittstellen und virtuelle Computer ohne sekundäre Netzwerkschnittstellen nicht in der gleichen Bereitstellung unterstützt werden und dass zudem ein virtueller Computer ohne sekundäre Netzwerkschnittstelle nicht aktualisiert werden kann, um sekundäre Netzwerkschnittstellen hinzuzufügen, und umgekehrt.
 
- Die Internet-VIP wird nur bei der Standard-NIC unterstützt. Es gibt nur eine VIP-Adresse, die an die IP-Adresse der Standard-NIC gebunden ist. 
- Zu diesem Zeitpunkt werden öffentliche IP-Adressen auf Instanzebene bei Multi-NIC-VMs nicht unterstützt. 
- Die interne Reihenfolge der Netzwerkkarten im virtuellen Computer ist zufällig und kann sich auch zwischen Azure-Infrastrukturaktualisierungen ändern. Die IP-Adressen und die zugehörigen Ethernet-MAC-Adressen ändern sich jedoch nicht. Nehmen wir beispielsweise an **Eth1** hat die IP-Adresse 10.1.0.100 und die MAC-Adresse 00-0D-3A-B0-39-0D. Nach einer Azure-Infrastrukturaktualisierung und einem Neustart kann Eth1 in Eth2 geändert worden sein, aber die zugehörige IP-Adresse und die MAC-Adresse bleiben gleich. Wenn der Neustart vom Benutzer initiiert wird, bleibt die NIC-Reihenfolge unverändert. 
- Die Adressen der einzelnen NICs in den einzelnen virtuellen Computer müssen sich in einem Subnetz befinden, mehrere NICs in einem virtuellen Computer können Adressen zugewiesen werden, die sich im gleichen Subnetz befinden. 
- Größe des virtuellen Computers bestimmt die Anzahl der Netzwerkkarten, die Sie für einen virtuellen Computer erstellen können. Die folgende Tabelle enthält die Anzahl der Netzwerkkarten für die verschiedenen Größen virtueller Computer: 

|VM-Größe (Standard-SKUs)|NICs (maximal zulässige Anzahl pro VM)|
|---|---|
|Alle Basic-Größen|1|
|A0\\sehr klein|1|
|A1\\klein|1|
|A2\\mittel|1|
|A3\\groß|2|
|A4\\sehr groß|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|Alle anderen Größen|1|

## Netzwerksicherheitsgruppen
Jede NIC in einem virtuellen Computer kann einer Netzwerksicherheitsgruppe (NSG) zugeordnet werden, auch die NICs eines virtuellen Computers, bei dem Multi-NIC aktiviert wurde. Wenn einer NIC eine Adresse innerhalb eines Subnetzes, das einer NSG zugeordnet ist, zugewiesen wird, dann gelten die Regeln der NSG des Subnetzes auch für diese NIC. Sie können nicht nur Subnetze, sondern auch NICs Netzwerksicherheitsgruppen zuordnen.

Wenn ein Subnetz einer NSG zugeordnet ist und eine NIC in diesem Subnetz einzeln einer NSG zugeordnet wird, dann werden die zugehörigen NSG-Regeln in der "**Reihenfolge der Flussrichtung**" entsprechend der Richtung des Datenverkehrs angewendet, der in die oder aus der NIC übertragen wird:

- ****Eingehender Datenverkehr**, dessen Ziel die fragliche Netzwerkkarte ist, durchläuft zunächst das Subnetz, wobei die NSG-Regeln des Subnetzes angewendet werden, bevor er an die Netzwerkkarte übergeben wird, wodurch die NSG-Regeln der NIC zum Tragen kommen.
- **Ausgehender Datenverkehr**, dessen Quelle die fragliche Netzwerkkarte ist, durchläuft zunächst die NIC, wobei die NSG-Regeln der NIC angewendet werden, bevor er an das Subnetz übergeben wird, wodurch die NSG-Regeln des Subnetzes zum Tragen kommen. 

Die obige Abbildung stellt dar, wie die NSG-Regeln anhand der Datenflussrichtung (vom virtuellen Computer zum Subnetz oder vom Subnetz zum virtuellen Computer) angewendet werden.

## Konfigurieren eines virtuellen Multi-NIC-Computers

Mithilfe der folgenden Anweisungen können Sie einen virtuellen Multi-NIC-Computer mit 3 NICs erstellen: einer Standard-NIC und zwei zusätzlichen NICs. Mit den Konfigurationsschritten wird ein virtueller Computer erstellt, der gemäß dem folgenden Ausschnitt aus der Dienstkonfigurationsdatei konfiguriert wird:

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


Um die PowerShell-Befehle in diesem Beispiel ausführen zu können, müssen die folgenden Voraussetzungen erfüllt sein.

- Ein Azure-Abonnement.
- Ein konfiguriertes virtuelles Netzwerk. Weitere Informationen zu virtuellen Netzwerken finden Sie unter [Virtuelle Netzwerke im Überblick](https://msdn.microsoft.com/library/azure/jj156007.aspx).
- Die neueste Version von Azure PowerShell wurde heruntergeladen und installiert. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell).

Führen Sie die folgenden Schritte aus, um einen virtuellen Computer mit mehreren Netzwerkkarten zu erstellen

1. Wählen Sie ein VM-Image aus der Azure-VM-Image-Galerie aus. Beachten Sie, dass sich die Images häufig ändern und nach Region verfügbar sind. Das im folgenden Beispiel angegebene Image kann sich möglicherweise ändern oder nicht in Ihrer Region verfügbar sein. Geben Sie daher unbedingt das Image an, das Sie benötigen. 
	    
		$image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Erstellen Sie eine Konfiguration für den virtuellen Computer.

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Erstellen Sie die Standardadministratoranmeldung.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Fügen Sie der Konfiguration des virtuellen Computers zusätzliche Netzwerkkarten hinzu.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Geben Sie das Subnetz und die IP-Adresse für die Standard-NIC an.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
		Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Erstellen Sie den virtuellen Computer in Ihrem virtuellen Netzwerk.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]Das virtuelle Netzwerk, das Sie hier angeben, muss bereits vorhanden sein (wie unter den Voraussetzungen angegeben). Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen **MultiNIC-VNet** angegeben.

## Sekundärer Netzwerkkartenzugriff auf andere Subnetze

Das aktuelle Modell in Azure sieht vor, dass alle Netzwerkkarten auf einem virtuellen Computer mit einem Standardgateway eingerichtet wurden. Dadurch können die Netzwerkkarten mit IP-Adressen außerhalb ihres Subnetzes kommunizieren. Bei Betriebssystemen, die das schwache Hostroutingmodell verwenden, z. B. Linux, werden Internetverbindungen unterbrochen, wenn eingehender und ausgehender Datenverkehr unterschiedliche Netzwerkkarten verwenden.

Um dieses Problem zu beheben, wird Anfang Juli 2015 ein Update für die Azure-Plattform eingeführt, mit dem das Standardgateway aus den sekundären Netzwerkkarten entfernt wird. Dies hat keine Auswirkungen auf vorhandene virtuelle Computer, solange diese nicht neu gestartet werden. Nach dem Neustart werden die neuen Einstellungen wirksam und der Datenverkehrsfluss für die sekundären Netzwerkkarten werden auf das gleiche Subnetz beschränkt. Wenn die Benutzer die Kommunikation der sekundäre Netzwerkkarten mit Adressen außerhalb ihres Subnetzes zulassen möchten, müssen sie, wie unten beschrieben, der Routingtabelle einen Eintrag hinzufügen, mit dem das Gateway konfiguriert wird.

### Konfigurieren von virtuellen Windows-Computern

Angenommen, Sie haben einen virtuellen Windows-Computer mit zwei Netzwerkkarten, der wie folgt konfiguriert ist:

- IP-Adresse der primären Netzwerkkarte: 192.168.1.4
- IP-Adresse der sekundären Netzwerkkarte: 192.168.2.5

Die IPv4-Routingtabelle für diesen virtuellen Computer sieht wie folgt aus:

	IPv4 Route Table
	===========================================================================
	Active Routes:
	Network Destination        Netmask          Gateway       Interface  Metric
	          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
	        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
	        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
	  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	    168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
	      192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
	      192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
	    192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
	      192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
	      192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
	    192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
	        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
	        224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
	        224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
	  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	  255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
	  255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
	===========================================================================

Beachten Sie, dass die Standardroute (0.0.0.0) nur für die primäre Netzwerkkarte verfügbar ist. Sie können nicht auf Ressourcen außerhalb des Subnetzes für die sekundäre Netzwerkkarte zugreifen, wie unten dargestellt:

	C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
	 
	Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.

Führen Sie die folgenden Schritte aus, um eine Standardroute für die sekundäre Netzwerkkarte hinzuzufügen:

1. Führen Sie an einer Eingabeaufforderung den folgenden Befehl aus, um die Indexnummer für die sekundäre Netzwerkkarte zu bestimmen:

		C:\Users\Administrator>route print
		===========================================================================
		Interface List
		 29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
		 27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
		  1...........................Software Loopback Interface 1
		 14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
		 20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
		===========================================================================

2. Beachten Sie den zweiten Eintrag in der Tabelle, der in diesem Beispiel den Index 27 aufweist.
3. Führen Sie an einer Eingabeaufforderung den Befehl **route add** aus, wie unten dargestellt. In diesem Beispiel geben Sie 192.168.2.1 als Standardgateway für die sekundäre Netzwerkkarte an:

		route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Zum Testen der Verbindung führen Sie an der Eingabeaufforderung von der sekundären Netzwerkkarte aus Ping-Befehle für die verschiedenen Subnetze aus, wie im Beispiel unten dargestellt:

		C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
		 
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Sie können die neu hinzugefügte Route auch in der Routingtabelle überprüfen, wie unten dargestellt:

		C:\Users\Administrator>route print

		...

		IPv4 Route Table
		===========================================================================
		Active Routes:
		Network Destination        Netmask          Gateway       Interface  Metric
		          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
		          0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
		        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Konfigurieren von virtuellen Linux-Computern

Da virtuelle Linux-Computer im Standardverhalten schwaches Hostrouting verwenden, wird empfohlen, die sekundäre Netzwerkkarte auf Datenverkehrsflüsse im gleichen Subnetz zu beschränken. Wenn es jedoch in bestimmten Szenarien erforderlich ist, Verbindungen mit Adressen außerhalb des Subnetzes herzustellen, sollten Benutzer das richtlinienbasierte Routing aktivieren, um sicherzustellen, dass für eingehenden und ausgehenden Datenverkehr dieselbe Netzwerkkarte verwendet wird.

<!---HONumber=August15_HO6-->