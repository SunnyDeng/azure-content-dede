<properties 
   pageTitle="Erfahren Sie, wie Sie einen internen Lastenausgleich für Clouddienste im klassischen Bereitstellungsmodell erstellen | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie mithilfe von PowerShell einen internen Lastenausgleich im klassischen Bereitstellungsmodell erstellen."
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

# Erste Schritte zum Erstellen eines internen Lastenausgleichs (klassisch) für Clouddienste

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](load-balancer-get-started-ilb-arm-ps.md).


## Konfigurieren des internen Lastenausgleichs für Clouddienste

Der interne Lastenausgleich wird sowohl für virtuelle Computer als auch für Clouddienste unterstützt. Ein Endpunkt eines internen Lastenausgleichs, der in einem Clouddienst außerhalb eines regionalen virtuellen Netzwerks erstellt wurde, ist nur innerhalb des Clouddiensts verfügbar.

Die Konfiguration des internen Lastenausgleichs muss während der Erstellung der ersten Bereitstellung im Clouddienst festgelegt werden, wie im folgenden Beispiel dargestellt.

>[AZURE.IMPORTANT] Voraussetzung für die Ausführung der folgenden Schritte ist, dass Sie bereits ein virtuelles Netzwerk für die Cloudbereitstellung erstellt haben. Zum Erstellen des internen Lastenausgleichs benötigen Sie den Namen des virtuellen Netzwerks und den Subnetznamen.

### Schritt 1

Öffnen Sie die Dienstkonfigurationsdatei (CSCFG) für Ihre Cloudbereitstellung in Visual Studio, und fügen Sie den folgenden Abschnitt hinzu, um den internen Lastausgleich unter dem letzten "`</Role>`"-Element für die Netzwerkkonfiguration zu erstellen.




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


Wir fügen nun die Werte für die Netzwerkkonfigurationsdatei hinzu, um den Vorgang zu veranschaulichen. Im Beispiel wird davon ausgegangen, dass Sie ein Subnetz mit dem Namen „test\_vnet“ und einem 10.0.0.0/24-Subnetz erstellt haben, das „test\_subnet“ heißt und die statische IP 10.0.0.4 aufweist. Der Load Balancer hat den Namen testLB.

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

Weitere Informationen zum Lastenausgleichsschema finden Sie unter [Hinzufügen eines Lastenausgleichs](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### Schritt 2


Ändern Sie die Dienstdefinitionsdatei (CSDEF), um Endpunkte zum internen Lastenausgleich hinzuzufügen. Zum Zeitpunkt der Erstellung einer Rolleninstanz fügt die Dienstdefinitionsdatei die Rolleninstanzen zum internen Lastenausgleich hinzu.


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

Als Nächstes fügen wir der Dienstdefinitionsdatei die Werte hinzu, indem wir die Werte aus dem obigen Beispiel verwenden.

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

Der Lastenausgleich für den Netzwerkdatenverkehr erfolgt für eingehende Anforderungen mit dem Lastenausgleichsmodul "testLB" über Port 80. Die Workerrolleninstanzen werden ebenfalls über Port 80 gesendet.


## Nächste Schritte

[Konfigurieren eines Load Balancer-Verteilungsmodus mithilfe der Quell-IP-Affinität](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->