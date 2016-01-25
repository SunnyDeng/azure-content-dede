<properties
   pageTitle="Service Fabric-Anwendungsupgrade | Microsoft Azure"
   description="Dieser Artikel bietet eine Einführung in das Upgrade einer Service Fabric-Anwendung, einschließlich Wahl des Upgrademodus und der Durchführung der Integritätsüberprüfungen."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>


# Service Fabric-Anwendungsupgrade

Eine Azure Service Fabric Anwendung ist eine Sammlung von Diensten. Während eines Upgrades vergleicht Service Fabric das neue [Anwendungsmanifest](service-fabric-application-model.md#describe-an-application) mit der vorherigen Version und ermittelt, welche Dienste in der Anwendung aktualisiert werden müssen. Fabric Service erledigt dies durch Vergleichen der Versionsnummern im Dienstmanifest mit denen der vorherigen Version. Wenn sich ein Dienst nicht geändert hat, wird er nicht aktualisiert.

## Übersicht über parallele Upgrades

Bei einem parallelen Anwendungsupgrade wird das Upgrade in verschiedenen Phasen durchgeführt. In jeder Phase wird das Upgrade auf eine Teilmenge von Knoten im Cluster angewendet, die als Updatedomäne bezeichnet wird. So bleibt die Anwendung während des gesamten Upgrades immer verfügbar. Während des Upgrades kann der Cluster eine Mischung aus der alten und neuen Version enthalten.

Aus diesem Grund müssen die beiden Versionen aufwärts- und abwärtskompatibel sein. Wenn sie nicht kompatibel sind, muss der Anwendungsadministrator ein mehrstufiges Upgrade bereitstellen, um die Verfügbarkeit aufrechtzuerhalten. Der Administrator führt ein Upgrade mit einer Zwischenversion der Anwendung durch, die mit der früheren Version kompatibel ist, bevor dann das Upgrade auf die endgültige Version vorgenommen wird.

Updatedomänen werden im Clustermanifest angegeben, wenn Sie den Cluster konfigurieren. Sie sollten nicht davon ausgehen, dass Updates in Updatedomänen in einer bestimmten Reihenfolge empfangen werden. Bei einer Updatedomäne handelt es sich um eine logische Bereitstellungseinheit für eine Anwendung. Updatedomänen ermöglichen eine gleichbleibend hohe Verfügbarkeit der Dienste während eines Upgrades.

Nicht parallele Upgrades sind möglich, wenn das Upgrade auf alle Knoten im Cluster angewendet wird. Dies ist der Fall, wenn die Anwendung nur über eine Updatedomäne verfügt. Wir raten jedoch davon ab, da der Dienst zum Zeitpunkt des Upgrades nicht aktiv und nicht verfügbar ist. Darüber hinaus übernimmt Azure keine Garantie, wenn ein Cluster mit nur einer Updatedomäne eingerichtet wird.

## Integritätsprüfungen bei Upgrades

Für ein Upgrade müssen Integritätsrichtlinien festgelegt (oder Standardwerte verwendet) werden. Ein Upgrade wird als erfolgreich bezeichnet, wenn alle Updatedomänen innerhalb der angegebenen Timeouts aktualisiert werden und wenn alle Updatedomänen als fehlerfrei gelten. Eine fehlerfreie Updatedomäne bedeutet, dass die Aktualisierungsdomäne alle in der Integritätsrichtlinie angegebenen Integritätsprüfungen bestanden hat. Eine Integritätsrichtlinie kann beispielsweise vorgeben, dass alle Dienste in einer Anwendungsinstanz *fehlerfrei* laufen müssen, so wie die Integrität von Service Fabric definiert ist.

Integritätsrichtlinien und -prüfungen während eines Upgrades von Service Fabric sind dienst- und anwendungsunabhängig. Das heißt, es werden keine dienstspezifischen Tests durchgeführt. Ihr Dienst hat zum Beispiel möglicherweise eine minimale Durchsatzanforderung. Service Fabric verfügt jedoch nicht über die Informationen für einen entsprechenden Test und überprüft daher den für die Anwendung definierten Durchsatz nicht. Nähere Informationen über die Tests, die durchgeführt werden, finden Sie in den [Artikeln zur Integrität](service-fabric-health-introduction.md). Die Überprüfungen, die während eines Upgrades durchgeführt werden, beinhalten Tests, die prüfen, ob das Anwendungspaket richtig kopiert wurde, ob die Instanz gestartet wurde, und so weiter.

Die Anwendungsintegrität ist eine Aggregation der untergeordneten Entitäten der Anwendung. Kurz gesagt evaluiert Service Fabric die Integrität der Anwendung über die für die Anwendung gemeldete Integrität. Es wird auch die Integrität aller Dienste für die Anwendung auf diese Weise ausgewertet. Service Fabric wertet ebenso die Integrität der Anwendungsdienste durch Aggregation der Integritätswerte der zugehörigen untergeordneten Elemente wie z. B. der Dienstreplikate aus. Sobald die Anwendungsintegritätsrichtlinie erfüllt ist, kann das Upgrade fortgesetzt werden. Wenn die Integritätsrichtlinie verletzt wird, schlägt die Aktualisierung der Anwendung fehl.

## Upgrademodi

Der Modus, den wir für Upgrades empfehlen, ist der überwachte Modus, der am häufigsten verwendete Modus. Im überwachten Modus wird das Update zunächst in einer Updatedomäne ausgeführt. Wenn alle Integritätsprüfungen erfolgreich durchgeführt wurden (entsprechend der angegebenen Richtlinie), wird automatisch die nächste Updatedomäne aktualisiert. Wenn die Integritätsprüfungen fehlschlagen und/oder Timeouts überschritten werden, wird das Upgrade für die Updatedomäne entweder zurückgesetzt oder der Modus in den nicht überwachten manuellen Modus geändert (wenn diese Option zum Zeitpunkt des Upgrades ausgewählt wurde).

Der nicht überwachte manuelle Modus benötigt nach jedem Upgrade in einer Updatedomäne einen manuellen Eingriff, um das Upgrade für die nächste Updatedomäne zu starten. Es werden keine Service Fabric-Integritätsprüfungen ausgeführt. Der Administrator überprüft den Zustand oder Status vor dem Upgrade in der nächsten Updatedomäne.

## Flussdiagramm eines Anwendungsupgrades

Das Flussdiagramm unten kann Ihnen dabei helfen, den Upgradevorgang einer Service Fabric-Anwendung zu verstehen. Im Speziellen ist hier schematisch dargestellt, wie die Timeouts, z. B. *HealthCheckStableDuration*, *HealthCheckRetryTimeout* und *UpgradeHealthCheckInterval*, beim Steuern helfen, wenn das Upgrade in einer Updatedomäne als erfolgreich oder fehlgeschlagen gilt.

![Upgradevorgang für eine Service Fabric-Anwendung][image]


## Nächste Schritte

[Tutorial für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md)

[Upgradeparameter](service-fabric-application-upgrade-parameters.md)

[Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)

[Manuelle Upgrades und Upgrades mit einem Diff-Paket](service-fabric-application-upgrade-advanced.md)

[Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png

<!---HONumber=AcomDC_0114_2016-->