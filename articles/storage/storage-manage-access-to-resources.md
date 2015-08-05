<properties 
	pageTitle="Verwalten des Zugriffs auf Azure Storage-Ressourcen | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Zugriff von Benutzern auf Ihre Azure Storage-Ressourcen verwalten." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="micurd;tamram"/>

# Verwalten des Zugriffs auf Azure Storage-Ressourcen

## Übersicht

Standardmäßig kann nur der Besitzer eines Speicherkontos auf Speicherressourcen in diesem Konto zugreifen. Wenn Ihr Dienst oder Ihre Anwendung diese Ressourcen für andere Clients zur Verfügung stellen muss, ohne den Zugriffsschlüssel freizugeben, stehen folgende Optionen zum Ermöglichen des Zugriffs zur Verfügung:

- Sie können die Berechtigungen eines Containers festlegen, um anonymen Lesezugriff auf den Container und seine Blobs zuzulassen. Dies ist für Tabellen oder Warteschlangen nicht zulässig.

- Sie können eine Ressource über eine SAS (Shared Access Signature, Signatur für freigegebenen Zugriff) verfügbar machen, die es Ihnen ermöglicht, beschränkten Zugriff auf eine Container-, Blob-, Tabellen- oder Warteschlangenressource zu delegieren, indem Sie das Intervall, für das die Ressourcen verfügbar sind, und die Berechtigungen, die ein Client haben muss, angeben.

- Sie können eine gespeicherte Zugriffsrichtlinie verwenden, um Shared Access Signatures für einen Container oder dessen Blobs, eine Warteschlange oder eine Tabelle zu verwalten. Über die gespeicherte Zugriffsrichtlinie haben Sie zusätzliche Kontrolle über die Shared Access Signatures und zudem eine direkte Möglichkeit, diese aufzuheben.

## Beschränken des Zugriffs auf Container und Blobs

Standardmäßig kann nur der Besitzer eines Speicherkontos auf einen Container und alle darin enthaltenen Blobs zugreifen. Wenn anonyme Benutzer Leseberechtigungen für einen Container und die enthaltenen Blobs erhalten sollen, können Sie den öffentlichen Zugriff durch Festlegen der Containerberechtigungen gestatten. Anonyme Benutzer können Blobs innerhalb eines öffentlich zugänglichen Containers lesen, ohne dass die Anforderung authentifiziert werden muss.

Container stellen die folgenden Optionen zum Verwalten des Containerzugriffs bereit:

- **Vollständiger öffentlicher Lesezugriff:** Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

- **Öffentlicher Lesezugriff nur für Blobs:** Blobdaten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.

- **Kein öffentlicher Lesezugriff:** Container- und Blobdaten können nur vom Kontobesitzer gelesen werden.

>[AZURE.NOTE]Wenn Sie für Ihren Dienst eine genauere Kontrolle über die Blob-Ressourcen benötigen, oder wenn Sie Berechtigungen für andere Vorgänge als Lesevorgänge angeben möchten, verwenden Sie eine SAS, um eine Ressource für Benutzer zugänglich zu machen.

### Für anonyme Benutzer verfügbare Funktionen
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

## Erstellen und Verwenden einer SAS (Shared Access Signature)
Eine SAS (Shared Access Signature) ist ein URI, der eingeschränkte Zugriffsrechte auf Container, Blobs, Warteschlangen und Tabellen für die Dauer eines bestimmten Zeitintervalls gewährt. Indem Sie einem Client eine SAS bereitstellen, können Sie diesem ermöglichen, auf Ressourcen in Ihrem Speicherkonto zuzugreifen, ohne dass Sie den Kontoschlüssel freigeben müssen.

>[AZURE.NOTE]Eine ausführliche Konzeptübersicht und ein Lernprogramm zu SAS finden Sie unter [SAS (Shared Access Signatures)](storage-dotnet-shared-access-signature-part-1.md).

Zu den unterstützten Vorgängen mithilfe von SAS gehören Folgende:

- Lesen und Schreiben von Seiten- oder Block-Blob-Inhalten, Blockierlisten, Eigenschaften und Metadaten

- Löschen, Leasen und Erstellen einer Momentaufnahme eines Blobs

- Auflisten der Blobs innerhalb eines Containers

- Hinzufügen, Entfernen, Aktualisieren und Löschen von Warteschlangennachrichten (in der Speicherclientbibliothek 2.0 und neuer)

- Abrufen von Warteschlangenmetadaten, einschließlich der Nachrichtenanzahl (in der Speicherclientbibliothek 2.0 und neuer)

