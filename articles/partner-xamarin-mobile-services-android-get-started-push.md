<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.Android apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services" documentationCenter="Mobile" authors="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Xamarin.Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe des Google Cloud Messaging (GCM)-Service zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App für Pushbenachrichtigungen](#register)
2.  [Konfigurieren von Mobile Services](#configure)
3.  [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
4.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
5.  [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Ein aktives Google-Konto

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started-xamarin-android) abschließen.

Registrieren Ihrer AppRegistrieren Ihrer App für Pushbenachrichtigungen
-----------------------------------------------------------------------

**Hinweis**

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302), um ein neues Google-Konto zu erstellen.

1.  Navigieren Sie zur [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303)-Website, melden Sie sich mit Ihren Google-Anmeldeinformationen an, und klicken Sie auf **Create project**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png)

    > [WACOM.NOTE] 
    > Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite **Dashboard** weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie **API Project&lt;**, klicken Sie unter **Other projects** auf **Create...**, geben Sie einen Projektnamen ein, und klicken Sie auf **Create project**.

2.  Klicken Sie in der linken Spalte auf die Schaltfläche für die Übersicht, und notieren Sie sich die Projektnummer im Dashboard-Bereich.

    Später im Lernprogramm legen Sie diesen Wert als die Variable **PROJECT\_ID** im Client fest.

3.  Klicken Sie auf der Seite [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) auf **Services**, und klicken Sie auf den Schalter, um **Google Cloud Messaging for Android** zu aktivieren. Akzeptieren Sie die Nutzungsbedingungen.

4.  Klicken Sie auf **API Access** und dann auf **Create new Server key...**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png)

5.  Klicken Sie unter **Configure Server Key for API Project** auf **Create**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png)

6.  Notieren Sie sich den Wert **API key**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png)

Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

<h2>Konfigurieren des ServicesKonfigurieren von Mobile Services zum Senden von Pushanforderungen</h2>

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert **API Key** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png)

    Ihr mobiler Dienst ist jetzt konfiguriert, um mit GCM Pushbenachrichtigungen zu senden.

<h2>Hinzufügen von PushbenachrichtigungenHinzufügen von Pushbenachrichtigungen zu Ihrer App</h2>

1.  Zunächst möchten wir **PushSharp** als Referenz in unserem Projekt hinzufügen. Hierzu müssen wir die aktuellste Version von PushSharp kompilieren und die kompilierte DLL als Referenz zu unserem Xamarin.Android-Projekt hinzufügen.

2.  Laden Sie sich von der Github-Seite [PushSharp](https://github.com/Redth/PushSharp) die neueste Version herunter. Sobald Sie die Sammlung von Dateien extrahiert haben, navigieren Sie zum folgenden Beispiel-Projektordner:

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. Öffnen Sie dort die Projetdatei:

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Erstellen Sie das PushSharp-Client-Beispiel "MonoForAndroid" im **Freigabemodus**.

4.  Erstellen Sie in Ihrem Xamarin.Android-Projekt einen Ordner **\_external**.

5.  Kopieren Sie die folgende Datei aus dem PushSharp-Client-Beispiel "MonoForAndroid" in den neu erstellten Ordner **\_external** in Ihrem Xamarin.Android-Projektordner:

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Öffnen Sie Ihr Xamarin.Android-Projekt in Xamarin Studio (oder Visual Studio).

7.  Klicken Sie mit der rechten Maustaste auf den Ordner **Verweise**, und wählen Sie **Verweise bearbeiten...**.

8.  Navigieren Sie zur Registerkarte **.Net Assembly**, rufen Sie den Ordner **\_external** Ihres Projekts auf, wählen Sie die zuvor erstellte **PushSharp.Client.MonoForAndroid.dll**, und klicken Sie auf **Hinzufügen**. Klicken auf **OK**, um das Dialogfenster zu schließen.

9.  Öffnen Sie **Constants.cs**, und fügen Sie die folgende Zeile hinzu, indem Sie **PROJECT\_ID** durch die zuvor notierte Google Project\_ID ersetzen:

         public const string SenderID = "PROJECT_ID"; // Google-API-Projektnummer

10. Kopieren Sie die Datei **PushService.cs** aus Ihrem PushSharp-Client-Beispiel "MonoForAndroid" in Ihren Xamarin.Android-Projektordner, und fügen Sie sie zu Ihrem Projekt hinzu.

11. Ändern Sie den in **PushService.cs** verwendeten Namespace, und passen Sie ihn an den Namespace Ihres Projekts an (z. B. XamarinTodoQuickStart).

12. Ändern Sie das Array **SENDER\_IDS** in **PushService.cs**, um auf die Konstante **SenderID** zu verweisen, die wir zuvor erstellt haben:

        public static string[] SENDER_IDS = new string[] { Constants.SenderID };

13. Fügen Sie eine neue statische Eigenschaft zum **PushHandlerService** in **PushService.cs** hinzu, um unsere Geräteregistrierungs-ID zu verfolgen:

        public static string RegistrationID { get; private set; }

14. Aktualisieren Sie die Methode **OnRegistered** in **PushService.cs**, um die erhaltene Registrierungs-ID in unserer lokalen statischen Variable zu speichern:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
        }

