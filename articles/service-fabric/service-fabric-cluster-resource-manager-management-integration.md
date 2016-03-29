<properties
   pageTitle="Clusterressourcen-Manager von Service Fabric – Integration der Verwaltung | Microsoft Azure"
   description="Übersicht über die Integrationspunkte zwischen dem Clusterressourcen-Manager und der Service Fabric-Verwaltung."
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


# Integration des Clusterressourcen-Managers in die Service Fabric-Clusterverwaltung
Der Ressourcen-Manager ist zwar nicht die Hauptkomponente von Service Fabric für die Ausführung von Verwaltungsvorgängen (wie z. B. Anwendungsupgrades), ist aber daran beteiligt. Zunächst hilft der Ressourcen-Manager bei der Verwaltung, indem der gewünschte Zustand des Clusters und der darin enthaltenen Dienste hinsichtlich Ressourcenbereitstellung und Lastenausgleich nachverfolgt wird. Außerdem ändert er das Subsystem von Service Fabric für Integrität, wenn etwas nicht in Ordnung ist. Ein weiterer Aspekt der Integration hat mit der Funktionsweise von Upgrades zu tun. Insbesondere im Verlauf von Upgrades ändern sich verschiedene Aspekte der Funktionsweise des Ressourcen-Managers, und verschiedene spezielle Verhalten werden ausgelöst. Darauf gehen wir weiter unten ein.

## Integration von Integrität
Der Ressourcen-Manager überwacht ständig die von Ihnen für Ihre Dienste definierten Regeln und gibt Integritätswarnungen und Fehler aus, wenn diese Regeln nicht erfüllt werden. Wenn z. B. ein Knoten seine Kapazität vollständig ausgeschöpft hat und der Ressourcen-Manager die Situation nicht korrigieren kann, wird eine Integritätswarnung ausgegeben, die angibt, welcher Knoten bei welchen Metriken keine Kapazität mehr hat.

Ein weiteres Beispiel der Anzeige von Integritätswarnungen durch den Ressourcen-Manager ist, wenn Sie eine Platzierungseinschränkung (z. B. „NodeColor == Blue“) definiert haben und ein Verstoß gegen diese Einschränkung erkannt wird. Dies erfolgt sowohl für benutzerdefinierte Einschränkungen als auch für die Standardeinschränkungen (z. B. Verteilung von Fehler- und Upgradedomänen), die der Ressourcen-Manager für Sie erzwingt. Hier ein Beispiel eines solchen Integritätsberichts. In diesem Fall gilt der Integritätsbericht einer der Partitionen des Systemdiensts, da die Replikate dieser Partition vorübergehend in zu wenig Fehlerdomänen abgelegt sind, was bei einer Kette von Ausfällen passieren kann:

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_FaultDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_FaultDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: FaultDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Diese Integritätsmeldung gibt Folgendes an:

1.	Alle Replikate selbst sind fehlerfrei (dies hat für Service Fabric erste Priorität).
2.	Derzeit wird gegen die Einschränkung für die Verteilung von Fehlerdomänen verstoßen (was heißt, dass eine bestimmte Fehlerdomäne mehr Replikate für diese Partition aufweist, als sie sollte).
3.	Den Knoten mit dem Replikat, der den Verstoß verursacht (mit der ID „3d1a4a68b2592f55125328cd0f8ed477“).
4.	Den Zeitpunkt des Verstoßes (10.08.2015 19:13:02 Uhr). Dies sind aussagekräftige Daten für eine Warnung, die in der Produktion ausgelöst wird. So werden Sie darüber informiert, dass etwas schiefgelaufen ist und Sie einen Blick darauf werfen sollten. In diesem Fall möchten wir herausfinden, warum der Ressourcen-Manager keine andere Wahl hatte, als die Replikate in der Fehlerdomäne abzulegen. Eine Möglichkeit ist, dass alle Knoten in den anderen Fehlerdomänen ausgefallen waren und es nicht genügend Ersatzfehlerdomänen gab. Oder wenn es genügend aktive Domänen gab, ist etwas anderes vorgefallen, das bewirkt hat, dass die Knoten in diesen anderen Fehlerdomänen ungültig waren (wie z. B. eine „InvalidDomain“-Richtlinie für den Dienst).

