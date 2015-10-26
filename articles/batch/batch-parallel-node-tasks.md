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
   	ms.date="09/30/2015"
   	ms.author="v-marsma"/>

# Maximieren der Azure Batch Compute-Ressourcenauslastung mit parallelen Knotenaufgaben

Die parallele Ausführung von Aufgaben ist ein zentrales Feature von Azure Batch und ermöglicht nicht nur das Ausführen von Aufgaben auf mehreren Computeknoten, sondern auch das Ausführen paralleler Aufgaben auf diesen Knoten. Mit Batch kann die Ausführung paralleler Aufgaben skaliert werden.

Durch Aktivieren der parallelen Ausführung auf den Computeknoten eines Pools wird die Maximierung der Ressourcenauslastung auf einer kleineren Anzahl von Knoten innerhalb des Pools möglich. In manchen Szenarien profitieren Sie davon, dass alle Ressourcen eines Knotens einer einzigen Aufgabe zugewiesen werden können. Dagegen profitieren Sie in vielen Situationen davon, diese Ressourcen von mehreren Aufgaben nutzen zu lassen.

 - **Minimieren des Datenübertragung**, wenn Aufgaben Daten gemeinsam nutzen können. In diesem Szenario können Sie durch das Kopieren gemeinsam genutzter Daten auf eine kleinere Anzahl von Knoten und das parallele Ausführen von Aufgaben auf jedem Knoten die Gebühren für Datenübertragungen beträchtlich reduzieren – insbesondere, wenn die in jeden Knoten kopierten Daten zwischen geografischen Regionen übertragen werden müssen.

 - **Maximieren der Speicherauslastung**, wenn Aufgaben viel Speicherplatz beanspruchen – jedoch nur für kurze Zeiträumen und zu unterschiedlichen Zeiten während der Ausführung. Weniger, dafür aber größere Knoteninstanzen mit mehr Arbeitsspeicher können für die effiziente Bewältigung solcher Spitzen eingesetzt werden. Möglich wird dies durch die Ausführung paralleler Aufgaben auf jedem Knoten, die zu unterschiedlichen Zeiten den großen Arbeitsspeicher nutzen.

 - **Verringern der Beschränkungen der Knotenanzahl**, wenn die Kommunikation zwischen Knoten in einem Pool erforderlich ist. Derzeit sind Pools, die für die Kommunikation zwischen den Knoten konfiguriert sind, auf 50 Knoten beschränkt. Daher kann eine größere Anzahl von Aufgaben gleichzeitig ausgeführt werden, wenn jeder Knoten in einem Pool Aufgaben parallel ausführen kann.

 - **Replizieren eines lokalen Computeclusters**, z. B. beim ersten Auslagern einer Compute-Umgebung in Azure. Die Erhöhung der maximalen Anzahl von Knotenaufgaben kann für eine bessere Spiegelung einer vorhandene physische Konfiguration erfolgen, wenn diese gerade mehrere Aufgaben pro Computeknoten ausführt.

## Beispielszenario

Um die Vorteile der parallelen Aufgabenausführung zu veranschaulichen, nehmen wir an, dass für Ihre Aufgabenanwendung CPU- und Speicheranforderungen vorliegen, sodass die Knotengröße Standard\_D1 geeignet ist. Um den Auftrag in der erforderlichen Zeit aufzuführen, sind jedoch 1000 solcher Knoten erforderlich. Statt Standard\_D1-Knoten zu verwenden, könnten Sie Standard\_D14-Knoten mit 16 Kernen verwenden und so die parallele Ausführung von Aufgaben ermöglichen. In diesem Fall könnten daher *16 Mal weniger Knoten* verwendet werden – statt 1000-Knoten wären nur 63 erforderlich. Dadurch wird die Ausführungszeit und Effizienz beträchtlich verbessert, wenn für jeden Knoten große Anwendungsdateien oder Referenzdaten benötigt werden.

## Aktivieren der parallelen Aufgabenausführung

Die Konfiguration der Computeknoten in Ihrer Batch-Lösung für die parallele Aufgabenausführung erfolgt auf Pool-Ebene. Beim Arbeiten mit der Batch .NET-API wird beim Erstellen eines Pools die [CloudPool.MaxTasksPerComputeNode][maxtasks_net]-Eigenschaft festgelegt. In der Batch REST-API wird das [maxTasksPerNode][maxtasks_rest]-Element bei der Erstellung des Pools im Anforderungstext festgelegt.

Die maximale Anzahl an Aufgaben pro Knoten in Azure Batch beträgt die bis zu vierfache (4x) Menge der Kerne pro Knoten. Ist für die Größe der Knoten im Pool beispielsweise „Groß“ (vier Kerne) konfiguriert, kann für `maxTasksPerNode` 16 festgelegt werden. Details zur Anzahl der Kerne für die einzelnen Knotengrößen finden Sie unter [Größen für virtuelle Computer](../virtual-machines/virtual-machines-size-specs.md), und weitere Informationen zu Diensteinschränkungen finden Sie in [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

> [AZURE.TIP]Berücksichtigen Sie unbedingt den `maxTasksPerNode`-Wert, wenn Sie für Ihren Pool eine [Formel für das automatische Skalieren][enable_autoscaling] erstellen. Beispielsweise könnte eine Formel zum Auswerten von `$RunningTasks` erheblich von einer Steigerung der Aufgaben pro Knoten betroffen sein. Weitere Informationen finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](batch-automatic-scaling.md).

