 <properties
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Mobile Services-Apps (Xamarin.Forms) – Mobile Services"
	description="Erfahren Sie mehr über die Verwendung von Pushbenachrichtigungen in Xamarin.Forms-Apps mit Azure Mobile Services."
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="03/18/2016"
	ms.author="wesmc"/>

# Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen zur entsprechenden Mobile Apps-Version dieses Themas finden Sie unter [Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin.Forms-App](../app-service-mobile/app-service-mobile-xamarin-forms-get-started-push.md).

##Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie Pushbenachrichtigungen mit Azure Mobile Services an eine Windows Phone-App Ihrer Xamarin.Forms-Lösung senden können. Erstellen Sie zunächst einen mobilen Dienst. Anschließend laden Sie ein Starter-Beispiel herunter, registrieren sich bei den entsprechenden Pushbenachrichtigungsdiensten und fügen der Lösung Code zum Empfangen von Benachrichtigungen von diesen Diensten hinzu.

Wenn Sie dieses Lernprogramm abgeschlossen haben, sendet der mobile Dienst immer dann, wenn ein Benutzer eine Aufgabe in einer der Apps hinzufügt, eine Pushbenachrichtigung. Sie finden das vollständige Beispiel hier: [Vollständiges Beispiel zu Xamarin.Forms Azure-Pushbenachrichtigungen].

Für dieses Lernprogramm ist Folgendes erforderlich:

+ Ein iOS 8-Gerät (Sie können keine Pushbenachrichtigungen in iOS-Simulator testen)
+ iOS-Entwicklerprogramm-Mitgliedschaft
+ [Xamarin.iOS Studio]
+ [Azure Mobile Services-Komponente]
+ Ein aktives Google-Konto
+ [Google Cloud Messaging Client-Komponente] Sie fügen diese Komponente während des Lernprogramms hinzu.

In diesem Thema:

