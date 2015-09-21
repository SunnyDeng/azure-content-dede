<properties 
	pageTitle="Kopieren eines vorhandenen Blobs in ein Media Services-Medienobjekt" 
	description="In diesem Thema wird erläutert, wie ein vorhandener Blob in ein Media Services-Medienobjekt kopiert wird." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Kopieren eines vorhandenen Blobs in ein Media Services-Medienobjekt

In diesem Thema wird erläutert, wie Blobs aus einem Speicherkonto in ein neues Microsoft Azure Media Services-Medienobjekt kopiert werden.

Ihre Blobs können sich in einem Speicherkonto befinden, das einem oder das keinem Media Services-Konto zugeordnet ist. In diesem Thema wird beschrieben, wie Blobs aus einem Speicherkonto in ein Media Services-Medienobjekt kopiert werden. Beachten Sie, dass Sie Blobs auch zwischen verschiedenen Rechenzentren kopieren können. Allerdings fallen dabei möglicherweise Kosten an. Die Preisübersicht und weitere Informationen finden Sie unter [Azure-Preise](http://azure.microsoft.com/pricing/#header-11).

>[AZURE.NOTE]Sie sollten nicht versuchen, den Inhalt von BLOB-Containern, die mit Media Services generiert wurden, ohne die Verwendung von Media Service-APIs zu ändern.

##Voraussetzungen

- Zwei Media Services-Konten in einem neuen oder vorhandenen Azure-Abonnement. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos](media-services-create-account.md).
- Betriebssystem: Windows 7, Windows 2008 R2 oder Windows 8.
- .NET Framework 4.5.
- Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express).

##Einrichten des Projekts

In diesem Abschnitt werden Sie ein C#-Konsolenanwendungsprojekt erstellen und einrichten.

1. Verwenden Sie Visual Studio 2012 oder Visual Studio 2010 SP1, um eine neue Projektmappe zu erstellen, die das C#-Konsolenanwendungsprojekt enthält. 
2. Geben Sie "CopyExistingBlobsIntoAsset" als Namen ein, und klicken Sie dann auf "OK".
1. Verwenden Sie Nuget, um Verweise auf die mit Media Services verknüpften DLLs hinzuzufügen. Klicken Sie im Hauptmenü in Visual Studio auf EXTRAS -> Bibliothekspaket-Manager -> Paket-Manager-Konsole. Geben Sie im Konsolenfenster "Install-Package windowsazure.mediaservices" ein, und drücken Sie die Eingabetaste.
1. Fügen Sie andere Verweise hinzu, die für dieses Projekt erforderlich sind: System.Configuration.
1. Ersetzen Sie die using-Anweisungen, die der Datei "Programs.cs" automatisch hinzugefügt wurden, durch die folgenden Anweisungen:
		
		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure;
		using System.Web;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Microsoft.WindowsAzure.Storage.Auth;

1. Fügen Sie der CONFIG-Datei den appSettings-Abschnitt hinzu, und ändern Sie die Werte entsprechend dem Schlüssel und Namen Ihres Media Services-Medienobjekts und Speichers.

		<appSettings>
		  <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
		  <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
		  <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
		  <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
		  <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
		  <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
		</appSettings>


##Kopieren von Blobs aus einem Speicherkonto in ein Media Services-Medienobjekt

Mit dem Codebeispiel unten werden die folgenden Aufgaben ausgeführt:

1. Erstellen der CloudMediaContext-Instanz. 
1. Erstellen der CloudStorageAccount-Instanzen: sourceStorageAccount und destinationStorageAccount.
1. Hochladen von Smooth Streaming-Dateien aus einem lokalen Verzeichnis in einen Blob-Container, der sich in sourceStorageAccount befindet.
1. Erstellen eines neuen Medienobjekts. Der Blob-Container, der für dieses Medienobjekt erstellt wird, befindet sich in destinationStorageAccount.
1. Kopieren der angegebenen Blobs mithilfe des Azure Storage-SDK in den mit dem Medienobjekt verknüpften Container.

>[AZURE.NOTE]Der Kopiervorgang löst keine Ausnahme aus, wenn der Locator abgelaufen ist.

