<properties
   pageTitle="Erste Schritte mit Docker und Compose auf einem virtuellen Azure-Computer"
   description="Kurze Einführung in das Arbeiten mit Compose und Docker in Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Erste Schritte mit Docker und Compose auf einem virtuellen Azure-Computer


Sie können mithilfe von [Compose](http://github.com/docker/compose) (Nachfolger von *Fig*) schnell komplexe Anwendungen mit Docker auf einem virtuellen Linux-Computer in Azure definieren und ausführen. Mit Compose definieren Sie eine Anwendung mit mehreren Containern in einer einzigen Datei. Anschließend können Sie die Anwendung mit nur einem Befehl auf dem virtuellen Computer ausführen.




## Erstellen einer Azure-VM mit der Docker-VM-Erweiterung und Installieren von Compose

Im Azure Marketplace stehen Ihnen verschieden Azure-Verfahren und Images zur Verfügung, um eine Azure-VM zu erstellen und darauf Docker und Compose zu installieren. Beispielsweise finden Sie unter [Verwenden der Docker-VM-Erweiterung aus der Azure-Befehlszeilenschnittstelle](virtual-machines-docker-with-xplat-cli) ein schnelles Verfahren zum Erstellen einer Ubuntu-VM mit der Docker-VM-Erweiterung. Sie verwenden hierfür die Azure-Befehlszeilenschnittstelle (Azure-CLI) für Mac, Linux oder Windows. Das Beispiel in diesem Artikel verwendet die CLI im **ASM**-Modus (Azure Service Management).


Sobald die Ubuntu-VM mit Docker ausgeführt wird, stellen Sie über SSH eine Verbindung dazu her. Installieren Sie dann mit zwei Befehlen [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md):

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Wenn Sie den Fehler "Zugriff verweigert" erhalten, ist das Verzeichnis /usr/local/bin wahrscheinlich schreibgeschützt. Installieren Sie Compose in diesem Fall als Superuser. Führen Sie `sudo -i` gefolgt von den beiden obigen Befehlen und schließlich `exit` aus.

Führen Sie zum Testen der Compose-Installation Folgendes aus:

```
docker-compose --version
```

Eine Ausgabe ähnlich der folgenden wird angezeigt.```
docker-compose 1.2.0
```


## Erstellen der Konfigurationsdatei docker-compose.yml

Compose verwendet eine Textkonfigurationsdatei namens `docker-compose.yml`, um die Container zu definieren, die auf dem virtuellen Computer ausgeführt werden. Die Datei beinhaltet Angaben zum Image, das für jeden Container ausgeführt werden soll (es kann auch ein Build einer Docker-Datei sein), erforderliche Umgebungsvariablen und Abhängigkeiten, Ports, Links zwischen Containern usw. Details zur Syntax der YML-Datei finden Sie in der [Referenz zu docker-compose.yml](http://docs.docker.com/compose/yml/).

Legen Sie ein Arbeitsverzeichnis auf Ihrem virtuellen Computer an, und erstellen Sie mit Ihrem bevorzugten Texteditor die Datei `docker-compose.yml`. Kopieren Sie probeweise einfach den folgenden Text in die Datei. Diese Konfiguration installiert WordPress (Open-Source-Blogging- und Content Management-System) und die verknüpfte BackEnd-Datenbank MariaDB-SQL mithilfe von Images aus der [Docker-Hub-Registrierung](https://registry.hub.docker.com/_/wordpress/).

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

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

Sie können nun direkt auf dem virtuellen Computer eine Verbindung zu WordPress herstellen, indem Sie zu `http://localhost:8080` navigieren. Wenn Sie über das Internet eine Verbindung mit dem virtuellen Computer herstellen möchten, konfigurieren Sie zunächst einen HTTP-Endpunkt auf dem virtuellen Computer, der den öffentlichen Port 80 dem privaten Port 8080 zuordnet. Führen Sie beispielsweise den folgenden Azure-CLI-Befehl aus:

```
azure vm endpoint create <machine-name> 80 8080

```

Daraufhin sollte der Startbildschirm von WordPress angezeigt werden, in dem Sie die Installation abschließen können.

![WordPress-Startbildschirm][wordpress_start]




## Nächste Schritte

* Weitere Beispiele zum Erstellen und Bereitstellen von Anwendungen mit mehreren Containern finden Sie in der [Composer-Befehlsreferenz](http://docs.docker.com/compose/cli/) und im [-Benutzerhandbuch](http://docs.docker.com/compose/).
* Sie können Docker Compose auch in ein [Docker Swarm-Cluster](virtual-machines-docker-swarm.md) integrieren. Szenarien finden Sie im Artikel über die [Integration von Docker Compose/Swarm](https://github.com/docker/compose/blob/master/SWARM.md).
* Verwenden Sie eine von Ihnen oder der [Community](http://azure.microsoft.com/documentation/templates/) erstellte Vorlage des Azure-Ressourcen-Managers, um eine Azure-VM mit Docker und eine mit Compose eingerichtete Anwendung bereitzustellen. Im Artikel über die [Ubuntu-VM mit Docker und drei Containervorlagen](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) erfahren Sie beispielsweise, wie Sie schnell drei Dienste aus Images in der [Docker-Hub-Registrierung](https://registry.hub.docker.com/) bereitstellen.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png
 

<!---HONumber=July15_HO2-->