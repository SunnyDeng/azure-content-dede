<properties
	pageTitle="Erste Schritte mit Fleet unter CoreOS | Microsoft Azure"
	description="Stellt grundlegende Beispiele für die Verwendung von Fleet und Docker auf einem mit dem klassischen Bereitstellungsmodell erstellten virtuellen Linux-Computer mit CoreOS in Azure bereit."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="08/03/2015"
	ms.author="danlep"/>

# Erste Schritte mit Fleet unter CoreOS in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


In diesem Artikel enthält zwei kurze Beispiele für die Verwendung von [Fleet](https://github.com/coreos/fleet) und [Docker](https://www.docker.com/) zum Ausführen von Anwendungen in einem Cluster von virtuellen [CoreOS]-Computern.

Um diese Beispiele verwenden zu können, müssen Sie zunächst ein CoreOS-Cluster wie in [Gewusst wie: Verwenden von CoreOS in Azure] beschrieben einrichten. Danach verstehen Sie die grundlegenden Elemente einer CoreOS-Bereitstellung und verfügen über ein funktionierendes Cluster und den Clientcomputer. In diesen Beispielen verwenden wir denselben Clusternamen. Zudem wird in diesen Beispielen davon ausgegangen, dass Sie den lokalen Linux-Host zum Ausführen der **fleetctl**-Befehle verwenden.




## <a id='simple'>Beispiel 1: "Hello World" mit Docker</a>

Hier ist eine einfache "Hello World"-Anwendung, die in einem einzelnen Docker-Container ausgeführt wird. Dazu wird das [Docker-Hub-Image BusyBox] verwendet.

Erstellen Sie auf dem Linux-Clientcomputer mit einem Text-Editor Ihrer Wahl die folgende **systemd**-Unit-Datei, und nennen Sie sie `helloworld.service`. (Nähere Informationen zur Syntax finden Sie unter [Unit Files].)

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

Dann stellen Sie eine Verbindung mit dem CoreOS-Cluster her, und starten Sie die Unit, indem Sie den folgenden **Fleetctl**-Befehl ausführen. Aus der Ausgabe geht hervor, dass die Unit gestartet wurde und wo sie sich befindet.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]Um die **fleetctl**-Remotebefehle ohne den **--tunnel**-Parameter auszuführen, können Sie optional die FLEETCTL\_TUNNEL-Umgebungsvariable zum Tunneln der Anforderungen festlegen. Beispiel: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


Sie können eine Verbindung mit dem Container herstellen, um die Ausgabe des Diensts anzuzeigen:

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

Zum Bereinigen beenden und entladen Sie die Unit.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>Beispiel 2: Apache-Server mit hoher Verfügbarkeit</a>

Ein Vorteil der Verwendung von CoreOS, Docker und **fleet** besteht darin, dass dann Dienste problemlos mit hoher Verfügbarkeit ausgeführt werden können. In diesem Beispiel stellen Sie einen Dienst bereit, der aus drei identischen Containern besteht, in denen der Apache-Webserver ausgeführt wird. Der Container werden auf die drei virtuellen Computern im Cluster ausgeführt. Dieses Beispiel ähnelt dem Beispiel in [Starten von Containern mit fleet] und verwendet das [CoreOS-Image Apache Docker Hub].

>[AZURE.IMPORTANT]Um den Apache-Server mit hoher Verfügbarkeit auszuführen, müssen Sie auf den virtuellen Computern einen HTTP-Endpunkt mit Lastenausgleich konfigurieren (öffentlicher Port 80, privater Port 80). Sie können dies nach der Erstellung des CoreOS-Clusters im Azure-Portal oder mit dem Befehl **azure vm endpoint** tun. Weitere Informationen finden Sie unter [Konfigurieren einer Gruppe mit Lastenausgleich].

Erstellen Sie auf dem Clientcomputer mit Ihrem bevorzugten Texteditor eine **systemd**-Unit-Vorlagendatei namens apache@.service. Sie verwenden diese Vorlage zum Starten von drei separaten Instanzen namens apache@1.service, apache@2.service und apache@3.service:

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]Das `X-Conflicts`-Attribut weist CoreOS an, dass nur eine Instanz dieses Containers auf einem bestimmten CoreOS-Host ausgeführt werden kann. Weitere Informationen finden Sie unter [Unit Files].

Jetzt starten Sie die Unit-Instanzen im CoreOS-Cluster. Sie sollten sehen, dass sie auf drei verschiedenen Computern ausgeführt werden:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.\service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
Um den Apache-Server, der in einer dieser Units ausgeführt wird, zu erreichen, senden Sie eine einfache Anforderung an den Clouddienst, der das CoreOS-Cluster hostet.

`curl http://coreos-cluster.cloudapp.net`

Sie sehen Standardtext, der vom Apache-Server zurückgegeben wird und etwa wie folgt lautet:

```
<html><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
```

Sie können versuchen, einen oder mehrere virtuelle Computer im Cluster herunterzufahren, um zu überprüfen, ob der Apache-Dienst weiterhin ausgeführt wird.

Abschließend beenden und entladen Sie die Units.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## Nächste Schritte

* Sie können das CoreOS-Cluster mit drei Knoten in Azure auch zu anderen Zwecken verwenden. Lesen Sie [Tim Park's CoreOS Tutorial] (Tim Parks CoreOS-Lernprogramm), [Patrick Chanezon's CoreOS Tutorial] (Patrick Chanezons CoreOS-Lernprogramm), die [Docker]-Dokumentation und [CoreOS Overview] (Übersicht zu CoreOS) (allesamt in englischer Sprache), um herauszufinden, wie Sie komplexere Cluster erstellen und Docker verwenden sowie interessantere Anwendungen erstellen können.

* Zum Einstieg in Fleet und CoreOS mit dem Azure-Ressourcen-Manager können Sie die [Schnellstart-Vorlage](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/) nutzen.

* Weitere Informationen zur Verwendung von Open Source-Umgebungen in virtuellen Linux-Computern in Azure finden Sie unter [Linux- und Open Source-Computing in Azure].

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[CoreOS Overview]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park's CoreOS Tutorial]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon's CoreOS Tutorial]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Gewusst wie: Verwenden von CoreOS in Azure]: virtual-machines-linux-coreos-how-to.md
[Konfigurieren einer Gruppe mit Lastenausgleich]: ../load-balancer/load-balancer-internet-getstarted.md
[Starten von Containern mit fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Unit Files]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[Docker-Hub-Image BusyBox]: https://registry.hub.docker.com/_/busybox/
[CoreOS-Image Apache Docker Hub]: https://registry.hub.docker.com/u/coreos/apache/
[Linux- und Open Source-Computing in Azure]: virtual-machines-linux-opensource.md

<!---HONumber=Oct15_HO3-->