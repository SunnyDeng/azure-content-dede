<properties urlDisplayName="Capture an image" pageTitle="Erfassen eines Images des virtuellen Computers unter Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Erfassen eines virtuellen Windows-Computers, um ihn als Vorlage zu verwenden#

Dieser Artikel erläutert, wie Sie einen virtuellen Azure-Computer erfassen, auf dem Windows läuft, um ihn wie eine Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage umfasst den Betriebssystemdatenträger und alle an den virtuellen Computer angefügten Datenträger. Da die Vorlage keine Netzwerkkonfiguration enthält, müssen Sie die Konfiguration später vornehmen, wenn Sie die anderen virtuellen Computer erstellen, die auf dieser Vorlage basieren.

Azure behandelt diese Vorlage als ein lokales Image und speichert es unter**My Images**. Hier werden sämtliche Images abgelegt, die Sie hochladen. Weitere Informationen zu Images finden Sie unter [Über Images virtueller Computer in Azure] [].

##Voraussetzungen##

Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:

- [Erstellen eines benutzerdefinierten virtuellen Computers] []
- [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer] []

##Erfassen des virtuellen Computers##

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie auf der Befehlsleiste auf **Verbinden** klicken. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird] [].

2.	Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.


3.	Wechseln Sie zum Verzeichnis `%windir%\system32\sysprep`, und führen Sie dann "sysprep.exe" aus.


4. 	Das Dialogfeld **Systemvorbereitungstool** wird angezeigt. Gehen Sie wie folgt vor:


	- Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [Verwenden von Sysprep: Einführung][].

	- Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

	- Klicken Sie auf **OK**.

	![Run Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep fährt den virtuellen Computer herunter. Dadurch wird der Status des virtuellen Computers im [Verwaltungsportal](http://manage.windowsazure.com) in **Angehalten** geändert.


8.	Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

9.	Klicken Sie in der Befehlsleiste auf **Aufnehmen**.

	![Capture virtual machine](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Das Dialogfeld **Virtuellen Computer erfassen** wird angezeigt.

10.	Geben Sie unter **Imagename** den Namen für das neue Image ein.

11.	Bevor Sie ein Windows Server-Image zu Ihren benutzerdefinierten Images hinzufügen, muss es durch Ausführen von Sysprep, wie in den vorherigen Schritten beschrieben, verallgemeinert werden. Klicken Sie auf **Ich habe Sysprep auf dem virtuellen Computer ausgeführt**, um anzugeben, dass Sie dies durchgeführt haben.

12.	Aktivieren Sie das Kontrollkästchen, um das Image zu erfassen. Wenn Sie ein Image eines generalisierten virtuellen Computers erfassen, wird der virtuelle Computer gelöscht.

	Das neue Image steht nun unter **Images** zur Verfügung.

	![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Nächste Schritte
Das Image kann jetzt als Vorlage zum Erstellen virtueller Computer verwendet werden. Dazu erstellen Sie mithilfe der Methode **Aus Katalog** einen benutzerdefinierten virtuellen Computer und wählen das gerade erstellte Image aus. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers] [].

	
[Informationen zu virtuellen Computern in Azure]: http://msdn.microsoft.com/de-de/library/azure/dn790290.aspx
[Erstellen eines benutzerdefinierten virtuellen Computers]: ../virtual-machines-create-custom/
[Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Computer]: ../storage-windows-attach-disk/
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]:http://www.windowsazure.com/de-de/manage/windows/how-to-guides/log-on-a-windows-vm/
[How to Use Sysprep: An Introduction]:http://technet.microsoft.com/de-de/library/bb457073.aspx
[Führen Sie Sysprep.exe aus]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Geben Sie Optionen für Sysprep.exe ein]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[Der virtuelle Computer wird angehalten]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Ein Image des virtuellen Computers erfassen]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Geben Sie den Containernamen ein]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image-Erfassung erfolgreich]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Verwenden des erfassten Image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!--HONumber=35_1-->
