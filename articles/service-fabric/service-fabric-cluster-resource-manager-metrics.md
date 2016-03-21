<properties
   pageTitle="Verwalten von Metriken mit dem Cluster-Resource Manager von Azure Service Fabric"
   description="Enthält Informationen zum Konfigurieren und Verwenden von Metriken in Service Fabric."
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

# Metriken
„Metriken“ ist in Service Fabric die generische Bezeichnung für die Ressourcen, die für Ihre Dienste wichtig sind. Im Allgemeinen sind mit einer Metrik alle Dinge gemeint, die Sie in Bezug auf eine Ressource verwalten möchten, um die Leistung Ihrer Dienste zu steuern.

In allen obigen Beispielen wurde implizit auf Metriken verwiesen, z. B. Arbeitsspeicher, Datenträger, CPU-Auslastung. All dies sind Beispiele für Metriken. Es handelt sich hierbei um physische Metriken, also Ressourcen, die physischen Ressourcen auf dem Knoten entsprechen und verwaltet werden müssen. Metriken können auch logische Metriken sein, z. B. „MyWorkQueueDepth“, die anwendungsdefiniert sind und für die eine bestimmte Ebene des Ressourcenverbrauchs gilt (wobei die Anwendung darüber aber keine eindeutigen Informationen besitzt und nicht weiß, wie diese gemessen wird).

## Standardmetriken
Angenommen, Sie möchten die ersten Schritte ausführen und wissen nicht, welche Ressourcen genutzt werden bzw. welche Ressourcen wichtig sein werden. Sie führen also die Implementierung durch und erstellen die Dienste, ohne Metriken anzugeben. Das ist kein Problem. Wir wählen einige Metriken für Sie aus. Die Standardmetriken, die wir derzeit für Sie verwenden, wenn Sie keine eigenen Metriken angeben, haben die Bezeichnungen „PrimaryCount“, „ReplicaCount“ und (zugegebenermaßen etwas vage) „Count“. In der folgenden Tabelle ist angegeben, welche Auslastung für diese Metriken standardmäßig nachverfolgt wird:

| Metrik | Auslastung für zustandslose Instanz |	Zustandsbehaftete sekundäre Auslastung |	Zustandsbehaftete primäre Auslastung |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Count |	1 |	1 |	1 |

Was erhalten Sie bei diesen Standardmetriken? Für einfache Workloads erhalten Sie eine ziemlich gute Verteilung der Arbeit. Im Beispiel unten wird veranschaulicht, was passiert, wenn wir einen zustandsbehafteten Dienst mit drei Partitionen und einer Größe der Zielreplikatgruppe von „3“ erstellen. Außerdem erstellen wir einen einzelnen zustandslosen Dienst mit einer Instanzanzahl von 3. Das Ergebnis ist wie folgt:

![Clusterlayout mit Standardmetriken][Image1]

In diesem Beispiel ist Folgendes zu beobachten:
-	Primäre Replikate für den zustandsbehafteten Dienst sind nicht auf einem einzelnen Knoten gestapelt.
-	Die Replikate einer Partition befinden sich nicht auf demselben Knoten.
-	Die primären und sekundären Replikate sind im Cluster gut verteilt.
-	Die Dienstobjekte (zustandslos und zustandsbehaftet) sind gleichmäßig auf die einzelnen Knoten verteilt.

Das ist gut!

Dies funktioniert hervorragend, bis Sie anfangen, sich näher hiermit zu beschäftigen, und sich folgende Frage stellen: Wie hoch ist eigentlich die Wahrscheinlichkeit, dass alle primären Replikate meiner unterschiedlichen Dienste jetzt gerade die gleiche Auslastung aufweisen? Wie hoch ist in Verbindung hiermit die Wahrscheinlichkeit, dass die Auslastung für einen bestimmten Dienst immer konstant bleibt? Es wird klar, dass die Wahrscheinlichkeit für größere Workloads hierfür ziemlich niedrig ist.

