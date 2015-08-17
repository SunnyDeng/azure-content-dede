<properties 
	pageTitle="Verwenden von Notification Hubs mit Python" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs aus einem Python-Back-End." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="python" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="yuaxu"/>

# Verwenden von Notofication Hubs mit Python
> [AZURE.SELECTOR] 
- [Java](notification-hubs-php-backend-how-to.md)
- [PHP](notification-hubs-python-backend-how-to.md)
- [Python](notification-hubs-nodejs-how-to-use-notification-hubs.md)
- [Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md)
		
Sie können von einem Java-/PHP-/Python-/Ruby-Back-End aus über die REST-Schnittstelle für Notification Hubs, die im MSDN-Thema [REST-APIs für Benachrichtigungshubs](http://msdn.microsoft.com/library/dn223264.aspx) beschrieben ist, auf alle Notification Hubs-Funktionen zugreifen.

> [AZURE.NOTE]Dies ist eine beispielhafte Referenzimplementierung für die Implementierung der Benachrichtigungsübermittlung in Python und nicht der offiziell unterstützte Notification Hub Python SDK.

> [AZURE.NOTE]Dieses Beispiel wurde mit Python 3.4 geschrieben.

In diesem Thema wird Folgendes erläutert:

* Erstellen eines REST-Clients für Notification Hubs-Features in Python.
* Senden von Benachrichtigungen über die Python-Schnittstelle an die Notification Hub-REST-APIs. 
* Abrufen eines Abbilds der HTTP-REST-Anforderung/Antwort zu Debugging-/Lernzwecken. 

Führen Sie die Schritte im Lernprogramm [Erste Schritte mit Notification Hubs](notification-hubs-windows-store-dotnet-get-started.md) aus, und implementieren Sie den Back-End-Teil in Python.

> [AZURE.NOTE]Das Beispiel ist auf das Senden von Benachrichtigungen begrenzt und enthält keine Registrierungsverwaltung.

## Clientschnittstelle
Über die Hauptclientschnittstelle können dieselben Methoden bereitgestellt werden, die im [.NET Notification Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx) verfügbar sind. Dadurch können Sie alle Lernprogramme und Beispiele, die derzeit auf dieser Website verfügbar sind und die von der Community im Internet beigesteuert werden, direkt übersetzen.

Den gesamten verfügbaren Code finden Sie im [Beispiel für Python-REST-Wrapper].

So erstellen Sie beispielsweise einen Client:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
	
So senden Sie eine Windows-Popupbenachrichtigung
	
	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Hello world!</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)
	
## Implementierung
Führen Sie, sofern nicht bereits geschehen, das Lernprogramm [Erste Schritte mit Notification Hubs] bis zum letzten Abschnitt aus, in dem Sie das Back-End implementieren müssen.

Alle Details für das Implementieren eines vollständigen REST-Wrappers finden sich auf [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). In diesem Abschnitt wird die Python-Implementierung mit den Hauptschritten beschrieben, die für den Zugriff auf REST-Endpunkte von Notification Hubs und für das Senden von Benachrichtigungen erforderlich sind:

1. Analysieren der Verbindungszeichenfolge
2. Generieren des Authentifizierungstokens
3. Senden einer Benachrichtigung über die HTTP-REST-API

### Analysieren der Verbindungszeichenfolge

Dies hier ist die Hauptklasse, die den Client implementiert, dessen Konstruktor die Verbindungszeichenfolge analysiert:

	class NotificationHub:
	    API_VERSION = "?api-version=2013-10"
	    DEBUG_SEND = "&test"
	
	    def __init__(self, connection_string=None, hub_name=None, debug=0):
	        self.HubName = hub_name
	        self.Debug = debug
	
	        # Parse connection string
	        parts = connection_string.split(';')
	        if len(parts) != 3:
	            raise Exception("Invalid ConnectionString.")
	
	        for part in parts:
	            if part.startswith('Endpoint'):
	                self.Endpoint = 'https' + part[11:]
	            if part.startswith('SharedAccessKeyName'):
	                self.SasKeyName = part[20:]
	            if part.startswith('SharedAccessKey'):
	                self.SasKeyValue = part[16:]


