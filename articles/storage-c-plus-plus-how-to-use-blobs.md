<properties 
    pageTitle="Verwenden von Blob-Speicher (C++) | Microsoft Azure" 
    description="Erfahren Sie, wie Sie den Blob-Speicherdienst in Azure verwenden. Die Beispiele sind in C++ geschrieben." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="tamram"/>

# Verwenden des Blob-Speichers mit C++  

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Übersicht
In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C++ geschrieben und greifen auf die [Azure-Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/blob/v0.5.0-preview/README.md) zurück. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs.

>[AZURE.NOTE]Diese Anleitung gilt für die Azure-Speicherclientbibliothek für C++ in der Version 0.5.0 und höher. Die empfohlene Version ist Speicherclientbibliothek 0.5.0, die über [NuGet](http://www.nuget.org/packages/wastorage) oder [GitHub](https://github.com/) verfügbar ist.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Erstellen einer C++-Anwendung
In diesem Handbuch werden Sie Speicherfunktionen verwenden, die innerhalb einer C++-Anwendung ausgeführt werden können.

Dafür müssen Sie die Azure-Speicherclientbibliothek für C++ installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen.

Zum Installieren der Azure-Speicherclientbibliothek für C++ können Sie die folgenden Methoden verwenden:

-	**Linux:** Befolgen Sie die Anweisungen auf der Seite [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) (in englischer Sprache).  
-	**Windows:** Klicken Sie in Visual Studio auf **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole**. Geben Sie im Fenster der [NuGet-Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein, und drücken Sie die **EINGABETASTE**:  

		Install-Package wastorage -Pre

## Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher  
Fügen Sie die folgenden "include"-Anweisungen am Anfang der C++-Datei hinzu, um die Stellen anzugeben, an denen Azure-Speicher-APIs auf Blobs zugreifen sollen:

	#include "was/storage_account.h"
	#include "was/blob.h"

## Einrichten einer Azure-Speicherverbindungszeichenfolge
Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten dargestellten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName*- und *AccountKey*-Werte eingegeben werden. Weitere Informationen über Speicherkonten und Zugriffsschlüssel finden Sie unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md). Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Zum Testen der Anwendung auf Ihrem lokalen Windows-Computer können Sie den [Microsoft Azure-Speicheremulator](https://msdn.microsoft.com/library/azure/hh403989.aspx) verwenden, der mit dem [Azure SDK](http://azure.microsoft.com/downloads/) installiert wird. Der Speicheremulator ist ein Dienstprogramm, das die in Azure verfügbaren Blob-, Warteschlangen- und Tabellen-Dienste auf dem lokalen Entwicklungscomputer simuliert. Im folgenden Beispiel wird dargestellt, wie Sie ein statisches Feld zur Übergabe der Verbindungszeichenfolge an den lokalen Speicheremulator deklarieren:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Wählen Sie zum Starten des Azure-Speicheremulators die Schaltfläche **Start** aus, oder drücken Sie die **Windows-Taste**. Geben Sie **Azure-Speicheremulator** ein, und wählen Sie den **Microsoft Azure-Speicheremulator** aus der Liste der Anwendungen aus.

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## Abrufen der Verbindungszeichenfolge
Sie können Ihre Speicherkontoinformationen mit der Klasse **cloud_storage_account** darstellen. Verwenden Sie zum Abrufen von Speicherkontoinformationen aus der Speicher-Verbindungszeichenfolge die **parse**-Methode.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Rufen Sie als nächstes einen Verweis auf die Klasse **cloud_blob_client** ab, da Sie mit dieser Objekte abrufen können, die im Blob-Speicherdienst gespeicherte Container und Blobs darstellen. Mit dem folgenden Code wird unter Verwendung des oben abgerufenen Speicherkontenobjekts ein **cloud_blob_client**-Objekt erstellt:

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## Erstellen von Containern
Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Dieses Beispiel zeigt, wie Sie einen Container erstellen, falls er nicht bereits vorhanden ist.

	try 
	{
   		// Retrieve storage account from connection string.
   		azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

   		// Create the blob client.
   		azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

   		// Retrieve a reference to a container.
   		azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

   		// Create the container if it doesn't already exist.
   		container.create_if_not_exists();
	}
	catch (const std::exception& e)
	{
	std::wcout << U("Error: ") << e.what() << std::endl;
	}  


Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien (Blobs) im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes öffentlich machen:

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

## Hochladen von Blobs in einen Container
Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen BLOB-Verweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **upload_from_stream**-Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

Sie können wahlweise auch die **upload_from_file**-Methode verwenden, um eine Datei in einen Block-Blob hochzuladen.

## Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach können Sie mit der **list_blobs_segmented**-Methode des Containers die Blobs oder die darin befindlichen Verzeichnisse abrufen. Um auf den umfassenden Satz an Eigenschaften und Methoden für ein zurückgegebenes **blob_result_segment** zuzugreifen, müssen Sie die **blob_result_segment.blobs**-Eigenschaft aufrufen, um ein **cloud_blob**-Objekt abzurufen, oder Sie rufen die **blob_result_segment.directories**-Eigenschaft auf, um ein "cloud_blob_directory"-Objekt abzurufen. Im folgenden Code wird gezeigt, wie der URI der einzelnen Elemente im Container **my-sample-container** abgerufen und ausgegeben wird:

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Loop over items within the container and output the length and URI.
	azure::storage::continuation_token token;
	do
	{
   		azure::storage::blob_result_segment result = container.list_blobs_segmented(token);
   		std::vector<azure::storage::cloud_blob> blobs = result.blobs();

   		for (std::vector<azure::storage::cloud_blob>::const_iterator it = blobs.cbegin(); it != blobs.cend(); ++it)
   		{
			std::wcout << U("Blob: ") << it->uri().primary_uri().to_string() << std::endl;
   		}

   		std::vector<azure::storage::cloud_blob_directory> directories = result.directories();

   		for (std::vector<azure::storage::cloud_blob_directory>::const_iterator it = directories.cbegin(); it != directories.cend(); ++it)
  		{
			std::wcout << U("Directory: ") << it->uri().primary_uri().to_string() << std::endl;
  		}
  		token = result.continuation_token();
	} while (!token.empty());  


## Herunterladen von Blobs
Zum Herunterladen von Blobs rufen Sie zunächst einen Blob-Verweis ab, und rufen Sie anschließend die **download_to_stream**-Methode auf. Im folgenden Beispiel wird die **download_to_stream**-Methode verwendet, um den Blob-Inhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();
		
	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

Alternativ können Sie die **download_to_file**-Methode verwenden, um den Inhalt eines Blobs in eine Datei herunterzuladen. Sie können auch die **download_text**-Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## Löschen von Blobs
Zum Löschen eines Blobs rufen Sie zunächst einen Blob-Verweis ab, und rufen Sie anschließend die **delete_blob**-Methode für diesen auf.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen von Blob-Speichern vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr über Azure-Speicher zu erfahren.

-	[Verwenden des Warteschlangenspeichers in C++](storage-c-plus-plus-how-to-use-queues.md)
-	[How to use Table Storage from C++](storage-c-plus-plus-how-to-use-tables.md)(in englischer Sprache)
-	[Speicherclientbibliothek für C++](https://msdn.microsoft.com/library/azure/gg433040.aspx) 
-	[MSDN-Referenz für Azure-Speicher](https://msdn.microsoft.com/library/azure/gg433040.aspx)
-	[Azure-Speicherdokumentation](http://azure.microsoft.com/documentation/services/storage/)





<!--HONumber=52-->
