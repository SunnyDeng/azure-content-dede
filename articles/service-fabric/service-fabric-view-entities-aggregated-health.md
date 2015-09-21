<properties
   pageTitle="Gewusst wie: Anzeigen der Integritätszusammenfassung für Azure Service Fabric-Entitäten"
   description="Es wird beschrieben, wie Sie eine Zusammenfassung der Integrität von Azure Service Fabric-Entitäten abfragen, anzeigen und auswerten, indem Sie Integritätsabfragen und allgemeine Abfragen nutzen."
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
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Gewusst wie: Anzeigen von Service Fabric-Integritätsberichten
Mit Service Fabric wird ein [Integritätsmodell](service-fabric-health-introduction.md) eingeführt. Es besteht aus Integritätsentitäten, auf denen Systemkomponenten und Watchdogs die von ihnen überwachten lokalen Bedingungen melden können. Im so genannten [Health Store](service-fabric-health-introduction.md#health-store) (Integritätsspeicher) werden alle Integritätsdaten zusammengefasst, um zu bestimmen, ob die Entitäten intakt sind.

Standardmäßig wird der Cluster mit den Integritätsberichten aufgefüllt, die von den Systemkomponenten gesendet werden. Weitere Informationen finden Sie unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric bietet Ihnen mehrere Möglichkeiten, den zusammengefassten Integritätsstatus der Entitäten abzurufen:

- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) oder andere Visualisierungstools

- Integritätsabfragen (per PowerShell/API/REST)

- Allgemeine Abfragen, mit denen eine Liste mit Entitäten zurückgegeben wird, von denen eine der Eigenschaften die Integrität ist (per PowerShell/API/REST)

Um diese Optionen zu demonstrieren, verwenden wir einen lokalen Cluster mit **5 Knoten**. Neben der Anwendung **fabric:/System** (standardmäßig vorhanden) werden auch einige andere Anwendungen bereitgestellt. Eine davon ist **fabric:/WordCount**. Diese Anwendung enthält einen zustandsbehafteten Dienst, der mit sieben Replikaten konfiguriert wurde. Da nur fünf Knoten vorhanden sind, wird von den Systemkomponenten per „Warning“ darauf hingewiesen, dass für die Partition die Zielanzahl nicht erreicht wurde.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Integrität im Service Fabric Explorer
Der Service Fabric Explorer bietet eine grafische Ansicht des Clusters. In der Abbildung unten ist Folgendes dargestellt:

- Die Anwendung **fabric:/WordCount** steht auf „rot“ („Error“), weil von MyWatchdog für die Availability-Eigenschaft ein Fehlerereignis gemeldet wurde.

- Einer der Dienste, **fabric:/WordCount/WordCount.Service**, ist „gelb“ gekennzeichnet („Warning“). Wie oben beschrieben, ist der Dienst mit sieben Replikaten konfiguriert, die nicht alle platziert werden können (da nur fünf Knoten vorhanden sind). Hier ist es zwar nicht zu sehen, aber die Dienstpartition ist aufgrund des Systemberichts „gelb“ gekennzeichnet. Die „gelbe“ Partition löst den „gelben“ Dienst aus.

- Der **Cluster** ist aufgrund der „roten“ Anwendung ebenfalls „rot“.

Für die Auswertung werden Standardrichtlinien aus dem Cluster- und Anwendungsmanifest verwendet, wobei es sich um strenge Richtlinien handelt (Ausfälle werden nicht toleriert).

![Ansicht des Clusters mit ServiceFabricExplorer][1]

