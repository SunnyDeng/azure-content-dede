<properties 
	pageTitle="Verwenden des Blob-Speichers mit Python | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Blob-Dienst mit Python nutzen können, um Blobs hoch- und herunterzuladen, aufzulisten und zu löschen." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="huvalo"/>

# Verwenden des Blob-Speichers mit Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung gängiger Szenarien mit dem Azure-Blob-Speicherdienst demonstriert. Die Beispiele sind in Python geschrieben und verwenden das [Python Azure-Paket][]. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen von Containern

> [AZURE.NOTE]Informationen zur Installation von Python bzw. des [Python Azure-Pakets][] finden Sie im [Python-Installationshandbuch](../python-how-to-install.md).

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

	from azure.storage import BlobService

Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein **BlobService**-Objekt erstellt: Ersetzen Sie 'myaccount' und 'mykey' durch das tatsächliche Konto und den tatsächlichen Schlüssel.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Sie können den Container mithilfe eines **BlobService**-Objekts erstellen, falls er nicht vorhanden ist:

	blob_service.create_container('mycontainer')

Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes erstellen und öffentlich machen:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Alternativ können Sie einen Container nach der Erstellung mit dem folgenden Code ändern:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Nach dieser Änderung kann jeder Benutzer im Internet Blobs in einem öffentlichen Container sehen, aber nur Sie können die Blobs ändern oder löschen.

## Hochladen von Blobs in einen Container

Zum Hochladen von Daten in einen Blob verwenden Sie die Methode **put_block_blob_from_path**, **put_block_blob_from_file**, **put_block_blob_from_bytes** oder **put_block_blob_from_text**. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

**put_block_blob_from_path** lädt den Inhalt einer Datei aus dem angegebenen Pfad hoch, **put_block_blob_from_file** lädt den Inhalt aus einer bereits geöffneten Datei/einem Stream. **put_block_blob_from_bytes** lädt ein Byte-Array hoch, und **put_block_blob_from_text** lädt den angegebenen Textwert unter Verwendung der festgelegten Codierung (standardmäßig UTF-8) hoch.

Das folgende Beispiel lädt den Inhalt der Datei **sunset.png** in das Blob **myblob** hoch.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzulisten, verwenden Sie die **list_blobs**-Methode mit einer **for**-Schleife, um die Namen der einzelnen Blobs im Container anzuzeigen. Der folgende Code gibt den **Namen** und die **URL** der einzelnen Blobs in einem Container in der Konsole aus.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## Herunterladen von Blobs

Verwenden Sie **get_blob_to_path**, **get_blob_to_file**, **get_blob_to_bytes** oder **get_blob_to_text**, um Daten aus einem Blob herunterzuladen. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

Das folgende Beispiel verwendet **get_blob_to_path**, um den Inhalt des Blobs **myblob** herunterzuladen und in der Datei **out-sunset.png** zu speichern:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Löschen eines Blobs

Um ein Blob zu löschen, rufen Sie **delete_blob** auf.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Blob-Speichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][].
-   Besuchen Sie den [Blog des Azure-Speicherteams][]

[Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure-Paket]: https://pypi.python.org/pypi/azure
[Python Azure-Pakets]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO2-->