1. [Erstellen eines neuen mobilen Diensts](#create-service)
2. [Herunterladen und Konfigurieren des Starter-Beispiels](#download-starter-sample)
4. [Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms.iOS-App](#iOS)
5. [Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms.Android-App](#Android)
6. [Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms.Windows-App](#Windows)
7. [Aktualisieren des Azure-Tabelleneinfügeskripts zum Senden von Pushbenachrichtigungen an alle Apps](#all-apps)

## <a name="create-service"></a>Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle erstellen.

1. Klicken Sie im klassischen Azure-Portal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und dann auf **+Erstellen**.

    ![][123]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie unter **Tabellenname** den Namen _TodoItem_ ein, und klicken Sie auf den Häkchenknopf.

    ![][124]

  	Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann Daten in der Tabelle abrufen und ändern.

    > [AZURE.NOTE] Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

4. Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5. Klicken Sie auf die Registerkarte **Columns** und vergewissern Sie sich, dass diese nur eine einzige **id**-Spalte enthält, die automatisch erstellt wurde.

  	Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    > [AZURE.NOTE] Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

## <a name="download-starter-sample"></a>Herunterladen und Konfigurieren des Starter-Beispiels
Im Folgenden werden Pushbenachrichtigungen einem vorhandenen Beispiel hinzugefügt.

1. Laden Sie das folgende Beispiel herunter: [Starter-Beispiel für Xamarin.Forms Azure-Pushbenachrichtigungen].

2. Klicken Sie im [klassischen Azure-Portal] auf **Mobile Services** und dann auf den mobilen Dienst. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site-URL**. Klicken Sie dann auf **Schlüssel verwalten** und notieren Sie den **Anwendungsschlüssel**. Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

3. Öffnen Sie im Projekt **ToDoAzure(Portable)** der Lösung die Datei **Constants.cs** und ersetzen Sie `ApplicationURL` und `ApplicationKey` durch die Website-URL und den Anwendungsschlüssel, die bzw. den Sie im vorherigen Schritt abgerufen haben.

## <a name="iOS"></a>Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms.iOS-App

In diesem Lernprogramm fügen Sie der iOS-App mithilfe des Apple Push Notification Service (APNS) Pushbenachrichtigungen hinzu. Sie benötigen ein aktives Google-Konto und die [Google Cloud Messaging Client-Komponente].

>[AZURE.IMPORTANT] Pushbenachrichtigungen müssen aufgrund von Anforderungen des Apple Push Notification Service (APNS) auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

APNS verwendet Zertifikate zur Authentifizierung Ihres mobilen Diensts. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service].

### <a name="certificates"></a>Erstellen der Zertifikatsignieranforderungsdatei

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1. Führen Sie unter den Hilfsprogrammen die **Schlüsselbundverwaltung** aus.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

    ![][5]

3. Machen Sie Angaben in den Feldern **E-Mail-Adresse des Benutzers** und **Allgemeiner Name**. Vergewissern Sie sich, dass **Auf Datenträger speichern** ausgewählt ist, und klicken Sie dann auf **Fortfahren**.

    ![][6]

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

    ![][7]

    Merken Sie sich den ausgewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

### <a name="register"></a>Registrieren der App für Pushbenachrichtigungen

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und für Pushbenachrichtigungen angemeldet werden.

1. Falls Sie Ihre App noch nicht registriert haben, navigieren Sie im Apple Developer Center zum <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine App-ID für Ihre App zu erstellen.

    ![][102]

2. Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den eindeutigen **Bundle Identifier** ein und merken Sie ihn sich, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer einzigartig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen.

    ![][103]

    Hierdurch wird Ihre App-ID generiert, und Sie werden zum **Übermitteln** der Daten aufgefordert. Klicken Sie auf **Senden**.

    ![][104]

    Nachdem Sie **Submit** angeklickt haben, wird der **Registration complete**-Bildschirm geöffnet, wie unten gezeigt. Klicken Sie auf **Done**.

    ![][105]

3. Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile.

    ![][106]

    Durch Klicken auf die App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Settings**.

    ![][107]

4. Scrollen Sie bis zur Unterseite des Bildschirms und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

    ![][108]

    Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.

    Hinweis: In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5. Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der CSR-Datei, und klicken Sie dann auf **Generate**.

    ![][110]

6. Nach Erstellung des Zertifikats vom Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

    ![][111]

    Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert.

    ![][9]

    Hinweis: Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens <strong>aps\_development.cer</strong>.

7. Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps\_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![][10]

    Hinweis: Der Name in Ihrem Zertifikat kann abweichen, weist jedoch das Präfix <strong>Apple Development iOS Push Notification Services:</strong> auf.

Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und zu Mobile Services hochladen, um Authentifizierung mit APNS zu ermöglichen.

### <a name="profile"></a>Erstellen eines Bereitstellungsprofils für die App

1. Zurück im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet

    ![][112]

2. Wählen Sie unter **Development** die Option **iOS App Development** als Bereitstellungsprofiltyp aus, und klicken Sie dann auf **Continue**.

3. Anschließend wählen Sie die App-ID für die Mobile Services Quickstart-App aus der **App ID**-Dropdown-Liste, und klicken Sie auf **Continue**.

    ![][113]

4. Wählen Sie auf dem Bildschirm **Select certificates** wählen Sie das zuvor erstellte Zertifikat aus, und klicken Sie auf **Continue**

    ![][114]

5. Anschließend wählen Sie die zum Testen zu verwendenden **Devices**, und klicken Sie auf **Continue**.

    ![][115]

6. Geben Sie zum Schluss noch einen Namen für das Profil im Feld **Profile Name** ein, klicken Sie auf **Generate**, dann auf **Done**.

    ![][116]

    Dadurch wird ein neues Bereitstellungsprofil erstellt.

    ![][117]

7. In Xcode öffnen Sie den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und klicken Sie schließlich auf die Schaltfläche **Refresh** an der Unterseite im mittleren Bereich.

### <a name="configure-mobileServices"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

Nachdem Sie Ihre App bei APNS registriert und Sie Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihren mobilen Dienst für Integration mit APNS konfigurieren.

1. Klicken Sie in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Export**, benennen Sie Ihre Datei, wählen Sie das **.p12**-Format, und klicken Sie dann auf **Save**.

    ![][28]

    Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

2. Melden Sie sich beim [klassischen Azure-Portal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

    ![][18]

3. Klicken Sie auf die Registerkarte **Push** und dann unter **apple push notification settings** auf **Upload**.

    ![][19]

    Der Dialog "Zertifikat hochladen" wird angezeigt.

4. Klicken Sie auf **Datei**, wählen Sie die exportierte .p12-Zertifikatsdatei, geben Sie das **Kennwort** ein, vergewissern Sie sich, dass der richtige **Modus** gewählt ist, klicken Sie auf das Häkchensymbol und dann auf **Speichern**.

    ![][20]

Ihr mobiler Dienst ist nun für APNS konfiguriert.

### <a name="configure-app"></a>Konfigurieren der Xamarin.iOS-Anwendung

1. Öffnen Sie in Xamarin.Studio oder Visual Studio die Datei **Info.plist**, und aktualisieren Sie die **Bundle-ID** mit der zuvor erstellten ID.

    ![][121]

2. Scrollen Sie nach unten zu **Background Modes**, und aktivieren Sie **Enable Background Modes** und **Remote notifications**.

    ![][122]

3. Doppelklicken Sie im Projektmappen-Bereich auf Ihr Projekt, um die **Projektoptionen** zu öffnen.

4.  Wählen Sie unter **Build** die Option **iOS Bundle Signing** aus, und wählen Sie die **Identität** und das **Bereitstellungsprofil**, die Sie gerade für dieses Projekt eingerichtet haben.

    ![][120]

    Dadurch wird sichergestellt, dass das Xamarin-Projekt das neue Profil für die Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning] (Xamarin – Gerätebereitstellung, in englischer Sprache).

### <a name="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

1. Erweitern Sie in Xamarin.Studio oder Visual Studio das Projekt **ToDoAzure.iOS**, öffnen Sie die **AppDelegate**-Klasse, und ersetzen Sie dann das **FinishedLaunching**-Ereignis durch folgenden Code:

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();

            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. Überschreiben Sie in **AppDelegate** das Ereignis **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient();

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };

            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("de-DE"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag);
        }

7. Überschreiben Sie in **AppDelegate** das Ereignis **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

Ihre App kann Pushbenachrichtigungen nun unterstützen.

### <a name="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im klassischen Azure-Portal

1. Klicken Sie im klassischen Azure-Portal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][21]

2. Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][22]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }

    Auf diese Weise wird ein neues Einfügeskript registriert, mit dem eine Pushbenachrichtigung (der eingefügte Text) an das in der Einfügeanforderung angegebene Gerät sendet.

   >[AZURE.NOTE] Dieses Skript verzögert die Übermittlung der Benachrichtigung, um Ihnen Zeit zum Schließen der App für den Empfang einer Toastbenachrichtigung zu geben.