Realistisch gesehen können Sie die Standardmetriken bzw. statische benutzerdefinierte Metriken verwenden. Dies bedeutet aber, dass Ihre Clusterauslastung geringer als gewünscht ist (da die Berichterstellung nicht adaptiv ist). Im schlimmsten Fall kann dies auch zu überlasteten Knoten und somit zu Leistungseinbußen führen. Besser geeignet sind benutzerdefinierte Metriken und Berichte zur dynamischen Auslastung. Somit gelangen wir zu den nächsten Abschnitten: „Benutzerdefinierte Metriken“ und „Dynamische Auslastungen“.

## Benutzerdefinierte Metriken
Es wurde bereits beschrieben, dass es sowohl physische als auch logische Metriken gibt und dass Sie Ihre eigenen Metriken definieren können. Wie sollen Sie dabei vorgehen? Der Ablauf ist sehr einfach. Konfigurieren Sie die Metrik und die standardmäßige Anfangsauslastung einfach beim Erstellen des Diensts. Damit sind Sie schon fertig! Sie können beim Erstellen des Diensts jede beliebige Gruppe von Metriken und Standardwerten pro Dienstinstanz konfigurieren.

Beachten Sie Folgendes: Wenn Sie mit dem Definieren von benutzerdefinierten Metriken beginnen, müssen Sie die Standardmetriken explizit wieder hinzufügen, wenn Sie möchten, dass diese von uns ebenfalls zum Ausbalancieren Ihres Diensts verwendet werden sollen. Der Grund ist, dass Sie sich über die Beziehung zwischen Standardmetriken und Ihren benutzerdefinierten Metriken im Klaren sein sollen. Es kann beispielsweise sein, dass die Arbeitsspeicherauslastung für Sie deutlich wichtiger als die Verteilung der primären Replikate ist, oder vielleicht verfügen Sie über mehrere Metriken, die Sie mit den Standardmetriken mischen möchten.

Angenommen, Sie möchten einen Dienst konfigurieren, von dem eine Metrik mit der Bezeichnung „Arbeitsspeicher“ gemeldet wird (zusätzlich zu den Standardmetriken). Sie haben einige grundlegende Messungen für den Arbeitsspeicher durchgeführt und ermittelt, dass für ein primäres Replikat des Diensts 20 MB Arbeitsspeicher verbraucht werden, während für sekundäre Replikate desselben Diensts 5 MB benötigt werden. Sie wissen, dass „Arbeitsspeicher“ die wichtigste Metrik in Bezug auf die Verwaltung der Leistung dieses Diensts ist. Sie möchten aber trotzdem erreichen, dass primäre Replikate gleichmäßig verteilt werden, damit beim Verlust eines Knotens oder einer Fehlerdomäne nicht eine übermäßig hohe Zahl von primären Replikaten ebenfalls verloren geht. Ansonsten verwenden Sie die Standardmetriken.

Gehen Sie wie folgt vor:

Code:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

ServiceLoadMetricDescription primaryCountMetric = new ServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

ServiceLoadMetricDescription replicaCountMetric = new ServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

ServiceLoadMetricDescription totalCountMetric = new ServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Zur Erinnerung: Falls Sie nur die Standardmetriken verwenden möchten, müssen Sie die Sammlung mit den Metriken nicht verwenden.)

Nachdem Sie erfahren haben, wie Sie Ihre eigenen Metriken definieren, geht es nun um die unterschiedlichen Eigenschaften, die Metriken aufweisen können. Wir haben sie Ihnen bereits gezeigt, aber jetzt wird beschrieben, was sie genau bedeuten. Derzeit kann eine Metrik vier verschiedene Eigenschaften haben:

-	Metrikname: Dies ist der Name der Metrik. Es handelt sich hierbei um einen eindeutigen Bezeichner für die Metrik im Cluster aus Resource Manager-Sicht.
-	PrimaryDefaultLoad: Dies ist die standardmäßige Auslastungsmenge, die für den Dienst für diese Metrik als primäres Replikat anfällt.
-	SecondaryDefaultLoad: Dies ist die standardmäßige Auslastungsmenge, die für den Dienst für diese Metrik als sekundäres Replikat anfällt.
-	Gewichtung: Gibt an, wie wichtig die Metrik relativ zu den anderen konfigurierten Metriken dieses Diensts ist.

