<properties
	pageTitle="Grundlagen des Azure Batch-Diensts | Microsoft Azure"
	description="Hier erhalten Sie Informationen über den Azure Batch-Dienst für umfangreiche parallele Workloads und HPC-Workloads."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/16/2016"
	ms.author="marsma"/>

# Grundlagen von Azure Batch

Azure Batch ermöglicht Ihnen die effiziente Ausführung von größeren parallelen und leistungsstarken Anwendungen (High Performance Computing, HPC) in der Cloud. Es handelt sich um einen Plattformdienst zur Planung rechenintensiver Aufgaben für die Ausführung auf einer verwalteten Sammlung von virtuellen Maschinen, und die Computeressourcen können automatisch skaliert werden, um die Anforderungen Ihrer Aufträge zu erfüllen.

Mit dem Batch-Dienst definieren Sie Azure-Computeressourcen auf programmgesteuerte Weise, um Batchaufträge größeren Umfangs auszuführen. Sie können diese Aufträge bedarfsgesteuert oder nach einem Zeitplan ausführen. Es ist nicht erforderlich, einen HPC-Cluster, einzelne virtuelle Maschinen, virtuelle Netzwerke oder einen Auftragsplaner manuell zu konfigurieren und zu verwalten.

## Anwendungsfälle für Batch

Batch ist ein verwalteter Azure-Dienst, der für die *Batchverarbeitung* bzw. das *Batchcomputing* verwendet wird. Es werden große Mengen an ähnlichen Aufgaben ausgeführt, um das gewünschte Ergebnis zu erzielen. Batchcomputing wird am häufigsten von Unternehmen eingesetzt, die regelmäßig große Mengen an Daten verarbeiten, transformieren und analysieren.

Batch funktioniert gut mit intrinsisch parallelen Anwendungen und Workloads (auch als „hochgradig parallel“ bezeichnet). Intrinsisch parallele Workloads können leicht in mehrere Aufgaben unterteilt werden, mit denen die Arbeitsschritte gleichzeitig auf mehreren Computern ausgeführt werden.

![Parallele Aufgaben][1]<br/>

Beispiele für Workloads, die mit diesem Verfahren häufig verarbeitet werden:

* Risikomodellierung im Finanzbereich
* Datenanalysen für die Bereiche Klima und Hydrologie
* Bildrendering, -analyse und -verarbeitung
* Mediencodierung und-transcodierung
* Analyse genetischer Sequenzen
* Belastungsanalysen für den Maschinenbau
* Testen von Software

Batch kann auch parallele Berechnungen mit einem abschließenden Reduzierungsschritt sowie komplexere HPC-Workloads ausführen, z. B. MPI-Anwendungen (Message Passing Interface).

Ein Vergleich zwischen Batch und anderen HPC-Lösungsoptionen in Azure finden Sie unter [Batch und HPC-Lösungen](batch-hpc-solutions.md).

>[AZURE.NOTE] Derzeit unterstützt Batch nur Workloads, die auf Windows Server-basierten virtuellen Maschinen ausgeführt werden.

## Entwickeln mit Batch

Beim Erstellen von Lösungen, bei denen Azure Batch für die Verarbeitung paralleler Workloads verwendet wird, gehen Sie unter Einsatz der Batch-APIs programmgesteuert vor. Mit den Batch-APIs erstellen und verwalten Sie Pools mit Computeknoten (virtuellen Maschinen) und planen Aufträge und Aufgaben, die auf diesen Knoten ausgeführt werden. Für eine Clientanwendung oder einen Dienst, die bzw. den Sie erstellen, werden die Batch-APIs zum Kommunizieren mit dem Batch-Dienst verwendet. Sie können größere Workloads für Ihr Unternehmen auf effiziente Weise verarbeiten oder ein Dienst-Front-End für Ihre Kunden bereitstellen, damit sie Aufträge und Aufgaben ausführen können – bedarfsgesteuert oder nach Zeitplan auf einem, Hunderten oder Tausenden von Knoten. Sie können Batch auch als Teil eines größeren Workflows nutzen, der mit Tools verwaltet wird, z. B. [Azure Data Factory][data_factory].

> [AZURE.TIP] Wenn Sie ein besseres Verständnis der Funktionen der Batch-API entwickeln möchten, können Sie sich die [Übersicht über Azure Batch-Features](batch-api-basics.md) ansehen.

### Benötigte Azure-Konten

Beim Entwickeln von Batch-Lösungen verwenden Sie in Microsoft Azure die folgenden Konten:

- **Azure-Konto und -Abonnement:** Wenn Sie noch kein Azure-Konto haben, können Sie Ihre [MSDN-Abonnentenleistungen][msdn_benefits] in Anspruch nehmen oder sich für eine [kostenlose Testversion][free_trial] registrieren. Beim Erstellen eines Kontos wird für Sie ein Standardabonnement erstellt.

