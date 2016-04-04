<properties
	pageTitle="Herstellen einer Verbindung mit einem virtuellen Linux-Computer mithilfe von Remotedesktop| Microsoft Azure"
	description="Erfahren Sie, wie Sie Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Microsoft Azure Linux-Computer installieren und konfigurieren."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="mingzhan"/>


#Herstellen einer Verbindung mit einem virtuellen Microsoft Azure Linux-Computer mithilfe von Remotedesktop

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


##Übersicht

RDP (Remote Desktop Protocol) ist ein proprietäres Protokoll für Windows. Wie können wir mit RDP eine Remoteverbindung mit einem virtuellen Linux-Computer (Linux VM, Linux Virtual Machine) herstellen?

In dieser Anleitung erhalten Sie die Antwort! Sie finden Informationen zum Installieren und Konfigurieren von Xrdp auf Ihrem virtuellen Microsoft Azure Linux-Computer, und anschließend können Sie diesen mit Remotedesktop auf einem Windows-Computer verbinden. Wir verwenden einen virtuellen Linux-Computer, der unter Ubuntu oder OpenSUSE ausgeführt wird, als Beispiel in dieser Anleitung.

Xrdp ist ein Open-Source-RDP-Server, der Ihnen das Herstellen einer Verbindung zwischen Ihrem Linux-Server und Remotedesktop auf einem Windows-Computer ermöglicht. Er bietet eine viel bessere Leistung als VNC (Virtual Network Computing). VNC weist Züge von „JPEG“-Qualität und langsames Verhalten auf, während RDP schnell und klar ist.


> [AZURE.NOTE] Sie müssen bereits über einen virtuellen Microsoft Azure-Computer verfügen, auf dem Linux ausgeführt wird. Informationen zum Erstellen und Einrichten eines virtuellen Linux-Computers finden Sie im [Lernprogramm zu virtuellen Azure-Linux-Computern](virtual-machines-linux-cli-create.md).


##Erstellen eines Endgeräts für Remotedesktop
Wir verwenden das Standardendgerät 3389 für Remotedesktop in diesem Dokument. Richten Sie also wie unten dargestellt Endgerät 3389 als Remotedesktop für Ihren virtuellen Linux-Computer ein:


![image](./media/virtual-machines-linux-classic-remote-desktop/no1.png)


Wenn Sie nicht wissen, wie die Einrichtung eines Endgeräts für Ihren virtuellen Computer funktioniert, lesen Sie die entsprechende [Anleitung](virtual-machines-linux-classic-setup-endpoints.md).


##Installation von Gnome Desktop

Stellen Sie über Putty eine Verbindung mit Ihrem virtuellen Linux-Computer her, und installieren Sie `Gnome Desktop`.

Für Ubuntu verwenden Sie:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Für OpenSUSE verwenden Sie:

	#sudo zypper install gnome-session

##Installation von Xrdp

Für Ubuntu verwenden Sie:

	#sudo apt-get install xrdp

Für OpenSUSE verwenden Sie:

> [AZURE.NOTE] Aktualisieren Sie die OpenSUSE-Version mit der Version, die Sie im nachfolgenden Befehl verwenden; dabei handelt es sich um einen Beispielbefehl für `OpenSUSE 13.2`.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Starten von Xrdp und Festlegen des Xdrp-Dienstes beim Start

Für OpenSUSE verwenden Sie:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp

Für Ubuntu wird Xrdp nach der Installation zum Startzeitpunkt automatisch gestartet und aktiviert.

##Verwenden Sie Xfce , wenn Sie eine neuere Ubuntu-Version als Ubuntu 12.04LTS verwenden.

Da das aktuelle Xrdp nicht in der Lage war, Gnome Desktop aus einer neueren Ubuntu-Version als Ubuntu 12.04LTS zu unterstützen, verwenden wir stattdessen `xfce` Desktop.

Installieren Sie `xfce`; verwenden Sie:

    #sudo apt-get install xubuntu-desktop

Aktivieren Sie dann `xfce`; verwenden Sie:

    #echo xfce4-session >~/.xsession

Bearbeiten Sie die Config-Datei `/etc/xrdp/startwm.sh`; verwenden Sie:

    #sudo vi /etc/xrdp/startwm.sh   

Fügen Sie die Zeile `xfce4-session` vor der Zeile `/etc/X11/Xsession` hinzu.

Starten Sie den Xrdp-Dienst neu; verwenden Sie:

    #sudo service xrdp restart


##Herstellen einer Verbindung mit Ihrem virtuellen Linux-Computer auf einem Windows-Computer
Starten Sie auf einem Windows-Computer den Remotedesktopclient, geben Sie den DNS-Namen Ihres virtuellen Linux-Computers ein, oder wechseln Sie zum `Dashboard` Ihres virtuellen Computers im klassischen Azure-Portal, und klicken Sie auf `Connect`, um Ihren virtuellen Linux-Computer zu verbinden. Daraufhin wird das folgende Anmeldefenster angezeigt:

![image](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Melden Sie sich mit dem `user` und dem `password` Ihres virtuellen Linux-Computers an, und nutzen Sie Remotedesktop sofort auf Ihrem virtuellen Microsoft Azure Linux-Computer!


##Weiter
Weitere Informationen zur Verwendung von Xrdp finden Sie [hier](http://www.xrdp.org/).

<!---HONumber=AcomDC_0323_2016-->