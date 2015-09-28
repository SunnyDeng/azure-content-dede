<properties
	pageTitle="Erfassen eines Images des virtuellen Computers unter Windows Server"
	description="Erfahren Sie, wie Sie ein Image eines virtuellen Azure-Computers unter Windows Server erfassen können."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="cynthn"/>

#Erfassen eines virtuellen Windows-Computers, um ihn als Image zu verwenden#

In diesem Artikel wird erläutert, wie Sie einen virtuellen Azure-Computer erfassen, auf dem Windows läuft, um ihn als Image zum Erstellen anderer virtueller Computer zu verwenden. Dieses Image enthält den Betriebssystem-Datenträger und alle Datenträger, die an den virtuellen Computer angefügt sind. Da die Vorlage keine Netzwerkkonfiguration enthält, muss diese später erfolgen, wenn Sie die anderen auf dieser Vorlage basierenden virtuellen Computer erstellen.

Azure speichert das Image unter**Eigene Images**. Hier werden auch sämtliche Images abgelegt, die Sie hochladen. Näheres zu Images finden Sie unter [Informationen zu Images für virtuelle Computer](virtual-machines-images.md).

##Voraussetzungen##

Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:

- [Erstellen eines benutzerdefinierten virtuellen Computers mit Windows][]
- [Anfügen eines Datenträgers an einen virtuellen Computer][]

> [AZURE.WARNING]Bei diesem Vorgang wird der ursprüngliche virtuelle Computer nach dem Erfassen gelöscht, daher ist er nicht als Option zum Sichern eines virtuellen Computers bestimmt. Eine Möglichkeit dafür ist Azure Backup, das in bestimmten Regionen als Vorschau verfügbar. Weitere Informationen finden Sie unter [Sichern eines virtuellen Azure-Computers](../backup/backup-azure-vms.md). Andere Lösungen sind von zertifizierten Partnern erhältlich. Aktuelle Angebote finden Sie auf dem Azure Marketplace.

##Erfassen des virtuellen Computers##

1. Stellen Sie im [Azure-Portal](http://manage.windowsazure.com) eine **Verbindung** mit dem virtuellen Computer her. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][].

2.	Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.

3.	Ändern Sie das Verzeichnis in `%windir%\system32\sysprep`, und führen Sie dann „sysprep.exe“ aus.

4. 	Das Dialogfeld **Systemvorbereitungstool** wird angezeigt. Gehen Sie wie folgt vor:

	- Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction][].

	- Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

	- Klicken Sie auf **OK**.

	![Sysprep ausführen](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep fährt den virtuellen Computer herunter. Dadurch wird der Status des virtuellen Computers im Azure-Portal in **Angehalten** geändert.

8.	Klicken Sie im Azure-Portal auf **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus, den Sie erfassen möchten.

9.	Klicken Sie in der Befehlsleiste auf **Aufnehmen**.

	![Virtuellen Computer erfassen](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Das Dialogfeld **Virtuellen Computer erfassen** wird angezeigt.

10.	Geben Sie unter **Imagename** den Namen für das neue Image ein.

11.	Bevor Sie ein Windows Server-Image zu Ihren benutzerdefinierten Images hinzufügen, muss es durch Ausführen von Sysprep, wie in den vorherigen Schritten beschrieben, verallgemeinert werden. Klicken Sie auf **Ich habe Sysprep auf dem virtuellen Computer ausgeführt**, um anzugeben, dass Sie diesen Schritt ausgeführt haben.

12.	Aktivieren Sie das Kontrollkästchen, um das Image zu erfassen. Das neue Image steht nun unter **Images** zur Verfügung.

 	![Image-Erfassung erfolgreich](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Nächste Schritte##
Das Image kann jetzt zum Erstellen virtueller Computer verwendet werden. Dazu erstellen Sie mithilfe des Menüeintrags **Aus Katalog** einen virtuellen Computer und wählen das gerade erstellte Image aus. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers mit Windows][].

[Erstellen eines benutzerdefinierten virtuellen Computers mit Windows]: virtual-machines-windows-create-custom.md
[Anfügen eines Datenträgers an einen virtuellen Computer]: storage-windows-attach-disk.md
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: virtual-machines-log-on-windows-server.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=Sept15_HO3-->