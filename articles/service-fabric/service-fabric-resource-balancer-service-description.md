<properties
   pageTitle="Resource Balancer-Dienstbeschreibungen"
   description="Sie erhalten einen Überblick über das Konfigurieren von Dienstbeschreibungen, die vom Resource Balancer verwendet werden können."
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

# Dienstbeschreibung – Übersicht

## Platzierungseinschränkungen
[Platzierungseinschränkungen](../service-fabric-placement-constraint) sind für einen Dienst der Mechanismus, mit dem eine bestimmte Dienstinstanz die erforderlichen Knoteneigenschaften auswählt. Wie bei Knoteneigenschaften auch, sind dies Schlüssel-Wert-Paare, mit denen der Eigenschaftsname und die Anforderungen des Diensts für den Wert beschrieben werden. Einzelne Anweisungen können per einfacher boolescher Logik gruppiert werden, um die erforderliche Einschränkung zu erstellen. Beachten Sie, dass der Service Fabric Resource Balancer die Einschränkungen interpretiert.

Platzierungseinschränkungen für Dienste können entweder per Dienst- oder Anwendungsmanifest oder direkt im Code definiert werden.

Über das Dienstmanifest werden die ServiceTypes-Definitionen bereitgestellt.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

Das Anwendungsmanifest enthält die ServiceTemplates- oder DefaultServices-Definitionen.

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Im folgenden Diagramm ist dargestellt, dass im Cluster mit den Knoten der einzige gültige Knoten für die Platzierung dieses Diensts der Knoten N5 ist.

![Platzierungseinschränkungen][Image1]

Wenn der Dienst eine Einschränkung für eine Eigenschaft angibt, die für einen bestimmten Knoten nicht definiert ist, wird dieser Knoten unabhängig vom angegebenen Wert nicht als Übereinstimmung angesehen.

## Dienstaffinität
Die Dienstaffinität ist eine spezielle Form der Platzierungseinschränkung. Sie ermöglicht die Beschreibung unterschiedlicher Dienste, die aus unterschiedlichen Gründen lokale Abhängigkeiten voneinander aufweisen. Dies bedeutet, dass beide Dienste auf demselben Knoten ausgeführt werden müssen, damit sie funktionieren. Diese Art der Architektur wird für Service Fabric-Anwendungen wahrscheinlich nicht sehr häufig verwendet werden. Für Service Fabric wird aber erkannt, dass es eine mögliche Übergangsarchitektur für bestimmte Arten von Legacyanwendungen sein kann, sodass diese Funktion zur Verfügung gestellt wird.

Mit dem Einrichten der Dienstaffinität zwischen zwei Diensten wird sichergestellt, dass die primären Replikate dieser Dienste immer auf demselben Knoten zusammengefasst sind.

Affinitätsbeziehungen zeichnen sich durch eine Hierarchie mit übergeordneten und untergeordneten Elementen aus. Der zuerst eingerichtete Dienst ist das übergeordnete Element der Beziehung, und der danach eingerichtete Dienst ist das untergeordnete Element. Bei der Beziehung handelt es sich um eine so genannte „harte“ Einschränkung.

Für die Dienstaffinität gelten derzeit die folgenden Einschränkungen:

- Sie kann nicht übergreifend für zustandslose und zustandsbehaftete Dienste verwendet werden.
- Sie kann nicht übergreifend für zustandslose Dienste mit unterschiedlicher Instanzanzahl verwendet werden. Beispielsweise sollten beide zustandslosen Dienste die gleiche InstanceCount-Eigenschaft aufweisen, wenn sie erstellt werden.
- Sie kann nicht übergreifend für zustandsbehaftete temporäre oder dauerhafte Dienste mit unterschiedlicher Replikatanzahl verwendet werden. Beispielsweise müssen für beide Dienste die gleichen ReplicaSetSizes-Werte („Target“ und „Min“) angegeben werden.
- Sie kann nicht mit partitionierten Diensten verwendet werden. Jeder Dienst muss über ein FABRIC\_PARTITION\_SCHEME\_SINGLETON-Partitionsschema verfügen.
- Affinitätsbeziehungen werden – wie andere Eigenschaften der Dienstbeschreibung auch – festgelegt, wenn der Dienst erstellt wird, und können nicht geändert werden.
- Dienstketten sind nicht zulässig. Wenn mehrere Dienste in eine Affinitätsbeziehung eingebunden werden müssen, muss ein „Sternmodell“ verwendet werden.

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