- **Batch-Konto:** Wenn Ihre Anwendungen mit dem Batch-Dienst interagieren, werden der Kontoname, die URL des Kontos und ein Zugriffsschlüssel als Anmeldeinformationen verwendet. Ihre Batch-Ressourcen, z. B. Pools, Computeknoten, Aufträge und Aufgaben, sind einem Batch-Konto zugeordnet. Sie können [ein Batch-Konto im Azure-Portal erstellen und verwalten](batch-account-create-portal.md).

- **Speicherkonto:** Batch enthält integrierte Unterstützung zum Verwenden von Dateien in [Azure Storage][azure_storage]. Für nahezu jedes Batch-Szenario wird Azure Storage für das Staging von Dateien verwendet: für die Programme, die von Ihren Aufgaben ausgeführt werden, und für die dabei verarbeiteten Daten – sowie für die Speicherung der Ausgabedaten, die von Ihren Aufgaben generiert werden. Informationen zum Erstellen eines Speicherkontos finden Sie unter [Informationen zu Azure-Speicherkonten](./../storage/storage-create-storage-account.md).

### Batch-Entwicklungsbibliotheken und -Tools

Zum Erstellen von Lösungen mit Azure Batch können Sie die Batch .NET-Clientbibliotheken oder PowerShell verwenden, oder Sie können auch direkte REST-API-Aufrufe ausgeben. Nutzen Sie diese Tools, um Clientanwendungen und Dienste zu entwickeln, die Aufträge in Batch ausführen.

- [Batch .NET][api_net]-Clientbibliothek: Die meisten Batch-Lösungen werden mit der Batch .NET-Clientbibliothek erstellt, die [unter NuGet verfügbar][api_net_nuget] ist.

- [Batch Management .NET][api_net_mgmt]-Clientbibliothek: Ist auch [unter NuGet verfügbar][api_net_mgmt_nuget]. Verwenden Sie die Batch Management .NET-Clientbibliothek, um Batch-Konten in Ihren Clientanwendungen oder Diensten programmgesteuert zu verwalten.

- [Batch REST][batch_rest]-API: Die Batch REST-APIs bieten die gleiche Funktionalität wie die Batch .NET-Clientbibliothek. Die Batch .NET-Bibliothek selbst nutzt die Batch REST-API im Hintergrund für die Interaktion mit dem Batch-Dienst.

- [Batch PowerShell-Cmdlets:][batch_ps] Mit den Azure Batch-Cmdlets im [Azure PowerShell](./../powershell-install-configure.md)-Modul können Sie Batch-Ressourcen mit PowerShell verwalten.

- [Azure Batch-Explorer:][batch_explorer] Der Batch-Explorer ist eine der Batch .NET-Beispielanwendungen, die [bei GitHub verfügbar][github_samples] sind. Erstellen Sie diese Windows Presentation Foundation-Anwendung (WPF) mit Visual Studio 2013 oder 2015, und verwenden Sie sie zum Durchsuchen und Verwalten der Ressourcen in Ihrem Batch-Konto, während Sie Ihre Batch-Lösungen entwickeln und debuggen. Sehen Sie sich Details zu Aufträgen, Pools und Aufgaben an, laden Sie Dateien von Computeknoten herunter, oder stellen Sie eine Remoteverbindung mit Remotedesktopdateien (RDP) her, die Sie nach wenigen Klicks auf der Batch-Explorer-Oberfläche erhalten.

- [Microsoft Azure-Speicher-Explorer:][storage_explorer] Dies ist zwar kein Azure Batch-Tool, aber der Speicher-Explorer ist ein weiteres wertvolles Tool, das Sie beim Entwickeln und Debuggen Ihrer Batch-Lösungen einsetzen können.

## Szenario: Horizontales Hochskalieren eines parallelen Workloads

Eine gängige Lösung, bei der die Batch-APIs zum Interagieren mit dem Batch-Dienst verwendet werden, umfasst das horizontale Hochskalieren von intrinsisch parallelen Arbeitsschritten – z. B. das Rendern von Bildern für 3D-Szenen – für einen Pool mit Computeknoten. Dieser Pool mit Computeknoten kann als Ihre „Renderfarm“ dienen, mit der für Ihren Renderauftrag beispielsweise Dutzende, Hunderte oder sogar Tausende von Kernen bereitgestellt werden können.

Im folgenden Diagramm ist ein gängiger Batch-Workflow dargestellt, bei dem eine Clientanwendung oder ein gehosteter Dienst Batch zum Ausführen einer parallelen Workload verwendet.

![Batch-Lösungsworkflow][2]

Bei diesem häufigen Szenario verarbeitet Ihre Anwendung bzw. Ihr Dienst eine Workloadberechnung in Azure Batch, indem die folgenden Schritte ausgeführt werden:

1. Hochladen der **Eingabedateien** und der **Anwendung**, mit der diese Dateien verarbeitet werden, in Ihr Azure Storage-Konto. Die Eingabedateien können alle Daten sein, die von Ihrer Anwendung verarbeitet werden, z. B. Daten für Finanzmodelle oder zu transcodierende Videodateien. Bei den Anwendungsdateien kann es sich um alle Anwendungen handeln, die zum Verarbeiten der Daten verwendet werden, z. B. eine 3D-Renderanwendung oder einen Medien-Transcoder.

