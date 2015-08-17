<properties
			pageTitle="Verwenden des Azure-Dateispeichers mit PowerShell und .NET | Microsoft Azure"
            description="Erfahren Sie, wie Sie den Azure-Dateispeicher zum Erstellen von Cloud-Dateifreigaben und zum Verwalten des Dateiinhalts verwenden. Der Dateispeicher ermöglicht Unternehmen, von SMB-Dateifreigaben abhängige Anwendungen nach Azure zu verschieben. Beibehalten Ihrer Speicherkonto-Anmeldeinformationen auf dem virtuellen Computer, sodass die Verbindung mit der Dateifreigabe beim Neustart des Computers hergestellt wird."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="08/04/2015"
      ms.author="tamram" />

# Verwenden des Azure-Dateispeichers mit PowerShell und .NET

## Übersicht

Der Azure-Dateidienst stellt Dateifreigaben mit dem SMB 2.1-Standardprotokoll bereit. Anwendungen, die in Azure ausgeführt werden, können nun ganz einfach über standardmäßige und bekannte Dateisystem-APIs wie ReadFile und WriteFile Dateien zwischen virtuellen Computern freigeben. Darüber hinaus kann der Dateizugriff gleichzeitig über eine REST-Schnittstelle erfolgen, die eine Vielzahl von Hybridszenarien ermöglicht. Schließlich beruhen Azure-Dateien auf der gleichen Technologie wie BLOB-, Tabellen-, und Warteschlangendienste, was bedeutet, dass Azure-Dateien die vorhandenen Verfügbarkeit, Dauerhaftigkeit, Skalierbarkeit und geografische Redundanz nutzen, die in die Azure Storage-Plattform integriert ist.

## Informationen zu diesem Lernprogramm

In diesem Lernprogramm für die ersten Schritte veranschaulichen wir die Grundlagen der Verwendung des Microsoft Azure-Dateispeichers. In diesem Lernprogramm wird Folgendes beschrieben:

- Verwenden von Azure PowerShell, um die Erstellung einer neuen Azure-Dateifreigabe, das Hinzufügen eines Verzeichnisses, das Hochladen einer lokalen Datei in die Freigabe und das Auflisten der Dateien in dem Verzeichnis zu veranschaulichen.
- Bereitstellen der Dateifreigabe von einem virtuellen Azure-Computer, genau wie bei der Bereitstellung einer SMB-Freigabe.
- Verwenden der Azure Storage-Clientbibliothek für .NET, um aus einer lokalen Anwendung auf die Dateifreigabe zuzugreifen. Sie erstellen eine Konsolenanwendung und führen diese Aktionen mit der Dateifreigabe aus:
	- Schreiben des Inhalts einer Datei in der Freigabe in das Konsolenfenster
	- Festlegen des Kontingents (maximale Größe) für die Dateifreigabe
	- Erstellen einer SAS (Shared Access Signature) für eine Datei, für die eine SAS-Richtlinie verwendet wird, die für die Freigabe definiert ist
	- Kopieren einer Datei in eine andere Datei im selben Speicherkonto
	- Kopieren einer Datei in ein BLOB im selben Speicherkonto

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Erstellen eines Azure-Speicherkontos

Der Azure-Dateispeicher befindet sich derzeit noch in der Vorschau. Navigieren Sie zur Anforderung des Zugriffs auf die Vorschau zur [Microsoft Azure-Vorschauseite](/services/preview/), und fordern Sie den Zugriff auf **Azure-Dateien** an. Nachdem Ihre Anforderung genehmigt wurde, werden Sie benachrichtigt, dass Sie auf die Dateispeichervorschau zugreifen können. Anschließend können Sie ein Speicherkonto für den Zugriff auf den Dateispeicher erstellen.

> [AZURE.NOTE]Dateispeicher ist derzeit nur für neue Speicherkonten verfügbar. Nachdem Sie in Ihrem Abonnement Zugriff auf den Dateispeicher erhalten haben, erstellen Sie ein neues Speicherkonto zur Verwendung in dieser Anleitung.
>
> Beachten Sie, dass der Azure-Dateispeicher derzeit keine Shared Access Signatures unterstützt.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Dateifreigabe mithilfe von PowerShell

