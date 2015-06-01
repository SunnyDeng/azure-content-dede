<properties
	pageTitle="Bereitstellen einer Linux-Anwendung mithilfe der Azure-CustomScript-Erweiterung"
	description="Erfahren Sie, Wie die Azure-CustomScript-Erweiterung zum Bereitstellen von Anwendungen auf virtuellen Linux-Computern verwendet wird."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services=""
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="2/23/2015"
	ms.author="guybo"/>

#Bereitstellen einer LAMP-App mithilfe der Azure-CustomScript-Erweiterung für Linux#

Die Azure-CustomScript-Erweiterung für Linux bietet eine Möglichkeit, Ihre virtuellen Computer (VMs) anzupassen, indem Sie beliebigen, in einer von der VM unterstützten Skriptsprache geschriebenen Code ausführen (z. B. Python, Bash usw.) Dies bietet eine sehr flexible Möglichkeit zum Automatisieren der Anwendungsbereitstellung auf verschiedenen Computern.

Sie können die CustomScript-Erweiterung mit dem Azure-Portal, mit PowerShell oder der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli) bereitstellen.

Im vorliegenden Beispiel wird die Bereitstellung einer einfachen LAMP-Anwendung auf Ubuntu mit der xplat-cli verdeutlicht.

## Voraussetzungen

Erstellen Sie für dieses Beispiel zwei Azure-VMs, auf denen Ubuntu 14.04 ausgeführt wird. Diese werden hier als *script-vm* und *lamp-vm* bezeichnet. Verwenden Sie eindeutige Namen, wenn Sie dieses Beispiel ausprobieren. Eine VM dient zur Ausführung der CLI-Befehle, auf der anderen wird die LAMP-App bereitgestellt.

Darüber hinaus benötigen Sie ein Azure Storage-Konto und einen Schlüssel für den Zugriff (Sie erhalten diesen über das Azure-Portal).

Wenn Sie beim Erstellen von Linux-VMs auf Azure Unterstützung benötigen, finden Sie weitere Informationen unter [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md).

Wenngleich bei den Installationsbefehlen von Ubuntu ausgegangen wird, können Sie die allgemeinen Schritte für beliebige Distributionen anpassen.

Auf der  *script-vm*-VM muss xplat-cli installiert sein, mit einer funktionierenden Verbindung zu Azure. Hilfe zu diesem Thema finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](xplat-cli.md).

## Hochladen eines Skripts

In diesem Beispiel wird die CustomScript-Erweiterung auf einer Remote-VM ausgeführt, um den LAMP-Stack zu installieren und eine PHP-Seite zu erstellen. Damit von einem beliebigen Standort aus auf das Skript zugegriffen werden kann, wird es als Azure-Blob hochgeladen.

**Das Skript**

Dieses Skript installiert einen LAMP-Stack auf Ubuntu (einschließlich Einrichtung einer unbeaufsichtigten Installation von MySQL), schreibt eine einfache PHP-Datei und startet Apache:

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**Hochladen**

Speichern Sie das Skript als Textdatei, z. B. *lamp_install.sh*, und laden Sie es dann nach Azure Storage hoch. Diese Aufgabe kann auf einfache Weise mit xplat-cli ausgeführt werden. Im folgenden Beispiel wird die Datei in einen Container namens "scripts" hochgeladen. Hinweis: Wenn der Container nicht vorhanden ist, müssen Sie ihn zunächst erstellen.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Erstellen Sie außerdem eine JSON-Datei, die beschreibt, wie das Skript aus Azure Storage heruntergeladen wird. Speichern Sie diese als *public_config.json* (ersetzen Sie "mystorage" durch den Namen Ihres Speicherkontos):

    {fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Bereitstellen der Erweiterung

Jetzt können wir die CustomScript-Erweiterung für Linux mit xplat-cli auf der Remote-VM bereitstellen:

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

Auf diese Weise wird das *lamp_install.sh*-Skript heruntergeladen und auf der VM namens *lamp-vm* ausgeführt.

Da die App einen Webserver umfasst, müssen Sie daran denken, einen HTTP-Lauschport auf der Remote-VM zu öffnen:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Überwachung und Problembehandlung

Sie können den Fortschritt bei der Ausführung des benutzerdefinierten Skripts mithilfe der Protokolldatei auf der Remote-VM überprüfen. Stellen Sie über SSH einen Verbindung mit *lamp-vm* her, und zeigen Sie mithilfe von "tail" die Protokolldatei an:

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Nachdem die CustomScript-Erweiterung die Ausführung abgeschlossen hat, können Sie zur erstellten PHP-Seite wechseln. Im vorliegenden Beispiel wäre das diese Seite: *http://lamp-vm.cloudapp.net/phpinfo.php*.

## Zusätzliche Ressourcen

Sie können dieselben grundlegenden Schritte auch zur Bereitstellung komplexerer Apps verwenden. In diesem Beispiel wurde das Installationsskript als öffentliches Blob in Azure Storage gespeichert Höhere Sicherheit erzielen Sie, indem Sie das Installationsskript mit [Secure Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) als sicheres Blob speichern.

Nachfolgend finden Sie einige zusätzliche Ressourcen für xplat-cli, Linux und die CustomScript-Erweiterung:

[Automatisieren von Aufgaben zur Anpassung von Linux-VMs mit der CustomScript-Erweiterung](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure-Erweiterungen für Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux und Open-Source-Computing auf Azure](virtual-machines-linux-opensource.md)

<!--HONumber=47-->
