<properties
   pageTitle="Beschreibung eines Resource Balancer-Clusters | Microsoft Azure"
   description="Beschreibung eines Service Fabric-Clusters durch Angabe von Fehlerdomänen, Upgradedomänen, Knoteneigenschaften und Knotenkapazitäten für den Clusterressourcen-Manager."
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

# Beschreiben eines Service Fabric-Clusters
Der Service Fabric-Cluster-Ressourcen-Manager stellt verschiedene Methoden zum Beschreiben eines Clusters bereit. Während der Laufzeit nutzt der Ressourcen-Manager diese Informationen, um die hohe Verfügbarkeit der im Cluster ausgeführten Dienste zu gewährleisten und sicherzustellen, dass die Ressourcen im Cluster ordnungsgemäß verwendet werden.

## Wichtige Begriffe
Cluster-Ressourcen-Manager-Funktionen, die einen Cluster beschreiben:

- Fehlerdomänen
- Upgradedomänen
- Knoteneigenschaften
- Knotenkapazitäten

## Fehlerdomänen
Eine Fehlerdomäne ist ein beliebiger Bereich mit einem koordinierten Ausfall. Ein einzelner Computer ist eine Fehlerdomäne (da er aus zahlreichen unterschiedlichen Gründen ausfallen kann, z. B. infolge eines Stromausfalls oder aufgrund von Laufwerkfehlern oder fehlerhafter NIC-Firmware). Eine Reihe von Computern, die mit dem gleichen Ethernet-Switch verbunden sind, befinden sich genau wie an eine einzelne Stromquelle angeschlossene Computer in derselben Fehlerdomäne.

Wenn Sie Ihren eigenen Cluster einrichten, müssen Sie diese verschiedenen Ausfallbereiche berücksichtigen und sicherstellen, dass Ihre Fehlerdomänen ordnungsgemäß eingerichtet wurden. So weiß Service Fabric, in welchen Bereichen Dienste sicher platziert werden können. Mit „sicher“ ist eigentlich „intelligent“ gemeint: Dienste sollen nicht so platziert werden, dass der Ausfall einer Fehlerdomäne zum Ausfall des Diensts führt. In der Azure-Umgebung nutzen wir die von Azure Fabric Controller bzw. vom Ressourcen-Manager bereitgestellten Fehlerdomäneninformationen, um die Knoten in Ihrem Cluster in Ihrem Auftrag ordnungsgemäß zu konfigurieren. In der folgenden Abbildung (Abb. 7) sind als einfaches Beispiel alle Entitäten, die sinnvollerweise eine Fehlerdomäne bilden, farbig hervorgehoben und alle resultierenden Fehlerdomänen aufgeführt. Dieses Beispiel enthält Datencenter (DC), Racks (R) und Blades (B). Wenn jedes Blade also mehrere virtuelle Computer enthält, kann die Fehlerdomänenhierarchie eine weitere Ebene umfassen.

![Mithilfe von Fehlerdomänen strukturierte Knoten][Image1]

 Zur Laufzeit sieht sich der Service Fabric-Cluster-Ressourcen-Manager die Fehlerdomänen im Cluster an und versucht, die Replikate für einen bestimmten Dienst so zu verteilen, dass sie sich jeweils in separaten Fehlerdomänen befinden. Mit diesem Prozess wird sichergestellt, dass die Verfügbarkeit des Diensts bei einem Ausfall einer Fehlerdomäne nicht gefährdet wird.

 Für den Cluster-Ressourcen-Manager von Service Fabric ist nicht wirklich wichtig, wie viele Ebenen die Hierarchie umfasst. Da er jedoch versucht, sicherzustellen, dass sich der Verlust eines Teils der Hierarchie nicht auf den Cluster oder die darauf aufgeführten Dienste auswirkt, wird im Allgemeinen empfohlen, auf jeder Fehlerdomänenebene die gleiche Anzahl von Computern bereitzustellen. Dadurch wird verhindert, dass ein Teil der Hierarchie am Ende des Tages mehr Dienste enthält als andere Teile.

 Wenn der Cluster mit einer unausgeglichenen „Struktur“ der Fehlerdomäne konfiguriert wird, erschwert dies dem Ressourcen-Manager die Ermittlung der optimalen Replikatzuweisung. Das bedeutet, dass der Verlust einer bestimmten Domäne eine zu große Auswirkung auf die Verfügbarkeit des Clusters hat – der Ressourcen-Manager schwankt zwischen der effizienten Verwendung der Computer in dieser intensiv genutzten Domäne und der Platzierung der Dienste, sodass der Ausfall der Domäne keine Probleme verursacht.

 Im folgenden Diagramm werden zwei verschiedene Cluster eingerichtet: ein Cluster, bei dem die Knoten gleichmäßig über die Fehlerdomänen verteilt sind, und ein Cluster, bei dem eine Fehlerdomäne viel mehr Knoten enthält. Beachten Sie, dass in Azure für Sie festgelegt wird, welche Knoten in welche Fehler- und Upgradedomänen verschoben werden, daher bemerken Sie diese Art von Diskrepanzen nicht. Wenn Sie jedoch einen eigenen Cluster lokal oder in einer anderen Umgebung einrichten, müssen Sie diese Diskrepanzen berücksichtigen.

 ![Zwei unterschiedliche Clusterlayouts][Image2]

