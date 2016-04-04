<properties
   pageTitle="Verwenden von ";docker-machine"; mit Azure | Microsoft Azure"
   description="Veranschaulicht das Abrufen und Ausführen auf Azure mit ";docker-machine"; auf Ubuntu unter Verwendung des klassischen Bereitstellungsmodells."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# Verwenden von "docker-machine" mit Azure

Dieses Thema beschreibt die Verwendung von [Docker](https://www.docker.com/) mit [Machine](https://github.com/docker/machine) und der [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-xplat-cli) (Azure-CLI) zum schnellen Erstellen eines virtuellen Azure-Computers, um Linux-Container von einem Ubuntu-Computer aus schnell und einfach zu verwalten. Zur Demonstration zeigt das Lernprogramm, wie die Docker Hub-Images [busybox](https://registry.hub.docker.com/_/busybox/) und [nginx](https://registry.hub.docker.com/_/nginx/) bereitgestellt werden und der Container zum Routen von Webanforderungen an den nginx-Container konfiguriert wird. (Die Docker-Dokumentation zu **machine** beschreibt, wie diese Anweisungen für andere Plattformen angepasst werden.)


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Für das Abschließen dieses Lernprogramms müssen verschiedene Voraussetzungen erfüllt sein. Sie müssen folgende Komponenten installieren:

1. [npm](https://docs.npmjs.com/) und [Node.js](http://nodejs.org/)
2. [Die Azure-CLI](https://github.com/Azure/azure-xplat-cli)
3. Einen [Docker-Client](https://docs.docker.com/installation/)

Wenn Sie diese Elemente in der angegebenen Reihenfolge installiert haben, ist Ihr Ubuntu-Computer bereit für das Lernprogramm. (Das Lernprogramm gilt in großen Teilen auch für andere dpkg-basierte Distributionen, wie z. B. Debian. Falls zusätzliche Anforderungen oder Schritte gelten, die hier fehlen, hinterlassen Sie einen Kommentar.)

## Abrufen oder Erstellen von "docker-machine"

Die schnellste Methode zum Einstieg mit **docker-machine** besteht darin, die geeignete Version direkt von der Seite mit der [freigegebenen Version](https://github.com/docker/machine/releases) herunterzuladen. Der Clientcomputer in diesem Lernprogramm führt Ubuntu auf einem x64-Computer aus, weshalb das Image **docker-machine\_linux-amd64**4 verwendet wird.

Sie können Ihre **docker-machine** auch selbst erstellen, indem Sie die Schritte unter [Beitragen zu "machine"](https://github.com/docker/machine#contributing) (in englischer Sprache) ausführen. Hierfür ist ein Download von 1 GB oder mehr erforderlich, anschließend können Sie aber genau die gewünschten Anpassungen vornehmen.

> [AZURE.NOTE] Sie können auch einen [symbolischen Link](http://en.wikipedia.org/wiki/Symbolic_link) zu Ihrer Plattformversion erstellen. In diesem Lernprogramm wird jedoch der binäre Name direkt verwendet, um das Verhalten klar zu verdeutlichen. Daher wird in diesem Lernprogramm anstelle von Befehlen wie `docker-machine env`, die in der Dokumentation zu **docker-machine** gezeigt werden, `docker-machine_linux-amd64 env` verwendet. Es liegt an Ihnen, ob Sie einen symbolischen Link oder den binären Namen direkt verwenden. Wenn Sie jedoch den verwendeten Namen ändern, müssen Sie daran denken, den Namen in den Anweisungen unten zu ändern.

<br />

>  Unabhängig von der gewählten Methode müssen Sie den binären Namen entweder direkt in der Befehlszeile aufrufen oder m Pfad platzieren, z. B. **/usr/local/bin**. Denken Sie daran, den Namen als ausführbar zu markieren, indem Sie `chmod +x` &lt;*`binaryName`*&gt; eingeben. Hierbei steht &lt;*`binaryName`*&gt; für den Namen der ausführbaren Datei des Docker-Computers. In diesem Lernprogramm wird **docker-machine\_linux-amd64** verwendet.

## Erstellen Sie die Zertifikat- und Schlüsseldateien für Docker, "machine" und Azure.

Erstellen Sie jetzt die Zertifikat- und Schlüsseldateien, die Azure zum Bestätigen Ihrer Identität und Berechtigungen benötigt und die **docker-machine** für die Kommunikation mit Ihrem virtuellen Azure-Computer zur Remoteerstellung und -verwaltung von Containern verwendet. Wenn diese Dateien bereits in einem Verzeichnis vorliegen -- beispielsweise zur Verwendung mit Docker --können Sie diese wiederverwenden. Die bewährte Methode zum Testen von **docker-machine** ist jedoch, die Dateien in einem separaten Verzeichnis zu erstellen und "docker-machine" darauf zu verweisen.

> [AZURE.NOTE] Wenn Sie **docker-machine** wiederholt verwenden, achten Sie darauf, auch dieselben Zertifikat- und Schlüsseldateien zu benutzen. **docker-machine** erstellt auch eine Reihe von Clientzertifikaten. Die erstellten Dokumente finden Sie unter `~/.docker/machine`. Wenn Sie diese Zertifikate auf einen anderen Computer verschieben, müssen Sie die Zertifikatordner für **docker-machine** ebenfalls verschieben. Dies ist wichtig, wenn Sie **docker-machine** auf einer anderen Plattform verwenden möchten, beispielsweise um zu sehen, wie alles funktioniert.

Wenn Sie Erfahrung mit Linux-Distributionen haben, stehen diese Dateien möglicherweise schon auf Ihrem Computer zur Verfügung. Diese Schritte werden in der [Docker-HTTPS-Dokumentation](https://docs.docker.com/articles/https/) beschrieben. Nachfolgend finden Sie die einfachste Form dieses Schritts.

1. Erstellen Sie einen lokalen Ordner, wechseln Sie an der Befehlszeile zu diesem Ordner, und geben Sie Folgendes ein:

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Geben Sie hier das Exportkennwort für Ihr Zertifikat ein, und speichern Sie es für die zukünftige Verwendung. Geben Sie anschließend Folgendes ein:

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Laden Sie die CER-Datei für Ihr Zertifikat nach Azure hoch. Klicken Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) links unten im Dienstbereich auf **Einstellungen** (siehe unten),

	![][portalsettingsitem]

	und klicken Sie dann auf **Verwaltungszertifikate**:

	![][managementcertificatesitem]

	Klicken Sie dann (im unteren Seitenbereich) auf **Hochladen** ![][uploaditem], um die im vorherigen Schritt erstellte Datei **mycert.cer** hochzuladen.

3. Klicken Sie im selben Fensterbereich **Einstellungen** im Portal auf **Abonnements**, und notieren Sie die Abonnement-ID zur Erstellung Ihrer VM, da Sie diese im nächsten Schritt benötigen. (Sie können die Abonnement-ID auch über die Befehlszeile mit dem Azure-CLI-Befehl `azure account list` ermitteln, der die Abonnement-ID für jedes Abonnement in Ihrem Konto anzeigt.)

4. Erstellen Sie mit dem Befehl **docker-machine create** eine Docker-Host-VM auf Azure. Für den Befehl ist die Abonnement-ID erforderlich, die Sie im vorherigen Schritt notiert haben, sowie der Pfad zur **PEM**-Datei, die Sie in Schritt 1 erstellt haben. In diesem Thema wird "machine-name" als Azure-Clouddienst (und Name für Ihre VM) verwendet, aber Sie sollten diesen durch einen Namen Ihrer Wahl ersetzen und daran denken, Ihren Clouddienstnamen in allen weiteren Schritten zu verwenden, die den VM-Namen erfordern. (Denken Sie daran, dass es sich hier um ein Beispiel handelt. Wir verwenden den vollständigen binären Namen und keinen symbolischen Link für **docker-machine**.)

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	Da die ersten zwei Schritte die Erstellung einer neuen VM, das Laden der Linux Azure-Agents und das Aktualisieren der neuen VM erfordern, sollten Sie einen ähnlichen Code wie den folgenden sehen.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE] Da eine VM erstellt wird, kann es ein paar Minuten dauern, bis diese betriebsbereit ist. Während Sie warten, können Sie den Status des neuen Docker-Hosts prüfen, indem Sie über die Azure-CLI `azure vm list` eingeben, bis Ihre VMs mit dem Status **ReadyRole** angezeigt werden.

5. Legen Sie die Umgebungsvariablen für "docker" und "machine" für die Terminalsitzung fest. In der letzten Infozeile wird die sofortige Ausführung des Befehls **env** empfohlen, um die Umgebungsvariablen zu exportieren, die zur direkten Verwendung Ihres Docker-Clients mit einem spezifischen Computer erforderlich sind.

		$(docker-machine_linux-amd64 env machine-name)

	Wenn Sie dies getan haben, ist kein weiterer spezieller Befehl erforderlich, um Ihren lokalen Docker-Client zur Verbindungsherstellung mit der VM zu nutzen, die mit dem Docker-Befehl **machine** erstellt wurde.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE] In diesem Lernprogramm wird über **docker-machine** eine VM erstellt. Sie können die Schritte jedoch wiederholen, um so viele virtuelle Computer wie gewünscht zu erstellen. Wenn mehrere VMs erstellt werden sollen, können Sie mit Docker am besten zwischen VMs wechseln, indem Sie den Befehl **env** als Inlinefunktion verwenden, um die **Docker**-Umgebungsvariablen für jeden einzelnen Befehl zu setzen. Um beispielsweise **docker info** mit einer anderen VM zu verwenden, können Sie `docker $(docker-machine env <VM name>) info` eingeben, damit der Befehl **env** die Docker-Verbindungsinformationen zur Verwendung mit dieser VM einfügt.

## Fertig. Wir führen jetzt einige Anwendungen mithilfe von Docker und Images aus dem Docker Hub aus.

Sie können Docker jetzt in der normalen Weise zum Erstellen einer Anwendung im Container verwenden. Die einfachste Demonstration ist [busybox](https://registry.hub.docker.com/_/busybox/):

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

Aber vielleicht möchten Sie eine Anwendung erstellen, die sofort im Internet sichtbar ist, wie beispielsweise [nginx](https://registry.hub.docker.com/_/nginx/) aus dem [Docker-Hub](https://registry.hub.docker.com/).

> [AZURE.NOTE] Denken Sie daran, die Option **-P** zu verwenden, damit **docker** dem Image nach dem Zufallsprinzip Ports zuweist. Stellen Sie zudem mit **-d** sicher, dass der Container kontinuierlich im Hintergrund ausgeführt wird. (Wenn Sie dies vergessen, wird nginx gestartet und sofort wieder beendet. Also nicht vergessen!)

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

Erstellen Sie zur Anzeige im Internet für die Azure-VM einen öffentlichen Endpunkt auf Port 80, und ordnen Sie diesen Port dem nginx-Containerport zu. Verwenden Sie zunächst `docker ps -a`, um den Ordner zu ermitteln und zu prüfen, welche Ports **Docker** dem Containerport 80 zugewiesen hat. (Die nachstehend Informationen sind bearbeitet, um nur die Portinformationen zu zeigen; eigentlich werden mehr Informationen angezeigt.)

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

In diesem Beispiel hat Docker den Containerport 80 dem VM-Port 49153 zugewiesen. Jetzt kann mithilfe der Azure-CLI der öffentliche Port 80 des externen Clouddienstes dem Port 49153 auf der VM zugeordnet werden. Docker stellt anschließend sicher, dass eingehender TCP-Datenverkehr auf VM-Port 49153 an den nginx-Container geroutet wird.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

Öffnen Sie Ihren bevorzugten Browser, und schauen Sie es sich an.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
Rufen Sie das [Docker-Benutzerhandbuch](https://docs.docker.com/userguide/) auf, und erstellen Sie Anwendungen auf Microsoft Azure. Oder schauen Sie sich [Verwenden von Docker mit Swarm] an, um zu sehen, wie [Swarm](https://github.com/docker/swarm) mit Docker und Azure verwendet werden kann.

<!--Image references-->
[nginx]: ./media/virtual-machines-linux-classic-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-linux-classic-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-linux-classic-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-linux-classic-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Verwenden von Docker mit Swarm]: virtual-machines-linux-docker-swarm.md

<!---HONumber=AcomDC_0323_2016-->