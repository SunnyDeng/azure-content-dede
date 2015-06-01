<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Gewusst wie: Erstellen eines virtuellen Windows Server-Computers" description="Beschreibt, wie ein virtueller Windows-Computer erstellt wird, ein Datenträger hinzugefügt wird und die Fernanmeldung erfolgt." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Erstellen eines virtuellen Windows Server-Computers #

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe der Imagegalerie im Windows Azure-Verwaltungsportal ganz einfach einen virtuellen Azure-Computer mit Windows Server erstellen. Die Image-Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungs-Images.

> [AZURE.NOTE]Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/).

In diesem Lernprogramm wird Folgendes erläutert:

- [Erstellen eines virtuellen Computers](#createvirtualmachine)
- [Anmelden bei einem virtuellen Computer nach dessen Erstellung](#logon)
- [Anschließen eines Datenträgers an den neuen virtuellen Computer](#attachdisk)

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer](http://go.microsoft.com/fwlink/p/?LinkID=271224) auf.


## <a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers##

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im Verwaltungsportal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung ##

In diesem Abschnitt lernen Sie, wie Sie sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

1. Melden Sie sich beim Azure [Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM** aus.

	![MyTestVM auswählen](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. Klicken Sie in der Befehlsleiste auf **Verbinden**.

	![Mit MyTestVM verbinden](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Klicken Sie auf **Öffnen**, um die Remotedesktopprotokoll-Datei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

	![RDP-Datei öffnen](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Klicken Sie auf **Verbinden**.

	![Stellen Sie eine Verbindung her.](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. Geben Sie in das Kennwortfeld den Benutzernamen und das Kennwort ein, das Sie beim Erstellen des virtuellen Computers angegeben haben, und klicken Sie dann auf **OK**.

7. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

	![Bestätigen Sie die Identität des Computers.](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Sie können jetzt mit dem virtuellen Computer wie mit einem Server in Ihrem Büro arbeiten.

## <a id="attachdisk"> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer ##

In diesem Abschnitt erfahren Sie, wie Sie einen leeren Datenträge an den virtuellen Computer anfügen. Weitere Informationen zum Anfügen eines leeren Datenträgers und zum Anfügen eines vorhandenen Datenträgers finden Sie im [Lernprogramm zum Anfügen eines Datenträgers](../articles/storage-windows-attach-disk.md).

1. Melden Sie sich beim Azure [Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM** aus.

	![MyTestVM auswählen](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Sie werden möglicherweise zuerst zur Seite "Schnellstart" geleitet. Klicken Sie in diesem Fall oben auf **Dashboard**.

	![Dashboard auswählen](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf die Popup-Option **Leeren Datenträger anschließen**.

	![Anschließen in der Befehlsleiste auswählen](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)

5. Der **Name des virtuellen Computers**, der **Speicherort**, der **Dateiname** und die **Host-Cache-Voreinstellungen** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein. Klicken Sie auf das Häkchen, um den leeren Datenträger an den virtuellen Computer anzufügen.

	![Größe des leeren Datenträgers festlegen](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)
	
	>[AZURE.NOTE]Alle Datenträger werden aus einer VHD-Datei im Windows Azure-Speicher erstellt. Sie können unter **Dateiname** einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

6. Kehren Sie zum Dashboard zurück, um zu überprüfen, ob der leere Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde. Er wird als zweiter Datenträger in der Liste **Datenträger** zusammen mit dem Betriebssystem-Datenträger aufgelistet.

	![Schließen Sie einen leeren Datenträger an.](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Nach dem Anschließen des Datenträgers an den virtuellen Computer ist der Datenträger offline und nicht initialisiert. Bevor Sie diesen zum Speichern von Daten verwenden können, müssen Sie sich bei dem virtuellen Computer anmelden und den Datenträger initialisieren.

7. Stellen Sie wie im vorherigen Abschnitt [Anmelden bei einem virtuellen Computer nach dessen Erstellung](#logon) beschrieben eine Verbindung zu dem virtuellen Computer her.

8. Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

	![Datei- und Speicherdienste im Server-Manager erweitern](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Wählen Sie im erweiterten Menü **Datenträger** aus.

	![Datei- und Speicherdienste im Server-Manager erweitern](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)
	
10. Im Abschnitt **Datenträger** sind drei Datenträger in der Liste vorhanden: Datenträger 0, Datenträger 1 und Datenträger 2. Datenträger 0 ist der Betriebssystem-Datenträger. Bei Datenträger 1 handelt es sich um einen temporären Ressourcendatenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Datenträger 2 ist der Datenträger, den Sie an den virtuellen Computer angefügt haben. Beachten Sie, dass der Datenträger eine Kapazität von 5 GB hat, wie zuvor festgelegt. Klicken Sie mit der rechten Maustaste auf Disk 2, und wählen Sie **Initialisieren** aus.

	![Starten Sie die Initialisierung.](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Klicken Sie auf **Ja**, um den Initialisierungsvorgang zu starten.

	![Initialisierung fortsetzen](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Klicken Sie erneut mit der rechten Maustaste auf Disk 2, und wählen Sie **Neues Volume** aus.

	![Erstellen Sie das Volume.](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet.

	![Erstellen Sie das Volume.](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.
	
## Nächste Schritte 

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer in einem Clouddienst](../articles/cloud-services-connect-virtual-machine.md)

[Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem](../articles/virtual-machines-create-upload-vhd-windows-server.md)

[Anfügen eines Datenträgers an einen virtuellen Computer](../articles/storage-windows-attach-disk.md)

[Verwalten der Verfügbarkeit virtueller Computer](../articles/manage-availability-virtual-machines.md)

[About virtual machines in Azure]: #virtualmachine
[How to create the virtual machine]: #custommachine
[How to log on to the virtual machine after you create it]: #logon
[How to attach a data disk to the new virtual machine]: #attachdisk
[How to set up communication with the virtual machine]: #endpoints



<!--HONumber=52-->
