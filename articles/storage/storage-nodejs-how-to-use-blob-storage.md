<properties
	pageTitle="Verwenden des Blob-Speichers mit Node.js | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Azure-Blob-Dienst zum Hochladen, Herunterladen, Auflisten und Löschen von Blobinhalten verwenden. Die Beispiele sind in Node.js geschrieben."
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
	ms.date="09/01/2015"
	ms.author="mwasson"/>



# Verwenden des Blob-Speichers mit Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

Dieser Artikel demonstriert Ihnen allgemeine Szenarien unter Verwendung des Azure-Blob-Dienstes. Die Beispiele werden über die Node.js-API geschrieben. Die behandelten Szenarien umfassen das Hochladen, Auflisten, Herunterladen und Löschen von Blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Node.js-Anwendung

Anleitungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Cloudsienst][Node.js Cloud Service] (mithilfe von Windows PowerShell) oder [Web-App mit WebMatrix].

## Konfigurieren der Anwendung für den Speicherzugriff

Um Azure-Speicher verwenden zu können, müssen Sie das Azure Storage-SDK für Node.js herunterladen. Es enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure-storage** in das Befehlsfenster ein. Die Ausgabe des Befehls ähnelt dem folgenden Codebeispiel.

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie das Paket **azure-storage** mit den Bibliotheken, die Sie benötigen, um auf den Speicher zuzugreifen.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

    var azure = require('azure-storage');

## Einrichten einer Azure-Speicherverbindung

Das Azure-Modul entnimmt den Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_ACCESS_KEY` oder `AZURE_STORAGE_CONNECTION_STRING` die erforderlichen Informationen zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto. Falls diese Umgebungsvariablen nicht gesetzt sind, müssen Sie die Kontoinformationen beim Aufruf von **createBlobService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen im Azure-Portal für eine Azure-Web-App finden Sie unter [Node.js-Web-Anwendung mit Storage].

## Erstellen eines Containers

Mit dem **BlobService**-Objekt können Sie auf Container und Blobs zugreifen. Der folgende Code erstellt ein **BlobService**-Objekt. Fügen Sie die folgende Zeile am Anfang von **server.js** hinzu:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE]Sie können anonym auf ein Blob zugreifen, indem Sie **createBlobServiceAnonymous** verwenden und die Hostadresse angeben. Verwenden Sie z. B. `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Verwenden Sie zum Erstellen eines neuen Containers **createContainerIfNotExists**. Im folgenden Codebeispiel wird ein neuer Container namens „mycontainer“ erstellt:

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows
        // anonymous read access to blob
        // content and metadata within this container
      }
	});

Wenn der Container neu erstellt wird, weist `result` den Wert „true“ auf. Wenn der Container bereits vorhanden ist, hat `result` den Wert „false“. `response` enthält Informationen über den Vorgang, einschließlich der [ETag](http://en.wikipedia.org/wiki/HTTP_ETag)-Informationen für den Container.

### Containersicherheit

Neue Container sind standardmäßig privat. Ein anonymer Zugriff ist auf sie so nicht möglich. Um den Container öffentlich zu machen, damit ein anonymer Zugriff darauf möglich ist, können Sie die Zugriffsebene des Containers auf **blob** oder **container** festlegen.

* **blob** – ermöglicht den anonymen Lesezugriff auf Blobinhalte und Metadaten in diesem Container, aber nicht auf Containermetadaten wie Listen aller Blobs in einem Container.

* **container** – ermöglicht den anonymen Lesezugriff auf Blobinhalte und Metadaten sowie auf Containermetadaten.

Das folgende Codebeispiel zeigt, wie Sie die Zugriffsebene auf **blob** setzen:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

Alternativ können Sie die Zugriffsebene eines Containers mit **setContainerAcl** angeben. Das folgende Codebeispiel ändert die Zugriffsebene des Containers:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Das Ergebnis enthält Informationen über den Vorgang, einschließlich der **ETag**-Informationen für den Container.

### Filter

Sie können optionale Filtervorgänge auf Vorgänge anwenden, die mithilfe von **BlobService** ausgeführt werden. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit der folgenden Signatur implementieren:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen abgeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das "returnObject"-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder es muss einfach "finallCallback" aufgerufen werden, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **BlobService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Hochladen eines Blobs in einen Container

Ein Blob kann entweder block- oder seitenbasiert sein. Blockblobs ermöglichen Ihnen das effizientere Hochladen großer Daten. Seitenblobs hingegen sind für Lese-/Schreibvorgänge optimiert. Weitere Informationen finden Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### Blockblobs

Verwenden Sie zum Hochladen von Daten zu einem Blockblob Folgendes:

* **createBlockBlobFromLocalFile** – erstellt einen neuen Blockblob und lädt die Inhalte einer Datei hoch.

* **createBlockBlobFromStream** – erstellt einen neuen Blockblob und lädt die Inhalte eines Datenstroms hoch.

* **createBlockBlobFromText** – erstellt einen neuen Blockblob und lädt die Inhalte einer Zeichenfolge hoch.

* **createWriteStreamToBlockBlob** – stellt einen Schreibdatenstrom für einen Blockblob bereit.

Das folgende Codebeispiel lädt den Inhalt der Datei **test.txt** in **myblob** hoch.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Das von diesen Methoden zurückgegebene `result` enthält Informationen über den Vorgang wie z. B. das **ETag** des Blobs.

### Seitenblobs

Verwenden Sie zum Hochladen von Daten zu einem Seitenblob Folgendes:

* **createPageBlob** – erstellt einen neuen Seitenblob mit einer bestimmten Länge.

* **createPageBlobFromLocalFile** – erstellt einen neuen Seitenblob und lädt die Inhalte einer Datei hoch.

* **createPageBlobFromStream** – erstellt einen neuen Seitenblob und lädt die Inhalte eines Datenstroms hoch.

* **createWriteStreamToExistingPageBlob** – stellt einen Schreibdatenstrom für einen vorhandenen Seitenblob bereit.

* **createWriteStreamToNewPageBlob** – erstellt einen neuen Blob und stellt dann einen Datenstrom bereit, um darin schreiben zu können.

Das folgende Codebeispiel lädt den Inhalt der Datei **test.txt** in **mypageblob** hoch.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE]Seitenblobs bestehen aus 512-Byte-"Seiten". Es wird möglicherweise ein Fehler angezeigt, wenn Daten mit einer Größe, die kein Vielfaches von 512 ist, hochgeladen werden.

