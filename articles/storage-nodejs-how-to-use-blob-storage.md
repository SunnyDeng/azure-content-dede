<properties 
	pageTitle="Verwenden des Blob-Speichers mit Node.js | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Blob-Dienst zum Hochladen, Herunterladen, Auflisten und Löschen von Blob-Inhalten verwenden. Die Beispiele sind in Node.js geschrieben." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="mwasson"/>



# Verwenden des Blob-Speichers mit Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Leitfaden zeigt die Durchführung häufiger Szenarien mit dem
Azure Blob-Dienst. Die Beispiele wurden mit
Node.js API. geschrieben. Die aufgeführten Szenarien umfassen das **Hochladen**, **Auflisten**,
**Herunterladen** und **Löschen** von Blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], in [Node.js-Cloud-Dienst][Node.js-Cloud-Dienst] (mithilfe von Windows PowerShell) oder auf der [Website mit WebMatrix].

## Konfigurieren der Anwendung für den Speicherzugriff

Um Azure-Speicher verwenden zu können, müssen Sie das Azure Storage-SDK für Node.js herunterladen. Es enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### Verwenden des Node-Paket-Managers (NPM) zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure-storage** in das Befehlsfenster ein.
    Die Ausgabe dieses Befehls sollte wie folgt aussehen:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der
    Ordner **node_modules** erstellt wurde. In diesem Ordner finden Sie das
    **azure-storage**-Paket, das die für den Speicherzugriff benötigten Bibliotheken
    enthält.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei
**server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

    var azure = require('azure-storage');

## Einrichten einer Azure-Speicherverbindung

Das Azure-Modul entnimmt den Umgebungsvariablen `AZURE_STORAGE_ACCOUNT`, `AZURE_STORAGE_ACCESS_KEY` und `AZURE_STORAGE_CONNECTION_STRING` die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createBlobService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher].

## Vorgehensweise: Erstellen eines Containers

