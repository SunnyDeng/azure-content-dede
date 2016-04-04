<properties
	pageTitle="Optimieren virtueller Linux-Computer in Azure | Microsoft Azure"
	description="Hier finden Sie einige Optimierungstipps, um mit virtuellen Linux-Computern in Azure eine optimale Leistung zu erzielen."
	keywords="virtuelle Linux-Maschine, virtuelle Maschine Linux, virtuelle Ubuntu-Maschine" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="rclaus"/>

# Optimieren virtueller Linux-Computer in Azure

Virtuelle Linux-Maschinen (VM) lassen sich einfach über die Befehlszeile oder über das Portal erstellen. In diesem Tutorial erfahren Sie, wie Sie mit virtuellen Computern im Rahmen der Microsoft Azure Platform optimale Ergebnisse erzielen. In diesem Thema wird eine Ubuntu Server-VM verwendet, aber Sie können virtuelle Linux-Computer auch mithilfe [Ihrer eigenen Images als Vorlagen](virtual-machines-linux-create-upload-generic.md) erstellen.

## Voraussetzungen
Es wird vorausgesetzt, dass Sie bereits über ein funktionierendes Azure-Abonnement verfügen ([Anmeldung für eine kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)) sowie [die Azure-Befehlszeilenschnittstelle installiert](../xplat-cli-install.md) und einen virtuellen Computer in Ihrem Azure-Abonnement bereitgestellt haben. Vor der Verwendung von Azure müssen Sie sich zunächst bei Ihrem Abonnement authentifizieren. Geben Sie hierzu über die Azure-Befehlszeilenschnittstelle einfach `azure login` ein, um den interaktiven Prozess zu starten.

## Betriebssystem-Datenträger von Azure
Nach dem Erstellen eines virtuellen Linux-Computers in Azure sind diesem zwei Datenträger zugeordnet: „/dev/sda“ (der Betriebssystem-Datenträger) und „/dev/sdb“ (der temporäre Datenträger). Verwenden Sie den Betriebssystem-Hauptdatenträger (/dev/sda) ausschließlich für das Betriebssystem. Er ist für schnelles Starten des virtuellen Computers optimiert eignet sich nicht besonders für Ihre Workloads. Es empfiehlt sich, dem virtuellen Computer mindestens einen Datenträger anzufügen, um eine beständige und optimierte Datenspeicherung zu erhalten.

## Hinzufügen von Datenträgern für Größe und Leistung 
Abhängig von der gewählten VM-Größe können Sie bis zu 16 zusätzliche Datenträger (A-Serie), 32 Datenträger (D-Serie) bzw. 64 Datenträger (G-Serie) anfügen, die jeweils eine Größe von bis zu 1 TB besitzen können. Orientieren Sie sich beim Hinzufügen zusätzlicher Datenträger an Ihren Platz- und IOPS-Anforderungen. Jeder Datenträger hat ein Leistungsziel von 500 IOPS (Storage Standard) bzw. von bis zu 5000 IOPS (Storage Premium). Weitere Informationen zu Storage Premium-Datenträgern finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../storage/storage-premium-storage-preview-portal.md).

Um bei Storage Premium-Datenträgern mit der Cacheeinstellung „ReadOnly“ oder „None“ die höchstmögliche IOPS-Leistung zu erzielen, müssen so genannte „Barriers“ beim Einbinden des Dateisystems in Linux deaktiviert werden. „Barriers“ werden nicht benötigt, da Schreibvorgänge auf Storage Premium-Datenträger bei diesen Cacheeinstellungen beständig sind.
- Wenn Sie **reiserFS** verwenden, deaktivieren Sie Sperren mithilfe der Bereitstellungsoption „barrier=none“ (verwenden Sie zum Aktivieren von Sperren „barrier=flush“).
- Wenn Sie **ext3/ext4** verwenden, deaktivieren Sie Sperren mithilfe der Bereitstellungsoption „barrier=0“ (verwenden Sie zum Aktivieren von Sperren „barrier=1“).
- Wenn Sie **XFS** verwenden, deaktivieren Sie Sperren mithilfe der Bereitstellungsoption „nobarrier“ (verwenden Sie zum Aktivieren von Sperren „barrier“).