## Upgradedomänen
Upgradedomänen sind ein weiteres Feature, mit dem der Service Fabric-Ressourcen-Manager das Layout des Clusters nachvollziehen kann, sodass er im Voraus für Ausfälle planen kann. Upgradedomänen definieren Bereiche (eigentlich Knotengruppen), die während eines Upgrades zur gleichen Zeit ausfallen.

Upgradedomänen sind Fehlerdomänen nicht unähnlich, es gibt jedoch einige wichtige Unterschiede. Zunächst einmal werden Upgradedomänen normalerweise durch eine Richtlinie definiert. Fehlerdomänen hingegen werden genau durch die Bereiche der koordinierten Ausfälle (und daher in der Regel durch das Hardwarelayout der Umgebung) festgelegt. Bei Upgradedomänen können Sie jedoch über die gewünschte Anzahl entscheiden. Ein weiterer Unterschied (zumindest zum jetzigen Zeitpunkt) ist, dass Upgradedomänen nicht hierarchisch aufgebaut sind – sie ähneln eher einem einfachen Tag als einer Hierarchie.

In der folgenden Abbildung ist ein fiktives Setup dargestellt, in dem drei Upgradedomänen auf drei Fehlerdomänen verteilt sind. Sie zeigt zudem eine mögliche Platzierung für drei verschiedene Replikate eines zustandsbehafteten Diensts. Beachten Sie, dass sie sich alle in verschiedenen Fehler- und Upgradedomänen befinden. Das bedeutet, dass mitten in einem Dienstupgrade eine Fehlerdomäne ausfallen kann und dennoch eine ausgeführte Kopie des Codes und der Daten im Cluster vorhanden ist. Je nach Bedarf kann dies ausreichend sein, diese Kopie ist jedoch möglicherweise alt (da Service Fabric eine quorumbasierte Replikation verwendet). Um tatsächlich zwei Ausfälle überstehen zu können, benötigen Sie mehr Replikate (mindestens fünf).

![Platzierung mit Fehler- und Upgradedomänen][Image3]

Eine große Anzahl von Upgradedomänen hat Vor- und Nachteile. Der Vorteil besteht in einer stärkeren Differenziertheit der einzelnen Upgradeschritte, die sich daher auf weniger Knoten oder Dienste auswirken. Infolgedessen muss jeweils eine kleinere Anzahl von Diensten verschoben werden, die weniger Änderungen im System verursachen, wodurch die Zuverlässigkeit insgesamt erhöht wird (da bei Problemen ein geringerer Teil des Diensts beeinträchtigt wird). Der Nachteil bei der Bereitstellung vieler Upgradedomänen besteht darin, dass Service Fabric beim Upgrade die Integrität jeder Upgradedomäne überprüft und sicherstellt, dass die Upgradedomäne fehlerfrei ist, bevor sie mit der nächsten Upgradedomäne fortfährt. Mit dieser Überprüfung soll vor dem Fortsetzen des Upgrades die Stabilisierung von Diensten sichergestellt und ihre Integrität bestätigt werden, um mögliche Probleme zu ermitteln. Dieser Kompromiss ist akzeptabel, da dadurch verhindert wird, dass sich negative Änderungen jeweils auf einen zu großen Teil des Diensts auswirken.

