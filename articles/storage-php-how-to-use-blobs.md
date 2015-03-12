<properties 
	pageTitle="Verwenden des Blob-Speichers (PHP) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Blob-Dienst nutzen können, um Blobs hoch- und runterzuladen, aufzulisten und zu löschen. Die Codebeispiele sind in PHP geschrieben." 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

#Verwenden des Blob-Diensts aus PHP

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des Blob-Diensts in Azure. Die Beispiele sind in PHP geschrieben und verwenden die [Azure SDK für PHP] [download]. Die behandelten Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie unter [Nächste Schritte](#NextSteps).

##Inhaltsverzeichnis

* [Was ist ein Blob-Speicher](#what-is)
* [Konzepte](#concepts)
* [Erstellen eines Azure-Speicherkontos](#CreateAccount)
* [Erstellen einer PHP-Anwendung](#CreateApplication)
* [Konfigurieren der Anwendung, um auf den Blob-Dienst zuzugreifen](#ConfigureStorage)
* [Einrichten einer Azure-Speicherverbindung](#ConnectionString)
* [Vorgehensweise: Erstellen eines Containers](#CreateContainer)
* [Vorgehensweise: Hochladen eines Blobs in einen Container](#UploadBlob)
* [Vorgehensweise: Auflisten der Blobs in einem Container](#ListBlobs)
* [Vorgehensweise: Herunterladen eines Blobs](#DownloadBlob)
* [Vorgehensweise: Löschen eines Blobs](#DeleteBlob)
* [Vorgehensweise: Löschen eines Blobcontainers](#DeleteContainer)
* [Nächste Schritte](#NextSteps)

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Erstellen eines Azure-Speicherkontos</h2>

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Erstellen einer PHP-Anwendung</h2>

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Blob-Dienst zugreift, ist das Referenzieren von Klassen im Azure-SDK für PHP aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

<h2><a id="GetClientLibrary"></a>Zu den Azure-Clientbibliotheken</h2>

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Konfigurieren der Anwendung, um auf den Blob-Dienst zuzugreifen</h2>

Zum Verwenden der Azure-Blob-Dienst-APIs müssen Sie Folgendes durchführen:

1. Verweisen Sie mithilfe der [require_once][require_once]-Anweisung auf die Autoloaderdatei und
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei integriert werden, und auf die **ServicesBuilder**-Klasse verwiesen werden kann.

> [AZURE.NOTE]
> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, verweisen Sie auf die `WindowsAzure.php` Autoladerdatei.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt, jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

<h2><a id="ConnectionString"></a>Einrichten einer Azure-Speicherverbindung</h2>

Um einen Azure-Blob-Dienstclient zu instanzieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Blob-Dienst-Verbindungszeichenfolge lautet:

Für den Zugriff auf einen Livedienst:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

	UseDevelopmentStorage=true


Um einen Azure-Dienstclient zu erstellen, verwenden Sie die **ServicesBuilder**-Klasse. Sie können:

* Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* verwenden Sie den **CloudConfigurationManager (CCM)**, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
	* Standardmäßig wird eine externe Quelle unterstützt. - Umgebungsvariablen
	* Neue Datenquellen können über die Erweiterung der **ConnectionStringSource**-Klasse hinzugefügt werden.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Vorgehensweise: Erstellen eines Containers</h2>

Ein **BlobRestProxy**-Objekt ermöglicht Ihnen das Erstellen eines Blobcontainers mit der **createContainer**-Methode. Wenn Sie einen Container erstellen, können Sie Optionen für den Container festlegen, was allerdings nicht erforderlich ist. (Das Beispiel unten zeigt, wie Sie die Container-ACL und Containermetadaten festlegen.)

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
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Wird **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** aufgerufen, kann über Anonyme Anforderungen auf die Container- und Blobdaten zugegriffen werden. Wird **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** aufgerufen, kann über Anonyme Anforderungen lediglich auf die Blobdaten zugegriffen werden. Weitere Informationen zu Container-ACLs finden Sie unter [Set Container ACL (REST-API)][container-acl].

Weitere Informationen zu Fehlercodes des Blob-Dienstes finden Sie unter [Fehlercodes Blob-Dienst][error-codes].

<h2><a id="UploadBlob"></a>Vorgehensweise: Hochladen eines Blobs in einen Container</h2>

Um eine Datei als Blob hochzuladen, verwenden Sie die **BlobRestProxy->createBlockBlob**-Methode. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Codebeispiel wird davon ausgegangen, dass der Container bereits erstellt wurde und [fopen][fopen] verwendet, um die Datei als Stream zu öffnen.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass in dem Beispiel oben ein Blob als Stream hochgeladen wird. Allerdings kann ein Blob auch als Zeichenfolge hochgeladen werden, z. B. über die [File\_get\_contents][file_get_contents]-Funktion. Ändern Sie dazu `$content = fopen("c:\myfile.txt", "r");` im Beispiel oben in `$content = file_get_contents("c:\myfile.txt");`.

<h2><a id="ListBlobs"></a>Vorgehensweise: Auflisten der Blobs in einem Container</h2>

Verwenden Sie zum Auflisten der Blobs in einem Container die **BlobRestProxy->listBlobs**-Methode mit einer **Foreach**-Schleife, um das Ergebnis durchzuschleifen. Der folgende Code gibt den Namen der einzelnen Blobs in einem Container und die einzelnen URIs an den Browser aus.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
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
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>Vorgehensweise: Herunterladen eines Blobs</h2>

Um einen Blob herunterzuladen, rufen Sie zunächst die **BlobRestProxy->getBlob**-Methode auf. Rufen Sie dann die **getContentStream**-Methode für das resultierende **GetBlobResult**-Objekt auf.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass in dem Beispiel oben ein Blob als Streamressource abgerufen wird (das Standardverhalten). Sie können jedoch die [stream\_get\_contents][stream-get-contents]-Funktion nutzen, um den zurückgegebenen Stream in eine Zeichenfolge zu konvertieren.

<h2><a id="DeleteBlob"></a>Vorgehensweise: Löschen eines Blobs</h2>

Um ein Blob zu löschen, übergeben Sie den Containernamen und Blobnamen an **BlobRestProxy->deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>Vorgehensweise: Löschen eines Blobcontainers</h2>

Um einen Blobcontainer zu löschen, übergeben Sie abschließend den Containernamen an **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>Nächste Schritte</h2>

Da Sie jetzt die Grundlagen des Azure-Blob-Diensts erlernt haben, folgen Sie diesem Link, um zu erfahren, wie Sie komplexere Speicheraufgaben ausführen können.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure] []
- Besuchen Sie den Blog des Azure-Speicherteams: <http://blogs.msdn.com/b/windowsazurestorage/>
- Siehe das PHP-Block Blobbeispiel unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Siehe das PHP-Seite Blobbeispiel unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[Download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
<!--HONumber=42-->
