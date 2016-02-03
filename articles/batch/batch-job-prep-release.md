<properties
	pageTitle="Auftragsvorbereitung und -bereinigung in Batch | Microsoft Azure"
	description="Verwenden Sie Vorbereitungs- und Freigabeaufgaben auf Auftragsebene, um Datenübertragungen auf Azure Batch-Computeknoten zu minimieren und Knoten nach Abschluss des Auftrags zu bereinigen."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/15/2015"
	ms.author="v-marsma"/>

# Ausführen von Auftragsvorbereitungs- und Auftragsabschlussaufgaben auf Azure Batch-Computeknoten

Azure Batch-Aufträge erfordern vor ihrer Ausführung häufig eine Einrichtung und eine Art Wartung nach Abschluss der Aufgaben des Auftrags. Batch stellt die Mechanismen für diese Vorbereitung und Wartung in Form von optionalen Aufgaben zur *Auftragsvorbereitung* und *Auftragsfreigabe* bereit.

Bevor eine Aufgabe eines Auftrags ausgeführt wird, wird die Auftragsvorbereitungsaufgabe auf allen Computeknoten durchgeführt, die für die Ausführung von Aufgaben eingeplant sind. Nach Abschluss des Auftrags wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Sowohl für die Auftragsvorbereitungs- als auch für die Freigabeaufgaben können Sie eine Befehlszeile hinterlegen, die ausgeführt werden soll, wenn die Aufgabe aufgerufen wird. Die Aufgaben bieten Funktionen wie das Herunterladen von Dateien, Ausführen mit höheren Rechten, benutzerdefinierte Umgebungsvariablen, maximale Ausführungsdauer, Anzahl der Wiederholungsversuche und Dateiaufbewahrungsdauer.

In den folgenden Abschnitten wird erläutert, wie Sie diese zwei speziellen Aufgabentypen mithilfe der Klassen „[JobPreparationTask][net_job_prep]” und „[JobReleaseTask][net_job_release]” in der [Batch .NET][api_net]-API verwenden.

> [AZURE.TIP]Auftragsvorbereitungs- und Auftragsfreigabeaufgaben sind besonders in Umgebungen mit einem „gemeinsam genutzten Pool“ hilfreich, d. h. Umgebungen, in denen ein Pool von Computeknoten zwischen Auftragsausführungen beibehalten und von vielen verschiedenen Aufträgen gemeinsam genutzt wird.

## Verwendungsmöglichkeiten für Auftragsvorbereitungs- und Auftragsfreigabeaufgaben

Auftragsvorbereitungs- und Auftragsfreigabeaufgaben sind in vielen Situationen nützlich. Hier seien nur einige genannt:

- **Übertragung von allgemeinen Aufgabendaten**: Batchaufträge erfordern oft einen gemeinsamen Satz von Daten als Eingabe für die Aufgaben des Auftrags. Bei täglich durchgeführten Berechnungen zur Risikoanalyse sind Marktdaten z. B. auftragsspezifisch, gelten aber trotzdem für alle Aufgaben im Auftrag. Diese Marktdaten, die oft mehrere GB groß sind, sollten auf jeden Computeknoten nur einmal heruntergeladen werden, sodass sie von jeder auf einem Knoten ausgeführten Aufgabe verwendet werden können. Verwenden Sie eine *Auftragsvorbereitungsaufgabe*, um die Daten vor der Ausführung anderer Aufgaben auf jeden Knoten herunterzuladen.
- **Löschen von Auftragsdaten**--In einer Umgebung mit einem gemeinsam genutzten Pool, in der die Computeknoten eines Pools zwischen Aufträgen nicht außer Betrieb gesetzt werden, müssen zwischen Ausführungen u. U. Auftragsdaten gelöscht werden, um Speicherplatz auf den Knoten zu sparen oder Sicherheitsrichtlinien des Unternehmens zu erfüllen. Verwenden Sie eine *Auftragsfreigabeaufgabe*, um Daten zu löschen, die von einer Auftragsvorbereitungsaufgabe heruntergeladen oder während der Aufgabenausführung generiert wurden.
- **Protokollaufbewahrung**-- Eventuell wollen Sie eine Kopie der von den Aufgaben generierten Protokolle, oder die von fehlerhaften Anwendungen generierten Absturzabbilddateien aufbewahren. Verwenden Sie in diesen Fällen eine *Auftragsfreigabeaufgabe*, um die Daten zu komprimieren und in ein [Azure-Speicherkonto][azure_storage] hochzuladen.

