<properties pageTitle="Authentifizieren Ihrer Windows Store-App mit Live Connect" description="Erfahren Sie, wie Sie das einmalige Anmelden mit Live Connect in Azure Mobile Services von einer Windows Store-Anwendung verwenden." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>

# Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	

In diesem Thema erfahren Sie, wie Sie die einmalige Anmeldung von Live Connect verwenden, um Benutzer in Azure Mobile Services über eine Windows Store- oder Windows Phone 8.1 Store-App zu authentifizieren.  In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe von Live Connect zum Schnellstartprojekt hinzu. Sobald die Authentifizierung durch Live Connect erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt und der Benutzer-ID-Wert wird angezeigt.  

>[AZURE.NOTE]Dieses Lernprogramm zeigt die Vorteile der Verwendung der einmaligen Anwendung mithilfe von Live Connect für Windows Store-Apps. Dadurch können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Informationen zu einem allgemeineren Authentifizierungsverfahren, das verschiedene Authentifizierungsanbieter unterstützt, finden Sie im Thema <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/">Erste Schritte mit der Authentifizierung</a>. 

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung der Live Connect-Authentifizierung behandelt:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Live SDK für Windows]
+ Microsoft Visual Studio 2012 Express für Windows 8 RC oder eine höhere Version
+ Sie müssen außerdem zunächst das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

##<a name="register"></a>Registrieren der App für den Windows Store

Sie müssen Ihre App beim Windows Store registrieren, damit Sie Benutzer authentifizieren können. Dann müssen Sie den geheimen Clientschlüssel registrieren, um Live Connect in Mobile Services zu integrieren.

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen auf authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie im Lernprogramm <a href="/de-de/documentation/articles/mobile-services-windows-store-get-started">Erste Schritte mit Mobile Services</a> erstellt haben.</p></li> 
<li><p>Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1. Laden Sie das [Live SDK für Windows] herunter, und installieren Sie es.

2. Klicken Sie in Visual Studio im Menü **Projekt** auf **Verweis hinzufügen**, erweitern Sie **Windows**, klicken Sie auf **Erweiterungen**, aktivieren Sie **Live SDK**, und klicken Sie dann auf **OK**. 

  	![][16]

  	So wird ein Verweis zum Live SDK zum Projekt hinzugefügt.

5. Öffnen Sie die Projektdatei "MainPage.xaml.cs", und fügen Sie die folgende using-Anweisung ein:

        using Microsoft.Live;        

6. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
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

    Auf diese Weise wird eine Membervariable zum Speichern der aktuellen Live Connect-Sitzung erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses. Dieser Code erzwingt wenn möglich eine Abmeldung, damit der Benutzer jedes Mal zur Eingabe der Anmeldeinformationen aufgefordert wird, wenn die Anwendung ausgeführt wird. Dies erleichtert das Testen der Anwendung mit verschiedenen Microsoft-Konten, um sicherzustellen, dass die Authentifizierung ordnungsgemäß funktioniert. Dieser Mechanismus funktioniert nur, wenn der angemeldete Benutzer nicht über ein verbundenes Microsoft-Konto verfügt. 

	>[AZURE.NOTE]Sie sollten nicht bei jeder Ausführung der App Live Connection-Authentifizierungstoken oder Mobile Services-Autorisierungstoken anfordern. Diese Methode ist ineffizient, und zudem können nutzungsbedingte Probleme auftreten, wenn viele Kunden die App gleichzeitig starten möchten. Ein besserer Ansatz ist es daher, die Token zwischenzuspeichern und zunächst zu versuchen, die zwischengespeicherten Mobile Services-Token zu verwenden, bevor Sie **LoginWithMicrosoftAccountAsync** aufrufen. Ein Beispiel zum Zwischenspeichern dieses Tokens finden Sie unter [Erste Schritte mit der Authentifizierung](/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens).
	

7. Aktualisieren Sie die Zeichenfolge << INSERT REDIRECT DOMAIN HERE >> aus dem vorherigen Schritt mit der Umleitungsdomäne, die bei der Konfiguration der App in Live Connect angegeben wurde, im Format **https://_service-name_.azure-mobile.net/**..

    > [AZURE.NOTE] In einer Windows Store-App wird eine Instanz der <strong>LiveAuthClient</strong>-Klasse erstellt, indem der URI-Wert der Umleitungsdomäne an den Klassenkonstruktor weitergegeben wird. In einer [Windows Phone 8-App](/de-de/develop/mobile/tutorials/single-sign-on-wp8/) wird die gleiche Klasse durch Übergabe der Client-ID instantiiert.

8. Ersetzen Sie den vorhandenen **OnNavigatedTo**-Ereignishandler durch den Handler, der die neue **Authenticate**-Methode aufruft:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
9. Drücken Sie die Taste F5, um die App auszuführen, und melden Sie sich bei Live Connect mit Ihrem Microsoft-Konto an. 

   Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm, [Autorisieren von Benutzern mit Skripts], verwenden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert, um die von Mobile Services zurückgegebenen Daten zu filtern. Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung]. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz].

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png

<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Hinzufügen von Mobile Services zu einer vorhandenen App]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
