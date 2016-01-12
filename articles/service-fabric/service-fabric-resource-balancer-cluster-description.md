<properties
   pageTitle="Beschreibung eines Resource Balancer-Clusters | Microsoft Azure"
   description="Beschreibung eines Service Fabric-Clusters durch Angabe von Fehlerdomänen, Upgradedomänen, Knoteneigenschaften und Knotenkapazitäten für Resource Balancer."
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

# Beschreiben eines Service Fabric-Clusters

Resource Balancer in Service Fabric bietet mehrere Mechanismen zum Beschreiben eines Clusters. Während der Laufzeit stellt Resource Balancer anhand dieser Informationen durch entsprechende Platzierung der Dienste die hohe Verfügbarkeit der im Cluster ausgeführten Dienste sowie eine optimale Auslastung der Clusterressourcen sicher. Die Resource Balancer-Features, mit denen ein Cluster beschrieben wird, sind Fehlerdomänen, Upgradedomänen, Knoteneigenschaften und Knotenkapazitäten. Darüber hinaus verfügt Resource Balancer über einige Konfigurationsoptionen zur Leistungsoptimierung.

## Wichtige Begriffe

### Fehlerdomänen

Mit Fehlerdomänen können Clusteradministratoren die physischen Knoten definieren, die unter Umständen gleichzeitig ausfallen können, weil gemeinsame physische Abhängigkeiten bestehen, z. B. Strom- und Netzwerkquellen. Fehlerdomänen stellen in der Regel Hierarchien dar, die sich auf diese gemeinsamen Abhängigkeiten beziehen, wobei ggf. mehr Knoten zusammen ausfallen können, je höher der Punkt in der Domänenstruktur ist. Die folgende Abbildung zeigt mehrere Knoten, die unter Verwendung hierarchischer Fehlerdomänen nach Rechenzentrum, Rack und Blatt strukturiert sind.

![Mithilfe von Fehlerdomänen strukturierte Knoten][Image1]

 Zur Laufzeit analysiert der Azure-Ressourcen-Manager die Fehlerdomänen im Cluster und versucht, die Replikate für einen bestimmten Dienst so zu verteilen, dass sie sich jeweils in separaten Fehlerdomänen befinden. Mit diesem Prozess wird sichergestellt, dass die Verfügbarkeit des Diensts und sein Zustand bei einem Ausfall einer Fehlerdomäne nicht gefährdet werden. Die folgende Abbildung zeigt die Replikate eines Diensts, die über mehrere Fehlerdomänen verteilt sind, obwohl ausreichend Platz für die Platzierung in einer oder zwei Domänen vorhanden wäre.

![Auf Fehlerdomänen verteilte Dienstreplikate][Image2]

Fehlerdomänen werden im Clustermanifest konfiguriert. Jeder Knoten ist so definiert, dass er in einer bestimmten Fehlerdomäne liegt. Während der Laufzeit kombiniert der Ressourcen-Manager die Berichte aller Knoten, um eine vollständige Übersicht über alle Fehlerdomänen des Systems zu erstellen.

### Upgradedomänen

Auch Upgradedomänen werden vom Ressourcen-Manager berücksichtigt. Upgradedomänen beschreiben – wie Fehlerdomänen auch – Gruppen von Knoten, die zur Durchführung von Upgrades ungefähr zur gleichen Zeit heruntergefahren werden. Upgradedomänen sind nicht hierarchisch und können als Tags angesehen werden.

