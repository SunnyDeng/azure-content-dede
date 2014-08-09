<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Erste Schritte bei der Authentifizierung in Mobile Services
===========================================================

[Windows Store C#](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

[Lernprogramm ansehen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) [Video abspielen](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:42

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-android) abschließen.

Dieses Lernprogramm benötigt Eclipse und Android 4.2 oder eine neuere Version.

Registrieren Ihrer AppRegistrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services
--------------------------------------------------------------------------------------------------------

Sie müssen Ihre App bei einem Identitätsanbieter registrieren, damit Sie Benutzer authentifizieren können. Sie müssen dann den vom Anbieter generierten geheimen Clientschlüssel bei Mobile Services registrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

   	![](./media/mobile-services-android-get-started-users/mobile-services-selection.png)

2.  Klicken Sie auf die Registerkarte **Dashboard** und notieren Sie die **Site-URL**.

   	![](./media/mobile-services-android-get-started-users/mobile-service-uri.png)

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

   	![](./media/mobile-services-android-get-started-users/mobile-identity-tab.png)

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit dem ausgewählten Authentifizierungsanbieter.

Einschränken von BerechtigungenEinschränken von Berechtigungen für authentifizierte Benutzer
--------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png)

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png)

3.  Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit mobilen Diensten](/de-de/develop/mobile/tutorials/get-started-android) erstellt haben.

4.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf den mobilen Dienst zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

Hinzufügen von AuthentifizierungHinzufügen von Authentifizierung zur App
------------------------------------------------------------------------

1.  Öffnen Sie die Datei ToDoActivity.java im Paket-Explorer in Eclipse und fügen Sie die folgenden Import-Anweisungen hinzu.

         import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
         import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
         import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu:

         private void authenticate() {
            
             // Anmelden mit dem Google-Anbieter.
             mClient.login(MobileServiceAuthenticationProvider.Google,
                     new UserAuthenticationCallback() {

                         @Override
                         public void onCompleted(MobileServiceUser user,
                                 Exception exception, ServiceFilterResponse response) {

                             if (exception == null) {
                                 createAndShowDialog(String.format(
                                                 "You are now logged in - %1$2s",
                                                 user.getUserId()), "Success");
                                 createTable();
                             } else {
                                 createAndShowDialog("You must log in. Login Required", "Error");
                             }
                         }
                     });
         }

    Diese neue Methode erledigt den Authentifizierungsprozess. Der Benutzer wird mithilfe einer Google-Anmeldung authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    **Hinweis**

    Falls Sie einen anderen Identitätsanbieter als Google verwenden, ändern Sie den an die **login**-Methode übergebenen Wert auf einen der folgenden Werte: *MicrosoftAccount*, *Facebook* oder *Twitter*.

3.  Fügen Sie in der Methode **onCreate** die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt definiert.

         authenticate();

    Dieser Aufruf startet den Authentifizierungsprozess.

4.  Verschieben Sie den verbleibenden Code nach `authenticate();` in der **onCreate**-Methode in eine neue **createTable**-Methode mit dem folgenden Code:

         private void createTable() {

             // Abrufen der zu verwendenden Tabelle im mobilen Dienst
             mToDoTable = mClient.getTable(ToDoItem.class);

             mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

             // Erstellen eines Adapters, um die Elemente an die Ansicht zu binden
             mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
             ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
             listViewToDo.setAdapter(mAdapter);

             // Laden der Elemente aus dem mobilen Dienst
             refreshItemsFromTable();
         }

5.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Nächste Schritte
----------------

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts](/de-de/develop/mobile/tutorials/authorize-users-in-scripts-android) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

