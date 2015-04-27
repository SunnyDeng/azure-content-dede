<properties 
	pageTitle="Erstellen und Hochladen einer Linux-VHD in Azure" 
	description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die das Linux-Betriebssystem enthält." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, um sie als eigenes Image für die Erstellung von virtuellen Computern in Azure zu nutzen. Sie erfahren, wie Sie das Betriebssystem vorbereiten, um es zum Erstellen mehrerer virtueller Computer auf Grundlage des Images zu verwenden.  

> [AZURE.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um die Schritte in diesem Artikel auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Testkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Erstellen eines Azure-Kontos](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Auf einem virtuellen Computer in Azure wird das Betriebssystem ausgeführt, das auf dem Image basiert, das Sie beim Erstellen des virtuellen Computers ausgewählt haben. Ihre Images werden im VHD-Format unter einem Speicherkonto in .vhd-Dateien gespeichert. Weitere Informationen zu Datenträgern und Images in Azure finden Sie unter [Verwalten von Datenträgern und Images](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

Wenn Sie den virtuellen Computer erstellen, können Sie einige Betriebssystemeinstellungen anpassen, damit sie für die Anwendung geeignet sind, die Sie ausführen möchten. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](/de-de/manage/windows/how-to-guides/custom-create-a-vm/).

**Wichtig**: Das Azure-Plattform-SLA bezieht sich nur dann auf die virtuellen Computer, die das Linux-Betriebssystem ausführen, wenn eine der unterstützten Verteilungen mit den Konfigurationsdetails verwendet wird, die in [diesem Artikel](http://support.microsoft.com/kb/2805216) angegeben sind. Alle Linux-Verteilungen, die in der Azure-Image-Galerie bereitgestellt werden, sind unterstützte Verteilungen mit der erforderlichen Konfiguration.


##Voraussetzungen##
In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

- **Ein Verwaltungszertifikat** - Sie haben ein Verwaltungszertifikat für das Abonnement erstellt, für das Sie eine virtuelle Festplatte hochladen möchten. Sie haben das Zertifikat in eine .cer-Datei exportiert. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Erstellen eines Verwaltungszertifikats für Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx). 

- **In einer .vhd-Datei installiertes Linux-Betriebssystem**  - Sie haben ein unterstütztes Linux-Betriebssystem auf einer virtuellen Festplatte installiert. Es gibt mehrere Tools zum Erstellen von .vhd-Dateien. Beispielsweise können Sie eine Virtualisierungslösung wie Hyper-V zum Erstellen der .vhd-Datei und zum Installieren des Betriebssystems verwenden. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx). 

	**Wichtig**: Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem Cmdlet "convert-vhd" in das VHD-Format konvertieren.

	Eine Liste der unterstützten Verteilungen finden Sie unter [Linux auf Azure - unterstützte Verteilungen](linux-endorsed-distributions.md). Alternativ dazu können Sie den Abschnitt am Ende dieses Artikels mit [Informationen zu nicht unterstützten Verteilungen](virtual-machines-linux-create-upload-vhd-generic.md) lesen.

- **Linux Azure-Befehlszeilentool**: Wenn Sie ein Linux-Betriebssystem zum Erstellen Ihres Images verwenden, können Sie die [Azure-Befehlszeilentools für Linux und Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409) zum Hochladen der virtuellen Festplatte verwenden.

