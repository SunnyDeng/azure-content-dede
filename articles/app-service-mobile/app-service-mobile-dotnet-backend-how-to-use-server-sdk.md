<properties
	pageTitle="Vorgehensweise beim Arbeiten mit der .NET-Back-End-Server-SDK für Mobile Apps | Azure App Service"
	description="Informationen zum Arbeiten mit der .NET-Back-End-Server-SDK für Azure App Service Mobile Apps."
	keywords="App Service, Azure App Service, mobile App, mobiler Service, skalieren, skalierbar, App-Bereitstellung, Azure-App-Bereitstellung"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="glenga"/>

# Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird das Verwenden des .NET-Back-End-Server-SDKs in Azure App Service Mobile Apps-Szenarien veranschaulicht. Das Azure-SDK für Mobile Apps erleichtert Ihnen die Arbeit mit mobilen Clients aus der ASP.NET-Anwendung.

>[AZURE.TIP]Das [.NET-Server-SDK für Azure Mobile Apps](https://github.com/Azure/azure-mobile-apps-net-server) ist als Open Source auf GitHub verfügbar. Das Repository enthält die gesamte Testsammlung der Server-SDK-Einheit sowie einige Projektbeispiele.

## Referenzdokumentation

Die Referenzdokumentation für das Server-SDK finden Sie hier: [Azure Mobile Apps .NET Referenz](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Erstellen eines .NET-Back-Ends für Ihre mobile Anwendung

Wenn Sie ein neues Projekt beginnen, können Sie entweder über das [Azure-Portal] oder mit Visual Studio eine App Service-Anwendung erstellen. In diesem Abschnitt wird erläutert, wie Sie mit einer dieser Komponenten ein neues Back-End für eine mobile Anwendung erstellen, das eine einfache Aufgabenlisten-API hostet. Sie können die Erstellung lokal vornehmen oder das Projekt in der cloudbasierten mobilen App Service-App veröffentlichen.

Wenn Sie einem vorhandenen Projekt mobile Funktionen hinzufügen, lesen Sie weiter unten die Informationen im Abschnitt [Herunterladen und Initialisieren des SDKs](#install-sdk).

### Erstellen eines .NET-Back-Ends mithilfe des Azure-Portals

Sie können direkt im [Azure-Portal ] eine neue mobile Anwendung erstellen. Sie können entweder die unten genannten Schritte ausführen oder anhand der Anweisungen im Tutorial [Erstellen einer mobilen App](app-service-mobile-ios-get-started.md) einen neuen Client und Server erstellen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. Wählen Sie auf dem Blatt _Erste Schritte_ unter **Erstellen einer Tabellen-API** die Option **C#** als **Back-End-Sprache**.

&nbsp;&nbsp;10. Klicken Sie auf „Herunterladen“, extrahieren Sie die komprimierten Projektdateien auf Ihrem lokalen Computer, und öffnen Sie die Projektmappe in Visual Studio.

### Erstellen eines .NET-Back-Ends mithilfe von Visual Studio 2013 und Visual Studio 2015

Um ein Mobile Apps-Projekt in Visual Studio zu erstellen, müssen Sie [Azure SDK für .NET](https://azure.microsoft.com/downloads/) (Version 2.8.1 oder höher) installieren. Erstellen Sie nach dem Installieren des SDKs eine neue ASP.NET-Anwendung:

1. Öffnen Sie das Dialogfeld **Neues Projekt** (über *Datei* > **Neu** > **Projekt...**).

2. Erweitern Sie **Vorlagen** > **Visual C#**, und wählen Sie **Web** aus.

3. Wählen Sie **ASP.NET-Webanwendung** aus.

4. Geben Sie den Projektnamen ein. Klicken Sie dann auf **OK**.

5. Wählen Sie unter _ASP.NET 4.5.2-Vorlagen_ die Option **Azure Mobile App** aus. Aktivieren Sie **In der Cloud hosten**, um eine neue mobile App in der Cloud zu erstellen, in der Sie dieses Projekt veröffentlichen können.

6. Klicken Sie auf **OK**. Die Anwendung wird erstellt und im Projektmappen-Explorer angezeigt.

## <a name="install-sdk"></a>Herunterladen und Initialisieren des SDKs

Das SDK steht unter [NuGet.org] zur Verfügung. Dieses Paket enthält die Basisfunktionalität, die für den Einstieg in das SDK erforderlich ist. Um das SDK zu initialisieren, müssen Sie Aktionen auf dem **HttpConfiguration**-Objekt ausführen.

###Installieren des SDKs

Um das SDK zu installieren, klicken Sie mit der rechten Maustaste auf das Serverprojekt in Visual Studio, wählen Sie **NuGet-Pakete verwalten**, suchen Sie das [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)-Paket, und klicken Sie dann auf **Installieren**.

###<a name="server-project-setup"></a> Initialisieren des Serverprojekts

Ein .NET-Back-End-Server-Projekt wird wie andere ASP.NET-Projekte durch das Einschließen einer OWIN-Startklasse initialisiert. Stellen Sie sicher, dass Sie auf das NuGet-Paket `Microsoft.Owin.Host.SystemWeb` verwiesen haben. Um diese Klasse in Visual Studio hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Server-Projekt, und wählen Sie **Hinzufügen** > **Neues Element** und dann **Web** > **Allgemein** > **OWIN-Startklasse** aus.

Dadurch wird eine Klasse mit dem folgenden Attribut generiert:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Richten Sie in der `Configuration()`-Methode der OWIN-Startklasse das Server-Projekt mit einem **HttpConfiguration**-Objekt ein, das die Konfigurationsoptionen für den Dienst darstellt. Im folgende Beispiel wird das Serverprojekt ohne zusätzliche Funktionen initialisiert:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

Um einzelne Funktionen zu aktivieren, müssen Sie die Erweiterungsmethoden im **MobileAppConfiguration**-Objekt vor dem Aufruf von **ApplyTo** aufrufen. Mit dem folgenden Code werden beispielsweise während der Initialisierung die Standardrouten für alle API-Controller hinzugefügt, die über das `[MobileAppController]`-Attribut verfügen:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Beachten Sie, dass mit `MapApiControllers` nur Controller mit dem `[MobileAppController]`-Attribut zugeordnet werden.

Viele der Methoden zur Funktionserweiterung sind über zusätzliche NuGet-Pakete verfügbar, die Sie übernehmen können und die im folgenden Abschnitt beschrieben werden.

Beim Serverschnellstart aus dem Azure-Portal wird **UseDefaultConfiguration()** aufgerufen. Dies entspricht der folgenden Konfiguration:
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### SDK-Erweiterungen

Die folgenden NuGet-basierten Erweiterungspakete bieten verschiedene mobile Funktionen, die von der Anwendung verwendet werden können. Sie aktivieren mithilfe des **MobileAppConfiguration**-Objekts Erweiterungen während der Initialisierung.

- [Microsoft.Azure.Mobile.Server.Quickstart] Unterstützt die grundlegende Einrichtung von Mobile Apps. Wird durch Aufrufen der **UseDefaultConfiguration**-Erweiterungsmethode während der Initialisierung zur Konfiguration hinzugefügt. Diese Erweiterung umfasst die folgenden Erweiterungen: Benachrichtigungen, Authentifizierung, Entität, Tabellen, Crossdomain und Home-Pakete. Dies ist äquivalent zum Schnellstart-Serverprojekt, das Sie aus dem Azure-Portal herunterladen.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/): Implementiert die Standardseite *Diese mobile App ist betriebsbereit* für den Websitestamm. Wird durch Aufrufen der **AddMobileAppHomeController**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/): Enthält Klassen zum Arbeiten mit Daten und richtet die Datenpipeline ein. Wird durch Aufrufen der **AddTables**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/): Ermöglicht dem Entity Framework, auf Daten in der SQL-Datenbank zuzugreifen. Wird durch Aufrufen der **AddTablesWithEntityFramework**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Authentication]\: Ermöglicht die Authentifizierung und richtet die OWIN-Middleware zum Überprüfen von Token ein. Wird durch Aufrufen der Erweiterungsmethoden **AddAppServiceAuthentication** und **IAppBuilder**.**UseMobileAppAuthentication** zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Notifications]\: Aktiviert Pushbenachrichtigungen und definiert einen Endpunkt für die Pushregistrierung. Wird durch Aufrufen der **AddPushNotifications**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/): Erstellt einen Controller, der Daten aus Ihrer mobilen App auf älteren Webbrowsern bereitstellt. Wird durch Aufrufen der **MapLegacyCrossDomainController**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Login] bietet Vorschauunterstützung für die benutzerdefinierte Authentifizierung über die MobileAppLoginHandler.CreateToken()-Methode. Hierbei handelt es sich um eine statische Methode, die nicht in der Konfiguration aktiviert werden muss.

