
# Anfügen eines Datenträgers an einen virtuellen Computer

* [Konzepte](#concepts)
* [Gewusst wie: Anfügen eines vorhandenen Datenträgers](#attachexisting)
* [Gewusst wie: Anfügen eines leeren Datenträgers](#attachempty)
* [Gewusst wie: Initialisieren eines neues Datenträgers unter Windows
  Server 2008 R2](#initializeinWS)
* [Gewusst wie: Initialisieren eines neues Datenträgers unter
  Linux](#initializeinlinux)

## <a id="concepts" ></a>Konzepte

Sie können einen Datenträger an einen virtuellen Computer anfügen, um Anwendungsdaten zu speichern. Ein Datenträger ist eine virtuelle Festplatte (virtual hard disk, VHD), die Sie mit Azure entweder lokal auf Ihrem Computer oder in der Cloud erstellen können. Datenträger werden auf virtuellen Computern auf dieselbe Weise verwaltet wie auf einem Server in Ihrem Büro.

Sie können das Verwaltungsportal verwenden, um einen Datenträger mit Daten hochzuladen und an den virtuellen Computer anzufügen oder um einen leeren Datenträger zum selben Speicherkonto hinzuzufügen, das auch der virtuelle Computer verwendet. In diesem Artikel werden diese Prozesse beschrieben. Wenn Sie einen leeren Datenträger anfügen möchten, der sich in einem anderen Speicherkonto befindet, verwenden Sie das Cmdlet [Add-AzureDataDisk][1], das im Azure PowerShell-Modul zur Verfügung steht. Informationen zum Herunterladen dieses Moduls finden Sie auf der Seite [Downloads][2].

Der virtuelle Computer wird nicht gestoppt, um den Datenträger hinzuzufügen. Die Anzahl der Datenträger, die Sie an einen virtuellen Computer anfügen können, hängt von der Größe des virtuellen Computers ab. Informationen über Größen von virtuellen Computern und Datenträgern finden Sie unter [Größen virtueller Computer und Clouddienste für Azure][3].

> [WACOM.NOTE] Der Azure-Speicher unterstützt Blobs bis zu einer Größe
> von 1 TB, was einer VHD mit einer maximalen virtuellen Größe von
> 999 GB entspricht. Wenn Sie allerdings Hyper-V zur Erstellung einer
> neuen VHD verwenden, ist die von Ihnen angegebene Größe die virtuelle
> Größe. Wenn Sie die VHD in Azure verwenden möchten, geben Sie eine
> Größe von bis zu 999 GB an.

**Datenträger und Ressourcendatenträger**  
 Datenträger befinden sich im Azure-Speicher und können zur dauerhaften Speicherung von Dateien und Anwendungsdaten verwendet werden.

An jeden virtuellen Computer ist auch ein temporärer lokaler *Ressourcendatenträger* angefügt. Da die Daten auf einem Ressourcendatenträger bei Neustarts verloren gehen können, werden Ressourcendatenträger oft von Anwendungen und Prozessen zur vorübergehenden (temporären) Datenspeicherung auf dem virtuellen Computer verwendet. Außerdem dienen sie zur Speicherung von Auslagerungsdateien für das Betriebssystem.

Unter Windows erhält der Ressourcendatenträger den Laufwerksbuchstaben **D:** . Unter Linux wird der Ressourcendatenträger normalerweise vom Azure Linux Agent verwaltet und automatisch an **/mnt/resource** (oder auf Ubuntu-Images an **/mnt**) angefügt. Weitere Informationen erhalten Sie im [Azure Linux Agent User Guide][4] (Benutzerhandbuch für Azure Linux, in englischer Sprache).

Weitere Informationen zur Verwendung von Datenträgern finden Sie unter
[Verwalten von Datenträgern und Images][5].

## <a id="attachexisting" ></a>Gewusst wie: Anfügen eines vorhandenen Datenträgers

1.  Melden Sie sich am [Azure-Verwaltungsportal][6] an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie anschließend den virtuellen Computer aus, an den Sie den Datenträger anfügen möchten.

3.  Klicken Sie auf der Befehlsleiste auf **Anfügen**, und wählen Sie anschließend **Datenträger anfügen**.
    
    ![Datenträger
    anfügen](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)
    
    Das Dialogfeld **Datenträger anfügen** wird angezeigt.
    
    ![Details zum Datenträger
    eingeben](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

4.  Wählen Sie den Datenträger aus, den Sie an den virtuelle Computer anfügen möchten.
5.  Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.
    
    Der Datenträger wird nun im Dashboard des virtuellen Computers aufgelistet.
    
    ![Datenträger erfolgreich
    angefügt](./media/howto-attach-disk-window-linux/AttachSuccess.png)

## <a id="attachempty" ></a>Gewusst wie: Anfügen eines leeren Datenträgers

Nachdem Sie eine als leerer Datenträger verwendbare VHD-Datei erstellt und hochgeladen haben, können Sie sie an einen virtuellen Computer anfügen. Verwenden Sie das Cmdlet [Add-AzureVhd][7], um die VHD-Datei in das Speicherkonto hochzuladen.

1.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie anschließend den virtuellen Computer aus, an den Sie den Datenträger anfügen möchten.

2.  Klicken Sie auf der Befehlsleiste auf **Anfügen**, und wählen Sie anschließend **Leeren Datenträger anfügen**.
    
    ![Anfügen eines leeren
    Datenträgers](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)
    
    Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.
    
    ![Neuen leeren Datenträger
    anfügen](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

3.  Übernehmen Sie unter **Dateiname** entweder den automatisch erstellten Namen, oder geben Sie einen Namen ein, unter dem die VHD-Datei gespeichert werden soll. Für den Datenträger, der aus der VHD-Datei erstellt wird, wird immer der automatisch erzeugte Name verwendet.

4.  Geben Sie unter **Größe** die Größe des Datenträgers ein.

5.  Aktivieren Sie das Kontrollkästchen, um den leeren Datenträger anzufügen.
    
    Der Datenträger wird nun im Dashboard des virtuellen Computers aufgelistet.
    
    ![Leerer Datenträger erfolgreich
    angefügt](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [WACOM.NOTE] Wenn Sie einen Datenträger angefügt haben, müssen Sie
> sich auf dem virtuellen Computer anmelden und den Datenträger
> initialisieren, damit der virtuelle Computer ihn zur Speicherung
> verwenden kann.

## <a id="initializeinWS" ></a>Gewusst wie: Initialisieren eines neues Datenträgers unter Windows Server

1.  Stellen sie eine Verbindung mit dem virtuellen Computer her. Eine Beschreibung der dafür erforderlichen Schritte finden Sie unter [Anmeldung auf einem virtuellen Windows Server-Computer](../virtual-machines-log-on-windows-server/).

2.  Öffnen Sie nach der Anmeldung im linken Bereich **Server-Manager**, erweitern Sie **Speicherung**, und klicken Sie anschließend auf **Datenträgerverwaltung**.
    
    ![Server-Manager
    öffnen](./media/howto-attach-disk-window-linux/ServerManager.png)

3.  Klicken Sie mit der rechten Maustaste auf **Datenträger 2** und anschließend auf **Datenträger initialisieren**.
    
    ![Datenträger
    initialisieren](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4.  Klicken Sie auf **OK**, um den Initialisierungsvorgang zu starten.

5.  Klicken Sie mit der rechten Maustaste auf den Speicherplatzzuordnungsbereich für Datenträger 2, klicken Sie auf **Neues einfaches Volume**, und beenden Sie anschließend den Assistenten mit den Standardwerten.
    
    ![Volume
    initialisieren](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)
    
    Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.
    
    ![Volume erfolgreich
    initialisiert](./media/howto-attach-disk-window-linux/InitializeSuccess.png)

## <a id="initializeinlinux" ></a>Gewusst wie: Initialisieren eines neues Datenträgers unter Linux

1.  Stellen Sie eine Verbindung mit dem virtuellen Computer her. Eine Beschreibung der dafür erforderlichen Schritte finden Sie unter [Anmeldung auf einem virtuellen Linux-Computer](../virtual-machines-linux-how-to-log-on/).

2.  Geben Sie im SSH-Fenster den folgenden Befehl ein, und geben Sie anschließend das Kennwort für das Konto ein, dass Sie zur Verwaltung des virtuellen Computers erstellt haben:
    
    `sudo grep SCSI /var/log/messages`
    
    In den angezeigten Meldungen können Sie den Bezeichner des letzten hinzugefügten Datenträgers finden.
    
    ![Datenträgermeldungen
    abrufen](./media/howto-attach-disk-window-linux/DiskMessages.png)

3.  Geben Sie im SSH-Fenster den folgen Befehl ein, um ein neues Gerät zu erstellen, und geben Sie anschließend das Kennwort für das Konto ein:
    
    `sudo fdisk /dev/sdc`


   
    > [WACOM.NOTE] In diesem Beispiel müssen Sie eventuell auf manchen
    > Distributionen `sudo -i` verwenden, wenn /sbin oder /usr/sbin sich
    > nicht In Ihrem `$PATH` befinden.

4.  Geben Sie **n** ein, um eine neue Partition zu erstellen.
    
    ![Neues Gerät
    erstellen](./media/howto-attach-disk-window-linux/DiskPartition.png)

5.  Geben Sie **p** ein, um die Partition zur primären zu machen, geben Sie **1** ein, um sie zur ersten Partition zu machen, und drücken Sie die Eingabetaste, um den Standardwert für den Zylinder zu übernehmen.
    
    ![Partition
    erstellen](./media/howto-attach-disk-window-linux/DiskCylinder.png)

6.  Geben Sie **p** ein, um die Details zum Datenträger anzuzeigen, der gerade partitioniert wird.
    
    ![Informationen zum Datenträger
    auflisten](./media/howto-attach-disk-window-linux/DiskInfo.png)

7.  Geben Sie **w** ein, um die Einstellungen für den Datenträger zu schreiben.
    
    ![Datenträgeränderungen
    schreiben](./media/howto-attach-disk-window-linux/DiskWrite.png)

8.  Sie müssen das Dateisystem auf der neuen Partition erstellen. Geben Sie z. B. den folgenden Befehl ein, um das Dateisystem zu erstellen, und geben Sie anschließend das Kontokennwort ein:
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![Dateisystem
    erstellen](./media/howto-attach-disk-window-linux/DiskFileSystem.png)


   
    > [WACOM.NOTE] Bitte beachten Sie, dass auf SUSE Linux Enterprise
    > 11-Systemen nur schreibgeschützter Zugriff für ext4-Dateisysteme
    > möglich ist. Für diese Systeme wird empfohlen, das neue
    > Dateisystem als ext3 statt als ext4 zu formatieren.

9.  Als Nächstes muss ein Verzeichnis zum Einbinden des neuen Dateisystems zur Verfügung stehen. Geben Sie z. B. den folgenden Befehl ein, um ein Verzeichnis zum Anfügen des Laufwerks zu erstellen, und geben Sie anschließend das Kontokennwort ein:
    
    `sudo mkdir /datadrive`

10. Geben Sie den folgenden Befehl ein, um das Laufwerk einzubinden:
    
    `sudo mount /dev/sdc1 /datadrive`
    
    Der Datenträger kann nun als **/datadrive** verwendet werden.

11. Fügen Sie das neue Laufwerk zu /etc/fstab hinzu:
    
    Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es zur Datei /etc/fstab hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique IDentifier) in /etc/fstab zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (d. h. auf /dev/sdc1) zu verweisen. Sie können das Hilfsprogramm **blkid** verwenden, um den UUID des neuen Laufwerks herauszufinden:
    
        `sudo -i blkid`
    
    Die Ausgabe sieht etwa wie folgt aus:
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`


   
    > [WACOM.NOTE] blkid erfordert nicht in allen Fällen einen
    > sudo-Zugang, ist aber auf manchen Distributions leichter mit `sudo
    > -i` auszuführen, wenn /sbin oder /usr/sbin sich nicht in Ihrem
    > `$PATH` befinden.
    
    **Vorsicht:** Eine fehlerhafte Bearbeitung der Datei /etc/fstab kann dazu führen, dass das System sich nicht mehr starten lässt. Wenn Sie sich nicht sicher sind, finden Sie in der Dokumentation der
    Distribution Informationen zur korrekten Bearbeitung dieser Datei. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.
    
    Geben Sie in einem Texteditor die Informationen zum neuen Dateisystem am Ende der Datei /etc/fstab ein. In diesem Beispiel verwenden wir den UUID-Wert für das neue Gerät **/dev/sdc1**, das in den vorhergehenden Schritten erstellt wurde, und den Bereitstellungspunkt **/datadrive**:
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    In auf SUSE Linux basierenden Systemen müssen Sie eventuell ein etwas unterschiedliches Format verwenden:
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    Wenn zusätzliche Datenlaufwerke oder Partitionen erstellt werden, müssen Sie diese ebenfalls einzeln in /etc/fstab einfügen.
    
    Sie können nun testen, ob das Dateisystem korrekt eingebunden wurde, indem Sie die Einbindung aufheben und wiederherstellen. Verwenden Sie dafür den zuvor erstellten Beispielbereitstellungspunkt `/datadrive`:
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    Wenn der zweite Befehl zu einem Fehler führt, prüfen Sie die Datei
    /etc/fstab auf korrekte Syntax.


   
    > [WACOM.NOTE] Wenn Sie später einen Datenträger entfernen, ohne
    > fstab zu bearbeiteten, kann der Start des virtuellen Computers
    > fehlschlagen. Für den Fall, dass dieses Problem häufiger auftritt,
    > bieten die meisten Distributionen die fstab-Optionen `nofail`
    > und/oder `nobootwait`, die einen Systemstart auch dann erlauben,
    > wenn der Datenträger nicht vorhanden ist. Weitere Informationen zu
    > diesen Parametern finden Sie in der Dokumentation zu Ihrer
    > Distribution.



[1]: http://go.microsoft.com/fwlink/p/?LinkId=391661
[2]: http://www.windowsazure.com/de-de/downloads/
[3]: http://go.microsoft.com/FWLink/p/?LinkID=294683
[4]: http://www.windowsazure.com/de-de/manage/linux/how-to-guides/linux-agent-guide/
[5]: http://msdn.microsoft.com/de-de/library/windowsazure/jj672979.aspx
[6]: http://manage.windowsazure.com
[7]: http://go.microsoft.com/FWLink/p/?LinkID=391684