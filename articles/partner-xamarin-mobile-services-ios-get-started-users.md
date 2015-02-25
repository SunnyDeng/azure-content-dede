<properties 
	pageTitle="Erste Schritte mit der Authentifizierung (Xamarin.iOS) - Mobile Services" 
	description="Erfahren Sie mehr über die Verwendung der Authentifizierung in Ihrer Azure Mobile Services-App für Xamarin.iOS." 
	documentationCenter="xamarin" 
	services="mobile-services" 
	manager="dwrede" 
	authors="lindydonna" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="donnam"/>

# Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren.  In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.  

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

Für dieses Lernprogramm sind [Xamarin.iOS], XCode 5.0 und iOS 5.0 oder eine höhere Version erforderlich.

<h2><a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services</h2>

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer</h2>


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] erstellt haben. 

4. Klicken Sie auf **Ausführen**, das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird. 
   
   	Dies liegt daran, dass die App versucht als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

<h2><a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App</h2>

1. Öffnen Sie die Projektdatei **TodoService**, und fügen Sie folgende Variablen hinzu:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Fügen Sie dann eine neue Methode namens **Authenticate** zu **TodoService** hinzu, die folgendermaßen definiert ist:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Falls Sie einen anderen Identitätsanbieter als Microsoft verwenden, ändern Sie den an **LoginAsync** übergebenen Wert in einen der folgenden Werte: _Facebook_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Verschieben Sie Anforderung für die **TodoItem**-Tabelle vom **TodoService**-Konstruktor in eine neue Methode namens **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }
	
4. Erstellen Sie eine neue asynchrone öffentliche Methode namens **LoginAndGetData**, die folgendermaßen definiert ist:

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. Setzen Sie im **TodoListViewController** die **ViewDidAppear**-Methode außer Kraft. Definieren Sie sie dann wie unten angegeben. Dadurch wird der Benutzer angemeldet, sofern der **TodoService** noch kein Handle für den Benutzer aufweist:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 
                
            RefreshAsync();
        }
6. Entfernen Sie den ursprünglichen Aufruf von **RefreshAsync** aus **TodoListViewController.ViewDidLoad**.
		
7. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

## Abgeschlossenes Beispiel abrufen
Laden Sie das [abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren. 

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm, [Autorisieren von Benutzern mit Skripts], werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. 

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[abgeschlossene Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331328




<!--HONumber=42-->
