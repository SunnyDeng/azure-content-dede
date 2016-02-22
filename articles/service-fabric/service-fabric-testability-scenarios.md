<properties
   pageTitle="Chaos- und Failovertests | Microsoft Azure"
   description="Mithilfe der Chaos- und Failovertestszenarien von Service Fabric können Sie Fehler herbeiführen und die Zuverlässigkeit Ihrer Dienste überprüfen."
   services="service-fabric"
   documentationCenter=".net"
   authors="anmolah"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/03/2016"
   ms.author="anmola"/>

# Testability-Szenarien
Große verteilte Systeme, z. B. Cloudinfrastrukturen, sind häufig unzuverlässig. Mit Azure Service Fabric können Entwickler Dienste schreiben, die für unzuverlässige Infrastrukturen ausgeführt werden. Um hochwertige Dienste schreiben zu können, müssen Entwickler dazu in der Lage sein, diese unzuverlässigen Infrastrukturen auszulösen, um die Stabilität ihrer Dienste testen zu können.

Service Fabric ermöglicht Entwicklern das Auslösen von Fehleraktionen, um das Verhalten von Diensten beim Auftreten von Fehlern zu testen. Mit zielgerichteten simulierten Fehlern können Sie aber nicht alle Eventualitäten abdecken. Um den Testumfang zu erweitern, können Sie die Testszenarien in Service Fabric verwenden: einen Chaostest und einen Failovertest. Mit diesen Szenarien werden im Cluster über längere Zeiträume hinweg fortlaufende überlappende Fehler simuliert (sowohl ordnungsgemäß als auch nicht ordnungsgemäß). Nachdem die Rate und die Art der Fehler für einen Test konfiguriert wurde, wird er als clientseitiges Tool ausgeführt. Hierbei werden entweder die C#-APIs oder PowerShell genutzt, um Fehler im Cluster und in Ihrem Dienst zu generieren.

## Chaostest
Beim Chaosszenario werden Fehler im gesamten Service Fabric-Cluster generiert. Fehler, die normalerweise in Zeiträumen von mehreren Monaten oder Jahren auftreten, werden auf wenige Stunden komprimiert. Bei dieser Kombination aus überlappenden Fehlern mit der hohen Fehlerrate werden auch Spezialfälle erkannt, die sonst nicht auffallen. Dies führt zu einer erheblichen Verbesserung bei der Codequalität des Diensts.

### Beim Chaostest simulierte Fehler
 - Neustart eines Knotens
 - Neustart eines bereitgestellten Codepakets
 - Entfernung eines Replikats
 - Neustart eines Replikats
 - Verschiebung eines primären Replikats (optional)
 - Verschiebung eines sekundären Replikats (optional)

Beim Chaostest werden im angegebenen Zeitraum mehrere Iterationen von Fehlern und Clusterüberprüfungen durchgeführt. Die Dauer der Stabilisierung des Clusters und des erfolgreichen Abschlusses der Überprüfung kann ebenfalls konfiguriert werden. Das Szenario ist nicht erfolgreich, wenn es bei der Clusterüberprüfung zu einem Einzelfehler kommt.

Nehmen wir beispielsweise an, ein Test soll eine Stunde lang ausgeführt werden und maximal drei gleichzeitige Fehler umfassen. Während des Tests werden drei Fehler ausgelöst, und anschließend wird die Clusterintegrität überprüft. Der Test durchläuft den vorherigen Schritt so lange, bis die Integrität des Clusters nicht mehr gewährleistet oder eine Stunde vergangen ist. Falls die Integrität des Clusters bei einer Iteration nicht mehr besteht, also keine Stabilisierung innerhalb eines konfigurierten Zeitraums möglich ist, ist der Test nicht erfolgreich, und es tritt eine Ausnahme auf. Mit der Ausnahme wird angegeben, dass ein Fehler aufgetreten ist, der weiter untersucht werden muss.

In seiner derzeitigen Form enthält das Fehlergenerierungsmodul des Chaostests nur sichere Fehler. Dies bedeutet, dass bei einem Nichtvorhandensein von externen Fehlern niemals ein Quorum- oder Datenverlust auftritt.

