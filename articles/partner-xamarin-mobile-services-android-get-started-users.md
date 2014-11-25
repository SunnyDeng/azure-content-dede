<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Erste Schritte bei der Authentifizierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer Xamarin.Android-App. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

Dieses Lernprogramm benötigt Xamarin.Android und Android SDK 4.2 oder eine höhere Version.

## <a name="register"></a><span class="short-header">Registrieren Ihrer App</span>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">Einschränken von Berechtigungen</span>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit mobilen Diensten][Erste Schritte mit Mobile Services] erstellt haben.

2.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a><span class="short-header">Hinzufügen von Authentifizierung</span>Hinzufügen von Authentifizierung zur App

1.  Fügen Sie der **TodoActivity**-Klasse die folgende Eigenschaft hinzu:

            private MobileServiceUser user;

2.  Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu:

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

    <div class="dev-callout"><b>Hinweis</b>
<p>Falls Sie einen anderen Identit&auml;tsanbieter als Microsoft verwenden, &auml;ndern Sie den an die <strong>login</strong>-Methode &uuml;bergebenen Wert auf einen der folgenden Werte: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> oder <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Fügen Sie in der **OnCreate**-Methode die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt instanziiert.

        await Authenticate();

    Dieser Aufruf startet den Authentifizierungsprozess und wartet asynchron auf eine Antwort.

4.  Verschieben Sie den verbleibenden Code nach `await Authenticate();` in der **OnCreate**-Methode in eine neue **CreateTable**-Methode mit dem folgenden Code:

            private async Task CreateTable()
            {
                // Get the Mobile Service Table instance to use
                todoTable = client.GetTable<TodoItem>();

                textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

                // Create an adapter to bind the items with the view
                adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
                var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
                listViewTodo.Adapter = adapter;

                // Load the items from the Mobile Service
                await RefreshItemsFromTableAsync();
            }

5.  Rufen Sie anschließend die neue **CreateTable**-Methode in **OnCreate** nach dem in Schritt 2 hinzugefügten **Authenticate**-Aufruf auf:

        await CreateTable();

6.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

## Abgeschlossenes Beispiel abrufen

Laden Sie das [abgeschlossene Beispielprojekt][abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.



  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android
  [abgeschlossene Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
