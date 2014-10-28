<properties linkid="virtual-machines-windows-tutorial-azure-preview" urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Create a virtual machine running Windows Server in the Azure Preview Portal" metaKeywords="Azure image gallery vm" description="Learn how to create an Azure virtual machine (VM) running Windows Server, using the VM Gallery in the Azure Preview Portal" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="danlep,kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="danlep,kathydav"></tags>

# Erstellen eines virtuellen Computers mit Windows Server im Azure-Vorschauportal

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial/" title="Azure-Portal">Azure-Portal</a><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure-Vorschauportal" class="current">Azure-Vorschauportal</a></div>

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe der VM-Galerie im Azure-Vorschauportal ganz einfach einen virtuellen Azure-Computer mit Windows Server erstellen. Die Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungs-Images.

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][Create a Windows Azure account (in englischer Sprache)].

In diesem Lernprogramm wird Folgendes erläutert:

-   [Erstellen eines virtuellen Computers][Erstellen eines virtuellen Computers]
-   [Anmelden bei einem virtuellen Computer nach dessen Erstellung][Anmelden bei einem virtuellen Computer nach dessen Erstellung]

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer][Virtuelle Computer] auf.

## <span id="createvirtualmachine"></span> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie das Vorschauportal zur Erstellung eines VMs mit Windows Server verwenden. Sie können die Standardeinstellungen von Azure für den größten Teil der Konfiguration verwenden und den virtuellen Computer in nur wenigen Minuten erstellen.

1.  Melden Sie sich beim [Azure-Vorschauportal][1] an. Testen Sie das [kostenlose Testkonto][kostenlose Testkonto], wenn Sie noch kein Abonnement haben.

2.  Klicken Sie im Hub-Menü auf **Neu**.

    ![Wählen Sie Neu in der Befehlsleiste aus][Wählen Sie Neu in der Befehlsleiste aus]

3.  Klicken Sie unter **Neu** auf **Alles** und klicken Sie anschließend unter **Galerie** auf **Virtuelle Computer**. Klicken Sie auf **Windows Server 2012 R2 Datencenter**. Klicken Sie dort auf **Erstellen**.

    ![Wählen Sie ein VM-Image aus der Galerie][Wählen Sie ein VM-Image aus der Galerie]

4.  Geben Sie im Fenster **VM Erstellen** den **Hostnamen** ein, den Sie für den VM verwenden möchten, sowie den **Benutzernamen** des Administrators und ein starkes **Passwort**.

    ![Konfiguration des Hostnamens und der Anmeldeberechtigungsnachweise][Konfiguration des Hostnamens und der Anmeldeberechtigungsnachweise]

    > [WACOM.NOTE] Der **Benutzername** bezieht sich auf das Administratorkonto, dass Sie zur Verwaltung des Servers verwenden. Geben Sie für dieses Konto ein individuelles Passwort an und stellen Sie sicher, dass Sie es nicht vergessen. **Sie brauchen den Benutzernamen und das Passwort, um sich am virtuellen Computer anzumelden**.

5.  Klicken Sie auf **Erstellen**, um die Standardeinstellungen für die übrigen VM-Optionen zu verwenden, und um die Erstellung des VMs zu beginnen. Wenn Sie möchten, können Sie sich die Einstellungen für die **Optionale Konfiguration** ansehen, bevor Sie auf **Erstellen** klicken. Sie können beispielsweise eine optionale Fehlerdiagnose konfigurieren, um später viele Metriken auf Ihrem VM zu verfolgen. Klicken Sie auf **Optionale Konfiguration**, klicken Sie anschließend auf **Fehlerdiagnose** und schalten Sie den **Status** auf **Ein**.

    ![Schalten Sie VM-Fehlerdiagnose ein][Schalten Sie VM-Fehlerdiagnose ein]

    > [WACOM.NOTE] Wenn Sie die Azure-Fehlerdiagnose einschalten, speichert Azure die Diagnosedaten in einem Azure-Speicherkonto, wodurch zusätzliche Speicherkosten entstehen.

6.  Während Azure den VM erstellt, können Sie den Fortschritt in **Benachrichtigungen** im Hub-Menü verfolgen. Nachdem Azure den VM erstellt hat, wird er Ihnen auf Ihrem Anmeldebildschirm angezeigt.

    ![VM erscheint im Startboard][VM erscheint im Startboard]

## <span id="logon"></span> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

In diesem Abschnitt lernen Sie, wie Sie sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

> [WACOM.NOTE] Tipps zu Anforderungen und Fehlerbehebung finden Sie unter [Verbindung zu einem virtuellen Azure-Computer mit RDP oder SSH][Verbindung zu einem virtuellen Azure-Computer mit RDP oder SSH].

1.  Melden Sie sich beim [Azure-Vorschauportal][1] an, falls noch nicht geschehen.

2.  Klicken Sie auf Ihren VM im Startboard Wenn Sie den VM suchen müssen, klicken Sie auf **Durchsuchen** und anschließend auf **Virtuelle Computer**.

    ![Durchsuchen, um den VM zu finden][Durchsuchen, um den VM zu finden]

3.  Klicken Sie im VM-Fenster oben auf **Verbinden**.

    ![Melden Sie sich beim virtuellen Computer an.][Melden Sie sich beim virtuellen Computer an.]

4.  Klicken Sie auf **Öffnen**, um die Remotedesktopprotokoll-Datei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

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

  [Azure-Portal]: /de-de/documentation/articles/virtual-machines-windows-tutorial/ "Azure-Portal"
  [Azure-Vorschauportal]: /de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Azure-Vorschauportal"
  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/
  [Erstellen eines virtuellen Computers]: #createvirtualmachine
  [Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon
  [Virtuelle Computer]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [1]: https://portal.azure.com
  [kostenlose Testkonto]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Wählen Sie Neu in der Befehlsleiste aus]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Wählen Sie ein VM-Image aus der Galerie]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Konfiguration des Hostnamens und der Anmeldeberechtigungsnachweise]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Schalten Sie VM-Fehlerdiagnose ein]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [VM erscheint im Startboard]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [Verbindung zu einem virtuellen Azure-Computer mit RDP oder SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Durchsuchen, um den VM zu finden]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [Melden Sie sich beim virtuellen Computer an.]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [Stellen Sie eine Verbindung her.]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Bestätigen Sie die Identität des Computers.]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [Verbinden virtueller Computer in einem Clouddienst]: http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/
  [Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem]: http://www.windowsazure.com/de-de/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Anfügen eines Datenträgers an einen virtuellen Computer]: http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/
  [Verwalten der Verfügbarkeit virtueller Computer]: http://www.windowsazure.com/de-de/documentation/articles/manage-availability-virtual-machines/
