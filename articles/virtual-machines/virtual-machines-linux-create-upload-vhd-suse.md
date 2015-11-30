<properties
	pageTitle="Erstellen und Hochladen einer SUSE-Linux-VHD in Azure"
	description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein SUSE-Linux-Betriebssystem enthält."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="szark"/>

# Vorbereiten eines virtuellen SLES- oder openSUSE-Computers für Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

##Voraussetzungen##

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein SUSE- oder openSUSE-Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

 - Mit [SUSE Studio](http://www.susestudio.com) können Sie Ihre SLES/openSUSE-Images für Azure und Hyper-V auf einfache Weise erstellen und verwalten. Dies ist die empfohlene Vorgehensweise zum Anpassen Ihrer eigenen SUSE- und openSUSE-Images. Die folgenden offiziellen Images in der SUSE Studio Gallery können in Ihr eigenes SUSE Studio heruntergeladen oder geklont werden:

  - [SLES 11 SP3 für Azure in SUSE Studio Gallery](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)
  - [openSUSE 13.1 für Azure in SUSE Studio Gallery](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)


- Als Alternative zum Erstellen einer eigenen VHD veröffentlicht SUSE auf [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007) auch BYOS-Images (Bring Your Own Subscription) für SLES.


**Installationshinweise zu SLES/openSUSE**

- Das VHDX-Format wird in Azure nicht unterstützt, sondern nur **virtuelle Festplatten mit fester Größe**. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. LVM oder [RAID](virtual-machines-linux-configure-raid.md) können bei Bedarf auf Datenträgern verwendet werden.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.


## Vorbereiten von SUSE Linux Enterprise Server 11 SP3 ##

1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2. Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3. Registrieren Sie Ihr SUSE Linux Enterprise-System, um das Herunterladen von Updates und das Installieren von Paketen zu ermöglichen.

4. Aktualisieren Sie das System mit den neuesten Patches:

		# sudo zypper update

5. Installieren Sie den Azure Linux Agent aus dem SLES-Repository:

		# sudo zypper install WALinuxAgent

6. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür „/boot/grub/menu.lst“ in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.

7.	Es wird empfohlen, die Datei "/etc/sysconfig/network/dhcp" zu bearbeiten und den Parameter `DHCLIENT_SET_HOSTNAME` wie folgt zu ändern:

		DHCLIENT_SET_HOSTNAME="no"

8.	Kommentieren Sie in "/etc/sudoers" die folgenden Zeilen aus, sofern sie vorhanden sind, oder entfernen Sie sie:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

10.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.


----------

## Vorbereiten von openSUSE 13.1+ ##

1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2. Klicken Sie auf **Verbinden**, um das Fenster für den virtuellen Computer zu öffnen.

3. Führen Sie in der Shell den Befehl "`zypper lr`" aus. Wenn dieser Befehl eine Ausgabe zurückgibt, die ähnlich wie folgende aussieht (beachten Sie, dass die Versionsnummern abweichen können):

		# | Alias                 | Name                  | Enabled | Refresh
		--+-----------------------+-----------------------+---------+--------
		1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
		2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
		3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

	dann werden die Repositorys wie erwartet konfiguriert, und es sind keine Anpassungen notwendig.

	Wenn der Befehl "Keine Repositorys definiert..." zurückgibt, verwenden Sie die folgenden Befehle zum Hinzufügen dieser Repositorys:

		# sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
		# sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
		# sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

	Sie können dann überprüfen, ob die Repositorys hinzugefügt wurden, indem Sie den Befehl "`zypper lr`" erneut ausführen. Falls eines der relevanten Update-Repositorys nicht aktiviert ist, können Sie es mit dem folgenden Befehl aktivieren:

		# sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Aktualisieren Sie den Kernel auf die neueste verfügbare Version:

		# sudo zypper up kernel-default

	Oder aktualisieren Sie das System mit allen neuen Patches:

		# sudo zypper update

5.	Installieren des Azure Linux-Agents

		# sudo zypper install WALinuxAgent

6.	Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür „/boot/grub/menu.lst“ in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Entfernen Sie außerdem die folgenden Parameter aus der Kernel-Boot-Zeile, sofern diese vorhanden sind:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

7.	Es wird empfohlen, die Datei "/etc/sysconfig/network/dhcp" zu bearbeiten und den Parameter `DHCLIENT_SET_HOSTNAME` wie folgt zu ändern:

		DHCLIENT_SET_HOSTNAME="no"

8.	**Wichtig:** Kommentieren Sie in "/etc/sudoers" die folgenden Zeilen aus, oder entfernen Sie sie, sofern vorhanden:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

10.	Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Stellen Sie sicher, dass der Azure Linux Agent beim Start ausgeführt wird:

		# sudo systemctl enable waagent.service

13. Klicken Sie im Hyper-V-Manager auf **Aktion -> Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

<!---HONumber=Nov15_HO4-->