Als Nächstes verwenden wir Azure PowerShell, um eine Dateifreigabe zu erstellen. Sobald die Dateifreigabe erstellt wurde, können Sie sie von einem beliebigen Dateisystem aus einbinden, das SMB 2.1 unterstützt.

### Installieren der PowerShell-Cmdlets für den Azure-Speicher

Laden Sie die Azure PowerShell-Cmdlets herunter und installieren Sie diese anschließend, um die Verwendung von PowerShell vorzubereiten. Informationen zum Installationspunkt und zu Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

> [AZURE.NOTE]Die PowerShell-Cmdlets für den Dateidienst sind nur im aktuellen Azure PowerShell-Modul, Version 0.8.5 oder höher, verfügbar. Sie sollten daher das neueste Azure PowerShell-Modul herunterladen und installieren bzw. ein Upgrade durchführen.

Öffnen Sie ein Azure PowerShell-Fenster, indem Sie auf **Start** klicken und **Azure PowerShell** eingeben. Im Azure PowerShell-Fenster wird das Azure Powershell-Modul geladen.

### Erstellen von Kontexten für Speicherkonten und -schlüssel

Erstellen Sie nun den Speicherkonto-Kontext. Der Kontext kapselt den Speicherkontonamen und den Kontoschlüssel. Anweisungen zum Kopieren Ihres Kontoschlüssels im Azure-Portal finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Ersetzen Sie im folgenden Beispiel `storage-account-name` und `storage-account-key` durch Ihren Speicherkontonamen und -schlüssel :

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Erstellen einer neuen Dateifreigabe

Erstellen Sie nun die neue Freigabe namens `logs` in diesem Beispiel:

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

Nun haben Sie eine Dateifreigabe im Dateispeicher. Als Nächstes fügen Sie ein Verzeichnis und eine Datei hinzu.

> [AZURE.IMPORTANT]Der Name der Dateifreigabe darf nur Kleinbuchstaben enthalten. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Erstellen eines Verzeichnisses in der Dateifreigabe

Erstellen Sie als Nächstes ein Verzeichnis in der Freigabe. Im folgenden Beispiel lautet der Verzeichnisname `CustomLogs`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Hochladen einer lokalen Datei in das Verzeichnis

Laden Sie nun eine lokale Datei in das Verzeichnis hoch. Im folgenden Beispiel wird eine Datei aus `C:\temp\Log1.txt` hochgeladen. Bearbeiten Sie den Dateipfad so, dass er auf eine gültige Datei auf Ihrem lokalen Computer verweist:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Auflisten der Dateien im Verzeichnis

Sie können die Dateien im Verzeichnis auflisten, um die Datei im Verzeichnis anzuzeigen. Mit diesem Befehl werden außerdem die Unterverzeichnisse aufgelistet, in diesem Beispiel ist jedoch kein Unterverzeichnis vorhanden, daher wird nur die Datei angezeigt.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Einbinden der Freigabe von einem virtuellen Azure-Computer unter Windows aus

Um zu veranschaulichen, wie eine Azure-Dateifreigabe eingebunden wird, erstellen wir nun einen virtuellen Azure-Computer unter Windows und greifen zum Einbinden der Freigabe remote darauf zu.

1. Erstellen Sie zunächst einen neuen virtuellen Azure-Computer, indem Sie die Anweisungen in [Erstellen eines virtuellen Windows Server-Computers](../virtual-machines-windows-tutorial.md) befolgen.
2. Als nächstes greifen Sie remote auf den virtuellen Computer zu, indem Sie die Anweisungen in [Anmeldung auf einem virtuellen Windows Server-Computer](../virtual-machines-log-on-windows-server.md) befolgen.
3. Öffnen Sie ein PowerShell-Fenster auf dem virtuellen Computer.

### Fortbestehen der Anmeldeinformationen Ihres Speicherkontos für den virtuellen Computer

