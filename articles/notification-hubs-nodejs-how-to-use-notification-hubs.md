<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Notification Hubs" pageTitle="Notification Hubs - Node.js Dev Center" metaKeywords="" description="Learn how to use Notification Hubs to send push notifications. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Notification Hubs" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Verwenden von Notification Hubs

In diesem Leitfaden erfahren Sie, wie Sie Notification Hubs
in Node.js-Anwendungen verwenden. Die behandelten Szenarien umfassen den **Versand von Nachrichten an Android-, iOS-, Windows Phone- und Windows Store-Apps**. Weitere Informationen zu Notification Hubs finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
-   [Erstellen einer Node.js-Anwendung][Erstellen einer Node.js-Anwendung]
-   [Konfigurieren einer Anwendung für Notification Hubs][Konfigurieren einer Anwendung für Notification Hubs]
-   [Gewusst wie: Senden von Benachrichtigungen][Gewusst wie: Senden von Benachrichtigungen]
-   [Nächste Schritte][Nächste Schritte]

## <span id="hub"></span></a> Was sind Notification Hubs?

Azure Notification Hubs bieten eine einfache, plattformübergreifende und skalierbare Infrastruktur für den Versand von Pushbenachrichtigungen an mobile Geräte. Weitere Informationen finden Sie unter [Azure Notification Hubs][Azure Notification Hubs].

## <span id="create"></span></a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website][Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Clouddienst][Node.js-Clouddienst] (mit Windows PowerShell) oder [Website mit WebMatrix][Website mit WebMatrix].

## <span id="config"></span></a> Konfigurieren einer Anwendung für Notification Hubs

Um Azure Notification Hubs zu verwenden, müssen Sie das
Node.js-Azure-Paket herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken,
die mit den REST-Diensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe
    dieses Befehls sollte wie folgt aussehen:

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

3.  Sie können den Befehl **ls** bzw. **dir** manuell ausführen, um zu überprüfen, ob der Ordner
    **node\_modules** erstellt wurde. In diesem Ordner finden Sie ein
    **azure**-Paket, das die für den Zugriff auf Notification Hubs
    benötigten Bibliotheken enthält.

### Importieren des Moduls

Fügen Sie mit einem Texteditor den folgenden Code
am Anfang der Datei **server.js** der Anwendung hinzu:

    var azure = require('azure');

### Einrichten einer Azure Notification Hub-Verbindung

Das **NotificationHubService**-Objekt ermöglicht Ihnen das Arbeiten mit Notification Hubs. Der
folgende Code erstellt ein **NotificationHubService**-Objekt für einen Notification Hub mit dem Namen **hubname**. Fügen Sie es am
Anfang der Datei **server.js** hinzu, nach der Anweisung zum
Importieren des Azure-Moduls:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Führen Sie die folgenden Schritte im Azure-Verwaltungsportal durch, um den Wert für **connectionstring** zu erhalten:

1.  Wählen Sie im Azure-Verwaltungsportal **Service Bus** aus, und wählen Sie anschließend den Namespace aus, der das Notification Hub enthält.

2.  Klicken Sie auf **NOTIFICATION HUBS** und wählen Sie das gewünschte Hub aus.

3.  Klicken Sie unter **Auf einen Blick** unter **Verbindungszeichenfolge anzeigen**, und kopieren Sie den Wert der Verbindungszeichenfolge.

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Sie k&ouml;nnen die Verbindungszeichenfolge auch mit dem <b>Get-AzureSbNamespace</b>-Cmdlet in Azure PowerShell oder dem <b>azure sb namespace show</b>-Befehl in den Azure-Befehlszeilentools abrufen.</p>

</div>

## <span id="send"></span></a> Senden von Benachrichtigungen

Das **NotificationHubService**-Objekt enthält die folgenden Objektinstanzen für den Versand von Benachrichtigungen an bestimmte Geräte und Anwendungen:

-   **Android** - verwenden Sie das **GcmService**-Objekt unter **notificationHubService.gcm**
-   **iOS** - verwenden Sie das **ApnsService**-Objekt unter **notificationHubService.apns**
-   **Windows Phone** - verwenden Sie das **MpnsService**-Objekt unter **notificationHubService.mpns**
-   **Windows Store-Apps** - verwenden Sie das **WnsService**-Objekt unter **notificationHubService.wns**

