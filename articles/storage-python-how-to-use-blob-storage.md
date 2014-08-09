<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="" videoId="" scriptId="" />

Verwenden des Blob-Speicherdiensts aus Python
=============================================

In diesem Leitfaden wird die Durchführung gängiger Szenarien mit dem Azure-Blob-Speicherdienst demonstriert. Die Beispiele wurden mit der Python-API geschrieben. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

[Was ist Blob-Speicher?](#what-is)
 [Konzepte](#concepts)
 [Erstellen eines Azure-Speicherkontos](#create-account)
 [Gewusst wie: Erstellen eines Containers](#create-container)
 [Gewusst wie: Hochladen eines Blobs in einen Container](#upload-blob)
 [Gewusst wie: Auflisten der Blobs in einem Container](#list-blob)
 [Gewusst wie: Herunterladen von Blobs](#download-blobs)
 [Gewusst wie: Löschen eines Blobs](#delete-blobs)
 [Gewusst wie: Hoch- und Herunterladen großer Blobs](#large-blobs)
 [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Gewusst wie: Erstellen eines Containers
---------------------------------------

**Hinweis:** Wenn Sie Python oder die Clientbibliotheken installieren müssen, informieren Sie sich im [Python-Installationshandbuch](../python-how-to-install/).

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

    from azure.storage import *

Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein **BlobService**-Objekt erstellt: Ersetzen Sie 'myaccount' und 'mykey' durch das tatsächliche Konto und den tatsächlichen Schlüssel.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Alle Speicher-Blobs befinden sich in einem Container. Sie können den Container mithilfe eines **BlobService**-Objekts erstellen, falls er nicht vorhanden ist:

    blob_service.create_container('mycontainer')

Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes erstellen und öffentlich machen:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Alternativ können Sie einen Container nach der Erstellung mit dem folgenden Code ändern:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Nach dieser Änderung kann jeder Benutzer im Internet Blobs in einem öffentlichen Container sehen, aber nur Sie können die Blobs ändern oder löschen.

Gewusst wie: Hochladen eines Blobs in einen Container
-----------------------------------------------------

Um eine Datei in ein Blob hochzuladen, erstellen Sie das Blob mit der **put\_blob**-Methode; verwenden Sie dabei einen Dateidatenstrom als Inhalt des Blobs. Erstellen Sie als Erstes einen Datei namens **task1.txt** (beliebiger Inhalt), und speichern Sie sie in demselben Verzeichnis wie die Python-Datei.

    myblob = open(r'task1.txt', 'r').read()
    blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

Gewusst wie: Auflisten der Blobs in einem Container
---------------------------------------------------

Um die Blobs in einem Container aufzulisten, verwenden Sie die **list\_blobs**-Methode mit einer **for**-Schleife, um die Namen der einzelnen Blobs im Container anzuzeigen. Der folgende Code gibt den **Namen** und die **URL** der einzelnen Blobs in einem Container in der Konsole aus.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

Gewusst wie: Herunterladen von Blobs
------------------------------------

Um Blobs herunterzuladen, übertragen Sie mithilfe der **get\_blob**-Methode den Blob-Inhalt in ein Datenstromobjekt, das danach in einer lokalen Datei gespeichert werden kann.

    blob = blob_service.get_blob('mycontainer', 'myblob')
    with open(r'out-task1.txt', 'w') as f:
        f.write(blob)

Gewusst wie: Löschen eines Blobs
--------------------------------

Um ein Blob zu löschen, rufen Sie **delete\_blob** auf.

    blob_service.delete_blob('mycontainer', 'myblob') 

Gewusst wie: Hoch- und Herunterladen großer Blobs
-------------------------------------------------

Die maximale Größe für einen Block-Blob beträgt 200 GB. Blobs kleiner als 64 MB können mit einem einzelnen Aufruf von **put\_blob** oder **get\_blob** hoch- oder heruntergeladen werden, wie zuvor demonstriert. Blobs größer als 64 MB müssen in Blöcken von maximal 4 MB hoch- oder heruntergeladen werden.

Der folgende Code enthält Beispiele von Funktionen zum Hoch- oder Herunterladen von Block-Blobs beliebiger Größe.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

Wenn Sie Blobs größer als 200 GB benötigen, können Sie einen Seiten-Blob anstelle eines Block-Blobs verwenden. Die maximale Größe eines Seiten-Blobs beträgt 1 TB, wobei die Seiten auf 512-Byte-Seitenränder ausgerichtet sind. Verwenden Sie **put\_blob** zum Erstellen eines Seiten-Blobs, **put\_page** zum Schreiben und **get\_blob**, um daraus zu lesen.

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen von Blob-Speicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx)
-   Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/).

  [Next Steps]: #next-steps
  [What is Blob Storage?]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [How To: Create a Container]: #create-container
  [How To: Upload a Blob into a Container]: #upload-blob
  [How To: List the Blobs in a Container]: #list-blob
  [How To: Download Blobs]: #download-blobs
  [How To: Delete a Blob]: #delete-blobs
  [How To: Upload and Download Large Blobs]: #large-blobs
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
