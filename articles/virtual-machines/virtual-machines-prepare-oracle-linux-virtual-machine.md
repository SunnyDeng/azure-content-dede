<properties
pageTitle="Vorbereiten eines virtuellen Oracle Linux-Computers für Azure | Microsoft Azure"
description="Schritt-für-Schritt-Konfiguration eines virtuellen Oracle-Computers unter Linux in Microsoft Azure."
services="virtual-machines"
authors="bbenz"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# Vorbereiten eines virtuellen Oracle Linux-Computers für Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Vorbereiten eines virtuellen Oracle Linux 6.4+-Computers für Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

-   [Vorbereiten eines virtuellen Oracle Linux 7.0+-Computers für Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Oracle Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren von Hyper-V und Erstellen eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

**Installationshinweise für Oracle Linux**

- Der Red Hat-kompatible Kernel von Oracle und der UEK3 (Unbreakable Enterprise Kernel) von Oracle werden beide auf Hyper-V und Azure unterstützt. Nehmen Sie eine Aktualisierung auf den neuesten Kernel vor, während Sie Ihre Oracle Linux-VHD vorbereiten, um die besten Ergebnisse zu erzielen.

- Der UEK2 von Oracle wird auf Hyper-V und Azure nicht unterstützt, da er nicht die erforderlichen Treiber enthält.

- Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem Cmdlet "convert-vhd" in das VHD-Format konvertieren.

- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder [RAID](virtual-machines-linux-configure-raid.md) können bei Bedarf auf Datenträgern verwendet werden.

- NUMA wird bei größeren VMs aufgrund eines Fehlers in den Linux Kernel-Versionen unter 2.6.37 nicht unterstützt. Dieses Problem betrifft in erster Linie jene Verteilungen, die den Red Hat 2.6.32-Upstream-Kernel verwenden. Bei der manuellen Installation des Azure Linux Agent (waagent) wird NUMA in der GRUB-Konfiguration für das Linux-Kernel automatisch deaktiviert. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.

- Stellen Sie sicher, dass das `Addons`-Repository aktiviert ist. Bearbeiten Sie die Datei `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) oder `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), und ändern Sie in dieser Datei unter **[ol6\_addons]** oder **[ol7\_addons]** die Zeile `enabled=0` in `enabled=1`.


## Oracle Linux 6.4+
Sie müssen die spezifischen Konfigurationsschritte im Betriebssystem für den virtuellen Computer abschließen, welcher unter Azure ausgeführt werden soll.

1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2. Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3. Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

		# sudo rpm -e --nodeps NetworkManager

	>[AZURE.NOTE] Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl mit einer Fehlermeldung fehl. Dies entspricht dem erwarteten Verhalten.

4. Erstellen Sie eine Datei mit der Benennung **network** im Verzeichnis "/etc/sysconfig/", die den folgenden Text enthält:

	`NETWORKING=yes` `HOSTNAME=localhost.localdomain`

5.  Erstellen Sie eine Datei mit der Benennung **ifcfg-eth0** im Verzeichnis "/etc/sysconfig/network-scripts/", die den folgenden Text enthält:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
			TYPE=Ethernet
			USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

6.  Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers in Azure oder Hyper-V zu Problemen führen:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7.  Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

		# chkconfig network on

8.  Installieren Sie python-pyasn1, indem Sie den folgenden Befehl ausführen:

		# sudo yum install python-pyasn1

9.  Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür "/boot/grub/menu.lst" in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Dadurch wird NUMA aufgrund eines Fehlers im Red Hat-kompatiblen Kernel von Oracle deaktiviert.

	Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen*:

		rhgb quiet crashkernel=auto

	Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

	Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

10.  Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

11.  Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

		# sudo yum install WALinuxAgent

	Beachten Sie, dass durch eine Installation des WALinuxAgent-Pakets die NetworkManager- und NetworkManager-gnome-Pakete entfernt werden, sofern sie nicht bereits wie in Schritt 2 beschrieben entfernt wurden.

12.  Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in "/etc/waagent.conf" entsprechend:

		ResourceDisk.Format=y

		ResourceDisk.Filesystem=ext4

		ResourceDisk.MountPoint=/mnt/resource

		ResourceDisk.EnableSwap=y

		ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

13.  Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

14.  Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## Oracle Linux 7.0+
**Änderungen in Oracle Linux 7**

Das Vorbereiten eines virtuellen Oracle Linux 7-Computers für Azure ähnelt dem Vorgehen für Oracle Linux 6. Es gibt jedoch einige wichtige Unterschiede zu beachten:

-   Sowohl der Red Hat-kompatible Kernel als auch der UEK3 von Oracle werden in Azure unterstützt. Der UEK3-Kernel wird empfohlen.

-   Das NetworkManager-Paket führt nicht mehr zu Konflikten mit dem Azure Linux Agent. Dieses Paket ist standardmäßig installiert, und Sie sollten es nicht entfernen.

-   GRUB2 wird nun als standardmäßiger Bootloader verwendet. Daher hat sich die Prozedur für das Bearbeiten der Kernelparameter geändert (siehe unten).

-   XFS ist nun das Standarddateisystem. Das EXT4-Dateisystem kann bei Bedarf weiterhin verwendet werden.

**Konfigurationsschritte**

1.  Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2.  Klicken Sie auf **Verbinden**, um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3.  Erstellen Sie eine Datei mit der Benennung **network** im Verzeichnis "/etc/sysconfig/", die den folgenden Text enthält:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.  Erstellen Sie eine Datei mit der Benennung **ifcfg-eth0** im Verzeichnis "/etc/sysconfig/network-scripts/", die den folgenden Text enthält:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

5.  Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

		# sudo chkconfig network on

7.  Installieren Sie das Paket python-pyasn1, indem Sie den folgenden Befehl ausführen:

		# sudo yum install python-pyasn1

8.  Führen Sie den folgenden Befehl aus, um die aktuellen yum-Metadaten zu löschen und um Updates zu installieren.

		# sudo yum clean all
		# sudo yum -y update

9.  Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu "/etc/default/grub" in einem Text-Editor, und bearbeiten Sie den Parameter "GRUB\_CMDLINE\_LINUX". Beispiel:

		GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen*:

		rhgb quiet crashkernel=auto

	Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

	Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert. Dies kann bei kleineren virtuellen Computern problematisch sein.

10.  Sobald Sie die Bearbeitung von "/etc/default/grub" abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11.  Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

12.  Installieren Sie den Azure Linux Agent, indem Sie den folgenden Befehl ausführen:

		# sudo yum install WALinuxAgent

13.  Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in "/etc/waagent.conf" entsprechend:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048 ## NOTE: Set this to whatever you need it to be.

14.  Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

15.  Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

<!---HONumber=AcomDC_0211_2016-->