## Verteilung von Aufgaben

Wenn die Computeknoten in einem Pool Aufgaben parallel ausführen können, müssen Sie angeben, wie die Aufgaben auf die Knoten innerhalb des Pools verteilt werden sollen.

Mithilfe der [CloudPool.TaskSchedulingPolicy][task_schedule]-Eigenschaft können Sie angeben, dass Aufgaben gleichmäßig allen Knoten im Pool zugewiesen werden sollen („Verteilen“) oder dass jedem Knoten so viele Vorgänge wie möglich zugewiesen werden sollen, bevor Vorgänge einem anderen Knoten im Pool zugewiesen werden („Packen“).

Wie wichtig dieses Feature ist, zeigte sich beispielsweise im oben aufgeführten Beispiel mit dem Pool der Standard\_D14-Knoten, in dem ein [CloudPool.MaxTasksPerComputeNode][maxtasks_net]-Wert von 16 konfiguriert wurde. Bei Konfiguration der [CloudPool.TaskSchedulingPolicy][task_schedule] mit einem [ComputeNodeFillType][fill_type] von *Pack* wird die Auslastung aller 16 Kerne für die einzelnen Knoten maximiert und ein [ automatischer Skalierungspool](./batch-automatic-scaling.md) ermöglicht, der nicht verwendete Knoten (Knoten, dem keine Aufgaben zugewiesen sind) aus dem Pool löscht und dadurch die Ressourcenauslastung und Kosten minimiert.

## Beispiel für Batch .NET

Dieser [Batch .NET][api_net] API-Codeausschnitt zeigt eine Anforderung zum Erstellen eines Pools aus vier großen Knoten mit maximal vier Aufgaben pro Knoten sowie das Festlegen einer Richtlinie zur Aufgabenplanung, die jedem Knoten automatisch Aufgaben zuweist, bevor einem anderen Knoten im Pool Aufgaben zugewiesen werden. Weitere Informationen zum Hinzufügen von Pools mit der Batch .NET-API finden Sie unter [BatchClient.PoolOperations.CreatePool][poolcreate_net].

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

> [AZURE.NOTE]Das `maxTasksPerNode`-Element und die [MaxTasksPerComputeNode][maxtasks_net]-Eigenschaft können nur zum Zeitpunkt der Pool-Erstellung festgelegt werden. Nach der Erstellung eines Pools ist keine Änderung mehr möglich.

## Untersuchen des Beispielprojekts

Erkunden Sie das [ParallelNodeTasks][parallel_tasks_sample]-Projekt auf GitHub, ein Codebeispiel zur Veranschaulichung der [CloudPool.MaxTasksPerComputeNode][maxtasks_net]-Nutzung. Diese C#-Konsolenanwendung verwendet die [Batch .NET][api_net]-Bibliothek zum Erstellen eines Pools aus einem oder mehreren Computeknoten und führt eine konfigurierbare Anzahl von Aufgaben auf diesen Knoten aus, um eine variable Auslastung zu simulieren. In der Ausgabe der Anwendung erfahren Sie, welche Knoten jede Aufgabe ausgeführt haben und Sie erhalten eine Zusammenfassung der Aufgabenparameter und -dauer. Der Zusammenfassung der Ausgabe von zwei verschiedenen Ausführungen der Beispielanwendung wird unten angezeigt.

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

Die zweite Ausführung des Beispiels zeigt eine deutliche Verkürzung der Aufgabedauer, da der Pool mit vier Aufgaben pro Knoten konfiguriert wurde. Durch die parallele Ausführung von Aufgaben kann die Aufgabe in einem Viertel der Zeit abgeschlossen werden.

> [AZURE.NOTE]In der Aufgabedauer in den oben aufgeführten Zusammenfassungen ist die Erstellung des Pools nicht berücksichtigt. Jede der oben aufgeführten Aufgaben wurde an bereits erstellte Pools übermittelt, deren Computeknoten sich Übermittlungszeitpunkt im *Idle*-Status befanden.

## Heat Map für Batch Explorer

[Batch Explorer][batch_explorer], eine der [Beispielanwendungen][github_samples] von Azure Batch, enthält eine *Heat Map*-Funktion, die die Auslastung der Knotenkerne in einem Pool veranschaulicht. Beim Ausführen der [ParallelTasks][parallel_tasks_sample]-Beispielanwendung verwenden Sie die Heat Map-Funktion für eine einfache Visualisierung der Aktivitäten der Knotenkerne.

![Heat Map für Batch Explorer][1]

*Heat Map für Batch Explorer mit vier Knoten von je vier Kernen, von denen jeder gerade eine Aufgabe ausführt*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/de-DE/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[maxtasks_rest]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png

<!---HONumber=Oct15_HO3-->