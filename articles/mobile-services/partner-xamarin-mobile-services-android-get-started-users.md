<properties 
	pageTitle="Erste Schritte mit der Authentifizierung (Xamarin.Android) - Mobile Services" 
	description="Erfahren Sie mehr über die Verwendung der Authentifizierung in Ihrer Azure Mobile Services-App für Xamarin.Android." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	manager="dwrede" 
	authors="lindydonna" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/14/2015" 
	ms.author="donnam"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

<p>Dieses Thema beschreibt die Authentifizierung von Benutzern in Azure Mobile Services aus Ihrer Xamarin.Android-App. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.</p>

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

Dieses Lernprogramm benötigt Xamarin.Android und Android SDK 4.2 oder eine höhere Version.

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] erstellt haben. 

4. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

	 Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf den mobilen Dienst zugreift und die _TodoItem_-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1. Fügen Sie der **TodoActivity**-Klasse die folgende Eigenschaft hinzu:

		private MobileServiceUser user;

2. Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu:

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

    Auf diese Weise wird eine neue Methode zur Verarbeitung des Authentifizierungsprozesses erstellt. Der Benutzer wird mithilfe einer Microsoft-Konto-Anmeldung authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    > [AZURE.NOTE]Falls Sie einen anderen Identitätsanbieter als Microsoft verwenden, ändern Sie den an die **login**-Methode übergebenen Wert auf einen der folgenden Werte: _Facebook_, _Google_, _Twitter_ oder _WindowsAzureActiveDirectory_.

3. Fügen Sie in der **onCreate**-Methode die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt instanziiert.

		await Authenticate();

	Dieser Aufruf startet den Authentifizierungsprozess und wartet asynchron auf eine Antwort.

4. Verschieben Sie den verbleibenden Code nach `await Authenticate();` in der **OnCreate**-Methode in eine neue **CreateTable**-Methode mit dem folgenden Code:

        private async Task CreateTable()
        {
            
            await InitLocalStoreAsync();

            // Get the Mobile Service Table instance to use
            toDoTable = client.GetTable<ToDoItem>();

            textNewToDo = FindViewById<EditText>(Resource.Id.textNewToDo);

            // Create an adapter to bind the items with the view
            adapter = new ToDoItemAdapter(this, Resource.Layout.Row_List_To_Do);
            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewToDo);
            listViewTodo.Adapter = adapter;

            // Load the items from the Mobile Service
            await RefreshItemsFromTableAsync();
        }

5. Rufen Sie anschließend die neue **CreateTable**-Methode in **OnCreate** nach dem in Schritt 2 hinzugefügten **Authenticate**-Aufruf auf:

		await CreateTable();


6. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

## Abgeschlossenes Beispiel abrufen
Laden Sie das [abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Autorisieren von Benutzern mit Skripts]: mobile-services-javascript-backend-service-side-authorization.md
[Azure Management Portal]: https://manage.windowsazure.com/
[abgeschlossene Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331328
 

<!---HONumber=July15_HO2-->