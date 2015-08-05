<properties
   pageTitle="Proaktives Packen von Metriken"
   description="Sie erhalten einen Überblick über das proaktive Packen von Metriken im Resource Balancer."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Proaktives Packen von Metriken

Eine häufig verwendete Konfiguration für den Service Fabric Resource Balancer hat das Ziel, für jede Metrik auf jedem Knoten die gleiche Auslastung zu erreichen. Der Resource Balancer hat außerdem die Aufgabe, einen Platz für einen Dienst zu finden, wenn neue Dienstanforderungen eingehen. Falls nicht genügend freier Speicherplatz zum Platzieren neuer Dienstreplikate vorhanden ist (alle Replikate aller Dienstpartitionen), versucht der Resource Balancer, dafür Speicherplatz zu schaffen, indem vorhandene Workloads anders verteilt werden. Dies ist zwar ein völlig normaler Vorgang, aber er kann relativ viel Zeit in Anspruch nehmen. Dies hängt davon ab, wie voll der Cluster ist und welcher Fragmentierungsgrad für die Workload besteht.

Wenn der Cluster relativ stark ausgelastet ist und der Kunde einen neuen Dienst mit einer hohen Standardauslastung hinzufügen möchte (z. B. maximale Knotenkapazität für eine oder mehrere Metriken), muss der Resource Balancer unter Umständen viele Replikate verschieben, um den neuen Dienst platzieren zu können. Wenn Dienste zustandsbehaftet und groß sind, kann das Ausführen der erforderlichen Verschiebungen einige Zeit in Anspruch nehmen, weil Daten kopiert werden müssen. Diese beiden Aspekte können zu einer Verlängerung der Diensterstellungsdauer führen. Im Allgemeinen sollten Dienste gelegentliche längere Erstellungsdauern zwar tolerieren, aber einige Workloads sind weniger tolerant und verlangen eine schnellstmögliche Erstellung. Dies bedeutet, dass der Resource Balancer im stabilen Zustand sicherstellen muss, dass das Cluster „defragmentiert“ ist. Dies erhöht die Chance, dass genügend Platz für neue Workloads vorhanden ist.

Der Mechanismus zum proaktiven Packen von Metriken (Defragmentierung) wird als Teil der Resource Balancer-Ausgleichsphase mit dem Ziel ausgeführt, die Dauer der Diensterstellung zu verringern. Hierzu werden Workloads auf weniger Knoten angeordnet, anstatt sie wie beim Lastenausgleich weiträumiger zu verteilen. Wenn eine Metrik für die Defragmentierung konfiguriert ist, strebt der Resource Balancer die maximale durchschnittliche Standardabweichung an, und nicht die minimale durchschnittliche Standardabweichung wie beim Lastenausgleich.

Bei der maximalen Abweichung versucht der Resource Balancer, auf einigen Knoten so viele Dienste wie möglich zu platzieren, während gleichzeitig so viele Knoten wie möglich leer gelassen werden. Eine der grundlegenden Einschränkungen für das Platzieren neuer Dienste lautet zudem, dass Replikate sich nicht in der derselben Upgradedomäne oder Fehlerdomäne befinden können. Da das Ziel das schnelle Hinzufügen neuer Dienste ist, sollte der Resource Balancer die minimale Standardabweichung der Lastenverteilung zwischen Upgradedomänen und Fehlerdomänen anstreben (Summe der Dienstauslastungen pro Upgradedomäne/Fehlerdomäne). Das Ergebnis ist, dass pro Upgradedomäne/Fehlerdomäne die gleiche Menge an freiem Speicherplatz vorhanden ist. Bei der Defragmentierung werden auch alle anderen Einschränkungen im System respektiert, z. B. Affinität, Platzierungseinschränkungen und Knotenmetrikkapazität.

## Resource Balancer-Clusterkonfiguration
Im Clustermanifest wird mit den folgenden unterschiedlichen Konfigurationswerten das allgemeine Verhalten der Funktion für das Packen von Metriken im Resource Balancer festgelegt:

### DefragmentationMetrics: Metriken, die vom Resource Balancer für das proaktive Packen bzw. die Defragmentierung berücksichtigt werden sollten.

Alle konfigurierten Metriken sollten in dieser Liste angegeben werden (wie in den Listen mit den Aktivitäts- und Ausgleichsschwellenwerten). Wenn diese Metrik mit dem Wert „true“ angegeben wird, wird sie als Defragmentierungsmetrik behandelt. Mit dem Wert „false“ (oder bei fehlender Angabe in der Liste) wird sie für die Defragmentierung nicht berücksichtigt.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Mit Ausgleichsschwellenwerten wird gesteuert, welchen Fragmentierungsgrad der Cluster für eine bestimmte Metrik aufweisen darf, bevor der Resource Balancer mit der Ausführung der Ausgleichsphase beginnt (mit der Logik für das Packen von Metriken). Wenn die Metrik als Defragmentierungsmetrik berücksichtigt wird, ist der Ausgleichsschwellenwert das minimale Verhältnis zwischen den maximal und minimal verwendeten Knoten pro Upgrade- oder Fehlerdomäne, die vom Resource Balancer vor Beginn der Defragmentierung im Cluster zugelassen werden. Wenn dieser Wert für eine Upgrade- oder Fehlerdomäne kleiner als der Schwellenwert ist, beginnt die Defragmentierungsphase.

Die folgende Abbildung enthält zwei Beispiele, bei denen der Ausgleichsschwellenwert für die Metrik den Wert 10 hat.

![Ausgleichsschwellenwert][Image1]

Beachten Sie, dass für die „Auslastung“ eines Knotens nicht die Größe des Knotens berücksichtigt wird, die anhand der Kapazität des Knotens bestimmt wurde. Es wird nur die absolute Nutzung berücksichtigt, die am Knoten für die angegebene Metrik gerade gemeldet wird.

Wenn die Metrik nicht angegeben ist, ist der Standardwert 1. In diesem Fall wird die Defragmentierung durchgeführt, bis der Cluster in jeder Upgradedomäne und Fehlerdomäne mindestens einen leeren Knoten enthält.

Der Wert 0 bedeutet, dass diese Metrik während der Lastenausgleichsphase nicht berücksichtigt werden soll – ob sie für die Defragmentierung berücksichtigt wird oder nicht.

Im Codebeispiel wird veranschaulicht, dass Ausgleichsschwellenwerte für Metriken pro Metrik über das FabricSettings-Element im Clustermanifest konfiguriert werden.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png
 

<!---HONumber=July15_HO4-->