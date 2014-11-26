<properties urlDisplayName="Blob Service" pageTitle="Verwenden des Blob-Speichers (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Verwenden von Blob-Speicher aus Java

In diesem Leitfaden wird die Durchführung gängiger Szenarien mit dem Microsoft Azure-Blobspeicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][]. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Hinweis: Ein SDK steht für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][]. 

## <a name="Contents"> </a>Inhaltsverzeichnis

* [Was ist Blob-Speicher?](#what-is)
* [Konzepte](#Concepts)
* [Erstellen eines Azure-Speicherkontos](#CreateAccount)
* [Erstellen einer Java-Anwendung](#CreateApplication)
* [Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher](#ConfigureStorage)
* [Einrichten einer Azure-Speicherverbindungszeichenfolge](#ConnectionString)
* [Gewusst wie: Erstellen eines Containers](#CreateContainer)
* [Gewusst wie: Hochladen eines Blobs in einen Container](#UploadBlob)
* [Gewusst wie: Auflisten der Blobs in einem Container](#ListBlobs)
* [Gewusst wie: Herunterladen eines Blobs](#DownloadBlob)
* [Gewusst wie: Löschen eines Blobs](#DeleteBlob)
* [Gewusst wie: Löschen eines Blob-Containers](#DeleteContainer)
* [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Erstellen eines Azure-Speicherkontos</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Erstellen einer Java-Anwendung

In diesem Leitfaden werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird.

Dafür müssen Sie das Java Development Kit (JDK) installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen. Sobald Sie dies erledigt haben, müssen Sie sicherstellen, dass Ihre Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][] auf GitHub aufgelistet sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Sobald Sie diese Aufgaben abgeschlossen haben, können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## <a name="ConfigureStorage"> </a>Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher

Fügen Sie die folgenden "import"-Anweisungen am Anfang der Java-Datei hinzu, um die Stellen anzugeben, an denen Azure-Speicher-APIs auf Blobs zugreifen sollen:

    // Die folgenden Importe zur Verwendung von Blob-APIs einschließen
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge
zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName-* und *AccountKey-* Werte eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

    // Verbindungszeichenfolge mit Ihren Werten definieren
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Microsoft Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="CreateContainer"> </a>Gewusst wie: Erstellen eines Containers

Ein CloudBlobClient-Objekt ermöglicht den Abruf von Referenzobjekten für Container und Blobs. Mit dem folgenden Code wird ein **CloudBlobClient**-Objekt erstellt. (Hinweis: Es gibt zusätzliche Möglichkeiten zum Erstellen von **CloudStorageAccount**-Objekten. Weitere Informationen finden Sie unter **CloudStorageAccount** im Thema zur [Azure Storage-Client-SDK-Referenz].)

Alle Blobs befinden sich in einem Container. Mithilfe des **CloudBlobClient**-Objekts können Sie einen Verweis auf den Container abrufen, den Sie verwenden möchten. Falls der Container nicht vorhanden ist, können Sie ihn mit der **createIfNotExists**-Methode erstellen. Andernfalls gibt die Methode den vorhandenen Container zurück. Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen.

	try
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Blobclient erstellen.
	   CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	   // Verweis auf einen Container abrufen.
	   // Der Containername muss aus Kleinbuchstaben bestehen
	   CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	   // Container erstellen, wenn er noch nicht vorhanden ist.
    	container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

###Optional: Konfigurieren eines Containers für den öffentlichen Zugriff ###

Die Berechtigungen eines Containers werden standardmäßig für den privaten Zugriff konfiguriert. Sie können die Berechtigungen eines Containers jedoch einfach konfigurieren, um allen Benutzern im Internet einen öffentlichen Schreibzugriff zu gewähren:

    // Berechtigungsobjekt erstellen.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Öffentlichen Zugriff in Berechtigungsobjekt einschließen.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Berechtigungen für den Container festlegen.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a>Gewusst wie: Hochladen eines Blobs in einen Container

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blob-Verweises, um eine Datei in ein Blob hochzuladen. Sobald Sie über einen Blob-Verweis verfügen, können Sie jeden Datenstrom hochladen, indem Sie die upload-Methode für den Blob-Verweis aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Dieses Codebeispiel zeigt diesen Vorgang. Dabei wird davon ausgegangen, dass der Container bereits erstellt wurde.

	try
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Blobclient erstellen.
    	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	   // Verweis auf einen zuvor erstellten Container abrufen.
    	CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
			
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

    	// Blob "myimage.jpg" erstellen oder mit Inhalt aus einer lokalen Datei überschreiben.
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
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Blobclient erstellen.
    	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    	// Verweis auf einen zuvor erstellten Container abrufen.
    	CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
			
    	// Schleife durch Blobs im Container ausführen und URI für die einzelnen Blobs ausgeben.
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

Optional können Sie Parameter an die **listBlobs**-Methode übergeben, indem Sie
den **useFlatBlobListing**-Parameter auf "true" festlegen. Dies führt dazu,
dass jedes Blob unabhängig vom Verzeichnis zurückgegeben wird. Weitere
Informationen finden Sie unter **CloudBlobContainer.listBlobs** in der [Azure Storage-Client-SDK-Referenz].

## <a name="DownloadBlob"> </a>Gewusst wie: Herunterladen eines Blobs

Führen Sie zum Herunterladen von Blobs die gleichen Schritte wie zum Hochladen eines Blobs aus, um einen Blob-Verweis abzurufen. Im Uploadbeispiel haben Sie die upload-Methode für das Blob-Objekt aufgerufen. Im folgenden Beispiel rufen Sie die download-Methode auf, um den Blob-Inhalt an ein Datenstromobjekt wie z. B. **FileOutputStream** zu übertragen, das Sie verwenden können, um das Blob in einer lokalen Datei zu speichern.

    try
    {
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Blobclient erstellen.
	   CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	   // Verweis auf einen zuvor erstellten Container abrufen.
	   CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
			
	   // Loop through each blob item in the container.
	   for (ListBlobItem blobItem : container.listBlobs()) {
	       // Wenn das Element ein Blob ist, kein virtuelles Verzeichnis.
	       if (blobItem instanceof CloudBlob) {
	           // Element herunterladen und in gleichnamiger Datei speichern.
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
	   // Speicherkonto aus Verbindungszeichenfolge abrufen.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Blobclient erstellen.
	   CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	   // Verweis auf einen zuvor erstellten Container abrufen.
	   CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
			
	   // Verweis auf ein Blob mit dem Namen "myimage.jpg" abrufen.
	   CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

	   // Blob löschen.
	   blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a>Gewusst wie: Löschen eines Blob-Containers

Zum Löschen eines Blob-Containers rufen Sie einen Blob-Containerverweis ab und
rufen **deleteIfExists** auf.

    try
    {
	   // Speicherkonto aus Verbindungszeichenfolge abrufen.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Blobclient erstellen.
	   CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	   // Verweis auf einen zuvor erstellten Container abrufen.
	   CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
			
	   // Blob-Container löschen.
	   container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- [Azure Storage-SDK für Java]
- [Azure Storage-Client-SDK-Referenz]
- [Azure Storage-REST-API]
- [Azure Storage-Teamblog]

[Azure SDK für Java]: http://www.windowsazure.com/de-de/develop/java/
[Azure Storage SDK für Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK für Android]: https://github.com/azure/azure-storage-android
[Azure Storage-Client-SDK-Referenz]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage-REST-API]: http://msdn.microsoft.com/de-de/library/azure/gg433040.aspx
[Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
