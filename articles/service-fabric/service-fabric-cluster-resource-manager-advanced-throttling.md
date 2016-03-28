<properties
   pageTitle="Einführung in den Clusterressourcen-Manager von Service Fabric | Microsoft Azure"
   description="Eine Einführung in den Clusterressourcen-Manager von Service Fabric."
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
   ms.date="03/10/2016"
   ms.author="masnider"/>


# Drosseln des Verhaltens des Clusterressourcen-Managers von Service Fabric
Selbst wenn Sie den Ressourcen-Manager ordnungsgemäß konfiguriert haben, kann der Cluster unterbrochen werden. Auslöser können z. B. zu viele Knoten- oder Fehlerdomänenfehler sein, während Aktualisierungen durchgeführt oder übernommen werden. Der Ressourcen-Manager versucht, alle Fehler zu beheben. In diesen Fällen sollten Sie über den Einsatz eines Backstops nachdenken, sodass sich der Cluster selbst stabilisiert (die Knoten, die zurückkommen sollen, kommen zurück, die Netzwerkbedingungen korrigieren sich von selbst und die korrigierten Bits werden bereitgestellt). Um Sie in Situationen wie diesen zu unterstützen, bietet der Ressourcen-Manager mehrere Drosselungen an. Beachten Sie, dass diese schwerwiegende Eingriffe darstellen. Sie sollten nicht verwendet werden, solange nicht berechnet wurde, wie viel Arbeit der Cluster parallel tatsächlich ausführen kann. Zudem sollte die Notwendigkeit bestehen, auf diese ungeplanten, makroskopischen Ereignisse (auch „sehr schlechte Tage“ genannt) zu reagieren.

Wie empfehlen gemeinhin, „sehr schlechte Tage“ mithilfe anderer Optionen zu vermeiden (z. B. durch reguläre Codeaktualisierungen oder generell durch das Vermeiden einer Überlastung des Clusters), statt den Cluster zu drosseln und daran zu hindern, Ressourcen zu nutzen, während er gleichzeitig versucht, sich selbst zu reparieren. Trotzdem können Sie Situationen bestimmen, in denen Drosselungen für Sie nützlich sind (bis Sie sie beheben können), auch wenn der Cluster dadurch länger zum Stabilisieren braucht.

##Konfigurieren der Drosselungen
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

In den meisten Fällen, in denen Kunden diese Drosselungen eingesetzt haben, lag bereits eine Umgebung mit Ressourceneinschränkungen vor (z. B. begrenzte Netzwerkbandbreite in den einzelnen Knoten). Solche Vorgänge konnten daher ohnehin nicht erfolgreich oder nur langsam ausgeführt werden, und die Kunden nahmen in Kauf, dass sie damit möglicherweise die Zeitspanne zum Erreichen eines stabilen Zustands des Clusters ausdehnten. Dazu gehörte auch das Wissen, dass sie während der Drosselung mit einer geringeren allgemeinen Zuverlässigkeit rechnen mussten.

## Nächste Schritte
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md).
- Der Clusterressourcen-Manager bietet viele Optionen für die Beschreibung des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).

<!---HONumber=AcomDC_0316_2016-->