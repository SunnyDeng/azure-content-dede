<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Verwalten: Leerlauftimeout für Lastenausgleich" 
   description="Verwaltungsfunktionen für das Leerlauftimeout für den Azure-Lastenausgleich" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="09/01/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# Verwalten virtueller Netzwerke: TCP-Leerlauftimeout für Lastenausgleich

Mithilfe eines **TCP-Leerlauftimeouts** kann ein Entwickler einen garantierten Schwellenwert für Inaktivität während Client/Server-Sitzungen festlegen, in denen der Azure-Lastenausgleich verwendet wird. Ein TCP-Leerlauftimeout von 4 Minuten (der Standardwert für den Azure-Lastenausgleich) bedeutet Folgendes: Wenn während einer Client/Server-Sitzung, in der der Azure-Lastenausgleich verwendet wird, über einen Zeitraum von mehr als 4 Minuten keine Aktivität festgestellt wird, wird die Verbindung geschlossen.

Sobald eine Client/Server-Verbindung geschlossen wird, wird in der Clientanwendung eine Fehlermeldung ähnlich dieser angezeigt: "Die zugrunde liegende Verbindung wurde geschlossen: Eine Verbindung, deren Aufrechterhaltung erwartet wurde, ist vom Server geschlossen worden."

[TCP-Keep-Alive](http://tools.ietf.org/html/rfc1122#page-101) ist eine gängige Methode, um Verbindungen während eines langen inaktiven Zeitraums aufrechtzuerhalten [(MSDN-Beispiel)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Bei Verwendung von TCP-Keep-Alive sendet ein Client regelmäßig einfache Pakete (üblicherweise in einem Intervall, das kürzer ist als der Leerlauftimeout-Schwellenwert des Servers). Der Server betrachtet diese Übertragungen als Beweis für eine aktive Verbindung, selbst wenn sonst keine Aktivität festzustellen ist. Daher wird der Leerlauftimeout-Schwellenwert nie erreicht und die Verbindung kann über einen langen Zeitraum aufrechterhalten werden.

TCP-Keep-Alive funktioniert üblicherweise gut, ist für mobile Anwendungen im Allgemeinen jedoch nicht zu empfehlen, da es die begrenzten Energieressourcen mobiler Geräte belastet. Eine mobile Anwendung, die TCP-Keep-Alive verwendet, verbraucht den Geräteakku schneller, da sie ständig Energie für die Netzwerknutzung benötigt.

Zur Unterstützung von Szenarien mit Mobilgeräten unterstützt der Azure-Lastenausgleich ein konfigurierbares TCP-Leerlauftimeout. Entwickler können das TCP-Leerlauftimeout für eingehende Verbindungen auf einen beliebigen Zeitraum zwischen 4 und 30 Minuten festlegen (das konfigurierbare TCP-Leerlauftimeout gilt nicht für ausgehende Verbindungen). So können Clients auch bei langen inaktiven Zeiträumen eine wesentlich längere Sitzung mit einem Server aufrechterhalten. Für eine Anwendung auf einem mobilen Gerät kann weiterhin das TCP-Keep-Alive-Verfahren genutzt werden, um Verbindungen mit einem Inaktivitätszeitraum von mehr als 30 Minuten aufrechtzuerhalten. Mit diesem höheren TCP-Leerlauftimeout ist es jedoch möglich, dass Anwendungen wesentlich seltener TCP-Keep-Alive-Anforderungen senden – dadurch werden die Energieressourcen von mobilen Geräten deutlich weniger belastet.

## Implementierung

Ein TCP-Leerlauftimeout kann für Folgendes konfiguriert werden:

* [Öffentliche IP auf Instanzebene](virtual-networks-instance-level-public-ip.md)
* [Endpunktsätze mit Lastenausgleich](../load-balancer/load-balancer-overview.md)
* [VM-Endpunkte](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Webrollen](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Workerrollen](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Nächste Schritte
* TBD

## PowerShell-Beispiele
Laden Sie die neueste [Azure PowerShell-Version](https://github.com/Azure/azure-sdk-tools/releases) herunter, um optimale Ergebnisse zu erzielen.

### Festlegen des TCP-Timeouts für Ihre öffentliche IP auf Instanzebene auf 15 Minuten

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

"IdleTimeoutInMinutes" ist optional. Wenn dieser Wert nicht festgelegt ist, beträgt das Standardtimeout 4 Minuten. Das Timeout kann jetzt auf einen Wert zwischen 4 und 30 Minuten festgelegt werden.

### Festlegen des Leerlauftimeouts beim Erstellen eines Azure-Endpunkts auf einem virtuellen Computer

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### Abrufen Ihrer Leerlauftimeout-Konfiguration

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

## Beispiele für den Cloud-Dienst

Sie können das Azure-SDK für .NET verwenden, um Ihren Cloud-Dienst zu aktualisieren.

Endpunkteinstellungen für Cloud-Dienste werden in der CSDEF-Datei vorgenommen. Um das TCP-Timeout für eine Bereitstellung mit Cloud-Diensten zu aktualisieren, ist ein Upgrade der Bereitstellung erforderlich. Ausnahme: das TCP-Timeout wird nur für eine öffentliche IP festgelegt. Einstellungen für die öffentliche IP sind in der CSCFG-Datei gespeichert und können während des Updates und Upgrades der Bereitstellung aktualisiert werden.

In der CSDEF-Datei müssen die Einstellungen für einen Endpunkt folgendermaßen geändert werden:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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
    
## API-Beispiele

Entwickler können die Lastenausgleichsverteilung mithilfe der Dienstverwaltungs-API konfigurieren. Stellen Sie sicher, dass der x-ms-version-Header eingefügt und auf Version 2014-06-01 oder höher festgelegt wird.

### Aktualisieren der Konfiguration der angegebenen Eingabeendpunkte mit Lastenausgleich auf allen virtuellen Computern in einer Bereitstellung

#### Anforderung

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

Der Wert von "LoadBalancerDistribution" kann "sourceIP" (2-Tupel-Affinität) oder "sourceIPProtocol" (3-Tupel-Affinität) lauten oder nicht festgelegt werden (keine Affinität, d. h. 5-Tupel-Konfiguration).

#### Antwort

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
 

<!---HONumber=Oct15_HO3-->