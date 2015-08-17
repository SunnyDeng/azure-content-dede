<properties
	pageTitle="Verwenden von Notification Hubs mit Node.js"
	description="Erfahren Sie, wie Sie Notification Hubs verwenden, um Pushbenachrichtigungen aus einer Node.js-Anwendung zu senden."
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="wesmc"/>

# Verwenden von Notification Hubs mit Node.js

> [AZURE.SELECTOR]
- [Java](notification-hubs-java-backend-how-to.md)
- [PHP](notification-hubs-php-backend-how-to.md)
- [Python](notification-hubs-python-backend-how-to)

##Übersicht

In diesem Leitfaden erfahren Sie, wie Sie Notification Hubs in Node.js-Anwendungen verwenden. Die behandelten Szenarien umfassen den **Versand von Nachrichten an Android-, iOS-, Windows Phone- und Windows Store-Apps**. Weitere Informationen zu Notification Hubs finden Sie im Abschnitt [Nächste Schritte](#next).

##Was sind Notification Hubs?

Azure Notification Hubs bieten eine einfache, plattformübergreifende und skalierbare Infrastruktur für den Versand von Pushbenachrichtigungen an mobile Geräte. Weitere Informationen finden Sie unter [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website][nodejswebsite], [Node.js-Clouddienst][Node.js Cloud Service] (mit Windows PowerShell) oder [Website mit WebMatrix].

##Konfigurieren einer Anwendung für Notification Hubs

Um Azure Notification Hubs zu verwenden, müssen Sie das Node.js-Azure-Paket herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den REST-Diensten kommunizieren.

### Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe dieses Befehls sollte wie folgt aussehen:

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Sie können den Befehl **ls** bzw. **dir** manuell ausführen, um zu überprüfen, ob der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie ein **azure**-Paket, das die für den Zugriff auf Notification Hubs benötigten Bibliotheken enthält.

### Importieren des Moduls

Verwenden Sie einen Texteditor, um den folgenden Code am Anfang der Datei **server.js** der Anwendung hinzuzufügen:

    var azure = require('azure');

### Einrichten einer Azure Notification Hub-Verbindung

Das **NotificationHubService**-Objekt ermöglicht Ihnen das Arbeiten mit Notification Hubs. Der folgende Code erstellt ein **NotificationHubService**-Objekt für ein Notification Hub mit dem Namen **hubname**. Fügen Sie ihn am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Führen Sie die folgenden Schritte im Azure-Verwaltungsportal durch, um den Wert für **connectionstring** zu erhalten:

1. Wählen Sie im Azure-Verwaltungsportal **Service Bus** aus, und wählen Sie anschließend den Namespace aus, der das Notification Hub enthält.

2. Klicken Sie auf **NOTIFICATION HUBS** und wählen Sie das gewünschte Hub aus.

3. Klicken Sie unter **Auf einen Blick** unter **Verbindungszeichenfolge anzeigen**, und kopieren Sie den Wert der Verbindungszeichenfolge.

> [AZURE.NOTE]Sie können die Verbindungszeichenfolge auch mit dem **Get-AzureSbNamespace**-Cmdlet in Azure PowerShell oder dem Befehl **azure sb namespace show** in der Azure-Befehlszeilenschnittstelle (Azure CLI) abrufen.

</div>

##Senden von Benachrichtigungen

Das **NotificationHubService**-Objekt enthält die folgenden Objektinstanzen für den Versand von Benachrichtigungen an bestimmte Geräte und Anwendungen:

* **Android** - verwenden Sie das **GcmService**-Objekt unter **notificationHubService.gcm**
* **iOS** - verwenden Sie das **ApnsService**-Objekt unter **notificationHubService.apns**
* **Windows Phone** - verwenden Sie das **MpnsService**-Objekt unter **notificationHubService.mpns**
* **Windows Store-Apps** - verwenden Sie das **WnsService**-Objekt unter **notificationHubService.wns**

### Senden von Benachrichtigungen an Android-Apps

Das **GcmService**-Objekt enthält eine **send**-Methode, mit der Sie Benachrichtigungen an Android-Apps verschicken können. Die **send**-Methode nimmt die folgenden Parameter entgegen:

* Tags – die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
* Payload - die Nutzlast der Nachricht als JSON oder String
* Callback - die Rückruffunktion

Weitere Informationen zum Nutzlastformat finden Sie im Abschnitt Nutzlast unter [Implementing GCM Server](http://developer.android.com/google/gcm/server.html#payload) (in englischer Sprache).

Der folgende Code verwendet die **GcmService**-Instanz aus **NotificationHubService**, um eine Nachricht an alle Clients zu schicken.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Senden von Benachrichtigungen an iOS-Apps

Das **ApnsService**-Objekt enthält eine **send**-Methode, mit der Sie Benachrichtigungen an iOS-Apps verschicken können. Die **send**-Methode nimmt die folgenden Parameter entgegen:

* Tags – die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
* Payload - die Nutzlast der Nachricht als JSON oder String
* Callback - die Rückruffunktion

Weitere Informationen zum Nutzlastformat finden Sie im Abschnitt Benachrichtigungs-Nutzlast unter [Local and Push Notification Programming Guide](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) (in englischer Sprache).

Der folgende Code verwendet die **ApnsService**-Instanz aus **NotificationHubService**, um eine Warnungs-Nachricht an alle Clients zu schicken:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Senden von Benachrichtigungen an Windows Phone-Apps

Das **MpnsService**-Objekt enthält eine **send**-Methode, mit der Sie Benachrichtigungen an Windows Phone-Apps verschicken können. Die **send**-Methode nimmt die folgenden Parameter entgegen:

* Tags – die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
* Payload - die Nutzlast der Nachricht als XML
* TargetName - 'toast' für Popupbenachrichtigungen. 'token' für Kachelbenachrichtigungen.
* NotificationClass - Die Priorität der Benachrichtigung. Eine Liste der gültigen Werte finden Sie im Bereich "HTTP Header Elements" von [Push notifications from a server](http://msdn.microsoft.com/library/hh221551.aspx) (in englischer Sprache).
* Options - zusätzliche Anforderungsheader
* Callback - die Rückruffunktion

Eine Liste der gültigen Optionen für TargetName, NotificationClass und header finden Sie unter [Push notifications from a server](http://msdn.microsoft.com/library/hh221551.aspx) (in englischer Sprache).

Der folgende Code verwendet die **MpnsService**-Instanz aus **NotificationHubService**, um ein Popup zu schicken:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Senden von Benachrichtigungen an Windows Store-Apps

Das **WnsService**-Objekt enthält eine **send**-Methode, mit der Sie Benachrichtigungen an Windows Store-Apps verschicken können. Die **send**-Methode nimmt die folgenden Parameter entgegen:

* Tags – die Tag-ID. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
* Payload - die Nutzlast der Nachricht als XML
* Type - der Benachrichtigungstyp
* Options - zusätzliche Anforderungsheader
* Callback - die Rückruffunktion

Eine Liste der gültigen Typen und Anforderungsheader finden Sie unter [Anforderungs- und Antwortheader des Pushbenachrichtigungsdiensts](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Der folgende Code verwendet die **WnsService**-Instanz aus **NotificationHubService**, um ein Popup zu schicken:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Nächste Schritte

Nachdem Sie nun mit der Verwendung von Notification Hubs grundlegend vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Azure Notification Hubs][].
-   Besuchen Sie das [Azure SDK für Node]-Repository auf GitHub.

  [Azure SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Management Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Website mit WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
 

<!---HONumber=August15_HO6-->