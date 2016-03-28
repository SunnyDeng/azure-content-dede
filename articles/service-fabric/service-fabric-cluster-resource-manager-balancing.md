<properties
   pageTitle="Lastenausgleich für einen Cluster mit dem Clusterressourcen-Manager von Azure Service Fabric | Microsoft Azure"
   description="Eine Einführung in den Lastenausgleich für einen Cluster mit dem Clusterressourcen-Manager von Service Fabric."
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

# Lastenausgleich für Service Fabric-Cluster
Der Clusterressourcen-Manager von Service Fabric ermöglicht das Melden dynamischer Lasten, Reagieren auf Änderungen im Cluster und Erzeugen von Plänen für den Ausgleich, doch wann erfolgen diese Schritte? Was löst tatsächlich einen Lastenausgleich im Cluster aus, wenn Dienste bei ihrer Erstellung entsprechend ihren Standardlastwerten platziert werden? Hierfür gibt es mehrere Steuerungsmöglichkeiten.

Die erste Gruppen von Steuerungsmöglichkeiten, die den Lastenausgleich bestimmen, ist eine Gruppe von Timern, die festlegen, wie oft der Clusterressourcen-Manager den Zustand des Clusters auf Aspekte untersucht, auf die eingegangen werden muss. Diese Timer beziehen sich auf die unterschiedlichen Phasen von Aufgaben, die stets ausgeführt werden. Diese sind wie folgt:

1.	Platzierung – In dieser Phase erfolgt die Platzierung von zustandsbehafteten und zustandslosen Instanzen, die fehlen. Dies umfasst sowohl neue Dienste und die Behandlung von Replikaten oder Instanzen, die ausgefallen sind und neu erstellt werden müssen. Das Löschen und Ablegen von Replikaten oder Instanzen erfolgt auch in dieser Phase.
2.	Einschränkungsüberprüfungen – In dieser Phase erfolgt eine Überprüfung auf und Korrektur bei Verstößen gegen verschiedene Platzierungseinschränkungen (Regeln) im System. Beispiele hierfür sind z. B. das Sicherstellen, dass Knoten nicht ihre Kapazität überschreiten und die Platzierungseinschränkungen eines Diensts erfüllt werden (mehr dazu weiter unten).
3.	Lastenausgleich – In dieser Phase wird geprüft, ob basierend auf dem gewünschten Ausgleichsgrad für verschiedene Metriken ein proaktiver erneuter Ausgleich erforderlich ist. Falls ja, wird versucht, eine ausgeglichenere Anordnung im Cluster zu finden.

## Konfigurieren von Schritten und Timern für den Clusterressourcen-Manager
Jede dieser Phasen wird durch einen anderen Timer gesteuert, der die Häufigkeit bestimmt. Wenn Sie z. B. stündlich neue Dienstworkloads im Cluster platzieren möchten (um diese zu Batches zusammenzufassen), jedoch regelmäßig alle paar Sekunden Lastenausgleichsprüfungen wünschen, können Sie diese erzwingen. Wenn die einzelnen Timer ausgelöst werden, wird ein Kennzeichen festgelegt, das besagt, dass wir uns um diesen Teil der Aufgaben des Ressourcen-Managers kümmern müssen. Seine Auswahl erfolgt dann im nächsten Gesamtdurchlauf durch den Zustandsautomat (deshalb werden diese Konfigurationen als „Mindestintervall“ definiert). Standardmäßig überprüft der Ressourcen-Manager seinen Zustand und wendet Updates jede Zehntelsekunde an und legt die Kennzeichen für die Platzierungs- und Einschränkungsüberprüfung sekündlich sowie das Kennzeichen für den Ausgleich alle fünf Sekunden fest.

ClusterManifest.xml

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Derzeit führen wir immer nur eine Aktion sequenziell aus. Dies ist so, damit wir z. B. bereits auf Anforderungen zum Erstellen neuer Replikate geantwortet haben, ehe wir mit dem Ausgleichen des Clusters fortfahren. Wie Sie anhand der angegebenen standardmäßigen Zeitintervalle erkennen, können wir sehr schnell eine Überprüfung auf alle auszuführenden Schritte durchführen. Das heißt, dass jede Gruppe von Änderungen in der Regel kleiner ist. (Wir suchen also nicht über mehrere Stunden im Cluster angefallene Änderungen, um zu versuchen, sie auf einmal zu korrigieren. Wir können uns effektiv um Vorkommnisse kümmern, sobald sie auftreten.) Dadurch kann der Ressourcen-Manager von Service Fabric sehr schnell auf Ereignisse im Cluster reagieren.

