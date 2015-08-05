<properties
   pageTitle="Testability-Aktion"
   description="Dieser Artikel befasst sich mit den Testability-Aktionen von Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="heeldin"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="heeldin;motanv"/>

# Testability-Aktionen
Um eine unzuverlässige Infrastruktur simulieren zu können, bietet Service Fabric Entwicklern die Möglichkeit, verschiedene Ausfälle und Statusübergänge aus der Praxis zu simulieren. Dies wird in Form von Testability-Aktionen (Aktionen zur Prüfbarkeit) verfügbar gemacht. Diese Aktionen sind die Low-Level-APIs, die eine bestimmte Fault Injection, einen Statusübergang oder eine Überprüfung bewirken. Wenn ein Dienstentwickler diese Aktionen kombiniert, kann er umfassende Testszenarien für Ihre Dienste schreiben.

Mit Service Fabric werden standardmäßig einige gängige Testszenarien bereitgestellt, die aus diesen Aktionen bestehen. Es wird dringend empfohlen, diese integrierten Szenarien zu verwenden. Sie wurden sorgfältig ausgewählt, um häufig vorkommende Statusübergänge und Ausfallsituationen zu testen. Aktionen können aber auch zum Erstellen von benutzerdefinierten Testszenarien verwendet werden, wenn Sie Fälle abdecken möchten, die entweder noch nicht in den integrierten Testszenarien enthalten sind oder speziell auf Ihre Anwendung zugeschnitten sind.

Die C#-Implementierung der Aktionen befindet sich in der Assembly „System.Fabric.Testability.dll“. Das PowerShell-Modul „Testability“ befindet sich in der Assembly „Microsoft.ServiceFabric.Testability.Powershell.dll“. Im Rahmen der Laufzeitinstallation wird das PowerShell-Modul ServiceFabricTestability installiert, um eine einfache Nutzung zu ermöglichen.

## Ordnungsgemäße und nicht ordnungsgemäße Fehleraktionen
Testability-Aktionen sind nach zwei Hauptbereichen (Buckets) klassifiziert:

* Nicht ordnungsgemäße Fehler: Mit diesen Fehlern werden Fehler wie Neustarts von Computern und Abstürze von Prozessen simuliert. Bei diesen Fehlern wird der Ausführungskontext von Prozessen abrupt beendet. Dies bedeutet, dass keine Bereinigung des Status ausgeführt werden kann, bevor die Anwendung neu gestartet wird.

* Ordnungsgemäße Fehler: Mit diesen Fehlern werden ordnungsgemäße Aktionen simuliert, z. B. Verschiebungen von Replikaten und per Lastenausgleich ausgelöst Ablegevorgänge. In diesen Fällen erhält der Dienst die Benachrichtigung zum Schließen und kann vor dem Beenden den Zustand bereinigen.

Führen Sie zur besseren Überprüfung der Qualität die Dienst- und Unternehmensworkload aus, während Sie verschiedene ordnungsgemäße und nicht ordnungsgemäße Fehler auslösen. Bei nicht ordnungsgemäßen Fehlern werden Szenarien simuliert, bei denen der Dienstprozess mitten in einem Workflow abrupt beendet wird. Auf diese Weise wird der Wiederherstellungspfad getestet, nachdem das Dienstreplikat von Service Fabric wiederhergestellt wurde. Dies ist eine Hilfe beim Testen der Datenkonsistenz und der richtigen Beibehaltung des Dienstzustands nach Ausfällen. Bei den anderen Fehlern, also den ordnungsgemäßen Fehlern, wird getestet, ob der Dienst richtig auf Replikate reagiert, die von Service Fabric verschoben werden. Hierbei wird auch die Behandlung des Abbruchs in der RunAsync-Methode getestet. Der Dienst muss überprüfen, ob das Abbruchtoken (Cancellation) festgelegt ist, seinen Zustand richtig speichern und die RunAsync-Methode beenden.

