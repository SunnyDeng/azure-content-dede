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
   ms.date="03/23/2016"
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
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## Integrität im Service Fabric Explorer
Der Service Fabric Explorer bietet eine grafische Ansicht des Clusters. In der unten gezeigten Abbildung ist Folgendes dargestellt:

- Die Anwendung **fabric:/WordCount** ist rot dargestellt (Fehler), weil **MyWatchdog** für die Eigenschaft **Availability** ein Fehlerereignis gemeldet hat.

- Einer der Dienste (**fabric:/WordCount/WordCountService**) ist gelb gekennzeichnet (Warnung). Wie oben beschrieben, ist der Dienst mit sieben Replikaten konfiguriert, die nicht alle platziert werden können (da nur fünf Knoten vorhanden sind). Hier ist es zwar nicht zu sehen, aber die Dienstpartition ist aufgrund des Systemberichts gelb gekennzeichnet. Die gelb dargestellte Partition löst den gelb dargestellten Dienst aus.

- Der Cluster ist aufgrund der rot gezeigten Anwendung ebenfalls rot dargestellt.

Die Auswertung verwendet Standardrichtlinien aus dem Cluster- und dem Anwendungsmanifest. Hierbei handelt es sich um strenge Richtlinien, die keine Fehler tolerieren.

Ansicht des Clusters mit dem Service Fabric Explorer:

