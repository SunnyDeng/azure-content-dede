Für das über das Portal heruntergeladene Serverprojekt sind bereits Datenfunktionen aktiviert.

In Ihrem ASP.NET-Projekt sollte Folgendes angezeigt werden:

* Die NuGet Pakete `Microsoft.Azure.Mobile.Server.Tables` und `Microsoft.Azure.Mobile.Server.Entity` sind installiert.

* In der Datei "WebApiConfig.cs" wird die`UseDefaultConfiguration()`-Methode für das MobileAppConfiguration-Objekt aufgerufen. Dieses wiederum ruft die `AddTablesWithEntityFramework()`-Erweiterungsmethode, auf, die von dem oben genannten NuGet-Paket bereitgestellt wird.

<!---HONumber=August15_HO6-->