<properties
	pageTitle="Effiziente Listenabfragen in Azure Batch | Microsoft Azure"
	description="Steigern der Leistung durch Reduzieren der zurückgegebenen Datenmenge beim Abfragen von Azure Batch-Entitäten wie Pools, Aufträgen, Aufgaben und Computeknoten."
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
	ms.date="10/12/2015"
	ms.author="v-marsma"/>

# Effizientes Abfragen des Azure Batch-Diensts

In diesem Artikel erfahren Sie, wie Sie die Anzahl der Elemente und die Datenmenge reduzieren, die beim Verwenden der [ Batch .NET][api_net]-API zum Abfragen des Azure Batch-Diensts für Listen von Aufträgen, Aufgaben, Computeknoten usw. zurückgegeben wird.

Azure Batch bietet „Big Compute“-Fähigkeiten – und in einer Produktionsumgebung kann die Anzahl von Entitäten wie Aufträge, Tasks und Computeknoten leicht in die Tausende gehen. Das Abrufen von Informationen zu diesen Elementen kann daher große Datenmengen generieren, die bei jeder Abfrage übertragen werden müssen. Indem Sie die Anzahl der Elemente und den Typ der zurückgegebenen Informationen einschränken, können Sie die Geschwindigkeit Ihrer Abfragen und damit die Leistung der Anwendung steigern.

Das Auflisten von Aufträgen, Aufgaben und Computeknoten sind Beispiele für Vorgänge, die in nahezu jeder Anwendung, die Azure Batch verwendet, ausgeführt werden müssen, und mitunter recht häufig. Die Überwachung ist ein gängiger Anwendungsfall. Beispielsweise müssen bei der Ermittlung der Kapazität und des Status eines Pools alle Computeknoten in einem Pool abgefragt werden. Ein weiteres Beispiel ist die Abfrage der Aufgaben für einen Auftrag, um zu ermitteln, ob sich noch Aufgaben in der Warteschlange befinden.

Dieser [Batch .NET][api_net]-API-Codeausschnitt dient zum Abrufen aller Aufgaben, die einem Auftrag zugeordnet sind, sowie der kompletten Palette der Eigenschaften dieser Aufgaben:

```
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks = batchClient.JobOperations.ListTasks("job-001");
```

Es kann jedoch eine deutlich effizientere Listenabfrage ausgeführt werden. Dazu übergeben Sie der [JobOperations.ListTasks][net_list_tasks]-Methode ein [ODATADetailLevel][odata]-Objekt. Dieser Ausschnitt gibt nur die ID-, Befehlszeilen- und Computeknoteneigenschaften ohne abgeschlossene Aufgaben zurück:

