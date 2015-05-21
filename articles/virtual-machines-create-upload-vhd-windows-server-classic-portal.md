\<properties pageTitle="Erstellen und Hochladen einer Windows Server-VHD nach Azure" description="Erfahren Sie, wie Sie eine virtuelle Festplatte \(VHD\) mit Windows Server-Betriebssystem in Azure erstellen und hochladen." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/ tags="AZURE CLASSIC PORTAL"/\>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="kathydav"/>


#Erstellen und Hochladen einer Windows Server-VHD im klassischen Azure-Portal#

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte \(Virtual Hard Disk, VHD\) mit einem Betriebssystem hochladen, um sie als Image für die Erstellung von virtuellen Computern zu nutzen. Weitere Informationen zu Datenträgern und Images in Microsoft Azure finden Sie unter [Informationen zu Datenträgern und Images in Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

> [AZURE.NOTE]Bei der Erstellung einer virtuellen Maschine basierend auf einem Image können Sie die Einstellungen des Betriebssystems für die Anwendungen entsprechend anpassen, die Sie auf dem virtuellen Computer ausführen möchten. Diese Konfiguration wird für die virtuelle Maschine gespeichert und wirkt sich nicht auf das Image aus. Anweisungen hierzu finden Sie unter [virtual-machines-windows-create-custom.md](virtual-machines-windows-create-custom.md).

##Voraussetzungen##
In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Voraussetzungen verfügen:

1. **Ein Azure-Abonnement** – wenn Sie nicht bereits eines besitzen, können Sie [ein Azure-Konto kostenlos erstellen](/pricing/free-trial/?WT.mc_id=A261C142F): Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind. Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.   

2. **Microsoft Azure PowerShell** – Sie haben das Microsoft Azure PowerShell-Modul installiert und für die Verwendung Ihres Abonnements konfiguriert. Informationen zum Herunterladen dieses Moduls finden Sie unter [Microsoft Azure-Downloads](http://azure.microsoft.com/downloads/). Ein Lernprogramm zum Installieren und Konfigurieren des Moduls steht [hier](install-configure-powershell.md) zur Verfügung. Verwenden Sie das Cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx), um die virtuelle Festplatte hochzuladen.

3. **Ein in einer .vhd-Datei gespeichertes unterstütztes Windows-Betriebssystem** - Sie haben ein unterstütztes Windows Server-Betriebssystem auf einer virtuellen Festplatte installiert. Zum Erstellen von .vhd-Dateien stehen mehrere verschiedene Tools bereit. Sie können eine Virtualisierungslösung wie etwa Hyper-V verwenden, um den virtuellen Computer zu erstellen und das Betriebssystem zu installieren. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE]Das VHDX-Format wird in Microsoft Azure nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem [Convert-VHD-Cmdlet](http://technet.microsoft.com/library/hh848454.aspx) in das VHD-Format konvertieren. Ein Lernprogramm zu diesem Thema finden Sie [hier](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Die folgenden Versionen von Windows Server werden unterstützt: <P> <TABLE BORDER="1" WIDTH="600"> <TR BGCOLOR="#E9E7E7"> <TH>OS</TH> <TH>SKU</TH> <TH>Service Pack</TH> <TH>Architektur</TH> </TR> <TR> <TD>Windows Server 2012 R2</TD> <TD>alle Editionen</TD> <TD>–</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2012</TD> <TD>alle Editionen</TD> <TD>–</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2008 R2</TD> <TD>alle Editionen</TD> <TD>SP1</TD> <TD>x64</TD> </TR> </TABLE> </P>


Diese Aufgabe umfasst die folgenden Schritte:

- [Schritt 1: Vorbereiten des hochzuladenden Images][]
- [Schritt 2: Erstellen eines Speicherkontos in Azure][]
- [Schritt 3: Vorbereiten der Verbindung zu Azure][]
- [Schritt 4: Hochladen der VHD-Datei][]

## Schritt 1: Vorbereiten des hochzuladenden Images ##

Bevor das Bild zu Azure hochgeladen werden kann, muss es mit dem Tool Sysprep generalisiert werden. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx) \(in englischer Sprache\).

Führen Sie auf dem virtuellen Computer, auf dem das Betriebssystem installiert wurde, die folgende Prozedur aus:

1. Melden Sie sich beim Betriebssystem an.

2. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator. Wechseln Sie in das Verzeichnis **%windir%\\system32\\sysprep**, und führen Sie dann `sysprep.exe` aus.

	![Öffnen eines Eingabeaufforderungsfensters](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/sysprep_commandprompt.png)

3.	Das Dialogfeld **Systemvorbereitungstool** wird angezeigt.

	![Starten von Sysprep](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/sysprepgeneral.png)

4.  Wählen Sie unter **Systemvorbereitungstool** die Option **Out-of-Box-Experience \(OOBE\) für System aktivieren**, und stellen Sie sicher, dass Verallgemeinern aktiviert ist.

5.  Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

6.  Klicken Sie auf **OK**.


## Schritt 2: Erstellen eines Speicherkontos in Azure ##

Sie benötigen in Azure ein Speicherkonto, um eine VHD-Datei hochladen zu können, die zum Erstellen eines virtuellen Computers verwendet werden kann. Über das klassische Azure-Portal können Sie ein Speicherkonto erstellen.

1. Melden Sie sich am klassischen Azure-Portal an.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Datendienste** \> **Speicher** \> **Schnellerfassung**.

	![Speicherkonto schnell erfassen](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storage-quick-create.png)

4. Füllen Sie die Felder wie folgt aus:
	
	- Geben Sie unter **URL** einen Unterdomänennamen ein, der im URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname im URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.
			
	- Wählen Sie den **Standort oder die Affinitätsgruppe** für das Speicherkonto aus. Mit einer Affinitätsgruppe können Sie Ihre Cloud-Dienste und Speicher im selben Rechenzentrum platzieren.
		 
	- Sie können wählen, ob Sie **Georeplikation** für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei an einem sekundären Speicherort repliziert. So wird die Speicherung auf jenen Speicherort umgeschaltet, wenn am primären Speicherort ein größerer Ausfall auftritt. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Wenn Sie aufgrund gesetzlicher Vorschriften oder Unternehmensrichtlinien mehr Kontrolle über den Speicherort des cloudbasierten Speichers benötigen, können Sie die Georeplikation deaktivieren. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten in dem sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten. Ausführliche Informationen zur Verwaltung der Georeplikation für Speicherkonten finden Sie unter: [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/#replication-options).

	![Speicherkontodetails eingeben](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storage-create-account.png)


5. Klicken Sie auf **Speicherkonto erstellen**. Das Konto wird nun unter **Speicher** angezeigt.

	![Speicherkonto erfolgreich erstellt](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Storagenewaccount.png)

6. Als Nächstes erstellen Sie einen Container für die hochgeladenen VHDs. Klicken Sie auf den Namen des Speicherkontos und dann auf **Container**.

	![Speicherkontodetails](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_detail.png)

7. Klicken Sie auf **Container erstellen**.

	![Speicherkontodetails](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_container.png)

8. Geben Sie einen **Namen** für den Container ein, und wählen Sie die **Zugriffsrichtlinie** aus.

	![Containername](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/storageaccount_containervalues.png)

	> [AZURE.NOTE]Der Container ist standardmäßig privat, und der Containerzugriff ist auf den Kontobesitzer eingeschränkt. Um öffentliche Lesezugriffe auf die im Container enthaltenen BLOBs, jedoch nicht auf die Containereigenschaften und -metadaten, zuzulassen, verwenden Sie die Option "Öffentlicher BLOB". Verwenden Sie die Option "Öffentlicher Container", um vollständigen öffentlichen Lesezugriff auf den Container und die BLOBs zuzulassen.

## Schritt 3: Vorbereiten der Verbindung mit Microsoft Azure ##

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen. Zu diesem Zweck können Sie die Microsoft Azure Active Directory-Methode oder die Zertifikatmethode verwenden.

### Verwenden der Microsoft Azure AD-Methode

1. Öffnen Sie die Microsoft Azure PowerShell-Konsole, wie unter [Installieren von Microsoft Azure PowerShell beschrieben](#Install).

2. Geben Sie den folgenden Befehl ein: `Add-AzureAccount`
	
	Durch diesen Befehl wird ein Anmeldefenster geöffnet, sodass Sie sich mit Ihrem Geschäfts- oder Schulkonto anmelden können.

	![PowerShell-Fenster](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/add_azureaccount.png)

3. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

### Verwenden eines Zertifikats 

1. Öffnen Sie die Azure PowerShell-Konsole. 

2.	Geben Sie Folgendes ein: `Get-AzurePublishSettingsFile`.


3. Es wird ein Browserfenster geöffnet, und Sie werden aufgefordert, eine PUBLISHSETTINGS-Datei herunterzuladen. Sie enthält Informationen und ein Zertifikat für Ihr Microsoft Azure-Abonnement.

	![Downloadseite des Browsers](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Browser_download_GetPublishSettingsFile.png)

3. Speichern Sie die .publishsettings-Datei.

4. Geben Sie Folgendes ein: `Import-AzurePublishSettingsFile <PathToFile>`

	Dabei stellt `<PathToFile>` den vollständigen Pfad zur Datei ".publishsettings" dar.


	For more information, see [Get Started with Microsoft Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx). 
	
	For more information on installing and configuring PowerShell, see [How to install and configure Microsoft Azure PowerShell](install-configure-powershell.md). 


## Schritt 4: Hochladen der VHD-Datei ##

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** den URL für das Speicherkonto dar, das Sie im Schritt 2 erstellt haben. Zudem stellt **YourImagesFolder** den Container innerhalb des Blobspeichers dar, in dem Sie Ihre Images speichern möchten. **VHDName** steht für die Bezeichnung, die im klassischen Portal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei dar.


1. Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/powershell_upload_vhd.png)

	Weitere Informationen zu diesem Cmdlet finden Sie unter [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Schritt 5: Hinzufügen des Images zur Liste benutzerdefinierter Images ##
Nach dem Hochladen fügen Sie die .vhd-Datei als Image zu der Ihrem Abonnement zugeordneten Liste benutzerdefinierter Images hinzu.

1. Klicken Sie im klassischen Portal unter **Alle Elemente** auf **Virtuelle Computer**.

2. Klicken Sie unter **Virtuelle Computer** auf Images.

3. Klicken Sie auf **Image erstellen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Create_Image.png)

4. Führen Sie unter **Ein Image aus einer VHD erstellen** folgende Schritte aus:
 	

	- Specify **name**
	- Specify **description**
	- To specify the **URL of your VHD**, click the folder button to open the following window:
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Select_VHD.png)

	- Select the storage account your VHD is in and click **Open**. This returns you to the **Create an image from a VHD** window.
	- After you return to the **Create an image from a VHD** window, select the Operating System Family.
	- Check **I have run Sysprep on the virtual machine associated with this VHD** to acknowledge that you generalized the operating system in Step 1, and then click **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/Create_Image_From_VHD.png)

