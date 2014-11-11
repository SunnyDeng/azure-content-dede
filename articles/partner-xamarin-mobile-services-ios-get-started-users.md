<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Erste Schritte bei der Authentifizierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1.  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2.  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3.  [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

Für den Abschluss dieses Lernprogramms ist [Xamarin.iOS], XCode 5.0 oder iOS 5.0 oder neuer erforderlich.

## <a name="register"></a><span class="short-header">Registrieren Ihrer App</span>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">Einschränken von Berechtigungen</span>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] erstellt haben.

2.  Klicken Sie auf **Ausführen**, das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

    Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die *TodoItem*-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a><span class="short-header">Hinzufügen von Authentifizierung</span>Hinzufügen von Authentifizierung zur App

1.  Öffnen Sie die Projektdatei **TodoService**, und fügen Sie folgende Variablen hinzu:

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  Fügen Sie dann eine neue Methode namens **Authentifizieren** zu **TodoService** hinzu, die folgendermaßen definiert ist:

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

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn Sie einen anderen Identit&auml;tsanbieter als Microsoft Account verwenden, &auml;ndern Sie den oben an <strong>LoginAsync</strong> &uuml;bergebenen Wert in Folgendes: <i>Facebook</i>, <i>Twitter</i>, <i>Google</i> oder <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Verschieben Sie Anforderung für die **TodoItem**-Tabelle vom **TodoService**-Konstruktor in eine neue Methode namens **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4.  Erstellen Sie eine neue asynchrone öffentliche Methode namens **LoginAndGetData**, die folgendermaßen definiert ist:

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5.  Setzen Sie im **TodoListViewController** die **ViewDidAppear**-Methode außer Kraft. Definieren Sie sie dann wie unten angegeben. Dadurch wird der Benutzer angemeldet, sofern der **TodoService** noch keinen Handle für den Benutzer hat:

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

6.  Entfernen Sie den ursprünglichen Aufruf von **RefreshAsync** aus **TodoListViewController.ViewDidLoad**.

7.  Klicken Sie auf die Schaltfläche **Run**, um das Projekt zu erstellen, starten Sie die App im iPhone-Emulator, und melden Sie sich dann mit dem ausgewählten Identitätsanbieter an.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

## Abgeschlossenes Beispiel abrufen

Laden Sie das [abgeschlossene Beispielprojekt][abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. TODO:: update completed example project link with project download -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
  [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
  [Hinzufügen von Authentifizierung zur App]: #add-authentication
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [abgeschlossene Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
