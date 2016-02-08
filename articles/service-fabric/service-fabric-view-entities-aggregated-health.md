<properties
   pageTitle="Anzeigen der aggregierten Integrität für Azure Service Fabric-Entitäten | Microsoft Azure"
   description="Beschreibt, wie Sie die aggregierte Integrität von Azure Service Fabric-Entitäten abfragen, anzeigen und auswerten, indem Sie Integritätsabfragen und allgemeine Abfragen nutzen."
   services="service-fabric"HealthManager
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
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Anzeigen von Service Fabric-Integritätsberichten
Mit Azure Service Fabric wird ein [Integritätsmodell](service-fabric-health-introduction.md) eingeführt. Es besteht aus Integritätsentitäten, auf denen Systemkomponenten und Watchdogs die von ihnen überwachten lokalen Bedingungen melden können. Im so genannten [Integritätsspeicher](service-fabric-health-introduction.md#health-store) werden alle Integritätsdaten zusammengefasst, um zu ermitteln, ob die Entitäten intakt sind.

Standardmäßig enthält das Cluster von den Systemkomponenten gesendete Integritätsberichte. Weitere Informationen erhalten Sie unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric bietet Ihnen mehrere Möglichkeiten, den aggregierten Integritätsstatus der Entitäten abzurufen:

- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) oder andere Visualisierungstools

- Integritätsabfragen (über PowerShell, die API oder REST)

- Allgemeine Abfragen, die eine Liste mit Entitäten zurückgeben, von denen eine der Eigenschaften die Integrität ist (über PowerShell, die API oder REST)

Um diese Optionen zu demonstrieren, verwenden wir einen lokalen Cluster mit fünf Knoten. Neben der Anwendung **fabric:/System** (standardmäßig vorhanden) werden auch einige andere bereitgestellt. Eine davon ist **fabric:/WordCount**. Diese Anwendung enthält einen zustandsbehafteten Dienst, der mit sieben Replikaten konfiguriert wurde. Da nur fünf Knoten vorhanden sind, zeigen die Systemkomponenten eine Warnung an, dass die Partition die Zielanzahl unterschreitet.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Integrität im Service Fabric Explorer
Der Service Fabric Explorer bietet eine grafische Ansicht des Clusters. In der unten gezeigten Abbildung ist Folgendes dargestellt:

- Die Anwendung **fabric:/WordCount** ist rot dargestellt (Fehler), weil **MyWatchdog** für die Eigenschaft **Availability** ein Fehlerereignis gemeldet hat.

- Einer der Dienste, **fabric:/WordCount/WordCount.Service**, ist gelb gekennzeichnet (Warnung). Wie oben beschrieben, ist der Dienst mit sieben Replikaten konfiguriert, die nicht alle platziert werden können (da nur fünf Knoten vorhanden sind). Hier ist es zwar nicht zu sehen, aber die Dienstpartition ist aufgrund des Systemberichts gelb gekennzeichnet. Die gelb dargestellte Partition löst den gelb dargestellten Dienst aus.

- Der Cluster ist aufgrund der rot gezeigten Anwendung ebenfalls rot dargestellt.

Die Auswertung verwendet Standardrichtlinien aus dem Cluster- und dem Anwendungsmanifest. Hierbei handelt es sich um strenge Richtlinien, die keine Fehler tolerieren.

Ansicht des Clusters mit dem Service Fabric Explorer:

