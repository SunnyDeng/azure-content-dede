<properties linkid="dev-net-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Verwenden des Blob-Speichers mit .NET

Diese Anleitung beschreibt die Ausführung gängiger Szenarien mithilfe des
Azure-Blobspeicherdiensts. Die Beispiele sind in C# geschrieben
und verwenden die Azure Speicher-Clientbibliothek für .NET. Die hier beschriebenen Szenarien umfassen das
**Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere
Informationen zu Blobs finden Sie im Abschnitt [nächste Schritte][nächste Schritte].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure Speicher-Clientbibliothek 2.x und neuere Versionen. Die empfohlene Version für die Speicher-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet][NuGet] oder als Teil des [Azure SDK für .NET][Azure SDK für .NET] erhältlich. Siehe [Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher][Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher] weiter unten für weitere Informationen zum Download der Speicher-Clientbibliothek.

## Inhaltsverzeichnis

-   [Was ist Blob-Speicher?][Was ist Blob-Speicher?]
-   [Konzepte][Konzepte]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Einrichten einer Speicherverbindungszeichenfolge][Einrichten einer Speicherverbindungszeichenfolge]
-   [Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher][Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher]
-   [Gewusst wie: Erstellen eines Containers][Gewusst wie: Erstellen eines Containers]
-   [Gewusst wie: Hochladen eines Blobs in einen Container][Gewusst wie: Hochladen eines Blobs in einen Container]
-   [Gewusst wie: Auflisten der Blobs in einem Container][Gewusst wie: Auflisten der Blobs in einem Container]
-   [Gewusst wie: Herunterladen von Blobs][Gewusst wie: Herunterladen von Blobs]
-   [Gewusst wie: Löschen von Blobs][Gewusst wie: Löschen von Blobs]
-   [Nächste Schritte][nächste Schritte]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span class="short-header">Erstellen eines Kontos</span>Erstellen eines Azure-Speicherkontos

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span class="short-header">Einrichten einer Verbindungszeichenfolge</span>Einrichten einer Speicherverbindungszeichenfolge

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Programmgesteuerter Zugriff</span>Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher

### Abrufen der Assembly

Sie können die Assembly `Microsoft.WindowsAzure.Storage.dll` mit NuGet abrufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Manage NuGet Packages** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist ebenfalls im Azure SDK für .NET enthalten, das Sie im [.NET Developer Center][.NET Developer Center] herunterladen können. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` installiert.

### Namespace-Deklarationen

Fügen Sie die folgenden Namespace-Deklarationen am Anfang aller C#-Dateien hinzu,
in denen Sie auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge

Sie können den Typ **CloudStorageAccount** verwenden, um Ihre
Speicherkontodaten abzubilden. Falls Sie eine
Azure-Projektvorlage verwenden und/oder einen Verweis auf den
Microsoft.WindowsAzure.CloudConfigurationManager haben, können Sie
den Typ **CloudConfigurationManager** verwenden,
um Ihre Speicherverbindungszeichenfolge und
Speicherkontodaten aus der Azure-Dienstkonfiguration abzurufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf Microsoft.WindowsAzure.CloudConfigurationManager"stellen und die Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen. Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Mit dem Typ **CloudBlobClient** können Sie Objekte abrufen, die
Container und Blobs darstellen, die im Blobspeicherdienst gespeichert sind. Der
folgende Code erstellt ein **CloudBlobClient**-Objekt mithilfe des zuvor abgerufenen
Speicherkonto-Objekts:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib-Abhängigkeiten

ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst. Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen. Die spezifischen ODataLib-Pakete sind [OData][OData], [Edm][Edm] und [Spatial][Spatial].

## <a name="create-container"> </a><span class="short-header">Erstellen eines Containers</span>Gewusst wie: Erstellen eines Containers

Alle Speicher-Blobs befinden sich in einem Container. Verwenden Sie das
**CloudBlobClient**-Objekt, um einen Verweis auf den gewünschten
Container abzurufen. Sie können den Container erstellen, wenn er nicht vorhanden ist:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Standardmäßig ist der neue Container privat, und Sie müssen Ihren
Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container
herunterzuladen. Wenn Sie die Dateien im Container für alle Benutzer
verfügbar machen möchten, können Sie den Container mit dem folgenden Code öffentlich
machen:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

Blobs in öffentlichen Containern sind für alle Internetbenutzer sichtbar,
können jedoch nur mit dem entsprechenden Zugriffsschlüssel verändert oder gelöscht werden.

## <a name="upload-blob"> </a><span class="short-header">Hochladen in einen Container</span>Gewusst wie: Hochladen eines Blobs in einen Container

Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Um eine Datei in einen Blockblob hochzuladen, müssen Sie zunächst einen Containerverweis
und anschließend über diesen Verweise einen Blockblob-Verweis abrufen. Mit dem Blobverweis können Sie einen beliebigen
Datenstream hochladen, indem Sie die **UploadFromStream**-Methode aufrufen. Diese Operation erstellt den Blob, falls dieser noch nicht existiert.
Andernfalls wird er überschrieben. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span class="short-header">Aufführen der Blobs in einem Container</span>Gewusst wie: Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Anschließend
können Sie die enthaltenen Blobs und/oder Verzeichnisse über die Methode **ListBlobs**
abrufen. Um die umfangreichen Eigenschaften und Menge eines
zurückgegebenen **IListBlobItem**-Objekts abzurufen, müssen Sie es zu einem **CloudBlockBlob-**,
**CloudPageBlob-** oder **CloudBlobDirectory-**-Objekt umwandeln. Falls der Typ unbekannt ist, können Sie den Zieltyp mit
einer Typprüfung ermitteln. Der folgende Code
zeigt, wie Sie die URIs der einzelnen Elemente
im `photos`-Container abrufen und ausgeben:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;
            
            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Wie Sie sehen, verwendet der Blob-Dienst auch Verzeichnisse innerhalb von
Containern. Auf diese Weise können Sie Ihre Blobs in einer ordnerähnlichen Struktur
organisieren. Betrachten Sie z. B. den folgenden Satz von Blockblobs mit dem
Namen `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Wenn Sie **ListBlobs** für den Container 'photos' aufrufen (wie im obigen Beispiel), dann enthält die zurückgegebene
Sammlung **CloudBlobDirectory-** und **CloudBlockBlob-**-Objekte
, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. Die Ausgabe würde folgendermaßen aussehen:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Optional können Sie für den **UseFlatBlobListing**-Parameter der **ListBlobs**-Methode die Option
**true** festlegen. Daraufhin würde jeder Blob unabhängig vom jeweiligen Verzeichnis als **CloudBlockBlob**
 zurückgegeben. Der Aufruf für **ListBlobs** sähe folgendermaßen aus:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

