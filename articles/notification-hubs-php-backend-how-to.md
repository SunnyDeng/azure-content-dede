<properties urlDisplayName="How to use Notification Hubs with PHP" pageTitle="Verwendung von Notification Hubs mit PHP" metaKeywords="" description="Learn how to use Azure Notification Hubs from a PHP back-end." metaCanonical="" services="mobile-services,notification-hubs,push,php" documentationCenter="" title="How to use Notification Hubs with PHP" authors="elioda" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="php" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# Verwenden von Notification Hubs von Java/PHP aus
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/de-de/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/de-de/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP" class="current">PHP</a>
</div>

Sie können auf alle Notification Hubs-Features von einem Java-/PHP-/Ruby-Back-End aus mithilfe der Notification Hub-REST-Schnittstelle zugreifen, die im MSDN-Thema [REST-APIs für Benachrichtigungshubs](http://msdn.microsoft.com/de-de/library/dn223264.aspx) beschrieben wird.

In diesem Thema wird Folgendes erläutert:

* Erstellen eines REST-Clients für Notification Hubs-Features in PHP
* Führe Sie die Schritte im [Erste-Schritte-Lernprogramm](http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/) für die mobile Plattform Ihrer Wahl aus, und implementieren Sie den Back-End-Teil in PHP.

## Clientschnittstelle
Die Client-Hauptschnittstelle kann dieselben Methoden bereitstellen, die im [.NET Notification Hubs SDK](http://msdn.microsoft.com/de-de/library/jj933431.aspx) verfügbar sind. Damit können Sie alle Lernprogramme und Beispiele, die aktuell auf dieser Website erhältlich sind und von der Community im Internet beigetragen werden, direkt umsetzen.

Sie finden den gesamten Code im [PHP REST-Wrapper-Beispiel].

So erstellen Sie beispielsweise einen Client:

	$hub = new NotificationHub("connection string", "hubname");	

So senden Sie eine native iOS-Benachrichtigung:
	
	$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
	$hub->sendNotification($notification);

## Implementierung
Folgen Sie, falls noch nicht geschehen, unserem [Lernprogramm zu den ersten Schritten] bis zum letzten Abschnitt, in dem Sie das Back-End implementieren müssen.
Sie können auch den Code aus dem [PHP REST-Wrapper-Beispiel] verwenden und direkt mit dem Abschnitt [Abschließen des Lernprogramms](#complete-tutorial) fortfahren.

Alle Details für das Implementieren eines vollständigen REST-Wrappers finden sich auf [MSDN](http://msdn.microsoft.com/de-de/library/dn530746.aspx). In diesem Abschnitt beschreiben wir die PHP-Implementierung der Hauptschritte, die für den Zugriff auf REST-Endpunkte von Notification Hubs erforderlich sind:

1. Analysieren der Verbindungszeichenfolge
2. Generieren des Authentifizierungstokens
3. Durchführen des HTTP-Aufrufs

### Analysieren der Verbindungszeichenfolge

Hier ist die Hauptklasse, die den Client implementiert, dessen Konstruktor die Verbindungszeichenfolge analysiert:

	class NotificationHub {
		const API_VERSION = "?api-version=2013-10";
	
		private $endpoint;
		private $hubPath;
		private $sasKeyName;
		private $sasKeyValue;
	
		function __construct($connectionString, $hubPath) {
			$this->hubPath = $hubPath;
	
			$this->parseConnectionString($connectionString);
		}
	
		private function parseConnectionString($connectionString) {
			$parts = explode(";", $connectionString);
			if (sizeof($parts) != 3) {
				throw new Exception("Error parsing connection string: " . $connectionString);
			}
	
			foreach ($parts as $part) {
				if (strpos($part, "Endpoint") === 0) {
					$this->endpoint = "https" . substr($part, 11);
				} else if (strpos($part, "SharedAccessKeyName") === 0) {
					$this->sasKeyName = substr($part, 20);
				} else if (strpos($part, "SharedAccessKey") === 0) {
					$this->sasKeyValue = substr($part, 16);
				}
			}
		}
	}


### Erstellen des Sicherheitstokens
Weitere Informationen zur Erstellung des Sicherheitstokens finden Sie [hier](http://msdn.microsoft.com/de-de/library/dn495627.aspx).
Die folgende Methode muss der **NotificationHub**-Klasse hinzugefügt werden, um das Token basierend auf dem URI der aktuellen Anforderung und den Anmeldeinformationen, die aus der Verbindungszeichenfolge extrahiert wurden, zu erstellen.

	private function generateSasToken($uri) {
		$targetUri = strtolower(rawurlencode(strtolower($uri)));

		$expires = time();
		$expiresInMins = 60;
		$expires = $expires + $expiresInMins * 60;
		$toSign = $targetUri . "\n" . $expires;

		$signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

		$token = "SharedAccessSignature sr=" . $targetUri . "&sig="
					. $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

		return $token;
	}

### Senden einer Benachrichtigung
Lassen Sie uns zuerst eine Klasse definieren, die eine Benachrichtigung darstellt.

	class Notification {
		public $format;
		public $payload;
	
		# array with keynames for headers
		# Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
		# Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
		public $headers;
	
		function __construct($format, $payload) {
			if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
				throw new Exception('Invalid format: ' . $format);
			}
	
			$this->format = $format;
			$this->payload = $payload;
		}
	}

Diese Klasse ist ein Container für einen nativen Benachrichtigungstext oder ein Satz von Eigenschaften einer Benachrichtigungsvorlage sowie ein Satz von Headern, die ein Format (native Plattform oder Vorlage) und plattformspezifische Eigenschaften (wie die Apple-Ablaufeigenschaft und WNS-Header) enthalten.

Alle verfügbaren Optionen finden Sie in der Dokumentation der [REST-APIs für Benachrichtigungshubs](http://msdn.microsoft.com/de-de/library/dn495827.aspx) und unter den Formaten der einzelnen Benachrichtigungsplattformen.

Mit dieser Klasse können wir jetzt die Methoden zum Senden von Benachrichtigungen in der **NotificationHub**-Klasse schreiben.

	public function sendNotification($notification) {
		$this->sendNotification($notification, "");
	}

	public function sendNotification($notification, $tagsOrTagExpression) {
		if (is_array($tagsOrTagExpression)) {
			$tagExpression = implode(" || ", $tagsOrTagExpression);
		} else {
			$tagExpression = $tagsOrTagExpression;
		}

		# build uri
		$uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
		$ch = curl_init($uri);

		if (in_array($notification->format, ["template", "apple", "gcm"])) {
			$contentType = "application/json";
		} else {
			$contentType = "application/xml";
		}

		$token = $this->generateSasToken($uri);

		$headers = [
		    'Authorization: '.$token,
		    'Content-Type: '.$contentType,
		    'ServiceBusNotification-Format: '.$notification->format
		];

		if ("" !== $tagExpression) {
			$headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
		}

		# add headers for other platforms
		if (is_array($notification->headers)) {
			$headers = array_merge($headers, $notification->headers);
		}
		
		curl_setopt_array($ch, array(
		    CURLOPT_POST => TRUE,
		    CURLOPT_RETURNTRANSFER => TRUE,
		    CURLOPT_SSL_VERIFYPEER => FALSE,
		    CURLOPT_HTTPHEADER => $headers,
		    CURLOPT_POSTFIELDS => $notification->payload
		));

		// Send the request
		$response = curl_exec($ch);

		// Check for errors
		if($response === FALSE){
		    throw new Exception(curl_error($ch));
		}

		$info = curl_getinfo($ch);

		if ($info['http_code'] <> 201) {
			throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
		}
	} 

Die vorstehenden Methoden senden eine HTTP POST-Anfrage an den /messages-Endpunkt des Notification Hubs, mit korrektem Text und Headern zum Senden der Benachrichtigung.

##<a name="complete-tutorial"></a>Abschließen des Lernprogramms
Sie können jetzt das Erste-Schritte-Lernprogramm abschließen, indem Sie die Benachrichtigung von einem PHP-Back-End aus senden.

Initialisieren Sie Ihren Notification Hubs-Client (ersetzen Sie die Verbindungszeichenfolge und den Hubnamen wie im [Lernprogramm zu den ersten Schritten] beschrieben):
	$hub = new NotificationHub("connection string", "hubname");	

Fügen Sie dann den Sendecode je nach mobiler Zielplattform hinzu.

### Windows Store und Windows Phone 8.1 (nicht Silverlight)

	$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
	$notification = new Notification("windows", $toast);
	$notification->headers[] = 'X-WNS-Type: wns/toast';
	$hub->sendNotification($notification);

### iOS

	$alert = '{"aps":{"alert":"Hello from PHP!"}}';
	$notification = new Notification("apple", $alert);
	$hub->sendNotification($notification);

### Android
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("gcm", $message);
	$hub->sendNotification($notification);

### Windows Phone 8.0 und 8.1 Silverlight

	$toast = '<?xml version="1.0" encoding="utf-8"?>' .
		        '<wp:Notification xmlns:wp="WPNotification">' .
		           '<wp:Toast>' .
		                '<wp:Text1>Hello from PHP!</wp:Text1>' .
		           '</wp:Toast> ' .
		        '</wp:Notification>';
	$notification = new Notification("mpns", $toast);
	$notification->headers[] = 'X-WindowsPhone-Target : toast';
	$notification->headers[] = 'X-NotificationClass : 2';
	$hub->sendNotification($notification);


### Kindle Fire
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("adm", $message);
	$hub->sendNotification($notification);

Beim Ausführen des PHP-Codes sollte jetzt eine Benachrichtigung erstellt werden, die auf dem Zielgerät angezeigt wird.


## Nächste Schritte
In diesem Thema haben wir gezeigt, wie Sie einen einfachen Java REST-Client für Notification Hubs erstellen. Mögliche nächste Schritte:

* Laden Sie das vollständige [PHP REST-Wrapper-Beispiel] herunter, das den gesamten vorstehenden Code enthält.
* Erfahren Sie mehr über das Tagging-Feature von Notification Hubs im [Lernprogramm zu den Neuigkeiten]
* Lernen Sie im [Lernprogramm zum Benachrichtigen von Benutzern], wie Sie Pushbenachrichtigungen an einzelne Benutzer senden.


[PHP REST-Wrapper-Beispiel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Lernprogramm zu den ersten Schritten]: http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/
