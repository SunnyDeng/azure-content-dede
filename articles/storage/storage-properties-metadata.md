
<properties 
  pageTitle="Festlegen und Abrufen von Eigenschaften und Metadaten für Objekte in Azure Storage | Microsoft Azure" 
  description="Speichern Sie benutzerdefinierten Metadaten für Objekte in Azure Storage, legen Sie Systemeigenschaften fest, und rufen Sie diese ab." 
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
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# Festlegen und Abrufen von Eigenschaften und Metadaten #

## Übersicht

Objekte in Azure-Speicher unterstützen zusätzlich zu den Daten, die sie enthalten, Systemeigenschaften und benutzerdefinierte Metadaten:

*   **Systemeigenschaften.** Systemeigenschaften sind in jeder Speicherressource vorhanden. Einige davon können gelesen oder festgelegt werden, während andere schreibgeschützt sind. Darüber hinaus entsprechen einige Systemeigenschaften bestimmten HTTP-Standardheadern. Die Azure-Speicher-Clientbibliothek verwaltet diese für Sie.  

*   **Benutzerdefinierte Metadaten.** Benutzerdefinierte Metadaten sind Metadaten, die für eine bestimmte Ressource in Form von Name-Wert-Paaren angegeben werden. Sie können Metadaten verwenden, um zusätzliche Werte für eine Speicherressource zu speichern. Diese Werte dienen nur den von Ihnen festgelegten Zwecken und haben keine Auswirkungen auf das Verhalten der Ressource.

## Festlegen und Abrufen von Eigenschaften

Das Abrufen von Eigenschafts- und Metadatenwerten einer Speicherressource ist ein zweistufiger Prozess. Bevor Sie diese Werte lesen können, müssen sie explizit durch Aufrufen der **FetchAttributes**- oder **FetchAttributesAsync**-Methode abgerufen werden.

> [AZURE.IMPORTANT]Eigenschafts- und Metadatenwerte werden bei einer Speicherressource nicht aufgefüllt, sofern Sie nicht eine der **FetchAttributes**-Methoden aufrufen.

Um Eigenschaften für ein Blob festzulegen, geben Sie den Eigenschaftswert an und rufen dann die **SetProperties**-Methode oder die **SetPropertiesAsync**-Methode auf.

Das folgende Codebeispiel erstellt einen Container und schreibt Eigenschaftswerte in ein Konsolenfenster:

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

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
 

<!---HONumber=Oct15_HO3-->