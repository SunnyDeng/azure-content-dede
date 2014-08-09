<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="How to use blob storage (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="waltpo" manager="bjsmith" editor="mollybos" />

Verwenden des Blob-Diensts aus PHP
==================================

Dieses Handbuch demonstriert Ihnen das Durchführen allgemeiner Szenarien mithilfe des Azur-Blob-Dienste. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure-SDK für PHP](http://go.microsoft.com/fwlink/?LinkID=252473). Die behandelten Szenarien umfassen das **Hochladen**, **Aufführen**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Blob-Speicher?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#CreateAccount)
-   [Erstellen einer PHP-Anwendung](#CreateApplication)
-   [Konfigurieren der Anwendung für den Zugriff auf den Blob-Dienst](#ConfigureStorage)
-   [Einrichten einer Azure-Speicherverbindung](#ConnectionString)
-   [Vorgehensweise: Erstellen eines Containers](#CreateContainer)
-   [Vorgehensweise: Hochladen eines Blobs in einen Container](#UploadBlob)
-   [Vorgehensweise: Auflisten der Blobs in einem Container](#ListBlobs)
-   [Vorgehensweise: Herunterladen eines Blobs](#DownloadBlob)
-   [Vorgehensweise: Löschen eines Blobs](#DeleteBlob)
-   [Vorgehensweise: Löschen eines Blob-Containers](#DeleteContainer)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer PHP-Anwendung
-----------------------------

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Blob-Dienst zugreift, ist das Referenzieren von Klassen im Azure-SDK für PHP aus dem Code heraus. Sie können beliebige Entwicklungstools zum Erstellen Ihrer Anwendung verwenden, einschließlich Editor.

In diesem Handbuch verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufzurufen.

Abrufen der Azure-Clientbibliotheken
------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Konfigurieren der Anwendung für den Zugriff auf den Blob-Dienst
---------------------------------------------------------------

Zum Verwenden der Azure-Blob-Dienst-APIs müssen Sie Folgendes durchführen:

1.  Verweisen Sie mithilfe der [require\_once](http://php.net/require_once)-Anweisung auf die Autoloaderdatei und
2.  Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServicesBuilder**-Klasse referenziert wird.

> [WACOM.NOTE] In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Falls Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei `WindowsAzure.php` verweisen.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt, aber nur die Klassen, die für die Ausführung des Beispiels erforderlich sind, werden referenziert.

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Um einen Azure-Blob-Dienstclient zu instanzieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Blob-Dienst-Verbindungszeichenfolge lautet:

Für den Zugriff auf einen Livedienst:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

    UseDevelopmentStorage=true

Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

-   Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
-   den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
    -   Standardmäßig verfügt sie über Unterstützung für eine externe Quelle – Umgebungsvariablen.
    -   Sie können neue Quellen durch Erweitern der **ConnectionStringSource**-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

Vorgehensweise: Erstellen eines Containers
------------------------------------------

Mithilfe eines **BlobRestProxy**-Objekts können Sie einen Blob-Container mit der **createContainer**-Methode erstellen. Wenn Sie einen Container erstellen, können Sie Optionen für den Container festlegen, was allerdings nicht erforderlich ist. (Das Beispiel unten zeigt, wie Sie die Container-ACL und Containermetadaten festlegen.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Blob\Models\CreateContainerOptions;
    use WindowsAzure\Blob\Models\PublicAccessType;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions(); 

    // Set public access policy. Possible values are 
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:     
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
    // anonymous request.
    // If this value is not specified in the request, container data is 
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Durch den Aufruf von **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** wird der Zugriff auf die Container- und Blob-Daten über anonyme Anforderungen ermöglicht. Durch den Aufruf von **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** wird nur der Zugriff auf Blob-Daten über anonyme Anforderungen ermöglicht. Weitere Informationen zu Container-ACLs finden Sie im Thema zum [Festlegen der Container-ACL (REST-API)](http://msdn.microsoft.com/de-de/library/windowsazure/dd179391.aspx).

Weitere Informationen zu Fehlercodes des Blob-Diensts finden Sie im Thema zu [Fehlercodes des Blob-Diensts](http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx).

Vorgehensweise: Hochladen eines Blobs in einen Container
--------------------------------------------------------

Um eine Datei als Blob hochzuladen, verwenden Sie die **BlobRestProxy-\>createBlockBlob**-Methode. Dieser Vorgang erstellt den Blob, falls er nicht vorhanden ist, oder überschreibt ihn andernfalls. Das Codebeispiel weiter unten nimmt an, dass der Container bereits erstellt wurde, und verwendet [fopen](http://www.php.net/fopen), um die Datei als Stream zu öffnen.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Beachten Sie, dass in dem Beispiel oben ein Blob als Stream hochgeladen wird. Ein Blob kann allerdings auch als Zeichenfolge hochgeladen werden, zum Beispiel mit der [file\_get\_contents](http://php.net/file_get_contents)-Funktion. Ändern Sie dazu `$content = fopen("c:\myfile.txt", "r");` im Beispiel oben in `$content = file_get_contents("c:\myfile.txt");`.

Vorgehensweise: Auflisten der Blobs in einem Container
------------------------------------------------------

Um die Blobs in einem Container aufzulisten, verwenden Sie die **BlobRestProxy-\>listBlobs**-Methode mit einem **foreach**-Loop, um das Ergebnis in einer Schleife zu durchlaufen. Der folgende Code gibt den Namen der einzelnen Blobs in einem Container und die einzelnen URIs an den Browser aus.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();
        
        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Herunterladen eines Blobs
-----------------------------------------

Um einen Blob herunterzuladen, rufen Sie die **BlobRestProxy-\>getBlob**-Methode gefolgt von der **getContentStream**-Methode für das resultierende **GetBlobResult**-Objekt auf.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Beachten Sie, dass in dem Beispiel oben ein Blob als Streamressource abgerufen wird (das Standardverhalten). Sie können allerdings die [stream\_get\_contents](http://www.php.net/stream_get_contents)-Funktion verwenden, um den zurückgegebenen Stream in eine Zeichenfolge zu konvertieren.

Vorgehensweise: Löschen eines Blobs
-----------------------------------

Um einen Blob zu löschen, geben Sie den Containernamen und den Blob-Namen an **BlobRestProxy-\>deleteBlob** weiter.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Löschen eines Blob-Containers
---------------------------------------------

Um schließlich einen Blob-Container zu löschen, geben Sie den Containernamen an **BlobRestProxy-\>deleteContainer** weiter.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Nächste Schritte
----------------

Da Sie jetzt die Grundlagen des Azure-Blob-Diensts erlernt haben, folgen Sie diesem Link, um zu erfahren, wie Sie komplexere Speicheraufgaben ausführen können.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx)
- Besuchen Sie den Blog des Azure-Speicherteams: <http://blogs.msdn.com/b/windowsazurestorage/>
- Ein PHP-Block-Blob-Beispiel finden Sie unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Ein PHP-Seiten-Blob-Beispiel finden Sie unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

