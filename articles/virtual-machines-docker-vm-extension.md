<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com"></tags>

# Verwenden der Docker-Erweiterung für virtuelle Linux-Computer in Azure

[Docker][] ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container][] statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung des [Azure Linux Agent][], um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet.

Dieses Thema folgt dieser Ankündigung im [MS Open Tech-Blog][] und beschreibt:

-   [Docker und Linux-Container][]
-   [Verwenden der Docker-VM-Erweiterung mit Azure][]
-   [Erweiterungen für virtuelle Linux- und Windows-Computer][]
-   [Container und Container-Verwaltungsressourcen für Azure][]

## Docker und Linux-Container

[Docker][] ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container][] statt virtueller Computer verwendet und andere Möglichkeiten zum schnellen Erstellen oder Zusammensetzen von Anwendungen und Verteilen dieser zwischen anderen Docker-Containern bietet.

Docker- und Linux-Container sind keine [Hypervisoren][] wie Windows Hyper-V und [KVM][] auf Linux, um nur zwei Beispiele zu nennen. Hypervisoren virtualisieren das zugrunde liegende Betriebssystem, sodass das komplette Betriebssystem im Hypervisor ausgeführt werden kann, als wäre es eine Anwendung.

Der Hypervisor-Ansatz bietet wichtige Sicherheitsvorteile, da der virtuelle "Gast"-Computer keinen Zugriff auf das "Host"-Betriebssystem hat, sondern nur die Ressourcen des Hypervisors nutzen darf. Zu den Nachteilen gehören jedoch eine umfangreiche Verarbeitung und Speicherbelastung sowie längere Startzeiten für neue virtuelle Computer, da sie unter anderem das Gast-Betriebssystem vollständig replizieren.

#### Docker und Linux-Container

Docker und andere *Container*-Ansätze haben die erforderlichen Startzeiten, Verarbeitung und Speicherbelastung enorm verringert. Sie nutzen die Isolationsfunktionen des Linux Kernels für Prozesse und Dateisysteme, um nur Kernel-Funktionen an einen ansonsten isolierten Container zur Verfügung zu stellen. Im Container erscheinen das Dateisystem und die Kernel-Funktionen gegenüber der Anwendung so, als wäre sie die einzige Anwendung, die ausgeführt wird.

Darüber hinaus bietet Docker verschiedene Container-Verwaltungsfunktionen, mit denen Sie mehrere Container-Abbilder aus der Community laden und Ihren eigenen Container schnell erstellen und laden können. Weitere Informationen zu Docker finden Sie unter [Was ist Docker?][].

Wie bei den meisten Technologien gibt es auch hier wesentliche Vorteile und einige Nachteile. Da Container Zugriff auf den Kernel des Hostcomputers freigeben, kann schädlicher Code nicht nur auf den Hostcomputer, sondern auch auf die anderen Container Zugriff erlangen. Um die Sicherheit Ihres Containersystems zu verbessern, [empfiehlt Docker][], Gruppenrichtlinien oder [rollenbasierte Sicherheit][] wie bspw. von [SELinux][] oder [AppArmor][] zu verwenden und die den Containern gewährten Kernelfähigkeiten so weit wie möglich zu reduzieren. Darüber hinaus gibt es im Internet zahlreiche weitere Dokumente, die Sicherheitsansätze bei der Verwendung von Containern wie Docker beschreiben.

## Verwenden der Docker-VM-Erweiterung mit Azure

Um die Docker-VM-Erweiterung mit Azure verwenden zu können, müssen Sie eine höhere Version der [azure-cli][] als 0.8.6 installieren (Bei der Erstellung dieses Dokuments ist die aktuelle Version 0.8.7). Sie können die azure-cli auf Mac und Linux installieren.

