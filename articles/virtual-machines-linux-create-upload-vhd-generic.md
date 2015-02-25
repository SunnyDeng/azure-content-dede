<properties pageTitle="Erstellen und Hochladen einer Linux-VHD in Azure" description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Linux-Betriebssystem enthält." services="virtual-machines" documentationCenter="" authors="szarkos" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/13/2015" ms.author="szarkos"/>


# <a id="nonendorsed"> </a>Informationen zu nicht bestätigten Verteilungen #

**Wichtig**: Das Azure-Plattform-SLA gilt für virtuelle Computer unter dem Betriebssystem Linux nur dann, wenn eine der [bestätigten Verteilungen](../virtual-machines-linux-endorsed-distributions) verwendet wird. Alle Linux-Verteilungen, die im Azure-Image-Katalog bereitgestellt werden, sind bestätigte Verteilungen mit der erforderlichen Konfiguration.

- [Linux auf Azure - bestätigte Verteilungen](../virtual-machines-linux-endorsed-distributions)
- [Unterstützung für Linux-Bilder in Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Alle auf Azure ausgeführten Verteilungen müssen eine Reihe an Voraussetzungen erfüllen, damit sie entsprechend auf der Plattform ausgeführt werden.  Dieser Artikel umfasst in keinem Fall alle Aspekte, da jede Verteilung unterschiedlich ist. Es ist durchaus möglich, dass Sie, selbst wenn Sie alle unten aufgeführten Kriterien erfüllen, Ihr Linus-System noch erheblich optimieren müssen, um gewährleisten zu können, dass es ordnungsgemäß auf der Plattform ausgeführt wird.

Daher wird empfohlen, dass Sie nach Möglichkeit mit einer unserer [bestätigten Linux-Verteilungen auf Azure](../linux-endorsed-distributions) beginnen. Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen bestätigten Linux-Verteilungen, die auf Azure unterstützt werden:

- **[CentOS-basierte Verteilungen](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES und openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**

Der Rest dieses Artikels bietet eine allgemeine Hilfe für das Ausführen Ihrer Linux-Verteilung auf Azure.


## <a id="linuxinstall"> </a>Allgemeine Installationshinweise für Linux ##

- Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

- Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird.  LVM oder [RAID](../virtual-machines-linux-configure-raid) können bei Bedarf auf Datenträgern verwendet werden.

- NUMA wird bei ausgedehnteren VM-Größen aufgrund eines Fehlers in den Linux Kernel-Versionen unterhalb von 2.6.37 nicht unterstützt. Dieses Problem betrifft in erster Linie jene Verteilungen, die den vorgeschalteten Red Hat 2.6.32 Kernel verwenden. Bei der manuellen Installation des Azure Linux-Agents (waagent) wird NUMA in der Grub-Konfiguration für den Linux-Kernel automatisch deaktiviert.

- Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen.  Weitere Informationen dazu finden Sie in den folgenden Schritten.

- Alle virtuellen Festplatten müssen eine Größe aufweisen, die ein Vielfaches von 1 MB ist.


### Installieren von Linux ohne Hyper-V ###

In einigen Fällen enthalten Linux-Installationsprogramme möglicherweise keine Treiber für Hyper-V in der anfänglichen Ramdisk (initrd oder initramfs), bis sie erkennen, dass sie eine Hyper-V-Umgebung ausführen.  Wenn ein anderes Virtualisierungssystem (d. h. Virtualbox, KVM usw.) zur Vorbereitung des Linux-Image verwendet wird,, müssen Sie möglicherweise die initrd neu erstellen, um sicherzustellen, dass mindestens die Kernel-Module  `hv_vmbus` und  `hv_storvsc` auf der anfänglichen Ramdisk verfügbar sind.  Dies ist ein bekanntes Problem, zumindest auf Systemen, die auf der vorgeschalteten Red Hat-Verteilung basieren.

Der Mechanismus für die Neuerstellung des initrd- oder initramfs-Image variiert je nach der Verteilung. In der Dokumentation zu Ihrer Verteilung oder über den Support erhalten Sie Informationen zur geeigneten Vorgehensweise.  Es folgt ein Beispiel für das Neuerstellen von initrd mit dem Hilfsprogramm  `mkinitrd`:

Sichern Sie zuerst das bestehende initrd-Image:

	# cd /boot
	# sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Erstellen Sie initrd dann neu und verwenden Sie dazu die Kernel-Module  `hv_vmbus` und  `hv_storvsc`:

	# sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### Ändern der Größe von virtuellen Festplatten ###

VHD-Images auf Azure benötigen eine virtuelle Größe, die auf 1 MB ist. Normalerweise sollten mit Hyper-V erstellte virtuelle Festplatten bereits korrekt ausgerichtet sein. Wenn die virtuelle Festplatte nicht ordnungsgemäß ausgerichtet wurde, wird möglicherweise sinngemäß die folgende Fehlermeldung angezeigt, wenn Sie ein  *Image* von der virtuellen Festplatte zu erstellen versuchen:

	"The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs)."

Zur Umgehung des Problems können Sie die Größe des virtuellen Computers mithilfe der Hyper-V-Manager-Konsole oder des Powershell-Cmdlet [Resize-VHD](http://technet.microsoft.com/de-de/library/hh848535.aspx) ändern.

Wenn Sie nicht in einer Windows-Umgebung arbeiten, sollten Sie die Konvertierung (falls erforderlich) mit qemu-img durchführen und die Größe der virtuellen Festplatte ändern:

 1. Das direkte Ändern der Größe der virtuellen Festplatte mithilfe von Tools wie `qemu-img` oder `vbox-manage` kann zu einer nicht startfähigen virtuellen Festplatte führen. Daher empfiehlt es sich, die virtuelle Festplatte zuerst in ein ROH-Datenträgerimage zu konvertieren. Wenn das VM-Image bereits als ROH-Datenträgerimage erstellt wurde (die Standardeinstellung für einige Hypervisoren wie KVM), können Sie diesen Schritt überspringen:

		# qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Berechnen Sie die erforderliche Größe des Datenträger-Image, um sicherzustellen, dass die virtuelle Größe auf 1 MB ausgerichtet wird. Das folgenden Bash-Shell-Skript kann dabei helfen. Das Skript verwendet "`qemu-img info`", um die virtuelle Größe des Datenträger-Image zu ermitteln, und berechnet dann die Größe bis zum nächsten 1 MB:

		rawdisk="MyLinuxVM.raw"
		vhddisk="MyLinuxVM.vhd"

		MB=$((1024*1024))
		size=$(qemu-img info -f raw --output json "$rawdisk" | \
		       gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

		rounded_size=$((($size/$MB + 1)*$MB))
		echo "Rounded Size = $rounded_size"

 3. Ändern Sie die Größe des Rohdatenträgers mit $rounded_size, wie im obigen Skript festgelegt:
 
		# qemu-img resize MyLinuxVM.raw $rounded_size

 4. Konvertieren Sie nun den ROH-Datenträger zurück in eine virtuelle Festplatte mit fester Größe:

		# qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## Linux-Kernelanforderungen ##

Die Treiber für die Linux-Integrationsdienste (Linux Integration Services, LIS) für Hyper-V und Azure tragen direkt zum Linux-Upstream-Kernel bei. Viele Verteilungen, die eine neue Linux-Kernelversion (d. h. 3.x) enthalten, verfügen bereits über diese Treiber oder bieten ansonsten zurückportierte Versionen dieser Treiber mit ihren Kerneln.  Diese Treiber werden ständig im Upstream-Kernel mit neuen Fehlerbehebungen und Features aktualisiert. Daher empfiehlt sich, nach Möglichkeit eine [bestätigte Verteilung](../linux-endorsed-distributions) auszuführen, die diese Fehlerbehebungen und Updates enthält.

Wenn Sie eine Variante der Red Hat Enterprise Linux-Versionen **6.0-6.3** ausführen, müssen Sie die neuesten LIS-Treiber für Hyper-V installieren. Die Treiber finden Sie [hier](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Ab RHEL **6.4+** (und Ableitungen) sind die LIS-Treiber bereits im Kernel enthalten. Daher sind keine zusätzlichen Installationspakete nötig, um diese Systeme auf Azure auszuführen.

Wenn ein benutzerdefinierter Kernel erforderlich ist, empfiehlt es sich dringend, keine neuere Kernelversion (d. h. **3.8+**) zu verwenden. Für diese Verteilungen oder Anbieter, die ihren eigenen Kernel verwalten, empfiehlt sich die Zurückportierung der LIS-Treiber vom Upstream-Kernel zu Ihrem benutzerdefinierten Kernel.  Selbst wenn Sie bereits eine relativ aktuelle Kernelversion ausführen, wird es im Allgemeinen empfohlen, die Upstream-Fehlerbehebungen in den LIS-Treibern nachzuverfolgen und diese bei Bedarf zurück zu portieren. Der Ort der LIS-Treiberquelldateien ist in der Datei [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) in der Linux-Kernelquellstruktur verfügbar:

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

Das Fehlen der folgenden Patches haben in einem sehr geringen Ausmaß Probleme auf Azure verursacht. Daher müssen diese im Kernel enthalten sein. Diese Liste ist möglicherweise nicht vollständig für alle Verteilungen:

- [ata_piix: defer disks to the Hyper-V drivers by default (Standardmäßiges Rückstellen von Datenträgern zu Hyper-V-Treibern, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: Account for in-transit packets in the RESET path](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) (Konto für Pakete auf dem Transportweg im RESET-Pfad, in englischer Sprache)


## Der Azure Linux-Agent ##

Der [Azure Linux-Agent](../virtual-machines-linux-agent-user-guide) (waagent) ist für die entsprechende Bereitstellung eines virtuellen Linux-Computers in Azure erforderlich. Unter [Linux Agent GitHub repo](https://github.com/Azure/WALinuxAgent) können Sie die neueste Version abrufen, Probleme einreichen oder Pull-Anforderungen senden.

- Der Linux-Agent wird unter der Apache 2.0-Lizenz veröffentlicht. Viele Verteilungen stellen RPM- oder DEB-Pakete bereits für den Agent bereit, in einigen Fällen können diese mit ein wenig Aufwand installiert und aktualisiert werden.

- Für den Azure Linux-Agent ist Python v2.6+ erforderlich.

- Für den Agent ist zudem das Modul python-pyasn1 erforderlich. Die meisten Verteilungen bieten dieses als ein separates Paket an, das installiert werden kann.

- In einigen Fällen ist der Azure Linux-Agent möglicherweise nicht mit dem NetworkManager kompatibel. Viele der über die Verteilungen bereitgestellten RPM-/DEB-Pakete konfigurieren den NetworkManager im Widerspruch zum waagent-Paket. Daher wird beim Installieren des Linux Agent-Pakets der NetworkManager deinstalliert.


## Allgemeine Linux-Systemanforderungen ##

- Ändern Sie die Kernel-Boot-Zeile in Grub oder Grub2, um die folgenden Parameter einzubinden. Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.

	Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu  *entfernen*, sofern sie vorhanden sind:

		rhgb quiet crashkernel=auto

	Weder der Graphical Boot noch der Quiet Boot sind in einer Cloud-Umgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.

	Die Option  `crashkernel` kann bei Bedarf konfiguriert gelassen werden. Beachten Sie, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer durch 128 MB oder mehr reduziert, was möglicherweise bei kleineren VM-Größen problematisch sein kann.

- Installieren des Azure Linux-Agents

	Der Azure Linux-Agent ist für das Bereitstellen eines Linux-Images auf Azure erforderlich. Viele Verteilungen stellen den Agent als ein RPM- oder DEB-Paket (das Paket heißt für gewöhnlich  'WALinuxAgent' oder  'walinuxagent') bereit. Der Agent kann zudem manuell installiert werden, indem die folgenden Schritte im [Linux Agent Guide] eingehalten werden(../virtual-machines-linux-agent-user-guide).

- Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

- Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

	Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein  *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

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




<!--HONumber=42-->
