<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Gewusst wie: Erstellen eines virtuellen Windows Server-Computers" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Erstellen eines virtuellen Windows Server-Computers

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe der Imagegalerie im Windows Azure-Verwaltungsportal ganz einfach einen virtuellen Azure-Computer mit Windows Server erstellen. Die Image-Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungs-Images. 

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)](http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/). 

In diesem Lernprogramm wird Folgendes erläutert:

- [Erstellen eines virtuellen Computers](#createvirtualmachine)
- [Anmelden bei einem virtuellen Computer nach dessen Erstellung](#logon)
- [Anschließen eines Datenträgers an den neuen virtuellen Computer](#attachdisk)

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer](http://go.microsoft.com/fwlink/p/?LinkID=271224) auf.


##<a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im Verwaltungsportal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

In diesem Abschnitt lernen Sie, wie Sie sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

1. Melden Sie sich beim Azure-[Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM** aus.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. Klicken Sie in der Befehlsleiste auf **Verbinden**.

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Klicken Sie auf **Öffnen**, um die Remotedesktopprotokoll-Datei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Klicken Sie auf **Verbinden**.

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. Geben Sie in das Kennwortfeld den Benutzernamen und das Kennwort ein, das Sie beim Erstellen des virtuellen Computers angegeben haben, und klicken Sie dann auf **OK**.

7. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Sie können jetzt mit dem virtuellen Computer wie mit einem Server in Ihrem Büro arbeiten.

## <a id="attachdisk"> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer

In diesem Abschnitt erfahren Sie, wie Sie einen leeren Datenträge an den virtuellen Computer anfügen. Weitere Informationen zum Anfügen eines leeren Datenträgers und zum Anfügen eines vorhandenen Datenträgers finden Sie im [Lernprogramm zum Anfügen eines Datenträgers] (http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/).

1. Melden Sie sich beim Azure-[Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM** aus.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Sie werden möglicherweise zuerst zur Seite "Schnellstart" geleitet. Klicken Sie in diesem Fall oben auf **Dashboard**.

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf die Popup-Option **Leeren Datenträger anschließen**.

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. Der **Name des virtuellen Computers**, der **Speicherort**, der **Dateiname** und die **Host-Cache-Voreinstellungen** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein. Klicken Sie auf das Häkchen, um den leeren Datenträger an den virtuellen Computer anzufügen.

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] Alle Datenträger werden aus einer VHD-Datei im Windows Azure-Speicher erstellt. Sie können unter **Dateiname** einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

6. Kehren Sie zum Dashboard zurück, um zu überprüfen, ob der leere Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde. Er wird als zweiter Datenträger in der Liste **Datenträger** zusammen mit dem Betriebssystem-Datenträger aufgelistet.

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Nach dem Anschließen des Datenträgers an den virtuellen Computer ist der Datenträger offline und nicht initialisiert. Bevor Sie diesen zum Speichern von Daten verwenden können, müssen Sie sich bei dem virtuellen Computer anmelden und den Datenträger initialisieren.

7. Stellen Sie wie im vorherigen Abschnitt [Anmelden bei einem virtuellen Computer nach dessen Erstellung] (#logon)beschrieben eine Verbindung zu dem virtuellen Computer her.

8. Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Wählen Sie im erweiterten Menü **Datenträger** aus.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. Im Bereich **Datenträger** sind drei Datenträger in der Liste vorhanden: Disk 0, Disk 1 und Disk 2. Disk 0 ist der Betriebssystem-Datenträger. Bei Disk 1 handelt es sich um einen temporären Ressourcendatenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Disk 2 ist der Datenträger, den Sie an den virtuellen Computer angefügt haben. Beachten Sie, dass der Datenträger eine Kapazität von 5 GB hat, wie zuvor festgelegt. Klicken Sie mit der rechten Maustaste auf Disk 2, und wählen Sie **Initialisieren** aus.

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Klicken Sie auf **Ja**, um den Initialisierungsvorgang zu starten.

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Klicken Sie erneut mit der rechten Maustaste auf Disk 2, und wählen Sie **Neues Volume** aus. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben. 
	
##Nächste Schritte 

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer in einem Clouddienst](http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/)

[Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem](http://www.windowsazure.com/de-de/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Anfügen eines Datenträgers an einen virtuellen Computer](http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/)

[Verwalten der Verfügbarkeit virtueller Computer](http://www.windowsazure.com/de-de/documentation/articles/manage-availability-virtual-machines/)

[Informationen zu virtuellen Computern in Azure]: #virtualmachine
[Erstellen eines virtuellen Computers]: #custommachine
[Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
[Anschließen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk
[Gewusst wie: Einrichten der Kommunikation mit einem virtuellen Computer]: #endpoints


