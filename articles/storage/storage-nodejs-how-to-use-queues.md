<properties 
	pageTitle="Verwenden des Warteschlangenspeichers mit Node.js | Microsoft Azure" 
	description="Erfahren Sie, wie Sie mit dem Azure-Warteschlangendienst Warteschlangen erstellen und löschen sowie Nachrichten einfügen, abrufen und löschen können. Die Beispiele sind in Node.js geschrieben." 
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


# Verwenden des Warteschlangenspeichers mit Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Warteschlangendienstes in Azure demonstriert. Die Beispiele wurden mit Node.js-API erstellt
. Die aufgeführten Szenarien umfassen das **Einfügen**, **Einsehen**,
**Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und 
Löschen von Warteschlangen**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], in [Node.js-Cloud-Dienst][Node.js-Cloud-Dienst] (mithilfe von Windows PowerShell) oder auf der [Website mit WebMatrix].

## Konfigurieren der Anwendung für den Speicherzugriff

Um Azure-Speicher verwenden zu können, müssen Sie das Azure Storage-SDK für Node.js herunterladen. Es enthält eine Reihe von Bibliotheken,
die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

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
    Ordner **node_modules** erstellt wurde. In diesem Ordner finden Sie
    das **Azure-Speicher**-Paket, das die für den Speicherzugriff benötigten Bibliotheken
    enthält.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei
**server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

    var azure = require('azure-storage');

## Einrichten einer Azure Storage-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE_STORAGE_ACCOUNT and AZURE_STORAGE_ACCESS_KEY oder AZURE_STORAGE_CONNECTION_STRING die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createQueueService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher].

## Gewusst wie: Erstellen einer Warteschlange

Über den folgenden Code wird ein **QueueService**-Objekt erstellt, das Ihnen ermöglicht,
mit Warteschlangen zu arbeiten.

    var queueSvc = azure.createQueueService();

Verwenden Sie die **createQueueIfNotExists**-Methode, mit der die angegebene
Warteschlange zurückgegeben wird, wenn sie bereits vorhanden ist, oder mit der eine neue Warteschlange mit dem angegebenen
Namen erstellt wird, sollte noch keine Warteschlange vorhanden sein.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Wenn die Warteschlange erstellt wurde, ist  `result` "True". Wenn die Warteschlange bereits vorhanden ist, ist  `result` "False".

### Filter

Auf die Vorgänge, die mit **QueueService** ausgeführt werden, können optionale Filtervorgänge angewendet werden. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, mit denen eine Methode mit einer Signatur implementiert wird:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, falls vorhanden, um weitere Filter zu verarbeiten, oder andernfalls einfach "finalCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Mit folgendem Code wird ein **QueueService**-Objekt erstellt, das **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## Gewusst wie: Einfügen einer Nachricht in eine Warteschlange

Um eine Nachricht in eine Warteschlange einzufügen, verwenden Sie die **createMessage**-Methode, um
eine neue Nachricht zu erstellen und sie zur Warteschlange hinzufügen.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## Gewusst wie: Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen,
indem Sie die Methode **peekMessages** aufrufen. Standardmäßig
wird über**peekMessages**jeweils nur eine Nachricht eingesehen.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Messages peeked
	  }
	});

Das  `result` enthält die Nachricht.

> [AZURE.NOTE] Wenn Sie **peekMessages** verwenden und keine Nachrichten in der Warteschlange vorhanden sind, werden weder Fehler noch Nachrichten zurückgegeben.

## Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange

Das Verarbeiten einer Nachricht besteht aus zwei Stufen:

1. Entfernen der Nachricht aus der Warteschlange.

2. Löschen der Nachricht.

Verwenden Sie **getMessage**, um eine Nachricht aus der Warteschlange zu entfernen. Dadurch wird die Nachricht in der Warteschlange unsichtbar, sodass sie nicht durch andere Clients verarbeitet werden kann. Sobald Ihre Anwendung die Nachricht verarbeitet hat, rufen Sie **deleteMessage** auf, um sie aus der Warteschlange zu löschen. Im folgenden Beispiel wird eine Nachricht abgerufen und anschließend gelöscht:

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [AZURE.NOTE] Standardmäßig wird eine Nachricht nur für 30 Sekunden ausgeblendet, danach ist sie für andere Client sichtbar. Sie können einen anderen Wert angeben, indem Sie `options.visibilityTimeout` mit **getMessages** verwenden.

> [AZURE.NOTE]
> Wenn Sie <b>getMessages</b> verwenden und keine Nachrichten in der Warteschlange vorhanden sind, werden weder Fehler noch Nachrichten zurückgegeben.

## Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht direkt in der Warteschlange ändern, indem Sie **updateMessage** verwenden. Im folgenden Beispiel wird der Text einer Nachricht aktualisiert:

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## Gewusst wie: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können:

* `options.numOfMessages` - Abrufen eines Stapels an Nachrichten (bis zu 32).
* `options.visibilityTimeout` - Festlegen eines längeren oder kürzeren Unsichtbarkeits-Zeitlimits.