### <a name="test"></a>Testen von Pushbenachrichtigungen in der App

1. Drücken Sie auf **Run**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren

   >[AZURE.NOTE] Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Klicken Sie in der App auf die Schaltfläche **Hinzufügen**, fügen Sie einen Aufgabentitel hinzu, und klicken Sie dann auf die Schaltfläche **Speichern**.

3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.


Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## <a name="Android"></a>Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms.Android-App

Zum Hinzufügen von Pushbenachrichtigungen zur Android-App verwenden Sie den Google Cloud Messaging-Dienst (GCM). Sie benötigen ein aktives Google-Konto und die [Google Cloud Messaging Client-Komponente].

###<a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>Konfigurieren von mobilen Diensten zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts zum Senden von Benachrichtigungen

>[AZURE.NOTE] Die folgenden Schritte veranschaulichen die Vorgehensweise beim Aktualisieren des Skripts, das für den Einfügevorgang in der TodoItem-Tabelle im klassischen Azure-Portal registriert ist. Der Zugriff und die Bearbeitung dieses Mobile Service-Skripts ist auch direkt in Visual Studio am Azure-Knoten im Server-Explorer möglich.

Klicken Sie im [klassischen Azure-Portal] auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

   ![][21]

2. Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

   ![][22]

Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Auf diese Weise wird ein neues Einfügeskript registriert, mit dem eine Pushbenachrichtigung (der eingefügte Text) an das in der Einfügeanforderung angegebene Gerät sendet.

   >[AZURE.NOTE] Dieses Skript verzögert die Übermittlung der Benachrichtigung, um Ihnen Zeit zum Schließen der App für den Empfang einer Toastbenachrichtigung zu geben.


