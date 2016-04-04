<properties
	pageTitle="Ausführen von MPI-Anwendungen in Azure Batch mit Tasks mit mehreren Instanzen | Microsoft Azure"
	description="Erfahren Sie, wie MPI-Anwendungen (Message Passing Interface) mithilfe des Tasktyps mit mehreren Instanzen in Azure Batch ausgeführt werden."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="02/19/2016"
	ms.author="marsma" />

# Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch

Tasks mit mehreren Instanzen dienen zum gleichzeitigen Ausführen eines Azure Batch-Tasks auf mehreren Computeknoten, um High Performance Computing-Szenarien wie MPI-Anwendungen (Message Passing Interface) zu ermöglichen. In diesem Artikel erfahren Sie, wie mithilfe der [Batch .NET][api_net]-Bibliothek Tasks mit mehreren Instanzen ausgeführt werden.

## Task mit mehreren Instanzen – Übersicht

In Batch wird normalerweise jeder Task auf einem einzelnen Computeknoten ausgeführt – Sie übermitteln mehrere Tasks an einen Auftrag, und Batch-Dienst plant die Ausführung der einzelnen Tasks auf einem Knoten. Durch Konfigurieren der **Einstellungen für mehrere Instanzen** eines Tasks können Sie Batch jedoch anweisen, diesen Task in Subtasks zur Ausführung auf mehreren Knoten aufzuteilen.

![Task mit mehreren Instanzen – Übersicht][1]

Wenn Sie einen Task mit Einstellungen für mehrere Instanzen an einen Auftrag übermitteln, führt Batch mehrere für Tasks mit mehreren Instanzen eindeutige Schritte aus:

1. Der Batch-Dienst teilt den Task automatisch in einen **Primärtask** und mehrere **Subtasks** auf. Batch plant dann die Ausführung des Primärtasks und der Subtasks auf den Computeknoten des Pools.
2. Sowohl der Primärtask als auch die Subtasks laden alle **gemeinsamen Ressourcendateien** herunter, die Sie in den Einstellungen für mehreren Instanzen angeben.
3. Nachdem die gemeinsamen Ressourcendateien heruntergeladen wurden, wird der **Koordinationsbefehl**, den Sie in den Einstellungen für mehreren Instanzen angeben, vom Primärtask und den Subtasks ausgeführt. Dieser Koordinationsbefehl wird in der Regel zum Starten einen Hintergrunddiensts (z. B. `smpd.exe` von [Microsoft MPI][msmpi_msdn]) verwendet und kann auch sicherstellen, dass die Knoten zum Verarbeiten von Nachrichten zwischen den Knoten bereit sind.
4. Wenn der Koordinationsbefehl vom Primärtask und allen Subtasks erfolgreich abgeschlossen wurde, wird die **Befehlszeile** des Tasks mit mehreren Instanzen (der „Anwendungsbefehl“) *nur* vom **Primärtask ausgeführt**. In einer [MS MPI][msmpi_msdn]-basierten Lösung führen Sie hier Ihre MPI-fähige Anwendung mit `mpiexec.exe` aus.

> [AZURE.NOTE] Obwohl er sich funktional unterscheidet, ist der „Task mit mehreren Instanzen“ kein eindeutiger Tasktyp wie [StartTask][net_starttask] oder [JobPreparationTask][net_jobprep]. Der Task mit mehreren Instanzen ist einfach eine standardmäßiger Batch-Task ([CloudTask][net_task] in Batch .NET), dessen Einstellungen für mehrere Instanzen konfiguriert wurden. In diesem Artikel bezeichnen wir sie als **Task mit mehreren Instanzen**.

## Anforderungen für Tasks mit mehreren Instanzen

Tasks mit mehreren Instanzen erfordern einen Pool, in dem die **Kommunikation zwischen Knoten aktiviert** und die **gleichzeitige Ausführung von Tasks deaktiviert** ist. Wenn Sie versuchen, einen Task mit mehreren Instanzen in einem Pool auszuführen, in dem die knotenübergreifende Kommunikation deaktiviert ist oder der einen höheren *maxTasksPerNode*-Wert als 1 aufweist, wird der Task nie geplant – er bleibt auf unbestimmte Zeit im aktiven Zustand. Dieser Codeausschnitt zeigt die Erstellung eines solchen Pools mithilfe der Batch-Bibliothek für .NET.

