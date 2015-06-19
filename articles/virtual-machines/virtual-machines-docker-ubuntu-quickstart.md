<properties
	pageTitle="Schnelles Verwenden von Docker mit einem Ubuntu Docker VM-Image"
	description="Beschreibt und veranschaulicht, wie Docker auf Ubuntu Server in Minuten direkt aus dem Azure-Imagekatalog verwendet werden kann."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="05/20/2015"
	ms.author="rasquill"/>

# Schneller Einstieg in Docker in Azure Marketplace

Die schnellste Möglichkeit für die ersten Schritte mit [Docker] besteht darin, Azure Marketplace aufzurufen und einen virtuellen Computer mit der Imagevorlage **Docker on Ubuntu Server** zu erstellen, die von [Canonical] in Verbindung mit [MSOpenTech] erstellt wurde. Damit wird eine Ubuntu Server-VM erstellt und automatisch die [Docker-VM-Erweiterung](virtual-machines-docker-vm-extension.md) zusammen mit dem **neuesten** Docker-Modul, das bereits auf Azure installiert und aktiv ist, installiert.

Sie können sofort ohne zusätzlichen Aufwand eine Verbindung mit dem virtuellen Computer über SSH herstellen und direkt mit dem Ausführen von Aufgaben mit Docker beginnen.

> [AZURE.NOTE]Der mit der Azure Marketplace-Vorlage erstellte virtuelle Computer ist kein Host für die Remote-API von Docker zur Verwaltung eines Remote-Docker-Clients. Informationen zur Remotesteuerung des Docker-Hosts auf diesem virtuellen Computer finden Sie unter [Running Docker with HTTPS](https://docs.docker.com/articles/https/) (Ausführen von Docker mit https) (in englischer Sprache). Sie können auch die Schritte unter [Verwenden der Docker-VM-Erweiterung mithilfe des Azure-Portals ](virtual-machines-docker-with-portal.md) oder [Verwenden der Docker-VM-Erweiterung über die Azure-CLI](virtual-machines-docker-with-xplat-cli.md) ausführen. Sie können aber auch den [Windows-Docker-Client](https://github.com/ahmetalpbalkan/Docker.DotNet) von Github erstellen und ausprobieren (auch unter [nuget](https://www.nuget.org/packages/Docker.DotNet/) abrufbar).

In diesem Thema:

- [Anmelden beim Portal]
- [Erstellen eines virtuellen Computers mit dem Docker-Image von Canonical und MSOpenTech]
- [Verbinden mit SSH und Spaß haben]

## <a id='logon'>Anmelden beim Portal</a>

Dieser Teil ist leicht, sofern Sie über ein Azure-Konto verfügen. [Sie erhalten dieses auf einfache Weise kostenlos](http://azure.microsoft.com/pricing/free-trial/)!

## <a id='createvm'>Erstellen eines virtuellen Computers mit dem Docker-Image von Canonical und MSOpenTech</a>

1. Nachdem Sie sich angemeldet haben, klicken Sie unten links auf **Neu**, um ein neues VM-Image zu erstellen. Das richtige Image wird möglicherweise sofort im Banner angezeigt:

> ![Wählen Sie das Docker Ubuntu-Image im Banner](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. Ist dies jedoch nicht der Fall, suchen Sie im Imagekatalog danach:

> ![Suchen Sie das Image in der Bildergalerie](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. Geben Sie den Benutzernamen und das Kennwort für die Instanz oder eine **PEM**-Datei an, um SSH mit einem Zertifikat zu aktivieren. (Die unten dargestellte Abbildung zeigt die Angabe einer Kombination aus Benutzername und Kennwort.) Klicken Sie anschließend unten auf **Erstellen**.

> ![Konfigurieren Sie die VM-Instanz](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Warten Sie die Ausführung ab. Es sollte nicht all zu lange dauern.

> ![Im Verwaltungsportal ausgeführtes Docker-Image](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'>Verbinden mit SSH und Spaß haben</a>

Jetzt beginnt der Spaß. Sie können sofort eine Verbindung mit dem virtuellen Computer über SSH herstellen:

> ![Herstellen einer Verbindung mit SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

Beginnen Sie mit der Ausgabe von Docker-Befehlen. (Hinweis: Bei dieser Azure-VM für die Standardkonfiguration ist **`sudo`** erforderlich):

> ![Abrufen von Images](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Sie könnten mit der Verwendung von [Docker] beginnen!

<!--Anchors-->
[Anmelden beim Portal]: #logon
[Erstellen eines virtuellen Computers mit dem Docker-Image von Canonical und MSOpenTech]: #createvm
[Verbinden mit SSH und Spaß haben]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/

<!---HONumber=58--> 