> [WACOM.NOTE] Sie können die azure-cli auch auf Microsoft Windows installieren. Da Docker jedoch mit Linux-Kernel-Abhängigkeiten erstellt wurde, müssen Sie eine volle Linux-Distribution als virtuellen Computer in Hyper-V oder einem anderen Hypervisor hosten, um Windows als Docker-Client zu verwenden. Dann können Sie die azure-cli und die Docker-Befehle in diesem Dokument und die Befehle von Docker verwenden. Um diesen Setup zu automatisieren, können Sie auch das Setupprogramm [Boot2Docker][] von Docker verwenden.

Die Verwendung von Docker auf Azure ist einfach:

-   Installieren Sie die azure-cli-Befehlszeilentools und ihre Abhängigkeiten auf dem Computer, von dem aus Sie Azure steuern möchten (in Windows ist dies eine Linux-Distribution, die als virtueller Computer ausgeführt wird).
-   Verwenden Sie die azure-cli Docker-Befehle, um einen Docker-VM-Host in Azure zu erstellen.
-   Verwenden Sie die lokalen Docker-Befehle, um die Docker-Container im virtuellen Docker-Computer in Azure zu verwalten.

> [WACOM.NOTE] Die azure-cli (Command Line Interface, Befehlszeilenschnittstelle) ist derzeit die einzige Möglichkeit zum Erstellen eines Docker-gesteuerten virtuellen Computers auf Azure, um Docker-Container zu hosten. Die allgemeine Installationsanleitung finden Sie [hier][]; in den folgenden Abschnitten geben wir Ihnen einige zusätzliche Hinweise für eine reibungslose Installation auf verschiedenen Betriebssystemen.

### Installieren der azure-cli auf Linux

In Linux erfordert die Installation der azure-cli den [Node Package Manager (npm)][], für den Sie nodejs benötigen. Verwenden Sie je nach gewählter Plattform Ihren bevorzugten Paket-Manager, um nodejs zu installieren. Wenn sie npm installiert haben, geben Sie das Folgende ein, um das azure-cli-Paket abzurufen:

    npm install -g azure-cli

Dadurch wird das azure-cli-Paket global installiert. Um die Installation zu bestätigen, geben Sie `azure` an der Eingabeaufforderung ein, nach wenigen Sekunden sollte die azure-cli ASCII Art angezeigt werden, die die verfügbaren grundlegenden Befehle auflistet. War die Installation erfolgreich, sollten Sie bei Eingabe von `azure help vm` unter den aufgelisteten Befehlen "docker" finden.

> [WACOM.NOTE] Wenn Sie eine Ubuntu 14.04 LTS-Installation verwenden, weicht die Knoteninstallation für dieses Abbild geringfügig ab und erfordert ggf. zusätzlichen Aufwand. Ein Möglichkeit ist die direkte Installation der neuesten Version von nodejs, die auf Ubuntu 14.04 LTS-Distributionen gut zu funktionieren scheint. Sie finden Sie [hier][1] im Abschnitt **Installieren mithilfe eines PPA**.

