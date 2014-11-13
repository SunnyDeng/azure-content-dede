<properties urlDisplayName="Create a virtual machine" pageTitle="Erstellen eines virtuellen Computers unter Windows in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Erfahren Sie, wie Sie einen virtuellen Computer unter Windows in Azure erstellen, sich anschlie&szlig;end anmelden und einen Datentr&auml;ger anf&uuml;gen k&ouml;nnen." metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav, rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="kathydav" />

# Erstellen eines virtuellen Computers unter Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial/" title="Azure-Portal" class="current">Azure-Portal</a><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure-Vorschauportal">Azure-Vorschauportal</a></div>

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer unter Windows mithilfe eines Windows Server-Imagebeispiels aus der Image-Galerie im Azure-Verwaltungsportal zu erstellen. Die Image-Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungs-Images.

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][Create a Windows Azure account (in englischer Sprache)].

In diesem Lernprogramm wird Folgendes erläutert:

-   [Erstellen eines virtuellen Computers][Erstellen eines virtuellen Computers]
-   [Anmelden bei einem virtuellen Computer nach dessen Erstellung][Anmelden bei einem virtuellen Computer nach dessen Erstellung]
-   [Anschließen eines Datenträgers an den neuen virtuellen Computer][Anschließen eines Datenträgers an den neuen virtuellen Computer]

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer][Virtuelle Computer] auf.

## <span id="createvirtualmachine"></span> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im Verwaltungsportal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [WACOM.NOTE] Wie viele und welche Imagearten in der Galerie verfügbar sind, hängt von Ihrem Abonnementtyp ab. In diesem Lernprogramm wird ein Windows Server-Image verwendet. Ein MSDN-Abonnement weist jedoch möglicherweise zusätzliche für Sie verfügbare Images auf, einschließlich Desktop-Images.

> Sie können auch das funktionsreichere, anpassbare [Azure-Vorschauportal][1] verwenden, um einen virtuellen Computer zu erstellen, die Bereitstellung von Anwendungsvorlagen für mehrere Computer zu automatisieren, erweiterte VM-Überwachungs- und Diagnosefeatures zu verwenden usw. Die verfügbaren VM-Konfigurationsoptionen in den beiden Portalen sind ähnlich, aber nicht identisch.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <span id="logon"></span> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

In diesem Abschnitt lernen Sie, wie Sie sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <span id="attachdisk"></span> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer

In diesem Abschnitt erfahren Sie, wie Sie einen leeren Datenträge an den virtuellen Computer anfügen. Weitere Informationen zum Anfügen eines leeren Datenträgers und zum Anfügen eines vorhandenen Datenträgers finden Sie im [Lernprogramm zum Anfügen eines Datenträgers][Lernprogramm zum Anfügen eines Datenträgers].

1.  Melden Sie sich beim Azure [Verwaltungsportal][Verwaltungsportal] an.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM** aus.

    ![MyTestVM auswählen][MyTestVM auswählen]

3.  Sie werden möglicherweise zuerst zur Seite "Schnellstart" geleitet. Klicken Sie in diesem Fall oben auf **Dashboard**.

    ![Dashboard auswählen][Dashboard auswählen]

4.  Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf die Popup-Option **Leeren Datenträger anschließen**.

    ![Anschließen in der Befehlsleiste auswählen][Anschließen in der Befehlsleiste auswählen]

5.  Der **Name des virtuellen Computers**, der **Speicherort**, der **Dateiname** und die **Host-Cache-Voreinstellungen** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein. Klicken Sie auf das Häkchen, um den leeren Datenträger an den virtuellen Computer anzufügen.

    > [WACOM.NOTE] Beachten Sie, dass die Datenträgerimages in Azure als Seitenblobs im Azure-Speicher gespeichert werden. Außerhalb von Azure können virtuelle Festplatten entweder das VHD- oder ein VHDX-Format verwenden. Sie können auch fest, dynamisch erweiterbar oder differenzierend sein. Azure unterstützt das VHD-Format; feste Datenträger. Das feste Format legt den logischen Datenträger in der Datei linear aus, daher wird Datenträger-Offset X bei Blob-Offset X gespeichert. Eine kleinere Fußzeile am Ende des Blobs beschreibt die Eigenschaften der VHD. Oftmals verschwendet das feste Format Speicherplatz, da die meisten Datenträger über große ungenutzte Bereiche davon verfügen. Azure speichert VHD-Dateien jedoch in einem platzsparenden Format. Daher profitieren Sie gleichzeitig von den Vorteilen fester und dynamischer Datenträger. Weitere Informationen finden Sie im Thema [Über VHDs in Azure][Über VHDs in Azure]

    ![Größe des leeren Datenträgers festlegen][Größe des leeren Datenträgers festlegen]

    > [WACOM.NOTE] Alle Datenträger werden anhand einer VHD-Datei im Windows Azure-Speicher erstellt. Sie können unter **Dateiname** einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

6.  Kehren Sie zum Dashboard zurück, um zu überprüfen, ob der leere Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde. Er wird als zweiter Datenträger in der Liste **Datenträger** zusammen mit dem Betriebssystem-Datenträger aufgelistet.

    ![Schließen Sie einen leeren Datenträger an.][Schließen Sie einen leeren Datenträger an.]

    Nach dem Anschließen des Datenträgers an den virtuellen Computer ist der Datenträger offline und nicht initialisiert. Bevor Sie diesen zum Speichern von Daten verwenden können, müssen Sie sich bei dem virtuellen Computer anmelden und den Datenträger initialisieren.

