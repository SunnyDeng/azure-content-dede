<properties urlDisplayName="Blob Service" pageTitle="Verwenden des Blob-Speichers mit .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Verwenden des Blob-Speichers mit .NET

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C\# geschrieben und
greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die aufgeführten Szenarien umfassen das
**Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere
Informationen zu Blobs finden Sie im Abschnitt [nächste Schritte][nächste Schritte].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure Speicher-Clientbibliothek 2.x und neuere Versionen. Die empfohlene Version für die Speicher-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) oder als Teil des [Azure SDK für .NET](/de-de/downloads/) erhältlich. Siehe [Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher][Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher] weiter unten für weitere Informationen zum Download der Speicher-Clientbibliothek.

##Inhaltsverzeichnis

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
-   [Gewusst wie: Asynchrones Auflisten der Blobs auf Seiten][Gewusst wie: Asynchrones Auflisten der Blobs auf Seiten]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">Erstellen eines Azure-Speicherkontos</span>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">Einrichten einer Speicherverbindungszeichenfolge</span>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher</span>

###Abrufen der Assembly
Es wird empfohlen, die Assembly `Microsoft.WindowsAzure.Storage.dll` mit NuGet abzurufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Manage NuGet Packages** aus.  Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist auch im Azure SDK für .NET 2.0 enthalten, das aus dem <a href="http://www.windowsazure.com/de-de/develop/net/#">.NET Developer Center</a> heruntergeladen werden kann. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` installiert.

###Namespace-Deklarationen
Fügen Sie zu Beginn aller C\#-Dateien
, in denen Sie programmgesteuert auf Azure-Speicher zugreifen möchten, die folgenden Namespace-Deklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

###Abrufen der Verbindungszeichenfolge
Sie können die **CloudStorageAccount** verwenden, um Ihre 
Speicherkontodaten abzubilden. Falls Sie eine 
Azure-Projektvorlage verwenden und/oder über einen Verweis auf 
Microsoft.WindowsAzure.CloudConfigurationManager verfügen, 
können Sie Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager**
aus
der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und die Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Der Typ **CloudBlobClient** ermöglicht den Abruf von Objekten, die im 
Blob-Speicherdienst gespeicherte Container und Blobs darstellen. Der
folgende Code erstellt ein **CloudBlobClient**-Objekt mithilfe des zuvor abgerufenen
Speicherkonto-Objekts:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###ODataLib-Abhängigkeiten
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst.  Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen.  Die spezifischen ODataLib-Pakete sind [OData], [Edm] und [Spatial].

## <a name="create-container"> </a><span  class="short-header">Gewusst wie: Erstellen eines Containers</span>

Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Dieses Beispiel zeigt, wie Sie einen Container erstellen, falls er nicht bereits vorhanden ist.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen Container abrufen. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Container erstellen, wenn er noch nicht vorhanden ist.
    container.CreateIfNotExists();

Standardmäßig ist der neue Container privat, und Sie müssen Ihren
Speicherzugriffsschlüssel angeben, um Blobs aus diesem
Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen,
können Sie den Container mithilfe des folgenden
Codes erstellen und öffentlich machen:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch
nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

## <a name="upload-blob"> </a><span  class="short-header">Gewusst wie: Hochladen eines Blobs in einen Container</span>

Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs.  In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Um eine Datei in einen Blockblob hochzuladen, müssen Sie zunächst einen Containerverweis
und anschließend über diesen Verweise einen Blockblob-Verweis abrufen. Mit dem Blobverweis können Sie einen beliebigen
Datenstream hochladen, indem Sie die **UploadFromStream**-Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist,
oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "myblob" abrufen.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Blob "myblob" erstellen oder durch Inhalt aus einer lokalen Datei überschreiben.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">Gewusst wie: Auflisten der Blobs in einem Container</span>

Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach
können Sie mit der**ListBlobs**-Methode des Containers die Blobs und/oder darin befindlichen Verzeichnisse
abrufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein 
zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, 
**CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln.  Falls der Typ unbekannt ist, können Sie den Zieltyp mit 
einer Typprüfung ermitteln.  Im folgenden Code 
wird gezeigt, wie der URI der einzelnen Elemente im 
 im Container `photos` abgerufen und ausgegeben wird:

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Verweis auf einen zuvor erstellten Container abrufen.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Schleife über Elemente im Container ausführen und Länge und URI ausgeben.
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

Wie oben gezeigt, wird beim Blobdienst ebenfalls das Konzept von Verzeichnissen in Containern
angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur
organisieren. Betrachten Sie z. B. den folgenden Satz von Blockblobs in einem Container
mit dem Namen `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Wenn Sie **ListBlobs** für den Container "photos" aufrufen (wie im Beispiel oben), enthält die zurückgegebene Sammlung
**CloudBlobDirectory**- und **CloudBlockBlob**-Objekte,
die die auf der obersten Ebene enthaltenen Verzeichnisse und Blobs darstellen. Die Ausgabe würde folgendermaßen aussehen:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Optional können Sie für den **UseFlatBlobListing**-Parameter der **ListBlobs**-Methode die Option 
**wahr** einstellen. Daraufhin würde jeder Blob unabhängig vom jeweiligen Verzeichnis als **CloudBlockBlob**
zurückgegeben.  Der Aufruf für **ListBlobs**sähe folgendermaßen aus:

    // Schleife über Elemente im Container ausführen und Länge und URI ausgeben.
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

