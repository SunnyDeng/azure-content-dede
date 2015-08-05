<properties
   pageTitle="Resource Balancer-Clusterbeschreibung"
   description="Enthält Informationen zum Angeben einer Clusterbeschreibung für den Resource Balancer."
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

# Clusterbeschreibung

Der Service Fabric Resource Balancer bietet mehrere Mechanismen zum Beschreiben eines Clusters. Während der Laufzeit stellt der Resource Balancer mit diesen Informationen sicher, dass Dienste so platziert werden, dass für die hohe Verfügbarkeit der im Cluster ausgeführten Dienste gesorgt ist. Außerdem wird damit die maximale Auslastung der Clusterressourcen sichergestellt. Die Resource Balancer-Features, mit denen ein Cluster beschrieben wird, sind Fehlerdomänen, Upgradedomänen, Knoteneigenschaften und Knotenkapazitäten. Darüber hinaus verfügt der Resource Balancer über einige Konfigurationsoptionen zum Optimieren der Leistung.

## Wichtige Begriffe

### Fehlerdomänen:

Mit Fehlerdomänen können Clusteradministratoren die physischen Knoten definieren, die unter Umständen gleichzeitig ausfallen können, weil gemeinsame physische Abhängigkeiten bestehen, z. B. Strom- und Netzwerkquellen. Fehlerdomänen stellen in der Regel Hierarchien dar, die sich auf diese gemeinsamen Abhängigkeiten beziehen, wobei ggf. mehr Knoten zusammen ausfallen können, je höher der Punkt in der Domänenstruktur ist. In der folgenden Abbildung sind mehrere Knoten dargestellt, die über hierarchische Fehlerdomänen in der Reihenfolge Rechenzentrum, Rack und Blatt organisiert sind.

![Fehlerdomänen][Image1]

 Zur Laufzeit sieht sich der Service Fabric-Ressourcen-Manager die Fehlerdomänen im Cluster an und versucht, die Replikate für einen bestimmten Dienst so zu verteilen, dass sie sich jeweils in separaten Fehlerdomänen befinden. Mit diesem Prozess wird sichergestellt, dass die Verfügbarkeit des Diensts und sein Zustand bei einem Ausfall einer Fehlerdomäne nicht gefährdet werden. Die folgende Abbildung zeigt die Replikate eines Diensts, der über mehrere Fehlerdomänen verteilt ist, obwohl ausreichend Platz für eine Anordnung in ein oder zwei Domänen vorhanden wäre.

![Fehlerdomänen][Image2]

Fehlerdomänen werden im Clustermanifest konfiguriert. Jeder Knoten ist so definiert, dass er in einer bestimmten Fehlerdomäne liegt. Während der Laufzeit kombiniert der Ressourcen-Manager die Berichte aller Knoten, um eine vollständige Übersicht über alle Fehlerdomänen des Systems zu erstellen.

### Upgradedomänen

Upgradedomänen sind ebenfalls ein Informationselement, das vom Ressourcen-Manager verwendet wird. Upgradedomänen beschreiben – wie Fehlerdomänen auch – Gruppen von Knoten, die zur Durchführung von Upgrades ungefähr zur gleichen Zeit heruntergefahren werden. Upgradedomänen sind nicht hierarchisch und können als Tags angesehen werden.

Während Fehlerdomänen durch das physische Layout der Knoten im Cluster definiert sind, werden Upgradedomänen von Clusteradministratoren basierend auf Richtlinien festgelegt. Die Richtlinien beziehen sich auf Upgrades innerhalb des Clusters. Mehr Upgradedomänen führen zu präziseren Upgrades, indem die Auswirkung auf das ausgeführte Cluster und die Dienste begrenzt und verhindert wird, dass Ausfälle eine große Zahl von Diensten betreffen. Je nach den anderen Richtlinien, z. B. der Service Fabric-Wartezeit nach dem Aktualisieren einer Domäne, bevor Service Fabric zur nächsten Domäne wechselt, können mehr Upgradedomänen auch eine Verlängerung des Zeitraums bewirken, der zum Durchführen eines Upgrades im Cluster erforderlich ist.

Aus diesen Gründen sammelt der Ressourcen-Manager Informationen zu Upgradedomänen und verteilt Replikate auf die Upgradedomänen in einem Cluster, wie dies auch bei Fehlerdomänen der Fall ist. Upgradedomänen können 1:1-Entsprechungen von Fehlerdomänen sein, aber in der Regel ist dies nicht der Fall. In der folgenden Abbildung ist eine Ebene mit mehreren Upgradedomänen oberhalb der vorher definierten Fehlerdomänen zu sehen. Der Ressourcen-Manager verteilt die Replikate weiterhin auf die Domänen, damit keine Fehler- oder Upgradedomäne eine höhere Konzentration von Replikaten aufweist. So wird die hohe Verfügbarkeit des Diensts auch bei Upgrades oder Ausfällen im Cluster sichergestellt.

![Upgradedomänen][Image3]

Sowohl Upgradedomänen als auch Fehlerdomänen werden im Rahmen der Knotendefinition im Clustermanifest konfiguriert. Dies ist unten dargestellt:

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
- In Azure-Bereitstellungen werden Fehlerdomänen und Upgradedomänen von Azure zugewiesen. Daher enthält die Definition Ihrer Knoten und Rollen unter der Infrastrukturoption für Azure keine Informationen zu Fehler- oder Upgradedomänen.

### Knoteneigenschaften
Knoteneigenschaften sind benutzerdefinierte Schlüssel-Wert-Paare, mit denen für einen bestimmten Knoten zusätzliche Metadaten bereitgestellt werden. Beispiele für Knoteneigenschaften: Vorhandensein einer Festplatte oder Grafikkarte am Knoten, die Anzahl der Spindeln in der Festplatte, Kerne und andere physische Eigenschaften.

Knoteneigenschaften können auch verwendet werden, um abstraktere Eigenschaften anzugeben, die beim Platzieren von Richtlinienentscheidungen hilfreich sind. Mehreren Knoten innerhalb eines Clusters kann eine „Farbe“ zugewiesen werden, um das Cluster in unterschiedliche Abschnitte zu unterteilen. Im Codebeispiel wird veranschaulicht, dass Knoteneigenschaften für Knoten über das Clustermanifest als Teil der Knotentypdefinitionen festgelegt werden. Die Definitionen können dann auf mehrere Knoten im Cluster angewendet werden.

Die Platzierungseigenschaften NodeName, NodeType, FaultDomain und UpgradeDomain verfügen über Standardwerte. Service Fabric stellt automatisch Standardwerte für Sie bereit, damit Sie sie beim Erstellen Ihres Diensts verwenden können. Benutzer sollten keine eigenen Platzierungseigenschaften mit denselben Namen angeben.

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

Während der Laufzeit stellt der Resource Balancer mit den Informationen der Knoteneigenschaften sicher, dass Dienste, die bestimmte Funktionen erfordern, auf den geeigneten Knoten platziert werden.

### Knotenkapazitäten
Knotenkapazitäten sind Schlüssel-Wert-Paare, mit denen der Name und die Menge einer bestimmten Ressource definiert werden, die von einem bestimmten Knoten zur Verwendung bereitgestellt werden. Im Codebeispiel ist für einen Knoten angegeben, dass er über Kapazität für eine Metrik mit dem Namen „MemoryInMb“ verfügt und standardmäßig 2048 MB an verfügbarem Arbeitsspeicher aufweist. Kapazitäten werden, ähnlich wie Knoteneigenschaften, über das Clustermanifest definiert.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![Knotenkapazität][Image4]

Da auf einem Knoten ausgeführte Dienste ihre Kapazitätsanforderungen per Auslastungsbericht (Reporting Load) aktualisieren können, wird vom Resource Balancer auch regelmäßig überprüft, ob für einen Knoten die Kapazität seiner Metriken erreicht oder überschritten wurde. Wenn dies der Fall ist, kann der Resource Balancer Dienste auf weniger stark ausgelastete Knoten verschieben, um Ressourcenkonflikte zu verringern und die allgemeine Leistung und Auslastung zu verbessern.

