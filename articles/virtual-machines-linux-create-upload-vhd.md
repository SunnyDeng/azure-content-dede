<properties urlDisplayName="Upload a VHD" pageTitle="Erstellen und Hochladen einer Linux-VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die das Linux-Betriebssystem enth&auml;lt." metaCanonical="" services="virtual-machines" documentationCenter="" title="Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enth&auml;lt" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Erstellen und Hochladen einer virtuellen Festplatte, die das Linux-Betriebssystem enthält

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, um sie als eigenes Image für die Erstellung von virtuellen Computern in Azure zu nutzen. Sie erfahren, wie Sie das Betriebssystem vorbereiten, um es zum Erstellen mehrerer virtueller Computer auf Grundlage des Images zu verwenden.

> [WACOM.NOTE] Sie benötigen keine Erfahrungen mit Azure-VMs, um die Schritte in diesem Artikel auszuführen. Allerdings benötigen Sie ein Azure-Konto. Sie können ein kostenloses Textkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)][Create a Windows Azure account (in englischer Sprache)].

Auf einem virtuellen Computer in Azure wird das Betriebssystem ausgeführt, das auf dem Image basiert, das Sie beim Erstellen des virtuellen Computers ausgewählt haben. Ihre Images werden im VHD-Format unter einem Speicherkonto in VHD-Dateien gespeichert. Weitere Informationen zu Datenträgern und Images in Azure finden Sie unter [Verwalten von Datenträgern und Images][Verwalten von Datenträgern und Images].

Wenn Sie den virtuellen Computer erstellen, können Sie einige Betriebssystemeinstellungen anpassen, damit sie für die Anwendung geeignet sind, die Sie ausführen möchten. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][Erstellen eines benutzerdefinierten virtuellen Computers].

**Wichtig**: Die Azure-Plattform-SLA bezieht sich nur dann auf die virtuellen Computer, die das Linux-Betriebssystem ausführen, wenn eine der bestätigten Distributionen mit den Konfigurationsdetails verwendet wird, die in [diesem Artikel][diesem Artikel] angegeben sind. Alle Linux-Distributionen, die in der Azure-Image-Galerie bereitgestellt werden, sind bestätigte Distributionen mit der erforderlichen Konfiguration.

## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

-   **Ein Verwaltungszertifikat** - Sie haben ein Verwaltungszertifikat für das Abonnement erstellt, für das Sie eine virtuelle Festplatte hochladen möchten. Sie haben das Zertifikat in eine .cer-Datei exportiert. Weitere Informationen zum Erstellen von Zertifikaten finden Sie unter [Erstellen eines Verwaltungszertifikats für Azure][Erstellen eines Verwaltungszertifikats für Azure].

-   **In einer .vhd-Datei installiertes Linux-Betriebssystem**: Sie haben ein unterstütztes Linux-Betriebssystem auf einem virtuellen Datenträger installiert. Es gibt mehrere Tools zum Erstellen von VHD-Dateien. Beispielsweise können Sie eine Virtualisierungslösung wie Hyper-V zum Erstellen der VHD-Datei und zum Installieren des Betriebssystems verwenden. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers][Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers].

    **Wichtig**: Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.

    Eine Liste der bestätigten Distributionen finden Sie unter [Linux zu für Azure bestätigte Distributionen][Linux zu für Azure bestätigte Distributionen]. Alternativ dazu können Sie den Abschnitt am Ende dieses Artikels über die [Informationen zu nicht bestätigten Distributionen][Informationen zu nicht bestätigten Distributionen] lesen.

-   **Linux Azure-Befehlszeilentool**: Wenn Sie ein Linux-Betriebssystem zum Erstellen Ihres Images verwenden, können Sie die [Azure-Befehlszeilentools für Linux und Mac][Azure-Befehlszeilentools für Linux und Mac] zum Hochladen der virtuellen Festplatte verwenden.

