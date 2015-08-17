<properties
	pageTitle="Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure"
	description="Beschreibt Docker und Container sowie die Azure Virtual Machines-Erweiterungen und verweist auf weitere Ressourcen zum Erstellen von Docker-Containern über die Azure-Befehlszeilenschnittstelle und über das Portal."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="06/29/2015"
	ms.author="rasquill"/>

# Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure
[Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, bei dem Anwendungsdaten und Computing für gemeinsam genutzte Ressourcen mithilfe von [Linux-Containern](http://en.wikipedia.org/wiki/LXC) (anstelle von virtuellen Computern) isoliert werden. Verwenden Sie die [Azure Docker-VM-Erweiterung](https://github.com/Azure/azure-docker-extension/blob/master/README.md) des [Azure Linux-Agents](virtual-machines-linux-agent-user-guide.md), um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet.

In diesem Thema wird Folgendes beschrieben:

+ [Docker und Linux-Container]
+ [Verwenden der Docker-VM-Erweiterung mit Azure]
+ [Erweiterungen für virtuelle Linux- und Windows-Computer]

Informationen zum Erstellen von Docker-fähigen virtuellen Computern finden Sie unter:

+ [Verwenden der Docker-VM-Erweiterung über die Azure-Befehlszeilenschnittstelle]
+ [Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]
+ [Schneller Einstieg in Docker in Azure Marketplace]

Weitere Informationen zur Erweiterung und Funktionsweise finden Sie im [Benutzerhandbuch für die Docker-Erweiterung](https://github.com/Azure/azure-docker-extension/blob/master/README.md).

## Docker und Linux-Container
[Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, bei dem Anwendungsdaten und Computing für gemeinsam genutzte Ressourcen mithilfe von [Linux-Containern](http://en.wikipedia.org/wiki/LXC) (anstelle von virtuellen Computern) isoliert werden. Darüber hinaus stehen weitere Dienste zum schnellen Erstellen oder Assemblieren von Anwendungen sowie zum Verteilen an andere Docker-Container bereit.

Docker- und Linux-Container sind keine [Hypervisoren](http://en.wikipedia.org/wiki/Hypervisor) wie Windows Hyper-V und [KVM](http://www.linux-kvm.org/page/Main_Page) auf Linux, um nur zwei Beispiele zu nennen. Hypervisoren virtualisieren das zugrunde liegende Betriebssystem, sodass ganze Betriebssysteme (so genannte *virtuelle Computer*) im Hypervisor wie eine Anwendung ausgeführt werden können.

Docker und andere *Container*-Ansätze haben die erforderlichen Startzeiten, Verarbeitung und Speicherbelastung enorm verringert. Sie nutzen die Isolationsfunktionen des Linux Kernels für Prozesse und Dateisysteme, um nur Kernel-Funktionen an einen ansonsten isolierten Container zur Verfügung zu stellen.

Die folgende Tabelle beschreibt auf einem sehr hohen Niveau die Arten der Featureunterschiede, die zwischen Hypervisoren und Linux-Containern vorhanden sind. Beachten Sie, dass einige Features in Abhängigkeit Ihrer eigenen Anwendungsanforderungen möglicherweise vorteilhafter oder weniger vorteilhaft sind.

| Funktion | Hypervisoren | Container |
| :------------- |-------------| ----------- |
| Prozessisolation | Mehr oder weniger abgeschlossen | Wenn Stamm abgerufen wird, wäre Containerhost kompromittiert |
| Speicher auf Datenträger erforderlich | Vollständiges Betriebssystem plus Apps | Nur App-Anforderungen |
| Startdauer | Wesentlich länger: Starten des Betriebssystems plus Laden der App | Wesentlich kürzer: Nur Apps müssen gestartet werden, da der Kernel bereits ausgeführt wird |
| Containerautomatisierung | Variiert stark in Abhängigkeit von Betriebssystem und Apps | [Docker-Image-Galerie](https://registry.hub.docker.com/); andere

Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (Whiteboard auf hoher Ebene zu Docker) (in englischer Sprache).

Weitere Informationen zu Docker und zur tatsächlichen Funktionsweise finden Sie unter [Was ist Docker?](https://www.docker.com/whatisdocker/).

#### Bewährte Methoden für die Docker- und Linux-Containersicherheit

Da Container Zugriff auf den Kernel des Hostcomputers freigeben, kann schädlicher Code nicht nur auf den Hostcomputer, sondern auch auf die anderen Container Zugriff erlangen. Um die Sicherheit Ihres Containersystems stark im Vergleich zur Standardkonfiguration zu verbessern, [empfiehlt Docker](https://docs.docker.com/articles/security/), Gruppenrichtlinien oder [rollenbasierte Sicherheit](http://en.wikipedia.org/wiki/Role-based_access_control) wie von [SELinux](http://selinuxproject.org/page/Main_Page) oder [AppArmor](http://wiki.apparmor.net/index.php/Main_Page) zu verwenden und die den Containern gewährten Kernelfähigkeiten so weit wie möglich zu reduzieren. Darüber hinaus gibt es im Internet zahlreiche weitere Dokumente, die Sicherheitsansätze bei der Verwendung von Containern wie Docker beschreiben.

## Verwenden der Docker-VM-Erweiterung mit Azure

Die Docker-VM-Erweiterung ist eine in der Instanz des virtuellen Computers installierte Komponente, die Sie erstellen, welche das Docker-Modul installiert und die Remotekommunikation mit dem virtuellen Computer verwaltet. Die VM-Erweiterung kann auf zwei Arten installiert werden: Sie können Ihren virtuellen Computer entweder mithilfe des Verwaltungsportals oder über die Azure-Befehlszeilenschnittstelle erstellen.

Sie können das Portal zum Hinzufügen der Docker-VM-Erweiterung zu kompatiblen virtuellen Linux-Computern (zurzeit ist das Ubuntu 14.04 LTS-Image das einzige Image, das es unterstützt (aktueller als Juli)) verwenden. Mit der Azure-Befehlszeilenschnittstelle können Sie jedoch die Docker-VM-Erweiterung installieren und Ihre Docker-Kommunikationszertifikate gleichzeitig erstellen und hochladen, wenn Sie die VM-Instanz erstellen.

Informationen zum Erstellen von Docker-fähigen virtuellen Computern finden Sie unter:

+ [Verwenden der Docker-VM-Erweiterung über die Azure-Befehlszeilenschnittstelle]
+ [Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]

## Erweiterungen für virtuelle Linux- und Windows-Computer
Die [Docker-VM-Erweiterung für Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) ist nur eine von vielen VM-Erweiterungen mit besonderen Funktionen. Weitere sind bereits in der Entwicklung. Beispielsweise ermöglichen es Ihnen einige Features der [Linux VM Agent-Erweiterung](virtual-machines-linux-agent-user-guide.md) unter anderem, den virtuellen Computer, einschließlich Sicherheits-, Kernel- und Netzwerkfeatures, zu ändern und zu verwalten. Mit der VMAccess-Erweiterung können Sie z. B. das Administratorkennwort oder den SSH-Schlüssel zurücksetzen.

Eine vollständige Liste finden Sie unter [Azure VM-Erweiterungen](http://msdn.microsoft.com/library/azure/dn606311.aspx).

<!--Anchors-->
[Verwenden der Docker-VM-Erweiterung über die Azure-Befehlszeilenschnittstelle]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Schneller Einstieg in Docker in Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker und Linux-Container]: #Docker-and-Linux-Containers
[Verwenden der Docker-VM-Erweiterung mit Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Erweiterungen für virtuelle Linux- und Windows-Computer]: #Virtual-Machine-Extensions-For-Linux-and-Windows
 

<!---HONumber=August15_HO6-->