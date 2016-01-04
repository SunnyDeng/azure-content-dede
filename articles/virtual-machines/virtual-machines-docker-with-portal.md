<properties
	pageTitle="Verwenden der Docker-VM-Erweiterung für Linux | Microsoft Azure"
	description="Beschreibt die Docker- und Azure Virtual Machines-Erweiterungen und zeigt die Erstellung von virtuellen Azure-Computern, die als Docker-Hosts verwendet werden, über die Azure-Befehlszeilenschnittstelle im klassischen Bereitstellungsmodell."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="09/22/2015"
	ms.author="rasquill"/>


# Verwenden der Docker-VM-Erweiterung mit dem klassischen Azure-Portal

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


[Docker](https://www.docker.com/) ist einer der beliebtesten Virtualisierungsansätze, der für das Isolieren von Daten und Computing auf gemeinsamen Ressourcen [Linux-Container](http://en.wikipedia.org/wiki/LXC) statt virtueller Computer verwendet. Verwenden Sie die Docker-VM-Erweiterung des [Azure Linux-Agents], um einen virtuellen Docker-Computer zu erstellen, der eine beliebige Anzahl von Containern für Ihre Anwendungen in Azure hostet.

> [AZURE.NOTE]Dieses Thema beschreibt das Erstellen eines virtuellen Docker-Computers im klassischen Azure-Portal. Informationen zum Erstellen eines virtuellen Docker-Computers an der Befehlszeile finden Sie unter [Verwenden der Docker-VM-Erweiterung über die Azure-Befehlszeilenschnittstelle (Azure-CLI)]. Eine allgemeine Diskussion über die Container und ihre Vorteile finden Sie unter [Docker High Level Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard) (Whiteboard auf hoher Ebene zu Docker) (in englischer Sprache).

## Erstellen eines neuen virtuellen Computers über den Image-Katalog
Für den ersten Schritt ist ein virtueller Azure-Computer von einem Linux-Image erforderlich, das die Docker-VM-Erweiterung unterstützt, mit einem Ubuntu 14.04 LTS-Image aus der Image-Galerie als einen Beispielserver und Ubuntu 14.04 Desktop als einen Client. Klicken Sie im Portal in der unteren linken Ecke auf **+ Neu** zum Erstellen einer neuen Instanz für einen virtuellen Computer, und wählen Sie ein Ubuntu 14.04 LTS-Image aus den verfügbaren Optionen oder analog zur folgenden Darstellung aus der vollständigen Image-Galerie aus.

> [AZURE.NOTE]Zurzeit unterstützen nur Ubuntu 14.04 LTS-Abbilder nach Juli 2014 die Docker-VM-Erweiterung.

![Erstellen eines neuen Ubuntu-Images](./media/virtual-machines-docker-with-portal/ChooseUbuntu.png)

## Erstellen von Docker-Zertifikaten

Stellen Sie nach dem Erstellen des virtuellen Computers sicher, dass der Docker auf Ihrem Clientcomputer installiert ist. (Einzelheiten finden Sie in den [Installationsanweisungen für Docker](https://docs.docker.com/installation/#installation).)

Erstellen Sie das Zertifikat und Schlüsseldateien für die Docker-Kommunikation gemäß [Running Docker with https] (Ausführen von Docker mit HTTPS, in englischer Sprache), und speichern Sie sie im Verzeichnis **`~/.docker`** auf Ihrem Clientcomputer.

> [AZURE.NOTE]Für die Docker-VM-Erweiterung im Portal sind zurzeit base64-codierte Anmeldeinformationen erforderlich.

Verwenden Sie an der Befehlszeile **`base64`** oder ein anderes bevorzugtes Codierungstool zum Erstellen base64-codierter Themen. Wenn Sie dies mit ein paar einfachen Zertifikaten und Schlüsseldateien vornehmen, sieht das möglicherweise in etwa so aus:

```
 ~/.docker$ l
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ l
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## Hinzufügen der Docker-VM-Erweiterung
Suchen Sie zum Hinzufügen der Docker-VM-Erweiterung die von Ihnen erstellte VM-Instanz, führen Sie einen Bildlauf zu **Erweiterungen** durch, um „VM-Erweiterungen“ analog zur unteren Beschreibung anzuzeigen.
> [AZURE.NOTE]Diese Funktionalität wird nur im Vorschauportal unterstützt: https://portal.azure.com/

![](./media/virtual-machines-docker-with-portal/ClickExtensions.png)
### Hinzufügen einer Erweiterung
Klicken Sie auf **+ Hinzufügen**, um die möglichen VM-Erweiterungen anzuzeigen, die Sie zu diesem virtuellen Computer hinzufügen können.

![](./media/virtual-machines-docker-with-portal/ClickAdd.png)
### Auswählen der Docker-VM-Erweiterung
Wählen Sie die Docker-VM-Erweiterung aus, wodurch die Docker-Beschreibung und wichtige Links angezeigt werden, und klicken Sie dann unten auf **Erstellen**, um die Installationsprozedur zu starten.

![](./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png)

![](./media/virtual-machines-docker-with-portal/CreateButtonFocus.png)
### Fügen Sie Ihr Zertifikat und Schlüsseldateien hinzu:

Geben Sie in den Formularfeldern die base64-codierten Versionen Ihres ZS-Zertifikats, Ihr Serverzertifikat und Ihren Serverschlüssel ein, wie dies in der folgenden Grafik gezeigt wird.

![](./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png)

> [AZURE.NOTE]Beachten Sie (analog zur vorherigen Abbildung), dass 2376 standardmäßig aufgefüllt wird. Sie können hier einen beliebigen Endpunkt eingeben. Der nächste Schritt ist jedoch für den übereinstimmenden Endpunkt gedacht. Wenn Sie den Standardwert ändern, müssen Sie die Erschließung des übereinstimmenden Endpunkts im nächsten Schritt sicherstellen.

## Hinzufügen des Docker-Kommunikationsendpunkts
Führen Sie beim Anzeigen Ihres virtuellen Computers in der von Ihnen erstellten Ressourcengruppe einen Bildlauf durch, um auf **Endpunkte** zu klicken, um die Endpunkte auf dem virtuellen Computer analog zur hier aufgeführten Darstellung anzuzeigen.

![](./media/virtual-machines-docker-with-portal/AddingEndpoint.png)

Klicken Sie zum Hinzufügen eines anderen Endpunkts auf **+ Hinzufügen**, und geben Sie im Standardfall einen Namen für den Endpunkt (in diesem Fall **docker**) und „2376“ für den privaten und öffentlichen Port ein. Belassen Sie den Protokollwert bei **TCP**, und klicken Sie auf **OK**, um den Endpunkt zu erstellen.

![](./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png)


## Testen des Docker-Clients und des Azure Docker-Hosts
Suchen und kopieren Sie den Namen der Domäne Ihres virtuellen Computers. Geben Sie zudem an der Befehlszeile Ihres Clientcomputers `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` ein (wobei *dockerextension* durch die Unterdomäne für Ihren virtuellen Computer ersetzt wird).

Das Ergebnis sollte in etwa so aussehen:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
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
```

Nachdem Sie die oben genannten Schritte abgeschlossen haben, verfügen Sie nun über einen voll funktionsfähigen Docker-Host auf einer Azure-VM, der so konfiguriert ist, dass eine Remoteverbindung von anderen Clients hergestellt werden kann.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Sie sind nun bereit, das [Docker-Benutzerhandbuch] und Ihren virtuellen Docker-Computer zu verwenden. Informationen zum Automatisieren der Erstellung von Docker-Hosts auf Azure-VMs über die Befehlszeilenschnittstelle finden Sie unter [Verwenden der Docker-VM-Erweiterung über die Azure-Befehlszeilenschnittstelle (Azure-CLI)].

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Verwenden der Docker-VM-Erweiterung über die Azure-Befehlszeilenschnittstelle (Azure-CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux-Agents]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Running Docker with https]: http://docs.docker.com/articles/https/
[Docker-Benutzerhandbuch]: https://docs.docker.com/userguide/

<!---HONumber=AcomDC_1203_2015-->