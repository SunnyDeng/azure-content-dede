<properties
   pageTitle="Authentifizieren eines Dienstprinzipals mit Azure Resource Manager | Microsoft Azure"
   description="Hierin wird beschrieben, wie einem Dienstprinzipal Zugriffsberechtigungen über die rollenbasierte Zugriffssteuerung gewährt werden und wie der Dienstprinzipal authentifiziert wird. Hierin wird gezeigt, wie diese Aufgaben mit PowerShell und der Azure-Befehlszeilenschnittstelle ausgeführt werden."
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
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager

In diesem Thema erfahren Sie, wie Sie einem Dienstprinzipal (z. B. einem automatisierten Prozess, einer Anwendung oder einem Dienst) den Zugriff auf andere Ressourcen in Ihrem Abonnement gestatten. Mithilfe des Azure-Ressourcen-Managers können Sie die rollenbasierte Zugriffssteuerung verwenden, um einem Dienstprinzipal zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren.

In diesem Thema erfahren Sie, wie Sie Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Windows zum Erstellen einer Anwendung und eines Dienstprinzipals einsetzen. Außerdem erfahren Sie, wie Sie dem Dienstprinzipal eine Rolle zuweisen und sich als Dienstprinzipal authentifizieren. Wenn Azure PowerShell noch nicht installiert ist, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](./powershell-install-configure.md). Wenn noch keine Azure-Befehlszeilenschnittstelle installiert ist, lesen [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md). Informationen über die Verwendung des Portals zum Ausführen dieser Schritte finden Sie unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mithilfe des Portals](resource-group-create-service-principal-portal.md)

## Konzepte
1. Azure Active Directory: Ein Clouddienst zur Identitäts- und Zugriffsverwaltung. Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](active-directory/active-directory-whatis.md).
2. Dienstprinzipal: Eine Instanz einer Anwendung in einem Verzeichnis, für die auf andere Ressourcen zugegriffen werden muss.
3. Active Directory-Anwendung: Verzeichnisdatensatz, mit dem eine Anwendung bei AAD identifiziert wird.

Eine ausführlichere Erläuterung zu Anwendungen und Dienstprinzipalen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory/active-directory-application-objects.md). Weitere Informationen zur Active Directory-Authentifizierung finden Sie unter [Authentifizierungsszenarien für Azure AD](active-directory/active-directory-authentication-scenarios.md).

In diesem Thema werden vier Pfade zum Erstellen einer Active Directory-Anwendung und Authentifizieren dieser Anwendung vorgestellt. Die Pfade basieren darauf, ob Sie ein Kennwort oder Zertifikat für die Authentifizierung nutzen möchten und ob Sie PowerShell oder die Azure-CLI bevorzugen. Dies sind die Pfade:

- [Authentifizierung mit Kennwort – PowerShell](#authenticate-with-password---powershell)
- [Authentifizierung mit Zertifikat – PowerShell](#authenticate-with-certificate---powershell)
- [Authentifizierung mit Kennwort – Azure-CLI](#authenticate-with-password---azure-cli)
- [Authentifizierung mit Zertifikat – Azure-CLI](#authenticate-with-certificate---azure-cli)

## Authentifizierung mit Kennwort – PowerShell

In diesem Abschnitt führen Sie folgende Schritte aus: Erstellen eines Dienstprinzipals für eine Azure Active Directory-Anwendung, Zuweisen einer Rolle zum Dienstprinzipal und Authentifizieren als Dienstprinzipal durch Bereitstellen des Anwendungsbezeichners und -kennworts.

1. Melden Sie sich bei Ihrem Konto an.

        PS C:\> Login-AzureRmAccount

1. Erstellen Sie eine neue Active Directory-Anwendung, indem Sie den Befehl **New-AzureRmADApplication** ausführen. Geben Sie einen Anzeigenamen für die Anwendung an, außerdem die URI zu einer Seite mit einer Beschreibung der Anwendung (der Link wird nicht geprüft), die URIs, von denen die Anwendung identifiziert wird, und das Kennwort für Ihre Anwendungsidentität.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Untersuchen Sie das neue Anwendungsobjekt. Die **ApplicationId**-Eigenschaft wird benötigt, um Dienstprinzipale und Rollenzuweisungen zu erstellen und das Zugriffstoken abzurufen.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung, indem Sie die Anwendungs-ID der Active Directory-Anwendung übergeben.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

3. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim **ServicePrincipalName**-Parameter entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Sie haben eine Active Directory-Anwendung und einen Dienstprinzipal für diese Anwendung erstellt. Sie haben den Dienstprinzipal einer Rolle zugewiesen. Nun müssen Sie sich als Dienstprinzipal anmelden, um Aufgaben als Dienstprinzipal zu erledigen. In diesem Thema werden drei Optionen gezeigt:

- [Manuelles Bereitstellen von Anmeldeinformationen über PowerShell](#manually-provide-credentials-through-powershell)
- [Bereitstellen von Anmeldeinformationen über automatisiertes PowerShell-Skript](#provide-credentials-through-automated-powershell-script)
- [Bereitstellen von Anmeldeinformationen über Code in einer Anwendung](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### Manuelles Bereitstellen von Anmeldeinformationen über PowerShell

Sie können die Anmeldeinformationen für den Dienstprinzipal beim Ausführen bedarfsgesteuerter Skripts oder Befehle manuell bereitstellen.

1. Erstellen Sie ein neues **PSCredential**-Objekt, das Ihre Anmeldeinformationen enthält, indem Sie den Befehl **Get-Credential** ausführen.

        PS C:\> $creds = Get-Credential

2. Sie werden zum Eingeben Ihrer Anmeldeinformationen aufgefordert. Verwenden Sie als Benutzernamen die **ApplicationId** oder die **IdentifierUris**, die Sie beim Erstellen der Anwendung verwendet haben. Verwenden Sie das Kennwort, das Sie beim Erstellen des Kontos angegeben haben.

     ![Anmeldeinformationen eingeben][1]

3. Rufen Sie das Abonnement ab, in dem die Rollenzuweisung erstellt wurde. Dieses Abonnement wird verwendet, um die **TenantId** des Mandanten abzurufen, in dem sich die Rollenzuweisung des Dienstprinzipals befindet.

        PS C:\> $subscription = Get-AzureRmSubscription

     Wenn die Rollenzuweisung in einem anderen Abonnement als dem aktuell ausgewählten erstellt wurde, können Sie den Parameter **SubscriptoinId** oder **SubscriptionName** angeben, um ein anderes Abonnement abzurufen.

4. Melden Sie sich mithilfe des Cmdlets **Login-AzureRmAccount** als Dienstprinzipal ab. Stellen Sie jedoch das „credentials“-Objekt bereit, und geben Sie an, dass dieses Konto ein Dienstprinzipal ist.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

Sie sind nun als Dienstprinzipal für die Active Directory-Anwendung authentifiziert, die Sie erstellt haben.

<a id="provide-credentials-through-automated-powershell-script" />
### Bereitstellen von Anmeldeinformationen über automatisiertes PowerShell-Skript

Dieser Abschnitt zeigt, wie Sie sich als Dienstprinzipal anmelden, ohne die Anmeldeinformationen manuell eingeben zu müssen. Sie müssen das Kennwort nicht manuell bereitstellen, da es aus einem Schlüsseltresor abgerufen wird.

> [AZURE.NOTE] Das direkte Hinzufügen eines Kennworts zu Ihrem PowerShell-Skript ist nicht sicher, da das Kennwort als Text verfügbar gemacht wird. Verwenden Sie stattdessen einen Dienst wie Azure Key Vault zum Speichern des Kennworts, und rufen Sie es beim Ausführen des Skripts ab.

Diese Schritte setzen voraus, dass Sie einen Schlüsseltresor mit einem geheimen Schlüssel eingerichtet haben, in dem das Kennwort gespeichert wird. Informationen zum Bereitstellen eines Schlüsseltresors und geheimen Schlüssels finden Sie unter [Key Vault-Vorlagenformat](). Allgemeine Informationen zu Key Vault finden Sie unter [Erste Schritte mit dem Azure-Schlüsseltresor](./key-vault/key-vault-get-started.md).

1. Rufen Sie Ihr Kennwort (das im nachstehenden Beispiel als geheimer Schlüssel mit dem Namen **appPassword** gespeichert wurde) aus dem Schlüsseltresor ab.

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Rufen Sie Ihre Active Directory-Anwendung ab. Beim Anmelden benötigen Sie die Anwendungs-ID.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. Erstellen Sie ein neues **PSCredential**-Objekt, indem Sie die Anwendungs-ID und das Kennwort als Anmeldeinformationen bereitstellen.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. Rufen Sie das Abonnement ab, in dem die Rollenzuweisung erstellt wurde. Dieses Abonnement wird später verwendet, um die **TenantId** des Mandanten abzurufen, in dem sich die Rollenzuweisung des Dienstprinzipals befindet.

        PS C:\> $subscription = Get-AzureRmSubscription

     Wenn die Rollenzuweisung in einem anderen Abonnement als dem aktuell ausgewählten erstellt wurde, können Sie den Parameter **SubscriptoinId** oder **SubscriptionName** angeben, um ein anderes Abonnement abzurufen.

5. Melden Sie sich mithilfe des Cmdlets **Login-AzureRmAccount** als Dienstprinzipal ab. Stellen Sie jedoch das „credentials“-Objekt bereit, und geben Sie an, dass dieses Konto ein Dienstprinzipal ist.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

Sie sind nun als Dienstprinzipal für die Active Directory-Anwendung authentifiziert, die Sie erstellt haben.

<a id="provide-credentials-through-code-in-an-application" />
### Bereitstellen von Anmeldeinformationen über Code in einer Anwendung

Zur Authentifizierung aus einer .NET-Anwendung heraus fügen Sie folgenden Code ein. Sie benötigen die Anwendungs-ID für die Active Directory-Anwendung, das Kennwort und die Mandanten-ID des Abonnements. Nach Abruf des Zugriffstokens können Sie mit Ressourcen im Abonnement arbeiten.

    public static string GetAccessToken()
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


## Authentifizierung mit Zertifikat – PowerShell

In diesem Abschnitt führen Sie folgende Schritte aus: Erstellen eines Dienstprinzipals für eine Azure Active Directory-Anwendung, Zuweisen einer Rolle zum Dienstprinzipal und Authentifizieren als Dienstprinzipal durch Bereitstellen eines Zertifikats. In diesem Thema wird davon ausgegangen, dass ein Zertifikat für Sie ausgestellt wurde.

Es werden zwei Möglichkeiten zum Arbeiten mit Zertifikaten aufgezeigt: Schlüsselanmeldeinformationen und Schlüsselwerte. Sie können beide Verfahren verwenden.

Zunächst müssen Sie einige Werte in PowerShell festlegen, die Sie später beim Erstellen der Anwendung verwenden.

1. Melden Sie sich bei Ihrem Konto an.

        PS C:\> Login-AzureRmAccount

1. Unabhängig davon, welches Verfahren Sie verwenden möchten, erstellen Sie ein „X509Certificate2“-Objekt anhand Ihres Zertifikats, und rufen Sie den Schlüsselwert ab. Verwenden Sie den Pfad zu Ihrem Zertifikat und das Kennwort für das Zertifikat.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Wenn Sie Schlüsselanmeldeinformationen verwenden, erstellen Sie das Objekt für diese Informationen, und legen Sie den Wert für dieses Objekt auf den `$keyValue`-Wert aus dem vorherigen Schritt fest. Das folgende Beispiel enthält einen Aufruf von `Add-Type` zum Hinzufügen eines Typs aus einer Assembly. Der in dem Beispiel gezeigte Pfad muss Ihrem Pfad ähnlich sein, darf sich aber geringfügig unterscheiden.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
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

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    Mit dem zweiten Beispiel können Sie Schlüsselanmeldeinformationen zuweisen.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    Untersuchen Sie das neue Anwendungsobjekt. Die **ApplicationId**-Eigenschaft wird benötigt, um Dienstprinzipale und Rollenzuweisungen zu erstellen und Zugriffstoken abzurufen.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung, indem Sie die Anwendungs-ID der Active Directory-Anwendung übergeben.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

5. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Geben Sie beim **ServicePrincipalName**-Parameter entweder die **ApplicationId** oder die **IdentifierUris** an, die Sie beim Erstellen der Anwendung verwendet haben. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Sie haben eine Active Directory-Anwendung und einen Dienstprinzipal für diese Anwendung erstellt. Sie haben den Dienstprinzipal einer Rolle zugewiesen. Nun müssen Sie sich als Dienstprinzipal anmelden, um Aufgaben als Dienstprinzipal zu erledigen. In diesem Thema werden zwei Optionen gezeigt:

- [Bereitstellen eines Zertifikats über automatisiertes PowerShell-Skript](#provide-certificate-through-automated-powershell-script)
- [Bereitstellen eines Zertifikats über Code in einer Anwendung](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### Bereitstellen eines Zertifikats über automatisiertes PowerShell-Skript

1. Rufen Sie Ihre Active Directory-Anwendung ab. Beim Anmelden benötigen Sie die Anwendungs-ID.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. Rufen Sie das Abonnement ab, in dem die Rollenzuweisung erstellt wurde. Dieses Abonnement wird später verwendet, um die **TenantId** des Mandanten abzurufen, in dem sich die Rollenzuweisung des Dienstprinzipals befindet.

        PS C:\> $subscription = Get-AzureRmSubscription

     Wenn die Rollenzuweisung in einem anderen Abonnement als dem aktuell ausgewählten erstellt wurde, können Sie den Parameter **SubscriptoinId** oder **SubscriptionName** angeben, um ein anderes Abonnement abzurufen.

3. Rufen Sie das Zertifikat ab, das Sie für die Authentifizierung verwenden möchten.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. Geben Sie für die Authentifizierung in PowerShell den Zertifikatfingerabdruck, die Anwendungs-ID und die Mandanten-ID an.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

Sie sind nun als Dienstprinzipal für die Active Directory-Anwendung authentifiziert, die Sie erstellt haben.

<a id="provide-certificate-through-code-in-an-application" />
### Bereitstellen eines Zertifikats über Code in einer Anwendung

Zur Authentifizierung aus einer .NET-Anwendung heraus fügen Sie folgenden Code ein. Nach dem Abrufen des Clients können Sie auf Ressourcen im Abonnement zugreifen.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
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
        

## Authentifizierung mit Kennwort – Azure-CLI

Zunächst wird ein Dienstprinzipal erstellt. Dazu muss im Verzeichnis eine Anwendung erstellt werden. Das Erstellen einer neuen Anwendung im Verzeichnis wird in diesem Abschnitt erläutert.

1. Wechseln Sie in den Azure-Ressourcen-Manager-Modus, und melden Sie sich bei Ihrem Konto an.

        azure config mode arm
        azure login

2. Erstellen Sie eine neue Active Directory-Anwendung, indem Sie den Befehl **azure ad app create** ausführen. Geben Sie einen Anzeigenamen für die Anwendung an, außerdem die URI zu einer Seite mit einer Beschreibung der Anwendung (der Link wird nicht geprüft), die URIs, von denen die Anwendung identifiziert wird, und das Kennwort für Ihre Anwendungsidentität.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Die Active Directory-Anwendung wird zurückgegeben. Die „AppId“-Eigenschaft wird benötigt, um Dienstprinzipale und Rollenzuweisungen zu erstellen und Zugriffstoken abzurufen.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung. Stellen Sie die Anwendungs-ID bereit, die im vorherigen Schritt zurückgegeben wurde.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Der neue Dienstprinzipal wird zurückgegeben. Die Objekt-ID wird beim Gewähren von Berechtigungen benötigt.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    Sie haben nun einen Dienstprinzipal im Verzeichnis erstellt. Dieser Dienstprinzipal verfügt jedoch noch nicht über Berechtigungen, und ihm wurde auch noch kein Bereich zugewiesen. Sie müssen dem Dienstprinzipal ausdrücklich die Berechtigungen zum Ausführen von Aktionen in einem bestimmten Bereich gewähren.

4. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Sie haben eine Active Directory-Anwendung und einen Dienstprinzipal für diese Anwendung erstellt. Sie haben den Dienstprinzipal einer Rolle zugewiesen. Nun müssen Sie sich als Dienstprinzipal anmelden, um Aufgaben als Dienstprinzipal zu erledigen. In diesem Thema werden drei Optionen gezeigt:

- [Manuelles Bereitstellen von Anmeldeinformationen über Azure-CLI](#manually-provide-credentials-through-azure-cli)
- [Bereitstellen von Anmeldeinformationen über automatisiertes Azure-CLI-Skript](#provide-credentials-through-automated-azure-cli-script)
- [Bereitstellen von Anmeldeinformationen über Code in einer Anwendung](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Manuelles Bereitstellen von Anmeldeinformationen über Azure-CLI

Wenn Sie sich als Dienstprinzipal manuell anmelden möchten, können Sie den Befehl **azure login** ausführen. Sie müssen die Mandanten-ID, Anwendungs-ID und das Kennwort angeben. Das direkte Hinzufügen eines Kennworts zu einem Skript ist nicht sicher, da das Kennwort in der Datei gespeichert wird. Im nächsten Abschnitt wird eine bessere Möglichkeit für die Ausführung eines automatisierten Skripts vorgestellt.

1. Bestimmen Sie die **TenantId** des Abonnements, das den Dienstprinzipal enthält. Wenn Sie die Mandanten-ID für das derzeit authentifizierte Abonnement abrufen, müssen Sie nicht die Abonnement-ID als Parameter angeben. Mit dem Switch **-r** wird der Wert ohne Anführungszeichen abgerufen.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Verwenden Sie als Benutzernamen die **AppId**, die Sie beim Erstellen des Dienstprinzipals verwendet haben. Wenn Sie die Anwendungs-ID abrufen müssen, führen Sie den folgenden Befehl aus. Geben Sie den Namen der Active Directory-Anwendung im **search**-Parameter an.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Melden Sie sich als Dienstprinzipal an.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

Geben Sie bei Aufforderung das Kennwort ein, das Sie beim Erstellen des Kontos angegeben haben.

    info:    Executing command login
    Password: ********

Sie sind nun als Dienstprinzipal für die Active Directory-Anwendung authentifiziert, die Sie erstellt haben.

<a id="provide-credentials-through-automated-azure-cli-script" />
### Bereitstellen von Anmeldeinformationen über automatisiertes Azure-CLI-Skript

Dieser Abschnitt zeigt, wie Sie sich als Dienstprinzipal anmelden, ohne die Anmeldeinformationen manuell eingeben zu müssen.

> [AZURE.NOTE] Das direkte Hinzufügen eines Kennworts zu Ihrer Azure-CLI ist nicht sicher, da das Kennwort als Text verfügbar gemacht wird. Verwenden Sie stattdessen einen Dienst wie Azure Key Vault zum Speichern des Kennworts, und rufen Sie es beim Ausführen des Skripts ab.

Diese Schritte setzen voraus, dass Sie einen Schlüsseltresor mit einem geheimen Schlüssel eingerichtet haben, in dem das Kennwort gespeichert wird. Informationen zum Bereitstellen eines Schlüsseltresors und geheimen Schlüssels finden Sie unter [Key Vault-Vorlagenformat](). Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit dem Azure-Schlüsseltresor](./key-vault/key-vault-get-started.md).

1. Rufen Sie Ihr Kennwort (das im nachstehenden Beispiel als geheimer Schlüssel mit dem Namen **appPassword** gespeichert wurde) aus dem Schlüsseltresor ab. Fügen Sie den Switch **-r** ein, um die öffnenden und schließenden doppelten Anführungszeichen zu entfernen, die von der JSON-Ausgabe zurückgegeben werden.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq -r '.value')
    
2. Bestimmen Sie die **TenantId** des Abonnements, das den Dienstprinzipal enthält. Wenn Sie die Mandanten-ID für das derzeit authentifizierte Abonnement abrufen, müssen Sie nicht die Abonnement-ID als Parameter angeben.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. Verwenden Sie als Benutzernamen die **AppId**, die Sie beim Erstellen des Dienstprinzipals verwendet haben. Wenn Sie die Anwendungs-ID abrufen müssen, führen Sie den folgenden Befehl aus. Geben Sie den Namen der Active Directory-Anwendung im **search**-Parameter an.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Melden Sie sich als Dienstprinzipal an, indem Sie die Anwendungs-ID, das Kennwort aus dem Schlüsseltresor und die Mandanten-ID angeben.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
Sie sind nun als Dienstprinzipal für die Active Directory-Anwendung authentifiziert, die Sie erstellt haben.

<a id="provide-credentials-through-code-in-an-application-cli" />
### Bereitstellen von Anmeldeinformationen über Code in einer Anwendung

Zur Authentifizierung aus einer .NET-Anwendung heraus fügen Sie folgenden Code ein. Sie benötigen die Anwendungs-ID für die Active Directory-Anwendung, das Kennwort und die Mandanten-ID des Abonnements. Nach Abruf des Zugriffstokens können Sie mit Ressourcen im Abonnement arbeiten.

    public static string GetAccessToken()
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

## Authentifizierung mit Zertifikat – Azure-CLI

In diesem Abschnitt führen Sie die Schritte zum Erstellen eines Dienstprinzipals für eine Azure Active Directory-Anwendung aus, die ein Zertifikat zur Authentifizierung verwendet. In diesem Thema wird davon ausgegangen, dass ein Zertifikat für Sie ausgestellt wurde und dass Sie [OpenSSL](http://www.openssl.org/) installiert haben.

1. Erstellen Sie eine **PEM**-Datei mit:

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. Öffnen Sie die **PEM** Datei, und kopieren Sie die Zertifikatdaten. Suchen Sie die lange Zeichenfolge zwischen **-----BEGIN CERTIFICATE-----** und **-----END CERTIFICATE-----**.

3. Erstellen Sie eine neue Active Directory-Anwendung durch Ausführen des Befehls **azure ad app create**, und geben Sie die Zertifikatdaten, die Sie im vorherigen Schritt kopiert haben, als Schlüsselwert an.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Die Active Directory-Anwendung wird zurückgegeben. Die „AppId“-Eigenschaft wird benötigt, um Dienstprinzipale und Rollenzuweisungen zu erstellen und Zugriffstoken abzurufen.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. Erstellen Sie einen Dienstprinzipal für Ihre Anwendung. Stellen Sie die Anwendungs-ID bereit, die im vorherigen Schritt zurückgegeben wurde.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Der neue Dienstprinzipal wird zurückgegeben. Die Objekt-ID wird beim Gewähren von Berechtigungen benötigt.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. Gewähren Sie dem Dienstprinzipal Berechtigungen für Ihr Abonnement. In diesem Beispiel wird dem Dienstprinzipal die Berechtigung zum Lesen aller Ressourcen im Abonnement gewährt. Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie unter [Rollenbasierte Access Control in Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Sie haben eine Active Directory-Anwendung und einen Dienstprinzipal für diese Anwendung erstellt. Sie haben den Dienstprinzipal einer Rolle zugewiesen. Nun müssen Sie sich als Dienstprinzipal anmelden, um Aufgaben als Dienstprinzipal zu erledigen. In diesem Thema werden zwei Optionen gezeigt:

- [Bereitstellen eines Zertifikats über automatisiertes Azure-CLI-Skript](#provide-certificate-through-automated-azure-cli-script)
- [Bereitstellen eines Zertifikats über Code in einer Anwendung](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### Bereitstellen eines Zertifikats über automatisiertes Azure-CLI-Skript

1. Sie müssen den Zertifikatfingerabdruck abrufen und nicht benötigte Zeichen entfernen.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     Ein Fingerabdruck wie der folgende wird zurückgegeben:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Bestimmen Sie die **TenantId** des Abonnements, das den Dienstprinzipal enthält. Wenn Sie die Mandanten-ID für das derzeit authentifizierte Abonnement abrufen, müssen Sie nicht die Abonnement-ID als Parameter angeben. Mit dem Switch **-r** wird der Wert ohne Anführungszeichen abgerufen.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. Verwenden Sie als Benutzernamen die **AppId**, die Sie beim Erstellen des Dienstprinzipals verwendet haben. Wenn Sie die Anwendungs-ID abrufen müssen, führen Sie den folgenden Befehl aus. Geben Sie den Namen der Active Directory-Anwendung im **search**-Parameter an.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Geben Sie für die Authentifizierung mit Azure-CLI den Zertifikatfingerabdruck, die Zertifikatdatei, Anwendungs-ID und Mandanten-ID an.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

Sie sind nun als Dienstprinzipal für die Active Directory-Anwendung authentifiziert, die Sie erstellt haben.

<a id="provide-certificate-through-code-in-an-application-cli" />
### Bereitstellen eines Zertifikats über Code in einer Anwendung

Zur Authentifizierung aus einer .NET-Anwendung heraus fügen Sie folgenden Code ein. Nach dem Abrufen des Clients können Sie auf Ressourcen im Abonnement zugreifen.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
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
       
Weitere Informationen zur Verwendung von Zertifikaten und der Azure-Befehlszeilenschnittstelle finden Sie unter [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Zertifikatbasierte Authentifizierung mit Azure-Dienstprinzipalen über Linux-Befehlszeile).

## Nächste Schritte
  
- Informationen zum Verwenden des Portals mit Dienstprinzipalen finden Sie unter [Erstellen eines neuen Azure-Dienstprinzipals mit dem Azure-Portal](./resource-group-create-service-principal-portal.md).  
- Anleitungen für die Implementierung von Sicherheitseinstellungen mit dem Azure-Ressourcen-Manager finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0323_2016-->