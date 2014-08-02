<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started-xamarin-ios) abschließen.

Für den Abschluss dieses Lernprogramms ist [Xamarin.iOS], XCode 5.0 oder iOS 5.0 oder neuer erforderlich.

Registrieren Ihrer AppRegistrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
--------------------------------------------------------------------------------------------------------

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Danach muss der vom Anbieter generierte geheime Clientschlüssel bei Mobile Services registriert werden.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

   	![][4]

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site-URL**.

   	![][5]

    Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3.  Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu registrieren:

-   [Microsoft-Konto](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Facebook-Login](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Twitter-Login](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Google-Login](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

    **Sicherheitshinweis**

    Der vom Anbieter generierte geheime Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

1.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**.

   	![][13]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

Einschränken von BerechtigungenEinschränken von Berechtigungen für authentifizierte Benutzer
--------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

   	![][14]

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

   	![][15]

3.  Öffnen Sie in Xcode das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started-xamarin-ios) erstellt haben.

4.  Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Überprüfen Sie, ob eine unverarbeitete Ausnahme mit dem Statuscode 401 (nicht autorisiert) nach dem Start der App ausgelöst wird.

   	Dies geschieht, da die App versucht, als nicht autorisierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_-Tabelle jetzt aber eine Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

Hinzufügen von AuthentifizierungHinzufügen von Authentifizierung zur App
------------------------------------------------------------------------

1.  Öffnen Sie die Projektdatei **TodoService**, und fügen Sie folgende Variablen hinzu:

         // Mobile Service logged in user
         private MobileServiceUser user; 
         public MobileServiceUser User { get { return user; } }

2.  Fügen Sie dann eine neue Methode namens **Authentifizieren** zu **TodoService** hinzu, die folgendermaßen definiert ist:

         private async Task Authenticate(UIViewController view)
         {
             try
             {
                 user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
             }
             catch (Exception ex)
             {
                 Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
             }
         }

    **Hinweis**

    Wenn Sie einen anderen Identitätsanbieter als Microsoft Account verwenden, ändern Sie den oben an **LoginAsync** übergebenen Wert in Folgendes: *Facebook*, *Twitter* oder *Google*.

3.  Verschieben Sie Anforderung für die **TodoItem**-Tabelle vom **TodoService**-Konstruktor in eine neue Methode namens **CreateTable**:

         private async Task CreateTable()
         {
             // Create an MSTable instance to allow us to work with the TodoItem table
             todoTable = client.GetTable<TodoItem>();
         }

4.  Erstellen Sie eine neue asynchrone öffentliche Methode namens **LoginAndGetData**, die folgendermaßen definiert ist:

         public async Task LoginAndGetData(UIViewController view)
         {
             await Authenticate(view);
             await CreateTable();
         }

5.  Setzen Sie im **TodoListViewController** die **ViewDidAppear**-Methode außer Kraft. Definieren Sie sie dann wie unten angegeben. Dadurch wird der Benutzer angemeldet, sofern der **TodoService** noch keinen Handle für den Benutzer hat:

         public override async void ViewDidAppear(bool animated)
         {
             base.ViewDidAppear(animated);

             if (TodoService.DefaultService.User == null)
             {
                 await TodoService.DefaultService.LoginAndGetData(this);
             }

             if (TodoService.DefaultService.User == null)
             {
                 // TODO:: show error
                 return;
             } 
                    
             RefreshAsync();
         }

6.  Entfernen Sie den ursprünglichen Aufruf von **RefreshAsync** aus **TodoListViewController.ViewDidLoad**.

7.  Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

 Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Abgeschlossenes Beispiel abrufen
--------------------------------

Laden Sie das [abgeschlossene Beispielprojekt](http://go.microsoft.com/fwlink/p/?LinkId=331328) herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

Nächste Schritte
----------------

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.




<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png