Angenommen, Sie möchten einen Dienst erstellen oder der Ressourcen-Manager versucht, einen Ort zu finden, an dem verschiedene Dienste platziert werden können, doch es gibt anscheinend keine Lösungen, die funktionieren. Dafür kann es zahlreiche Gründe geben, doch meist ist eine der beiden folgenden Bedingungen dafür verantwortlich:

1.	Eine vorübergehende Bedingung hat es unmöglich gemacht, diese Dienstinstanz bzw. dieses Replikat ordnungsgemäß zu platzieren.
2.	Die Anforderungen des Diensts sind auf eine Weise falsch konfiguriert, die das Erfüllen seiner Anforderungen unmöglich machen.

Bei beiden Bedingungen zeigt der Ressourcen-Manager einen Integritätsbericht an, anhand dessen Informationen Sie bestimmen können, was passiert ist und warum der Dienst nicht platziert werden kann. Wir nennen diesen Prozess „Sequenz zum Entfernen von Einschränkungen“. In dessen Verlauf durchlaufen wir die konfigurierten Einschränkungen, die sich auf den Dienst auswirken, und prüfen, was sie entfernen. Wenn Komponenten nicht platziert werden können, lässt sich prüfen, welche Knoten entfernt wurden und warum. Nun wollen wir uns die verschiedenen Einschränkungen und ihre Prüfungen in diesen Integritätsberichten genauer ansehen. Zumeist erkennen Sie nicht, dass diese Einschränkungen Knoten entfernen, da sich die Einschränkungen standardmäßig auf den Stufen „Soft“ oder „Optimize“ befinden (was zuvor erwähnt wurde). Sie können sie erkennen, wenn sie als „harte“ Einschränkungen behandelt werden, weshalb wir sie hier der Vollständigkeit halber präsentieren:

-	„ReplicaExclusionStatic“ und „ReplicaExclusionDynamic“ – Dies ist eine interne Einschränkung, die angibt, dass wir während der Suche festgestellt haben, dass zwei Replikate auf einem Knoten platziert würden (was nicht zulässig ist). Die Regel für „ReplicaExclusionStatic“ und „ReplicaExclusionDynamic“ ist fast identisch. Die Einschränkung „ReplicaExclusionDynamic“ besagt, dass wir dieses Replikat hier nicht platzieren können, da die einzige vorgeschlagene Lösung hier bereits ein Replikat platziert hat. Dies unterscheidet sich vom Ausschluss „ReplicaExclusionStatic“, der nicht auf einen vermeintlichen Konflikt, sondern auf einen tatsächlichen verweist, denn es gibt bereits ein Replikat auf dem Knoten. Ist das verwirrend? Ja. Ist das wirklich ein Problem? Nein. Es reicht der Hinweis, dass wenn Sie eine Sequenz zum Entfernen von Einschränkungen sehen, die entweder die Einschränkung „ReplicaExclusionStatic“ oder „ReplicaExclusionDynamic“ aufweist, der Ressourcen-Manager davon ausgeht, dass es zur Platzierung aller Replikate nicht genügend Knoten gibt. Die weiteren Einschränkungen informieren uns meist, wie wir enden, wenn von Anfang an zu wenig vorhanden sind.
-	PlacementConstraint: Wenn diese Meldung angezeigt wird, bedeutet dies, dass wir einige Knoten entfernt haben, da sie nicht mit den Platzierungseinschränkungen des Diensts übereinstimmen. Als Teil dieser Nachricht finden wir die derzeit konfigurierten Platzierungseinschränkungen.
-	NodeCapacity: Wenn Sie diese Einschränkung sehen, bedeutet dies, dass wir die Replikate nicht auf den angegebenen Knoten platzieren konnten, da dadurch die Kapazität des Knotens überschritten würde.
-	Affinity: Diese Einschränkung gibt an, dass wir das Replikat nicht auf den betroffenen Knoten platzieren konnten, da dies zu einem Verstoß gegen die Affinitätseinschränkung führen würde.
-	FaultDomain und UpgradeDomain: Diese Einschränkung entfernt Knoten, wenn das Platzieren des Replikats auf den angegebenen Knoten eine Überlastung in einer bestimmten Fehler- oder Upgradedomäne bewirken würde.
-	PreferredLocation: Diese Einschränkung wird normalerweise nicht angezeigt. Sie bewirkt, dass Knoten aus der Lösung entfernt werden, da sie standardmäßig nur auf Optimierung ausgelegt ist. Darüber hinaus ist die Einschränkung „PreferredLocation“ meist nur während Upgrades vorhanden (wenn sie verwendet wird, um Replikate dorthin zurück zu verschieben, wo sie bei Beginn des Upgrades waren). Sie wird jedoch möglicherweise angezeigt.