1. Festlegen der ISM-Datei als primäre Datei.
1. Erstellen der Smooth Streaming-URL für den mit dem Medienobjekt verknüpften OnDemandOrigin-Locator. 
		
		class Program
		{
		    // Read values from the App.config file. 
		    static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
		    static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		    static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
		    static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
		    static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
		    static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];
		
		    private static MediaServicesCredentials _cachedCredentials = null;
		    private static CloudMediaContext _context = null;
		
		    private static CloudStorageAccount _sourceStorageAccount = null;
		    private static CloudStorageAccount _destinationStorageAccount = null;
		
		    static void Main(string[] args)
		    {
		        _cachedCredentials = new MediaServicesCredentials(
		                        _accountName,
		                        _accountKey);
		        // Use the cached credentials to create CloudMediaContext.
		        _context = new CloudMediaContext(_cachedCredentials);
		
		        // In this example the storage account from which we copy blobs is not 
		        // associated with the Media Services account into which we copy blobs.
		        // But the same code will work for coping blobs from a storage account that is 
		        // associated with the Media Services account.
		        //
		        // Get a reference to a storage account that is not associated with a Media Services account
		        // (an external account).  
		        StorageCredentials externalStorageCredentials =
		            new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
		        _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);
		
		        //Get a reference to the storage account that is associated with a Media Services account. 
		        StorageCredentials mediaServicesStorageCredentials =
		            new StorageCredentials(_storageAccountName, _storageAccountKey);
		        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);
		
		        // Upload Smooth Streaming files into a storage account.
		        string localMediaDir = @"C:\supportFiles\streamingfiles";
		        CloudBlobContainer blobContainer =
		            UploadContentToStorageAccount(localMediaDir);
		
		        // Create a new asset and copy the smooth streaming files into 
		        // the container that is associated with the asset.
		        IAsset asset = CreateAssetFromExistingBlobs(blobContainer);
		
		        // Get the streaming URL for the smooth streaming files 
		        // that were copied into the asset.   
		        string urlForClientStreaming = CreateStreamingLocator(asset);
		        Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);
		
		        Console.ReadLine();
		    }
		
		    /// <summary>
		    /// Uploads content from a local directory into the specified storage account.
		    /// In this example the storage account is not associated with the Media Services account.
		    /// </summary>
		    /// <param name="localPath">The path from which to upload the files.</param>
		    /// <returns>The container that contains the uploaded files.</returns>
		    static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
		    {
		        CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();
		        CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");
		
		        externalMediaBlobContainer.CreateIfNotExists();
		
		        // Upload files to the blob container.  
		        DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
		        foreach (var file in uploadDirectory.EnumerateFiles())
		        {
		            CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);
		
		            blob.UploadFromFile(file.FullName, FileMode.Open);
		        }
		
		        return externalMediaBlobContainer;
		    }
		
		    /// <summary>
		    /// Creates a new asset and copies blobs from the specifed storage account.
		    /// </summary>
		    /// <param name="mediaBlobContainer">The specified blob container.</param>
		    /// <returns>The new asset.</returns>
		    static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
		    {
		        // Create a new asset. 
		        IAsset asset = _context.Assets.Create("Burrito_" + Guid.NewGuid(), AssetCreationOptions.None);
		
		        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy", TimeSpan.FromHours(24), AccessPermissions.Write);
		        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
		
		        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
		
		        // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
		        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
		
		        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);
		
		        if (assetContainer.CreateIfNotExists())
		        {
		            assetContainer.SetPermissions(new BlobContainerPermissions
		            {
		                PublicAccess = BlobContainerPublicAccessType.Blob
		            });
		        }
		
		        var blobList = mediaBlobContainer.ListBlobs();
		        foreach (var sourceBlob in blobList)
		        {
		            var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
		            CopyBlob(sourceBlob as ICloudBlob, assetContainer);
		            assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
		            assetFile.Update();
		        }
		
		        destinationLocator.Delete();
		        writePolicy.Delete();
		
		        // Since we copied a set of Smooth Streaming files, 
		        // set the .ism file to be the primary file. 
		        SetISMFileAsPrimary(asset);
		
		        return asset;
		    }
		
		    /// <summary>
		    /// Creates the OnDemandOrigin locator in order to get the streaming URL.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    /// <returns>The streaming URL.</returns>
		    static public string CreateStreamingLocator(IAsset asset)
		    {
		        var ismAssetFile = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();
		
		        // Create a 30-day readonly access policy. 
		        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		            TimeSpan.FromDays(30),
		            AccessPermissions.Read);
		
		        // Create a locator to the streaming content on an origin. 
		        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		            policy,
		            DateTime.UtcNow.AddMinutes(-5));
		
		        return originLocator.Path + ismAssetFile.Name + "/manifest";
		    }
		
		    /// <summary>
		    /// Copies the specified blob into the specified container.
		    /// </summary>
		    /// <param name="sourceBlob">The source container.</param>
		    /// <param name="destinationContainer">The destination container.</param>
		    static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
		    {
		        var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
		        {
		            Permissions = SharedAccessBlobPermissions.Read,
		            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
		        });
		
		        ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);
		
		        if (destinationBlob.Exists())
		        {
		            Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
		        }
		        else
		        {
		            try
		            {
		                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
		                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine(string.Format("Error copying blob '{0}': {1}", sourceBlob.Name, ex.Message));
		            }
		        }
		    }
		
		    /// <summary>
		    /// Sets a file with the .ism extension as a primary file.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    static private void SetISMFileAsPrimary(IAsset asset)
		    {
		        var ismAssetFiles = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
		
		        if (ismAssetFiles.Count() != 1)
		            throw new ArgumentException("The asset should have only one, .ism file");
		
		        ismAssetFiles.First().IsPrimary = true;
		        ismAssetFiles.First().Update();
		    }
		}
 

##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

<!---HONumber=Sept15_HO2-->