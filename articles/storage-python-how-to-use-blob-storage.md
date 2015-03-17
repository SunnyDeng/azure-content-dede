<properties 
	pageTitle="Verwenden des Blob-Speichers (Python) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Blobdienst zum Hochladen, Herunterladen, Auflisten und Löschen von Blobs verwenden." 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>

# Verwenden des Blob-Speicherdiensts aus Python
In diesem Leitfaden wird die Durchführung gängiger Szenarien mit dem Azure-Blob-Speicherdienst demonstriert. Die Beispiele wurden mit der Python-API geschrieben. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

[Was ist der Blob-Speicher?][]   
 [Konzepte][]   
 [Erstellen eines Azure-Speicherkontos][]   
 [Vorgehensweise: Erstellen eines Containers][]   
 [Vorgehensweise: Hochladen eines Blobs in einen Container][]   
 [Vorgehensweise: Auflisten der Blobs in einem Container][]   
 [Vorgehensweise: Herunterladen von Blobs][]   
 [Vorgehensweise: Löschen eines Blobs][]   
 [Nächste Schritte][]

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

## <a name="create-account"> </a>Erstellen eines Azure-Speicherkontos

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## <a name="create-container"> </a>Vorgehensweise: Erstellen eines Containers

**Hinweis:** Informationen zur Installation von Python oder den Clientbibliotheken finden Sie im [Python-Installationshandbuch](../python-how-to-install/).


Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

	from azure.storage import BlobService

Mit dem folgenden Code wird unter Verwendung des Speicherkontonamens und Kontoschlüssels ein **BlobService**-Objekt erstellt:  Ersetzen Sie 'myaccount' und 'mykey' durch das tatsächliche Konto und den tatsächlichen Schlüssel.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Alle Speicher-Blobs befinden sich in einem Container. Sie können den Container mithilfe eines **BlobService**-Objekts erstellen, falls er nicht vorhanden ist:

	blob_service.create_container('mycontainer')

Standardmäßig ist der neue Container privat, sodass Sie Ihren Speicherzugriffsschlüssel wie zuvor angeben müssen, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes erstellen und öffentlich machen:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Alternativ können Sie einen Container nach der Erstellung mit dem folgenden Code ändern:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Nach dieser Änderung kann jeder Benutzer im Internet Blobs in einem öffentlichen Container sehen, aber nur Sie können die Blobs ändern oder löschen.

## <a name="upload-blob"> </a>Vorgehensweise: Hochladen eines Blobs in einen Container

Zum Hochladen von Daten in ein Blob verwenden Sie eine der Methoden **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** oder **put\_block\_blob\_from\_text**. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

**put\_block\_blob\_from\_path** lädt den Inhalt einer Datei aus dem angegebenen Pfad hoch, **put\_block\_blob\_from\_file** lädt den Inhalt einer Datei aus einer bereits geöffneten Datei/einem bereits geöffneten Stream. **put\_block\_blob\_from\_bytes** lädt ein Byte-Array hoch, **put\_block\_blob\_from\_text** lädt den angegebenen Textwert wie vorgegeben codiert hoch (Standardcodierung ist UTF-8).

Das folgende Beispiel lädt den Inhalt der Datei **task1.txt** in das Blob **myblob** hoch.

	blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Vorgehensweise: Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzulisten, verwenden Sie die **list\_blobs**-Methode mit einer **for**-Schleife, um die Namen der einzelnen Blobs im Container anzuzeigen. Der folgende Code gibt den **Namen** und die **URL** der einzelnen Blobs in einem Container in der Konsole aus.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>Vorgehensweise: Herunterladen von Blobs

Verwenden Sie zum Herunterladen von Daten aus einem Blob die Methode **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** oder **get\_blob\_to\_text**. Dies sind allgemeine Methoden zur Durchführung der erforderlichen Teilung, wenn die Größe der Daten 64 MB übersteigt.

Das folgende Beispiel verwendet **get\_blob\_to\_path**, um den Inhalt des Blobs **myblob** herunterzuladen und in der Datei **out-task1.txt** zu speichern:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Vorgehensweise: Löschen eines Blobs

Um ein Blob zu löschen, rufen Sie **delete_blob** auf.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie jetzt die Grundlagen des Blob-Speichers erlernt haben, folgen Sie diesen Links, um Informationen zum Ausführen komplexerer Speicheraufgaben zu erhalten.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][]
-   Besuchen Sie den [Blog des Azure-Speicherteams][].

  [Nächste Schritte]: #next-steps
  [Was ist der Blob-Speicher?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Vorgehensweise: Erstellen eines Containers]: #create-container
  [Vorgehensweise: Hochladen eines Blobs in einen Container]: #upload-blob
  [Vorgehensweise: Auflisten der Blobs in einem Container]: #list-blob
  [Vorgehensweise: Herunterladen von Blobs]: #download-blobs
  [Vorgehensweise: Löschen eines Blobs]: #delete-blobs
  [Vorgehensweise: Hoch- und Herunterladen großer Blobs]: #large-blobs
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
<!--HONumber=42-->