```
CloudPool myCloudPool =
	myBatchClient.PoolOperations.CreatePool(
		poolId: "MultiInstanceSamplePool",
		osFamily: "4",
		virtualMachineSize: "small",
		targetDedicated: 3);

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Darüber hinaus werden Tasks mit mehreren Instanzen *nur* auf Knoten in **Pools, die nach dem 14. Dezember 2015 erstellt wurden**, ausgeführt.

> [AZURE.TIP] Wenn Sie in Ihrem Batch-Pool [Computeknoten der Größe A8 oder A9](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) verwenden, kann die MPI-Anwendung das hochleistungsfähige RDMA-Netzwerk (Remote Direct Memory Access) mit geringer Latenz von Azure nutzen. Die vollständige Liste der für Batch-Pools verfügbaren Computeknotengrößen finden Sie unter [Größen für Clouddienste](./../cloud-services/cloud-services-sizes-specs.md).

### Verwenden eines StartTask für die Installation der MPI-Anwendung

Zum Ausführen von MPI-Anwendungen mit einem Task mit mehreren Instanzen müssen Sie zunächst die MPI-Software auf den Computeknoten im Pool verfügbar machen. Dies ist der ideale Zeitpunkt, um einen [StartTask][net_starttask] zu verwenden, der immer ausgeführt wird, wenn ein Knoten mit einem Pool verknüpft oder neu gestartet wird. Dieser Codeausschnitt erstellt einen StartTask, der das MS-MPI-Setuppaket als [Ressourcendatei][net_resourcefile] festlegt, sowie die Befehlszeile, die ausgeführt wird, nachdem die Ressourcendatei auf den Knoten heruntergeladen wurde.

```
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

> [AZURE.NOTE] Bei der Implementierung einer MPI-Lösung mit Tasks mit mehreren Instanzen in Batch sind Sie nicht auf die Verwendung von MS-MPI beschränkt. Sie können jede Implementierung des MPI-Standards verwenden, die mit dem Betriebssystem kompatibel ist, das Sie für die Computeknoten in Ihrem Pool angeben.

## Erstellen eines Tasks mit mehreren Instanzen mithilfe von Batch .NET

Nachdem wie die Poolanforderungen und die MPI-Paketinstallation jetzt behandelt haben, erstellen wir den Task mit mehreren Instanzen. In diesem Ausschnitt erstellen wir einen Standard-[CloudTask][net_task] und konfigurieren dann seine [MultiInstanceSettings][net_multiinstance_prop] Eigenschaft. Wie bereits erwähnt, ist der Task mit mehreren Instanzen kein eigener Tasktyp, sondern ein standardmäßiger Batch-Task, der mit Einstellungen für mehrere Instanzen konfiguriert ist.

```
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## Primärer Task und Subtasks

Beim Erstellen der Einstellungen für mehrere Instanzen für einen Task geben Sie die Anzahl von Computeknoten an, auf denen der Task ausgeführt werden soll. Wenn Sie den Task an einen Auftrag übermitteln, erstellt der Batch-Dienst einen **Primärtask** und eine ausreichende Anzahl von **Subtasks**, die zusammen der festgelegten Anzahl von Knoten entsprechen.

Diesen Tasks wird eine ganzzahlige ID im Bereich von 0 bis *numberOfInstances - 1* zugewiesen. Der Task mit der ID 0 ist der primäre Task, und alle anderen IDs sind Subtasks. Wenn Sie z. B. für einen Task die folgenden -Einstellungen für mehrere Instanzen erstellen, erhält der Primärtask die ID 0, und die Subtasks erhalten die IDs 1 bis 9.

```
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

## Koordinations- und Anwendungsbefehle

Der **Koordinationsbefehl** wird vom Primärtask und den Subtasks ausgeführt. Nachdem der Primärtask und die Subtasks die Ausführung des Koordinationsbefehls abgeschlossen haben, wird die Befehlszeile des Tasks mit mehreren Instanzen *nur* vom Primärtask ausgeführt. Wir bezeichnen diese Befehlszeile zur Unterscheidung vom Koordinationsbefehl als **Anwendungsbefehl**.

