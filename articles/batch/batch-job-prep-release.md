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

Azure Batch-Aufträge erfordern vor ihrer Ausführung häufig eine Einrichtung und ebenso eine Art von Wartung nach Abschluss der Aufgaben des Auftrags. Batch stellt die Mechanismen für diese Vorbereitung und Wartung in Form von optionalen Aufgaben zur *Auftragsvorbereitung* und *Auftragsfreigabe* bereit.

Die Auftragsvorbereitungsaufgabe wird vor der Ausführung der Aufgaben eines Auftrags auf allen zum Ausführen von Aufgaben geplanten Computeknoten ausgeführt. Nach Abschluss des Auftrags wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Sowohl Auftragsvorbereitungs- als auch Auftragsfreigabeaufgaben bieten die Möglichkeit zur Angabe einer Befehlszeile, die beim Aufrufen der Aufgabe ausgeführt wird, sowie Funktionen wie Dateidownload, Ausführung mit erhöhten Rechten, benutzerdefinierte Umgebungsvariablen, maximale Ausführungsdauer, Wiederholungsanzahl und Dateiaufbewahrungsdauer.

In den folgenden Abschnitten wird erläutert, wie Sie diese zwei speziellen Aufgabentypen mithilfe von [JobPreparationTask][net_job_prep] und [JobReleaseTask][net_job_release] in der [Batch .NET][api_net]-API verwenden.

> [AZURE.TIP]Auftragsvorbereitungs- und Auftragsfreigabeaufgaben sind besonders in Umgebungen mit einem „gemeinsam genutzten Pool“ hilfreich, d. h. Umgebungen, in denen ein Pool von Computeknoten zwischen Auftragsausführungen beibehalten und von vielen verschiedenen Aufträgen gemeinsam genutzt wird.

## Verwendungsmöglichkeiten für Auftragsvorbereitungs- und Auftragsfreigabeaufgaben

Auftragsvorbereitungs- und Auftragsfreigabeaufgaben sind in vielen Situationen nützlich. Hier seien nur einige genannt:

- **Übertragung von allgemeinen Aufgabendaten**: Batchaufträge erfordern oft einen gemeinsamen Satz von Daten als Eingabe für die Aufgaben des Auftrags. Bei täglich durchgeführten Berechnungen zur Risikoanalyse sind Marktdaten z. B. auftragsspezifisch, gelten aber trotzdem für alle Aufgaben im Auftrag. Diese Marktdaten, die oft mehrere GB groß sind, sollten nur einmal auf jeden Computeknoten heruntergeladen werden, sodass sie von jeder auf einem Knoten ausgeführten Aufgabe verwendet werden können. Verwenden Sie eine *Auftragsvorbereitungsaufgabe*, um die Daten vor der Ausführung anderer Aufgaben auf jeden Knoten herunterzuladen.
- **Löschen von Auftragsdaten**: In einer Umgebung mit einem gemeinsam genutzten Pool, in der die Computeknoten eines Pools zwischen Aufträgen nicht außer Betrieb gesetzt werden, müssen zwischen Ausführungen u. U. Auftragsdaten gelöscht werden, um Speicherplatz auf den Knoten zu sparen oder Sicherheitsrichtlinien des Unternehmens zu erfüllen. Verwenden Sie eine *Auftragsfreigabeaufgabe*, um Daten zu löschen, die von einer Auftragsvorbereitungsaufgabe heruntergeladen oder während der Aufgabenausführung generiert wurden.
- **Protokollaufbewahrung**: Sie können eine Kopie der von Aufgaben generierten Protokolldateien oder der von fehlerhaften Anwendungen generierten Absturzabbilddateien aufbewahren. Verwenden Sie in diesen Fällen eine *Auftragsfreigabeaufgabe*, um die Daten zu komprimieren und in ein [Azure-Speicherkonto][azure_storage] hochzuladen.

## Auftragsvorbereitungsaufgabe

Vor der Ausführung der Aufgaben eines Auftrags wird die Auftragsvorbereitungsaufgabe auf jedem Computeknoten, der zum Ausführen einer Aufgabe geplant ist, ausgeführt. Standardmäßig wartet der Batch-Dienst, bis die Auftragsvorbereitungsaufgabe abgeschlossen ist, bevor er die geplanten Aufgaben auf dem Knoten ausführt. Sie können den Dienst aber auch so konfigurieren, dass er nicht auf den Abschluss wartet. Die Auftragsvorbereitungsaufgabe wird erneut auf einem Computeknoten ausgeführt, wenn dieser neu gestartet wird, Sie können jedoch auch dieses Verhalten deaktivieren.