###<a id="configure-app"></a>Konfigurieren vorhandener Projekte für Pushbenachrichtigungen

1. Erweitern Sie in der Projektmappenansicht den **Komponentenordner** in der Xamarin.Android-App, und vergewissern Sie sich, dass das Azure Mobile Services-Paket installiert ist.

2. Klicken Sie mit der rechten Maustaste auf den **Komponentenordner**, klicken Sie auf **Get More Components...**, suchen Sie nach der Komponente **Google Cloud Messaging Client**, und fügen Sie sie dem Projekt hinzu.

1. Öffnen Sie die Projektdatei "MainActivity.cs", und fügen Sie der Klasse die folgende using-Anweisung hinzu:

		using Gcm.Client;


4.	Fügen Sie folgenden Code in der **OnCreate**-Methode der **MainActivity**-Klasse nach dem Aufruf der **LoadApplication**-Methode hinzu:

            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

Die **MainActivity** ist damit auf das Hinzufügen von Pushbenachrichtigungen vorbereitet.

###<a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

5. Erstellen Sie im Projekt "ToDoAzure.Droid" eine neue Klasse im Projekt mit dem Namen `GcmService`.

5. Fügen Sie **GcmService** die folgenden using-Anweisungen hinzu:

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. Fügen Sie die folgenden Berechtigungsanforderungen zwischen den **using**-Anweisungen und der **namespace**-Deklaration hinzu:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. Fügen Sie in der Projektdatei **GcmService.cs** die folgende Klasse hinzu:

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {

            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	Ersetzen Sie im obigen Code _`<PROJECT_NUMBER>`_ durch die von Google beim Bereitstellen Ihrer App im Google-Entwicklerportal zugewiesene Projektnummer.

8. Fügen Sie in der Projektdatei "GcmService.cs" folgenden Code hinzu, mit dem die **GcmService**-Klasse definiert wird:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	Beachten Sie, dass die Klasse von **GcmServiceBase** abgeleitet ist, und dass diese über das Attribut **Service** verfügen muss.

	>[AZURE.NOTE]Die **GcmServiceBase**-Klasse implementiert die Methoden **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** und **OnError()**. Sie müssen diese Methoden in der **GcmService**-Klasse überschreiben.

5. Fügen Sie folgenden Code der **GcmService**-Klasse hinzu, mit dem der **OnRegistered**-Ereignishandler überschrieben wird.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;

            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	Diese Methode verwendet die zurückgegebene GCM-Registrierungs-ID für die Pushbenachrichtigungsregistrierung bei Azure.

10. Überschreiben Sie die **OnMessage**-Methode in **GcmService** mit folgendem Code:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. Fügen Sie die folgenden Methodenüberschreibungen für **OnUnRegistered()** und **OnError()** hinzu. Diese sind erforderlich, um das Projekt zu kompilieren.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("GcmService", "Unregistered RegisterationId : " + registrationId);
		}

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>Testen von Pushbenachrichtigungen in der App

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

> [AZURE.IMPORTANT] Damit Sie Pushbenachrichtigungen erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu **Einstellungen**, und klicken Sie auf **Konto hinzufügen**). Überprüfen Sie zudem, dass der Emulator mit dem Internet verbunden ist.

1. Klicken Sie unter **Tools** auf **Open Android Emulator Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Edit**.

    ![][125]

2. Wählen Sie **Google APIs** unter **Ziel** aus, und klicken Sie auf **OK**.

    ![][126]

3. Klicken Sie in der oberen Symbolleiste auf **Run**, und wählen Sie Ihre App aus. Daraufhin wird der Emulator gestartet und die App ausgeführt.

  Die App ruft die *registrationId* von GCM ab, und registriert sich beim Notification Hub.

1. Fügen Sie in der App eine neue Aufgabe hinzu.

2. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.

	![][127]

## <a name="Windows"></a>Hinzufügen von Pushbenachrichtigungen zur Xamarin Forms.Windows-App

In diesem Abschnitt erfahren Sie, wie Sie Pushbenachrichtigungen mit Azure Mobile Services an eine Windows Phone-Silverlight-App Ihrer Xamarin.Forms-Lösung senden.