Alternativ können Sie wie bei den meisten Linux-Komponenten die Quelle klonen, kompilieren und ebenfalls lokal installieren. Die Anweisungen hierzu finden Sie unter [][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a>.

### Installieren der azure-cli auf Mac

Auf einem Mac ist es am einfachsten, npm mit dem folgenden Befehl zu verwenden: `npm install -g azure-cli`. Sie können jedoch auch den [Mac Installer][] verwenden. Wie bei Linux und Windows können Sie auch hier `azure` an der dazugehörigen Eingabeaufforderung eingeben und bestätigen, dass die azure-cli installiert ist.

### Verknüpfen der azure-cli mit Ihrem Azure-Konto

Bevor Sie die azure-cli nutzen können, müssen Sie Ihre Azure-Anmeldeinformationen mit der azure-cli auf der Plattform verknüpfen. Im Abschnitt [Verbindung mit Ihrem Azure-Abonnement herstellen][] wird erklärt, wie Sie Ihre **.publishsettings**-Datei herunterladen und importieren oder die azure-cli-Befehlszeile mit einer Organisations-ID verknüpfen. Die Schritte für beide Methoden der Authentifizierung und Autorisierung sind in diesem Dokument beschrieben.

> [WACOM.NOTE] Beide Methoden der Authentifizierung verhalten sich unterschiedlich. Lesen Sie daher unbedingt das genannte Dokument, um die Unterschiede zu verstehen.

### Installieren und Verwenden der Docker-VM-Erweiterung für Azure

Die azure-cli ist nun auf Ihrem Computer (oder einem virtuellen Computer auf diesem) installiert und mit Ihrem Azure-Konto verknüpft. Folgen Sie den [Installationsanweisungen für Docker][], um Docker auf Ihrem Computer lokal zu installieren. Bei den meisten Betriebssystemen und Distributionen geben Sie dazu `apt-get install docker.io` ein. Bestätigen Sie, dass es sich um die Docker-Version 1.0 oder höher handelt.

Sie haben nun die azure-cli installiert, mit Ihrem Azure-Konto verknüpft und Docker installiert. Um einen neuen virtuellen Docker-Hostcomputer in Azure zu installieren, benötigen Sie ein Linux-Abbild, auf dem der [Azure Linux VM Agent][Azure Linux Agent] installiert ist. Derzeit ist dieser nur auf

-   Ubuntu-Abbildern aus der Image-Galerie oder

-   benutzerdefinierten Linux-Abbildern, die mit dem Azure Linux VM Agent installiert und konfiguriert wurden, installiert. Weitere Informationen zum Erstellen eines benutzerdefinierten virtuellen Linux-Computers mit dem Azure VM Agent finden Sie unter [Azure Linux VM Agent][Azure Linux Agent].

Suchen Sie mit der azure-cli-Eingabeaufforderung nach dem aktuellsten Ubuntu-Abbild in der VM-Galerie, indem Sie

`azure vm image list | grep Ubuntu-14_04`

eingeben und kopieren Sie den Namen des aktuellsten Abbilds. Geben Sie an der Eingabeaufforderung Folgendes ein:

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-en-us-30GB" <username> <password>

Hinweis:

-   *<vm-cloudservice name>* ist der Name des virtuellen Computers, der als Hostcomputer für die Docker-Container in Azure dienen wird.

-   *<username>* ist der Benutzername des Standard-Root-Benutzers des virtuellen Computers

-   *<password>* ist das Kennwort des *username*-Kontos, das den Komplexitätsanforderungen für Azure entspricht.

> [WACOM.NOTE] Derzeit muss ein Kennwort aus mindestens 8 Zeichen bestehen und dabei einen Kleinbuchstaben, einen Großbuchstaben, eine Ziffer und ein Sonderzeichen wie [!@\#$%^&+=][] enthalten. Der Punkt am Ende des vorhergehenden Satzes zählt nicht zu den Sonderzeichen.

War die Eingabe erfolgreich, sollte Ihnen in etwa das Folgende angezeigt werden, abhängig von den genauen Argumenten und Optionen, die Sie verwendet haben:

![][]

> [WACOM.NOTE] Wie bereits erwähnt kann das Erstellen eines virtuellen Computers einige Minuten dauern. Nach der Bereitstellung wird der Daemon jedoch gestartet und Sie können sich mit dem Host der Docker-Container verbinden.

Um den virtuellen Docker-Computer, den Sie in Azure erstellt haben, zu testen, geben Sie Folgendes ein:

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

*<vm-name-you-used>* ist der Name des virtuellen Computers, den Sie in Ihrem Aufruf von `azure vm docker create` verwendet haben. Abhängig von den genauen Argumenten und Optionen, die Sie verwendet haben, sollte Ihnen in etwa das Folgende angezeigt werden. Daran sehen Sie, dass Ihr virtueller Docker-Hostcomputer in Azure ausgeführt wird und auf Ihre Eingaben wartet.

![][2]

## Hinweis zur Authentifizierung des virtuellen Docker-Hostcomputers

Zusätzlich zur Erstellung des virtuellen Docker-Computers erstellt der Befehl `azure vm docker create` automatisch auch die nötigen Zertifikate, um Ihrem Docker-Clientcomputer über HTTPS die Verbindung mit dem Azure-Containerhost zu ermöglichen. Die Zertifikate werden sowohl auf dem Client- als auch auf dem Hostcomputer gespeichert. Bei den nachfolgenden Ausführungen werden die Zertifikate erneut verwendet und an den neuen Host freigegeben.

Standardmäßig werden die Zertifikate unter `~/.docker` abgelegt, und Docker wird für die Ausführung auf Port **4243** konfiguriert. Wenn Sie einen anderen Port oder ein anderes Verzeichnis verwenden möchten, dann können Sie eine der folgenden `azure vm docker create`-Befehlszeilenoptionen verwenden, damit der virtuelle Docker-Containerhost einen anderen Port oder andere Zertifikate für Clients, von denen eine Verbindung hergestellt wird, verwendet:

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

Der Docker-Daemon auf dem Host ist so konfiguriert, dass er Clientverbindungen auf dem angegebenen Port mithilfe der durch den Befehl `azure vm docker create` generierten Zertifikate abhört und authentifiziert. Der Clientcomputer muss über diese Zertifikate verfügen, um Zugriff auf den Docker-Host zu erlangen.

> [WACOM.NOTE] Umgekehrt ist ein vernetzter Host ohne diese Zertifikate anfällig für jeden, der sich mit dem Computer verbindet. Bevor Sie die Standardkonfiguration ändern, setzen Sie sich mit den Risiken für Ihre Computer und Anwendungen auseinander.

## Nächste Schritte

Nun können Sie Docker-Befehle des [Docker-Benutzerhandbuchs][] an den virtuellen Docker-Host in Azure erteilen.

## Erweiterungen für virtuelle Linux- und Windows-Computer

Die Docker-VM-Erweiterung ist nur eine von mehreren VM-Erweiterungen mit besonderen Funktionen und weitere befinden sich in der Entwicklung. Beispielsweise ermöglichen es Ihnen einige Features der [Linux VM Agent-Erweiterung][Azure Linux Agent], das Abbild einschließlich Sicherheits-, Kernel- und Netzwerkfeatures zu ändern und zu verwalten. Mit der VMAccess-Erweiterung für Windows können Sie die Einstellungen des Remotedesktop-Zugriffs zurücksetzen oder ändern und das Administratorkennwort zurücksetzen.

Eine vollständige Liste finden Sie unter [Azure VM-Erweiterungen][].

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [Linux-Container]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux Agent]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-agent-user-guide/
  [MS Open Tech-Blog]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Docker und Linux-Container]: #Docker-and-Linux-Containers
  [Verwenden der Docker-VM-Erweiterung mit Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Erweiterungen für virtuelle Linux- und Windows-Computer]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Container und Container-Verwaltungsressourcen für Azure]: #Container-and-Container-Management-Resources-for-Azure
  [Hypervisoren]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Was ist Docker?]: https://www.docker.com/whatisdocker/
  [empfiehlt Docker]: https://docs.docker.com/articles/security/
  [rollenbasierte Sicherheit]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [hier]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#install
  [Node Package Manager (npm)]: http://en.wikipedia.org/wiki/Npm_%28software%29
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [Mac Installer]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [Verbindung mit Ihrem Azure-Abonnement herstellen]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/#configure
  [Installationsanweisungen für Docker]: https://docs.docker.com/installation/#installation
  [!@\#$%^&+=]: mailto:!@#$%^&+=
  []: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Docker-Benutzerhandbuchs]: https://docs.docker.com/userguide/
  [Azure VM-Erweiterungen]: http://msdn.microsoft.com/en-us/library/azure/dn606311.aspx
