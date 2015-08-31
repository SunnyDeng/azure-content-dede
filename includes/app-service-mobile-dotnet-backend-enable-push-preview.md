Für das über das Portal heruntergeladene Serverprojekt wurden bereits Pushbenachrichtigungen aktiviert.

Sie können im ASP.NET-Projekt Folgendes überprüfen:

* Das NuGet-Paket `Microsoft.Azure.Mobile.Server.Notifications` ist installiert.

* In der Datei "WebApiConfig.cs" wird die`UseDefaultConfiguration()`-Methode für das MobileAppConfiguration-Objekt aufgerufen. Dieses wiederum ruft die `AddPushNotifications()`-Erweiterungsmethode, auf, die von dem oben genannten NuGet-Paket bereitgestellt wird.

<!---HONumber=August15_HO8-->