<properties
   pageTitle="Service Fabric-Knotentypen und VM-Skalierungsgruppen | Microsoft Azure"
   description="Beschreibt, wie Service Fabric-Knotentypen mit VM-Skalierungsgruppen in Zusammenhang stehen, und wie die Remoteverbindung mit einer VM-Skalierungsgruppeninstanz oder einem Clusterknoten hergestellt wird."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/17/2016"
   ms.author="chackdan"/>


# Die Beziehung zwischen Service Fabric-Knotentypen und VM-Skalierungsgruppen

VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Sammlung von virtuellen Computern bereitstellen und verwalten können. Jeder Knotentyp, der in einem Service Fabric-Cluster definiert ist, wird als separate VM-Skalierungsgruppe festgelegt. Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen.

Der folgende Screenshot zeigt einen Cluster mit zwei Knotentypen: FrontEnd und BackEnd. Jeder Knotentyp verfügt über fünf Knoten.

![Screenshot eines Clusters mit zwei Knotentypen][NodeTypes]

## Zuordnen von VM-Skalierungsgruppeninstanzen zu Knoten

Wie Sie oben sehen können, beginnen VM-Skalierungsgruppeninstanzen bei Instanz 0 und werden dann heraufgezählt. Die Namen spiegeln die Nummerierung wider. Beispiel: „BackEnd\_0“ ist Instanz 0 der BackEnd-VM-Skalierungsgruppe. Diese bestimmte VM-Skalierungsgruppe hat fünf Instanzen mit den Namen „BackEnd\_0“, „BackEnd\_1“, „BackEnd\_2“, „BackEnd\_3“ und „BackEnd\_4“.

Wenn Sie eine VM-Skalierungsgruppe zentral hochskalieren, wird eine neue Instanz erstellt. Der neue VM-Skalierungsgruppen-Instanzname hat in der Regel folgende Struktur: VM-Skalierungsgruppenname + Nummer der nächsten Instanz. In diesem Beispiel lautet er „BackEnd\_5“.


## Zuordnen des VM-Skalierungsgruppen-Lastenausgleichs für jeden Knotentyp/jede VM-Skalierungsgruppe

Wenn Sie Ihren Cluster vom Portal aus bereitgestellt oder die bereitgestellte ARM-Beispielvorlage verwendet haben und dann eine Liste aller Ressourcen in einer Ressourcengruppe abrufen, wird der Lastenausgleich für jede VM-Skalierungsgruppe oder jeden Knotentyp angezeigt.

Der Name würde etwa folgendermaßen lauten: **LB-&lt;Knotentypname&gt;**. Z. B. „LB-sfcluster4doc-0“, wie in diesem Screenshot gezeigt:


![Ressourcen][Resources]


## Herstellen einer Remoteverbindung mit einer VM-Skalierungsgruppeninstanz oder einem Clusterknoten
Jeder Knotentyp, der in einem Cluster definiert ist, wird als separate VM-Skalierungsgruppe eingerichtet. Dies bedeutet, dass die Knotentypen unabhängig zentral hoch- bzw. herunterskaliert und aus verschiedenen VM-SKUs erstellt werden können. Im Gegensatz zu Einzelinstanz-VMs erhalten die VM-Skalierungsgruppeninstanzen keine eigene virtuelle IP-Adresse. Daher kann es für Sie eine gewisse Herausforderung sein, nach einer IP-Adresse und einem Port zum Herstellen einer Remoteverbindung mit einer bestimmten Instanz zu suchen.

Mit den folgenden Schritten können Sie diese ermitteln.

### Schritt 1: Suchen der virtuellen IP-Adresse für den Knotentyp und dann der Regeln für eingehenden NAT-Datenverkehr für RDP

Hierzu müssen Sie die Werte der Regeln für eingehenden NAT-Datenverkehr abrufen, die als Teil der Ressourcendefinition für **Microsoft.Network/loadBalancers** definiert wurden.

