<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect
==================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

In diesem Thema erfahren Sie, wie Sie die einmalige Anmeldung von Live Connect verwenden, um Benutzer in Azure Mobile Services über eine Windows Store-App zu authentifizieren. In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe von Live Connect zum Schnellstartprojekt hinzu. Sobald die Authentifizierung durch Live Connect erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt und der Benutzer-ID-Wert wird angezeigt.
**Hinweis**

Dieses Lernprogramm zeigt die Vorteile der Verwendung der einmaligen Anwendung mithilfe von Live Connect für Windows Store-Apps. So können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem mobilen Dienst authentifizieren. Informationen zu einer generalisierteren Authentifizierung, die verschiedene Authentifizierungsanbieter unterstützt, finden Sie unter [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung der Live Connect-Authentifizierung behandelt:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services](#register)
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer](#permissions)
3.  [Hinzufügen von Authentifizierung zur App](#add-authentication)

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Live SDK für Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express für Windows 8 RC oder eine höhere Version

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started) abschließen.

Registrieren Ihrer AppRegistrieren Ihrer App für den Windows Store
------------------------------------------------------------------

Sie müssen Ihre App beim Windows Store registrieren, damit Sie Benutzer authentifizieren können. Dann müssen Sie den Clientschlüssel registrieren, um Live Connect in Mobile Services zu integrieren.

1.  Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden](http://go.microsoft.com/fwlink/p/?LinkID=266582), melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **Anwendungsname**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png)

2.  Geben Sie einen Namen für Ihre App unter **App-Name** ein, klicken Sie auf **App-Namen reservieren** und dann auf **Speichern**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png)

     Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre App erstellt.

3.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started) abgeschlossen haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **App dem Store zuordnen**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png) 

   Auf diese Weise wird der Assistent zum **Zuordnen Ihrer App zum Windows Store** angezeigt.

1.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

2.  Wählen Sie die App aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png)

   Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

3.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png)

4.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich den Wert der **Site-URL**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png)

    Diesen Wert verwenden Sie, um die Umleitungsdomäne zu definieren.

5.  Navigieren Sie zur Seite [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) im Live Connect Developer Center, und klicken Sie in der Liste **My applications** auf Ihre App.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png) 

6.  Klicken Sie auf **Einstellungen bearbeiten**, dann auf **API-Einstellungen**, und notieren Sie sich dann die Werte für **Client-ID** und **Geheimer Clientschlüssel**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Sicherheitshinweis</b>
    <p>Der Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.</p>
    </div>

1.  Geben Sie unter **Umleitungsdomäne** die URL Ihres mobilen Dienstes aus Schritt 8 ein, und klicken Sie auf **Speichern**.

2.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identität**, geben Sie den Wert von **Geheimer Clientschlüssel** ein, den Sie von Windows Store erhalten haben, und klicken Sie auf **Speichern**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png)

Sowohl Ihr mobiler Dienst als auch Ihre App sind nun konfiguriert und funktionieren mit Live Connect.

Einschränken von BerechtigungenEinschränken von Berechtigungen für authentifizierte Benutzer
--------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png)

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png)

3.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started) abgeschlossen haben.

4.  Drücken Sie **F5**, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass eine Ausnahme mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer mit Live Connect zu authentifizieren, bevor diese Ressourcen vom mobilen Dienst anfordern.

Hinzufügen von AuthentifizierungHinzufügen von Authentifizierung zur App
------------------------------------------------------------------------

1.  Laden Sie [Live SDK für Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253) herunter, und installieren Sie es.

2.  Klicken Sie im Menü **Projekt** in Visual Studio auf **Verweis hinzufügen**, erweitern Sie dann **Windows**, klicken Sie auf **Erweiterungen**, markieren Sie **Live SDK**, und klicken Sie dann auf **OK**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png) 
    
    So wird ein Verweis zum Live SDK zum Projekt hinzugefügt.

1.  Öffnen Sie die Projektdatei "MainPage.xaml.cs", und fügen Sie die folgende **using**-Anweisung ein:

         using Microsoft.Live;        

2.  Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

         private LiveConnectSession session;
         private async System.Threading.Tasks.Task Authenticate()
         {
             LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

             while (session == null)
             {
                 // Force a logout to make it easier to test with multiple Microsoft Accounts
                 if (liveIdClient.CanLogout)
                     liveIdClient.Logout();

                 LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                 if (result.Status == LiveConnectSessionStatus.Connected)
                 {
                     session = result.Session;
                     LiveConnectClient client = new LiveConnectClient(result.Session);
                     LiveOperationResult meResult = await client.GetAsync("me");
                     MobileServiceUser loginResult = await App.MobileService
                         .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                     string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                     var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                     var dialog = new MessageDialog(message, title);
                     dialog.Commands.Add(new UICommand("OK"));
                     await dialog.ShowAsync();
                 }
                 else
                 {
                     session = null;
                     var dialog = new MessageDialog("You must log in.", "Login Required");
                     dialog.Commands.Add(new UICommand("OK"));
                     await dialog.ShowAsync();
                 }
             }
          }

    Auf diese Weise wird eine Membervariable zum Speichern der aktuellen Live Connect-Sitzung erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses.

    **Hinweis**

    Dieser Code erzwingt wenn möglich eine Abmeldung, damit der Benutzer jedes Mal zur Eingabe der Anmeldeinformationen aufgefordert wird, wenn die Anwendung ausgeführt wird. Dies erleichtert das Testen der Anwendung mit verschiedenen Microsoft-Konten, um sicherzustellen, dass die Authentifizierung ordnungsgemäß funktioniert. Dieser Mechanismus funktioniert nur, wenn der angemeldete Benutzer nicht über ein verbundenes Microsoft-Konto verfügt.

3.  Aktualisieren Sie die Zeichenfolge *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* aus dem vorherigen Schritt mit der Umleitungsdomäne, die bei der Einrichtung der App in Live Connect festgelegt wurde, im Format **https://*service-name*.azure-mobile.net/**.

    **Hinweis**

    In einer Windows Store-App wird eine Instanz der Klasse **LiveAuthClient** erstellt, indem der URI-Wert der Umleitungsdomäne an den Klassenkonstruktor weitergegeben wird. In einer [Windows Phone 8-App](/en-us/develop/mobile/tutorials/single-sign-on-wp8/) wird dieselbe Klasse durch Weitergabe der Client-ID initiiert.

4.  Ersetzen Sie den vorhandenen Ereignishandler **OnNavigatedTo** mit dem Handler, der die neue Methode **Authentifizieren** aufruft:

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

5.  Drücken Sie die Taste **F5**, um die App auszuführen und sich mit Ihrem Microsoft-Konto bei Live Connect anzumelden.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Nächste Schritte
----------------

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet). Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz](/en-us/develop/mobile/how-to-guides/work-with-net-client-library).

