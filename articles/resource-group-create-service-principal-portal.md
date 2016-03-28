<properties
   pageTitle="Erstellen einer AD-Anwendung und eines Dienstprinzipals im Portal | Microsoft Azure"
   description="Beschreibt das Erstellen einer neuen Active Directory-Anwendung und eines Dienstprinzipals, der mit der rollenbasierten Zugriffskontrolle in Azure-Ressourcen-Manager zum Verwalten des Zugriffs auf Ressourcen verwendet werden kann."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mithilfe des Portals

## Übersicht
Bei Verwendung eines automatisierten Prozesses oder einer automatisierten Anwendung, der bzw. die auf Ressourcen zugreifen oder diese ändern muss, können Sie im klassischen Portal eine Active Directory-Anwendung erstellen. Wenn Sie eine Active Directory-Anwendung über das klassische Portal erstellen, wird tatsächlich sowohl die Anwendung als auch ein Dienstprinzipal erstellt. Sie können die Anwendung unter ihrer eigenen Identität oder unter der Identität des angemeldeten Benutzers Ihrer Anwendung ausführen. Diese beiden Methoden zur Authentifizierung von Anwendungen werden als interaktiv (Anmeldung durch Benutzer) und nicht interaktiv (Bereitstellung eigener Anmeldeinformationen durch die App ) bezeichnet. Im nicht interaktiven Modus müssen Sie den Dienstprinzipal einer Rolle mit den richtigen Berechtigungen zuweisen.

In diesem Thema wird erklärt, wie eine neue Anwendung und ein neuer Dienstprinzipal mithilfe des klassischen Portals erstellt werden. Derzeit müssen Sie das klassische Portal zum Erstellen einer neuen Active Directory-Anwendung verwenden. Das Azure-Portal wird in einer späteren Version um diese Möglichkeit erweitert. Im Portal können Sie die Anwendung einer Rolle zuweisen. Sie können diese Schritte auch über Azure PowerShell oder Azure CLI ausführen. Informationen zur Verwendung von PowerShell oder CLI mit dem Dienstprinzipal finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md).

## Konzepte
1. Azure Active Directory (AAD) - ein Cloud-Dienst für Identitäts- und Zugriffsverwaltung. Weitere Informationen finden Sie unter [Was ist Azure Active Directory](active-directory/active-directory-whatis.md)
2. Dienstprinzipal - ein Anwendungsbeispiel in einem Verzeichnis.
3. AD-Anwendung - ein Verzeichnisdatensatz im AAD, der eine Anwendung für AAD identifiziert. 

