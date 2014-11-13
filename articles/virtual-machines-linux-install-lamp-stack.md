<properties urlDisplayName="Install LAMP stack" pageTitle="Installieren des LAMP-Stacks auf einem virtuellen Linux-Computer" metaKeywords="" description="Erfahren Sie, wie Sie den LAMP-Stack auf einem virtuellen Linux-Computer in Azure verwenden. Sie k&ouml;nnen die Installation auf Ubuntu oder CentOS vornehmen." metaCanonical="" services="virtual-machines" documentationCenter="" title="Installieren des LAMP-Stacks auf einem virtuellen Linux-Computer in Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Installieren des LAMP-Stacks auf einem virtuellen Linux-Computer in Azure

Ein LAMP-Stack besteht aus den folgenden verschiedenen Elementen:

-   **L**inux - Betriebssystem
-   **A**pache - Webserver
-   **M**ySQL - Datenbankserver
-   **P**HP - Programmiersprache

## Installation auf Ubuntu

Es müssen die folgenden Pakete installiert sein:

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`

Nachdem Sie `apt-get update` zum Aktualisieren der lokalen Paketliste ausgeführt haben, können Sie diese Pakete mit nur einem `apt-get install`-Befehl installieren:

    # sudo apt-get update
    # sudo apt-get install apache2 mysql-server php5 php5-mysql

Nach der Ausführung des oben genannten Befehls werden Sie aufgefordert, diese Pakete und eine Reihe weiterer Abhängigkeiten zu installieren. Drücken Sie "y" und dann die EINGABETASTE, um den Vorgang fortzusetzen, und befolgen Sie weitere Aufforderungen zum Festlegen eines Administratorkennworts für MySQL.

Daraufhin werden die PHP-Erweiterungen installiert, die mindestens für die Verwendung von PHP mit MySQL erforderlich sind. Führen Sie folgenden Befehl aus, um weitere PHP-Erweiterungen anzuzeigen, die als Pakete verfügbar sind:

    # apt-cache search php5

## Installation auf CentOS & Oracle Linux

Es müssen die folgenden Pakete installiert sein:

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

Sie können diese Pakete mit nur einem `yum install`-Befehl installieren:

    # sudo yum install httpd mysql mysql-server php php-mysql

Nach der Ausführung des oben genannten Befehls werden Sie aufgefordert, diese Pakete und eine Reihe weiterer Abhängigkeiten zu installieren. Drücken Sie "y" und dann die EINGABETASTE, um den Vorgang fortzusetzen.

Daraufhin werden die PHP-Erweiterungen installiert, die mindestens für die Verwendung von PHP mit MySQL erforderlich sind. Führen Sie folgenden Befehl aus, um weitere PHP-Erweiterungen anzuzeigen, die als Pakete verfügbar sind:

    # yum search php

## Installation auf SUSE Linux Enterprise Server

Es müssen die folgenden Pakete installiert sein:

-   apache2
-   mysql
-   apache2-mod\_php53
-   php53-mysql

Sie können diese Pakete mit nur einem `zypper install`-Befehl installieren:

    # sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

Nach der Ausführung des oben genannten Befehls werden Sie aufgefordert, diese Pakete und eine Reihe weiterer Abhängigkeiten zu installieren. Drücken Sie "y" und dann die EINGABETASTE, um den Vorgang fortzusetzen.

Daraufhin werden die PHP-Erweiterungen installiert, die mindestens für die Verwendung von PHP mit MySQL erforderlich sind. Führen Sie folgenden Befehl aus, um weitere PHP-Erweiterungen anzuzeigen, die als Pakete verfügbar sind:

    # zypper search php

## Einrichten

1.  Richten Sie **Apache** ein.

    -   Führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Apache-Webserver gestartet wurde:

        -   Ubuntu & SLES: `sudo service apache2 restart`

        -   CentOS & Oracle: `sudo service httpd restart`

    -   Apache lauscht standardmäßig an Port 80. Für den Remotezugriff auf den Apache-Server müssen Sie u. U. einen Endpunkt öffnen. Ausführlichere Anweisungen finden Sie in der Dokumentation zur [Konfiguration von Endpunkten][Konfiguration von Endpunkten].

    -   Jetzt können Sie überprüfen, ob Apache ausgeführt wird und Inhalte bereitstellt. Wechseln Sie im Browser zu `http://[MYSERVICE].cloudapp.net`, wobei **[MYSERVICE]** dem Namen des Cloud-Diensts entspricht, in dem sich der virtuelle Computer befindet. Bei einigen Distributionen kann eine Standardwebseite mit dem Hinweis angezeigt werden, dass der Server ordnungsgemäß arbeitet. Bei anderen kann eine ausführlichere Webseite mit Links zu zusätzlicher Dokumentation und Inhalten zur Konfiguration des Apache-Servers eingeblendet werden.

2.  Richten Sie **MySQL** ein.

    -   Dieser Schritt ist in Ubuntu nicht erforderlich, da bei der Installation des mysql-server-Pakets bereits ein MySQL-`root`-Kennwort angefordert wurde.

    -   Legen Sie in anderen Distributionen das Stammkennwort für MySQL fest, indem Sie den folgenden Befehl ausführen:

            # mysqladmin -u root -p password yourpassword

    -   Anschließend können Sie MySQL mit dem Hilfsprogramm `mysql` oder `mysqladmin` verwalten.

## Weitere nützliche Informationen

Es gibt zahlreiche Ressourcen für die Einrichtung eines LAMP-Stacks in Ubuntu.

-   [][]<https://help.ubuntu.com/community/ApacheMySQLPHP></a>
-   [][1]<http://fedorasolved.org/server-solutions/lamp-stack></a>

  [Konfiguration von Endpunkten]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-set-up-endpoints/
  []: https://help.ubuntu.com/community/ApacheMySQLPHP
  [1]: http://fedorasolved.org/server-solutions/lamp-stack