Bevor die Bereitstellung für die Dateifreigabe erfolgt, bestätigen Sie zunächst die Anmeldeinformationen für Ihr Speicherkonto auf dem virtuellen Computer. Durch diesen Schritt kann Windows bei einem Neustart des virtuellen Computers automatisch erneut eine Verbindung zur Dateifreigabe herstellen. Um Ihre Anmeldeinformationen beizubehalten, führen Sie im PowerShell-Fenster auf dem virtuellen Computer den Befehl `cmdkey` aus. Ersetzen Sie `<storage-account-name>` durch den Namen Ihres Speicherkontos und `<storage-account-key>` durch den Schlüssel des Speicherkontos:

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows stellt nun bei einem Neustart des virtuellen Computers erneut eine Verbindung zur Dateifreigabe her. Sie können überprüfen, ob die Freigabe erneut verbunden wurde, indem Sie den Befehl `net use` in einem PowerShell-Fenster ausführen.

### Bereitstellen der Dateifreigabe mithilfe der dauerhaften Anmeldeinformationen

Nachdem Sie eine Remoteverbindung zu dem virtuellen Computer hergestellt haben, können Sie den Befehl `net use` mit folgender Syntax ausführen, um die Dateifreigabe bereitzustellen. Ersetzen Sie `<storage-account-name>` durch den Namen Ihres Speicherkontos und `<share-name>` durch den Namen Ihrer Dateispeicher-Freigabe:

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