15. Aktualisieren Sie die Methode **OnMessage** in **PushService.cs**, um die erhaltenen Pushnachrichten als Teil der Benachrichtigung anzuzeigen (ersetzen Sie den vorhandenen Aufruf **createNotification**):

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);

16. Beachten Sie, dass die Methode **OnMessage** standardmäßig über den folgenden Code verfügt, um die letzte erhaltene Pushnachricht zu speichern:

        //Nachricht speichern
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. Aktualisieren Sie die Methode **createNotification** in **PushService.cs**, um auf **TodoActivity** anstatt auf **DefaultActivity** zu verweisen.

18. Öffnen Sie **TodoActivity.cs**, und fügen Sie die folgende "using"-Anweisung hinzu:

        using PushSharp.Client;

19. Fügen Sie in **TodoActivity.cs** die folgenden Zeilen direkt über der Stelle ein, an der der **MobileServiceClient** erstellt wird:

        // Prüfen, ob alles richtig eingerichtet ist
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Für Pushbenachrichtigungen registrieren
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. Öffnen Sie **TodoItem.cs**, und fügen Sie ein neues Feld hinzu, um die registrierte Geräte-ID für die Person nachzuverfolgen, die das TodoItem hinzugefügt hat:

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. Aktualisieren Sie in **TodoActivity.cs** die Methode **AddItem**, um die **RegistrationID** des neu hinzugefügten **TodoItem** auf die Registrierungs-ID des Geräts festzulegen, die Sie bei der Registrierung erhalten haben:

        // Neues Element erstellen
        var item = new TodoItem() {
            Text = textNewTodo.Text,
            Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
        };

Ihre App kann Pushbenachrichtigungen nun unterstützen.

Aktualisieren des EinfügeskriptsAktualisieren des registrierten Einfügeskripts im Verwaltungsportal
---------------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  Klicken Sie unter **TodoItem** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

	![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png)

    Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **TodoItem** eine Einfügung auftritt.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // In die Antwort schreiben und dann die Benachrichtigung im Hintergrund senden
                     request.respond();
                     push.gcm.send(item.channel, item.text, {
                         success: function(response) {
                             console.log('Push notification sent: ', response);
                         }, error: function(error) {
                             console.log('Error sending push notification: ', error);
                         }
                     });
                 }
             });
         }

    Auf diese Weise wird ein neues Einfügeskript registriert, das über das [gcm object](http://go.microsoft.com/fwlink/p/?LinkId=282645) eine Pushbenachrichtigung (den eingefügten Text) zum in der Einfügeanforderung angegebenen Gerät sendet.

Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

1.  Führen Sie die App aus, und fügen Sie einen neuen Aktivitäteneintrag hinzu. Überprüfen Sie, ob Sie eine Pushbenachrichtigung erhalten haben, dass ein neuer Aktivitäteneintrag hinzugefügt wurde.

2.  Rufen Sie im Azure Verwaltungsportal die Registerkarte **Protokolle** Ihrer mobilen App auf, um die Protokollnachrichten anzuzeigen, die wir oben zur Methode **Einfügen** in der Tabelle **TodoItem** hinzugefügt haben.

3.  Rufen Sie die Tabelle **TodoItem** im Azure Verwaltungsportal auf, um die neue Spalte **Kanal** anzuzeigen, die hinzugefügt wurde und die eindeutige Geräteregistrierungs-IDs enthält.

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

Abgeschlossenes Beispiel abrufen
--------------------------------

Laden Sie das [abgeschlossene Beispielprojekt](http://go.microsoft.com/fwlink/p/?LinkId=331303) herunter. Stellen Sie sicher, dass Sie die Variablen **ApplicationURL**, **ApplicationKey** und **SenderID** mit Ihren eigenen Azure-Einstellungen aktualisieren.

Nächste Schritte
----------------

In diesem einfachen Beispiel empfängt ein Benutzer eine Pushbenachrichtigung mit den soeben eingegebenen Daten. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer](/en-us/develop/mobile/tutorials/push-notifications-to-users-android) werden wir eine eigene Gerätetabelle zum Speichern von Gerätetoken erstellen und beim Auftreten einer Einfügung eine Pushbenachrichtigung an alle gespeicherten Kanäle aussenden.


<!-- Anchors. -->
[Register your app for push notifications]: #register
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[18]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png







<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services Xamarin conceptual]: /en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[PushSharp Github page]: https://github.com/Redth/PushSharp