Der Aufruf des Koordinationsbefehls führt zu einer Blockierung – Batch führt den Anwendungsbefehl erst dann aus, wenn der Koordinationsbefehl für alle Subtasks erfolgreich zurückgegeben wurde. Der Koordinationsbefehl sollte daher alle erforderlichen Hintergrunddienste starten, sicherstellen, dass sie einsatzbereit sind, und dann beendet werden. Dieser Koordinationsbefehl für eine Lösung mit MS-MPI Version 7 startet z. B. den SMPD-Dienst auf dem Knoten und wird dann beendet:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Beachten Sie die Verwendung von `start` in diesem Koordinationsbefehl. Dies ist erforderlich, da die `smpd.exe`-Anwendung nicht sofort nach der Ausführung zurückgegeben wird. Ohne die Verwendung des [start][cmd_start]-Befehls würde der Koordinationsbefehl nicht zurückgegeben und würde daher die Ausführung des Anwendungsbefehls blockieren.

Der **Anwendungsbefehl**, die für den Task mit mehreren Instanzen angegebene Befehlszeile, wird *nur* vom Primärtask ausgeführt. In MS-MPI-Anwendungen ist dies die Ausführung Ihrer MPI-fähigen Anwendung mit `mpiexec.exe`. Hier sehen Sie ist z. B. einen Anwendungsbefehl für eine Lösung mit MS-MPI Version 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

## Ressourcendateien

Es gibt zwei Sätze von Ressourcendateien, die bei Tasks mit mehreren Instanzen in berücksichtigt werden müssen: **gemeinsame Ressourcendateien**, die von *allen* Tasks (Primärtask und Subtasks) heruntergeladen werden, und die **Ressourcendateien** für den Task mit mehreren Instanzen selbst, die *nur vom Primärtask* heruntergeladen werden.

In den Einstellungen für mehrere Instanzen eines Tasks können Sie eine oder mehrere **gemeinsame Ressourcendateien** angeben. Diese gemeinsamen Ressourcendateien werden vom Primärtask und den Subtasks aus [Azure Storage](./../storage/storage-introduction.md) in das freigegebene Verzeichnis des Tasks aller Knoten heruntergeladen. Sie können mithilfe der `AZ_BATCH_TASK_SHARED_DIR`-Umgebungsvariable über Anwendungs- und Koordinationsbefehlszeilen auf das freigegebene Verzeichnis eines Tasks zugreifen.

Ressourcendateien, die Sie für den Task mit mehreren Instanzen selbst festlegen, werden *nur* vom Primärtask in das Arbeitsverzeichnis `AZ_BATCH_TASK_WORKING_DIR` des Tasks heruntergeladen – die Subtasks laden die für den Task mit mehreren Instanzen angegebenen Ressourcendateien nicht herunter.

Der Primärtask und die Subtasks, die auf einem Knoten ausgeführt werden, haben Zugriff auf die Inhalte von `AZ_BATCH_TASK_SHARED_DIR`. Ein Beispiel für das freigegebene Verzeichnis eines Tasks ist `tasks/mybatchjob/job-1/mymultiinstancetask/`. Der Primärtask und jeder Subtask verfügen außerdem über auch über ein Arbeitsverzeichnis, das nur für diesen Task mithilfe der Umgebungsvariable `AZ_BATCH_TASK_WORKING_DIR` zugänglich ist

Beachten Sie, dass in den Codebeispielen in diesem Artikel keine Ressourcendateien für den Task mit mehreren Instanzen selbst angegeben sind, nur für den StartTask des Pools und die [CommonResourceFiles][net_multiinsance_commonresfiles] der Einstellungen für mehreren Instanzen.

> [AZURE.IMPORTANT] Verwenden Sie immer die Umgebungsvariablen `AZ_BATCH_TASK_SHARED_DIR` und `AZ_BATCH_TASK_WORKING_DIR`, um in Ihren Befehlszeilen auf diese Verzeichnisse zu verweisen. Versuchen Sie nicht, die Pfade manuell zu erstellen.

## Gültigkeitsdauer von Tasks

