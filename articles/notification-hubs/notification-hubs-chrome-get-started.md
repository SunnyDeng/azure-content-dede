<properties
	pageTitle="Erste Schritte mit Azure Notification Hubs für Chrome-Apps | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine Chrome-App senden."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-chrome"
	ms.devlang="JavaScript"
	ms.topic="hero-article"
	ms.date="09/03/2015"
	ms.author="wesmc"/>

# Erste Schritte mit Notification Hubs für Chrome-Apps

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Chrome-App senden können.

Einer der wichtigsten Vorteile der Verwendung von Benachrichtigungen der Chrome-App besteht darin, dass die Benachrichtigungen im Kontext des Google Chrome-Browsers angezeigt werden. Sie müssen die Chrome-App nicht ausführen oder im Browser öffnen (der Chrome-Browser selbst muss allerdings ausgeführt werden). Außerdem wird eine konsolidierte Ansicht all Ihrer Benachrichtigungen im Chrome-Benachrichtigungsfenster angezeigt.

>[AZURE.NOTE]Dies ist eine Chrome-Apps-spezifische und keine generische In-Browser-Pushbenachrichtigung – Weitere Informationen finden Sie unter [Chrome-Apps – Übersicht]. Chrome-Apps wurden zuvor als "App-Pakete" bezeichnet und unterscheiden sich von einfacheren "Gehosteten Apps". Informationen zu den Unterschieden finden Sie unter [Installierbare Web-Apps]. Chrome-Apps können auch auf Mobilgeräten (Android und iOS) über Apache Cordova ausgeführt werden. Weitere Informationen finden Sie unter [Chrome-Apps auf Mobile].

In diesem Lernprogramm erstellen Sie eine Chrome-App, die Pushbenachrichtigungen über Google Cloud Messaging (GCM) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Chrome-Benutzer senden, die diese Chrome-App installiert haben.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