```
// Configure an ODATADetailLevel specifying a subset of tasks and their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks = batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Wenn der Auftrag, wie im obigen Beispielszenario, Tausende von Aufgaben umfasst, werden die Ergebnisse der zweiten Abfrage meist viel schneller als die der ersten zurückgegeben. Weitere Informationen zum Verwenden von „ODATADetailLevel“ beim Auflisten von Elementen mit der Batch .NET-API finden Sie weiter unten in diesem Dokument.

> [AZURE.IMPORTANT]Es wird dringend empfohlen, dass Sie *immer* ein ODATADetailLevel-Objekt für Ihre .NET API-Listenaufrufe verwenden, um eine maximale Effizienz und optimale Leistung der Anwendung zu gewährleisten. Das Angeben einer Detailebene hilft dem Batch-Dienst beim Verkürzen von Antwortzeiten, Verbessern der Netzwerkauslastung und Minimieren der Speicherbelegung von Clientanwendungen.

## Tools für effiziente Abfragen

Die [Batch .NET][api_net]- und [Batch REST][api_rest]-APIs bieten die Möglichkeit, sowohl die Anzahl der Elemente, die in einer Liste zurückgegeben werden, als auch die Menge von Informationen für jedes Element zu reduzieren. Dazu geben Sie beim Ausführen von Listenabfragen mit den APIs Zeichenfolgen zum *Filtern* (Filter), *Auswählen* (Select) und *Erweitern* (Expand) an.

- **Filter**: Die *Filterzeichenfolge* ist ein Ausdruck, der die Anzahl der zurückgegebenen Elemente reduziert. Sie können beispielsweise die derzeit ausgeführten Aufgaben für einen Auftrag oder nur Computeknoten auflisten, die zum Ausführen von Aufgaben bereit sind.
  - Eine Filterzeichenfolge besteht aus mindestens einem Ausdruck, wobei ein Ausdruck aus einem Eigenschaftsnamen, einem Operator und einem Wert besteht. Die Eigenschaften, die angegeben werden können, sind spezifisch für jeden API-Aufruftyp. Dies gilt auch für die für jede Eigenschaft unterstützten Operatoren.
  - Mehrere Ausdrücke können mithilfe der logischen Operatoren `and` und `or` kombiniert werden.
  - Ein Beispiel für eine Filterzeichenfolge, die nur aktuell ausgeführte Renderaufgaben auflistet: `startswith(id, 'renderTask') and (state eq 'running')`
- **Auswählen**: Die *Auswählzeichenfolge* begrenzt die Eigenschaftswerte, die für jedes Element zurückgegeben werden. In der Auswählzeichenfolge kann eine Liste der Eigenschaften für ein Element angegeben werden. Dann werden in den Listenabfrageergebnissen für jedes Element nur diese Eigenschaftswerte zurückgegeben.
  - Die Auswählzeichenfolge besteht aus einer durch Trennzeichen getrennte Liste von Eigenschaftsnamen. Es können beliebige Eigenschaften eines Elements angegeben werden, die von einem Listenvorgang zurückgegeben werden.
  - Ein Beispiel für eine Auswählzeichenfolge, die nur drei Eigenschaften angibt, die für jede Aufgabe zurückgegeben werden sollen: `id, state, stateTransitionTime`
- **Erweitern**: Die *Erweiterungszeichenfolge* verringert die Anzahl der API-Aufrufe, die zum Abrufen bestimmter Informationen erforderlich sind. Ausführlichere Informationen zu jedem Listenelement können mit einem einzelnen API-Aufruf für eine Liste abgerufen werden, anstatt die Liste abzurufen und dann für jedes Element in der Liste einen Aufruf durchzuführen.
  - Ähnlich wie die Auswählzeichenfolge steuert die Erweiterungszeichenfolge, ob bestimmte Daten in die Listenabfrageergebnisse einbezogen werden.
  - Die Erweiterungszeichenfolge wird nur beim Auflisten von Aufträgen, Auftragszeitplänen, Aufgaben und Pools unterstützt. Derzeit bietet sie nur Unterstützung für Statistikinformationen.
  - Ein Beispiel für eine Erweiterungszeichenfolge, die angibt, dass Statistikinformationen für jedes Element zurückgegeben werden sollen: `stats`
  - Wenn alle Eigenschaften erforderlich sind und keine Auswählzeichenfolge angegeben wurde, *muss* die Erweiterungszeichenfolge zum Abrufen von Statistikinformationen verwendet werden. Wenn eine Auswählzeichenfolge zum Abrufen einer Teilmenge der Eigenschaften genutzt wird, kann `stats` in der Auswählzeichenfolge angegeben werden, und die Erweiterungszeichenfolge muss nicht angegeben werden.

> [AZURE.NOTE]Beim Erstellen dieser drei Typen von Abfragezeichenfolgen (Filtern, Auswählen, Erweitern) müssen Sie sicherstellen, dass die Namen und die Groß-/Kleinschreibung der Eigenschaften mit den entsprechenden REST-API-Elementen übereinstimmen. Wenn Sie beispielsweise mit der [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask)-Klasse aus .NET arbeiten, müssen Sie **state** anstelle von **State** angeben, obwohl die .NET-Eigenschaft [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state) heißt. In den folgenden Tabellen finden Sie Eigenschaftszuordnungen zwischen der .NET- und der REST-API.

### Spezifikationen für Filter-, Auswähl- und Erweiterungszeichenfolgen

- Eigenschaften, die in Filter-, Auswähl- und Erweiterungszeichenfolgen angegeben werden, entsprechen den Eigenschaftsnamen in der [Batch REST][api_rest]-API. Dies gilt auch, wenn Sie die [Batch .NET][api_net]-Bibliothek verwenden.
- Bei allen Eigenschaftsnamen muss die Groß- und Kleinschreibung beachtet werden, bei Eigenschaftswerten dagegen nicht.
- Datum/Uhrzeit-Zeichenfolgen können eines von zwei Formaten aufweisen, und ihnen muss `DateTime` vorangestellt sein.
  - Beispiel für das W3C-DTF-Formats: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Beispiel für das RFC 1123-Formats: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Boolesche Zeichenfolgen sind entweder `true` oder `false`.
- Bei Angabe einer ungültigen Eigenschaft oder eines ungültigen Operators wird der Fehler `400 (Bad Request)` angezeigt.

## Effizientes Abfragen in Batch .NET

Innerhalb der [Batch .NET][api_net]-API wird die [ODATADetailLevel][odata]-Klasse zum Angeben von Filter-, Auswähl- und Erweiterungszeichenfolgen für Listenvorgänge verwendet. Ein ODataDetailLevel-Objekt verfügt über drei öffentliche Zeichenfolgeneigenschaften, die im Konstruktor angegeben oder direkt festgelegt werden können. Dieses Objekt wird dann als Parameter an die verschiedenen Listenvorgänge übergeben, z. B. an [ListPools][net_list_pools], [ListJobs][net_list_jobs] und [ListTasks][net_list_tasks].

- [ODATADetailLevel.FilterClause][odata_filter]\: Dient zum Begrenzen der Anzahl der zurückgegebenen Elemente.
- [ODATADetailLevel.SelectClause][odata_select]\: Dient zum Angeben einer Teilmenge von Eigenschaftswerten, die mit jedem Element zurückgegeben werden.
- [ODATADetailLevel.ExpandClause][odata_expand]\: Dient zum Abrufen von Elementdaten in einem einzelnen API-Aufruf im Gegensatz zu Aufrufen für jedes einzelne Element.

Im folgenden Codeausschnitt wird die Batch .NET-API verwendet, um den Batch-Dienst effizient auf die Statistik zu einer bestimmten Gruppe von Pools abzufragen. In diesem Szenario verfügt der Batch-Benutzer über Test- und Produktionspools. Den Testpool-IDs ist das Präfix „test“ vorangestellt, und den Produktionspool-IDs ist das Präfix „prod“ vorangestellt. Im Codeausschnitt ist *myBatchClient* eine ordnungsgemäß initialisierte Instanz von [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// We want to pull only the "test" pools, so we limit the number of items returned by using a
	// FilterClause and specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties that are returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// Now get our collection of pools, minimizing the amount of data that is returned by specifying the
	// detail level that we configured above
	List<CloudPool> testPools = await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();

> [AZURE.TIP]Eine mit „Select“- und „Expand“-Klauseln konfigurierte Instanz von [ODATADetailLevel][odata] kann auch an geeignete „Get“-Methoden, z. B. [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), übergeben werden, um die Menge der zurückgegebenen Daten einzuschränken.

## Zuordnungen zwischen der Batch REST-API und .NET-API

Eigenschaftsnamen in Filter-, Auswähl- und Erweiterungszeichenfolgen *müssen* ihren Gegenstücken in der REST-API hinsichtlich Name und Groß-/Kleinschreibung entsprechen. Die folgenden Tabellen enthalten die Zuordnungen zwischen den .NET-APIs und ihren REST-API-Entsprechungen.

### Zuordnungen für Filterzeichenfolgen

- **.NET-Listenmethoden**: Jede der .NET API-Methoden in dieser Spalte akzeptiert ein [ODATADetailLevel][odata]-Objekt als Parameter.
- **REST-Listenanforderungen**: Jeder der in dieser Spalte enthaltenen Links zu einer REST-API-Seite führt zu einer Tabelle mit den Eigenschaften und Vorgängen, die in *Filterzeichenfolgen* zulässig sind. Sie verwenden diese Eigenschaftsnamen und Vorgänge beim Erstellen einer [ODATADetailLevel.FilterClause][odata_filter]-Zeichenfolge.

| .NET-Listenmethoden | REST-Listenanforderungen |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Auflisten der Zertifikate in einem Konto][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Auflisten der einer Aufgabe zugeordneten Dateien][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Auflisten des Status der Aufgaben zur Auftragsvorbereitung und -freigabe für einen Auftrag][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Auflisten der Aufträge in einem Konto][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Auflisten der Dateien auf einem Knoten][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Auflisten der einem Auftrag zugeordneten Aufgaben][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Auflisten der Auftragszeitpläne in einem Konto][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Auflisten der einem Auftragszeitplan zugeordneten Aufträge][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Auflisten der Computeknoten in einem Pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Auflisten der Pools in einem Konto][rest_list_pools]

### Zuordnungen für Auswählzeichenfolgen

- **Bach .NET-Typen**: Batch .NET-API-Typen.
- **REST-API-Entitäten**: Jede Seite in dieser Spalte enthält eine oder mehrere Tabellen mit den Namen der REST-API-Eigenschaften für den Typ. Diese Eigenschaftsnamen werden beim Erstellen von *Auswählzeichenfolgen* verwendet. Sie verwenden diese Eigenschaftsnamen, wenn Sie eine [ODATADetailLevel.SelectClause][odata_select]-Zeichenfolge erstellen.

| Batch .NET-Typen | REST-API-Entitäten |
|---|---|
| [Certificate][net_cert] | [Abrufen von Informationen zu einem Zertifikat][rest_get_cert] |
| [CloudJob][net_job] | [Abrufen von Informationen zu einem Auftrag][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Abrufen von Informationen zu einem Auftragszeitplan][rest_get_schedule] |
| [ComputeNode][net_node] | [Abrufen von Informationen zu einem Knoten][rest_get_node] |
| [CloudPool][net_pool] | [Abrufen von Informationen zu einem Pool][rest_get_pool] |
| [CloudTask][net_task] | [Abrufen von Informationen zu einer Aufgabe][rest_get_task] |

### Beispiel: Erstellen einer Filterzeichenfolge

Wenn Sie eine Filterzeichenfolge für eine [ODATADetailLevel.FilterClause][odata_filter] erstellen, konsultieren Sie die obige Tabelle unter „Zuordnungen für Filterzeichenfolgen“, um die REST-API-Dokumentationsseite zu finden, die dem durchzuführenden Auflistungsvorgang entspricht. Sie finden die filterbaren Eigenschaften und die jeweils unterstützten Operatoren in der ersten mehrzeiligen Tabelle auf der entsprechenden Seite. Wenn Sie z. B. alle Aufgaben abrufen möchten, deren Exitcode ungleich 0 war, gibt diese Zeile in [Auflisten der einem Auftrag zugeordneten Aufgaben][rest_list_tasks] die entsprechende Eigenschaftszeichenfolge und zulässigen Operatoren an:

| Eigenschaft | Zulässige Vorgänge | Typ |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Daher lautet die Filterzeichenfolge zum Auflisten aller Aufgaben mit einem Exitcode ungleich 0 wie folgt:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

### Beispiel: Erstellen einer Auswählzeichenfolge

Konsultieren Sie zum Erstellen einer [ODATADetailLevel.SelectClause][odata_select] die Tabelle oben unter „Zuordnungen für Auswählzeichenfolgen“, und navigieren Sie zur REST-API-Seite, die dem Typ der aufzulistenden Entität entspricht. Sie finden die auswählbaren Eigenschaften und die jeweils unterstützten Operatoren in der ersten mehrzeiligen Tabelle auf der entsprechenden Seite. Wenn Sie z. B. nur die ID und Befehlszeile für jede Aufgabe in einer Liste abrufen möchten, finden Sie diese Zeilen in der entsprechenden Tabelle unter [Abrufen von Informationen zu einer Aufgabe][rest_get_task]\:

| Eigenschaft | Typ | Hinweise |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

Die Auswählzeichenfolge zum Einbeziehen von ausschließlich der ID und Befehlszeile für jede aufgeführte Aufgabe lautet dann wie folgt:

`id, commandLine`

## Nächste Schritte

Probieren Sie das Beispielprojekt [EfficientListQueries][efficient_query_sample] auf GitHub aus, um zu sehen, wie sich effiziente Listenabfragen in einer Anwendung auf die Leistung auswirken können. Diese C#-Konsolenanwendung erstellt eine große Anzahl von Aufgaben und fügt sie einem Auftrag hinzu. Anschließend führt sie mehrere Aufrufe für die [JobOperations.ListTasks][net_list_tasks]-Methode aus und übergibt [ODATADetailLevel][odata]-Objekte, die mit verschiedenen Eigenschaftswerten konfiguriert sind, um unterschiedliche Mengen von Rückgabedaten zu erhalten. Sie erzeugt eine Ausgabe ähnlich der Folgenden:

		Adding 5000 tasks to job jobEffQuery...
		5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

		4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
		0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
		59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
		5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
		5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
		5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

		Sample complete, hit ENTER to continue...

Wie den Informationen über die jeweils verstrichene Zeit zu entnehmen ist, können Sie durch eine Begrenzung der Eigenschaften und der Anzahl von zurückgegebenen Elementen die Antwortzeiten für Abfragen erheblich verkürzen. Sie finden dieses Beispielprojekt und weitere Beispielprojekte im [azure-batch-samples][github_samples]-Repository auf GitHub.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

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

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

<!---HONumber=AcomDC_0121_2016-->