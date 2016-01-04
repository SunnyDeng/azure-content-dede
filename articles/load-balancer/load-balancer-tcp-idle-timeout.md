<properties 
   pageTitle="Konfigurieren des TCP-Leerlauftimeouts für Lastenausgleich | Microsoft Azure"
   description="Konfigurieren des TCP-Leerlauftimeouts für Lastenausgleich"
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
   ms.date="11/19/2015"
   ms.author="joaoma" />

# Ändern von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich

In der Standardkonfiguration ist der Azure-Lastenausgleich auf ein Leerlauftimeout von 4 Minuten eingestellt.

Dies bedeutet, dass bei einen Zeitraum der Inaktivität der TCP- oder HTTP-Sitzungen, der länger als der Timeoutwert ist, keine Garantie dafür gegeben werden kann, dass die Verbindung zwischen Client und Dienst bestehen bleibt.

Sobald die Verbindung geschlossen wird, wird in der Clientanwendung eine Fehlermeldung ähnlich dieser angezeigt: "Die zugrunde liegende Verbindung wurde geschlossen: Eine Verbindung, deren Aufrechterhaltung erwartet wurde, ist vom Server geschlossen worden."

Eine gängige Praxis, die Verbindung über einen längeren Zeitraum aktiv zu halten, ist die Verwendung von "TCP Keep-alive" (Beispiele für .NET finden Sie [hier](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)).

Pakete werden gesendet, wenn keine Aktivität der Verbindung erkannt wird. Durch regelmäßige Netzwerkaktivität wird der Wert für den Leerlauftimeout niemals erreicht, und die Verbindung bleibt über einen langen Zeitraum aufrechterhalten.

Der grundlegende Gedanke dahinter ist, "TCP Keep-alive" mit einem kürzeren Intervall als in der Standardeinstellung zu konfigurieren, sodass die Verbindung nicht getrennt wird, oder den Wert des Leerlauftimeouts zu erhöhen, um die TCP-Verbindung aufrecht zu erhalten.

Während "TCP Keep-alive" für Szenarios gut funktioniert, in denen Akkuleistung keine Rolle spielt, ist es im Allgemeinen keine geeignete Option für mobile Anwendungen. "TCP Keep-alive" sorgt in einer mobilen Anwendung wahrscheinlich für eine geringere Akkulebensdauer.

Für solche Szenarios haben wir Unterstützung für konfigurierbare Leerlauftimeouts hinzugefügt. Sie können nun eine Dauer zwischen 4 und 30 Minuten festlegen. Diese Einstellung funktioniert nur für eingehende Verbindungen.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## Ändern von Leerlauftimeout-Einstellungen von virtuellen Computern und Clouddiensten

- Konfigurieren des TCP-Timeouts für einen Endpunkt auf einem virtuellen Computer über PowerShell oder die Dienstverwaltungs-API
- Konfigurieren des TCP-Timeouts für den Endpunkt mit Lastenausgleich über PowerShell oder die Dienstverwaltungs-API.
- Festlegen des TCP-Timeouts für die öffentliche IP auf Instanzebene
- Konfigurieren des TCP-Timeouts für Web-/Workerrollen über das Dienstmodell
 

>[AZURE.NOTE]Beachten Sie, dass einige Befehle nur im aktuellen Azure PowerShell-Paket vorhanden sind. Wenn der Powershell-Befehl nicht vorhanden ist, laden Sie ein aktuelles PowerShell-Paket herunter.

 
### Festlegen des TCP-Timeouts für Ihre öffentliche IP auf Instanzebene auf 15 Minuten

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

"IdleTimeoutInMinutes" ist optional. Wenn dieser Wert nicht festgelegt ist, beträgt das Standardtimeout 4 Minuten.

>[AZURE.NOTE]Der zulässige Timeoutbereich liegt zwischen 4 und 30 Minuten.
 
### Festlegen des Leerlauftimeouts beim Erstellen eines Azure-Endpunkts auf einem virtuellen Computer

Ändern der Timeouteinstellung für einen Endpunkt

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
Abrufen Ihrer Leerlauftimeout-Konfiguration

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
 
### Festlegen des TCP-Timeouts für einen Endpunktsatz mit Lastenausgleich

Wenn Endpunkte Bestandteil eines Endpunktsatzes mit Lastenausgleich sind, muss das TCP-Timeout für den Endpunktsatz mit Lastenausgleich festgelegt werden.

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
 
### Ändern von Timeouteinstellungen für Clouddienste

Sie können das Azure SDK für .NET 2.4 verwenden, um Ihren Clouddienst zu aktualisieren.

Endpunkteinstellungen für Clouddienste werden in der CSDEF-Datei vorgenommen. Um das TCP-Timeout für eine Bereitstellung mit Clouddiensten zu aktualisieren, ist ein Upgrade der Bereitstellung erforderlich. Ausnahme: das TCP-Timeout wird nur für eine öffentliche IP festgelegt. Einstellungen für die öffentliche IP sind in der CSCFG-Datei gespeichert und können während des Updates und Upgrades der Bereitstellung aktualisiert werden.

In der CSDEF-Datei müssen die Einstellungen für einen Endpunkt folgendermaßen geändert werden:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

Die Änderungen in der CSCFG-Datei für die Timeouteinstellung für öffentliche IP-Adressen lauten:

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

## REST-API-Beispiel

Sie können den TCP-Leerlauftimeout mit der Dienstverwaltungs-API konfigurieren. Stellen Sie dabei sicher, dass Sie den X-MS-Versionsheader auf Version 2014-06-01 oder höher festlegen.
 
Aktualisieren der Konfiguration der angegebenen Eingabeendpunkte mit Lastenausgleich auf allen virtuellen Computern in einer Bereitstellung
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<InputEndpoint>
	<LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
	<LocalPort>local-port-number</LocalPort>
	<Port>external-port-number</Port>
	<LoadBalancerProbe>
	<Path>path-of-probe</Path>
	<Port>port-assigned-to-probe</Port>
	<Protocol>probe-protocol</Protocol>
	<IntervalInSeconds>interval-of-probe</IntervalInSeconds>
	<TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
	</LoadBalancerProbe>
	<LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
	<Protocol>endpoint-protocol</Protocol>
	<IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
	<EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
	<EndpointACL>
	<Rules>
	<Rule>
	<Order>priority-of-the-rule</Order>
	<Action>permit-rule</Action>
	<RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
	<Description>description-of-the-rule</Description>
	</Rule>
	</Rules>
	</EndpointACL>
	</InputEndpoint>
	</LoadBalancedEndpointList>

## Nächste Schritte

[Interner Lastenausgleich (Übersicht)](load-balancer-internal-overview.md)

[Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen](load-balancer-internet-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

 

<!---HONumber=AcomDC_1125_2015-->