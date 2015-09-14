<properties
	pageTitle="Effiziente Listenabfragen in Azure Batch | Microsoft Azure"
	description="Erfahren Sie, wie Sie bei der Abfrage von Pools, Aufträgen, Aufgaben, Computeknoten und mehr in Azure Batch die Menge der zurückgegebenen Daten reduzieren und die Leistung steigern können."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="Batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/27/2015"
	ms.author="davidmu;v-marsma"/>

# Effiziente Listenabfragen mit Batch

Azure Batch ist eine "Big Compute"-Lösung – und in einer Produktionsumgebung kann die Anzahl von Entitäten wie Aufträge, Tasks und Computeknoten leicht in die Tausende gehen. Das Abrufen von Informationen zu diesen Elementen kann daher große Datenmengen generieren, die bei jeder Abfrage übertragen werden müssen. Indem Sie die Anzahl der Elemente und den Typ der zurückgegebenen Informationen einschränken, können Sie die Geschwindigkeit Ihrer Abfragen und damit die Leistung der Anwendung steigern.

Die folgenden [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)-API-Methoden sind Beispiele für Vorgänge, die in nahezu jeder Anwendung, die Azure Batch verwendet, ausgeführt werden müssen – und häufig sogar regelmäßig:

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx)
- [ListComputeNodes](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx)

Überwachung ist ein häufiger Anwendungsfall. Bei der Ermittlung der Kapazität und des Status eines Pools müssen beispielsweise alle Computeknoten (VMs) in einem Pool abgefragt werden. Ein weiteres Beispiel ist die Abfrage der Aufgaben für einen Auftrag, um zu ermitteln, ob sich noch Aufgaben in der Warteschlange befinden. In einigen Fällen sind umfangreiche Daten erforderlich, in anderen Fällen wiederum ist nur eine Zählung der Gesamtanzahl aller Elemente oder eine Sammlung von Elementen mit einem bestimmten Status erforderlich.

Dabei ist zu beachten, dass sowohl die Anzahl der zurückgegebenen Elemente als auch die Menge der Daten, die zum Anzeigen der Elemente erforderlich ist, sehr groß sein kann. Die Abfrage von vielen Elementen mit umfangreichen Antworten kann zu verschiedenen Problemen führen:

- Die Antwortzeiten der Batch-API können zu langsam werden. Je größer die Anzahl der Elemente, desto länger ist die vom Batch-Dienst benötigte Antwortzeit. Bei einer großen Anzahl von Elementen müssen diese in Blöcke aufgeteilt werden. Daher muss die Clientbibliothek möglicherweise mehrere API-Aufrufe an den Dienst ausführen, um alle Elemente für eine Liste abzurufen.
- Je mehr Elemente zu verarbeiten sind, desto länger dauert die API-Verarbeitung durch die Anwendung, die Batch aufruft.
- Je mehr Elemente vorhanden sind und/oder je größer die Elemente werden, desto mehr Speicher wird durch die Anwendung verbraucht, die Batch aufruft.
- Mehr Elemente und/oder größere Elemente führen zu einem erhöhten Netzwerkdatenverkehr. Die Übertragung dauert länger, und je nach Architektur der Anwendung erhöhen sich möglicherweise die Kosten für Daten, die außerhalb der Region des Batch-Kontos übertragen werden.

Für alle Batch-APIs gelten folgende Bedingungen:

- Jeder Eigenschaftenname ist eine Zeichenfolge, die der Eigenschaft des Objekts zugeordnet ist.
- Bei allen Eigenschaftennamen muss die Groß- und Kleinschreibung beachtet werden, bei Eigenschaftswerten dagegen nicht.
- Eigenschaftennamen und deren Groß- und Kleinschreibung entsprechen der Darstellung der Elemente in der Batch-REST-API.
- Datum/Uhrzeit-Zeichenfolgen können in einem der beiden folgenden Formate angegeben werden, und ihnen muss "DateTime" vorangestellt sein.
	- W3CDTF (z. B. *creationTime gt DateTime'2011-05-08T08:49:37Z'*)
	- RFC1123 (z. B. *creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'*)
- Boolesche Zeichenfolgen sind "true" oder "false".
- Bei Angabe einer ungültigen Eigenschaft oder eines ungültigen Operators wird der Fehler "400 (Bad Request)" ausgegeben.

## Effizientes Abfragen in Batch .NET

Mithilfe der Batch-.NET-API können Sie sowohl die Anzahl der in einer Liste zurückgegebenen Elemente als auch die Menge der Informationen reduzieren, die für jedes Element zurückgegeben wird, indem Sie [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) für eine Abfrage festlegen. Bei "DetailLevel" handelt es sich um eine abstrakte Basisklasse, sodass ein [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx)-Objekt erstellt und als Parameter an die entsprechenden Methoden übergeben werden muss.

Ein ODataDetailLevel-Objekt verfügt über drei öffentliche Zeichenfolgeneigenschaften, die entweder im Konstruktor angegeben oder direkt festgelegt werden können:

- [FilterClause](#filter): Filtern und eventuell Reduzieren der Anzahl der zurückgegebenen Elemente
- [SelectClause](#select): Angeben einer Teilmenge von zurückzugebenden Eigenschaftswerten für jedes Element, sodass die Element- und Antwortgröße reduziert wird
- [ExpandClause](#expand): Zurückgeben aller erforderlichen Daten in einem Aufruf anstatt in mehreren Aufrufen

> [AZURE.TIP]Eine mit Select- und Expand-Klauseln konfigurierte Instanz von "DetailLevel" kann auch an geeignete Get-Methoden, z. B. [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), übergeben werden, um die Menge der zurückgegebenen Daten einzuschränken.

### <a id="filter"></a> FilterClause

Die Anzahl der zurückgegebenen Elemente kann durch eine Filterzeichenfolge reduziert werden. Sie können einen oder mehrere Eigenschaftswerte mit Qualifizierern angeben, um sicherzustellen, dass nur die für Ihre Abfrage relevanten Elemente zurückgegeben werden. Beispielsweise möchten Sie vielleicht nur die derzeit ausgeführten Aufgaben für einen Auftrag oder nur die Computeknoten auflisten, die zum Ausführen von Aufgaben bereit sind.

 [FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) ist eine Zeichenfolge, die aus einem oder mehreren Ausdrücken besteht, wobei ein Ausdruck aus einem *Eigenschaftennamen*, einem *Operator* und einem *Wert* besteht. Die Eigenschaften, die angegeben werden können, sind spezifisch für jeden API-Aufruf. Dies gilt auch für die für jede Eigenschaft unterstützten Operatoren. Mehrere Ausdrücke können mithilfe der logischen Operatoren **and** und **or** kombiniert werden.

Diese Filterzeichenfolge gibt beispielsweise nur derzeit ausgeführte Tasks zurück, deren *displayName* mit "MyTask" beginnt:

	startswith(displayName, 'MyTask') and (state eq 'Running')

Jeder der folgenden Artikel zur Batch-REST-API enthält eine Tabelle, in der die unterstützten Eigenschaften und Vorgänge für diese Eigenschaften für die verschiedenen Auflistungsvorgänge angegeben werden.

- [Auflisten der Pools in einem Konto](https://msdn.microsoft.com/library/azure/dn820101.aspx)
- [List the compute nodes in a pool](https://msdn.microsoft.com/library/azure/dn820159.aspx) (Auflisten der Computeknoten in einem Pool, in englischer Sprache)
- [List the jobs in an account](https://msdn.microsoft.com/library/azure/dn820117.aspx) (Auflisten der Aufträge in einem Konto, in englischer Sprache)
- [List the status of the job preparation and job release tasks for a job](https://msdn.microsoft.com/library/azure/mt282170.aspx) (Auflisten des Status der Aufgaben zur Auftragsvorbereitung und -freigabe für einen Auftrag, in englischer Sprache)
- [List the job schedules in an account](https://msdn.microsoft.com/library/azure/mt282174.aspx) (Auflisten der Auftragszeitpläne in einem Konto, in englischer Sprache)
- [List the jobs associated with a job schedule](https://msdn.microsoft.com/library/azure/mt282169.aspx) (Auflisten der einem Auftragszeitplan zugeordneten Aufträge, in englischer Sprache)
- [List the tasks associated with a job](https://msdn.microsoft.com/library/azure/dn820187.aspx) (Auflisten der einem Auftrag zugeordneten Aufgaben, in englischer Sprache)
- [List the files associated with a task](https://msdn.microsoft.com/library/azure/dn820142.aspx) (Auflisten der einer Aufgabe zugeordneten Dateien, in englischer Sprache)
- [Auflisten der Zertifikate in einem Konto](https://msdn.microsoft.com/library/azure/dn820154.aspx)
- [List the files on a node](https://msdn.microsoft.com/library/azure/dn820151.aspx) (Auflisten der Dateien auf einem Knoten, in englischer Sprache)

> [AZURE.IMPORTANT]Stellen Sie beim Angeben von Eigenschaften in jedem der drei Klauseltypen sicher, dass Eigenschaftenname und Groß- und Kleinschreibung mit dem entsprechenden Batch-REST-API-Element übereinstimmen. Wenn Sie beispielsweise mit [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) in .NET arbeiten, müssen Sie **state** anstelle von **State** angeben, obwohl die .NET-Eigenschaft [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state) lautet. Den richtigen Namen und die richtige Groß- und Kleinschreibung können Sie z. B. für die **state**-Eigenschaft ermitteln, indem Sie den Elementnamen unter [Get information about a task](https://msdn.microsoft.com/library/azure/dn820133.aspx) (Informationen zu einer Aufgabe abrufen, in englischer Sprache) in der Dokumentation zur Batch-REST-API überprüfen.

### <a id="select"></a> SelectClause

Die Eigenschaftswerte, die für jedes Element zurückgegeben werden, können mithilfe einer select-Zeichenfolge begrenzt werden. Für ein Element kann eine Liste mit Eigenschaften angegeben werden. Dann werden nur die entsprechenden Eigenschaftswerte zurückgegeben.

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) ist eine Zeichenfolge mit einer durch Trennzeichen getrennten Liste mit Eigenschaftennamen. Es kann eine beliebige Kombination der Eigenschaften angegeben werden, die für ein von einem Auflistungsvorgang zurückgegebenes Element verfügbar sind.

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

Die Anzahl der API-Aufrufe kann durch eine expand-Klausel reduziert werden. Ausführlichere Informationen zu jedem Listenelement können mit einem einzigen API-Aufruf abgerufen werden, anstatt die Liste abzurufen und dann für jedes Element in der Liste einzeln einen Aufruf durchzuführen.

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) ähnelt der select-Klausel, da mit ihr gesteuert wird, ob bestimmte Daten in den Ergebnissen zurückgegeben werden. Die expand-Klausel wird nur für die Auftragsliste, die Taskliste und die Poolliste unterstützt. Zum gegenwärtigen Zeitpunkt werden nur statistische Informationen unterstützt. Wenn alle Eigenschaften erforderlich sind und keine select-Klausel angegeben wurde, müssen statistische Informationen mit der expand-Klausel abgerufen werden. Wenn mit einer select-Klausel eine Teilmenge von Eigenschaften abgerufen wird, können statistische Informationen ebenfalls in der select-Klausel angegeben und die expand-Klausel auf Null gesetzt werden.

## Beispiel für eine effiziente Abfrage

Im Folgenden finden Sie einen Codeausschnitt, der die Batch-.NET-API verwendet, um eine effiziente Abfrage der statistischen Informationen für eine bestimmte Gruppe von Pools an den Batch-Dienst zu richten. In diesem Szenario verfügt der Batch-Benutzer sowohl über Test- als auch über Produktionspools, wobei den IDs der Testpools "test" und den IDs der Produktionspools "prod" vorangestellt ist. Im Codeausschnitt ist *myBatchClient* eine ordnungsgemäß initialisierte Instanz von [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient).

	// First we need an ODATADetailLevel instance on which to set the expand, filter, and select
	// clause strings
	ODATADetailLevel detailLevel = new ODATADetailLevel();

	// Specify the ExpandClause so that the .NET API pulls the statistics for the CloudPools in a single
	// underlying REST API call. Note that we use the pool's REST API element name "stats" here as opposed
	// to "Statistics" as it appears in the .NET API (CloudPool.Statistics)
	detailLevel.ExpandClause = "stats";

	// We want to pull only the "test" pools, so we limit the items returned by using a Filterclause and
	// specifying that the pool IDs must start with "test"
	detailLevel.FilterClause = "startswith(id, 'test')";

	// To further limit the data that crosses the wire, configure the SelectClause to limit the
	// properties returned on each CloudPool object to only CloudPool.Id and CloudPool.Statistics
	detailLevel.SelectClause = "id, stats";

	// Now get our collection of pools, minimizing the amount of data returned by specifying the
	// detail level we configured above
	List<CloudPool> testPools = myBatchClient.PoolOperations.ListPools(detailLevel).ToList();

> [AZURE.TIP]Es wird empfohlen, dass Sie *immer* filter- und select-Klauseln für Ihre API-Aufrufe zur Listenerstellung verwenden, um die maximale Effizienz und optimale Leistung der Anwendung zu gewährleisten.

## Nächste Schritte

1. Wenn Sie dies nicht bereits getan haben, sollten Sie unbedingt die Batch-API-Dokumentation für Ihr Entwicklungsszenario lesen.
    - [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) (in englischer Sprache)
    - [Batch .NET](https://msdn.microsoft.com/library/azure/dn865466.aspx)
2. Sehen Sie sich die [Azure Batch-Beispiele](https://github.com/Azure/azure-batch-samples) auf GitHub an, und machen Sie sich mit dem Code vertraut.

<!---HONumber=September15_HO1-->