Im folgenden Beispiel wird die **getMessages**-Methode verwendet, um 15 Nachrichten pro Aufruf abzurufen. Anschließend werden die
Nachrichten in einer Schleife verarbeitet. Zudem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten für alle Nachrichten festgelegt, die durch diese Methode zurückgegeben werden.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## Gewusst wie: Abrufen der Warteschlangenlänge

Über **getQueueMetadata** werden Metadaten über die Warteschlange zurückgegeben, einschließlich der geschätzten Anzahl der Nachrichten in der Warteschlange.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## Gewusst wie: Auflisten von Warteschlangen

Verwenden Sie **listQueuesSegmented** zum Aufrufen einer Warteschlangenliste. Verwenden Sie **listQueuesSegmentedWithPrefix**, um eine Liste abzurufen, die nach einem bestimmten Präfix gefiltert ist.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Wenn nicht alle Warteschlangen zurückgegeben werden können, kann `result.continuationToken` als erster Parameter von **listQueuesSegmented** oder als zweiter Parameter von **listQueuesSegmentedWithPrefix** verwendet werden, um weitere Ergebnisse abzurufen.

## Gewusst wie: Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die
**deleteQueue**-Methode für das Warteschlangenobjekt.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Verwenden Sie **clearMessages**, um alle Nachrichten aus einer Warteschlange zu entfernen, ohne diese zu löschen.

## Gewusst wie: Arbeiten mit Shared Access Signatures

Shared Access Signature (SAS) ist eine sichere Möglichkeit zum Bereitstellen einen granularen Zugriffs auf Warteschlangen, ohne dass dabei Ihr Speicherkontoname oder -schlüssel angegeben werden müssen. SAS wird oftmals zum Bereitstellen des beschränkten Zugriffs auf Ihre Warteschlangen verwendet, dazu zählt beispielsweise auch, einer mobilen App das Senden von Nachrichten zu erlauben.

Mit einer vertrauenswürdigen Anwendung wie einem cloudbasierten Dienst wird mittels **generateSharedAccessSignature** von **QueueService** eine SAS generiert und einer nicht vertrauenswürdigen oder teilweise vertrauenswürdigen Anwendung bereitgestellt. Zum Beispiel für eine mobile App. Die SAS wird mithilfe einer Richtlinie generiert, die das Anfangs- und das Enddatum der Gültigkeit der SAS sowie die Zugriffsstufe definiert, die dem Inhaber der SAS gewährt wird.

Im folgenden Beispiel wird eine neue Richtlinie für den geteilten Zugriff generiert, wodurch der SAS-Inhaber der Warteschlange Nachrichten hinzufügen kann, die 100 Minuten nach ihrer Erstellung abläuft.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

Beachten Sie, dass auch die Hostinformationen angegeben werden müssen, da sie erforderlich sind, wenn der SAS-Inhaber versucht, auf die Warteschlange zuzugreifen.

Über die Clientanwendung wird die SAS dann zusammen mit **QueueServiceWithSAS** verwendet, um Vorgänge für die Warteschlange auszuführen. Im folgenden Beispiel wird eine Verbindung zur Warteschlange hergestellt, und es wird eine Nachricht erstellt.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Da die SAS mit "add access" generiert wurde, würde beim Versuch, Nachrichten zu lesen, zu aktualisieren oder zu löschen, ein Fehler angezeigt werden.

### Zugriffssteuerungslisten

Sie können auch eine Zugriffssteuerungsliste (Access Control List, ACL) verwenden, um die Zugriffsrichtlinie für eine SAS festzulegen. Dies ist nützlich, wenn mehrere Clients auf die Warteschlange zugreifen sollen und für jeden Client unterschiedliche Zugriffsrichtlinien bereitgestellt werden.

Eine ACL wird in einem Array von Zugriffsrichtlinien implementiert, wobei jeder Richtlinie eine ID zugeordnet wird. Im folgenden Beispiel werden zwei Richtlinien definiert, eine für 'user1' und eine für 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

Im folgenden Beispiel wird die aktuelle ACL für **myqueue** abgerufen. Anschließend werden die neuen Richtlinien mithilfe von **setQueueAcl** hinzugefügt. Dieser Ansatz ermöglicht Folgendes:

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Nachdem die ACL festgelegt wurde, können Sie basierend auf der ID für eine Richtlinie eine SAS erstellen. Im folgenden Beispiel wird eine neue SAS für 'user2' erstellt:

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Warteschlangenspeicherung vertraut gemacht haben, folgen Sie diesen Links,
um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][]
-   Besuchen Sie den [Blog des Azure-Speicherteams][].
-   Besuchen Sie das [Azure Storage-SDK für Node][]-Repository auf GitHub.

  [Azure Storage-SDK für Node]: https://github.com/Azure/azure-storage-node
  [mithilfe der REST-API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: ../web-sites-nodejs-develop-deploy-mac.md
  [Node.js-Cloud-Dienst mit Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js-Webanwendung mit Storage]: ../storage-nodejs-use-table-storage-web-site.md

  
  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png
  
  
  
  [Node.js-Cloud-Dienst]: ../cloud-services-nodejs-develop-deploy-app.md
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
 [Website mit WebMatrix]: ../web-sites-nodejs-use-webmatrix.md

<!--HONumber=49--> 