## <a name="publish-server-project"></a>Vorgehensweise: Veröffentlichen des Serverprojekts

In diesem Abschnitt erfahren Sie, wie Sie Ihr .NET-Back-End-Projekt über Visual Studio veröffentlichen. Sie können Ihr Back-End-Projekt auch mithilfe von Git oder einer der anderen Methoden bereitstellen, die in der [Dokumentation zur Azure App Service-Bereitstellung](../app-service-web/web-site-deploy.md) behandelt werden.

1. Erstellen Sie das Projekt in Visual Studio neu, um die NuGet-Pakete wiederherzustellen.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**. Bei der erstmaligen Veröffentlichung muss ein Veröffentlichungsprofil definiert werden. Wenn Sie bereits ein Profil definiert haben, können Sie es einfach auswählen und auf **Veröffentlichen** klicken.

2. Wenn Sie zum Auswählen eines Veröffentlichungsziels aufgefordert werden, klicken Sie auf **Microsoft Azure App Service** > **Weiter**, und melden Sie sich dann bei Bedarf mit Ihren Azure-Anmeldeinformationen an. Visual Studio lädt die Veröffentlichungseinstellungen direkt von Azure herunter und speichert sie sicher.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Wählen Sie Ihr Abonnement und unter **Ansicht** die Option **Ressourcentyp** aus, erweitern Sie **Mobile App**, klicken Sie auf das Back-End Ihrer mobilen App, und klicken Sie anschließend auf **OK**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Überprüfen Sie die Angaben des Veröffentlichungsprofils, und klicken Sie auf **Veröffentlichen**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	Wenn das mobile App-Back-End erfolgreich veröffentlicht wurde, wird eine Startseite angezeigt.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## Vorgehensweise: Definieren eines Tabellencontrollers