## Upgrades
Der Ressourcen-Manager hilft auch bei Anwendungs- und Clusterupgrades, um sicherzustellen, dass das Upgrade reibungslos verläuft. Er soll aber auch sicherstellen, dass die Regeln und Leistung des Clusters nicht beeinträchtigt werden.

### Fortsetzen des Erzwingens der Regeln
Ein überaus wichtige Aspekt ist, dass die Regeln, d. h. die strikten Kontrollen von Aspekten wie Platzierungseinschränkungen, auch im Verlauf von Upgrades weiter erzwungen werden. Sie meinen, dass sei wohl selbstverständlich, was es auch ist und wir hiermit noch einmal betonen möchten. Der positive Aspekt ist, dass Sie sicherstellen können, dass bestimmte Workloads nicht auf bestimmten Knoten ausgeführt werden, und zwar auch während des Upgrades, ohne dass das Betriebsteam eingreifen muss. Wenn Ihre Umgebung zahlreiche Einschränkungen aufweist, können Upgrades lange dauern, da es nur einige wenige Optionen gibt, was mit einem Dienst passieren kann, wenn er für das Einspielen von Patches heruntergefahren werden muss.

### Intelligenter Ersatz
Wenn ein Upgrade gestartet wird, erstellt der Ressourcen-Manager eine Momentaufnahme der aktuellen Anordnung des Clusters und versucht, nach Abschluss des Upgrades diesen Status wiederherzustellen. Der Grund dafür ist einfach. Erstens wird sichergestellt, dass nur ein paar Übergänge für jeden Dienst im Rahmen des Upgrades erfolgen (die Verschiebung vom betroffenen Knoten und wieder zurück). Zweitens wird sichergestellt, dass das Upgrade selbst keine große Auswirkung auf das Layout des Clusters hat. Wenn der Cluster vor dem Upgrade entsprechend angeordnet war, wird dies auch im Anschluss daran der Fall sein.

### Weniger Änderungen
Wichtig ist der Hinweis, dass der Ressourcen-Manager während des Upgrades den Lastenausgleich für die Entität deaktiviert, für die das Upgrade erfolgt. Wenn Sie zwei verschiedene Anwendungsinstanzen haben und auf einer davon ein Upgrade starten, wird der Lastenausgleich für diese Anwendungsinstanz, aber nicht für die andere angehalten. Das Verhindern eines reaktiven Lastenausgleichs verhindert, dass der Ressourcenverantwortliche nicht so auf das Upgrade reagiert: „Oh nein! Ein leerer Knoten! Da muss doch was drauf“. Verhindert werden auch viele zusätzliche Verschiebungen für Dienste im Cluster, die wieder rückgängig gemacht werden müssen, wenn die Dienste nach Abschluss des Upgrades zurück auf die Knoten verschoben werden müssen. Wenn das betreffende Upgrade ein Clusterupgrade ist, wird der Lastenausgleich für die Dauer des Upgrades im gesamten Cluster angehalten (Einschränkungsüberprüfungen – Sicherstellen der Erzwingung von Regeln – Aktiv bleiben).

### Gelockerte Regeln
Einer der Aspekte, der allgemein für Upgrades gilt, ist, dass Sie wollen, dass das Upgrade abgeschlossen wird, auch wenn der Cluster insgesamt einschränkt oder voll ist. Wir haben bereits angesprochen, wie das abläuft. Doch im Verlauf von Upgrades ist dies noch wichtiger, da meist 5-20 % Ihres Clusters zu dem Zeitpunkt deaktiviert sind, an dem das Upgrade in den Cluster eingespielt wird. Und dieser Workload muss woanders bewältigt werden. Hier kommt das Konzept der gepufferten Kapazität zum Tragen, das wir bereits angesprochen haben. Während die gepufferte Kapazität während des Normalbetriebs berücksichtigt wird, füllt der Ressourcen-Manager diese im Verlauf von Upgrades bis zur Gesamtkapazität auf.

## Nächste Schritte
- Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md).

<!---HONumber=AcomDC_0316_2016-->