<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine running Linux in Azure" metaKeywords="disk VM Azure, initialize new disk Azure, initialize disk Azure Linux, attaching empty disk Azure" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="http://www.windowsazure.com/de-de/manage/windows/how-to-guides/attach-a-disk/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav"></tags>

# Anfügen eines Datenträgers an einen virtuellen Linux-Computer

Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. In beiden Fällen sind die Datenträger eigentlich VHD-Dateien, die sich in einem Azure-Speicherkonto befinden. Außerdem müssen Sie in beiden Fällen den Datenträger nach dem Anfügen initialisieren, damit er verwendet werden kann.

> [WACOM.NOTE] Es empfiehlt sich, einen oder mehrere separate Datenträger zu verwenden, um die Daten eines virtuellen Computers zu speichern. Wenn Sie einen virtuellen Azure-Computer erstellen, verfügt dieser über einen Betriebssystem-Datenträger und über einen temporären Datenträger. **Verwenden Sie den temporären Datenträger nicht zum Speichern von Daten.** Wie der Name schon sagt, bietet er nur temporäre Speicherung. Er bietet keine Redundanz oder Sicherungsmöglichkeiten, da er sich nicht in Azure Storage befindet.
> Der temporäre Datenträger wird in der Regel vom Azure Linux-Agent verwaltet und automatisch in **/mnt/resource** (oder **/mnt** auf Ubuntu-Images) eingebunden. Andererseits kann dem Datenträger unter Linux vom Kernel der Name `/dev/sdc` zugewiesen werden. In diesem Fall muss diese Ressource partitioniert, formatiert und eingebunden werden. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux-Agent][Benutzerhandbuch für Azure Linux-Agent].

-   [Gewusst wie: Anfügen eines leeren Datenträgers][Gewusst wie: Anfügen eines leeren Datenträgers]
-   [Gewusst wie: Anfügen eines vorhandenen Datenträgers][Gewusst wie: Anfügen eines vorhandenen Datenträgers]
-   [Gewusst wie: Initialisieren eines neues Datenträgers unter Linux][Gewusst wie: Initialisieren eines neues Datenträgers unter Linux]

[WACOM.INCLUDE [howto-attach-disk-windows-linux][howto-attach-disk-windows-linux]]

## <span id="initializeinlinux"></span></a>Gewusst wie: Initialisieren eines neues Datenträgers unter Linux

1.  Stellen Sie eine Verbindung mit dem virtuellen Computer her. Eine Beschreibung der dafür erforderlichen Schritte finden Sie unter [Anmeldung auf einem virtuellen Linux-Computer][Anmeldung auf einem virtuellen Linux-Computer].

2.  Geben Sie im SSH-Fenster den folgenden Befehl ein, und geben Sie anschließend das Kennwort für das Konto ein, dass Sie zur Verwaltung des virtuellen Computers erstellt haben:

        # sudo grep SCSI /var/log/messages

    In den angezeigten Meldungen können Sie den Bezeichner des letzten hinzugefügten Datenträgers finden.

    ![Datenträgermeldungen abrufen][Datenträgermeldungen abrufen]

3.  Geben Sie im SSH-Fenster den folgen Befehl ein, um ein neues Gerät zu erstellen, und geben Sie anschließend das Kennwort für das Konto ein:

        # sudo fdisk /dev/sdc

    > [WACOM.NOTE] In diesem Beispiel müssen Sie eventuell auf manchen Distributionen `sudo -i` verwenden, wenn /sbin oder /usr/sbin sich nicht in Ihrem `$PATH` befinden.

4.  Geben Sie **n** ein, um eine neue Partition zu erstellen.

    ![Neues Gerät erstellen][Neues Gerät erstellen]

5.  Geben Sie **p** ein, um die Partition zur primären zu machen, geben Sie **1** ein, um sie zur ersten Partition zu machen, und drücken Sie die Eingabetaste, um den Standardwert für den Zylinder zu übernehmen.

    ![Partition erstellen][Partition erstellen]

6.  Geben Sie **p** ein, um die Details zum Datenträger anzuzeigen, der gerade partitioniert wird.

    ![Informationen zum Datenträger auflisten][Informationen zum Datenträger auflisten]

7.  Geben Sie **w** ein, um die Einstellungen für den Datenträger zu schreiben.

    ![Datenträgeränderungen schreiben][Datenträgeränderungen schreiben]

