<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Verwenden von Blob-Speicher aus Java

In diesem Leitfaden wird die Durchführung gängiger Szenarien mit dem Microsoft Azure-Blobspeicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][Azure Storage-SDK für Java]. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

Hinweis: Ein SDK steht für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][Azure Storage-SDK für Android].

## <a name="Contents"> </a>Inhaltsverzeichnis

-   [Was ist Blob-Speicher?][Was ist Blob-Speicher?]
-   [Konzepte][Konzepte]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Erstellen einer Java-Anwendung][Erstellen einer Java-Anwendung]
-   [Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher][Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher]
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge][Einrichten einer Azure-Speicherverbindungszeichenfolge]
-   [Gewusst wie: Erstellen eines Containers][Gewusst wie: Erstellen eines Containers]
-   [Gewusst wie: Hochladen eines Blobs in einen Container][Gewusst wie: Hochladen eines Blobs in einen Container]
-   [Gewusst wie: Auflisten der Blobs in einem Container][Gewusst wie: Auflisten der Blobs in einem Container]
-   [Gewusst wie: Herunterladen eines Blobs][Gewusst wie: Herunterladen eines Blobs]
-   [Gewusst wie: Löschen eines Blobs][Gewusst wie: Löschen eines Blobs]
-   [Gewusst wie: Löschen eines Blob-Containers][Gewusst wie: Löschen eines Blob-Containers]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Erstellen eines Azure-Speicherkontos

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Erstellen einer Java-Anwendung

In diesem Leitfaden werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird.

Dafür müssen Sie das Java Development Kit (JDK) installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen. Sobald Sie dies erledigt haben, müssen Sie sicherstellen, dass Ihre Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][Azure Storage-SDK für Java] auf GitHub aufgelistet sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Sobald Sie diese Aufgaben abgeschlossen haben, können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## <a name="ConfigureStorage"> </a>Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher

Fügen Sie die folgenden "import"-Anweisungen am Anfang der Java-Datei hinzu, um die Stellen anzugeben, an denen Azure-Speicher-APIs auf Blobs zugreifen sollen:

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge
zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName-* und *AccountKey-* Werte eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Microsoft Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="CreateContainer"> </a>Gewusst wie: Erstellen eines Containers

Ein CloudBlobClient-Objekt ermöglicht den Abruf von Referenzobjekten für Container und Blobs. Mit dem folgenden Code wird ein **CloudBlobClient**-Objekt erstellt. (Hinweis: Es gibt zusätzliche Möglichkeiten zum Erstellen von **CloudStorageAccount**-Objekten. Weitere Informationen finden Sie unter **CloudStorageAccount** im Thema zur [Azure Storage-Client-SDK-Referenz][Azure Storage-Client-SDK-Referenz].)

Alle Blobs befinden sich in einem Container. Mithilfe des **CloudBlobClient**-Objekts können Sie einen Verweis auf den Container abrufen, den Sie verwenden möchten. Falls der Container nicht vorhanden ist, können Sie ihn mit der **createIfNotExists**-Methode erstellen. Andernfalls gibt die Methode den vorhandenen Container zurück. Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Optional: Konfigurieren eines Containers für den öffentlichen Zugriff

Die Berechtigungen eines Containers werden standardmäßig für den privaten Zugriff konfiguriert. Sie können die Berechtigungen eines Containers jedoch einfach konfigurieren, um allen Benutzern im Internet einen öffentlichen Schreibzugriff zu gewähren:

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a>Gewusst wie: Hochladen eines Blobs in einen Container

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blob-Verweises, um eine Datei in ein Blob hochzuladen. Sobald Sie über einen Blob-Verweis verfügen, können Sie jeden Datenstrom hochladen, indem Sie die upload-Methode für den Blob-Verweis aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Dieses Codebeispiel zeigt diesen Vorgang. Dabei wird davon ausgegangen, dass der Container bereits erstellt wurde.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a>Gewusst wie: Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzulisten, müssen Sie zuerst einen Containerverweis abrufen, ebenso wie beim Hochladen eines Blobs. Sie können die **listBlobs**-Methode des Containers mit einer **for**-Schleife verwenden. Der folgende Code gibt den URI der einzelnen Blobs in einem Container in der Konsole aus.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Beim Blob-Dienst wird ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren.

