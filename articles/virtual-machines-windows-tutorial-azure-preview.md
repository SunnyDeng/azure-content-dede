<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Erstellen eines virtuellen Computers mit Windows im Azure-Vorschauportal" metaKeywords="Azure Image-Galerie virtueller Computer" description="Erfahren Sie, wie Sie mithilfe der VM-Galerie im Azure-Vorschauportal einen virtuellen Azure-Computer unter Windows erstellen." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create a Virtual Machine Running Windows in the Azure Preview Portal" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="danlep,kathydav,rasquill" />

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

# Erstellen eines virtuellen Computers mit Windows im Azure-Vorschauportal#

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Azure-Portal</a><a href="/de-de/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Azure-Vorschauportal</a></div>

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer unter Windows mithilfe eines Windows Server-Imagebeispiels aus der Image-Galerie im Azure-Vorschauportal zu erstellen. Die Galerie bietet verschiedene Images, unter anderem von Windows-Betriebssystemen, Linux-basierten Betriebssystemen sowie Anwendungsimages. 

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um dieses Lernprogramm auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Weitere Informationen finden Sie unter [Erstellen eines Azure-Kontos](http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/). 

In diesem Lernprogramm wird Folgendes erläutert:

- [Erstellen des virtuellen Computers](#createvirtualmachine)
- [Anmelden bei einem virtuellen Computer nach dessen Erstellung](#logon)

Wenn Sie ausführlichere Informationen wünschen, rufen Sie [Virtuelle Computer](http://go.microsoft.com/fwlink/p/?LinkID=271224) auf.


##<a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers

Dieser Abschnitt zeigt Ihnen die Verwendung des Vorschauportals zum Erstellen eines virtuellen Computers, wobei Windows Server 2012 R2 Datacenter als ein Beispiel dient. Sie können die Azure-Standardeinstellungen für den Hauptteil der Konfiguration verwenden und den virtuellen Computer in nur ein paar Minuten erstellen.

> [WACOM.NOTE] Die verfügbaren Images sind von Ihrem Abonnement abhängig. In diesem Lernprogramm wird ein Windows Server-Image verwendet. Ein MSDN-Abonnement weist jedoch möglicherweise zusätzliche Images auf, einschließlich Desktop-Images. 
 

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an. Testen Sie die [kostenlose Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/), wenn Sie noch nicht über ein Abonnement verfügen.

2. Klicken Sie im Menü "Hub" auf **Neu**.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. Klicken Sie auf dem Blatt **Neu** auf **Alles**, klicken Sie auf **Virtuelle Computer**, klicken Sie auf **Windows Server 2012 R2 Datacenter**, und klicken Sie dann auf **Erstellen**.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. Geben Sie im Blatt **VM erstellen** den **Hostnamen**, den Sie für den virtuellen Computer wünschen, sowie den administrativen **Benutzernamen** und ein sicheres **Kennwort** ein.  

	>[WACOM.NOTE] **Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Erstellen Sie ein eindeutiges Kennwort für dieses Konto, und merken Sie sich das Kennwort. **Sie benötigen Benutzername und Kennwort, um sich am virtuellen Computer anzumelden**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. Überprüfen Sie die Standardeinstellungen, wie z. B. die **Preisstufe**, **Optionale Konfiguration** und **Speicherort**. Diese Optionen beeinflussen die Größe des virtuellen Computers und auch Netzwerkoptionen, z. B. die Domänenmitgliedschaft. Um beispielsweise Premium-Speicher auf einem virtuellen Computer zu testen, müssen Sie eine unterstützte Region und Größe auswählen. 

	>[WACOM.NOTE] Premium-Speicher befindet sich in der Vorschau und ist für virtuelle Computer der DS-Reihe in bestimmten Regionen verfügbar. Weitere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](http://azure.microsoft.com/de-de/documentation/articles/storage-premium-storage-preview-portal/).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. Wenn Sie die Einstellungen überprüft oder aktualisiert haben, klicken Sie auf **Erstellen**.	

7. Während Azure den virtuellen Computer erstellt, können Sie den Status unter **Benachrichtigungen** im Menü "Hub" nachverfolgen. Nachdem Azure den virtuellen Computer erstellt hat, sehen Sie ihn in Ihrem Startmenü.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

In diesem Abschnitt erfahren Sie, wie Sie sich beim von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

>[WACOM.NOTE] Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Melden Sie sich am [Azure-Vorschauportal](https://portal.azure.com) an, falls noch nicht geschehen.

2. Klicken Sie im Startmenü auf Ihren virtuellen Computer. Wenn Sie ihn suchen müssen, klicken Sie auf **Durchsuchen** und dann auf **Virtuelle Computer**. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. Klicken Sie oben auf dem VM-Blatt auf **Verbinden**.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

4. Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.
	
5. Klicken Sie auf **Verbinden**, um die Verbindung herzustellen.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6. Geben Sie den Benutzernamen und das Kennwort des Administratorkontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.
	
7. Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

##Nächste Schritte 

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

[Verbinden virtueller Computer in einem Cloud-Dienst](http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/)

[Erstellen und Hochladen einer eigenen virtuellen Festplatte mit Windows Server-Betriebssystem](http://www.windowsazure.com/de-de/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Anfügen eines Datenträgers an einen virtuellen Computer](http://www.windowsazure.com/de-de/documentation/articles/storage-windows-attach-disk/)

[Verwalten der Verfügbarkeit virtueller Computer](http://www.windowsazure.com/de-de/documentation/articles/manage-availability-virtual-machines/)

[Informationen zu Konfigurationseinstellungen von virtuellen Azure-Computern](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[Erstellen des virtuellen Computers]: #custommachine
[Anmelden bei einem virtuellen Computer nach dessen Erstellung]: #logon

<!--HONumber=35.2-->