## Liste mit Testability-Aktionen

| Aktionen | Beschreibung | Verwaltete API | PowerShell-Cmdlet | Ordnungsgemäße und nicht ordnungsgemäße Fehler |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Entfernt alle Testzustände aus dem Cluster, falls es zu einem fehlerhaften Herunterfahren des Testtreibers kommt. | CleanTestStateAsync | Remove-ServiceFabricTestState | Nicht zutreffend |
| InvokeDataLoss | Verursacht einen Datenverlust für eine Dienstpartition. | InvokeDataLossAsync | Invoke-ServiceFabricPartitionDataLoss | Ordnungsgemäß |
| InvokeQuorumLoss | Versetzt eine bestimmte zustandsbehaftete Dienstpartition in den Zustand eines Quorumverlusts. | InvokeQuorumLossAsync | Invoke-ServiceFabricQuorumLoss | Ordnungsgemäß |
| Move Primary | Verschiebt das angegebene primäre Replikat des zustandsbehafteten Diensts auf den angegebenen Clusterknoten. | MovePrimaryAsync | Move-ServiceFabricPrimaryReplica | Ordnungsgemäß |
| Move Secondary | Verschiebt das aktuelle sekundäre Replikat des zustandsbehafteten Diensts auf einen anderen Clusterknoten. | MoveSecondaryAsync | Move-ServiceFabricSecondaryReplica | Ordnungsgemäß |
| RemoveReplica | Simuliert einen Replikatausfall, indem ein Replikat aus einem Cluster entfernt wird. Das Replikat wird geschlossen, und es wird der Übergang zur Rolle „Keine“ durchgeführt, sodass alle dazugehörigen Zustände aus dem Cluster entfernt werden. | RemoveReplicaAsync | Remove-ServiceFabricReplica | Ordnungsgemäß |
| RestartDeployedCodePackage | Simuliert den Ausfall eines Codepaketprozesses, indem ein Codepaket neu gestartet wird, das auf einem Knoten in einem Cluster bereitgestellt wurde. Dadurch wird der Codepaketprozess abgebrochen, und alle unter dem Prozess gehosteten Benutzerdienstreplikate werden neu gestartet. | RestartDeployedCodePackageAsync | Restart-ServiceFabricDeployedCodePackage | Nicht ordnungsgemäß |
| RestartNode | Simuliert den Ausfall eines Service Fabric-Clusterknotens, indem ein Knoten neu gestartet wird. | RestartNodeAsync | Restart-ServiceFabricNode | Nicht ordnungsgemäß |
| RestartPartition | Simuliert einen Stromausfall in einem Rechenzentrum oder Cluster, indem einige oder alle Replikate einer Partition neu gestartet werden. | RestartPartitionAsync | Restart-ServiceFabricPartition | Ordnungsgemäß |
| RestartReplica | Simuliert den Ausfall eines Replikats, indem ein dauerhaftes Replikat in einem Cluster neu gestartet und das Replikat dann geschlossen und wieder geöffnet wird. | RestartReplicaAsync | Restart-ServiceFabricReplica | Ordnungsgemäß |
| StartNode | Startet einen Knoten in einem Cluster, der bereits beendet ist. | StartNodeAsync | Start-ServiceFabricNode | Nicht zutreffend |
| StopNode | Simuliert den Ausfall eines Knotens, indem ein Knoten in einem Cluster beendet wird. Der Knoten bleibt so lange heruntergefahren, bis StartNode aufgerufen wird. | StopNodeAsync | Stop-ServiceFabricNode | Nicht ordnungsgemäß |
| ValidateApplication | Überprüft die Verfügbarkeit und Integrität aller Service Fabric-Dienste innerhalb einer Anwendung. Dies erfolgt in der Regel nach dem Auslösen eines Fehlers im System. | ValidateApplicationAsync | Test-ServiceFabricApplication | Nicht zutreffend |
| ValidateService | Überprüft die Verfügbarkeit und Integrität eines Service Fabric-Diensts. Dies erfolgt in der Regel nach dem Auslösen eines Fehlers im System. | ValidateServiceAsync | Test-ServiceFabricService | Nicht zutreffend |

