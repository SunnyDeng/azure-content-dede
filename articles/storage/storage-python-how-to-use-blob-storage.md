<properties
	pageTitle="Verwenden des Azure-Blob-Speichers mit Python | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Azure-Blob-Speicher mit Python nutzen können, um Blobs hoch- und herunterzuladen, aufzulisten und zu löschen."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>

# Verwenden des Azure-Blob-Speichers mit Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Artikel wird die Durchführung gängiger Szenarien mit dem Blob-Speicher demonstriert. Die Beispiele sind in Python geschrieben und verwenden das [Python Azure Storage-Paket][]. Die behandelten Szenarien umfassen das Hochladen, Auflisten, Herunterladen und Löschen von Blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen eines Containers

> [AZURE.NOTE]Informationen zur Installation von Python bzw. des [Python Azure-Pakets][] finden Sie im [Python-Installationshandbuch](../python-how-to-install.md).

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

	from azure.storage.blob import BlobService

Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein **BlobService**-Objekt erstellt: Ersetzen Sie 'myaccount' und 'mykey' durch das tatsächliche Konto und den tatsächlichen Schlüssel.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Im folgenden Codebeispiel können Sie den Container mithilfe eines **BlobService**-Objekts erstellen, falls er nicht vorhanden ist.

	blob_service.create_container('mycontainer')

Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes erstellen und öffentlich machen.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

Alternativ können Sie einen Container nach der Erstellung mit dem folgenden Code ändern.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Nach dieser Änderung kann jeder Benutzer im Internet Blobs in einem öffentlichen Container sehen, aber nur Sie können die Blobs ändern oder löschen.

## Hochladen eines Blobs in einen Container

Zum Hochladen von Daten in einen Blob verwenden Sie die Methode **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** oder **put\_block\_blob\_from\_text**. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

**put\_block\_blob\_from\_path** lädt den Inhalt einer Datei aus dem angegebenen Pfad hoch, und **put\_block\_blob\_from\_file** lädt den Inhalt aus einer bereits geöffneten Datei/einem Stream. **put\_block\_blob\_from\_bytes** lädt ein Byte-Array hoch, und **put\_block\_blob\_from\_text** lädt den angegebenen Textwert unter Verwendung der festgelegten Codierung (standardmäßig UTF-8) hoch.

Das folgende Beispiel lädt den Inhalt der Datei **sunset.png** in das Blob **myblob** hoch.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Auflisten der Blobs in einem Container

Verwenden Sie zum Auflisten des Blobs in einem Container die Methode **list\_blobs**. Jeder Aufruf von **list\_blobs** gibt ein Segment mit den Ergebnissen zurück. Um alle Ergebnisse zu erhalten, überprüfen Sie die Variable **next\_marker** bei den Ergebnissen und rufen Sie bei Bedarf **list\_blobs** erneut ab. Der folgende Code gibt den **Namen** der einzelnen Blobs in einem Container in der Konsole aus.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Herunterladen von Blobs

Jedes Segment mit Ergebnissen kann eine unterschiedliche Anzahl von Blobs enthalten, insgesamt bis zu 5.000. Wenn die Variable **next\_marker** für ein bestimmtes Segment vorhanden ist, gibt es möglicherweise weitere Blobs im Container.

Verwenden Sie **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** oder **get\_blob\_to\_text**, um Daten aus einem Blob herunterzuladen. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

Das folgende Beispiel verwendet **get\_blob\_to\_path**, um den Inhalt des Blobs **myblob** herunterzuladen und in der Datei **out-sunset.png** zu speichern.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Löschen eines Blobs

Um ein Blob zu löschen, rufen Sie **delete\_blob** auf.

	blob_service.delete_blob('mycontainer', 'myblob')

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Blob-Speichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Besuchen Sie den [Blog des Azure-Speicherteams][]
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy)

Weitere Informationen finden Sie außerdem im [Python Developer Center](/develop/python/).

[Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure-Pakets]: https://pypi.python.org/pypi/azure
[Python Azure Storage-Paket]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_0114_2016-->