![Ansicht des Clusters mit dem Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Erfahren Sie mehr über den [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Integritätsabfragen
Service Fabric macht für jeden unterstützten [Entitätstyp](service-fabric-health-introduction.md#health-entities-and-hierarchy) Integritätsabfragen verfügbar. Darauf kann über die API (Methoden finden Sie unter **FabricClient.HealthManager**), mit PowerShell-Cmdlets und mit REST zugegriffen werden. Mit diesen Abfragen werden vollständige Integritätsinformationen zur Entität zurückgegeben, z. B. der zusammengefasste Integritätsstatus, für die Entität gemeldete Integritätsereignisse, der untergeordnete Integritätsstatus (falls verfügbar) und Fehlerauswertungen, sofern Fehler für die Entität auftreten.

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

- [Optional] Die Clusterintegritätsrichtlinie zum Auswerten der Knoten und der Clusterereignisse.

- [Optional] Die Zuordnung der Anwendungsintegritätsrichtlinien mit den Integritätsrichtlinien zum Außerkraftsetzen der Richtlinien für das Anwendungsmanifest.

- [Optional] Filter für Ereignisse, Knoten und Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse, Knoten und Anwendungen – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Clusterintegrität ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) für das dazugehörige Element vom Typ **HealthManager** auf.

Die Clusterintegrität wird wie folgt abgerufen:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Mit dem folgenden Verfahren wird die Clusterintegrität abgerufen, indem eine benutzerdefinierte Clusterintegritätsrichtlinie und Filter für Knoten und Anwendungen verwendet werden. Beachten Sie, dass [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx) mit allen Eingabedaten erstellt wird.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### PowerShell
Zum Abrufen der Clusterintegrität wird das Cmdlet [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her.

Der Status des Clusters umfasst fünf Knoten, die Systemanwendung und „fabric:/WordCount“ in oben beschriebener Konfiguration.

Mit dem folgenden Cmdlet wird die Clusterintegrität mit standardmäßigen Integritätsrichtlinien abgerufen. Der zusammengefasste Integritätsstatus lautet „Warnung“, weil die Anwendung „fabric:/WordCount“ den Status „Warnung“ aufweist. Beachten Sie, dass die Fehlerauswertungen mit Details zu den Bedingungen angezeigt werden, durch die die aggregierte Integrität ausgelöst wurde.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
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
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


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

- [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Knotenintegrität über die API ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen.

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Mit dem folgenden Code wird die Knotenintegrität für den angegebenen Knotennamen abgerufen. Der Ereignisfilter und die benutzerdefinierte Richtlinie werden mit [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx) übergeben:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### PowerShell
Zum Abrufen der Knotenintegrität wird das Cmdlet [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her. Mit dem folgenden Cmdlet wird die Knotenintegrität mit standardmäßigen Integritätsrichtlinien abgerufen:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Mit dem folgenden Cmdlet wird die Integrität aller Knoten im Cluster abgerufen:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

## Abrufen der Anwendungsintegrität
Gibt die Integrität einer Anwendungsentität zurück. Enthält die Integritätsstatusangaben der bereitgestellten Anwendung und der untergeordneten Elemente des Diensts. Eingabe:

- [Erforderlich] Der Anwendungsname (URI) zum Identifizieren der Anwendung.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinien.

- [Optional] Filter für Ereignisse, Dienste und bereitgestellte Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse, Dienste und bereitgestellten Anwendungen – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Anwendungsintegrität ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) für das dazugehörige HealthManager-Element auf.

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen (URI) abgerufen:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Mit dem folgenden Code wird die Anwendungsintegrität für den angegebenen Anwendungsnamen (URI) abgerufen. Filter und die benutzerdefinierte Richtlinie werden mit [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx) angegeben.

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
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### PowerShell
Zum Abrufen der Anwendungsintegrität wird das Cmdlet [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung **fabric:/WordCount** zurückgegeben:

```powershell
PS c:>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Das folgende PowerShell-Cmdlet übergibt benutzerdefinierte Richtlinien. Außerdem filtert es Elemente und Ereignisse.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Abrufen der Dienstintegrität
Gibt die Integrität einer Dienstentität zurück. Enthält die Integritätsstatusangaben der Partition. Eingabe:

- [Erforderlich] Der Dienstname (URI) zum Identifizieren des Diensts.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.

- [Optional] Filter für Ereignisse und Partitionen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse und Partitionen – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Dienstintegrität über die API ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) für das dazugehörige HealthManager-Element auf.

Im folgenden Beispiel wird die Integrität eines Diensts mit dem angegebenen Dienstnamen (URI) abgerufen:

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Mit dem folgenden Code wird die Dienstintegrität für den angegebenen Dienstnamen (URI) abgerufen. Filter und die benutzerdefinierte Richtlinie werden mit [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx) angegeben:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### PowerShell
Zum Abrufen der Dienstintegrität wird das Cmdlet [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Dienstintegrität anhand von standardmäßigen Integritätsrichtlinien abgerufen.

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

## Abrufen der Partitionsintegrität
Gibt die Integrität einer Partitionsentität zurück. Enthält die Angaben zum Replikatintegritätsstatus. Eingabe:

- [Erforderlich] Die Partitions-ID (GUID) zur Identifizierung der Partition.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.

- [Optional] Filter für Ereignisse und Replikate, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse und Replikate – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Partitionsintegrität über die API ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) für das dazugehörige HealthManager-Element auf. Erstellen Sie zum Angeben optionaler Parameter das Element [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### PowerShell
Zum Abrufen der Partitionsintegrität wird das Cmdlet [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Integrität für alle Partitionen des Diensts **fabric:/WordCount/WordCountService** abgerufen:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## Abrufen der Replikatintegrität
Gibt die Integrität eines zustandsbehafteten Dienstreplikats oder einer zustandslosen Dienstinstanz zurück. Eingabe:

- [Erforderlich] Die Partitions-ID (GUID) und die Replikat-ID zum Identifizieren des Replikats.

- [Optional] Die Anwendungsintegritätsrichtlinien-Parameter zum Überschreiben der Anwendungsmanifestrichtlinien.

- [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Replikatintegrität über die API ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) für das dazugehörige HealthManager-Element auf. Erweiterte Parameter können mit [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx) angegeben werden.

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### PowerShell
Zum Abrufen der Replikatintegrität wird das Cmdlet [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her.

Mit dem folgenden Cmdlet wird die Integrität des primären Replikats für alle Partitionen des Diensts abgerufen:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Abrufen der Integrität der bereitgestellten Anwendung
Gibt die Integrität einer Anwendung zurück, die auf einer Knotenentität bereitgestellt wird. Enthält die Integritätsstatusangaben der bereitgestellten Dienstpakete. Eingabe:

- [Erforderlich] Der Anwendungsname (URI) und der Knotenname (Zeichenfolge) zum Identifizieren der bereitgestellten Anwendung.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinien.

- [Optional] Filter für Ereignisse und bereitgestellte Dienstpakete, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse und bereitgestellten Dienste – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Um die Integrität für eine auf einem Knoten bereitgestellte Anwendung über die API abzurufen, erstellen Sie ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) für das dazugehörige HealthManager-Element auf. Optionale Parameter können mit [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx) angegeben werden.

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### PowerShell
Zum Abrufen der Integrität von bereitgestellten Anwendungen wird das Cmdlet [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her. Führen Sie [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) aus, und ermitteln Sie anhand der untergeordneten Elemente der bereitgestellten Anwendung, wo eine Anwendung bereitgestellt wird.

Mit dem folgenden Cmdlet wird die Integrität der Anwendung **fabric:/WordCount** abgerufen, die auf **\_Node\_2** bereitgestellt wurde.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Abrufen der Integrität von bereitgestellten Dienstpaketen
Gibt die Integrität einer bereitgestellten Dienstpaketentität zurück. Eingabe:

- [Erforderlich] Der Anwendungsname (URI), der Knotenname (Zeichenfolge) und der Dienstmanifestname (Zeichenfolge) zum Identifizieren des bereitgestellten Dienstpakets.

- [Optional] Die Anwendungsintegritätsrichtlinie zum Außerkraftsetzen der Anwendungsmanifestrichtlinie.

- [Optional] Filter für Ereignisse, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen (z. B. nur Fehler oder sowohl Warnungen als auch Fehler). Beachten Sie, dass alle Ereignisse – unabhängig vom Filter – zum Auswerten der aggregierten Entitätsintegrität verwendet werden.

### API
Erstellen Sie zum Abrufen der Integrität eines bereitgestellten Dienstpakets über die API ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) für das dazugehörige HealthManager-Element auf. Optionale Parameter können mit [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx) angegeben werden.

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### PowerShell
Zum Abrufen der Integrität bereitgestellter Dienstpakete wird das Cmdlet [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her. Führen Sie [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) aus, und ermitteln Sie anhand der bereitgestellten Anwendungen, wo eine Anwendung bereitgestellt wird. Sehen Sie sich die untergeordneten Elemente der bereitgestellten Dienstpakete in der Ausgabe von [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) an, um zu ermitteln, welche Dienstpakete in einer Anwendung enthalten sind.

Mit dem folgenden Cmdlet wird die Integrität des Dienstpakets **WordCountServicePkg** der Anwendung **fabric:/WordCount** abgerufen, die auf **\_Node\_2** bereitgestellt wird. Die Entität verfügt über Berichte vom Typ **System.Hosting** für die erfolgreiche Aktivierung von Dienstpaketen und Einstiegspunkten sowie die erfolgreiche Registrierung von Diensttypen.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Integritätsblockabfragen
Integritätsblockabfragen können pro Eingabefilter rekursiv mehrstufige untergeordnete Clusterelemente zurückgeben. Sie unterstützen erweiterte Filter, mit denen auf äußerst flexible Weise angegeben werden kann, welche untergeordneten Elemente zurückgegeben werden sollen. Die Elemente werden entweder über ihren eindeutigen Bezeichner oder über einen anderen Gruppenbezeichner und/oder den Integritätsstatus identifiziert. Im Gegensatz zu Integritätsbefehlen, bei denen immer untergeordnete Elemente der ersten Ebene einbezogen werden, werden hier standardmäßig keine untergeordneten Elemente einbezogen.

Die [Integritätsabfragen](service-fabric-view-entities-aggregated-health.md#health-queries) geben pro erforderlichem Filter nur untergeordnete Elemente der ersten Ebene der angegebenen Entität zurück. Zum Abrufen der untergeordneten Elemente weiterer Ebenen müssen für die betreffenden Entitäten zusätzliche Integritäts-APIs aufgerufen werden. Analog dazu müssen Benutzer jeweils eine API pro gewünschter Entität aufrufen, um die Integrität bestimmter Entitäten abzurufen. Mit der erweiterten Filterung der Blockabfrage können Benutzer mehrere Elemente in einer einzelnen Abfrage anfordern und so die Größe und Anzahl von Nachrichten verringern.

Der Vorteil der Blockabfrage besteht darin, dass Benutzer mit nur einem Aufruf den Integritätszustand mehrerer Clusterentitäten (potenziell sogar aller Clusterentitäten am erforderlichen Stamm) abrufen können. Dies ermöglicht komplexe Integritätsabfragen, die beispielsweise Folgendes bewirken:

- Es werden nur fehlerhafte Anwendungen zurückgegeben. Für diese Anwendungen werden alle Dienste mit einem Warnungs-/Fehlerstatus eingeschlossen. Bei den zurückgegebenen Diensten werden alle Partitionen eingeschlossen.

- Es wird nur die Integrität von vier namentlich angegebenen Anwendungen zurückgegeben.

- Es wird nur die Integrität von Anwendungen eines bestimmten Anwendungstyps zurückgegeben.

- Es werden alle auf einem Knoten bereitgestellten Entitäten zurückgegeben. Dabei werden alle Anwendungen, alle auf dem angegebenen Knoten bereitgestellten Anwendungen und alle auf dem Knoten bereitgestellten Dienstpakete zurückgegeben.

- Es werden alle Replikate mit einem Fehlerstatus zurückgegeben. Dabei werden alle Anwendungen, Dienste, Partitionen und nur Replikate mit Fehlerstatus zurückgegeben.

- Es werden alle Anwendungen zurückgegeben. Für einen angegebenen Dienst werden alle Partitionen einbezogen.

Momentan wird die Integritätsblockabfrage nur für die Clusterentität verfügbar gemacht. Sie gibt einen Clusterintegritätsblock mit folgendem Inhalt zurück:

- Aggregierter Integritätszustand des Clusters.

- Integritätszustandsblock-Liste der Knoten, die Eingabefilter berücksichtigen.

- Integritätszustandsblock-Liste der Anwendungen, die Eingabefilter berücksichtigen. Jeder Anwendungs-Integritätszustandsblock enthält eine Blockliste mit allen Diensten, die Eingabefilter berücksichtigen, und eine Blockliste mit allen bereitgestellten Anwendungen, die die Filter berücksichtigen. Gleiches gilt für die untergeordneten Elemente von Diensten und bereitgestellten Anwendung. Dadurch können potenziell alle Entitäten im Cluster auf Anforderung hierarchisch zurückgegeben werden.

### Cluster-Integritätsblockabfrage
Hierbei wird die Integrität der Clusterentität zurückgegeben (einschließlich der hierarchischen Integritätszustandsblöcke der erforderlichen untergeordneten Elemente). Eingabe:

- [Optional] Die Clusterintegritätsrichtlinie zum Auswerten der Knoten und der Clusterereignisse.

- [Optional] Die Zuordnung der Anwendungsintegritätsrichtlinien mit den Integritätsrichtlinien zum Außerkraftsetzen der Richtlinien für das Anwendungsmanifest.

- [Optional] Filter für Knoten und Anwendungen, die angeben, welche Einträge von Interesse sind und im Ergebnis zurückgegeben werden sollen. Die Filter gelten speziell für eine Entität/Entitätsgruppe oder für alle Entitäten auf dieser Ebene. Die Liste mit den Filtern kann einen allgemeinen Filter und/oder Filter für bestimmte Bezeichner enthalten, um die von der Abfrage zurückgegebenen Entitäten zu differenzieren. Ohne Angabe werden die untergeordneten Elemente standardmäßig nicht zurückgegeben. Weitere Informationen zu den Filtern finden Sie unter [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) sowie unter [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). Mit den Anwendungsfiltern können rekursiv erweiterte Filter für untergeordnete Elemente angegeben werden.

Das Blockergebnis enthält die untergeordneten Elemente, die die Filter berücksichtigen.

Derzeit werden von der Blockabfrage keine fehlerhaften Auswertungen oder Entitätsereignisse zurückgegeben. Diese können mit der vorhandenen Clusterintegritätsabfrage abgerufen werden.

### API
Erstellen Sie zum Abrufen des Clusterintegritätsblocks ein Element vom Typ `FabricClient`, und rufen Sie die Methode [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) für das dazugehörige Element vom Typ **HealthManager** auf. Integritätsrichtlinien und erweiterte Filter können durch Übergeben von [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) definiert werden.

Im Folgenden wird ein Clusterintegritätsblock mit erweiterten Filtern abgerufen:

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### PowerShell
Zum Abrufen der Clusterintegrität wird das Cmdlet [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx) verwendet. Stellen Sie über das Cmdlet [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) zunächst eine Verbindung mit dem Cluster her.

Im Folgenden werden Knoten nur dann abgerufen, wenn sie einen Fehlerstatus besitzen – mit Ausnahme eines bestimmten Knotens, der immer zurückgegeben werden soll:

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Das folgende Cmdlet ruft den Clusterblock mit Anwendungsfiltern ab:

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

Das folgende Cmdlet gibt alle bereitgestellten Entitäten auf einem Knoten zurück:

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

## Allgemeine Abfragen
Mit den allgemeinen Abfragen wird die Liste der Service Fabric-Entitäten des angegebenen Typs zurückgegeben. Sie werden über die API (über die Methoden unter **FabricClient.QueryManager**), über PowerShell-Cmdlets und über REST verfügbar gemacht. Mit diesen Abfragen werden Unterabfragen aus mehreren Komponenten zusammengefasst. Eine davon ist der [Integritätsspeicher](service-fabric-health-introduction.md#health-store), in dem der aggregierte Integritätszustand für jedes Abfrageergebnis aufgefüllt wird.

> [AZURE.NOTE] Mit den allgemeinen Abfragen wird der aggregierte Integritätsstatus der Entität zurückgegeben. Sie enthalten keine umfassenden Integritätsdaten. Wenn eine Entität fehlerhaft ist, können Sie mit Integritätsabfragen alle Informationen zur Integrität abrufen, z. B. Ereignisse, Integritätsstatusangaben untergeordneter Elemente und Fehlerauswertungen.

Wenn allgemeine Abfragen einen unbekannten Integritätsstatus für eine Entität zurückgeben, ist es möglich, dass der Integritätsspeicher keine vollständigen Daten über die Entität besitzt. Es ist auch möglich, dass eine Unterabfrage an den Integritätsspeicher nicht erfolgreich war (z. B. ein Kommunikationsfehler aufgetreten ist oder der Integritätsspeicher gedrosselt wurde). Verwenden Sie danach eine Integritätsabfrage für die Entität. Wenn bei der Unterabfrage vorübergehende Fehler, beispielsweise Netzwerkfehler, aufgetreten sind, ist diese nachfolgende Abfrage möglicherweise erfolgreich. Sie kann Ihnen auch weitere Details aus dem Integritätsspeicher zu den Gründen bereitstellen, aus denen die Entität nicht verfügbar gemacht wird.

Die Abfragen, die **HealthState** für Entitäten enthalten, lauten:

- Knotenliste: Gibt die (auf Seiten aufgeteilte) Liste mit den Knoten im Cluster zurück.
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- Anwendungsliste: Gibt die (auf Seiten aufgeteilte) Liste mit den Anwendungen im Cluster zurück.
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- Dienstliste: Gibt die (auf Seiten aufgeteilte) Liste mit den Diensten einer Anwendung zurück.
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- Partitionsliste: Gibt die (auf Seiten aufgeteilte) Liste mit den Partitionen eines Diensts zurück.
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- Replikatliste: Gibt die (auf Seiten aufgeteilte) Liste mit den Replikaten in einer Partition zurück.
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- Liste bereitgestellter Anwendungen: Gibt die Liste mit den bereitgestellten Anwendungen eines Knotens zurück.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- Liste bereitgestellter Dienstpakete: Gibt die Liste mit den Dienstpaketen einer bereitgestellten Anwendung zurück.
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Einige Abfragen geben unter Umständen auf Seiten aufgeteilte Ergebnisse zurück. Die Rückgabe dieser Abfragen ist eine von [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx) abgeleitete Liste. Falls die Ergebnisse nicht in eine Nachricht passen, wird nur eine Seite zurückgegeben und mithilfe von ContinuationToken festgehalten, wo die Enumeration beendet wurde. Der Benutzer muss die gleiche Abfrage wiederholen und dabei das Fortsetzungstoken aus der vorherigen Abfrage übergeben, um weitere Ergebnisse zu erhalten.

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
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Mit dem folgenden Cmdlet werden die Dienste mit dem Integritätsstatus „Warnung“ abgerufen:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Cluster- und Anwendungsupgrades
Während eines überwachten Upgrades von Cluster und Anwendung wird von Service Fabric die Integrität überprüft, um sicherzustellen, dass alles fehlerfrei bleibt. Wenn eine Entität von den konfigurierten Integritätsrichtlinien als fehlerhaft ausgewertet wird, wendet das Upgrade spezifische Richtlinien an, um die nächste Aktion festzulegen. Das Upgrade kann angehalten werden, um eine Benutzerinteraktion (z. B. das Beheben von Fehlerbedingungen oder das Ändern von Richtlinien) zu ermöglichen, oder es kann automatisch einen Rollback auf die vorherige, einwandfreie Version durchführen.

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

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Erfahren Sie mehr über das [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

## Verwenden von Integritätsauswertungen zur Problembehandlung
Wenn im Cluster oder in einer Anwendung ein Problem vorliegt, können Sie sich die Integrität des Clusters oder der Anwendung ansehen, um die Ursache zu ermitteln. In den Fehlerauswertungen werden Details zur Ursache bereitgestellt, durch die der aktuelle Fehlerstatus ausgelöst wurde. Nach Bedarf können Sie ein Detailinformationen zu den fehlerhaften untergeordneten Entitäten anzeigen, um die Ursache zu ermitteln.

> [AZURE.NOTE] Die Fehlerauswertungen geben Aufschluss über den ersten Grund für den aktuellen Integritätszustand der Entität. Der Zustand ist unter Umständen auf mehrere andere Ereignisse zurückzuführen, diese werden bei der Auswertung jedoch nicht berücksichtigt. Sie müssen sich die Detailinformationen der Integritätsentitäten ansehen, um alle Fehlerberichte im Cluster zu ermitteln.

## Nächste Schritte
[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten](service-fabric-report-health.md)

[Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0323_2016-->