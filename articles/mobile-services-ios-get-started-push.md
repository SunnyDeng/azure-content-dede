<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" solutions="" manager="" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an eine iOS-App senden. In diesem Lernprogramm fügen Sie mithilfe des Apple Push Notification Services (APNS) Pushbenachrichtigungen zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

[Lernprogramm ansehen](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) [Video abspielen](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) 10:37

> [WACOM.NOTE] In diesem Thema erfahren Sie, wie Sie Pushbenachrichtigungen mithilfe der Legacyunterstützung von Mobile Services aktivieren. Azure Notification Hubs ist in Mobile Services integriert, damit Sie vorlagenbasierte und plattformübergreifende Pushbenachrichtigungen an Millionen von Geräte senden können. Standardmäßig sind Pushbenachrichtigungen unter Verwendung von Notification Hubs nicht aktiviert, und es gibt derzeit keine Notification Hub-Unterstützung für iOS in den Mobile Services-Bibliotheken. Sie können jedoch mithilfe von Notification Hub-Bibliotheken Pushbenachrichtigungen von Ihrem mobilen Dienst senden. Weitere Informationen finden Sie unter [Erste Schritte mit Notification Hubs](/de-de/documentation/articles/notification-hubs-ios-get-started/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Erstellen der Zertifikatsignieranforderungsdatei](#certificates)
2.  [Registrieren der App und Aktivieren von Pushbenachrichtigungen](#register)
3.  [Erstellen eines Bereitstellungsprofils für die App](#profile)
4.  [Konfigurieren von Mobile Services](#configure)
5.  [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
6.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
7.  [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Ein Gerät, das auf iOS 5.0 (oder einer neueren Version) laufen kann
-   iOS-Entwicklerprogramm-Mitgliedschaft

    > [WACOM.NOTE] Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem iOS-fähigen Gerät (iPhone oder iPad) anstatt im Emulator bereitgestellt und getestet werden.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-ios) abschließen.

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Dienstes. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Erstellen der Zertifikatsignieranforderungsdatei
------------------------------------------------

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1. Führen Sie das das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

3. Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name**, vergewissern Sie sich, dass **Auf der Festplatte sichern** gewählt ist, und klicken Sie dann auf **Fortfahren**. Lassen Sie das Feld **E-Mail der Zert.-Instanz** leer, da hier keine Eingabe benötigt wird.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png)
  
  	Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

Registrieren der App für Pushbenachrichtigungen
-----------------------------------------------

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.

1.  Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456), melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine neue App zu registrieren.

  ![][102] 

2.  Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den Wert *MobileServices.Quickstart* in **Bundle Identifier** ein, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer einzigartig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen.

  ![][103]
     
  Hierdurch wird Ihre App-ID generiert, und Sie werden zum **Übermitteln** der Daten aufgefordert. Klicken Sie auf **Submit**
     
  ![][104] 
     
  Nachdem Sie **Submit** angeklickt haben, erscheint die Registrierabschlussanzeige **Registration complete**, wie unten gezeigt. Klicken Sie auf **Done**.
     
  ![][105]

    > [WACOM.NOTE] Wenn Sie sich dazu entscheiden, einen anderen **Bundle Identifier**-Wert als *MobileServices.Quickstart* vorzugeben, müssen Sie auch die Bundle-ID in Ihrem Xcode-Projekt aktualisieren.

3.  Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile.

  ![][106]
     
  Durch Anklicken der App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Settings**.
   
  ![][107] 

4.  Scrollen Sie bis zur Unterseite des Bildschirms und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

  ![][108] 

  Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.
     
  ![][108] 

    > [WACOM.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5.  Klicken Sie auf **Choose File**, browsen Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei und klicken Sie dann auf **Generate**.

  ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

6.  Nach Erstellung des Zertifikats vom Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

  ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

  Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert. ![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.

7.  Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps\_development.cer**.

   Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

   ![][10]

    > [WACOM.NOTE] Der Name in Ihrem Zertifikat kann verschieden sein, aber er wird den Vorsatz **Apple Development iOS Push Notification Services:** haben.

Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und zu Mobile Services hochladen, um Authentifizierung mit APNS zu ermöglichen.

Erstellen eines Bereitstellungsprofils für die App
--------------------------------------------------

1.  Zurück im [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet

  ![][112]

2.  Unter **Development** wählen Sie **iOS App Development** als Bereitstellungsprofiltyp, und klicken Sie dann auf **Continue**

  ![][113]

3.  Anschließend wählen Sie die App-ID für die Mobile Services Quickstart-App aus der **App ID**-Dropdown-Liste, und klicken Sie auf **Continue**

  ![][114]

4.  Auf dem Bildschirm **Select certificates** wählen Sie das zuvor erstellte Zertifikat und klicken Sie auf **Continue**

  ![][115]

5.  Anschließend wählen Sie die zum Testen zu verwendenden **Devices** und klicken Sie auf **Continue**

  ![][116]

6.  Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, klicken Sie auf **Generate**, dann auf **Done**

  ![][117]

  Dadurch wird ein neues Bereitstellungsprofil erstellt.

7.  In Xcode öffnen Sie den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und klicken Sie schließlich auf die Schaltfläche **Refresh** an der Unterseite im mittleren Bereich.

  ![][101]

8.  Unter **Targets** klicken Sie auf **Quickstart**, erweitern Sie **Code Signing Identity**, und unter **Debug** wählen Sie dann das neue Profil.

  ![][17]

Hierdurch wird sichergestellt, dass das Xcode-Projekt das neue Profil für Codesignierung verwendet. Als Nächstes laden wir das Zertifikat zu Mobile Services hoch.

Konfigurieren von Mobile Services zum Senden von Pushanforderungen
------------------------------------------------------------------

Nachdem Sie Ihre App bei APNS registriert und Sie Ihr Projekt konfiguriert haben, müssen Sie als Nächstes Ihren mobilen Dienst für Integration mit APNS konfigurieren.

1.  In der Schlüsselbundverwaltung klicken Sie mit der rechten Maustaste auf das neue Zertifikat, klicken Sie auf **Exportieren**, nennen Sie Ihre Datei "QuickstartPusher", wählen Sie das **.p12**-Format, und klicken Sie dann auf **Sichern**.

  ![][28]

  Notieren Sie sich den Dateinamen und den Ort des exportierten Zertifikats.

   > [WACOM.NOTE] In diesem Lernprogramm wird eine QuickstartPusher.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

  ![][18]

2.  Klicken Sie auf die Registerkarte **Push** und dann auf **Hochladen**.

  ![][19]

  Der Dialog "Zertifikat hochladen" wird angezeigt.

3.  Klicken Sie auf **Datei**, wählen Sie die exportierte QuickstartPusher.p12-Zertifikatsdatei, geben Sie das **Kennwort** ein, vergewissern Sie sich, dass der richtige **Modus** gewählt ist, klicken Sie auf das Häkchensymbol und dann auf **Speichern**.

  ![][20] 

   > [WACOM.NOTE] In diesem Lernprogramm werden Entwicklungszertifikate verwendet.

Ihr mobiler Dienst ist nun für APNS konfiguriert.

Hinzufügen von Pushbenachrichtigungen zur App
---------------------------------------------

1.  In Xcode öffnen Sie die Datei "QSAppDelegate.h" und fügen Sie die folgende Property unter der **\*window**-Property hinzu:

         @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, wird automatisch eine neue "deviceToken"-Spalte zur **TodoItem**-Tabelle hinzugefügt, wenn ein neues Element mit dieser Property eingefügt wird.

2.  In QSAppDelegate.m ersetzen Sie die folgende Handlermethode in der Implementierung:

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
         (NSDictionary *)launchOptions
         {
             // Register for remote notifications
             [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
             UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
             return YES;
         }

3.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

         // We are registered, so now store the device token (as a string) on the AppDelegate instance
         // taking care to remove the angle brackets first.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {
             NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
             self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
         }

4.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

         // Handle any failure to register. In this case we set the deviceToken to an empty
         // string to prevent the insert from failing.
         - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
         (NSError *)error {
             NSLog(@"Failed to register for remote notifications: %@", error);
             self.deviceToken = @"";
         }

5.  In QSAppDelegate.m fügen Sie die folgende Handlermethode in der Implementierung hinzu:

         // Because toast alerts don't work when the app is running, the app handles them.
         // This uses the userInfo in the payload to display a UIAlertView.
         - (void)application:(UIApplication *)application didReceiveRemoteNotification:
         (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

6.  In QSTodoListViewController.m importieren Sie die Datei "QSAppDelegate.h", sodass Sie den Delegaten zum Erhalten des Gerätetokens verwenden können:

         #import "QSAppDelegate.h"

7.  In QSTodoListViewController.m modifizieren Sie die Aktion **(IBAction)onAdd**, indem Sie die folgende Zeile aufsuchen:

         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

    Ersetzen Sie dies durch den folgenden Code:

         // Get a reference to the AppDelegate to easily retrieve the deviceToken
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // add the device token property to our todo item payload
             @"deviceToken" : delegate.deviceToken
         };

  Auf diese Weise wird eine Referenz zum **QSAppDelegate** hinzugefügt, um den Gerätetoken zu erhalten, und die Anforderungsnutzlast dann modifiziert, um diesen Gerätetoken einzubeziehen.

  > [WACOM.NOTE] Dieser Code muss vor dem Aufruf der <strong>addItem</strong>-Methode hinzugefügt werden.

Ihre App kann Pushbenachrichtigungen nun unterstützen.

Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal
-------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

  ![][21]

2.  Klicken Sie unter **todoitem** auf die Registerkarte **Skript** und wählen Sie **Einfügen** aus.

  ![](./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png)

  Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **TodoItem** eine Einfügung auftritt.

1.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

         function insert(item, user, request) {
             request.execute();
             // Set timeout to delay the notification, to provide time for the 
             // app to be closed on the device to demonstrate toast notifications
             setTimeout(function() {
                 push.apns.send(item.deviceToken, {
                     alert: "Toast: " + item.text,
                     payload: {
                         inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                     }
                 });
             }, 2500);
         }

   	Auf diese Weise wird ein neues Einfügeskript registriert, das über das [apns-Objekt] eine Pushbenachrichtigung (den eingefügten Text) zum in der Einfügeanforderung angegebenen Gerät sendet. 


  > [WACOM.NOTE] Dieses Skript verzögert die Übermittlung der Benachrichtigung, um Ihnen Zeit zum Schließen der App für Empfang einer Toastbenachrichtigung zu geben.

Testen von Pushbenachrichtigungen in der App
--------------------------------------------

1.  Drücken Sie auf **Run**, um das Projekt zu erstellen, starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren

  ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

  > [WACOM.NOTE] Pushbenachrichtigungen von Ihrer App müssen ausdrücklich akzeptiert werden. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2.  In der App geben Sie eine Beschreibung ein, wie zum Beispiel *Neue Mobile Services-Aufgabe*, und klicken Sie dann auf das Plussymbol (**+**).

  ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

3.  Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK**, um diese zu schließen.

  ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

4.  Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob der folgende Toast angezeigt wird.

  ![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

Nächste Schritte
----------------

In diesem einfachen Beispiel empfängt ein Benutzer eine Benachrichtigung mit den soeben eingegebenen Daten. Der von APNS verwendete Gerätetoken wird vom Client in der Anforderung an den mobilen Dienst geliefert. Im nächsten Lernprogramm [Pushbenachrichtigungen an App-Benutzer](/de-de/develop/mobile/tutorials/push-notifications-to-users-ios) werden wir eine eigene Gerätetabelle zum Speichern von Gerätetoken erstellen und beim Auftreten einer Einfügung eine Pushbenachrichtigung an alle gespeicherten Kanäle aussenden.


<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[apns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=272333