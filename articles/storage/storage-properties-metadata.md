
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
  ms.date="01/05/2016" 
  ms.author="tamram"/>


# Festlegen und Abrufen von Eigenschaften und Metadaten #

## Übersicht

Objekte in Azure-Speicher unterstützen zusätzlich zu den Daten, die sie enthalten, Systemeigenschaften und benutzerdefinierte Metadaten:

*   **Systemeigenschaften.** Systemeigenschaften sind in jeder Speicherressource vorhanden. Einige davon können gelesen oder festgelegt werden, während andere schreibgeschützt sind. Darüber hinaus entsprechen einige Systemeigenschaften bestimmten HTTP-Standardheadern. Die Azure-Speicher-Clientbibliothek verwaltet diese für Sie.  

*   **Benutzerdefinierte Metadaten.** Benutzerdefinierte Metadaten sind Metadaten, die für eine bestimmte Ressource in Form von Name-Wert-Paaren angegeben werden. Sie können Metadaten verwenden, um zusätzliche Werte für eine Speicherressource zu speichern. Diese Werte dienen nur den von Ihnen festgelegten Zwecken und haben keine Auswirkungen auf das Verhalten der Ressource.

Das Abrufen von Eigenschafts- und Metadatenwerten einer Speicherressource ist ein zweistufiger Prozess. Bevor Sie diese Werte lesen können, müssen Sie sie explizit durch Aufrufen der **FetchAttributes**-Methode abrufen.

> [AZURE.IMPORTANT]Eigenschafts- und Metadatenwerte werden bei einer Speicherressource nicht aufgefüllt, sofern Sie nicht eine der **FetchAttributes**-Methoden aufrufen.

## Festlegen und Abrufen von Eigenschaften

Zum Abrufen von Eigenschaftswerten rufen Sie die **FetchAttributes**-Methode für das Blob oder den Container auf, um die Eigenschaften aufzufüllen, und lesen anschließend die Werte.

Um Eigenschaften für ein Objekt festzulegen, geben Sie den Eigenschaftswert an und rufen dann die **SetProperties**-Methode auf.

Das folgende Codebeispiel erstellt einen Container und schreibt einige der zugehörigen Eigenschaftswerte in ein Konsolenfenster:

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

Sie können Metadaten als ein oder mehrere Name-Wert-Paare für ein Blob oder einen Container angeben. Fügen Sie zum Festlegen von Metadaten Name-Wert-Paare zur **Metadaten**-Auflistung der Ressource hinzu, und rufen Sie dann die **SetMetadata**-Methode auf, um die Werte für den Dienst zu speichern.

> [AZURE.NOTE]Der Name der Metadaten muss den Benennungskonventionen für C#-Bezeichner entsprechen.
 
Das folgende Codebeispiel legt die Metadaten für einen Container fest. Mittels der **Hinzufügen**-Methode der Sammlung wird ein Wert festgelegt. Der andere Wert wird mit der impliziten Schlüssel-Wert-Syntax festgelegt. Beide eignen sich hierzu.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Zum Abrufen von Metadaten rufen Sie die **FetchAttributes**-Methode für das Blob oder den Container auf, um die **Metadaten**-Auflistung zu füllen, und lesen anschließend die Werte wie im unten stehenden Beispiel gezeigt.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## Siehe auch  

- [Azure Storage-Clientbibliothek für .NET-Referenz](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Azure Storage-Clientbibliothek für .NET-Paket](https://www.nuget.org/packages/WindowsAzure.Storage/) 

<!---HONumber=AcomDC_0107_2016-->