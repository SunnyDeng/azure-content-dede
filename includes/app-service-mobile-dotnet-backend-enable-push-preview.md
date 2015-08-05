Für das über das Portal heruntergeladene Serverprojekt ist bereits Push aktiviert.

In Ihrem ASP.NET-Projekt sollte Folgendes angezeigt werden:

* Das NuGet-Paket `Microsoft.Azure.Mobile.Server.Notifications` ist installiert.

* In der Datei "WebApiConfig.cs" wird die`UseDefaultConfiguration()`-Methode für das MobileAppConfiguration-Objekt aufgerufen. Dieses wiederum ruft die `AddPushNotifications()`-Erweiterungsmethode, auf, die von dem oben genannten NuGet-Paket bereitgestellt wird.

<!---HONumber=July15_HO4-->