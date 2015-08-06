<properties 
	pageTitle="Verschieben von Daten in den und aus dem Azure-Blobspeicher | Microsoft Azure" 
	description="Verschieben von Daten in den und aus dem Azure-Blobspeicher" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;sachouks;mohabib;bradsev" />

# Verschieben von Daten in den und aus dem Azure-Blobspeicher

Mithilfe der Informationen im Artikel [Szenarien für die Advanced Analytics Process and Technology (ADAPT) in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md) können Sie die Ressourcen ermitteln, die Sie für verschiedene, im erweiterten Analyseprozess verwendeten Datenwissenschaftsworkflows benötigen. Wenn Sie Daten zum und vom Azure-Blobspeicher verschieben müssen, wenden Sie eine der folgenden Methoden an:

- [Mit dem Azure Storage-Explorer](#explorer)
- [Mit dem Befehlszeilenprogramm AzCopy](#AzCopy)
- [Mit dem Azure SDK in Python](#PythonSDK)


> [AZURE.TIP]Alternativ dazu können Sie [Azure Data Factory](https://azure.microsoft.com/de-de/services/data-factory/) zum Erstellen und Planen einer Pipeline verwenden, die Daten aus dem Azure-Blobspeicher herunterlädt, an einen veröffentlichten Azure Machine Learning-Webdienst übergibt, die Predictive Analytics-Ergebnisse empfängt und diese in den Speicher hochlädt. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning](../data-factory/data-factory-create-predictive-pipelines.md).

<para></para>

> [AZURE.NOTE]Eine umfassende Einführung in Azure-Blobspeicher finden Sie unter [Grundlagen zu Azure-Blobspeicher](../storage-dotnet-how-to-use-blobs.md) und [Azure-Blobdienst](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Bevor Sie Daten hoch- und herunterladen können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. Anleitungen zum Abrufen dieser Informationen finden Sie unter [Verwalten von Azure-Speicherkonten](../storage-create-storage-account.md) im Abschnitt "Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln". In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Speicherkonto und die zugehörigen Speicherschlüssel verfügen.


<a id="explorer"></a>
## Verwenden von Azure Storage Explorer 

Azure Storage Explorer ist ein kostenloses Windows-Tool zum Prüfen und Ändern der Daten in einem Azure-Speicherkonto. Es kann von [Azure Storage-Explorer](http://azurestorageexplorer.codeplex.com/) heruntergeladen werden. Die folgenden Schritte beschreiben das Hoch- und Herunterladen von Daten mithilfe von Azure Storage Explorer.

1.  Starten von Azure Storage Explorer 
2.  Wenn das Speicherkonto, auf das Sie zugreifen möchten, noch nicht in Azure Storage Explorer hinzugefügt wurde, klicken Sie auf die Schaltfläche "Add Account", um das Konto hinzuzufügen. Wenn es bereits hinzugefügt wurde, wählen Sie das Konto aus der Dropdownliste "--Wählen Sie ein Speicherkonto aus--" aus. ![Arbeitsbereich erstellen][1] <br>
3. Geben Sie den Speicherkontonamen und den Speicherkontoschlüssel ein, und klicken Sie dann auf "Add Storage Account". Sie können mehrere Speicherkonten hinzufügen. Jedes Konto wird auf einer Registerkarte angezeigt. Die Container unter diesem Speicherkonto werden im linken Bereich angezeigt. Wählen Sie einen Container aus, um die Blobs im Container im rechten Bereich anzuzeigen. 
![Arbeitsbereich erstellen][2]
<br>
![Arbeitsbereich erstellen][3]
<br>
4. Sie laden Daten hoch, indem Sie auf die Schaltfläche "Upload" klicken. Wählen Sie eine oder mehrere Dateien aus dem Dateisystem zum Hochladen aus, und klicken Sie auf "Öffnen", um mit dem Hochladen der Dateien zu beginnen.
5. Sie laden Daten herunter, indem Sie das Blob im entsprechenden Container auswählen und dann auf die Schaltfläche "Download" klicken.

<a id="AzCopy"></a>
## Verwenden von AzCopy

AzCopy ist ein Befehlszeilenprogramm zum Hoch- und Herunterladen von Daten.

**Warnung** Wenn Sie einen anderen Computer als den zu einem früheren Zeitpunkt im erweiterten Analyseprozess eingerichteten virtuellen Computer verwenden, installieren Sie AzCopy mithilfe der folgenden Installationsanweisungen: [Herunterladen und Installieren von AzCopy](../storage-use-azcopy.md#install).

###Beispiele für das Hoch- und Herunterladen von Dateien in und aus Blobs:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]1. Beim Hochladen von Dateien wird der Vorgang mit "/S" rekursiv durchgeführt. Ohne diesen Parameter werden Dateien im Unterverzeichnis nicht hochgeladen. 2. Beim Herunterladen von Datei durchsucht "/S" die Container rekursiv, bis alle Dateien im angegebenen Verzeichnis und seinen Unterverzeichnissen oder alle Dateien, die dem angegebenen Muster im angegebenen Verzeichnis und seinen Unterverzeichnissen entsprechen, heruntergeladen wurden. 3. Sie können mit dem "/Source"-Parameter keine bestimmte Blobdatei für das Herunterladen angeben. Um eine bestimmte Datei herunterzuladen, geben Sie den Blobdateinamen mithilfe des "/Pattern"-Parameters an. Der "/S"-Parameter kann verwendet werden, damit AzCopy rekursiv nach einem Dateinamensmuster sucht. Ohne den "/Pattern"-Parameter lädt AzCopy alle Dateien in diesem Verzeichnis herunter.

Ausführliche Informationen zur Verwendung von AzCopy finden Sie unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy](../storage-use-azcopy.md#install).


<a id="PythonSDK"></a>
## Verwenden von Python

Mit der Python-API im Azure SDK können Sie folgende Aufgaben ausführen:

- Erstellen eines Containers
- Hochladen eines Blobs in einen Container
- Herunterladen von Blobs
- Auflisten der Blobs in einem Container
- Löschen eines Blobs

In diesem Abschnitt wird beschrieben, wie Blobs aufgelistet, hochgeladen und heruntergeladen werden. Weitere Informationen zur Verwendung der Python-API finden Sie unter [Verwenden des Blobspeicherdiensts aus Python](../storage-python-how-to-use-blob-storage.md).

> [AZURE.NOTE]Wenn Sie einen anderen Computer als den zu einem früheren Zeitpunkt im erweiterten Analyseprozess eingerichteten virtuellen Computer verwenden, müssen Sie das [Python Azure SDK](../python-how-to-install.md) installieren, bevor Sie den unten stehenden Beispielcode verwenden können:

### Hochladen von Daten zum Blob

Fügen Sie am Anfang jedes Python-Codes, in dem Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Codeausschnitt ein:

	from azure.storage import BlobService

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein BlobService-Objekt erstellt. Ersetzen Sie "account_name" und "account_key" durch das tatsächliche Konto und den tatsächlichen Schlüssel.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Verwenden Sie die folgenden Methoden zum Hochladen von Daten in ein Blob:
 
1. put_block_blob_from_path (lädt den Inhalt einer Datei aus dem angegebenen Pfad hoch)
2. put_block_blob_from_file (lädt den Inhalt aus einer/m bereits geöffneten Datei/Stream hoch)
3. put_block_blob_from_bytes (lädt ein Array aus Bytes hoch)
4. put_block_blob_from_text (lädt den angegebenen Textwert mit der angegebenen Codierung hoch)
 
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

### Herunterladen von Daten aus dem Blob

Verwenden Sie die folgenden Methoden, um Daten aus einem Blob herunterzuladen: 1. get_blob_to_path 2. get_blob_to_file 3. get_blob_to_bytes 4. get_blob_to_text

Dies sind Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

Der folgende Beispielcode lädt den Inhalt eines Blobs in einem Container in eine lokale Datei herunter:

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Der folgende Beispielcode lädt alle Blobs aus einem Container herunter. Dabei wird "list_blobs" verwendet, um die Liste der verfügbaren Blobs im Container abzurufen und in ein lokales Verzeichnis herunterzuladen.

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

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png
 

<!----HONumber=July15_HO4-->