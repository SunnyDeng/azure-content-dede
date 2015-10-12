<properties
	pageTitle="Verwenden des Blob-Speichers mit PHP | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Azure-Blob-Dienst nutzen können, um Blobs hoch- und herunterzuladen, aufzulisten und zu löschen. Die Codebeispiele sind in PHP geschrieben."
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
	ms.date="09/01/2015"
	ms.author="tomfitz"/>

# Verwenden des Blob-Speichers mit PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des Azure-Blob-Diensts. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure-SDK für PHP][download]. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Blob-Dienst zugreift, ist das Verweisen auf Klassen im Azure-SDK für PHP aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

## Abrufen der Azure-Clientbibliotheken

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Konfigurieren der Anwendung für den Zugriff auf den Blob-Dienst

Zum Verwenden der Azure-Blob-Dienst-APIs müssen Sie Folgendes durchführen:

1. Verweisen Sie mithilfe der [require\_once][require_once]-Anweisung auf die Autoloaderdatei und
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServicesBuilder**-Klasse referenziert wird.

> [AZURE.NOTE]In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei `WindowsAzure.php` verweisen.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

## Einrichten einer Azure-Speicherverbindung

Um einen Azure-Blob-Dienstclient zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Blob-Dienst-Verbindungszeichenfolge lautet:

Für den Zugriff auf einen Livedienst:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Speicheremulator:

	UseDevelopmentStorage=true


Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

* die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
	* Standardmäßig verfügt sie über Unterstützung für eine externe Quelle – Umgebungsvariablen.
	* Sie können neue Quellen durch Erweitern der **ConnectionStringSource**-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## Erstellen eines Containers

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

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

	// Set container metadata.
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");

	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Durch den Aufruf von **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** wird der Zugriff auf die Container- und Blob-Daten über anonyme Anforderungen ermöglicht. Durch den Aufruf von **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** wird nur der Zugriff auf Blob-Daten über anonyme Anforderungen ermöglicht. Weitere Informationen zu Container-ACLs finden Sie im Thema zum [Festlegen der Container-ACL (REST-API)][container-acl].

Weitere Informationen zu Fehlercodes des Blob-Diensts finden Sie im Thema zu [Fehlercodes des Blob-Diensts][error-codes].

## Hochladen eines Blobs in einen Container

Um eine Datei als Blob hochzuladen, verwenden Sie die **BlobRestProxy->createBlockBlob**-Methode. Bei diesem Vorgang wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es vorhanden ist. Das Codebeispiel weiter unten nimmt an, dass der Container bereits erstellt wurde, und verwendet [fopen][fopen], um die Datei als Stream zu öffnen.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass im vorherigen Beispiel ein Blob als Stream hochgeladen wird. Ein Blob kann allerdings auch als Zeichenfolge hochgeladen werden, zum Beispiel mit der [file\_get\_contents][file_get_contents]-Funktion. Ändern Sie dazu im vorherigen Beispiel `$content = fopen("c:\myfile.txt", "r");` zu `$content = file_get_contents("c:\myfile.txt");`.

## Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzulisten, verwenden Sie die **BlobRestProxy->listBlobs**-Methode mit einem **foreach**-Loop, um das Ergebnis in einer Schleife zu durchlaufen. Der folgende Code zeigt den Namen der einzelnen Blobs als Ausgabe in einem Container und die einzelnen URIs an den Browser an.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## Herunterladen eines Blobs

Um einen Blob herunterzuladen, rufen Sie die **BlobRestProxy->getBlob**-Methode gefolgt von der **getContentStream**-Methode für das resultierende **GetBlobResult**-Objekt auf.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass in dem Beispiel oben ein Blob als Streamressource abgerufen wird (das Standardverhalten). Sie können allerdings die [stream\_get\_contents][stream-get-contents]-Funktion verwenden, um den zurückgegebenen Stream in eine Zeichenfolge zu konvertieren.

## Löschen eines Blobs

Um einen Blob zu löschen, geben Sie den Containernamen und den Blob-Namen an **BlobRestProxy->deleteBlob** weiter.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Löschen eines Blob-Containers

Um schließlich einen Blob-Container zu löschen, geben Sie den Containernamen an **BlobRestProxy->deleteContainer** weiter.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Azure-Blob-Diensts vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Weitere Informationen finden Sie in der MSDN-Referenz [Azure-Speicher](http://msdn.microsoft.com/library/azure/gg433040.aspx).
- Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/).
- Ein PHP-Block-Blob-Beispiel finden Sie unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Ein PHP-Seiten-Blob-Beispiel finden Sie unter <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

Weitere Informationen finden Sie außerdem im [PHP Developer Center](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and accessing data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!---HONumber=Oct15_HO1-->