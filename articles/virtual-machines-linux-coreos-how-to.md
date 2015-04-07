<properties 
	pageTitle="Gewusst wie: Verwenden von CoreOS in Azure" 
	description="Beschreibt CoreOS, das Erstellen eines virtuellen CoreOS-Computers in Azure und dessen grundlegende Verwendung." 
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
	ms.date="01/02/2015" 
	ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title--> 
# Gewusst wie: Verwenden von CoreOS in Azure

Dieses Thema beschreibt [CoreOS] und zeigt das Erstellen eines Clusters von drei virtuellen CoreOS-Computern in Azure als einen Schnellstart zum Verstehen von CoreOS. Es enthält die sehr grundlegenden Elemente von CoreOS-Bereitstellungen und Beispiele aus [CoreOS with Azure] (CoreOS mit Azure), [Tim Park's CoreOS Tutorial] (Tim Parks CoreOS-Lernprogramm) und [Patrick Chanezon's CoreOS Tutorial] (Patrick Chanezons CoreOS-Lernprogramm) (allesamt in englischer Sprache) zum Veranschaulichen der absoluten Mindestvoraussetzungen, um die grundlegende Struktur einer CoreOS-Bereitstellung zu verstehen und einen Cluster von drei erfolgreich ausgeführten virtuellen Computern zu erhalten. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
Dieses Thema enthält die folgenden Abschnitte:

+ [CoreOS, Cluster und Linux-Container]
+ [Sicherheitshinweise]
+ [Gewusst wie: Verwenden von CoreOS in Azure]
+ [Nächste Schritte]


## <a id='intro'>CoreOS, Cluster und Linux-Container</a>

CoreOS ist eine abgespeckte Version von Linux, die zum Unterstützen der schnellen Erstellung von potenziell großen Clustern von virtuellen Computern entwickelt wurde, die Linux-Container als den einzigen Paketmechanismus verwenden, einschließlich [Docker]-Containern. CoreOS soll Folgendes unterstützen:

+ Eine sehr hohe Automatisierungsebene
+ Einfachere und konsistentere Anwendungsbereitstellung
+ Skalierbarkeit auf Anwendungs- und Systemebene

Auf übergeordneter Ebene lauten die CoreOS-Features, die diese Ziele unterstützen, wie folgt:

1. Ein Paketsystem: CoreOS führt nur Linux-Containerimages aus, die in Linux-Containern zwecks Geschwindigkeit, Uniformität und einfacher Bereitstellung ausgeführt werden.
2. Automatisch ausgeführte Betriebssystemupdates, sodass Betriebssysteme als eine einzelne Entität aktualisiert werden und einfach auf einen bekannten Zustand zurückgesetzt werden können.
3. Integrierte [etcd](https://github.com/coreos/etcd)- und [fleet](https://github.com/coreos/fleet)-Daemons (Dienste) für dynamische virtuelle Computer sowie Clusterkommunikation und -verwaltung.

Hierbei handelt es sich um eine sehr allgemeine Beschreibung von CoreOS und seiner Features. Vollständige Informationen über CoreOS finden Sie unter [CoreOS Overview (Übersicht zu CoreOS)] (in englischer Sprache).

## <a id='security'>Überlegungen zur Sicherheit</a>
CoreOS geht aktuell davon aus, dass diejenigen, die per SSH im Cluster agieren können, die Berechtigung für die Verwaltung besitzen. Im Ergebnis sind CoreOS-Cluster ohne Änderung für Test- und Entwicklungsumgebungen ausstehend, Sie sollten jedoch weitergehende Sicherheitsmaßnahmen in jeder Produktionsumgebung anwenden. 

## <a id='usingcoreos'>Gewusst wie: Verwenden von CoreOS in Azure</a>

Dieser Abschnitt beschreibt das Erstellen eines Azure-Clouddiensts mit drei darin enthaltenen virtuellen CoreOS-Computern mithilfe der [plattformübergreifenden Azure-Schnittstelle (xplat-cli)]. Die grundlegenden Schritte lauten wie folgt:

1. Erstellen der SSH-Zertifikate und -Schlüssel zur sicheren Kommunikation mit dem virtuellen CoreOS-Computer
2. Abrufen der etcd-ID Ihres Clusters für die Vernetzung
3. Erstellen der Datei "cloud-config" im Format [YAML]
4. Verwenden der "xplat-cli" zum Erstellen eines neuen Azure-Clouddiensts und drei neuer virtueller CoreOS-Computer
5. Testen Ihres CoreOS-Clusters auf dem virtuellen Azure-Computer
6. Testen Ihres CoreOS-Clusters über localhost 

### Erstellen von öffentlichen und privaten Schlüsseln für die Kommunikation
 
Verwenden Sie die Anweisungen in [Gewusst wie: Verwenden von SSH mit Linux in Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/), um einen öffentlichen und privaten Schlüssel für SSH zu erstellen. (Die grundlegenden Schritte befinden sich in den folgenden Anweisungen.) Sie verwenden diese Schlüssel, um eine Verbindung zu den virtuellen Computern im Cluster herzustellen, um zu überprüfen, ob sie funktionieren und miteinander kommunizieren können.

> [AZURE.NOTE] In diesem Thema wird davon ausgegangen, dass Sie nicht über diese Schlüssel verfügen. Das Erstellen der Dateien **`myPrivateKey.pem`** und **`myCert.pem`** aus Gründen der Übersichtlichkeit ist erforderlich. Wenn Sie bereits über ein in **`~/.ssh/id_rsa`** gespeichertes öffentliches und privates Schlüsselpaar verfügen, können Sie einfach  `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` eingeben, um die PEM-Datei abzurufen, die Sie für das Hochladen zu Azure benötigen.

1. Geben Sie in einem Arbeitsverzeichnis  `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` und ein damit verknüpftes X.509-Zertifikat ein, um den privaten Schlüssel zu erstellen. 

2. Geben Sie  `chmod 600 myPrivateKey.key` ein, damit der Besitzer des privaten Schlüssels die Datei lesen oder schreiben kann. 

In Ihrem Arbeitsverzeichnis sollte sowohl die Datei **`myPrivateKey.key`** als auch die Datei **`myCert.pem`** enthalten sein. 


### Abrufen der etcd-ID Ihres Clusters

Für den **etcd**-Daemon von CoreOS ist eine Ermittlungs-ID zum automatischen Abrufen sämtlicher Knoten im Cluster erforderlich. Geben Sie Folgendes ein, um Ihre Ermittlungs-ID abzurufen und sie in der Datei  **etcdid** zu speichern:

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Erstellen der Datei "cloud-config"

Erstellen Sie im selben Arbeitsverzeichnis eine Datei mit Ihrem bevorzugten Texteditor mit dem folgenden Text, und speichern Sie sie als **`cloud-config.yaml`**. (Sie können sie bei Bedarf als Dateinamen speichern. Wenn Sie Ihre virtuellen Computer jedoch im nächsten Schritt erstellen, müssen Sie diesen Dateinamen in der Option **--custom-data** für den Befehl **azure create vm** referenzieren.)

> [AZURE.NOTE] Geben Sie  `cat etcdid` zum Abrufen der Ermittlungs-ID etcd aus der von Ihnen oben erstellten Datei  `etcdid` ein, und ersetzen Sie **`<token>`** in der folgenden Datei **cloud-config.yaml** mit der generierten Zahl aus der Datei  `etcdid`. Wenn Sie am Ende nicht in der Lage sind, Ihr Cluster zu überprüfen, haben Sie möglicherweise einen Schritt übersehen!

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

(Weitere Informationen über die Datei "cloud-config" finden Sie unter [Using Cloud-Config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) (Verwenden von Cloud-Config) (in englischer Sprache) in der CoreOS-Dokumentation.)

### Verwenden der xplat-cli zum Erstellen eines neuen virtuellen CoreOS-Computers
<!--Jedes Thema sollte weitere Schritte und Links zum nächsten logischen Satz von Inhalten umfassen, um den Kunden weiter zu informieren.-->

1. Installieren Sie die [plattformübergreifenden Azure-Schnittstelle (xplat-cli)], wenn Sie dies noch nicht vorgenommen haben, und melden Sie sich entweder mit einer Arbeits- oder Schul-ID an, oder laden Sie eine .publishsettings-Datei herunter, und importieren Sie diese in Ihr Konto.
2. Suchen Sie das CoreOS-Image. Um die Images jederzeit lokalisieren zu können, geben Sie  `azure vm image list | grep CoreOS` ein. Sie sehen eine Liste der Ergebnisse, die folgender Liste ähnelt:

	data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0              Public    Linux

3. Erstellen Sie einen Clouddienst für ein grundlegendes Cluster, indem Sie
`azure service create <cloud-service-name>` eingeben, wobei *<cloud-service-name>* der Name für Ihren CoreOS-Clouddienst ist. In diesem Beispiel wird der Name **`coreos-cluster`** verwendet. Sie müssen den Namen erneut verwenden, den Sie zum Erstellen Ihrer virtuellen CoreOS-Computerinstanzen im Clouddienst auswählen. 

Hinweis: Wenn Sie Ihre bisherige Arbeit im [Portal](https://portal.azure.com) begutachten, können Sie sehen, dass Ihr Clouddienstname sowohl eine Ressourcengruppe als auch eine Domäne ist, wie in der folgenden Abbildung gezeigt wird:

![][CloudServiceInNewPortal]  
4. Stellen Sie eine Verbindung zu Ihrem Clouddienst her, und erstellen Sie einen neuen virtuellen CoreOS-Computer darin, indem Sie den Befehl **azure vm create** verwenden. Sie übergeben den Ort Ihres X.509-Zertifikats in der Option **--ssh-cert**. Erstellen Sie Ihr erstes Image für virtuelle Computer, indem Sie Folgendes eingeben (beachten Sie, **coreos-cluster** durch den von Ihnen erstellten Clouddienstnamen zu ersetzen):

```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. Erstellen Sie den zweiten Knoten, indem Sie den Befehl in Schritt 4 wiederholen, wodurch der Wert **--vm-name** durch **node-2** und der Portwert **--ssh** durch "2022" ersetzt wird. 
 
6. Erstellen Sie den dritten Knoten, indem Sie den Befehl in Schritt 4 wiederholen, wodurch der Wert **--vm-name** durch **node-3** und der Portwert **--ssh** durch "3022" ersetzt wird. 
 
Im Bild unten können Sie erkennen, wie der CoreOS-Cluster im neuen Portal angezeigt wird.

![][EmptyCoreOSCluster]

### Testen Ihres CoreOS-Clusters auf einem virtuellen Azure-Computer

Zum Testen Ihres Clusters müssen Sie sich in Ihrem Arbeitsverzeichnis befinden und dann eine Verbindung zu **node-1** mithilfe von **ssh** herstellen sowie den privaten Schlüssel übergeben, indem Sie Folgendes eingeben:

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

Geben Sie  `sudo fleetctl list-machines` ein, sobald Sie verbunden sind, um anzuzeigen, ob der Cluster bereits alle virtuellen Computer im Cluster identifiziert hat. Sie sollten eine Antwort empfangen, die in etwa der folgenden entspricht:


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### Testen Ihres CoreOS-Clusters über localhost

Lassen Sie uns nun das CoreOS-Cluster vom lokalen Linux-Client aus testen. Installieren Sie dazu **fleet**. **fleet** setzt **golang** voraus, das Sie möglicherweise erst installieren müssen. Geben Sie dazu Folgendes ein:

`sudo apt-get install golang` 

Klonen Sie anschließend das Repository **fleet** von github, indem Sie Folgendes eingeben:

`git clone https://github.com/coreos/fleet.git`

Erstellen Sie **fleet**, indem Sie Folgendes eingeben:

`./build`

Platzieren Sie **fleet** abschließend für die einfache Verwendung (hängt von Ihrer Konfiguration ab, für die Sie evtl. **sudo** benötigen):

`cp bin/fleetctl /usr/local/bin`

Stellen Sie sicher, dass **fleet** auf  `myPrivateKey.key` im Arbeitsverzeichnis zugreifen kann. Geben Sie dazu Folgendes ein:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] Wenn Sie bereits den Schlüssel **`~/.ssh/id_rsa`** verwenden, fügen Sie diesen mit  `ssh-add ~/.ssh/id_rsa` hinzu.

Nun können Sie mit demselben **fleetctl**-Befehl, den Sie von **node-1** verwendet haben, remote testen; geben Sie jedoch einige Remoteargumente weiter:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Die Ergebnisse sollten identisch sein:


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## Nächste Schritte

Sie sollten über einen betriebsbereiten CoreOS-Cluster mit drei Knoten in Azure verfügen. Anhand der Lektüre von [Tim Park's CoreOS Tutorial] (Tim Parks CoreOS-Lernprogramm), [Patrick Chanezon's CoreOS Tutorial] (Patrick Chanezons CoreOS-Lernprogramm), der [Docker]-Dokumentation und [CoreOS Overview] (Übersicht zu CoreOS) (allesamt in englischer Sprache) können Sie ermitteln, wie Sie komplexere Cluster erstellen und Docker verwenden sowie interessantere Anwendungen erstellen können.

<!--Anchors-->
[CoreOS, Cluster und Linux-Container]: #intro
[Sicherheitshinweise]: #security
[Gewusst wie: Verwenden von CoreOS in Azure]: #usingcoreos
[Unterüberschrift 3]: #subheading-3
[Nächste Schritte]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[plattformübergreifenden Azure-Schnittstelle (xplat-cli)]: ../xplat-cli/

[CoreOS]: https://coreos.com/
[CoreOS-Überblick]: https://coreos.com/using-coreos/
[CoreOS mit Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park's CoreOS Tutorial]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon's CoreOS Tutorial]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/

<!--HONumber=45--> 
