<properties 
	pageTitle="Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe von AzCopy | Microsoft Azure" 
	description="Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe von AzCopy" 
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
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe von AzCopy

Nachstehend finden Sie Links zu Anleitungen für Technologien zum Verschieben von Daten in den und aus dem Azure-BLOB-Speicher:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Einführung

AzCopy ist ein Befehlszeilenprogramm, das zum leistungsstarken Hochladen, Herunterladen und Kopieren von Daten zu und vom Microsoft Azure Blob-, Datei- und Tabellenspeicher konzipiert wurde.

Anleitungen zur Installation von AzCopy sowie zusätzliche Informationen zu seiner Verwendung mit der Azure-Plattform finden Sie unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy](../storage-use-azcopy.md).

> [AZURE.NOTE]Wenn Sie einen virtuellen Computer verwenden, der mit den von den [virtuellen Data Science-Computern in Azure](machine-learning-data-science-virtual-machines.md) bereitgestellten Skripts eingerichtet wurde, ist AzCopy bereits auf dem virtuellen Computer installiert.

> [AZURE.NOTE]Eine umfassende Einführung in Azure-Blobspeicher finden Sie unter [Grundlagen zu Azure-Blobspeicher](../storage-dotnet-how-to-use-blobs.md) und [Azure-Blobdienst](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Voraussetzungen

In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Abonnement, ein Speicherkonto und den zugehörigen Speicherschlüssel für dieses Konto verfügen. Bevor Sie Daten hoch- und herunterladen können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen.

- Informationen zum Einrichten eines Azure-Abonnements finden Sie unter [Kostenlose 1-Monat-Testversion](https://azure.microsoft.com/de-DE/pricing/free-trial/).
- Anleitungen zum Erstellen eines Speicherkontos und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md).

## Hochladen von Dateien in einen Azure-Blob

Zum Hochladen einer Datei verwenden Sie den folgenden Befehl im AzCopy-Befehlszeilenprogramm.

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

## Herunterladen von Dateien aus einem Azure-Blob

Zum Herunterladen einer Datei aus einem Azure-Blob verwenden Sie den folgenden Befehl im AzCopy-Befehlszeilenprogramm.

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

## Übertragen von Blobs zwischen Azure-Containern

Zum Übertragen von Blobs zwischen Azure-Containern verwenden Sie den folgenden Befehl im AzCopy-Befehlszeilenprogramm.

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

## Tipps zur Verwendung von AzCopy

> [AZURE.TIP]1. Beim Hochladen von Dateien wird der Vorgang mit "/S" rekursiv durchgeführt. Ohne diesen Parameter werden Dateien im Unterverzeichnis nicht hochgeladen. 2. Beim Herunterladen von Datei durchsucht "/S" die Container rekursiv, bis alle Dateien im angegebenen Verzeichnis und seinen Unterverzeichnissen oder alle Dateien, die dem angegebenen Muster im angegebenen Verzeichnis und seinen Unterverzeichnissen entsprechen, heruntergeladen wurden. 3. Sie können mit dem "/Source"-Parameter keine bestimmte Blobdatei für das Herunterladen angeben. Um eine bestimmte Datei herunterzuladen, geben Sie den Blobdateinamen mithilfe des "/Pattern"-Parameters an. Der "/S"-Parameter kann verwendet werden, damit AzCopy rekursiv nach einem Dateinamensmuster sucht. Ohne den "/Pattern"-Parameter lädt AzCopy alle Dateien in diesem Verzeichnis herunter.

<!---HONumber=Nov15_HO1-->