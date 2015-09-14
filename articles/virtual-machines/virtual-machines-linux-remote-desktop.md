<properties
	pageTitle="Herstellen einer Verbindung mit einem virtuellen Microsoft Azure Linux-Computer mithilfe von Remotedesktop über Xrdp"
	description="Erfahren Sie mehr zur Installation und Konfiguration von Remotedesktop auf einem virtuellen Microsoft Azure Linux-Computer."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="mingzhan"/>


#Herstellen einer Verbindung mit einem virtuellen Microsoft Azure Linux-Computer mithilfe von Remotedesktop über Xrdp

##Übersicht

RDP (Remote Desktop Protocol) jedoch ein proprietäres Protokoll, das für Windows verwendet wird. Wie können wir mithilfe von RDP eine Remoteverbindung mit einem virtuellen Linux-Computer herstellen?

In dieser Anleitung erhalten Sie die Antwort! Sie finden Informationen zum Installieren und Konfigurieren von Xrdp auf Ihrem virtuellen Microsoft Azure Linux-Computer, und anschließend können Sie diesen mit Remotedesktop auf einem Windows-Computer verbinden.

Xrdp ist ein Open-Source-RDP-Server, der Ihnen das Herstellen einer Verbindung zwischen Ihrem Linux-Server und Remotedesktop auf einem Windows-Computer ermöglicht. Er bietet eine viel bessere Leistung als VNC (Virtual Network Computing). VNC weist Züge von „JPEG“-Qualität und langsames Verhalten auf, während RDP schnell und klar ist.
 

> [AZURE.NOTE]Sie müssen bereits über einen virtuellen Microsoft Azure-Computer verfügen, auf dem Linux ausgeführt wird. Informationen zum Erstellen und Einrichten eines virtuellen Linux-Computers finden Sie im [Lernprogramm zu virtuellen Linux-Computern](virtual-machines-linux-tutorial.md).


##Erstellen eines Endgeräts für Remotedesktop
Wir verwenden das Standardendgerät 3389 für Remotedesktop in diesem Dokument. Richten Sie also wie unten dargestellt Endgerät 3389 als Remotedesktop für Ihren virtuellen Linux-Computer ein:


![image](./media/virtual-machines-linux-remote-desktop/no1.png)


Wenn Sie nicht wissen, wie die Einrichtung eines Endgeräts für Ihren virtuellen Computer funktioniert, lesen Sie die entsprechende [Anleitung](virtual-machines-set-up-endpoints.md).


##Installation von Gnome Desktop

Stellen Sie über Putty eine Verbindung mit Ihrem virtuellen Linux-Computer her, und installieren Sie `Gnome Desktop`.

Für die Linux-Produktfamilie Red Hat verwenden Sie:

	#sudo yum install gnome* "xorg*" -y

Für Debian und Ubuntu verwenden Sie:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Für OpenSUSE verwenden Sie:

	#sudo zypper -y install gnome-session


##Installation von Xrdp

Für die Linux-Produktfamilie Red Hat müssen Sie Ihrem virtuellen Linux-Computer zuerst das EPEL-Repository hinzufügen, um das Xrdp-Paket über `yum` zu installieren; verwenden Sie:

	#sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
	#sudo yum -y install xrdp tigervnc-server tigervnc-server-module xterm

Für Debian und Ubuntu Linux verwenden Sie:

	#sudo apt-get install xrdp


Für OpenSUSE verwenden Sie:

> [AZURE.NOTE]Aktualisieren Sie die OpenSUSE-Version mit der Version, die Sie im nachfolgenden Befehl verwenden; dabei handelt es sich um einen Beispielbefehl für `OpenSUSE 13.2`.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Starten von Xrdp und Festlegen des Xdrp-Dienstes beim Start

Für die Linux-Produktfamilie Red Hat verwenden Sie:

	#sudo service xrdp start
	#sudo chkconfig xrdp on


Für OpenSUSE verwenden Sie:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp
 

##Deaktivieren Sie bei Verwendung der Linux-Produktfamilie Red Hat iptables. 

Verwendung:

	#sudo service iptables stop


##Verwenden Sie Xfce , wenn Sie eine neuere Ubuntu-Version als Ubuntu 12.04LTS verwenden.

Da das aktuelle Xrop nicht in der Lage war, Gnome Desktop aus einer neueren Ubuntu-Version als Ubuntu 12.04LTS zu unterstützen, verwenden wir stattdessen `xfce` Desktop.

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
Starten Sie auf einem Windows-den Computer Remotedesktopclient, geben Sie den DNS-Namen Ihres virtuellen Linux-Computers ein, oder wechseln Sie zum `Dashboard` Ihres virtuellen Computers in Azure-Portal, und klicken Sie auf `Connect`. Daraufhin wird das folgende Anmeldefenster angezeigt:

![image](./media/virtual-machines-linux-remote-desktop/no2.png)

Melden Sie sich mit dem `user` und dem `password` für Ihren virtuellen Linux-Computer an, und nutzen Sie Remotedesktop sofort auf Ihrem virtuellen Microsoft Azure Linux-Computer!


##Weiter
Weitere Informationen zur Verwendung von Xrdp finden Sie [hier](http://www.xrdp.org/).





 

<!---HONumber=September15_HO1-->