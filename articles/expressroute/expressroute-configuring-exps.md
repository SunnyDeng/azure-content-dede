<properties
   pageTitle="Konfigurieren von ExpressRoute über einen Exchange-Anbieter"
   description="Dieses Lernprogramm führt Sie durch die Einrichtung von ExpressRoute über Exchange-Anbieter (EXPs)."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2015"
   ms.author="cherylmc"/>

#  Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter

Um Ihre ExpressRoute-Verbindung über einen Exchange-Anbieter zu konfigurieren, müssen Sie mehrere Schritte in der richtigen Reihenfolge ausführen. Diese Anweisungen helfen Ihnen dabei, die folgenden Aktionen auszuführen:

- Erstellen und Verwalten von ExpressRoute-Verbindungen
- Konfigurieren von BGP-Peering für ExpressRoute-Verbindungen
- Verknüpfen eines virtuelles Netzwerks mit der ExpressRoute-Verbindung

##  Konfigurationsvoraussetzungen


Bevor Sie mit der Konfiguration beginnen, vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind:

- Azure-Abonnement
- Aktuelle Version von Azure PowerShell
- Die folgenden Anforderungen hinsichtlich virtueller Netzwerke:
	- Eine Gruppe von IP-Adresspräfixen, die in virtuellen Netzwerken in Azure verwendet werden
	- Eine Gruppe von lokalen IP-Präfixen (kann öffentliche IP-Adressen enthalten)
	- Das Gateway des virtuellen Netzwerks muss mit einem /28-Subnetz erstellt werden.
	- Zusätzliche Gruppe von IP-Präfixen (/28) außerhalb des virtuellen Netzwerks. Dies wird zum Konfigurieren von BGP-Peering verwendet.
	- AS-Nummer für Ihr Netzwerk. Weitere Informationen zu AS-Nummern finden Sie unter [Autonome Systemnummern (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- MD5-Hash, wenn Sie eine authentifizierte BGP-Sitzung benötigen
	- VLAN-IDs, über die der Datenverkehr gesendet wird. Sie benötigen 2 VLAN-IDs pro Verbindung: eine für virtuelle Netzwerke und die andere für Dienste, die unter öffentlichen IP-Adressen gehostet werden.
	- [Autonome Systemnummern (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml) für Ihr Netzwerk.
	- Zwei 1-Gbit/s- bzw. 10-Gbit/s-Querverbindungen mit dem Ethernet-Exchange des Exchange-Anbieters.
	- Ein Routerpaar, das für das Routing BGP unterstützen kann

##  Bereitstellungsworkflow


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  Konfigurieren von Einstellungen mithilfe von PowerShell

Windows PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen finden Sie in der PowerShell-Dokumentation in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Rufen Sie die Liste der unterstützten Anbieter, Standorte und Bandbreiten ab.**

	Bevor Sie eine Verbindung herstellen, benötigen Sie eine Liste der Dienstanbieter sowie der unterstützten Standorte und Bandbreitenoptionen für jeden Standort. Verwenden Sie das folgende PowerShell-Cmdlet, um diese Informationen abzurufen, die Sie für einen späteren Schritt benötigen.

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**


		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths
		----                 -------------------------      --------------------------
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		                     as,New York,Seattle,Silicon
		                     Valley,Washington
		                     DC,London,Hong
		                     Kong,Singapore,Sydney,Tokyo
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		Exchange
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		IPVPN
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		International
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000

3. **Erstellen Sie eine dedizierte Verbindung.**

	Das folgende Beispiel zeigt, wie Sie eine 200-Mbit/s-ExpressRoute-Verbindung über Equinix Silicon Valley herstellen können. Wenn Sie einen anderen Anbieter und andere Einstellungen verwenden, ersetzen Sie bei Ihrer Anforderung die entsprechenden Informationen.

	Nachfolgend sehen Sie eine Beispielanforderung für einen neuen Dienstschlüssel:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location

		#Getting service key
		Get-AzureDedicatedCircuit

	Die Antwort wird in etwa wie im folgendem Beispiel gezeigt lauten:

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Sie können diese Informationen jederzeit mithilfe des Cmdlets "Get-AzureCircuit" abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet. Der Dienstschlüssel wird im Feld "ServiceKey" aufgeführt.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Senden Sie den Dienstschlüssel an Ihren Exchange-Anbieter.**

	Der Exchange-Anbieter verwendet den Dienstschlüssel, um die Verbindung an seinem Ende zu aktivieren. Sie müssen dem Konnektivitätsanbieter zusätzliche Informationen bereitstellen, um die Verbindung zu aktivieren.

5. **Überprüfen Sie regelmäßig den Status und Zustands des Verbindungsschlüssels.**

	Dadurch wissen Sie, wann Ihr Anbieter die Verbindung aktiviert hat. Sobald die Verbindung aktiviert wurde, zeigt *ServiceProviderProvisioningState* wie im folgenden Beispiel den Status *Provisioned* an.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

6. **Konfigurieren Sie das Routing für das virtuelle Netzwerk.**

	Mithilfe von BGP-Sitzungen können Routen getauscht werden. Zudem können Sie die hohe Verfügbarkeit sicherstellen. Verwenden Sie das folgende Beispiel, um eine BGP-Sitzung für Ihre Verbindung zu erstellen. Ersetzen Sie beim Erstellen der Sitzung die Beispielwerte durch Ihre eigenen Werte.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	Durch die Angabe des Dienstschlüssels erhalten Sie mithilfe von Get-AzureBGPPeering die Routinginformationen für eine Verbindung. Sie können die BGP-Einstellungen auch mithilfe von Set-AzureBGPPeering aktualisieren. Die BGP-Sitzung wird nicht gestartet, wenn dieser Befehl ausgeführt wird. Die Verbindung muss mit mindestens einem VNet verknüpft sein, damit die BGP-Sitzung gestartet werden kann.

	Die unten stehende Antwort enthält die Informationen, die Sie für die nächsten Schritte benötigen. Verwenden Sie die Peer-ASN, um für Ihre Router-VRFs BGP zu konfigurieren.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **Konfigurieren Sie das Routing für Dienste, die unter öffentlichen IP-Adressen gehostet werden.**

	Mithilfe von BGP-Sitzungen können Routen getauscht werden. Zudem können Sie die hohe Verfügbarkeit sicherstellen. Verwenden Sie das folgende Beispiel, um eine BGP-Sitzung für Ihre Verbindung zu erstellen. Ersetzen Sie beim Erstellen der Sitzung die Beispielwerte durch Ihre eigenen Werte.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public

	Durch die Angabe des Dienstschlüssels erhalten Sie mithilfe von Get-AzureBGPPeering die Routinginformationen für eine Verbindung. Sie können die BGP-Einstellungen auch mithilfe von Set-AzureBGPPeering aktualisieren. Die BGP-Sitzung wird nicht gestartet, wenn dieser Befehl ausgeführt wird. Die Verbindung muss mit mindestens einem VNet verknüpft sein, damit die BGP-Sitzung gestartet werden kann.

	Die unten stehende Antwort enthält die Informationen, die Sie für die nächsten Schritte benötigen. Verwenden Sie die Peer-ASN, um für Ihre Router-VRFs BGP zu konfigurieren.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101

8. **Konfigurieren Sie das virtuelle Netzwerk und das Gateway.**

	Weitere Informationen finden Sie unter [Konfigurieren eines virtuellen Netzwerks und Gateways für ExpressRoute](expressroute-configuring-vnet-gateway.md). Beachten Sie, dass das Gatewaysubnetz "/28" sein muss, um mit einer ExpressRoute-Verbindung arbeiten zu können.

9. **Verknüpfen Sie Ihr Netzwerk mit einer Verbindung.** Führen Sie die folgenden Anweisungen erst aus, nachdem Sie sichergestellt haben, dass Ihre Verbindung den folgenden Zustand und Status hat:
	- ServiceProviderProvisioningState: Provisioned
	- Status: Enabled

			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
 
## Nächste Schritte

- Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!---HONumber=Oct15_HO2-->