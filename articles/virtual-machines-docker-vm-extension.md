<properties pageTitle="Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure" description="Beschreibt Docker und Container, die Azure Virtual Machines-Erweiterungen und verweist auf weitere Ressourcen zum Erstellen von Docker-Containern über xplat-cli und über das Portal." services="virtual-machines" documentationCenter="" authors="squillace" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill"/>

# Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure
[Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container](http://en.wikipedia.org/wiki/LXC) statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung des [Azure Linux Agent](http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-agent-user-guide/), um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet.

In diesem Thema wird Folgendes beschrieben:

+ [Docker und Linux-Container]
+ [Verwenden der Docker-VM-Erweiterung mit Azure]
+ [Erweiterungen für virtuelle Linux- und Windows-Computer] 

Informationen zum Erstellen von Docker-fähigen virtuellen Computern finden Sie unter:

+ [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]
+ [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]
+ [Gewusst wie: Schneller Einstieg mit Docker in Azure Marketplace]

## <a id='Docker and Linux Containers'>Docker und Linux-Container</a>
[Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container](http://en.wikipedia.org/wiki/LXC) statt virtueller Computer verwendet und andere Möglichkeiten zum schnellen Erstellen oder Zusammensetzen von Anwendungen und Verteilen dieser zwischen anderen Docker-Containern bietet.

Docker und Linux-Container sind keine [Hypervisoren](http://en.wikipedia.org/wiki/Hypervisor) wie Windows Hyper-V und [KVM](http://www.linux-kvm.org/page/Main_Page) auf Linux, um nur zwei Beispiele zu nennen. Hypervisoren virtualisieren das zugrunde liegende Betriebssystem, sodass das komplette Betriebssystem im Hypervisor ausgeführt werden kann, als wäre es eine Anwendung. 

Docker und andere  *container*-Ansätze haben die erforderlichen Startzeiten, Verarbeitung und Speicherbelastung enorm verringert. Sie nutzen die Isolationsfunktionen des Linux Kernels für Prozesse und Dateisysteme, um nur Kernel-Funktionen an einen ansonsten isolierten Container zur Verfügung zu stellen.

Die folgende Tabelle beschreibt auf einem sehr hohen Niveau die Arten der Featureunterschiede, die zwischen Hypervisoren und Linux-Containern vorhanden sind. Beachten Sie, dass einige Features in Abhängigkeit Ihrer eigenen Anwendungsanforderungen möglicherweise vorteilhafter oder weniger vorteilhaft sind.

|   Funktion      | Hypervisoren | Container  |
| :------------- |-------------| ----------- |
| Prozessisolation | Mehr oder weniger abgeschlossen | Wenn Stamm abgerufen wird, könnte Containerhost gefährdet sein |
| Speicher auf der Festplatte erforderlich | Komplette Betriebssysteme und Anwendungen | Nur App-Anforderungen |
| Zeitbedarf zum Starten | Wesentlich länger: Start des Betriebssystems plus Laden der Anwendung | Wesentlich kürzer: Nur Anwendungen müssen gestartet werden, da der Kernel bereits ausgeführt wird  |
| Container-Automatisierung | Variiert erheblich je nach Betriebssystem und Anwendungen | [Docker-Abbildkatalog](https://registry.hub.docker.com/); Sonstiges 

Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (Allgemeines Whiteboard zu Docker, in englischer Sprache).

Weitere Informationen zu Docker und zur tatsächlichen Funktionsweise finden Sie unter [Was ist Docker?](https://www.docker.com/whatisdocker/).

#### Bewährte Methoden für die Docker- und Linux-Containersicherheit

Da Container Zugriff auf den Kernel des Hostcomputers freigeben, kann schädlicher Code nicht nur auf den Hostcomputer, sondern auch auf die anderen Container Zugriff erlangen. Um die Sicherheit Ihres Containersystems stark im Vergleich zur Standardkonfiguration zu verbessern, [empfiehlt Docker](https://docs.docker.com/articles/security/), Gruppenrichtlinien oder [rollenbasierte Sicherheit](http://en.wikipedia.org/wiki/Role-based_access_control) wie von [SELinux](http://selinuxproject.org/page/Main_Page) oder [AppArmor](http://wiki.apparmor.net/index.php/Main_Page) zu verwenden und die den Containern gewährten Kernelfähigkeiten so weit wie möglich zu reduzieren. Darüber hinaus gibt es im Internet zahlreiche weitere Dokumente, die Sicherheitsansätze bei der Verwendung von Containern wie Docker beschreiben.

## <a id='How to use the Docker VM Extension with Azure'>Verwenden der Docker-VM-Erweiterung mit Azure</a>

Die Docker-VM-Erweiterung ist eine in der Instanz des virtuellen Computers installierte Komponente, die Sie erstellen, welche das Docker-Modul installiert und die Remotekommunikation mit dem virtuellen Computer verwaltet. Es gibt zwei Möglichkeiten zum Installieren der VM-Erweiterung: Sie können Ihren virtuellen Computer mithilfe des Verwaltungsportals erstellen. Alternativ können Sie das Image über die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli) erstellen. 

Sie können das Portal zum Hinzufügen der Docker-VM-Erweiterung zu kompatiblen virtuellen Linux-Computern (zurzeit ist das Ubuntu 14.04 LTS-Image das einzige Image, das es unterstützt (aktueller als Juli)) verwenden. Mit der Befehlszeile "xplat-cli" können Sie jedoch die Docker-VM-Erweiterung installieren und Ihre Docker-Kommunikationszertifikate gleichzeitig erstellen und hochladen, wenn Sie die VM-Instanz erstellen.

Informationen zum Erstellen von Docker-fähigen virtuellen Computern finden Sie unter:

+ [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]
+ [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]

## <a id='Virtual Machine Extensions for Linux and Windows'>Erweiterungen für virtuelle Linux- und Windows-Computer</a>
Die Docker-VM-Erweiterung ist nur eine von mehreren VM-Erweiterungen mit besonderen Funktionen und weitere befinden sich in der Entwicklung. Beispielsweise ermöglichen es Ihnen einige Features der [Linux VM Agent-Erweiterung](http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-agent-user-guide/) unter anderem, das Abbild einschließlich Sicherheits-, Kernel- und Netzwerkfeatures zu ändern und zu verwalten. Mit der VMAccess-Erweiterung für Windows können Sie die Einstellungen des Remotedesktop-Zugriffs zurücksetzen oder ändern und das Administratorkennwort zurücksetzen. 

Eine vollständige Liste finden Sie unter [Azure VM-Erweiterungen](http://msdn.microsoft.com/de-de/library/azure/dn606311.aspx).

<!--Anchors-->
[Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-docker-with-portal/
[Gewusst wie: Schneller Einstieg mit Docker in Azure Marketplace]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker und Linux-Container]: #Docker-and-Linux-Containers
[Verwenden der Docker-VM-Erweiterung mit Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Erweiterungen für virtuelle Linux- und Windows-Computer]: #Virtual-Machine-Extensions-For-Linux-and-Windows




<!--HONumber=42-->
