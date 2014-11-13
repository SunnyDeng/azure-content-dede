<properties urlDisplayName="Configure RAID on Linux" pageTitle="Konfigurieren von Software-RAID auf einem virtuellen Computer unter Linux in Azure" metaKeywords="raid in Azure, madadm Azure, stripe disks in Azure" description="Erfahren Sie, wie Sie mdadm zum Konfigurieren von RAID auf Linux in Azure verwenden." metaCanonical="http://www.windowsazure.com/de-de/manage/linux/articles/virtual-machines-linux-configure-raid" services="virtual-machines" documentationCenter="" title="" authors="szark" solutions="" writer="szark" manager="timlt" editor=""  />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Konfigurieren des Software-RAID unter Linux

Ein häufiges Szenario ist die Verwendung von Software-RAID auf virtuellen Linux-Computern in Azure, um mehrere angefügte Datenträger als einzelnes RAID-Gerät darzustellen. Dies kann normalerweise angewendet werden, um die Leistung zu verbessern und optimierten Durchsatz im Vergleich zur Verwendung eines einzelnen Datenträgers zu ermöglichen.

## Anfügen von Datenträgern

In der Regel sind zwei oder mehr leere Datenträger erforderlich, um ein RAID-Gerät zu konfigurieren. In diesem Artikel wird nicht erläutert, wie Sie Datenträger an einen virtuellen Linux-Computer anfügen. Eine ausführliche Anleitung, wie Sie einen leeren Datenträger an einen virtuellen Linux-Computer in Azure anfügen, finden Sie im Windows Azure-Artikel [Anfügen eines Datenträgers][Anfügen eines Datenträgers].

> [WACOM.NOTE] Die Größe "ExtraSmall" für virtuelle Computer unterstützt maximal einen angefügten Datenträger pro virtuellem Computer. Ausführliche Informationen zu den Größen virtueller Computer und der Anzahl der unterstützten Datenträger finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Windows Azure][Größen virtueller Computer und Cloud-Dienste für Windows Azure].

## Installieren des mdadm-Dienstprogramms

-   **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install mdadm

-   **CentOS & Oracle Linux**

        # sudo yum install mdadm

-   **SLES und openSUSE**

        # zypper install mdadm

## Erstellen der Datenträgerpartitionen

In diesem Beispiel erstellen wir eine einzelne Datenträgerpartition unter "/dev/sdc". Die neue Datenträgerpartition wird "/dev/sdc1" genannt.

-   Starten Sie "fdisk", um mit dem Erstellen der Partitionen zu beginnen.

        # sudo fdisk /dev/sdc
        Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
        Building a new DOS disklabel with disk identifier 0xa34cb70c.
        Changes will remain in memory only, until you decide to write them.
        After that, of course, the previous content won't be recoverable.

        WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                 switch off the mode (command 'c') and change display units to
                 sectors (command 'u').

-   Drücken Sie an der Eingabeaufforderung 'n', um eine **n**eue Partition zu erstellen:

        Command (m for help): n

-   Drücken Sie anschließend 'p', um ein **p**rimäre Partition zu erstellen:

        Command action
            e   extended
            p   primary partition (1-4)
        p

-   Drücken Sie '1', um Partitionsnummer 1 auszuwählen:

        Partition number (1-4): 1

-   Wählen Sie den Startpunkt der neuen Partition aus, oder drücken Sie einfach `<enter>`, um die Standardposition für die Partition zu Beginn des freien Speicherplatzes auf dem Laufwerk zu übernehmen:

        First cylinder (1-1305, default 1):
        Using default value 1

-   Legen Sie die Größe der Partition fest, z. B. '+10G', um eine 10-Gigabyte-Partition zu erstellen. Oder drücken Sie einfach `<enter>`, um eine einzelne Partition zu erstellen, die das gesamte Laufwerk umfasst:

        Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
        Using default value 1305

-   Ändern Sie anschließend die ID und den Partitions**t**yp von der Standard-ID '83' (Linux) in ID 'fd' (Linux-RAID automatisch):

        Command (m for help): t
        Selected partition 1
        Hex code (type L to list codes): fd

-   Zum Abschluss schreiben Sie die Partitionstabelle in das Laufwerk und beenden "fdisk":

        Command (m for help): w
        The partition table has been altered!

## Erstellen des RAID-Arrays

