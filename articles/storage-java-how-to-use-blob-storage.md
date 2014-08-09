<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

Verwenden von Blob-Speicher aus Java
====================================

In diesem Leitfaden wird die Durchführung gängiger Szenarien mit dem Azure-Blob-Speicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java](http://www.windowsazure.com/de-de/develop/java/). Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Blob-Speicher?](#what-is)
-   [Konzepte](#Concepts)
-   [Erstellen eines Azure-Speicherkontos](#CreateAccount)
-   [Erstellen einer Java-Anwendung](#CreateApplication)
-   [Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher](#ConfigureStorage)
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge](#ConnectionString)
-   [Gewusst wie: Erstellen eines Containers](#CreateContainer)
-   [Gewusst wie: Hochladen eines Blobs in einen Container](#UploadBlob)
-   [Gewusst wie: Auflisten der Blobs in einem Container](#ListBlobs)
-   [Gewusst wie: Herunterladen eines Blobs](#DownloadBlob)
-   [Gewusst wie: Löschen eines Blobs](#DeleteBlob)
-   [Gewusst wie: Löschen eines Blob-Containers](#DeleteContainer)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer Java-Anwendung
------------------------------

In diesem Leitfaden verwenden Sie Speicherfunktionen, die lokal innerhalb einer Java-Anwendung oder in Code innerhalb einer Webrolle oder Workerrolle in Azure ausgeführt werden können. Es wird vorausgesetzt, dass Sie das Java Development Kit (JDK) heruntergeladen und installiert und die Anweisungen auf der [Downloadseite des Azure-SDK für Java](http://www.windowsazure.com/de-de/develop/java/) ausgeführt haben, um die Azure-Bibliotheken für Java und das Azure-SDK zu installieren, und dass Sie ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellt haben.

Sie können beliebige Entwicklungstools zum Erstellen Ihrer Anwendung verwenden, einschließlich Editor. Sie benötigen lediglich die Möglichkeit, ein Java-Projekt zu kompilieren und auf die Azure-Bibliotheken für Java zu verweisen.

Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher
-------------------------------------------------------------

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei hinzu, um die Stellen anzugeben, an denen Azure-Speicher-APIs auf Blobs zugreifen sollen:

    // Die folgenden Importe zur Verwendung von Blob-APIs einschließen
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

Einrichten einer Azure-Speicherverbindungszeichenfolge
------------------------------------------------------

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zu Speicherendpunkten und Anmeldeinformationen zum Zugreifen auf Datenverwaltungsdienste. Bei Ausführung in einer Clientanwendung müssen Sie die Speicherverbindungszeichenfolge in folgendem Format angeben und dabei den Namen Ihres Speicherkontos und den primären Zugriffsschlüssel für das Speicherkonto verwendet, wie im Verwaltungsportal für die Werte *AccountName* und *AccountKey* angegeben. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren können:

    // Definieren Sie die Verbindungszeichenfolge mit Ihren Werten
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Rolle in Azure ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei (ServiceConfiguration.cscfg) gespeichert werden; der Zugriff darauf kann mit einem Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Nachfolgend sehen Sie ein Beispiel für den Abruf der Verbindungszeichenfolge aus einem **Setting**-Element namens *StorageConnectionString* in der Dienstkonfigurationsdatei:

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Gewusst wie: Erstellen eines Containers
---------------------------------------

Ein CloudBlobClient-Objekt ermöglicht den Abruf von Referenzobjekten für Container und Blobs. Mit dem folgenden Code wird ein **CloudBlobClient**-Objekt erstellt.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Blob-Client erstellen
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Alle Blobs befinden sich in einem Container. Mithilfe des **CloudBlobClient**-Objekts können Sie einen Verweis auf den Container abrufen, den Sie verwenden möchten. Falls der Container nicht vorhanden ist, können Sie ihn mit der **createIfNotExist**-Methode erstellen; andernfalls gibt die Methode den vorhandenen Container zurück. Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen.

    // Verweis auf einen Container abrufen
    // Der Containername muss aus Kleinbuchstaben bestehen
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Container erstellen, wenn er noch nicht vorhanden ist
    container.createIfNotExist();

Wenn Sie diese Dateien öffentlich machen möchten, können Sie die Containerberechtigungen festlegen.

    // Berechtigungsobjekt erstellen
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Öffentlichen Zugriff in Berechtigungsobjekt einschließen
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Berechtigungen für den Container festlegen
    container.uploadPermissions(containerPermissions);

Blobs in einem öffentlichen Container können von allen Benutzern des Internets angezeigt werden, der öffentliche Zugriff ist jedoch auf Lesevorgänge beschränkt.

Gewusst wie: Hochladen eines Blobs in einen Container
-----------------------------------------------------

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blob-Verweises, um eine Datei in ein Blob hochzuladen. Sobald Sie über einen Blob-Verweis verfügen, können Sie jeden Datenstrom hochladen, indem Sie die upload-Methode für den Blob-Verweis aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Dieses Codebeispiel zeigt diesen Vorgang. Dabei wird davon ausgegangen, dass der Container bereits erstellt wurde.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Blob-Client erstellen
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Blob "myimage.jpg" erstellen oder mit Inhalt aus einer lokalen Datei überschreiben
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\myimages\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

Gewusst wie: Auflisten der Blobs in einem Container
---------------------------------------------------

Um die Blobs in einem Container aufzulisten, müssen Sie zuerst einen Containerverweis abrufen, ebenso wie beim Hochladen eines Blobs. Sie können die **listBlobs**-Methode des Containers mit einer for-Schleife verwenden. Der folgende Code gibt den URI der einzelnen Blobs in einem Container in der Konsole aus.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Blob-Client erstellen
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Schleife durch Blobs im Container ausführen und URI für die einzelnen Blobs ausgeben
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

Beim Blob-Dienst wird ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren.

Beispiel: Sie verfügen über einen Container mit der Bezeichnung "photos", in den Sie Blobs mit der Bezeichnung "rootphoto1", "2010/photo1", "2010/photo2" und "2011/photo1" hochladen. Hierdurch werden die virtuellen Verzeichnisse "2010" und "2011" im Container "photos" erstellt. Wenn Sie **listBlobs** für den Container "photos" aufrufen, enthält die zurückgegebene Sammlung **CloudBlobDirectory**- und **CloudBlob**-Objekte, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. In diesem Fall werden die Verzeichnisse "2010" und "2011" sowie das Foto "rootphoto1" zurückgegeben. Mit dem **instanceof**-Operator können Sie diese Objekte unterscheiden.

Optional können Sie Parameter an die **listBlobs**-Methode übergeben, indem Sie den **useFlatBlobListing**-Parameter auf "true" festlegen. Dies führt dazu, dass jedes Blob unabhängig vom Verzeichnis zurückgegeben wird. Weitere Informationen finden Sie unter "CloudBlobContainer.listBlobs" in der Javadocs-Dokumentation.

Gewusst wie: Herunterladen eines Blobs
--------------------------------------

Führen Sie zum Herunterladen von Blobs die gleichen Schritte wie zum Hochladen eines Blobs aus, um einen Blob-Verweis abzurufen. Im Uploadbeispiel haben Sie die upload-Methode für das Blob-Objekt aufgerufen. Im folgenden Beispiel rufen Sie die download-Methode auf, um den Blob-Inhalt an ein Datenstromobjekt wie z. B. **FileOutputStream** zu übertragen, das Sie verwenden können, um das Blob in einer lokalen Datei zu speichern.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Blob-Client erstellen
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Für jedes Element im Container
    for (ListBlobItem blobItem : container.listBlobs()) {
        // Wenn das Element ein Blob ist, kein virtuelles Verzeichnis
        if (blobItem instanceof CloudBlob) {
            // Element herunterladen und in gleichnamiger Datei speichern
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

Gewusst wie: Löschen eines Blobs
--------------------------------

Zum Löschen eines Blobs rufen Sie einen Blob-Verweis ab und rufen dann **delete** auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Blob-Client erstellen
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "myimage.jpg" abrufen
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Blob löschen
    blob.delete();

Gewusst wie: Löschen eines Blob-Containers
------------------------------------------

Zum Löschen eines Blob-Containers rufen Sie einen Blob-Containerverweis ab und rufen "delete" auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Blob-Client erstellen
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Blob-Container löschen
    container.delete();

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen von Blob-Speicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure]
-   Besuchen Sie den Blog des Azure-Speicherteams: http://blogs.msdn.com/b/windowsazurestorage/

[Azure SDK for Java]: http://www.windowsazure.com/de-de/develop/java/
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
