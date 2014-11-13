<properties title="Gewusst wie: Verwenden der Docker-VM-Erweiterung mit dem Azure-Portal" pageTitle="Verwenden der Docker-VM-Erweiterung f&uuml;r Linux auf Azure" description="Beschreibt die Docker- und Azure Virtual Machines-Erweiterungen und zeigt die programmgesteuerte Erstellung von virtuellen Computern auf Azure, die Docker hostet, &uuml;ber die Befehlszeile mithilfe der &bdquo;azure-cli&ldquo;-Befehlsschnittstelle." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />
<!--The next line, with one pound sign at the beginning, is the page title-->

# Verwenden der Docker-VM-Erweiterung mithilfe des Azure-Portals

[Docker][Docker] ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsam genutzten Ressourcen [Linux-Container][Linux-Container] statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung des [Azure Linux Agent][Azure Linux Agent], um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Inhalt dieses Abschnitts

-   [Erstellen eines neuen virtuellen Computers über die Image-Galerie][Erstellen eines neuen virtuellen Computers über die Image-Galerie]
-   [Erstellen von Docker-Zertifikaten][Erstellen von Docker-Zertifikaten]
-   [Hinzufügen der Docker-VM-Erweiterung][Hinzufügen der Docker-VM-Erweiterung]
-   [Testen des Docker-Clients und des Azure Docker-Hosts][Testen des Docker-Clients und des Azure Docker-Hosts]
-   [Nächste Schritte][Nächste Schritte]

> [WACOM.NOTE] Dieses Thema beschreibt das Erstellen eines virtuellen Docker-Computers im Azure-Portal. Informationen zum Erstellen eines virtuellen Docker-Computers an der Befehlszeile finden Sie unter [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)][Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]. Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard][Docker High Level Whiteboard] (Whiteboard auf hoher Ebene zu Docker) (in englischer Sprache).

## <span id="createvm"></span>Erstellen eines neuen virtuellen Computers über die Image-Galerie</a>

Für den ersten Schritt ist ein virtueller Azure-Computer von einem Linux-Image erforderlich, das die Docker-VM-Erweiterung unterstützt, mit einem Ubuntu 14.04 LTS-Image aus der Image-Galerie als einen Beispielserver und Ubuntu 14.04 Desktop als einen Client. Klicken Sie im Portal in der unteren linken Ecke auf **+ Neu** zum Erstellen einer neuen Instanz für einen virtuellen Computer, und wählen Sie ein Ubuntu 14.04 LTS-Image aus den verfügbaren Optionen oder analog zur folgenden Darstellung aus der vollständigen Image-Galerie aus.

> [WACOM.NOTE] Zurzeit unterstützen nur Ubuntu 14.04 LTS-Images nach Juli 2014 die Docker-VM-Erweiterung.

![Erstellen eines neuen Ubuntu-Images][Erstellen eines neuen Ubuntu-Images]

## <a id'dockercerts'>Erstellen von Docker-Zertifikaten</a>

Stellen Sie nach dem Erstellen des virtuellen Computers sicher, dass der Docker auf Ihrem Clientcomputer installiert ist. (Einzelheiten finden Sie in den [Installationsanweisungen für Docker][Installationsanweisungen für Docker].)

Erstellen Sie das Zertifikat und Schlüsseldateien für die Docker-Kommunikation gemäß [Running Docker with https][Running Docker with https] (Ausführen von Docker mit HTTPS) (in englischer Sprache), und platzieren Sie sie im Verzeichnis **`~/.docker`** auf Ihrem Clientcomputer.

> [WACOM.NOTE] Für die Docker-VM-Erweiterung im Portal sind zurzeit base64-codierte Anmeldeinformationen erforderlich.

Verwenden Sie an der Befehlszeile **`base64`** oder ein anderes bevorzugtes Codierungstool zum Erstellen base64-codierter Themen. Wenn Sie dies mit ein paar einfachen Zertifikaten und Schlüsseldateien vornehmen, sieht das möglicherweise in etwa so aus:

     ~/.docker$ l
     ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
     ~/.docker$ base64 ca.pem > ca64.pem
     ~/.docker$ base64 server-cert.pem > server-cert64.pem
     ~/.docker$ base64 server-key.pem > server-key64.pem
     ~/.docker$ l
     ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
     ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem

## <a id'adddockerextension'>Hinzufügen der Docker-VM-Erweiterung</a>

Suchen Sie zum Hinzufügen der Docker-VM-Erweiterung die von Ihnen erstellte VM-Instanz, führen Sie einen Bildlauf zu **Erweiterungen** durch, um „VM-Erweiterungen“ analog zur unteren Beschreibung anzuzeigen.

![][0]

### Hinzufügen einer Erweiterung

Klicken Sie auf **+ Hinzufügen**, um die möglichen VM-Erweiterungen anzuzeigen, die Sie zu diesem virtuellen Computer hinzufügen können.

