<properties urlDisplayName="Upload a Linux VHD" pageTitle="Erstellen und Hochladen einer Linux-VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that contains a Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains a Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />


# <a id="nonendorsed"> </a>Informationen zu nicht bestätigten Distributionen #

**Wichtig**: Das Azure-Plattform-SLA gilt für virtuelle Computer unter dem Betriebssystem Linux nur dann, wenn eine der [bestätigten Distributionen] verwendet wird(../virtual-machines-linux-endorsed-distributions). Alle Linux-Distributionen, die in der Azure-Image-Galerie bereitgestellt werden, sind bestätigte Distributionen mit der erforderlichen Konfiguration.

- [Linux auf Azure-unterstützten Distributionen](../virtual-machines-linux-endorsed-distributions)
- [Unterstützung für Linux-Bilder in Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Alle auf Azure ausgeführten Distributionen müssen eine Reihe an Voraussetzungen erfüllen, damit sie entsprechend auf der Plattform ausgeführt werden.  Dieser Artikel umfasst in keinem Fall alle Aspekte, da jede Distribution unterschiedlich ist. Es ist durchaus möglich, dass Sie, selbst wenn Sie alle unten aufgeführten Kriterien erfüllen, Ihr Linus-System noch erheblich optimieren müssen, um gewährleisten zu können, dass es ordnungsgemäß auf der Plattform ausgeführt wird.

Daher wird empfohlen, dass Sie nach Möglichkeit mit einem unserer [Linux auf Azure-unterstützten Distributionen](../linux-endorsed-distributions)  beginnen. Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen bestätigten Linux-Distributionen, die auf Azure unterstützt werden:

- **[CentOS-basierte Distributionen](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES & openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**

Der Rest dieses Artikels bietet eine allgemeine Hilfe für das Ausführen Ihrer Linux-Distribution auf Azure.


##Allgemeine Installationshinweise für Linux##

- Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird.  LVM oder [RAID](../virtual-machines-linux-configure-raid) können bei Bedarf auf Datenträgern verwendet werden.

- NUMA wird bei ausgedehnteren VM-Größen aufgrund eines Fehlers in den Linux Kernel-Versionen unterhalb von 2.6.37 nicht unterstützt. Dieses Problem betrifft in erster Linie jene Distributionen, die den vorgeschalteten Red Hat 2.6.32 Kernel verwenden. Bei der manuellen Installation des Azure Linux-Agents (waagent) wird NUMA in der Grub-Konfiguration für den Linux-Kernel automatisch deaktiviert.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen.  Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.


## Linux-Kernelanforderungen ##

Die Treiber für die Linux-Integrationsdienste (Linux Integration Services, LIS) für Hyper-V und Azure tragen direkt zum Linux-Upstream-Kernel bei. Viele Distributionen, die eine neue Linux-Kernelversion (d. h. 3.x) enthalten, verfügen bereits über diese Treiber oder bieten ansonsten zurückportierte Versionen dieser Treiber mit ihren Kerneln.  Diese Treiber werden ständig im Upstream-Kernel mit neuen Fehlerbehebungen und Features aktualisiert. Daher empfiehlt sich, nach Möglichkeit eine [bestätigte Distribution](../linux-endorsed-distributions) auszuführen, die diese Fehlerbehebungen und Updates enthält.

Wenn Sie eine Variante der Red Hat Enterprise Linux-Versionen **6.0-6.3** ausführen, müssen Sie die neuesten LIS-Treiber für Hyper-V installieren. Die Treiber finden Sie [hier](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Ab RHEL **6.4+** (und Ableitungen) sind die LIS-Treiber bereits im Kernel enthalten. Daher sind keine zusätzlichen Installationspakete nötig, um diese Systeme auf Azure auszuführen.

Wenn ein benutzerdefinierter Kernel erforderlich ist, empfiehlt es sich dringend, keine neuere Kernelversion (d. h. **3.8+**) zu verwenden. Für diese Distributionen oder Anbieter, die ihren eigenen Kernel verwalten, empfiehlt sich die Zurückportierung der LIS-Treiber vom Upstream-Kernel zu Ihrem benutzerdefinierten Kernel.  Selbst wenn Sie bereits eine relativ aktuelle Kernelversion ausführen, wird es im Allgemeinen empfohlen, die Upstream-Fehlerbehebungen in den LIS-Treibern nachzuverfolgen und diese bei Bedarf zurückzuportieren. Der LIS-Treiberquelldateien sind in der Datei [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) verfügbar, die sich im Linux-Kernel-Codebaum befindet.

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

Das Fehlen der folgenden Patches haben in einem sehr geringen Ausmaß Probleme auf Azure verursacht. Daher müssen diese im Kernel enthalten sein. Diese Liste ist möglicherweise nicht vollständig für alle Distributionen:

- [ata_piix: defer disks to the Hyper-V drivers by default (Standardmäßiges Rückstellen von Datenträgern zu Hyper-V-Treibern) (in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: Account for in-transit packets in the RESET path (Konto für Pakete auf dem Transportweg im RESET-Pfad) (in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)


## Der Azure Linux-Agent ##

Der [Azure Linux-Agent](../virtual-machines-linux-agent-user-guide) (waagent) ist für die entsprechende Bereitstellung eines virtuellen Linux-Computers in Azure erforderlich. Unter [Linux Agent GitHub repo] können Sie die neueste Version abrufen, Probleme einreichen oder Pull-Anforderungen senden(https://github.com/Azure/WALinuxAgent).

- Der Linux-Agent wird unter der Apache 2.0-Lizenz veröffentlicht. Viele Distributionen stellen RPM- oder DEB-Pakete bereits für den Agent bereit, in einigen Fällen können diese mit ein wenig Aufwand installiert und aktualisiert werden.

- Für den Azure Linux-Agent ist Python v2.6+ erforderlich.

- Für den Agent ist zudem das Modul python-pyasn1 erforderlich. Die meisten Distributionen bieten dieses als ein separates Paket an, das installiert werden kann.

- In einigen Fällen ist der Azure Linux-Agent möglicherweise nicht mit dem NetworkManager kompatibel. Viele der über die Distributionen bereitgestellten RPM-/DEB-Pakete konfigurieren den NetworkManager im Widerspruch zum waagent-Paket. Daher wird beim Installieren des Linux Agent-Pakets der NetworkManager deinstalliert.


## Allgemeine Linux-Systemanforderungen ##

- Ändern Sie die Kernel-Boot-Zeile in Grub oder Grub2, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.

	Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen*, sofern sie vorhanden sind:

		rhgb quiet crashkernel=auto

	Weder der Graphical Boot noch der Quiet Boot sind in einer Cloud-Umgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

	Die Option `crashkernel` kann bei Bedarf konfiguriert gelassen werden. Beachten Sie, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer durch 128 MB oder mehr reduziert, was möglicherweise bei kleineren VM-Größen problematisch sein kann.

- Installieren des Azure Linux-Agents

	Der Azure Linux-Agent ist für das Bereitstellen eines Linux-Images auf Azure erforderlich.  Viele Distributionen stellen den Agent als ein RPM- oder DEB-Paket (das Paket heißt für gewöhnlich "WALinuxAgent" oder "walinuxagent") bereit.  Der Agent kann zudem manuell installiert werden, indem die folgenden Schritte im [Linux Agent Guide] eingehalten werden(../virtual-machines-linux-agent-user-guide).

- Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.

- Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- In "/etc/sudoers" müssen Sie die folgenden Zeilen entfernen oder kommentieren, sofern sie vorhanden sind:

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- Führen Sie abschließend Befehle zum Rückgängigmachen des virtuellen Computers aus:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

- Sie müssen den virtuellen Computer anschließend herunterfahren und die virtuelle Festplatte in Azure hochladen.



<!--HONumber=35_1-->
