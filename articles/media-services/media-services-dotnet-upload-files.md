<properties 
	pageTitle="Hochladen von Dateien in ein Media Services-Konto mit .NET" 
	description="Erfahren Sie, wie Sie Medieninhalte in Media Services nutzen können, indem Sie Medienobjekte erstellen und hochladen." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>


<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="juliako"/>




#Hochladen von Dateien in ein Media Services-Konto mit .NET

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch oder erfassen sie auf diese Weise. Die **Medienobjekt**-Entität kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die Metadaten zu diesen Dateien) enthalten. Nachdem die Dateien hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert.

Die Dateien im Medienobjekt heißen **Medienobjektdateien**. Die **AssetFile**-Instanz und die eigentliche Mediendatei sind zwei verschiedene Objekte. Die AssetFile-Instanz enthält Metadaten zur Mediendatei, während die Mediendatei die tatsächlichen Medieninhalte enthält.

Wenn Sie Medienobjekte erstellen, können Sie die folgenden Verschlüsselungsoptionen angeben.

- **None:** Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind. Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option. 
- **CommonEncryption:** Verwenden Sie diese Option, wenn Sie Inhalte hochladen, die bereits verschlüsselt wurden und durch Common Encryption oder PlayReady-DRM geschützt sind (z. B. mit PlayReady-DRM geschütztes Smooth Streaming).
- **EnvelopeEncrypted:** Verwenden Sie diese Option, wenn Sie mit AES verschlüsseltes HLS hochladen. Beachten Sie, dass die Dateien durch Transform Manager codiert und verschlüsselt sein müssen.
- **StorageEncrypted:** Verschlüsselt Ihre Inhalte lokal mithilfe von AES-256-Bit-Verschlüsselung und lädt sie dann in Azure Storage hoch, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Storage Encryption geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für Storage Encryption ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.

	Media Services bieten Speicherverschlüsselung auf der Festplatte und nicht für die Netzwerkkommunikation wie Digital Rights Manager (DRM).

	Wenn Ihr Medienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-dotnet-configure-asset-delivery-policy.md).

Wenn Sie für Ihr Medienobjekt eine Verschlüsselung unter Verwendung der **CommonEncrypted**-Option oder der **EnvelopeEncypted**-Option angeben, müssen Sie dem Medienobjekt einen **ContentKey** zuordnen. Weitere Informationen finden Sie unter [Erstellen eines ContentKey](media-services-dotnet-create-contentkey.md).

Wenn Sie für Ihr Medienobjekt eine Verschlüsselung mit einer **StorageEncrypted**-Option angeben, wird vom Media Services SDK für .NET ein **StorateEncrypted** **ContentKey** für Ihr Medienobjekt erstellt.

