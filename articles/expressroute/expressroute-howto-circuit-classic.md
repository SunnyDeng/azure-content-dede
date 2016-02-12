<properties
   pageTitle="Schritte zum Konfigurieren einer ExpressRoute-Verbindung mit PowerShell | Microsoft Azure"
   description="In diesem Artikel werden Sie durch die Schritte zum Erstellen und Bereitstellen einer ExpressRoute-Verbindung geführt. Außerdem wird veranschaulicht, wie Sie Status überprüfen, Updates durchführen oder die Verbindung löschen oder deren Bereitstellung aufheben."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Erstellen und Ändern einer ExpressRoute-Verbindung mit PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

In diesem Artikel werden Sie durch die Schritte zum Erstellen einer ExpressRoute-Verbindung mithilfe von PowerShell-Cmdlets und des klassischen Bereitstellungsmodells geführt. In den Schritten unten wird auch veranschaulicht, wie Sie den Status prüfen, ein Update durchführen oder eine ExpressRoute-Verbindung löschen oder deren Bereitstellung aufheben.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]


## Konfigurationsvoraussetzungen

- Sie benötigen die neueste Version der Azure PowerShell-Module. Sie können das neueste PowerShell-Modul aus dem PowerShell-Abschnitt der [Azure-Downloadseite](https://azure.microsoft.com/downloads/) herunterladen. Befolgen Sie die Anleitung auf der Seite [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Sie erhalten dort eine Schritt-für-Schritt-Anleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module. 
- Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md) gelesen haben.

## So können Sie eine ExpressRoute-Verbindung erstellen und bereitstellen

1. **Importieren Sie das PowerShell-Modul für ExpressRoute.**

 	Sie müssen die Azure- und ExpressRoute-Module in die PowerShell-Sitzung importieren, um die ExpressRoute-Cmdlets verwenden zu können. Führen Sie die folgenden Befehle zum Importieren der Azure- und ExpressRoute-Module in die PowerShell-Sitzung aus.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Rufen Sie die Liste der unterstützten Anbieter, Standorte und Bandbreiten ab.**

	Vor dem Erstellen einer ExpressRoute-Verbindung benötigen Sie die Liste der Konnektivitätsanbieter, unterstützten Standorte und Bandbreitenoptionen. Das PowerShell-Cmdlet *Get-AzureDedicatedCircuitServiceProvider* gibt diese Informationen zurück, die Sie in späteren Schritten noch verwenden werden. Wenn Sie das Cmdlet ausführen, ähnelt das Ergebnis dem folgenden Beispiel.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Erstellen Sie eine ExpressRoute-Verbindung.**

	Das folgende Beispiel zeigt, wie Sie eine ExpressRoute-Verbindung mit 200 MBit/s über Equinix im Silicon Valley herstellen können. Wenn Sie einen anderen Anbieter und andere Einstellungen verwenden, ersetzen Sie bei Ihrer Anforderung die entsprechenden Informationen.

	Nachfolgend sehen Sie eine Beispielanforderung für einen neuen Dienstschlüssel:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData 

	Verwenden Sie das unten angegebene Beispiel, falls Sie eine ExpressRoute-Verbindung mit dem Premium-Add-On erstellen möchten. Weitere Details zum Premium-Add-On finden Sie auf der Seite [ExpressRoute – FAQ](expressroute-faqs.md).

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData
	
	
	Die Antwort enthält den Dienstschlüssel. Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie Folgendes ausführen:

		get-help new-azurededicatedcircuit -detailed 

4. **Listen Sie alle ExpressRoute-Verbindungen auf.**

	Sie können den Befehl *Get-AzureDedicatedCircuit* ausführen, um eine Liste mit allen ExpressRoute-Verbindungen abzurufen, die Sie erstellt haben.

		#Getting service key
		Get-AzureDedicatedCircuit

	Die Antwort wird in etwa wie im folgendem Beispiel gezeigt lauten:

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Sie können diese Informationen jederzeit mithilfe des *Get-AzureDedicatedCircuit*-Cmdlets abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet. Der Dienstschlüssel wird im Feld *ServiceKey* aufgeführt.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie Folgendes ausführen:

		get-help get-azurededicatedcircuit -detailed 

5. **Senden Sie den Dienstschlüssel zur Bereitstellung an Ihren Konnektivitätsanbieter.**

	Wenn Sie eine neue ExpressRoute-Verbindung erstellen, hat die Verbindung folgenden Zustand:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	*ServiceProviderProvisioningState* enthält Informationen zum aktuellen Status der Bereitstellung auf Service Provider-Seite, und mit "Status" wird der Status auf Microsoft-Seite angegeben. Eine ExpressRoute-Verbindung muss sich im folgenden Zustand befinden, damit Sie sie verwenden können.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	Die Verbindung wird in den folgenden Zustand versetzt, wenn sie vom Konnektivitätsanbieter für Sie aktiviert wird.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Überprüfen Sie regelmäßig den Status und Zustand des Verbindungsschlüssels.**

	So sind Sie informiert, wenn Ihr Anbieter die Verbindung aktiviert hat. Sobald die Verbindung konfiguriert wurde, zeigt *ServiceProviderProvisioningState* wie im folgenden Beispiel den Status *Provisioned* an.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **Erstellen Sie die Routingkonfiguration.**
	
	Eine Schritt-für-Schritt-Anleitung finden Sie auf der Seite [Routingkonfiguration für ExpressRoute-Verbindung (Erstellen und Ändern von Verbindungspeerings)](expressroute-howto-routing-classic.md).

