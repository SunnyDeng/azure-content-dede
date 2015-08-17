Für das über das Portal heruntergeladene Serverprojekt ist bereits Authentifizierung aktiviert.

In Ihrem ASP.NET-Projekt sollte Folgendes angezeigt werden:

* Das NuGet-Paket `Microsoft.Azure.Mobile.Server.Authentication` ist installiert.

* In der Datei "WebApiConfig.cs" wird die`UseDefaultConfiguration()`-Methode für das MobileAppConfiguration-Objekt aufgerufen. Dieses wiederum ruft die `AddAppServiceAuthentication()`-Erweiterungsmethode, auf, die von dem oben genannten NuGet-Paket bereitgestellt wird. Es registriert außerdem eine OWIN-Middleware, die für die Authentifizierung benötigt wird, indem `app.UseAppServiceAuthentication()` während des Starts von OWIN aufgerufen wird.

<!---HONumber=August15_HO6-->