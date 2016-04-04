<properties
	pageTitle="Aktualisieren des Azure Linux-Agent von GitHub | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie den Azure Linux-Agent für Ihren virtuellen Linux-Computer in Azure auf die neueste Version von GitHub aktualisieren."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# Aktualisieren des Azure Linux-Agent auf einem virtuellen Computer auf die neueste Version von GitHub

Zum Aktualisieren Ihres [Azure Linux-Agents](https://github.com/Azure/WALinuxAgent) auf einem virtuellen Linux-Computer benötigen Sie:

1. Einen ausgeführten virtuellen Linux-Computer in Azure.
2. Eine SSH-Verbindung mit diesem virtuellen Linux-Computer.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


> [AZURE.NOTE] Wenn Sie diese Aufgabe von einem Windows-Computer ausführen, können Sie mit PuTTY eine SSH-Verbindung mit dem Linux-Computer herstellen. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Linux ausgeführt wird](virtual-machines-linux-classic-log-on.md).

Linux-Distributionen mit Azure-Unterstützung haben das Azure Linux Agent-Paket in ihren Repositorys. Bitte überprüfen Sie dies und installieren Sie nach Möglichkeit die neueste Version aus dem Distrbutions-Repository zuerst.

Für Ubuntu, geben Sie einfach ein:

    #sudo apt-get install walinuxagent

Für CentOS geben Sie ein:

    #sudo yum install waagent

Stellen Sie bei Oracle Linux sicher, dass das `Addons`-Repository aktiviert ist. Bearbeiten Sie die Datei `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) oder `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), und ändern Sie in dieser Datei unter **[ol6\_addons]** oder **[ol7\_addons]** die Zeile `enabled=0` in `enabled=1`.

Installieren Sie anschließend die aktuelle Version des Azure Linux-Agents. Geben Sie dazu Folgendes ein:

    #sudo yum install WALinuxAgent

Wenn Sie das Add-On-Repository nicht finden, können Sie diese Zeilen einfach am Ende der REPO-Datei Ihrer Oracle Linux-Version hinzufügen:

Für virtuelle Oracle Linux 6-Computer:

  [ol6\_addons] name=Add-Ons for Oracle Linux $releasever ($basearch) baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86\_64 gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6 gpgcheck=1 enabled=1

Für virtuelle Oracle Linux 7-Computer:

  [ol7\_addons] name=Oracle Linux $releasever Add ons ($basearch) baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/ gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle gpgcheck=1 enabled=0

Geben Sie anschließend Folgendes ein:

    #sudo yum update WALinuxAgent

Normalerweise benötigen Sie nicht mehr. Wenn Sie aus irgendeinem Grund jedoch die Installation von https://github.com direkt verwenden müssen, befolgen Sie diese Schritte.


## Installieren von wget

Melden Sie sich in Ihrem virtuellen Computer über SSH an.

Installieren Sie wget (bei einigen Distributionen nicht standardmäßig installiert, wie z. B. Redhat CentOS und Oracle Linux-Versionen 6.4 und 6.5) durch Eingabe von `#sudo yum install wget` in der Befehlszeile.


## Herunterladen der aktuellen Version

Öffnen Sie [die Version des Azure Linux-Agent auf GitHub](https://github.com/Azure/WALinuxAgent/releases) auf einer Webseite, und finden Sie die neueste Versionsnummer heraus. (Die aktuelle Version finden Sie durch Eingabe von `#waagent --version`.)

### Geben Sie für Version 2.0.x Folgendes ein:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   In der folgenden Zeile wird als Beispiel Version 2.0.14 verwendet:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### Geben Sie für Version 2.1.x oder höher Folgendes ein:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   In der folgenden Zeile wird als Beispiel Version 2.1.0 verwendet:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## Installieren des Azure Linux-Agents

### Verwenden Sie für Version 2.0.x Folgendes:

 Waagent ausführbar machen:

    #chmod +x waagent

 Kopieren Sie die neue ausführbare Datei nach „/usr/sbin/“.

  Verwenden Sie für die meisten Linux-Distributionen Folgendes:

    #sudo cp waagent /usr/sbin

  Für CoreOS verwenden Sie:

    #sudo cp waagent /usr/share/oem/bin/

  Bei einer neuen Installation des Azure Linux-Agents führen Sie Folgendes aus:
 
    #sudo /usr/sbin/waagent -install -verbose

### Verwenden Sie für Version 2.1.x Folgendes:

Sie müssen ggf. zuerst das Paket `setuptools` installieren (siehe [hier](https://pypi.python.org/pypi/setuptools)). Führen Sie anschließend Folgendes aus:

    #sudo python setup.py install

## Neustarten des Waagent-Diensts

Für die meisten Linux-Distributionen:

    #sudo service waagent restart

Für Ubuntu verwenden Sie:

    #sudo service walinuxagent restart

Für CoreOS verwenden Sie:

    #sudo systemctl restart waagent

## Bestätigen der Azure Linux Agent-Version

    #waagent -version

Für CoreOS funktioniert oben genannter Befehl möglicherweise nicht.

Sie sehen, dass die Azure Linux Agent-Version auf die neue Version aktualisiert wurde.

Weitere Informationen zum Azure Linux-Agent finden Sie in der [Azure Linux-Agent-Infodatei](https://github.com/Azure/WALinuxAgent).

<!---HONumber=AcomDC_0323_2016-->