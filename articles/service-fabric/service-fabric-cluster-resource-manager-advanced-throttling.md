<properties
   pageTitle="Einführung in den Clusterressourcen-Manager von Service Fabric"
   description="Eine Einführung in den Clusterressourcen Manager von Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Drosselungen
Selbst wenn Sie den Ressourcen-Manager ordnungsgemäß konfiguriert haben, kann der Cluster unterbrochen werden. Auslöser können z. B. zu viele Knoten- oder Fehlerdomänenfehler sein, während Aktualisierungen durchgeführt oder übernommen werden. Der Ressourcen-Manager versucht, alle Fehler zu beheben. In diesen Fällen sollten Sie über den Einsatz eines Backstops nachdenken, sodass sich der Cluster selbst stabilisiert (die Knoten, die zurückkommen sollen, kommen zurück, die Netzwerkbedingungen korrigieren sich von selbst und die korrigierten Bits werden bereitgestellt). Um Sie in Situationen wie diesen zu unterstützen, bietet der Ressourcen-Manager mehrere Drosselungen an. Beachten Sie, dass diese schwerwiegende Eingriffe darstellen. Sie sollten nicht verwendet werden, solange nicht berechnet wurde, wie viel Arbeit der Cluster parallel tatsächlich ausführen kann. Zudem sollte die Notwendigkeit bestehen, auf diese ungeplanten, makroskopischen Ereignisse (auch „sehr schlechte Tage“ genannt) zu reagieren.

Wie empfehlen gemeinhin, „sehr schlechte Tage“ mithilfe anderer Optionen zu vermeiden (z. B. durch reguläre Codeaktualisierungen oder generell durch das Vermeiden einer Überlastung des Clusters), statt den Cluster zu drosseln und daran zu hindern, Ressourcen zu nutzen, während er gleichzeitig versucht, sich selbst zu reparieren. Trotzdem können Sie Situationen bestimmen, in denen Drosselungen für Sie nützlich sind (bis Sie sie beheben können), auch wenn der Cluster dadurch länger zum Stabilisieren braucht.

Die standardmäßig enthaltenen Drosselungen sind:
-	GlobalMovementThrottleThreshold – steuert die Gesamtzahl der Bewegungen im Cluster über eine bestimmte Zeit (definiert als das GlobalMovementThrottleCountingInterval, Wert in Sekunden)
-	MovementPerPartitionThrottleThreshold – steuert die Gesamtzahl der Bewegungen für jede Dienstpartition über eine bestimmte Zeit (definiert als das MovementPerPartitionThrottleCountingInterval, Wert in Sekunden)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Beachten Sie, dass die Kunden, die Drosselungen verwenden, dies tun, weil:
-	sie bereits in einer Umgebung mit eingeschränkten Ressourcen (z. B. begrenzte Netzwerkbandbreite in den einzelnen Knoten) arbeiten, weswegen solche Vorgänge nicht erfolgreich ausgeführt können oder ohnehin langsam sind, UND
-	sie sich bewusst waren, dass die Drosselungen möglicherweise die Zeitspanne verlängern, bis der Cluster sich wieder stabilisiert hat, und die Prozesse während des Drosselns vielleicht sogar bei einer niedrigeren allgemeinen Zuverlässigkeit ausgeführt werden.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Learn about how the Cluster Resource Manager Balances Load in the Cluster](service-fabric-cluster-resource-manager-balancing.md) (Informationen zum Lastenausgleich im Cluster durch den Clusterressourcen-Manager)

<!---HONumber=AcomDC_0309_2016-->