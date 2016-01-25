<properties 
	pageTitle="Verwalten des anonymen Lesezugriffs auf Container und Blobs | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Container und Blobs für den anonymen Zugriff verfügbar machen und darauf programmgesteuert zugreifen." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="tamram"/>

# Verwalten des anonymen Lesezugriffs auf Container und Blobs

## Übersicht

Standardmäßig kann nur der Besitzer eines Speicherkontos auf Speicherressourcen in diesem Konto zugreifen. Nur für Blobspeicher können Sie die Berechtigungen für einen Container so festlegen, dass der anonyme Lesezugriff auf den Container und seine Blobs erlaubt wird. Dadurch können Sie Zugriff auf diese Ressourcen gewähren, ohne Ihren Kontoschlüssel freigeben zu müssen.

Anonymer Zugriff ist am besten für Szenarien, in denen bestimmte Blobs für den anonymen Lesezugriff stets zur Verfügung stehen sollen. Für eine präzisere Steuerung können Sie eine SAS (Shared Access Signature) erstellen, die Ihnen das Delegieren eines eingeschränkten Zugriffs mithilfe verschiedener Berechtigungen und über einen angegebenen Zeitraum ermöglicht. Weitere Informationen zum Erstellen einer SAS finden Sie unter [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md).

## Erteilen von anonymen Benutzerberechtigungen für Container und Blobs

Standardmäßig kann nur der Besitzer eines Speicherkontos auf einen Container und alle darin enthaltenen Blobs zugreifen. Wenn anonyme Benutzer Leseberechtigungen für einen Container und die enthaltenen Blobs erhalten sollen, können Sie den öffentlichen Zugriff durch Festlegen der Containerberechtigungen gestatten. Anonyme Benutzer können Blobs innerhalb eines öffentlich zugänglichen Containers lesen, ohne dass die Anforderung authentifiziert werden muss.

Container stellen die folgenden Optionen zum Verwalten des Containerzugriffs bereit:

- **Vollständiger öffentlicher Lesezugriff:** Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

- **Öffentlicher Lesezugriff nur für Blobs:** Blobdaten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.

- **Kein öffentlicher Lesezugriff:** Container- und Blobdaten können nur vom Kontobesitzer gelesen werden.

Sie können Containerberechtigungen auf folgende Weise festlegen:

- Im [Azure-Portal](portal.azure.com)
- Programmgesteuert mithilfe der Speicherclientbibliothek oder der REST-API.
- Mit PowerShell Informationen zum Festlegen von Containerberechtigungen über Azure PowerShell finden Sie unter [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full#how-to-manage-azure-blobs).

### Festlegen von Containerberechtigungen im Azure-Portal

Befolgen Sie zum Festlegen von Containerberechtigungen im [Azure-Portal](portal.azure.com) diese Anweisungen:

1. Navigieren Sie zum Dashboard Ihres Speicherkontos.
2. Wählen Sie den Containernamen in der Liste aus. Beachten Sie, dass Sie rechts neben der Spalte „Name“ klicken müssen, um den Containernamen auszuwählen. Durch Klicken auf den Namen werden die im Container enthaltenen Blobs angezeigt.
3. Klicken Sie auf der Symbolleiste auf **Bearbeiten**.
4. Wählen Sie im Dialogfeld **Metadaten bearbeiten** im Feld **Zugriff** die gewünschten Berechtigungsstufe aus (siehe das nachstehende Bildschirmfoto).

	![Dialogfeld „Containermetadaten bearbeiten“](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-1.png)

### Programmgesteuertes Festlegen von Containerberechtigungen mithilfe von .NET

Zum Festlegen von Berechtigungen für einen Container mithilfe der .NET-Clientbibliothek rufen Sie zunächst mithilfe der **GetPermissions**-Methode die vorhandenen Berechtigungen ab. Legen Sie dann die **PublicAccess**-Eigenschaft für das **BlobContainerPermissions**-Objekt fest, das von der **GetPermissions**-Methode zurückgegeben wird. Rufen Sie abschließend die **SetPermissions**-Methode mit den aktualisierten Berechtigungen auf.

Im folgenden Beispiel werden die Berechtigungen des Containers auf vollständigen öffentlichen Lesezugriff festgelegt. Legen Sie zum Festlegen von Berechtigungen auf den öffentlichen Lesezugriff nur für Blobs die **PublicAccess**-Eigenschaft auf **BlobContainerPublicAccessType.Blob** fest. Um alle Berechtigungen für anonyme Benutzer zu entfernen, legen Sie die Eigenschaft auf **BlobContainerPublicAccessType.Off** fest.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## Anonymer Zugriff auf Container und Blobs

Ein Client, der auf Container und Blobs anonym zugreift, kann Konstruktoren verwenden, für die keine Anmeldeinformationen erforderlich sind. In den folgenden Beispielen werden verschiedene Möglichkeiten zum anonymen Verweisen auf Blob-Dienstressourcen gezeigt.

### Erstellen eines anonymen Clientobjekts

Sie können ein neues Dienstclientobjekt für den anonymen Zugriff durch Bereitstellen des Blob-Dienstendpunkts für das Konto erstellen. Allerdings müssen Sie auch den Namen eines Containers in diesem Konto kennen, der für den anonymen Zugriff verfügbar ist.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### Anonymes Verweisen auf einen Container

Wenn Sie über die URL zu einem Container verfügen, der anonym verfügbar ist, können Sie damit direkt auf den Container verweisen.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### Anonymes Verweisen auf ein Blob

Wenn Sie über die URL zu einem Blob verfügen, das für den anonymen Zugriff verfügbar ist, können Sie damit über diese URL direkt auf das Blob verweisen:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## Für anonyme Benutzer verfügbare Features

Die folgende Tabelle zeigt, welche Vorgänge von anonymen Benutzern aufgerufen werden können, wenn der öffentliche Zugriff in der Zugriffssteuerungsliste (Access Control List, ACL) eines Containers gestattet wurde.

| REST-Vorgang | Berechtigung mit vollständigem öffentlichen Lesezugriff | Berechtigung mit öffentlichem Lesezugriff nur für Blobs |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Nur Besitzer | Nur Besitzer |
| Create Container | Nur Besitzer | Nur Besitzer |
| Get Container Properties | Alle | Nur Besitzer |
| Get Container Metadata | Alle | Nur Besitzer |
| Set Container Metadata | Nur Besitzer | Nur Besitzer |
| Get Container ACL | Nur Besitzer | Nur Besitzer |
| Set Container ACL | Nur Besitzer | Nur Besitzer |
| Delete Container | Nur Besitzer | Nur Besitzer |
| List Blobs | Alle | Nur Besitzer |
| Put Blob | Nur Besitzer | Nur Besitzer |
| Get Blob | Alle | Alle |
| Get Blob Properties | Alle | Alle |
| Set Blob Properties | Nur Besitzer | Nur Besitzer |
| Get Blob Metadata | Alle | Alle |
| Set Blob Metadata | Nur Besitzer | Nur Besitzer |
| Put Block | Nur Besitzer | Nur Besitzer |
| Get Block List (nur Blöcke mit ausgeführtem Commit) | Alle | Alle |
| Get Block List (nur Blöcke ohne ausgeführten Commit oder alle Blöcke) | Nur Besitzer | Nur Besitzer |
| Put Block List | Nur Besitzer | Nur Besitzer |
| Delete Blob | Nur Besitzer | Nur Besitzer |
| Kopieren von Blobs | Nur Besitzer | Nur Besitzer |
| Snapshot Blob | Nur Besitzer | Nur Besitzer |
| Lease Blob | Nur Besitzer | Nur Besitzer |
| Put Page | Nur Besitzer | Nur Besitzer |
| Get Page Ranges | Alle | Alle |
| Blob anfügen | Nur Besitzer | Nur Besitzer |


## Weitere Informationen

- [Authentifizierung für Azure Storage-Dienste](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md)
- [Delegieren des Zugriffs mit einer Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=AcomDC_0114_2016-->