Ein Tabellecontroller ermöglicht den Zugriff auf Entitätsdaten in einem tabellenbasierten Datenspeicher, z. B. SQL-Datenbank oder Azure Tabellenspeicher. Tabellencontroller erben von der generischen **TableController**-Klasse, in der der generische Typ eine Entität im Modell ist, die das Tabellenschema wie folgt darstellt:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Tabellencontroller werden mithilfe der **AddTables**-Erweiterungsmethode initialisiert. Dadurch werden für alle Unterklassen von `TableController` Routen unter `/tables/` hinzugefügt.

Im folgende Beispiel wird ein Tabellencontroller initialisiert, der Entity Framework für den Datenzugriff verwendet:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Ein Beispiel für einen Tabellencontroller, der für den Datenzugriff in einer Azure SQL-Datenbank das Entity Framework verwendet, finden Sie im Azure-Portal in der **TodoItemController**-Klasse im Downloadbereich des Schnellstartserverprojekts.

## Gewusst wie: Definieren eines benutzerdefinierten API-Controllers

Der benutzerdefinierte API-Controller bietet Grundfunktionen für Ihr Mobile App-Back-End, indem ein Endpunkt verfügbar gemacht wird. Mit dem `MobileAppControllerAttribute`-Attribut können Sie einen mobilspezifischen API-Controller registrieren. Mit diesem Attribut wird die Route registriert und zudem das JSON-Serialisierungsprogramm für Mobile Apps eingerichtet.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner "Controller", und klicken Sie dann auf **Hinzufügen** > **Controller**. Wählen Sie **Web-API 2-Controller &ndash;Empty**, und klicken Sie auf **Hinzufügen**.

2. Geben Sie einen **Controllernamen** an, z. B. `CustomController`, und klicken Sie auf **Hinzufügen**. Dadurch wird eine neue **CustomController**-Klasse erstellt, die von **ApiController** erbt.