Das Codebeispiel zeigt die alternative Verwendung von DefaultServices-Definitionen.

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

Im Codebeispiel wird veranschaulicht, wie Sie eine Affinitätsbeziehung erstellen, nachdem die enthaltende Anwendung erstellt wurde.

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Metriken für den Lastenausgleich von Ressourcen
Die Platzierungseinschränkungen des Diensts und die Knoteneigenschaften stellen die harten Regeln und Richtlinien dar, mit denen beschrieben wird, welche Anordnungen der Dienste gültig sind. Die Metriken für den Lastenausgleich von Ressourcen sind aber im Allgemeinen hilfreich, um die optimale Anordnung der Dienste im Cluster zu beschreiben. Die vom Dienst definierten Metriken verfügen über mehrere unterschiedliche Eigenschaften, die beschreiben, wie sie im Cluster verwendet werden.

### Verwenden von Standardmetriken
Als Erstes muss verstanden werden, dass der Umgang mit Ressourcenmetriken für Dienste absolut optional ist und dass der Service Fabric Resource Balancer standardmäßig einige „integrierte“ Systemmetriken nutzt. Das Ziel dabei ist die Durchführung eines einfachen Lastenausgleichs für Ressourcen, ohne dass Administratoren von Diensten oder Clustern aktiv werden müssen. Die Standardmetriken, die vom Service Fabric Resource Balancer im Cluster nachverfolgt und ausgeglichen werden, sind PrimaryCount, ReplicaCount und Count. PrimaryCount ist die Anzahl von primären zustandsbehafteten Dienstreplikaten auf einem Knoten. ReplicaCount ist die Anzahl aller zustandsbehafteten Replikate auf einem Knoten. Count ist die Gesamtzahl aller Dienstobjekte auf dem Knoten, einschließlich zustandslose und zustandsbehaftete Dienste. Generell wird die PrimaryCount-Metrik als am wichtigsten angesehen und weist daher die höchste Gewichtung auf. Dies ist im folgenden Abschnitt beschrieben. Auf die PrimaryCount-Metrik folgt die ReplicaCount-Metrik und danach die Count-Metrik.

Das folgende Diagramm enthält ein Beispiel für einen standardmäßigen Lastenausgleich für drei zustandsbehaftete Dienste mit jeweils drei Replikaten.

![Standardmetriken][Image2]

Im Allgemeinen reichen die Standardmetriken für viele Dienste aus und sollten verwendet werden, sofern keine spezielle Anforderung für zusätzliche Metriken oder Funktionen gilt.

### Definieren von benutzerdefinierten Metriken
Wenn die Standardmetriken für einen bestimmten Dienst nicht ausreichen oder der Dienst über bekannte Anforderungen in Bezug auf die Anforderungen einer bestimmten Ressource verfügt, z. B. Festplattenspeicher oder Arbeitsspeicher, sollten benutzerdefinierte Metriken verwendet werden. Benutzerdefinierte Metriken sind nützlich, wenn ein Dienst eine oder mehrere Ressourcen aufweist, die zur Verhinderung von Konflikten optimal ausgeglichen sein müssen. Die Verwendung dieser Metriken kann zwischen Replikaten im Cluster erheblich variieren. Beispielsweise kann ein primäres Element 100 % der Metrik nutzen, während ein anderes Element nur 20 % davon nutzt. Es kann auch nützlich sein, benutzerdefinierte Metriken zum Erfassen von Ressourcen zu verwenden, die auf einem Computer stark eingeschränkt sind, z. B. Arbeitsspeicher, Festplattenspeicher oder Verbindungen. Es können auch zusätzliche benutzerdefinierte Metriken erstellt werden, mit denen Metriken ohne obere Grenzwerte für Knoten erfasst oder dargestellt werden, aber mit denen die Arbeit und der Ressourcenverbrauch aus Sicht des Diensts dargestellt werden. Dies kann beispielsweise die Metrik für die „derzeit ausstehenden Transaktionen“ sein. Auch wenn für die Metrik vielleicht keine Beschränkung in Bezug auf die „Kapazität“ auf einem Knoten besteht, ergibt sich trotzdem ein Leistungsvorteil. Hierzu wird sichergestellt, dass die Metrik gleichmäßig im Cluster verteilt ist.

