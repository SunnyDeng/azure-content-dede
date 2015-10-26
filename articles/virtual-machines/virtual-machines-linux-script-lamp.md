<properties
	pageTitle="Verwenden der CustomScript-Erweiterung auf einem virtuellen Linux-Computer | Microsoft Azure"
	description="Erfahren Sie, wie die CustomScript-Erweiterung zum Bereitstellen von Anwendungen auf virtuellen Linux-Computern in Azure, die mit dem klassischen Bereitstellungsmodell erstellt wurden, verwendet wird."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Bereitstellen einer LAMP-App mithilfe der Azure-CustomScript-Erweiterung für Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Die Microsoft Azure-CustomScript-Erweiterung für Linux bietet eine Möglichkeit, Ihre virtuellen Computer anzupassen, indem Sie beliebigen, in einer vom virtuellen Computer unterstützten Skriptsprache geschriebenen Code ausführen (z. B. Python, Bash usw.) Dies bietet eine sehr flexible Möglichkeit zum Automatisieren der Anwendungsbereitstellung auf verschiedenen Computern.

Sie können die CustomScript-Erweiterung mit dem Azure-Portal, mit Windows PowerShell oder der Azure-Befehlszeilenschnittstelle (Azure-CLI) bereitstellen.

In diesem Artikel verwenden wir die Azure-Befehlszeilenschnittstelle zum Bereitstellen einer einfachen LAMP-Anwendung auf einem virtuellen Ubuntu-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde.

## Voraussetzungen

Erstellen Sie für dieses Beispiel zuerst zwei virtuelle Azure-Computer, auf denen Ubuntu 14.04 oder höher ausgeführt wird. Die virtuellen Computer heißen *script-vm* und *lamp-vm*. Verwenden Sie beim Erstellen der virtuellen Computer eindeutige Namen. Einer davon dient zur Ausführung der CLI-Befehle, auf dem anderen wird die LAMP-App bereitgestellt.

Darüber hinaus benötigen Sie ein Azure Storage-Konto und einen Schlüssel für den Zugriff (Sie erhalten diesen über das Azure-Portal).

Weitere Informationen zum Erstellen von Linux-VMs auf Azure finden Sie unter [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md).

Für die Installationsbefehle wird von Ubuntu ausgegangen, Sie können die Installation jedoch an alle unterstützten Linux-Distribution anpassen.

Auf dem virtuellen Computer „script-vm“ muss die Azure-CLI mit einer funktionierenden Verbindung zu Azure installiert sein. Hilfe zu diesem Thema finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).

## Hochladen von Skripts

Wir führen mit der CustomScript-Erweiterung ein Skript auf einem virtuellen Remotecomputer aus, um den LAMP-Stack zu installieren und eine PHP-Seite zu erstellen. Damit von einem beliebigen Standort aus auf das Skript zugegriffen werden kann, wird es als Azure-Blob hochgeladen.

### Skriptübersicht

Das nächste Skriptbeispiel installiert einen LAMP-Stack auf Ubuntu (einschließlich Einrichtung einer unbeaufsichtigten Installation von MySQL), schreibt eine einfache PHP-Datei und startet Apache.

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

### Skript hochladen

Speichern Sie das Skript als Textdatei (z. B. *lamp\_install.sh*), und laden Sie es dann in Azure Storage hoch. Diese Aufgabe kann auf einfache Weise mit der Azure-CLI ausgeführt werden. Im folgenden Beispiel wird die Datei in einen Container namens "scripts" hochgeladen. Wenn der Container nicht vorhanden ist, müssen Sie ihn zunächst erstellen.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Erstellen Sie außerdem eine JSON-Datei, die beschreibt, wie das Skript aus Azure Storage heruntergeladen wird. Speichern Sie diese als *public\_config.json* (ersetzen Sie "mystorage" durch den Namen Ihres Speicherkontos):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Bereitstellen der Erweiterung

Jetzt können wir mit dem nächsten Befehl die CustomScript-Erweiterung für Linux mit der Azure-CLI auf dem virtuellen Remotecomputer bereitstellen.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

Mit dem vorhergehenden Befehl wird das Skript *lamp\_install.sh* heruntergeladen und auf dem virtuellen Computer namens *lamp-vm* ausgeführt.

Da die App einen Webserver umfasst, müssen Sie daran denken, mithilfe des nächsten Befehls einen HTTP-Lauschport auf dem virtuellen Remotecomputer zu öffnen.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Überwachung und Problembehandlung

Sie können den Fortschritt bei der Ausführung des benutzerdefinierten Skripts mithilfe der Protokolldatei auf dem virtuellen Computer überprüfen. Stellen Sie mit dem nächsten Befehl über SSH eine Verbindung mit *lamp-vm* her, und zeigen Sie mithilfe von "tail" die Protokolldatei an.

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Nach dem Ausführen der CustomScript-Erweiterung können Sie die von Ihnen erstellte PHP-Seite nach Informationen durchsuchen. Die PHP-Seite für das Beispiel in diesem Artikel finden Sie unter **http://lamp-vm.cloudapp.net/phpinfo.php*.

## Zusätzliche Ressourcen

Sie können dieselben grundlegenden Schritte auch zur Bereitstellung komplexerer Apps verwenden. In diesem Beispiel wurde das Installationsskript als öffentliches Blob in Azure Storage gespeichert. Höhere Sicherheit erzielen Sie, indem Sie das Installationsskript mit [Secure Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) als sicheres Blob speichern.

Nachfolgend finden Sie einige zusätzliche Ressourcen für die Azure-CLI, Linux und die CustomScript-Erweiterung.

[Automatisieren von Aufgaben zur Anpassung von Linux-VMs mit der CustomScript-Erweiterung](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure-Erweiterungen für Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux und Open-Source-Computing auf Azure](virtual-machines-linux-opensource.md)

<!---HONumber=Oct15_HO3-->