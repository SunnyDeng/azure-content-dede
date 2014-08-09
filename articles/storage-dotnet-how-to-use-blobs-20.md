<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Microsoft Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use the Windows Azure blob service to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use the Windows Azure Blob Storage Service in .NET" authors="tamram" />

# Verwenden von Blob-Speicher von .NET

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die behandelten Szenarien umfassen das **Hochladen**,
**Aufführen**, **Herunterladen** und **Löschen** von Blobs. Weitere
Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## Inhaltsverzeichnis

* [Was ist Blob-Speicher?](#what-is)
* [Konzepte](#concepts)
* [Erstellen eines Azure-Speicherkontos](#create-account)
* [Einrichten einer
  Speicherverbindungszeichenfolge](#setup-connection-string)
* [Gewusst wie: Programmgesteuertes Zugreifen auf
  Blobspeicher](#configure-access)
* [Gewusst wie: Erstellen eines Containers](#create-container)
* [Gewusst wie: Hochladen eines Blobs in einen Container](#upload-blob)
* [Gewusst wie: Aufführen der Blobs in einem Container](#list-blob)
* [Gewusst wie: Herunterladen von Blobs](#download-blobs)
* [Gewusst wie: Löschen von Blobs](#delete-blobs)
* [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span  class="short-header">Erstellen eines Kontos</span>Erstellen eines Azure-Speicherkontos

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span  class="short-header">Einrichten einer Verbindungszeichenfolge</span>Einrichten einer Speicherverbindungszeichenfolge

Die Azure-Speicherclientbibliothek für .NET unterstützt die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Sie können die Speicherverbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

* Bei der Verwendung von Azure-Clouddiensten empfiehlt es sich, die
  Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems
  (Dateien `*.csdef` und `*.cscfg`) zu speichern.
* Bei der Verwendung von Azure-Websites und virtuellen Azure-Computern
  oder der Erstellung von .NET-Anwendungen, die nicht in Azure
  ausgeführt werden sollen, empfiehlt es sich, die
  Verbindungszeichenfolge mit dem .NET-Konfigurationssystem (z. B. der
  Datei `web.config` oder `app.config`) zu speichern.

Der Abruf der Verbindungszeichenfolge wird weiter unten in diesem Leitfaden gezeigt.

### Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Clouddiensten

Die Dienstkonfiguration ist in Azure-Clouddienstprojekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner
    **Rollen** des Azure-Bereitstellungsprojekts mit der rechten
    Maustaste auf Ihre Webrolle oder Workerrolle, und klicken Sie dann
    auf **Eigenschaften**.  
     ![Eigenschaften für eine Clouddienstrolle in Visual Studio
    auswählen](./media/storage-dotnet-how-to-use-blobs-20/blob5.png)

2.  Klicken Sie auf die Registerkarte **Einstellungen**, und wählen Sie
    die Schaltfläche **Einstellung hinzufügen** aus.  
     ![Clouddiensteinstellung in Visual Studio
    hinzufügen](./media/storage-dotnet-how-to-use-blobs-20/blob6.png)
    
    In der Einstellungstabelle wird daraufhin der neue Eintrag
    **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Typ** des neuen Eintrags
    **Setting1** die Option **Verbindungszeichenfolge** aus.  
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag
    **Setting1**. Das Dialogfeld **Verbindungszeichenfolge für das
    Speicherkonto** wird geöffnet.

5.  Wählen Sie aus, ob das Ziel der Speicheremulator (simulierter
    Azure-Speicher auf dem lokalen Computer) oder ein Speicherkonto in
    der Cloud sein soll. Der Code in diesem Leitfaden kann mit beiden
    Optionen verwendet werden. Geben Sie den Wert **Primärer
    Zugriffsschlüssel** ein, den Sie an früherer Stelle in diesem
    Lernprogramm kopiert haben, wenn Sie Blobdaten in dem Speicherkonto
    speichern möchten, das zuvor in Azure erstellt wurde.   
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Ändern Sie den Eintrag **Name** von **Setting1** in einen
    benutzerfreundlicheren Namen wie **StorageConnectionString**. Sie
    verweisen später im Code in diesem Leitfaden auf diese
    Verbindungszeichenfolge.  
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Konfigurieren der Verbindungszeichenfolge in der .NET-Konfiguration

Wenn Sie eine Anwendung schreiben, bei der es sich nicht um einen Azure-Clouddienst handelt (siehe vorheriger Abschnitt), empfiehlt es sich, das .NET-Konfigurationssystem (z. B. `web.config` oder
`app.config`) zu verwenden. Dies umfasst Azure-Websites oder virtuelle
Azure-Computer sowie Anwendungen, die für die Ausführung außerhalb von Azure konzipiert sind. Verwenden Sie folgenden Code, um die Verbindungszeichenfolge mit dem Element `<a ppSettings>` zu speichern:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie
unter [Konfigurieren von Windows Azure-Verbindungszeichenfolgen][1].

 Sie sind nun bereit für die Gewusst-wie-Aufgaben in diesem Leitfaden.

## <a name="configure-access"> </a><span  class="short-header">Programmgesteuerter Zugriff</span>Gewusst wie: Programmgesteuertes Zugreifen auf Blobspeicher

### Abrufen der Assembly

 Mithilfe von NuGet können Sie die
`Microsoft.WindowsAzure.Storage.dll`-Assembly abrufen. Klicken Sie im
**Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt,
und wählen Sie **Manage NuGet Packages** aus. Suchen Sie online nach
"WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das
Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

 `Microsoft.WindowsAzure.Storage.dll` ist auch im Azure SDK für .NET
enthalten, das aus dem [.NET Developer Center][2] heruntergeladen werden kann. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` installiert.

### Namespace-Deklarationen

 Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf
Azure-Speicher zugreifen möchten, die folgenden Namespace-Deklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

 Stellen Sie sicher, dass auf die
`Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge

 Sie können Ihre Speicherkontoinformationen mit dem Typ
**CloudStorageAccount** darstellen. Wenn Sie eine Azure-Projektvorlage
verwenden und/oder über einen Verweis auf
"Microsoft.WindowsAzure.CloudConfigurationManager" verfügen, können Sie
Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Wenn Sie eine Anwendung ohne einen Verweis auf
"Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und die
Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit
**ConfigurationManager** abrufen. Sie müssen einen Verweis auf
"System.Configuration.dll" auf Ihr Projekt und eine andere
Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 Der Typ **CloudBlobClient** ermöglicht den Abruf von Objekten, die im
Blob-Speicherdienst gespeicherte Container und Blobs darstellen. Mit dem folgenden Code wird unter Rückgriff auf das oben abgerufene Speicherkontoobjekt ein **CloudBlobClient**-Objekt erstellt:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib-Abhängigkeiten

 ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden
durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst. Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird
über Ihr Codeprojekt durch NuGet darauf verwiesen. Die spezifischen
ODataLib-Pakete sind [OData][3], [Edm][4] und [Spatial][5].

## <a name="create-container"> </a><span  class="short-header">Erstellen eines Containers</span>Gewusst wie: Erstellen eines Containers

 Alle Speicherblobs befinden sich in einem Container. Mithilfe des
**CloudBlobClient**-Objekts können Sie einen Verweis auf den Container
abrufen, den Sie verwenden möchten. Sie können den Container erstellen, wenn er nicht vorhanden ist:

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
Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes öffentlich machen:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob });

Jede Person im Internet kann Blobs in einem öffentlichen Container
anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie
über den entsprechenden Zugriffsschlüssel verfügen.

 <h2> <a name="upload-blob"> </a><span  class="short-header">Hochladen in einen Container</span>Gewusst wie: Hochladen eines Blobs in einen Container</h2>


 Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs. In den
meisten Fällen wird die Verwendung von Blockblobs empfohlen.

 Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen
eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen Blobverweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **UploadFromStream**-Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

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

## <a name="list-blob"> </a><span  class="short-header">Aufführen der Blobs in einem Container</span>Gewusst wie: Aufführen der Blobs in einem Container

 Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen
Containerverweis abrufen. Danach können Sie mit der
**ListBlobs**-Methode des Containers die Blobs und/oder darin
befindlichen Verzeichnisse abrufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes
**IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein
**CloudBlockBlob**-, **CloudPageBlob**- oder
**CloudBlobDirectory**-Objekt umwandeln. Wenn der Typ unbekannt ist,
können Sie eine Typenüberprüfung durchführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Im folgenden Code wird gezeigt, wie der URI der einzelnen Elemente im Container `photos` abgerufen und ausgegeben wird:

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

 Wie oben gezeigt, wird beim Blobdienst ebenfalls das Konzept von
Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren. Beachten Sie beispielsweise den folgenden Satz von Blockblobs in einem Container mit dem Namen `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

 Wenn Sie **ListBlobs** für den Container "photos" aufrufen (wie im
Beispiel oben), enthält die zurückgegebene Sammlung
**CloudBlobDirectory**- und **CloudBlockBlob**-Objekte, die die auf der
obersten Ebene enthaltenen Verzeichnisse und Blobs darstellen. Die Ausgabe würde folgendermaßen aussehen:

    Directory: https://<a ccountname>.blob.core.windows.net/photos/2010/
    Directory: https://<a ccountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 Optional können Sie für den **UseFlatBlobListing**-Parameter der
**ListBlobs**-Methode die Option **true** festlegen. Daraufhin würde
jeder Blob unabhängig vom jeweiligen Verzeichnis als **CloudBlockBlob** zurückgegeben. Der Aufruf für **ListBlobs** sähe folgendermaßen aus:

    // Schleife über Elemente im Container ausführen und Länge und URI ausgeben.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

 Das Ergebnis sähe folgendermaßen aus:

    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<a ccountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 Weitere Informationen finden Sie unter
[CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a><span  class="short-header">Herunterladen von Blobs</span>Gewusst wie: Herunterladen von Blobs

 Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab, und
rufen Sie dann die **DownloadToStream**-Methode auf. Im folgenden Beispiel wird die **DownloadToStream**-Methode verwendet, um den Blobinhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

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

 Sie können auch die **DownloadToStream**-Methode verwenden, um den
Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

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

## <a name="delete-blobs"> </a><span  class="short-header">Löschen von Blobs</span>Gewusst wie: Löschen von Blobs

 Um ein Blob zu löschen, rufen Sie zuerst einen Blobverweis ab, und rufen
Sie dann die **Delete**-Methode dafür auf.

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

## <a name="next-steps"></a><span  class="short-header">Nächste Schritte</span>Nächste Schritte

 Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut
gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

 *  Vollständige Informationen zu verfügbaren APIs finden Sie in der
  Blobdienst-Referenzdokumentation:
  *  [Referenz zur Speicherclientbibliothek für .NET][6]
  *  [REST-API-Referenz][7]
  

*  Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure-Speicher
  finden Sie unter [Speicher][8].
*  Weitere Informationen zu zusätzlichen Optionen für das Speichern von
  Daten in Azure finden Sie in den anderen Featureleitfäden.
  *  Verwenden Sie
    [Tabellenspeicher](/de-de/develop/net/how-to-guides/table-services/) zum Speichern strukturierter Daten.
  *  Verwenden Sie eine
    [SQL-Datenbank](/de-de/develop/net/how-to-guides/sql-database/) zum Speichern relationaler Daten.
  


 </appsettings>



[1]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/de-de/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/de-de/library/windowsazure/dn495001(v=azure.10).aspx
[7]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179355
[8]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx