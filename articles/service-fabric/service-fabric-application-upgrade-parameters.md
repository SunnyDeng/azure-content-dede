
<properties
   pageTitle="Service Fabric-Anwendungsupgrade: Upgradeparameter"
   description="In diesem Artikel werden die verschiedenen Parameter in Bezug auf das Upgrade einer Service Fabric-Anwendung beschrieben."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Anwendungsupgradeparameter

In diesem Artikel werden die verschiedenen Parameter beschrieben, die während des Upgrades einer Service Fabric-Anwendung gelten. Die Parameter enthalten den Namen und die Version der Anwendung. Sie sind Schalter, die die Timeouts und Integritätsprüfungen steuern, die während des Upgrades angewendet werden, und geben die Richtlinien an, die angewendet werden müssen, wenn ein Upgrade fehlschlägt.


<br>

| Parameter | Beschreibung |
| --- | --- |
| ApplicationName | Der Name der Anwendung, die aktualisiert wird. Beispiele: fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion | Die Version des Anwendungstyps, für den das Upgrade durchgeführt wird. |
| FailureAction | Die FailureAction-Eigenschaft beschreibt die Aktion, die von Service Fabric ausgeführt werden soll, wenn das Upgrade fehlschlägt. Die Anwendung kann auf die letzte Version vor dem Update zurückgesetzt werden (Rollback), oder das Anwendungsupgrade wird bei der aktuellen Upgradedomäne beendet und der Upgrademodus in "Manual" geändert. Zulässige Werte sind "Rollback" und "Manual". |
| HealthCheckWaitDurationSec | Die Wartezeit (in Sekunden) nach dem Abschluss des Upgrades in der Upgradedomäne, nach der Service Fabric die Integrität der Anwendung evaluiert. Diese Dauer kann auch als die Zeit betrachtet werden, die eine Anwendung ausgeführt werden soll, bevor sie als fehlerfrei gilt. Wenn die Integritätsprüfung erfolgreich ist, wird der Upgradevorgang bei der nächsten Upgradedomäne fortgesetzt. Wenn die Integritätsprüfung fehlschlägt, wartet Service Fabric auf ein Intervall ("UpgradeHealthCheckInterval"), bevor die Integritätsprüfung wiederholt wird, bis das "HealthCheckRetryTimeout" erreicht ist. Der empfohlene Standardwert ist 0 Sekunden. |
| HealthCheckRetryTimeoutSec | Die Zeit (in Sekunden), in der Service Fabric die Integritätsevaluierung fortsetzt, bevor das Upgrade als fehlgeschlagen deklariert wird. Der Standardwert ist 600 Sekunden. Dieser Zeitraum beginnt, nachdem "HealthCheckWaitDuration" erreicht wurde. Innerhalb von "HealthCheckRetryTimeout" kann Service Fabric möglicherweise mehrere Integritätsprüfungen der Anwendungsintegrität durchführen. Der Standardwert ist 10 Minuten, und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
| HealthCheckStableDurationSec | Die Wartezeit (in Sekunden), in der überprüft wird, ob die Anwendung stabil ist, bevor mit der nächsten Upgradedomäne fortgefahren oder das Upgrade abgeschlossen wird. Diese Wartezeit wird verwendet, um nicht erkannte Integritätsänderungen direkt nach dem Durchführen der Integritätsprüfung zu verhindern. Der Standardwert ist 0 Sekunden, und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
| UpgradeDomainTimeoutSec | Maximale Zeit (in Sekunden) für das Aktualisieren einer einzelnen Upgradedomäne. Nach Erreichen dieses Timeouts wird das Upgrade beendet und die Aktion ausgeführt, die durch "UpgradeFailureAction" angegeben ist. Der Standardwert ist "never" (unendlich), und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
| UpgradeTimeout | Ein Timeout (in Sekunden), das für das gesamte Upgrade gilt. Bei Erreichen dieses Timeouts wird das Upgrade beendet und die "UpgradeFailureAction" ausgelöst. Der Standardwert ist "never" (unendlich), und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
| UpgradeHealthCheckInterval | Diese Einstellung wird im Abschnitt "ClusterManager" des _Cluster-__Manifests_ angegeben (wird nicht als Teil des Upgrade-Cmdlets angegeben) und legt die Häufigkeit fest, mit der der Integritätsstatus geprüft wird (der Standardwert ist 60 Sekunden). |






<br>
## Dienstintegritätsevaluierung während des Anwendungsupgrades

<br> Die Kriterien für die Integritätsevaluierung sind optional. Wenn beim Starten eines Upgrades keine Kriterien für die Integritätsevaluierung angegeben werden, verwendet Service Fabric die Anwendungsintegritätsrichtlinien, die in der Datei "ApplicationManifest.xml" der zu aktualisierenden Anwendungsinstanz angegeben sind.


<br>

