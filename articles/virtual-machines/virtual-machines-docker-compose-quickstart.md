<properties
   pageTitle="Docker und Compose auf einem virtuellen Computer | Microsoft Azure"
   description="Kurze Einführung in das Arbeiten mit Compose und Docker auf virtuellen Azure-Computern."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="danlep"/>

# Erste Schritte mit Docker und Compose auf einem virtuellen Azure-Computer

In diesem Artikel werden die ersten Schritte mit Docker und [Compose](http://github.com/docker/compose) zum Festlegen und Ausführen einer komplexen Anwendung auf einem virtuellen Linux-Computer in Azure beschrieben. Bei Compose (dem Nachfolger von *Fig*) verwenden Sie eine einfache Textdatei zum Definieren einer Anwendung, die aus mehreren Docker-Containern besteht. Anschließend erstellen Sie Ihre Anwendung mit nur einem Befehl, der alle erforderlichen Schritte zur Ausführung der Anwendung auf dem virtuellen Computer ausführt. In diesem Artikel wird beispielsweise veranschaulicht, wie Sie schnell einen WordPress-Blog mit einer MariaDB SQL-Back-End-Datenbank einrichten. Sie können aber auch Compose verwenden, um komplexere Anwendungen einzurichten.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel bezieht sich auf virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell und mit dem klassischen Bereitstellungsmodell erstellt wurden.

Wenn Sie mit Docker und Containern noch nicht vertraut sind, lesen Sie die Informationen unter [Docker High Level Whiteboard](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/) (Whiteboard auf hoher Ebene zu Docker; in englischer Sprache).

## Schritt 1: Einrichten eines virtuellen Linux-Computers als Docker-Host

Im Azure Marketplace stehen Ihnen verschieden Azure-Verfahren und Images zur Verfügung, um einen virtuellen Azure-Computer zu erstellen und als Docker-Host einzurichten. Beispielsweise finden Sie unter [Verwenden der Docker-VM-Erweiterung aus der Azure-Befehlszeilenschnittstelle](virtual-machines-docker-with-xplat-cli-install.md) ein schnelles Verfahren zum Erstellen eines virtuellen Ubuntu-Computers mit der Docker-VM-Erweiterung. Wenn Sie die Docker-VM-Erweiterung verwenden, wird Ihr virtueller Computer automatisch als Docker-Host eingerichtet. Das Beispiel in diesem Artikel veranschaulicht, wie Sie die [Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows ](../xplat-cli-install.md) (die Azure-CLI) im Dienstverwaltungsmodus verwenden, um den virtuellen Computer zu erstellen.

## Schritt 2: Installieren von Compose

Wenn der virtuelle Linux-Computer mit Docker ausgeführt wird, stellen Sie von Ihrem Clientcomputer aus über SSH eine Verbindung mit ihm her. Installieren Sie ggf. [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) mithilfe der folgenden beiden Befehle.

>[AZURE.TIP]Wenn Sie den virtuellen Computer mithilfe der Docker-VM-Erweiterung erstellt haben, wurde Compose bereits für Sie installiert. Überspringen Sie diese Befehle, und fahren Sie mit Schritt 3 fort. Sie müssen Compose nur installieren, wenn Sie Docker selbst auf dem virtuellen Computer installiert haben.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Wenn Sie den Fehler „Zugriff verweigert“ erhalten, ist das Verzeichnis „/usr/local/bin“ auf dem virtuellen Computer wahrscheinlich schreibgeschützt. Installieren Sie Compose in diesem Fall als Superuser. Führen Sie `sudo -i` gefolgt von den beiden obigen Befehlen und schließlich `exit` aus.

Führen Sie zum Testen der Compose-Installation den folgenden Befehl aus:

```
$ docker-compose --version
```

Eine Ausgabe ähnlich der folgenden wird angezeigt.
```
docker-compose 1.3.2
```


## Schritt 3: Erstellen der Konfigurationsdatei „docker-compose.yml“

Als Nächstes erstellen Sie die Datei `docker-compose.yml` (eine reine Textkonfigurationsdatei), um die Docker-Container zu definieren, die auf dem virtuellen Computer ausgeführt werden sollen. Die Datei beinhaltet Angaben zum Image, das für jeden Container ausgeführt werden soll (es kann auch ein Build einer Docker-Datei sein), erforderliche Umgebungsvariablen und Abhängigkeiten, Ports, Links zwischen Containern usw. Details zur Syntax der YML-Datei finden Sie in der [Referenz zu docker-compose.yml](http://docs.docker.com/compose/yml/).

Legen Sie ein Arbeitsverzeichnis auf Ihrem virtuellen Computer an, und erstellen Sie mit Ihrem bevorzugten Texteditor die Datei `docker-compose.yml`. Kopieren Sie probeweise einfach den folgenden Text in die Datei. Diese Konfiguration nutzt Images aus der [Docker-Hub-Registrierung](https://registry.hub.docker.com/_/wordpress/), um WordPress (Open-Source-Blogging- und Content Management-System) und die verknüpfte MariaDB SQL-Back-End-Datenbank zu installieren.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Step 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE]Verwenden Sie zu Beginn die Option **-d**, damit die Container kontinuierlich im Hintergrund ausgeführt werden.

Um zu prüfen, ob die Container ausgeführt werden, geben Sie `docker-compose ps` ein. Die Ausgabe sollte folgendermaßen aussehen:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

Sie können nun direkt auf dem virtuellen Computer eine Verbindung zu WordPress herstellen, indem Sie zu `http://localhost:8080` navigieren. Wenn Sie über das Internet eine Verbindung mit dem virtuellen Computer herstellen möchten, konfigurieren Sie zunächst einen HTTP-Endpunkt auf dem virtuellen Computer, der den öffentlichen Port 80 dem privaten Port 8080 zuordnet. Führen Sie in einer Azure-Dienstverwaltungsbereitstellung den folgenden Azure-CLI-Befehl aus:

```
$ azure vm endpoint create <machine-name> 80 8080

```

Daraufhin sollte der Startbildschirm von WordPress angezeigt werden, auf dem Sie die Installation abschließen und erste Schritte mit der Anwendung ausführen können.

![WordPress-Startbildschirm][wordpress_start]


## Nächste Schritte

* Weitere Beispiele zum Erstellen und Bereitstellen von Anwendungen mit mehreren Containern finden Sie in der [Compose-Befehlsreferenz](http://docs.docker.com/compose/cli/) und im [Compose-Benutzerhandbuch](http://docs.docker.com/compose/).
* Verwenden Sie eine von Ihnen oder der [Community](http://azure.microsoft.com/documentation/templates/) erstellte Vorlage des Azure-Ressourcen-Managers, um eine Azure-VM mit Docker und eine mit Compose eingerichtete Anwendung bereitzustellen. Die Vorlage [WordPress-Blog mit Docker bereitstellen](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) verwendet Docker und Compose, um WordPress schnell mit einem MySQL-Back-End auf einem virtuellen Ubuntu-Computer bereitzustellen.
* Sie können Docker Compose auch in ein [Docker Swarm-Cluster](virtual-machines-docker-swarm.md) integrieren. Szenarien finden Sie im Artikel über die
[Integration von Docker Compose/Swarm](https://github.com/docker/compose/blob/master/SWARM.md).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=Oct15_HO2-->