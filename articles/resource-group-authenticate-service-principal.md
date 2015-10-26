<properties
   pageTitle="Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager"
   description="Hierin wird beschrieben, wie einem Dienstprinzipal über rollenbasierte Zugriffskontrolle Zugriffsberechtigungen gewährt werden und wie der Dienstprinzipal authentifiziert wird. Hierin wird gezeigt, wie diese Aufgaben mit PowerShell und der Azure-Befehlszeilenschnittstelle ausgeführt werden."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="tomfitz"/>

# Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager

In diesem Thema erfahren Sie, wie Sie einem Dienstprinzipal (z. B. einem automatisierten Prozess, einer Anwendung oder einem Dienst) den Zugriff auf andere Ressourcen in Ihrem Abonnement gestatten. Mithilfe des Azure-Ressourcen-Managers können Sie die rollenbasierte Zugriffssteuerung verwenden, um einem Dienstprinzipal zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren. In diesem Thema wird gezeigt, wie dem Dienstprinzipal mithilfe von PowerShell und der Azure-Befehlszeilenschnittstelle eine Rolle zugewiesen und wie der Dienstprinzipal authentifiziert wird.

Es wird ebenfalls erläutert, wie die Authentifizierung entweder mithilfe eines Benutzernamens und Kennworts oder mit einem Zertifikat erfolgt.

Sie können entweder Azure PowerShell oder die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows verwenden. Wenn Azure PowerShell noch nicht installiert ist, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](./powershell-install-configure.md). Wenn noch keine Azure-Befehlszeilenschnittstelle installiert ist, lesen [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

## Konzepte
1. Azure Active Directory (AAD): Dies ist ein Clouddienst zur Identitäts- und Zugriffsverwaltung. Weitere Informationen finden Sie unter [What is Azure Active Directory?](active-directory/active-directory-whatis.md) (Was ist Azure Active Directory?).
2. Dienstprinzipal: Eine Instanz einer Anwendung in einem Verzeichnis, für die auf andere Ressourcen zugegriffen werden muss.
3. AD-Anwendung: Dies ist ein Verzeichnisdatensatz, von dem eine Anwendung bei AAD identifiziert wird. Weitere Informationen finden Sie unter [Grundlagen der Authentifizierung im Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Authentifizieren des Dienstprinzipals mit einem Kennwort – PowerShell

In diesem Abschnitt führen Sie folgende Schritte aus: Erstellen eines Dienstprinzipals für eine Azure Active Directory-Anwendung, Zuweisen einer Rolle zum Dienstprinzipal und Authentifizieren als Dienstprinzipal durch Bereitstellen des Anwendungsbezeichners und -kennworts.

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. Melden Sie sich bei Ihrem Konto an.

        PS C:\> Login-AzureRmAccount

1. Erstellen Sie eine neue AAD-Anwendung, indem Sie den Befehl **New-AzureRmADApplication** ausführen. Geben Sie einen Anzeigenamen für die Anwendung an, außerdem die URI zu einer Seite mit einer Beschreibung der Anwendung (der Link wird nicht geprüft), die URIs, von denen die Anwendung identifiziert wird, und das Kennwort für Ihre Anwendungsidentität.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Untersuchen Sie das neue Anwendungsobjekt. Die Eigenschaft **ApplicationId** wird benötigt, um Dienstprinzipale zu erstellen, Rollenzuweisungen vorzunehmen und JWT-Token abzurufen.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

3. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim Parameter **ServicePrincipalName** entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Managing and Auditing Access to Resources](azure-portal/resource-group-rbac.md) (Verwalten und Überwachen des Zugriffs auf Ressourcen).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Rufen Sie das Abonnement ab, in dem die Rollenzuweisung erstellt wurde. Dieses Abonnement wird später verwendet, um die **TenantId** des Mandanten abzurufen, in dem sich die Rollenzuweisung des Dienstprinzipals befindet.

        PS C:\> $subscription = Get-AzureRmSubscription

     Wenn die Rollenzuweisung in einem anderen Abonnement als dem aktuell ausgewählten erstellt wurde, können Sie einen der Parameter **SubscriptoinId** oder **SubscriptionName** angeben, um ein anderes Abonnement abzurufen.

5. Erstellen Sie ein neues **PSCredential**-Objekt, das Ihre Anmeldeinformationen enthält, indem Sie den Befehl **Get-Credential** ausführen.

        PS C:\> $creds = Get-Credential

     Sie werden zum Eingeben Ihrer Anmeldeinformationen aufgefordert.

     ![][1]

     Verwenden Sie als Benutzernamen die **ApplicationId** oder die **IdentifierUris**, die Sie beim Erstellen der Anwendung verwendet haben. Verwenden Sie das Kennwort, das Sie beim Erstellen des Kontos angegeben haben.

6. Verwenden Sie die Anmeldeinformationen, die Sie als Eingabe für das Cmdlet **Add-AzureAccount** verwendet haben, von welchem der Dienstprinzipal angemeldet wird:

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     Die Authentifizierung des Dienstprinzipals für die ADD-Anwendung, die Sie erstellt haben, ist hiermit abgeschlossen.

7. Zur Authentifizierung aus einer Anwendung heraus fügen Sie folgenden .NET-Code ein. Nach dem Abrufen des Tokens können Sie auf Ressourcen im Abonnement zugreifen.

        public static string GetAToken()
        {
          var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
          var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
          var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

          if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }



## Authentifizieren des Dienstprinzipals mit einem Zertifikat – PowerShell

In diesem Abschnitt führen Sie folgende Schritte aus: Erstellen eines Dienstprinzipals für eine Azure Active Directory-Anwendung, Zuweisen einer Rolle zum Dienstprinzipal und Authentifizieren als Dienstprinzipal durch Bereitstellen eines Zertifikats. In diesem Thema wird davon ausgegangen, dass ein Zertifikat für Sie ausgestellt wurde.

Es werden zwei Möglichkeiten zum Arbeiten mit Zertifikaten aufgezeigt: Schlüsselanmeldeinformationen und Schlüsselwerte. Sie können beide Verfahren verwenden.

Zunächst müssen Sie einige Werte in PowerShell festlegen, die Sie später beim Erstellen der Anwendung verwenden.

1. Melden Sie sich bei Ihrem Konto an.

        PS C:\> Login-AzureRmAccount

1. Unabhängig davon, welches Verfahren Sie verwenden möchten, erstellen Sie ein X509Certificate-Objekt aus Ihrem Zertifikat, und rufen Sie den Schlüsselwert ab. Verwenden Sie den Pfad zu Ihrem Zertifikat und das Kennwort für das Zertifikat.

        $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Wenn Sie Schlüsselanmeldeinformationen verwenden, erstellen Sie das Objekt für diese Informationen, und legen Sie den Wert für dieses Objekt auf den `$keyValue`-Wert aus dem vorherigen Schritt fest.

        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Erstellen Sie eine Anwendung im Verzeichnis. Der erste Befehl zeigt die Verwendung der Schlüsselwerte.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Mit dem zweiten Beispiel können Sie Schlüsselanmeldeinformationen zuweisen.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    Untersuchen Sie das neue Anwendungsobjekt. Die Eigenschaft **ApplicationId** wird benötigt, um Dienstprinzipale zu erstellen, Rollenzuweisungen vorzunehmen und JWT-Token abzurufen.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

5. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim Parameter **ServicePrincipalName** entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Managing and Auditing Access to Resources](azure-portal/resource-group-rbac.md) (Verwalten und Überwachen des Zugriffs auf Ressourcen).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. Zur Authentifizierung aus einer Anwendung heraus fügen Sie folgenden .NET-Code ein. Nach dem Abrufen des Clients können Sie auf Ressourcen im Abonnement zugreifen.

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
        try 
        { 
            store.Open(OpenFlags.ReadOnly); 
            var certCollection = store.Certificates; 
            var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
            cert = certs[0]; 
        } 
        finally 
        { 
            store.Close(); 
        }        

        var certCred = new ClientAssertionCertificate(clientId, cert); 
        var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## Authentifizieren des Dienstprinzipals mit einem Kennwort – Azure-Befehlszeilenschnittstelle

