
<properties
   pageTitle="Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen | Microsoft Azure"
   description="Einrichten des ersten Lastenausgleichs für Internetverbindungen für Ihre virtuellen Computer oder Clouddienste."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/12/2015"
   ms.author="joaoma" />

# Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen

> [AZURE.SELECTOR]
- [Azure classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)

Lastenausgleichsdienste in Microsoft Azure funktionieren mit allen Mandantentypen (IaaS oder PaaS) und allen unterstützten Betriebssystemen (Windows- oder unterstützte Linux-Betriebssysteme).


## Einrichten des Lastenausgleichs für Internetverbindungen für virtuelle Computer

Um Lastenausgleich für Netzwerkverkehr aus dem Internet auf virtuellen Computern eines Clouddiensts auszuführen, müssen Sie eine Gruppe mit Lastenausgleich erstellen. Dieses Verfahren setzt voraus, dass Sie die virtuellen Computer bereits erstellt haben und diese sich im selben Clouddienst befinden.

**So konfigurieren Sie eine Gruppe mit Lastenausgleich für virtuelle Computer**

1. Klicken Sie im Azure-Portal auf **Virtuelle Computer** und anschließend auf den Namen eines virtuellen Computers in der Gruppe mit Lastenausgleich.
2.	Klicken Sie auf **Endpunkte** und anschließend auf **Hinzufügen**.

4.	Klicken Sie auf der Seite **Endpunkt zu einem virtuellen Computer hinzufügen** auf den rechten Pfeil.

4.	Gehen Sie auf der Seite **Geben Sie die Details des Endpunktes ein** folgendermaßen vor:
	- Geben Sie unter **Name** einen Namen für den Endpunkt ein, oder wählen Sie einen der vordefinierten Endpunkte für gängige Protokolle aus der Liste aus.
	-  Wählen Sie unter **Protokoll** das Protokoll aus, das für den Endpunkttyp erforderlich ist: TCP oder UDP.
 	-  Geben Sie unter **Öffentlicher Port** und "Privater Port" die Portnummern ein, die der virtuelle Computer verwenden soll. Sie können die Regeln für privaten Port und Firewall auf dem virtuellen Computer verwenden, um Datenverkehr entsprechend Ihren Anforderungen umzuleiten. Der private Port kann mit dem öffentlichen Port identisch sein. Für einen Endpunkt für Webverkehr (HTTP) könnten Sie zum Beispiel Port 80 sowohl für den öffentlichen als auch für den privaten Port verwenden.

5.	Klicken Sie auf **Satz mit Lastenausgleich erstellen** und klicken Sie auf den Pfeil nach rechts.

6.	Geben Sie auf der Seite **Gruppe mit Lastenausgleich konfigurieren** einen Namen für die Gruppe mit Lastenausgleich ein, und weisen Sie dann die Werte für das Testverhalten des Azure-Lastenausgleichsmoduls zu. Das Lastenausgleichsmodul führt Tests durch, um zu ermitteln, ob die virtuellen Computer in einem Satz mit Lastenausgleich empfangsbereit für eingehenden Datenverkehr sind.

7.	Klicken Sie auf das Häkchen, um den Endpunkt mit Lastenausgleich zu erstellen. In der Spalte **Name des Satzes mit Lastenausgleich** auf der Seite **Endpunkt** für den virtuellen Computer wird der Eintrag **Ja** angezeigt.

8.	Klicken Sie im Portal auf **Virtuelle Computer**, klicken Sie auf den Namen eines weiteren virtuellen Computers in der Gruppe mit Lastenausgleich, klicken Sie auf **Endpunkte** und dann auf **Hinzufügen**.

9.	Klicken Sie auf der Seite **Endpunkt zu virtuellem Computer hinzufügen** auf **Endpunkt zu einer vorhandenen Gruppe mit Lastenausgleich hinzufügen**, wählen Sie den Namen der Gruppe mit Lastenausgleich aus, und klicken Sie auf den Pfeil nach rechts.

10.	Geben Sie auf der Seite **Geben Sie die Details des Endpunktes ein** einen Namen für den Endpunkt ein, und klicken Sie auf das Häkchen. Wiederholen Sie die Schritte 8 bis 10 für die weiteren virtuellen Computer in der Gruppe mit Lastenausgleich.

Sie können auch Endpunkte mit den folgenden Windows PowerShell-Cmdlets konfigurieren:

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## Einrichten des Lastenausgleichs für Internetverbindungen für Clouddienste


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

## Einrichten des Load Balancers mit PowerShell

Nach dem Erstellen eines virtuellen Computers können Sie PowerShell-Cmdlets verwenden, um einem virtuellen Computer in demselben Clouddienst einen Load Balancer hinzuzufügen.

Im folgenden Beispiel fügen Sie einen Load Balancer mit dem Namen "webfarm" dem Clouddienstendpunkt "mycloudservice" (bzw. "mycloudservice.cloudapp.net") und einem virtuellen Computer mit dem Namen "myVM" hinzu. Der Load Balancer empfängt Datenverkehr über Port 80 und führt für den Netzwerkdatenverkehr zwischen den virtuellen Computern über Port 8080 per HTTP einen Lastenausgleich durch.

	Get-AzureVM -ServiceName "mycloudservice" -Name "MyVM" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM


## Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-internal-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=August15_HO8-->