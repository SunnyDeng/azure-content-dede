<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="mobile-services" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>

In diesem Thema erfahren Sie, wie Sie die einmalige Anmeldung von Live Connect verwenden, um Benutzer in Azure Mobile Services über eine Windows Store- oder Windows Phone 8.1 Store-App zu authentifizieren. In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe von Live Connect zum Schnellstartprojekt hinzu. Sobald die Authentifizierung durch Live Connect erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt und der Benutzer-ID-Wert wird angezeigt.

> [WACOM.NOTE]Dieses Lernprogramm zeigt die Vorteile der Verwendung des einmaligen Anmeldens mithilfe von Live Connect für Windows Store-Apps. Dadurch können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Informationen zu einer allgemeineren Authentifizierung mit verschiedenen Authentifizierungsanbietern finden Sie unter [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung der Live Connect-Authentifizierung behandelt:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Live SDK für Windows][Live SDK für Windows]
-   Microsoft Visual Studio 2012 Express für Windows 8 RC oder eine höhere Version

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

> [WACOM.NOTE] In diesem Lernprogramm wird ein mobiler JavaScript-Back-End-Dienst verwendet. Die clientverwaltete Authentifizierung unter Verwendung von Live Connect oder anderer Authentifizierungsclients wird in einem mobilen .NET-Back-End-Dienst noch nicht unterstützt.

## <a name="register"></a>Registrieren der App für den Windows Store

Sie müssen Ihre App beim Windows Store registrieren, damit Sie Benutzer authentifizieren können. Dann müssen Sie den geheimen Clientschlüssel registrieren, um Live Connect in Mobile Services zu integrieren.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Öffnen Sie im Visual Studio 2012 Express für Windows 8 das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services][1] erstellt haben.

2.  Drücken Sie F5, um diese Schnellstart-basierte App auszuführen. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1.  Laden Sie das [Live SDK für Windows][Live SDK für Windows] herunter, und installieren Sie es.

2.  Klicken Sie im Menü **Project** in Visual Studio auf **Add Reference**, erweitern Sie **Windows**, klicken Sie auf **Extensions**, markieren Sie **Live SDK** und klicken Sie auf **OK**.

    ![][0]

    So wird ein Verweis zum Live SDK zum Projekt hinzugefügt.

3.  Öffnen Sie die Projektdatei "MainPage.xaml.cs", und fügen Sie die folgende **using**-Anweisung ein:

        using Microsoft.Live;        

4.  Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

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

    > [WACOM.NOTE]Sie sollten nicht bei jeder Ausführung der App Live Connection-Authentifizierungstoken oder Mobile Services-Autorisierungstoken anfordern. Diese Methode ist ineffizient, und zudem können nutzungsbedingte Probleme auftreten, wenn viele Kunden die App gleichzeitig starten möchten. Ein besserer Ansatz ist es daher, die Token zwischenzuspeichern und zunächst zu versuchen, die zwischengespeicherten Mobile Services-Token zu verwenden, bevor Sie **LoginWithMicrosoftAccountAsync** aufrufen. Ein Beispiel zum Zwischenspeichern dieses Tokens finden Sie unter [Erste Schritte mit der Authentifizierung][2].

5.  Aktualisieren Sie die Zeichenfolge *\<\< INSERT REDIRECT DOMAIN HERE \>\>* aus dem vorherigen Schritt durch die Umleitungsdomäne, die bei der Konfiguration der App in Live Connect festgelegt wurde, im Format **<https://_service-name_.azure-mobile.net/>**.

    <div class="dev-callout"><b>Hinweis</b>
<p>In einer Windows Store-App wird eine Instanz der Klasse <strong>LiveAuthClient</strong> erstellt, indem der URI-Wert der Umleitungsdom&auml;ne an den Klassenkonstruktor weitergegeben wird. In einer <a href="/de-de/develop/mobile/tutorials/single-sign-on-wp8/">Windows Phone&nbsp;8-App</a> wird dieselbe Klasse durch Weitergabe der Client-ID initiiert.</p>
</div>

6.  Ersetzen Sie den vorhandenen Ereignishandler **OnNavigatedTo** mit dem Handler, der die neue Methode **Authentifizieren** aufruft:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }

7.  Drücken Sie die Taste F5, um die App auszuführen, und melden Sie sich bei Live Connect mit Ihrem Microsoft-Konto an.

Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung][3]. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz][Mobile Services .NET – Erstellen einer konzeptionellen Referenz].



  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
  [1]: /de-de/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png
  [2]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [3]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [Mobile Services .NET – Erstellen einer konzeptionellen Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
