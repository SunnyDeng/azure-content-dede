
Weitere Informationen zu Datenträgern finden Sie unter [Informationen zu virtuellen Computerdatenträgern in Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="attachempty"></a>Anfügen eines leeren Datenträgers
Eine einfachere Methode zum Hinzufügen eines Datenträgers besteht im Anfügen eines leeren Datenträgers, weil Azure die VHD-Datei automatisch erstellt und sie im Speicherkonto ablegt.

1. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

2. Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf **Leeren Datenträger anschließen**.


	![Anfügen eines leeren Datenträgers](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.


	![Neuen leeren Datenträger anfügen](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

 
	Gehen Sie wie folgt vor:

	- Übernehmen Sie in **Dateiname** den Standardnamen, oder geben Sie einen anderen Namen für die VHD-Datei ein, die für den Datenträger verwendet wird. Der Datenträger verwendet einen automatisch generierten Namen, selbst wenn Sie einen anderen Namen für die VHD-Datei eingeben.

	- Geben Sie die **Größe (GB)** des Datenträgers ein.

	- Klicken Sie auf das Häkchen, um fortzufahren.

4.	Nachdem der Datenträger erstellt und angefügt ist, wird er im Dashboard des virtuellen Computers aufgeführt.

	![Leerer Datenträger erfolgreich angefügt](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

##<a id="attachexisting"></a>Anfügen eines vorhandenen Datenträgers

Zum Anfügen eines vorhandenen Datenträgers wird eine VHD-Datei im Speicherkonto benötigt. Verwenden Sie das Cmdlet [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684), um die VHD-Datei in das Speicherkonto hochzuladen. Nachdem Sie eine VHD-Datei erstellt und hochgeladen haben, können Sie sie an einen virtuellen Computer anfügen.

1. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

2. Klicken Sie auf der Befehlsleiste auf **Anfügen**, und wählen Sie anschließend **Datenträger anfügen**.


	![Datenträger anfügen](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

	Das Dialogfeld **Datenträger anfügen** wird angezeigt.



	![Details zum Datenträger eingeben](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. Wählen Sie den Datenträger aus, den Sie an den virtuellen Computer anfügen möchten.

4. Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.
 
5.	Nachdem der Datenträger angefügt ist, wird er im Dashboard des virtuellen Computers aufgeführt.


	![Datenträger erfolgreich angefügt](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

> [AZURE.NOTE]Wenn Sie einen Datenträger angefügt haben, müssen Sie sich auf dem virtuellen Computer anmelden und den Datenträger initialisieren, damit der virtuelle Computer ihn zur Speicherung verwenden kann.

<!---HONumber=July15_HO2-->