<properties
	pageTitle="Verwenden des Dateispeichers mit Java | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Azure-Dateispeicherdienst zum Hochladen, Herunterladen, Auflisten und Löschen von Dateien verwenden. Die Beispiele wurden in Java geschrieben."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="jutang"/>

# Verwenden des Dateispeichers mit Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Übersicht

In dieser Anleitung erfahren Sie, wie allgemeine Vorgänge im Microsoft Azure-Dateispeicherdienst ausgeführt werden. Anhand von in Java geschriebenen Beispielen lernen Sie, wie Freigaben und Verzeichnisse erstellt sowie Dateien hochgeladen, aufgelistet und gelöscht werden. Wenn Sie noch nicht mit dem Microsoft Azure-Dateispeicherdienst vertraut sind, sind die in den folgenden Abschnitten erläuterten Konzepte sehr hilfreich zum besseren Verständnis der Beispiele.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Java-Anwendung

Zum Erstellen der Beispiele benötigen Sie das Java Development Kit (JDK) und das [Azure Storage-SDK für Java][]. Sie sollten auch ein Azure-Speicherkonto erstellt haben.

## Einrichten der Anwendung zur Verwendung des Dateispeichers

Um die Azure-Speicher-APIs zu verwenden, fügen Sie die folgende Anweisung am Anfang der Java-Datei dort ein, wo der Zugriff auf den Speicherdienst erfolgen soll.

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Einrichten einer Azure-Speicherverbindungszeichenfolge

Zur Verwendung des Dateidiensts müssen Sie eine Verbindung mit Ihrem Azure-Speicherkonto herstellen. Im ersten Schritt muss eine Verbindungszeichenfolge konfiguriert werden, über die die Verbindung mit dem Speicherkonto hergestellt wird. Definieren Sie dazu eine statische Variable.

	// Configure the connection-string with your values
	public static final String storageConnectionString =
	    "DefaultEndpointsProtocol=http;" +
	    "AccountName=your_storage_account_name;" +
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Ersetzen Sie "your\_storage\_account\_name" und "your\_storage\_account\_key" durch die tatsächlichen Werte für Ihr Speicherkonto.

## Herstellen einer Verbindung mit einem Azure-Speicherkonto

Um eine Verbindung mit Ihrem Speicherkonto herzustellen, müssen Sie das **CloudStorageAccount**-Objekt verwenden, um eine Verbindungszeichenfolge an die zugehörige **parse**-Methode zu übergeben.

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse** löst eine InvalidKeyException aus, daher müssen Sie sie in einem try/catch-Block platzieren.

## Erstellen einer Freigabe

Alle Dateien und Verzeichnisse im Dateispeicher befinden sich in einem Container mit dem Namen **Share**. Das Speicherkonto kann so viele Freigaben aufweisen, wie es die Kapazität des Kontos zulässt. Für den Zugriff auf eine Freigabe und ihren Inhalt muss ein Dateispeicherclient verwendet werden.

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

Mit dem Dateispeicherclient können Sie dann einen Verweis auf die Freigabe abrufen.

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare");

Um die Freigabe zu erstellen, verwenden Sie die **createIfNotExists**-Methode des CloudFileShare-Objekts.

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

An diesem Punkt enthält **share** einen Verweis auf eine Freigabe mit dem Namen **sampleshare**.

## Hochladen einer Datei

Eine Azure-Dateispeicherfreigabe enthält mindestens ein Stammverzeichnis, in dem Dateien gespeichert werden können. In diesem Abschnitt erfahren Sie, wie Sie eine Datei vom lokalen Speicher in das Stammverzeichnis einer Freigabe hochladen.

Im ersten Schritt beim Hochladen einer Datei wird ein Verweis auf das Verzeichnis abgerufen, in dem sie gespeichert werden soll. Rufen Sie dazu die **getRootDirectoryReference**-Methode des Freigabeobjekts auf.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

Da Sie nun einen Verweis auf das Stammverzeichnis der Freigabe erstellt haben, können Sie mit dem folgenden Code eine Datei in dieses Verzeichnis hochladen.

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## Erstellen eines Verzeichnisses

Sie können zudem den Speicher organisieren, indem Sie Dateien in Unterverzeichnissen ablegen, anstatt alle Dateien im Stammverzeichnis zu speichern. Mit dem Azure-Dateispeicherdienst können Sie so viele Verzeichnisse erstellen, wie in Ihrem Konto zugelassen sind. Mit dem folgenden Code wird ein Unterverzeichnis mit dem Namen **sampledir** im Stammverzeichnis erstellt.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## Auflisten von Dateien und Verzeichnissen in einer Freigabe

Eine Liste von Dateien und Verzeichnissen in einer Freigabe kann einfach durch Aufrufen von **listFilesAndDirectories** in einem CloudFileDirectory-Verweis abgerufen werden. Die Methode gibt eine Liste von ListFileItem-Objekten zurück, die Sie durchlaufen können. Mit dem folgenden Code werden beispielsweise Dateien und Verzeichnisse im Stammverzeichnis aufgelistet.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## Herunterladen einer Datei

Ein Vorgang, den Sie in Bezug auf den Dateispeicher häufiger durchführen werden, ist das Herunterladen von Dateien. Im folgenden Beispiel wird mit dem Code die Datei "SampleFile.txt" heruntergeladen und ihr Inhalt angezeigt.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## Löschen von Dateien

Ein weiterer häufig durchgeführter Vorgang ist das Löschen von Dateien im Dateispeicher. Mit dem folgenden Code wird die Datei "SampleFile.txt" gelöscht, die im Verzeichnis **sampledir** gespeichert ist.


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## Löschen von Verzeichnissen

Das Löschen eines Verzeichnisses ist eine ziemlich einfache Angelegenheit. Dabei ist jedoch zu beachten, dass Verzeichnisse, die noch Dateien oder andere Verzeichnisse enthalten, nicht gelöscht werden können.

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## Löschen von Freigaben

Das Löschen einer Freigabe erfolgt durch Aufrufen der **deleteIfExists**-Methode für ein CloudFileShare-Objekt. Hier ein Beispielcode dazu.

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");

	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   }
	} catch (Exception e) {
		e.printStackTrace();
	}

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über andere Azure-Speicher-APIs erfahren möchten.

- [Java Developer Center](http://azure.microsoft.com/develop/java/)
- [Azure Storage-SDK für Java](https://github.com/azure/azure-storage-java)
- [Azure Storage-SDK für Android](https://github.com/azure/azure-storage-android)
- [Referenz für Azure Storage-Client-SDKs](http://dl.windowsazure.com/storage/javadoc/)
- [REST-API für Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/)
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0224_2016-->