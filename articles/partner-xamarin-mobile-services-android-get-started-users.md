<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" documentationCenter="Mobile" authors="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer Xamarin.Android-App. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-xamarin-android) abschließen.

Dieses Lernprogramm benötigt [Xamarin.Android] und Android SDK 4.2 oder eine neuere Version.

Registrieren Ihrer AppRegistrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
--------------------------------------------------------------------------------------------------------

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Danach muss der vom Anbieter generierte geheime Clientschlüssel bei Mobile Services registriert werden.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

   	![][4]

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site-URL**.

   	![][5]

    Möglicherweise müssen Sie diesen Wert an den Identitätsanbieter übermitteln, wenn Sie Ihre App registrieren.

3.  Wählen Sie aus der Liste unten einen unterstützten Identitätsanbieter aus, und folgen Sie den unten aufgeführten Schritten, um Ihre App bei diesem Anbieter zu registrieren:

-   [Microsoft-Konto](/de-de/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Facebook-Login](/de-de/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Twitter-Login](/de-de/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Google-Login](/de-de/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Notieren Sie die Clientidentität und die geheimen Werte, die vom Anbieter generiert wurden.

    <div class="dev-callout"><b>Sicherheitshinweis</b>
	<p>Der vom Anbieter generierte geheime Schlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.</p>
    </div>


1. Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den App-Bezeichner und die freigegebenen geheimen Werte ein, die Sie von Ihrem Identitätsanbieter erhalten haben, und klicken Sie auf **Speichern**.

   	![][13]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

Einschränken von BerechtigungenEinschränken von Berechtigungen für authentifizierte Benutzer
--------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

   	![][14]

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

   	![][15]

3.  Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit mobilen Diensten](/de-de/develop/mobile/tutorials/get-started-xamarin-android) erstellt haben.

4.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf den mobilen Dienst zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

Hinzufügen von AuthentifizierungHinzufügen von Authentifizierung zur App
------------------------------------------------------------------------

1.  Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu:

             private async Task Authenticate()
             {
                 try
                 {
                     user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                     CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                 }
                 catch (Exception ex)
                 {
                     CreateAndShowDialog(ex, "Authentication failed");
                 }
             }

    Diese neue Methode erledigt den Authentifizierungsprozess. Der Benutzer wird mithilfe einer Microsoft-Konto-Anmeldung authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    **Hinweis**

    Falls Sie einen anderen Identitätsanbieter als Microsoft verwenden, ändern Sie den an die **login**-Methode übergebenen Wert auf einen der folgenden Werte: *Facebook*, *Google* oder *Twitter*.

2.  Fügen Sie in der Methode **OnCreate** die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt definiert.

         await Authenticate();

    Dieser Aufruf startet den Authentifizierungsprozess und wartet asynchron auf eine Antwort.

3.  Verschieben Sie den verbleibenden Code nach `await Authenticate();` in der **OnCreate**-Methode in eine neue **CreateTable**-Methode mit dem folgenden Code:

             private async Task CreateTable()
             {
                 // Abrufen der zu verwendenden Tabelle im mobilen Dienst
                 todoTable = client.GetTable<TodoItem>();

                 textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

                 // Erstellen eines Adapters, um die Elemente an die Ansicht zu binden
                 adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
                 var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
                 listViewTodo.Adapter = adapter;

                 // Laden der Elemente aus dem mobilen Dienst
                 await RefreshItemsFromTableAsync();
             }

4.  Rufen Sie anschließend die neue **CreateTable**-Methode in **OnCreate** nach dem in Schritt 2 hinzugefügten **Authenticate**-Aufruf auf:

         await CreateTable();

5.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Abgeschlossenes Beispiel abrufen
--------------------------------

Laden Sie das [abgeschlossene Beispielprojekt](http://go.microsoft.com/fwlink/p/?LinkId=331328) herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

Nächste Schritte
----------------

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.



<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png