<properties
   pageTitle="Erstellen eines lokalen Images eines virtuellen Computers für den Azure Marketplace | Microsoft Azure"
   description="Informationen über die Schritte zum Erstellen eines lokalen Images eines virtuellen Computers und zur Bereitstellung für den Kauf im Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="02/04/2016"
  ms.author="hascipio; v-divte"/>

# Entwickeln eines lokalen Images eines virtuellen Computers für Azure Marketplace
Es wird dringend empfohlen, virtuelle Azure-Festplatten (Azure-VHDs) direkt in der Cloud mithilfe des Remotedesktopprotokolls zu entwickeln. Bei Bedarf ist es jedoch auch möglich, eine VHD herunterzuladen und mithilfe lokaler Infrastruktur zu entwickeln.

Für die lokale Entwicklung müssen Sie die Betriebssystem-VHD des erstellten virtuellen Computers herunterladen. Diese Schritte werden als Teil von Schritt 3.3 oben ausgeführt.

## Herunterladen eines VHD-Images
### Suchen einer Blob-URL
Um die VHD-Datei herunterzuladen, müssen Sie zuerst nach der Blob-URL für den Betriebssystem-Datenträger suchen.

Suchen Sie die Blob-URL im neuen [Microsoft Azure-Portal](https://ms.portal.azure.com):

1.	Wechseln Sie zu **Durchsuchen** > **VMs**, und wählen Sie dann den bereitgestellten virtuellen Computer aus.
2.	Wählen Sie unter **Konfigurieren** die Kachel **Datenträger** aus. Dadurch wird das Blatt „Datenträger“ geöffnet.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)

3.	Wählen Sie den **Betriebssystemdatenträger** aus. Dadurch wird ein anderes Blatt mit Datenträgereigenschaften angezeigt, einschließlich des Speicherorts der VHD-Datei.
4.	Kopieren Sie die BLOB-URL.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)

5.	Löschen Sie jetzt den bereitgestellten virtuellen Computer, ohne die zugrunde liegenden Datenträger zu löschen. Sie können den virtuellen Computer auch beenden, anstatt ihn zu löschen. Laden Sie die Betriebssystem-VHD-Datei nicht herunter, wenn der virtuelle Computer ausgeführt wird.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### Herunterladen einer VHD-Datei
Nachdem Sie die Blob-URL kennen, können Sie die virtuelle Festplatte über das [Azure-Portal](http://manage.windowsazure.com/) oder per PowerShell herunterladen.
> [AZURE.NOTE] Zum Zeitpunkt der Erstellung dieser Anleitung ist die Funktionalität zum Herunterladen einer VHD-Datei noch nicht im neuen Microsoft Azure-Portal vorhanden.

**Herunterladen der Betriebssystem-VHD-Datei über das aktuelle [Azure-Portal](http://manage.windowsazure.com/)**

1.	Melden Sie sich beim Azure-Portal an, sofern Sie noch nicht angemeldet sind.
2.	Klicken Sie auf die Registerkarte **Speicher**.
3.	Wählen Sie das Speicherkonto aus, in dem die VHD-Datei gespeichert ist.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)

4.	Dadurch werden die Eigenschaften des Speicherkontos angezeigt. Wählen Sie die Registerkarte **Container** aus.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)

5.	Wählen Sie den Container, in dem die VHD-Datei gespeichert ist. Standardmäßig wird die VHD-Datei beim Erstellen über das Portal in einem virtuellen Festplattencontainer gespeichert.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)

6.	Wählen Sie die VHD-Datei mit dem richtigen Betriebssystem aus, indem Sie die URL mit der gespeicherten URL vergleichen.
7.	Klicken Sie auf **Download**.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### Herunterladen einer VHD-Datei mithilfe von PowerShell
Zusätzlich zur Verwendung des Azure-Portals können Sie das Cmdlet [Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) zum Herunterladen der Betriebssystem-VHD-Datei verwenden.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Beispiel:
        Save-AzureVhd -Source „https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd“ -LocalFilePath „C:\\Users\\Administrator\\Desktop\\baseimagevm.vhd“ -StorageKey <String>

