<properties 
   pageTitle="Konfigurieren des Lastenausgleichs-Verteilungsmodus | Microsoft Azure"
   description="Konfigurieren des Verteilungsmodus für den Azure Load Balancer zur Unterstützung von Quell-IP-Affinität"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2015"
   ms.author="joaoma" />



# Load Balancer-Verteilungsmodus (Quell-IP-Affinität)

Wir haben einen neue Verteilungsmodus namens Quell-IP-Affinität (auch Sitzungsaffinität oder Client-IP-Affinität genannt) eingeführt. Der Azure-Lastenausgleich kann für die Verwendung von 2 Tupeln (Quell-IP, Ziel-IP) oder 3 Tupeln (Quell-IP, Ziel-IP, Protokoll) konfiguriert werden, um Datenverkehr den verfügbaren Servern zuzuordnen. Mithilfe der Quell-IP-Affinität werden Verbindungen, die vom gleichen Clientcomputer initiiert werden, an den gleichen DIP-Endpunkt geleitet.

![Lastenausgleich auf Hashbasis](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Mit der Quell-IP-Affinität wird das Problem der Inkompatibilität zwischen dem Azure-Lastenausgleich und dem RD-Gateway gelöst. Jetzt können Sie eine RD-Gatewayfarm in einem einzelnen Clouddienst erstellen. Ein anderes Verwendungsszenario ist das Hochladen von Medien, wobei der tatsächliche Datenhochladevorgang über UDP erfolgt, während die Steuerungsebene über TCP erreicht wird:

- Ein Client initiiert zunächst eine TCP-Sitzung mit der öffentlichen Adresse mit Lastenausgleich, wird dann an eine bestimmte DIP-Adresse geleitet, und dieser Kanal bleibt zur Überwachung des Zustands der Verbindung aktiv.
- Eine neue UDP-Sitzung des gleichen Clientcomputers wird auf dem gleichen öffentlichen Endpunkt mit Lastenausgleich initiiert. Es wird hier davon ausgegangen, dass diese Verbindung auch an den gleichen DIP-Endpunkt geleitet wird wie die vorherige TCP-Verbindung, sodass der Medienupload mit hohem Durchsatz ausgeführt werden kann, während ein Steuerungskanal über TCP erhalten bleibt.
 
Beachten Sie, dass bei Änderungen der Lastenausgleichsgruppe (Entfernen oder Hinzufügen eines virtuellen Computers) die Verteilung der Clientanforderungen neu berechnet wird. Sie können sich nicht auf neue Verbindungen von vorhandenen Clientsitzungen verlassen, die auf demselben Server enden. Darüber hinaus kann die Verwendung des Quell-IP-Affinitäts-Verteilungsmodus zu einer ungleichen Verteilung des Datenverkehrs führen. Clients, die hinter Proxys ausgeführt werden, können als eine einzige Clientanwendung betrachtet werden.

Standardmäßig wird ein 5-Tupel-Hash-Verteilungsalgorithmus (Quell-IP, Quellport, IP-Zieladresse, Zielport, Protokolltyp) zum Zuordnen des Datenverkehrs an verfügbare Server verwendet. Dabei wird Bindung nur in einer Transportsitzung angeboten. Pakete in derselben TCP- oder UDP-Sitzung werden an die gleiche Instanz der Rechenzentrums-IP (DIP) hinter dem Lastenausgleichs-Endpunkt geleitet. Wenn der Client die Verbindung schließt und erneut öffnet oder eine neue Sitzung über die gleiche Quell-IP startet, wird der Quellport geändert, sodass der Datenverkehr an einen anderen DIP-Endpunkt geleitet wird.

![Lastenausgleich auf Hashbasis](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## Konfigurieren von Quell-IP-Affinitätseinstellungen für Lastenausgleich
 
Für virtuelle Computer können Sie per PowerShell die Timeouteinstellungen ändern:
 
Fügen Sie einem virtuellen Computer einen Azure-Endpunkt hinzu, und legen Sie den Lastenausgleichs-Verteilungsmodus fest.

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution “sourceIP”| Update-AzureVM

>[AZURE.NOTE]"LoadBalancerDistribution" kann für eine 2-Tupel-Konfiguration (Quell-IP, Ziel-IP) auf "sourceIP", für eine 3-Tupel-Konfiguration (Quell-IP, Ziel-IP, Protokoll) auf "sourceIPProtocol" oder gar nicht festgelegt werden, um das Standardverhalten (5-Tupel-Lastenausgleich) zu verwenden.


Abrufen einer Lastenausgleichs-Verteilungsmoduskonfiguration für einen Endpunkt

	PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint

	VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
	LBSetName : MyLoadBalancedSet
	LocalPort : 80
	Name : HTTP
	Port : 80
	Protocol : tcp
	Vip : 65.52.xxx.xxx
	ProbePath :
	ProbePort : 80
	ProbeProtocol : tcp
	ProbeIntervalInSeconds : 15
	ProbeTimeoutInSeconds : 31
	EnableDirectServerReturn : False
	Acl : {}
	InternalLoadBalancerName :
	IdleTimeoutInMinutes : 15
	LoadBalancerDistribution : sourceIP
 
Wenn das Element "LoadBalancerDistribution" nicht vorhanden ist, verwendet der Azure-Lastenausgleich den standardmäßigen 5-Tupel-Algorithmus.

 
### Festlegen des Verteilungsmodus für einen Endpunktsatz mit Lastenausgleich

Wenn Endpunkte Bestandteil eines Endpunktsatzes mit Lastenausgleich sind, muss der Verteilungsmodus für den Endpunktsatz mit Lastenausgleich festgelegt werden:

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution "sourceIP"

### Clouddienstkonfiguration zum Ändern des Verteilungsmodus

Sie können das Azure SDK für .NET 2.5 (wird im November veröffentlicht) nutzen, um Ihre Clouddienst-Endpunkteinstellungen in der CSDEF-Datei zu aktualisieren. Um den Lastenausgleichs-Verteilungsmodus für eine Bereitstellung mit Clouddiensten zu aktualisieren, ist ein Upgrade der Bereitstellung erforderlich. Nachfolgend sehen Sie ein Beispiel für in der CSDEF-Datei vorgenommene Änderungen für Endpunkteinstellungen:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
  	<Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />

  	</Endpoints>
	</WorkerRole>
	<NetworkConfiguration>
  	<VirtualNetworkSite name="VNet"/>

  	<AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>

      </PublicIPs>
    </InstanceAddress>
  	</AddressAssignments>
	</NetworkConfiguration>


## API-Beispiel

Sie können die Lastenausgleichsverteilung mit der Dienstverwaltungs-API konfigurieren. Stellen Sie dabei sicher, dass Sie den X-MS-Versionsheader auf Version 2014-09-01 oder höher festlegen.
 
Aktualisieren der Konfiguration für den angegebenen Endpunktsatz mit Lastenausgleich in einer Bereitstellung

Anforderungsbeispiel

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 

	x-ms-version: 2014-09-01 

	Content-Type: application/xml 

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
	<InputEndpoint> 
	<LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
	<LocalPort> local-port-number </LocalPort> 
	<Port> external-port-number </Port> 
	<LoadBalancerProbe> 
	<Port> port-assigned-to-probe </Port> 
	<Protocol> probe-protocol </Protocol> 
	<IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
	<TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
	</LoadBalancerProbe> 
	<Protocol> endpoint-protocol </Protocol> 
	<EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
	<IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
	<LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
	</InputEndpoint> 
	</LoadBalancedEndpointList>

Der Wert von "LoadBalancerDistribution" kann "sourceIP" (2-Tupel-Affinität) oder "sourceIPProtocol" (3-Tupel-Affinität) lauten oder nicht festgelegt werden (keine Affinität, d. h. 5-Tupel-Konfiguration).

	Response

	HTTP/1.1 202 Accepted 
	Cache-Control: no-cache 
	Content-Length: 0 
	Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
	x-ms-servedbyregion: ussouth2 
	x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
	Date: Thu, 16 Oct 2014 22:49:21 GMT

## Nächste Schritte

[Interner Lastenausgleich (Übersicht)](load-balancer-internal-overview.md)

[Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen](load-balancer-internet-getstarted.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=August15_HO6-->