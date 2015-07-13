### (Optional) Konfigurieren von .NET Mobile Services für Azure Active Directory

>[AZURE.NOTE]Diese Schritte sind optional, da sie nur für den Azure Active Directory-Anmeldeanbieter gelten.

1. Installieren Sie das NuGet-Paket [WindowsAzure.MobileServices.Backend.Security](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security).

2. Erweitern Sie in Visual Studio "App_Start", und öffnen Sie die Datei "WebApiConfig.cs". Fügen Sie die folgende `using`-Anweisung am Anfang hinzu:

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. Fügen Sie ebenfalls in "WebApiConfig.cs" der `Register`-Methode direkt nach der Instanziierung von `options` folgenden Code hinzu:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=62-->