Das Ergebnis sähe folgendermaßen aus:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Weitere Informationen finden Sie unter [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs].

## <a name="download-blobs"> </a><span class="short-header">Herunterladen von Blobs</span>Gewusst wie: Herunterladen von Blobs

Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die **DownloadToStream**-Methode auf. Das folgende
Beispiel verwendet die **DownloadToStream**-Methode, um die Blobinhalte
in ein Streamobjekt zu übertragen, das Sie anschließend in einer lokalen Datei speichern können.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Sie können auch die **DownloadToStream**-Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span class="short-header">Löschen von Blobs</span>Gewusst wie: Löschen von Blobs

Um ein Blob zu löschen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die
**Delete**-Methode dafür auf.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Blobspeichers vertraut gemacht haben,
folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   In der Referenzdokumentation für den Blobspeicherdienst finden Sie alle Details zu verfügbaren APIs:
    -   [Referenz zur Speicherclientbibliothek für .NET][Referenz zur Speicherclientbibliothek für .NET]
    -   [REST-API-Referenz][REST-API-Referenz]

-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter [Speicher][Speicher].
-   Informationen zum Arbeiten mit dem Azure-Speicher in Back-End-Prozessen für Azure-Webseiten finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK][Erste Schritte mit dem Azure WebJobs SDK].
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Tabellenspeicher][Tabellenspeicher] zum Speichern strukturierter Daten.
    -   Verwenden Sie den [Warteschlangenspeicher][Warteschlangenspeicher] zum Speichern unstrukturierter Daten.
    -   Verwenden Sie eine [SQL-Datenbank][SQL-Datenbank] zum Speichern relationaler Daten.

</p>

  [nächste Schritte]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK für .NET]: /de-de/downloads/
  [Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher]: #configure-access
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Einrichten einer Speicherverbindungszeichenfolge]: #setup-connection-string
  [Gewusst wie: Erstellen eines Containers]: #create-container
  [Gewusst wie: Hochladen eines Blobs in einen Container]: #upload-blob
  [Gewusst wie: Auflisten der Blobs in einem Container]: #list-blob
  [Gewusst wie: Herunterladen von Blobs]: #download-blobs
  [Gewusst wie: Löschen von Blobs]: #delete-blobs
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [.NET Developer Center]: http://www.windowsazure.com/de-de/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Referenz zur Speicherclientbibliothek für .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST-API-Referenz]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179355
  [Speicher]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Erste Schritte mit dem Azure WebJobs SDK]: /de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Tabellenspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-tables/
  [Warteschlangenspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL-Datenbank]: /de-de/documentation/articles/sql-database-dotnet-how-to-use/