>[AZURE.NOTE]Media Services verwendet beim Erstellen von URLs für den Streaminginhalt den Wert der IAssetFile.Name-Eigenschaft (z. B. http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Aus diesem Grund ist die Prozentcodierung nicht zulässig. Der Wert der **Name**-Eigenschaft darf keines der folgenden [für die Prozentcodierung reservierten Zeichen enthalten](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !\*'();:@&=+$,/?%#". Darüber hinaus wird für die Dateinamenerweiterung nur ein Punkt (.) unterstützt.

In diesem Thema wird beschrieben, wie Sie mit dem Media Services .NET SDK und den Media Services .NET SDK-Erweiterungen Dateien in ein Media Services-Medienobjekt hochladen.

 
## Hochladen einer einzelnen Datei mit Media Services .NET SDK 

Im Beispielcode unten wird das .NET SDK für folgende Aufgaben verwendet:

- Erstellen eines leeren Medienobjekts.
- Erstellt eine AssetFile-Instanz, der wir das Medienobjekt zuweisen werden.
- Erstellt eine AccessPolicy-Instanz mit den Berechtigungen und der Zugriffsdauer auf das Medienobjekt.
- Erstellt eine Locator-Instanz, die Zugriff auf das Medienobjekt bietet.
- Hochladen einer einzelnen Datei in Media Services. 

		
		static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
		{
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
		}

##Hochladen mehrerer Dateien mit Media Services .NET SDK 

Der folgende Code zeigt, wie Sie ein Medienobjekt erstellen und mehrere Dateien hochladen können.

Im Code werden folgende Schritte ausgeführt:
	
- 	Erstellen eines leeren Medienobjekts mithilfe der im vorherigen Schritt definierten CreateEmptyAsset-Methode.
 	
- 	Erstellen einer **AccessPolicy**-Instanz mit den Berechtigungen und der Zugriffsdauer auf das Medienobjekt.
 	
- 	Erstellen einer **Locator**-Instanz, die Zugriff auf das Medienobjekt gewährt.
 	
- 	Erstellen einer **BlobTransferClient**-Instanz. Dieser Typ stellt einen Client dar, mit dem die Azure-Blobs bearbeitet werden. In diesem Beispiel verwenden wir den Client zum Überwachen des Uploadstatus.
 	
- 	Aufzählen der Dateien im angegebenen Verzeichnis und Erstellen einer **AssetFile**-Instanz für jede Datei.
 	
- 	Hochladen der Dateien in Media Services mithilfe der **UploadAsync**-Methode.
 	
>[AZURE.NOTE]Verwenden Sie die UploadAsync-Methode, um sicherzustellen, dass die Aufrufe nicht blockiert und die Dateien parallel hochgeladen werden.
 	
 	
	static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
	{
	    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();
	
	    var asset = CreateEmptyAsset(assetName, assetCreationOptions);
	
	    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                        AccessPermissions.Write | AccessPermissions.List);

	    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	    var blobTransferClient = new BlobTransferClient();
		blobTransferClient.NumberOfConcurrentTransfers = 20;
	    blobTransferClient.ParallelTransferThreadCount = 20;
	
	    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;
	
	    var filePaths = Directory.EnumerateFiles(folderPath);
	
	    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);
	
	    if (!filePaths.Any())
	    {
	        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
	    }
	
	    var uploadTasks = new List&lt;Task&gt;();
	    foreach (var filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        Console.WriteLine("Created assetFile {0}", assetFile.Name);
	                
	        // It is recommended to validate AccestFiles before upload. 
	        Console.WriteLine("Start uploading of {0}", assetFile.Name);
	        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
	    }
	
	    Task.WaitAll(uploadTasks.ToArray());
	    Console.WriteLine("Done uploading the files");
	
	    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;
	
	    locator.Delete();
	    accessPolicy.Delete();
	
	    return asset;
	}
	
	static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
	{
	    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
	    {
	        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
	    }
	}



Wenn Sie eine große Anzahl von Medienobjekten hochladen, sollten Sie Folgendes beachten.

- Erstellen Sie ein neues **CloudMediaContext**-Objekt pro Thread. Die **CloudMediaContext**-Klasse ist nicht threadsicher.
 
- Erhöhen Sie NumberOfConcurrentTransfers vom Standardwert 2 auf einen höheren Wert wie 5. Das Festlegen dieser Eigenschaft wirkt sich auf alle Instanzen von **CloudMediaContext** aus.
 
- Behalten Sie für ParallelTransferThreadCount den Standardwert 10 bei.
 
##<a id="ingest_in_bulk"></a>Sammelerfassung von Medienobjekten mithilfe von Media Services .NET SDK 

Das Hochladen großer Medienobjektdateien kann während der Erstellung von Medienobjekten einen Engpass verursachen. Beim Erfassen von Medienobjekten in einem Massenvorgang bzw. bei der "Sammelerfassung" wird die Erstellung von Medienobjekten vom Uploadvorgang entkoppelt. Zur Verwendung der Sammelerfassung erstellen Sie ein Manifest (IngestManifest), in dem das Medienobjekt und die zugeordneten Dateien beschrieben werden. Laden Sie dann die zugeordneten Dateien mithilfe der gewünschten Uploadmethode in den Blobcontainer des Manifests hoch. Microsoft Azure Media Services überwacht den dem Manifest zugeordneten Blobcontainer. Nachdem eine Datei in den Blobcontainer hochgeladen wurde, wird die Erstellung des Medienobjekts basierend auf dessen Konfiguration im Manifest (IngestManifestAsset) durch Microsoft Azure Media Services abgeschlossen.


Zum Neuerstellen des Manifests (IngestManifest) rufen Sie die Create-Methode auf, die von der IngestManifests-Auflistung im CloudMediaContext verfügbar gemacht wird. Durch diese Methode wird ein neues Manifest (IngestManifest) mit dem von Ihnen angegebenen Manifestnamen erstellt.

	IIngestManifest manifest = context.IngestManifests.Create(name);

Erstellen Sie die Medienobjekte, die dem Sammelmanifest (IngestManifest) zugeordnet werden. Konfigurieren Sie die gewünschten Verschlüsselungsoptionen für die Sammelerfassung des Medienobjekts.

	// Create the assets that will be associated with this bulk ingest manifest
	IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
	IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

