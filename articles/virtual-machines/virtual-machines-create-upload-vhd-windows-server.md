<properties
	pageTitle="Erstellen und Hochladen einer Windows Server-VHD nach Azure"
	description="Erfahren Sie, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit dem Betriebssystem Windows Server in Azure erstellen und hochladen."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="kathydav"/>


# Erstellen und Hochladen einer Windows Server-VHD nach Azure#

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte \(Virtual Hard Disk, VHD\) mit einem Betriebssystem hochladen, um sie als Image für die Erstellung von virtuellen Computern zu nutzen. Weitere Details zu Datenträgern und VHDs in Microsoft Azure finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-disks-vhds.md).

> [AZURE.NOTE]Bei der Erstellung einer virtuellen Maschine basierend auf einem Image können Sie die Einstellungen des Betriebssystems für die Anwendungen entsprechend anpassen, die Sie auf dem virtuellen Computer ausführen möchten. Diese Konfiguration wird für die virtuelle Maschine gespeichert und wirkt sich nicht auf das Image aus.

## Voraussetzungen##

In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Voraussetzungen verfügen:

1. **Ein Azure-Abonnement** – wenn Sie nicht bereits eines besitzen, können Sie [ein Azure-Konto kostenlos erstellen](/pricing/free-trial/?WT.mc_id=A261C142F): Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind. Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

2. **Microsoft Azure PowerShell** – Sie haben das Microsoft Azure PowerShell-Modul installiert und für die Verwendung Ihres Abonnements konfiguriert. Informationen zum Herunterladen dieses Moduls finden Sie unter [Microsoft Azure-Downloads](http://azure.microsoft.com/downloads/). Ein Lernprogramm zum Installieren und Konfigurieren des Moduls ist [hier](../powershell-install-configure.md) verfügbar. Sie verwenden das Cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) zum Hochladen der VHD.

3. **Ein in einer .vhd-Datei gespeichertes unterstütztes Windows-Betriebssystem** - Sie haben ein unterstütztes Windows Server-Betriebssystem auf einer virtuellen Festplatte installiert. Zum Erstellen von .vhd-Dateien stehen mehrere verschiedene Tools bereit. Sie können eine Virtualisierungslösung wie etwa Hyper-V verwenden, um den virtuellen Computer zu erstellen und das Betriebssystem zu installieren. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE]Das VHDX-Format wird in Microsoft Azure nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem [Convert-VHD-Cmdlet](http://technet.microsoft.com/library/hh848454.aspx) in das VHD-Format konvertieren. Ein Lernprogramm zu diesem Thema finden Sie [hier](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

 Die folgenden Windows Server-Versionen werden unterstützt:

Betriebssystem|SKU|Service Pack|Architektur
---|---|---|---
Windows Server 2012 R2|Alle Editionen|n. z.|x64
Windows Server 2012|Alle Editionen|n. z.|x64
Windows Server 2008 R2|Alle Editionen|SP1|x64

## Schritt 1: Vorbereiten des hochzuladenden Images ##

Bevor Sie das Image in Azure hochladen können, müssen Sie es mit dem Befehl "Sysprep" verallgemeinern. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/library/bb457073.aspx) \(Verwenden von Sysprep: Einführung, in englischer Sprache\).

Führen Sie auf dem virtuellen Computer, auf dem das Betriebssystem installiert wurde, die folgende Prozedur aus:

1. Melden Sie sich beim Betriebssystem an.

