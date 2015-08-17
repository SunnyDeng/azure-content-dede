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
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/01/2015"
   ms.author="danlep"/>


# Batch- und HPC-Lösungen

In diesem Artikel werden Azure-Lösungen für Batch-Computing und High Performance Computing (HPC) vorgestellt. Diese Lösungen werden auch als *Big Compute-Lösungen* bezeichnet. Der Artikel richtet sich zwar in erster Linie an Entscheidungsträger im technischen Bereich sowie an IT-Manager und unabhängige Softwareanbieter, ist aber auch für andere IT-Experten und Entwickler interessant, die sich mit diesen Lösungen vertraut machen möchten.

Organisationen sehen sich mit umfangreichen IT-Problemen konfrontiert – etwa bei der technischen Entwicklung und Analyse, beim Rendern von Bildern, bei komplexen Modellen, bei Monte Carlo-Simulationen und bei der Berechnung finanzieller Risiken. Azure bietet skalierbare und bedarfsgerechte Compute-Funktionen und -Dienste, um Organisationen dabei zu unterstützen, diese Probleme zu bewältigen und Entscheidungen mit den passenden Ressourcen, der passenden Skalierung und dem passenden Zeitplan zu treffen. Azure bietet Organisationen folgende Möglichkeiten:

* Erstellen von Hybridlösungen, um einen lokalen HPC-Cluster zu erweitern und Workload-Spitzen in die Cloud auszulagern

* Vollständiges Ausführen von HPC-Clusterworkloads in Azure mithilfe vorhandener Toolsets und Anwendungen