![Ansicht des Clusters mit dem Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Erfahren Sie mehr über den [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Integritätsabfragen
Service Fabric macht für jeden unterstützten [Entitätstyp](service-fabric-health-introduction.md#health-entities-and-hierarchy) Integritätsabfragen verfügbar. Darauf kann über die API (Methoden finden Sie unter **FabricClient.HealthManager**), mit PowerShell-Cmdlets und mit REST zugegriffen werden. Mit diesen Abfragen werden vollständige Integritätsinformationen zur Entität zurückgegeben, z. B. der zusammengefasste Integritätsstatus, für die Entität gemeldete Integritätsereignisse, der untergeordnete Integritätsstatus (falls verfügbar) und Fehlerauswertungen, sofern Fehler für die Entität auftreten.

> [AZURE.NOTE] Eine Integritätsentität wird an den Benutzer zurückgegeben, wenn sie im Integritätsspeicher vollständig aufgefüllt ist. Die Entität muss aktiv sein (nicht gelöscht) und einen Systembericht aufweisen. Außerdem müssen für ihre übergeordneten Entitäten in der Hierarchiekette ebenfalls Systemberichte vorliegen. Falls eine dieser Bedingungen nicht erfüllt ist, geben die Integritätsabfragen eine Ausnahme mit Informationen dazu zurück, warum die Entität nicht zurückgegeben wurde.

Die Integritätsabfragen müssen den Entitätsbezeichner übergeben, der sich nach dem Entitätstyp richtet. Die Abfragen akzeptieren optionale Integritätsrichtlinienparameter. Wenn diese nicht angegeben werden, werden die [Integritätsrichtlinien](service-fabric-health-introduction.md#health-policies) aus dem Cluster- oder Anwendungsmanifest für die Auswertung verwendet. Außerdem akzeptieren die Abfragen auch Filter für die Teilrückgabe von untergeordneten Elementen oder Ereignissen, sofern die angegebenen Filter jeweils berücksichtigt werden.

> [AZURE.NOTE] Die Ausgabefilter werden auf der Serverseite angewendet, damit die Antwortgröße der Nachricht reduziert wird. Es wird empfohlen, die Ausgabefilter zum Eingrenzen der zurückgegebenen Daten zu verwenden, anstatt Filter auf der Clientseite anzuwenden.

Ein Entitätsintegritätselement enthält die folgenden Informationen:

- Den zusammengefassten Integritätsstatus der Entität. Dieser wird vom Integritätsspeicher anhand der Berichte zur Entitätsintegrität, anhand des Integritätsstatus der untergeordneten Elemente (falls zutreffend) und anhand der Integritätsrichtlinien berechnet. Erfahren Sie mehr über die [Auswertung der Entitätsintegrität](service-fabric-health-introduction.md#entity-health-evaluation).  

- Die Integritätsereignisse der Entität

- Die Integritätsstatusauflistung für alle untergeordneten Elemente für die Entitäten, denen Elemente untergeordnet sein können. Die Integritätsstatusangaben enthalten Entitätsbezeichner und den aggregierten Integritätsstatus. Rufen Sie zum Erhalten der vollständigen Integrität für ein untergeordnetes Element die Abfrageintegrität für den untergeordneten Entitätstyp auf, und übergeben Sie den Bezeichner des untergeordneten Elements.

- Wenn die Entität nicht fehlerfrei ist, die Fehlerauswertungen in Verbindung mit dem Bericht, über den der Status der Entität ausgelöst wurde.

## Abrufen der Clusterintegrität
Hierdurch wird die Integrität der Clusterentität zurückgegeben. Enthält außerdem die Integritätsstatusangaben von Anwendungen und Knoten (untergeordnete Elemente des Clusters). Eingabe:

- [Optional] Die Zuordnung der Anwendungsintegritätsrichtlinien mit den Integritätsrichtlinien zum Außerkraftsetzen der Richtlinien für das Anwendungsmanifest.

- [Optional] Filter für Ereignisse, Knoten und Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse, Knoten und Anwendungen – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Clusterintegrität ein **FabricClient**-Element, und rufen Sie die [**GetClusterHealthAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx)-Methode für das dazugehörige **HealthManager**-Element auf.

Die Clusterintegrität wird wie folgt abgerufen:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

Mit dem folgenden Verfahren wird die Clusterintegrität abgerufen, indem eine benutzerdefinierte Clusterintegritätsrichtlinie und Filter für Knoten und Anwendungen verwendet werden. Beachten Sie, dass das **System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)**-Element mit allen Eingabedaten erstellt wird.

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
Das Cmdlet zum Abrufen der Clusterintegrität lautet **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her.

Der Status des Clusters umfasst fünf Knoten, die Systemanwendung und „fabric:/WordCount“ in oben beschriebener Konfiguration.

Mit dem folgenden Cmdlet wird die Clusterintegrität mit standardmäßigen Integritätsrichtlinien abgerufen. Der zusammengefasste Integritätsstatus lautet „Warnung“, weil die Anwendung „fabric:/WordCount“ den Status „Warnung“ aufweist. Beachten Sie, dass die Fehlerauswertungen mit Details zu den Bedingungen angezeigt werden, durch die die aggregierte Integrität ausgelöst wurde.

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

Mit dem folgenden PowerShell-Cmdlet wird die Integrität des Clusters anhand einer benutzerdefinierten Anwendungsrichtlinie abgerufen. Die Ergebnisse werden gefiltert, um nur Anwendungen und Knoten mit dem Status „Fehler“ oder „Warnung“ abzurufen. Aus diesem Grund werden keine Knoten zurückgegeben, da sie alle fehlerfrei sind. Nur die Anwendung „fabric:/WordCount“ berücksichtigt den Anwendungsfilter. Da mit der benutzerdefinierten Richtlinie festgelegt wird, dass Warnungen für die Anwendung „fabric:/WordCount“ als Fehler anzusehen sind, wird die Anwendung auf den Status „Fehler“ heraufgestuft. Dasselbe gilt auch für den Cluster.

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
Dies gibt die Integrität einer Knotenentität zurück und enthält die auf dem Knoten gemeldeten Integritätsereignisse. Eingabe:

- [Erforderlich] Der Knotenname, mit dem der Knoten identifiziert wird.

- [Optional] Einstellungen zur Clusterintegritätsrichtlinie zum Auswerten der Integrität.

- [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Knotenintegrität über die API ein FabricClient-Element, und rufen Sie die **GetNodeHealthAsync**-Methode für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen. Der Ereignisfilter und die benutzerdefinierte Richtlinie werden mit **System.Fabric.Description.[NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)** übergeben:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Knotenintegrität lautet **Get-ServiceFabricNodeHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her. Mit dem folgenden Cmdlet wird die Knotenintegrität mit standardmäßigen Integritätsrichtlinien abgerufen:

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

Mit dem folgenden Cmdlet wird die Integrität aller Knoten im Cluster abgerufen:

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
Gibt die Integrität einer Anwendungsentität zurück. Enthält die Integritätsstatusangaben der bereitgestellten Anwendung und der untergeordneten Elemente des Diensts. Eingabe:

- [Erforderlich] Der Anwendungsname (URI) zum Identifizieren der Anwendung.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinien.

- [Optional] Filter für Ereignisse, Dienste und bereitgestellte Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse, Dienste und bereitgestellten Anwendungen – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Anwendungsintegrität ein FabricClient-Element, und rufen Sie die **GetApplicationHealthAsync**-Methode für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen (URI) abgerufen:

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen (URI) abgerufen. Filter und die benutzerdefinierte Richtlinie werden mit **System.Fabric.Description.ApplicationHealthQueryDescription** angegeben.

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
Das Cmdlet zum Abrufen der Anwendungsintegrität lautet **Get-ServiceFabricApplicationHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung „fabric:/WordCount“ zurückgegeben:

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

Das folgende PowerShell-Cmdlet übergibt benutzerdefinierte Richtlinien. Außerdem filtert es Elemente und Ereignisse.

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
Gibt die Integrität einer Dienstentität zurück. Enthält die Integritätsstatusangaben der Partition. Eingabe:

- [Erforderlich] Der Dienstname (URI) zum Identifizieren des Diensts.
- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.
- [Optional] Filter für Ereignisse und Partitionen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse und Partitionen – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Dienstintegrität über die API ein FabricClient-Element, und rufen Sie die **GetServiceHealthAsync**-Methode für das dazugehörige HealthManager-Element auf.

Im folgenden Beispiel wird die Integrität eines Diensts mit dem angegebenen Dienstnamen (URI) abgerufen:

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

Mit dem folgenden Code wird die Dienstintegrität für den angegebenen Dienstnamen (URI) abgerufen. Filter und die benutzerdefinierte Richtlinie werden mit „System.Fabric.Description.ServiceHealthQueryDescription“ angegeben:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Dienstintegrität lautet **Get-ServiceFabricServiceHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her.

Mit dem folgenden Cmdlet wird die Dienstintegrität anhand von standardmäßigen Integritätsrichtlinien abgerufen.

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
Gibt die Integrität einer Partitionsentität zurück. Enthält die Angaben zum Replikatintegritätsstatus. Eingabe:

- [Erforderlich] Die Partitions-ID (GUID) zur Identifizierung der Partition.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.

- [Optional] Filter für Ereignisse und Replikate, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse und Replikate – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Partitionsintegrität über die API ein FabricClient-Element, und rufen Sie die **GetPartitionHealthAsync**-Methode für das dazugehörige HealthManager-Element auf. Erstellen Sie zum Angeben optionaler Parameter das **System.Fabric.Description.PartitionHealthQueryDescription**-Element.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Partitionsintegrität lautet **Get-ServiceFabricPartitionHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her.

Mit dem folgenden Cmdlet wird die Integrität für alle Partitionen des Diensts zur Ermittlung der Wortzahl abgerufen:

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
Gibt die Integrität eines Replikats zurück. Eingabe:

- [Erforderlich] Die Partitions-ID (GUID) und die Replikat-ID zum Identifizieren des Replikats.

- [Optional] Die Anwendungsintegritätsrichtlinien-Parameter zum Überschreiben der Anwendungsmanifestrichtlinien.

- [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Replikatintegrität über die API ein FabricClient-Element, und rufen Sie die **GetReplicaHealthAsync**-Methode für das dazugehörige HealthManager-Element auf. Geben Sie erweiterte Parameter mit **System.Fabric.Description.ReplicaHealthQueryDescription** an.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Replikatintegrität lautet **Get-ServiceFabricReplicaHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her.

Mit dem folgenden Cmdlet wird die Integrität des primären Replikats für alle Partitionen des Diensts abgerufen:

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
Gibt die Integrität einer Anwendung zurück, die auf einer Knotenentität bereitgestellt wird. Enthält die Integritätsstatusangaben der bereitgestellten Dienstpakete. Eingabe:

- [Erforderlich] Der Anwendungsname (URI) und der Knotenname (Zeichenfolge) zum Identifizieren der bereitgestellten Anwendung.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinien.

- [Optional] Filter für Ereignisse und bereitgestellte Dienstpakete, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse und bereitgestellten Dienste – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Um die Integrität für eine auf einem Knoten bereitgestellte Anwendung über die API abzurufen, erstellen Sie ein FabricClient-Element, und rufen Sie die **GetDeployedApplicationHealthAsync**-Methode für das zugehörige HealthManager-Element auf. Verwenden Sie zum Angeben optionaler Parameter das **System.Fabric.Description.DeployedApplicationHealthQueryDescription**-Element.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Integrität von bereitgestellten Anwendungen lautet **Get-ServiceFabricDeployedApplicationHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her. Führen Sie **Get-ServiceFabricApplicationHealth** aus, und sehen Sie sich die untergeordneten Elemente der bereitgestellten Anwendung an, um herauszufinden, wo eine Anwendung bereitgestellt wird.

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

- [Erforderlich] Der Anwendungsname (URI), der Knotenname (Zeichenfolge) und der Dienstmanifestname (Zeichenfolge) zum Identifizieren des bereitgestellten Dienstpakets.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.

- [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Integrität eines bereitgestellten Dienstpakets über die API ein FabricClient-Element, und rufen Sie die **GetDeployedServicePackageHealthAsync**-Methode für das dazugehörige HealthManager-Element auf.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
Das Cmdlet zum Abrufen der Integrität bereitgestellter Dienstpakete lautet **Get-ServiceFabricDeployedServicePackageHealth**. Stellen Sie zuerst eine Verbindung mit dem Cluster über das Cmdlet **Connect-ServiceFabricCluster** her. Führen Sie **Get-ServiceFabricApplicationHealth** aus, und sehen Sie sich die bereitgestellten Anwendungen an, um herauszufinden, wo eine Anwendung bereitgestellt wird. Sehen Sie sich die untergeordneten Elemente der bereitgestellten Dienstpakete in der **Get-ServiceFabricDeployedApplicationHealth**-Ausgabe an, um zu ermitteln, welche Dienstpakete in einer Anwendung enthalten sind.

Mit dem folgenden Cmdlet wird die Integrität des Dienstpakets **WordCount.Service** der Anwendung „fabric:/WordCount“ abgerufen, die auf dem Knoten „Node.1“ bereitgestellt wird. Die Entität verfügt über **System.Hosting**-Berichte für die erfolgreiche Aktivierung von Dienstpaketen und Einstiegspunkten sowie die erfolgreiche Registrierung von Diensttypen.

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
Mit den allgemeinen Abfragen wird die Liste der Service Fabric-Entitäten des angegebenen Typs zurückgegeben. Sie werden über die API (Methoden unter **FabricClient.QueryManager**), über PowerShell-Cmdlets und über REST verfügbar gemacht. Mit diesen Abfragen werden Unterabfragen aus mehreren Komponenten zusammengefasst. Eine davon ist der [Integritätsspeicher](service-fabric-health-introduction.md#health-store), in dem der aggregierte Integritätsstatus für jedes Abfrageergebnis aufgefüllt wird.

> [AZURE.NOTE] Mit den allgemeinen Abfragen wird der aggregierte Integritätsstatus der Entität zurückgegeben. Sie enthalten keine umfassenden Integritätsdaten. Wenn eine Entität fehlerhaft ist, können Sie mit Integritätsabfragen alle Informationen zur Integrität abrufen, z. B. Ereignisse, Integritätsstatusangaben untergeordneter Elemente und Fehlerauswertungen.

Wenn allgemeine Abfragen einen unbekannten Integritätsstatus für eine Entität zurückgeben, ist es möglich, dass der Integritätsspeicher keine vollständigen Daten über die Entität besitzt. Es ist auch möglich, dass eine Unterabfrage an den Integritätsspeicher nicht erfolgreich war (z. B. ein Kommunikationsfehler aufgetreten ist oder der Integritätsspeicher gedrosselt wurde). Verwenden Sie danach eine Integritätsabfrage für die Entität. Wenn bei der Unterabfrage vorübergehende Fehler, beispielsweise Netzwerkfehler, aufgetreten sind, ist diese nachfolgende Abfrage möglicherweise erfolgreich. Sie kann Ihnen auch weitere Details aus dem Integritätsspeicher zu den Gründen bereitstellen, aus denen die Entität nicht verfügbar gemacht wird.

Die Abfragen, die **HealthState** für Entitäten enthalten, lauten:

- Knotenliste: Gibt die Liste mit den Knoten im Cluster zurück.
  - API: FabricClient.QueryManager.GetNodeListAsync
  - PowerShell: Get-ServiceFabricNode
- Anwendungsliste: Gibt die Liste der Anwendungen im Cluster zurück.
  - API: FabricClient.QueryManager.GetApplicationListAsync
  - PowerShell: Get-ServiceFabricApplication
- Dienstliste: Gibt die Liste mit den Diensten einer Anwendung zurück.
  - API: FabricClient.QueryManager.GetServiceListAsync
  - PowerShell: Get-ServiceFabricService
- Partitionsliste: Gibt die Liste mit den Partitionen eines Diensts zurück.
  - API: FabricClient.QueryManager.GetPartitionListAsync
  - PowerShell: Get-ServiceFabricPartition
- Replikatliste: Gibt die Liste mit den Replikaten in einer Partition zurück.
  - API: FabricClient.QueryManager.GetReplicaListAsync
  - PowerShell: Get-ServiceFabricReplica
- Liste bereitgestellter Anwendungen: Gibt die Liste mit den bereitgestellten Anwendungen eines Knotens zurück.
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication
- Liste bereitgestellter Dienstpakete: Gibt die Liste mit den Dienstpaketen einer bereitgestellten Anwendung zurück.
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - PowerShell: Get-ServiceFabricDeployedApplication

### Beispiele

Mit dem folgenden Code werden die fehlerhaften Anwendungen im Cluster abgerufen:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Mit dem folgenden Cmdlet werden die Anwendungsdetails für die Anwendung „fabric:/WordCount“ abgerufen. Beachten Sie, dass der Integritätsstatus „Warnung“ lautet.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

Mit dem folgenden Cmdlet werden die Dienste mit dem Integritätsstatus „Warnung“ abgerufen:

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

## Cluster- und Anwendungsupgrades
Während eines überwachten Upgrades von Cluster und Anwendung wird von Service Fabric die Integrität überprüft, um sicherzustellen, dass alles fehlerfrei bleibt. Wenn eine Entität von den konfigurierten Integritätsrichtlinien als fehlerhaft ausgewertet wird, wendet das Upgrade spezifische Richtlinien an, um die nächste Aktion festzulegen. Das Upgrade kann angehalten werden, um eine Benutzerinteraktion (z. B. das Beheben von Fehlerbedingungen oder das Ändern von Richtlinien) zu ermöglichen, oder es kann automatisch einen Rollback auf die vorherige, einwandfreie Version durchführen.

Während des Upgrades eines *Clusters* können Sie den Clusterupgradestatus abrufen. Dazu gehören alle Fehlerauswertungen, die anzeigen, was im Cluster fehlerhaft ist. Falls für das Upgrade aufgrund von Integritätsproblemen ein Rollback durchgeführt wird, werden im Upgradestatus die letzten Gründe für Fehler beibehalten. So werden Informationen gespeichert, mit denen Administratoren die Fehlerursachen untersuchen können.

Ebenso enthält der Anwendungsupgradestatus beim Upgrade einer *Anwendung* die entsprechenden Fehlerauswertungen.

Im folgenden Beispiel wird der Anwendungsupgradestatus für die geänderte Anwendung „fabric:/WordCount“ veranschaulicht. Ein Watchdog-Element hat für eines der Replikate einen Fehler gemeldet. Für das Upgrade wird ein Rollback durchgeführt, weil die Integritätsüberprüfungen nicht berücksichtigt wurden.

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

## Verwenden von Integritätsauswertungen zur Problembehandlung
Wenn im Cluster oder in einer Anwendung ein Problem vorliegt, können Sie sich die Integrität des Clusters oder der Anwendung ansehen, um die Ursache zu ermitteln. In den Fehlerauswertungen werden Details zur Ursache bereitgestellt, durch die der aktuelle Fehlerstatus ausgelöst wurde. Nach Bedarf können Sie ein Detailinformationen zu den fehlerhaften untergeordneten Entitäten anzeigen, um die Ursache zu ermitteln.

## Nächste Schritte
[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten](service-fabric-report-health.md)

[Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->