<properties 
	pageTitle="Erstellen und Verwenden einer SAS mit dem Blob-Dienst | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Shared Access Signatures erstellen und dann mit dem Blob-Dienst verwenden." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>


# Shared Access Signatures, Teil 2: Erstellen und Verwenden einer SAS mit dem Blob-Dienst

In [Teil 1](../storage-dotnet-shared-access-signature-part-1/) dieses Lernprogramms haben Sie das Konzept der Shared Access Signatures (SAS) sowie bewährte Methoden für deren Einsatz kennengelernt. In Teil 2 erfahren Sie, wie Sie Shared Access Signatures mit dem Azure Blob-Dienst erstellen und dann verwenden können. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die gezeigten Szenarios umfassen die folgenden Aspekte der Arbeit mit Shared Access Signatures:

- Generieren einer Shared Access Signature für einen Container
- Generieren einer Shared Access Signature für ein Blob
- Erstellen einer gespeicherten Zugriffsrichtlinie zum Verwalten von Signaturen für die Ressourcen eines Containers
- Testen der Shared Access Signatures über eine Client-Anwendung

# Informationen zu diesem Lernprogramm #
In diesem Lernprogramm liegt das Hauptaugenmerk auf der Erstellung von Shared Access Signatures für Container und Blobs mithilfe von zwei Konsolenanwendungen. Die erste Konsolenanwendung generiert Shared Access Signatures in einem Container und einem Blob. Diese Anwendung kennt die Speicherkontoschlüssel. Die zweite Konsolenanwendung, die als Client-Anwendung fungiert, greift über die mit der ersten Anwendung erstellten Shared Access Signatures auf Container- und Blob-Ressourcen zu. Diese Anwendung verwendet die Shared Access Signatures nur, um den Zugriff auf die Container- und Blob-Ressourcen zu authentifizieren; sie kennt die Kontoschlüssel nicht.

# Teil 1: Erstellen einer Konsolenanwendung zum Generieren von Shared Access Signatures #