- **Azure Powershell-Tools** - Das Cmdlet  `Add-AzureVhd` kann auch zum Hochladen der virtuellen Festplatte verwendet werden. Besuchen Sie die [Azure-Downloads](http://azure.microsoft.com/downloads/) zum Herunterladen der Azure PowerShell-Cmdlets. Referenzinformationen hierzu finden Sie unter [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).


Diese Aufgabe umfasst die folgenden Schritte:

- [Schritt 1: Vorbereiten des hochzuladenden Images] []
- [Schritt 2: Erstellen eines Speicherkontos in Azure] []
- [Schritt 3: Vorbereiten der Verbindung mit Azure] []
- [Schritt 4: Hochladen des Images zu Azure] []

## <a id="prepimage"> </a>Schritt 1: Vorbereiten des hochzuladenden Images ##

Microsoft Azure unterstützt eine Vielzahl von Linux-Verteilungen (siehe [Unterstützte Verteilungen](linux-endorsed-distributions.md)). Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen Linux-Verteilungen, die auf Azure unterstützt werden:

- **[CentOS-basierte Verteilungen](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES und openSUSE](virtual-machines-linux-create-upload-vhd-suse.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Sonstige - nicht unterstützte Verteilungen](virtual-machines-linux-create-upload-vhd-generic.md)**

Beachten Sie auch die **[Installationshinweise für Linux](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)**. Dort erhalten Sie weitere Tipps zur Vorbereitung der Linux-Images für Azure.

Im Anschluss an die Schritte in den obigen Leitfäden sollten Sie über eine VHD-Datei verfügen, die in Azure hochgeladen werden kann.


## <a id="createstorage"> </a>Schritt 2: Erstellen eines Speicherkontos in Azure ##

Ein Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf die Speicherdienste dar. Es ist mit Ihrem Azure-Abonnement verknüpft. Sie benötigen in Azure ein Speicherkonto, um eine .vhd-Datei nach Azure hochladen zu können, die zum Erstellen eines virtuellen Computers verwendet werden kann. Sie können ein Speicherkonto erstellen, indem Sie das Azure-Verwaltungsportal verwenden.

1. Melden Sie sich beim Azure-Verwaltungsportal an.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. Klicken Sie auf **Speicherkonto** und anschließend auf **Schnellerfassung**.

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. Füllen Sie die Felder wie folgt aus:

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- Geben Sie unter **URL** einen Unterdomänennamen ein, der in der URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname in der URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.
	
- Wählen Sie den Standort oder die Affinitätsgruppe für das Speicherkonto aus. Durch das Angeben einer Affinitätsgruppe können Sie die Clouddienste im selben Rechenzentrum gemeinsam mit dem Speicher unterbringen.
 
- Sie können wählen, ob Sie Georeplikation für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei zu einem sekundären Speicherort repliziert. So wird die Speicherung auf einen sekundären Speicherort umgeschaltet, wenn ein größerer Ausfall auftritt, der am primären Speicherort nicht behoben werden kann. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Falls die gesetzlichen Anforderungen oder Unternehmensrichtlinien eine schärfere Kontrolle des Speicherorts für eine cloudbasierte Speicherung erforderlich machen, können Sie die Georeplikation abschalten. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten zum sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten.

5. Klicken Sie auf **Speicherkonto erstellen**.

	Das Konto ist nun unter den **Speicherkonten** aufgelistet.

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>Schritt 3: Vorbereiten der Verbindung mit Azure ##

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen. 

1. Öffnen Sie ein Azure PowerShell-Fenster.

2. Geben Sie Folgendes ein: 

	`Get-AzurePublishSettingsFile`

	Mit diesem Befehl wird ein Browser-Fenster geöffnet und eine .publishsettings-Datei automatisch heruntergeladen. Letztere enthält die Informationen und ein Zertifikat zu Ihrem Azure-Abonnement. 

3. Speichern Sie die Datei ".publishsettings". 

4. Geben Sie Folgendes ein:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Dabei stellt `<Pfad zu Datei>` den vollständigen Pfad zur Datei ".publishsettings" dar. 

	Weitere Informationen finden Sie unter [Erste Schritte mit Azure-Cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx). 


## <a id="upload"> </a>Schritt 4: Hochladen des Images zu Azure ##

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blob-Speichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** die URL für das Speicherkonto dar, das Sie im Schritt 2 erstellt haben. Zudem stellt **YourImagesFolder** den Container innerhalb des Blob-Speichers dar, in dem Sie Ihre Images speichern möchten. **VHDName** ist die Bezeichnung, die im Verwaltungsportal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei dar. 

Führen Sie einen der folgenden Schritte aus:

- Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Weitere Informationen hierzu finden Sie unter [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx).

- Verwenden Sie das Linux-Befehlszeilentool zum Hochladen des Images. Sie können ein Image hochladen, indem Sie den folgenden Befehl verwenden:

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[Schritt 1: Vorbereiten des hochzuladenden Images]: #prepimage
[Schritt 2: Erstellen eines Speicherkontos in Azure]: #createstorage
[Schritt 3: Vorbereiten der Verbindung mit Azure]: #connect
[Schritt 4: Hochladen des Images zu Azure]: #upload


<!--HONumber=45--> 
