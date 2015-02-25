<properties pageTitle="Erste Schritte bei der Authentifizierung in Mobile Services für Xamarin Android-Apps - Azure Mobile Services" description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Xamarin Android-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." services="mobile-services" documentationCenter="xamarin" authors="lindydonna" manager="dwrede" editor="mollybos"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam"/>

# Erste Schritte bei der Authentifizierung in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>Führen Sie das Client-Projekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Simulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1. Fügen Sie der **TodoActivity**-Klasse die folgende Eigenschaft hinzu:

			private MobileServiceUser user;

2. Fügen Sie der **TodoActivity**-Klasse die folgende Methode hinzu: 

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    Damit wird eine neue Methode erstellt, die den Authentifizierungsprozess handhabt. Der Benutzer wird mithilfe eines Facebook-Logins authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. 

    > [AZURE.NOTE] Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **LoginAsync** oben übergebenen Wert auf einen der folgenden Werte: _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Fügen Sie in der **OnCreate**-Methode die folgende Codezeile im Anschluss an den Code hinzu, der das  `MobileServiceClient`-Objekt instanziiert.

		// Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Dieser Aufruf startet den Authentifizierungsprozess und wartet asynchron auf eine Antwort.


4. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden. 

   	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.


<!-- ## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. 
 -->
 
<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push/
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript und HTML]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