## Auflisten der Blobs in einem Container

Verwenden Sie zum Auflisten des Blobs in einem Container die Methode **listBlobsSegmented**. Verwenden Sie **listBlobsSegmentedWithPrefix**, wenn Sie Blobs mit einem bestimmten Präfix ausgeben möchten.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result.entries contains the entries
        // If not all blobs were returned, result.continuationToken has the continuation token.
	  }
	});

Das `result` enthält eine `entries`-Sammlung. Hierbei handelt es sich um ein Array von Objekten, die die einzelnen Blobs beschreiben. Wenn nicht alle Blobs zurückgegeben werden können, stellt das `result` auch ein `continuationToken` bereit, das als zweiter Parameter zum Abrufen zusätzlicher Einträge verwendet werden kann.

## Herunterladen von Blobs

Verwenden Sie zum Herunterladen von Daten aus einem Blob Folgendes:

* **getBlobToLocalFile** – schreibt die Blobinhalte in eine Datei.

* **getBlobToStream** – schreibt die Blobinhalte in einen Datenstrom.

* **getBlobToText** – schreibt die Blobinhalte in eine Zeichenfolge.

* **createReadStream** – stellt einen Datenstrom bereit, um aus dem Blob zu lesen.

Das folgende Codebeispiel verwendet **getBlobToStream**, um den Inhalt des **myblob**-Blobs herunterzuladen und mithilfe eines Datenstroms in der **output.txt**-Datei zu speichern:

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

Das `result` enthält Informationen zum Blob, einschließlich der **ETag**-Informationen.

## Löschen eines Blobs

Um einen Blob zu löschen, rufen Sie **deleteBlob** auf. Im folgenden Codebeispiel wird das Blob namens **myblob** gelöscht.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Paralleler Zugriff

Um den parallelen Zugriff auf eine Blob von mehreren Clients oder mehreren Prozessinstanzen zu unterstützen, können Sie **ETags** oder **Leases** verwenden.

* **Etag** – bietet eine Möglichkeit, um zu ermitteln, dass das Blob oder der Container durch einen anderen Prozess geändert wurde.

* **Lease** – bietet eine Möglichkeit zum Abrufen eines exklusiven, erneuerbaren Lese- oder Schreibzugriffs auf ein Blob für einen Zeitraum.

### ETag

Verwenden Sie ETags, wenn mehrere Clients oder Instanzen gleichzeitig in das Blob schreiben müssen. Das ETag erlaubt Ihnen zu ermitteln, ob der Container oder das Blob geändert wurde, seitdem Sie es erstmals gelesen oder erstellt haben. Dadurch können Sie Überschreibungsänderungen vermeiden, die durch einen anderen Client oder Prozess verübt wurden.

Sie können ETag-Bedingungen mithilfe des optionalen `options.accessConditions`-Parameters festlegen. Im folgenden Codebeispiel wird die Datei **test.txt** nur hochgeladen, wenn das Blob bereits vorhanden ist und den ETag-Wert aufweist, der in `etagToMatch` enthalten ist.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

Wenn Sie ETags verwenden, lautet das allgemeine Muster:

1. Rufen Sie das ETag als Ergebnis eines "create"-, "list"- oder "get"-Vorgangs ab.

2. Führen Sie eine Aktion aus, bei der Sie sicherstellen, dass der ETag-Wert nicht geändert wurde.

Wenn der Wert geändert wurde, deutet dies darauf hin, dass ein anderer Client oder eine Instanz das Blob oder den Container geändert hat, seitdem Sie den ETag-Wert abgerufen haben.

### Lease

