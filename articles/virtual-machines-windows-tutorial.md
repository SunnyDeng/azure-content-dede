<properties linkid="manage-windows-tutorial-virtual-machine-gallery" urlDisplayName="Create a virtual machine" pageTitle="Create a virtual machine running Windows Server in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Erstellen eines virtuellen Windows Server-Computers

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial/" title="Azure-Portal" class="current">Azure-Portal</a><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure-Vorschauportal">Azure-Vorschauportal</a></div>

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe der Image-Galerie im Azure-Verwaltungsportal ganz einfach einen virtuellen Azure-Computer mit Windows Server erstellen. Die Image-Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungs-Images.

> [WACOM.NOTE] Sie benötigen keine Erfahrung mit Azure-VMs, um dieses Lernprogramm zu verwenden. Sie benötigen jedoch ein Azure-Konto. Sie können ein kostenloses Testkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][].

In diesem Lernprogramm wird Folgendes erläutert:

-   [Erstellen eines virtuellen Computers][]
-   [Anmelden bei einem virtuellen Computer nach dessen Erstellung][]
-   [Anschließen eines Datenträgers an den neuen virtuellen Computer][]

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer][] auf.

## <span id="createvirtualmachine"></span> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im Verwaltungsportal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [WACOM.NOTE] Sie können auch das funktionsreichere, anpassbare [Azure-Vorschauportal][1] verwenden, um einen virtuellen Computer zu erstellen, die Bereitstellung von Anwendungsvorlagen für mehrere Computer zu automatisieren, erweiterte VM-Überwachungs- und Diagnosefeatures zu verwenden usw. Die verfügbaren VM-Konfigurationsoptionen in den beiden Portalen sind ähnlich, aber nicht identisch.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][]]

## <span id="logon"></span> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

In diesem Abschnitt lernen Sie, wie Sie sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

[WACOM.INCLUDE [virtual-machines-log-on-win-server][]]

## <span id="attachdisk"></span> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer

In diesem Abschnitt erfahren Sie, wie Sie einen leeren Datenträge an den virtuellen Computer anfügen. Weitere Informationen zum Anfügen eines leeren Datenträgers und zum Anfügen eines vorhandenen Datenträgers finden Sie im [Lernprogramm zum Anfügen eines Datenträgers][].

1.  Melden Sie sich beim Azure [Verwaltungsportal][] an.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM** aus.

    ![MyTestVM auswählen][]

3.  Sie werden möglicherweise zuerst zur Seite "Schnellstart" geleitet. Klicken Sie in diesem Fall oben auf **Dashboard**.

    ![Dashboard auswählen][]

4.  Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf die Popup-Option **Leeren Datenträger anschließen**.

    ![Anschließen in der Befehlsleiste auswählen][]

5.  Der **Name des virtuellen Computers**, der **Speicherort**, der **Dateiname** und die **Host-Cache-Voreinstellungen** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein. Klicken Sie auf das Häkchen, um den leeren Datenträger an den virtuellen Computer anzufügen.

    ![Größe des leeren Datenträgers festlegen][]

    > [WACOM.NOTE] Alle Datenträger werden anhand einer VHD-Datei im Windows Azure-Speicher erstellt. Sie können unter **Dateiname** einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

6.  Kehren Sie zum Dashboard zurück, um zu überprüfen, ob der leere Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde. Er wird als zweiter Datenträger in der Liste **Datenträger** zusammen mit dem Betriebssystem-Datenträger aufgelistet.

    ![Schließen Sie einen leeren Datenträger an.][]

    Nach dem Anschließen des Datenträgers an den virtuellen Computer ist der Datenträger offline und nicht initialisiert. Bevor Sie diesen zum Speichern von Daten verwenden können, müssen Sie sich bei dem virtuellen Computer anmelden und den Datenträger initialisieren.

7.  Stellen Sie wie im vorherigen Abschnitt [Anmelden bei einem virtuellen Computer nach dessen Erstellung][] (\#logon) beschrieben eine Verbindung zu dem virtuellen Computer her.

8.  Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

    ![Datei- und Speicherdienste im Server-Manager erweitern][]

9.  Wählen Sie im erweiterten Menü **Datenträger** aus.

    ![Datei- und Speicherdienste im Server-Manager erweitern][2]

10. Im Bereich **Datenträger** sind drei Datenträger in der Liste vorhanden: Disk 0, Disk 1 und Disk 2. Disk 0 ist der Betriebssystem-Datenträger. Bei Disk 1 handelt es sich um einen temporären Ressourcendatenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Disk 2 ist der Datenträger, den Sie an den virtuellen Computer angefügt haben. Beachten Sie, dass der Datenträger eine Kapazität von 5 GB hat, wie zuvor festgelegt. Klicken Sie mit der rechten Maustaste auf Disk 2, und wählen Sie **Initialisieren** aus.

    ![Starten Sie die Initialisierung.][]

11. Klicken Sie auf **Ja**, um den Initialisierungsvorgang zu starten.

    ![Initialisierung fortsetzen][]

12. Klicken Sie erneut mit der rechten Maustaste auf Disk 2, und wählen Sie **Neues Volume** aus.

    ![Erstellen Sie das Volume.][]

13. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet.

    ![Erstellen Sie das Volume.][3]

    Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.

## Nächste Schritte

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer in einem Clouddienst][]

[Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem][]

[Anfügen eines Datenträgers an einen virtuellen Computer][Lernprogramm zum Anfügen eines Datenträgers]

[Verwalten der Verfügbarkeit virtueller Computer][]

  [Azure-Portal]: /de-de/documentation/articles/virtual-machines-windows-tutorial/ "Azure-Portal"
  [Azure-Vorschauportal]: /de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure-Vorschauportal"
  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [Erstellen eines virtuellen Computers]: #createvirtualmachine
  [Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
  [Anschließen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk
  [Virtuelle Computer]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [virtual-machines-log-on-win-server]: ../includes/virtual-machines-log-on-win-server.md
  [Lernprogramm zum Anfügen eines Datenträgers]: http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/
  [Verwaltungsportal]: http://manage.windowsazure.com
  [MyTestVM auswählen]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Dashboard auswählen]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Anschließen in der Befehlsleiste auswählen]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Größe des leeren Datenträgers festlegen]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [Schließen Sie einen leeren Datenträger an.]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Datei- und Speicherdienste im Server-Manager erweitern]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [2]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [Starten Sie die Initialisierung.]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Initialisierung fortsetzen]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [Erstellen Sie das Volume.]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [3]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [Verbinden virtueller Computer in einem Clouddienst]: http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/
  [Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem]: http://www.windowsazure.com/de-de/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Verwalten der Verfügbarkeit virtueller Computer]: http://www.windowsazure.com/de-de/documentation/articles/manage-availability-virtual-machines/
