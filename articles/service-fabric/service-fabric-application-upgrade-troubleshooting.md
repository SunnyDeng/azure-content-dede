<properties
   pageTitle="Problembehandlung bei Service Fabric-Anwendungsupgrades"
   description="In diesem Artikel werden einige bekannte Probleme beim Upgrade einer Service Fabric-Anwendung und ihre Behebung behandelt."
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
   ms.date="08/11/2015"
   ms.author="subramar"/>

# Problembehandlung bei Anwendungsupgrades

In diesem Artikel werden einige bekannte Probleme beim Upgrade einer Service Fabric-Anwendung und ihre Behebung behandelt.

## Problembehandlung bei einem fehlgeschlagenen Anwendungsupgrade

Wenn ein Upgrade fehlschlägt, enthält die Ausgabe des Befehls **Get-ServiceFabricApplicationUpgrade** zusätzliche Informationen zum Debuggen des Fehlers. Diese Informationen können wie folgt verwendet werden:

1. Identifizieren des Fehlertyps
2. Identifizieren der Fehlerursache
3. Isolieren der fehlerhaften Komponente(n) zur weiteren Untersuchung

Diese Informationen sind verfügbar, sobald Service Fabric einen Fehler erkennt, unabhängig davon, ob die **FailureAction** darin besteht, das Upgrade zurückzusetzen oder anzuhalten.

### Identifizieren des Fehlertyps

In der Ausgabe von **Get-ServiceFabricApplicationUpgrade** gibt **FailureTimestampUtc** den Zeitstempel (in UTC) an, bei dem ein Upgradefehler von Service Fabric erkannt und die **FailureAction** ausgelöst wurde. **FailureReason** gibt eine der drei potenziellen allgemeinen Ursachen des Fehlers an:

1. **UpgradeDomainTimeout**: Gibt an, dass der Abschluss einer bestimmten Upgradedomäne zu lange dauerte und dass "UpgradeDomainTimeout" abgelaufen ist.
2. **OverallUpgradeTimeout**: Gibt an, dass das gesamte Upgrade zu lange dauerte und dass "UpgradeTimeout" abgelaufen ist.
3. **HealthCheck**: Gibt an, dass die Anwendung nach dem Upgrade einer Upgradedomäne entsprechend den angegebenen Integritätsrichtlinien fehlerhaft ist und dass "HealthCheckRetryTimeout" abgelaufen ist.

Diese Einträge werden in der Ausgabe nur angezeigt, wenn das Upgrade fehlschlägt und ein Rollback ausgeführt wird. Weitere Informationen werden je nach Art des Fehlers angezeigt.

### Untersuchen von Timeouts bei Upgrades

Timeoutfehler bei Upgrades sind am häufigsten auf Probleme mit der Dienstverfügbarkeit zurückzuführen. Die folgende Ausgabe ist typisch für Upgrades, bei denen beim Starten von Dienstreplikaten oder -instanzen in der neuen Codeversion Fehler auftreten. Im Feld **UpgradeDomainProgressAtFailure** wird eine Momentaufnahme der ausstehenden Upgradevorgänge zum Zeitpunkt des Fehlers erfasst.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

In diesem Beispiel ist zu sehen, dass das Upgrade in der Upgradedomäne *MYUD1* fehlgeschlagen ist und dass zwei Partitionen (*744c8d9f-1d26-417e-a60e-cd48f5c098f0*und *4b43f4d8-b26b-424e-9307-7a7a62e79750*) nicht mehr reagieren, sodass primäre Replikate nicht in den Zielknoten *Node1* und *Node4* platziert werden können (*WaitForPrimaryPlacement*). Mit dem Befehl **Get-ServiceFabricNode** kann überprüft werden, ob sich diese beiden Knoten in der Upgradedomäne *MYUD1* befinden. *UpgradePhase* gibt *PostUpgradeSafetyCheck* aus, was bedeutet, dass diese Sicherheitsprüfungen nach dem Aktualisieren aller Knoten in der Upgradedomäne stattfinden. Alle diese Informationen lassen auf ein mögliches Problem mit der neuen Version des Anwendungscodes schließen. Die häufigsten Probleme sind Dienstfehler im Vordergrund oder bei der Heraufstufung auf primäre Codepfade.

*PreUpgradeSafetyCheck* als *UpgradePhase* bedeutet, dass vor der eigentlichen Durchführung des Upgrades Probleme bei der Vorbereitung der Upgradedomäne aufgetreten sind. In diesem Fall sind die häufigsten Probleme Fehler im Hintergrund oder bei der Herabstufung von primären Codepfaden.

Der aktuelle **UpgradeState** ist *RollingBackCompleted*, d. h., das ursprüngliche Upgrade muss mit dem Wert "Rollback" für **FailureAction** durchgeführt worden sein, sodass nach dem Fehlschlagen automatisch ein Rollback des Upgrades ausgeführt wurde. Wenn das ursprüngliche Upgrade mit dem Wert "Manual" für **FailureAction** durchgeführt worden wäre, befände sich das Upgrade stattdessen in einem angehaltenen Zustand, um das Livedebuggen der Anwendung zuzulassen.

