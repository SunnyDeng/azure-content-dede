<properties
   pageTitle="Service Fabric-Anwendungsupgrade | Microsoft Azure"
   description="Dieser Artikel bietet eine Einführung in das Upgrade einer Service Fabric-Anwendung, einschließlich Wahl des Upgrademodus und der durchzuführenden Integritätsüberprüfungen."
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

Eine Service Fabric-Anwendung ist eine Sammlung von Diensten. Während eines Upgrades vergleicht Service Fabric das neue [Anwendungsmanifest](service-fabric-application-model.md#describe-an-application) mit der vorherigen Version und ermittelt, welche Dienste in der Anwendung aktualisiert werden müssen. Dies erfolgt durch Vergleichen der Versionsnummern in den Dienstmanifesten mit der vorherigen Version. Wenn sich ein Dienst nicht geändert hat, wird er nicht aktualisiert.

## Übersicht über parallele Upgrades

Bei einem parallelen Anwendungsupgrade wird das Upgrade in verschiedenen Phasen durchgeführt. In jeder Phase wird das Upgrade auf eine Teilmenge von Knoten im Cluster angewendet, die als "Upgradedomäne" bezeichnet wird. So bleibt die Anwendung während des gesamten Upgrades immer verfügbar. Während des Upgrades kann der Cluster eine Mischung aus der alten und neuen Version enthalten. Aus diesem Grund müssen die beiden Versionen aufwärts- und abwärtskompatibel sein. Wenn sie nicht kompatibel sind, muss der Anwendungsadministrator ein mehrstufiges Upgrade bereitstellen, um die Verfügbarkeit aufrechtzuerhalten. Dies erfolgt durch Ausführen eines Upgrades mit einer Zwischenversion der Anwendung, die mit der früheren Version kompatibel ist, bevor dann das Upgrade auf die endgültige Version vorgenommen wird.

Upgradedomänen werden beim Konfigurieren des Clusters im Clustermanifest angegeben. Sie sollten nicht davon ausgehen, dass Upgrades in Upgradedomänen in einer bestimmten Reihenfolge ausgeführt werden. Bei einer Upgradedomäne handelt es sich um eine logische Bereitstellungseinheit für eine Anwendung. Upgradedomänen ermöglichen eine gleichbleibend hohe Verfügbarkeit der Dienste während eines Upgrades.

Nicht parallele Upgrades sind möglich, wenn das entsprechende Upgrade auf alle Knoten im Cluster angewendet wird. Dies ist der Fall, wenn die Anwendung nur über eine Upgradedomäne verfügt. Davon wird jedoch abgeraten, da der Dienst zum Zeitpunkt des Upgrades nicht aktiv und nicht verfügbar ist. Darüber hinaus übernimmt Azure keine Garantie, wenn ein Cluster mit nur einer Upgradedomäne eingerichtet wird.

## Integritätsprüfungen bei Upgrades

Für ein Upgrade müssen Integritätsrichtlinien festgelegt (oder Standardwerte verwendet) werden. Ein Upgrade wird als erfolgreich bezeichnet, wenn alle Upgradedomänen innerhalb der angegebenen Timeouts aktualisiert werden und alle Upgradedomänen als fehlerfrei gelten. Fehlerfrei bedeutet für eine Upgradedomäne, dass alle in der Integritätsrichtlinie angegebenen Integritätsprüfungen für die Upgradedomäne erfolgreich durchgeführt werden. In einer Integritätsrichtlinie kann beispielsweise vorgegeben werden, dass alle Dienste in einer Anwendungsinstanz entsprechend der Definition der Integrität von Service Fabric <em>fehlerfrei</em> sein müssen.

Integritätsrichtlinien und -prüfungen während eines Upgrades von Service Fabric sind dienst- und anwendungsunabhängig. Das heißt, es werden keine dienstspezifischen Tests durchgeführt. Beispiel: Für einen Dienst ist eine Anforderung für einen minimalen Durchsatz festgelegt. Service Fabric verfügt jedoch nicht über die Informationen für einen entsprechenden Test und überprüft daher den für die Anwendung definierten Durchsatz nicht. Informationen zu den durchgeführten Prüfungen finden Sie in den [Artikeln zur Integrität](service-fabric-health-introduction.md). Bei diesen Prüfungen während des Upgrades wird beispielsweise überprüft, ob das Anwendungspaket richtig kopiert wurde, ob die Instanz gestartet wurde usw.

Die Anwendungsintegrität ist eine Aggregation der untergeordneten Entitäten der Anwendung. Kurz gesagt evaluiert Service Fabric die Integrität der Anwendung über die für die Anwendung gemeldete Integrität sowie über die für alle Dienste der Anwendung jeweils gemeldeten Integritätswerte. Die Integrität der Anwendungsdienste wird durch Aggregation der Integritätswerte der zugehörigen untergeordneten Elemente wie z. B. der Dienstreplikate weiter evaluiert. Wenn die Integritätsrichtlinie für die Anwendung erfüllt ist, kann das Upgrade fortgesetzt werden. Wenn die Integritätsrichtlinie jedoch nicht erfüllt ist, schlägt das Anwendungsupgrade fehl.

## Upgrademodi

Der allgemeine (und empfohlene) Modus für Upgrades ist der überwachte Modus ("Monitored"). Im überwachten Modus wird das Upgrade zunächst in einer Upgradedomäne ausgeführt. Wenn alle Integritätsprüfungen erfolgreich durchgeführt wurden (entsprechend der angegebenen Richtlinie), wird die nächste Upgradedomäne aktualisiert usw. Wenn die Integritätsprüfungen fehlschlagen und/oder Timeouts überschritten werden, wird das Upgrade für die Upgradedomäne entweder zurückgesetzt oder der Modus in den Modus "Unmonitored/Manual" geändert, wenn diese Option zum Zeitpunkt des Upgrades ausgewählt wurde.

Der Modus "Unmonitored/Manual" erfordert nach jedem Upgrade in einer Upgradedomäne einen manuellen Eingriff, um das Upgrade für die nächste Upgradedomäne zu starten. Service Fabric-Integritätsprüfungen werden nicht durchgeführt. Stattdessen hängt es von der eingreifenden Person ab, ob Integritäts- oder Statusprüfungen vor dem Starten des Upgrades in der nächsten Upgradedomäne durchgeführt werden.

## Flussdiagramm eines Anwendungsupgrades

Das Flussdiagramm unten soll dem Verständnis des Upgradevorgangs einer Service Fabric-Anwendung dienen. Im Speziellen ist hier schematisch dargestellt, wie anhand der Timeouts, z. B. *HealthCheckStableDuration*, *HealthCheckRetryTimeout* und *UpgradeHealthCheckInterval*, gesteuert wird, ob ein Upgrade in einer Upgradedomäne als erfolgreich oder fehlgeschlagen gilt.

![Upgradevorgang für eine Service Fabric-Anwendung][image]


## Nächste Schritte

[Lernprogramm für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md)

[Anwendungsupgradeparameter](service-fabric-application-upgrade-parameters.md)

[Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)

[Weiterführende Themen](service-fabric-application-upgrade-advanced.md)

[Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=Nov15_HO2-->