Stellen Sie zunächst sicher, dass Sie die Azure-Speicher-Clientbibliothek für .NET installiert haben. Sie können das [NuGet-PaketNuGet-Paket](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet package") mit den aktuellen Assemblys für die Clientbibliothek installieren; dabei handelt es sich um die empfohlene Methode, um sicherzustellen, dass Sie über die aktuellen Fehlerbehebungen verfügen. Sie können die Clientbibliothek auch als Teil der aktuellen Version des [Azure SDK für .NET] herunterladen(http://www.windowsazure.com/de-de/downloads/).

Erstellen Sie in Visual Studio eine neue Windows-Konsolenanwendung, und geben Sie ihr den Namen **GenerateSharedAccessSignatures**. Fügen Sie mit einer der folgenden Methoden Verweise auf **Microsoft.WindowsAzure.Configuration.dll** und **Microsoft.WindowsAzure.Storage.dll** hinzu:

- 	Wenn Sie das NuGet-Paket installieren möchten, installieren Sie zuerst die [NuGet-Paket-Manager-Erweiterung für Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Wählen Sie in Visual Studio **Projekt | NuGet-Pakete verwalten**, suchen Sie online nach **Azure-Speicher** und befolgen Sie die Installationsanweisungen.
- 	Suchen Sie alternativ nach den Assemblys in Ihrer Installation des Azure SDK, und fügen Sie Verweise darauf hinzu.
 
Fügen Sie zu Beginn der Datei Program.cs die folgenden **using**-Anweisungen hinzu:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Bearbeiten Sie die Datei "app.config", sodass sie eine Konfigurationseinstellung mit einer Verbindungszeichenfolge enthält, die auf Ihr Speicherkonto verweist. Ihre Datei "app.config" sollte ähnlich der folgenden aussehen:

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

## Generieren eines Shared Access Signature-URI für einen Container ##

Zu Beginn fügen wir eine Methode hinzu, um eine Shared Access Signature in einem neuen Container zu generieren. In diesem Fall ist die Signatur nicht mit einer gespeicherten Zugriffsrichtlinie verbunden, sodass sie im URI Informationen bezüglich Ablaufzeit und zu gewährenden Berechtigungen enthält.

Fügen Sie den Code zunächst der **Main()**-Methode hinzu, um den Zugriff auf Ihr Speicherkonto zu authentifizieren, und erstellen Sie einen neuen Container:

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Insert calls to the methods created below here...
	    
	    //Require user input before closing the console window.
	    Console.ReadLine();
    }

Fügen Sie dann eine neue Methode hinzu, die die Shared Access Signature für den Container generiert und den Signatur-URI zurückgibt:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
	    //Set the expiry time and permissions for the container.
	    //In this case no start time is specified, so the shared access signature becomes valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
	    
	    //Generate the shared access signature on the container, setting the constraints directly on the signature.
	    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

Fügen Sie am Ende der **Main()**-Methode, vor dem Aufruf von **Console.ReadLine()**, die folgenden Zeilen hinzu, um **GetContainerSasUri()** aufzurufen und den Signatur-URI in das Konsolenfenster zu schreiben:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Führen Sie eine Kompilierung durch, und führen Sie den Code aus, um den Shared Access Signature-URI für den neuen Container auszugeben. Der URI sieht etwa wie der folgende URI aus:       

https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Nach dem Ausführen des Codes wird die Shared Access Signature, die Sie im Container erstellt haben, für die nächsten vier Stunden gültig sein. Die Signatur gewährt einem Client die Berechtigung, Blobs im Container aufzulisten und ein neues Blob im Container zu schreiben.

## Generieren eines Shared Access Signature-URI für ein Blob ##

Als Nächstes schreiben wir einen ähnlichen Code, um ein neues Blob im Container zu erstellen und um eine Shared Access Signature dafür zu erstellen. Diese Shared Access Signature ist nicht mit einer gespeicherten Zugriffsrichtlinie verbunden, sodass sie die Startzeit, Ablaufzeit sowie Berechtigungsinformationen im URI enthält.

Fügen Sie eine neue Methode hinzu, mit der ein neues Blob erstellt wird, und geben Sie Text ein, woraufhin eine Shared Access Signature generiert und der Signature-URI zurückgegeben wird:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Set the expiry time and permissions for the blob.
	    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
	    //The shared access signature will be valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
	    
	    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
	    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

Fügen Sie am Ende der **Main()**-Methode, vor dem Aufruf von **Console.ReadLine()**, die folgenden Zeilen hinzu, um **GetBlobSasUri()** aufzurufen, und schreiben Sie den Shared Access Signature-URI in das Konsolenfenster:    
    
    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();
    

Führen Sie eine Kompilierung durch, und führen Sie den Code aus, um den Shared Access Signature-URI für das neue Blob auszugeben. Der URI sieht etwa wie der folgende URI aus:

https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

## Erstellen einer gespeicherten Zugriffsrichtlinie im Container ##

Erstellen Sie nun im Container eine gespeicherte Zugriffsrichtlinie, mit der die Einschränkungen für Shared Access Signatures definiert werden, die damit verbunden sind.

In den vorherigen Beispielen wurden die Startzeit (implizit oder explizit), das Ablaufdatum und die Berechtigungen im Shared Access Signature-URI selbst festgelegt. In den folgenden Beispielen legen Sie diese in der gespeicherten Zugriffsrichtlinie und nicht in der Shared Access Signature fest. Auf diese Weise können Sie die Einschränkungen ändern, ohne die Shared Access Signature erneut ausgeben zu müssen.

Sie können eine oder mehrere Einschränkungen in der Shared Access Signature verwalten und die restlichen Einschränkungen in der gespeicherten Zugriffsrichtlinie. Sie können die Startzeit, Ablaufzeit und Berechtigungen jedoch nur an dem einen oder dem anderen Ort festlegen. Sie können Berechtigungen beispielsweise nicht in der Shared Access Signature und in der gespeicherten Zugriffsrichtlinie festlegen.

Fügen Sie eine neue Methode hinzu, mit der eine neue gespeicherte Zugriffsrichtlinie erstellt und der Name der Richtlinie zurückgegeben wird:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
	    //Create a new stored access policy and define its constraints.
	    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
	    {
		    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
		    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
	    };
	    
	    //Get the container's existing permissions.
	    BlobContainerPermissions permissions = new BlobContainerPermissions();
	    
	    //Add the new policy to the container's permissions.
	    permissions.SharedAccessPolicies.Clear();
	    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
	    container.SetPermissions(permissions);
    }

Fügen Sie am Ende der **Main()**-Methode, vor dem Aufruf von **Console.ReadLine()**, die folgenden Zeilen hinzu, um die **CreateSharedAccessPolicy()**-Methode aufzurufen:    

    //Create an access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

## Generieren eines Shared Access Signature-URI im Container mit einer Zugriffsrichtlinie ##