Ansicht des Clusters mit ServiceFabricExplorer

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]Erfahren Sie mehr über den [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Integritätsabfragen
Service Fabric macht für jeden unterstützten [Entitätstyp](service-fabric-health-introduction.md#health-entities-and-hierarchy) Integritätsabfragen verfügbar. Darauf kann per API (Methoden unter FabricClient.HealthManager), mit PowerShell-Cmdlets und per REST zugegriffen werden. Mit diesen Abfragen werden vollständige Integritätsinformationen zur Entität zurückgegeben, z. B. der zusammengefasste Integritätsstatus, für die Entität gemeldete Integritätsereignisse, der untergeordnete Integritätsstatus (falls verfügbar) und Fehlerauswertungen, sofern Fehler für die Entität auftreten.

> [AZURE.NOTE]Eine Integritätsentität wird an den Benutzer zurückgegeben, wenn sie im Health Store vollständig aufgefüllt wurde: Die Entität verfügt über einen Systembericht, sie ist aktiv (nicht gelöscht), und übergeordnete Entitäten in der Hierarchiekette weisen Systemberichte auf. Falls eine dieser Bedingungen nicht erfüllt ist, geben die Integritätsabfragen eine Ausnahme mit Informationen dazu zurück, warum die Entität nicht zurückgegeben wurde.

Die Integritätsabfragen erfordern eine Übergabe des Entitätsbezeichners, der sich nach dem Entitätstyp richtet. Sie akzeptieren optionale Integritätsrichtlinienparameter. Wenn keine Angabe erfolgt, werden die [Integritätsrichtlinien](service-fabric-health-introduction.md#health-policies) aus dem Cluster- oder Anwendungsmanifest für die Auswertung verwendet. Außerdem werden auch Filter für die Teilrückgabe von untergeordneten Elementen oder Ereignissen akzeptiert, sofern die angegebenen Filter jeweils berücksichtigt werden.

> [AZURE.NOTE]Die Ausgabefilter werden auf der Serverseite angewendet, damit die Antwortgröße der Nachricht reduziert wird. Es wird empfohlen, die Filter zum Eingrenzen der zurückgegebenen Daten zu verwenden, anstatt Filter auf der Clientseite anzuwenden.

Ein Entitätsintegritätselement enthält die folgenden Informationen:

- Den zusammengefassten Integritätsstatus der Entität. Dieser wird vom Health Store anhand der Berichte zur Entitätsintegrität, des Integritätsstatus der untergeordneten Elemente (falls zutreffend) und der Integritätsrichtlinien berechnet. Erfahren Sie mehr über die [Auswertung der Entitätsintegrität](service-fabric-health-introduction.md#entity-health-evaluation).  

- Die Integritätsereignisse der Entität

- Für Entitäten, die über untergeordnete Elemente verfügen können, die Auflistung der Integritätsstatus für alle untergeordneten Elemente. Die Integritätsstatus enthalten den Entitätsbezeichner und den zusammengefassten Integritätsstatus. Rufen Sie zum Erhalten der vollständigen Integrität für ein untergeordnetes Element die Abfrageintegrität für den untergeordneten Entitätstyp auf, und übergeben Sie den Bezeichner des untergeordneten Elements.

- Wenn die Entität nicht fehlerfrei ist, die Fehlerauswertungen in Verbindung mit dem Bericht, über den der Status der Integrität ausgelöst wurde.

## Abrufen der Clusterintegrität
Hierbei wird die Integrität der Clusterentität zurückgegeben. Enthält die Integritätsstatus von Anwendungen und Knoten (untergeordnete Elemente des Clusters). Eingabe:

- [optional] Richtlinienzuordnung der Anwendungsintegrität mit Integritätsrichtlinien zum Überschreiben der Richtlinien für das Anwendungsmanifest.

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen, Knoten oder Anwendungen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen usw.).

### API
Erstellen Sie zum Abrufen der Clusterintegrität ein FabricClient-Element, und rufen Sie die GetClusterHealthAsync-Methode für das dazugehörige HealthManager-Element auf.

Die Clusterintegrität wird wie folgt abgerufen:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

Mit dem folgenden Verfahren wird die Clusterintegrität abgerufen, indem die benutzerdefinierte Clusterintegritätsrichtlinie und Filter für Knoten und Anwendungen verwendet werden. Beachten Sie, dass das System.Fabric.Description.ClusterHealthQueryDescription-Element mit allen Eingabedaten erstellt wird.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Clusterintegrität lautet Get-ServiceFabricClusterHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her. Status des Clusters: 5 Knoten, Systemanwendung und „fabric:/WordCount“ konfiguriert wie oben.

Mit dem folgenden Cmdlet wird die Clusterintegrität mit standardmäßigen Integritätsrichtlinien abgerufen. Der zusammengefasste Integritätsstatus lautet „Warning“, weil die Anwendung „fabric:/WordCount“ den Status „Warning“ aufweist. Beachten Sie, dass die Fehlerauswertungen mit Details zur Bedingung angezeigt werden, über die die zusammengefasste Integrität ausgelöst wurde.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

Mit dem folgenden PowerShell-Cmdlet wird die Integrität des Clusters mit der benutzerdefinierten Anwendungsrichtlinie abgerufen. Die Ergebnisse werden gefiltert, um nur Anwendungen und Knoten mit dem Status „Error“ oder „Warning“ abzurufen. Aus diesem Grund werden keine Knoten zurückgegeben, da sie alle fehlerfrei sind. Nur die Anwendung „fabric:/WordCount“ berücksichtigt den Anwendungsfilter. Da mit der benutzerdefinierten Richtlinie angegeben wird, die Warnung für die Anwendung „fabric:/WordCount“ als Fehler anzusehen, wird die Anwendung auf den Status „Error“ heraufgestuft. Dies gilt auch für den Cluster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Abrufen der Integrität von Knoten
Hierbei wird die Integrität einer Knotenentität zurückgegeben. Enthält die Integritätsereignisse, die für den Knoten gemeldet wurden. Eingabe:

- [erforderlich] Der Knotenname, mit dem der Knoten identifiziert wird.

- [optional] Einstellungen zur Clusterintegritätsrichtlinie zum Auswerten der Integrität.

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Knotenintegrität per API ein FabricClient-Element, und rufen Sie die GetNodeHealthAsync-Methode für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen. Der Ereignisfilter und die benutzerdefinierte Richtlinie werden mit System.Fabric.Description.NodeHealthQueryDescription übergeben.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Knotenintegrität lautet Get-ServiceFabricNodeHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her. Mit dem folgenden Cmdlet wird die Knotenintegrität mit standardmäßigen Integritätsrichtlinien abgerufen.

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

Mit dem folgenden Cmdlet wird die Integrität aller Knoten im Cluster abgerufen.

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Abrufen der Anwendungsintegrität
Gibt die Integrität einer Anwendungsentität zurück. Enthält die Integritätsstatus der bereitgestellten Anwendung und der untergeordneten Elemente des Diensts. Eingabe:

- [erforderlich] Anwendungsname (URI) zum Identifizieren der Anwendung

- [optional] Anwendungsintegritätsrichtlinie zum Überschreiben der Anwendungsmanifestrichtlinien

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen, Diensten oder bereitgestellten Anwendungen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Anwendungsintegrität ein FabricClient-Element, und rufen Sie die GetApplicationHealthAsync-Methode für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen-URI abgerufen.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen-URI abgerufen. Filter und die benutzerdefinierte Richtlinie werden mit System.Fabric.Description.ApplicationHealthQueryDescription angegeben.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Anwendungsintegrität lautet Get-ServiceFabricApplicationHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung „fabric:/WordCount“ zurückgegeben.

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

Mit dem folgenden PowerShell-Code werden die benutzerdefinierte Richtlinie übergeben und untergeordnete Elemente und Ereignisse zurückgegeben.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Abrufen der Dienstintegrität
Gibt die Integrität einer Dienstentität zurück. Enthält die Integritätsstatus der Partition. Eingabe:

- [erforderlich] Dienstname (URI) zum Identifizieren des Diensts
- [optional] Anwendungsintegritätsrichtlinie zum Überschreiben der Anwendungsmanifestrichtlinie
- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen und Partitionen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Dienstintegrität per API ein FabricClient-Element, und rufen Sie die GetServiceHealthAsync-Methode für das dazugehörige HealthManager-Element auf.

Im folgenden Beispiel wird die Integrität eines Diensts mit dem angegebenen Dienstnamen (URI) abgerufen:

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

Mit dem folgenden Code wird die Dienstintegrität für den angegebenen Dienstnamen-URI abgerufen. Filter und die benutzerdefinierte Richtlinie werden mit System.Fabric.Description.ServiceHealthQueryDescription angegeben.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Dienstintegrität lautet Get-ServiceFabricServiceHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her.

Mit dem folgenden Cmdlet wird die Dienstintegrität mit standardmäßigen Integritätsrichtlinien abgerufen.

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Abrufen der Partitionsintegrität
Gibt die Integrität einer Partitionsentität zurück. Enthält die Replikatintegritätsstatus. Eingabe:

- [erforderlich] Partitions-ID (GUID) zur Identifizierung der Partition

- [optional] Anwendungsintegritätsrichtlinie zum Überschreiben der Anwendungsmanifestrichtlinie

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen oder Replikaten mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Partitionsintegrität per API ein FabricClient-Element, und rufen Sie die GetPartitionHealthAsync-Methode für das dazugehörige HealthManager-Element auf. Erstellen Sie zum Angeben von optionalen Parametern das System.Fabric.Description.PartitionHealthQueryDescription-Element.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Partitionsintegrität lautet Get-ServiceFabricPartitionHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her.

Mit dem folgenden Cmdlet wird die Integrität für alle Partitionen des Diensts zur Ermittlung der Wortzahl abgerufen.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Abrufen der Replikatintegrität
Gibt den Zustand eines Replikats zurück. Eingabe:

- [erforderlich] Partitions-ID (GUID) und Replikat-ID zum Identifizieren des Replikats

- [optional] Anwendungsintegritätsrichtlinien-Parameter zum Überschreiben der Anwendungsmanifestrichtlinien

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Replikatintegrität per API ein FabricClient-Element, und rufen Sie die GetReplicaHealthAsync-Methode für das dazugehörige HealthManager-Element auf. Geben Sie erweiterte Parameter mit System.Fabric.Description.ReplicaHealthQueryDescription an.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Replikatintegrität lautet Get-ServiceFabricReplicaHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her.

Mit dem folgenden Cmdlet wird die Integrität des primären Replikats für alle Partitionen des Diensts abgerufen.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Abrufen der Integrität der bereitgestellten Anwendung
Gibt die Integrität einer Anwendung zurück, die auf einer Knotenentität bereitgestellt wird. Enthält die Integritätsstatus der bereitgestellten Dienstpakete. Eingabe:

- [erforderlich] Anwendungsname (URI) und Knotenname (Zeichenfolge) zum Identifizieren der bereitgestellten Anwendung

- [optional] Anwendungsintegritätsrichtlinie zum Überschreiben der Anwendungsmanifestrichtlinien

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen und bereitgestellten Anwendungspaketen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Integrität für eine auf einem Knoten bereitgestellte Anwendung per API ein FabricClient-Element, und rufen Sie die GetDeployedApplicationHealthAsync-Methode für das dazugehörige HealthManager-Element auf. Verwenden Sie zum Angeben von optionalen Parametern das System.Fabric.Description.DeployedApplicationHealthQueryDescription-Element.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Integrität von bereitgestellten Anwendungen lautet Get-ServiceFabricDeployedApplicationHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her. Führen Sie Get-ServiceFabricApplicationHealth aus, und sehen Sie sich die bereitgestellten untergeordneten Elemente der Anwendung an, um herauszufinden, wo eine Anwendung bereitgestellt wird.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung „fabric:/WordCount“ abgerufen, die auf dem Knoten „Node.1“ bereitgestellt wurde.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Abrufen der Integrität von bereitgestellten Dienstpaketen
Gibt die Integrität einer bereitgestellten Dienstpaketentität zurück. Eingabe:

- [erforderlich] Anwendungsname (URI), Knotenname (Zeichenfolge) und Dienstmanifestname (Zeichenfolge) zum Identifizieren des bereitgestellten Dienstpakets

- [optional] Anwendungsintegritätsrichtlinie zum Überschreiben der Anwendungsmanifestrichtlinie

- [optional] Filter zum ausschließlichen Zurückgeben von Ereignissen mit bestimmten Integritätsstatus (z. B. nur Fehler, Warnungen oder Fehler usw.).

### API
Erstellen Sie zum Abrufen der Integrität eines bereitgestellten Dienstpakets per API ein FabricClient-Element, und rufen Sie die GetDeployedServicePackageHealthAsync-Methode für das dazugehörige HealthManager-Element auf.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
Das Cmdlet zum Abrufen von bereitgestellten Dienstpaketen lautet Get-ServiceFabricDeployedServicePackageHealth. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Connect-ServiceFabricCluster-Cmdlet her. Führen Sie Get-ServiceFabricApplicationHealth aus, und sehen Sie sich die bereitgestellten Anwendungen an, um zu ermitteln, wo eine Anwendung bereitgestellt wurde. Sehen Sie sich die untergeordneten Elemente der bereitgestellten Dienstpakete in der Get-ServiceFabricDeployedApplicationHealth-Ausgabe an, um zu ermitteln, welche Dienstpakete in einer Anwendung enthalten sind.

Mit dem folgenden Cmdlet wird die Integrität des WordCount.Service-Dienstpakets der Anwendung „fabric:/WordCount“ abgerufen, die auf dem Knoten „Node.1“ bereitgestellt wurde. Die Entität verfügt über System.Hosting-Berichte für die erfolgreiche Aktivierung von Dienstpaketen und Einstiegspunkten und die erfolgreiche Registrierung von Diensttypen.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Allgemeine Abfragen
Mit den allgemeinen Abfragen wird die Liste der Service Fabric-Entitäten des angegebenen Typs zurückgegeben. Sie werden per API (Methoden unter FabricClient.QueryManager), mit PowerShell-Cmdlets und per REST verfügbar gemacht. Mit diesen Abfragen werden Unterabfragen aus mehreren Komponenten zusammengefasst. Eine davon ist der [Health Store](service-fabric-health-introduction.md#health-store), in dem der zusammengefasste Integritätsstatus für jedes Abfrageergebnis aufgefüllt wird.

> [AZURE.NOTE]Mit den allgemeinen Abfragen wird der zusammengefasste Integritätsstatus der Entität zurückgegeben. Sie enthalten keine umfassenden Integritätsdaten. Wenn eine Entität fehlerhaft ist, können Sie mit Integritätsabfragen alle Informationen zur Integrität abrufen, z. B. Ereignisse, Integritätsstatus von untergeordneten Elementen und Fehlerauswertungen.

Wenn mit den allgemeinen Abfragen für eine Entität der Integritätsstatus „Unknown“ zurückgegeben wird, verfügt der Health Store unter Umständen nicht über vollständige Daten zur Entität, oder die Unterabfrage für den Health Store war nicht erfolgreich (z. B. Kommunikationsfehler, Drosselung des Health Store usw.). Verwenden Sie danach eine Integritätsabfrage für die Entität. Dies ist unter Umständen erfolgreich, wenn mit der Unterabfrage vorübergehende Fehler (z. B. Netzwerkprobleme) ermittelt wurden, oder Sie erhalten weitere Details dazu, warum die Entität vom Health Store nicht verfügbar gemacht wird.

Die Abfragen, die HealthState für Entitäten enthalten, lauten:

- Knotenliste: Gibt die Liste mit den Knoten im Cluster zurück.
  - Api: FabricClient.QueryManager.GetNodeListAsync
  - PowerShell: Get-ServiceFabricNode
- Anwendungsliste: Gibt die Liste der Anwendungen im Cluster zurück.
  - Api: FabricClient.QueryManager.GetApplicationListAsync
  - PowerShell: Get-ServiceFabricApplication
- Liste der Dienste. Gibt die Liste mit den Diensten einer Anwendung zurück.
  - Api: FabricClient.QueryManager.GetServiceListAsync
  - PowerShell: Get-ServiceFabricService
- Partitionsliste: Gibt die Liste mit den Partitionen eines Diensts zurück.
  - Api: FabricClient.QueryManager.GetPartitionListAsync
  - PowerShell: Get-ServiceFabricPartition
- Replikatliste: Gibt die Liste mit den Replikaten einer Partition zurück.
  - Api: FabricClient.QueryManager.GetReplicaListAsync
  - PowerShell: Get-ServiceFabricReplica
- Liste mit bereitgestellten Anwendungen: Gibt die Liste mit den bereitgestellten Anwendungen eines Knotens zurück.
  - Api: FabricClient.QueryManager.GetDeployedApplicationListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication
- Liste mit bereitgestellten Dienstpaketen: Gibt die Liste mit den Dienstpaketen einer bereitgestellten Anwendung zurück.
  - Api: FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication

### Beispiele

Mit dem folgenden Code werden die fehlerhaften Anwendungen im Cluster abgerufen:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Mit dem folgenden Cmdlet werden die Anwendungsdetails für die Anwendung „fabric:/WordCount“ abgerufen. Beachten Sie, dass der Integritätsstatus „Warning“ lautet.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

Mit dem folgenden Cmdlet werden die Dienste mit dem Integritätsstatus „Warning“ abgerufen.

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Cluster- und Anwendungsupgrade
Während eines überwachten Upgrades von Cluster und Anwendung wird von Service Fabric die Integrität überprüft, um sicherzustellen, dass alles fehlerfrei ist und bleibt. Wenn etwas laut konfigurierter Richtlinie fehlerhaft ist, wird das Upgrade entweder angehalten, um eine Benutzerinteraktion zu ermöglichen, oder es wird ein automatischer Rollback durchgeführt.

Während des **Cluster**upgrades können Sie den Clusterupgradestatus abrufen. Er enthält die Fehlerauswertungen, in denen darauf verwiesen wird, welche Fehler im Cluster vorliegen. Falls für das Upgrade aufgrund von Integritätsproblemen ein Rollback durchgeführt wird, werden für den Upgradestatus die letzten Fehlergründe beibehalten, damit Administratoren nach der Ursache suchen können. Ebenso enthält der Anwendungsupgradestatus bei einem **Anwendung**supgrade die Fehlerauswertungen.

Im folgenden Beispiel wird der Anwendungsupgradestatus für die geänderte Anwendung „fabric:/WordCount“ veranschaulicht. Ein Watchdog-Element hat für eines der Replikate einen Fehler (Error) gemeldet. Für das Upgrade wird ein Rollback durchgeführt, weil die Integritätsüberprüfungen nicht berücksichtigt wurden.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Erfahren Sie mehr über das [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

## Problembehandlung in Bezug auf die Integrität
Wenn im Cluster oder in einer Anwendung ein Problem vorliegt, können Sie sich die Integrität des Clusters oder der Anwendung ansehen, um die Ursache zu ermitteln. In den Fehlerauswertungen wird mit Details angegeben, wodurch der aktuelle Fehlerstatus ausgelöst wurde. Bei Bedarf können Sie einen Drilldown in die fehlerhaften untergeordneten Entitäten durchführen, um die Probleme zu ermitteln.

## Nächste Schritte
[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten](service-fabric-report-health.md)

[Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
 

<!---HONumber=Sept15_HO2-->