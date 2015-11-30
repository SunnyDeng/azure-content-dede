<properties 
   pageTitle="Erste Schritte zum Erstellen eines Lastenausgleichs für Internetzugriff in einem klassischen Bereitstellungsmodell für Clouddienste | Microsoft Azure"
   description="Erfahren Sie, wie Sie für Clouddienste einen Lastenausgleich mit Internetzugriff in einem klassischen Bereitstellungsmodell erstellen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/03/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff für Clouddienste

[AZURE.INCLUDE [Load-Balancer-Get-Started-Internet-Classic-Selectors-include.MD](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können auch [erfahren, wie Sie mit dem Ressourcen-Manager einen Load Balancer mit Internetzugriff erstellen](load-balancer-get-started-internet-arm-cli.md).

Clouddienste werden automatisch mit einem Lastenausgleich konfiguriert und können über das Dienstmodell angepasst werden.

Sie können das Azure SDK für .NET 2.5 verwenden, um Ihren Clouddienst zu aktualisieren. Endpunkteinstellungen für Clouddienste werden in der Dienstdefinitionsdatei ([.csdef](https://msdn.microsoft.com/library/azure/gg557553.aspx)) vorgenommen.

Das folgende Beispiel zeigt, wie eine "servicedefinition.csdef"-Datei für eine Cloudbereitstellung konfiguriert wird:

Wenn Sie den von einer Cloudbereitstellung generierten Codeabschnitt für die CSDEF-Datei überprüfen, sehen Sie, dass der externe Endpunkt für die Verwendung von HTTP an den Ports 10000, 10001 und 10002 konfiguriert wurde.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### Überprüfen des Integritätsstatus des Lastenausgleichs für Clouddienste


Im Folgenden finden Sie ein Beispiel für das Erstellen eines Integritätstests:

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

Der Lastenausgleich verbindet die Informationen des Endpunkts und die Informationen des Tests, um eine URL in Form von "http://{DIP von VM}:80/Probe.aspx" zur Abfrage der Integrität des Diensts zu erstellen.

Der Dienst erkennt regelmäßige Tests derselben IP-Adresse. Dies ist die Integritätstestanforderung vom Host des Knotens, auf dem der virtuelle Computer ausgeführt wird. Der Dienst muss mit einem HTTP-Statuscode 200 für den Lastenausgleich antworten, sodass davon ausgegangen wird, dass der Dienst fehlerfrei reagiert. Bei allen anderen HTTP-Statuscodes (z. B. 503) wird der virtuelle Computer direkt aus der Rotation genommen.

Die Testdefinition steuert auch die Häufigkeit des Tests. In unserem Fall testet der Lastenausgleich den Endpunkt alle 5 Sekunden. Wenn nach 10 Sekunden (zwei Testintervallen) keine positive Antwort empfangen wurde, wird von einem Ausfall ausgegangen und der virtuelle Computer aus der Rotation genommen. Dementsprechend wird der Dienst wieder in die Rotation aufgenommen, wenn eine positive Antwort empfangen wird. Wenn der Dienst zwischen fehlerfrei und fehlerhaft schwankt, kann der Lastenausgleich die erneute Einführung des Diensts in die Rotation verzögern, bis er in einer Reihe von Tests als fehlerfrei erkannt wurde.

Überprüfen Sie für weitere Informationen das Dienstdefinitionsschema für das [LoadBalancerProbe-Schema](https://msdn.microsoft.com/library/azure/jj151530.aspx).

## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-internal-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO4-->