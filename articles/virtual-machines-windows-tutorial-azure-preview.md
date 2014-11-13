<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Erstellen Sie einen virtuellen Computer unter Windows im Azure-Vorschauportal" metaKeywords="Azure image gallery vm" description="Erfahren Sie, wie Sie mithilfe der VM-Galerie im Azure-Vorschauportal einen virtuellen Azure-Computer unter Windows erstellen." metaCanonical="" services="virtual-machines" documentationCenter="" title="Erstellen Sie einen virtuellen Computer unter Windows im Azure-Vorschauportal" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep,kathydav,rasquill" />

# Erstellen Sie einen virtuellen Computer unter Windows im Azure-Vorschauportal

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial/" title="Azure-Portal">Azure-Portal</a><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure-Vorschauportal" class="current">Azure-Vorschauportal</a></div>

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer unter Windows mithilfe eines Windows Server-Imagebeispiels aus der Image-Galerie im Azure-Vorschauportal zu erstellen. Die Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungsimages.

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][Create a Windows Azure account (in englischer Sprache)].

In diesem Lernprogramm wird Folgendes erläutert:

-   [Erstellen eines virtuellen Computers][Erstellen eines virtuellen Computers]
-   [Anmelden bei einem virtuellen Computer nach dessen Erstellung][Anmelden bei einem virtuellen Computer nach dessen Erstellung]

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer][Virtuelle Computer] auf.

## <span id="createvirtualmachine"></span> </a>Erstellen eines virtuellen Computers

Dieser Abschnitt zeigt Ihnen die Verwendung des Vorschauportals zum Erstellen eines virtuellen Computers, wobei Windows Server als ein Beispiel dient. Sie können die Azure-Standardeinstellungen für den Hauptteil der Konfiguration verwenden und den virtuellen Computer in nur ein paar Minuten erstellen.

> [WACOM.NOTE] Wie viele und welche Imagearten in der Galerie verfügbar sind, hängt von Ihrem Abonnementtyp ab. In diesem Lernprogramm wird ein Windows Server-Image verwendet. Ein MSDN-Abonnement weist jedoch möglicherweise zusätzliche für Sie verfügbare Images auf, einschließlich Desktop-Images.

1.  Melden Sie sich beim [Azure-Vorschauportal][1] an. Testen Sie die [kostenlose Testversion][kostenlose Testversion], wenn Sie noch nicht über ein Abonnement verfügen.

2.  Klicken Sie im Menü „Hub“ auf **Neu**.

    ![Wählen Sie „Neu“ auf der Befehlsleiste aus.][Wählen Sie „Neu“ auf der Befehlsleiste aus.]

3.  Klicken Sie unter **Neu** auf **Alles**, und klicken Sie dann unter **Galerie**, auf **Virtuelle Computer**. Klicken Sie auf **Windows Server 2012 R2 Datacenter**. Klicken Sie dort auf **Erstellen**.

    ![Wählen Sie ein VM-Image aus der Galerie aus.][Wählen Sie ein VM-Image aus der Galerie aus.]

4.  Geben Sie im Blatt **VM erstellen** den **Hostnamen**, den Sie für den virtuellen Computer wünschen, sowie den administrativen **Benutzernamen** und ein sicheres **Kennwort** ein.

    ![Konfigurieren Sie den Hostnamen und die Anmeldeinformationen.][Konfigurieren Sie den Hostnamen und die Anmeldeinformationen.]

    > [WACOM.NOTE] **Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Erstellen Sie ein eindeutiges Kennwort für dieses Konto, und merken Sie sich das Kennwort. **Sie benötigen Benutzername und Kennwort, um sich am virtuellen Computer anzumelden**.

