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
	ms.date="05/15/2015"
	ms.author="kathydav"/>

# Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, um sie als eigenes Image für die Erstellung von virtuellen Computern in Azure zu nutzen. Sie erfahren, wie Sie das Betriebssystem vorbereiten, um es zum Erstellen mehrerer virtueller Computer auf Grundlage des Images zu verwenden.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Auf einem virtuellen Computer in Azure wird das Betriebssystem ausgeführt, das auf dem Image basiert, das Sie beim Erstellen des virtuellen Computers ausgewählt haben. Ihre Images werden im VHD-Format unter einem Speicherkonto in .vhd-Dateien gespeichert. Einzelheiten finden Sie unter [Informationen zu Datenträgern und Images in Azure](https://msdn.microsoft.com/library/azure/jj672979.aspx).

Wenn Sie den virtuellen Computer erstellen, können Sie einige Betriebssystemeinstellungen anpassen, damit sie für die Anwendung geeignet sind, die Sie ausführen möchten. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](virtual-machines-create-custom.md).

**Wichtig**: Das Azure-Plattform-SLA bezieht sich nur dann auf die virtuellen Computer mit Linux-Betriebssystem, wenn eine unterstützte Distribution mit Konfigurationsdetails verwendet wird, die unter [Linux auf von Azure unterstützten Distributionen](virtual-machines-../linux-endorsed-distributions.md) angegeben sind. Alle Linux-Distributionen im Azure-Image-Katalog sind unterstützte Distributionen mit der erforderlichen Konfiguration.


##Voraussetzungen##
In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

- **Ein Verwaltungszertifikat** - Sie haben ein Verwaltungszertifikat für das Abonnement erstellt, für das Sie eine virtuelle Festplatte hochladen möchten. Sie haben das Zertifikat in eine .cer-Datei exportiert. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).

- **In einer .vhd-Datei installiertes Linux-Betriebssystem**: Sie haben ein unterstütztes Linux-Betriebssystem auf einem virtuellen Datenträger installiert. Es gibt verschiedene Tools zum Erstellen von VHD-Dateien. Beispielsweise können Sie eine Virtualisierungslösung wie Hyper-V zum Erstellen der VHD-Datei und zum Installieren des Betriebssystems verwenden. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

	**Wichtig**: Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

	Eine Liste der bestätigten Distributionen finden Sie unter [Linux zu für Azure bestätigte Distributionen](../linux-endorsed-distributions.md). Alternativ dazu können Sie den Abschnitt am Ende dieses Artikels über die [Informationen zu nicht bestätigten Distributionen](virtual-machines-linux-create-upload-vhd-generic.md) lesen.

- **Azure-Befehlszeilenschnittstelle**: Wenn Sie ein Linux-Betriebssystem zum Erstellen Ihres Image verwenden, können Sie zum Hochladen der virtuellen Festplatte die [Azure-Befehlszeilenschnittstelle](../virtual-machines-command-line-tools.md) verwenden.

- **Azure Powershell-Tools**: Das `Add-AzureVhd`-Cmdlet kann ebenfalls zum Hochladen der virtuellen Festplatte verwendet werden. Besuchen Sie [Azure-Downloads](http://azure.microsoft.com/downloads/) zum Herunterladen der Azure PowerShell-Cmdlets. Referenzinformationen hierzu finden Sie unter [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

## <a id="prepimage"> </a>Schritt 1: Vorbereiten des hochzuladenden Image ##

Microsoft Azure unterstützt eine Vielzahl von Linux-Distributionen (siehe [Unterstützte Distributionen](../linux-endorsed-distributions.md)). Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen Linux-Verteilungen, die auf Azure unterstützt werden:

- **[CentOS-basierte Verteilungen](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES und openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Sonstige – nicht unterstützte Distributionen](virtual-machines-linux-create-upload-vhd-generic.md)**

Beachten Sie auch die **[Installationshinweise für Linux](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)**. Dort erhalten Sie weitere Tipps zur Vorbereitung der Linux-Images für Azure.

Im Anschluss an die Schritte in den obigen Leitfäden sollten Sie über eine VHD-Datei verfügen, die in Azure hochgeladen werden kann.


## <a id="connect"> </a>Schritt 2: Vorbereiten der Verbindung mit Azure ##

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen.


### Bei Verwendung der Azure-Befehlszeilenschnittstelle

1. Öffnen Sie ein Fenster für die Azure-Befehlszeilenschnittstelle.

2. Geben Sie Folgendes ein:

	`azure login`

	Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.

**ODER**: Verwenden Sie stattdessen eine PUBLISHSETTINGS-Datei:

1. Öffnen Sie ein Fenster für die Azure-Befehlszeilenschnittstelle.

2. Geben Sie Folgendes ein:

	`azure account download`

	Mit diesem Befehl wird ein Browser-Fenster geöffnet und eine .publishsettings-Datei automatisch heruntergeladen. Letztere enthält die Informationen und ein Zertifikat zu Ihrem Azure-Abonnement.

3. Speichern Sie die PUBLISHSETTINGS-Datei.

4. Geben Sie Folgendes ein:

	`azure account import <PathToFile>`

	Dabei stellt `<PathToFile>` den vollständigen Pfad zur Datei ".publishsettings" dar.


### Bei Verwendung von Azure PowerShell

1. Öffnen Sie ein Azure PowerShell-Fenster.

2. Geben Sie Folgendes ein:

	`Get-AzurePublishSettingsFile`

	Mit diesem Befehl wird ein Browser-Fenster geöffnet und eine .publishsettings-Datei automatisch heruntergeladen. Letztere enthält die Informationen und ein Zertifikat zu Ihrem Azure-Abonnement.

3. Speichern Sie die .publishsettings-Datei.

4. Geben Sie Folgendes ein:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Dabei stellt `<PathToFile>` den vollständigen Pfad zur Datei ".publishsettings" dar.

	Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).


## <a id="upload"> </a>Schritt 3: Hochladen des Image zu Azure ##

### Bei Verwendung der Azure-Befehlszeilenschnittstelle

Laden Sie das Image mithilfe der Azure-Befehlszeilenschnittstelle hoch. Sie können ein Image hochladen, indem Sie den folgenden Befehl verwenden:

		azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>

### Bei Verwendung von PowerShell

Zum Hochladen der VHD-Datei wird ein Speicherkonto benötigt. Wählen Sie entweder ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues. Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** die URL für das zu verwendende Speicherkonto dar. Bei **YourImagesFolder** handelt es sich um den Container innerhalb des Blobspeichers, in dem Sie Ihre Images speichern möchten. **VHDName** steht für die Bezeichnung, die im Verwaltungsportal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei dar.

Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Weitere Informationen hierzu finden Sie unter [Add-AzureVhd]((https://msdn.microsoft.com/library/azure/dn495173.aspx).




[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
 

<!---HONumber=July15_HO1-->