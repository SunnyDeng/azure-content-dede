<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Authentifizieren Ihrer Windows Phone 8-App mit einmaliger Anmeldung von Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

In diesem Thema erfahren Sie, wie Sie die einmalige Anmeldung von Live Connect verwenden, um Benutzer in Azure Mobile Services über eine Windows Phone 8-App zu authentifizieren. In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe von Live Connect zum Schnellstartprojekt hinzu. Sobald die Authentifizierung durch Live Connect erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt und der Benutzer-ID-Wert wird angezeigt.

<div class="dev-callout"><b>Hinweis</b>
    <p>Dieses Lernprogramm zeigt die Vorteile der Verwendung der einmaligen Anwendung mithilfe von Live Connect f&uuml;r Windows Phone-Apps. Dadurch k&ouml;nnen Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Informationen zu einer allgemeineren Authentifizierung mit verschiedenen Authentifizierungsanbietern finden Sie unter <a href="/de-de/develop/mobile/tutorials/get-started-with-users-wp8/">Erste Schritte mit der Authentifizierung</a>. </p>
</div>

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung der Live Connect-Authentifizierung behandelt:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Live SDK für Windows und Windows Phone][Live SDK für Windows und Windows Phone]
-   Microsoft Visual Studio 2012 Express für Windows Phone

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <a name="register"></a><span class="short-header">Registrieren Ihrer App</span>Registrieren Ihrer App bei Live Connect

Damit Sie Benutzer authentifizieren können, müssen Sie Ihre App beim Live Connect Developer Center registrieren. Dann müssen Sie den geheimen Clientschlüssel registrieren, um Live Connect in Mobile Services zu integrieren.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie die **Site-URL**.

    ![][1]

    Diesen Wert verwenden Sie, um die Umleitungsdomäne zu definieren.

3.  Navigieren Sie im Live Connect Developer Center zur Seite [Eigene Apps][Eigene Apps], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

4.  Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

    ![][2]

    Damit wird die Anwendung bei Live Connect registriert.

5.  Klicken Sie auf die Seite **App-Einstellungen**, dann auf **API-Einstellungen**, und notieren Sie sich die Werte von **Client-ID** und **Geheimer Clientschlüssel**.

    ![][3]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der geheime Clientschl&uuml;ssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschl&uuml;ssel mit niemandem, und geben Sie ihn nicht &uuml;ber Ihre App frei.</p>
</div>

6.  Geben Sie in **Umleitungsdomäne** die URL des mobilen Diensts aus Schritt 2 ein, klicken Sie auf **Ja** unter **Mobile Client-App**, und klicken Sie dann auf **Speichern**.

7.  Gehen Sie wieder zum Verwaltungsportal, klicken Sie auf die Registerkarte **Identität**, geben Sie den **Geheimen Clientschlüssel** aus Live Connect ein, und klicken Sie dann auf **Speichern**.

    ![][4]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit Live Connect.

## <a name="permissions"></a><span class="short-header">Einschränken von Berechtigungen</span>Einschränken von Berechtigungen für authentifizierte Benutzer

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

    ![][5]

2.  Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Only authenticated users** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

    ![][6]

3.  Öffnen Sie in Visual Studio 2012 Express für Windows Phone das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abgeschlossen haben.

4.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass eine Ausnahme mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift, die *TodoItem*-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer mit Live Connect zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a><span class="short-header">Hinzufügen von Authentifizierung</span>Hinzufügen von Authentifizierung zur App

1.  Laden Sie [Live SDK für Windows und Windows Phone][Live SDK für Windows und Windows Phone] herunter, und installieren Sie es.

2.  Klicken Sie im Menü **Projekt** in Visual Studio auf **Verweis hinzufügen**, erweitern Sie dann **Assemblys**, klicken Sie auf **Erweiterungen**, aktivieren Sie **Microsoft.Live**, und klicken Sie dann auf **OK**.

    ![][7]

    So wird ein Verweis zum Live SDK zum Projekt hinzugefügt.

3.  Öffnen Sie die Projektdatei "mainpage.xaml.cs", und fügen Sie folgende Ausdrücke ein:

        using Microsoft.Live;      

4.  Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

            while (session == null)
            {
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
                    MessageBox.Show(message, title, MessageBoxButton.OK);                    
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);                    
                }
            }
         }

    Auf diese Weise wird eine Membervariable zum Speichern der aktuellen Live Connect-Sitzung erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses.

5.  Aktualisieren Sie die Zeichenfolge *\<\< INSERT CLIENT ID HERE \>\>* aus dem vorherigen Schritt mit dem Client-ID-Wert, der beim Registrieren der App bei Live Connect generiert wurde.

    <div class="dev-callout"><b>Hinweis</b>
<p>In einer Windows Phone 8-App wird eine Instanz der Klasse <strong>LiveAuthClient</strong> erstellt, indem der Client-ID-Wert an den Klassenkonstruktor weitergegeben wird. In einer <a href="/de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">Windows Store-App</a> wird dieselbe Klasse instanziiert, indem der Umleitungsdom&auml;nen-URI weitergegeben wird.</p>
</div>

6.  Löschen Sie die vorhandene Methodenüberschreibung **OnNavigatedTo** oder kommentieren diese aus, und ersetzen Sie diese durch folgende Methode, die das Ereignis **Loaded** für die Seite behandelt.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    Diese Methode ruft die neue **Authenticate**-Methode auf.

7.  Ersetzen Sie den MainPage-Konstruktor durch den folgenden Code:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    Dieser Konstruktor registriert zudem den Handler für das Ereignis Loaded.

8.  Drücken Sie die Taste F5, um die App auszuführen, und melden Sie sich bei Live Connect mit Ihrem Microsoft-Konto an.

    Wenn Sie sich erfolgreich angemeldet haben, wird die App fehlerfrei ausgeführt, und Sie können Mobile Services abfragen und Daten aktualisieren.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung][8].



  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8/
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Live SDK für Windows und Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-wp8
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [Eigene Apps]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