Als Nächstes erstellen wir eine andere Shared Access Signature im Container, den wir zuvor erstellt haben, doch diesmal verbinden wir die Signatur mit der Zugriffsrichtlinie, die wir im vorherigen Beispiel erstellt haben.

Fügen Sie eine neue Methode hinzu, um eine andere Shared Access Signature im Container zu generieren:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Generate the shared access signature on the container. In this case, all of the constraints for the 
	    //shared access signature are specified on the stored access policy.
	    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }
    
Fügen Sie am Ende der **Main()**-Methode, vor dem Aufruf von **Console.ReadLine()**, die folgenden Zeilen hinzu, um die **GetContainerSasUriWithPolicy**-Methode aufzurufen:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

## Generieren eines Shared Access Signature-URI im Blob mit einer Zugriffsrichtlinie ##

Abschließend fügen wir eine ähnliche Methode hinzu, um ein anderes Blob zu erstellen und um eine Shared Access Signature zu generieren, die mit einer Zugriffsrichtlinie verbunden ist.

Fügen Sie eine neue Methode hinzu, um ein Blob zu erstellen und eine Shared Access Signature zu generieren:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
	    "A stored access policy defines the constraints for the signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Generate the shared access signature on the blob.
	    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

Fügen Sie am Ende der **Main()**-Methode, vor dem Aufruf von **Console.ReadLine()**, die folgenden Zeilen hinzu, um die **GetBlobSasUriWithPolicy**-Methode aufzurufen:    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Die **Main()**-Methode sollte jetzt insgesamt folgendermaßen aussehen. Führen Sie sie aus, um die Shared Access Signature-URIs in das Konsolenfenster zu schreiben. Kopieren Sie sie dann in eine Textdatei, um sie im zweiten Teil dieses Lernprogramms zu verwenden.    

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Generate a SAS URI for the container, without a stored access policy.
	    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, without a stored access policy.
	    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
	    Console.WriteLine();
	    
	    //Create an access policy on the container, which may be optionally used to provide constraints for 
	    //shared access signatures on the container and the blob.
	    string sharedAccessPolicyName = "tutorialpolicy";
	    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
	    
	    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    Console.ReadLine();
    }

Wenn Sie die Konsolenanwendung "GenerateSharedAccessSignatures" ausführen, erhalten Sie eine Ausgabe ähnlich der folgenden im Konsolenfenster. Dies sind die Shared Access Signatures, die Sie in Teil 2 dieses Lernprogramms verwenden werden.

![sas-console-output-1][sas-console-output-1]

# Teil 2: Erstellen einer Konsolenanwendung zum Testen von Shared Access Signatures #

Zum Testen der Shared Access Signatures, die Sie in den vorherigen Beispielen erstellt haben, erstellen Sie eine zweite Konsolenanwendung, die die Signaturen zum Durchführen von Container- und Blob-Vorgängen verwendet.

Wenn seit dem Abschluss des ersten Teils des Lernprogramms mehr als vier Stunden vergangen sind, sind die Signaturen, die Sie mit einer Ablaufzeit von vier Stunden generiert haben, nicht mehr gültig. Gleichermaßen laufen die Signaturen, die mit der gespeicherten Zugriffsrichtlinie verbunden sind, nach zehn Stunden ab. Wenn einer oder beide dieser Zeiträume abgelaufen sind, sollten Sie den Code in der ersten Konsolenanwendung ausführen, um neue Shared Access Signatures für den zweiten Teil des Lernprogramms zu generieren.

Erstellen Sie in Visual Studio eine neue Windows-Konsolenanwendung, und geben Sie Ihr den Namen **ConsumeSharedAccessSignatures**. Fügen Sie wie bereits zuvor Verweise auf **Microsoft.WindowsAzure.Configuration.dll** und **Microsoft.WindowsAzure.Storage.dll** hinzu.

Fügen Sie zu Beginn der Datei Program.cs die folgenden **using**-Anweisungen hinzu:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
Fügen Sie im Hauptteil der **Main()**-Methode die folgenden Konstanten hinzu, und aktualisieren Sie die Werte in den Shared Access Signatures, die Sie in Teil 1 des Lernprogramms generiert haben.

    static void Main(string[] args)
    {
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }
    
## Hinzufügen einer Methode zum Testen von Container-Vorgängen mit einer Shared Access Signature ##

Als Nächstes fügen wir eine Methode hinzu, mit der einige repräsentative Container-Vorgänge mit einer Shared Access Signature im Container getestet werden. Beachten Sie, dass die Shared Access Signature zur Rückgabe eines Verweises auf den Container verwendet wird, wobei der Zugriff auf den Container allein auf der Signatur basierend authentifiziert wird.

