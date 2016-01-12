<properties
	pageTitle="Grundlagen des Azure Batch-Diensts | Microsoft Azure"
	description="Hier erhalten Sie Informationen über den Azure Batch-Dienst für umfangreiche parallele Workloads und HPC-Workloads."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/19/2015"
	ms.author="danlep"/>

# Grundlagen von Azure Batch

Azure Batch unterstützt Sie beim effizienten Ausführen von größeren parallelen und leistungsstarken Anwendungen (High Performance Computing, HPC) in der Cloud. Es handelt sich um einen Plattformdienst zur Planung rechenintensiver Aufgaben für die Ausführung auf einer verwalteten Sammlung von virtuellen Computern (Computeknoten), die zur Erfüllung der Anforderungen des Auftrags skaliert werden können. Mit dem Batch-Dienst können Sie Azure-Computeressourcen und umfangreiche Batchaufträge programmgesteuert definieren, die bedarfsgesteuert oder zeitplanabhängig ausgeführt werden. Es ist deshalb nicht erforderlich, einen HPC-Cluster, einzelne VMs, virtuelle Netzwerke oder einen Auftragsplaner zu konfigurieren und zu verwalten.

## Anwendungsfälle

Batch ist ein verwalteter Dienst für die *Batchverarbeitung* bzw. das *Batchcomputing* – Es werden große Mengen an ähnlichen Aufgaben ausgeführt, um das gewünschte Ergebnis zu erzielen. Batchcomputing ist ein gängiges Muster für Unternehmen, die große Datenmengen verarbeiten, transformieren und analysieren – entweder nach einem Zeitplan oder nach Bedarf. Beispielunternehmen sind Finanzdienstleister und Maschinenbauer.

Batch funktioniert sehr gut bei intrinsisch parallelen (mitunter als „hochgradig parallel“ bezeichneten) Anwendungen oder Workloads, mit denen parallele Aufgaben auf mehreren Computern ausgeführt werden. Siehe Abbildung 1.

![Parallele Aufgaben][parallel]

**Abbildung 1: Parallele Aufgaben, die auf mehreren Computern ausgeführt werden**

Beispiele:

* Risikomodellierung im Finanzbereich
* Bildrendering und-verarbeitung
* Mediencodierung und-transcodierung
* Analyse genetischer Sequenzen
* Testen von Software

Batch kann auch parallele Berechnungen mit einem abschließenden Reduzierungsschritt sowie komplexere HPC-Workloads ausführen, z. B. MPI-Anwendungen (Message Passing Interface).

>[AZURE.NOTE]Derzeit unterstützt Batch nur Workloads, die auf Windows Server-basierten virtuellen Computern ausgeführt werden.

Ein Vergleich von Batch mit anderen HPC-Lösungsoptionen in Azure finden Sie unter [Batch und HPC-Lösungen](batch-hpc-solutions.md).

## Entwickeln mit Batch

Entwickeln Sie mit den Batch-APIs, um verwaltete Pools von Computeknoten zu erstellen und zu verwalten sowie um die Aufträge und Aufgaben zu planen, die auf diesen ausgeführt werden. Schreiben Sie Clientanwendungen oder Front-Ends, um Aufträge und Aufgaben nach Bedarf, nach einem Zeitplan oder als Teil eines größeren von Tools wie [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) verwalteten Workflows auszuführen.

In der [Übersicht über Azure Batch-Features](batch-api-basics.md) finden Sie weitere Informationen zu den Batch-Konzepten.

### Benötigte Konten

+ **Azure-Konto und -Abonnement** Wenn Sie kein Konto haben, können Sie Ihre [MSDN-Abonnentenleistungen](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) in Anspruch nehmen oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.

+ **Batch-Konto**: Sie verwenden den Namen und die URL des Batch-Kontos und einen Zugriffsschlüssel als Anmeldeinformationen, wenn Sie Batch-APIs aufrufen. Alle Ihre Batch-Ressourcen, wie z. B. Computeknoten, Pools, Aufträge und Aufgaben, sind einem Batch-Konto zugeordnet. Im [Azure-Portal](batch-account-create-portal.md) können Sie ein Batch-Konto erstellen und Zugriffsschlüssel für das Konto verwalten.

+ **Speicherkonto**: Für die meisten Batch-Szenarien benötigen Sie ein Azure-Speicherkonto zum Speichern Ihrer Datenein- und -ausgaben sowie der Skripts oder ausführbaren Dateien, die auf den Computeknoten ausgeführt werden. Informationen zum Erstellen eines Speicherkontos finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md).