###<a id="update-app"></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1. Öffnen Sie in Visual Studio die Datei "App.xaml.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Phone.Notification;

3. Fügen Sie der Datei "App.xaml.cs" folgenden Code hinzu:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    Dieser Code ruft den Kanal-URI für die App aus dem von Windows Phone 8.x "Silverlight" verwendeten Microsoft-Pushbenachrichtigungsdienst (MPNS) ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen.

	>[AZURE.NOTE]In diesem Lernprogramm sendet der mobile Dienst eine Popupbenachrichtigung an das Gerät. Wenn Sie eine Kachelbenachrichtigung senden, müssen Sie stattdessen die **BindToShellTile**-Methode für den Kanal aufrufen.

4. Fügen Sie in der Datei "App.xaml.cs" am Anfang des **Application\_Launching**-Ereignishandlers den folgenden Aufruf der neuen **AcquirePushChannel**-Methode hinzu:

        AcquirePushChannel();

	Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

5. Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

6.	Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei „WMAppManifest.xml“, klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie die Funktion **ID\_CAP\_PUSH\_NOTIFICATION**.

   	![Aktivieren von Benachrichtigungen in VS](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen ausgeben kann.

###<a id="update-scripts"></a> Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen

Schließlich müssen Sie das Skript, das für den Einfügevorgang in der Tabelle "TodoItem" registriert wird, aktualisieren, um Benachrichtigungen zu senden.

1. Klicken Sie im [klassischen Azure-Portal] auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![][21]

2. Klicken Sie unter **todoitem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][22]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Auf diese Weise wird ein neues Einfügeskript registriert, mit dem eine Pushbenachrichtigung (der eingefügte Text) an das in der Einfügeanforderung angegebene Gerät sendet.

3. Klicken Sie auf die Registerkarte **Push**, aktivieren Sie **Enable unauthenticated push notifications**, und klicken Sie dann auf **Speichern**.

	Daraufhin kann der mobile Dienst im nicht authentifizierten Modus eine Verbindung mit MPNS zum Senden von Pushbenachrichtigungen herstellen.

	>[AZURE.NOTE]Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. In diesem Modus beschränkt MPNS die Anzahl der Benachrichtigungen, die an einen Gerätekanal gesendet werden können. Um diese Beschränkung aufzuheben, müssen Sie ein Zertifikat generieren und hochladen, indem Sie auf **Upload** klicken und das Zertifikat auswählen. Weitere Informationen zum Generieren des Zertifikats finden Sie unter [Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone].

###<a id="test"></a> Testen von Pushbenachrichtigungen in der App

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

    >[AZURE.NOTE] Beim Ausführen von Tests im Windows Phone-Emulator kann der Fehler "401 Nicht autorisiert – RegistrationAuthorizationException" auftreten. Der Fehler kann während des `RegisterNativeAsync()`-Aufrufs auftreten und wird dadurch verursacht, wie der Windows Phone-Emulator seine Uhr mit dem Host-PC synchronisiert. Dies kann dazu führen, dass ein Sicherheitstoken abgelehnt wird. Um das Problem zu beheben, stellen Sie die Uhrzeit im Emulator vor dem Testen einfach manuell ein.

5. Erstellen Sie in der App eine neue Aufgabe mit dem Titel **Hello push**, und klicken Sie dann umgehend auf die Schaltfläche "Start" oder "Zurück", um die App zu verlassen.

  	Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet. Das Gerät empfängt eine Popupbenachrichtigung mit dem Text **hello push**.

	![Empfangene Toast-Benachrichtigung](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Wenn Sie sich noch in der App befinden, erhalten Sie die Benachrichtigung nicht. Um eine Toast-Benachrichtigung zu empfangen, während die App aktiv ist, müssen Sie das [ShellToastNotificationReceived] (http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx)-Ereignis behandeln.

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[klassischen Azure-Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Google Cloud Messaging Client-Komponente]: http://components.xamarin.com/view/GCMClient/
[Starter-Beispiel für Xamarin.Forms Azure-Pushbenachrichtigungen]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[Vollständiges Beispiel zu Xamarin.Forms Azure-Pushbenachrichtigungen]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms

<!---HONumber=AcomDC_0323_2016-->