7. **Verknüpfen Sie ein VNet mit einer ExpressRoute-Verbindung.**

	Verknüpfen Sie als Nächstes ein VNet mit der ExpressRoute-Verbindung. Eine schrittweise Anleitung finden Sie unter [Verknüpfen von ExpressRoute-Verbindungen mit VNETs](expressroute-howto-linkvnet-classic.md). Wenn Sie ein virtuelles Netzwerk für ExpressRoute mithilfe des klassischen Bereitstellungsmodell erstellen möchten, hilft Ihnen die Anleitung unter [Erstellen eines virtuellen Netzwerks für ExpressRoute](expressroute-howto-vnet-portal-classic.md) weiter.

##  So rufen Sie den Status einer ExpressRoute-Verbindung ab

Sie können diese Informationen jederzeit mithilfe des Cmdlets *Get-AzureCircuit* abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Sie können Informationen zu einer bestimmten ExpressRoute-Verbindung erhalten, indem Sie den Dienstschlüssel als Parameter an den Aufruf übergeben.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie Folgendes ausführen:

		get-help get-azurededicatedcircuit -detailed 

##  So ändern Sie eine ExpressRoute-Verbindung

Sie können bestimmte Eigenschaften einer ExpressRoute-Verbindung ändern, ohne die Konnektivität zu beeinträchtigen.

Sie können Folgendes durchführen:

- Aktivieren/Deaktivieren eines ExpressRoute Premium-Add-Ons für Ihre ExpressRoute-Verbindung ohne Ausfallzeit
- Erhöhen der Bandbreite Ihrer ExpressRoute-Verbindung ohne Ausfallzeit

Weitere Informationen zu Beschränkungen und Grenzwerten finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md).

### Aktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet aktivieren:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

Für Ihre Verbindung sind nun die Features des ExpressRoute Premium-Add-Ons aktiviert. Beachten Sie, dass die Gebühren für das Premium-Add-On ab der erfolgreichen Ausführung des Befehls erhoben werden.

### Deaktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet deaktivieren:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Das Premium-Add-On ist jetzt für Ihre Verbindung deaktiviert.

>[AZURE.IMPORTANT] Dieser Vorgang kann fehlschlagen, wenn Sie Ressourcen verwenden, die die zulässige Menge für die Standardverbindung überschreiten.
>
>- Sie müssen sicherstellen, dass die Anzahl von virtuellen Netzwerken, die mit der Verbindung verknüpft sind, kleiner als zehn ist, bevor Sie ein Downgrade von Premium auf Standard durchführen. Wenn Sie dies nicht beachten, tritt für die Updateanforderung ein Fehler auf, und Ihnen werden die Premium-Gebühren berechnet.
- Sie müssen die Verknüpfung für alle virtuellen Netzwerke in anderen geopolitischen Regionen aufheben. Wenn Sie dies nicht beachten, tritt für die Updateanforderung ein Fehler auf, und Ihnen werden die Premium-Gebühren berechnet.
- Ihre Routentabelle muss für das private Peering weniger als 4.000 Routen aufweisen. Wenn Ihre Routentabelle mehr als 4.000 Routen umfasst, wird die BGP-Sitzung verworfen. Eine erneute Aktivierung ist dann erst wieder möglich, wenn die Anzahl der angekündigten Präfixe unter 4.000 fällt.


### Aktualisieren der Bandbreite für die ExpressRoute-Verbindung

Informationen zu unterstützten Bandbreitenoptionen für Ihren Anbieter finden Sie auf der Seite [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md). Sie können jede Größe auswählen, die die Größe der vorhandenen Verbindung übersteigt. Sobald Sie sich für die benötigte Größe entschieden haben, können Sie den folgenden Befehl verwenden, um die Größe der Verbindung anzupassen.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

Die Größe Ihrer Verbindung wurde bereits auf der Microsoft-Seite angepasst. Sie müssen sich an Ihren Konnektivitätsanbieter wenden, um die entsprechende Aktualisierung der Konfigurationen auf Anbieterseite vornehmen zu lassen. Beachten Sie, dass wir Ihnen ab diesem Zeitpunkt die aktualisierte Bandbreitenoption in Rechnung stellen.

>[AZURE.IMPORTANT] Es ist nicht möglich, die Bandbreite einer ExpressRoute-Verbindung ohne Störungen zu reduzieren. Ein Downgrade der Bandbreite erfordert, dass Sie die Bereitstellung der ExpressRoute-Verbindung aufheben und dann eine neue ExpressRoute-Verbindung bereitstellen.

##  So löschen Sie eine ExpressRoute-Verbindung und heben die Bereitstellung auf

Sie können die ExpressRoute-Verbindung löschen, indem Sie den folgenden Befehl ausführen:

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

Beachten Sie, dass Sie die Verknüpfung aller virtuellen Netzwerke für die ExpressRoute aufheben müssen, damit dieser Vorgang erfolgreich ist. Überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind, falls dieser Vorgang nicht erfolgreich ist.

Wenn der Service Provider-Bereitstellungsstatus für die ExpressRoute-Verbindung aktiviert ist, wechselt der Status von "enabled" in *disabling*. Arbeiten Sie mit Ihrem Service Provider zusammen, um die Bereitstellung der Verbindung auf Anbieterseite aufzuheben. Wir reservieren weiterhin Ressourcen für Sie und stellen Ihnen dies in Rechnung, bis der Service Provider die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns eine Benachrichtigung sendet.

Wenn der Service Provider die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungsstatus des Service Providers lautet *not provisioned*), bevor Sie das obige Cmdlet ausführen, führen wir die Aufhebung der Verbindungsbereitstellung durch und stellen Ihnen keine Gebühren mehr in Rechnung.

## Nächste Schritte

- [Konfigurieren des Routings](expressroute-howto-routing-classic.md)

<!---HONumber=AcomDC_0204_2016-->