### Erstellen des Sicherheitstokens
Einzelheiten zum Erstellen des Sicherheitstokens finden Sie [hier](http://msdn.microsoft.com/library/dn495627.aspx). Die folgenden Methoden müssen der **NotificationHub**-Klasse hinzugefügt werden, um das Token basierend auf dem URI der aktuellen Anforderung und den Anmeldeinformationen, die aus der Verbindungszeichenfolge extrahiert wurden, zu erstellen.

	@staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### Senden einer Benachrichtigung über die HTTP-REST-API
Lassen Sie uns zuerst eine Klasse definieren, die eine Benachrichtigung darstellt.

	class Notification:
	    def __init__(self, notification_format=None, payload=None, debug=0):
	        valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
	        if not any(x in notification_format for x in valid_formats):
	            raise Exception(
	                "Invalid Notification format. " +
	                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
	
	        self.format = notification_format
	        self.payload = payload
	
	        # array with keynames for headers
	        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
	        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
	        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
	        self.headers = None

Diese Klasse ist ein Container für einen nativen Benachrichtigungstext oder einen Satz von Eigenschaften (bei einer Benachrichtigungsvorlage) sowie ein Satz von Headern, der ein Format (native Plattform oder Vorlage) und plattformspezifische Eigenschaften (wie die Apple-Ablaufeigenschaft und WNS-Header) enthält.

Alle verfügbaren Optionen finden Sie in der Dokumentation der [REST-APIs für Benachrichtigungshubs](http://msdn.microsoft.com/library/dn495827.aspx) und unter den Formaten der einzelnen Benachrichtigungsplattformen.

Mit dieser Klasse können jetzt die Methoden zum Senden von Benachrichtigungen in der **NotificationHub**-Klasse geschrieben werden.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

Die vorstehenden Methoden senden eine HTTP-POST-Anforderung mit dem korrekten Text und Headern zum Senden der Benachrichtigung an den "/messages"-Endpunkt des Notification Hubs.

### Aktivieren der ausführlichen Protokollierung mithilfe der Debug-Eigenschaft
Durch das Aktivieren der Debug-Eigenschaft während der Initialisierung des Notification Hubs werden detaillierte Protokollinformationen zu HTTP-Anforderung und -Antwort sowie ein detailliertes Sendeergebnis für die Benachrichtigungsnachricht geschrieben. Wir haben diese Eigenschaft mit dem Namen [Notification Hubs TestSend-Eigenschaft](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) vor Kurzem hinzugefügt. Sie gibt ausführliche Informationen zum Sendeergebnis der Benachrichtigung zurück. Wenn Sie diese Eigenschaft verwenden möchten, initialisieren Sie sie wie folgt:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Der HTTP-URL der Sendeanforderung des Notification Hubs wird einer "test"-Abfragezeichenfolge als Ergebnis angehängt.

##<a name="complete-tutorial"></a>Abschließen des Lernprogramms
Sie können jetzt das Lernprogramm "Erste Schritte" abschließen, indem Sie die Benachrichtigung von einem Python-Back-End aus senden.

Initialisieren Sie Ihren Notification Hubs-Client (ersetzen Sie die Verbindungszeichenfolge und den Hubnamen wie im [Erste-Schritte-Lernprogramm] beschrieben):

	hub = NotificationHub("myConnectionString", "myNotificationHubName")

Fügen Sie dann den Sendecode je nach mobiler Zielplattform hinzu. Mit diesem Beispiel werden außerdem Methoden auf höherer Ebene hinzugefügt, um das Senden von Benachrichtigungen auf Plattformbasis zu aktivieren, z. B. "send\_windows\_notification" (für Windows), send\_apple\_notification (für Apple) usw.

### Windows Store und Windows Phone 8.1 (nicht-Silverlight)

	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Test</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)

### Windows Phone 8.0 und 8.1 Silverlight

	hub.send_mpns_notification(toast)

### iOS

	alert_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_apple_notification(alert_payload)

### Android
	gcm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_gcm_notification(gcm_payload)

### Kindle Fire
	adm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_adm_notification(adm_payload)

### Baidu
	baidu_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_baidu_notification(baidu_payload)

Beim Ausführen des Python-Codes sollte eine Benachrichtigung erstellt und auf dem Zielgerät angezeigt werden.

## Beispiele:

### Aktivieren der Debug-Eigenschaft
Wenn Sie beim Initialisieren von "NotificationHub" das Debug-Flag aktivieren, werden die detaillierte HTTP-Anforderung und -Antwort sowie das Benachrichtigungsergebnis wie folgt angezeigt. Dabei können Sie erkennen, welche HTTP-Header in der Anforderung übergeben werden und welche HTTP-Antwort vom Notification Hub empfangen wurde: ![][1]

Das detaillierte Ergebnis des Notification Hubs wird angezeigt, z. B.

- wenn die Nachricht erfolgreich zum Pushbenachrichtigungsdienst gesendet wurde. 
	
		<Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Wenn für eine Pushbenachrichtigung keine Ziele gefunden wurden, wird wahrscheinlich Folgendes in der Antwort angezeigt (dies besagt, dass keine Registrierungen zum Übermitteln der Benachrichtigung gefunden wurden, möglicherweise aufgrund nicht übereinstimmender Registrierungs-Tags).

		'<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### Broadcast-Popupbenachrichtigung an Windows 

Beachten Sie die Header, die beim Senden einer Broadcast-Popupbenachrichtigung an einen Windows-Client gesendet werden.

	hub.send_windows_notification(wns_payload)

![][2]

### Senden einer Benachrichtigung mit Angabe eines Tags (oder Tag-Ausdrucks)

Beachten Sie den HTTP-Header des Tags, der der HTTP-Anforderung hinzugefügt wird (im folgenden Beispiel wird die Benachrichtigung nur an Registrierungen mit der "payload" des Typs "sports" gesendet).

	hub.send_windows_notification(wns_payload, "sports")

![][3]

### Senden einer Benachrichtigung mit Angabe mehrerer Tags

Beachten Sie, wie sich der HTTP-Header der Tags ändert, wenn mehrere Tags gesendet werden.
	
	tags = {'sports', 'politics'}
	hub.send_windows_notification(wns_payload, tags)

![][4]

### Auf Vorlagen basierende Benachrichtigung

Beachten Sie, dass sich der Format-HTTP-Header ändert und der "payload"-Text als Teil des HTTP-Anforderungstexts gesendet wird:

**Clientseitig – registrierte Vorlage**

		var template =
		                @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Serverseitig – Senden der Nutzlast**
		
		template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
		hub.send_template_notification(template_payload)

![][5]


## Nächste Schritte
In diesem Thema haben wir gezeigt, wie Sie einen einfachen Python-REST-Client für Notification Hubs erstellen. Mögliche nächste Schritte:

* Laden Sie das vollständige [Beispiel für Python-REST Wrapper] herunter, das den gesamten vorstehenden Code enthält.
* Erfahren Sie mehr über das Markierungsfeature von Notification Hubs im [Lernprogramm zum Übermitteln aktueller Nachrichten].
* Erfahren Sie mehr über das Templates-Feature von Notification Hubs im [Lernprogramm zum Lokalisieren von Nachrichten].

<!-- URLs -->
[Beispiel für Python-REST Wrapper]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Beispiel für Python-REST-Wrapper]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Erste Schritte mit Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Erste-Schritte-Lernprogramm]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Lernprogramm zum Übermitteln aktueller Nachrichten]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Lernprogramm zum Lokalisieren von Nachrichten]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 

<!---HONumber=August15_HO6-->