IngestManifestAsset ordnet ein Medienobjekt einem Sammelmanifest (IngestManifest) für die Sammelerfassung zu. Außerdem werden die AssetFiles zugeordnet, aus denen sich das jeweilige Medienobjekt zusammensetzt. Zum Erstellen eines ingestmanifestasset-Objekts verwenden Sie die Create-Methode im Serverkontext.

Das folgende Beispiel zeigt das Hinzufügen zwei neuer IngestManifestAssets, durch die die beiden zuvor erstellten Medienobjekte dem Manifest für die Sammelerfassung zugeordnet werden. Jedes IngestManifestAsset-Objekt ordnet zusätzlich eine Reihe von Dateien zu, die während der Sammelerfassung für jedes Medienobjekt hochgeladen werden.

	string filename1 = _singleInputMp4Path;
	string filename2 = _primaryFilePath;
	string filename3 = _singleInputFilePath;
	
	IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
	IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
	
Sie können eine beliebige hochleistungsfähige Clientanwendung nutzen, die in der Lage ist, die Medienobjektdateien unter dem durch die **IIngestManifest.BlobStorageUriForUpload**-Eigenschaft von IngestManifest angegebenen URI des Blobspeichercontainers hochzuladen. Der Uploaddienst [Aspera On Demand für Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6) bietet beispielsweise hohe Geschwindigkeiten. Sie können auch Code zum Hochladen der Medienobjektdateien schreiben, wie im folgenden Codebeispiel veranschaulicht.
	
	static void UploadBlobFile(string destBlobURI, string filename)
	{
	    Task copytask = new Task(() =>
	    {
	        var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
	        CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
	        CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
	
	        string[] splitfilename = filename.Split('\\');
	        var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
	
	        using (var stream = System.IO.File.OpenRead(filename))
	            blob.UploadFromStream(stream);
	
	        lock (consoleWriteLock)
	        {
	            Console.WriteLine("Upload for {0} completed.", filename);
	        }
	    });
	
	    copytask.Start();
	}

Der Code zum Hochladen der Medienobjektdateien für das in diesem Thema verwendete Beispiel ist im folgenden Codebeispiel zu sehen.
	
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
	

Sie können den Status der Sammelerfassung für alle Medienobjekte bestimmen, die **IngestManifest** zugeordnet sind, indem Sie die Statistics-Eigenschaft von **IngestManifest** abrufen. Zum Aktualisieren von Statusinformationen müssen Sie jedes Mal, wenn Sie die Statistics-Eigenschaft abrufen, ein neues **CloudMediaContext**-Objekt verwenden.

Das folgende Beispiel zeigt, wie IngestManifest anhand der **ID** abgerufen wird.
	
	static void MonitorBulkManifest(string manifestID)
	{
	   bool bContinue = true;
	   while (bContinue)
	   {
	      CloudMediaContext context = GetContext();
	      IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
	
	      if (manifest != null)
	      {
	         lock(consoleWriteLock)
	         {
	            Console.WriteLine("\nWaiting on all file uploads.");
	            Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
	            Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
	            Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
	
	            if (manifest.Statistics.PendingFilesCount == 0)
	            {
	               Console.WriteLine("Completed\n");
	               bContinue = false;
	            }
	         }
	
	         if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
	            Thread.Sleep(60000);
	      }
	      else // Manifest is null
	         bContinue = false;
	   }
	}
	


##Hochladen von Dateien mit .NET SDK-Erweiterungen 

Das folgende Beispiel zeigt, wie eine einzelne Datei mit den.NET SDK-Erweiterungen hochgeladen wird. In diesem Fall wird die **CreateFromFile**-Methode verwendet, allerdings steht auch die asynchrone Version zur Verfügung (**CreateFromFileAsync**). Mit der **CreateFromFile**-Methode können Sie den Dateinamen, die Verschlüsselungsoption und einen Rückruf angeben, damit der Uploadstatus der Datei nachverfolgt werden kann.


	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}

Im folgenden Beispiel wird die UploadFile-Funktion aufgerufen. Außerdem wird die Speicherverschlüsselung als Option für die Medienobjekterstellung angegeben.


	var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##Nächste Schritte
Sie haben nun ein Medienobjekt in den Mediendienst hochgeladen und können mit dem Artikel [Abrufen eines Medienprozessors][] fortfahren.

[Abrufen eines Medienprozessors]: media-services-get-media-processor.md
 

<!---HONumber=August15_HO6-->