> [AZURE.NOTE] **Save-AzureVhd** verfügt auch über eine **NumberOfThreads**-Option, mit der die Parallelität so erhöht wird, dass die verfügbare Bandbreite optimal für den Download eingesetzt wird.

## Hochladen von VHD-Dateien in ein Azure-Speicherkonto
Wenn Sie die virtuellen Festplatten lokal vorbereitet haben, müssen Sie sie in ein Speicherkonto in Azure hochladen. Dieser Schritt erfolgt, nachdem die VHD-Datei lokal erstellt wurde, jedoch bevor eine Zertifizierung für das VM-Image abgerufen wird.

### Erstellen eines Speicherkontos und Containers
Es wird empfohlen, virtuelle Festplatten in ein Speicherkonto in einer Region in den USA hochzuladen. Alle virtuellen Festplatten für eine einzelne SKU sollten in einem einzelnen Container innerhalb eines einzelnen Speicherkontos platziert werden.

Um ein Speicherkonto zu erstellen, können Sie das [Microsoft Azure-Portal](https://portal.azure.com/), PowerShell oder das Linux-Befehlszeilentool verwenden.

**Erstellen eines Speicherkontos im Microsoft Azure-Portal**

1.	Klicken Sie auf **Neu**.
2.	Wählen Sie **Speicher**.
3.	Geben Sie den Namen des Speicherkontos ein, und wählen Sie dann einen Speicherort aus.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)

4.	Klicken Sie auf **Erstellen**.
5.	Das Blatt für das erstellte Speicherkonto sollte geöffnet werden. Wählen Sie andernfalls **Durchsuchen** > **Speicherkonten** aus. Wählen Sie auf dem Blatt „Speicherkonto“ das erstellte Speicherkonto aus.
6.	Wählen Sie **Container** aus.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img09.png)

7.	Wählen Sie auf dem Blatt „Container“ die Option **Hinzufügen** aus, und geben Sie dann einen Containernamen und die Berechtigungen für den Container ein. Wählen Sie **Privat** als Containerberechtigung aus.

> [AZURE.TIP] Es wird empfohlen, einen Container pro SKU zu erstellen, die Sie veröffentlichen möchten.

  ![Abbildung](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### Erstellen eines Speicherkontos mithilfe von PowerShell
Erstellen Sie per PowerShell mit dem Cmdlet [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) ein Speicherkonto.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Erstellen Sie anschließend mit dem Cmdlet [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) einen Container in diesem Speicherkonto.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [AZURE.NOTE] Bei diesen Befehlen wird davon ausgegangen, dass der aktuelle Kontext des Speicherkontos in PowerShell bereits festgelegt wurde. Weitere Informationen zur Einrichtung von PowerShell finden Sie unter [Einrichten von Azure PowerShell](marketplace-publishing-powershell-setup.md).
### Erstellen eines Speicherkontos mit dem Befehlszeilentool für Mac und Linux
Erstellen Sie wie folgt über das [Linux-Befehlszeilentool](../virtual-machines/command-line-tools/) ein Speicherkonto.

        azure storage account create mystorageaccount --location "West US"

Erstellen Sie wie folgt einen Container.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## Hochladen einer VHD-Datei
Nachdem das Speicherkonto und der Container erstellt wurden, können Sie Ihre vorbereiteten virtuellen Festplatten hochladen. Dazu können Sie PowerShell, das Befehlszeilentool für Linux oder andere Azure-Speicherverwaltungstools verwenden.

### Hochladen einer virtuellen Festplatte über PowerShell
Verwenden Sie das Cmdlet [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### Hochladen einer virtuellen Festplatte über das Befehlszeilentool für Mac und Linux
Verwenden Sie folgenden Befehl im [Linux-Befehlszeilentool](../virtual-machines/command-line-tools/): 
azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## Weitere Informationen
- [Erstellen eines Images eines virtuellen Computers für den Marketplace](marketplace-publishing-vm-image-creation.md)
- [Einrichten von Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0211_2016-->