Eine zu geringe Anzahl von Upgradedomänen hat ebenfalls Nebeneffekte: Während jede einzelne Upgradedomäne außer Betrieb genommen und aktualisiert wird, steht ein großer Teil der Gesamtkapazität nicht zur Verfügung. Beispiel: Wenn Sie nur drei Upgradedomänen besitzen, nehmen Sie jeweils etwa ein Drittel der Dienst- oder Clustergesamtkapazität außer Betrieb. Dies ist nicht erwünscht, da im übrigen Cluster ausreichend Kapazität zum Verarbeiten der Workload verfügbar sein muss. Das bedeutet, dass diese Knoten bei normalem Betrieb weniger ausgelastet sind, was zu höheren Umsatz- und Betriebskosten führt.

Es gibt keine tatsächliche Beschränkung bei der Gesamtanzahl der Fehler- oder Upgradedomänen in einer Umgebung bzw. keine Einschränkungen in Bezug auf ihre Überlappung. Wir haben folgende allgemeine Strukturen betrachtet: 1:1 (jede eindeutige Fehlerdomäne ist auch ihrer eigenen Upgradedomäne zugeordnet), eine Upgradedomäne pro Knoten (physische oder virtuelle Betriebssysteminstanz) und ein Stripeset- oder Matrixmodell, bei dem die Fehler- und Upgradedomänen eine Matrix bilden, in der Computer in der Regel diagonal zugeordnet sind.

![Layouts von Fehler- und Upgradedomänen][Image4]

Es gibt keine allgemeingültige Empfehlung für die Auswahl des Layouts, da jedes Layout seine Vor- und Nachteile hat. Das 1FD:1UD-Modell ist beispielsweise einfach einzurichten. Mit dem Modell mit einer Upgradedomäne pro Knoten sind die meisten Benutzer wahrscheinlich bereits vertraut, wenn sie in der Vergangenheit kleine Gruppen mit Computern verwaltet haben, die unabhängig voneinander heruntergefahren werden.

Das am häufigsten verwendete Modell (das wir auch für die gehosteten Azure Service Fabric-Cluster verwenden) ist die FD/UD-Matrix. Dabei bilden die FDs und UDs eine Tabelle, und Knoten werden diagonal platziert. Ob die Tabelle eine geringe oder hohe Dichte aufweist hängt von der Gesamtanzahl der Knoten verglichen mit der Anzahl der FDs und UDs ab. Mit anderen Worten: Bei ausreichend großen Clustern sieht beinahe jede Struktur wie das Matrixmuster mit hoher Dichte aus (siehe Option unten rechts in Abbildung 10).

## Konfigurieren von Fehler- und Upgradedomänen
Das Festlegen von Fehler- und Upgradedomänen erfolgt in von Azure gehosteten Service Fabric-Bereitstellungen automatisch. Service Fabric greift einfach die Umgebungsinformationen von Azure auf. Sie als Benutzer können wiederum die gewünschte Anzahl von Domänen auswählen. In Azure scheinen die Informationen der Fehlerdomäne und der Upgradedomäne der gleichen Ebene anzugehören. Tatsächlich werden jedoch Informationen aus niedrigeren Ebenen von Azure Stack einbezogen und nur die logischen Fehler- und Upgradedomänen aus der Perspektive des Benutzers dargestellt.

Wenn Sie einen eigenen Cluster einrichten (oder nur eine bestimmte Topologie auf Ihrem Entwicklungscomputer ausführen möchten), müssen Sie die Informationen zu den Fehler- und Upgradedomänen selbst bereitstellen. In diesem Beispiel definieren wir einen Cluster mit neun Knoten, der drei Datencenter (mit jeweils drei Racks) umfasst, und drei Upgradedomänen, die über diese drei Datencenter verteilt sind. Dies sieht in Ihrem Clustermanifest etwa wie folgt aus:

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [AZURE.NOTE] In Azure-Bereitstellungen werden Fehlerdomänen und Upgradedomänen von Azure zugewiesen. Daher enthält die Definition Ihrer Knoten und Rollen unter der Infrastrukturoption für Azure keine Informationen zu Fehler- oder Upgradedomänen.

## Platzierungseinschränkungen und Knoteneigenschaften
Manchmal (eigentlich in den meisten Fällen) möchten Sie sicherstellen, dass bestimmte Workloads nur auf bestimmten Knoten oder bestimmten Knotengruppen ausgeführt werden. Für einige Workloads sind beispielsweise GPUs oder SSDs erforderlich, für andere hingegen nicht. Ein gutes Beispiel hierfür ist nahezu jede Architektur mit n Ebenen, in der bestimmte Computer als Front-End bzw. Schnittstelle der Anwendung fungieren, während eine andere Gruppe (häufig mit anderen Hardwareressourcen) die Arbeitslast der Compute- oder Speicherebenen behandelt. Service Fabric erwartet, dass es selbst in der Welt der Microservices Fälle gibt, in denen bestimmte Workloads in bestimmten Hardwarekonfigurationen, ausgeführt werden müssen, z. B.:

