<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service (2.0)" pageTitle="How to use blob storage in .NET | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob .NET, Azure blob C#, Azure blob C#" description="Learn how to use the Azure blob service to upload,,download, list, and delete blob content. Samples are written in C#." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Azure Blob Storage Service in .NET" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden des Windows Azure Blob-Speicherdiensts in .NET
========================================================

[Version 1.7](/de-de/develop/net/how-to-guides/blob-storage-v17/ "Version 1.7") [Version 2.0](/de-de/develop/net/how-to-guides/blob-storage/ "Version 2.0")

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Windows Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist Blob-Speicher?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Einrichten einer Verbindungszeichenfolge für den Speicher](#setup-connection-string)
-   [Vorgehensweise: Programmgesteuertes Zugreifen auf Blobspeicher](#configure-access)
-   [Vorgehensweise: Erstellen eines Containers](#create-container)
-   [Vorgehensweise: Hochladen eines Blobs in einen Container](#upload-blob)
-   [Vorgehensweise: Auflisten der Blobs in einem Container](#list-blob)
-   [Vorgehensweise: Herunterladen von Blobs](#download-blobs)
-   [Vorgehensweise: Löschen von Blobs](#delete-blobs)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Erstellen eines KontosErstellen eines Azure-Speicherkontos
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Einrichten einer VerbindungszeichenfolgeEinrichten einer Verbindungszeichenfolge für den Speicher
-------------------------------------------------------------------------------------------------

Die Azure .NET Speicher-API unterstützt Speicher-Verbindungszeichenfolgen zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Sie können Ihre Speicher-Verbindungszeichenfolge in einer Konfigurationsdatei angeben, statt sie hart zu codieren:

-   Bei der Verwendung von Azure-Cloud-Diensten sollten Sie Ihre Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems speichern (`*.csdef`- und `*.cscfg`-Dateien).
-   Bei der Verwendung von Azure-Websites oder virtuellen Computern in Azure sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B. `web.config`-Datei).

In beiden Fällen können Sie die Verbindungszeichenfolge mit der `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in diesem Leitfaden gezeigt.

### Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Clouddiensten

Azure-Cloud-Dienst-Projekte bieten einen einzigartigen Dienstkonfigurationsmechanismus, mit dem Sie Ihre Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch ändern können, ohne Ihre Anwendung neu bereitstellen zu müssen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner **Roles** des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf Ihre Webrolle oder Workerrolle, und klicken Sie dann auf **Eigenschaften**.
     ![Eigenschaften für eine Clouddienstrolle in Visual Studio auswählen](./media/storage-dotnet-how-to-use-blobs-17/blob5.png)

2.  Klicken Sie auf die Registerkarte **Einstellungen** und anschließend auf die Schaltfläche **Einstellung hinzufügen**.
     ![Clouddiensteinstellung in Visual Studio hinzufügen](./media/storage-dotnet-how-to-use-blobs-17/blob6.png)

    In der Einstellungstabelle wird daraufhin der neue Eintrag **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Typ** des neuen Eintrags **Setting1** die Option **Verbindungszeichenfolge** aus.
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag **Setting1**. Das Dialogfeld **Verbindungszeichenfolge für das Speicherkonto** wird geöffnet.

5.  Wählen Sie aus, ob das Ziel der Speicheremulator (simulierter Windows Azure-Speicher auf dem lokalen Computer) oder ein Speicherkonto in der Cloud sein soll. Der Code in diesem Leitfaden kann mit beiden Optionen verwendet werden. Geben Sie den Wert für **Primärer Zugriffsschlüssel** ein, den Sie an früherer Stelle in diesem Lernprogramm kopiert haben, wenn Sie Blob-Daten in dem Speicherkonto speichern möchten, das zuvor in Azure erstellt wurde.
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Ändern Sie den Eintrag **Name** von **Setting1** in einen benutzerfreundlicheren Namen wie **StorageConnectionString**. Sie werden später in diesem Leitfaden auf diese Verbindungszeichenfolge verweisen.
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden (z. B. `web.config`). Sie speichern die Verbindungszeichenfolge mit dem Element `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie unter [Konfigurieren von Windows Azure-Verbindungszeichenfolgen](http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx).

Sie sind nun bereit, um die einzelnen Aufgaben in dieser Anleitung auszuführen.

Programmgesteuerter ZugriffVorgehensweise: Programmgesteuertes Zugreifen auf Blobspeicher
-----------------------------------------------------------------------------------------

Fügen Sie die folgenden Namespace-Deklarationen zum Anfang aller C\#-Dateien hinzu, in denen Sie programmgesteuert auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Sie können Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudStorageAccount** und dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Der Typ **CloudBlobClient** ermöglicht den Abruf von Objekten, die im Blob-Speicherdienst gespeicherte Container und Blobs darstellen. Mit dem folgenden Code wird unter Rückgriff auf das oben abgerufene Speicherkontoobjekt ein **CloudBlobClient**-Objekt erstellt:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Erstellen eines ContainersVorgehensweise: Erstellen eines Containers
--------------------------------------------------------------------

Alle Speicherblobs befinden sich in einem Container. Mithilfe des **CloudBlobClient**-Objekts können Sie einen Verweis auf den Container abrufen, den Sie verwenden möchten. Sie können den Container erstellen, wenn er nicht vorhanden ist:

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen Container abrufen. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Container erstellen, wenn er noch nicht vorhanden ist.
    container.CreateIfNotExist();

Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes öffentlich machen:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob });

Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

Hochladen in einen ContainerVorgehensweise: Hochladen eines Blobs in einen Container
------------------------------------------------------------------------------------

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blobverweises, um eine Datei in einen Blob hochzuladen. Sobald Sie über einen Blobverweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **UploadFromStream**-Methode im Blobverweis aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Das nachfolgende Codebeispiel zeigt diesen Vorgang. Dabei wird davon ausgegangen, dass der Container bereits erstellt wurde.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "myblob" abrufen.
    CloudBlob blob = container.GetBlobReference("myblob");

    // Blob "myblob" erstellen oder durch Inhalt aus einer lokalen Datei überschreiben.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

Auflisten der Blobs in einem ContainerVorgehensweise: Auflisten der Blobs in einem Container
--------------------------------------------------------------------------------------------

Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach können Sie mit der **ListBlobs**-Methode des Containers die darin vorhandenen Blobs abrufen. Im folgenden Code wird gezeigt, wie der **Uri** der einzelnen Blobs in einem Container abgerufen und ausgegeben wird:

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Schleife über Blobs im Container ausführen und URI für die einzelnen Blobs ausgeben.
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

Beim Blobdienst wird ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren. Beispielsweise verfügen Sie über einen Container mit der Bezeichnung "photos", in den Sie Blobs mit der Bezeichnung "rootphoto1", "2010/photo1", "2010/photo2" und "2011/photo1" hochladen. Daraufhin werden die Verzeichnisse "2010" und "2011" im Container "photos" erstellt. Wenn Sie **ListBlobs** für den Container "photos" aufrufen, enthält die zurückgegebene Sammlung **CloudBlobDirectory**- und **CloudBlob**-Objekte, die die auf der obersten Ebene enthaltenen Verzeichnisse und Blobs darstellen. In diesem Fall werden die Verzeichnisse "2010" und "2011" sowie das Foto "rootphoto1" zurückgegeben. Sie können optional eine neue **BlobRequestOptions**-Klasse übergeben, wobei **UseFlatBlobListing** auf **true** festgelegt ist. Daraufhin würde jeder Blob unabhängig vom jeweiligen Verzeichnis zurückgegeben. Weitere Informationen finden Sie unter [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs].

Herunterladen von BlobsVorgehensweise: Herunterladen von Blobs
--------------------------------------------------------------

Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab. Im folgenden Beispiel wird die **DownloadToStream**-Methode verwendet, um den Blobinhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann. Sie können auch die Methode **DownloadToFile**, **DownloadByteArray** oder **DownloadText** des Blobs aufrufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "myblob" abrufen.
    CloudBlob blob = container.GetBlobReference("myblob");

    // Blobinhalt auf einem Datenträger speichern.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

Löschen von BlobsVorgehensweise: Löschen von Blobs
--------------------------------------------------

Um ein Blob zu löschen, rufen Sie einen Blobverweis ab, und rufen Sie dann die **Delete**-Methode dafür auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Blobclient erstellen.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Verweis auf einen zuvor erstellten Container abrufen.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Verweis auf ein Blob mit dem Namen "myblob" abrufen.
    CloudBlob blob = container.GetBlobReference("myblob");

    // Blob löschen.
    blob.Delete(); 

Nächste SchritteNächste Schritte
--------------------------------

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Vollständige Informationen zu verfügbaren APIs finden Sie in der Blobdienst-Referenzdokumentation:
    -   [Referenz zur .NET-Clientbibliothek](http://msdn.microsoft.com/de-de/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST-API-Referenz](http://msdn.microsoft.com/de-de/library/windowsazure/dd179355)
-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx).
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Tabellenspeicher](/de-de/develop/net/how-to-guides/table-services/) zum Speichern strukturierter Daten.
    -   Verwenden Sie eine [SQL-Datenbank](/de-de/develop/net/how-to-guides/sql-database/) zum Speichern relationaler Daten.