## Auftragsvorbereitungsaufgabe

Vor der Ausführung der Aufgaben eines Auftrags wird die Auftragsvorbereitungsaufgabe auf jedem Computeknoten, der zum Ausführen einer Aufgabe eingeplant ist, ausgeführt. Standardmäßig wartet der Batch-Dienst bis die Auftragsvorbereitungsaufgabe fertig ist, bevor er die eingeplanten Aufgaben auf dem Knoten ausführt. Sie können den Dienst allerdings auch dahingehend konfigurieren, dass er nicht wartet. Die Auftragsvorbereitungsaufgabe wird erneut auf einem Computeknoten ausgeführt, wenn dieser neu gestartet wird, Sie können jedoch auch dieses Verhalten deaktivieren.

Die Auftragsvorbereitungsaufgabe wird nur auf Knoten ausgeführt, die zum Ausführen einer Aufgabe eingeplant sind. Das verhindert die unnötige Ausführung von Auftragsvorbereitungsaufgaben an Knoten, denen keine Aufgaben zugewiesen sind. Dies spart beispielsweise Datenübertragungsgebühren. Diese Situation tritt ein, wenn die Anzahl der Aufgaben für einen Auftrag geringer ist, als die Anzahl der Knoten in einem Pool. Sie tritt auch ein, wenn die [gleichzeitige Aufgabenausführung](batch-parallel-node-tasks.md) aktiviert ist. Einige Knoten bleiben unbeschäftigt, wenn die Gesamtanzahl an Aufgaben geringer ist, als die Gesamtzahl der möglichen zeitgleich ausgeführten Aufgaben.

> [AZURE.NOTE] [JobPreparationTask][net\_job\_prep\_cloudjob] unterscheidet sich von der [CloudPool.StartTask][pool_starttask] insofern, dass die JobPreparationTask beim Start jedes Auftrags ausgeführt wird, während die StartTask nur ausgeführt wird, wenn ein Computeknoten einem Pool erstmals hinzugefügt oder neu gestartet wird.

## Auftragsfreigabeaufgabe

Nach Abschluss eines Auftrags wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Um einen Auftrag als abgeschlossen zu markieren, geben Sie eine Terminate-Anforderung aus. Der Batch-Dienst legt anschließend den Status des Auftrags auf *Abbrechen* fest, beendet alle aktiven bzw. ausgeführten Aufgaben im Zusammenhang mit dem Auftrag und führt die Auftragsfreigabeaufgabe aus. Danach wird der Status des Auftrags in *Abgeschlossen* geändert.

> [AZURE.NOTE]Beim Löschen eines Auftrags wird die Auftragsfreigabeaufgabe ebenfalls ausgeführt. Wurde ein Auftrag zuvor jedoch beendet, wird die Freigabeaufgabe kein zweites Mal ausgeführt, wenn dieser Auftrag anschließend gelöscht wird.

## Auftragsvorbereitungs- und -freigabeaufgaben in der Batch .NET API

Sie erstellen und konfigurieren ein „[JobPreparationTask][net_job_prep]”-Objekt um eine Auftragsvorbereitungsaufgabe zu spezifizieren und weisen es der „[CloudJob.JobPreparationTask][net_job_prep_cloudjob]”-Eigenschaft Ihres Auftrages zu. Auf ähnliche Weise initialisieren Sie eine „[JobReleaseTask][net_job_release]”, die Sie der „[CloudJob.JobReleaseTask][net_job_prep_cloudjob]”-Eigenschaft zuweisen, um die Freigabeaufgabe des Auftrags festzulegen.

In diesem Codeausschnitt ist `myBatchClient` eine vollständig initialisierte Instanz von [BatchClient][net_batch_client], und `myPool` ist ein vorhandener Pool im Batch-Konto.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

