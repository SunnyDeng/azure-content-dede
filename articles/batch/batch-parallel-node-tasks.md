<properties
   pageTitle="Maximieren der Nutzung von Batch-Knoten mit parallelen Aufgaben | Microsoft Azure"
   description="Steigern der Effizienz und Reduzieren von Kosten durch Verringern der Computeknoten und Ausführen paralleler Aufgaben auf jedem Knoten in einem Azure Batch-Pool"
   services="batch"
   documentationCenter=".net"
   authors="mmacy"
   manager="timlt"
   editor=""/>

   <tags
   	ms.service="batch"
   	ms.devlang="multiple"
   	ms.topic="article"
   	ms.tgt_pltfrm="vm-windows"
   	ms.workload="big-compute"
   	ms.date="11/02/2015"
   	ms.author="v-marsma"/>

# Maximieren der Azure Batch Compute-Ressourcenauslastung mit parallelen Knotenaufgaben

Die parallele Ausführung von Aufgaben im großen Maßstab ist ein zentrales Feature von Azure Batch. Diese Fähigkeit beinhaltet nicht nur viele Computeknoten die Ihre Aufgaben ausführen, sondern auch das gleichzeitige Ausführen von Aufgaben auf diesen Knoten. Mit Batch kann die Ausführung paralleler Aufgaben skaliert werden.

Durch Aktivieren der zeitgleichen Ausführung auf den Computeknoten eines Pools wird die Maximierung der Ressourcenauslastung auf einer kleineren Anzahl von Knoten innerhalb des Pools möglich. In manchen Szenarien profitieren Sie davon, dass alle Ressourcen eines Knotens einer einzigen Aufgabe zugewiesen werden können. Dagegen profitieren Sie in vielen Situationen davon, diese Ressourcen von mehreren Aufgaben nutzen zu lassen.

 - **Minimieren des Datenübertragung**, wenn Aufgaben Daten gemeinsam nutzen können. In diesem Szenario können Sie die Gebühren für die Datenübertragung deutlich reduzieren, indem Sie freigegebene Daten auf eine kleinere Anzahl Knoten kopieren und Aufgaben parallel an jedem Knoten ausführen. Dies trifft besonders dann zu, wenn die zu jedem Knoten zu kopierenden Daten zwischen verschiedenen geografischen Regionen übertragen werden müssen.

 - **Maximieren der Speicherauslastung**, wenn Aufgaben viel Speicherplatz beanspruchen – jedoch nur für kurze Zeiträumen und zu unterschiedlichen Zeiten während der Ausführung. Weniger, dafür aber größere Knoteninstanzen mit mehr Arbeitsspeicher, können für die effiziente Handhabung solcher Spitzen verwendet werden. Diese Knoteninstanzen hätten Parallelaufgaben, die auf jedem Knoten ausgeführt werden, was den umfangreichen Arbeitsspeicher der Knoten zu verschiedenen Zeitpunkten optimal ausnutzen würde.

 - **Verringern der Beschränkungen der Knotenanzahl**, wenn die Kommunikation zwischen Knoten in einem Pool erforderlich ist. Derzeit sind Pools, die für die Kommunikation zwischen den Knoten konfiguriert sind, auf 50 Knoten beschränkt. Daher kann eine größere Anzahl von Aufgaben gleichzeitig ausgeführt werden, wenn jeder Knoten in einem Pool Aufgaben parallel ausführen kann.

 - **Replizieren eines lokalen Computeclusters**, z. B. beim ersten Auslagern einer Compute-Umgebung in Azure. Sie können die maximalen Anzahl von Knotenaufgaben erhöhen, um eine vorhandene physische Konfiguration besser abzubilden, falls diese Konfiguration gegenwärtig mehrere Aufgaben per Computeknoten ausführt.

## Beispielszenario

Nachstehend finden Sie ein Beispiel, das die Vorteile der parallelen Aufgabenausführung demonstriert: Nehmen wir an, dass für Ihre Aufgabenanwendung CPU- und Arbeitsspeicher-Anforderungen der Art vorliegen, dass die Knotengröße Standard\_D1 geeignet ist. Um den Auftrag in der geforderten Zeit aufzuführen, sind jedoch 1.000 solcher Knoten nötig.