2. Erstellen Sie einen Batch-**Pool** mit Computeknoten in Ihrem Batch-Konto. Dies sind die virtuellen Maschinen, mit denen Ihre Aufgaben ausgeführt werden. Sie geben Eigenschaften, z. B. die [Knotengröße](./../cloud-services/cloud-services-sizes-specs.md), das Betriebssystem und den Speicherort in Azure Storage für die Anwendung an, die installiert werden soll, wenn die Knoten dem Pool beitreten (die in Schritt 1 hochgeladene Anwendung). Sie können für den Pool auch die [automatische Skalierung](batch-automatic-scaling.md) konfigurieren – indem Sie die Anzahl von Computeknoten im Pool dynamisch anpassen –, um auf die Workload zu reagieren, die von Ihren Aufgaben generiert wird.

3. Erstellen Sie einen Batch-**Auftrag**, um die Workload im Pool mit den Computeknoten auszuführen. Wenn Sie einen Auftrag erstellen, ordnen Sie ihn einem Batch-Pool zu.

4. Fügen Sie dem Auftrag **Aufgaben** hinzu. Wenn Sie einem Auftrag Aufgaben hinzufügen, plant der Batch-Dienst die Aufgaben automatisch für die Ausführung auf den Computeknoten im Pool ein. Jede Aufgabe verwendet die Anwendung, die Sie hochgeladen haben, zum Verarbeiten der Eingabedateien.

    - 4a. Bevor eine Aufgabe ausgeführt wird, können die zu verarbeitenden Daten (die Eingabedateien) auf den zugewiesenen Computeknoten heruntergeladen werden. Falls die Anwendung nicht bereits auf dem Knoten installiert wurde (siehe Schritt 2), kann sie stattdessen hier heruntergeladen werden. Nach Abschluss der Downloads werden die Aufgaben auf ihren zugewiesenen Knoten ausgeführt.

5. Während der Ausführung der Aufgaben können Sie Batch abfragen, um den Fortschritt des Auftrags und seiner Aufgaben zu überwachen. Die Clientanwendung bzw. der Dienst kommuniziert mit dem Batch-Dienst per HTTPS. Da Sie unter Umständen Tausende von Aufgaben auf Tausenden von Computeknoten überwachen müssen, sollten Sie für ein [effizientes Abfragen des Batch-Diensts](batch-efficient-list-queries.md) sorgen.

6. Nach Abschluss der Aufgaben können die Ergebnisdaten in Azure Storage hochgeladen werden. Sie können Dateien auch direkt von Computeknoten abrufen.

7. Wenn bei der Überwachung erkannt wird, dass die Aufgaben Ihres Auftrags abgeschlossen wurden, kann Ihre Clientanwendung bzw. der Dienst die Ausgabedaten zur weiteren Verarbeitung oder Auswertung herunterladen.

Bedenken Sie, dass dies nur eine Möglichkeit zur Verwendung von Batch ist und dass in diesem Szenario nur einige der verfügbaren Features beschrieben werden. Beispielsweise können Sie auf jedem Computeknoten [mehrere Aufgaben parallel](batch-parallel-node-tasks.md) ausführen, und Sie können die [Vorbereitung und den Abschluss von Aufträgen](batch-job-prep-release.md) nutzen, um die Knoten für Ihre Aufträge vorzubereiten. Anschließend können Sie die Bereinigung durchführen.

## Nächste Schritte

Nachdem Sie nun ein Beispiel für ein Batch-Szenario gesehen haben, sollten Sie tiefer in den Dienst einsteigen und sich darüber informieren, wie Sie ihn zum Verarbeiten Ihrer rechenintensiven parallelen Workloads verwenden können.

- Lesen Sie [Erste Schritte mit der Azure Batch-Bibliothek für .NET](batch-dotnet-get-started.md), um zu erfahren, wie Sie C# und die Batch .NET-Bibliothek verwenden, um die oben beschriebenen Verfahren durchzuführen. Dies sollte beim Erlernen des Batch-Diensts einer Ihrer ersten Anlaufpunkte sein.

- Sehen Sie sich die [Übersicht über Azure Batch-Features](batch-api-basics.md) an, um eingehendere Informationen zu den API-Features zu erhalten, die von Batch für die Verarbeitung Ihrer rechenintensiven Workloads bereitgestellt werden.

- Zusätzlich zum Batch-Explorer verdeutlichen die anderen [Codebeispiele bei GitHub][github_samples] die Nutzung vieler Batch-Features mithilfe der Batch .NET-Bibliothek.

- Sehen Sie sich den [Batch-Lernpfad][learning_path] an, um sich über die Ressourcen zu informieren, die zum Erlernen des Umgangs mit Batch verfügbar sind.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_trial]: https://azure.microsoft.com/pricing/free-trial/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0218_2016-->