Fehlerdomänen sind durch das physische Layout der Knoten im Cluster definiert. Upgradedomänen werden dagegen von Clusteradministratoren auf der Grundlage von Richtlinien festgelegt. Die Richtlinien beziehen sich auf Upgrades innerhalb des Clusters. Mehr Upgradedomänen führen zu präziseren Upgrades, da die Auswirkung auf den ausgeführten Cluster und die Dienste begrenzt und verhindert wird, dass Ausfälle eine große Zahl von Diensten betreffen. Abhängig von anderen Richtlinien (etwa der Zeit, die nach dem Upgraden einer Domäne gewartet wird, bis Service Fabric zur nächsten Domäne wechselt), können mehr Upgradedomänen auch die Durchführungsdauer eines Upgrades im Cluster verlängern.

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
> [AZURE.NOTE]In Azure-Bereitstellungen werden Fehlerdomänen und Upgradedomänen von Azure zugewiesen. Daher enthält die Definition Ihrer Knoten und Rollen unter der Infrastrukturoption für Azure keine Informationen zu Fehler- oder Upgradedomänen.

### Knoteneigenschaften
Knoteneigenschaften sind benutzerdefinierte Schlüssel-Wert-Paare, mit denen für einen bestimmten Knoten zusätzliche Metadaten bereitgestellt werden. Beispiele für Knoteneigenschaften: Vorhandensein einer Festplatte oder Grafikkarte am Knoten, Spindelanzahl der Festplatte, Kerne und andere physische Eigenschaften.

Knoteneigenschaften können auch verwendet werden, um abstraktere Eigenschaften anzugeben, die beim Platzieren von Richtlinienentscheidungen hilfreich sind. Mehreren Knoten innerhalb eines Clusters kann eine „Farbe“ zugewiesen werden, um das Cluster in unterschiedliche Abschnitte zu unterteilen. Im Codebeispiel wird veranschaulicht, dass Knoteneigenschaften für Knoten über das Clustermanifest als Teil der Knotentypdefinitionen festgelegt werden. Die Knoteneigenschaften können dann auf mehrere Knoten im Cluster angewendet werden.

Die Platzierungseigenschaften NodeName, NodeType, FaultDomain und UpgradeDomain verfügen über Standardwerte. Service Fabric stellt automatisch Standardwerte bereit, die Sie beim Erstellen Ihres Diensts verwenden können. Benutzer sollten keine eigenen Platzierungseigenschaften mit denselben Namen angeben.

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

Während der Laufzeit stellt Resource Balancer anhand der Knoteneigenschaften sicher, dass Dienste, die bestimmte Funktionen erfordern, auf den geeigneten Knoten platziert werden.

### Knotenkapazitäten
Knotenkapazitäten sind Schlüssel-Wert-Paare, mit denen der Name und die Menge einer bestimmten Ressource definiert werden, die von einem bestimmten Knoten zur Verwendung bereitgestellt werden. Das folgende Codebeispiel zeigt einen Knoten mit Kapazität für eine Metrik namens „MemoryInMB“ und standardmäßig 2.048 MB verfügbarem Arbeitsspeicher. Kapazitäten werden, ähnlich wie Knoteneigenschaften, über das Clustermanifest definiert.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![Knotenkapazität][Image4]

Da auf einem Knoten ausgeführte Dienste ihre Kapazitätsanforderungen per Auslastungsbericht aktualisieren können, wird von Resource Balancer auch regelmäßig überprüft, ob bei einem Knoten die Kapazitätsgrenze für seine Metriken erreicht oder überschritten wurde. Ist dies der Fall, kann Resource Balancer Dienste auf weniger stark ausgelastete Knoten verschieben, um Ressourcenkonflikte zu verringern und die allgemeine Leistung und Auslastung zu verbessern.

Beachten Sie, dass eine Metrik auch im Eigenschaftsabschnitt des Knotentyps aufgeführt sein kann. Es ist aber besser, sie als Kapazität aufzuführen, wenn es sich um eine Eigenschaft des Knotens handelt, die zur Laufzeit verwendet werden kann. Das zusätzliche Aufführen als Eigenschaft ermöglicht es einem Dienst, der von Mindesthardwareanforderungen abhängig ist, den Knoten mit Platzierungseinschränkungen abzufragen. Dies kann erforderlich sein, wenn die Ressource zur Laufzeit von anderen Diensten genutzt wird. Es empfiehlt sich, auch die Einbindung als Kapazität durchzuführen, damit Resource Balancer zusätzliche Aktionen ausführen kann.

