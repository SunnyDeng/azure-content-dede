<properties
   pageTitle="Verwalten: Lastenausgleichs-Verteilungsmodus (Quell-IP-Affinität)"
   description="Verwaltungsfunktionen für den Azure-Lastenausgleichs-Verteilungsmodus"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"
   />

# Verwalten des virtuellen Netzwerks: Lastenausgleichs-Verteilungsmodus (Quell-IP-Affinität)
**Quell-IP-Affinität** (auch als **Sitzungsaffinität** oder **Client-IP-Affinität **bezeichnet), ein Azure-Lastenausgleichs-Verteilungsmodus, erzeugt eine Bindung zwischen einem einzelnen Client und einem über Azure gehosteten Server, statt die Clientverbindungen dynamisch auf unterschiedliche über Azure gehostete Server zu verteilen (dies ist das Standardverhalten für den Lastenausgleich).

Mithilfe der Quell-IP-Affinität kann der Azure-Lastenausgleich so konfiguriert werden, dass mit einer 2-Tupel-Kombination (Quell-IP, Ziel-IP) oder eine 3-Tupel-Kombination (Quell-IP, Ziel-IP, Protokoll) der Datenverkehr einem Pool verfügbarer Server zugeordnet wird, die über Azure gehostet werden. Bei Verwendung der Quell-IP-Affinität werden vom selben Clientcomputer initiierte Verbindungen durch einen einzelnen DIP-Endpunkt verarbeitet (einen einzelnen, über Azure gehosteten Server).

## Dienstursprung

Mit der Quell-IP-Affinität wird das vorherige Problem der [Inkompatibilität zwischen Azure-Lastenausgleich und RD-Gateway (DOC)](http://go.microsoft.com/fwlink/p/?LinkId=517389) gelöst.

## Implementierung

Die Quell-IP-Affinität kann für folgende Komponenten konfiguriert werden:

* [VM-Endpunkte](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)
* [Endpunktsätze mit Lastenausgleich](../load-balancer/load-balancer-overview.md)
* [Webrollen](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Workerrollen](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Szenarios
1. Remotedesktop-Gatewaycluster mit Verwendung eines einzelnen Cloud-Diensts
2. Hochladen von Mediendaten (z. B. UDP für Daten, TCP für Steuerung)
  * Client initiiert eine TCP-Sitzung mit einer über Azure gehosteten öffentlichen IP-Adresse mit Lastenausgleich
  * Clientanforderung wird über den Lastenausgleich an einen DIP Client geleitet; dieser Kanal bleibt aktiv, um die Verbindungsintegrität zu überwachen
  * Client initiiert eine UDP-Sitzung mit einer über Azure gehosteten öffentlichen IP-Adresse mit Lastenausgleich
  * Der Azure-Lastenausgleich leitet die Anforderung an denselben DIP-Endpunkt wie die TCP-Verbindung
  * Client lädt Mediendaten mit höherem UDP-Durchsatz hoch, während der Steuerungskanal über TCP zur Gewährleistung der Zuverlässigkeit aktiv bleibt

## Einschränkungen
* Wenn sich der Komponentensatz mit Lastenausgleich ändert (beispielsweise durch das Hinzufügen oder Entfernen von virtuellen Computern), wird die Verteilung der Clientkanäle neu berechnet; neue Verbindungen vorhandener Clients werden möglicherweise durch einen anderen Server als ursprünglich verarbeitet.
* Die Verwendung der Quell-IP-Affinität kann zu einer ungleichen Verteilung des Datenverkehrs auf die von Azure gehosteten Server führen.
* Clients, die ihren Datenverkehr über einen Proxy routen, werden vom Azure-Lastenausgleich möglicherweise als einzelner Client erkannt.

## PowerShell-Beispiele
Laden Sie [die neueste Azure PowerShell-Version](https://github.com/Azure/azure-sdk-tools/releases) herunter, um optimale Ergebnisse zu erzielen.

### Fügen Sie einem virtuellen Computer einen Azure-Endpunkt hinzu, und legen Sie den Lastenausgleichs-Verteilungsmodus fest.

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution “sourceIP”| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LoadBalancerDistribution "sourceIP"| Update-AzureVM  

"LoadBalancerDistribution" kann für eine 2-Tupel-Konfiguration (Quell-IP, Ziel-IP) auf "sourceIP", für eine 3-Tupel-Konfiguration (Quell-IP, Ziel-IP, Protokoll) auf "sourceIPProtocol", oder gar nicht festgelegt werden, um das Standardverhalten (5-Tupel-Lastenausgleich) zu verwenden.

### Abrufen einer Lastenausgleichs-Verteilungsmoduskonfiguration für einen Endpunkt
    PS C:\> Get-AzureVM –ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint

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

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 LoadBalancerDistribution "sourceIP"

Wenn Endpunkte Bestandteil eines Endpunktsatzes mit Lastenausgleich sind, muss der Verteilungsmodus für den Endpunktsatz mit Lastenausgleich festgelegt werden.

## Beispiele für den Cloud-Dienst

Sie können das Azure-SDK für .NET verwenden, um Ihren Cloud-Dienst zu aktualisieren.

Endpunkteinstellungen für Cloud-Dienste werden in der CSDEF-Datei vorgenommen. Um den Lastenausgleichs-Verteilungsmodus für eine Bereitstellung mit Cloud-Diensten zu aktualisieren, ist ein Upgrade der Bereitstellung erforderlich.

Nachfolgend sehen Sie ein Beispiel für in der CSDEF-Datei vorgenommene Änderungen für Endpunkteinstellungen:

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

## API-Beispiele

Entwickler können die Lastenausgleichsverteilung mithilfe der Dienstverwaltungs-API konfigurieren. Stellen Sie sicher, dass der x-ms-version-Header eingefügt und auf Version 2014-09-01 oder höher festgelegt wird.

### Aktualisieren der Konfiguration für den angegebenen Endpunktsatz mit Lastenausgleich in einer Bereitstellung

#### Anforderung

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

Der Wert von "LoadBalancerDistribution" kann "sourceIP" (2-Tupel-Affinität) oder "sourceIPProtocol" (3-Tupel-Affinität) lauten oder nicht festgelegt werden (keine Affinität, d. h. 5-Tupel-Konfiguration).

#### Antwort

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

<!---HONumber=AcomDC_0323_2016-->