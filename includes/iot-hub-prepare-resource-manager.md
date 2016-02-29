## Vorbereiten der Authentifizierung von Anforderungen des Ressourcen-Managers

Sie müssen alle Vorgänge authentifizieren, die Sie mithilfe des [Azure-Ressourcen-Managers][lnk-authenticate-arm] mit Azure Active Directory (AD) für die Ressourcen ausführen. Die einfachste Möglichkeit hierzu ist die Verwendung von PowerShell oder der Azure-Befehlszeilenschnittstelle.

Installieren Sie [Azure PowerShell 1.0][lnk-powershell-install] oder höher, bevor Sie fortfahren.

Die folgenden Schritte zeigen, wie Sie die Kennwortauthentifizierung für eine AD-Anwendung mithilfe von PowerShell einrichten. Sie können diese Befehle in einer standardmäßigen PowerShell-Sitzung ausführen.

1. Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Abonnement an:

    ```
    Login-AzureRmAccount
    ```

2. Notieren Sie sich Ihre **TenantId** und **SubscriptionId**. Diese werden später benötigt werden.

3. Erstellen Sie mit dem folgenden Befehl eine neue Azure Active Directory-Anwendung, und ersetzen Sie die Platzhalter:

    - **{Display name}:** Ein Anzeigename für Ihre Anwendung, z. B. **MySampleApp**.
    - **{Home page URL}:** Die URL der Startseite der App, z. B. ****http://mysampleapp/home**. Diese URL muss nicht auf eine echte Anwendung verweisen.
    - **{Application identifier}:** Ein eindeutiger Bezeichner, z. B. ****http://mysampleapp**. Diese URL muss nicht auf eine echte Anwendung verweisen.
    - **{Password}:** Ein Kennwort, das Sie zur Authentifizierung mit Ihrer Anwendung verwenden werden.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Notieren Sie sich die **ApplicationId** der Anwendung, die Sie erstellt haben. Sie benötigen sie später.

5. Erstellen Sie einen neuen Dienstprinzipal mithilfe des folgenden Befehls, und ersetzen Sie **{MyApplicationId}** durch die **ApplicationId** aus dem vorherigen Schritt:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Richten Sie eine Rollenzuweisung mithilfe des folgenden Befehls ein, und ersetzen Sie **{MyApplicationId}** durch Ihre **ApplicationId**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Sie haben jetzt eine Azure AD-Anwendung fertig erstellt, mit der die Authentifizierung aus einer benutzerdefinierten C#-Anwendung möglich ist. Die folgenden Werte werden Sie später in diesem Tutorial benötigen:

- TenantId
- SubscriptionId
- ApplicationId
- Kennwort

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0218_2016-->