- Abfragen, Hinzufügen, Aktualisieren, Löschen von Tabellenentitäten sowie Ausführen von Upsert-Vorgängen für Tabellenentitäten (in der Speicherclientbibliothek 2.0 und neuer)

Die Abfrageparameter des SAS-URI enthalten alle Informationen, die erforderlich sind, um gesteuerten Zugriff auf eine Speicherressource zu gewähren. Die URI-Abfrageparameter geben das Zeitintervall an, in dem die SAS gültig ist, die gewährten Berechtigungen, die Ressource, die bereitgestellt werden soll, und die Signatur, die die Speicherdienste verwenden sollen, um die Anforderung zu authentifizieren.

Darüber hinaus kann der URI der SAS auf eine gespeicherte Zugriffsrichtlinie verweisen, die ein höheres Maß an Kontrolle für eine Gruppe von Signaturen bietet, einschließlich der Möglichkeit, den Zugriff auf die Ressource ggf. zu ändern oder aufzuheben.

Weitere Informationen zum URI-Format einer SAS finden Sie unter [Delegieren des Zugriffs mit einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/ee395415.aspx).

### Sichere Verwendung einer SAS
Eine SAS erlaubt den Zugriff auf die durch die gewährten Berechtigungen des URI angegebene Ressource. Sie sollten immer HTTPS verwenden, um einen SAS-URI zu erstellen. Durch Verwendung von HTTP für eine SAS kann Ihr Speicherkonto anfällig für böswillige Benutzer werden.

Wenn eine SAS Zugriff gewährt, der nicht für die Öffentlichkeit vorgesehen ist, sollte sie mit möglichst wenigen Berechtigungen erstellt werden. Darüber hinaus sollte eine SAS über eine sichere Verbindung an Clients verteilt werden, einer gespeicherten Zugriffsrichtlinie zugeordnet werden, um sie aufheben zu können, und die kürzest mögliche Lebensdauer der Signatur angeben.

>[AZURE.NOTE]Ein SAS-URI wird dem Kontoschlüssel, mit dem die Signatur erstellt wurde, und der zugehörigen gespeicherten Zugriffsrichtlinie (sofern vorhanden) zugeordnet. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, kann eine SAS nur durch Änderung des Kontoschlüssels aufgehoben werden.

### Erstellen einer SAS
Im folgenden Codebeispiel wird eine Zugriffsrichtlinie für einen Container erstellt. Anschließend wird eine SAS für den Container generiert. Diese SAS kann dann an Clients verteilt werden:

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### Verwenden einer SAS
Ein Client, der eine SAS erhält, kann sie aus seinem Code heraus verwenden, um ein Objekt vom Typ [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx) zu erstellen. Diese Anmeldeinformationen können dann verwendet werden, um ein [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx)- oder ein [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx)-Objekt für die Verwendung mit der Ressource zu erstellen, wie im folgenden Beispiel veranschaulicht:

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## Verwenden einer gespeicherten Zugriffsrichtlinie
Eine gespeicherte Zugriffsrichtlinie bietet ein höheres Maß an Kontrolle für eine SAS auf Serverseite. Durch Festlegen einer gespeicherten Zugriffsrichtlinie können SAS gruppiert werden, und es werden zusätzliche Einschränkungen für Signaturen bereitgestellt, die von der Richtlinie abhängig sind. Sie können eine gespeicherte Zugriffsrichtlinie verwenden, um die Startzeit, die Ablaufzeit oder die Berechtigungen für eine Signatur zu ändern, oder um diese aufzuheben, nachdem sie ausgegeben wurde.

Eine gespeicherte Zugriffsrichtlinie bietet größere Kontrolle über die von Ihnen freigegebenen SAS. Statt die Lebensdauer und die Berechtigungen der Signatur in der URL anzugeben, können Sie diese Parameter in der gespeicherten Zugriffsrichtlinie angeben, die im freigegebenen Blob, im freigegebenen Container, in der freigegebenen Warteschlange oder in der freigegebenen Tabelle gespeichert wird. Um diese Parameter für eine oder mehrere Signaturen zu ändern, können Sie die gespeicherte Zugriffsrichtlinie ändern und müssen die Signaturen nicht erneut ausgeben. Sie können die Signatur auch schnell aufheben, indem Sie die gespeicherte Zugriffsrichtlinie ändern.

Angenommen, Sie haben eine SAS ausgegeben, die einer gespeicherten Zugriffsrichtlinie zugeordnet ist. Wenn Sie die Ablaufzeit in der gespeicherten Zugriffsrichtlinie angegeben haben, können Sie die Zugriffsrichtlinie ändern, um die Lebensdauer der Signatur zu verlängern, ohne eine neue Signatur ausgeben zu müssen.

