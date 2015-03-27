<properties 
   pageTitle="Verwenden von "docker-machine" mit Azure" 
   description="Veranschaulicht das Abrufen und Ausführen auf Azure mit "docker-machine" auf Ubuntu." 
   services="virtual-machines" 
   documentationCenter="virtual-machines" 
   authors="squillace" 
   manager="timlt" 
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="n/a"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure" 
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# Verwenden von "docker-machine" mit Azure

Dieses Thema beschreibt die Verwendung von [Docker](https://www.docker.com/) mit [machine](https://github.com/docker/machine) und der [plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli)](https://github.com/Azure/azure-xplat-cli) zum schnellen Erstellen einer virtuellen Azure-VM, um Linux-Container von einem Computer mit Ubuntu aus schnell und einfach zu verwalten. Zur Demonstration zeigt das Lernprogramm, wie sowohl das [Docker Hub-Image "busybox"](https://registry.hub.docker.com/_/busybox/) als auch das [Docker Hub-Image "nginx"](https://registry.hub.docker.com/_/nginx/) bereitgestellt werden und der Container zum Routen von Webanforderungen an den nginx-Container konfiguriert wird. (Die Docker-Dokumentation zu **machine** beschreibt, wie diese Anweisungen für andere Plattformen angepasst werden.)

Für das Abschließen dieses Lernprogramms müssen verschiedene Voraussetzungen erfüllt sein. Sie müssen folgende Komponenten installieren:

1. [npm](https://docs.npmjs.com/) und [Node.js](http://nodejs.org/)
2. [Die Azure xplat-cli](https://github.com/Azure/azure-xplat-cli)
3. Einen [Docker-Client](https://docs.docker.com/installation/)

Wenn Sie diese Elemente in der angegebenen Reihenfolge installiert haben, ist Ihr Ubuntu-Computer bereit für das Lernprogramm. (Das Lernprogramm gilt in großen Teilen auch für andere dpkg-basierte Distributionen, wie z. B. Debian. Falls zusätzliche Anforderungen oder Schritte gelten, die hier fehlen, hinterlassen Sie einen Kommentar.)

## Abrufen oder Erstellen von "docker-machine"

Die schnellste Methode zum Einstieg mit **docker-machine** besteht darin, die geeignete Version direkt von der Seite mit[freigegebenen Versionen](https://github.com/docker/machine/releases) herunterzuladen. Der Clientcomputer in diesem Lernprogramm führt Ubuntu auf einem x64-Computer aus, deshalb wird das Image **docker-machine_linux-amd64** verwendet.

Sie können Ihre **docker-machine** auch selbst erstellen, indem Sie die Schritte unter [Beitragen zu "machine"](https://github.com/docker/machine#contributing) (in englischer Sprache) ausführen. Hierfür ist ein Download von 1 GB oder mehr erforderlich, anschließend können Sie aber genau die gewünschten Anpassungen vornehmen.

> [AZURE.NOTE] Es ist ebenfalls möglich, einen [symbolischen Link](http://en.wikipedia.org/wiki/Symbolic_link) zu Ihrer Plattformversion zu erstellen, aber in diesem Lernprogramm wird der binäre Name direkt verwendet, um das Verhalten klar zu verdeutlichen. Als Ergebnis wird anstelle von Befehlen wie `docker-machine env`, die in der Dokumentation zu **docker-machine** gezeigt werden, in diesem Lernprogramm `docker-machine_linux-amd64 env` verwendet. Es liegt an Ihnen, ob Sie einen symbolischen Link oder den binären Namen direkt verwenden. Wenn Sie jedoch den verwendeten Namen ändern, müssen Sie daran denken, den Namen in den Anweisungen unten zu ändern.

<br />

>  Unabhängig von der gewählten Methode müssen Sie entweder den binären Namen direkt in der Befehlszeile aufrufen oder den binären Namen im Pfad platzieren, z. B. **/usr/local/bin**. Denken Sie daran, den Namen als ausführbar zu markieren, indem Sie `chmod +x` &lt;*`binaryName`*&gt; eingeben. Hierbei steht &lt;*`binaryName`*&gt; für den Namen der ausführbaren Docker-Maschine. In diesem Lernprogramm wird **docker-machine_linux-amd64** verwendet.

## Erstellen Sie die Zertifikat- und Schlüsseldateien für Docker, "machine" und Azure.

Jetzt müssen Sie die Zertifikat- und Schlüsseldateien erstellen, die Azure zum Bestätigen Ihrer Identität und Berechtigungen und **docker-machine** zur Kommunikation mit Ihrer Azure-VM für die Remoteerstellung und -verwaltung von Containern benötigt. Wenn diese Dateien bereits in einem Verzeichnis vorliegen -- beispielsweise zur Verwendung mit Docker --können Sie diese wiederverwenden. Die bewährte Methode zum Testen von **docker-machine** ist jedoch, die Dateien in einem separaten Verzeichnis zu erstellen und "docker-machine" darauf zu verweisen. 

> [AZURE.NOTE] Wenn Sie **docker-machine** mehrfach testen müssen, sollten Sie dieselben Zertifikat- und Schlüsseldateien wiederverwenden. **docker-machine** erstellt auch einen Satz Clientzertifikate -- alle erstellten Elemente können in`~/.docker/machine` überprüft werden. Wenn Sie diese Zertifikate auf einen anderen Computer verschieben, müssen Sie die Zertifikatordner für **docker-machine** ebenfalls verschieben. Dies ist wichtig, wenn Sie **docker-machine** auf einer anderen Plattform verwenden möchten, beispielsweise um zu sehen, wie alles funktioniert.

Wenn Sie Erfahrung mit Linux-Distributionen haben, stehen diese Dateien möglicherweise schon auf Ihrem Computer zur Verfügung, und diese Schritte werden in der [Docker-HTTPS-Dokumentation](https://docs.docker.com/articles/https/) gut beschrieben. Nachfolgend finden Sie die einfachste Form dieses Schritts.

1. Erstellen Sie einen lokalen Ordner, wechseln Sie an der Befehlszeile zu diesem Ordner, und geben Sie Folgendes ein:

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	Be ready here to enter the export password for your certificate and capture it for future usage. Then type:

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. Laden Sie die CER-Datei für Ihr Zertifikat nach Azure hoch. Klicken Sie im [Azure-Portal](https://manage.windowsazure.com), links unten im Dienstbereich auf **Einstellungen** (siehe unten),

	![][portalsettingsitem]

	und klicken Sie dann auf **Verwaltungszertifikate**: 

	![][managementcertificatesitem]

	Klicken Sie dann (im unteren Seitenbereich) auf **Hochladen** ![][uploaditem], um die im vorherigen Schritt erstellte Datei **mycert.cer** hochzuladen.

3. Klicken Sie im selben Fensterbereich **Einstellungen** im Portal auf **Abonnements**, und notieren Sie die Abonnement-ID zur Erstellung Ihrer VM, da Sie diese im nächsten Schritt benötigen. (Sie können die Abonnement-ID auch über die Befehlszeile mit dem xplat-cli-Befehl `azure account list` ermitteln, der die Abonnement-ID für jedes Abonnement in Ihrem Konto anzeigt.) 

4. Erstellen Sie mit dem Befehl **docker-machine create** eine Docker-Host-VM auf Azure. Für den Befehl ist die Abonnement-ID erforderlich, die Sie im vorherigen Schritt notiert haben, sowie der Pfad zur **PEM**-Datei, die Sie in Schritt 1 erstellt haben. In diesem Thema wird "machine-name" als Azure-Cloud-Dienst (und Name für Ihre VM) verwendet, aber Sie sollten diesen durch einen Namen Ihrer Wahl ersetzen und daran denken, Ihren Cloud-Dienstnamen in allen weiteren Schritten zu verwenden, die den VM-Namen erfordern. (Denken Sie daran, dass es sich hier um ein Beispiel handelt. Wir verwenden den vollständigen binären Namen und keinen symbolischen **docker-machine**-Link.)

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

    > [AZURE.NOTE] Da eine VM erstellt wird, kann es ein paar Minuten dauern, bis diese betriebsbereit ist. Während Sie warten, können Sie den Status des neuen Docker-Hosts prüfen, indem Sie über die xplat-cli  `azure vm list` eingeben, bis Ihre VMs mit dem Status **ReadyRole** angezeigt werden. 

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

> [AZURE.NOTE] In diesem Lernprogramm wird über **docker-machine** eine VM erstellt. Sie können die Schritte jedoch wiederholen, um so viele virtuelle Computer wie gewünscht zu erstellen. Wenn mehrere VMs erstellt werden sollen, können Sie mit Docker am besten zwischen VMs wechseln, indem Sie den Befehl **env** als Inlinefunktion verwenden, um die **docker**-Umgebungsvariablen für jeden einzelnen Befehl zu setzen. Um beispielsweise **docker info** mit einer anderen VM zu verwenden, können Sie `docker $(docker-machine env <VM name>) info` eingeben, damit der **env**-Befehl die Docker-Verbindungsinformationen zur Verwendung mit dieser VM einfügt.

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

Aber vielleicht möchten Sie eine Anwendung erstellen, die sofort im Internet sichtbar ist, wie beispielsweise [nginx](https://registry.hub.docker.com/_/nginx/) aus dem [Docker Hub](https://registry.hub.docker.com/). 

> [AZURE.NOTE] Danken Sie daran, die Option **-P** zu verwenden, damit **docker** dem Image zufällige Ports zuweist. Verwenden Sie außerdem **-d** um sicherzustellen, dass der Container kontinuierlich im Hintergrund ausgeführt wird. (Wenn Sie dies vergessen, wird nginx gestartet und sofort wieder beendet. Also nicht vergessen!)

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

Erstellen Sie zur Anzeige im Internet für die Azure-VM einen öffentlichen Endpunkt auf Port 80, und ordnen Sie diesen Port dem nginx-Containerport zu. Verwenden Sie zunächst `docker ps -a`, um den Ordner zu ermitteln und zu prüfen, welche Ports **docker** dem Containerport 80 zugewiesen hat. (Die nachstehend Informationen sind bearbeitet, um nur die Portinformationen zu zeigen; eigentlich werden mehr Informationen angezeigt.)

	$ docker ps -a
    IMAGE               PORTS                                           
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp   
    busybox:latest                                                      
 
In diesem Beispiel hat Docker den Containerport 80 dem VM-Port 49153 zugewiesen. Jetzt kann mithilfe der xplat-cli der öffentliche Port 80 für den externen Cloud-Dienst Port 49153 auf der VM zugeordnet werden. Docker stellt anschließend sicher, dass eingehender TCP-Datenverkehr auf VM-Port 49153 an den nginx-Container geroutet wird. 

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
Wechseln Sie zum [Docker-Benutzerhandbuch](https://docs.docker.com/userguide/), und erstellen Sie einige Anwendungen auf Microsoft Azure. Oder probieren Sie [**docker** und [swarm](https://github.com/docker/swarm) auf Azure](https:azure.microsoft.com/documentation/articles/virtual-machines-docker-swarm/) aus, um zu sehen, wie "swarm" mit "docker" und Azure verwendet werden kann.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references--In actual articles, you only need a single period before the slash.-->
[Link 1 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../virtual-machines-windows-tutorial/
[Link 2 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../web-sites-custom-domain-name/
[Link 3 zu einem anderen Thema der azure.microsoft.com-Dokumentation]: ../storage-whatis-account/

<!--HONumber=47-->