Da Sie die Speicherkonto-Anmeldeinformationen im vorherigen Schritt dauerhaft gespeichert haben, müssen Sie diese nicht mit dem Befehl `net use` angeben. Wenn Sie Ihre Anmeldeinformationen noch nicht dauerhaft gespeichert haben, fügen Sie sie als Parameter hinzu, der an den Befehl `net use` übergeben wird, wie in diesem Beispiel gezeigt:

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Sie können nun mit der Dateispeicher-Freigabe vom virtuellen Computer aus arbeiten, wie von jedem anderen Laufwerk auch. Sie können die Standarddateibefehle über die Eingabeaufforderung eingeben oder die bereitgestellte Freigabe und deren Inhalt im Datei-Explorer anzeigen. Sie können auf dem virtuellen Computer auch Code ausführen, der mithilfe der standardmäßigen Datei-E/A-APIs von Windows (z. B. die von [System.IO-Namespaces](http://msdn.microsoft.com/library/gg145019.aspx) im .NET Framework bereitgestellten APIs) auf die Dateifreigabe zugreift.

Sie können die Dateifreigabe auch über eine im Azure-Clouddienst ausgeführte Rolle bereitstellen, indem Sie eine Remoteverbindung zur Rolle herstellen.

## Erstellen einer lokalen Anwendung für den Zugriff auf den Dateispeicher

Sie können die Dateifreigabe von einem virtuellen Computer aus oder von einem in Azure ausgeführten Cloud-Dienst einbinden, wie oben gezeigt. Sie können die Dateifreigabe jedoch nicht von einer lokalen Anwendung aus einbinden. Zum Zugriff auf freigegebene Daten von einer lokalen Anwendung aus verwenden Sie die Dateispeicher-API. In diesem Beispiel wird gezeigt, wie Sie über die [Azure .NET-Speicherclientbibliothek](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409) mit einer Dateifreigabe arbeiten.

Um die Verwendung der API von einer lokalen Anwendung aus zu veranschaulichen, erstellen wir eine einfache Konsolenanwendung, die auf dem Desktop ausgeführt wird.

### Erstellen der Konsolenanwendung und Abrufen der Assembly

So erstellen Sie eine neue Konsolenanwendung in Visual Studio und installieren das Azure Storage NuGet-Paket:

1. Wählen Sie in Visual Studio **Datei** -> **Neues Projekt**, und wählen Sie dann **Windows** -> **Konsolenanwendung** aus der Liste der Visual C#-Vorlagen aus.
2. Stellen Sie einen Namen für die Konsolenanwendung bereit, und klicken Sie dann auf **OK**.
3. Nachdem Ihr Projekt erstellt wurde, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf das Projekt und wählen **NuGet-Pakete verwalten** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

### Speichern Ihrer Speicherkonto-Anmeldeinformationen in der Datei „app.config“

Als Nächstes speichern Sie Ihre Anmeldeinformationen in der Datei „app.config“ des Projekts. Bearbeiten Sie die Datei "app.config" ähnlich wie im folgenden Beispiel, indem Sie `myaccount` durch den Namen Ihres Speicherkontos und `mykey` durch den Schlüssel Ihres Speicherkontos ersetzen:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]Die neueste Version des Azure-Speicheremulators bietet keine Unterstützung für Dateispeicher. Die Verbindungszeichenfolge muss auf ein Azure-Speicherkonto in der Cloud mit Zugriff auf die Files-Vorschau verweisen.


### Hinzufügen von Namespace-Deklarationen

Öffnen Sie die Datei „program.cs“ über den Projektmappen-Explorer, und fügen Sie die folgenden Namespace-Deklarationen am Anfang der Datei hinzu:

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### Programmgesteuertes Abrufen der Verbindungszeichenfolge

Sie können Ihre gespeicherten Anmeldeinformationen aus der Datei „app.config“ entweder mit der Klasse `Microsoft.WindowsAzure.CloudConfigurationManager` oder der Klasse `System.Configuration.ConfigurationManager ` abrufen. Das Microsoft Azure-Konfigurations-Manager-Paket, das die `Microsoft.WindowsAzure.CloudConfigurationManager`-Klasse enthält, steht auf [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) zur Verfügung.

In diesem Beispiel wird dargestellt, wie Sie Ihre Anmeldeinformationen mithilfe der Klasse `CloudConfigurationManager` abrufen und dann mit der Klasse `CloudStorageAccount` kapseln. Fügen Sie den folgenden Code der `Main()`-Methode in "program.cs" hinzu:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### Programmgesteuertes Zugreifen auf die Dateifreigabe

Fügen Sie als Nächstes den folgenden Code der `Main()`-Methode nach dem oben angegebenen Code hinzu, um die Verbindungszeichenfolge abzurufen. Dieser Code ruft einen Verweis auf die zuvor erstellte Datei ab und gibt dessen Inhalt im Konsolenfenster an.

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        //Ensure that the file exists.
	        if (file.Exists())
	        {
	            //Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Führen Sie die Konsolenanwendung aus, um die Ausgabe zu sehen.

## Festlegen der maximalen Größe für eine Dateifreigabe

Ab Version 5.x der Azure Storage-Clientbibliothek können Sie das Kontingent (oder die maximale Größe) für eine Freigabe in Gigabyte festlegen. Durch Festlegen des Kontingents für eine Freigabe können Sie die Gesamtgröße der Dateien einschränken, die in der Freigabe gespeichert werden.

Überschreitet die Gesamtgröße der Dateien in der Freigabe das für die Freigabe festgelegte Kontingent, können die Clients weder die Größe von vorhandenen Dateien ändern noch neue Dateien erstellen, es sei denn, diese sind leer.

Im folgende Beispiel wird gezeigt, wie das Kontingent für eine vorhandene Dateifreigabe festgelegt wird.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
		//Specify the maximum size of the share, in GB.
	    share.Properties.Quota = 100;
	    share.SetProperties();
	}

Um den Wert eines vorhandenen Kontingents für die Freigabe zu ermitteln, rufen Sie die **FetchAttributes()**-Methode auf, um die Eigenschaften der Freigabe abzurufen.

## Generieren einer SAS für eine Datei oder Dateifreigabe

Ab Version 5.x der Azure Storage-Clientbibliothek können Sie eine SAS (Shared Access Signature) für eine Dateifreigabe oder für eine einzelne Datei generieren. Sie können auch eine SAS-Richtlinie für eine Dateifreigabe erstellen, um Shared Access Signatures zu verwalten. Erstellen einer SAS-Richtlinie wird empfohlen, weil sie eine Möglichkeit bietet, die SAS zu widerrufen, wenn diese gefährdet sein sollte.

Im folgenden Beispiel wird eine SAS-Richtlinie für eine Freigabe erstellt und die Richtlinie dann dazu verwendet, die Einschränkungen für eine SAS für eine Datei in der Freigabe bereitzustellen.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        //Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        //Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        //Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        //Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        //Create a new CloudFile object from the SAS, and write some text to the file. 
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

Weitere Informationen zum Erstellen und Verwenden von Shared Access Signatures finden Sie unter [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md) und [Erstellen und Verwenden einer SAS mit dem Blob-Dienst](storage-dotnet-shared-access-signature-part-2.md).

## Kopieren von Dateien

Ab Version 5.x der Azure Storage-Clientbibliothek können Sie eine Datei in eine andere Datei, eine Datei in ein BLOB oder ein Blob in eine Datei kopieren. Im Folgenden wird demonstriert, wie diese Kopiervorgänge programmgesteuert ausgeführt werden können.

Sie können auch AzCopy verwenden, um eine Datei in eine andere oder ein BLOB in eine Datei oder umgekehrt zu kopieren. Ausführliche Informationen zum Kopieren von Dateien mit AzCopy finden Sie unter [Verwenden von AzCopy mit Microsoft Azure Storage](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only).

> [AZURE.NOTE]Wenn Sie ein BLOB in eine Datei oder eine Datei in ein BLOB kopieren, müssen Sie eine SAS verwenden, um das Quellobjekt zu authentifizieren. Dies gilt selbst dann, wenn Sie innerhalb desselben Speicherkontos kopieren.

### Kopieren einer Datei in eine andere Datei

Im folgenden Beispiel wird eine Datei in eine andere Datei in derselben Freigabe kopiert. Weil bei diesem Kopiervorgang zwischen Dateien im selben Speicherkonto kopiert wird, können Sie die Gemeinsam verwendeter Schlüssel-Authentifizierung verwenden, um den Kopiervorgang auszuführen.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
        //Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        //Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        //Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            //Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            //Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                //Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                //Start the copy operation.
                destFile.StartCopy(sourceFile);

                //Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### Kopieren einer Datei in ein BLOB

Im folgenden Beispiel wird eine Datei erstellt und diese in ein BLOB im selben Speicherkonto kopiert. In dem Beispiel wird für die Quelldatei eine SAS erstellt, die der Dienst dazu verwendet, während des Kopiervorgangs den Zugriff auf die Quelldatei zu authentifizieren.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    //Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    //Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    //Create a SAS for the file that's valid for 24 hours.
    //Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    //to authenticate access to the source object, even if you are copying within the same 
    //storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        //Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    //Construct the URI to the source file, including the SAS token. 
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    //Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    //Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

Auf gleiche Weise können Sie ein BLOB in eine Datei kopieren. Wenn das Quellobjekt ein BLOB ist, erstellen Sie eine SAS, um den Zugriff auf dieses BLOB während des Kopiervorgangs zu authentifizieren.

## Verwenden von Dateispeicher mit Linux

Um eine Dateifreigabe aus Linux zu erstellen und zu verwalten, verwenden Sie die Azure-Befehlszeilenschnittstelle. Informationen zum Verwenden der Azure-Befehlszeilenschnittstelle mit Dateispeicher finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher](storage-azure-cli.md#create-and-manage-file-shares).

Sie können eine Azure-Dateifreigabe aus einem virtuellen Computer unter Linux bereitstellen. Wenn Sie Ihren virtuellen Azure-Computer erstellen, können Sie ein Linux-Image, das SMB 2.1 unterstützt, aus dem Azure-Imagekatalog angeben, z. B. die neueste Version von Ubuntu. Jede Linux-Distribution, die SMB 2.1 unterstützt, kann die Azure-Dateifreigabe einbinden.

Weitere Informationen dazu, wie eine Azure-Dateifreigabe unter Linux eingebunden wird, finden Sie unter [Freigegebener Speicher unter Linux mithilfe der Vorschau der Azure-Dateien - Teil 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) auf Channel 9.

## Nächste Schritte

Weitere Informationen zum Azure-Dateispeicher erhalten Sie über diese Links.

### Lernprogramme und Referenz

- [Referenz zur Storage-Clientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Referenz zur REST-API des Dateidiensts](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Verwenden von AzCopy mit Microsoft Azure Storage](storage-use-azcopy.md)
- [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher](storage-azure-cli.md)

### Blogbeiträge

- [Einführung in den Microsoft Azure-Dateidienst](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Beibehalten von Verbindungen zu Microsoft Azure-Dateien](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
 

<!---HONumber=August15_HO6-->