Beachten Sie, dass eine bestimmte Metrik auch im Eigenschaftsabschnitt des Knotentyps aufgelistet sein kann. Es ist aber besser, sie als Kapazität aufzulisten, wenn es sich um eine Eigenschaft des Knotens handelt, die zur Laufzeit verwendet werden kann. Das zusätzliche Auflisten als Eigenschaft ermöglicht es einem Dienst, der von „Mindesthardwareanforderungen“ abhängig ist, den Knoten mit Platzierungseinschränkungen abzufragen. Dies kann erforderlich sein, wenn die Ressource zur Laufzeit von anderen Diensten genutzt wird. Wir raten Ihnen, auch die Einbindung als Kapazität durchzuführen, damit der Resource Balancer zusätzliche Aktionen ausführen kann.

Beim Erstellen neuer Dienste verwendet der Service Fabric Cluster Resource Balancer die Informationen zur Kapazität vorhandener Knoten und zur Verwendung vorhandener Dienste, um zu ermitteln, ob die Kapazität zum Platzieren des gesamten neuen Diensts ausreicht. Bei unzureichender Kapazität wird die Anforderung „Dienst erstellen“ mit einer Fehlermeldung abgelehnt und angegeben, dass nicht genügend Clusterkapazität verfügbar ist.


### Resource Balancer-Konfigurationen

Im Clustermanifest wird mit den folgenden unterschiedlichen Konfigurationswerten das allgemeine Resource Balancer-Verhalten festgelegt:

- Mit Ausgleichsschwellenwerten wird gesteuert, wie „unausgeglichen“ der Zustand des Clusters in Bezug auf eine bestimmte Metrik werden darf, bevor der Resource Balancer reagiert. Ein Ausgleichsschwellenwert ist das maximale Verhältnis zwischen den maximal verwendeten und minimal verwendeten Knoten, deren Vorhandensein der Resource Balancer zulässt, bevor die Aufteilung auf die Cluster neu durchgeführt wird.

Die folgende Abbildung enthält zwei Beispiele, bei denen der Ausgleichsschwellenwert für die Metrik den Wert 10 hat.

![Ausgleichsschwellenwert][Image5]

Beachten Sie, dass für die „Auslastung“ eines Knotens nicht die Größe des Knotens berücksichtigt wird, die anhand der Kapazität des Knotens bestimmt wurde. Es wird nur die absolute Nutzung berücksichtigt, die am Knoten für die angegebene Metrik gerade gemeldet wird.

Im Codebeispiel wird veranschaulicht, dass Ausgleichsschwellenwerte für Metriken pro Metrik über das FabricSettings-Element im Clustermanifest konfiguriert werden.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

- Aktivitätsschwellenwerte fungieren als Wächter dafür, wie häufig der Resource Balancer ausgeführt wird. Hierzu werden die Fälle begrenzt, in denen der Resource Balancer reagiert, wenn eine erhebliche absolute Auslastung vorhanden ist. Falls der Aufwand im Cluster für eine bestimmte Metrik nicht sehr hoch ist, wird der Resource Balancer hierbei auch dann nicht ausgeführt, wenn für diesen geringen Metrikbetrag eine starke Unausgeglichenheit im Cluster besteht. Mit dieser Maßnahme wird die Verschwendung von Ressourcen verhindert, weil für den Cluster kein Ausgleich stattfindet, wenn sich daraus nur ein geringer Vorteil ergibt. In der folgenden Abbildung ist zu sehen, dass der Ausgleichsschwellenwert für die Metrik 4 und der Aktivitätsschwellenwert 1536 beträgt.

![Aktivitätsschwellenwert][Image6] Beachten Sie auch, dass für eine Metrik sowohl der Aktivitätsschwellenwert als auch der Ausgleichsschwellenwert überschritten werden muss, damit der Resource Balancer ausgeführt wird. Wenn einer dieser Schwellenwerte für zwei separate Metriken erreicht wird, wird der Resource Balancer nicht ausgeführt.