| Parameter | Beschreibung |
| --- | --- |
| ConsiderWarningAsError | Der Standardwert ist "False". Bei der Evaluierung der Integrität der Anwendung während des Upgrades werden die Warnereignisse für die Anwendung als Fehler behandelt. In der Standardeinstellung bewertet Service Fabric Warnereignisse in Bezug auf die Integrität nicht als Fehler, sodass das Upgrade auch bei Warnereignissen fortgesetzt werden kann. |
| MaxPercentUnhealthyDeployedApplications | Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der bereitgestellten Anwendungen an (siehe dazu den [Abschnitt zur Integrität](service-fabric-health-introduction.md)), die fehlerhaft sein können, bevor die Anwendung als fehlerhaft gilt und das Upgrade fehlschlägt. Dabei handelt es sich um die Integrität des Anwendungspakets im Knoten. Daher ist diese Einstellung nützlich, um das unmittelbare Problem während des Upgrades zu erkennen und um zu ermitteln, wo das im Knoten bereitgestellte Anwendungspaket fehlerhaft ist (abstürzt usw.). In einem typischen Fall erfolgt ein Lastenausgleich der Replikate der Anwendung auf den anderen Knoten, sodass die Anwendung fehlerfrei zu sein scheint und das Upgrade so fortgesetzt werden kann. Durch Angeben eines strikten Werts für "MaxPercentUnhealthyDeployedApplications" für die Integrität kann Service Fabric ein Problem mit der Anwendung schnell erkennen und das Upgrade fehlschlagen lassen. |
| MaxPercentUnhealthyServices | Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der Dienste in der Anwendungsinstanz an, die fehlerhaft sein können, bevor die Anwendung als fehlerhaft gilt und das Upgrade fehlschlägt. |
| MaxPercentUnhealthyPartitionsPerService | Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der Partitionen in einem Dienst an, die fehlerhaft sein können, bevor der Dienst als fehlerhaft gilt. |
| MaxPercentUnhealthyReplicasPerPartition | Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der Replikate in einer Partition an, die fehlerhaft sein können, bevor die Partition als fehlerhaft gilt. |
| UpgradeReplicaSetCheckTimeout | Zustandsloser Dienst – Service Fabric versucht innerhalb einer Upgradedomäne sicherzustellen, dass zusätzliche Instanzen des Diensts verfügbar sind. Wenn die Anzahl der Zielinstanzen größer als 1 ist, wartet Service Fabric bis zu einem maximalen Timeoutwert (angegeben durch die "UpgradeReplicaSetCheckTimeout"-Eigenschaft), dass mehr als eine Instanz verfügbar ist. Wenn das Timeout überschritten wird, fährt Service Fabric unabhängig von der Anzahl der Dienstinstanzen mit dem Upgrade fort. Wenn die Anzahl der Zielinstanzen gleich 1 ist, wartet Service Fabric nicht, sondern setzt das Upgrade direkt fort. Zustandsbehafteter Dienst – Service Fabric versucht innerhalb einer Upgradedomäne sicherzustellen, dass die Replikatgruppe über ein Quorum verfügt. Service Fabric wartet bis zu einem maximalen Timeoutwert (angegeben durch die "UpgradeReplicaSetCheckTimeout"-Eigenschaft), dass ein Quorum verfügbar ist. Wenn das Timeout überschritten wird, fährt Service Fabric unabhängig vom Quorum mit dem Upgrade fort. Diese Einstellung ist bei einem Rollforward auf "never" (unendlich) festgelegt (wird angewendet, wenn das Upgrade wie erwartet fortgesetzt wird) und bei einem Rollback auf 900 Sekunden (wird angewendet, wenn beim Upgrade Fehler auftreten und das Upgrade zurückgesetzt wird). |
| ForceRestart | Wenn Sie ein Konfigurations- oder Datenpaket aktualisieren, ohne den Dienstcode zu aktualisieren, startet Service Fabric den Dienst nur neu, wenn die "ForceRestart"-Eigenschaft als Teil des API-Aufrufs auf "true" festgelegt ist. Wenn das Upgrade abgeschlossen ist, benachrichtigt Service Fabric den Dienst, dass ein neues Konfigurations- oder Datenpaket verfügbar ist. Der Dienst muss dann die Änderungen anwenden. Bei Bedarf kann sich der Dienst neu starten. |



<br> <br> Die Kriterien für "MaxPercentUnhealthyServices", "MaxPercentUnhealthyPartitionsPerService" und "MaxPercentUnhealthyReplicasPerPartition" können pro Diensttyp für eine Anwendungsinstanz angegeben werden. Dadurch wird sichergestellt, dass für eine Anwendung, die verschiedene Diensttypen enthält, unterschiedliche Evaluierungsrichtlinien für die einzelnen Diensttypen festgelegt werden können. Ein zustandsloser Gatewaydienst kann beispielsweise einen "MaxPercentUnhealthyPartitionsPerService"-Wert aufweisen, der sich von dem Wert für einen zustandsbehafteten Moduldienst für eine bestimmte Anwendungsinstanz unterscheidet.

## Nächste Schritte


[Lernprogramm für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md)

[Weiterführende Themen](service-fabric-application-upgrade-advanced.md)

[Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md)

[Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=July15_HO4-->