- Eine vorhandene n-Ebenen-Anwendung wurde in eine Service Fabric-Umgebung verschoben.
- Eine Workload soll aus Leistungs-, Skalierbarkeits- oder Sicherheitsisolationsgründen auf bestimmter Hardware ausgeführt werden.
-	Eine Workload muss aufgrund von Richtlinien oder des Ressourcenverbrauchs von anderen Workloads isoliert werden.

Um diese Konfigurationstypen zu unterstützen, kommen bei Service Fabric erstklassige sogenannte Platzierungseinschränkungen zum Einsatz. Mit Einschränkungen kann angegeben werden, wo bestimmte Dienste ausgeführt werden sollen. Der Satz von Einschränkungen kann durch Benutzer erweitert werden, d. h. sie können Knoten mit benutzerdefinierten Eigenschaften markieren und für sie dann ebenfalls eine Auswahl treffen.

![Clusterlayout bei verschiedenen Workloads][Image5]

Die verschiedenen Schlüssel-Wert-Tags für Knoten werden als Knotenplatzierungseigenschaften (oder einfach Knoteneigenschaften) bezeichnet. Die Anweisung im Dienst hingegen heißt Platzierungseinschränkung. Der Wert in der Knoteneigenschaft kann als Zeichenfolge, boolescher Wert oder als Wert vom Typ „signed long“ angegeben werden. Die Einschränkung kann als boolesche Anweisung angegeben werden, die auf den verschiedenen Knoteneigenschaften im Cluster basiert. Der Dienst kann nur auf Knoten platziert werden, bei denen die Anweisung als wahr (TRUE) ausgewertet wird. Knoten ohne definierte Eigenschaft entsprechen keiner Platzierungseinschränkung mit dieser Eigenschaft. Service Fabric definiert ebenfalls einige Standardeigenschaften, die automatisch verwendet werden können, ohne dass sie vom Benutzer definiert werden müssen. Zum Zeitpunkt der Erstellung dieses Dokuments sind für jeden Knoten die Standardeigenschaften „NodeType“ und „NodeName“ definiert. Im Allgemeinen haben wir festgestellt, dass „NodeType“ die am häufigsten verwendete Eigenschaft ist, da sie in der Regel genau mit dem Typ eines Computers übereinstimmt, der wiederum einem Workloadtyp in einer herkömmlichen n-Ebenen-Anwendungsarchitektur entspricht.

![Platzierungseinschränkungen und Knoteneigenschaften][Image6]

Nehmen wir an, dass die folgenden Knoteneigenschaften für einen bestimmten Knotentyp definiert wurden: ClusterManifest.xml.

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasDisk" Value="true"/>
        <Property Name="Value" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Sie können Dienstplatzierungseinschränkungen für einen Dienst wie folgt erstellen:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true && Value >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasDisk == true && Value >= 4"
