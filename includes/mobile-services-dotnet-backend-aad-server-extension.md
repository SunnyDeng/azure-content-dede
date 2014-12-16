### (Optional) Konfigurieren Ihres mobilen .NET-Diensts für die AAD-Anmeldung

>[WACOM.NOTE] Diese Schritte sind optional, da sie nur für den Azure Active Directory-Anmeldeanbieter gelten.

1. Installieren Sie das NuGet-Paket **WindowsAzure.MobileServices.Backend.Security**.

2. Erweitern Sie in Visual Studio "App_Start", und öffnen Sie die Datei "WebApiConfig.cs". Fügen Sie der Methode `Register` die folgende Codezeile nach der Definition von `options` hinzu:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