Grundsätzlich muss der Clusterressourcen-Manager auch wissen, wann der Cluster als unausgeglichenen gilt und welche Replikate für eine Korrektur verschoben werden müssen. Hierfür gibt es zwei wesentliche Konfigurationseinstellungen: Ausgleichsschwellenwerte und Aktivitätsschwellenwerte.

## Ausgleichsschwellenwerte
Ein Ausgleichsschwellenwert ist das Hauptsteuerinstrument für das Auslösen eines proaktiven erneuten Ausgleichs. Der Ausgleichsschwellenwert definiert, wie unausgeglichenen der Cluster für eine bestimmte Metrik sein muss, damit dieser vom Ressourcen-Manager als unausgeglichenen eingestuft wird und bei der nächsten Ausführung ein Ausgleich ausgelöst wird. Ausgleichsschwellenwerte werden als Teil des Clustermanifests metrikbezogen definiert:

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Der Ausgleichsschwellenwert für eine Metrik ist ein Verhältnis. Wenn das Maß der Auslastung auf dem am stärksten ausgelasteten Knoten dividiert durch das Maß der Auslastung auf dem am wenigsten ausgelasteten Knoten dieses Verhältnis überschreitet, gilt der Cluster als unausgeglichen. Bei der nächsten Ausführung des Ressourcen-Managers wird deshalb ein Ausgleich ausgelöst.

![Beispiel eines Ausgleichsschwellenwerts][Image1]

Bei diesem einfachen Beispiel belegt jeder Dienst nur eine Einheit einer bestimmten Metrik. Im Beispiel oben ist die maximale Auslastung auf einem Knoten 5 und die minimale Auslastung 2. Angenommen, der Ausgleichsschwellenwert für diese Metrik ist 3. Daher gilt der Cluster bei diesem Beispiel als ausgeglichen, weshalb kein Lastenausgleich ausgelöst wird. Im nachstehenden Beispiel ist die maximale Auslastung auf einem Knoten 10, während die Mindestauslastung 2 ist, sodass wir den vorgesehenen Ausgleichsschwellenwert 3 überschreiten. Daher wird die Verarbeitungslast mit großer Wahrscheinlichkeit auf Knoten 3 verteilt werden, sobald der Ressourcen-Manager ausgeführt werden kann. Da wir keinen „gierigen“ Ansatz befolgen, könnte ein Teil der Last auch auf Knoten 2 landen, da dies zu einer Minimierung der allgemeinen Unterschiede zwischen Knoten führen würde.

![Ausgleichsschwellenwert – Beispielaktionen][Image2]

Beachten Sie, dass das Unterschreiten des Ausgleichsschwellenwerts kein explizites Ziel ist. Ausgleichsschwellenwerte sind lediglich Trigger.

## Aktivitätsschwellenwerte
Mitunter ist die Gesamtlast des Clusters niedrig, obwohl Knoten relativ unausgeglichen sind. Der Grund hierfür kann bloß die Tageszeit sein oder dass der Cluster neu ist und einem Bootstrapping unterzogen wird. In beiden Fällen möchten Sie möglicherweise auf einen Lastenausgleich verzichten, da der Nutzen sehr gering ist und Sie bloß viel Zeit für das Verschieben von Netzwerk- und Computeressourcen aufwenden. Im Ressourcen-Manager gibt es mit dem Aktivitätsschwellenwert ein weiteres Steuerungsinstrument, mit dem Sie eine absolute Untergrenze für eine Aktivität angeben können. Wenn eine Knoten nicht mindestens diese Last aufweist, wird kein Ausgleich ausgelöst, selbst wenn der Ausgleichsschwellenwert erreicht wird. Nehmen wir als Beispiel Berichte mit den folgenden Summen für die Nutzung auf diesen Knoten. Nehmen wir außerdem an, dass wir unseren Ausgleichsschwellenwert 3 beibehalten, aber nun auch den Aktivitätsschwellenwert 1536 haben. Obgleich im ersten Fall der Cluster gemäß dem Ausgleichsschwellenwert unausgeglichen ist, erreicht kein Knoten den Mindestaktivitätsschwellenwert, weshalb wir nicht eingreifen. Im folgenden Beispiel überschreitet Knoten 1 ein wenig den Aktivitätsschwellenwert, weshalb ein Ausgleich erfolgt.

