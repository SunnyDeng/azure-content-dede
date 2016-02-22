<properties
   pageTitle="Bereitstellen einer Node.js-Anwendung auf virtuellen Linux-Computern in Azure"
   description="Lernen Sie, wie Sie eine Node.js-Anwendung auf virtuellen Linux-Computern in Azure bereitstellen."
   services=""
   documentationCenter="nodejs"
   authors="stepro"
   manager="dmitryr"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="stephpr"/>

# Bereitstellen einer Node.js-Anwendung auf virtuellen Linux-Computern in Azure

Dieses Tutorial zeigt, wie eine Node.js-Anwendung auf virtuellen Linux-Computern in Azure bereitgestellt und ausgeführt wird. Die Anweisungen in diesem Tutorial gelten für alle Betriebssysteme, unter denen Node.js ausgeführt werden kann.

Sie lernen Folgendes:

- Verzweigen und Klonen eines GitHub-Repositorys, das eine einfache TODO-Anwendung enthält;
- Erstellen und Konfigurieren von zwei virtuellen Linux-Computern in Azure, um die Anwendung auszuführen;
- Durchlaufen der Anwendung, indem Updates per Push auf den virtuellen Webfrontend-Computer übertragen werden.

> [AZURE.NOTE]
Um dieses Tutorial abzuschließen, benötigen Sie ein GitHub-Konto, ein Microsoft Azure-Konto und die Möglichkeit, von einem Entwicklungscomputer aus Git zu verwenden.