2. Öffnen Sie ein Eingabeaufforderungsfenster als Administrator. Wechseln Sie in das Verzeichnis **%windir%\\system32\\sysprep**, und führen Sie dann `sysprep.exe` aus.

	![Öffnen eines Eingabeaufforderungsfensters](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Das Dialogfeld **Systemvorbereitungstool** wird angezeigt.

	![Starten von Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Wählen Sie unter **Systemvorbereitungstool** die Option **Out-of-Box-Experience \(OOBE\) für System aktivieren**, und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist.

5.  Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

6.  Klicken Sie auf **OK**.

## Schritt 2: Erstellen eines Speicherkontos in Azure ##

Sie benötigen ein Speicherkonto in Azure, um eine VHD-Datei hochzuladen, damit diese in Azure zum Erstellen eines virtuellen Computers verwendet werden kann. Über das Azure-Portal können Sie ein Speicherkonto erstellen.

1. Melden Sie sich beim [Portal](http://manage.windowsazure.com) an.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Datendienste** \> **Speicher** \> **Schnellerfassung**.

	![Speicherkonto schnell erfassen](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Füllen Sie die Felder wie folgt aus:

	- Geben Sie unter **URL** einen Unterdomänennamen ein, der im URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname im URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

	- Wählen Sie den **Standort oder die Affinitätsgruppe** für das Speicherkonto aus. Mit einer Affinitätsgruppe können Sie Ihre Cloud-Dienste und Speicher im selben Rechenzentrum platzieren.

	- Sie können wählen, ob Sie **Georeplikation** für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei an einem sekundären Speicherort repliziert. So wird die Speicherung auf jenen Speicherort umgeschaltet, wenn am primären Speicherort ein größerer Ausfall auftritt. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Wenn Sie aufgrund gesetzlicher Vorschriften oder Unternehmensrichtlinien mehr Kontrolle über den Speicherort des cloudbasierten Speichers benötigen, können Sie die Georeplikation deaktivieren. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten in dem sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten. Ausführliche Informationen zur Verwaltung der Georeplikation für Speicherkonten finden Sie unter: [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/#replication-options).

	![Speicherkontodetails eingeben](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Klicken Sie auf **Speicherkonto erstellen**. Das Konto wird nun unter **Speicher** angezeigt.

	![Speicherkonto erfolgreich erstellt](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Als Nächstes erstellen Sie einen Container für die hochgeladenen VHDs. Klicken Sie auf den Namen des Speicherkontos und dann auf **Container**.

	![Speicherkontodetails](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Klicken Sie auf **Container erstellen**.

	![Speicherkontodetails](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Geben Sie einen **Namen** für den Container ein, und wählen Sie die **Zugriffsrichtlinie** aus.

	![Containername](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]Der Container ist standardmäßig privat, und der Containerzugriff ist auf den Kontobesitzer eingeschränkt. Um öffentliche Lesezugriffe auf die im Container enthaltenen BLOBs, jedoch nicht auf die Containereigenschaften und -metadaten, zuzulassen, verwenden Sie die Option "Öffentlicher BLOB". Verwenden Sie die Option "Öffentlicher Container", um vollständigen öffentlichen Lesezugriff auf den Container und die BLOBs zuzulassen.

## Schritt 3: Vorbereiten der Verbindung mit Microsoft Azure ##

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen. Zu diesem Zweck können Sie die Microsoft Azure Active Directory-Methode oder die Zertifikatmethode verwenden.

> [AZURE.TIP]Informationen zum Einstieg in Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). Weitere Informationen finden Sie unter [Erste Schritte mit Microsoft Azure-Cmdlets.](https://msdn.microsoft.com/library/azure/jj554332.aspx)

### Verwenden der Microsoft Azure AD-Methode

1. Öffnen Sie die Azure PowerShell-Konsole.

2. Geben Sie Folgendes ein: `Add-AzureAccount`

	Durch diesen Befehl wird ein Anmeldefenster geöffnet, sodass Sie sich mit Ihrem Geschäfts- oder Schulkonto anmelden können.

	![PowerShell-Fenster](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

### Verwenden eines Zertifikats

1. Öffnen Sie die Azure PowerShell-Konsole.

2.	Geben Sie Folgendes ein: `Get-AzurePublishSettingsFile`.

3. Es wird ein Browserfenster geöffnet, und Sie werden aufgefordert, eine PUBLISHSETTINGS-Datei herunterzuladen. Sie enthält Informationen und ein Zertifikat für Ihr Microsoft Azure-Abonnement.

	![Downloadseite des Browsers](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Speichern Sie die .publishsettings-Datei.

4. Geben Sie Folgendes ein: `Import-AzurePublishSettingsFile <PathToFile>`

	Dabei stellt `<PathToFile>` den vollständigen Pfad zur Datei ".publishsettings" dar.

## Schritt 4: Hochladen der VHD-Datei

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** den URL für das Speicherkonto dar, das Sie im Schritt 2 erstellt haben. Zudem stellt **YourImagesFolder** den Container innerhalb des Blobspeichers dar, in dem Sie Ihre Images speichern möchten. **VHDName** steht für die Bezeichnung, die im Verwaltungsportal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei dar.


1. Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Weitere Informationen zum Add-AzureVhd-Cmdlet finden Sie unter [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Schritt 5: Hinzufügen des Images zur Liste benutzerdefinierter Images ##

Nach dem Hochladen fügen Sie die .vhd-Datei als Image zu der Ihrem Abonnement zugeordneten Liste benutzerdefinierter Images hinzu.

1. Klicken Sie im Portal unter **Alle Elemente** auf **Virtuelle Computer**.

2. Klicken Sie unter "Virtuelle Computer" auf **Images**.

3. Klicken Sie auf **Image erstellen**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. Führen Sie unter **Ein Image aus einer VHD erstellen** folgende Schritte aus:

	- Geben Sie einen **Namen** an.
	- Geben Sie eine **Beschreibung** an.
	- Um die **URL der VHD** anzugeben, klicken Sie auf die Ordnerschaltfläche. Das nachfolgend dargestellte Dialogfeld wird geöffnet:

	![VHD auswählen](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- Wählen Sie das Speicherkonto aus, unter dem die VHD verwaltet wird, und klicken Sie auf **Öffnen**. Dadurch kehren Sie zum Fenster **Ein Image aus einer VHD erstellen** zurück.
	- Nachdem Sie zum Fenster **Ein Image aus einer VHD erstellen** zurückgekehrt sind, wählen Sie die Betriebssystemfamilie aus.
	- Markieren Sie ** Ich habe Sysprep auf dem virtuellen Computer ausgeführt, der dieser VHD zugeordnet ist.**, um zu bestätigen, dass Sie das Betriebssystem in Schritt 1 generalisiert haben, und klicken Sie dann auf **OK**.

	![Image hinzufügen](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **OPTIONAL:** Sie können das Add-AzureVMImage-Cmdlet anstelle des Portals verwenden, um die VHD als Image hinzuzufügen. Geben Sie Folgendes in die Azure PowerShell-Konsole ein:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Nachdem Sie die vorherigen Schritte abgeschlossen haben, wird das neue Image aufgelistet, sobald Sie die Registerkarte **Images** auswählen.


	![Benutzerdefiniertes Image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Dieses neue Image steht jetzt beim Erstellen eines virtuellen Computers unter **Eigene Images** zur Verfügung. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers mit Windows](virtual-machines-windows-create-custom.md).

	![VM von benutzerdefiniertem Image erstellen](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]Wenn Sie beim Erstellen eines virtuellen Computers einen Fehler mit der folgenden Fehlermeldung erhalten: "Die VHD https://XXXXX... weist eine nicht unterstützte virtuelle Größe von YYYY Bytes auf. Die Größe muss eine ganze Zahl \(in MB\) sein", bedeutet dies, dass die virtuelle Festplatte keine ganze Zahl von MBs hat und eine VHD mit fester Größe sein muss. Verwenden Sie anstelle des Verwaltungsportals das Add-AzureVMImage-PowerShell-Cmdlet, um das Image hinzuzufügen \(siehe oben, Schritt 5\). Die Azure-Cmdlets sorgen dafür, dass die virtuelle Festplatte die Anforderungen für Azure erfüllt.

## Nächste Schritte ##

Nachdem Sie einen virtuellen Computer erstellt haben, sollten Sie versuchen, einen virtuellen SQL Server-Computer zu erstellen. Anweisungen finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers in Microsoft Azure](virtual-machines-provision-sql-server.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=August15_HO7-->