Die Gültigkeitsdauer des Primärtasks steuert die Gültigkeitsdauer des gesamten Tasks mit mehreren Instanzen. Wenn der Primärtask beendet wird, werden auch alle Subtasks beendet. Der Exitcode des Primärtasks ist der Exitcode des Tasks und wird daher verwendet, um den Erfolg oder Misserfolg des Tasks zu Wiederholungszwecken zu bestimmen.

Wenn beispielsweise einer der Subtasks fehlschlägt und mit einem Rückgabecode ungleich NULL beendet wird, schlägt der gesamte Task mit mehreren Instanzen fehl. Der Task mit mehreren Instanzen wird dann beendet und wiederholt, bis die Grenze für erneute Versuche erreicht ist.

Wenn Sie einen Task mit mehreren Instanzen löschen, werden der Primärtask und alle Subtasks ebenfalls vom Batch-Dienst gelöscht. Alle Subtaskverzeichnisse und die Dateien darin werden genau wie bei einem Standardtask von den Computeknoten gelöscht.

[TaskConstraints][net_taskconstraints] für einen Task mit mehreren Instanzen, z. B. die [MaxTaskRetryCount][net_taskconstraint_maxretry]- [MaxWallClockTime][net_taskconstraint_maxwallclock]- und [RetentionTime][net_taskconstraint_retention]-Eigenschaften werden berücksichtigt, da sie für einen Standardtask angegeben sind und für den Primärtask sowie alle Subtasks gelten. Wenn Sie die [RetentionTime][net_taskconstraint_retention]-Eigenschaft jedoch nach dem Hinzufügen des Tasks mit mehreren Instanzen zum Auftrag ändern, wird diese Änderung nur für den Primärtask übernommen. Alle Subtasks verwenden weiterhin die ursprüngliche [RetentionTime][net_taskconstraint_retention]-Eigenschaft.

In der Liste der aktuellen Tasks eines Computeknotens wird die ID eines Subtasks angezeigt, wenn der aktuelle Task Teil eines Tasks mit mehreren Instanzen war.

## Abrufen von Informationen zu Subtasks

Zum Abrufen von Informationen zu Subtasks mithilfe der Batch-Bibliothek für .NET rufen Sie die [CloudTask.ListSubtasks][net_task_listsubtasks]-Methode auf. Diese Methode gibt Informationen zu allen Subtasks sowie zum Computeknoten zurück, auf dem die Tasks ausgeführt wurden. Anhand dieser Informationen können Sie u. a. das Stammverzeichnis, die Pool-ID, den aktuellen Zustand und den Exitcode der einzelnen Subtasks bestimmen. Diese Informationen können Sie in Kombination mit der [PoolOperations.GetNodeFile][poolops_getnodefile]-Methode zum Abrufen der Dateien des Subtasks verwenden. Beachten Sie, dass diese Methode keine Informationen für den Primärtask (ID 0) zurückgibt.

> [AZURE.NOTE] Sofern nicht anders angegeben, gelten Batch .NET-Methoden, die für den [CloudTask][net_task] mit mehreren Instanzen selbst angewendet werden, *nur* für den Primärtask. Wenn Sie z. B. die [CloudTask.ListNodeFiles][net_task_listnodefiles]-Methode für einen Task mit mehreren Instanzen aufrufen, werden nur die Dateien des Primärtasks zurückgegeben.

Der folgende Codeausschnitt zeigt das Abrufen von Informationen zu Subtasks sowie das Anfordern von Dateiinhalten von den Knoten, auf denen sie ausgeführt werden.

```
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
			await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## Nächste Schritte

- Es empfiehlt sich, eine einfache MS-MPI-Anwendung zu erstellen, die zum Testen von Tasks mit mehreren Instanzen in Batch verwendet wird. Der Blogartikel [How to compile and run a simple MS-MPI program (Kompilieren und Ausführen eines einfachen MS-MPI-Programms][msmpi_howto] des Microsoft HPC- und Azure Batch-Teams enthält eine exemplarische Vorgehensweise zum Erstellen einer einfachen MPI-Anwendung mithilfe von MS-MPI.

- Aktuelle Informationen zu MS-MPI finden Sie auf der Seite [Microsoft MPI][msmpi_msdn] auf MSDN.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Mehrere Instanzen – Übersicht"

<!---HONumber=AcomDC_0323_2016-->