Zunächst wird ein Dienstprinzipal erstellt. Dazu muss im Verzeichnis eine Anwendung erstellt werden. Das Erstellen einer neuen Anwendung im Verzeichnis wird in diesem Abschnitt erläutert.

1. Wechseln Sie in den Azure-Ressourcen-Manager-Modus, und melden Sie sich bei Ihrem Konto an.

        azure config mode arm
        azure login

2. Erstellen Sie eine neue AAD-Anwendung, indem Sie den Befehl **azure ad app create** ausführen. Geben Sie einen Anzeigenamen für die Anwendung an, außerdem die URI zu einer Seite mit einer Beschreibung der Anwendung (der Link wird nicht geprüft), die URIs, von denen die Anwendung identifiziert wird, und das Kennwort für Ihre Anwendungsidentität.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Die Azure AD-Anwendung wird zurückgegeben. Die ApplicationId-Eigenschaft wird benötigt, um Dienstprinzipale und Rollenzuweisungen zu erstellen und JWT-Token abzurufen.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung. Stellen Sie die Anwendungs-ID bereit, die im vorherigen Schritt zurückgegeben wurde.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Der neue Dienstprinzipal wird zurückgegeben. Die Objekt-ID wird beim Gewähren von Berechtigungen benötigt.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

4. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim Parameter **ServicePrincipalName** entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Managing and Auditing Access to Resources](azure-portal/resource-group-rbac.md) (Verwalten und Überwachen des Zugriffs auf Ressourcen).

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. Ermitteln Sie die **TenantId** des Mandanten, der die Rollenzuweisung des Dienstprinzipals enthält, indem Sie die Konten auflisten und in der Ausgabe nach der Eigenschaft **TenantId** suchen.

        azure account list

6. Melden Sie sich an, und verwenden Sie dabei den Dienstprinzipal als Identität. Verwenden Sie als Benutzernamen die **ApplicationId**, die Sie beim Erstellen der Anwendung verwendet haben. Verwenden Sie das Kennwort, das Sie beim Erstellen des Kontos angegeben haben.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    Die Authentifizierung des Dienstprinzipals für die ADD-Anwendung, die Sie erstellt haben, ist hiermit abgeschlossen.

## Nächste Schritte
  
- Eine Übersicht über die rollenbasierte Zugriffssteuerung finden Sie unter [Verwalten und Überwachen des Zugriffs auf Ressourcen](azure-portal/resource-group-rbac.md).  
- Informationen zum Verwenden des Portals mit Dienstprinzipalen finden Sie unter [Erstellen eines neuen Azure-Dienstprinzipals mit dem Azure-Portal](./resource-group-create-service-principal-portal.md).  
- Anleitungen für die Implementierung von Sicherheitseinstellungen mit dem Azure-Ressourcen-Manager finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=Oct15_HO3-->