```

Wenn Sie sicher sind, dass alle Knoten von NodeType01 gültig sind, können Sie auch nur diesen Knotentyp auswählen. Verwenden Sie dazu Platzierungseinschränkungen wie in den obigen Abbildungen.

Das Praktische an den Platzierungseinschränkungen eines Diensts ist, dass sie während der Laufzeit dynamisch aktualisiert werden können. Falls erforderlich, können Sie also einen Dienst im Cluster verschieben, Anforderungen hinzufügen und entfernen usw. Service Fabric stellt sicher, dass der Dienst einsatzbereit und verfügbar bleibt, selbst wenn diese Art von Änderungen fortlaufend ausgeführt wird.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Platzierungseinschränkungen (und zahlreiche weitere Eigenschaften, über die wir sprechen werden) werden für jede unterschiedliche Dienstinstanz angegeben. Updates ersetzen (überschreiben) immer die vorherige Angabe.

## Kapazität
Eine der wichtigsten Aufgaben eines Orchestrators besteht in der Verwaltung des Ressourcenverbrauchs im Cluster. Bei einer effizienten Dienstausführung sollen keine Knoten überlastet oder zu wenig ausgelastet sein. Eine Überlastung führt zu Ressourcenkonflikten und niedriger Leistung und eine zu geringe Auslastung zu einer Verschwendung von Ressourcen. Denken wir vor dem Lastenausgleich (dazu kommen wir gleich) jedoch zunächst an etwas Grundlegenderes: Warum stellen wir nicht einfach sicher, dass immer ausreichend Ressourcen vorhanden sind?

Service Fabric stellt Ressourcen als sogenannte Metriken dar. Bei Metriken handelt es sich um alle logischen oder physischen Ressourcen, die Sie für Service Fabric beschreiben möchten. Beispiele für Metriken: WorkQueueDepth oder MemoryInMb. Metriken unterscheiden sich von Einschränkungen und Knoteneigenschaften dahingehend, dass Knoteneigenschaften in der Regel statische Deskriptoren der Knoten selbst sind. Metriken hingegen beziehen sich auf physische Ressourcen, die Dienste nutzen, wenn sie auf einem Knoten ausgeführt werden. Eine Eigenschaft wäre also beispielsweise „HasSSD“, und sie könnte auf wahr oder falsch festgelegt werden. Der auf dem SSD verfügbare (und von den Diensten verbrauchte) Speicherplatz wäre eine Metrik wie „DriveSpaceInMb“. Die Kapazität auf dem Knoten würde als „DriveSpaceInMb“ die Menge des gesamten nicht reservierten Speicherplatzes auf dem Laufwerk festlegen, und Dienste würden den zur Laufzeit verwendeten Teil der Metrik melden.

Wenn Sie den Lastenausgleich für alle Ressourcen deaktivieren, kann der Service Fabric-Ressourcen-Manager dennoch sicherstellen, dass kein Knoten überlastet wird (es sei denn, der Cluster insgesamt war zu voll). Anhand von Kapazitäten vollzieht Service Fabric nach, wie viele Ressourcen ein Knoten belegt. Davon ziehen wir den Verbrauch durch verschiedene Dienste ab (dazu später mehr), um den verbleibenden Teil zu ermitteln. Sowohl die Kapazität als auch der Verbrauch auf Dienstebene werden als Metrik ausgedrückt.

Während der Laufzeit verfolgt der Ressourcen-Manager, welcher Anteil von jeder Ressource auf den einzelnen Knoten (definiert durch seine Kapazität) vorhanden und welcher Anteil übrig ist (durch Subtrahieren der angegebenen Nutzung von jedem Dienst). Mit diesen Informationen kann der Service Fabric-Ressourcen-Manager ermitteln, wo Replikate platziert oder wohin diese verschoben werden sollen, um die Kapazität der Knoten nicht zu überschreiten.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "Memory";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 1024;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,1024,1024”)
```

![Clusterknoten und -kapazität][Image7]

Diese finden Sie im Clustermanifest:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="Memory" Value="10"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

Möglicherweise ändert sich die Last eines Diensts auch dynamisch. In diesem Fall kann der Ort, an dem ein Replikat oder eine Instanz derzeit platziert ist, ungültig werden, da durch die kombinierte Nutzung aller Replikate und Instanzen auf dem Knoten die Kapazität dieses Knotens überschritten wird. Auf das Szenario, in dem sich die Last dynamisch ändern kann, gehen wir später genauer ein. Die Kapazität wird jedoch genauso behandelt: Die Service Fabric-Ressourcenverwaltung schaltet sich automatisch ein und reduziert die Kapazität des Knotens, indem Sie Replikate oder Instanzen auf diesem Knoten auf andere Knoten verschiebt. Dabei versucht der Ressourcen-Manager, die Kosten aller Verschiebungen zu minimieren (auf die Kosten kommen wir später zu sprechen).

##Clusterkapazität
Wie verhindern wir eine Überlastung des Clusters? Bei der dynamischen Auslastung kann nicht viel unternommen werden, da die Auslastung von Diensten unabhängig von den vom Ressourcen-Manager unternommenen Aktionen rasant ansteigen kann: Ein Cluster, der heute noch genügend Spielraum bietet, hat morgen womöglich nicht mehr ausreichend Kapazität, wenn Sie berühmt werden. Es gibt jedoch einige integrierte Sicherheitsmechanismen, die allgemeine Fehler verhindern. Zunächst einmal können wir die Erstellung neuer Workloads verhindern, die eine Überlastung des Clusters verursachen.