8.  Erstellen Sie das Dateisystem auf der neuen Partition. Geben Sie beispielsweise folgenden Befehl und dann das Kontokennwort ein:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Dateisystem erstellen][Dateisystem erstellen]

    > [WACOM.NOTE] Beachten Sie, dass SUSE Linux Enterprise 11-Systeme nur schreibgeschützte Zugriffe für ext4-Dateisysteme unterstützen. Für diese Systeme wird empfohlen, das neue Dateisystem als ext3 statt als ext4 zu formatieren.

9.  Erstellen Sie ein Verzeichnis zum Einbinden des neuen Dateisystems. Geben Sie beispielsweise folgenden Befehl und dann das Kontokennwort ein:

        # sudo mkdir /datadrive

10. Geben Sie den folgenden Befehl ein, um das Laufwerk einzubinden:

        # sudo mount /dev/sdc1 /datadrive

    Der Datenträger kann nun als **/datadrive** verwendet werden.

11. Fügen Sie das neue Laufwerk zu /etc/fstab hinzu:

    Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es zur Datei /etc/fstab hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique IDentifier) in /etc/fstab zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (d. h. auf /dev/sdc1) zu verweisen. Sie können das Hilfsprogramm **blkid** verwenden, um den UUID des neuen Laufwerks herauszufinden:

        # sudo -i blkid

    Die Ausgabe sieht etwa wie folgt aus:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"

    > [WACOM.NOTE] Eine falsche Bearbeitung der Datei **/etc/fstab** könnte zu einem System führen, das nicht gestartet werden kann. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der Distribution Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

    Öffnen Sie als Nächstes die Datei **/etc/fstab** in einem Texteditor. Beachten Sie, dass "/etc/fstab" eine Systemdatei ist, sodass Sie `sudo` zum Bearbeiten der Datei verwenden müssen. Beispiel:

        # sudo vi /etc/fstab

    In diesem Beispiel verwenden wir den UUID-Wert für das neue Gerät **/dev/sdc1**, das in den vorhergehenden Schritten erstellt wurde, und den Bereitstellungspunkt **/datadrive**. Fügen Sie am Ende der Datei **/etc/fstab** die folgende Zeile hinzu:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

    In auf SUSE Linux basierenden Systemen müssen Sie eventuell ein etwas unterschiedliches Format verwenden:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2

    Sie können nun testen, ob das Dateisystem korrekt eingebunden wurde, indem Sie die Einbindung aufheben und wiederherstellen. Verwenden Sie dafür den zuvor erstellten Beispielbereitstellungspunkt `/datadrive`:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Wenn der `mount`-Befehl zu einem Fehler führt, prüfen Sie die Datei "/etc/fstab" auf korrekte Syntax. Wenn zusätzliche Datenlaufwerke oder Partitionen erstellt werden, müssen Sie diese ebenfalls einzeln in /etc/fstab einfügen.

    > [WACOM.NOTE] Wenn Sie später einen Datenträger entfernen, ohne fstab zu bearbeiteten, kann der Start des virtuellen Computers fehlschlagen. Für den Fall, dass dieses Problem häufiger auftritt, bieten die meisten Distributionen die fstab-Optionen `nofail` und/oder `nobootwait`, die einen Systemstart auch dann erlauben, wenn der Datenträger zur Startzeit nicht eingebunden werden kann. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution.

  [Benutzerhandbuch für Azure Linux-Agent]: http://www.windowsazure.com/de-de/manage/linux/how-to-guides/linux-agent-guide/
  [Gewusst wie: Anfügen eines leeren Datenträgers]: #attachempty
  [Gewusst wie: Anfügen eines vorhandenen Datenträgers]: #attachexisting
  [Gewusst wie: Initialisieren eines neues Datenträgers unter Linux]: #initializeinlinux
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Anmeldung auf einem virtuellen Linux-Computer]: ../virtual-machines-linux-how-to-log-on/
  [Datenträgermeldungen abrufen]: ./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png
  [Neues Gerät erstellen]: ./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png
  [Partition erstellen]: ./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png
  [Informationen zum Datenträger auflisten]: ./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png
  [Datenträgeränderungen schreiben]: ./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png
  [Dateisystem erstellen]: ./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png