> Wenn Sie kein GitHub-Konto besitzen, können Sie sich [hier](https://github.com/join) anmelden.

> Wenn Sie kein [Microsoft Azure-Konto](https://azure.microsoft.com/) besitzen, können Sie sich [hier](https://azure.microsoft.com/pricing/free-trial/) für eine KOSTENLOSE Testversion registrieren. Dies führt Sie auch durch den Registrierungsprozess für ein [Microsoft-Konto](http://account.microsoft.com), wenn Sie noch keines besitzen. Alternativ können Sie, wenn Sie Visual Studio-Abonnent sind, [Ihre MSDN-Vorteile aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> Wenn Sie auf dem Entwicklungscomputer nicht über Git verfügen, installieren Sie bei Verwendung eines Macintosh- oder Windows-Computers Git von [hier](http://www.git-scm.com) aus. Wenn Sie Linux verwenden, installieren Sie Git mit der für Sie am besten geeigneten Methode, z. B. `sudo apt-get install git`.

## Verzweigen und Klonen der TODO-Anwendung

Die in diesem Tutorial verwendete TODO-Anwendung implementiert ein einfaches Webfrontend über eine MongoDB-Instanz, die eine TODO-Liste überwacht. Wechseln Sie nach dem Registrieren bei GitHub nach [hier](https://github.com/stepro/node-todo), um die Anwendung zu finden, und verzweigen Sie sie über den Link in der rechten oberen Ecke. Damit sollte in Ihrem Konto ein Repository mit dem Namen *accountname*/node-todo erstellt werden.

Klonen Sie jetzt auf Ihrem Entwicklungscomputer dieses Repository:

    git clone https://github.com/accountname/node-todo.git

Diesen lokalen Klon des Repositorys verwenden wir etwas später zu Änderungen des Quellcodes.

## Erstellen und Konfigurieren der virtuellen Linux-Computer

Azure bietet hervorragende Unterstützung für Rohberechnungen mit virtuellen Linux-Computern. Dieser Teil des Tutorials zeigt, wie Sie problemlos zwei virtuelle Linux-Computer erstellen und die TODO-Anwendung auf Ihnen bereitstellen können, wobei das Webfrontend auf dem einen und die MongoDB-Instanz auf dem anderen ausgeführt wird.

### Erstellen virtueller Computer

Die einfachste Möglichkeit, einen neuen virtuellen Computer in Azure zu erstellen, bietet das Azure-Portal. Klicken Sie [hier](https://portal.azure.com), um sich anzumelden, und starten Sie das Azure-Portal in Ihrem Webbrowser. Sobald das Azure-Portal geladen ist, führen Sie folgende Schritte aus:

- Klicken Sie auf den Link „+ Neu“.
- Wählen Sie die Kategorie „Compute“ und dann „Ubuntu Server 14.04 LTS“;
- Wählen Sie das Bereitstellungsmodell „Ressourcen-Manager“, und klicken Sie auf „Erstellen“.
- Tragen Sie die allgemeinen Informationen unter Beachtung folgender Richtlinien ein:
  - Geben Sie einen Namen an, den Sie später problemlos erkennen können;
  - Wählen Sie für dieses Tutorial die Kennwortauthentifizierung;
  - Erstellen Sie eine neue Ressourcengruppe mit einem eindeutigen Namen.
- Für die Größe des virtuellen Computers ist „A1 Standard“ eine vernünftige Wahl für dieses Tutorial.
- Stellen Sie für zusätzliche Einstellungen sicher, dass der Datenträgertyp „Standard“ ist, und akzeptieren Sie alle verbleibenden Standardwerte.
- Starten Sie die Erstellung auf der Zusammenfassungsseite.

Führen Sie das oben beschriebene Verfahren zweimal aus, um zwei virtuelle Linux-Computer zu erstellen – einen für das Webfrontend und einen für die MongoDB-Instanz. Das Erstellen der virtuellen Computer dauert ungefähr 5 bis 10 Minuten.

### Zuweisen eines DNS-Eintrags für virtuelle Computer

Auf virtuelle Computer, die in Azure erstellt werden, kann standardmäßig nur über eine öffentliche IP-Adresse wie 1.2.3.4 zugegriffen werden. Damit die Computer leichter identifizierbar sind, weisen wir ihnen DNS-Einträge zu.

Sobald das Portal angibt, dass die virtuellen Computer erstellt wurden, klicken Sie in der linken Navigationsleiste auf den Link „Virtuelle Computer“, und suchen Sie Ihren Computer. Für jeden Computer:

- Klicken Sie auf der Registerkarte „Zusammenfassung“ auf die „Öffentliche IP-Adresse“;
- Weisen Sie in der Konfiguration der öffentlichen IP-Adresse einen DNS-Namen zu, und speichern Sie ihn.

Das Portal stellt sicher, dass der von Ihnen angegebene Name verfügbar ist. Nach dem Speichern der Konfiguration haben Ihre virtuellen Computer Hostnamen wie etwa `machinename.region.cloudapp.azure.com`.

### Herstellen der Verbindung mit den virtuellen Computern

Als Ihre virtuellen Computer bereitgestellt wurden, wurden sie dazu vorkonfiguriert, Remoteverbindungen über SSH zuzulassen. Dies ist die Methode, mit der wir die virtuellen Computer konfigurieren. Wenn Sie Windows für die Entwicklung verwenden, müssen Sie einen SSH-Client erhalten, wenn Sie nicht bereits über einen verfügen. Eine gängige Wahl hierzu ist die Software PuTTY, die Sie [hier](http://www.chiark.greenend.org.uk/~sgtatham/putty/) herunterladen können. Bei Macintosh- und Linux-Betriebssystemen ist eine Version von SSH vorinstalliert.

### Konfigurieren des virtuellen Webfrontend-Computers

Stellen Sie über die SSH-Befehlszeile oder mit Ihrem bevorzugten SSH-Tool eine SSH-Verbindung mit dem Webfrontend-Computer her, den Sie mit PuTTY erstellt haben. Daraufhin sollte eine Begrüßung, gefolgt von einer Befehlszeile, angezeigt werden.

Stellen Sie zunächst sicher, dass Git und Knoten installiert sind:

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs
    
Da das Webfrontend der Anwendung von einigen nativen Node.js-Module abhängt, müssen wir auch die wesentlichen Buildtools installieren:

    sudo apt-get install -y build-essential

Abschließend installieren wir eine Node.js-Anwendung namens *forever*, die dabei hilft, Node.js-Serveranwendungen auszuführen:

    sudo npm install -g forever
    
Dies sind alle Abhängigkeiten, die auf diesem virtuellen Computer erforderlich sind, damit das Webfrontend der Anwendung ausgeführt werden kann, also lassen wir es ausführen. Hierzu erstellen wir zunächst einen reinen Klon des GitHub-Repositorys, das Sie zuvor verzweigt haben, damit Sie Updates schnell und einfach auf dem virtuellen Computer veröffentlichen können (dieses Updateszenario wird später erläutert), und klonen anschließend den reinen Klon, um eine Version des Repositorys bereitzustellen, die tatsächlich ausgeführt werden kann.

Führen Sie ausgehend vom Basisverzeichnis (~) die folgenden Befehle aus (ersetzen Sie dabei *accountname* durch Ihren GitHub-Benutzerkontonamen):

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

Geben Sie jetzt das node-todo-Verzeichnis ein, und führen Sie folgende Befehle aus:

    npm install
    forever start server.js
    
Das Webfrontend der Anwendung wird nun ausgeführt, allerdings ist ein weiterer Schritt erforderlich, bevor Sie über einen Webbrowser auf die Anwendung zugreifen können. Der von Ihnen erstellte virtuelle Computer ist durch eine Azure-Ressource mit dem Namen *Netzwerksicherheitsgruppe* geschützt, die für Sie erstellt wurde, als Sie den virtuellen Computer bereitgestellt haben. Diese Ressource lässt derzeit nur zu, dass externe Anforderungen auf Port 22 an den virtuellen Computer weitergeleitet werden, sodass SSH-Kommunikation ausschließlich mit dem Computer möglich ist. Um die TODO-Anwendung anzuzeigen, die zur Ausführung auf Port 8080 konfiguriert ist, muss dieser Port also auch geöffnet werden.

Kehren Sie zum Azure-Portal zurück, und führen Sie die folgenden Schritte aus:

- Klicken Sie auf der linken Navigationsleiste auf „Ressourcengruppen“;
- Wählen Sie die Ressourcengruppe, die Ihren virtuellen Computer enthält;
- Wählen Sie in der resultierenden Liste von Ressourcen die Netzwerksicherheitsgruppe (mit einem Schildsymbol);
- Wählen Sie in den Eigenschaften „Eingangssicherheitsregeln“;
- Klicken Sie auf der Symbolleiste auf „Hinzufügen“;
- Geben Sie einen Namen wie „default-allow-todo“ an;
- Setzen Sie das Protokoll auf „TCP“;
- Setzen Sie den Zielportbereich auf „8080“;
- Klicken Sie auf „OK“, und warten Sie, bis die Sicherheitsregel erstellt wird.

Nach dem Erstellen dieser Sicherheitsregel ist die TODO-Anwendung im Internet öffentlich sichtbar, und Sie können sie aufrufen, z. B. über eine URL:

    http://machinename.region.cloudapp.azure.com:8080

Sie werden bemerken, dass die TODO-Anwendung offensichtlich sehr funktionsfähig ist, obwohl wir die MongoDB-VM noch nicht konfiguriert haben. Der Grund hierfür ist, dass das Quellrepository zur Verwendung einer vorab bereitgestellten MongoDB-Instanz hartcodiert ist. Nachdem wir die MongoDB-VM konfiguriert haben, kehren wir zurück und ändern den Quellcode, um stattdessen unsere private MongoDB-Instanz zu nutzen.

### Konfigurieren der MongoDB-VM

Stellen Sie über die SSH-Befehlszeile oder mit Ihrem bevorzugten SSH-Tool eine SSH-Verbindung mit dem zweiten Computer her, den Sie mit PuTTY erstellt haben. Installieren Sie MongoDB, nachdem Sie die Begrüßung gelesen haben, und sobald die Befehlszeile angezeigt wird (diese Anweisungen wurden [hier](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/) entnommen):

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

Standardmäßig ist MongoDB so konfiguriert, dass nur lokal darauf zugegriffen werden kann. Für dieses Tutorial konfigurieren wir MongoDB so, dass von der Anwendung des virtuellen Computers aus darauf zugegriffen werden kann. Öffnen Sie in einem Sudokontext die Datei „/etc/mongod.conf“, und suchen Sie den Abschnitt `# network interfaces`. Ändern Sie den Konfigurationswert `net.bindIp` in `0.0.0.0`.

> [AZURE.NOTE]
Diese Konfiguration ist nur für die Zwecke dieses Tutorials bestimmt. Sie ist **KEINE** empfohlene Sicherheitspraxis und sollte nicht in Produktionsumgebungen verwendet werden.

Stellen Sie nun sicher, dass der MongoDB-Dienst gestartet wurde:

    sudo service mongod restart

MongoDB verwendet standardmäßig den Port 27017. Genauso, wie wir Port 8080 auf dem virtuellen Webfrontend-Computer öffnen mussten, müssen wir Port 27017 auf der MongoDB-VM öffnen.

Kehren Sie zum Azure-Portal zurück, und führen Sie die folgenden Schritte aus:

* Klicken Sie auf der linken Navigationsleiste auf „Ressourcengruppen“;
* Wählen Sie die Ressourcengruppe, die Ihre MongoDB-VM enthält;
* Wählen Sie in der resultierenden Liste von Ressourcen die Netzwerksicherheitsgruppe (den Knoten mit einem Schildsymbol) mit dem gleichen Namen, den Sie der MongoDB-VM gegeben haben;
* Wählen Sie in den Eigenschaften „Eingangssicherheitsregeln“;
* Klicken Sie auf der Symbolleiste auf „Hinzufügen“;
* Geben Sie einen Namen wie „default-allow-mongo“ an;
* Setzen Sie das Protokoll auf „TCP“;
* Setzen Sie den Zielportbereich auf „27017“;
* Klicken Sie auf „OK“, und warten Sie, bis die Sicherheitsregel erstellt wird.

## Durchlaufen der TODO-Anwendung
Bisher haben wir zwei virtuelle Linux-Computer bereitgestellt: einen, auf dem das Webfrontend der Anwendung, und einen, auf dem die MongoDB-Instanz ausgeführt wird. Aber es gibt ein Problem – das Webfrontend benutzt die bereitgestellte MongoDB-Instanz überhaupt noch nicht. Durch Aktualisieren des Webfrontendcodes zur Verwendung einer Umgebungsvariablen statt einer hartcodierten Instanz schaffen wir hier Abhilfe.

### Ändern der TODO-Anwendung

Öffnen Sie auf dem Entwicklungscomputer, auf dem Sie zuerst das node-todo-Repository geklont haben, die Datei `node-todo/config/database.js` in Ihrem bevorzugten Editor, und ändern Sie den URL-Wert von dem hartcodierten Wert wie `mongodb://...` in `process.env.MONGODB`.

Committen Sie Ihre Änderungen, und führen Sie eine Pushübertragung auf den GitHub-Master aus:

    git commit -am "Get MongoDB instance from env"
    git push origin master

Leider wird die Änderung hiermit nicht auf dem virtuellen Webfrontendcomputer veröffentlicht. Wir nehmen ein paar weitere Änderungen des virtuellen Computers vor, um eine einfache, aber effektive Methode für die Veröffentlichung von Updates zu aktivieren, damit Sie die Auswirkungen der Änderungen schnell in der Liveumgebung beobachten können.

### Konfigurieren des virtuellen Webfrontend-Computers
Denken Sie daran, dass wir zuvor einen reinen Klon des node-todo-Repositorys auf dem virtuellen Webfrontend-Computer erstellt haben. Es stellt sich heraus, dass diese Aktion ein neues Git-Remoteverzeichnis erstellt, in das Änderungen per Push übertragen werden können. Die einfache Übertragung per Push in dieses Remoteverzeichnis bietet nicht das schnelle Iterationsmodell, das Entwickler sich zur Arbeit an ihrem Code wünschen.

Wir wären gern in der Lage, sicherzustellen, dass die ausgeführte TODO-Anwendung bei einer Pushübertragung in das Remoterepository auf dem virtuellen Computer automatisch aktualisiert wird. Glücklicherweise ist dies mit Git problemlos realisierbar.

Git bietet eine Reihe von Hooks, die zu bestimmten Zeiten aufgerufen werden, um auf Aktionen zu reagieren, die an dem Repository vorgenommen werden. Diese werden mithilfe von Shellskripts im Ordner `hooks` des Repositorys angegeben. Der Hook, der sich am besten für das automatische Updateszenario eignet, ist das `post-update`-Ereignis.

Wechseln Sie in einer SSH-Sitzung mit dem virtuellen Webfrontend-Computer in das Verzeichnis `~/node-todo.git/hooks`, und fügen Sie den folgenden Inhalt einer Datei namens `post-update` hinzu (wobei Sie `machinename` und `region` durch die Daten Ihres virtuellen MongoDB-Computers ersetzen):

    #!/bin/bash
    
    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info
    
Stellen Sie sicher, dass diese Datei ausführbar ist, indem Sie den folgenden Befehl ausführen:

    chmod 755 post-update

Dieses Skript stellt sicher, dass die aktuelle Serveranwendung gestoppt und der Code im geklonten Repository auf den neuesten Stand aktualisiert wird, alle aktualisierten Abhängigkeiten berücksichtigt werden und der Server neu gestartet wird. Außerdem stellt es sicher, dass die Umgebung zur Vorbereitung auf den Empfang des ersten Anwendungsupdates zum Abrufen der MongoDB-Instanz aus einer Umgebungsvariablen konfiguriert wurde.

### Konfigurieren Ihres Entwicklungscomputers
Nun verknüpfen wir Ihren Entwicklungscomputer mit dem virtuellen Webfrontend-Computer. Dies ist so einfach wie das Hinzufügen des reinen Repositorys auf dem virtuellen Computer als Remoteverzeichnis. Führen Sie hierzu den folgenden Befehl aus (ersetzen Sie *user* durch Ihren Anmeldenamen für den virtuellen Webfrontend-Computer und *machinename* und *region* nach Bedarf):

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Mehr ist nicht erforderlich, um die Pushübertragung von Änderungen auf den virtuellen Webfrontend-Computer, d. h. deren Veröffentlichung, zu ermöglichen.

### Veröffentlichen von Updates

Wir veröffentlichen nun die eine Änderung, die bisher vorgenommen wurde, damit die Anwendung unsere eigene MongoDB-Instanz verwendet:

    git push azure master

Eine ähnliche Ausgabe wie die folgende sollte angezeigt werden:

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

Versuchen Sie nach Abschluss dieses Befehls, die Anwendung in einem Webbrowser zu aktualisieren. Sie sollten sehen können, dass die hier angezeigte TODO-Liste leer und nicht mehr an die freigegebene bereitgestellte MongoDB-Instanz gebunden ist.

Zum Abschluss des Tutorials nehmen wir eine weitere, besser sichtbare Änderung vor. Öffnen Sie auf dem Entwicklungscomputer die Datei „node-todo/public/index.html“ in Ihrem bevorzugten Editor. Ändern Sie im Header „jumbotron“ den Titel von „I‘m a Todo-aholic“ in „I‘m a Todo-aholic on Azure!“.

Jetzt committen wir:

    git commit -am "Azurify the title"

In diesem Fall veröffentlichen wir die Änderung in Azure, bevor wir eine Pushübertragung zurück ins GitHub-Repository ausführen:

    git push azure master

Wenn Sie nach Abschluss dieses Befehls die Webseite aktualisieren, sehen Sie die Änderungen. Da die Änderungen gut aussehen, übertragen Sie sie per Push zurück in das ursprüngliche Remoteverzeichnis:

    git push origin master

## Nächste Schritte
Dieser Artikel zeigte Ihnen, wie eine Node.js-Anwendung auf virtuellen Linux-Computern in Azure bereitgestellt und ausgeführt wird. Weitere Informationen zu virtuellen Linux-Computern in Azure finden Sie unter [Einführung in Linux in Azure](/documentation/articles/virtual-machines-linux-introduction/).
    
Weitere Informationen zum Entwickeln von Node.js-Anwendungen in Azure finden Sie im [Node.js Developer Center](/develop/nodejs/).

<!---HONumber=AcomDC_0211_2016-->