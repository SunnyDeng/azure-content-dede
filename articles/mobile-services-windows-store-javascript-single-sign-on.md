<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect
==================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer Windows Store-App. In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe von Live Connect zum Schnellstartprojekt hinzu. Sobald die Authentifizierung durch Live Connect erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt und der Benutzer-ID-Wert wird angezeigt.

**Hinweis**

Dieses Lernprogramm zeigt die Vorteile der Verwendung der einmaligen Anwendung mithilfe von Live Connect für Windows Store-Apps. Dadurch können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Informationen zu einer allgemeineren Authentifizierung mit verschiedenen Authentifizierungsanbietern finden Sie unter [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js/).

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

Sie müssen Ihre App beim Windows Store registrieren, damit Sie Benutzer authentifizieren können. Dann müssen Sie den geheimen Clientschlüssel registrieren, um Live Connect in Mobile Services zu integrieren.

1.  Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Submit an app page](http://go.microsoft.com/fwlink/p/?LinkID=266582), melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App name**.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png)

2.  Geben Sie einen Namen für die App unter **App Name** ein, klicken Sie auf **Reserve app name** und dann auf **Save**.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png)

      Daraufhin wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started) abgeschlossen haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Associate App with the Store...**.

    ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png)


    Daraufhin wird der Assistent **Associate Your App with the Windows Store** angezeigt.

1.  Klicken Sie im Assistenten auf **Sign in**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

2.  Wählen Sie die App aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Next** und dann auf **Associate**.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png)

      Daraufhin werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

3.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png)

4.  Klicken Sie auf die Registerkarte **Dashboard** und notieren Sie die **Site-URL**.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png)

    Diesen Wert verwenden Sie, um die Umleitungsdomäne zu definieren.

5.  Navigieren Sie zur Seite [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) im Live Connect Developer Center, und klicken Sie in der Liste **My applications** auf Ihre App.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png) 

6.  Klicken Sie auf **Edit Settings**, dann auf **API Settings** und notieren Sie sich dann die Werte für **Client ID** und **Client secret**.

    ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Security Note</b>
    <p>Der Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.</p>
    </div>

1.  Geben Sie unter **Redirect domain** die URL Ihres mobilen Dienstes aus Schritt 8 ein, und klicken Sie auf **Save**.

2.  Klicken Sie dann im Verwaltungsportal auf die Registerkarte **Identity**, geben Sie den Wert für **Client secret** ein, den Sie von Windows Store erhalten haben, und klicken Sie auf **Save**.

    ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png)

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit Live Connect.

Einschränken von BerechtigungenEinschränken von Berechtigungen für authentifizierte Benutzer
--------------------------------------------------------------------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png)

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

      ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png)

3.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started) abgeschlossen haben.

4.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass eine Ausnahme mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird.

      Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer mit Live Connect zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

Hinzufügen von AuthentifizierungHinzufügen von Authentifizierung zur App
------------------------------------------------------------------------

1.  Laden Sie das [Live SDK für Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253) herunter, und installieren Sie es.

2.  Klicken Sie im Menü **Project** in Visual Studio auf **Add Reference**, erweitern Sie **Windows**, klicken Sie auf **Extensions**, markieren Sie **Live SDK** und klicken Sie auf **OK**.

    ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png) 


    So wird ein Verweis zum Live SDK zum Projekt hinzugefügt.

1.  Öffnen Sie die Projektdatei default.html und fügen Sie dem &lt;script\>-Element unterhalb des &lt;head\>-Element Folgendes hinzu:

         <script src="///LiveSDKHTML/js/wl.js"></script>

      Dieser Code aktiviert Microsoft IntelliSense in der default.html-Datei.

2.  Öffnen Sie die Projektdatei default.js und fügen Sie den folgenden Kommentar am Anfang der Datei hinzu.

         /// <reference path="///LiveSDKHTML/js/wl.js" />

      Dieser Code aktiviert Microsoft IntelliSense in der default.js-Datei.

3.  Ersetzen Sie in der Methodenüberladung **app.OnActivated** den Aufruf der **refreshTodoItems**-Methode durch den folgenden Code:

         var session = null;   

         var logout = function () {
             return new WinJS.Promise(function (complete) {
                 WL.getLoginStatus().then(function () {
                     if (WL.canLogout()) {
                         WL.logout(complete);                            
                     }
                     else {
                         complete();
                     }
                 });
             });
         };                  

         var login = function () {
             return new WinJS.Promise(function (complete) {                    
                 WL.login({ scope: "wl.basic"}).then(function (result) {
                     session = result.session;

                     WinJS.Promise.join([
                         WL.api({ path: "me", method: "GET" }),
                         client.login(result.session.authentication_token)
                     ]).done(function (results) {
                         var profile = results[0];
                         var mobileServicesUser = results[1];
                         refreshTodoItems();
                         var title = "Welcome " + profile.first_name + "!";
                         var message = "You are now logged in as: " + mobileServicesUser.userId;
                         var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                         dialog.showAsync().done(complete);                                
                     });                       
                 }, function (error) {                        
                     session = null;
                     var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                     dialog.showAsync().done(complete);                        
                 });
             });
         }

         var authenticate = function () {
             // Abmeldung erzwingen, um Tests mit mehreren Microsoft-Konten zu erleichtern
             logout().then(login).then(function () {
                 if (session === null) {
                     // Authentifizierung fehlgeschlagen, erneut versuchen.
                     authenticate();
                 }
             });
         }

         WL.init({
             redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
         });           
                
         authenticate();

    Dieser Code initialisiert den Live Connect-Client, erzwingt eine Abmeldung, sendet eine neue Anmelde-Anforderung an Live Connect, sendet das zurückgegebene Authentifizierungs-Token an Mobile Services und zeigt anschließend Informationen über den angemeldeten Benutzer an.

    **Hinweis**

    Dieser Code erzwingt wenn möglich eine Abmeldung, damit der Benutzer jedes Mal zur Eingabe der Anmeldeinformationen aufgefordert wird, wenn die Anwendung ausgeführt wird. Dies erleichtert das Testen der Anwendung mit verschiedenen Microsoft-Konten, um sicherzustellen, dass die Authentifizierung ordnungsgemäß funktioniert. Dieser Mechanismus funktioniert nur, wenn der angemeldete Benutzer nicht über ein verbundenes Microsoft-Konto verfügt.

4.  Aktualisieren Sie die Zeichenfolge *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* aus dem vorherigen Schritt durch die Umleitungsdomäne, die bei der Einrichtung der App in Live Connect festgelegt wurde, im Format **https://*service-name*.azure-mobile.net/**.

5.  Drücken Sie die Taste F5, um die App auszuführen, und melden Sie sich bei Live Connect mit Ihrem Microsoft-Konto an.

      Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Nächste Schritte
----------------

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js) werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js).