Beim Erstellen neuer Dienste ermittelt Service Fabric Resource Balancer anhand der Informationen zur Kapazität vorhandener Knoten und der Informationen zur Verwendung vorhandener Dienste, ob die verfügbare Kapazität zum Platzieren des gesamten neuen Diensts ausreicht. Bei unzureichender Kapazität wird die Diensterstellungsanforderung mit einer entsprechenden Fehlermeldung abgelehnt.


### Resource Balancer-Konfigurationen

Im Clustermanifest wird mit den folgenden Konfigurationswerten das allgemeine Verhalten von Resource Balancer festgelegt:

Mit *Ausgleichsschwellenwerten* wird gesteuert, wie unausgeglichen der Zustand des Clusters bei einer bestimmten Metrik werden darf, bevor Resource Balancer reagiert. Ein Ausgleichsschwellenwert ist das maximal zulässige Verhältnis zwischen maximal und minimal genutzten Knoten, ab dem Resource Balancer die Clusterbalance nachbessert.

Die folgende Abbildung enthält zwei Beispiele, bei denen der Ausgleichsschwellenwert für die Metrik den Wert 10 hat.

![Ausgleichsschwellenwert][Image5]

Beachten Sie, dass für die Auslastung eines Knotens nicht die Größe des Knotens berücksichtigt wird, die anhand der Kapazität des Knotens ermittelt wurde. Es wird nur die absolute Nutzung berücksichtigt, die am Knoten momentan für die angegebene Metrik gemeldet wird.

Im folgenden Codebeispiel wird veranschaulicht, dass metrikspezifische Ausgleichsschwellenwerte über das FabricSettings-Element im Clustermanifest konfiguriert werden.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

*Aktivitätsschwellenwerte* dienen zum Steuern der Ausführungshäufigkeit von Resource Balancer. Hierzu werden die Fälle begrenzt, in denen Resource Balancer auf erhebliche absolute Auslastungen reagiert. Falls für eine bestimmte Metrik im Cluster also keine hohe Auslastung vorhanden ist, wird Resource Balancer auch dann nicht ausgeführt, wenn für diese geringe Metrikauslastung eine starke Unausgeglichenheit im Cluster besteht. Mit dieser Maßnahme wird die Verschwendung von Ressourcen verhindert, weil für den Cluster kein Ausgleich stattfindet, wenn sich daraus nur ein geringer Vorteil ergibt. In der folgenden Abbildung ist zu sehen, dass der Ausgleichsschwellenwert für die Metrik 4 und der Aktivitätsschwellenwert 1536 beträgt.

![Aktivitätsschwellenwert][Image6]

Beachten Sie auch, dass für eine Metrik sowohl der Aktivitätsschwellenwert als auch der Ausgleichsschwellenwert überschritten werden muss, damit Resource Balancer ausgeführt wird. Wenn einer der Schwellenwerte für zwei separate Metriken erreicht wird, wird Resource Balancer nicht ausgeführt.

Im folgenden Codebeispiel wird veranschaulicht, dass Aktivitätsschwellenwerte genau wie Ausgleichsschwellenwerte pro Metrik über das FabricSettings-Element im Clustermanifest konfiguriert werden.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval: Steuert, wie häufig Resource Balancer die Informationen scannt, die auf Einschränkungsverletzungen überprüft werden müssen. Einschränkungsverletzungen sind beispielsweise nicht eingehaltene Platzierungseinschränkungen, Dienste mit einer zu geringen Anzahl von Instanzen und Replikaten, Knoten mit Kapazitätsüberschreitungen für eine Metrik, überlastete Fehler- oder Upgradedomänen sowie Clusterdiskrepanzen. Resource Balancer zieht zur Identifizierung von Einschränkungsverletzungen die Ausgleichs- und Aktivitätsschwellenwerte sowie die aktuelle Auslastung der Knoten im Cluster heran. Das Aktualisierungsintervall wird in Sekunden angegeben, und der Standardwert ist 1. Die Häufigkeit der Einschränkungsüberprüfung und Platzierung kann alternativ auch mit zwei neuen Intervallen („MinConstraintCheckInterval“ und „MinPlacementInterval“) gesteuert werden. Wenn die neuen Parameter definiert werden, wird „PLBRefreshInterval“ nicht verwendet und kann nicht definiert werden.