5.  Klicken Sie zum Verwenden der Standardeinstellungen für die verbleibenden VM-Optionen und zum Starten der VM-Erstellung auf **Erstellen**. Vor dem Klicken auf **Erstellen** können Sie jedoch auch die Einstellungen für die optionale Konfiguration durchsuchen. Beispielsweise können Sie optionale Diagnosen auf dem virtuellen Computer konfigurieren, um später jede Menge Metriken auf Ihrem virtuellen Computer nachverfolgen zu können. Klicken Sie auf die Option für die optionale Konfiguration, auf **Diagnose**, und wechseln Sie den **Status** zu **Ein**.

    ![Aktivieren Sie die VM-Diagnose.][Aktivieren Sie die VM-Diagnose.]

    > [WACOM.NOTE] Wenn Sie die Azure-Diagnose aktivieren, speichert Azure die Diagnosedaten in einem Azure-Speicherkonto, wodurch zusätzliche Speicherkosten anfallen.

6.  Während Azure den virtuellen Computer erstellt, können Sie den Status unter **Benachrichtigungen** im Menü „Hub“ nachverfolgen. Nachdem Azure den virtuellen Computer erstellt hat, sehen Sie ihn in Ihrem Startmenü.

    ![Der virtuelle Computer wird im Startmenü angezeigt.][Der virtuelle Computer wird im Startmenü angezeigt.]

## <span id="logon"></span> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

In diesem Abschnitt erfahren Sie, wie Sie sich beim von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

> [WACOM.NOTE] Anforderungen und Tipps zur Problembehandlung finden Sie unter [Verbindung mit einem virtuellen Azure-Computer mit RDP oder SSH][Verbindung mit einem virtuellen Azure-Computer mit RDP oder SSH].

1.  Melden Sie sich am [Azure-Vorschauportal][1] an, falls noch nicht geschehen.

2.  Klicken Sie im Startmenü auf Ihren virtuellen Computer. Wenn Sie ihn finden müssen, klicken Sie auf **Durchsuchen** und dann auf **Virtuelle Computer**.

    ![Suchen Sie den virtuellen Computer.][Suchen Sie den virtuellen Computer.]

3.  Klicken Sie oben auf dem VM-Blatt auf **Verbinden**.

    ![Melden Sie sich beim virtuellen Computer an.][Melden Sie sich beim virtuellen Computer an.]

4.  Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

5.  Klicken Sie auf **Verbinden**, um die Verbindung herzustellen.

    ![Stellen Sie eine Verbindung her.][Stellen Sie eine Verbindung her.]

6.  Geben Sie den Benutzernamen und das Kennwort des Administratorkontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.

7.  Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

    ![Bestätigen Sie die Identität des Computers.][Bestätigen Sie die Identität des Computers.]

    Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

## Nächste Schritte

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer in einem Clouddienst][Verbinden virtueller Computer in einem Clouddienst]

[Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem][Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem]

[Anfügen eines Datenträgers an einen virtuellen Computer][Anfügen eines Datenträgers an einen virtuellen Computer]

[Verwalten der Verfügbarkeit virtueller Computer][Verwalten der Verfügbarkeit virtueller Computer]

[Starten, Beenden und Löschen virtueller Computer][Starten, Beenden und Löschen virtueller Computer]

  [Azure-Vorschauportal]: /de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure-Vorschauportal"
  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/
  [Erstellen eines virtuellen Computers]: #createvirtualmachine
  [Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
  [Virtuelle Computer]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [kostenlose Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Wählen Sie „Neu“ auf der Befehlsleiste aus.]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Wählen Sie ein VM-Image aus der Galerie aus.]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Konfigurieren Sie den Hostnamen und die Anmeldeinformationen.]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Aktivieren Sie die VM-Diagnose.]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [Der virtuelle Computer wird im Startmenü angezeigt.]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [Verbindung mit einem virtuellen Azure-Computer mit RDP oder SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Suchen Sie den virtuellen Computer.]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [Melden Sie sich beim virtuellen Computer an.]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [Stellen Sie eine Verbindung her.]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Bestätigen Sie die Identität des Computers.]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [Verbinden virtueller Computer in einem Clouddienst]: http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/
  [Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem]: http://www.windowsazure.com/de-de/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Anfügen eines Datenträgers an einen virtuellen Computer]: http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/
  [Verwalten der Verfügbarkeit virtueller Computer]: http://www.windowsazure.com/de-de/documentation/articles/manage-availability-virtual-machines/
  [Starten, Beenden und Löschen virtueller Computer]: http://msdn.microsoft.com/library/azure/dn763935.aspx