Fügen Sie "Program.cs" die folgende Methode hinzu:


	static void UseContainerSAS(string sas)
	{
	    //Try performing container operations with the SAS provided.
	
	    //Return a reference to the container using the SAS URI.
	    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
	
	    //Create a list to store blob URIs returned by a listing operation on the container.
	    List<Uri> blobUris = new List<Uri>();
	
	    try
	    {
	        //Write operation: write a new blob to the container. 
	        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
	        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
	        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	        msWrite.Position = 0;
	        using (msWrite)
	        {
	            blob.UploadFromStream(msWrite);
	        }
	        Console.WriteLine("Write operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Write operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //List operation: List the blobs in the container, including the one just added.
	        foreach (ICloudBlob blobListing in container.ListBlobs())
	        {
	            blobUris.Add(blobListing.Uri);
	        }
	        Console.WriteLine("List operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("List operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Read operation: Get a reference to one of the blobs in the container and read it. 
	        CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
	        MemoryStream msRead = new MemoryStream();
	        msRead.Position = 0;
	        using (msRead)
	        {
	            blob.DownloadToStream(msRead);
	            Console.WriteLine(msRead.Length);
	        }
	        Console.WriteLine("Read operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Read operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	    Console.WriteLine();
	
	    try
	    {
	        //Delete operation: Delete a blob in the container.
	        CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
	        blob.Delete();
	        Console.WriteLine("Delete operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Delete operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }        
	}


Aktualisieren Sie die **Main()**-Methode, um **UseContainerSAS()** mit den beiden im Container erstellten Shared Access Signatures aufzurufen:


	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}


## Hinzufügen einer Methode zum Testen von Blob-Vorgängen mit einer Shared Access Signature ##

Abschließend fügen wir eine Methode hinzu, mit der einige repräsentative Blob-Vorgänge mit einer Shared Access Signature im Blob getestet werden. In diesem Fall verwenden wir den Konstruktor **CloudBlockBlob(String)**, wobei die Shared Access Signature übergeben wird, um einen Verweis auf das Blob zurückzugeben. Es ist keine weitere Authentifizierung erforderlich; sie basiert allein auf der Signatur.

Fügen Sie "Program.cs" die folgende Methode hinzu:


	static void UseBlobSAS(string sas)
	{
	    //Try performing blob operations using the SAS provided.
	
	    //Return a reference to the blob using the SAS URI.
	    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));
	
	    try
	    {
	        //Write operation: write a new blob to the container. 
	        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
	        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	        msWrite.Position = 0;
	        using (msWrite)
	        {
	            blob.UploadFromStream(msWrite);
	        }
	        Console.WriteLine("Write operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Write operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Read operation: Read the contents of the blob.
	        MemoryStream msRead = new MemoryStream();
	        using (msRead)
	        {
	            blob.DownloadToStream(msRead);
	            msRead.Position = 0;
	            using (StreamReader reader = new StreamReader(msRead, true))
	            {
	                string line;
	                while ((line = reader.ReadLine()) != null)
	                {
	                    Console.WriteLine(line);
	                }
	            }
	        }
	        Console.WriteLine("Read operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Read operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Delete operation: Delete the blob.
	        blob.Delete();
	        Console.WriteLine("Delete operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Delete operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }        
	}


Aktualisieren Sie die **Main()**-Methode, um **UseBlobSAS()** mit den beiden im Blob erstellten Shared Access Signatures aufzurufen.

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}

Führen Sie die Konsolenanwendung aus, und beobachten Sie die Ausgabe, um herauszufinden, welche Vorgänge für welche Signaturen zulässig sind. Die Ausgabe im Konsolenfenster sieht etwa wie folgt aus:

![sas-console-output-2][sas-console-output-2]

# Nächste Schritte #

[Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell](../storage-dotnet-shared-access-signature-part-1/)

[Verwalten des Zugriffs auf Azure-Speicherressourcen](http://msdn.microsoft.com/de-de/library/windowsazure/ee393343.aspx)

[Delegieren des Zugriffs mit einer Shared Access Signature (REST API)](http://msdn.microsoft.com/de-de/library/windowsazure/ee395415.aspx)

[Introducing Table and Queue SAS (Einführung in Tabellen- und Warteschlangen-SAS; in englischer Sprache)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG


<!--HONumber=42-->