### Wichtige Konfigurationsoptionen
 - **TimeToRun**: Gesamtdauer der Ausführung des Tests, bevor er erfolgreich abgeschlossen wird. Beim Auftreten eines Überprüfungsfehlers kann der Test auch früher abgeschlossen werden.
 - **MaxClusterStabilizationTimeout**: Maximaler Zeitraum, wie lange auf die Wiederherstellung der Integrität des Clusters gewartet wird, bevor der Test als nicht erfolgreich gewertet wird. Folgende Überprüfungen werden durchgeführt: ob Clusterintegrität besteht, ob Dienstintegrität besteht, ob für die Dienstpartition die Größe der Zielreplikatgruppe erreicht wird und ob keine InBuild-Replikate vorhanden sind.
 - **MaxConcurrentFaults**: Maximale Anzahl von gleichzeitigen Fehlern, die bei jeder Iteration ausgelöst werden. Je höher die Anzahl ist, desto aggressiver ist der Test. Dies führt zu komplexeren Failovern und Übergangskombinationen. Mit dem Test wird garantiert, dass es bei einem Nichtvorhandensein von externen Fehlern nicht zu einem Quorum- oder Datenverlust kommt. Dabei spielt es keine Rolle, wie hoch der Wert für diese Konfiguration gewählt wurde.
 - **EnableMoveReplicaFaults**: Aktiviert oder deaktiviert die Fehler, die zur Verschiebung der primären oder sekundären Replikate führen. Diese Fehler sind standardmäßig deaktiviert.
 - **WaitTimeBetweenIterations**: Gibt an, wie lange zwischen Iterationen gewartet wird (also nach einer Fehlerrunde und der entsprechenden Überprüfung).

### Ausführen des Chaostests
C#-Beispiel

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## Failovertest

Das Failovertestszenario ist eine Version des Chaostestszenarios für eine bestimmte Dienstpartition. Damit werden die Auswirkungen eines Failovers auf eine bestimmte Dienstpartition getestet, während die anderen Dienste nicht beeinträchtigt werden. Nach der Konfiguration mit den Informationen zur Zielpartition und anderen Parametern wird es entweder mit C#-APIs oder PowerShell als clientseitiges Tool ausgeführt, um Fehler für eine Dienstpartition zu generieren. Bei diesem Szenario werden eine Sequenz von simulierten Fehlern und die Dienstüberprüfung durchlaufen, während Ihre Geschäftslogik im Hintergrund ausgeführt wird, damit eine Workload vorhanden ist. Ein Fehler bei der Dienstüberprüfung weist auf ein Problem hin, das genauer untersucht werden muss.

### Beim Failovertest simulierte Fehler
- Neustart eines bereitgestellten Codepakets, mit dem die Partition gehostet wird
- Entfernung eines primären bzw. sekundären Replikats oder einer zustandslosen Instanz
- Neustart eines primären bzw. sekundären Replikats (bei einem dauerhaften Dienst)
- Verschiebung eines primären Replikats
- Verschiebung eines sekundären Replikats
- Neustart der Partition

Beim Failovertest wird ein ausgewählter Fehler ausgelöst, und anschließend wird die Überprüfung für den Dienst durchgeführt, um dessen Stabilität sicherzustellen. Beim Failovertest wird nur jeweils ein Fehler ausgelöst, während beim Chaostest mehrere Fehler ausgelöst werden können. Wenn die Dienstpartition nach jedem Fehler nicht innerhalb des konfigurierten Zeitraums stabilisiert wird, ist der Test nicht erfolgreich. Beim Test werden nur sichere Fehler ausgelöst. Dies bedeutet, dass bei einem Nichtvorhandensein von externen Fehlern kein Quorum- oder Datenverlust auftritt.

### Wichtige Konfigurationsoptionen
 - **PartitionSelector**: Selektorobjekt zum Angeben der gewünschten Partition.
 - **TimeToRun**: Gesamtdauer der Ausführung des Tests, bevor er abgeschlossen wird.
 - **MaxServiceStabilizationTimeout**: Maximaler Zeitraum, wie lange auf die Wiederherstellung der Integrität des Clusters gewartet wird, bevor der Test als nicht erfolgreich gewertet wird. Folgende Überprüfungen werden durchgeführt: ob Dienstintegrität besteht, ob für die Dienstpartition die Größe der Zielreplikatgruppe erreicht wird und ob keine InBuild-Replikate vorhanden sind.
 - **WaitTimeBetweenFaults**: Gibt an, wie lange zwischen den einzelnen Fehler- und Überprüfungszyklen gewartet wird.

### Ausführen des Failovertests
C#-Beispiel

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario chaosScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

<!---HONumber=AcomDC_0211_2016-->