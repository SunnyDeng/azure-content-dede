<properties 
   pageTitle="Konfigurieren von ExpressRoute mit NSPs"
   description="Dieses Lernprogramm führt Sie durch die Einrichtung von ExpressRoute über NSPs."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/29/2015"
   ms.author="cherylmc"/>

#  Konfigurieren einer ExpressRoute-Verbindung über einen Netzwerkdienstanbieter

Um Ihre ExpressRoute-Verbindung über einen Netzwerkdienstanbieter zu konfigurieren, müssen Sie mehrere Schritte in der richtigen Reihenfolge ausführen. Diese Anweisungen helfen Ihnen bei Folgendem:

- Erstellen und Verwalten von ExpressRoute-Verbindungen
- Verknüpfen eines virtuelles Netzwerks mit der ExpressRoute-Verbindung

##  Konfigurationsvoraussetzungen


Bevor Sie mit der Konfiguration beginnen, vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind:

- Azure-Abonnement
- Aktuelle Version von Windows PowerShell
- Die folgenden Anforderungen hinsichtlich virtueller Netzwerke:
	- Eine Gruppe von IP-Adresspräfixen, die in virtuellen Netzwerken in Azure verwendet werden.
	- Eine Gruppe von lokalen IP-Präfixen (kann öffentliche IP-Adressen enthalten).
	- Das Gateway des virtuellen Netzwerks muss mit einem /28-Subnetz erstellt werden.
	- Zusätzliche Gruppe von IP-Präfixen (/28) außerhalb des virtuellen Netzwerks. Wird zum Konfigurieren von Routen verwendet. Sie müssen dies dem Netzwerkdienstanbieter angeben.
	- AS-Nummer für Ihr Netzwerk. Sie müssen dies dem Netzwerkdienstanbieter angeben. Sie können private AS-Nummern verwenden. Wenn Sie sich dafür entscheiden, muss die Nummer größer als 65000 sein. Weitere Informationen zu AS-Nummern finden Sie unter "Autonome Systemnummern (AS)". - 

- Vom Netzwerkdienstanbieter:
	- Sie müssen über einen VPN-Dienst verfügen, der mit ExpressRoute unterstützt wird. Erkundigen Sie sich bei Ihrem Netzwerkdienstanbieter, ob Ihr vorhandener VPN-Dienst qualifiziert ist.

##  Bereitstellungsworkflow

![Netzwerkdienstanbieter-Workflow](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  Konfigurieren von Einstellungen mithilfe von PowerShell
Windows PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen finden Sie in der PowerShell-Dokumentation in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).



1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **Rufen Sie die Liste der unterstützten Anbieter, Standorte und Bandbreiten ab.**

	Bevor Sie eine Verbindung herstellen, benötigen Sie eine Liste der Dienstanbieter sowie der unterstützten Standorte und Bandbreitenoptionen für jeden Standort. Verwenden Sie das folgende PowerShell-Cmdlet, um diese Informationen abzurufen, die Sie für einen späteren Schritt benötigen.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

	Die zurückgegebenen Informationen sehen ähnlich wie im folgenden Beispiel aus:

		PS C:\> Get-AzureDedicatedCircuitServiceProvider
	
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
		

3. **Fordern Sie einen Dienstschlüssel an übergeben Sie ihn an Ihren Netzwerkdienstanbieter.**

	Für diese Anforderung verwenden Sie ein PowerShell-Cmdlet. In diesem Beispiel verwenden Sie AT&T Netbond als Dienstanbieter und geben eine 50-Mbps-ExpressRoute-Verbindung in Silicon Valley an. Wenn Sie einen anderen Anbieter und andere Einstellungen verwenden, ersetzen Sie bei Ihrer Anforderung die entsprechenden Informationen.

	Nachfolgend sehen Sie eine Beispielanforderung für einen neuen Dienstschlüssel:

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	Die Antwort wird in etwa wie im folgendem Beispiel gezeigt lauten:

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Sie können diese Informationen jederzeit mithilfe des Cmdlets "Get-AzureCircuit" abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet. Der Dienstschlüssel wird im Feld "ServiceKey" aufgeführt.

		PS C:\> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Sobald dieser Schritt abgeschlossen ist, haben Sie eine Verbindung zum Azure-Speicher und zu anderen Diensten.



4. **Konfigurieren Sie das virtuelle Netzwerk und das Gateway.**

	Weitere Informationen finden Sie unter [Konfigurieren eines virtuellen Netzwerks und Gateways für ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx). Beachten Sie, dass das Gatewaysubnetz "/28" sein muss, um mit einer ExpressRoute-Verbindung arbeiten zu können.

5. **Verknüpfen Sie Ihr Netzwerk mit einer Verbindung.**

	Fahren Sie mit den nächsten Schritten erst fort, wenn Sie Folgendes bestätigt haben:
 
	- ServiceProviderState: Provisioned
	- Status: Enabled

	Stellen Sie sicher, dass Sie über mindestens ein virtuelles Azure-Netzwerk mit einem Gateway verfügen. Das Gateway muss ausgeführt werden.

		PS C:\> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=July15_HO4-->