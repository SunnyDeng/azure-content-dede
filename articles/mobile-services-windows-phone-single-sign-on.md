<properties 
	pageTitle="Authentifizieren Ihrer App mit Live Connect (Windows Phone) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie das einmalige Anmelden mit Live Connect in Azure Mobile Services von einer Windows Phone-Anwendung verwenden." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# Authentifizieren Ihrer Windows Phone 8-App mit einmaliger Anmeldung von Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Windows Store C#</a><a href="/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

In diesem Thema erfahren Sie, wie Sie die einmalige Anmeldung von Live Connect verwenden, um Benutzer in Azure Mobile Services über eine Windows Phone 8-App zu authentifizieren.  In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe von Live Connect zum Schnellstartprojekt hinzu. Sobald die Authentifizierung durch Live Connect erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt und der Benutzer-ID-Wert wird angezeigt.  

>[AZURE.NOTE] Dieses Lernprogramm zeigt die Vorteile der Verwendung der einmaligen Anwendung mithilfe von Live Connect für Windows Phone-Apps. Dadurch können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Informationen zu einem allgemeineren Authentifizierungsverfahren, das verschiedene Authentifizierungsanbieter unterstützt, finden Sie im Thema <a href="mobile-services-windows-phone-get-started-users.md">Hinzufügen von Authentifizierung zur App</a>. 

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung der Live Connect-Authentifizierung behandelt:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Live SDK für Windows und Windows Phone]
+ Microsoft Visual Studio 2012 Express für Windows Phone
+ Sie müssen außerdem zunächst das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

<h2><a name="register"></a>Registrieren Ihrer App bei Live Connect</h2>

Damit Sie Benutzer authentifizieren können, müssen Sie Ihre App beim Live Connect Developer Center registrieren. Dann müssen Sie den geheimen Clientschlüssel registrieren, um Live Connect in Mobile Services zu integrieren.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

   	![][4]

2. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie den Wert der **Site-URL**.

   	![][5]

    Diesen Wert verwenden Sie, um die Umleitungsdomäne zu definieren.

3. Navigieren Sie im Live Connect Developer Center zur Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Eigene Apps</a>, und melden Sie sich ggf. mit Ihrem Microsoft-Konto an. 

4. Klicken Sie auf **Anwendung erstellen**, geben Sie einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

   	![][1] 

   	Damit wird die Anwendung bei Live Connect registriert.

5. Klicken Sie auf die Seite **App-Einstellungen**, dann auf **API-Einstellungen**, und notieren Sie sich die Werte von **Client-ID** und **Geheimer Clientschlüssel**. 

   	![][2]

 > [AZURE.NOTE] **Sicherheitshinweis** Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

6. Geben Sie in **Umleitungsdomäne** die URL des mobilen Diensts aus Schritt 2 ein, klicken Sie unter **Mobile Client-App** auf **Ja**, und klicken Sie dann auf **Speichern**.

7. Gehen Sie wieder zum Verwaltungsportal, klicken Sie auf die Registerkarte **Identität**, geben Sie den **Geheimen Clientschlüssel** aus Live Connect ein, und klicken Sie dann auf **Speichern**.

   	![][13]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit Live Connect.

<h2><a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer</h2>

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**. 

   	![][14]

2. Klicken Sie auf die Registerkarte **Berechtigungen**, legen Sie für alle Berechtigungen **Nur authentifizierte Benutzer** fest, und klicken Sie dann auf **Speichern**. So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist. Dies vereinfacht auch die Skripts im nächsten Lernprogramm, da diese keine anonymen Benutzer zulassen müssen.

   	![][15]

3. Öffnen Sie in Visual Studio 2012 Express für Windows Phone das Projekt, das Sie im Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App] erstellt haben. 

4. Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass eine Ausnahme mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird. 
   
   	Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer mit Live Connect zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

<h2><a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App</h2>

1. Laden Sie das [Live SDK für Windows und Windows Phone] herunter, und installieren Sie es.

2. Klicken Sie im Menü **Projekt** in Visual Studio auf **Verweis hinzufügen**, erweitern Sie dann **Assemblys**, klicken Sie auf **Erweiterungen**, aktivieren Sie **Microsoft.Live**, und klicken Sie dann auf **OK**. 

   	![][16]

  	So wird ein Verweis zum Live SDK zum Projekt hinzugefügt.

5. Öffnen Sie die Projektdatei "mainpage.xaml.cs", und fügen Sie folgende Ausdrücke ein:

        using Microsoft.Live;      

6. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:
	
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

7. Aktualisieren Sie die Zeichenfolge << INSERT CLIENT ID HERE >> aus dem vorherigen Schritt mit dem Client-ID-Wert, der beim Registrieren der App bei Live Connect generiert wurde.

    > [AZURE.NOTE] In einer Windows Phone 8-App wird eine Instanz der **LiveAuthClient**-Klasse erstellt, indem der Client-ID-Wert an den Klassenkonstruktor übergegeben wird. In einer [Windows Store-App](/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)wird dieselbe Klasse durch Übergeben des Umleitungsdomänen-URI instanziiert.

8. Löschen Sie die vorhandene **OnNavigatedTo**-Methodenüberschreibung, oder kommentieren Sie sie aus, und ersetzen Sie sie durch die folgende Methode, die das **Loaded**-Ereignis für die Seite verarbeitet. 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Diese Methode ruft die neue **Authenticate**-Methode auf. 

9. Ersetzen Sie den MainPage-Konstruktor durch den folgenden Code:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Dieser Konstruktor registriert zudem den Handler für das Ereignis Loaded.
		
9. Drücken Sie die Taste F5, um die App auszuführen, und melden Sie sich bei Live Connect mit Ihrem Microsoft-Konto an. 

   	Wenn Sie sich erfolgreich angemeldet haben, wird die App fehlerfrei ausgeführt, und Sie können Mobile Services abfragen und Daten aktualisieren.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung]. 

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png

[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[Eigene Apps]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows und Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-windows-phone-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-windows-phone-get-started-users.md
[Autorisieren von Benutzern mit Skripts]: mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=49-->