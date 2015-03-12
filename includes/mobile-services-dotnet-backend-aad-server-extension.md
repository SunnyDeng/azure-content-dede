### (Optional) Konfigurieren Ihres mobilen .NET-Diensts für die AAD-Anmeldung

>[AZURE.NOTE] Diese Schritte sind optional, da sie nur für den Azure Active Directory-Anmeldeanbieter gelten.

1. Installieren Sie das **WindowsAzure.MobileServices.Backend.Security**-NuGet-Paket.

2. Erweitern Sie in Visual Studio "App_Start", und öffnen Sie die Datei "WebApiConfig.cs". Fügen Sie der  `Register`-Methode direkt nach der Instanziierung von  `options` folgenden Code hinzu:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!--HONumber=42-->
