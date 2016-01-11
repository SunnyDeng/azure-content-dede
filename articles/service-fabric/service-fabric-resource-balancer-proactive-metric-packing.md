<properties
   pageTitle="Proaktives Packen von Metriken | Microsoft Azure"
   description="Übersicht über das proaktive Packen von Metriken im Resource Balancer"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Proaktives Packen von Metriken

Eine häufig verwendete Konfiguration für den Resource Balancer in Azure Service Fabric zielt darauf ab, für jede Metrik auf jedem Knoten eine gleichmäßige Auslastung zu erreichen. Der Resource Balancer hat außerdem die Aufgabe, bei Eingang neuer Dienstanforderungen einen Platz für Dienste zu finden. Sollte für neue Dienstreplikate (alle Replikate aller Dienstpartitionen) nicht genügend Platz vorhanden sein, versucht der Resource Balancer, durch Umverteilung vorhandener Workloads Platz zu schaffen. Dieser Vorgang ist ganz normal, kann aber zeitaufwendig sein. Das hängt davon ab, wie voll der Cluster ist und welcher Fragmentierungsgrad für die Workload besteht.

Wenn der Cluster beispielsweise ziemlich stark ausgelastet ist und der Kunde einen neuen Dienst mit einer hohen Standardauslastung hinzufügen möchte (z. B. maximale Knotenkapazität für eine oder mehrere Metriken), muss der Resource Balancer unter Umständen viele Replikate verschieben, um den neuen Dienst platzieren zu können. Wenn Dienste zustandsbehaftet und groß sind, kann das Ausführen der erforderlichen Verschiebungen einige Zeit in Anspruch nehmen, weil Daten kopiert werden müssen. Diese beiden Aspekte können zu einer Verlängerung der Diensterstellungsdauer führen. Im Allgemeinen wird eine gelegentlich länger dauernde Diensterstellung zwar toleriert, einige Workloads sind jedoch weniger tolerant und erwarten eine schnellstmögliche Erstellung. Das bedeutet, dass der Resource Balancer im stabilen Zustand die Defragmentierung des Clusters sicherstellen muss, um die Chance zu erhöhen, dass genügend Platz für neue Workloads vorhanden ist.

Der Mechanismus zum proaktiven Packen von Metriken (Defragmentierung) wird als Teil der Ausgleichsphase im Resource Balancer ausgeführt, um die Diensterstellung zu beschleunigen. Hierzu werden Workloads auf weniger Knoten platziert, anstatt sie wie beim Lastenausgleich zu verteilen. Wenn eine Metrik für die Defragmentierung konfiguriert ist, strebt der Resource Balancer nicht wie beim Lastenausgleich die minimale durchschnittliche Standardabweichung, sondern die maximale durchschnittliche Standardabweichung an.

Bei der maximalen Abweichung versucht der Resource Balancer, möglichst viele Dienste auf einigen wenigen Knoten zu platzieren und dabei möglichst viele Knoten leer zu lassen. Eine der grundlegenden Einschränkungen für das Platzieren neuer Dienste ist zudem, dass sich Replikate nicht in der gleichen Upgrade- oder Fehlerdomäne befinden können. Da das Ziel das schnelle Hinzufügen neuer Dienste ist, muss der Resource Balancer die minimale Standardabweichung der Lastenverteilung zwischen Upgradedomänen und Fehlerdomänen anstreben (Summe der Dienstauslastungen pro Upgradedomäne/Fehlerdomäne). Das führt dann dazu, dass pro Upgradedomäne/Fehlerdomäne die gleiche Menge an freiem Speicherplatz vorhanden ist. Bei der Defragmentierung werden auch alle anderen Einschränkungen des Systems (etwa Affinität, Platzierungseinschränkungen und Knotenmetrikkapazität) respektiert.

## Resource Balancer-Clusterkonfiguration
Im Clustermanifest wird mit den folgenden Konfigurationswerten das allgemeine Verhalten des Features für das Packen von Metriken im Resource Balancer festgelegt:

### DefragmentationMetrics

Defragmentierungsmetriken sind Metriken, die vom Resource Balancer beim proaktiven Packen bzw. Defragmentieren berücksichtigt werden müssen. Alle konfigurierten Metriken müssen in dieser Liste angegeben werden (genau wie bei den Listen mit den Aktivitäts- und Ausgleichsschwellenwerten). Mit dem Wert „true“ angegebene Metriken werden als Defragmentierungsmetrik behandelt. Wenn die Metrik den Wert „false“ besitzt (oder nicht in der Liste angegeben ist), wird sie bei der Defragmentierung nicht berücksichtigt.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalancingThreshholds

Mit Ausgleichsschwellenwerten wird gesteuert, welchen Fragmentierungsgrad der Cluster für eine bestimmte Metrik aufweisen darf, bevor der Resource Balancer die Ausgleichsphase (mit der Logik für das Packen von Metriken) ausführt. Wenn die Metrik als Defragmentierungsmetrik behandelt wird, ist der Ausgleichsschwellenwert das minimale Verhältnis zwischen den maximal und minimal verwendeten Knoten pro Upgrade- oder Fehlerdomäne, die vom Resource Balancer vor Beginn der Defragmentierung im Cluster zugelassen werden. Wenn dieses Verhältnis für eine Upgrade- oder Fehlerdomäne kleiner als der Schwellenwert ist, beginnt die Defragmentierungsphase.

Die folgende Abbildung enthält zwei Beispiele, in denen der Ausgleichsschwellenwert für die Metrik den Wert 10 besitzt:

![Beispiele für Ausgleichsschwellenwerte][Image1]

Beachten Sie, dass in diesem Fall bei der Auslastung eines Knotens nicht die durch die Kapazität des Knotens bestimmte Knotengröße berücksichtigt wird. Stattdessen wird nur die absolute Nutzung berücksichtigt, die am Knoten momentan für die angegebene Metrik gemeldet wird.

Wenn die Metrik nicht angegeben ist, wird der Standardwert 1 verwendet. In diesem Fall wird die Defragmentierung durchgeführt, bis der Cluster in jeder Upgrade- und Fehlerdomäne mindestens einen leeren Knoten enthält.

Der Wert 0 bedeutet, dass diese Metrik während der Lastenausgleichsphase nicht berücksichtigt werden soll – ganz gleich, ob sie für die Defragmentierung berücksichtigt wird.

Das folgende Codebeispiel zeigt, dass metrikspezifische Ausgleichsschwellenwerte über das FabricSettings-Element im Clustermanifest konfiguriert werden:

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_1223_2015-->