Im Codebeispiel wird veranschaulicht, dass Aktivitätsschwellenwerte – wie Ausgleichsschwellenwerte auch – pro Metrik über das FabricSettings-Element im Clustermanifest konfiguriert werden.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval: Steuert, wie häufig der Resource Balancer die Informationen scannt, die auf Verletzungen der Einschränkungen überprüft werden müssen. Einschränkungsverletzungen sind beispielsweise nicht eingehaltene Platzierungseinschränkungen, Dienste mit einer geringeren Anzahl an Instanzen und Replikaten als erforderlich, Knoten mit Kapazitätsüberschreitungen für eine Metrik und überlastete Fehler- oder Upgradedomänen. Für Clusterdiskrepanzen werden die Ausgleichs- und Aktivitätsschwellenwerte sowie die aktuelle Ansicht der Auslastung von Knoten im Cluster genutzt. Das Aktualisierungsintervall wird in Sekunden angegeben, und der Standardwert ist 1. Die Häufigkeit der Einschränkungsüberprüfung und Platzierung kann alternativ auch mit zwei neuen Intervallen (MinConstraintCheckInterval und MinPlacementInterval) gesteuert werden. Wenn die neuen Parameter definiert werden, wird PLBRefreshInterval nicht verwendet und kann nicht definiert werden.

- MinConstraintCheckInterval: Steuert, wie häufig der Resource Balancer die Informationen scannt, die auf Verletzungen der Einschränkungen überprüft werden müssen. Einschränkungsverletzungen sind beispielsweise nicht eingehaltene Platzierungseinschränkungen, Dienste mit einer geringeren Anzahl an Instanzen und Replikaten als erforderlich, Knoten mit Kapazitätsüberschreitungen für eine Metrik und überlastete Fehler- oder Upgradedomänen. Für Clusterdiskrepanzen werden die Ausgleichs- und Aktivitätsschwellenwerte sowie die aktuelle Ansicht der Auslastung von Knoten im Cluster genutzt. Das Intervall für die Einschränkungsüberprüfung wird in Sekunden definiert. Wenn das Intervall für die Einschränkungsüberprüfung nicht festgelegt ist, ist der Standardwert gleich dem Wert von PLBRefreshInterval (beide Werte können nicht gleichzeitig angegeben werden).

- MinPlacementInterval: Steuert, wie häufig der Resource Balancer überprüft, ob neue zu platzierende Instanzen oder Replikate vorhanden sind, und versucht, die Platzierung durchzuführen. Das Platzierungsintervall wird in Sekunden definiert. Wenn das Platzierungsintervall nicht festgelegt ist, ist der Standardwert gleich dem Wert von PLBRefreshInterval (beide Werte können nicht gleichzeitig angegeben werden).

- MinLoadBalancingInterval: Gibt die Mindestdauer zwischen Resource Balancer-Ausführungen an. Wenn der Resource Balancer aufgrund der aus dem Scan während des letzten PLBRefreshInterval-Intervalls gewonnenen Informationen aktiv geworden ist, wird er für mindestens den gleichen Zeitraum nicht erneut ausgeführt. Der Wert wird in Sekunden definiert, und der Standardwert ist 5.

Beachten Sie, dass diese Zeiträume zwar relativ kurz sind, der Lastenausgleich im Allgemeinen im Cluster aber nur erfolgt, wenn die Ausgleichs- und Aktivitätsschwellenwerte für eine bestimmte Metrik erreicht werden. Im Codebeispiel wird Folgendes verdeutlicht: Wenn für einen bestimmten Cluster kein genauer und aktiver Lastenausgleich erforderlich ist, können diese Werte durch die folgende Konfiguration im FabricSettings-Element weniger eng gewählt werden. In dieser Beispielkonfiguration beträgt der zeitliche Mindestabstand zwischen zwei Einschränkungsüberprüfungen 10 Sekunden. Für die Platzierung lautet der Wert 5 Sekunden, und der Lastenausgleich wird alle 5 Minuten durchgeführt. Beachten Sie, dass PLBRefreshInterval in diesem Fall nicht definiert ist.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

Unten in der zweiten Beispielkonfiguration sind PLBRefreshInterval und MinLoadBalancingInterval festgelegt. Da PLBRefreshInterval auf 2 Sekunden festgelegt ist, wird der Wert sowohl für MinPlacementInterval als auch für MinConstraintCheckInterval ebenfalls auf 2 Sekunden festgelegt.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png
 

<!---HONumber=July15_HO4-->