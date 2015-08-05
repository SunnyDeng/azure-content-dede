<properties
   pageTitle="Verwenden von Systemintegritätsberichten für die Problembehandlung"
   description="Enthält Beschreibungen der Systemintegritätsberichte und der Verwendung für die Problembehandlung von Cluster- oder Anwendungsproblemen."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# Verwenden von Systemintegritätsberichten für die Problembehandlung

Für Service Fabric-Komponenten werden für alle Entitäten im Cluster standardmäßig Berichte erstellt. Im so genannten [Health Store](service-fabric-health-introduction.md#health-store) (Integritätsspeicher) werden Entitäten basierend auf den Systemberichten erstellt und gelöscht und in einer Hierarchie organisiert, in der Interaktionen zwischen den Entitäten erfasst werden.

> [AZURE.NOTE]Informieren Sie sich über das [Service Fabric-Integritätsmodell](service-fabric-health-introduction.md), um die auf die Integrität bezogenen Konzepte zu verstehen.

Die Systemintegritätsberichte sorgen für Transparenz in Bezug auf die Cluster- und Anwendungsfunktionen und weisen auf Probleme mit der Integrität hin. Für Anwendungen und Dienste wird mit den Systemintegritätsberichten überprüft, ob Entitäten implementiert sind und sich aus Sicht von Service Fabric richtig verhalten. Die Berichte umfassen keine Integritätsüberwachung der Geschäftslogik des Diensts und keine Erkennung von hängenden Prozessen. Benutzerdienste können die Integritätsdaten um spezielle Informationen zu ihrer Logik erweitern.

> [AZURE.NOTE]Watchdog-Integritätsberichte werden erst angezeigt, **nachdem** die Systemkomponenten eine Entität erstellt haben. Wenn eine Entität gelöscht wird, werden vom Health Store automatisch alle dazugehörigen Integritätsberichte gelöscht. Dasselbe gilt, wenn eine neue Instanz der Entität erstellt wird (z. B. eine neue Dienstreplikatinstanz): Alle Berichte, die der alten Instanz zugeordnet sind, werden gelöscht und im Store bereinigt.

Die Systemkomponentenberichte werden über die Quelle identifiziert, die mit dem Präfix *„System“* beginnt. Watchdogs können nicht das gleiche Präfix für ihre Quellen verwenden, da Berichte mit ungültigen Parametern abgelehnt werden. Wir sehen uns nun einige Systemberichte an, um zu verstehen, wodurch sie ausgelöst werden und wie mögliche Probleme behoben werden, die damit verbunden sind.

> [AZURE.NOTE]Service Fabric fügt weiterhin Berichte für relevante Bedingungen hinzu, die zu einer besseren Transparenz in Bezug auf die Vorgänge im Cluster bzw. in der Anwendung führen.

## Cluster-Systemintegritätsberichte
Die Entität für die Clusterintegrität wird im Health Store automatisch erstellt. Wenn alles richtig funktioniert, liegt also kein Systembericht vor.

### Verlust der Nachbarschaft
System.Federation meldet einen Fehler, wenn ein Verlust der „Nachbarschaft“ (Neighborhood) erkannt wird. Der Bericht von den einzelnen Knoten und die Knoten-ID wird in den Eigenschaftsnamen eingefügt. Wenn ein Verlust der Nachbarschaft im gesamten Service Fabric-Ring auftritt, sind meist zwei Ereignisse zu erwarten (für beide Seiten der Lücke wird ein Bericht erstellt). Falls weitere Nachbarschaften verloren gegangen sind, ist die Anzahl der Ereignisse höher. Im Bericht wird der Global Lease-Timeout als Gültigkeitsdauer (Time to Live, TTL) angegeben. Der Bericht wird jeweils zur Hälfte der Gültigkeitsdauer erneut gesendet, solange die Bedingung aktiv ist. Das Ereignis wird nach dem Ablaufen automatisch entfernt. Wenn der berichtende Knoten ausfällt, ist so trotzdem eine korrekte Bereinigung im Health Store möglich.

- SourceId: System.Federation
- Eigenschaft: Beginnt mit „Neighborhood“ und enthält Knoteninformationen.
- Nächste Schritte: Untersuchen, warum die Nachbarschaft verloren gegangen ist. Beispiel: Überprüfen der Kommunikation zwischen Clusterknoten.

## Knoten-Systemintegritätsberichte
System.FM steht für den Failover-Manager-Dienst und ist die Autorität, mit der die Informationen zu Clusterknoten verwaltet werden. Jeder Knoten sollte über einen Bericht von System.FM verfügen, in dem der Zustand angegeben wird. Die Knotenentitäten werden entfernt, wenn der Knoten deaktiviert wird.

### Knoten heraufgefahren/heruntergefahren
System.FM meldet „OK“, wenn der Knoten dem Ring beitritt (betriebsbereit), und „Error“, wenn der Knoten den Ring verlässt (nicht betriebsbereit, entweder aufgrund eines Upgrades oder eines Fehlers). Die vom Health Store erstellte Integritätshierarchie wird für bereitgestellte Entitäten in Korrelation mit System.FM-Knotenberichten aktiv. Ein Knoten wird als virtuelles übergeordnetes Element aller bereitgestellten Entitäten angesehen. Die bereitgestellten Entitäten auf diesem Knoten werden nicht über Abfragen verfügbar gemacht, wenn der Knoten inaktiv ist oder keinen Bericht gesendet hat oder wenn der Knoten über eine andere Instanz als die Instanz verfügt, die den Entitäten zugeordnet ist. Wenn FM meldet, dass der Knoten inaktiv ist oder neu gestartet wurde (neue Instanz), werden im Health Store automatisch die bereitgestellten Entitäten bereinigt, die nur auf dem inaktiven Knoten oder der vorherigen Instanz des Knoten vorhanden sein können.

- SourceId: System.FM
- Eigenschaft: State (Zustand)
- Nächste Schritte: Wenn der Knoten aufgrund eines Upgrades inaktiv ist, sollte er nach Abschluss des Vorgangs wieder hochfahren. Der Integritätsstatus sollte dann wieder als „OK“ angegeben werden. Falls der Knoten nicht hochfährt oder ein Fehler auftritt, ist eine eingehendere Untersuchung erforderlich.

Das folgende Beispiel zeigt das System.FM-Ereignis mit dem Integritätsstaus „OK“ für einen aktiven Knoten:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### Zertifikatablauf
System.FabricNode gibt die Meldung „Warning“ aus, wenn vom Knoten verwendete Zertifikate kurz vor dem Ablauf stehen. Es gibt drei Zertifikate pro Knoten: Certificate_cluster, Certificate_server und Certificate_default_client. Wenn das Ablaufdatum noch mindestens zwei Wochen entfernt ist, lautet der Berichtstyp „OK“. Wenn der Ablauf in weniger als zwei Wochen erfolgt, lautet der Berichtstyp „Warning“. Die Gültigkeitsdauer (TTL) dieser Ereignisse ist unendlich. Sie werden entfernt, wenn ein Knoten den Cluster verlässt.

- SourceId: System.FabricNode
- Eigenschaft: Beginnt mit „Certificate“ (Zertifikat) und enthält weitere Informationen zum Zertifikatstyp.
- Nächste Schritte: Aktualisieren Sie die Zertifikate, wenn ihr Ablauf kurz bevorsteht.

### Verletzung der Ladekapazität
Der Service Fabric Load Balancer gibt die Meldung „Warning“ aus, wenn eine Verletzung einer Knotenkapazität erkannt wird.

 - SourceId: System.PLB
 - Eigenschaft: Beginnt mit „Capacity“ (Kapazität).
 - Nächste Schritte: Überprüfen Sie die bereitgestellten Metriken, und zeigen Sie die aktuelle Kapazität auf dem Knoten an.

## Systemintegritätsberichte für Anwendungen
System.CM steht für den Cluster-Manager-Dienst und ist die Autorität, mit der die Informationen zur Anwendung verwaltet werden.

### Zustand
System.CM gibt die Meldung „OK“ aus, wenn die Anwendung erstellt oder aktualisiert wird. Der Health Store wird informiert, wenn die Anwendung gelöscht wird, damit sie aus dem Store entfernt werden kann.

- SourceId: System.CM
- Eigenschaft: State (Zustand)
- Nächste Schritte: Wenn die Anwendung erstellt wird, sollte sie über den CM-Integritätsbericht verfügen. Überprüfen Sie andernfalls den Zustand der Anwendung, indem Sie eine Abfrage ausgeben (z. B. PowerShell-Cmdlet Get-ServiceFabricApplication -ApplicationName <applicationName>).

Unten ist das State-Ereignis für die Anwendung „fabric:/WordCount“ dargestellt.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## Dienst-Systemintegritätsberichte
System.FM steht für den Failover-Manager-Dienst und ist die Autorität, mit der die Informationen zu Diensten verwaltet werden.

### Zustand
System.FM gibt die Meldung „OK“ aus, wenn der Dienst erstellt wird. Die Entität wird aus dem Health Store gelöscht, wenn der Dienst gelöscht wird.

- SourceId: System.FM
- Eigenschaft: State (Zustand)

Unten ist das State-Ereignis für den Dienst fabric:/WordCount/WordCountService dargestellt.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### Verletzung aufgrund von nicht platzierten Replikaten
System.PLB gibt die Meldung „Warning“ aus, wenn keine Platzierung für ein oder mehrere Dienstreplikate gefunden werden kann. Der Bericht wird entfernt, wenn er abgelaufen ist.

- SourceId: System.FM
- Eigenschaft: State (Zustand)
- Nächste Schritte: Überprüfen Sie die Diensteinschränkungen. Überprüfen Sie den aktuellen Status der Platzierung.

## Systemintegritätsberichte für Partitionen
System.FM steht für den Failover-Manager-Dienst und ist die Autorität, mit der die Informationen zu den Dienstpartitionen verwaltet werden.

### Zustand
System.FM gibt die Meldung „OK“ aus, wenn die Partition erstellt wurde und fehlerfrei ist. Die Entität wird aus dem Health Store gelöscht, wenn die Partition gelöscht wird. Wenn die Replikatanzahl der Partition unterhalb des Mindestwerts liegt, wird „Error“ gemeldet. Wenn der Mindestwert für die Replikatanzahl der Partition erfüllt ist, die Zielreplikatanzahl aber nicht, wird „Warning“ ausgegeben. Falls für die Partition ein Quorumverlust vorliegt, gibt FM die Meldung „Error“ aus. Andere wichtige Ereignisse sind: Eine Warnung, wenn die Neukonfiguration länger als erwartet dauert und wenn die Erstellung länger als erwartet dauert. Die erwarteten Zeiträume für die Erstellung oder Neukonfiguration sind basierend auf Dienstszenarien konfigurierbar. Wenn ein Dienst beispielsweise über Zustandsdaten im TB-Bereich verfügt, wie SQL Azure, dauert die Erstellung länger als bei einem Dienst mit einer geringeren Menge an Zustandsdaten.

- SourceId: System.FM
- Eigenschaft: State (Zustand)
- Nächste Schritte: Wenn der Integritätsstatus nicht „OK“ lautet, ist es möglich, dass einige Replikate für primäre oder sekundäre Elemente nicht richtig erstellt, geöffnet oder heraufgestuft werden. Häufig ist die Hauptursache ein Dienstfehler bei der Implementierung zum Öffnen oder Ändern von Rollen.

Das folgende Beispiel zeigt eine fehlerfreie Partition.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

Das folgende Beispiel zeigt die Integrität einer Partition, für die die Zielreplikatanzahl nicht erreicht wird. Nächste Schritte: Rufen Sie die Partitionsbeschreibung ab, in der angegeben ist, wie sie konfiguriert wurde: MinReplicaSetSize ist 2, TargetReplicaSetSize ist 7. Geben Sie anschließend die Anzahl der Knoten im Cluster an: 5. In diesem Fall können zwei Replikate also nicht platziert werden.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### Verletzung der Replikateinschränkung
System.PLB gibt die Meldung „Warning“ aus, wenn eine Verletzung der Replikateinschränkung erkannt wird und Replikate der Partition nicht platziert werden können.

- SourceId: System.PLB
- Eigenschaft: Beginnt mit „ReplicaConstraintViolation“.

## Systemintegritätsberichte für Replikate
System.RA steht für die Reconfiguration Agent-Komponente und ist die Autorität für den Replikatzustand.

### Zustand
System.RA gibt die Meldung „OK“ aus, wenn das Replikat erstellt wird.

- SourceId: System.RA
- Eigenschaft: State (Zustand)

Das folgende Beispiel zeigt ein fehlerfreies Replikat:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### Öffnungszustand des Replikats
Die Beschreibung dieses Integritätsberichts enthält den Startzeitpunkt (UTC), zu dem der API-Aufruf erfolgt ist. System.RA gibt die Meldung „Warning“ aus, wenn das Öffnen des Replikats länger als der konfigurierte Zeitraum dauert (Standardwert: 30 Minuten). Wenn die API sich auf die Dienstverfügbarkeit auswirkt, wird der Bericht deutlich schneller ausgegeben (konfigurierbares Intervall, standardmäßig 30 Sekunden). Dieser Zeitraum enthält auch die Zeit für das Öffnen des Replikators und des Diensts. Die Eigenschaft ändert sich in „OK“, wenn das Öffnen abgeschlossen ist.

- SourceId: System.RA
- Eigenschaft: ReplicaOpenStatus
- Nächste Schritte: Wenn der Integritätsstatus nicht „OK“ lautet, sollten Sie überprüfen, warum das Öffnen des Replikats länger als erwartet dauert.

### Langsamer Dienst-API-Aufruf
System.RAP und System.Replicator geben die Meldung „Warning“ aus, wenn ein Aufruf von Benutzerdienstcode länger als in der Konfiguration angegeben dauert. Die Warnung wird gelöscht, wenn der Aufruf abgeschlossen ist.

- SourceId: System.RAP oder System.Replicator
- Eigenschaft: Name der langsamen API Die Beschreibung liefert weitere Details dazu, wie lange die API bereits aussteht.
- Nächste Schritte: Untersuchen Sie, warum der Aufruf länger als erwartet dauert.

Im folgenden Beispiel sind eine Partition mit Quorumverlust und die Schritte zur Ermittlung der Ursache zu sehen. Eines der Replikate weist den Integritätsstatus „Warning“ auf, sodass wir also über die Integrität informiert sind. Es wird angezeigt, dass der Dienstvorgang länger als erwartet dauert. Das Ereignis wird von System.RAP gemeldet. Nach diesen Informationen besteht der nächste Schritt darin, sich den Dienstcode anzusehen und ihn zu untersuchen. Für diesen Fall löst die RunAsync-Implementierung des zustandsbehafteten Diensts einen Ausnahmefehler aus. Beachten Sie, dass für Replikate ein Recycling durchgeführt wird. Unter Umständen sehen Sie also keine Replikate mit dem Zustand „Warning“. Versuchen Sie erneut, den Integritätsstatus zu ermitteln, und achten Sie auf etwaige Unterschiede bei der Replikat-ID. Dies kann in bestimmten Fällen Anhaltspunkte ergeben.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Wenn die fehlerhafte Anwendung mit aktiviertem Debugger gestartet wird, wird in den Fenstern mit den Diagnoseereignissen die von RunAsync ausgelöste Ausnahme angezeigt:

![Visual Studio 2015-Diagnoseereignisse: RunAsync-Fehler in fabric:/HelloWorldStatefulApplication.][1]

Visual Studio 2015-Diagnoseereignisse: RunAsync-Fehler in fabric:/HelloWorldStatefulApplication.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Replikationswarteschlange ist voll
System.Replicator gibt die Meldung „Warning“ aus, wenn die Replikationswarteschlange voll ist. Auf dem primären Element passiert dies in der Regel, weil ein oder mehrere sekundäre Replikate beim Bestätigen von Vorgängen sehr langsam sind. Auf sekundären Elementen erfolgt dies gewöhnlich, wenn der Dienst langsam beim Anwenden der Vorgänge ist. Die Warnung wird gelöscht, wenn die Warteschlange nicht mehr voll ist.

- SourceId: System.Replicator
- Eigenschaft: PrimaryReplicationQueueStatus oder SecondaryReplicationQueueStatus, je nach Replikatsrolle.

## DeployedApplication-Systemintegritätsberichte
System.Hosting ist die Autorität auf bereitgestellten Entitäten.

### Aktivierung
System.Hosting gibt die Meldung „OK“ aus, wenn eine Anwendung auf dem Knoten aktiviert werden kann, und andernfalls „Error“.

- SourceId: System.Hosting
- Eigenschaft: Activation (Aktivierung) Enthält die Rolloutversion.
- Nächste Schritte: Untersuchen Sie, warum die Aktivierung nicht erfolgreich war, sofern keine Integrität gegeben ist.

Das folgende Beispiel zeigt eine erfolgreiche Aktivierung:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Herunterladen
System.Hosting gibt die Meldung „Error“ aus, wenn das Herunterladen des Anwendungspakets nicht erfolgreich ist.

- SourceId: System.Hosting
- Eigenschaft: Download:<RolloutVersion>
- Nächste Schritte: Untersuchen Sie, warum das Herunterladen für den Knoten nicht erfolgreich war.

## DeployedServicePackage-Systemintegritätsberichte
System.Hosting ist die Autorität auf bereitgestellten Entitäten.

### Aktivierung des Dienstpakets
System.Hosting gibt die Meldung „OK“ aus, wenn die Aktivierung des Dienstpakets auf dem Knoten erfolgreich ist, und andernfalls „Error“.

- SourceId: System.Hosting
- Eigenschaft: Activation (Aktivierung)
- Nächste Schritte: Untersuchen Sie, warum die Aktivierung nicht erfolgreich war.

### Aktivierung des Codepakets
System.Hosting gibt die Meldung „OK“ für jedes Codepaket aus, wenn die Aktivierung erfolgreich ist. Wenn die Aktivierung nicht erfolgreich ist, wird gemäß Konfiguration „Warning“ gemeldet. Wenn CodePackage nicht aktiviert werden kann oder mit einem Fehler beendet wurde, der über den konfigurierten Wert unter „CodePackageHealthErrorThreshold“ hinausgeht, gibt Hosting die Meldung „Error“ aus. Wenn mehrere Codepakete in einem Dienstpaket enthalten sind, wird jeweils ein eigener Aktivierungsbericht erstellt.

- SourceId: System.Hosting
- Eigenschaft: Das Präfix lautet CodePackageActivation, und sie enthält den Namen des Codepakets und den Einstiegspunkt. CodePackageActivation:<CodePackageName>:<SetupEntryPoint/EntryPoint>. Beispiel: CodePackageActivation:Code:SetupEntryPoint.

### Diensttypregistrierung
System.Hosting gibt die Meldung „OK“ aus, wenn das Registrieren des Diensttyps erfolgreich war. Es wird „Error“ gemeldet, wenn die Registrierung nicht in der vorgegebenen Zeit erfolgt ist (Konfiguration per ServiceTypeRegistrationTimeout). Wenn die Registrierung des Diensttyps für den Knoten aufgehoben wird, weil die Laufzeit geschlossen wurde, gibt Hosting die Meldung „Warning“ aus.

- SourceId: System.Hosting
- Eigenschaft: Das Präfix lautet ServiceTypeRegistration, und sie enthält den Diensttypnamen. Beispiel: „ServiceTypeRegistration:FileStoreServiceType“

Das folgende Beispiel zeigt ein fehlerfreies bereitgestelltes Dienstpaket.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Herunterladen
System.Hosting gibt die Meldung „Error“ aus, wenn das Herunterladen des Dienstpakets nicht erfolgreich ist.

- SourceId: System.Hosting
- Eigenschaft: Download:<RolloutVersion>
- Nächste Schritte: Untersuchen Sie, warum das Herunterladen für den Knoten nicht erfolgreich war.

### Upgradeüberprüfung
System.Hosting gibt die Meldung „Error“ aus, wenn die Überprüfung während des Upgrades nicht erfolgreich ist oder wenn das Upgrade auf dem Knoten zu einem Fehler führt.

- SourceId: System.Hosting
- Eigenschaft: Das Präfix lautet FabricUpgradeValidation, und sie enthält die Upgradeversion.
- Beschreibung: Zeigt auf den aufgetretenen Fehler.

## Nächste Schritte
[Gewusst wie: Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

[Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO4-->