Wie oben erwähnt, wird die Freigabeaufgabe ausgeführt, wenn ein Auftrag beendet oder gelöscht wird. Ein Auftrag mit der Batch .NET API wird beendet, indem „[PoolOperations.TerminateJobAsync][net_job_terminate]” aufgerufen wird. Ein Auftrag wird mit „[PoolOperations.DeleteJobAsync][net_job_delete]” gelöscht. Diese beiden Aktionen werden typischerweise durchgeführt, wenn die Aufgaben eines Auftrag fertig sind, oder ein von Ihnen definierter Timeout erreicht wurde.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Nächste Schritte

### Beispielprojekt auf GitHub

Sehen Sie sich das Beispielprojekt [JobPrepRelease][job_prep_release_sample] auf GitHub an, um Auftragsvorbereitungs- und Auftragsfreigabeaufgaben in Aktion zu erleben. Diese Konsolenanwendung führt folgende Schritte aus:

1. Sie erstellt einen Pool mit zwei „kleinen“ Knoten.
2. Sie erstellt einen Auftrag mit Auftragsvorbereitungs-, Auftragsfreigabe- und Standardaufgaben.
3. Sie führt die Auftragsvorbereitungsaufgabe aus, die zunächst die Knoten-ID in eine Textdatei im „freigegebenen“ Verzeichnis des Knotens schreibt.
4. Sie führt auf jedem Knoten eine Aufgabe aus, welche die Aufgaben-ID in dieselbe Textdatei schreibt.
5. Nach dem Abschluss aller Aufgaben (oder Erreichen des Timeouts) gibt sie den Inhalt der Textdatei jedes Knotens an die Konsole aus.
6. Sie führt die Auftragsfreigabeaufgabe aus, um die Datei aus dem Knoten zu löschen, wenn der Auftrag abgeschlossen ist.
6. Sie gibt die Exitcodes der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben für jeden Knoten aus, auf dem diese ausgeführt wurden.
7. Sie hält die Ausführung an, um auf die Löschbestätigung für den Auftrag und/oder den Pool zu warten.

Die Ausgabe der Beispielanwendung sieht in etwa wie folgt aus:

```
Attempting to create pool: JobPrepReleaseSamplePool
The pool already existed when we tried to create it
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-3105992504_1-20151015t150030z:
-------------------------------------------
tvm-3105992504_1-20151015t150030z tasks:
  task001
  task002
  task006
  task007

Contents of shared\job_prep_and_release.txt on tvm-3105992504_2-20151015t150030z:
-------------------------------------------
tvm-3105992504_2-20151015t150030z tasks:
  task003
  task005
  task004
  task008

Waiting for job JobPrepReleaseSampleJob to reach state Completed
....

tvm-3105992504_1-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-3105992504_2-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
no

Sample complete, hit ENTER to exit...
```

### Überprüfen der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben mit dem Batch-Explorer

Der [Batch-Explorer][batch_explorer_article], der Ihnen ebenfalls im Batch-[Beispielcoderepository][batch_explorer_project] auf GitHub zur Verfügung steht, ist exzellent wenn Sie Lösungen mit Azure Batch entwickeln. Beim Ausführen der obigen Beispielanwendung können Sie beispielsweise mit dem Batch-Explorer die Eigenschaften des Auftrags und seine Aufgaben ansehen oder sogar die, durch die Aufgaben des Auftrags geänderte, freigegebene Textdatei herunterladen.

Der folgende Screenshot hebt die im Bereich „**Auftragsdetails**“ angezeigten Eigenschaften der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben hervor, die angezeigt werden, wenn Sie auf der Registerkarte „**Aufträge**“ den Auftrag „*JobPrepReleaseSampleJob*” ausgewählen.

![Batch-Explorer][1]

*Batch-Explorer-Screenshot mit Auftragsvorbereitungs- und Auftragsfreigabeaufgaben*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_article]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/batchexplorer-01.png
[2]: ./media/batch-job-prep-release/batchexplorer-02.png

<!---HONumber=AcomDC_0114_2016-->