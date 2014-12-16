<properties urlDisplayName="How to use Notification Hubs with Java" pageTitle="Verwendung von Notification Hubs mit Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="elioda" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# Verwenden von Notification Hubs von Java/PHP aus
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/de-de/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/de-de/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

Sie können auf alle Notification Hubs-Features von einem Java-/PHP-/Ruby-Back-End aus mithilfe der Notification Hub-REST-Schnittstelle zugreifen, die im MSDN-Thema [REST-APIs für Benachrichtigungshubs](http://msdn.microsoft.com/de-de/library/dn223264.aspx) beschrieben wird.

In diesem Thema wird Folgendes erläutert:

* Erstellen eines REST-Clients für Notification Hubs-Features in Java
* Führen Sie die Schritte im [Lernprogramm zu den ersten Schritten](http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/) für die mobile Plattform Ihrer Wahl aus, und implementieren Sie den Back-End-Teil in Java.

##<a name="client-interface"></a>Clientschnittstelle
Die Client-Hauptschnittstelle kann dieselben Methoden bereitstellen, die im [.NET Notification Hubs SDK](http://msdn.microsoft.com/de-de/library/jj933431.aspx) verfügbar sind. Damit können Sie alle Lernprogramme und Beispiele, die aktuell auf dieser Website erhältlich sind und von der Community im Internet beigetragen werden, direkt umsetzen.

Sie finden den gesamten Code im [Java REST-Wrapper-Beispiel].

So erstellen Sie beispielsweise einen Client:

	new NotificationHub("connection string", "hubname");	

So erstellen Sie eine iOS-Registrierung (analog für Windows, Android, Windows Phone und Kindle Fire):

	String id = hub.createRegistrationId();
	AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.upsertRegistration(reg);

So senden Sie eine native iOS-Benachrichtigung:
	
	Notification n = Notification.createAppleNotifiation("APNS body");
	hub.sendNotification(n);

##<a name="implementation"></a>Implementierung
Folgen Sie, falls noch nicht geschehen, unserem [Lernprogramm zu den ersten Schritten] bis zum letzten Abschnitt, in dem Sie das Back-End implementieren müssen.
Sie können auch den Code aus dem [Java REST-Wrapper-Beispiel] verwenden und direkt mit dem Abschnitt [Abschließen des Lernprogramms](#complete-tutorial) fortfahren.

Alle Details für das Implementieren eines vollständigen REST-Wrappers finden sich auf [MSDN](http://msdn.microsoft.com/de-de/library/dn530746.aspx). In diesem Abschnitt beschreiben wir die Java-Implementierung der Hauptschritte, die für den Zugriff auf REST-Endpunkte von Notification Hubs erforderlich sind:

1. Analysieren der Verbindungszeichenfolge
2. Generieren des Authentifizierungstokens
3. Durchführen des HTTP-Aufrufs

In den folgenden Codeausschnitten nutzen wir diese Komponenten:

* [Apache HttpComponents](http://hc.apache.org/httpcomponents-client-ga/)
* [Apache Commons-Codec](http://commons.apache.org/proper/commons-codec/)
* [Apache Commons-Io](http://commons.apache.org/proper/commons-io/)

### Analysieren der Verbindungszeichenfolge

Hier ist die Hauptklasse, die den Client implementiert, dessen Konstruktor die Verbindungszeichenfolge analysiert:

	public class NotificationHub {

		private static final String APIVERSION = "?api-version=2013-10";
		private static final String CONTENT_LOCATION_HEADER = "Location";
		private String endpoint;
		private String hubPath;
		private String SasKeyName;
		private String SasKeyValue;
	
		private HttpClient httpClient;
	
		public NotificationHub(String connectionString, String hubPath) {
			this.httpClient = HttpClients.createDefault();
			this.hubPath = hubPath;
	
			String[] parts = connectionString.split(";");
			if (parts.length != 3)
				throw new RuntimeException("Error parsing connection string: "
						+ connectionString);
	
			for (int i = 0; i < parts.length; i++) {
				if (parts[i].startsWith("Endpoint")) {
					this.endpoint = "https" + parts[i].substring(11);
				} else if (parts[i].startsWith("SharedAccessKeyName")) {
					this.SasKeyName = parts[i].substring(20);
				} else if (parts[i].startsWith("SharedAccessKey")) {
					this.SasKeyValue = parts[i].substring(16);
				}
			}
		}
	}


### Erstellen des Sicherheitstokens
Weitere Informationen zur Erstellung des Sicherheitstokens finden Sie [hier](http://msdn.microsoft.com/de-de/library/dn495627.aspx).
Die folgende Methode muss der **NotificationHub**-Klasse hinzugefügt werden, um das Token basierend auf dem URI der aktuellen Anforderung und den Anmeldeinformationen, die aus der Verbindungszeichenfolge extrahiert wurden, zu erstellen.

	private String generateSasToken(URI uri) {
		String targetUri;
		try {
			targetUri = URLEncoder
					.encode(uri.toString().toLowerCase(), "UTF-8")
					.toLowerCase();

			long expiresOnDate = System.currentTimeMillis();
			int expiresInMins = 60; // 1 hour
			expiresOnDate += expiresInMins * 60 * 1000;
			long expires = expiresOnDate / 1000;
			String toSign = targetUri + "\n" + expires;

			// Get an hmac_sha1 key from the raw key bytes
			byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
			SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

			// Get an hmac_sha1 Mac instance and initialize with the signing key
			Mac mac = Mac.getInstance("HmacSHA256");
			mac.init(signingKey);

			// Compute the hmac on input data bytes
			byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

			// Convert raw bytes to Hex
			String signature = URLEncoder.encode(
					Base64.encodeBase64String(rawHmac), "UTF-8");

			// construct authorization string
			String token = "SharedAccessSignature sr=" + targetUri + "&sig="
					+ signature + "&se=" + expires + "&skn=" + SasKeyName;
			return token;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

### Senden einer Benachrichtigung
Lassen Sie uns zuerst eine Klasse definieren, die eine Benachrichtigung darstellt.

	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;
	import org.apache.http.entity.ContentType;

	public class Notification {
		private Map<String, String> headers = new HashMap<String, String>();
		private String body;
		private ContentType contentType;
	
		public static Notification createWindowsNotification(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windows");
	
			if (body.contains("<toast>"))
				n.headers.put("X-WNS-Type", "wns/toast");
			if (body.contains("<tile>"))
				n.headers.put("X-WNS-Type", "wns/tile");
			if (body.contains("<badge>"))
				n.headers.put("X-WNS-Type", "wns/badge");
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createAppleNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "apple");
			return n;
		}
	
		public static Notification createGcmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "gcm");
			return n;
		}

		public static Notification createAdmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "adm");
			return n;
		}

		public static Notification createMpnsNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windowsphone");
	
			if (body.contains("<wp:Toast>")) {
				n.headers.put("X-WindowsPhone-Target", "toast");
				n.headers.put("X-NotificationClass", "2");
			}
			if (body.contains("<wp:Tile>")) {
				n.headers.put("X-WindowsPhone-Target", "tile");
				n.headers.put("X-NotificationClass", "1");
			}
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createTemplateNotification(
				Map<String, String> properties) {
			Notification n = new Notification();
			StringBuffer buf = new StringBuffer();
			buf.append("{");
			for (Iterator<String> iterator = properties.keySet().iterator(); iterator
					.hasNext();) {
				String key = iterator.next();
				buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
				if (iterator.hasNext())
					buf.append(",");
			}
			buf.append("}");
			n.body = buf.toString();
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "template");
			return n;
		}
	
		public Map<String, String> getHeaders() { return headers; }
	
		public void setHeaders(Map<String, String> headers) { this.headers = headers; }
	
		public String getBody() { return body; }
	
		public void setBody(String body) { this.body = body; }
	
		public ContentType getContentType() { return contentType; }
	
		public void setContentType(ContentType contentType) { this.contentType = contentType; }
	}

Diese Klasse ist ein Container für einen nativen Benachrichtigungstext oder ein Satz von Eigenschaften einer Benachrichtigungsvorlage sowie ein Satz von Headern, die ein Format (native Plattform oder Vorlage) und plattformspezifische Eigenschaften (wie die Apple-Ablaufeigenschaft und WNS-Header) enthalten. Wir definieren außerdem einige praktische Konstruktoren für das Generieren häufig verwendeter Benachrichtigungstypen.

Alle verfügbaren Optionen finden Sie in der Dokumentation der [REST-APIs für Benachrichtigungshubs](http://msdn.microsoft.com/de-de/library/dn495827.aspx) und unter den Formaten der einzelnen Benachrichtigungsplattformen.

Mit dieser Klasse können wir jetzt die Methoden zum Senden von Benachrichtigungen in der **NotificationHub**-Klasse schreiben.

	public void sendNotification(Notification notification) {
		sendNotification(notification, "");
	}

	public void sendNotification(Notification notification, Set<String> tags) {
		if (tags.isEmpty())
			throw new IllegalArgumentException(
					"tags has to contain at least an element");

		StringBuffer exp = new StringBuffer();
		for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
			exp.append(iterator.next());
			if (iterator.hasNext())
				exp.append(" || ");
		}

		sendNotification(notification, exp.toString());
	}

	public void sendNotification(Notification notification, String tagExpression) {
		HttpPost post = null;
		try {
			URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
			post = new HttpPost(uri);
			post.setHeader("Authorization", generateSasToken(uri));

			if (tagExpression != null && !"".equals(tagExpression)) {
				post.setHeader("ServiceBusNotification-Tags", tagExpression);
			}

			for (String header : notification.getHeaders().keySet()) {
				post.setHeader(header, notification.getHeaders().get(header));
			}

			post.setEntity(new StringEntity(notification.getBody()));
			HttpResponse response = httpClient.execute(post);

			if (response.getStatusLine().getStatusCode() != 201) {
				String msg = "";
				if (response.getEntity() != null
						&& response.getEntity().getContent() != null) {
					msg = IOUtils.toString(response.getEntity().getContent());
				}
				throw new RuntimeException("Error: " + response.getStatusLine()
						+ " body: " + msg);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (post != null)
				post.releaseConnection();
		}
	}

Die vorstehenden Methoden senden eine HTTP POST-Anfrage an den /messages-Endpunkt des Notification Hubs, mit korrektem Text und Headern zum Senden der Benachrichtigung.

##<a name="complete-tutorial"></a>Abschließen des Lernprogramms
Sie können jetzt das Erste-Schritte-Lernprogramm abschließen, indem Sie die Benachrichtigung von einem Java-Back-End aus senden.

Initialisieren Sie Ihren Notification Hubs-Client (ersetzen Sie die Verbindungszeichenfolge und den Hubnamen wie im [Lernprogramm zu den ersten Schritten] beschrieben):
	NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

Fügen Sie dann den Sendecode je nach mobiler Zielplattform hinzu.

### Windows Store und Windows Phone 8.1 (nicht Silverlight)

	String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
	Notification n = Notification.createWindowsNotification(toast);
	hub.sendNotification(n);

### iOS

	String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
	Notification n = Notification.createAppleNotification(alert);
	hub.sendNotification(n);

### Android
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createGcmNotification(message);
	hub.sendNotification(n);

### Windows Phone 8.0 und 8.1 Silverlight

	String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		        "<wp:Notification xmlns:wp=\"WPNotification\">" +
		           "<wp:Toast>" +
		                "<wp:Text1>Hello from Java!</wp:Text1>" +
		           "</wp:Toast> " +
		        "</wp:Notification>";
	Notification n = Notification.createMpnsNotification(toast);
	hub.sendNotification(n);

### Kindle Fire
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createAdmNotification(message);
	hub.sendNotification(n);

Beim Ausführen des Java-Codes sollte jetzt eine Benachrichtigung erstellt werden, die auf dem Zielgerät angezeigt wird.


##<a name="next-steps"></a>Nächste Schritte
In diesem Thema haben wir gezeigt, wie Sie einen einfachen Java REST-Client für Notification Hubs erstellen. Mögliche nächste Schritte:

* Laden Sie das vollständige [Java REST-Wrapper-Beispiel] herunter, das den gesamten vorstehenden Code sowie die Registrierungsverwaltung enthält.
* Erfahren Sie mehr über das Tagging-Feature von Notification Hubs im [Lernprogramm zu den Neuigkeiten]
* Lernen Sie im [Lernprogramm zum Benachrichtigen von Benutzern], wie Sie Pushbenachrichtigungen an einzelne Benutzer senden.




[Java REST-Wrapper-Beispiel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
[Lernprogramm zu den ersten Schritten]: http://azure.microsoft.com/de-de/documentation/articles/notification-hubs-ios-get-started/