* Verwenden eines verwalteten und skalierbaren Azure-Diensts wie etwa [Batch](http://azure.microsoft.com/documentation/services/batch/), um rechenintensive Workloads ohne Bereitstellung und Verwaltung einer Compute-Infrastruktur auszuführen

Darüber hinaus bietet Azure Entwicklungstools und -dienste, mit denen Organisationen und Softwarehersteller maßgeschneiderte und umfassende Big Compute-Lösungen erstellen können.


## Hintergrund: Batch- und HPC-Anwendungen

Im Gegensatz zu Webanwendungen und vielen Branchenanwendungen ist für Batch- und HPC-Anwendungen jeweils ein Anfang und Ende definiert, und sie können nach einem Zeitplan oder nach Bedarf für mehrere Stunden (oder noch länger) ausgeführt werden. Die meisten lassen sich einer von zwei Hauptkategorien zuordnen: *intrinsisch parallel* (auch als „hochgradig parallel“ bezeichnet, da die zu lösenden Probleme parallel auf mehreren Computern oder Prozessoren ausgeführt werden) und *eng gekoppelt*. Weitere Informationen zu diesen Anwendungstypen finden Sie in der folgenden Tabelle. Einige Azure-Lösungsansätze eignen sich besonders für einen bestimmten Typ.

>[AZURE.NOTE]Bei Batch- und HPC-Lösungen wird die ausgeführte Instanz einer Anwendung üblicherweise als *Auftrag* bezeichnet. Die einzelnen Aufträge können zudem in *Aufgaben* unterteilt sein. Die Compute-Clusterressourcen für die Anwendung werden häufig als *Compute-Knoten* bezeichnet.

Typ | Merkmale | Beispiele
------------- | ----------- | ---------------
**Intrinsisch parallel**<br/>
<br/>
![Intrinsisch parallel][parallel] |• Einzelcomputer führen Anwendungslogik unabhängig aus.<br/>
<br/>
• Durch Hinzufügen von Computern kann die Anwendung skaliert und die Rechenzeit verkürzt werden.<br/>
<br/>
• Anwendung besteht aus separaten ausführbaren Dateien oder aus einer Gruppe von Diensten, die von einem Client aufgerufen werden (Anwendung in einer serviceorientierten Architektur). |• Risikomodelle im Finanzbereich<br/>
<br/>
• Bildrendering und -verarbeitung<br/>
<br/>
• Codierung und Transcodierung von Medien<br/>
<br/>
• Monte Carlo-Simulationen<br/>
<br/>
• Softwaretests
**Enge Kopplung**<br/>
<br/>
![Eng gekoppelt][coupled] |• Anwendung benötigt Compute-Knoten für die Interaktion oder den Austausch von Zwischenergebnissen.<br/>
<br/>
• Compute-Knoten können per MPI (Message Passing Interface; gängiges Kommunikationsprotokoll für paralleles Computing) kommunizieren.<br/>
<br/>
• Die Anwendung berücksichtigt Netzwerklatenz und Bandbreite.<br/>
<br/>
• Die Anwendungsleistung kann durch die Verwendung einer Compute-Infrastruktur verbessert werden, die Technologien für Hochgeschwindigkeitsnetzwerke wie etwa InfiniBand und RDMA (Remote Direct Memory Access) unterstützt. |• Modelle für Öl- und Gasvorkommen<br/>
<br/>
• Technische Entwicklung und Analyse (beispielsweise Strömungssimulationen)<br/>
<br/>
• Physiksimulationen (beispielsweise für Autounfälle und Kernreaktionen)<br/>
<br/>
• Wettervorhersage

### Überlegungen zur Ausführung von Batch- und HPC-Anwendungen in der Cloud

Sie können problemlos eine Vielzahl von Anwendungen, die für die Ausführung in lokalen HPC-Clustern konzipiert sind, nach Azure oder in eine (standortübergreifende) Hybridumgebung migrieren. Dabei sind jedoch ggf. einige Einschränkungen und Überlegungen zu berücksichtigen:


* **Ununterbrochene Verfügbarkeit von Cloudressourcen**: Abhängig von der Art der für die Lösung ausgewählten Cloud Computing-Ressourcen ist ein Computer während der Ausführung eines Auftrags unter Umständen nicht kontinuierlich verfügbar. Zustandsbehandlung und die Erstellung von Fortschrittsprüfpunkten sind gängige Techniken zur Behandlung von Übergangsfehlern und bei der Nutzung von Cloudressourcen umso wichtiger.


* **Datenzugriff**: Gängige Datenzugriffstechniken eines Unternehmensnetzwerkclusters (wie etwa NFS) erfordern eine besondere Konfiguration in der Cloud oder den Einsatz anderer Datenzugriffsverfahren und -muster für die Cloud.

* **Datenverschiebung**: Für Anwendungen, die große Datenmengen verarbeiten, werden Strategien für die Verschiebung der Daten in den Cloudspeicher und an Compute-Ressourcen benötigt. Zudem empfiehlt sich unter Umständen der Einsatz einer standortübergreifenden Hochgeschwindigkeitsnetzwerklösung wie [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/). Berücksichtigen Sie auch rechtliche, gesetzliche oder richtlinienbedingte Einschränkungen für die Datenspeicherung und den Datenzugriff.


* **Lizenzierung**: Informieren Sie sich bei Verwendung kommerzieller Anwendungen beim jeweiligen Hersteller über lizenzbedingte oder anderweitige Einschränkungen für die Ausführung in der Cloud. Nicht alle Hersteller bieten ein nutzungsbasiertes Lizenzmodell an. Unter Umständen müssen Sie für Ihre Lösung einen Lizenzserver in der Cloud oder eine Verbindung mit einem lokalen Lizenzserver einplanen.


### Big Compute oder Big Data?

Die Abgrenzung zwischen Big Compute- und Big Data-Anwendungen ist nicht immer eindeutig, und einige Anwendungen weisen Merkmale beider Bereiche auf. In beiden Fällen werden umfangreiche Berechnungen ausgeführt – üblicherweise in einem Cluster mit Computern, die lokal, in der Cloud oder in einer Hybridumgebung ausgeführt werden. Die Lösungsansätze und die unterstützenden Tools können sich jedoch unterscheiden.

• **Big Compute**: Hier kommen tendenziell Anwendungen zum Einsatz, die viel CPU-Leistung und Arbeitsspeicher benötigen (also beispielsweise technische Simulationen, Risikomodelle im Finanzbereich oder digitales Rendering). Die Cluster, auf denen eine Big Compute-Lösung beruht, können Computer mit spezialisierten Multicore-Prozessoren für die eigentliche Berechnung sowie spezielle Hardware für Hochgeschwindigkeitsnetzwerke enthalten, über die die Computer miteinander verbunden sind.

• **Big Data**: Big Data löst Probleme bei der Datenanalyse mit umfangreichen Datenmengen, die nicht von einem einzelnen Computer oder einem Datenbankmanagementsystem bewältigt werden können. Hierzu zählen beispielsweise große Mengen von Webprotokollen oder andere Business Intelligence-Daten. Bei Big Data kommt es in der Regel weniger auf die CPU-Leistung und mehr auf die Datenträgerkapazität und auf die E/A-Leistung an. Zudem kommen bei Big Data-Lösungen häufig spezialisierte Tools wie Apache Hadoop für die Clusterverwaltung und die Datenpartitionierung zum Einsatz. (Informationen zu Azure HDInsight und anderen Azure-Hadoop-Lösungen finden Sie unter[Hadoop](http://azure.microsoft.com/solutions/hadoop/).)

## Ressourcenverwaltung und Auftragsplanung

Bei Verwendung von Batch- und HPC-Anwendungen werden in der Regel ein *Cluster-Manager* und ein *Auftragsplaner* verwendet, um die Verwaltung von Compute-Clusterressourcen zu vereinfachen und sie den Anwendungen zuzuordnen, die die Aufträge ausführen. Diese Funktionen können mit separaten Tools oder mit einem integrierten Tool genutzt werden.

* **Cluster-Manager**: Dient zum Bereitstellen, Freigeben und Verwalten von Compute-Ressourcen (oder Compute-Knoten). Abhängig vom verwendeten Tool automatisiert ein Cluster-Manager unter Umständen die Installation von Betriebssystemimages und Anwendungen auf Compute-Knoten, skaliert Compute-Ressourcen nach Bedarf und überwacht die Leistung der Knoten.

* **Auftragsplaner**: Dient zum Angeben der Ressourcen (etwa Prozessoren oder Arbeitsspeicher) für eine Anwendung sowie der Ausführungsbedingungen. Ein Auftragsplaner verwaltet eine Warteschlange mit Aufträgen und ordnet Ressourcen auf der Grundlage einer zugewiesenen Priorität oder anderer Merkmale zu.

Clustering- und Auftragsplanungstools für Windows- und Linux-basierte (oder unabhängig entwickelte) Cluster lassen sich problemlos nach Azure migrieren. Bei [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) handelt es sich beispielsweise um die kostenlose Computeclusterlösung von Microsoft für Windows Server- und Windows-basierte Computer. Zur Verringerung des Bedarfs an dedizierten lokalen Serverressourcen können Sie einen HPC Pack-Cluster bei Bedarf mit Azure Compute-Knoten erweitern oder einen Cluster vollständig auf virtuellen Azure-Computern bereitstellen.

### Big Compute-Workflows

Clusterverwaltungs- und Auftragsplanungstools ermöglichen die schrittweise Einrichtung von Compute-Workflows. Je nach verwendeter Lösung können Sie unter Umständen einige Schritte in der folgenden Liste weglassen oder zusätzliche, benutzerdefinierte Tools hinzufügen. Bei Workflows mit umfangreichen Daten sind möglicherweise zusätzliche Schritte für die Vor- und Nachbearbeitung der Daten erforderlich. Diese Schritte sind in der Liste nicht enthalten.

1. **Bereitstellung**: Vorbereitung der Compute-Umgebung mit der erforderlichen Infrastruktur, den erforderlichen Compute-Ressourcen und dem erforderlichen Speicher für die Anwendungsausführung

2. **Staging**: Einbringung von Eingabedaten und Anwendungen in die Compute-Umgebung

3. **Zeitplanung**: Konfiguration von Aufträge und Aufgaben und Zuordnung von Compute-Ressourcen, wenn Ressourcen verfügbar sind

4. **Überwachung**: Bereitstellung von Statusinformationen für Aufträge und Aufgaben und Behandlung von Fehlern und Ausnahmen

5. **Fertigstellung**: Rückgabe von Ergebnissen und ggf. Nachbereitung der Ausgabedaten

## Azure-Dienste für Big Compute

Azure bietet für Big Compute-Lösungen und -Workflows eine Reihe von Compute-, Daten- und Netzwerkdiensten und ähnliche Dienste. Ausführliche Informationen zu den einzelnen Diensten finden Sie in der Dokumentation zu den Azure-Diensten. Gängige Ansätze für Batch- und HPC-Lösungen werden in diesem Artikel unter [Lösungsszenarien](#solution-scenarios) erläutert.

>[AZURE.NOTE]Die Azure-Plattform wird regelmäßig um neue Dienste erweitert, die für Ihr Szenario hilfreich sein können. Die Verwendung der Vorschauversionen von Diensten wird nur für Tests oder Machbarkeitsstudien, nicht aber für Produktionsworkloads empfohlen. Wenden Sie sich bei Fragen an einen [Azure-Partner](https://pinpoint.microsoft.com/en-US/search?keyword=azure), oder senden Sie eine E-Mail an \**bigcompute@microsoft.com*.

### Compute Services

Die Compute Services in Azure bilden das Herzstück einer Big Compute-Lösung. Die folgende Tabelle enthält häufig verwendete Compute Services mit Vorteilen für verschiedene Szenarien. Diese Dienste bieten grundsätzlich verschiedene Modi für die Ausführung von Anwendungen auf VM-basierten Compute-Instanzen, die Azure auf der Grundlage der Windows Server Hyper-V-Technologie bereitstellt. Je nach Dienst können von diesen Instanzen verschiedenste standardmäßige und angepasste Linux- und Windows-Betriebssysteme und -Tools ausgeführt werden. Azure ermöglicht die Verwendung [verschiedener Instanzgrößen](../virtual-machines/virtual-machines-sizes-specs.md) mit unterschiedlichen Konfigurationen für Prozessorkerne, Arbeitsspeicher, Festplattenkapazität und andere Merkmale. Bei Bedarf können Sie die Instanzen auf Tausende von Prozessorkernen hoch- und anschließend wieder herunterskalieren, wenn Sie nicht mehr so viele Ressourcen benötigen.

>[AZURE.NOTE]Mit den Instanzen A8 bis A11 können Sie die Leistung einiger rechenintensiver Workloads verbessern. Hierzu zählen auch parallele MPI-Anwendungen, die ein Anwendungsnetzwerk mit geringer Latenz und hohem Durchsatz erfordern. Siehe [Informationen zu den rechenintensiven A8-, A9-, A10- und A11-Instanzen](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

Dienst | Beschreibung
------------- | -----------
**[Clouddienste](http://azure.microsoft.com/documentation/services/cloud-services)**<br/>
<br/>
 |• Ausführung von Big Compute-Anwendungen in Workerrolleninstanzen (virtuelle Computer mit einer Version von Windows Server, die vollständig von Azure verwaltet werden)<br/>
<br/>
• Möglichkeit zur Verwendung skalierbarer, zuverlässiger Anwendungen mit geringem Verwaltungsaufwand im Rahmen eines Platform-as-a-Service (PaaS)-Modells<br/>
<br/>
• Möglicherweise zusätzliche Tools oder Entwicklung für die Integration in lokale HPC-Clusterlösungen erforderlich
**[Virtuelle Maschinen](http://azure.microsoft.com/documentation/services/virtual-machines)**<br/>
<br/>
 |• Bereitstellung einer Compute-Infrastruktur als Dienst (Infrastructure-as-a-Service, IaaS) unter Verwendung von Microsoft Hyper-V-Technologie<br/>
<br/>
• Möglichkeit zur flexiblen Bereitstellung und Verwaltung persistenter Cloudcomputer auf der Grundlage standardmäßiger Windows Server- oder Linux-Images oder auf der Grundlage von Images und Datenträgern, die von Ihnen selbst oder über den [Azure Marketplace](https://azure.microsoft.com/marketplace/) bereitgestellt werden<br/>
<br/>
• Vollständige Ausführung lokaler Computeclustertools und -anwendungen in der Cloud
**[Batch](http://azure.microsoft.com/documentation/services/batch)**<br/>
<br/>
 |• Ausführung umfangreicher Parallel- und Batch-Workloads wie Bildrendering und Codierung/Transcodierung von Medien in einem vollständig verwalteten Dienst<br/>
<br/>
• Möglichkeit zur Auftragsplanung sowie zur automatischen Skalierung eines verwalteten Pools virtueller Computer<br/>
<br/>
• Möglichkeit zur Entwicklung und Ausführung von Anwendungen als Dienst sowie zur Nutzung vorhandener Anwendungen in der Cloud<br/>


### Speicherdienste

Big Compute-Lösungen basieren in der Regel auf einem Satz von Eingabedaten und generieren Daten für ihre Ergebnisse. In vielen Big Compute-Lösungen kommen unter anderem folgende Azure-Speicherdienste zum Einsatz:

* [Blob-, Tabellen- und Warteschlangenspeicher](http://azure.microsoft.com/documentation/services/storage): Dient zum Verwalten großer Mengen unstrukturierter Daten, NoSQL-Daten und Nachrichten für Workflows bzw. für die Kommunikation. BLOB-Speicher kann beispielsweise für umfangreiche technische Datasets oder für die eingehenden Bilder oder Mediendateien verwenden, die Ihre Anwendung verarbeitet. Warteschlangen können für die asynchrone Kommunikation in einer Lösung verwendet werden. Weitere Informationen zu diesen Speicherlösungen finden Sie unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md).

* [Azure Dateispeicher](http://azure.microsoft.com/services/storage/files/): Dient zum Freigeben häufig verwendeter Dateien und Daten in Azure mithilfe des standardmäßigen SMB-Protokolls, das für einige HPC-Clusterlösungen benötigt wird.

### Daten- und Analysedienste

Einige Big Compute-Szenarien beinhalten umfangreiche Datenflüsse oder generieren Daten, die einer weiteren Verarbeitung oder Analyse bedürfen. Hierzu bietet Azure eine Reihe von Daten- und Analysediensten:

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory): Dient zum Erstellen datengesteuerter Workflows (Pipelines) für die Verknüpfung, Aggregierung und Transformierung von Daten aus lokalen, cloudbasierten und internetbasierten Datenspeichern.

* [SQL-Datenbank](http://azure.microsoft.com/documentation/services/sql-database): Stellt die zentralen Features eines auf Microsoft SQL Server basierenden relationalen Datenbankverwaltungssystems in Form eines verwalteten Plattformdiensts bereit.

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight): Stellt Windows Server- oder Linux-basierte Apache Hadoop-Cluster in der Cloud bereit, um eine hochverfügbare und zuverlässige Verwaltung, Analyse und Berichterstellung für umfangreiche Datenmengen (Big Data) zu ermöglichen.

* [Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning): Unterstützt Sie beim Erstellen, Testen, Betreiben und Verwalten von Predictive Analytics-Lösungen im Rahmen eines vollständig verwalteten Plattformdiensts.

### Zusätzliche Dienste

Ihre Big Compute-Lösung benötigt unter Umständen weitere Infrastruktur- und Plattformdienste von Azure, um eine Verbindung mit lokalen Ressourcen oder mit Ressourcen in anderen Umgebungen herstellen zu können. Beispiele:

* [Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network): Erstellt einen logisch isolierten Bereich in Azure, um mittels IPSec eine Verbindung zwischen Azure-Ressourcen und Ihrem lokalen Rechenzentrum oder einem einzelnen Clientcomputer herzustellen, und ermöglicht Big Compute-Anwendungen den Zugriff auf lokale Daten, Active Directory-Dienste und Lizenzserver.

* [ExpressRoute](http://azure.microsoft.com/documentation/services/expressroute): Stellt eine private Verbindung zwischen Microsoft-Rechenzentren und der lokalen oder in einer Kollokationsumgebung befindlichen Infrastruktur her und bietet dabei mehr Sicherheit und Zuverlässigkeit sowie höhere Geschwindigkeiten und eine geringere Latenz als bei normalen Verbindungen über das Internet.

* [Service Bus](http://azure.microsoft.com/documentation/services/service-bus): Stellt verschiedene Mechanismen für die Kommunikation und den Datenaustausch von Anwendungen bereit. Dabei spielt es keine Rolle, ob sich diese in Azure, auf einer anderen Cloudplattform oder in einem Rechenzentrum befinden.

## Lösungsszenarios

Im Anschluss finden Sie gängige Szenarien für Big Compute-Workloads in Azure, in denen vorhandene HPC-Clusterlösungen, Azure-Dienste oder eine Kombination aus beidem zur Anwendung kommen.

>[AZURE.NOTE]Für Organisationen mit rechenintensiven Workloads, die nicht für standardmäßige HPC-Clustertools geeignet sind oder nicht direkt in Azure-Dienste migriert werden können, bietet Azure Entwicklern und Partnern einen umfassenden Satz von Compute-Funktionen, Diensten, Architekturoptionen und Entwicklungstools. Azure unterstützt benutzerdefinierte Big Compute-Workflows mit mehreren tausend Prozessorkernen.

### Szenario 1: Burst eines lokalen HPC-Clusters nach Azure

**Verwendung**: Sie verfügen bereits über einen lokalen HPC-Cluster zum Ausführen rechenintensiver Workloads, benötigen aber zusätzliche Compute-Ressourcen für Spitzenzeiten (etwa zur Berichterstellung am Monatsende oder für spezielle Projekte). Anstatt nun zusätzliche Hard- und Software zu kaufen, bereitzustellen und zu verwalten, die die meiste Zeit gar nicht genutzt wird, können Sie die Rechenleistung Ihres Clusters mit Azure bedarfsgerecht erhöhen.

Basiert Ihr vorhandener lokaler HPC-Cluster beispielsweise auf [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), können Sie zusätzliche Compute-Ressourcen in Form von in einem Clouddienst ausgeführten Azure-Workerrolleninstanzen hinzufügen. Dies wird in der folgenden Abbildung veranschaulicht. Weitere Informationen und ausführliche Anleitungen finden Sie unter [Erweiterung auf Azure mit Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

![Clusterburst][burst_cluster]

>[AZURE.NOTE]Wenn Sie den Platzbedarf des HPC Pack-Clusters minimieren möchten, können Sie den lokalen Cluster auf den HPC Pack-Hauptknoten verringern. Fügen Sie dann alle Compute-Ressourcen bedarfsgerecht in Azure hinzu. Ein ausführliches Tutorial für dieses Szenario finden Sie unter [Einrichten eines Hybrid-Computeclusters mit Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md).

Mit dieser Hybridlösung können Sie Investitionen, die Sie bereits für einen lokalen Cluster getätigt haben, nutzen und die feste lokale Infrastruktur für typische Workloads (außerhalb der Spitzenzeiten) skalieren. Falls Sie Zugriff auf einen lokalen Lizenzserver oder Datenspeicher benötigen, können Sie ein virtuelles Azure-Netzwerk einrichten, um den lokalen Cluster mit Azure zu verknüpfen.

### Szenario 2: Vollständiges Erstellen eines HPC-Clusters in Azure

**Verwendung**: Sie haben bereits eine erhebliche Investition für einen lokalen Windows- oder Linux-basierten HPC-Cluster (einschließlich Verwaltungs- und Zeitplanungstools und benutzerdefinierter Anwendungen) getätigt. Gegebenenfalls benötigen Sie zusätzliche Clusterkapazität für Ihre vorhandenen Toolsets und Anwendungen, möchten aber nicht noch mehr Geld für die lokale Infrastruktur ausgeben oder Ihre Anwendungen anpassen. Vielleicht müssen Sie aber auch kurz- oder langfristig einen neuen Cluster erstellen, scheuen aber die Kosten für dessen Erwerb, Verwaltung und Betrieb oder möchten sich den Platz für die Installation und Bereitstellung sparen.

Mit Azure Automation-Tools können Sie einen HPC-Cluster auf virtuellen Azure-Computern erstellen, um die benötigte Kapazität zu erhalten. Je nachdem, welche virtuellen Computer Sie bereitstellen, können Sie unterschiedliche HPC-basierte und parallele Workloads ausführen (einschließlich eng gekoppelter MPI-Anwendungen).

>[AZURE.NOTE]Erkundigen Sie sich beim Hersteller Ihrer lokalen Clusterlösung und -anwendungen nach zusätzlichen Anforderungen und bewährten Methoden für die Ausführung in einer öffentlichen Cloud mit IaaS (Infrastructure-as-a-Service).

So können Sie beispielsweise einen Windows Server-basierten HPC-Cluster mit [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) auf virtuellen Azure-Computern mit Infrastrukturdiensten (IaaS) erstellen, um Ihre Workloads auszuführen (wie in der folgenden vereinfachten Abbildung zu sehen). Über die standardmäßigen Auftragsübermittlungstools von HPC Pack, die auf einem Clientcomputer ausgeführt werden, kann ein Clusterbenutzer einen Auftrag sicher an den Cloudcluster übermitteln. Ausführlichere Informationen und Bereitstellungsoptionen finden Sie unter [Microsoft HPC Pack in Azure VMs](https://msdn.microsoft.com/library/azure/dn518135.aspx).

![Cluster in IaaS][iaas_cluster]

**Automatisierte Bereitstellung**: Zur Bereitstellung einer großen Anzahl von Windows Server- oder Linux-basierten VMs können Sie standardmäßige oder benutzerdefinierte VM-Images und Azure Automation-Tools wie die [Azure-Befehlszeilenschnittstelle](../xplat-cli.md) oder [Azure PowerShell](../powershell-install-configure.md) verwenden. Beispiele:

* Wenn Sie einen HPC Pack-Cluster in Azure-Infrastrukturdiensten bereitstellen möchten, können Sie von einem Clientcomputer aus ein flexibles [Azure PowerShell-Skript](https://msdn.microsoft.com/library/azure/dn864734.aspx) ausführen. Das Skript verwendet ein Windows Server-basiertes VM-Image mit vorinstalliertem HPC Pack. Ein HPC Pack-Cluster kann auch mithilfe einer [Azure-Schnellstartvorlage](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) über Azure PowerShell oder über die Azure-Befehlszeilenschnittstelle bereitgestellt werden.

* Mit einer [Azure-Schnellstartvorlage](https://azure.microsoft.com/documentation/templates/slurm/) und Azure PowerShell oder der Azure-Befehlszeilenschnittstelle können Sie einen Linux-Cluster mit dem Open Source-Workload-Manager [SLURM](https://computing.llnl.gov/linux/slurm/) bereitstellen.

Die Platzierung eines gesamten HPC-Clusters in der Cloud kann einige klare Vorteile mit sich bringen.

* Eine Organisation kann HPC-Aufträge ohne Anschaffung und Verwaltung zusätzlicher lokale Hardware verwenden und die Größe des Clusters steuern, um eine effiziente Nutzung der Compute-Ressourcen zu gewährleisten.

* Viele lokale Clusteranwendungen können unverändert (oder mit geringfügigen Anpassungen) in einem cloudbasierten Cluster ausgeführt werden.

* Im Vergleich zu einer Hybridlösung, die einen lokalen Cluster auf die Cloud ausdehnt, kann eine Anwendung, die vollständig in der Cloud ausgeführt wird, den Datenzugriff vereinfachen. Die Daten werden nicht zwischen der Cloud und lokalen Installationen aufgeteilt, und es ist auch kein Remotedatenzugriff für einen Teil der Anwendung erforderlich. Stattdessen können alle Anwendungsdaten in der Cloud gespeichert werden.

* Einige Softwarehersteller optimieren ihre Anwendungen für die Ausführung in cloudbasierten Clustern. Wenn Sie also beispielsweise [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) von MathWorks für einen HPC Pack-Cluster auf virtuellen Azure-Computern bereitstellen, können parallele MATLAB-Aufträge vollständig mit cloudbasierten Compute-Ressourcen ausgeführt werden.

### Szenario 3: Skalieren einer parallelen Anwendung in Azure

**Verwendung**: Möglicherweise führen Sie eine rechenintensive Anwendung (beispielsweise eine Monte Carlo-Simulation, Animationsrendering oder Medientranscodierung) auf lokalen Arbeitsstationen oder in einem kleinen Cluster aus. Sie möchten sich weder um die Compute-Ressourcen noch um einen Auftragsplaner kümmern. Stattdessen möchten Sie sich auf die effiziente Ausführung Ihrer Anwendung und auf die Lösung Ihrer Geschäftsprobleme konzentrieren. Möglicherweise möchten Sie aber auch Ihre rechenintensive Anwendung oder eine Drittanbieteranwendung auslagern und vollständig als Dienst in der Cloud ausführen.

Je nach Workload können Sie ggf. einen vorhandenen Big Compute-Dienst in Azure nutzen, der von Microsoft oder einem anderen Dienstanbieter gehostet wird, um die Infrastruktur- und Anwendungsverwaltung für Ihre Lösung zu vereinfachen. Einige Dienste hosten spezielle Anwendungen für Kunden in bestimmten Branchen. Einige Dienste werden in lokale Anwendungen integriert und bilden eine Hybridlösung. Andere (etwa [Azure Media Services](http://azure.microsoft.com/documentation/services/media-services)) sind dedizierte Plattformdienste.

[Batch](http://azure.microsoft.com/documentation/services/batch) bietet APIs zum Planen von Aufträgen sowie zum Verwalten von Compute-Ressourcen in einem Dienst. Dadurch können Sie problemlos eine aktuelle Anwendung in der Cloud ausführen und skalieren. Batch verwaltet die Bereitstellung und automatische Skalierung virtueller Computer, die Auftragsplanung, die Notfallwiederherstellung, Datenverschiebungen, die Abhängigkeitsverwaltung, die Anwendungsbereitstellung und all die anderen Arbeiten, die zum Ausführen eines Auftrags in der Cloud erforderlich sind. Derzeit eignet sich Batch perfekt für intrinsisch parallele Anwendungen wie Bildrendering, Risikomodelle im Finanzbereich und Monte Carlo-Simulationen in Windows Server-basierten Compute-Ressourcen.

Die folgende Abbildung zeigt einen typischen Workflow, den ein Entwickler mit Batch erstellen kann.

![Azure Batch][batch_proc]

1. Laden Sie Eingabedateien an Azure Storage hoch – beispielsweise Quelldaten oder Bilder, erforderliche ausführbare Anwendungsdateien oder Skriptdateien und deren Abhängigkeiten).

2. Erstellen Sie einen Batch-Dienst, der folgende Aufgaben erfüllt:

    a. Bereitstellen eines Pools identischer virtueller Azure-Computer zum Ausführen der Anwendung (analog zu Compute-Knoten in einem HPC-Cluster). Der Entwickler gibt die Größe, das ausgeführte Betriebssystem und andere Eigenschaften an (beispielsweise, ob der Pool automatisch skaliert werden kann). Wenn eine Aufgabe ausgeführt wird, wird ihr automatisch ein virtueller Computer aus diesem Pool zugewiesen.

    b. Erstellen eines Auftrags, um die Anwendung auszuführen. Der Entwickler kann einen Zeitplan und die Priorität für den Auftrag angeben, aber auch eine bedarfsgesteuerte Ausführung ist möglich.

    c. Unterteilen des Auftrags in Aufgaben. Jede Aufgabe ist im Wesentlichen eine Befehlszeile, die auf einem der virtuellen Computer im Pool ausgeführt wird, um Informationen aus einer der Datendateien zu verarbeiten, die an Storage hochgeladen wurde.

3. Führen Sie den Dienst aus, und überwachen Sie die Ergebnisse.


## Nächste Schritte

* Eine technische Anleitung für Ihre Lösung finden Sie unter [Technische Ressourcen für Batch und HPC](big-compute-resources.md).

* Die neuesten Ankündigungen finden Sie im [Blog des HPC- und Batch-Teams von Microsoft](http://blogs.technet.com/b/windowshpc/) und im [Azure-Blog](http://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=August15_HO6-->