Statt Standard\_D1-Knoten zu verwenden, könnten Sie Standard\_D14-Knoten mit 16 Kernen verwenden und so die parallele Ausführung von Aufgaben ermöglichen. In diesem Fall könnten daher *16 Mal weniger Knoten* verwendet werden – statt 1.000 Knoten wären nur 63 erforderlich. Dadurch wird die Ausführungszeit und Effizienz beträchtlich verbessert, wenn für jeden Knoten große Anwendungsdateien oder Referenzdaten benötigt werden.

## Aktivieren der parallelen Aufgabenausführung

Sie konfigurieren die Computeknoten in Ihrer Batch-Lösung für die parallele Aufgabenausführung auf der Pool-Ebene. Beim Arbeiten mit der Batch .NET-API legen Sie die „[CloudPool.MaxTasksPerComputeNode][maxtasks_net]”-Eigenschaft fest, wenn Sie einen Pool erstellen. In der Batch REST-API legen Sie das „[maxTasksPerNode][maxtasks_rest]”-Element bei der Erstellung des Pools im Anforderungstext fest.

Die maximale Anzahl an Aufgaben pro Knoten in Azure Batch beträgt bis zu viermal (4x) die Anzahl der Knotenkerne. Ist der Pool beispielsweise mit Knoten der Größe „Groß“ (vier Kerne) konfiguriert, kann für „`maxTasksPerNode`” „16“ festgelegt werden. Mehr Informationen zur Anzahl der Kerne für jede Knotengröße finden Sie unter [Größen für Clouddienste](../cloud-services/cloud-services-sizes-specs.md). Weitere Informationen zu den Grenzen des Dienstes finden Sie in [Kontingente und Einschränkungen für den Azure Batch-Dienst](batch-quota-limit.md).

> [AZURE.TIP]Berücksichtigen Sie unbedingt den `maxTasksPerNode`-Wert, wenn Sie für Ihren Pool eine [Formel für das automatische Skalieren][enable_autoscaling] erstellen. Beispielsweise könnte eine Formel zum Auswerten von `$RunningTasks` erheblich von einer Steigerung der Aufgaben pro Knoten betroffen sein. Weitere Informationen finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](batch-automatic-scaling.md).

## Verteilung von Aufgaben

Wenn die Computeknoten in einem Pool Aufgaben parallel ausführen können, müssen Sie angeben, wie die Aufgaben auf die Knoten innerhalb des Pools verteilt werden sollen.

Mithilfe der „[CloudPool.TaskSchedulingPolicy][task_schedule]”-Eigenschaft können Sie angeben, dass Aufgaben gleichmäßig über alle Knoten im Pool zugewiesen werden sollen („Verteilen“). Oder Sie können angeben, dass jedem Knoten so viele Aufgaben wie möglich zugewiesen werden sollen, bevor sie einem anderen Knoten im Pool zugewiesen werden („Packen“).

Als Beispiel für den Wert dieser Eigenschaft betrachten Sie den Pool aus Standard\_D14-Knoten ( im obigen Beispiel) die mit einem [CloudPool.MaxTasksPerComputeNode][maxtasks_net] - Wert von 16 konfiguriert wurden. Bei Konfiguration der „[CloudPool.TaskSchedulingPolicy][task_schedule]” mit einem „[ComputeNodeFillType][fill_type]” der Art „*Pack*” wird die Auslastung aller 16 Kerne für die einzelnen Knoten maximiert und ein [ automatischer Skalierungspool](./batch-automatic-scaling.md) ermöglicht, der nicht verwendete Knoten (Knoten, denen keine Aufgaben zugewiesen sind) aus dem Pool löscht. Dies minimiert die Ressourcenverwendung und spart Geld.

## Beispiel für Batch .NET