## Last
Die Last oder auch Auslastung ist eine allgemeine Angabe, welcher Anteil einer bestimmten Metrik von einer Dienstinstanz oder einem Replikat auf einem bestimmten Knoten verbraucht wird.

### Standardauslastung
Die Standardauslastung gibt an, welche Auslastung der Resource Manager für die einzelnen Dienstinstanzen oder Replikate des Diensts ansetzen soll, bis Updates von tatsächlichen Dienstinstanzen oder Replikaten eingehen. Für Dienste mit einfachem Aufbau wird dies zu einer statischen Definition, die niemals dynamisch aktualisiert und somit während der gesamten Lebensdauer des Diensts verwendet wird. Dies eignet sich gut für eine einfache Kapazitätsplanung, weil es genau die Vorgehensweise ist, die wir kennen: die Zuordnung bestimmter Ressourcen zu bestimmten Workloads. Der Vorteil besteht aber darin, dass wir uns jetzt im Bereich der Microservices befinden. Hierbei werden Ressourcen nicht statisch bestimmten Workloads zugewiesen, und der Mensch ist nicht am Treffen der Entscheidungen beteiligt.

Wir lassen es zu, dass zustandsbehaftete Dienste die Standardauslastung für ihre primären und sekundären Replikate angeben. In Wirklichkeit sehen diese Werte für viele Dienste aufgrund der unterschiedlichen Workloads, die von primären und sekundären Replikaten ausgeführt werden, anders aus. Und da primäre Replikate normalerweise sowohl Lese- als auch Schreibvorgänge bereitstellen (und außerdem die größte Rechenlast tragen), ist die Standardauslastung für ein primäres Replikat höher als für sekundäre Replikate.

Wir nehmen nun aber an, dass Sie Ihren Dienst eine Zeit lang ausgeführt und bemerkt haben, dass einige Instanzen oder Replikate Ihres Diensts deutlich mehr Ressourcen als andere verbrauchen oder dass der Verbrauch im Laufe der Zeit variiert. Es kann beispielsweise sein, dass sie einem bestimmten Kunden zugeordnet sind oder dass sie nur für bestimmte Workloads bestimmt sind, die sich im Laufe des Tages ändern, z. B. Nachrichtendatenverkehr oder Aktienhandel. Ihnen fällt jedenfalls auf, dass es keinen alleinigen Wert gibt, den Sie für die Auslastung angeben können, ohne relativ weit daneben zu liegen. Außerdem bemerken Sie, dass Ihre unrealistische erste Einschätzung dazu führt, dass Service Fabric für Ihren Dienst entweder zu viele oder zu wenig Ressourcen zuordnet und daher Knoten vorhanden sind, die eine zu hohe oder zu niedrige Auslastung aufweisen. Vorgehensweise Es ist möglich, dass Ihr Dienst seine Auslastung ständig „on the fly“ meldet.

## Dynamische Auslastung

Mit Berichten zur dynamischen Auslastung können Replikate oder Instanzen ihre Zuordnung bzw. die gemeldete Auslastung von Metriken im Cluster über ihre Lebensdauer hinweg anpassen. Ein Dienstreplikat oder eine Instanz, das bzw. die derzeit keine Auslastung aufweist, meldet in der Regel eine geringe Nutzung der Ressourcen, und für Replikate oder Instanzen mit höherer Auslastung wird eine höhere Nutzung gemeldet. Aufgrund dieser allgemeinen Veränderungen im Cluster können wir die Dienstreplikate und -instanzen im Cluster ständig neu organisieren, um sicherzustellen, dass die Dienste und Instanzen die benötigten Ressourcen erhalten. Höher ausgelastete Dienste können so Ressourcen von anderen Replikaten oder Instanzen nutzen, die gerade keine oder nur wenig Arbeit verrichten. Das kontinuierliche Melden der Auslastung ist mit der ReportLoad-Methode möglich, die unter ServicePartition und als Eigenschaft des StatefulService-Basiselements verfügbar ist. In Ihrem Dienst würde der Code wie folgt aussehen:

Code:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("Foo", 42) });
```

Dienstreplikate oder -instanzen können die Auslastung nur für die Metriken melden, für deren Verwendung sie konfiguriert wurden. Die Liste mit den Metriken wird jeweils festgelegt, wenn ein Dienst erstellt wird. Wenn ein Dienstreplikat oder eine Instanz versucht, die Auslastung für eine Metrik zu melden, die derzeit nicht für die Verwendung konfiguriert ist, protokolliert Service Fabric den Bericht, ignoriert ihn aber. Dies bedeutet, dass er nicht verwendet wird, wenn der Zustand des Clusters berechnet oder gemeldet wird. Dies ist praktisch, weil Sie so besser experimentieren können. Der Code kann alle Dinge messen und melden, für die die Vorgehensweise bekannt ist. Der Bediener kann die Regeln für den Lastenausgleich des Diensts kontinuierlich konfigurieren, optimieren und aktualisieren, ohne jemals den Code ändern zu müssen. Beispiele hierfür sind das Deaktivieren einer Metrik mit einem Fehlerbericht, das Neukonfigurieren der Gewichtungen von Metriken anhand des Verhaltens oder das Aktivieren einer neuen Metrik erst dann, wenn der Code bereits bereitgestellt und überprüft wurde.

Ist es sinnvoll, eine Standardauslastung für einen Dienst anzugeben, für den die Auslastung dynamisch gemeldet wird? Auf jeden Fall. In diesem Fall dient die Standardauslastung als Schätzung, bis die richtigen Berichte vom jeweiligen Replikat oder der Instanz des Diensts vorhanden sind. Dies ist vorteilhaft, da Resource Manager hiermit arbeiten kann, wenn das Replikat bzw. die Instanz bei der Erstellung angeordnet wird. Die Standardauslastung dient als anfängliche Schätzung, sodass Resource Manager die Dienstinstanzen oder -replikate von Beginn an möglichst gut anordnen kann. Wenn keine Informationen vorhanden wären, würde die Anordnung zufällig erfolgen, und wir müssten mit ziemlicher Sicherheit eine Anpassung vornehmen, sobald die Berichte zur tatsächlichen Auslastung verfügbar sind.

Wir sehen uns das vorherige Beispiel an und prüfen, was passiert, wenn wir eine benutzerdefinierte Auslastung hinzufügen und diese nach der Erstellung des Diensts dynamisch aktualisiert wird. In diesem Beispiel verwenden wir „Memory“ (Arbeitsspeicher) und setzen voraus, dass der zustandsbehaftete Dienst zuvor mit dem folgenden Befehl erstellt wurde:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Diese Syntax wurde bereits beschrieben (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad). Weiter unten geht es dann darum, was der jeweilige Wert für „Weight“ (Gewichtung) bedeutet.

Ein mögliches Clusterlayout kann beispielsweise wie folgt aussehen:

![Ausgleich für Cluster mit Standardmetriken und benutzerdefinierten Metriken][Image2]

Beachten Sie folgende Punkte:
-	Da Replikate oder Instanzen die Standardauslastung des Diensts verwenden, bis sie ihre eigene Auslastung melden, wissen wir, dass die Replikate in Partition 1 des zustandsbehafteten Diensts noch keine eigene Auslastung gemeldet haben.
-	Sekundäre Replikate innerhalb einer Partition können über eine eigene Auslastung verfügen.
-	Insgesamt sehen die Metriken ziemlich gut aus. Die Differenz zwischen der maximalen und minimalen Auslastung eines Knotens (für den Arbeitsspeicher, der uns in diesem Fall am wichtigsten ist) weist nur einen Faktor von 1,75 auf (der Knoten mit der höchsten Auslastung für den Arbeitsspeicher ist N3, und N2 hat die geringste Auslastung, also gilt 28/16 = 1,75). Das ist ein guter Ausgleichswert!

Einige zu klärende Punkte

-	Wie wird bestimmt, ob ein Verhältnis von 1,75 gut oder nicht gut ist? Wie können wir sicher sein, dass der Wert gut genug ist und nicht noch mehr Arbeit erforderlich ist?
-	Wann wird ein Lastenausgleich durchgeführt?
-	Was bedeutet es, dass „Memory“ die Gewichtung „Hoch“ erhalten hat?

## Metrikgewichtungen
Anhand von Metrikgewichtungen können zwei unterschiedliche Dienste die gleichen Metriken melden, die Wichtigkeit eines Ausgleichs für diese Metriken aber anders bewerten. Beispiele hierfür sind ein speicherinternes Analysemodul und eine dauerhafte Datenbank. Für beide ist die Metrik „Memory“ sicherlich von Bedeutung, aber für den speicherinternen Dienst ist die Metrik „Disk“ (Datenträger) wahrscheinlich weniger wichtig. Unter Umständen wird diese Metrik in geringem Umfang genutzt, aber sie ist für die Leistung des Diensts nicht entscheidend. Es ist hilfreich, die gleichen Metriken über unterschiedliche Dienste hinweg nachverfolgen zu können, da der Resource Manager so die tatsächliche Nutzung im Cluster überwachen, die Einhaltung der Kapazitätsobergrenze sicherstellen und vieles mehr tun kann.

Dank Metrikgewichtungen kann der Resource Manager nützliche Entscheidungen darüber treffen, wie der Ausgleich für den Cluster durchgeführt werden soll, wenn es keine perfekte Lösung gibt (was häufig der Fall ist). Metriken können vier unterschiedliche Gewichtungen haben: Null, Niedrig, Mittel und Hoch. Eine Metrik mit der Gewichtung „Null“ leistet keinen Beitrag, wenn überprüft wird, ob ein guter Ausgleich besteht. Ihre Auslastung wird aber trotzdem berücksichtigt, wenn es beispielsweise um die Kapazitätsmessung geht.

Die eigentlichen Auswirkungen von unterschiedlichen Metrikgewichtungen im Cluster sind, dass wir zu deutlich unterschiedlichen Anordnungen der Dienste gelangen, weil für den Resource Manager folgende allgemeine Vorgabe besteht: Es soll sichergestellt werden, dass bestimmte Metriken wichtiger als andere Metriken sind und dass diese beim Ausgleich daher bevorzugt behandelt werden sollen, wenn sie mit Metriken mit niedrigerer Priorität in Konflikt stehen.

Wir sehen uns nun ein einfaches Beispiel für einige Auslastungsberichte und dafür an, wie unterschiedliche Metrikgewichtungen zu unterschiedlichen Zuordnungen im Cluster führen können. In diesem Beispiel sehen wir, dass die Umstellung der relativen Gewichtung von Metriken dazu führt, dass der Resource Manager bestimmte Lösungen vorzieht, indem unterschiedliche Anordnungen von Diensten erstellt werden.

![Beispiel für Metrikgewichtung und Auswirkung auf Ausgleichslösungen][Image3]

In diesem Beispiel werden vier verschiedene Dienste verwendet, die alle unterschiedliche Werte für die beiden Metriken A und B melden. In einem Fall definieren alle Dienste „MetricA“ als wichtigste Metrik (Weight = High) und „MetricB“ als relativ unwichtig (Weight = Low). Wir sehen, dass der Service Fabric Resource Manager die Dienste so anordnet, dass „MetricA“ einen besseren Ausgleich (geringere Abweichung) als „MetricB“ aufweist. Im zweiten Fall kehren wir die Metrikgewichtungen um und sehen, dass der Resource Manager die Dienste A und B voraussichtlich tauschen würde, um eine Zuordnung zu erzielen, bei der „MetricB“ einen besseren Ausgleich als „MetricA“ aufweist.

### Globale Metrikgewichtungen
Wenn „ServiceA“ also „MetricA“ als wichtigste Metrik definiert und diese für „ServiceB“ keine Rolle spielt, welche Gewichtung wird vom Resource Manager dann letztendlich verwendet?

Für jede Metrik werden eigentlich zwei Gewichtungen nachverfolgt. Die Gewichtung, die vom Dienst selbst definiert wird, und die globale durchschnittliche Gewichtung über alle Dienste hinweg, für die diese Metrik wichtig ist. Wir nutzen beide Gewichtungen beim Berechnen der Punktzahlen von generierten Lösungen. Es ist wichtig sicherzustellen, dass ein Dienst in Bezug auf seine eigenen Prioritäten einen Lastenausgleich erfährt, aber es muss auch dafür gesorgt sein, dass der Cluster insgesamt über eine geeignete Anordnung verfügt.

Was würde passieren, wenn wir uns nicht um den globalen und lokalen Ausgleich kümmern würden? Es ist nicht schwierig, Lösungen zu erstellen, für die ein globaler Ausgleich durchgeführt wird, die für einzelne Dienste aber eine sehr schlechte Leistung und Ressourcenzuordnung ergeben. Im folgenden Beispiel sehen wir uns die Standardmetriken an, mit denen ein zustandsbehafteter Dienst konfiguriert wurde (PrimaryCount, ReplicaCount und TotalCount). Wir können verfolgen, was passiert, wenn wir uns nur um den globalen Ausgleich kümmern:

![Auswirkung einer rein globalen Lösung][Image4]

Im oberen Beispiel, in dem wir uns nur den globalen Ausgleich angesehen haben, ist der Cluster insgesamt gut ausgeglichen. Alle Knoten verfügen über die gleiche Zahl an primären Replikaten und Gesamtreplikaten. So weit, so gut. Es besteht ein Ausgleich. Wenn wir uns aber die tatsächlichen Auswirkungen dieser Zuordnung ansehen, ist das Ergebnis nicht so gut: Der Verlust eines Knotens wirkt sich unverhältnismäßig stark auf eine bestimmte Workload aus, da alle zugehörigen primären Replikate betroffen sind. Nehmen wir beispielsweise an, dass der erste Knoten ausfällt. In diesem Fall verlieren wir gleichzeitig die drei primären Replikate für die drei verschiedenen Partitionen des Diensts „Circle“. Für die anderen beiden Dienste („Triangle“ und „Hexagon“) verlieren die Partitionen ein Replikat, was nicht zu Problemen führt (bis auf die Wiederherstellung des ausgefallenen Replikats).

Im unteren Beispiel haben wir die Replikate sowohl basierend auf dem globalen Ausgleich als auch auf dem Ausgleich pro Dienst verteilt. Beim Berechnen der Punktzahl für die Lösung ordnen wir den größten Teil der Gewichtung der globalen Lösung zu, aber ein (konfigurierbarer) Teil wird für die Sicherstellung verwendet, dass auch die Dienste selbst so weit wie möglich ausgeglichen sind. Wenn hier nun derselbe erste Knoten ausfällt, sehen wir, dass der Verlust der primären (und sekundären) Replikate auf alle Partitionen aller Dienste verteilt ist und die Auswirkungen jeweils gleich sind.

Indem die Metrikgewichtungen berücksichtigt werden, wird der globale Ausgleich basierend auf dem Durchschnitt der Metrikgewichtungen berechnet. Wir führen den Ausgleich für einen Dienst in Bezug auf seine eigenen definierten Metrikgewichtungen durch.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Informationen zur Konfiguration von Diensten](service-fabric-cluster-resource-manager-configure-services.md)
- [Informationen zu Defragmentierungsmetriken](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- [Informationen zum Lastenausgleich durch den Cluster-Resource Manager im Cluster](service-fabric-cluster-resource-manager-balancing.md)
- [Einführung in den Cluster-Resource Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- [Informationen zu den Kosten von Dienstverschiebungen](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0309_2016-->