Sie können eine neue Lease mithilfe der Methode **acquireLease** abrufen, die das Blob oder den Container angibt, für das bzw. den Sie eine Lease abrufen möchten. Mit dem folgenden Code wird beispielsweise eine Lease auf **myblob** abgerufen.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Nachfolgende Vorgänge mit **myblob** müssen den `options.leaseId`-Parameter angeben. Die Lease-ID wird als `result.id` von **acquireLease** zurückgegeben.

> [AZURE.NOTE]Die Leasedauer ist standardmäßig unendlich. Sie können eine endliche Dauer (zwischen 15 und 60 Sekunden) festlegen, indem Sie den `options.leaseDuration`-Parameter angeben.

Verwenden Sie zum Entfernen einer Lease **releaseLease**. Zum Unterbrechen einer Lease, während andere darin gehindert werden sollen, eine neue Lease abzurufen, bis die ursprüngliche Dauer abgelaufen ist, verwenden Sie **breakLease**.

## Arbeiten mit Shared Access Signatures

Shared Access Signatures (SAS) sind eine sichere Möglichkeit zum Bereitstellen einen granularen Zugriffs auf Blobs und Container, ohne dass dabei Ihr Speicherkontoname oder -schlüssel angegeben werden müssen. Shared Access Signatures werden oftmals zum Bereitstellen des beschränkten Zugriffs auf Ihre Daten verwendet, dazu zählt beispielsweise auch, einer mobilen App den Zugriff auf Blobs zu gewähren.

> [AZURE.NOTE]Während Sie auch den anonymen Zugriff auf Blobs erlauben können, verleihen Ihnen Shared Access Signatures einen kontrollierteren Zugriff, da Sie die Shared Access Signatures generieren müssen.

Eine vertrauenswürdige Anwendung wie ein cloudbasierter Dienst generiert mittels **generateSharedAccessSignature** von **BlobService** Shared Access Signatures und stellt diese für eine nicht vertrauenswürdige oder teilweise vertrauenswürdige Anwendung wie beispielsweise eine mobile App bereit. Shared Access Signatures werden mithilfe einer Richtlinie generiert, die das Anfangs- und das Enddatum der Gültigkeit der Shared Access Signatures sowie die Zugriffsstufe definiert, die dem Inhaber der Shared Access Signatures gewährt wird.

Im folgenden Codebeispiel wird eine neue Richtlinie für den geteilten Zugriff generiert, wodurch der Inhaber von Shared Access Signatures Lesevorgänge im **myblob**-Blob vornehmen kann, die 100 Minuten nach ihrer Erstellung abläuft.

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

Beachten Sie, dass auch die Hostinformationen angegeben werden müssen, da sie erforderlich sind, wenn der Inhaber von Shared Access Signatures versucht, auf den Container zuzugreifen.

Die Clientanwendung verwendet dann die Shared Access Signatures mit **BlobServiceWithSAS**, um Vorgänge auf das Blob auszuführen. Im Folgenden werden Informationen über **myblob** abgerufen.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Da Shared Access Signatures mit einem schreibgeschützten Zugriff generiert wurden, wird ein Fehler bei dem Versuch zurückgegeben, den Blob zu ändern.

### Zugriffssteuerungslisten

Sie können auch eine Zugriffssteuerungsliste (Access Control List, ACL) verwenden, um die Zugriffsrichtlinie für SAS festzulegen. Dies ist nützlich, wenn mehrere Clients auf einen Container zugreifen sollen und für jeden Client unterschiedliche Zugriffsrichtlinien bereitgestellt werden.

Eine ACL wird in einem Array von Zugriffsrichtlinien implementiert, wobei jeder Richtlinie eine ID zugeordnet wird. Im folgenden Codebeispiel werden zwei Richtlinien definiert, eine für „user1“ und eine für „user2“:

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

Im folgenden Codebeispiel wird die aktuelle Zugriffssteuerungsliste für **mycontainer** abgerufen. Anschließend werden die neuen Richtlinien mithilfe von **setBlobAcl** hinzugefügt. Dieser Ansatz ermöglicht Folgendes:

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

Nachdem die ACL festgelegt wurde, können Sie basierend auf der ID für eine Richtlinie Shared Access Signatures erstellen. Im folgenden Codebeispiel werden neue Shared Access Signatures für „user2“ erstellt:

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen.

-   [API-Referenz zum Azure Storage-SDK für Node][]
-   MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][]
-   [Azure Storage-Teamblog][]
-   [Azure Storage SDK for Node.js][]-Repository auf GitHub (in englischer Sprache)
-   [Node.js Developer Center](/develop/nodejs/)

[Azure Storage SDK for Node.js]: https://github.com/Azure/azure-storage-node
[Create and deploy a Node.js application to an Azure Web Site]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
[Node.js-Web-Anwendung mit Storage]: ../storage-nodejs-use-table-storage-web-site.md
[Web-App mit WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure portal]: http://manage.windowsazure.com
[Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
[API-Referenz zum Azure Storage-SDK für Node]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!---HONumber=Oct15_HO4-->