Dieser [Batch .NET][api_net]-API-Codeausschnitt zeigt eine Anforderung zum Erstellen eines Pools aus vier großen Knoten mit maximal vier Aufgaben pro Knoten. Er gibt eine Richtlinie für die Aufgabenplanung vor, die besagt, dass jeder Knoten mit Aufgaben gefüllt werden soll, bevor diese den anderen Knoten im Pool zugewiesen werden. Weitere Informationen zum Hinzufügen von Pools mit der Batch .NET-API finden Sie unter [BatchClient.PoolOperations.CreatePool][poolcreate_net].

        CloudPool pool = batchClient.PoolOperations.CreatePool(poolId: "mypool",
        													osFamily: "2",
        													virtualMachineSize: "large",
        													targetDedicated: 4);
        pool.MaxTasksPerComputeNode = 4;
        pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
        pool.Commit();

## Beispiel für Batch REST

Dieser [Batch REST][api_rest]-API-Ausschnitt zeigt eine Anforderung zum Erstellen eines Pools aus zwei großen Knoten mit maximal vier Aufgaben pro Knoten. Weitere Informationen zum Hinzufügen von Pools mit der REST-API finden Sie unter [Hinzufügen eines Pools zu einem Konto][maxtasks_rest].

        {
          "id": "mypool",
          "vmSize": "Large",
          "osFamily": "2",
          "targetOSVersion": "*",
          "targetDedicated": 2,
          "enableInterNodeCommunication": false,
          "maxTasksPerNode": 4
        }

> [AZURE.NOTE]Sie können das `maxTasksPerNode`-Element und die „[MaxTasksPerComputeNode][maxtasks_net]”-Eigenschaft nur zum Zeitpunkt der Pool-Erstellung festlegen. Nach der Erstellung eines Pools können sie nicht mehr geändert werden.

## Untersuchen des Beispielprojekts

Sehen Sie sich das [ParallelNodeTasks][parallel_tasks_sample] -Projekt auf GitHub an. Es handelt sich dabei um ein ausführbares Codebeispiel, das die Verwendung von „[CloudPool.MaxTasksPerComputeNode][maxtasks_net]” veranschaulicht.

Diese C#-Konsolenanwendung verwendet die [Batch .NET][api_net]- Bibliothek zum Erstellen eines Pools mit einem oder mehreren Computeknoten. Sie führt eine konfigurierbare Anzahl an Aufgaben auf diesen Knoten aus, um eine variable Auslastung zu simulieren. Die Ausgabe der Anwendung gibt an, welcher Knoten welche Aufgabe ausgeführt hat. Die Anwendung liefert auch eine Zusammenfassung der Aufgabenparameter und der -dauer. Die Zusammenfassung der Ausgabe von zwei verschiedenen Ausführungen der Beispielanwendung wird unten angezeigt.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Die erste Ausführung der Beispielanwendung zeigt, dass die Aufgabe bei Nutzung eines einzigen Knotens im Pool und einer Aufgabe pro Knoten mehr als 30 Minuten dauert.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Die zweite Ausführung des Beispiels zeigt eine deutliche Verringerung der Aufgabendauer. Das liegt daran, dass der Pool mit vier Aufgaben pro Knoten konfiguriert wurde, was die parallele Aufgabenausführung in etwa einem Viertel der Zeit ermöglicht.

> [AZURE.NOTE]In der Aufgabedauer in den oben aufgeführten Zusammenfassungen ist die Erstellung des Pools nicht berücksichtigt. Jede der oben aufgeführten Aufgaben wurde an bereits erstellte Pools übermittelt, deren Computeknoten sich zum Übermittlungszeitpunkt im *Idle*-Status befanden.

## Heat Map für Batch Explorer

[Batch Explorer][batch_explorer], eine der [Beispielanwendungen][github_samples] von Azure Batch, enthält eine *Heat Map*-Funktion, welche die Ausführung der Aufgaben veranschaulicht. Wenn Sie die [ParallelTasks][parallel_tasks_sample]-Beispielanwendung ausführen, können Sie die Heat Map-Funktion für eine einfache Visualisierung der Ausführung paralleler Aufgaben an jedem Knoten verwenden.

![Heat Map für Batch Explorer][1]

*Heat Map von Batch-Explorer zeigt einen Pool mit vier Knoten, wobei jeder Knoten derzeit vier Aufgaben ausführt*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=AcomDC_0114_2016-->