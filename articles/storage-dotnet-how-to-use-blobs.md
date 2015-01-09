<properties urlDisplayName="Blob Service" pageTitle="Verwenden des Blob-Speichers mit .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Verwenden des Blob-Speichers mit .NET

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C\# geschrieben und
greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die aufgeführten Szenarien umfassen das
**Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere
Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte][].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure .NET Storage Client Library 2.x und höher. Die empfohlene Version ist Storage Client Library 4.x, die über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) oder als Teil des [Azure SDK für .NET](/de-de/downloads/)verfügbar ist. Weitere Informationen zum Abrufen der Storage Client Library finden Sie unter [Vorgehensweise: Programmgesteuerter Zugriff auf Blob-Speicher][] weiter unten.

##Inhaltsverzeichnis

-   [Was ist Blob-Speicher?][]
-   [Konzepte][]
-   [Erstellen eines Azure-Speicherkontos][]
-   [Einrichten einer Speicherverbindungszeichenfolge][]
-   [Vorgehensweise: Programmgesteuerter Zugriff auf Blob-Speicher][]
-   [Vorgehensweise: Erstellen eines Containers][]
-   [Vorgehensweise: Hochladen eines Blob in einen Container][]
-   [Vorgehensweise: Auflisten der Blobs in einem Container][]
-   [Vorgehensweise: Herunterladen von Blobs][]
-   [Vorgehensweise: Löschen von Blobs][]
-   [Vorgehensweise: Asynchrones Auflisten von Blobs auf Seiten][]
-   [Nächste Schritte][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Erstellen eines Azure-Speicherkontos
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>Einrichten einer Speicherverbindungszeichenfolge

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Vorgehensweise: Programmgesteuerter Zugriff auf den Blobspeicher

###Abrufen der Assembly
Es wird empfohlen, die Microsoft.WindowsAzure.Storage.dll-Assembly mit NuGet abzurufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.  Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure Storage-Paket und die Abhängigkeiten zu installieren.

Microsoft.WindowsAzure.Storage.dll ist auch im Azure SDK für .NET enthalten, das aus dem <a href="http://www.windowsazure.com/de-de/develop/net/#">.NET Developer Center</a>heruntergeladen werden kann. Die Assembly wird im Verzeichnis "%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\" installiert.

###Namespace-Deklarationen
Fügen Sie zu Beginn aller C\#-Dateien,
in denen Sie programmgesteuert auf Azure-Speicher zugreifen möchten, die folgenden Namespace-Deklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Stellen Sie sicher, dass auf die Microsoft.WindowsAzure.Storage.dll-Assembly verwiesen wird.

###Abrufen der Verbindungszeichenfolge
Sie können den **CloudStorageAccount**-Typ verwenden, um Ihre 
Speicherkontodaten abzubilden. Falls Sie eine 
Azure-Projektvorlage verwenden und/oder über einen Verweis auf 
Microsoft.WindowsAzure.CloudConfigurationManager verfügen, 
können  Sie Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager**
aus
der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und sich die Verbindungszeichenfolge in der web.config- oder der app.config-Datei befindet, wie oben gezeigt, können Sie **ConfigurationManager** verwenden, um die Verbindungszeichenfolge abzurufen.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Der Typ **CloudBlobClient** ermöglicht das Abrufen von Objekten, die im
Blob-Speicherdienst gespeicherte Container und Blobs darstellen. Der
folgende Code erstellt ein **CloudBlobClient**-Objekt mithilfe des zuvor abgerufenen
Speicherkonto-Objekts:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib-Abhängigkeiten
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst.  Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen.  Die spezifischen ODataLib-Pakete sind [OData], [Edm] und [Spatial].

## <a name="create-container"> </a>Vorgehensweise: Erstellen eines Containers

Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Dieses Beispiel zeigt, wie Sie einen Container erstellen, falls er nicht bereits vorhanden ist.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes öffentlich machen:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

## <a name="upload-blob"> </a>Vorgehensweise: Hochladen eines Blobs in einen Container

Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs.  In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen BLOB-Verweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die Methode **UploadFromStream** aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

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

##<a name="list-blob"> </a>Vorgehensweise: Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach können Sie mit der Methode **ListBlobs** des Containers die Blobs und/oder darin befindlichen Verzeichnisse abrufen. Um auf die umfangreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zuzugreifen, müssen Sie es zu einem **CloudBlockBlob**-, **CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln.  Wenn der Typ unbekannt ist, können Sie eine Typenüberprüfung durchführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll.  Der folgenden Code zeigt, wie Sie den URI jedes Elements im Computer 
"photos" abrufen und ausgeben:

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

Wie oben gezeigt, wird beim Blobdienst ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren. Beachten Sie beispielsweise den folgenden Satz von Block-Blobs in einem Container namens "photos":

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Wenn Sie **ListBlobs** im Container "photos" aufrufen (wie im obigen Beispiel), enthält die zurückgegebene Auflistung **CloudBlobDirectory**- und **CloudBlockBlob**-Objekte,
die die auf der obersten Ebene enthaltenen Verzeichnisse und Blobs darstellen. Die Ausgabe würde folgendermaßen aussehen:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623:: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Optional können Sie für den Parameter **UseFlatBlobListing** der Methode **ListBlobs** den Wert **true** festlegen. Daraufhin würde jeder Blob unabhängig vom jeweiligen Verzeichnis als **CloudBlockBlob** zurückgegeben werden.  Hier ist der Aufruf an **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

Das Ergebnis sähe folgendermaßen aus:

	Block blob of length 4:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4:: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048:: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388:: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751:: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623:: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Weitere Informationen finden Sie unter [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>Vorgehensweise: Herunterladen von Blobs

Zum Herunterladen von Blobs rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die Methode **DownloadToStream** auf. Im folgenden
Beispiel wie die Methode **DownloadToStream** verwendet, um den Blob-Inhalt
auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

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

Sie können auch die Methode **DownloadToStream** verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

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

##<a name="delete-blobs"> </a>Vorgehensweise: Löschen von Blobs

Zum Löschen eines Blobs rufen Sie einen Blobverweis ab und rufen dann die
**Delete**-Methode auf.

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


##<a name="list-blobs-async"> </a>Vorgehensweise: Asynchrones Auflisten von Blobs auf Seiten

Wenn Sie eine große Anzahl von Blobs auflisten oder die Anzahl der Ergebnisse steuern möchten, die in einem Auflistungsvorgang zurückgegeben werden, können Sie Blobs auf Ergebnisseiten auflisten. In diesem Beispiel wird gezeigt, wie Sie Ergebnisse auf Seiten asynchron zurückgeben, sodass die Ausführung nicht durch einen großen Ergebnissatz blockiert wird.

In diesem Beispiel wird eine einfache Blob-Auflistung gezeigt, aber Sie können auch eine hierarchische Auflistung verwenden, indem Sie den Parameter "useFlatBlobListing" der Methode **ListBlobsSegmentedAsync** auf "false" festlegen.

Da die Beispielmethode eine asynchrone Methode aufruft, muss sie mit dem Schlüsselwort "async" eingeleitet werden und ein **Task**-Objekt zurückgeben. Das für die Methode **ListBlobsSegmentedAsync** angegebene Schlüsselwort "await" hält die Ausführung der Beispielmethode an, bis die Auflistung abgeschlossen ist.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.
<ul>
<li>Vollständige Informationen zu verfügbaren APIs finden Sie in der Blobdienst-Referenzdokumentation:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Speicherclientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/de-de/library/windowsazure/dd179355">REST-API-Referenz</a></li>
  </ul>
</li>
<li>Unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx">Speichern und Zugreifen auf Daten in Azure</a>finden Sie weitere Informationen zu den erweiterten Aufgaben, die Sie mit Azure Storage durchführen können.</li>
<li>Unter <a href="/de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Erste Schritte mit dem Azure WebJobs SDK</a>finden Sie weitere Informationen zum Arbeiten mit Azure Storage in Back-End-Prozessen für Azure-Webseiten.</li>
<li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden von <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-tables/">Table Storage</a> zum Speichern strukturierter Daten</li>
    <li>Verwenden von <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-queues/">Warteschlangenspeicher</a> zum Speichern unstrukturierter Daten</li>
    <li>Verwenden von <a href="/de-de/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten</li>
  </ul>
</li>
</ul>

  [Nächste Schritte]: #next-steps
  [Was ist Blob-Speicher?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Einrichten einer Speicherverbindungszeichenfolge]: #setup-connection-string
  [Vorgehensweise: Programmgesteuerter Zugriff auf Blob-Speicher]: #configure-access
  [Vorgehensweise: Erstellen eines Containers]: #create-container
  [Vorgehensweise: Hochladen eines Blob in einen Container]: #upload-blob
  [Vorgehensweise: Auflisten der Blobs in einem Container]: #list-blob
  [Vorgehensweise: Herunterladen von Blobs]: #download-blobs
  [Vorgehensweise: Löschen von Blobs]: #delete-blobs
  [Vorgehensweise: Asynchrones Auflisten von Blobs auf Seiten]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Speichern von und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx
  [Referenz zur .NET-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST-API-Referenz]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
\n<!--HONumber=35.1--> 
