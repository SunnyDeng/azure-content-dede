<properties
	pageTitle="Erstellen und Hochladen einer Linux-VHD | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD), die das Linux-Betriebssystem enthält, mit dem klassischen Bereitstellungsmodell erstellen und hochladen."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, um sie als eigenes Image für die Erstellung von virtuellen Computern in Azure zu nutzen. Sie erfahren, wie Sie das Betriebssystem vorbereiten, um es zum Erstellen mehrerer virtueller Computer auf Grundlage des Images zu verwenden.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Ein virtueller Computer in Azure führt das Betriebssystem basierend auf dem Image aus, das Sie beim Erstellen auswählen. Diese Images werden im VHD-Format unter einem Speicherkonto in .vhd-Dateien gespeichert. Weitere Informationen finden Sie unter [Datenträger in Azure](virtual-machines-disks-vhds.md) und [Images in Azure](virtual-machines-images.md).

Wenn Sie den virtuellen Computer erstellen, können Sie einige Betriebssystemeinstellungen anpassen, damit sie für die Anwendung geeignet sind, die Sie ausführen möchten. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](virtual-machines-create-custom.md).

**Wichtig**: Das Azure-Plattform-SLA bezieht sich nur dann auf die virtuellen Computer mit Linux-Betriebssystem, wenn eine unterstützte Distribution mit Konfigurationsdetails verwendet wird, die unter [Linux auf von Azure unterstützten Distributionen](virtual-machines-../linux-endorsed-distributions.md) angegeben sind. Alle Linux-Distributionen im Azure-Image-Katalog sind unterstützte Distributionen mit der erforderlichen Konfiguration.


## Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

- **Ein Verwaltungszertifikat** - Sie haben ein Verwaltungszertifikat für das Abonnement erstellt, für das Sie eine virtuelle Festplatte hochladen möchten. Sie haben das Zertifikat in eine .cer-Datei exportiert. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Übersicht über Zertifikate für Azure](../cloud-services/cloud-services-certs-create.md).

- **In einer .vhd-Datei installiertes Linux-Betriebssystem**: Sie haben ein unterstütztes Linux-Betriebssystem auf einem virtuellen Datenträger installiert. Es gibt verschiedene Tools zum Erstellen von VHD-Dateien. Beispielsweise können Sie eine Virtualisierungslösung wie Hyper-V zum Erstellen der VHD-Datei und zum Installieren des Betriebssystems verwenden. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx).

	**Wichtig**: Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

	Eine Liste der bestätigten Distributionen finden Sie unter [Linux zu für Azure bestätigte Distributionen](../linux-endorsed-distributions.md). Eine allgemeine Liste mit Linux-Distributionen finden Sie unter [Informationen zu nicht unterstützten Verteilungen](virtual-machines-linux-create-upload-vhd-generic.md).

- **Azure-Befehlszeilenschnittstelle**: Wenn Sie ein Linux-Betriebssystem zum Erstellen Ihres Image verwenden, können Sie zum Hochladen der virtuellen Festplatte die [Azure-Befehlszeilenschnittstelle](../virtual-machines-command-line-tools.md) verwenden.

- **Azure Powershell-Tools**: Das `Add-AzureVhd`-Cmdlet kann ebenfalls zum Hochladen der virtuellen Festplatte verwendet werden. Besuchen Sie [Azure-Downloads](https://azure.microsoft.com/downloads/) zum Herunterladen der Azure PowerShell-Cmdlets. Referenzinformationen hierzu finden Sie unter [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).

<a id="prepimage"> </a>
## Schritt 1: Vorbereiten des hochzuladenden Images

Microsoft Azure unterstützt eine Vielzahl von Linux-Distributionen (siehe [Unterstützte Distributionen](../linux-endorsed-distributions.md)). Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen Linux-Verteilungen, die auf Azure unterstützt werden:

- **[CentOS-basierte Verteilungen](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Debian Linux](virtual-machines-linux-create-upload-vhd-debian.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES und openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[Sonstige – nicht unterstützte Distributionen](virtual-machines-linux-create-upload-vhd-generic.md)**

Beachten Sie auch die **[Installationshinweise für Linux](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)**. Dort erhalten Sie weitere Tipps zur Vorbereitung der Linux-Images für Azure.

Nachdem Sie die Schritte in den obigen Leitfäden ausgeführt haben, sollten Sie über eine VHD-Datei verfügen, die in Azure hochgeladen werden kann.

<a id="connect"> </a>
## Schritt 2: Vorbereiten der Verbindung mit Azure

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen.


### Bei Verwendung der Azure-Befehlszeilenschnittstelle

Mit der neuesten Azure-Befehlszeilenschnittstelle wird standardmäßig das Ressourcen-Manager-Bereitstellungsmodell verwenden. Stellen Sie mit dem folgenden Befehl sicher, dass Sie das klassische Bereitstellungsmodell verwenden:

		azure config mode asm  

Stellen Sie als Nächstes mit einer der folgenden Anmeldemethoden die Verbindung zu Ihrem Azure-Abonnement her.

Verwenden Sie die Azure AD-Methode für die Anmeldung:

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

	Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure über die Azure-Befehlszeilenschnittstelle](../xplat-cli-connect.md).


### Bei Verwendung von Azure PowerShell

Verwenden Sie die Azure AD-Methode für die Anmeldung:

1. Öffnen Sie ein Azure PowerShell-Fenster.

2. Geben Sie Folgendes ein:

	`Add-AzureAccount`

	Geben Sie die Benutzer-ID und das Kennwort Ihrer Organisation ein, wenn Sie dazu aufgefordert werden.

**ODER**: Speichern Sie stattdessen die Datei ".publishsettings".

1. Öffnen Sie ein Azure PowerShell-Fenster.

2. Geben Sie Folgendes ein:

	`Get-AzurePublishSettingsFile`

	Mit diesem Befehl wird ein Browser-Fenster geöffnet und eine .publishsettings-Datei automatisch heruntergeladen. Letztere enthält die Informationen und ein Zertifikat zu Ihrem Azure-Abonnement.

3. Speichern Sie die .publishsettings-Datei.

4. Geben Sie Folgendes ein:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Dabei stellt `<PathToFile>` den vollständigen Pfad zur Datei ".publishsettings" dar.

	Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

> [AZURE.NOTE] Es wird empfohlen, dass Sie die neuere Azure Active Directory-Methode zum Anmelden in Ihrem Azure-Abonnement verwenden, entweder aus der Azure-Befehlszeilenschnittstelle oder aus Azure PowerShell.

<a id="upload"> </a>
## Schritt 3: Hochladen des Image in Azure

Zum Hochladen der VHD-Datei wird ein Speicherkonto benötigt. Wählen Sie entweder ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues. Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** die URL für das zu verwendende Speicherkonto dar. Bei **YourImagesFolder** handelt es sich um den Container innerhalb des Blobspeichers, in dem Sie Ihre Images speichern möchten. **VHDName** steht für die Bezeichnung, die im [Azure-Portal](http://portal.azure.com) oder im [klassischen Azure-Portal](http://manage.windowsazure.com) zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei auf Ihrem Computer dar.


### Bei Verwendung der Azure-Befehlszeilenschnittstelle

Verwenden Sie die Azure-Befehlszeilenschnittstelle zum Hochladen des Images mithilfe des folgenden Befehls:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

Weitere Informationen finden Sie unter [Azure-Befehlszeilenschnittstellenreferenz für die Azure-Dienstverwaltung](virtual-machines-command-line-tools.md).


### Bei Verwendung von PowerShell

Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

Weitere Informationen hierzu finden Sie unter [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx).


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0128_2016-->