### Senden von Benachrichtigungen an Android-Apps

Das **GcmService**-Objekt enthält eine **send**-Methode, mit der Sie Benachrichtigungen an Android-Apps verschicken können. Die **send**-Methode nimmt die folgenden Parameter entgegen:

-   Tags - der Tag-Bezeichner. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
-   Payload - die Nutzlast der Nachricht als JSON oder String
-   Callback - die Rückruffunktion

Weitere Informationen zum Nutzlastformat finden Sie im Abschnitt Nutzlast unter [Implementing GCM Server][Implementing GCM Server] (in englischer Sprache).

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

-   Tags - der Tag-Bezeichner. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
-   Payload - die Nutzlast der Nachricht als JSON oder String
-   Callback - die Rückruffunktion

Weitere Informationen zum Nutzlastformat finden Sie im Abschnitt Benachrichtigungs-Nutzlast unter [Local and Push Notification Programming Guide][Local and Push Notification Programming Guide] (in englischer Sprache).

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

-   Tags - der Tag-Bezeichner. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
-   Payload - die Nutzlast der Nachricht als XML
-   TargetName - 'toast' für Popupbenachrichtigungen. 'token' für Tile-Benachrichtigungen.
-   NotificationClass - Die Priorität der Benachrichtigung. Eine Liste der gültigen Werte finden Sie im Bereich "HTTP Header Elements" von [Push notifications from a server][Push notifications from a server] (in englischer Sprache).
-   Options - zusätzliche Anforderungsheader
-   Callback - die Rückruffunktion

Eine Liste der gültigen Optionen für TargetName, NotificationClass und header finden Sie unter [Push notifications from a server][Push notifications from a server] (in englischer Sprache).

Der folgende Code verwendet die **MpnsService**-Instanz aus **NotificationHubService**, um ein Popup zu schicken:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### Senden von Benachrichtigungen an Windows Store-Apps

Das **WnsService**-Objekt enthält eine **send**-Methode, mit der Sie Benachrichtigungen an Windows Store-Apps verschicken können. Die **send**-Methode nimmt die folgenden Parameter entgegen:

-   Tags - der Tag-Bezeichner. Falls kein Tag angegeben ist, wird die Benachrichtigung an alle Clients geschickt
-   Payload - die Nutzlast der Nachricht als XML
-   Type - der Benachrichtigungstyp
-   Options - zusätzliche Anforderungsheader
-   Callback - die Rückruffunktion

Eine Liste der gültigen Typen und Anforderungsheader finden Sie unter [Anforderungs- und Antwortheader des Pushbenachrichtigungsdiensts][Anforderungs- und Antwortheader des Pushbenachrichtigungsdiensts].

Der folgende Code verwendet die **WnsService**-Instanz aus **NotificationHubService**, um ein Popup zu schicken:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <span id="next"></span></a>Nächste Schritte

Nachdem Sie nun mit der Verwendung von Notification Hubs grundlegend
vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Azure Notification Hubs][Azure Notification Hubs].
-   Besuchen Sie das [Azure SDK für Node][Azure SDK für Node]-Repository auf GitHub.

  [Nächste Schritte]: #next
  [Was sind Notification Hubs?]: #hub
  [Erstellen einer Node.js-Anwendung]: #create
  [Konfigurieren einer Anwendung für Notification Hubs]: #config
  [Gewusst wie: Senden von Benachrichtigungen]: #send
  [Azure Notification Hubs]: http://msdn.microsoft.com/de-de/library/windowsazure/jj927170.aspx
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js-Clouddienst]: /de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Website mit WebMatrix]: /de-de/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Implementing GCM Server]: http://developer.android.com/google/gcm/server.html#payload
  [Local and Push Notification Programming Guide]: http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html
  [Push notifications from a server]: http://msdn.microsoft.com/de-de/library/hh221551.aspx
  [Anforderungs- und Antwortheader des Pushbenachrichtigungsdiensts]: http://msdn.microsoft.com/de-de/library/windows/apps/hh465435.aspx
  [Azure SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