Angenommen, Sie erstellen einen einfachen zustandslosen Dienst, dem eine bestimmte Last zugeordnet ist (weitere Informationen zur Meldung standardmäßiger und dynamischer Auslastung finden Sie weiter unten). Bei diesem Dienst gehen wir davon aus, dass er für eine gewisse Ressource, z. B. Speicherplatz, verwendet wird und standardmäßig fünf Speicherplatzeinheiten pro Dienstinstanz verbraucht. Sie möchten drei Instanzen des Diensts erstellen. Prima. Das bedeutet, dass im Cluster 15 Speicherplatzeinheiten vorhanden sein müssen, damit wir diese Dienstinstanzen überhaupt erstellen können. Service Fabric berechnet kontinuierlich die gesamte Kapazität und den Verbrauch der einzelnen Metriken, sodass einfach bestimmt werden kann, ob der Aufruf zur Diensterstellung abgewiesen werden soll, falls nicht ausreichend Platz zur Verfügung steht.

Hinweis: Da nur die Anforderung besteht, dass 15 Einheiten vorhanden sein müssen, kann dieser Platz auf verschiedene Art und Weise zugewiesen werden: Beispielsweise kann eine Kapazitätseinheit auf 15 verschiedenen Knoten oder es können drei Kapazitätseinheiten auf fünf verschiedenen Knoten übrig sein. Ist nicht ausreichend Kapazität auf drei verschiedenen Knoten vorhanden, organisiert Service Fabric die bereits im Cluster bestehenden Dienste neu, um Platz auf den drei erforderlichen Knoten zu schaffen. Solche Neuanordnung ist fast immer möglich, es sei denn, der Cluster als Ganzes ist fast vollständig voll.

Wir haben der für jeden Knoten angegebenen Kapazität reservierten Puffer hinzugefügt, um Benutzer bei der Verwaltung der gesamten Clusterkapazität zu unterstützen. Diese Einstellung ist optional, ermöglicht Benutzern jedoch die Reservierung eines Teils der Knotengesamtkapazität, sodass dieser Teil nur für die Platzierung von Diensten während Upgrades und Ausfällen verwendet wird – also in Fällen, in denen die Kapazität des Clusters anderweitig eingeschränkt ist. Heute wird der Puffer global pro Metrik für alle Knoten über das Clustermanifest angegeben. Der von Ihnen ausgewählte Wert für die reservierte Kapazität ist eine Funktion, die angibt, welche Ressourcen Ihrer Dienste stärker eingeschränkt sind und wie viele Fehler- und Upgradedomänen der Cluster enthält. Im Allgemeinen bedeutet eine höhere Anzahl von Fehler- und Upgradedomänen, dass Sie eine geringere Menge für die gepufferte Kapazität auswählen müssen, da bei Upgrades und Ausfällen ein geringerer Anteil Ihres Clusters nicht verfügbar ist. Beachten Sie, dass die Angabe des Pufferprozentsatzes nur sinnvoll ist, wenn Sie auch die Knotenkapazität für eine Metrik angegeben haben.

ClusterManifest.xml

```xml
        <Section Name=" NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="10" />
            <Parameter Name="Memory" Value="15" />
            <Parameter Name="SomeOtherMetric" Value="20" />
        </Section>
```
Bei Erstellungsaufrufen, die zu neuen Diensten führen, tritt ein Fehler auf, wenn dem Cluster keine gepufferte Kapazität mehr zur Verfügung steht. Dadurch wird sichergestellt, dass der Cluster über ausreichend Kapazitätsreserven verfügt und Upgrades und Ausfälle nicht dazu führen, dass Knoten die Kapazität überschreiten. Der Ressourcen-Manager macht viele dieser Informationen über PowerShell und die Abfrage-APIs verfügbar, sodass Sie die Einstellungen für die gepufferte Kapazität, die Gesamtkapazität und den aktuellen Verbrauch für jede angegebene Metrik anzeigen können. Hier sehen Sie ein Beispiel für diese Ausgabe:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## Nächste Schritte
- Informationen über die Architektur und den Informationsfluss innerhalb des Clusterressourcen-Managers finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-architecture.md).
- Das Definieren von Defragmentierungsmetriken ist eine Möglichkeit, die Last auf Knoten zu konsolidieren, statt sie auszubreiten. Weitere Informationen zum Konfigurieren der Defragmentierung finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
- Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---HONumber=AcomDC_0316_2016-->