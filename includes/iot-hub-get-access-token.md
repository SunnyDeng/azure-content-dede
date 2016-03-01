## Ein Ressourcen-Manager-Token abrufen

Azure Active Directory muss alle Aufgaben authentifizieren, die Sie mithilfe des Azure-Ressourcen-Managers für die Ressourcen ausführen. Im hier gezeigten Beispiel wird die Kennwortauthentifizierung verwendet, andere Ansätze finden Sie unter [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers][lnk-authenticate-arm].

1. Fügen Sie der **Main**-Methode in „Program.cs“ folgenden Code hinzu, um mithilfe der Anwendungs-ID und des Kennworts ein Token von Azure AD abzurufen.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Erstellen Sie ein **ResourceManagementClient**-Objekt, das das Token verwendet. Fügen Sie dazu folgenden Code am Ende der **Main**-Methode hinzu:

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Erstellen Sie oder erhalten Sie einen Verweis auf die Ressourcengruppe, die Sie verwenden:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!---HONumber=AcomDC_0218_2016-->