1.  Im folgenden Beispiel werden drei Partitionen (RAID-Level 0) in "Stripes" auf drei separaten Datenträgern unterteilt (sdc1, sdd1, sde1):

        # sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
          /dev/sdc1 /dev/sdd1 /dev/sde1

In diesem Beispiel wird nach dem Ausführen dieses Befehls ein neues RAID-Gerät namens **/dev/md127** erstellt. Beachten Sie außerdem Folgendes: Falls diese Datenträger zuvor Teil eines anderen, nicht mehr bestehenden RAID-Arrays waren, kann es nötig sein, den `--force`-Parameter zum Befehl `mdadm` hinzuzufügen.

1.  Erstellen des Dateisystems auf dem neuen RAID-Gerät

    **CentOS, Oracle Linux, openSUSE und Ubuntu**

        # sudo mkfs -t ext4 /dev/md127

    **SLES**

        # sudo mkfs -t ext3 /dev/md127

2.  **SLES und openSUSE** - boot.md aktivieren und mdadm.conf erstellen

        # sudo -i chkconfig --add boot.md
        # sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

    > [WACOM.NOTE] Nach den Änderungen an den SUSE-Systemen kann ein Neustart erforderlich sein.

## Hinzufügen des neuen Laufwerks zu /etc/fstab

**Vorsicht:** Eine fehlerhafte Bearbeitung der Datei /etc/fstab kann dazu führen, dass das System sich nicht mehr starten lässt. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der Distribution Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

1.  Erstellen Sie den gewünschten Bereitstellungspunkt für das neue Dateisystem, zum Beispiel:

        # sudo mkdir /data

2.  Beim Bearbeiten von /etc/fstab sollte die **UUID** verwendet werden, um auf das Dateisystem zu verweisen statt auf den Gerätenamen. Verwenden Sie das Hilfsprogramm `blkid`, um die UUID für das neue Dateisystem zu bestimmen:

        # sudo /sbin/blkid
        ...........
        /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3.  Öffnen Sie /etc/fstab in einem Texteditor, und fügen Sie einen Eintrag für das neue Dateisystem hinzu, z. B.:

        UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

    Oder für **SLES und openSUSE**:

        /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

    Speichern und schließen Sie anschließend /etc/fstab.

4.  Testen Sie, ob der Eintrag für /etc/fstab korrekt ist:

        # sudo mount -a

    Wenn dieser Befehl zu einer Fehlermeldung führt, überprüfen Sie die Syntax in der Datei /etc/fstab.

    Führen Sie nun den Befehl `mount` aus, um sicherzustellen, dass das Dateisystem eingebunden wurde:

        # mount
        .................
        /dev/md127 on /data type ext4 (rw)

5.  Optionale Parameter

    Viele Distributionen enthalten entweder den Bereitstellungsparameter `nobootwait` oder `nofail`, die der Datei "/etc/fstab" hinzugefügt werden können. Diese Parameter lassen Fehler beim Bereitstellen eines bestimmten Dateisystems zu und ermöglichen dem Linux-System das Fortsetzen des Startvorgangs, selbst wenn das RAID-Dateisystem nicht ordnungsgemäß geladen werden kann. Weitere Informationen zu diesen Parametern erhalten Sie in der Dokumentation der Distribution.

    Beispiel (Ubuntu):

        UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

    Zusätzlich zu den oben angegebenen Parametern kann der Kernelparameter `bootdegraded=true` das Starten des Systems ermöglichen, selbst wenn das RAID-System beschädigt oder beeinträchtigt ist, weil beispielsweise unabsichtlich ein Datenträger aus dem virtuellen Computer entfernt wurde. Normalerweise kann dies auch den Start des Systems verhindern.

    Informationen zum Bearbeiten der Kernel-Parameter erhalten Sie in der Dokumentation der Distribution. Beispielsweise können diese Parameter in vielen Distributionen (CentOS, Oracle Linux, SLES 11) der Datei "`/boot/grub/menu.lst`" manuell hinzugefügt werden. In Ubuntu kann dieser Parameter der Variablen `GRUB_CMDLINE_LINUX_DEFAULT` unter "/etc/default/grub" hinzugefügt werden.

  [Anfügen eines Datenträgers]: http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/#attachempty
  [Größen virtueller Computer und Cloud-Dienste für Windows Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/dn197896.aspx