Beachten Sie Folgendes: Wenn ein Dienst eine benutzerdefinierte Metrik definiert, werden vom Dienst nicht die standardmäßigen Metriken des Systems verwendet. Die Standardmetriken können weiterhin verwendet werden, aber sie müssen bei der Erstellung explizit neu in die Liste der Metriken für den Dienst eingebunden werden.

### Metriknamen
Benutzerdefinierte Metriken können erstellt werden, indem Sie beim Erstellen eines Diensts einfach einen Metriknamen festlegen. Beachten Sie, dass der Service Fabric Resource Balancer Metriken über ihren Namen zuordnet. Wenn die Metrik also als Kapazität in Knotendefinitionen oder dienstübergreifend verwendet wird, müssen die Metriknamen genau übereinstimmen, damit der Service Fabric Resource Balancer darauf Bezug nehmen kann.

### Metrikgewichtungen
Wenn über einen Dienst mehrere Metriken definiert werden, ist es auch nützlich, die Metrikgewichtungen für diese Metriken zu definieren. Mit unterschiedlichen Metrikgewichtungen wird dem Service Fabric Resource Balancer mitgeteilt, welche Metrik für die Funktion des Diensts wichtiger ist. Eine In-Memory-Warteschlange kann von der Netzwerkbandbreite beeinträchtigt werden, aber die größte Einschränkung ist wahrscheinlich die tatsächliche Speicherauslastung auf dem Knoten. Die Warteschlange kann zwar mehrere Metriken haben, aber die Metrik für die Arbeitsspeicherauslastung hat die höchste Gewichtung. Ebenso ist eine dauerhafte Datenbank wahrscheinlich sowohl vom Arbeitsspeicher als auch von der Festplattenauslastung abhängig. Aber während ein eingeschränkter Arbeitsspeicher die Fähigkeit zur Verarbeitung komplexer Abfragen verringert, verhindert fehlender Speicherplatz weitere Speichervorgänge, was meist ein schwerwiegenderes Problem ist. Aus diesem Grund wird für den dauerhaften Speicher meist der Festplattenspeicher als die Metrik mit der höheren Gewichtung ausgewählt.

Die Gewichtung wird im Service Fabric Resource Balancer praktisch nur verwendet, wenn für eine Reihe von Metriken kein vollständiger Ausgleich erzielt werden kann. Dann müssen Lösungen gefunden werden, bei denen eine Metrik weniger stark ausgeglichen als eine andere Metrik ist. In diesem Fall hat der Ausgleich der Metrik mit der geringeren Gewichtung eine niedrigere Priorität als die Metrik mit der höheren Gewichtung. Daher werden vom Service Fabric Resource Balancer Vorschläge gemacht, bei denen der Balancer der Metrik mit der höheren Gewichtung bevorzugt behandelt wird.

Wenn der Service Fabric Resource Balancer erkennt, dass für mehrere Dienste dem Namen nach dieselbe Metrik definiert wurde, werden diese Metriken als ein und dieselbe Metrik angesehen. Falls die Metrik identisch ist, die Gewichtungen sich aber unterscheiden, bildet der Resource Balancer den Mittelwert aus den angegebenen Gewichtungen. So werden die tatsächlichen Gewichtungen ermittelt, die zur Laufzeit verwendet werden. Daher gilt Folgendes: Wenn ein Dienst die Gewichtung 0 definiert, um anzugeben, dass kein Lastenausgleich erforderlich ist, und ein anderer Dienst für die gleiche Metrik die Gewichtung „Hoch“ definiert, liegt die tatsächliche Gewichtung dazwischen.

![Metrikgewichtungen][Image3]