## <a name="download-blobs"> </a><span  class="short-header">Gewusst wie: Herunterladen von Blobs</span>

Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die **DownloadToStream**-Methode auf. Im folgenden
 Beispiel wird die**DownloadToStream**-Methode verwendet, um den Blobinhalt
auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "photo1.jpg" abrufen.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Blobinhalt in einer Datei speichern.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Sie können auch die **DownloadToStream**-Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Verweis auf ein Blob mit dem Namen "myblob.txt" abrufen.
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">Gewusst wie: Löschen von Blobs</span>

Zum Löschen eines Blobs rufen Sie einen Blobverweis ab und rufen dann die
**Löschen**-Methode auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "myblob.txt" abrufen.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Blob löschen.
    blockBlob.Delete(); 


##<a name="list-blobs-async"> </a><span  class="short-header">Gewusst wie: Asynchrones Auflisten der Blobs auf Seiten</span> 

Wenn Sie eine große Anzahl von Blobs auflisten oder die Anzahl der Ergebnisse steuern möchten, die in einem Auflistungsvorgang zurückgegeben werden, können Sie Blobs auf Ergebnisseiten auflisten. In diesem Beispiel wird gezeigt, wie Sie Ergebnisse auf Seiten asynchron zurückgeben, sodass die Ausführung nicht durch einen großen Ergebnissatz blockiert wird.

Dieses Beispiel verwendet eine einfache Blob-Auflistung, aber Sie können auch eine hierarchische Auflistung verwenden, indem Sie den Parameter `useFlatBlobListing` der **ListBlobsSegmentedAsync**-Methode auf`false` einstellen.

Da die Beispielmethode eine asynchrone Methode aufruft, muss sie mit dem Schlüsselwort `async` eingeleitet werden und ein **Task**-Objekt zurückgeben. Das Schlüsselwort "await" für die Methode **ListBlobsSegmentedAsync** hält die Ausführung der Beispielmethode an, bis die Auflistung abgeschlossen ist.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Blobclient erstellen.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Verweis auf einen zuvor erstellten Container abrufen.
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

## <a name="next-steps"></a><span  class="short-header">Nächste Schritte</span>

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links
, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.
<ul>
<li>Vollständige Informationen zu verfügbaren APIs finden Sie in der Blobdienst-Referenzdokumentation:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Speicherclientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/de-de/library/windowsazure/dd179355">REST-API-Referenz</a></li>
  </ul>
</li>
<li>Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx">Speichern und Zugreifen auf Daten in Azure</a>.</li>
<li>Informationen zum Arbeiten mit dem Azure-Speicher in Back-End-Prozessen für Azure-Webseiten finden Sie unter <a href="/de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Erste Schritte mit dem Azure WebJobs SDK</a>.</li>
<li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden Sie <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-tables/">Tabellenspeicher</a> zum Speichern strukturierter Daten.</li>
    <li>Verwenden Sie den <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-queues/">Warteschlangenspeicher</a> zum Speichern unstrukturierter Daten.</li>
    <li>Verwenden Sie eine <a href="/de-de/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten.</li>
  </ul>
</li>
</ul>

  [Nächste Schritte]: #next-steps
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Gewusst wie: Programmgesteuerter Zugriff auf den Blobspeicher]: #configure-access
  [Gewusst wie: Erstellen eines Containers]: #create-container
  [Gewusst wie: Hochladen eines Blobs in einen Container]: #upload-blob
  [Gewusst wie: Auflisten der Blobs in einem Container]: #list-blob
  [Gewusst wie: Herunterladen von Blobs]: #download-blobs
  [Gewusst wie: Löschen von Blobs]: #delete-blobs
  [Gewusst wie: Asynchrones Auflisten der Blobs auf Seiten]: #list-blobs-async
  
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