5. **Optional:** Sie können das Add-AzureVMImage-Cmdlet anstelle des Verwaltungsportals verwenden, um die virtuelle Festplatte als Image hinzuzufügen. Geben Sie Folgendes in die Azure PowerShell-Konsole ein:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/add_azureimage_powershell.png)

6. Das neue Image wird jetzt in der Auswahl auf der Registerkarte **Images** aufgeführt.


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/vm_custom_image.png)

	This new image is now available under **My Images** when you create a virtual machine. For instructions, see [Create a Virtual Machine Running Windows Server](virtual-machines-windows-tutorial.md).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server-classic-portal/create_vm_custom_image.png)

	> [AZURE.TIP] If you get an error when you try to create a VM, with this error message, "The VHD https://XXXXX... has an unsupported virtual size of YYYY bytes. The size must be a whole number (in MBs)," it means your VHD is not a whole number of MBs and needs to be a fixed size VHD. Try using the Add-AzureVMImage PowerShell cmdlet instead of the classic portal to add the image (see step 5, above). The Azure cmdlets ensure that the VHD meets the Azure requirements.
	
## Nächste Schritte ##

Nachdem Sie einen virtuellen Computer erstellt haben, sollten Sie versuchen, einen virtuellen SQL Server-Computer zu erstellen. Anweisungen finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers in Microsoft Azure](virtual-machines-provision-sql-server.md).

[Schritt 1: Vorbereiten des hochzuladenden Images]: #prepimage
[Schritt 2: Erstellen eines Speicherkontos in Azure]: #createstorage
[Schritt 3: Vorbereiten der Verbindung zu Azure]: #prepAzure
[Schritt 4: Hochladen der VHD-Datei]: #upload

<!--HONumber=52-->
