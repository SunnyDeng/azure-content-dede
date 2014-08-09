<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Get Started with Users" pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Video abspielen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:04

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Dieses Lernprogramm basiert auf dem Mobile Services-Schnellstart. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started) abschließen.

**Hinweis**

Dieses Lernprogramm zeigt Ihnen die grundlegende Methode von Mobile Services zur Authentifizierung von Benutzern mithilfe einer Vielzahl von Identitätsanbietern. Diese Methode lässt sich einfach konfigurieren und unterstützt verschiedene Anbieter. Allerdings müssen Sie sich mit dieser Methode auch bei jedem Start Ihrer App anmelden. Wenn Sie stattdessen Live Connect für eine einmalige Anmeldung in Ihrer Windows Store App verwenden möchten, finden Sie hierzu weitere Information unter [Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect](/de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

Registrieren Ihrer AppRegistrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
--------------------------------------------------------------------------------------------------------

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Sie müssen dann den vom Anbieter generierten geheimen Clientschlüssel bei Mobile Services registrieren.

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren Mobile Service.

        ![][4]

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich den Wert der **Mobile Service URL**.

        ![][5]

    Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3.  Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu registrieren:

-   [Microsoft Account](/de-de/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Facebook-Login](/de-de/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Twitter-Login](/de-de/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Google-Login](/de-de/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

    **Sicherheitshinweis**

    Der vom Anbieter generierte geheime Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

1.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**.

        ![][13]

2.  (Optional) Führen Sie die unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung](/de-de/develop/mobile/how-to-guides/register-windows-store-app-package) beschriebenen Schritte durch.

    **Hinweis**

    Dieser Schritt ist optional, da er nur für Anmeldeanbieter für Microsoft Account relevant ist. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Schließen Sie diesen Schritt ab, wenn Sie einen Identitätsanbieter für Microsoft Account verwenden möchten.

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

Einschränken von BerechtigungenEinschränken von Berechtigungen für authentifizierte Benutzer
--------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

        ![][14]

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

        ![][15]

3.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started) abgeschlossen haben.

4.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

        Dies liegt daran, dass die App versucht als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

Hinzufügen von AuthentifizierungHinzufügen von Authentifizierung zur App
------------------------------------------------------------------------

1.  Öffnen Sie die Projektdatei mainpage.xaml.cs, und fügen Sie den folgenden Ausdruck ein:

         using Windows.UI.Popups;

2.  Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

         private MobileServiceUser user; private async System.Threading.Tasks.Task Authenticate() { while (user == null) { string message; try { user = await App.MobileService .LoginAsync(MobileServiceAuthenticationProvider.Facebook); message = string.Format("You are now logged in - {0}", user.UserId); } catch (InvalidOperationException) { message = "You must log in. Login Required"; } var dialog = new MessageDialog(message); dialog.Commands.Add(new UICommand("OK")); await dialog.ShowAsync(); } }

    Auf diese Weise wird eine Membervariable zum Speichern des aktuellen Benutzers erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für **MobileServiceAuthenticationProvider** oben entsprechend Ihrem Anbieter.

    **Hinweis**

    Wenn Sie Ihre Windows Store-App-Paketinformationen mit Mobile Services registriert haben, sollten Sie die [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594)-Methode aufrufen, indem Sie für *useSingleSignOn* den Wert **Wahr** angeben. Wenn Sie dies nicht tun, werden Ihre Benutzer jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird.

3.  Ersetzen Sie die vorhandene Methodenüberschreibung **OnNavigatedTo** mit der folgenden Methode, die die neue Methode **Authentifizieren** aufruft:

         protected override async void OnNavigatedTo(NavigationEventArgs e) { await Authenticate(); RefreshTodoItems(); }

4.  Drücken Sie F5, um die App auszuführen und sich mit dem von Ihnen ausgewählten Identitätsanbieter bei der App anzumelden.

        Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Nächste Schritte
----------------

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz](/de-de/develop/mobile/how-to-guides/work-with-net-client-library).