-   **Azure PowerShell-Tools**: Das Cmdlet `Add-AzureVhd` kann auch zum Hochladen der virtuellen Festplatte verwendet werden. Besuchen Sie die [Azure-Downloads][Azure-Downloads] zum Herunterladen der Azure PowerShell-Cmdlets. Referenzinformationen hierzu finden Sie unter [Add-AzureVhd][Add-AzureVhd].

Diese Aufgabe umfasst die folgenden Schritte:

-   [Schritt 1: Vorbereiten des hochzuladenden Images][Schritt 1: Vorbereiten des hochzuladenden Images]
-   [Schritt 2: Erstellen eines Speicherkontos in Azure][Schritt 2: Erstellen eines Speicherkontos in Azure]
-   [Schritt 3: Vorbereiten der Verbindung mit Azure][Schritt 3: Vorbereiten der Verbindung mit Azure]
-   [Schritt 4: Hochladen des Abbilds zu Azure][Schritt 4: Hochladen des Abbilds zu Azure]

## <span id="prepimage"></span> </a>Schritt 1: Vorbereiten des hochzuladenden Images

Microsoft Azure unterstützt eine Vielzahl von Linux-Distributionen (Informationen dazu finden Sie in den [bestätigten Distributionen][Linux zu für Azure bestätigte Distributionen]). Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen Linux-Distributionen, die auf Azure unterstützt werden:

-   **[CentOS-basierte Distributionen][CentOS-basierte Distributionen]**
-   **[Oracle Linux][Oracle Linux]**
-   **[SLES & openSUSE][SLES & openSUSE]**
-   **[Ubuntu][Ubuntu]**
-   **[Sonstige – nicht unterstützte Distributionen][Informationen zu nicht bestätigten Distributionen]**

Im Anschluss an die Schritte in den obigen Leitfäden sollten Sie über eine VHD-Datei verfügen, die für das Hochladen in Azure bereit ist.

## <span id="createstorage"></span> </a>Schritt 2: Erstellen eines Speicherkontos in Azure

Ein Speicherkonto stellt die höchste Ebene des Namespace für den Zugriff auf die Speicherdienste dar. Es ist mit Ihrem Azure-Abonnement verknüpft. Sie benötigen in Azure ein Speicherkonto, um eine .vhd-Datei nach Azure hochladen zu können, die zum Erstellen eines virtuellen Computers verwendet werden kann. Sie können ein Speicherkonto erstellen, indem Sie das Azure-Verwaltungsportal verwenden.

1.  Melden Sie sich auf dem Azure-Verwaltungsportal an.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

    ![Speicherkonto erstellen][Speicherkonto erstellen]

3.  Klicken Sie auf **Speicherkonto** und anschließend auf **Schnellerfassung**.

    ![Speicherkonto schnell erfassen][Speicherkonto schnell erfassen]

4.  Füllen Sie die Felder wie folgt aus:

    ![Speicherkontodetails eingeben][Speicherkontodetails eingeben]

-   Geben Sie unter **URL** einen Unterdomänennamen ein, der im URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname im URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

-   Wählen Sie den Standort oder die Affinitätsgruppe für das Speicherkonto aus. Durch das Angeben einer Affinitätsgruppe können Sie die Clouddienste im selben Datencenter gemeinsam mit der Speicherung unterbringen.

-   Sie können wählen, ob Sie Georeplikation für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei in einem sekundären Speicherort repliziert. So wird die Speicherung auf einen sekundären Speicherort umgeschaltet, wenn ein größerer Ausfall auftritt, der im primären Speicherort nicht behoben werden kann. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Falls die gesetzlichen Anforderungen oder Unternehmensrichtlinien eine schärfere Kontrolle des Speicherorts für eine cloudbasierte Speicherung erforderlich machen, können Sie die Georeplikation abschalten. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten in dem sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten.

1.  Klicken Sie auf **Speicherkonto erstellen**.

    Das Konto ist nun unter den **Speicherkonten** aufgelistet.

    ![Speicherkonto erfolgreich erstellt][Speicherkonto erfolgreich erstellt]

## <span id="connect"></span> </a>Schritt 3: Vorbereiten der Verbindung mit Azure

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen.

