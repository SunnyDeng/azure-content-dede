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
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps

In diesem Thema wird das Verwenden des .NET-Back-End-Server-SDKs in Azure App Service Mobile Apps-Szenarien veranschaulicht. Das Azure-SDK für Mobile Apps erleichtert Ihnen die Arbeit mit mobilen Clients aus der ASP.NET-Anwendung.

>[AZURE.TIP]Das [.NET-Server-SDK für Azure Mobile Apps](https://github.com/Azure/azure-mobile-apps-net-server) ist als Open Source auf GitHub verfügbar. Das Repository enthält die gesamte Testsammlung der Server-SDK-Einheit sowie einige Projektbeispiele.

## Vorgehensweise: Erstellen eines .NET-Back-Ends für Ihre mobile Anwendung

Wenn Sie ein neues Projekt beginnen, können Sie entweder mit dem [Azure-Vorschauverwaltungsportal] oder mit Visual Studio eine App Service-Anwendung erstellen. In diesem Abschnitt wird erläutert, wie Sie mit einer dieser Komponenten ein neues Back-End für eine mobile Anwendung erstellen, das eine einfache Aufgabenlisten-API hostet. Sie können die Erstellung lokal vornehmen oder das Projekt in der cloudbasierten mobilen App Service-App veröffentlichen.

Wenn Sie einem vorhandenen Projekt mobile Funktionen hinzufügen, lesen Sie weiter unten die Informationen im Abschnitt [Herunterladen und Initialisieren des SDKs](#install-sdk).

### Erstellen eines .NET-Back-Ends mithilfe des Azure-Portals

Sie können direkt im [Azure-Vorschauverwaltungsportal ] eine neue mobile Anwendung erstellen. Sie können entweder die unten genannten Schritte ausführen oder anhand der Anweisungen im Tutorial [Erstellen einer mobilen App](app-service-mobile-ios-get-started.md) einen neuen Client und Server erstellen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. Wählen Sie auf dem Blatt _Erste Schritte_ unter **Erstellen einer Tabellen-API** die Option **C#** als **Back-End-Sprache**.

&nbsp;&nbsp;10. Klicken Sie auf „Herunterladen“, extrahieren Sie die komprimierten Projektdateien auf Ihrem lokalen Computer, und öffnen Sie die Projektmappe in Visual Studio.

### Erstellen eines .NET-Back-Ends mithilfe von Visual Studio 2013 und Visual Studio 2015

Um ein Projekt für mobile Apps in Visual Studio zu erstellen, müssen Sie [Azure SDK für .NET](https://azure.microsoft.com/downloads/) (Version 2.8.1 oder höher) installieren. Erstellen Sie nach dem Installieren des SDKs eine neue ASP.NET-Anwendung:

1. Öffnen Sie das Dialogfeld **Neues Projekt** (über *Datei* > **Neu** > **Projekt...**).

2. Erweitern Sie **Vorlagen** > **Visual C#**, und wählen Sie **Web** aus.

3. Wählen Sie **ASP.NET-Webanwendung**.

4. Geben Sie den Projektnamen ein. Klicken Sie dann auf **OK**.

5. Wählen Sie unter _ASP.NET 4.5.2-Vorlagen_ die Option **Azure Mobile App**. Aktivieren Sie **In der Cloud hosten**, um eine neue mobile App in der Cloud zu erstellen, in der Sie dieses Projekt veröffentlichen können.

6. Klicken Sie auf **OK**. Die Anwendung wird erstellt und im Projektmappen-Explorer angezeigt.

## <a name="install-sdk"></a>Vorgehensweise: Herunterladen und Initialisieren des SDKs

Das SDK steht unter [NuGet.org] zur Verfügung. Dieses Paket enthält die Basisfunktionalität, die für den Einstieg in das SDK erforderlich ist. Um das SDK zu initialisieren, müssen Sie Aktionen auf dem **HttpConfiguration**-Objekt ausführen.

###Installieren des SDKs

Um das SDK zu installieren, klicken Sie mit der rechten Maustaste auf das Serverprojekt in Visual Studio, wählen Sie **NuGet-Pakete verwalten**, suchen Sie das [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)-Paket, und klicken Sie dann auf **Installieren**.

###<a name="server-project-setup"></a> Initialisieren des Serverprojekts

Ein .NET-Back-End-Server-Projekt wird wie andere ASP.NET-Projekte durch das Einschließen einer OWIN-Startklasse initialisiert. Um diese Klasse in Visual Studio hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Server-Projekt, und wählen Sie **Hinzufügen** -> **Neues Element** und dann **Web** -> **Allgemein** -> **OWIN-Startklasse** aus.

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

Um einzelne Funktionen zu aktivieren, müssen Sie die Erweiterungsmethoden im **MobileAppConfiguration**-Objekt vor dem Aufruf von **ApplyTo** aufrufen. Der folgende Code fügt beispielsweise die Standardrouten für alle API-Controller während der Initialisierung hinzu:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Viele der Methoden zur Funktionserweiterung sind über zusätzliche NuGet-Pakete verfügbar, die Sie übernehmen können und die im folgenden Abschnitt beschrieben werden. Beim Schnellstartserverprojekt aus dem Azure-Portal wird **UseDefaultConfiguration()** aufgerufen. Dies entspricht der folgenden Konfiguration:
    
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

- [Microsoft.Azure.Mobile.Server.Login] bietet Vorschauunterstützung für die benutzerdefinierte Authentifizierung über die Methode „MobileAppLoginHandler.CreateToken()“. Hierbei handelt es sich um eine statische Methode, die nicht in der Konfiguration aktiviert werden muss.

## Vorgehensweise: Veröffentlichen des Serverprojekts

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

Sie können auch eine der anderen Methoden verwenden, die in der [Dokumentation zur Azure App Service-Bereitstellung](../app-service-web/web-site-deploy.md) behandelt werden.

## Vorgehensweise: Definieren eines Tabellencontrollers

Ein Tabellecontroller ermöglicht den Zugriff auf Entitätsdaten in einem tabellenbasierten Datenspeicher, z. B. SQL-Datenbank oder Azure Tabellenspeicher. Tabellencontroller erben von der generischen **TableController**-Klasse, in der der generische Typ eine Entität im Modell ist, die das Tabellenschema wie folgt darstellt:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Tabellencontroller werden mithilfe der **AddTables**-Erweiterungsmethode initialisiert. Im folgende Beispiel wird ein Tabellencontroller initialisiert, der Entity Framework für den Datenzugriff verwendet:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Ein Beispiel für einen Tabellencontroller, der für den Datenzugriff in einer Azure SQL-Datenbank das Entity Framework verwendet, finden Sie im Azure-Portal in der **TodoItemController**-Klasse im Downloadbereich des Schnellstartserverprojekts.


## Gewusst wie: Definieren eines benutzerdefinierten API-Controllers

Der benutzerdefinierte API-Controller bietet Grundfunktionen für Ihr Mobile App-Back-End, indem ein Endpunkt verfügbar gemacht wird. Der benutzerdefinierte API-Controller

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

Clients haben noch immer Zugriff auf alle Controller ohne angewendetes **MobileAppControllerAttribute**. Die Controller werden jedoch nicht ordnungsgemäß von Clients genutzt, die ein Mobile App-Client-SDK verwenden.


## Vorgehensweise: Authentifizierung zu einem Serverprojekt hinzufügen

Sie können dem Serverprojekt eine Authentifizierung hinzufügen, indem Sie das **MobileAppConfiguration**-Objekt erweitern und OWIN-Middleware konfigurieren. Wenn Sie das [Microsoft.Azure.Mobile.Server.Quickstart]-Paket installieren und die **UseDefaultConfiguration**-Erweiterungsmethode aufrufen, können Sie direkt mit Schritt 3 fortfahren.

1. Installieren Sie in Visual Studio das [Microsoft.Azure.Mobile.Server.Authentication]-Paket. 

2. Fügen Sie in der Projektdatei "Startup.cs" die folgende Codezeile am Anfang der **Configuration**-Methode hinzu:

		app.UseMobileAppAuthentication(config);

	Diese fügt die OWIN-Middleware-Komponente hinzu, mit der Ihre Azure Mobile App das vom zugeordneten App Service-Gateway ausgestellte Token überprüfen kann.

3. Fügen Sie jedem Controller oder jeder Methode das `[Authorize]`-Attribut hinzu, falls dafür eine Authentifizierung erforderlich ist. Benutzer müssen nun für den Zugriff auf diesen Endpunkt oder bestimmte APIs authentifiziert werden.

Informationen zum Authentifizieren von Clients bei Ihrem Mobile Apps-Back-End finden Sie unter [Hinzufügen von Authentifizierung zur App](app-service-mobile-ios-get-started-users.md).

## <a name="custom-auth"></a>Vorgehensweise: Verwenden der benutzerdefinierten Authentifizierung für Ihre Anwendung.

Sie können auch eigene Anmeldesysteme bereitstellen, wenn Sie keinen der Authentifizierungs-/Autorisierungsanbieter von App Service nutzen möchten. Installieren Sie dazu das Paket [Microsoft.Azure.Mobile.Server.Login].

Sie müssen Ihre eigene Logik bereitstellen, mit der ermittelt wird, ob ein Benutzer angemeldet werden soll. Sie können beispielsweise eine Datenbank auf Kennwörter mit Salt und Hash überprüfen. Im folgenden Beispiel übernimmt die Methode `isValidAssertion()` diese Überprüfungen. Sie wird an anderer Stelle definiert.

Die benutzerdefinierte Authentifizierung wird durch Erstellen eines neuen ApiController-Elements und Anmeldeaktionen (siehe weiter unten) verfügbar gemacht. Der Client kann die Anmeldung ausführen, indem er die relevanten Informationen vom Benutzer abruft und einen HTTPS-POST-Aufruf mit den Benutzerinformationen im Text an die API sendet. Sobald diese Informationen überprüft wurden, kann mithilfe der Methode `MobileAppLoginHandler.CreateToken()` ein Token ausgestellt werden.

Beispiel für eine Anmeldeaktion:

		public HttpResponseMessage Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = MobileAppLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return this.Request.CreateResponse(HttpStatusCode.OK, new LoginResult()
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

Die Methode `MobileAppLoginHandler.CreateToken()` enthält die Parameter _audience_ und _issuer_. Für diese beiden Parameter wird in der Regel mithilfe des HTTPS-Schemas die URL des Anwendungsstammverzeichnisses festgelegt. Für _secretKey_ sollte der Wert des Signaturschlüssels Ihrer Anwendung festgelegt werden. Dies ist ein vertraulicher Wert, der keinesfalls freigegeben werden oder in einem Client enthalten sein sollte. Sie können diesen in App Service gehosteten Wert abrufen, indem Sie auf die Umgebungsvariable _WEBSITE\_AUTH\_SIGNING\_KEY_ verweisen. Falls er in einem lokalen Debuggingkontext erforderlich ist, befolgen Sie die Anweisungen im Abschnitt [Lokales Debuggen mit Authentifizierung](#local-debug), um den Schlüssel abzurufen und als Anwendungseinstellung zu speichern.

Sie müssen darüber hinaus eine Lebensdauer für das ausgestellte Token sowie alle Ansprüche angeben, die aufgenommen werden sollen. Wie im Beispielcode gezeigt, müssen Sie den Anspruch „Betreff“ angeben.

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

## Vorgehensweise: Hinzufügen von Tags zu einer Geräteinstallation zum Aktivieren von Pushvorgängen an Tags

Gemäß den Schritten unter **Gewusst wie: Definieren eines benutzerdefinierten API-Controllers** können Sie eine benutzerdefinierte API auf Ihrem Back-End einrichten, die mit Notification Hubs zusammenarbeitet, um Tags zu einer bestimmten Geräteinstallation hinzuzufügen. Stellen Sie sicher, dass Sie die im lokalen Clientspeicher gespeicherte Installations-ID und die hinzuzufügenden Tags übergeben. (Letzteres ist optional, da Sie Tags auch direkt auf Ihrem Back-End angeben können.) Fügen Sie Ihrem Controller den folgenden Ausschnitt für die Zusammenarbeit mit Notification Hubs hinzu, um ein Tag zu einer Geräteinstallations-ID hinzuzufügen.

Verwendung von [Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([Referenz](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

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

Verwenden Sie [Notification Hubs-APIs](https://msdn.microsoft.com/library/azure/dn495101.aspx), um einen Pushvorgang an diese Tags durchzuführen.

Sie können Ihre benutzerdefinierte API auch so einrichten, dass Geräteinstallationen mit Notification Hubs direkt auf Ihrem Back-End registriert werden.

## Vorgehensweise: Debuggen und Problembehandlung des .NET-Server-SDKs

Azure App Service stellt mehrere Debug- und Problembehandlungsverfahren für ASP.NET-Anwendungen bereit.

- [Überwachen eines Azure App Service](../app-service-web/web-sites-monitor.md)
- [Aktivieren der Diagnoseprotokollierung in Azure App Service](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Problembehandlung für einen Azure App Service in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Protokollierung

Sie können über das standardmäßige Schreiben von ASP.NET-Ablaufverfolgungen in App Service-Diagnoseprotokolle schreiben:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

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

Im oben genannten Beispiel sollten Sie die _authAudience_- und die _authIssuer_-Anwendungseinstellung in der Datei „Web.config“ so konfigurieren, dass beide die URL des Anwendungsstammverzeichnisses darstellen. Verwenden Sie dazu das HTTPS-Schema. Für _authSigningKey_ sollte der Wert des Signaturschlüssels Ihrer Anwendung festgelegt werden. Dies ist ein vertraulicher Wert, der keinesfalls freigegeben werden oder in einem Client enthalten sein sollte. Navigieren Sie zum Abrufen dieses Werts im [Azure-Vorschauverwaltungsportal] zu Ihrer App, und klicken Sie auf **Tools**. Wählen Sie dann **Kudu**, und klicken Sie auf **Go**. Dadurch gelangen Sie zum Kudu-Verwaltungsendpunkt für Ihre Website. Klicken Sie auf **Umgebung**, und suchen Sie den Wert unter _WEBSITE\_AUTH\_SIGNING\_KEY_. Dies ist der Wert, den Sie in Ihrer lokalen App-Konfiguration für _AuthSigningKey_ verwenden sollten.

Ihr lokal ausgeführter Server kann nun Token überprüfen, die der Client vom cloudbasierten Endpunkt abruft.


[Azure-Vorschauverwaltungsportal]: https://portal.azure.com
[Azure-Vorschauverwaltungsportal ]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_1125_2015-->