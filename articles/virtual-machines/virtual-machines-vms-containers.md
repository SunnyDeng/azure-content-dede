<properties 
	pageTitle="Virtuelle Computer und Container in Azure" 
	description="Beschreibt virtuelle Computer, Docker- und Linux-Container und ihre Verwendung in den entsprechenden Gruppen in Azure, einschließlich der jeweiligen Vorteile und von Szenarien, in denen jeder Ansatz gut funktioniert." 
	services="virtual-machines" 
	documentationCenter="virtual-machines" 
	authors="squillace" 
	manager="timlt" 
/>
	

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="infrastructure" 
	ms.workload="infrastructure" 
	ms.date="07/02/2015" 
	ms.author="rasquill" 
/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Virtuelle Computer und Container in Azure

Azure bietet Ihnen hervorragende Cloudlösungen auf Basis virtueller Computer – beruhend auf der Emulation physischer Computerhardware – und ermöglicht Ihnen so hohe Flexibilität bei der Softwarebereitstellung und eine deutlich bessere Ressourcenkonsolidierung als mit physischer Hardware. Insbesondere dank des [Docker](https://www.docker.com)-Ansatzes für Container und des Docker-Ökosystems hat die Linux-Containertechnologie in den letzten Jahren immer mehr Möglichkeiten erschlossen, wie Sie verteilte Software entwickeln und verwalten können. Der Anwendungscode in einem Container wird vom virtuellen Azure-Hostcomputer sowie von anderen Containern auf demselben virtuellen Computer isoliert. Auf diese Weise erhalten Sie mehr Flexibilität für die Entwicklung und Bereitstellung auf Anwendungsebene – zusätzlich zu der Flexibilität, die Ihnen die virtuellen Azure-Computer bereits bieten.

**Aber das ist nichts Neues.** Die tatsächliche *Neuigkeit* ist, dass Azure Ihnen noch mehr Docker-Vorteile bietet:

- [Viele](virtual-machines-docker-with-xplat-cli.md) [verschiedene](virtual-machines-docker-with-portal.md) [Möglichkeiten](virtual-machines-docker-ubuntu-quickstart.md) zum [Erstellen von Docker-Hosts](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)für Container, die zu Ihren individuellen Anforderungen passen
- [Azure-Ressourcen-Manager](resource-group-overview.md) und [Ressourcengruppenvorlagen](resource-group-authoring-templates.md) zur Vereinfachung von Bereitstellung und Updates von komplexen verteilten Anwendungen
- Integration einer großen Anzahl von proprietären und Open-Source-Tools für die Konfigurationsverwaltung

Und da Sie virtuelle Computer und Linux-Container in Azure programmgesteuert erstellen können, können Sie auch Tools zur *Orchestrierung* für virtuelle Computer und Container verwenden, um VM-Gruppen (Virtual Machines, virtuelle Computer) zu erstellen und um Anwendungen in Linux-Containern und bald auch in [Windows Server-Containern](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) bereitzustellen.

In diesem Artikel werden diese Konzepte nicht nur erörtert, sondern Sie finden auch zahlreiche Links zu weiteren Informationen, Lernprogrammen und Produkten in Bezug auf die Container- und Clusternutzung in Azure. Wenn für Sie lediglich die Links von Interesse sind, finden Sie diese [hier](#tools-for-working-with-containers).

## Der Unterschied zwischen virtuellen Computern und Containern

Virtuelle Computer werden in einer Virtualisierungsumgebung mit isolierter Hardware ausgeführt, die von einem [Hypervisor](http://en.wikipedia.org/wiki/Hypervisor) bereitgestellt wird. In Azure übernimmt der Dienst [Virtual Machines](http://azure.microsoft.com/services/virtual-machines/) diese Aufgaben für Sie. Sie erstellen virtuelle Computer lediglich, in dem Sie das Betriebssystem auswählen und die Konfiguration nach Ihren Wünschen vornehmen – oder indem Sie ein eigenes benutzerdefiniertes VM-Image hochladen. Virtuelle Computer sind eine bewährte, vielfach erprobte Technologie und es stehen viele Tools zur Verfügung, um Betriebssysteme zu verwalten und die Anwendungen zu konfigurieren, die Sie installieren und ausführen. Alle auf einem virtuellen Computer ausgeführten Prozesse sind vor dem Hostbetriebssystem verborgen und aus der Perspektive einer Anwendung oder eines Benutzers des virtuellen Computers scheint es sich bei diesem um einen autonomen physischen Computer zu handeln.

[Linux-Container](http://en.wikipedia.org/wiki/LXC) – darunter auch die mithilfe von Docker-Tools erstellten und gehosteten Container (und es gibt noch weitere Ansätze) – erfordern und verwenden keinen Hypervisor, um Isolation bereitzustellen. Stattdessen verwendet der Containerhost die Isolationsfunktionen für Prozesse und Dateisysteme des Linux-Kernels, um für den Container (und dessen Anwendung) nur bestimmte Kernel-Funktionen und das eigene isolierte Dateisystem offenzulegen (mindestens). Aus der Perspektive einer Anwendung, die in einem Container ausgeführt wird, scheint es sich bei dem Container um eine eindeutige Betriebssysteminstanz zu handeln. Für eine enthaltene Anwendung sind Prozesse und andere Ressourcen außerhalb ihres Containers nicht sichtbar.

Da der Kernel des Docker-Hosts in diesem Isolations- und Ausführungsmodell gemeinsam genutzt wird und die Speicherplatzanforderungen des Containers kein vollständiges Betriebssystem umfassen, ist die Startzeit des Containers deutlich kürzer und der erforderliche Speicheraufwand ist wesentlich kleiner.

Das ist wirklich praktisch.

[Windows Server-Container](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) bieten für Anwendungen unter Windows dieselben Vorteile wie Linux-Container. Windows Server-Container unterstützen das Docker-Imageformat und die Docker-API. Daher kann eine Anwendung mithilfe von Windows Server-Containern über ähnliche Befehle wie unter Mac und Linux entwickelt, veröffentlicht, abgerufen und bereitgestellt werden. Zusätzlich gibt es noch die [neue Docker-Unterstützung in Microsoft Visual Studio](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0). Das größere [Containerökosystem](https://msdn.microsoft.com/virtualization/windowscontainers/about/container_ecosystem) stellt allen Benutzern die Tools zur Verfügung, die sie zum Arbeiten mit Containern benötigen.

Das ist auch äußerst praktisch.

### Ist das alles wirklich möglich?

Ja – und nein. Wie bei jeder anderen Technologie auch wird durch Container die harte Arbeit, die für verteilte Anwendungen nötig ist, nicht einfach von Zauberhand verschwinden. Dennoch ändert sich mit Containern Folgendes:

- Wie schnell Anwendungscode entwickelt und umfassend freigegeben werden kann
- Wie schnell und wie zuverlässig der Code getestet werden kann
- Wie schnell und wie zuverlässig der Code bereitgestellt werden kann

Denken Sie daran, dass Container auf einem Containerhost ausgeführt werden – ein Betriebssystem und in Azure bedeutet das: ein virtueller Azure-Computer. Auch wenn das Konzept von Containern Sie schon überzeugt hat, benötigen Sie weiterhin eine VM-Infrastruktur, die die Container hostet. Der Vorteil ist jedoch, dass es nicht wichtig ist, auf welchem virtuellen Computer die Container ausgeführt werden (ob ein Container allerdings in einer Linux- oder in einer Windows-Ausführungsumgebung ausgeführt wird, ist zum Beispiel wichtig).

## Welche Vorteile bieten Container?

Sie eignen sich für vieles, aber besonders – ebenso wie die [Azure-Clouddienste](http://azure.microsoft.com/services/cloud-services/) und [Azure Service Fabric](service-fabric-overview.md) – für die Erstellung von [Microservice]-orientierten verteilten Einzeldienstanwendungen, bei denen der Anwendungsentwurf auf mehr kleinen, zusammensetzbaren Teilen beruht statt auf größeren, stärker verknüpften Komponenten.

Dies gilt insbesondere in öffentlichen Cloudumgebungen wie Azure, bei denen Sie virtuelle Computer nach Bedarf mieten. Nicht nur erhalten Sie Tools für Isolation, schnelle Bereitstellung und Orchestrierung, sondern Sie können auch effizientere Entscheidungen zur Anwendungsinfrastruktur treffen.

Beispielsweise besteht Ihre derzeitige Bereitstellung vielleicht aus 9 großen virtuellen Azure-Computern für eine verteilte Anwendung mit hoher Verfügbarkeit. Wenn die Komponenten dieser Anwendung in Containern bereitgestellt werden können, kann es möglich sein, dass Sie nur 4 virtuelle Computer verwenden und die Anwendungskomponenten innerhalb von 20 Containern für Redundanz und Lastenausgleich bereitstellen.

Dies ist natürlich nur ein Beispiel, aber wenn dies in Ihrem Szenario denkbar ist, können Sie Nutzungsspitzen mit mehr Containern anstelle von mehr virtuellen Azure-Computern anpassen und die verbleibende CPU-Gesamtauslastung wesentlich effizienter verwenden.

Außerdem gibt es viele Szenarios, die nicht für einen Microservices-Ansatz geeignet sind. Sie können selbst am besten einschätzen, ob Microservices und Container Ihnen helfen.

### Vorteile von Containern für Entwickler

Allgemein gesehen, ist es offenkundig, dass die Containertechnologie ein Schritt nach vorne ist, aber es gibt auch spezifischere Vorteile. Zur Veranschaulichung dient hier das Beispiel der Docker-Container. In diesem Thema wird Docker nicht ausführlich behandelt (lesen Sie für mehr Informationen [What is Docker?](https://www.docker.com/whatisdocker/) (in englischer Sprache) oder [Wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)), dennoch bieten Docker und das zugehörige Ökosystem erhebliche Vorteile für Entwickler und IT-Experten.

Entwickler sind schnell von Docker-Containern überzeugt, da diese insbesondere die Verwendung von Linux-Containern vereinfachen:

- Sie können einfache, inkrementelle Befehle verwenden, um ein festes Image zu erstellen, das leicht bereitgestellt werden kann, und die Erstellung dieser Images mithilfe einer Docker-Datei automatisieren.
- Sie können diese Images problemlos über einfache Push- und Pull-Befehle im [git](https://git-scm.com/)-Stil für [öffentliche](https://registry.hub.docker.com/) oder [private](virtual-machines-docker-registry-on-azure-blob-storage.md) Docker-Registrierungen freigeben. 
- Sie können sich auf isolierte Anwendungskomponenten anstelle von Computern konzentrieren.
- Sie können eine große Anzahl von Tools verwenden, die für Docker-Container und andere Basisimages geeignet sind.

### Vorteile von Containern für Betriebs- und IT-Experten

IT- und Betriebsexperten profitieren ebenfalls von der Kombination aus Containern und virtuellen Computern.

- Enthaltene Dienste sind von der Ausführungsumgebung des VM-Hosts isoliert.
- Enthaltener Code ist überprüfbar identisch.
- Enthaltene Dienste können schnell gestartet und beendet sowie zwischen Entwicklungs-, Test- und Produktionsumgebungen verschoben werden.

Funktionen wie diese – und es gibt noch mehr – sind außerordentlich interessant für etablierte Unternehmen, deren IT-Abteilungen die Aufgabe zukommt, die Ressourcen – darunter auch die reine Verarbeitungsleistung – an die geforderten Aufgaben anzupassen, um nicht nur im Geschäft zu bleiben, sondern auch um die Kundenzufriedenheit zu erhöhen und um mehr Kunden zu erreichen. Die Anforderungen von kleinen Unternehmen, ISVs und Startups sind identisch, werden aber möglicherweise anders formuliert.

## Welche Vorteile bieten virtuelle Computer?

Virtuelle Computer sind die tragende Säule des Cloud Computing und das ist unumstößlich. Virtuelle Computer starten langsamer, haben einen größeren Datenträgerbedarf und sind nicht direkt einer Microservices-Architektur zugeordnet, haben aber dennoch sehr große Vorteile:

1. Ihre Standardsicherheitsmaßnahmen für Hostcomputer sind wesentlich robuster.
2. Sie unterstützen alle wichtigen Betriebssystem- und Anwendungskonfigurationen.
3. Sie verfügen über einen bewährten Bestand an Befehls- und Steuerungstools.
4. Sie stellen die Ausführungsumgebung für Hostcontainer bereit.

Der letzte Punkt ist wichtig, da eine enthaltene Anwendung weiterhin ein bestimmtes Betriebssystem und einen bestimmten CPU-Typ erfordert, abhängig von den Aufrufen, die die Anwendung vornimmt. Es ist wichtig zu beachten, dass Container auf virtuellen Computern installiert werden, da sie die Anwendungen enthalten, die Sie bereitstellen möchten. Container sind kein Ersatz für virtuelle Computer oder Betriebssystemen.

## Allgemeiner Funktionsvergleich zwischen virtuellen Computern und Containern

Die folgende Tabelle beschreibt auf allgemeiner Ebene die Arten der Funktionsunterschiede, die – ohne großen Mehraufwand – zwischen virtuellen Computern und Linux-Containern bestehen. Beachten Sie, dass manche Features nicht so geeignet sind wie andere, abhängig von Ihren eigenen Anwendungsanforderungen, und dass zusätzliche Arbeit wie bei jeder Software mehr Funktionsunterstützung mit sich bringt, insbesondere im Bereich der Sicherheit.

| Funktion | VMs | Container |
| :------------- |-------------| ----------- |
| "Standardmäßige" Sicherheitsunterstützung | In höherem Maß | In etwas geringerem Maß |
| Speicher auf Datenträger erforderlich | Vollständiges Betriebssystem plus Apps | Nur App-Anforderungen |
| Startdauer | Wesentlich länger: Starten des Betriebssystems plus Laden der App | Wesentlich kürzer: Nur Apps müssen gestartet werden, da der Kernel bereits ausgeführt wird |
| Portabilität | Portabel bei richtiger Vorbereitung | Portable innerhalb des Imageformats; in der Regel kleiner | 
| Imageautomatisierung | Variiert stark in Abhängigkeit von Betriebssystem und Apps | [Docker-Registrierung](https://registry.hub.docker.com/); andere

## Erstellen und Verwalten von Gruppen von virtuellen Computern und Containern

Da das alles automatisiert werden kann, betrachten Architekten, Entwickler oder IT-Betriebsexperten dies an diesem Punkt möglicherweise als "Rechenzentrum-as-a-Service".

Das kann durchaus zutreffen und es gibt eine große Anzahl von Systemen, von denen Sie vielleicht viele schon nutzen, die Gruppen von Azure-VMs verwalten und benutzerdefinierten Code mithilfe von Skripts einfügen können, oft mit [CustomScriptingExtension für Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) oder mit [CustomScriptingExtension für Linux](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Sie können – und haben es vielleicht schon getan – Ihre Azure-Bereitstellungen mithilfe von PowerShell oder Azure-Befehlszeilenschnittstellen-Skripts automatisieren, indem Sie [wie hier beschrieben](virtual-machines-create-multi-vm-deployment-xplat-cli.md) vorgehen.

Diese Fähigkeiten werden dann oft in Tools wie [Puppet](https://puppetlabs.com/)und[Chef](https://www.chef.io/) migriert, um die skalierte Erstellung und Konfiguration für virtuelle Computer zu automatisieren. (Links zur Verwendung dieser Tools mit Azure finden Sie [hier](#tools-for-working-with-containers).)

### Azure-Ressourcengruppenvorlagen

Vor Kurzem wurde die REST-API der [Azure Ressourcenverwaltung](virtual-machines-azurerm-versus-azuresm.md) veröffentlicht und die PowerShell- und Azure-Befehlszeilenschnittstellen-Tools wurden aktualisiert, um die Verwendung zu erleichtern. Sie können gesamte Anwendungstopologien bereitstellen, ändern oder erneut bereitstellen, indem Sie die [Azure-Ressourcen-Manager-Vorlagen](https://msdn.microsoft.com/library/azure/dn835138.aspx) mit der Azure Ressourcenverwaltungs-API verwenden. Hierzu verwenden Sie:

- Das [Azure-Vorschauportal mit Vorlagen](https://github.com/Azure/azure-quickstart-templates) (Hinweis: Verwenden Sie die Schaltfläche "DeployToAzure".)
- Die [Azure-Befehlszeilenschnittstelle](virtual-machines-deploy-rmtemplates-azure-cli.md)
- Die [Azure PowerShell-Module](virtual-machines-deploy-rmtemplates-azure-cli.md)


### Bereitstellung und Verwaltung vollständiger Gruppen von Azure-VMs und Containern

Es gibt mehrere beliebte Systeme, die ganze Gruppen von virtuellen Computern bereitstellen und auf diesen Docker (oder andere Linux-Containerhostsysteme) als automatisierbare Gruppe installieren können. Direkte Links finden Sie im Abschnitt [Container und Tools](#containers-and-vm-technologies) weiter unten. Es gibt eine Reihe von Systemen, die dies in größerem oder weniger großem Umfang können. Diese Liste ist nicht vollständig. Wie nützlich sie sind, hängt von Ihren Kenntnissen und Szenarien ab.

Docker verfügt über einen eigenen Satz von Tools zur Erstellung von virtuellen Computern ([docker-machine](virtual-machines-docker-machine.md)) und über ein Clusterverwaltungstool für Lastenausgleich und Docker-Container ([swarm](virtual-machines-docker-swarm.md)). Darüber hinaus bietet die [Azure VM-Erweiterung für Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) standardmäßig Unterstützung für[`docker-compose`](https://docs.docker.com/compose/), womit konfigurierte Anwendungscontainer über mehrere Container hinweg bereitgestellt werden können.

Außerdem können Sie auch [Data Center Operating System (DCOS) von Mesosphere](http://docs.mesosphere.com/install/azurecluster/) testen. DCOS basiert auf dem "Open-Source-Kernel für verteilte Systeme" [mesos](http://mesos.apache.org/), der es Ihnen ermöglicht, Ihr Rechenzentrum als einen adressierbaren Dienst zu behandeln. DCOS verfügt über integrierte Pakete für verschiedene wichtige Systeme wie [Spark](http://spark.apache.org/) und [Kafka](http://kafka.apache.org/) (und andere) sowie über integrierte Dienste wie [Marathon](https://mesosphere.github.io/marathon/) (ein Containersteuerungssystem) und [Chronos](https://mesosphere.github.io/chronos/) (ein verteilter Scheduler). Mesos entstand auf Basis von Erfahrungen mit Twitter, AirBnb und anderen im Web operierenden Unternehmen.

Darüber hinaus ist [kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure) ein Open-Source-System für die VM- und Containergruppenverwaltung, das auf Grundlage der gewonnenen Erkenntnisse bei Google entstand. Möglich ist auch die [Verwendung von kubernetes mit weave, um Netzwerkunterstützung bereitzustellen](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) ist eine Open-Source-"Plattform-as-a-Service" (PaaS), die das Bereitstellen und Verwalten von Anwendungen auf Ihren eigenen Servern erleichtert. Deis baut auf Docker und CoreOS auf, um eine einfache PaaS mit einem durch Heroku inspirierten Workflow bereitzustellen. Sie können in Azure auf einfache Weise [eine Azure-VM-Gruppe mit 3 Knoten erstellen und Deis installieren](virtual-machines-deis-cluster.md) und dann[ eine Hello World-Go-Anwendung installieren](virtual-machines-deis-cluster.md#deploy-and-scale-a-hello-world-application).

[CoreOS](virtual-machines-linux-coreos-how-to.md), eine Linux-Distribution mit optimiertem Ressourcenbedarf, Docker-Unterstützung und einem eigenen Containersystem namens [rkt](https://github.com/coreos/rkt) verfügt ebenfalls über ein Verwaltungstool für Containergruppen namens [fleet](virtual-machines-linux-coreos-fleet-get-started.md).

Ubuntu, eine weitere sehr populäre Linux-Distribution, unterstützt Docker sehr gut, aber auch [Linux-Cluster (LXC-Format)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## Tools für das Arbeiten mit Azure-VMs und Containern

Für die Arbeit mit Containern und Azure-VMs sind Tools erforderlich. Dieser Abschnitt enthält eine Liste einiger der nützlichsten oder wichtigsten Konzepte und Tools für Container und Gruppen sowie der umfassenderen Konfigurations- und Orchestrierungstools, die mit ihnen verwendet werden.

> [AZURE.NOTE]Dieser Bereich ist schnellen Veränderungen unterworfen und obwohl wir stets intensiv daran arbeiten, dieses Thema und die zugehörigen Links auf dem aktuellen Stand zu halten, ist dies vielleicht nicht immer möglich. Recherchieren Sie zu Themen, die Sie interessieren, um auf dem Laufenden zu bleiben.

### Container- und VM-Technologien

Einige Technologien für Linux-Container:

- [Docker](https://www.docker.com)
- [LXC](https://linuxcontainers.org/)
- [CoreOS und rkt](https://github.com/coreos/rkt)
- [Open Container-Projekt](http://opencontainers.org/)
- [RancherOS](http://rancher.com/rancher-os/)

Links zu Windows Server-Containern:

- [Windows Server-Container](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Links zu Visual Studio-Docker:

- [Visual Studio 2015 RC-Tools für Docker - Vorschau](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker-Tools:

- [Docker-Daemon](https://docs.docker.com/installation/#installation)
- Docker-Clients
	- [Windows-Docker-Client auf Chocolatey](https://chocolatey.org/packages/docker)
	- [Docker-Installationsanweisungen](https://docs.docker.com/installation/#installation)


Docker in Microsoft Azure:

- [Docker-VM-Erweiterung für Linux auf Azure](virtual-machines-docker-vm-extension.md)
- [Benutzerhandbuch für die Azure-Docker-VM-Erweiterung](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
- [Verwenden der Docker-VM-Erweiterung aus der Azure-Befehlszeilenschnittstelle (Azure-CLI)](virtual-machines-docker-with-xplat-cli.md)
- [Verwenden der Docker-VM-Erweiterung aus dem Azure-Vorschauportal](virtual-machines-docker-with-portal.md)
- [Schneller Einstieg in Docker in Azure Marketplace](virtual-machines-docker-ubuntu-quickstart.md)
- [Verwenden von "docker-machine" auf Azure](virtual-machines-docker-machine.md)
- [Verwenden von Docker mit Swarm auf Azure](virtual-machines-docker-swarm.md)
- [Erste Schritte mit Docker und Compose auf einem virtuellen Azure-Computer](virtual-machines-docker-compose-quickstart.md)
- [Using an Azure resource group template to create a Docker host on Azure quickly](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) ("Verwenden einer Azure-Ressourcengruppenvorlage zur schnellen Erstellung eines Docker-Hosts in Azure", in englischer Sprache)
- [Integrierte Unterstützung`compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) für enthaltene Anwendungen
- [Implementieren einer privaten Docker-Registrierung für Azure](virtual-machines-docker-registry-on-azure-blob-storage.md)

Linux-Distributionen und Azure-Beispiele:

- [CoreOS](virtual-machines-linux-coreos-how-to.md)

Konfiguration, Clusterverwaltung und Containerorchestrierung:

- [Fleet in CoreOS](virtual-machines-linux-coreos-fleet-get-started.md)

-	Deis
	- [Erstellen einer Gruppe von virtuellen Azure-Computern mit 3 Knoten, Installieren von Deis und Starten einer Hello World-Go-Anwendung](virtual-machines-deis-cluster.md)
	
-	Kubernetes
	- [Vollständige Anleitung für die automatisierte Kubernetes- Clusterbereitstellung mit CoreOS und Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
	
-	[Mesos](http://mesos.apache.org/)
	-	[Data Center Operating System (DCOS) von Mesosphere](http://beta-docs.mesosphere.com/install/azurecluster/)
	
-	[Jenkins](https://jenkins-ci.org/) und [Hudson](http://hudson-ci.org/)
	- [Blog: Jenkins Slave-Plug-In für Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub-Repository: Jenkins Speicher-Plug-In für Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Drittanbieter: Hudson Slave-Plug-In für Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Drittanbieter: Hudson Speicher-Plug-In für Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
	
-	[Chef](https://docs.chef.io/index.html)
	- [Chef und virtuelle Computer](virtual-machines-windows-install-chef-client.md)
	- [Video: Was ist Chef, und wie funktioniert es??](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

-	[Azure-Automatisierung](http://azure.microsoft.com/services/automation/)
	- [Video: Verwenden von Azure-Automatisierung mit virtuellen Linux-Computern](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
	
-	Powershell DSC für Linux
    - [Blog: Powershell DSC für Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: Docker Client DSC](https://github.com/anweiss/DockerClientDSC)

## Nächste Schritte

Auschecken von [Docker](https://www.docker.com) und [Windows Server-Containern](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->

<!---HONumber=July15_HO4-->