### Untersuchen von Fehlern bei der Integritätsprüfung

Fehler bei der Integritätsprüfung können durch eine Vielzahl zusätzlicher Probleme ausgelöst werden, die auftreten können, nachdem alle Knoten in einer Upgradedomäne aktualisiert und alle Sicherheitsprüfungen erfolgreich durchgeführt wurden. Die folgende Ausgabe ist typisch für einen Upgradefehler aufgrund fehlerhafter Integritätsprüfungen. Im Feld **UnhealthyEvaluations** wird entsprechend der vom Benutzer angegebenen [Integritätsrichtlinie](service-fabric-health-introduction.md) eine Momentaufnahme aller fehlgeschlagenen Integritätsprüfungen zum Zeitpunkt des Upgradefehlers erfasst.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Die Untersuchung von Fehlern bei der Integritätsprüfung erfordert zunächst ein umfassendes Verständnis des Service Fabric-Integritätsmodells. Aber auch ohne dieses Verständnis können wir sehen, dass zwei Dienste fehlerhaft sind: *fabric:/DemoApp/Svc3* und *fabric:/DemoApp/Svc2* zusammen mit dem Fehlerintegritätsbericht (in diesem Fall "InjectedFault"). In diesem Beispiel sind 2 von 4 Diensten fehlerhaft. Dies liegt unter dem Standardziel von 0 % fehlerhafte Dienste (*MaxPercentUnhealthyServices*).

Das Upgrade wurde nach dem Fehlschlagen angehalten, da beim Starten des Upgrades für **FailureAction** der Wert "Manual" angegeben wurde. Daher kann das Livesystem im fehlerhaften Zustand bei Bedarf überprüft werden, bevor weitere Aktionen durchgeführt werden.

### Wiederherstellen eines angehaltenen Upgrades

Wenn als **FailureAction** "Rollback" angegeben ist, ist keine Wiederherstellung erforderlich, da nach dem Fehlschlagen für das Upgrade automatisch ein Rollback ausgeführt wird. Wenn als **FailureAction** "Manual" angegeben ist, gibt es mehrere Möglichkeiten zur Wiederherstellung:

1. Manuelles Auslösen eines Rollbacks
2. Manuelles Durchlaufen des restlichen Upgrades
3. Fortsetzen des überwachten Upgrades

Der Befehl **Start ServiceFabricApplicationRollback** kann jederzeit verwendet werden, um einen Rollback für die Anwendung auszuführen. Wenn der Befehl erfolgreich zurückgegeben wird, wurde die Rollbackanforderung im System registriert und wird bald darauf gestartet.

Mit dem Befehl **Resume-ServiceFabricApplicationUpgrade** kann das restliche Upgrade manuell fortgesetzt werden. Dabei werden die einzelnen Upgradedomänen nacheinander durchlaufen. In diesem Modus werden vom System nur Sicherheitsprüfungen durchgeführt. Weitere Integritätsprüfungen werden nicht durchgeführt. Dieser Befehl kann nur verwendet werden, wenn *UpgradeState* den Wert *RollingForwardPending* aufweist, was bedeutet, dass das Upgrade der aktuellen Upgradedomäne abgeschlossen ist, die nächste Upgradedomäne jedoch noch nicht gestartet wurde (ausstehend ist).

Der Befehl **Update-ServiceFabricApplicationUpgrade** kann zum Fortsetzen des überwachten Upgrades mit Sicherheits- und Integritätsprüfungen verwendet werden.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

Das Upgrade wird ab der Upgradedomäne fortgesetzt, in der es zuletzt angehalten wurde. Dabei werden die gleichen Upgradeparameter und Integritätsrichtlinien wie zuvor verwendet. Bei Bedarf können beim Fortsetzen des Upgrades alle in der Ausgabe oben angegebenen Upgradeparameter und Integritätsrichtlinien im gleichen Befehl geändert werden. In diesem Beispiel wurde das Upgrade im überwachten Modus fortgesetzt, während alle anderen Parameter unverändert blieben, sodass die gleichen Parameter und Integritätsrichtlinien wie zuvor verwendet wurden.

## Weitere Problembehandlungen

### Service Fabric befolgt nicht die angegebenen Integritätsrichtlinien

Mögliche Ursache 1:

Service Fabric übersetzt für die Integritätsevaluierung alle Prozentsätze in die tatsächliche Anzahl der Entitäten (z. B. Replikate, Partitionen und Dienste) und rundet immer auf die nächste Zahl der gesamten Entitäten auf. Wenn für _MaxPercentUnhealthyReplicasPerPartition_ beispielsweise maximal 21 % angegeben und 5 Replikate vorhanden sind, lässt Service Fabric bei der Evaluierung der Partitionsintegrität bis zu 2 Replikate (d. h. `Math.Ceiling (5*0.21)`) zu. Dies sollte beim Festlegen der Integritätsrichtlinien berücksichtigt werden.

