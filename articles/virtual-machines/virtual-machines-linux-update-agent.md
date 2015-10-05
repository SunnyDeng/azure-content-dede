<properties
	pageTitle="Aktualisieren des Azure Linux-Agent von GitHub | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie den Azure Linux-Agent für Ihren virtuellen Linux-Computer in Azure auf die neueste Version von GitHub aktualisieren."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Aktualisieren des Azure Linux-Agent auf einem virtuellen Computer auf die neueste Version von GitHub

Zum Aktualisieren Ihres [Azure Linux-Agent](https://github.com/Azure/WALinuxAgent) auf einem virtuellen Linux-Computer benötigen Sie:

1. einen laufenden virtuellen Linux-Computer in Azure
2. Sie sind mit diesem virtuellen Linux-Computer über SSH verbunden.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Verwalten einer Ressource mit dem Ressourcen-Manager- oder klassischen Bereitstellungsmodell.

> [AZURE.NOTE]Wenn Sie diese Aufgabe von einem Windows-Computer ausführen, können Sie mit Putty eine SSH-Verbindung zum Linux-Computer herstellen. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird](virtual-machines-linux-how-to-log-on.md).

Linux-Distributionen mit Azure-Unterstützung haben das Azure Linux Agent-Paket in ihren Repositorys. Bitte überprüfen Sie dies und installieren Sie nach Möglichkeit die neueste Version aus dem Distrbutions-Repository zuerst.

Für Ubuntu, geben Sie einfach ein:

    #sudo apt-get install walinuxagent

Für CentOS, geben Sie ein:

    #sudo yum install waagent

Für Oracle Linux müssen Sie sich vergewissern, dass das Add-On-Repository in der Datei `/etc/yum.repo.d/public-yum-ol6.repo` oder `/etc/yum.repo.d/public-yum-ol7.repo` aktiviert ist. Geben Sie dann Folgendes ein:

    #sudo yum install WALinuxAgent

Normalerweise benötigen Sie nicht mehr. Wenn Sie aus irgendeinem Grund jedoch die Installation von https://github.com direkt verwenden müssen, befolgen Sie diese Schritte.


## Installieren von wget

Melden Sie sich in Ihrem virtuellen Computer über SSH an.

Installieren Sie wget (bei einigen Distributionen nicht standardmäßig installiert, wie z. B. Redhat CentOS und Oracle Linux-Versionen 6.4 und 6.5) durch Eingabe von `#sudo yum install wget` in der Befehlszeile.


## Aktuelle Version herunterladen

Öffnen Sie [die Version des Azure Linux-Agent auf GitHub](https://github.com/Azure/WALinuxAgent/releases) auf einer Webseite, und finden Sie die neueste Versionsnummer heraus. (Die aktuelle Version finden Sie durch Eingabe von `#waagent --version`.)

###Geben Sie für Version 2.0.x Folgendes ein:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   In der folgenden Zeile wird als Beispiel Version 2.0.14 verwendet:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

###Geben Sie für Version 2.1.x oder höher Folgendes ein:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   In der folgenden Zeile wird als Beispiel Version 2.1.0 verwendet:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

##Installieren des Linux-Agents

###Verwenden Sie für Version 2.0.x Folgendes:

 Waagent ausführbar machen

    #chmod +x waagent

 Kopieren Sie die neue ausführbare Datei nach in/usr/Sbin /

  Verwenden Sie für die meisten Linux-Distributionen Folgendes:

      #sudo cp waagent /usr/sbin

  Für CoreOS verwenden Sie:

    #sudo cp waagent /usr/share/oem/bin/

###Verwenden Sie für Version 2.1.x Folgendes:

Sie müssen ggf. zuerst das Paket `setuptools` installieren (siehe [hier](https://pypi.python.org/pypi/setuptools)). Führen Sie nachstehenden Befehl aus:

    #sudo python setup.py install

## Waagent-Dienst neu starten

Für die meisten Linux-Distributionen:

    #sudo service waagent restart

Für Ubuntu verwenden Sie:

    #sudo service walinuxagent restart

Für CoreOS verwenden Sie:

    #sudo systemctl restart waagent

## Bestätigen Sie die Azure Linux Agent-Version

    #waagent -version

Für CoreOS funktioniert oben genannter Befehl möglicherweise nicht.

Sie sehen, dass die Linux Agent-Version auf die neue Version aktualisiert wurde.

Weitere Informationen zu Azure Linux Agent finden Sie in der [Azure Linux Agent-Infodatei](https://github.com/Azure/WALinuxAgent).

<!---HONumber=Sept15_HO4-->