Als bewährte Methode wird empfohlen, eine gespeicherte Zugriffsrichtlinie für jede signierte Ressource anzugeben, für die Sie eine SAS ausgeben, da die gespeicherte Richtlinie verwendet werden kann, um die Signatur nach dem Ausgeben zu ändern oder zu widerrufen. Wenn Sie keine gespeicherte Richtlinie angeben, wird empfohlen, die Lebensdauer der Signatur zu begrenzen, um mögliche Risiken für die Speicherkontoressourcen zu minimieren.

### Zuordnen einer SAS zu einer gespeicherten Zugriffsrichtlinie
Eine gespeicherte Zugriffsrichtlinie beinhaltet einen Namen mit einer Länge von bis zu 64 Zeichen, der innerhalb des Containers, der Warteschlange oder der Tabelle eindeutig ist. Um eine SAS einer gespeicherten Zugriffsrichtlinie zuzuordnen, geben Sie nun diesen Bezeichner beim Erstellen der SAS an. Im URI der SAS gibt das Feld *signedidentifier* den Bezeichner der gespeicherten Zugriffsrichtlinie an.

Ein Container, eine Warteschlange oder eine Tabelle kann bis zu 5 gespeicherte Zugriffsrichtlinien enthalten. Richtlinie kann von einer beliebigen Anzahl von freigegebenen Zugriffssignaturen verwendet werden.

>[AZURE.NOTE]Wenn Sie eine gespeicherte Zugriffsrichtlinie für einen Container, eine Warteschlange oder eine Tabelle einrichten, kann es bis zu 30 Sekunden dauern, bis die Richtlinie angewendet wird. Während dieses Intervalls tritt bei einer SAS, die der gespeicherten Zugriffsrichtlinie zugeordnet ist, so lange ein Fehler mit dem Statuscode 403 (Unzulässig) auf, bis die Zugriffsrichtlinie aktiv ist.

### Angeben von Zugriffsrichtlinienparametern für eine SAS
In der gespeicherten Zugriffsrichtlinie können die folgenden Zugriffsrichtlinienparameter für die zugeordneten Signaturen angegeben werden:

- Startzeit

- Ablaufzeit

- Berechtigungen

Je nachdem, wie Sie den Zugriff auf die Speicherressource steuern möchten, können Sie all diese Parameter in der gespeicherten Zugriffsrichtlinie angeben und sie in der URL für die SAS auslassen. Auf diese Weise können Sie das Verhalten der zugeordneten Signatur jederzeit ändern sowie die Signatur aufheben. Oder Sie können einen oder mehrere der Zugriffsrichtlinienparameter in der gespeicherten Zugriffsrichtlinie und die anderen Zugriffsrichtlinienparameter in der URL angeben. Und Sie können alle Parameter in der URL angeben. In diesem Fall können Sie die gespeicherte Zugriffsrichtlinie verwenden, um die Signatur aufzuheben, nicht jedoch, um ihr Verhalten zu ändern.

Die SAS und die gespeicherte Zugriffsrichtlinie müssen zusammen alle Felder enthalten, die zum Authentifizieren der Signatur erforderlich sind. Wenn Pflichtfelder fehlen, tritt bei der Anforderung ein Fehler mit dem Statuscode 403 (Unzulässig) auf. Wenn ein Feld sowohl in der SAS-URL als auch in der gespeicherten Zugriffsrichtlinie angegeben ist, tritt beim Ausführen der Anforderung ein Fehler mit dem Statuscode 403 (Fehlerhafte Anforderung) auf. Weitere Informationen zu den Feldern, aus denen sich die Signatur zusammensetzt, finden Sie unter "Erstellen und Verwenden einer SAS (Shared Access Signature)".

### Ändern oder Aufheben einer gespeicherten Zugriffsrichtlinie

Um den Zugriff auf SAS aufzuheben, die dieselbe gespeicherte Zugriffsrichtlinie verwenden, entfernen Sie die gespeicherte Richtlinie aus der Speicherressource, indem Sie die Liste der gespeicherten Richtlinien durch eine neue Liste überschreiben, in der der betreffende Richtlinienname nicht enthalten ist. Wenn Sie die Zugriffseinstellungen für eine gespeicherte Zugriffsrichtlinie ändern möchten, überschreiben Sie die Liste der gespeicherten Richtlinien durch eine neue Liste, die eine Richtlinie mit demselben Namen enthält, die über neue Zugriffssteuerungsdetails verfügt.

## Siehe auch

- [Authentifizierung für Azure Storage-Dienste](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md)
- [Delegieren des Zugriffs mit einer Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=July15_HO4-->