## Überlegungen zum Speicherkonto
Wenn Sie den virtuellen Linux-Computer in Azure erstellen, empfiehlt es sich, Datenträger aus Speicherkonten anzufügen, die sich in der gleichen Region befinden wie Ihr virtueller Computer, um weite Strecken zu vermeiden und die Netzwerklatenz zu minimieren. Die Kapazität jedes Storage Standard-Kontos ist auf maximal 20.000 IOPS und eine Größe von 500 TB beschränkt. Dies entspricht etwa 40 stark ausgelasteten Datenträgern (einschließlich Betriebssystem-Datenträger und aller von Ihnen erstellten Datenträger). Bei Storage Premium-Konten gilt kein IOPS-Limit, die Größe ist jedoch auf 32 TB beschränkt.

Wenn Sie sehr IOPS-intensive Workloads verwenden und sich für Storage Standard-Datenträger entschieden haben, müssen Sie die Datenträger ggf. auf mehrere Speicherkonten aufteilen, um zu verhindern, dass Sie das Limit von 20.000 IOPS erreichen. Der virtuelle Computer kann eine Mischung aus Datenträgern verschiedener Speicherkonten und Speicherkontotypen enthalten, um eine optimale Konfiguration zu erreichen.

## Temporäres Laufwerk des virtuellen Computers
Beim Erstellen eines neuen virtuellen Computers stellt Azure standardmäßig einen Betriebssystem-Datenträger (/dev/sda) und einen temporären Datenträger (/dev/sdb) bereit. Alle weiteren Datenträger, die Sie hinzufügen, werden als „/dev/sdc“, „/dev/sdd“, „/dev/sde“ usw. angezeigt. Die Daten auf dem temporären Datenträger (/dev/sdb) sind nicht beständig und können in bestimmten Fällen verloren gehen – etwa, wenn die Größe des virtuellen Computers geändert oder der virtuelle Computer neu bereitgestellt oder aufgrund von Wartungsarbeiten neu gestartet wird. Größe und Typ des temporären Datenträgers hängen mit der Größe des virtuellen Computers zusammen, die Sie zum Zeitpunkt der Bereitstellung ausgewählt haben. Im Falle eines virtuellen Premium-Computers (DS-, G- und DS\_V2-Serie) wird das temporäre Laufwerk durch eine lokale SSD unterstützt, um eine höhere Leistung von bis zu 48.000 IOPS zu erzielen.

## Linux-Auslagerungsdatei
Über den Azure Marketplace bereitgestellte VM-Images verfügen über einen in das Betriebssystem integrierten VM-Linux-Agent, der dem virtuellen Computer die Interaktion mit verschiedenen Azure-Diensten ermöglicht. Falls Sie ein Standardimage aus dem Azure Marketplace bereitgestellt haben, gehen Sie wie folgt vor, um die Einstellungen für die Linux-Auslagerungsdatei korrekt zu konfigurieren:

Ändern Sie in der Datei **/etc/waagent.conf** zwei Einträge. Diese steuern, ob eine dedizierte Auslagerungsdatei vorhanden ist und welche Größe sie besitzt. Folgende Parameter müssen geändert werden: - ResourceDisk.EnableSwap=N - ResourceDisk.SwapSizeMB=0 Diese müssen wie folgt geändert werden: - ResourceDisk.EnableSwap=Y - ResourceDisk.SwapSizeMB={angemessene Größe in MB} Nach dieser Änderung muss der Agent (waagent) oder der virtuelle Linux-Computer neu gestartet werden, damit die Änderungen wirksam werden. Zeigen Sie mithilfe des Befehls `free` den freien Speicherplatz an, um sich zu vergewissern, dass die Änderungen umgesetzt wurden und eine Auslagerungsdatei erstellt wurde. Im folgenden Beispiel wurde infolge der Änderung der Datei „waagent.conf“ eine Auslagerungsdatei mit einer Größe von 512 MB erstellt:

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## E/A-Scheduling-Algorithmus für Storage Premium
Mit Version 2.6.18 des Linux-Kernels wurde der standardmäßige E/A-Scheduling-Algorithmus von „Deadline“ in „CFQ“ (Completely Fair Queuing) geändert. Bei wahlfreien E/A-Zugriffsmustern sind die Leistungsunterschiede zwischen „CFQ“ und „Deadline“ vernachlässigbar. Bei SSD-basierten Datenträgern, bei denen vorwiegend ein sequenzielles E/A-Muster verwendet wird, lässt sich durch einen Wechsel zum zuvor verwendeten NOOP- oder Deadline-Algorithmus unter Umständen eine bessere E/A-Leistung erzielen.

### Anzeigen des aktuellen E/A-Schedulers
Verwenden Sie den folgenden Befehl:

	admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Sie sehen die folgende Ausgabe; diese gibt den aktuellen Scheduler an.

	noop [deadline] cfq

###Ändern des aktuellen Geräts (/dev/sda) des E/A-Scheduling-Algorithmus
Verwenden Sie die folgenden Befehle:

	azureuser@mylinuxvm:~$ sudo su -
	root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Nur für /dev/sda ist diese Einstellung nicht sinnvoll. Sie muss für alle Datenträger festgelegt werden, bei denen vorwiegend ein sequenzielles E/A-Muster verwendet wird.

Die folgende Ausgabe zeigt an, dass grub.cfg erfolgreich neu erstellt wurde und dass der Standard-Scheduler zu NOOP aktualisiert wurde.

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Bei Redhat-Verteilungen benötigen Sie nur den folgenden Befehl:

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## Erzielen höherer IOPS-Werte mithilfe von Software-RAID

Wenn Ihre Workloads mehr IOPS erfordern als ein einzelner Datenträger bereitstellen kann, müssen Sie eine Software-RAID-Konfiguration mit mehreren Datenträgern verwenden. Da Azure bereits Datenträgerresilienz auf der lokalen Fabric-Ebene bietet, erreichen Sie die höchste Leistung mit einer RAID-0-Konfiguration. Sie müssen in der Azure-Umgebung neue Datenträger bereitstellen/erstellen und sie an den virtuellen Linux-Computer anfügen, bevor Sie die Laufwerke partitionieren, formatieren und einbinden. Ausführlichere Informationen zum Konfigurieren einer Software-RAID-Einrichtung für den virtuellen Linux-Computer in Azure finden Sie unter **[Konfigurieren von Software-RAID unter Linux](virtual-machines-linux-configure-raid.md)**.


## Nächste Schritte

Wie bei allen Optimierungen müssen auch hier vor und nach jeder Änderung Tests ausgeführt werden, um die Auswirkungen der jeweiligen Änderung zu ermitteln. Die Optimierung ist ein schrittweiser Prozess, der auf unterschiedlichen Computern in Ihrer Umgebung unterschiedliche Ergebnisse liefert. Was bei einer Konfiguration funktioniert, ist für andere möglicherweise ungeeignet.

Einige nützliche Links zu weiteren Ressourcen:
- [Premium-Speicher: Hochleistungsspeicher für Workloads in Azure Virtual Machine](../storage/storage-premium-storage-preview-portal.md)
- [Benutzerhandbuch für Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md)
- [Optimieren der MySQL-Leistung auf virtuellen Azure Linux-Computern](virtual-machines-linux-classic-optimize-mysql.md)
- [Konfigurieren von Software-RAID unter Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0323_2016-->