Die Auftragsvorbereitungsaufgabe wird nur auf Knoten ausgeführt, die zum Ausführen einer Aufgabe geplant sind. Dadurch wird die unnötige Ausführung einer Vorbereitungsaufgabe verhindert, wenn ein Knoten einer Aufgabe nicht zugewiesen ist (dies ermöglicht z. B. Einsparungen bei den Gebühren für die Datenübertragung). Diese Situation liegt vor, wenn die Anzahl von Aufgaben für einen Auftrag kleiner ist als die Anzahl von Knoten in einem Pool oder die [gleichzeitige Aufgabenausführung](batch-parallel-node-tasks.md) aktiviert ist. In diesem Fall bleiben einige Knoten im Leerlauf, wenn die Anzahl von Aufgaben geringer ist als die Gesamtanzahl gleichzeitig ausführbarer Aufgaben.

> [AZURE.NOTE]Die [JobPreparationTask][net_job_prep_cloudjob] unterscheidet sich von der [CloudPool.StartTask][pool_starttask]. Die JobPreparationTask wird beim Start jedes Auftrags ausgeführt, während die StartTask nur ausgeführt wird, wenn ein Computeknoten einem Pool erstmals hinzugefügt oder neu gestartet wird.

## Auftragsfreigabeaufgabe

Nach Abschluss eines Auftrags wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Um einen Auftrag als abgeschlossen zu markieren, geben Sie eine Terminate-Anforderung aus. Der Batch-Dienst legt anschließend den Status des Auftrags auf *Abbrechen* fest, beendet alle aktiven bzw. ausgeführten Aufgaben im Zusammenhang mit dem Auftrag und führt die Auftragsfreigabeaufgabe aus. Danach wird der Status des Auftrags in *Abgeschlossen* geändert.

> [AZURE.NOTE]Beim Löschen eines Auftrags wird die Auftragsfreigabeaufgabe ebenfalls ausgeführt. Wurde ein Auftrag zuvor jedoch beendet, wird die Freigabeaufgabe kein zweites Mal ausgeführt, wenn dieser Auftrag anschließend gelöscht wird.

## Auftragsvorbereitungs- und Auftragsfreigabeaufgaben in Batch .NET

Zum Angeben einer Auftragsvorbereitungsaufgabe erstellen und konfigurieren Sie eine [JobPreparationTask][net_job_prep], die Sie der [CloudJob.JobPreparationTask][net_job_prep_cloudjob]-Eigenschaft des Auftrags zuweisen. Auf ähnliche Weise initialisieren Sie eine [JobReleaseTask][net_job_release], die Sie der [CloudJob.JobReleaseTask][net_job_prep_cloudjob]-Eigenschaft zuweisen, um die Freigabeaufgabe des Auftrags festzulegen.

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

Wie oben erwähnt, wird die Freigabeaufgabe ausgeführt, wenn ein Auftrag beendet oder gelöscht wird. Zum Beenden eines Auftrags mit der Batch .NET-API wird [PoolOperations.TerminateJobAsync][net_job_terminate] aufgerufen und der Auftrag mit [PoolOperations.DeleteJobAsync][net_job_delete] gelöscht. Diese beiden Vorgänge werden in der Regel ausgeführt, wenn die Aufgaben eines Auftrags abgeschlossen wurden oder ein von Ihnen definiertes Timeout erreicht wurde.

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
4. Sie führt auf jedem Knoten eine Aufgabe aus, die die Aufgaben-ID in dieselbe Textdatei schreibt.
5. Nach dem Abschluss aller Aufgaben (oder Erreichen des Timeouts) gibt sie den Inhalt der Textdatei jedes Knotens an die Konsole aus.
6. Wenn der Auftrag abgeschlossen ist, führt sie die Auftragsfreigabeaufgabe aus, um die Datei aus dem Knoten zu löschen.
6. Sie gibt die Exitcodes der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben für jeden Knoten aus, auf dem diese ausgeführt wurden.
7. Sie hält die Ausführung an, um auf die Löschbestätigung für den Auftrag und/oder Pool zu warten.

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

Der [Batch-Explorer][batch_explorer_article], der Ihnen ebenfalls im Batch-[Beispielcoderepository][batch_explorer_project] auf GitHub zur Verfügung steht, ist beim Entwickeln von Lösungen mit Azure Batch äußerst hilfreich. Beim Ausführen der obigen Beispielanwendung können Sie mit dem Batch-Explorer z. B. die Eigenschaften des Auftrags und die zugehörigen Aufgaben anzeigen oder sogar die freigegebene Textdatei herunterladen, die durch die Aufgaben des Auftrags geändert wurde.

Der folgende Screenshot zeigt die Eigenschaften der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben, die im Bereich „Auftragsdetails“ angezeigt werden, wenn auf der Registerkarte „Aufträge“ der Auftrag *JobPrepReleaseSampleJob* ausgewählt wird.

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

<!---HONumber=Nov15_HO1-->