7.  Stellen Sie wie im vorherigen Abschnitt [Anmelden bei einem virtuellen Computer nach dessen Erstellung][Anmelden bei einem virtuellen Computer nach dessen Erstellung] (\#logon) beschrieben eine Verbindung zu dem virtuellen Computer her.

8.  Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

    ![Datei- und Speicherdienste im Server-Manager erweitern][Datei- und Speicherdienste im Server-Manager erweitern]

9.  Wählen Sie im erweiterten Menü **Datenträger** aus.

    ![Datei- und Speicherdienste im Server-Manager erweitern][2]

10. Im Bereich **Datenträger** sind drei Datenträger in der Liste vorhanden: Disk 0, Disk 1 und Disk 2. Disk 0 ist der Betriebssystem-Datenträger. Bei Disk 1 handelt es sich um einen temporären Ressourcendatenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Disk 2 ist der Datenträger, den Sie an den virtuellen Computer angefügt haben. Beachten Sie, dass der Datenträger eine Kapazität von 5 GB hat, wie zuvor festgelegt. Klicken Sie mit der rechten Maustaste auf Disk 2, und wählen Sie **Initialisieren** aus.

    ![Starten Sie die Initialisierung.][Starten Sie die Initialisierung.]

11. Klicken Sie auf **Ja**, um den Initialisierungsvorgang zu starten.

    ![Initialisierung fortsetzen][Initialisierung fortsetzen]

12. Klicken Sie erneut mit der rechten Maustaste auf Disk 2, und wählen Sie **Neues Volume** aus.

    ![Erstellen Sie das Volume.][Erstellen Sie das Volume.]

13. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet.

    ![Erstellen Sie das Volume.][3]

    Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.

## Nächste Schritte

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer in einem Clouddienst][Verbinden virtueller Computer in einem Clouddienst]

[Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem][Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem]

[Verwalten der Verfügbarkeit virtueller Computer][Verwalten der Verfügbarkeit virtueller Computer]

[Starten, Beenden und Löschen virtueller Computer][Starten, Beenden und Löschen virtueller Computer]

[VIDEO: Getting Started with VHDs - What's Really Happening][VIDEO: Getting Started with VHDs - What's Really Happening] (Erste Schritte mit VHDs – was wirklich geschieht) (in englischer Sprache)

[VIDEO: FAQ with Mark Russinovich - Does Windows Azure run Windows?][VIDEO: FAQ with Mark Russinovich - Does Windows Azure run Windows?] (FAQ mit Mark Russinovich – führt Microsoft Azure Windows aus?) (in englischer Sprache)

[VIDEO: Adding a new virtual machine to a Web Farm by making reusable images][VIDEO: Adding a new virtual machine to a Web Farm by making reusable images] (Hinzufügen eines neuen virtuellen Computers zu einer Webfarm durch das Erstellen wiederverwendbarer Images) (in englischer Sprache)

[VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines][VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines] (Hinzufügen von VHDs, Speicherkonten und skalierbaren virtuellen Computern) (in englischer Sprache)

[VIDEO: Scott Guthrie starts with Virtual Machines][VIDEO: Scott Guthrie starts with Virtual Machines] (Scott Guthrie beginnt mit der Verwendung von virtuellen Computern) (in englischer Sprache)

[VIDEO: Storage and Disk Basics with Azure Virtual Machines][VIDEO: Storage and Disk Basics with Azure Virtual Machines] (Speicher- und Datenträgergrundlagen bei virtuellen Azure-Computern) (in englischer Sprache)

  [Azure-Vorschauportal]: /de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure-Vorschauportal"
  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/
  [Erstellen eines virtuellen Computers]: #createvirtualmachine
  [Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
  [Anschließen eines Datenträgers an den neuen virtuellen Computer]: #attachdisk
  [Virtuelle Computer]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [Lernprogramm zum Anfügen eines Datenträgers]: http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/
  [Verwaltungsportal]: http://manage.windowsazure.com
  [MyTestVM auswählen]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Dashboard auswählen]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Anschließen in der Befehlsleiste auswählen]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Über VHDs in Azure]: http://msdn.microsoft.com/de-de/library/azure/dn790344.aspx
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
  [Starten, Beenden und Löschen virtueller Computer]: http://msdn.microsoft.com/library/azure/dn763935.aspx
  [VIDEO: Getting Started with VHDs - What's Really Happening]: http://azure.microsoft.com/de-de/documentation/videos/getting-started-with-azure-virtual-machines
  [VIDEO: FAQ with Mark Russinovich - Does Windows Azure run Windows?]: http://azure.microsoft.com/de-de/documentation/videos/mark-russinovich-windows-on-azure
  [VIDEO: Adding a new virtual machine to a Web Farm by making reusable images]: http://azure.microsoft.com/de-de/documentation/videos/adding-virtual-machines-web-farm
  [VIDEO: Adding Virtual Hard Drives, Storage Accounts, and Scaling Virtual Machines]: http://azure.microsoft.com/de-de/documentation/videos/adding-drives-scaling-virtual-machines
  [VIDEO: Scott Guthrie starts with Virtual Machines]: http://azure.microsoft.com/de-de/documentation/videos/virtual-machines-scottgu
  [VIDEO: Storage and Disk Basics with Azure Virtual Machines]: http://azure.microsoft.com/de-de/documentation/videos/storage-and-disks-virtual-machines