Wechseln Sie in das Portal zum Blatt „Load Balancer“ und dann zu **Einstellungen**.

![LBBlade][LBBlade]


Klicken Sie in **Einstellungen** auf **NAT-Eingangsregeln**. Jetzt erhalten Sie die IP-Adresse und den Port zum Herstellen einer Remoteverbindung mit der ersten VM-Skalierungsgruppeninstanz. Im folgenden Screenshot sind dies **104.42.106.156** und **3389**.

![NATRules][NATRules]

### Schritt 2: Suchen des Ports, mit dem Sie eine Remoteverbindung mit der bestimmten VM-Skalierungsgruppeninstanz/dem bestimmten Knoten herstellen können

Weiter oben in diesem Dokument habe ich die Zuordnung der VM-Skalierungsgruppeninstanzen zu den Knoten erörtert. Wir verwenden diese, um den richtigen Port zu ermitteln.

Die Zuweisung der Ports erfolgt in der aufsteigenden Reihenfolge der VM-Skalierungsgruppeninstanz. In meinem Beispiel für den Knotentyp „FrontEnd“ erhalten die fünf Instanzen also die folgenden Ports. Jetzt müssen Sie die gleiche Zuordnung für Ihre VM-Skalierungsgruppeninstanz vornehmen.

|**VMSS-Instanz**|**Port**|
|-----------------------|--------------------------|
|FrontEnd\_0|3389|
|FrontEnd\_1|3390|
|FrontEnd\_2|3391|
|FrontEnd\_3|3392|
|FrontEnd\_4|3393|
|FrontEnd\_5|3394|


### Schritt 3: Herstellen der Remoteverbindung mit der bestimmten VM-Skalierungsgruppeninstanz

Im folgenden Screenshot wird die Remotedesktopverbindung zum Herstellen der Verbindung mit „FrontEnd\_1“ verwendet:

![RDP][RDP]

## Ändern der Werte des RDP-Portbereichs

### Vor der Clusterbereitstellung

Wenn Sie den Cluster mithilfe einer ARM-Vorlage einrichten, können Sie den Bereich in **inboundNatPools** angeben.

Wechseln Sie zur Ressourcendefinition für **Microsoft.Network/loadBalancers**. Darunter finden Sie die Beschreibung für **inboundNatPools**. Ersetzen Sie die Werte *frontendPortRangeStart* und *frontendPortRangeEnd*.

![InboundNatPools][InboundNatPools]


### Nach der Clusterbereitstellung
Dies ist etwas komplizierter und kann dazu führen, dass die virtuellen Computern neu gestartet werden. Sie müssen nun neue Werte mit Azure PowerShell festlegen. Stellen Sie sicher, dass Azure PowerShell 1.0 oder höher auf Ihrem Computer installiert ist. Falls noch nicht erfolgt, sollten Sie unbedingt die Schritte unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) befolgen.

Melden Sie sich beim Azure-Konto an. Wenn dieser PowerShell-Befehl aus irgendeinem Grund Fehler verursacht, sollten Sie prüfen, ob Azure PowerShell ordnungsgemäß installiert ist.

```
Login-AzureRmAccount
```

Wenn Sie Folgendes ausführen, um die Details für den Lastenausgleich abzurufen, sehen Sie die Werte, denn Sie finden die Beschreibung für **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Legen Sie jetzt die gewünschten Werte für *frontendPortRangeEnd* und *frontendPortRangeStart* fest.

```
$PropertiesObject = @{
	#Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## Nächste Schritte

- [Übersicht über das Feature „Deploy Anywhere“ (umgebungsunabhängige Bereitstellung) und ein Vergleich mit Clustern, die von Azure verwaltet werden](service-fabric-deploy-anywhere.md)
- [Clustersicherheit](service-fabric-cluster-security.md)
- [ Service Fabric-SDK und erste Schritte](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

<!---HONumber=AcomDC_0224_2016-->