Beispiel: Sie verfügen über einen Container mit der Bezeichnung "photos", in den Sie Blobs mit der Bezeichnung "rootphoto1", "2010/photo1", "2010/photo2" und "2011/photo1" hochladen. Hierdurch werden die virtuellen Verzeichnisse "2010" und "2011" im Container "photos" erstellt. Wenn Sie **listBlobs** für den Container "photos" aufrufen, enthält die zurückgegebene Sammlung **CloudBlobDirectory**- und **CloudBlob**-Objekte, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. In diesem Fall werden die Verzeichnisse "2010" und "2011" sowie das Foto "rootphoto1" zurückgegeben. Mit dem **instanceof**-Operator können Sie diese Objekte unterscheiden.

Optional können Sie Parameter an die **listBlobs**-Methode übergeben,
indem Sie den **useFlatBlobListing**-Parameter auf "true" festlegen. Dies führt dazu,
dass jedes Blob unabhängig vom Verzeichnis zurückgegeben wird. Weitere
Informationen finden Sie unter **CloudBlobContainer.listBlobs** in der [Azure Storage-Client-SDK-Referenz][Azure Storage-Client-SDK-Referenz].

## <a name="DownloadBlob"> </a>Gewusst wie: Herunterladen eines Blobs

Führen Sie zum Herunterladen von Blobs die gleichen Schritte wie zum Hochladen eines Blobs aus, um einen Blob-Verweis abzurufen. Im Uploadbeispiel haben Sie die upload-Methode für das Blob-Objekt aufgerufen. Im folgenden Beispiel rufen Sie die download-Methode auf, um den Blob-Inhalt an ein Datenstromobjekt wie z. B. **FileOutputStream** zu übertragen, das Sie verwenden können, um das Blob in einer lokalen Datei zu speichern.

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a>Gewusst wie: Löschen eines Blobs

Zum Löschen eines Blobs rufen Sie einen Blobverweis ab und rufen dann **deleteIfExists** auf.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a>Gewusst wie: Löschen eines Blob-Containers

Zum Löschen eines Blobcontainers rufen Sie einen Blobcontainerverweis ab
und rufen **deleteIfExists** auf.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   [Azure Storage-SDK für Java][Azure Storage-SDK für Java]
-   [Azure Storage-Client-SDK-Referenz][Azure Storage-Client-SDK-Referenz]
-   [Azure Storage-REST-API][Azure Storage-REST-API]
-   [Azure Storage-Teamblog][Azure Storage-Teamblog]

  [Azure Storage-SDK für Java]: https://github.com/azure/azure-storage-java
  [Nächste Schritte]: #NextSteps
  [Azure Storage-SDK für Android]: https://github.com/azure/azure-storage-android
  [Konzepte]: #Concepts
  [Erstellen eines Azure-Speicherkontos]: #CreateAccount
  [Erstellen einer Java-Anwendung]: #CreateApplication
  [Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher]: #ConfigureStorage
  [Einrichten einer Azure-Speicherverbindungszeichenfolge]: #ConnectionString
  [Gewusst wie: Erstellen eines Containers]: #CreateContainer
  [Gewusst wie: Hochladen eines Blobs in einen Container]: #UploadBlob
  [Gewusst wie: Auflisten der Blobs in einem Container]: #ListBlobs
  [Gewusst wie: Herunterladen eines Blobs]: #DownloadBlob
  [Gewusst wie: Löschen eines Blobs]: #DeleteBlob
  [Gewusst wie: Löschen eines Blob-Containers]: #DeleteContainer
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Azure Storage-Client-SDK-Referenz]: http://dl.windowsazure.com/storage/javadoc/
  [Azure Storage-REST-API]: http://msdn.microsoft.com/de-de/library/azure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