Dieses Beispiel zeigt, wie zwei unterschiedliche Gewichtungsszenarien für Metriken zu zwei verschiedenen Lastenausgleichsergebnissen führen, bei denen die Metrik mit der höheren Priorität einen besseren allgemeinen Ausgleich aufweist. Im Beispiel auf der linken Seite ist B besser ausgeglichen, während auf der rechten Seite A besser ausgeglichen ist. Da in diesem Beispiel keine Lastenausgleichslösung enthalten ist, die einen Ausgleich beider Metriken bewirkt, bestimmt der Service Fabric Resource Balancer anhand der Gewichtung der Metriken, welche wichtiger ist. So kann ermittelt werden, welche Metrik beim Lastenausgleich bevorzugt werden soll.

Beachten Sie, dass die Metrikgewichtung 0 für Fälle verfügbar ist, in denen ein Dienst eine Metrik nachverfolgt, aber kein auf dieser Metrik basierender Lastenausgleich erforderlich ist. Stellen Sie sich beispielsweise eine Metrik vor, für die auf mehreren Knoten explizite Einschränkungen bestehen, die „gleichmäßige Auslastung über Knoten hinweg“ – also das eigentliche Ziel des Lastenausgleichs für Ressourcen – aber unwichtig ist. Für diese Arten von Metriken, die keinen Lastenausgleich erfordern, sondern nur die Durchsetzung von festgelegten Grenzwerten auf Knoten, kann die Ausgleichsgewichtung 0 definiert werden. Zur Laufzeit setzt der Service Fabric Resource Balancer die Kapazität durch. Er versucht aber auch dann nicht, die Metrik proaktiv auszugleichen, wenn für diese Metrik über die Knoten hinweg eine starke Unausgeglichenheit besteht.

### Standardauslastung von Metriken für eine primäre oder sekundäre Rolle
Beim Definieren einer Metrik für einen Dienst geben Sie die erwartete Verwendung für die Metrik für den Fall an, dass sich der Dienst entweder in einer primären oder sekundären Rolle befindet. Diese Informationen helfen dem Resource Balancer nicht nur beim anfänglichen effizienten Platzieren des Diensts, sondern können auch als gute Annäherung an die tatsächliche Nutzung der Metriken durch den Dienst dienen, mit denen er über seine gesamte Lebensdauer verknüpft ist. Der Service Fabric Resource Balancer berücksichtigt diese Metrikverwendung automatisch. Nicht nur beim Platzieren des Diensts, sondern bei allen erforderlichen Verschiebungen von Replikaten aufgrund des Lastenausgleichs oder anderer Änderungen im Cluster. Wenn die Auslastung eines Diensts relativ gut vorhersagbar und stabil ist, bedeutet dies Folgendes: Durch das Festlegen dieser Werte kann für den Dienst vermieden werden, dass dieser die Auslastung zur Laufzeit melden muss.

Das folgende Codebeispiel enthält einen Dienst, für den zwei benutzerdefinierte Metriken vollständig definiert werden – eine für die Arbeitsspeicherauslastung und die andere für den Festplattenspeicher.

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Im Codebeispiel werden ServiceTypes-Definitionen veranschaulicht, die über das Dienstmanifest erfolgen.

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

Im Codebeispiel werden ServiceTemplates-Definitionen veranschaulicht, die über das Anwendungsmanifest erfolgen.

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
Im Codebeispiel werden DefaultServices-Definitionen veranschaulicht, die über das Anwendungsmanifest erfolgen. ``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

Da die standardmäßigen Auslastungswerte nur aktualisiert werden, wenn für Dienste das Melden der Auslastung zur Laufzeit speziell per Code festgelegt wird, können die Werte auch zum Einrichten eines Lastenausgleichmodells für Ressourcen verwendet werden, das eher den Typ „ Kapazitätsreservierung “ aufweist. Wenn Workloads beispielsweise generell in mehrere Größenbereiche fallen und jederzeit eine bekannte Anzahl von Arbeitseinheiten auf einem Knoten platziert werden kann, kann eine benutzerdefinierte Metrik mit „Einheiten“ erstellt werden. Sowohl die Knotenkapazität als auch die standardmäßige Dienstauslastung werden dann als Einheiten definiert.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md), [Platzierungseinschränkungen](service-fabric-placement-constraint.md)

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png
 

<!---HONumber=August15_HO6-->