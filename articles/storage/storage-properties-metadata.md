
<properties 
  pageTitle="Festlegen und Abrufen von Eigenschaften und Metadaten für den Blob-Speicher | Microsoft Azure" 
  description="Erfahren Sie, wie Sie Eigenschaften und Metadaten für Azure Storage-Container und -Blobs festlegen und abrufen können." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="04/21/2015" 
  ms.author="tamram"/>


# Festlegen und Abrufen von Eigenschaften und Metadaten #

## Übersicht

Container und Blobs unterstützen zusätzlich zu den enthaltenen Daten zwei Formen von zugeordneten Daten:

*   **Systemeigenschaften.** Systemeigenschaften sind in jeder Container- oder Blob-Ressource vorhanden. Einige davon können gelesen oder festgelegt werden, während andere schreibgeschützt sind. Darüber hinaus entsprechen einige Systemeigenschaften bestimmten HTTP-Standardheadern, die in der verwalteten Azure-Bibliothek verwaltet werden.  

*   **Benutzerdefinierte Metadaten.** Benutzerdefinierte Metadaten sind Metadaten, die für eine bestimmte Ressource in Form von Name-Wert-Paaren angegeben werden. Sie können Metadaten verwenden, um zusätzliche Werte für einen Container oder Blob zu speichern. Diese Werte dienen nur den von Ihnen festgelegten Zwecken und haben keine Auswirkungen auf das Verhalten des Containers oder Blobs.

> [AZURE.IMPORTANT]Das Abrufen von Eigenschafts- und Metadatenwerten einer Ressource ist ein zweistufiger Prozess. Bevor Sie diese Werte lesen können, müssen Sie sie explizit von den Objekten [CloudBlobContainer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx), [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) oder [CloudPageBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.aspx) abrufen. Zum synchronen Abrufen von Eigenschaften und Metadaten rufen Sie die **FetchAttributes**-Methode für den Container oder Blob auf. Für einen asynchronen Abruf verwenden Sie **FetchAttributesAsync**.

## Festlegen und Abrufen von Eigenschaften

Ein Container besitzt nur schreibgeschützte Eigenschaften, während ein Blob über sowohl schreibgeschützte als auch Schreib-Lese-Eigenschaften verfügt. Um Eigenschaften für ein Blob festzulegen, geben Sie den Eigenschaftswert an und rufen dann die [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.setproperties.aspx)-Methode oder die [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.setproperties.aspx)-Methode auf.

Um Eigenschaften eines Containers oder Blobs zu lesen, rufen Sie die **FetchAttributes**-Methode auf und rufen dann den Eigenschaftswert ab.

Das folgende Codebeispiel erstellt einen Container und einen Blob und schreibt Eigenschaftswerte in ein Konsolenfenster. In diesem Beispiel wird der Speicheremulator verwendet, sodass der Dienst für emulierten Speicher ausgeführt werden muss, damit der Code funktioniert:

	// Use the storage emulator account.
	CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

	// As an alternative, you can create the credentials from the account name and key.
	// string accountName = "myaccount";
	// string accountKey = "SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";
	// StorageCredentials credentials = new StorageCredentials(accountName, accountKey);
	// CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	CloudBlockBlob blob = container.GetBlockBlobReference(<span style="color:#A31515;">"myblob.txt");

	// Create or overwrite the "myblob.txt" blob with contents from a local file.
	<span style="color:Blue;">using (<span style="color:Blue;">var fileStream = System.IO.File.OpenRead(<span style="color:#A31515;">@"c:\test\myblob.txt"))
	{
	   blob.UploadFromStream(fileStream);
	} 

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	Uri blobUri =<span style="color:Blue;">new UriBuilder(containerUri.AbsoluteUri + <span style="color:#A31515;">"/ablob.txt").Uri;
	CloudPageBlob blob = <span style="color:Blue;">new CloudPageBlob(blobUri, credentials);
	blob.Create(1024);
				

	// Set the CacheControl property.
	blob.Properties.CacheControl = <span style="color:#A31515;">"public, max-age=31536000";
	blob.SetProperties();

	// Fetch blob attributes.
	blob.FetchAttributes();

	Console.WriteLine(<span style="color:#A31515;">"Read-only properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"BlobType: " + blob.Properties.BlobType);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + blob.Properties.ETag);
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUtc: " + blob.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"Length: " + blob.Properties.Length);
	Console.WriteLine();

	Console.WriteLine(<span style="color:#A31515;">"Read-write properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"CacheControl: " +
	   (blob.Properties.CacheControl == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.CacheControl));
	Console.WriteLine(<span style="color:#A31515;">"ContentEncoding: " +
	   (blob.Properties.ContentEncoding == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentEncoding));
	Console.WriteLine(<span style="color:#A31515;">"ContentLanguage: " +
	   (blob.Properties.ContentLanguage == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentLanguage));
	Console.WriteLine(<span style="color:#A31515;">"ContentMD5: " +
	   (blob.Properties.ContentMD5 == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentMD5));
	Console.WriteLine(<span style="color:#A31515;">"ContentType: " +
	   (blob.Properties.ContentType == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentType));

	// Clean up.
	blob.DeleteIfExists();
	container.Delete();
## Festlegen und Abrufen von Metadaten

Sie können Metadaten als ein oder mehrere Name-Wert-Paare für einen Blob oder Container angeben. Fügen Sie zum Festlegen von Metadaten Name-Wert-Paare zur **Metadaten**-Auflistung der Ressource hinzu, und rufen Sie dann die **SetMetadata**-Methode auf, um die Werte für den Dienst zu speichern.

> [AZURE.NOTE]Der Name der Metadaten muss den Benennungskonventionen für C#-Bezeichner entsprechen.
 
Zum Abrufen von Metadaten rufen Sie die **FetchAttributes**-Methode für den Blob oder Container auf, um die **Metadaten**-Auflistung zu füllen, und lesen Sie anschließend die Werte.

Im folgenden Codebeispiel wird ein neuer Container erstellt, und es werden Metadaten für diesen festgelegt, anschließend werden die Metadatenwerte wieder ausgelesen:

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## Weitere Informationen  

- [Azure Storage Client Library Reference](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) (in englischer Sprache)
- [Get Started with the Blob Storage for .NET](storage-dotnet-how-to-use-blobs.md) (in englischer Sprache)  
 

<!---HONumber=July15_HO2-->