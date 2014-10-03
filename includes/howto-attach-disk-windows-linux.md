Weitere Informationen zu Datenträgern in Azure-VMs finden Sie unter [About Virtual Machine Disks in Azure][].

## <span id="attachempty"></span></a>Gewusst wie: Anfügen eines leeren Datenträgers

Eine einfachere Methode zum Hinzufügen eines Datenträger besteht im Anfügen eines leeren Datenträgers, weil Azure die VHD-Datei automatisch erstellt und sie im Speicherkonto ablegt.

1.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

2.  Klicken Sie auf der Befehlsleiste auf **Anfügen**, und wählen Sie anschließend **Leeren Datenträger anfügen**.

    ![Anfügen eines leeren Datenträgers][]

    Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.

    ![Neuen leeren Datenträger anfügen][]

3.  Übernehmen Sie entweder den automatisch generierten Namen in **Dateiname**, oder geben Sie einen aussagekräftigen Namen ein. Für den Datenträger, der aus der VHD-Datei erstellt wird, wird immer der automatisch erzeugte Name verwendet.

4.  Geben Sie unter **Größe** die Größe des Datenträgers ein.

5.  Aktivieren Sie das Kontrollkästchen, um den leeren Datenträger anzufügen.

    Der Datenträger wird nun im Dashboard des virtuellen Computers aufgelistet.

    ![Leerer Datenträger erfolgreich angefügt][]

## <span id="attachexisting"></span></a>Gewusst wie: Anfügen eines vorhandenen Datenträgers

Zum Anfügen eines vorhandenen Datenträgers wird eine VHD-Datei im Speicherkonto benötigt. Verwenden Sie das Cmdlet [Add-AzureVhd][], um die VHD-Datei in das Speicherkonto hochzuladen. Nachdem Sie eine VHD-Datei erstellt und hochgeladen haben, können Sie sie an einen virtuellen Computer anfügen.

1.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

2.  Klicken Sie auf der Befehlsleiste auf **Anfügen**, und wählen Sie anschließend **Datenträger anfügen**.

    ![Datenträger anfügen][]

    Das Dialogfeld **Datenträger anfügen** wird angezeigt.

    ![Details zum Datenträger eingeben][]

3.  Wählen Sie den Datenträger aus, den Sie an den virtuelle Computer anfügen möchten.
4.  Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

    Der Datenträger wird nun im Dashboard des virtuellen Computers aufgelistet.

    ![Datenträger erfolgreich angefügt][]

> [WACOM.NOTE]
> Wenn Sie einen Datenträger angefügt haben, müssen Sie sich auf dem virtuellen Computer anmelden und den Datenträger initialisieren, damit der virtuelle Computer ihn zur Speicherung verwenden kann.

  [About Virtual Machine Disks in Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [Anfügen eines leeren Datenträgers]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [Neuen leeren Datenträger anfügen]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [Leerer Datenträger erfolgreich angefügt]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [Datenträger anfügen]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [Details zum Datenträger eingeben]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [Datenträger erfolgreich angefügt]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