3. Fügen Sie in der neuen Controller-Klassendatei die folgende using-Anweisung hinzu:

		using Microsoft.Azure.Mobile.Server.Config;

4. Wenden Sie das **MobileAppControllerAttribute** wie im folgenden Beispiel auf die API-Controller-Klassendefinition an:

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. Fügen Sie in der Datei "App\_Start/Startup.MobileApp.cs" einen Aufruf der **MapApiControllers**-Erweiterungsmethode wie im folgenden Beispiel hinzu:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Beachten Sie, dass Sie **MapApiControllers** nicht aufrufen müssen, wenn Sie stattdessen **UseDefaultConfiguration** aufrufen, wodurch alle Funktionen initialisiert werden.

Clients haben noch immer Zugriff auf alle Controller ohne angewendetes **MobileAppControllerAttribute**. Die Controller werden jedoch möglicherweise nicht ordnungsgemäß von Clients genutzt, die ein Client-SDK für mobile Apps verwenden.

## Vorgehensweise: Verwenden der Authentifizierung

Mobile Apps greift zur Vereinfachung der Authentifizierungsimplementierung in Ihre Apps auf die App Service-Authentifizierung und auf ASP.NET zurück. In diesem Abschnitt erfahren Sie, wie Sie die folgenden authentifizierungsbezogenen Aufgaben in Ihrem .NET-Back-End-Serverprojekt ausführen:

+ [Vorgehensweise: Authentifizierung zu einem Serverprojekt hinzufügen](#add-auth) 
+ [Vorgehensweise: Verwenden einer benutzerdefinierten Authentifizierung für Ihre Anwendung](#custom-auth) 
+ [Vorgehensweise: Abrufen von Informationen zu authentifizierten Benutzern](#user-info)

### <a name="add-auth"></a>Vorgehensweise: Hinzufügen einer Authentifizierung zu einem Serverprojekt

Sie können dem Serverprojekt eine Authentifizierung hinzufügen, indem Sie das **MobileAppConfiguration**-Objekt erweitern und OWIN-Middleware konfigurieren. Wenn Sie das [Microsoft.Azure.Mobile.Server.Quickstart]-Paket installieren und die **UseDefaultConfiguration**-Erweiterungsmethode aufrufen, können Sie direkt mit Schritt 3 fortfahren.

1. Installieren Sie in Visual Studio das [Microsoft.Azure.Mobile.Server.Authentication]-Paket. 

2. Fügen Sie in der Projektdatei "Startup.cs" die folgende Codezeile am Anfang der **Configuration**-Methode hinzu:

		app.UseAppServiceAuthentication(config);

	Diese fügt die OWIN-Middleware-Komponente hinzu, mit der Ihre Azure Mobile App das vom zugeordneten App Service-Gateway ausgestellte Token überprüfen kann.

3. Fügen Sie jedem Controller oder jeder Methode das `[Authorize]`-Attribut hinzu, falls dafür eine Authentifizierung erforderlich ist. Benutzer müssen nun für den Zugriff auf diesen Endpunkt oder bestimmte APIs authentifiziert werden.

Informationen zum Authentifizieren von Clients bei Ihrem Mobile Apps-Back-End finden Sie unter [Hinzufügen von Authentifizierung zur App](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Vorgehensweise: Verwenden einer benutzerdefinierten Authentifizierung für Ihre Anwendung

Sie können auch eigene Anmeldesysteme bereitstellen, wenn Sie keinen der Authentifizierungs-/Autorisierungsanbieter von App Service nutzen möchten. Installieren Sie dazu das Paket [Microsoft.Azure.Mobile.Server.Login].

Sie müssen Ihre eigene Logik bereitstellen, mit der ermittelt wird, ob ein Benutzer angemeldet werden soll. Sie können beispielsweise eine Datenbank auf Kennwörter mit Salt und Hash überprüfen. Im folgenden Beispiel übernimmt die `isValidAssertion()`-Methode diese Überprüfungen. Sie wird an anderer Stelle definiert.

Die benutzerdefinierte Authentifizierung wird durch Erstellen eines neuen ApiController-Elements und Anmeldeaktionen (siehe weiter unten) verfügbar gemacht. Der Client kann die Anmeldung ausführen, indem er die relevanten Informationen vom Benutzer abruft und einen HTTPS-POST-Aufruf mit den Benutzerinformationen im Text an die API sendet. Wenn der Server die Assertion überprüft, kann mithilfe der `AppServiceLoginHandler.CreateToken()`-Methode ein Token ausgegeben werden.

Beispiel für eine Anmeldeaktion:

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

In diesem Beispiel sind „LoginResult“ und „LoginResultUser“ nur einfache Objekte mit den angezeigten Eigenschaften. Der Client erwartet, dass Anmeldeantworten als JSON-Objekte im folgenden Format zurückgegeben werden:

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

Die `MobileAppLoginHAppServiceLoginHandlerandler.CreateToken()`-Methode enthält die Parameter _audience_ und _issuer_. Für diese beiden Parameter wird in der Regel mithilfe des HTTPS-Schemas die URL des Anwendungsstammverzeichnisses festgelegt. Für _secretKey_ muss der Wert des Signaturschlüssels Ihrer Anwendung festgelegt werden. Dies ist ein vertraulicher Wert, der keinesfalls freigegeben werden oder in einem Client enthalten sein sollte. Sie können diesen in App Service gehosteten Wert abrufen, indem Sie auf die Umgebungsvariable _WEBSITE\_AUTH\_SIGNING\_KEY_ verweisen. Falls er in einem lokalen Debuggingkontext erforderlich ist, befolgen Sie die Anweisungen im Abschnitt [Lokales Debuggen mit Authentifizierung](#local-debug), um den Schlüssel abzurufen und als Anwendungseinstellung zu speichern.

Sie müssen darüber hinaus eine Lebensdauer für das ausgestellte Token sowie alle Ansprüche angeben, die aufgenommen werden sollen. Wie im Beispielcode gezeigt, müssen Sie den Anspruch „Betreff“ angeben.

Sie können den Clientcode auch vereinfachen, um die `loginAsync()`-Methode (die Benennung kann über Plattformen hinweg variieren) anstelle einer manuellen HTTP POST-Methode zu verwenden. Sie verwenden die Überlagerung, die einen zusätzlichen Parameter verwendet, der mit dem Assertionsprojekt zusammenhängt, das Sie bereitstellen möchten. Der Anbieter sollte in diesem Fall einen benutzerdefinierten Namen Ihrer Wahl tragen. Auf dem Server erfolgt Ihre Anmeldeaktion dann unter dem Pfad _/.auth/login/{customProviderName}_, der diesen benutzerdefinierten Namen enthält. Um den Controller unter diesem Pfad zu speichern, fügen Sie Ihrer HttpConfiguration eine Route hinzu, bevor Sie Ihre MobileAppConfiguration anwenden.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" }); 
		
Ersetzen Sie die Zeichenfolge „CustomAuth“ oben durch den Namen des Controller, der Ihre Anmeldeaktion hostet.

>[AZURE.TIP]Mit dem loginAsync()-Ansatz wird sichergestellt, dass das Authentifizierungstoken jedem nachfolgenden Aufruf des Diensts angefügt wird.

###<a name="user-info"></a>Vorgehensweise: Abrufen von Informationen zu authentifizierten Benutzern

Wenn ein Benutzer von App Service authentifiziert wird, können Sie in Ihrem .NET-Back-End-Code auf die zugewiesene Benutzer-ID sowie auf andere Informationen zugreifen. Dies ist hilfreich, wenn für einen bestimmten Benutzer im Back-End Autorisierungsentscheidungen zu treffen sind – etwa, ob ein bestimmter Benutzer auf eine Tabellenzeile oder andere Ressource Zugriff haben soll. Der folgende Code zeigt, wie Sie die Benutzer-ID für einen angemeldeten Benutzer abrufen:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Die SID leitet sich von der anbieterspezifischen Benutzer-ID ab und ist für einen bestimmten Benutzer und Anmeldeanbieter statisch.

App Service ermöglicht auch die Anforderung bestimmter Ansprüche von Ihrem Anmeldeanbieter. Dadurch können Sie weitere Informationen vom Anbieter anfordern (etwa mithilfe der Facebook-Graph-APIs). Ansprüche können im Portal auf dem Anbieterblatt angegeben werden. Einige Ansprüche erfordern zusätzliche Konfigurationsschritte für den Anbieter.

Der folgende Code ruft die Erweiterungsmethode **GetAppServiceIdentityAsync** auf, um die Anmeldeinformationen abzurufen (einschließlich des Zugriffstokens, das für Anforderungen an die Facebook-Graph-API erforderlich ist):

    // Get the credentials for the logged-in user.
    var credentials = 
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token=" 
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Beachten Sie, dass für `System.Security.Principal` eine using-Anweisung hinzugefügt werden muss, damit die Erweiterungsmethode **GetAppServiceIdentityAsync** funktioniert.


## Vorgehensweise: Hinzufügen von Pushbenachrichtigungen zu einem Serverprojekt

Sie können dem Serverprojekt Pushbenachrichtigungen hinzufügen, indem Sie das **MobileAppConfiguration**-Objekt erweitern und einen Notification Hubs-Client erstellen. Wenn Sie das [Microsoft.Azure.Mobile.Server.Quickstart]-Paket installieren und die **UseDefaultConfiguration**-Erweiterungsmethode aufrufen, können Sie direkt mit Schritt 3 fortfahren.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**. Suchen Sie "Microsoft.Azure.Mobile.Server.Notifications", und klicken Sie anschließend auf **Installieren**. Dadurch wird das [Microsoft.Azure.Mobile.Server.Notifications]-Paket installiert.
 
3. Wiederholen Sie diesen Schritt zum Installieren des `Microsoft.Azure.NotificationHubs`-Pakets, das die Notification Hubs-Clientbibliothek enthält.

2. Fügen Sie in "App\_Start/Startup.MobileApp.cs" einen Aufruf der **AddPushNotifications**-Erweiterungsmethode während der Initialisierung hinzu, was wie folgt aussieht:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	Dadurch wird der Registrierungsendpunkt für die Pushbenachrichtigung in Ihrem Serverprojekt erstellt. Dieser Endpunkt wird von Clients verwendet, um sich mit dem dazugehörigen Hub zu registrieren. Nun müssen Sie den Notification Hub-Client hinzufügen, der zum Senden von Benachrichtigungen verwendet wird.

3. Fügen Sie in einem Controller, von dem Sie Pushbenachrichtigungen senden möchten, Sie die folgende using-Anweisung hinzu:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Fügen Sie folgenden Code hinzu, der einen Notification Hubs-Client erstellt:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

An diesem Punkt können Sie den Notification Hubs-Client zum Senden von Pushbenachrichtigungen an registrierte Geräte verwenden. Weitere Informationen finden Sie unter [Hinzufügen von Pushbenachrichtigungen zur App](app-service-mobile-ios-get-started-push.md) Weitere Informationen zu allem, was mit Notification Hubs machbar ist, finden Sie unter [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Hinzufügen von Tags zu einer Geräteinstallation zum Aktivieren von Pushvorgängen an Tags

Gemäß den Schritten unter **Vorgehensweise: Definieren eines benutzerdefinierten API-Controllers** können Sie eine benutzerdefinierte API auf Ihrem Back-End einrichten, die mit Notification Hubs zusammenarbeitet, um Tags einer bestimmten Geräteinstallation hinzuzufügen. Stellen Sie sicher, dass Sie die im lokalen Clientspeicher gespeicherte Installations-ID und die hinzuzufügenden Tags übergeben. (Letzteres ist optional, da Sie Tags auch direkt auf Ihrem Back-End angeben können.) Fügen Sie Ihrem Controller den folgenden Ausschnitt für die Zusammenarbeit mit Notification Hubs hinzu, um ein Tag zu einer Geräteinstallations-ID hinzuzufügen.

Verwendung von [NuGet (Azure Notification Hubs)](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([Referenz](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});

Verwenden Sie [Notification Hubs-APIs](https://msdn.microsoft.com/library/azure/dn495101.aspx), um einen Pushvorgang an diese Tags auszuführen.

Sie können Ihre benutzerdefinierte API auch so einrichten, dass Geräteinstallationen mit Notification Hubs direkt auf Ihrem Back-End registriert werden.

## Vorgehensweise: Debuggen und Problembehandlung des .NET-Server-SDKs

Azure App Service stellt mehrere Debug- und Problembehandlungsverfahren für ASP.NET-Anwendungen bereit.

- [Überwachen eines Azure App Service](../app-service-web/web-sites-monitor.md)
- [Aktivieren der Diagnoseprotokollierung in Azure App Service](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Problembehandlung für einen Azure App Service in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Protokollierung

Zum Schreiben in App Service-Diagnoseprotokolle kann das standardmäßige Schreiben von ASP.NET-Ablaufverfolgungen verwendet werden. Um in die Protokolle schreiben zu können, müssen Sie in Ihrem Mobile App-Back-End die Diagnose aktivieren.

So können Sie die Diagnose aktivieren und in die Protokolle schreiben:

1. Führen Sie die unter [Aktivieren der Diagnose](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag) angegebenen Schritte aus.

2. Fügen Sie Ihrer Codedatei folgende using-Anweisung hinzu:

		using System.Web.Http.Tracing;

3. Erstellen Sie wie folgt einen Ablaufverfolgungs-Writer für Schreibvorgänge aus dem .NET Back-End in die Diagnoseprotokolle:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

4. Veröffentlichen Sie Ihr Serverprojekt neu, und greifen Sie auf das Mobile App-Back-End zu, um den Codepfad mit der Protokollierung auszuführen.

5. Gehen Sie zum Herunterladen und Auswerten der Protokolle wie unter [Vorgehensweise: Herunterladen von Protokollen](../app-service-web/web-sites-enable-diagnostic-log.md#download) beschrieben vor.

### <a name="local-debug"></a>Lokales Debuggen mit Authentifizierung

Sie können zum Testen von Änderungen Ihre Anwendung vor der Veröffentlichung in der Cloud lokal ausführen. Bei vielen Apps muss dazu in Visual Studio nur die Taste *F5* gedrückt werden. Bei der Verwendung der Authentifizierung gibt es jedoch einige zusätzliche Aspekte zu berücksichtigen.

Sie benötigen eine cloudbasierte mobile App, für die App Service-Authentifizierung/-Autorisierung konfiguriert ist. Für den Client muss als alternativer Anmeldehost zudem der Cloudendpunkt angegeben sein. Die für Ihre ausgewählte Clientplattform ([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) erforderlichen Schritte finden Sie in der entsprechenden Dokumentation.

Stellen Sie sicher, dass für Ihre Anwendung [Microsoft.Azure.Mobile.Server.Authentication] installiert ist. Fügen Sie dann in der OWIN-Startklasse Ihrer Anwendung Folgendes hinzu (nach der Anwendung von `MobileAppConfiguration` auf `HttpConfiguration`):
		
		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

Im obigen Beispiel müssen die Anwendungseinstellungen _authAudience_ und _authIssuer_ in der Datei „Web.config“ so konfiguriert werden, dass beide die URL des Anwendungsstammverzeichnisses darstellen. Verwenden Sie dazu das HTTPS-Schema. _authSigningKey_ muss auf den Wert des Signaturschlüssels Ihrer Anwendung festgelegt werden. Dies ist ein vertraulicher Wert, der keinesfalls freigegeben werden oder in einem Client enthalten sein sollte. Navigieren Sie zum Abrufen dieses Werts im [Azure-Portal] zu Ihrer App, und klicken Sie auf **Tools**. Wählen Sie dann **Kudu** aus, und klicken Sie auf **Go**. Dadurch gelangen Sie zum Kudu-Verwaltungsendpunkt für Ihre Website. Klicken Sie auf **Umgebung**, und suchen Sie den Wert unter _WEBSITE\_AUTH\_SIGNING\_KEY_. Dies ist der Wert, den Sie in Ihrer lokalen App-Konfiguration für _authSigningKey_ verwenden müssen.

Ihr lokal ausgeführter Server kann nun Token überprüfen, die der Client vom cloudbasierten Endpunkt abruft.


[Azure-Portal]: https://portal.azure.com
[Azure-Portal ]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0121_2016-->