* [Aktivieren von Google Cloud Messaging](#register)
* [Konfigurieren Ihres Notification Hub](#configure-hub)
* [Verbinden Ihrer Chrome-App mit dem Notification Hub](#connect-app)
* [Senden einer Benachrichtigung an Ihre Chrome-App](#send)
* [Nächste Schritte](#next-steps)

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. GCM und Azure Notification Hubs werden genau wie unter Android konfiguriert, da [Google Cloud Messaging für Chrome] veraltet ist und dieselbe GCM jetzt sowohl Android-Geräte als auch Chrome-Instanzen unterstützt.

Bearbeiten Sie auch die im Abschnitt "Nächste Schritte" aufgeführten Lernprogramme, um mehr über die Verwendung von Notification Hubs zur Adressierung von bestimmten Benutzern und Gerätegruppen zu erfahren.

>[AZURE.NOTE]Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Aktivieren von Google Cloud Messaging

1. Navigieren Sie zur Website [Google Cloud Console], melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, und klicken Sie dann auf **Create Project**. Geben Sie unter **Project Name** einen geeigneten Projektnamen an, und klicken Sie dann auf die Schaltfläche **Create**.

   	![][1]

2. Notieren Sie sich die Projektnummer unter **Project Number** auf der Seite **Projects** für das Projekt, das Sie gerade erstellt haben. Sie verwenden diese Nummer in der Chrome-App als **GCM Sender ID** für die Registrierung bei GCM.

   	![][2]

3. Klicken Sie im linken Bereich auf **APIs & auth**, scrollen Sie dann nach unten, und klicken Sie auf die Umschaltfläche, um **Google Cloud Messaging for Android** zu aktivieren. Sie müssen **Google Cloud Messaging for Chrome** nicht aktivieren.

   	![][3]

4. Klicken Sie im linken Bereich auf **Credentials** -> **Create New Key** -> **Server Key** -> **Create**.

   	![][4]

5. Notieren Sie sich den API-Schlüssel des Servers (unter **API Key**). Sie konfigurieren diesen anschließend in Ihrem Notification Hub, um ihn zum Senden von Pushbenachrichtigungen an GCM zu aktivieren.

   	![][5]

##<a id="configure-hub"></a>Konfigurieren Ihres Notification Hubs

1. Melden Sie sich beim [Azure-Portal] an, und klicken Sie unten links im Bildschirm auf **+NEW**.

2. Klicken Sie auf **App Services** > **Service Bus** > **Notification Hub** > **Schnellerfassung**. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub**.

   	![][6]

4. Wechseln Sie zum soeben erstellten Notification Hub. Klicken Sie auf den Namespace, der Ihren Notification Hub enthält (in der Regel ***Notification Hub-Name*-ns**).

   	![][7]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs**.

   	![][8]

6. Klicken Sie oben auf die Registerkarte **Konfigurieren**.

   	![][9]

7. Scrollen Sie auf der Registerkarte **Konfigurieren** nach unten zu den **Google Cloud Messaging-Einstellungen**, geben Sie den Wert für den **API-Schlüssel** ein, den Sie im vorherigen Abschnitt erhalten haben, und klicken Sie auf **Speichern**.

   	![][10]

8. Wählen Sie oben die Registerkarte **Dashboard** aus, und klicken Sie am unteren Rand auf **Verbindungsinformationen**.

   	![][11]

9. Notieren Sie sich den Wert von **DefaultListenSharedAccessSignature** (diesen benötigen Sie in der Chrome-App zur Registrierung beim Notification Hub) und von **DefaultFullSharedAccessSignature** (diesen benötigen Sie zum Senden von Benachrichtigungen).

   	![][12]

Der Notification Hub ist jetzt für die Arbeit mit GCM konfiguriert, und Sie verfügen über die erforderlichen Verbindungszeichenfolgen für die weitere Konfiguration.

##<a id="connect-app"></a>Verbinden Ihrer Chrome-App mit dem Notification Hub

###Erstellen einer neuen Chrome-App

Das folgende Beispiel basiert auf dem [GCM-Beispiel für Chrome-Apps] und orientiert sich an der empfohlenen Methode zum Erstellen einer Chrome-App. In den folgenden Abschnitten werden die Schritte im Zusammenhang mit Azure Notification Hubs näher erläutert. Es wird empfohlen, den Quellcode für diese Chrome-App aus dem [Notification Hub-Beispiel für Chrome-Apps] herunterzuladen.

Die Chrome-App wird mit JavaScript erstellt, und Sie können dazu einen beliebigen Text-Editor verwenden. Im folgenden wird dargestellt, wie die Chrome-App aussieht.

   	![][15]

2. Erstellen Sie einen Ordner mit dem Namen **ChromePushApp** oder einem anderen Namen.

3. Laden Sie die **cryto-js-Bibliothek** aus [cryto-js library] in diesen Ordner herunter. Dieser Bibliotheksordner enthält zwei Unterordner: **components** und **rollups**.

4. Erstellen Sie eine **manifest.json**-Datei. Alle Chrome-Apps werden durch eine Manifestdatei gesichert, die die Metadaten der App und insbesondere die für die App verfügbaren Berechtigungen beschreibt.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}

	Beachten Sie das **permissions**-Element, das angibt, dass diese Chrome-App Pushbenachrichtigungen von GCM empfangen kann. Es muss auch den Azure Notification Hubs-URI zum Senden eines REST-Aufrufs zur Registrierung durch die Chrome-App angeben. Dabei wird eine Symboldatei ("gcm\_128.png") verwendet, die sich in der Quelle befindet und aus dem ursprünglichen GCM-Beispiel wiederverwendet wird. Sie können ein beliebiges Bild verwenden.

5. Erstellen Sie eine Datei namens **background.js** mit dem folgenden Code:

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}

		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.

		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);

		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}

		var registerWindowCreated = false;

		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {

		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}

		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);

		// Set up listeners to trigger the first-time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Dies ist die Datei, die die HTML-Datei des Chrome-App-Fensters einblendet (**register.html**) und den Handler **messageReceived** definiert, der die eingehende Pushbenachrichtigung verarbeitet.

6. Erstellen Sie eine Datei namens **register.html**, die die Benutzeroberfläche der Chrome-App definiert. Beachten Sie, dass in diesem Beispiel *CryptoJS v3.1.2* verwendet wird. Wenn Sie eine andere Version heruntergeladen haben, korrigieren Sie den src-Pfad des Skripts.

		<html>

		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>

		<body>

		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>

		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

		<br/>

		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

		<br/>
		<br/>

		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

		</body>

		</html>