1.  Öffnen Sie ein Azure PowerShell-Fenster.

2.  Geben Sie Folgendes ein:

    `Get-AzurePublishSettingsFile`

    Mit diesem Befehl wird ein Browser-Fenster geöffnet und eine .publishsettings-Datei automatisch heruntergeladen. Letztere enthält die Informationen und ein Zertifikat zu Ihrem Azure-Abonnement.

3.  Speichern Sie die .publishsettings-Datei.

4.  Geben Sie Folgendes ein:

    `Import-AzurePublishSettingsFile <PathToFile>`

    Dabei stellt `<PathToFile>` den vollständigen Pfad zur PUBLISHSETTINGS-Datei dar.

    Weitere Informationen finden Sie unter [Erste Schritte mit Azure Cmdlets][Erste Schritte mit Azure Cmdlets].

## <span id="upload"></span> </a>Schritt 4: Hochladen des Abbilds zu Azure

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** den URL für das Speicherkonto dar, das Sie im Schritt 2 erstellt haben. Zudem stellt **YourImagesFolder** den Container innerhalb des Blobspeichers dar, in dem Sie Ihre Images speichern möchten. **VHDName** steht für die Bezeichnung, die im Verwaltungsportal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** stellt den vollständigen Pfad und den Namen der .vhd-Datei dar.

Führen Sie einen der folgenden Schritte aus:

-   Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

        `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Weitere Informationen hierzu finden Sie unter [Add-AzureVhd][1].

-   Verwenden Sie das Linux-Befehlszeilentool zum Hochladen des Images. Sie können ein Image hochladen, indem Sie den folgenden Befehl verwenden:

        # azure vm image create <image-name> --location <location-of-the-data-center> --OS Linux <source-path-to the vhd>

  [Create a Windows Azure account (in englischer Sprache)]: http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/
  [Verwalten von Datenträgern und Images]: http://msdn.microsoft.com/de-de/library/windowsazure/jj672979.aspx
  [Erstellen eines benutzerdefinierten virtuellen Computers]: /de-de/manage/windows/how-to-guides/custom-create-a-vm/
  [diesem Artikel]: http://support.microsoft.com/kb/2805216
  [Erstellen eines Verwaltungszertifikats für Azure]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
  [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers]: http://technet.microsoft.com/library/hh846766.aspx
  [Linux zu für Azure bestätigte Distributionen]: ../linux-endorsed-distributions
  [Informationen zu nicht bestätigten Distributionen]: ../virtual-machines-linux-create-upload-vhd-generic
  [Azure-Befehlszeilentools für Linux und Mac]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [Azure-Downloads]: http://azure.microsoft.com/de-de/downloads/
  [Add-AzureVhd]: http://msdn.microsoft.com/library/windowsazure/dn495173.aspx
  [Schritt 1: Vorbereiten des hochzuladenden Images]: #prepimage
  [Schritt 2: Erstellen eines Speicherkontos in Azure]: #createstorage
  [Schritt 3: Vorbereiten der Verbindung mit Azure]: #connect
  [Schritt 4: Hochladen des Abbilds zu Azure]: #upload
  [CentOS-basierte Distributionen]: ../virtual-machines-linux-create-upload-vhd-centos
  [Oracle Linux]: ../virtual-machines-linux-create-upload-vhd-oracle
  [SLES & openSUSE]: ../virtual-machines-linux-create-upload-vhd-suse
  [Ubuntu]: ../virtual-machines-linux-create-upload-vhd-ubuntu
  [Speicherkonto erstellen]: ./media/virtual-machines-linux-create-upload-vhd/create.png
  [Speicherkonto schnell erfassen]: ./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png
  [Speicherkontodetails eingeben]: ./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png
  [Speicherkonto erfolgreich erstellt]: ./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png
  [Erste Schritte mit Azure Cmdlets]: http://msdn.microsoft.com/de-de/library/windowsazure/jj554332.aspx
  [1]: http://msdn.microsoft.com/de-de/library/windowsazure/dn205185.aspx
