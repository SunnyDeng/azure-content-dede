<properties 
	pageTitle="Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe von Python | Microsoft Azure" 
	description="Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe von Python" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="bradsev" />

# Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe von Python

## Einführung
Dieses Thema beschreibt das Auflisten, Hochladen und Herunterladen von Blobs mit der Python-API.

Mit der Python-API im Azure SDK können Sie folgende Aufgaben ausführen:

- Erstellen eines Containers
- Hochladen eines Blobs in einen Container
- Herunterladen von Blobs
- Auflisten der Blobs in einem Container
- Löschen eines Blobs

Weitere Informationen zur Verwendung der Python-API finden Sie unter [Verwenden des Blob-Speicherdiensts in Python](../storage-python-how-to-use-blob-storage.md).

Nachstehend finden Sie Links zu Anleitungen für Technologien zum Verschieben von Daten in den und aus dem Azure-BLOB-Speicher:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Wenn Sie einen virtuellen Computer verwenden, der mit den von den [virtuellen Data Science-Computern in Azure](machine-learning-data-science-virtual-machines.md) bereitgestellten Skripts eingerichtet wurde, ist AzCopy bereits auf dem virtuellen Computer installiert.

> [AZURE.NOTE] Eine umfassende Einführung in Azure-Blobspeicher finden Sie unter [Grundlagen zu Azure-Blobspeicher](../storage-dotnet-how-to-use-blobs.md) und [Azure-Blobdienst](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Voraussetzungen

In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Abonnement, ein Speicherkonto und den zugehörigen Speicherschlüssel für dieses Konto verfügen. Bevor Sie Daten hoch- und herunterladen können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen.

- Informationen zum Einrichten eines Azure-Abonnements finden Sie unter [Kostenlose 1-Monat-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- Anleitungen zum Erstellen eines Speicherkontos und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md).

## Hochladen von Daten zum Blob

Fügen Sie am Anfang jedes Python-Codes, in dem Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Codeausschnitt ein:

	from azure.storage import BlobService

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein BlobService-Objekt erstellt. Ersetzen Sie "account\_name" und "account\_key" durch das tatsächliche Konto und den tatsächlichen Schlüssel.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Verwenden Sie die folgenden Methoden zum Hochladen von Daten in ein Blob:
 
1. put\_block\_blob\_from\_path (lädt den Inhalt einer Datei aus dem angegebenen Pfad hoch)
2. put\_block\_blob\_from\_file (lädt den Inhalt aus einer/m bereits geöffneten Datei/Stream hoch)
3. put\_block\_blob\_from\_bytes (lädt ein Array aus Bytes hoch)
4. put\_block\_blob\_from\_text (lädt den angegebenen Textwert mit der angegebenen Codierung hoch)
 
Der folgende Beispielcode lädt eine lokale Datei in einen Container hoch:
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Der folgende Beispielcode lädt alle Dateien (ausgenommen Verzeichnisse) in einem lokalen Verzeichnis in den Blobspeicher hoch:

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

## Herunterladen von Daten aus dem Blob

Verwenden Sie die folgenden Methoden, um Daten aus einem Blob herunterzuladen: 1. get\_blob\_to\_path 2. get\_blob\_to\_file 3. get\_blob\_to\_bytes 4. get\_blob\_to\_text

Dies sind Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

Der folgende Beispielcode lädt den Inhalt eines Blobs in einem Container in eine lokale Datei herunter:

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Der folgende Beispielcode lädt alle Blobs aus einem Container herunter. Dabei wird "list\_blobs" verwendet, um die Liste der verfügbaren Blobs im Container abzurufen und in ein lokales Verzeichnis herunterzuladen.

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!---HONumber=AcomDC_0211_2016-->