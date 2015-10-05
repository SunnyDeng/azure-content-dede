<properties
	pageTitle="Vorgehensweise beim Arbeiten mit der .NET-Back-End-Server-SDK für Mobile Apps | Azure App Service"
	description="Informationen zum Arbeiten mit der .NET-Back-End-Server-SDK für Azure App Service Mobile Apps."
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

## Vorgehensweise: Herunterladen und Initialisieren des SDKs

Das SDK steht unter [NuGet.org] zur Verfügung. Dieses Paket enthält die Basisfunktionalität, die für den Einstieg in das SDK erforderlich ist. Um das SDK zu initialisieren, müssen Sie Aktionen auf dem **HttpConfiguration**-Objekt ausführen.

###Installieren des SDKs

Um das SDK zu installieren, klicken Sie mit der rechten Maustaste auf das Serverprojekt in Visual Studio, wählen Sie **NuGet-Pakete verwalten**, suchen Sie das [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)-Paket, und klicken Sie dann auf **Installieren**.

###Initialisieren des Serverprojekts

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

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Fügt dem Stammverzeichnis der Website eine einfache Startseite hinzu. Wird durch Aufrufen der **AddMobileAppHomeController**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) Enthält Klassen zum Arbeiten mit Daten und richtet die Datenpipeline ein. Wird durch Aufrufen der **AddTables**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Ermöglicht dem Entity Framework, auf Daten in der SQL-Datenbank zuzugreifen. Wird durch Aufrufen der **AddTablesWithEntityFramework**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Authentication] Ermöglicht die Authentifizierung und richtet die OWIN-Middleware zum Überprüfen von Token ein. Wird durch Aufrufen der Erweiterungsmethoden **AddAppServiceAuthentication** und **IAppBuilder**.**UseMobileAppAuthentication** zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.Notifications] Aktiviert Pushbenachrichtigungen und definiert einen Endpunkt für die Pushregistrierung. Wird durch Aufrufen der **AddPushNotifications**-Erweiterungsmethode zur Konfiguration hinzugefügt.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Erstellt einen Controller, der Daten aus Ihrer mobilen App auf älteren Webbrowsern bereitstellt. Wird durch Aufrufen der **MapLegacyCrossDomainController**-Erweiterungsmethode zur Konfiguration hinzugefügt.

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

Clients haben noch immer Zugriff auf alle Controller ohne angewendetes **MobileAppControllerAttribute**, die Controller werden jedoch nicht ordnungsgemäß von Clients behandelt, die ein Mobile App-Client-SDK verwenden.

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

## Vorgehensweise: Authentifizierung zu einem Serverprojekt hinzufügen

Sie können dem Serverprojekt eine Authentifizierung hinzufügen, indem Sie das **MobileAppConfiguration**-Objekt erweitern und OWIN-Middleware konfigurieren. Wenn Sie das [Microsoft.Azure.Mobile.Server.Quickstart]-Paket installieren und die **UseDefaultConfiguration**-Erweiterungsmethode aufrufen, können Sie direkt mit Schritt 3 fortfahren.

1. Installieren Sie in Visual Studio das [Microsoft.Azure.Mobile.Server.Authentication]-Paket. 

2. Fügen Sie in der Projektdatei "Startup.cs" die folgende Codezeile am Anfang der **Configuration**-Methode hinzu:

		app.UseMobileAppAuthentication(config);

	Diese fügt die OWIN-Middleware-Komponente hinzu, mit der Ihre Azure Mobile App das vom zugeordneten App Service-Gateway ausgestellte Token überprüfen kann.

3. Fügen Sie jedem Controller oder jeder Methode das `[Authorize]`-Attribut hinzu, falls dafür eine Authentifizierung erforderlich ist. Benutzer müssen nun für den Zugriff auf diesen Endpunkt oder auf diese bestimmten APIs authentifiziert werden.

Informationen zum Authentifizieren von Clients auf Ihrem Mobile Apps-Back-End finden Sie unter [Hinzufügen von Authentifizierung zur App](app-service-mobile-dotnet-backend-ios-get-started-users-preview.md).

## Vorgehensweise: Hinzufügen von Pushbenachrichtigungen zu einem Serverprojekt

Sie können dem Serverprojekt Pushbenachrichtigungen hinzufügen, indem Sie das **MobileAppConfiguration**-Objekt erweitern und einen Notification Hubs-Client erstellen. Wenn Sie das [Microsoft.Azure.Mobile.Server.Quickstart]-Paket installieren und die **UseDefaultConfiguration**-Erweiterungsmethode aufrufen, können Sie direkt mit Schritt 3 fortfahren.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**. Suchen Sie nach "Microsoft.Azure.Mobile.Server.Notifications", und klicken Sie anschließend auf **Installieren**. Dadurch wird das [Microsoft.Azure.Mobile.Server.Notifications]-Paket installiert.
 
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

An diesem Punkt können Sie den Notification Hubs-Client zum Senden von Pushbenachrichtigungen an registrierte Geräte verwenden. Weitere Informationen finden Sie unter [Hinzufügen von Pushbenachrichtigungen zur App](app-service-mobile-dotnet-backend-ios-get-started-push-preview.md) Weitere Informationen zu allem, was mit Notification Hubs machbar ist, finden Sie unter [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-overview.md).

## Vorgehensweise: Veröffentlichen des Serverprojekts

Führen Sie die folgenden Schritte durch, um das Serverprojekt in Azure zu veröffentlichen:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Sept15_HO4-->