![Beispiel eines Aktivitätsschwellenwerts][Image3]

Aktivitätsschwellenwerte werden wie Ausgleichsschwellenwerte metrikbezogen im Clustermanifest definiert:

``` xml
      <Section Name="MetricActivityThresholds">
        <Parameter Name="Memory" Value="1536"/>
      </Section>
```

## Gemeinsamer Lastenausgleich von Diensten
Ein interessanter Hinweis ist, dass die Entscheidung, ob der Cluster unausgeglichen ist oder nicht, clusterweit gefällt wird. Doch die Korrektur darin besteht, einzelne Dienstreplikate und -instanzen zu verschieben. Das ist einleuchtend, nicht wahr? Wenn Arbeitsspeicher auf einem Knoten gestapelt wird, können mehrere Replikate oder Instanzen dazu beitragen. Dadurch kann das Verschieben aller Replikate oder Instanzen erforderlich sein, die die betroffene, unausgeglichene Metrik nutzen.

Gelegentlich aber ruft uns ein Kunde an oder schickt uns ein Ticket, das besagt, dass ein nicht unausgeglichener Dienst verschoben wurde. Wie kann das passieren, wenn alle Metriken dieses Diensts zum Zeitpunkt der anderen Diskrepanz (geradezu perfekt) ausgeglichen waren? Das wollen wir uns einmal ansehen.

Nehmen Sie beispielsweise die vier Dienste: S1, S2, S3 und S4. S1 meldet die Metriken M1 und M2, S2 meldet M2 und M3, S3 meldet M3 und M4, und S4 meldet M99. Sicherlich können Sie erkennen, in welche Richtung wir hier gehen. Wir haben eine Kette! Aus Sicht des Ressourcen-Managers haben wir nicht wirklich vier unabhängige Dienste, sondern eine Reihe von Diensten, die in Bezug zueinander stehen (S1, S2 und S3), und einen Dienst, der auf sich allein gestellt ist.

![Gemeinsamer Lastenausgleich von Diensten][Image4]

Daher ist es möglich, dass ein Ungleichgewicht bei Metrik 1 bewirken kann, dass Replikate oder Instanzen, die zu Dienst 3 gehören, verschoben werden. In der Regel sind diese Verschiebungen eher begrenzt, können aber umfassender sein, was genau davon abhängt, wie unausgeglichen Metrik 1 geworden ist und welche Änderungen im Cluster für die Korrektur erforderlich waren. Wir können auch mit Sicherheit sagen, dass ein Ungleichgewicht bei den Metriken 1, 2 oder 3 nie Verschiebungen in Dienst 4 bewirkt. Dies wäre sinnlos, da das Verschieben von Replikaten oder Instanzen, die zu Dienst 4 gehören, keinerlei Auswirkung auf die Ausgeglichenheit der Metriken 1, 2 oder 3 hat.

Der Ressourcen-Manager ermittelt bei jeder Ausführung automatisch, welche Dienste in Beziehung stehen, da Dienste ggf. hinzugefügt oder entfernt werden oder sich die Konfiguration ihrer Metriken geändert haben könnte. Zwischen zwei Ausführungen des Lastenausgleichs könnte Dienst 2 z. B. so neu konfiguriert worden sein, dass Metrik 2 entfernt wurde. Dadurch wird die Kette zwischen Dienst 1 und Dienst 2 unterbrochen. Dann haben Sie anstelle von zwei Dienstgruppen drei:

![Gemeinsamer Lastenausgleich von Diensten][Image5]

## Nächste Schritte
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
- Bewegungskosten sind eine Möglichkeit, dem Clusterressourcen-Manager mitzuteilen, dass bestimmte Dienste teurer zu bewegen sind als andere. Weitere Informationen zu Bewegungskosten finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-movement-cost.md).
- Der Clusterressourcen-Manager bietet mehrere Drosselungen, die Sie konfigurieren können, um Änderungen im Cluster zu verlangsamen. Sie sind normalerweise nicht erforderlich, aber bei Bedarf finden Sie [hier](service-fabric-cluster-resource-manager-advanced-throttling.md) weitere Informationen.


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0316_2016-->