<properties
   pageTitle="Batch- und HPC-Lösungen in der Cloud | Microsoft Azure"
   description="Stellt Batch- und HPC-Szenarien (High Performance Computing; auch: Big Compute) sowie Lösungsoptionen in Azure vor."
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/21/2016"
   ms.author="danlep"/>

# Batch- und HPC-Lösungen in der Azure-Cloud

Azure bietet effiziente, skalierbare Lösungen für Batch- und High Performance Computing (HPC), die auch als *Big Compute-Lösungen* bezeichnet werden. Lernen Sie Big Compute-Workloads und die Azure-Dienste zu ihrer Unterstützung kennen, oder springen Sie direkt zu den [Lösungsszenarien](#scenarios) in diesem Artikel. Der Artikel richtet sich in erster Linie an Entscheidungsträger im technischen Bereich sowie an IT-Manager und unabhängige Softwareanbieter, ist aber auch für andere IT-Experten und Entwickler interessant, die sich mit diesen Lösungen vertraut machen möchten.

Organisationen sehen sich mit umfangreichen IT-Problemen konfrontiert – etwa bei der technischen Entwicklung und Analyse, beim Rendern von Bildern, bei komplexen Modellen, bei Monte Carlo-Simulationen und bei der Berechnung finanzieller Risiken. Azure hilft Organisationen bei der Lösung dieser Probleme und beim Treffen von Entscheidungen mit den benötigten Ressourcen im Rahmen ihres Zeitplans. Azure bietet Organisationen folgende Möglichkeiten:

* Erstellen von Hybridlösungen, um einen lokalen HPC-Cluster zu erweitern und Workload-Spitzen in die Cloud auszulagern

* Vollständiges Ausführen von HPC-Clustertools und -Workloads in Azure

* Verwenden eines verwalteten und skalierbaren Azure-Diensts wie etwa [Batch](https://azure.microsoft.com/documentation/services/batch/), um rechenintensive Workloads ohne Bereitstellung und Verwaltung einer Compute-Infrastruktur auszuführen

Wenngleich nicht Gegenstand dieses Artikels, bietet Azure Entwicklern und Partnern auch eine vollständige Palette von Funktionen, Architekturoptionen und Entwicklungstools zum Einrichten umfangreicher, benutzerdefinierter Big Compute-Workflows. Zudem steht ein wachsendes Ökosystem bereit, um in der Azure-Cloud für produktive Big Compute-Workloads zu sorgen.


## Batch- und HPC-Anwendungen

Im Gegensatz zu Webanwendungen und vielen Branchenanwendungen ist für Batch- und HPC-Anwendungen jeweils ein Anfang und Ende definiert, und sie können nach einem Zeitplan oder nach Bedarf für mehrere Stunden (oder noch länger) ausgeführt werden. Die meisten lassen sich einer von zwei Hauptkategorien zuordnen: *intrinsisch parallel* (auch als „hochgradig parallel“ bezeichnet, da die zu lösenden Probleme parallel auf mehreren Computern oder Prozessoren ausgeführt werden) und *eng gekoppelt*. Weitere Informationen zu diesen Anwendungstypen finden Sie in der folgenden Tabelle. Einige Azure-Lösungsansätze eignen sich besonders für einen bestimmten Typ.

>[AZURE.NOTE] Bei Batch- und HPC-Lösungen wird die ausgeführte Instanz einer Anwendung üblicherweise als *Auftrag* bezeichnet. Die einzelnen Aufträge können zudem in *Aufgaben* unterteilt sein. Die Compute-Clusterressourcen für die Anwendung werden häufig als *Compute-Knoten* bezeichnet.

Typ | Merkmale | Beispiele
------------- | ----------- | ---------------
**Intrinsisch parallel**<br/><br/>![Intrinsisch parallel][parallel] |• Einzelcomputer führen Anwendungslogik unabhängig aus.<br/><br/>• Durch Hinzufügen von Computern kann die Anwendung skaliert und die Rechenzeit verkürzt werden.<br/><br/>• Anwendung besteht aus separaten ausführbaren Dateien oder aus einer Gruppe von Diensten, die von einem Client aufgerufen werden (Anwendung in einer serviceorientierten Architektur). |• Risikomodelle im Finanzbereich<br/><br/>• Bildrendering und -verarbeitung<br/><br/>• Codierung und Transcodierung von Medien<br/><br/>• Monte Carlo-Simulationen<br/><br/>• Softwaretests
**Enge Kopplung**<br/><br/>![Eng gekoppelt][coupled] |• Anwendung benötigt Computeknoten für die Interaktion oder den Austausch von Zwischenergebnissen.<br/><br/>• Computeknoten können per MPI (Message Passing Interface; gängiges Kommunikationsprotokoll für paralleles Computing) kommunizieren.<br/><br/>• Die Anwendung berücksichtigt Netzwerklatenz und Bandbreite.<br/><br/>• Die Anwendungsleistung kann durch Technologien für Hochgeschwindigkeitsnetzwerke wie etwa InfiniBand und RDMA (Remote Direct Memory Access) verbessert werden. |• Modelle für Öl- und Gasvorkommen<br/><br/>• Technische Entwicklung und Analyse (beispielsweise Strömungssimulationen)<br/><br/>• Physiksimulationen (beispielsweise für Autounfälle und Kernreaktionen)<br/><br/>• Wettervorhersage

### Überlegungen zur Ausführung von Batch- und HPC-Anwendungen in der Cloud

Sie können problemlos eine Vielzahl von Anwendungen, die für die Ausführung in lokalen HPC-Clustern konzipiert sind, nach Azure oder in eine (standortübergreifende) Hybridumgebung migrieren. Dabei sind jedoch ggf. einige Einschränkungen und Überlegungen zu berücksichtigen:


* **Verfügbarkeit von Cloudressourcen**: Abhängig von der Art Ihrer Cloud Computing-Ressourcen ist ein Computer während der Ausführung eines Auftrags unter Umständen nicht kontinuierlich verfügbar. Zustandsbehandlung und die Erstellung von Fortschrittsprüfpunkten sind gängige Techniken zur Behandlung von Übergangsfehlern und bei der Nutzung von Cloudressourcen umso wichtiger.


* **Datenzugriff**: Gängige Datenzugriffstechniken eines Unternehmensnetzwerkclusters (wie etwa NFS) erfordern eine besondere Konfiguration in der Cloud oder den Einsatz anderer Datenzugriffsverfahren und -muster für die Cloud.

* **Datenverschiebung**: Für Anwendungen, die große Datenmengen verarbeiten, werden Strategien für die Verschiebung der Daten in den Cloudspeicher und an Compute-Ressourcen benötigt. Zudem empfiehlt sich unter Umständen der Einsatz einer standortübergreifenden Hochgeschwindigkeitsnetzwerklösung wie [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Berücksichtigen Sie auch rechtliche, gesetzliche oder richtlinienbedingte Einschränkungen für die Datenspeicherung und den Datenzugriff.


* **Lizenzierung**: Informieren Sie sich bei Verwendung kommerzieller Anwendungen beim jeweiligen Hersteller über lizenzbedingte oder anderweitige Einschränkungen für die Ausführung in der Cloud. Nicht alle Hersteller bieten ein nutzungsbasiertes Lizenzmodell an. Unter Umständen müssen Sie für Ihre Lösung einen Lizenzserver in der Cloud oder eine Verbindung mit einem lokalen Lizenzserver einplanen.


### Big Compute oder Big Data?

Die Abgrenzung zwischen Big Compute- und Big Data-Anwendungen ist nicht immer eindeutig, und einige Anwendungen weisen Merkmale beider Bereiche auf. In beiden Fällen werden umfangreiche Berechnungen ausgeführt – üblicherweise in einem Cluster von Computern. Die Lösungsansätze und die unterstützenden Tools können sich jedoch unterscheiden.

• **Big Compute**: Hier kommen tendenziell Anwendungen zum Einsatz, die viel CPU-Leistung und Arbeitsspeicher benötigen (also beispielsweise technische Simulationen, Risikomodelle im Finanzbereich oder digitales Rendering). Die Cluster, auf denen eine Big Compute-Lösung beruht, können Computer mit spezialisierten Multicore-Prozessoren für die eigentliche Berechnung sowie spezielle Hardware für Hochgeschwindigkeitsnetzwerke enthalten, über die die Computer miteinander verbunden sind.

• **Big Data**: Big Data löst Probleme bei der Datenanalyse mit umfangreichen Datenmengen, die nicht von einem einzelnen Computer oder einem Datenbankmanagementsystem bewältigt werden können. Hierzu zählen beispielsweise große Mengen von Webprotokollen oder andere Business Intelligence-Daten. Bei Big Data kommt es in der Regel weniger auf die CPU-Leistung und mehr auf die Datenträgerkapazität und E/A-Leistung an. Zudem kommen häufig spezialisierte Tools wie Apache Hadoop für die Clusterverwaltung und die Datenpartitionierung zum Einsatz. (Informationen zu Azure HDInsight und anderen Azure-Hadoop-Lösungen finden Sie unter[Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## Computeverwaltung und Auftragsplanung

Bei der Ausführung von Batch- und HPC-Anwendungen werden in der Regel ein *Cluster-Manager* und ein *Auftragsplaner* verwendet, um die Verwaltung von Computeclusterressourcen zu vereinfachen und sie den Anwendungen zuzuordnen, die die Aufträge ausführen. Diese Funktionen können mit separaten Tools oder mit einem integrierten Tool oder Dienst genutzt werden.

* **Cluster-Manager**: Dient zum Bereitstellen, Freigeben und Verwalten von Compute-Ressourcen (oder Compute-Knoten). Ein Cluster-Manager automatisiert unter Umständen die Installation von Betriebssystemimages und Anwendungen auf Computeknoten, skaliert Compute-Ressourcen nach Bedarf und überwacht die Leistung der Knoten.

* **Auftragsplaner**: Dient zum Angeben der Ressourcen (etwa Prozessoren oder Arbeitsspeicher) für eine Anwendung sowie der Ausführungsbedingungen. Ein Auftragsplaner verwaltet eine Warteschlange mit Aufträgen und ordnet Ressourcen auf der Grundlage einer zugewiesenen Priorität oder anderer Merkmale zu.

Clustering- und Auftragsplanungstools für Windows- und Linux-basierte Cluster lassen sich problemlos nach Azure migrieren. Beispielsweise bietet [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), die kostenlose Computeclusterlösung von Microsoft für Windows- und Linux-HPC-Workloads, mehrere Optionen für die Ausführung in Azure. Sie können auch Linux-Cluster zum Ausführen von Open-Source-Tools wie z. B. Torque und SLURM erstellen oder im Handel erhältliche Tools wie [TIBCO DataSynapse GridServer](http://www.tibco.com/products/automation/application-development/grid-computing/gridserver) und [Univa Raster Engine](http://www.univa.com/products/grid-engine) in Azure ausführen.

Wie in den folgenden Abschnitten gezeigt, können Sie auch Azure-Diensten nutzen, um Computeressourcen zu verwalten und Aufträge ohne (oder zusätzlich zu) herkömmlichen Clusterverwaltungstools zu planen.


## Szenarien

Nachstehend finden Sie gängige Szenarien für Big Compute-Workloads in Azure, in denen vorhandene HPC-Clusterlösungen, Azure-Dienste oder eine Kombination aus beidem zur Anwendung kommen. Außerdem finden Sie eine nicht allumfassende Liste mit Aspekten für die Wahl des jeweiligen Szenarios. Weitere Informationen zu den verfügbaren Azure-Diensten, die Sie ggf. in Ihrer Lösung nutzen können, finden Sie weiter unten in diesem Artikel.

 | Szenario | Gründe für die Wahl
------------- | ----------- | ---------------
**Burst eines HPC-Clusters in Azure**<br/><br/>[![Clusterburst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Weitere Informationen:<br/>• [Burst in Azure mit Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Einrichten eines Hybridcomputeclusters mit Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>|• Kombinieren Ihres lokalen [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029)-Clusters mit zusätzlichen Azure-Ressourcen in einer Hybridlösung.<br/><br/>• Erweitern Ihrer Big Compute-Workloads für die Ausführung auf PaaS-VM-Instanzen (Platform-as-a-Service) (derzeit nur Windows Server).<br/><br/>• Zugreifen auf einen lokalen Lizenzserver oder Datenspeicher über ein optionales virtuelles Azure-Netzwerk.|• Sie haben bereits einen HPC Pack-Cluster und benötigen mehr Ressourcen <br/><br/>• Sie möchten keine zusätzliche HPC-Clusterinfrastruktur erwerben und verwalten <br/><br/>• Es gibt vorübergehende Zeiträume mit Bedarfsspitzen oder besondere Projekte.
**Vollständiges Erstellen eines HPC-Clusters in Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Weitere Informationen:<br/>• [HPC-Cluster-Lösungen in Azure](./big-compute-resources.md)<br/><br/>|• Schnelle und konsistente Bereitstellung Ihrer Anwendungen und Clustertools in standardmäßiger oder benutzerdefinierter Windows- oder Linux-Infrastruktur als IaaS-VMs (Infrastructure as a Service)<br/><br/>• Ausführen einer Vielzahl von Big Compute-Workloads mithilfe der Auftragsplanungslösung Ihrer Wahl<br/><br/>• Verwenden zusätzlicher Azure-Dienste, einschließlich Netzwerk- und Speicherdienste, zum Erstellen vollständiger cloudbasierter Lösungen |• Sie möchten keine weitere Linux- oder Windows-HPC-Clusterinfrastruktur erwerben und verwalten.<br/><br/>• Es gibt vorübergehende Zeiträume mit Bedarfsspitzen oder besondere Projekte<br/><br/>• Sie benötigen einen weiteren Cluster für einen bestimmten Zeitraum, möchten aber nicht in Computer und Aufstellfläche für dessen Bereitstellung investieren.<br/><br/>• Sie möchten Ihre rechenintensive Anwendung so auslagern, dass Sie als Dienst komplett in der Cloud ausgeführt wird.
**Horizontales Hochskalieren einer parallelen Anwendung in Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Weitere Informationen:<br/>• [Grundlagen von Azure Batch](./batch-technical-overview.md)<br/><br/>• [Erste Schritte mit der Azure Batch-Bibliothek für .NET](./batch-dotnet-get-started.md)|• Entwickeln mit [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)-APIs zum horizontalen Hochskalieren einer Vielzahl von Big Compute-Workloads für die Ausführung in Pools von PaaS-VMs (Platform as a Service) (derzeit nur Windows Server).<br/><br/>• Verwenden eines Azure-Diensts zum Verwalten der Bereitstellung und automatischen Skalierung von VMs, der Auftragsplanung, der Notfallwiederherstellung, der Datenverschiebung, der Abhängigkeitsverwaltung und Anwendungsbereitstellung, ohne dass ein getrennter HPC-Cluster oder Auftragsplaner erforderlich ist.|• Sie möchten sich weder um Computeressourcen noch um einen Auftragsplaner kümmern. Stattdessen möchten Sie sich auf die effiziente Ausführung Ihrer Anwendung konzentrieren.<br/><br/>• Sie möchten Ihre rechenintensive Anwendung so auslagern, dass Sie als Dienst in der Cloud ausgeführt wird.<br/><br/>• Sie möchten Ihre Computeressourcen entsprechend Ihrem Computeworkload automatisch skalieren.


## Azure-Dienste für Big Compute

Nachstehend finden Sie weitere Informationen zu den Compute-, Daten-, Netzwerk- und zugehörigen Diensten, die Sie für Big Compute-Lösungen und -Workflows kombinieren können. Ausführliche Informationen zu Azure-Diensten finden Sie in der [Dokumentation](https://azure.microsoft.com/documentation/) zu den Azure-Diensten. Die [Szenarien](#scenarios) weiter oben in diesem Artikel veranschaulichen lediglich verschiedene Möglichkeiten der Verwendung dieser Dienste.

>[AZURE.NOTE] Azure führt regelmäßig neue Dienste ein, die für Ihr Szenario hilfreich sein können. Wenden Sie sich bei Fragen an einen [Azure-Partner](https://pinpoint.microsoft.com/de-DE/search?keyword=azure), oder senden Sie eine E-Mail an **bigcompute@microsoft.com*.

### Compute Services

Azure Compute Services sind das Kernstück einer Big Compute-Lösung, und die verschiedenen Dienste bieten Vorteile für verschiedene Szenarien. Diese Dienste bieten grundsätzlich verschiedene Modi für die Ausführung von Anwendungen auf VM-basierten Compute-Instanzen, die Azure auf der Grundlage der Windows Server Hyper-V-Technologie bereitstellt. Von diesen Instanzen können verschiedenste standardmäßige und angepasste Linux- und Windows-Betriebssysteme und -Tools ausgeführt werden. Azure bietet eine Auswahl [verschiedener Instanzgrößen](../virtual-machines/virtual-machines-size-specs.md) mit unterschiedlichen Konfigurationen für Prozessorkerne, Arbeitsspeicher, Festplattenkapazität und andere Merkmale. Bei Bedarf können Sie die Instanzen auf Tausende von Prozessorkernen hoch- und anschließend wieder herunterskalieren, wenn Sie nicht mehr so viele Ressourcen benötigen.

>[AZURE.NOTE] Mit den Instanzen A8 bis A11 können Sie die Leistung einiger HPC-Workloads verbessern. Hierzu zählen auch parallele MPI-Anwendungen, die ein Anwendungsnetzwerk mit geringer Latenz und hohem Durchsatz erfordern. Siehe [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

Dienst | Beschreibung
------------- | -----------
**[Clouddienste](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Ausführung von Big Compute-Anwendungen in Workerrolleninstanzen (virtuelle Computer mit einer Version von Windows Server, die vollständig von Azure verwaltet werden)<br/><br/>• Möglichkeit zur Verwendung skalierbarer, zuverlässiger Anwendungen mit geringem Verwaltungsaufwand im Rahmen eines Platform-as-a-Service (PaaS)-Modells<br/><br/>• Möglicherweise zusätzliche Tools oder Entwicklung für die Integration in lokale HPC-Clusterlösungen erforderlich
**[Virtuelle Maschinen](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Bereitstellung einer Compute-Infrastruktur als Dienst (Infrastructure-as-a-Service, IaaS) unter Verwendung von Microsoft Hyper-V-Technologie<br/><br/>• Möglichkeit zur flexiblen Bereitstellung und Verwaltung persistenter Cloudcomputer auf der Grundlage standardmäßiger Windows Server- oder Linux-Images oder auf der Grundlage von Images und Datenträgern, die von Ihnen selbst oder über den [Azure Marketplace](https://azure.microsoft.com/marketplace/) bereitgestellt werden<br/><br/>• Vollständige Ausführung lokaler Computeclustertools und -anwendungen in der Cloud
**[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Ausführung umfangreicher paralleler und Batchworkloads in einem vollständig verwalteten Dienst<br/><br/>• Möglichkeit zur Auftragsplanung sowie zur automatischen Skalierung eines verwalteten Pools virtueller Computer<br/><br/>• Möglichkeit zur Entwicklung und Ausführung von Anwendungen als Dienst sowie zur Nutzung vorhandener Anwendungen in der Cloud<br/>

### Speicherdienste

Big Compute-Lösungen basieren in der Regel auf einem Satz von Eingabedaten und generieren Daten für ihre Ergebnisse. In Big Compute-Lösungen kommen unter anderem folgende Azure-Speicherdienste zum Einsatz:

* [Blob-, Tabellen- und Warteschlangenspeicher](https://azure.microsoft.com/documentation/services/storage/): Dient zum Verwalten großer Mengen unstrukturierter Daten, NoSQL-Daten und Nachrichten für Workflows bzw. für die Kommunikation. BLOB-Speicher kann beispielsweise für umfangreiche technische Datasets oder für die eingehenden Bilder oder Mediendateien verwenden, die Ihre Anwendung verarbeitet. Warteschlangen können für die asynchrone Kommunikation in einer Lösung verwendet werden. Siehe [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md).

* [Azure Dateispeicher](https://azure.microsoft.com/services/storage/files/): Dient zum Freigeben häufig verwendeter Dateien und Daten in Azure mithilfe des standardmäßigen SMB-Protokolls, das für einige HPC-Clusterlösungen benötigt wird.

### Daten- und Analysedienste

Einige Big Compute-Szenarien beinhalten umfangreiche Datenflüsse oder generieren Daten, die einer weiteren Verarbeitung oder Analyse bedürfen. Hierzu bietet Azure eine Reihe von Daten- und Analysediensten:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/): Dient zum Erstellen datengesteuerter Workflows (Pipelines) für die Verknüpfung, Aggregierung und Transformierung von Daten aus lokalen, cloudbasierten und internetbasierten Datenspeichern.

* [SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/): Stellt die zentralen Features eines auf Microsoft SQL Server basierenden relationalen Datenbankverwaltungssystems in Form eines verwalteten Diensts bereit.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): Stellt Windows Server- oder Linux-basierte Apache Hadoop-Cluster in der Cloud bereit, um eine Verwaltung, Analyse und Berichterstellung für umfangreiche Datenmengen (Big Data) zu ermöglichen.

* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/): Unterstützt Sie beim Erstellen, Testen, Betreiben und Verwalten von Predictive Analytics-Lösungen im Rahmen eines vollständig verwalteten Diensts.

### Zusätzliche Dienste

Ihre Big Compute-Lösung benötigt unter Umständen weitere Azure-Dienste, um eine Verbindung mit lokalen Ressourcen oder mit Ressourcen in anderen Umgebungen herstellen zu können. Beispiele:

* [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/): Erstellt einen logisch isolierten Bereich in Azure, um mittels IPSec eine Verbindung zwischen Azure-Ressourcen und Ihrem lokalen Rechenzentrum oder einem einzelnen Clientcomputer herzustellen, und ermöglicht Big Compute-Anwendungen den Zugriff auf lokale Daten, Active Directory-Dienste und Lizenzserver.

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/): Stellt eine private Verbindung zwischen Microsoft-Rechenzentren und der lokalen oder in einer Kollokationsumgebung befindlichen Infrastruktur her und bietet dabei mehr Sicherheit und Zuverlässigkeit sowie höhere Geschwindigkeiten und eine geringere Latenz als bei normalen Verbindungen über das Internet.

* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/): Stellt verschiedene Mechanismen für die Kommunikation und den Datenaustausch von Anwendungen bereit. Dabei spielt es keine Rolle, ob sich diese in Azure, auf einer anderen Cloudplattform oder in einem Rechenzentrum befinden.

## Nächste Schritte

* Eine technische Anleitung zum Erstellen Ihrer Lösung finden Sie unter [Technische Ressourcen für Batch und HPC](big-compute-resources.md).

* Besprechen Sie Ihre Azure-Optionen mit Partnern einschließlich Cycle Computing und UberCloud.

* Erfahren Sie mehr über Azure Big Compute-Lösungen von [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/) und [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Die neuesten Ankündigungen finden Sie im [Blog des HPC- und Batch-Teams von Microsoft](http://blogs.technet.com/b/windowshpc/) und im [Azure-Blog](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=AcomDC_0218_2016-->