## Ausführen einer Testability-Aktion mit PowerShell

In diesem Lernprogramm erfahren Sie, wie Sie eine Testability-Aktion mit PowerShell ausführen. Es wird beschrieben, wie Sie eine Testability-Aktion für einen lokalen Cluster (One-Box) oder einen Azure-Cluster ausführen. „Microsoft.Fabric.Testability.Powershell.dll“ (das PowerShell-Modul „Testability“) wird automatisch installiert, wenn Sie den MSI für Microsoft Service Fabric installieren. Das Modul wird automatisch geladen, wenn Sie eine PowerShell-Eingabeaufforderung öffnen.

Abschnitte des Lernprogramms:

- [Ausführen einer Aktion für einen One-Box-Cluster](#run-an-action-against-a-one-box-cluster)
- [Ausführen einer Aktion für einen Azure-Cluster](#run-an-action-against-an-azure-cluster)

### Ausführen einer Aktion für einen One-Box-Cluster

Um eine Testability-Aktion für einen lokalen Cluster ausführen zu können, müssen Sie zuerst eine Verbindung mit dem Cluster herstellen und die PowerShell-Eingabeaufforderung im Administratormodus öffnen. Wir sehen uns jetzt die Aktion **Restart-ServiceFabricNode** an.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Hier wird die Aktion **Restart-ServiceFabricNode** auf einem Knoten mit dem Namen „Node1“ ausgeführt, und für den Beendigungsmodus (CompletionMode) wird angegeben, dass nicht überprüft werden soll, ob die Neustartaktion erfolgreich war. Wenn für den Beendigungsmodus „Verify“angegeben wird, wird überprüft, ob die Neustartaktion erfolgreich war. Anstatt den Knoten direkt anhand des Namens anzugeben, können Sie ihn auch wie folgt per Partitionsschlüssel und Art des Replikats angeben:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** sollte verwendet werden, um einen Service Fabric-Knoten in einem Cluster neu zu starten. Dadurch wird der Prozess „Fabric.exe“ beendet, und alle Systemdienst- und Benutzerdienstreplikate auf dem Knoten werden neu gestartet. Die Verwendung dieser API zum Testen Ihres Diensts dient Ihnen als Hilfe beim Entdecken von Fehlern in den Failover-Wiederherstellungspfaden. Damit können Ausfälle von Knoten im Cluster besser simuliert werden.

Im folgenden Screenshot ist der Testability-Befehl **Restart-ServiceFabricNode** in Aktion dargestellt.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Die Ausgabe des ersten *Get-ServiceFabricNode*-Elements (ein Cmdlet aus dem ServiceFabric-PowerShell-Modul) zeigt, dass der lokale Cluster über fünf Knoten verfügt: Node.1 bis Node.5. Nach dem Ausführen der Testability-Aktion (Cmdlet) **Restart-ServiceFabricNode** auf dem Knoten mit dem Namen Node.4 ist zu sehen, dass die Betriebszeit des Knotens zurückgesetzt wurde.

### Ausführen einer Aktion für einen Azure-Cluster

Das Ausführen einer Testability-Aktion (mit PowerShell) für einen Azure-Cluster ähnelt dem Ausführen der Aktion für einen lokalen Cluster. Der einzige Unterschied ist, dass Sie vor dem Ausführen der Aktion keine Verbindung mit dem lokalen Cluster herstellen müssen, sondern zuerst eine Verbindung mit dem Azure-Cluster.

## Ausführen einer Testability-Aktion mit C# 

Zum Ausführen einer Testability-Aktion mit C# müssen Sie zuerst per FabricClient eine Verbindung mit dem Cluster herstellen. Anschließend rufen Sie die Parameter ab, die Sie zum Ausführen der Aktion benötigen. Es können verschiedene Parameter verwendet werden, um die gleiche Aktion auszuführen. Eine Möglichkeit zum Ausführen der Aktion RestartServiceFabricNode ist die Verwendung der Knoteninformationen (Knotenname und Knoteninstanz-ID) im Cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Beschreibung der Parameter:

**CompleteMode**: Über den Beendigungsmodus wird angegeben, dass nicht überprüft werden soll, ob die Neustartaktion erfolgreich war. Wenn für den Beendigungsmodus „Verify“ angegeben wird, wird überprüft, ob die Neustartaktion erfolgreich war. **OperationTimeout**: Legt den Zeitraum fest, in dem der Vorgang abgeschlossen werden muss, bevor eine TimeoutException-Ausnahme ausgelöst wird. **CancellationToken**: Ermöglicht das Abbrechen eines anstehenden Aufrufs.

Anstatt den Knoten direkt anhand seines Namens anzugeben, können Sie ihn über einen Partitionsschlüssel und die Art des Replikats angeben.

Weitere Informationen finden Sie unter [Partitionsselektor und Replikatselektor](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //RestartNode using the replicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to Restart Node using Nodename and NodeInstanceID.
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection & security information here.
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection & security information here.
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## Partitionsselektor und Replikatselektor

### Partitionsselektor
Der Partitionsselektor (PartitionSelector) ist eine Hilfsklasse, die im Testability-Modul verfügbar gemacht wird. Sie wird zum Auswählen einer bestimmten Partition verwendet, auf der Testability-Aktionen ausgeführt werden. Sie können damit eine bestimmte Partition auswählen, wenn die Partitions-ID im Voraus bekannt ist. Alternativ dazu können Sie den Partitionsschlüssel angeben. Der Vorgang löst die Partitions-ID dann intern auf. Sie haben auch die Möglichkeit, eine zufällige Partition auszuwählen.

Erstellen Sie zum Verwenden das PartitionSelector-Objekt, und wählen Sie die Partition mit einer der Select*-Methoden aus. Übergeben Sie das PartitionSelector-Objekt anschließend an die API, für die es erforderlich ist. Wenn keine Option ausgewählt wird, wird standardmäßig eine zufällige Partition verwendet.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select Random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select partition based on Id
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### Replikatselektor
Der Replikatselektor (ReplicaSelector) ist eine Hilfsklasse, die im Testability-Modul verfügbar gemacht wird. Sie wird zum Auswählen eines Replikats verwendet, auf dem Testability-Aktionen ausgeführt werden. Sie können damit ein bestimmtes Replikat auswählen, wenn die Replikat-ID im Voraus bekannt ist. Außerdem haben Sie die Möglichkeit, ein primäres Replikat oder auch ein zufälliges sekundäres Replikat auszuwählen. ReplicaSelector wird von PartitionSelector abgeleitet. Sie müssen also sowohl das Replikat als auch die Partition auswählen, auf dem bzw. der der Testability-Vorgang durchgeführt werden soll.

Erstellen Sie zum Verwenden ein ReplicaSelector-Objekt, und legen Sie fest, wie Sie das Replikat und die Partition auswählen möchten. Anschließend können Sie es an die API übergeben, für die es erforderlich ist. Wenn keine Option ausgewählt wird, werden standardmäßig ein zufälliges Replikat und eine zufällige Partition verwendet.

Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829"); PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid); long replicaId = 130559876481875498;

```csharp
// Select Random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select replica by Id
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## Nächste Schritte

- [Testability-Szenarien](service-fabric-testability-scenarios.md)
- Gewusst wie: Testen Ihres Diensts
   - [Simulieren von Ausfällen während der Bearbeitung von Dienstworkloads](service-fabric-testability-workload-tests.md)
   - [Ausfälle bei der Kommunikation von Dienst zu Dienst](service-fabric-testability-scenarios-service-communication.md)
 

<!---HONumber=July15_HO4-->