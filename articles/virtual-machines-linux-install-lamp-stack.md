<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Installieren des LAMP-Stacks auf einem virtuellen Linux-Computer in Azure
=========================================================================

Ein LAMP-Stack besteht aus den folgenden verschiedenen Elementen:

-   **L**inux - Betriebssystem
-   **A**pache - Webserver
-   **M**ySQL - Datenbankserver
-   **P**HP - Programmiersprache

Installation auf Ubuntu
-----------------------

Es müssen die folgenden Pakete installiert sein:

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`
-   `libapache2-mod-auth-mysql`
-   `libapache2-mod-php5`
-   `php5-xsl`
-   `php5-gd`
-   `php-pear`

Sie können dies als einen einzelnen `apt-get install`-Befehl ausführen:

    apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear

Installation auf CentOS
-----------------------

Es müssen die folgenden Pakete installiert sein:

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

Sie können dies als einen einzelnen `yum install`-Befehl ausführen:

    yum install httpd mysql mysql-server php-php-mysql

Einrichten
----------

1.  Richten Sie **Apache** ein.

    1.  Sie müssen den Apache-Webserver neu starten. Führen Sie den folgenden Befehl aus:

             sudo /etc/init.d/apache2 restart

    2.  Überprüfen Sie, ob die Installation ausgeführt wird. Verweisen Sie den Browser auf: <http://localhost>. Es sollte "It works!" angezeigt werden.

2.  Richten Sie **MySQL** ein.
    1.  Legen Sie das Stammkennwort für mysql fest, indem Sie den folgenden Befehl ausführen:

             mysqladmin -u root -p password yourpassword

    2.  Melden Sie sich bei der Konsole mit `mysql` oder mehreren MySQL-Clients an.

3.  Richten Sie **PHP** ein.

    1.  Aktivieren Sie den PHP-Modul von Apache, indem Sie den folgenden Befehl ausführen:

             sudo a2enmod php5

    2.  Starten Sie Apache neu, indem Sie den folgenden Befehl ausführen:

             sudo service apache2 restart

Weitere nützliche Informationen
-------------------------------

Es gibt zahlreiche Ressourcen für die Einrichtung eines LAMP-Stacks in Ubuntu.

-   <https://help.ubuntu.com/community/ApacheMySQLPHP>
-   <http://fedorasolved.org/server-solutions/lamp-stack>