Mögliche Ursache 2:

Integritätsrichtlinien werden als Prozentsätze aller Dienste und nicht für bestimmte Dienstinstanzen angegeben. Angenommen, vor einem Upgrade umfasst eine Anwendung beispielsweise die vier Dienstinstanzen A, B, C und D, wobei Dienst D fehlerhaft ist, jedoch ohne signifikante Auswirkungen auf die Anwendung. Der bekannte fehlerhafte Dienst D soll beim Upgrade ignoriert werden, also setzen wir den *MaxPercentUnhealthyServices*-Parameter auf 25 % und setzen voraus, dass nur A, B und C fehlerfrei sein müssen. Allerdings kann während des Upgrades D fehlerfrei und C fehlerhaft werden. In diesem Fall wird das Upgrade dennoch erfolgreich abgeschlossen, da nur 25 % der Dienste fehlerhaft sind. Dies kann zu unerwarteten Fehlern führen, wenn unerwarteterweise C anstatt D fehlerhaft ist. In diesem Fall sollte D als anderer Diensttyp wie A, B und C modelliert werden. Da Integritätsrichtlinien für einzelne Diensttypen angegeben werden können, ist eine Anwendung unterschiedlicher Prozentschwellenwerte auf verschiedene Dienste basierend auf ihren Rollen in der Anwendung möglich.

### Ich habe keine Integritätsrichtlinie für das Anwendungsupgrade angegeben, das Upgrade schlägt jedoch aufgrund einiger Timeouts fehl, die ich nie angegeben habe

Wenn für die Upgradeanforderung keine Integritätsrichtlinien angegeben werden, werden die in der Datei *ApplicationManifest.xml* der aktuellen Version der Anwendung angegebenen Richtlinien verwendet (beim Upgrade der Anwendung X von Version 1 auf Version 2 werden z. B. die in Version 1 angegebenen Integritätsrichtlinien für Anwendung X verwendet). Wenn für das Upgrade eine andere Integritätsrichtlinie verwendet werden soll, muss die Richtlinie als Teil des API-Aufrufs für das Anwendungsupgrade angegeben werden. Beachten Sie, dass die Richtlinien, die als Teil des API-Aufrufs angegeben werden, nur für die Dauer des Upgrades gelten. Nach Abschluss des Upgrades werden wieder die in der Datei *ApplicationManifest.xml* angegebenen Richtlinien verwendet.

### Falsche Timeouts angegeben

Möglicherweise haben sich Benutzer gefragt, was geschieht, wenn die Timeouts uneinheitlich festgelegt sind, z. B. wenn *UpgradeTimeout* kleiner ist als *UpgradeDomainTimeout*. Die Antwort ist einfach: Es wird ein Fehler zurückgegeben. Andere mögliche Fälle sind: *UpgradeDomainTimeout* ist kleiner als die Summe von *HealthCheckWaitDuration* und *HealthCheckRetryTimeout*, oder *UpgradeDomainTimeout* ist kleiner als die Summe von *HealthCheckWaitDuration* und *HealthCheckWaitDuration*.

### Die Upgrades dauern zu lange

Der Zeitaufwand für ein Upgrade hängt von den verschiedenen angegebenen Integritätsprüfungen und Timeouts ab, die wiederum vom Zeitaufwand für das Upgrade der Anwendung abhängen (einschließlich Kopieren des Pakets, Bereitstellung und Stabilisierung). Wenn Timeouts zu kurz angesetzt werden, führt dies möglicherweise zu einer größeren Zahl fehlgeschlagener Upgrades. Daher empfiehlt sich eine konservative Vorgehensweise mit längeren Timeouts.

Eine kurze Erläuterung der Interaktion von Timeouts mit den Upgradezeiten:

Das Upgrade für eine Upgradedomäne kann nicht schneller abgeschlossen werden als *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Fehler beim Upgrade können nicht schneller auftreten als *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Die Upgradezeit für eine Upgradedomäne wird durch *UpgradeDomainTimeout* begrenzt. Wenn *HealthCheckRetryTimeout* und *HealthCheckStableDuration* ungleich null sind und die Integrität der Anwendung hin und her wechselt, wird für das Upgrade schließlich das Timeout bei *UpgradeDomainTimeout* überschritten. *UpgradeDomainTimeout* zählt rückwärts ab dem Moment, an dem das Upgrade für die aktuelle Upgradedomäne gestartet wird.

## Nächste Schritte

[Lernprogramm für Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md)

[Anwendungsupgradeparameter](service-fabric-application-upgrade-parameters.md)

[Weiterführende Themen](service-fabric-application-upgrade-advanced.md)

[Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=Oct15_HO3-->