![][1]

### Auswählen der Docker-VM-Erweiterung

Wählen Sie die Docker-VM-Erweiterung aus, wodurch die Docker-Beschreibung und wichtige Links angezeigt werden, und klicken Sie dann unten auf **Erstellen**, um die Installationsprozedur zu starten.

![][2]

![][3]

### Fügen Sie Ihr Zertifikat und Schlüsseldateien hinzu:

Geben Sie in den Formularfeldern die base64-codierten Versionen Ihres ZS-Zertifikats, Ihr Serverzertifikat und Ihren Serverschlüssel ein, wie dies in der folgenden Grafik gezeigt wird.

![][4]

> [WACOM.NOTE] Beachten Sie (analog zur vorherigen Abbildung), dass 4243 standardmäßig aufgefüllt wird. Sie können hier einen beliebigen Endpunkt eingeben. Der nächste Schritt ist jedoch für den übereinstimmenden Endpunkt gedacht. Wenn Sie den Standardwert ändern, müssen Sie die Erschließung des übereinstimmenden Endpunkts im nächsten Schritt sicherstellen.

## Hinzufügen des Docker-Kommunikationsendpunkts

Führen Sie beim Anzeigen Ihres virtuellen Computers in der von Ihnen erstellten Ressourcengruppe einen Bildlauf durch, um auf **Endpunkte** zu klicken, um die Endpunkte auf dem virtuellen Computer analog zur hier aufgeführten Darstellung anzuzeigen.

![][5]

Klicken Sie zum Hinzufügen eines anderen Endpunkts auf **+ Hinzufügen**. Und geben Sie im standardmäßigen Fall einen Namen für den Endpunkt (in diesem Fall **docker**) und „4243“ die privaten und öffentlichen Ports ein. Belassen Sie den Protokollwert bei **TCP**, und klicken Sie auf **OK**, um den Endpunkt zu erstellen.

![][6]

## <span id="testclientandserver"></span>Testen des Docker-Clients und des Azure Docker-Hosts</a>

Suchen und kopieren sie den Namen der Domäne Ihres virtuellen Computers. Geben Sie zudem an der Befehlszeile Ihres Clientcomputers `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (*dockerextension* wird hierbei durch die Unterdomäne für Ihren virtuellen Computer ersetzt) ein.

Das Ergebnis sollte in etwa so aussehen:

    $ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
     Pool Name: docker-8:1-131214-pool
     Pool Blocksize: 65.54 kB
     Data file: /var/lib/docker/devicemapper/devicemapper/data
     Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
     Data Space Used: 305.7 MB
     Data Space Total: 107.4 GB
     Metadata Space Used: 729.1 kB
     Metadata Space Total: 2.147 GB
     Library Version: 1.02.82-git (2013-10-04)
    Execution Driver: native-0.2
    Kernel Version: 3.13.0-36-generic
    WARNING: No swap limit support

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Nächste Schritte

Sie sind nun bereit, das [Docker-Benutzerhandbuch][Docker-Benutzerhandbuch] und Ihren virtuellen Docker-Computer zu verwenden. Wenn Sie mit dem schnellen und wiederholten Erstellen von virtuellen Docker-Computern auf Azure beginnen möchten, finden Sie unter [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)][Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)] entsprechende Informationen.

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Docker]: https://www.docker.com/
  [Linux-Container]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux Agent]: ../virtual-machines-linux-agent-user-guide/
  [Erstellen eines neuen virtuellen Computers über die Image-Galerie]: #createvm
  [Erstellen von Docker-Zertifikaten]: #dockercerts
  [Hinzufügen der Docker-VM-Erweiterung]: #adddockerextension
  [Testen des Docker-Clients und des Azure Docker-Hosts]: #testclientandserver
  [Nächste Schritte]: #next-steps
  [Gewusst wie: Verwenden der Docker-VM-Erweiterung über die plattformübergreifende Azure-Oberfläche (xplat-cli)]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Docker High Level Whiteboard]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Erstellen eines neuen Ubuntu-Images]: ./media/virtual-machines-docker-with-portal/ChooseUbuntu.png
  [Installationsanweisungen für Docker]: https://docs.docker.com/installation/#installation
  [Running Docker with https]: http://docs.docker.com/articles/https/
  [0]: ./media/virtual-machines-docker-with-portal/ClickExtensions.png
  [1]: ./media/virtual-machines-docker-with-portal/ClickAdd.png
  [2]: ./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png
  [3]: ./media/virtual-machines-docker-with-portal/CreateButtonFocus.png
  [4]: ./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png
  [5]: ./media/virtual-machines-docker-with-portal/AddingEndpoint.png
  [6]: ./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png
  [Docker-Benutzerhandbuch]: https://docs.docker.com/userguide/