Eine ausführlichere Erläuterung zu Anwendungen und Dienstprinzipalen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory/active-directory-application-objects.md). Weitere Informationen zur Active Directory-Authentifizierung finden Sie unter [Authentifizierungsszenarien für Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Erstellen der Anwendung

Für interaktive und nicht interaktive Anwendungen müssen sie Ihre Active Directory-Anwendung erstellen und konfigurieren.

1. Melden Sie sich über das [klassische Portal](https://manage.windowsazure.com/) bei Ihrem Azure-Konto an.

2. Wählen Sie im linken Bereich **Active Directory** aus.

     ![Active Directory auswählen][1]

3. Wählen Sie das Verzeichnis aus, das Sie zum Erstellen der neuen Anwendung nutzen möchten.

     ![Verzeichnis wählen][2]

3. Klicken Sie auf **Anwendungen**, um die Anwendungen in Ihrem Verzeichnis anzuzeigen.

     ![Anwendungen anzeigen][11]

4. Wenn Sie in diesem Verzeichnis zuvor noch keine Anwendung erstellt haben, sollten Sie eine Abbildung ähnlich dieser hier sehen. Klicken Sie auf **EINE ANWENDUNG HINZUFÜGEN**

     ![Anwendung hinzufügen][6]

     Oder klicken Sie im unteren Bereich auf **Hinzufügen**.

     ![Hinzufügen][12]

5. Wählen Sie den Anwendungstypen aus, den Sie erstellen möchten. Für dieses Lernprogramm wird keine Anwendung aus der Galerie verwendet.

     ![neue Anwendung][10]

6. Füllen Sie den Namen der Anwendung aus und wählen Sie den Anwendungstyp aus, den Sie nutzen möchten. Wählen Sie den Anwendungstyp aus, den Sie erstellen. In diesem Tutorial erstellen wir eine **WEBANWENDUNG UND/ODER WEB-API**, und klicken auf die Schaltfläche „Weiter“.

     ![Anwendung benennen][9]

7. Tragen Sie die Eigenschaften Ihrer Anwendung ein. Geben Sie für die **ANMELDE-URL** die URI einer Website an, die Ihre Anwendung beschreibt. Das Vorhandensein der Website wird nicht überprüft. Geben Sie für die **APP-ID URI ** die URI an, die Ihre Anwendung identifiziert. Die Eindeutigkeit oder das Vorhandensein des Endpunktes wird nicht überprüft. Wenn Sie als Anwendungstyp **Native Clientanwendung** ausgewählt haben, geben Sie einen Wert für **Umleitungs-URI** an. Klicken Sie auf **Abschließen**, um Ihre AAD-Anwendung zu erstellen.

     ![Anwendungseigenschaften][4]

Sie haben Ihre Anwendung erstellt.

## Abrufen von Client-ID und Mandanten-ID

Beim programmgesteuerten Zugriff auf Ihre Anwendung benötigen Sie die ID für Ihre Anwendung. Wählen Sie die Registerkarte **Konfigurieren**, und kopieren Sie die **CLIENT-ID**.
  
   ![Client-ID][5]

In einigen Fällen müssen Sie bei Ihrer Authentifizierungsanforderung die Mandanten-ID übergeben. Bei Web-Apps und Web-API-Apps können Sie die Mandanten-ID abrufen, indem Sie unten auf der Seite **Endpunkte anzeigen** auswählen und die ID wie unten dargestellt abrufen.

   ![Mandanten-ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

Endpunkte sind nicht für native Clientanwendungen verfügbar. Stattdessen können Sie die Mandanten-ID über PowerShell abrufen:

    PS C:\> Get-AzureRmSubscription

Oder mit der Azure-Befehlszeilenschnittstelle:

    azure account show --json

## Erstellen eines Authentifizierungsschlüssels

Wenn Ihre Anwendung mit eigenen Anmeldeinformationen ausgeführt wird, müssen Sie einen Schlüssel für die Anwendung erstellen.

1. Klicken Sie auf die Registrierkarte **Konfigurieren**, um das Kennwort für Ihre Anwendung zu konfigurieren.

     ![Anwendung konfigurieren][3]

2. Scrollen Sie herunter zum Abschnitt **Schlüssel** und wählen Sie den gewünschten Gültigkeitszeitraum für Ihr Kennwort aus.

     ![Schlüssel][7]

3. Wählen Sie **Speichern** aus, um Ihren Schlüssel zu erstellen.

     ![speichern][13]

     Der gespeicherte Schlüssel wird angezeigt, sodass Sie ihn kopieren können. Sie können den Schlüssel später nicht mehr abrufen. Sie sollten ihn also jetzt kopieren.

     ![gespeicherter Schlüssel][8]

Ihre Anwendung ist nun bereit und das Dienstprinzipal in Ihrem Mandanten erstellt. Wenn Sie sich als Dienstprinzipal anmelden, achten Sie auf die Nutzung von:

* **CLIENT-ID** - als Ihren Benutzername.
* **SCHLÜSSEL** - als Ihr Kennwort.

## Festlegen der delegierten Berechtigungen

Wenn Ihre Anwendung im Auftrag eines angemeldeten Benutzers auf Ressourcen zugreift, müssen Sie der Anwendung die delegierten Berechtigungen für den Zugriff auf andere Anwendungen gewähren. Dieser Schritt wird auf der Registerkarte **Konfigurieren** im Abschnitt **Berechtigungen für andere Anwendungen** ausgeführt. Eine delegierte Berechtigung ist standardmäßig bereits für die Azure Active Directory-Instanz aktiviert. Lassen Sie diese delegierte Berechtigung unverändert.

1. Wählen Sie **Anwendung hinzufügen**.

2. Wählen Sie in der Liste die **Azure-Dienstverwaltungs-API** aus.

      ![App auswählen](./media/resource-group-create-service-principal-portal/select-app.png)

3. Fügen Sie der Dienstverwaltungs-API die delegierte Berechtigung **Auf Azure-Service-Verwaltung zugreifen (Vorschau)** hinzu.

       ![Berechtigung auswählen](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Speichern Sie die Änderungen.

## Konfigurieren einer mehrinstanzenfähigen Anwendung

Wenn Benutzer aus anderen Azure Active Directory-Instanzen ihre Zustimmung für die Anwendung geben und sich bei ihr anmelden können, müssen Sie die Mehrinstanzenfähigkeit aktivieren. Legen Sie auf der Registerkarte **Konfigurieren** für **Anwendung ist mehrinstanzenfähig** die Option **Ja** fest.

![mehrinstanzenfähig](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Zuweisen einer Anwendung zur Rolle

Wenn Ihre Anwendung nicht unter der Identität eines angemeldeten Benutzers ausgeführt wird, müssen Sie die Anwendung einer Rolle zuweisen, um ihr Berechtigungen zum Ausführen bestimmter Aktionen zu gewähren. Zum Zuweisen der Anwendung an eine Rolle wechseln Sie vom klassischen Portal zum [Azure-Portal](https://portal.azure.com). Sie müssen entscheiden, welcher Rolle die Anwendung in welchem Umfang hinzugefügt wird. Informationen zu verfügbaren Rollen finden Sie unter [RBAC: Integrierte Rollen](./active-directory/role-based-access-built-in-roles.md). Sie können den Umfang auf Abonnement-, Ressourcengruppen- oder Ressourcenebene festlegen. Die Berechtigungen werden in niedrigere Umfangsebenen übernommen (wird der Leserrolle für eine Ressourcengruppe beispielsweise eine Anwendung hinzugefügt, kann sie die Ressourcengruppe und alle darin enthaltenen Ressourcen lesen).

1. Navigieren Sie im Portal zur Umfangsebenene, der Sie die Anwendung zuweisen möchten. Für dieses Thema können Sie zu einer Ressourcengruppe navigieren und auf dem Blatt der Ressourcengruppe das Symbol **Zugriff** wählen.

     ![Benutzer auswählen](./media/resource-group-create-service-principal-portal/select-users.png)

2. Wählen Sie **Hinzufügen**.

     ![„Hinzufügen“ wählen](./media/resource-group-create-service-principal-portal/select-add.png)

3. Wählen Sie die **Leser**-Rolle (bzw. die Rolle, der die Anwendung zugewiesen werden soll).

     ![Rolle wählen](./media/resource-group-create-service-principal-portal/select-role.png)

4. Beim ersten Anzeigen der Liste mit Benutzern, die Sie der Rolle hinzufügen können, werden keine Anwendungen angezeigt. Nur Gruppen und Benutzer werden angezeigt.

     ![Benutzer anzeigen](./media/resource-group-create-service-principal-portal/show-users.png)

5. Sie müssen nach Ihrer Anwendung suchen. Geben Sie den Anfang des Namens Ihrer Anwendung ein, und die Liste der verfügbaren Optionen ändert sich. Wählen Sie Ihre Anwendung aus, wenn Sie in der Liste angezeigt wird.

     ![An Rolle zuweisen](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Wählen Sie **OK**, um das Zuweisen der Rolle abzuschließen. Jetzt sollten Sie Ihre Anwendung in der Liste der Benutzer sehen, die einer Rolle für die Ressourcengruppe zugewiesen sind.

     ![einblenden](./media/resource-group-create-service-principal-portal/show-app.png)

Weitere Informationen zum Zuweisen von Benutzern und Anwendungen zu Rollen über das Portal finden Sie unter [Verwalten des Zugriffs über das Azure-Verwaltungsportal](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Abrufen des Zugriffstokens mit Code

Wenn Sie mit .NET arbeiten, können Sie das Zugriffstoken für Ihre Anwendung mit dem folgenden Code abrufen.

Zunächst müssen Sie die Active Directory-Authentifizierungsbibliothek in Ihrem Visual Studio-Projekt installieren. Die einfachste Möglichkeit hierfür ist das NuGet-Paket. Öffnen Sie die Paket-Manager-Konsole, und geben Sie die folgenden Befehle ein.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

Wenn Sie sich mit der Client-ID und dem geheimen Schlüssel anmelden möchten, rufen Sie das Token mithilfe der folgenden Methode ab:

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Wenn Sie sich im Auftrag des Benutzers anmelden möchten, rufen Sie das Token mithilfe der folgenden Methode ab:

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

Sie können das Token im Anforderungsheader mit dem folgenden Code übergeben:

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## Nächste Schritte

- Informationen zum Festlegen von Sicherheitsrichtlinien finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](./active-directory/role-based-access-control-configure.md).  
- Eine Videodemo dieser Schritte finden Sie unter [Aktivieren der programmgesteuerten Verwaltung einer Azure-Ressource mit Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Informationen zur Verwendung von Azure PowerShell oder der Azure-Befehlszeilenschnittstelle zum Arbeiten mit Active Directory-Anwendungen und Dienstprinzipalen, einschließlich der Verwendung eines Zertifikats für die Authentifizierung, finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](./resource-group-authenticate-service-principal.md).
- Anleitungen für die Implementierung von Sicherheitseinstellungen mit dem Azure-Ressourcen-Manager finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0316_2016-->