Mit dem **BlobService**-Objekt können Sie mit Containern und Blobs arbeiten. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie Folgendes am Anfang der Datei **server.js** ein.

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Sie können anonym auf ein Blob zugreifen, indem Sie **createBlobServiceAnonymous** verwenden und die Hostadresse angeben. Beispiel: `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

Alle Blobs befinden sich in einem Container. Verwenden Sie zum Erstellen eines neuen Containers **createContainerIfNotExists**. Im Folgenden wird ein neuer Container namens 'mycontainer' erstellt.

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Wenn der Container erstellt wurde, ist  `result` "True". Wenn der Container bereits vorhanden ist, ist  `result` "False". `response` enthält Informationen über den Vorgang, einschließlich der [ETag](http://en.wikipedia.org/wiki/HTTP_ETag)-Informationen für den Container.

### Containersicherheit

Neue Container sind standardmäßig privat. Ein anonymer Zugriff ist auf sie so nicht möglich. Um den Container öffentlich zu machen, damit ein anonymer Zugriff darauf möglich ist, können Sie die Zugriffsebene des Containers auf **Blob** oder **Container** festlegen.

* **Blob** - ermöglicht den anonymen Lesezugriff auf Blobinhalte und Metadaten in diesem Container, aber nicht auf Containermetadaten wie das Auflisten aller Blobs in einem Container.

* Über **Container** wird der anonyme Lesezugriff auf Blobinhalte und Metadaten sowie auf Containermetadaten ermöglicht. 

Das folgende Beispiel zeigt, wie Sie die Zugriffsebene auf **Blob setzen können:** 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

Alternativ können Sie die Zugriffsebene eines Containers mit **setContainerAcl** bearbeiten. Das folgende Beispiel ändert die Zugriffsebene des Containers:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Das Ergebnis enthält Informationen über den Vorgang, einschließlich der aktuellen **ETag**-Informationen für den Container.

### Filter

Auf die Vorgänge, die mit **BlobService** ausgeführt werden, können optionale Filtervorgänge angewendet werden. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, mit denen eine Methode mit einer Signatur implementiert wird:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das "returnObject"-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder es muss einfach "finallCallback" aufgerufen werden, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Mit folgendem Code wird ein **BlobService**-Objekt erstellt, das **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Gewusst wie: Hochladen eines Blobs in einen Container

Ein Blob kann entweder block- oder seitenbasiert sein. Blockblobs ermöglichen Ihnen das effizientere Hochladen großer Daten. Seitenblobs hingegen sind für Lese-/Schreibvorgänge optimiert. Weitere Informationen finden Sie unter [Grundlegendes zu Blockblobs und Seitenblobs](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### Blockblobs

Verwenden Sie zum Hochladen von Daten zu einem Blockblob Folgendes:

* **createBlockBlobFromLocalFile** - erstellt einen neuen Blockblob und lädt die Inhalte einer Datei hoch.

* **createBlockBlobFromStream** - erstellt einen neuen Blockblob und lädt die Inhalte eines Streams hoch.

* **createBlockBlobFromText** - erstellt einen neuen Blockblob und lädt die Inhalte einer Zeichenfolge hoch.

* **createWriteStreamToBlockBlob** - stellt einen Schreibdatenstrom für einen Blockblob bereit.

Im folgenden Beispiel wird der Inhalt der **test.txt**-Datei in **myblob** hochgeladen.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Das von diesen Methoden zurückgegebene  `result` enthält Informationen über den Vorgang wie das **ETag** des Blobs.

### Seitenblobs

Verwenden Sie zum Hochladen von Daten zu einem Seitenblob Folgendes:

* **createPageBlob** - erstellt einen neuen Seitenblob mit einer bestimmten Länge.

* **createPageBlobFromLocalFile** - erstellt einen neuen Seitenblob und lädt die Inhalte einer Datei hoch.

* **createPageBlobFromStream** - erstellt einen neuen Seitenblob und lädt die Inhalte eines Streams hoch.

* **createWriteStreamToExistingPageBlob** - stellt einen Schreibdatenstrom für einen vorhandenen Seitenblob bereit.

* **createWriteStreamToNewPageBlob** - erstellt ein neues Blob und stellt dann einen Stream bereit, um darin schreiben zu können.

Im folgenden Beispiel wird der Inhalt der **test.txt**-Datei in **mypageblob** hochgeladen.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE] Seitenblobs bestehen aus 'pages' von je 512 Byte. Es wird möglicherweise ein Fehler angezeigt, wenn Daten mit einer Größe, die kein Vielfaches von 512 ist, hochgeladen werden.

## Gewusst wie: Auflisten der Blobs in einem Container

Verwenden Sie zum Auflisten der Blobs in einem Container die Methode **listBlobsSegmented**. Verwenden Sie **listBlobsSegmentedWithPrefix**, wenn Sie Blobs mit einem bestimmten Präfix zurückgeben möchten.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

Das  `result` enthält eine  `entries`-Sammlung. Hierbei handelt es sich um ein Array von Objekten, die jedes Blob beschreiben. Wenn nicht alle Blobs zurückgegeben werden können, stellt `result` auch ein `continuationToken` bereit, das als zweiter Parameter zum Abrufen zusätzlicher Einträge verwendet werden kann.

## Gewusst wie: Herunterladen von Blobs

Verwenden Sie zum Herunterladen von Daten aus einem Blob Folgendes:

* **getBlobToFile** - schreibt die Blobinhalte in die Datei

* **getBlobToStream** - schreibt die Blobinhalte in einen Stream.

* **getBlobToText** - schreibt die Blobinhalte in eine Zeichenfolge.

* **createReadStream** - stellt einen Datenstrom bereit, um aus dem Blob zu lesen

Im folgenden Beispiel wird **getBlobToStream** verwendet, um den Inhalt des **myblob**-Blobs herunterzuladen und mithilfe eines Streams in der **output.txt**-Datei zu speichern:

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

Das  `result` enthält Informationen über das Blob, einschließlich der **ETag**-Informationen.

## Vorgehensweise: Löschen eines Blobs

Um ein Blob zu löschen, rufen Sie **deleteBlob** auf. Im folgenden Beispiel wird das Blob namens **myblob** gelöscht.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Gewusst wie: Paralleler Zugriff

Um den parallelen Zugriff auf ein Blob von mehreren Clients oder mehreren Prozessinstanzen aus zu unterstützen, können Sie **ETags** oder **Leases** verwenden.

* **Etag** - bietet eine Möglichkeit, um zu ermitteln, ob das Blob oder der Container durch einen anderen Prozess geändert wurde.

* **Lease** - bietet eine Möglichkeit zum Abrufen eines exklusiven, erneuerbaren Lese- oder Schreibzugriffs auf ein Blob für einen bestimmten Zeitraum.

### ETag

ETags sollten verwendet werden, wenn mehrere Clients oder Instanzen gleichzeitig in das Blob schreiben müssen. Das ETag erlaubt Ihnen zu ermitteln, ob der Container oder das Blob geändert wurde, seitdem Sie es erstmals gelesen oder erstellt haben. Dadurch können Sie Überschreibungsänderungen vermeiden, die durch einen anderen Client oder Prozess verübt wurden.

ETag-Bedingungen können mit dem optionalen `options.accessConditions`-Parameter festgelegt werden. Im folgenden Beispiel wird die **test.txt**-Datei nur hochgeladen, wenn das Blob bereits vorhanden ist und den ETag-Wert aufweist, der in `etagToMatch` enthalten ist.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

Das allgemeine Muster bei der Verwendung von ETags lautet:

1. Rufen Sie das ETag als Ergebnis eines "create"-, "list"- oder "get"-Vorgangs ab.

2. Führen Sie eine Aktion aus, bei der Sie sicherstellen, dass der ETag-Wert nicht geändert wurde.

Wenn der Wert geändert wurde, deutet dies darauf hin, dass ein anderer Client oder eine Instanz das Blob oder den Container geändert hat, seitdem Sie den ETag-Wert abgerufen haben.

### Lease

Eine neue Lease kann mithilfe der Methode **acquireLease** abgerufen werden, die das Blob oder den Container angibt, für das bzw. den Sie eine Lease abrufen möchten. Im Folgenden wird beispielsweise eine Lease auf **myblob** abgerufen.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Nachfolgende Vorgänge auf **myblob** müssen den `options.leaseId`-Parameter angeben. Die Lease-ID wird als  `result.id` von **acquireLease** zurückgegeben.

> [AZURE.NOTE] Die Leasedauer ist standardmäßig unendlich. Sie können eine endliche Dauer (zwischen 15 und 60 Sekunden) angeben, indem Sie den `options.leaseDuration`-Parameter angeben.

Verwenden Sie zum Entfernen einer Lease **releaseLease**. Verwenden Sie **breakLease**, um eine Lease zu unterbrechen und andere Benutzer daran zu hindern, eine neue Lease abzurufen, bis die ursprüngliche Dauer abgelaufen ist.

## Gewusst wie: Arbeiten mit Shared Access Signatures

Shared Access Signature (SAS) ist eine sichere Möglichkeit zum Bereitstellen einen granularen Zugriffs auf Blobs und Container, ohne dass dabei Ihr Speicherkontoname oder -schlüssel angegeben werden müssen. SAS wird oftmals zum Bereitstellen des beschränkten Zugriffs auf Ihre Daten verwendet, dazu zählt beispielsweise auch, einer mobilen App den Zugriff auf Blobs zu gewähren.

> [AZURE.NOTE] Während Sie auch den anonymen Zugriff auf Blobs erlauben können, verleiht Ihnen SAS einen kontrollierteren Zugriff, da Sie die SAS generieren müssen.

Mit einer vertrauenswürdigen Anwendung wie einem cloudbasierten Dienst wird mittels **generateSharedAccessSignature** von **BlobService** eine SAS generiert und einer nicht vertrauenswürdigen oder teilweise vertrauenswürdigen Anwendung bereitgestellt. Zum Beispiel für eine mobile App. Die SAS wird mithilfe einer Richtlinie generiert, die das Anfangs- und das Enddatum der Gültigkeit der SAS sowie die Zugriffsstufe definiert, die dem Inhaber der SAS gewährt wird.

Im folgenden Beispiel wird eine neue Richtlinie für den freigegebenen Zugriff generiert, mit der der SAS-Inhaber Lesevorgänge im **myblob**-Blob vornehmen kann und die 100 Minuten nach ihrer Erstellung abläuft.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Beachten Sie, dass auch die Hostinformationen angegeben werden müssen, da sie erforderlich sind, wenn der SAS-Inhaber versucht, auf den Container zuzugreifen.

Die Clientanwendung verwendet dann die SAS mit **BlobServiceWithSAS**, um Vorgänge für das Blob auszuführen. Im Folgenden werden Informationen über **myblob** abgerufen.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Da die SAS nur mit Lesezugriff generiert wurde, würde beim Versuch, das Blob zu ändern, ein Fehler zurückgegeben werden.

### Zugriffssteuerungslisten

Sie können auch eine Zugriffssteuerungsliste (Access Control List, ACL) verwenden, um die Zugriffsrichtlinie für eine SAS festzulegen. Dies ist nützlich, wenn mehrere Clients auf einen Container zugreifen sollen und für jeden Client unterschiedliche Zugriffsrichtlinien bereitgestellt werden.

Eine ACL wird in einem Array von Zugriffsrichtlinien implementiert, wobei jeder Richtlinie eine ID zugeordnet wird. Im folgenden Beispiel werden zwei Richtlinien definiert, eine für 'user1' und eine für 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

Im folgenden Beispiel wird die aktuelle ACL für **mycontainer** abgerufen. Anschließend werden die neuen Richtlinien mithilfe von **setBlobAcl** hinzugefügt. Dieser Ansatz ermöglicht Folgendes:

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Nachdem die ACL festgelegt wurde, können Sie basierend auf der ID für eine Richtlinie eine SAS erstellen. Im folgenden Beispiel wird eine neue SAS für 'user2' erstellt:

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Blobspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Lesen Sie die [API-Referenz zum Azure Storage-SDK für Node][]
-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][]
-   Besuchen Sie den [Blog des Azure-Speicherteams][].
-   Besuchen Sie das [Azure Storage-SDK für Node][]-Repository auf GitHub.

[Azure Storage-SDK für Node]: https://github.com/Azure/azure-storage-node
[Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js-Cloud-Dienst mit Storage]: storage-nodejs-use-table-storage-cloud-service-app.md
[Node.js-Webanwendung mit Storage]: storage-nodejs-use-table-storage-web-site.md
[Website mit WebMatrix]: web-sites-nodejs-use-webmatrix.md
[mit der REST-API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[Node.js-Cloud-Dienst]: cloud-services-nodejs-develop-deploy-app.md
[Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
[API-Referenz zum Azure Storage-SDK für Node]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!--HONumber=49-->