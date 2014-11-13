<properties title="Die Docker-Erweiterung f&uuml;r virtuelle Linux-Computer auf Azure" pageTitle="Die Docker-Erweiterung f&uuml;r virtuelle Linux-Computer auf Azure" description="Beschreibt Docker und Container, die Azure Virtual Machines-Erweiterungen und verweist auf weitere Ressourcen zum Erstellen von Docker-Containern &uuml;ber xplat-cli und &uuml;ber das Portal." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />

# Die Docker-Erweiterung für virtuelle Linux-Computer auf Azure

[Docker][Docker] ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container][Linux-Container] statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung des [Azure Linux Agent][Azure Linux Agent], um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet.

In diesem Thema wird Folgendes beschrieben:

-   [Docker und Linux-Container][Docker und Linux-Container]
-   [Verwenden der Docker-VM-Erweiterung mit Azure][Verwenden der Docker-VM-Erweiterung mit Azure]
-   [Erweiterungen für virtuelle Linux- und Windows-Computer][Erweiterungen für virtuelle Linux- und Windows-Computer]

Informationen zum Erstellen von Docker-fähigen virtuellen Computern finden Sie unter:

-   [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)][Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]
-   [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal][Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]

## <span id="Docker and Linux Containers"></span>Docker und Linux-Container</a>

[Docker][Docker] ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container][Linux-Container] statt virtueller Computer verwendet und andere Möglichkeiten zum schnellen Erstellen oder Zusammensetzen von Anwendungen und Verteilen dieser zwischen anderen Docker-Containern bietet.

Docker- und Linux-Container sind keine [Hypervisoren][Hypervisoren] wie Windows Hyper-V und [KVM][KVM] auf Linux, um nur zwei Beispiele zu nennen. Hypervisoren virtualisieren das zugrunde liegende Betriebssystem, sodass das komplette Betriebssystem im Hypervisor ausgeführt werden kann, als wäre es eine Anwendung.

Docker und andere *Container*-Ansätze haben die erforderlichen Startzeiten, Verarbeitung und Speicherbelastung enorm verringert. Sie nutzen die Isolationsfunktionen des Linux Kernels für Prozesse und Dateisysteme, um nur Kernel-Funktionen an einen ansonsten isolierten Container zur Verfügung zu stellen.

Die folgende Tabelle beschreibt auf einem sehr hohen Niveau die Arten der Featureunterschiede, die zwischen Hypervisoren und Linux-Containern vorhanden sind. Beachten Sie, dass einige Features in Abhängigkeit Ihrer eigenen Anwendungsanforderungen möglicherweise vorteilhafter oder weniger vorteilhaft sind.

| Funktion                              | Hypervisoren                                                  | Container                                                                                |
|---------------------------------------|---------------------------------------------------------------|------------------------------------------------------------------------------------------|
| Prozessisolation                      | Mehr oder weniger abgeschlossen                               | Wenn Stamm abgerufen wird, wäre Containerhost kompromittiert                             |
| Speicher auf Datenträger erforderlich | Vollständiges Betriebssystem plus Apps                        | Nur App-Anforderungen                                                                    |
| Startdauer                            | Deutlich länger: Start des Betriebssystems plus Laden der App | Deutlich kürzer: Nur Apps müssen gestartet werden, da der Kernel bereits ausgeführt wird |
| Containerautomatisierung              | Variiert stark in Abhängigkeit von Betriebssystem und Apps    | [Docker-Image-Galerie][Docker-Image-Galerie]; andere                                                         |

Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard][Docker High Level Whiteboard] (Whiteboard auf hoher Ebene zu Docker) (in englischer Sprache).

Weitere Informationen zu Docker und zur tatsächlichen Funktionsweise finden Sie unter [Was ist Docker?][Was ist Docker?].

#### Bewährte Methoden für die Docker- und Linux-Containersicherheit

Da Container Zugriff auf den Kernel des Hostcomputers freigeben, kann schädlicher Code nicht nur auf den Hostcomputer, sondern auch auf die anderen Container Zugriff erlangen. Um die Sicherheit Ihres Containersystems stark im Vergleich zur Standardkonfiguration zu verbessern, [empfiehlt Docker][empfiehlt Docker], Gruppenrichtlinien oder [rollenbasierte Sicherheit][rollenbasierte Sicherheit] wie von [SELinux][SELinux] oder [AppArmor][AppArmor] zu verwenden und die den Containern gewährten Kernelfähigkeiten so weit wie möglich zu reduzieren. Darüber hinaus gibt es im Internet zahlreiche weitere Dokumente, die Sicherheitsansätze bei der Verwendung von Containern wie Docker beschreiben.

## <span id="How to use the Docker VM Extension with Azure"></span>Verwenden der Docker-VM-Erweiterung mit Azure</a>

Die Docker-VM-Erweiterung ist eine in der Instanz des virtuellen Computers installierte Komponente, die Sie erstellen, welche das Docker-Modul installiert und die Remotekommunikation mit dem virtuellen Computer verwaltet. Es gibt zwei Möglichkeiten zum Installieren der VM-Erweiterung: Sie können Ihren virtuellen Computer mithilfe des Verwaltungsportals erstellen. Alternativ können Sie das Image über die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli) erstellen.

Sie können das Portal zum Hinzufügen der Docker-VM-Erweiterung zu kompatiblen virtuellen Linux-Computern (zurzeit ist das Ubuntu 14.04 LTS-Image das einzige Image, das es unterstützt (aktueller als Juli)) verwenden. Mit der Befehlszeile „xplat-cli“ können Sie jedoch die Docker-VM-Erweiterung installieren und Ihre Docker-Kommunikationszertifikate gleichzeitig erstellen und hochladen, wenn Sie die VM-Instanz erstellen.

Informationen zum Erstellen von Docker-fähigen virtuellen Computern finden Sie unter:

-   [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)][Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]
-   [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal][Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]

## <span id="Virtual Machine Extensions for Linux and Windows"></span>Erweiterungen für virtuelle Linux- und Windows-Computer</a>

Die Docker-VM-Erweiterung ist nur eine von mehreren VM-Erweiterungen mit besonderen Funktionen und weitere befinden sich in der Entwicklung. Beispielsweise ermöglichen es Ihnen einige Features der [Linux VM Agent-Erweiterung][Azure Linux Agent], das Abbild einschließlich Sicherheits-, Kernel- und Netzwerkfeatures zu ändern und zu verwalten. Mit der VMAccess-Erweiterung für Windows können Sie die Einstellungen des Remotedesktop-Zugriffs zurücksetzen oder ändern und das Administratorkennwort zurücksetzen.

Eine vollständige Liste finden Sie unter [Azure VM-Erweiterungen][Azure VM-Erweiterungen].

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [Linux-Container]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux Agent]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-agent-user-guide/
  [Docker und Linux-Container]: #Docker-and-Linux-Containers
  [Verwenden der Docker-VM-Erweiterung mit Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Erweiterungen für virtuelle Linux- und Windows-Computer]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-docker-with-portal/
  [Hypervisoren]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Docker-Image-Galerie]: https://registry.hub.docker.com/
  [Docker High Level Whiteboard]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Was ist Docker?]: https://www.docker.com/whatisdocker/
  [empfiehlt Docker]: https://docs.docker.com/articles/security/
  [rollenbasierte Sicherheit]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [Azure VM-Erweiterungen]: http://msdn.microsoft.com/de-de/library/azure/dn606311.aspx