7. Erstellen Sie eine Datei namens **register.js** mit dem folgenden Code. Diese Datei legt das Skript hinter **register.html** fest. Chrome-Apps erlauben keine Inline-Ausführung, daher müssen Sie ein separates Sicherungsskript für Ihre Benutzeroberfläche erstellen.

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

		window.onload = function() {
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH;
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
		}

		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }

		  document.getElementById("console").innerHTML = currentStatus  + status;
		}

		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);

		  // Prevent register button from being clicked again before the registration finishes.
		  document.getElementById("registerWithGCM").disabled = true;
		}

		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;

		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }

		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;

		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}

		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}

		// From http://msdn.microsoft.com/library/dn495627.aspx
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";

		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });

		  originalUri = endpoint + hubName;
		}

		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration

		  // Set expiration in seconds.
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;

		  // Using CryptoJS.
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);

		  // Construct authorization string.
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}

		function sendNHRegistrationRequest()
		{
		  var registrationPayload =
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" +
		      "<content type="application/xml">" +
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";

		  // Update the payload with the registration ID obtained earlier.
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();

		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };

		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }

		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");

		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	Das oben stehende Skript bietet die folgenden Vorteile: - *window.onload* definiert die Klick-Ereignisse für die zwei Schaltflächen in der Benutzeroberfläche. Eine führt die Registrierung bei GCM durch, und die andere verwendet die nach der Registrierung bei GCM zurückgegebene Registrierungs-ID für die Registrierung bei Azure Notification Hubs. - Die Funktion *updateLog* definiert eine einfache Protokollierungsfunktion. - *registerWithGCM* ist der Klick-Handler für die erste Schaltfläche, der den **chrome.gcm.register**-Aufruf zum Registrieren dieser Chrome-App-Instanz an GCM sendet. - *registerCallback* ist die Rückruffunktion, die bei der Rückgabe des oben stehenden GCM-Registrierungsaufrufs aufgerufen wird. - *registerWithNH* ist der Klick-Handler für die zweite Schaltfläche, der die Registrierung bei Notification Hubs durchführt. Er ruft die vom Benutzer angegebenen Werte für **hubName** und **connectionString** ab und erstellt den REST-API-Aufruf für die Notification Hubs-Registrierung. - *splitConnectionString* und *generateSaSToken* sind JavaScript-Implementierungen zum Erstellen eines SaS-Tokens, das an alle REST-API-Aufrufe gesendet werden muss. Weitere Informationen finden Sie unter [Allgemeine Konzepte](http://msdn.microsoft.com/library/dn495627.aspx). - Die Funktion *sendNHRegistrationRequest* sendet einen HTTP-REST-Aufruf. – *registrationPayload* definiert die Nutzlast der XML-Datei der Registrierung. Weitere Informationen finden Sie unter [Erstellen der Registrierungs-NH-REST-API]. Die darin enthaltene Registrierungs-ID wird mit den von GCM empfangenen Daten aktualisiert. - *client* ist eine Instanz von **XMLHttpRequest**, die zum Senden der HTTP POST-Anforderung verwendet wird. Beachten Sie, dass der **Authorization**-Header mit **sasToken** aktualisiert wird. Beim erfolgreichen Abschluss dieses Aufrufs wird diese Chrome-App-Instanz bei Azure Notification Hubs registriert.


Am Ende dieser Schritte sollte die folgende Ansicht für den Ordner angezeigt werden: ![][21]

###Einrichten und Testen Ihrer Chrome-App

1. Öffnen Sie den Chrome-Browser. Öffnen Sie **Chrome-Erweiterungen**, und aktivieren Sie **Entwicklermodus**.

   	![][16]

2. Klicken Sie auf **Entpackte Erweiterung laden**, und navigieren Sie zu dem Ordner, in dem Sie die Dateien erstellt haben. Sie können optional auch das **Chrome Apps & Extensions Developer Tool** verwenden. Dieses Tool ist eine eigene Chrome-App, die aus dem Chrome Web Store installiert werden muss und erweiterte Debuggingfunktionen für die Chrome-App-Entwicklung bereitstellt.

   	![][17]

3. Wenn die Chrome-App ohne Fehler erstellt wird, wird sie angezeigt.

   	![][18]

4. Geben Sie die **Projektnummer**, die Sie zuvor in der **Google Cloud Console** erhalten haben, als Absender-ID ein, und klicken Sie auf **Bei GCM registrieren**. Die Meldung **Registration with GCM succeeded** muss angezeigt werden.

   	![][19]

5. Geben Sie den **Notification Hub-Namen** und die **DefaultListenSharedAccessSignature** ein, die Sie zuvor im Azure-Portal erhalten haben, und klicken Sie auf **Bei Azure Notification Hub registrieren**. Die Meldung **Notification Hub Registration successful!** und die Details der Antwort auf die Registrierung mit der Azure Notification Hubs-Registrierungs-ID werden angezeigt.

   	![][20]

##<a name="send"></a>Senden einer Benachrichtigung an Ihre Chrome-App

In diesem Lernprogramm senden Sie Benachrichtigungen mithilfe einer .NET-Konsolenanwendung. Sie können Benachrichtigungen mit Notification Hubs jedoch von jedem beliebigen Back-End über die <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden.

Ein Beispiel zum Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End aus finden Sie unter "Erste Schritte mit Pushbenachrichtigungen in Mobile Services" ([.NET-Back-End](../mobile-services-javascript-backend-android-get-started-push.md) | [JavaScript-Back-End](../mobile-services-javascript-backend-android-get-started-push.md)). Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter "Verwenden von Notification Hubs mit Java/PHP/Python" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** und anschließend **Projekt** aus. Klicken Sie unter **Visual C#** auf **Windows**, auf **Konsolenanwendung** und dann auf **OK**. Damit wird ein neues Konsolenanwendungsprojekt erstellt.

2. Klicken Sie im Menü **Extras** auf **Bibliothekspaket-Manager** und danach auf **Paket-Manager-Konsole**. Daraufhin wird die Paket-Manager-Konsole angezeigt.

3. Führen Sie im Konsolenfenster folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus

   	Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Paket</a> hinzugefügt.

4. Öffnen Sie die Datei **Program.cs**, und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

5. Fügen Sie in der Klasse **Program** folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Stellen Sie sicher, dass Sie den Platzhalter **hub name** durch den Namen des Notification Hubs ersetzen, der im Portal auf der Registerkarte *Notification Hubs* angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hubs" erhalten haben.

	>[AZURE.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff (**Listen**) verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

5. Fügen Sie die folgenden Zeilen zur **Main**-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

6. Stellen Sie sicher, dass Ihr Chrome-Browser geöffnet ist. Ihre Chrome-App muss nicht geöffnet sein. Das folgende Benachrichtigungspopupfenster sollte auf dem Desktop angezeigt werden.

   	![][13]

7. Sie können Ihre Benachrichtigungen auch im Fenster "Chrome-Benachrichtigungen" in der Taskleiste anzeigen (unter Windows), wenn Chrome ausgeführt wird.

   	![][14]

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Benachrichtigungen an Ihre Chrome-App gesendet. Weitere Informationen zu Notification Hubs finden Sie unter [Übersicht über Notification Hubs]. Informationen zum Adressieren bestimmter Benutzer finden Sie im Lernprogramm [Azure Notification Hubs – Benachrichtigen von Benutzern]. Wenn Sie Ihre Benutzer in Interessengruppen einteilen möchten, finden Sie unter [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] weitere Informationen.

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Notification Hub-Beispiel für Chrome-Apps]: http://google.com
[Google Cloud Console]: http://cloud.google.com/console
[Azure-Portal]: https://manage.windowsazure.com/
[Übersicht über Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Chrome-Apps – Übersicht]: https://developer.chrome.com/apps/about_apps
[GCM-Beispiel für Chrome-Apps]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installierbare Web-Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome-Apps auf Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Erstellen der Registrierungs-NH-REST-API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[cryto-js library]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging für Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs – Benachrichtigen von Benutzern]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!---HONumber=Oct15_HO3-->