### Batch-Entwicklungsbibliotheken und -Tools

Verwenden Sie diese .NET-Bibliotheken und -Tools mit Visual Studio, um Azure Batch-Anwendungen zu entwickeln und zu verwalten.

+ [.NET-Bibliothek für Batch-Clients](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – Dient zum Entwickeln von Clientanwendungen zum Ausführen von Aufträgen mit dem Batch-Dienst.
+ [.NET-Bibliothek für die Batch-Verwaltung](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – Dient zum Verwalten von Batch-Konten.
+ [Batch-Explorer](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) – Erstellen Sie diese GUI-Anwendung zum Durchsuchen von, Zugreifen auf und Aktualisieren von Ressourcen in einem Batch-Konto, einschließlich Aufträgen und Aufgaben, Computeknoten und Pools sowie Dateien auf einem Computeknoten. Weitere Informationen finden Sie in diesem [Blogbeitrag](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Szenario: Horizontales Hochskalieren eines parallelen Workloads

Ein gängiges Szenario mit den Batch-APIs ist das horizontale Hochskalieren einer intrinsisch parallelen Aufgabe wie dem Bildrendering in einem Pool von Computeknoten, die Tausende von Computekernen bereitstellen.

Abbildung 2 zeigt einen Workflow, der die Batch .NET-Clientanwendung verwendet, um einen parallelen Workload mit dem Batch-Dienst auszuführen.


![Workflow für Arbeitsaufgaben][work_item_workflow]

**Abbildung 2: Horizontales Hochskalieren eines parallelen Workloads auf Batch**

1.	Hochladen von Eingabedateien (z. B. Quelldaten oder Bilder), die für einen Auftrag zu einem Azure-Speicherkonto erforderlich sind. Der Batch-Dienst lädt Dateien auf Computeknoten, wenn die Aufgaben im Auftrag ausgeführt werden.

2.	Laden Sie die Programmdateien oder Skripts, die auf den Computeknoten ausgeführt werden sollen, in das Speicherkonto hoch. Dazu können Binärdateien mit ihren abhängigen Assemblys gehören. Der Batch-Dienst lädt diese Dateien auch auf die Computeknoten hoch, wenn die Aufgaben ausgeführt werden.

3.	Erstellen Sie programmgesteuert einen Pool von Batch-Computeknoten in Ihrem Batch-Konto, und geben Sie Eigenschaften an, wie z. B. ihre Größe oder das ausgeführte Betriebssystem. Sie können auch bestimmen, wie die Anzahl der Knoten im Pool dem Workload entsprechend [nach oben oder unten skaliert](batch-automatic-scaling.md) wird. Wenn eine Aufgabe ausgeführt wird, wird ihr ein Knoten aus diesem Pool zugewiesen.

4.	Definieren Sie programmgesteuert einen Batch-Auftrag aus, um den Workload im Knotenpool auszuführen.

5.	Hinzufügen von Aufgaben zum Auftrag. Jede Aufgabe verwendet das Programm, das Sie hochgeladen haben, um Informationen aus einer hochgeladenen Datei zu verarbeiten. Je nach Workload möchten Sie ggf. [mehrere Aufgaben gleichzeitig](batch-parallel-node-tasks.md) auf jedem Computeknoten zum Maximieren der Ressourcenauslastung ausführen. Batch unterstützt auch spezielle [Auftragsvorbereitungs- und -ausführungsaufgaben](batch-job-prep-release.md) zum Vorbereiten der Computeknoten vor der Ausführung geplanter Aufgaben und zur Bereinigung im Anschluss.

6.	Führen Sie den Batch-Workload aus, und überwachen Sie Fortschritt und Ergebnisse. Die Anwendung kommuniziert über HTTPS mit dem Batch-Dienst. Um die Leistung der Anwendung zu verbessern, wenn eine große Anzahl von Pools, Aufträgen und Aufgaben überwacht wird, nutzen Sie [effiziente Verfahren zur Abfrage](batch-efficient-list-queries.md) des Batch-Diensts.






## Nächste Schritte

* Erste Schritte beim Entwickeln Ihrer ersten Anwendung mit der [.NET-Bibliothek für Batch-Clients](batch-dotnet-get-started.md)
* Durchsuchen der Batch-Codebeispiele auf [GitHub](https://github.com/Azure/azure-batch-samples)

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=AcomDC_0107_2016-->