- MinConstraintCheckInterval: Steuert, wie häufig Resource Balancer die Informationen scannt, die auf Einschränkungsverletzungen überprüft werden müssen. Einschränkungsverletzungen sind beispielsweise nicht eingehaltene Platzierungseinschränkungen, Dienste mit einer zu geringen Anzahl von Instanzen und Replikaten, Knoten mit Kapazitätsüberschreitungen für eine Metrik, überlastete Fehler- oder Upgradedomänen sowie Clusterdiskrepanzen. Resource Balancer zieht zur Identifizierung von Einschränkungsverletzungen die Ausgleichs- und Aktivitätsschwellenwerte sowie die aktuelle Auslastung der Knoten im Cluster heran. Das Intervall für die Einschränkungsüberprüfung wird in Sekunden definiert. Wenn das Intervall für die Einschränkungsüberprüfung nicht festgelegt wird, wird als Standardwert der Wert von „PLBRefreshInterval“ verwendet. (Die beiden Werte können nicht gleichzeitig angegeben werden.)

- MinPlacementInterval: Steuert, wie häufig Resource Balancer überprüft, ob neue zu platzierende Instanzen oder Replikate vorhanden sind, und versucht, die Platzierung durchzuführen. Das Platzierungsintervall wird in Sekunden definiert. Wenn das Platzierungsintervall nicht festgelegt wird, wird als Standardwert der Wert von „PLBRefreshInterval“ verwendet. (Die beiden Werte können nicht gleichzeitig angegeben werden.)

- MinLoadBalancingInterval: Gibt die Mindestdauer zwischen Resource Balancer-Ausführungen an. Wenn Resource Balancer aufgrund der Informationen aus dem Scan des letzten PLBRefreshInterval-Intervalls aktiv geworden ist, wird er für mindestens den gleichen Zeitraum nicht erneut ausgeführt. Der Wert wird in Sekunden definiert, und der Standardwert ist 5.

Beachten Sie, dass diese Zeiträume zwar relativ kurz sind, der Lastenausgleich im Cluster aber im Allgemeinen nur erfolgt, wenn die Ausgleichs- und Aktivitätsschwellenwerte für eine bestimmte Metrik erreicht werden. Wenn für einen bestimmten Cluster kein präziser und aktiver Lastenausgleich erforderlich ist, können diese Werte im FabricSettings-Element auch weniger aggressiv konfiguriert werden (wie im folgenden Codebeispiel zu sehen). In dieser Beispielkonfiguration beträgt der zeitliche Mindestabstand zwischen zwei Einschränkungsüberprüfungen zehn Sekunden. Für die Platzierung ist der Wert auf fünf Sekunden festgelegt, und der Lastenausgleich findet alle fünf Minuten statt. Beachten Sie, dass PLBRefreshInterval in diesem Fall nicht definiert ist.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

In der zweiten Beispielkonfiguration sind „PLBRefreshInterval“ und „MinLoadBalancingInterval“ festgelegt. Da PLBRefreshInterval auf 2 Sekunden festgelegt ist, wird der Wert sowohl für MinPlacementInterval als auch für MinConstraintCheckInterval ebenfalls auf 2 Sekunden festgelegt.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png

<!---HONumber=AcomDC_1223_2015-->