<properties
	pageTitle="Dienstprinzipalauthentifizierung für API-Apps in Azure App Service | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine API-App in Azure App Service für Dienst-zu-Dienst-Szenarien schützen."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016" 
	ms.author="tdykstra"/>

# Dienstprinzipalauthentifizierung für API-Apps in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Artikel lernen Sie Folgendes:

* Verwenden von Azure Active Directory (Azure AD) zum Schützen einer API-App vor Zugriff ohne Authentifizierung
* Nutzen einer geschützten API-App mit Dienstprinzipal-Anmeldeinformationen (App-Identität)
* Sicherstellen, dass die geschützte API-App von angemeldeten Benutzern nicht aufgerufen werden kann
* Sicherstellen, dass die geschützte API-App nur über einen speziellen Azure AD-Dienstprinzipal aufgerufen werden kann

Diese Methode zum Schützen einer API-App wird häufig für [interne Szenarien](app-service-api-authentication.md#internal) verwendet, z. B. für einen Aufruf einer API-App von einer anderen API-App.

Der Artikel enthält zwei Abschnitte:

* Der Abschnitt [Gewusst wie: Konfigurieren der Dienstprinzipalauthentifizierung in Azure App Service](#authconfig) enthält eine allgemeine Beschreibung, wie Sie die Authentifizierung für eine API-App konfigurieren und die geschützte API-App nutzen. Dieser Abschnitt gilt für alle Frameworks, die von App Service unterstützt werden, z. B. .NET, Node.js und Java.

* Im [weiteren Verlauf des Artikels](#tutorialstart) werden Sie durch die Konfiguration eines Szenarios vom Typ „interner Zugriff“ für eine .NET-Beispielanwendung geführt, die in App Service ausgeführt wird.

## <a id="authconfig"></a> Konfigurieren der Dienstprinzipalauthentifizierung in Azure App Service

Dieser Abschnitt enthält allgemeine Anweisungen, die für alle API-Apps gelten. Die speziellen Schritte für die .NET-Beispielanwendung (Aufgabenliste) sind in [Fortsetzen der „Erste Schritte“-Tutorials für .NET](#tutorialstart) enthalten.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt **API-App** der API-App, die Sie schützen möchten, und klicken Sie dann auf **Einstellungen**.

2. Suchen Sie auf dem Blatt **Einstellungen** nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

5. Wählen Sie unter **Authentifizierungsanbieter** die Option **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Konfigurieren Sie das Blatt **Azure Active Directory-Einstellungen**, um eine neue Azure AD-Anwendung zu erstellen, oder verwenden Sie eine vorhandene Azure AD-Anwendung, falls Sie bereits über eine geeignete Anwendung verfügen.

	Interne Szenarien umfassen normalerweise eine API-App, die eine andere API-App aufruft. Sie können separate Azure AD-Anwendungen für jede API-App oder nur eine Azure AD-Anwendung verwenden.

	Ausführliche Informationen zu diesem Blatt finden Sie unter [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Wenn Sie mit dem Blatt für die Konfiguration des Authentifizierungsanbieters fertig sind, klicken Sie auf **OK**.

7. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.

Wenn dies konfiguriert wird, wird von App Service verhindert, dass nicht authentifizierte API-Aufrufe die API-App erreichen. In der geschützten API-App ist kein Authentifizierungs- oder Autorisierungscode erforderlich.

Diese Authentifizierungsfunktion funktioniert für alle von App Service unterstützten Sprachen gleich, z. B. .NET, Node.js und Java.

#### Nutzen der geschützten API-App

Der Aufrufer muss ein Azure AD-Bearertoken mit API-Aufrufen bereitstellen. Zum Abrufen eines Bearertokens mit Dienstprinzipal-Anmeldeinformationen verwendet der Aufrufer die Active Directory Authentication Library (ADAL für [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) oder [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Der Code, mit dem die ADAL aufgerufen wird, stellt für die ADAL die folgenden Informationen bereit, um ein Token zu erhalten:

* Name des Azure AD-Mandanten
* Client-ID und geheimer Clientschlüssel (App-Schlüssel) der Azure AD-App, die dem Aufrufer zugeordnet ist
* Client-ID der Azure AD-Anwendung, die der projizierten API-App zugeordnet ist (Wenn nur eine Azure AD-Anwendung verwendet wird, ist dies dieselbe Client-ID wie für den Aufrufer.)

Diese Werte sind auf den Azure AD-Seiten des [klassischen Azure-Portals](https://manage.windowsazure.com/) verfügbar.

Nachdem das Token abgerufen wurde, fügt der Aufrufer es mit HTTP-Anforderungen in der Autorisierungsheader ein. App Service überprüft das Token und lässt es zu, dass die Anforderungen die geschützte API-App erreichen.

#### Schützen der API-App vor dem Zugriff von Benutzern in demselben Mandanten

Bearertoken für Benutzer in demselben Mandanten werden für die geschützte API-App als gültig angesehen. Wenn Sie sicherstellen möchten, dass nur ein Dienstprinzipal die geschützte API-App aufrufen kann, können Sie der App Code hinzufügen, mit dem die folgenden Ansprüche überprüft werden:

* `appid` sollte der Client-ID der Azure AD-Anwendung entsprechen, die dem Aufrufer zugeordnet ist.
* `objectidentifier` sollte die Dienstprinzipal-ID des Aufrufers sein.

### Schützen der API-App vor Browserzugriff

Wenn Sie Ansprüche im Code der geschützten API-App nicht überprüfen und eine separate Azure AD-Anwendung für die geschützte API-App verwenden, sollten Sie sicherstellen, dass sich die Antwort-URL der Azure AD-Anwendung von der Basis-URL der API-App unterscheidet. Wenn die Antwort-URL direkt auf die geschützte API-App verweist, kann ein Benutzer in demselben Azure AD-Mandanten zur API-App navigieren, sich anmelden und einen erfolgreichen Aufruf der API durchführen.

## <a id="tutorialstart"></a> Fortsetzen der Erste-Schritte-Tutorials für .NET

Falls Sie die API-App-Reihe „Erste Schritte“ für Node.js oder Java durcharbeiten, können Sie mit dem Abschnitt [Nächste Schritte](#next-steps) fortfahren.

Im weiteren Verlauf dieses Artikels wird die .NET-Reihe „Erste Schritte“ für API-Apps fortgesetzt und davon ausgegangen, dass Sie das [Tutorial zur Benutzerauthentifizierung](app-service-api-user-principal-authentication.md) abgeschlossen haben und dass die Beispielanwendung in Azure mit aktivierter Benutzerauthentifizierung ausgeführt wird.

## Einrichten der Authentifizierung in Azure

In diesem Abschnitt konfigurieren Sie App Service so, dass nur die HTTP-Anforderungen die API-App der Datenebene erreichen können, die über gültige Azure AD-Bearertoken verfügen.

Im folgenden Abschnitt konfigurieren Sie die API-App der mittleren Ebene für das Senden von Anmeldeinformationen einer Anwendung an Azure AD, das Zurückerhalten eines Bearertokens und das Senden des Bearertokens an die API-App der Datenebene. Dieser Prozess ist in der Abbildung dargestellt.

![](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt „API-App“ der API-App, die Sie für die API-App ToDoListDataAPI (Datenebene) erstellt haben. Klicken Sie anschließend auf **Einstellungen**.

2. Suchen Sie auf dem Blatt **Einstellungen** nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

	Dies ist die Einstellung, mit der App Service sicherstellen kann, dass nur authentifizierte Anforderungen die API-App erreichen. Für Anforderungen mit gültigen Bearertoken übergibt App Service die Token an die API-App und füllt HTTP-Header mit häufig verwendeten Ansprüchen auf, damit diese Informationen für Ihren Code besser verfügbar sind.

5. Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**.

6. Klicken Sie auf dem Blatt **Azure Active Directory-Einstellungen** auf **Express**.

	Mit der Option **Express** kann von Azure automatisch eine AAD-Anwendung in Ihrem Azure AD-[Mandanten](https://msdn.microsoft.com/de-DE/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) erstellt werden.

	Sie müssen keinen Mandanten erstellen, da jedes Azure-Konto automatisch darüber verfügt.

7. Klicken Sie unter **Verwaltungsmodus** auf **Neue AD-App erstellen**.

	Das Portal fügt einen Standardwert in das Eingabefeld **App erstellen** ein.
	
	![](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	Standardmäßig hat die Azure AD-Anwendung den gleichen Namen wie die API-App. Wenn Sie möchten, können Sie auch einen anderen Namen eingeben.

	Alternativ können Sie auch nur eine Azure AD-Anwendung zum Aufrufen der API-App und der geschützten API-App verwenden. Wenn Sie diese Alternative ausgewählt haben, benötigen Sie die Option **Neue AD-App erstellen** hier nicht, da Sie bereits im Tutorial zur Benutzerauthentifizierung eine Azure AD-Anwendung erstellt haben. In diesem Tutorial verwenden Sie separate Azure AD-Anwendungen für das Aufrufen der API-App und der geschützten API-App.

8. Notieren Sie sich den Wert im Eingabefeld **App erstellen**. Sie werden später im klassischen Azure-Portal nach dieser AAD-Anwendung suchen.

7. Klicken Sie auf **OK**.

10. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.

	![](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	App Service erstellt eine Azure Active Directory-Anwendung, bei der die **Anmelde-URL** und die **Antwort-URL** automatisch auf die URL Ihrer API-App festgelegt sind. Letztere ermöglicht Benutzern in Ihrem AAD-Mandanten das Anmelden an der API-App und den Zugriff darauf.

### Sicherstellen des Schutzes für die API-App

1. Navigieren Sie in einem Browser zur URL der API-App: Klicken Sie auf dem Blatt **API-App** im Azure-Portal auf den Link unter **URL**. 

	Sie werden auf eine Anmeldeseite umgeleitet, da nicht authentifizierte Anforderungen die API-App nicht erreichen dürfen.

	Falls in Ihrem Browser auf die Swagger-Benutzeroberfläche zugegriffen wird, kann es sein, dass für den Browser bereits eine Anmeldung besteht. Öffnen Sie in diesem Fall ein InPrivate- oder Incognito-Fenster, und greifen Sie auf die URL der Swagger-Benutzeroberfläche zu.

18. Melden Sie sich mit den Anmeldeinformationen eines Benutzers an Ihrem AAD-Mandanten an.

	Nachdem Sie sich angemeldet haben, wird im Browser die Seite mit dem Hinweis zur erfolgreichen Erstellung angezeigt.

## Konfigurieren des Projekts ToDoListAPI zum Abrufen und Senden des Azure AD-Tokens

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Hinzufügen von Code zur API-App der mittleren Ebene, wobei Anmeldeinformationen der Azure AD-Anwendung verwendet werden, um ein Token abzurufen, und Senden des Tokens mit HTTP-Anforderungen an die API-App der Datenebene
* Abrufen der erforderlichen Anmeldeinformationen von Azure AD
* Eingeben der Anmeldeinformationen für Einstellungen der Azure App Service-Laufzeitumgebung in der API-App der mittleren Ebene 

### Konfigurieren des Projekts ToDoListAPI zum Abrufen und Senden des Azure AD-Tokens

Nehmen Sie in Visual Studio die folgenden Änderungen am Projekt ToDoListAPI vor.

1. Heben Sie die Auskommentierung des gesamten Codes in der Datei *ServicePrincipal.cs* auf.

	Dies ist der Code, in dem die ADAL für .NET zum Abrufen des Azure AD-Bearertokens verwendet wird. Hierbei werden mehrere Konfigurationswerte genutzt, die Sie später in der Azure-Laufzeitumgebung festlegen. Der Code lautet wie folgt:

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**Hinweis:** Für diesen Code ist das NuGet-Paket „ADAL für .NET“ (Microsoft.IdentityModel.Clients.ActiveDirectory) erforderlich, das im Projekt bereits installiert ist. Wenn Sie dieses Projekt von Grund auf neu erstellen, müssen Sie dieses Paket installieren. Das Paket wird von der Projektvorlage für neue API-Apps nicht automatisch installiert.

2. Heben Sie in *Controllers/ToDoListController* die Auskommentierung des Codes in der `NewDataAPIClient`-Methode auf, mit der das Token den HTTP-Anforderungen im Autorisierungsheader hinzugefügt wird.

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Stellen Sie das Projekt ToDoListAPI bereit. (Klicken Sie mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Veröffentlichen > Veröffentlichen**.)

4. Schließen Sie das Browserfenster, das geöffnet wird, nachdem das Projekt erfolgreich bereitgestellt wurde.

### Abrufen von Azure AD-Konfigurationswerten

11. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) zu **Azure Active Directory**.

12. Klicken Sie auf der Registerkarte **Verzeichnis** auf Ihren AAD-Mandanten.

14. Klicken Sie auf **Anwendungen > Anwendungen im Besitz meines Unternehmens** und dann auf das Häkchen.

15. Klicken Sie in der Liste mit den Anwendungen auf den Namen der Anwendung, die von Azure für Sie erstellt wurde, als Sie die Authentifizierung für die API-App ToDoListDataAPI (Datenebene) aktiviert haben.

16. Klicken Sie auf die Registerkarte **Konfigurieren**.

5. Kopieren Sie den Wert **Client-ID**, und speichern Sie ihn an einem Ort, an dem Sie später darauf zugreifen können.

8. Kehren Sie im klassischen Azure-Portal zur Liste **Anwendungen im Besitz meines Unternehmens** zurück, und klicken Sie auf die AAD-Anwendung, die Sie für die API-App ToDoListAPI (mittlere Ebene) erstellt haben.

16. Klicken Sie auf die Registerkarte **Konfigurieren**.

5. Kopieren Sie den Wert **Client-ID**, und speichern Sie ihn an einem Ort, an dem Sie später darauf zugreifen können.

6. Wählen Sie unter **Schlüssel** in der Dropdownliste **Dauer auswählen** die Option **1 Jahr** aus.

6. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Kopieren Sie den Schlüsselwert, und speichern Sie ihn an einem Ort, an dem Sie später darauf zugreifen können.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### Konfigurieren von Azure AD-Einstellungen in der Laufzeitumgebung der API-App für die mittlere Ebene

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/) und dann zum Blatt **API-App** für die API-App, von der das Projekt TodoListAPI (mittlere Ebene) gehostet wird.

2. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

3. Fügen Sie im Abschnitt **Anwendungseinstellungen** die folgenden Schlüssel und Werte hinzu:

	|Schlüssel|Wert|Beispiel
	|---|---|---|
	|ida:Authority|https://login.microsoftonline.com/{your Azure AD tenant name = Ihr Azure AD-Mandantenname}|https://login.microsoftonline.com/contoso.onmicrosoft.com|
	|ida:ClientId|Client-ID der aufrufenden Anwendung (ToDoListAPI)|960adec2-b74a-484a-960adec2-b74a-484a|
	|ida:ClientSecret|App-Schlüssel der aufrufenden Anwendung (ToDoListAPI)|oCgdj3EYLfnR0p6iR3UvHFAfkn+zQB+0VqZT/6=
	|ida:Resource|Client-ID der aufgerufenen Anwendung (ToDoListDataAPI)|e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8|

	Wenn Sie sowohl für die aufrufende API-App als auch die geschützte API-App nur eine Azure AD-Anwendung verwenden würden, müssten Sie für `ida:ClientId` und `ida:Resource` den gleichen Wert nutzen.

	Im Code wird ConfigurationManager zum Abrufen dieser Werte verwendet, sodass sie in der Datei „Web.config“ des Projekts oder in der Azure-Laufzeitumgebung gespeichert werden können. Während eine ASP.NET-Anwendung in Azure App Service ausgeführt wird, setzen die Umgebungseinstellungen die Einstellungen der Datei „Web.config“ automatisch außer Kraft. Umgebungseinstellungen sind im Allgemeinen eine [sicherere Möglichkeit zum Speichern vertraulicher Informationen (im Vergleich zur Datei „Web.config“)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### Testen der Anwendung

1. Greifen Sie in einem Browser auf die HTTPS-URL der AngularJS-Front-End-Web-App zu.

2. Klicken Sie auf die Registerkarte **Aufgabenliste**, und melden Sie sich mit Anmeldeinformationen für einen Benutzer im Azure AD-Mandanten an.

4. Fügen Sie Aufgabenelemente hinzu, um zu überprüfen, ob die Anwendung funktioniert.

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	Wenn die Anwendung nicht wie erwartet funktioniert, sollten Sie alle Einstellungen überprüfen, die Sie im Azure-Portal eingegeben haben. Falls alle Einstellungen korrekt zu sein scheinen, hilft Ihnen der Abschnitt [Problembehandlung](#troubleshooting) weiter unten in diesem Tutorial weiter.

## Schützen der API-App vor dem Browserzugriff

In diesem Tutorial haben Sie eine separate Azure AD-Anwendung für die API-App ToDoListDataAPI (Datenebene) erstellt. Sie haben Folgendes gesehen: Wenn App Service eine AAD-Anwendung erstellt, wird diese so konfiguriert, dass Benutzer in einem Browser auf die URL der API-App zugreifen und sich anmelden können. Das heißt, dass ein Endbenutzer in Ihrem Azure AD-Mandanten auf die API zugreifen kann, und nicht nur ein Dienstprinzipal.

Falls Sie den Browserzugriff verhindern möchten, ohne Code in der geschützten API-App zu schreiben, können Sie die **Antwort-URL** in der AAD-Anwendung ändern, damit sie sich von der Basis-URL der API-App unterscheidet.

### Deaktivieren des Browserzugriffs

1. Ändern Sie im klassischen Portal auf der Registerkarte **Konfigurieren** der AAD-Anwendung, die für TodoListService erstellt wurde, den Wert im Feld **Antwort-URL** in eine gültige URL, die nicht die URL der API-App ist.
 
2. Klicken Sie auf **Speichern**.

### Prüfen, ob der Browserzugriff nicht mehr funktioniert

Sie haben weiter oben bereits sichergestellt, dass Sie über einen Browser auf die URL der API-App zugreifen können, indem Sie sich mit den Anmeldeinformationen eines einzelnen Benutzers angemeldet haben. In diesem Abschnitt stellen Sie sicher, dass dies nicht mehr möglich ist.

1. Öffnen Sie in einem neuen Browserfenster erneut die URL der API-App.

2. Melden Sie sich an, wenn Sie dazu aufgefordert werden.

3. Die Anmeldung ist erfolgreich, führt aber zu einer Seite mit einer Fehlermeldung.

	Sie haben AAD-App so konfiguriert, dass sich Benutzer im AAD-Mandanten nicht anmelden und nicht über einen Browser auf die API zugreifen können. Sie selbst können weiterhin auf die API-App zugreifen, indem Sie ein Dienstprinzipaltoken verwenden. Sie können dies überprüfen, indem Sie auf die URL der Web-App zugreifen und weitere Aufgabenelemente hinzufügen.

## Einschränken des Zugriffs auf einen bestimmten Dienstprinzipal  

Zum jetzigen Zeitpunkt können alle Aufrufer, die sich ein Token für einen Benutzer oder einen Dienstprinzipal in Ihrem Azure AD-Mandanten beschaffen können, die API-App TodoListDataAPI (Datenebene) aufrufen. Es kann ratsam sein sicherzustellen, dass die API-App der Datenebene nur Aufrufe von der API-App TodoListAPI (mittlere Ebene) und nur von einem bestimmten Dienstprinzipal akzeptiert.

Sie können diese Einschränkungen hinzufügen, indem Sie Code zum Überprüfen der Ansprüche `appid` und `objectidentifier` von eingehenden Anrufen hinzufügen.

Für dieses Tutorial fügen Sie den Code, mit dem die App-ID und die Dienstprinzipal-ID überprüft werden, direkt in Ihre Controlleraktionen ein. Alternativen sind die Verwendung eines benutzerdefinierten `Authorize`-Attributs oder das Durchführen der Überprüfung in Ihren Startsequenzen (z. B. OWIN-Middleware).

Nehmen Sie die folgenden Änderungen am Projekt TodoListDataAPI vor.

2. Öffnen Sie die Datei *Controllers/TodoListController.cs*.

3. Heben Sie die Auskommentierung der Zeilen auf, mit denen `trustedCallerClientId` und `trustedCallerServicePrincipalId` festgelegt wird.

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Heben Sie die Auskommentierung des Codes in der CheckCallerId-Methode auf. Diese Methode wird zu Beginn jeder Aktionsmethode im Controller aufgerufen.

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. Stellen Sie das Projekt ToDoListDataAPI erneut in Azure App Service bereit.

6. Greifen Sie in Ihrem Browser auf die HTTPS-URL der AngularJS-Front-End-Web-App zu, und klicken Sie auf der Startseite auf die Registerkarte **Aufgabenliste**.

	Die Anwendung funktioniert nicht, da die Aufrufe des Back-Ends nicht erfolgreich sind. Der neue Code überprüft die tatsächlichen Werte für appid und objectidentifier, aber er verfügt noch nicht über die richtigen Werte für den Abgleich. Im Browser wird auf der Entwicklertools-Konsole die Meldung angezeigt, dass der Server den Fehler „HTTP 401“ zurückgibt.

	![](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	Mit den folgenden Schritten konfigurieren Sie die erwarteten Werte.

8. Mit der Azure AD PowerShell rufen Sie den Wert des Dienstprinzipals für die Azure AD-Anwendung ab, die Sie für das Projekt TodoListWebApp erstellt haben.

	a. Eine Anleitung, wie Sie Azure PowerShell installieren und eine Verbindung mit Ihrem Abonnement herstellen, finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).

	b. Um eine Liste mit Dienstprinzipalen zu erhalten, führen Sie den Befehl `Login-AzureRmAccount` und dann den Befehl `Get-AzureRmADServicePrincipal` aus.

	c. Suchen Sie nach der objectid für den Dienstprinzipal der Anwendung TodoListAPI, und speichern Sie sie an einem Ort, von dem Sie sie später kopieren können.

7. Navigieren Sie im Azure-Portal zum Blatt „Web-App“ für die Web-App, für die Sie das Projekt ToDoListAngular bereitgestellt haben.

9. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

3. Fügen Sie im Abschnitt **Anwendungseinstellungen** die folgenden Schlüssel und Werte hinzu:

	|Schlüssel|Wert|Beispiel
	|---|---|---|
	|todo:TrustedCallerServicePrincipalId|Dienstprinzipal-ID der aufrufenden Anwendung|4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072|
	|todo:TrustedCallerClientId|Client-ID der aufrufenden Anwendung – aus der AAD-Anwendung TodoListAPI kopiert|960adec2-b74a-484a-960adec2-b74a-484a|

6. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. Kehren Sie im Browser zur URL der Web-App zurück, und klicken Sie auf der Startseite auf die Registerkarte **Aufgabenliste**.

	Nun funktioniert die Anwendung wie erwartet, da die App-ID des vertrauenswürdigen Aufrufers und die Dienstprinzipal-ID die erwarteten Werte sind.

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## Neues Erstellen der Projekte von Grund auf

Die beiden Web-API-Projekte wurden erstellt, indem die Projektvorlage **Azure-API-App** verwendet wurde und der standardmäßige Values-Controller durch einen ToDoList-Controller ersetzt wurde. Zum Beschaffen von Azure AD-Dienstprinzipaltoken im Projekt ToDoListAPI wurde das NuGet-Paket [Active Directory Authentication Library](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) (ADAL) für .NET installiert.
 
Weitere Informationen zum Erstellen einer AngularJS-Single-Page-Anwendung mit einem Web-API-Back-End wie ToDoListAngular finden Sie unter [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs) (Hands On Lab: Erstellen einer Single-Page-Anwendung (SPA) mit ASP.NET-Web-API und „Angular.js“). Informationen zum Hinzufügen von Azure AD-Authentifizierungscode finden Sie unter [Schützen von einseitigen AngularJS-Apps mit Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## Problembehandlung

Wenn Sie die Anwendung ohne Authentifizierung erfolgreich ausgeführt haben und diese beim Implementieren der Authentifizierung dann nicht richtig funktioniert, sind häufig fehlerhafte oder inkonsistente Konfigurationseinstellungen das Problem. Beginnen Sie, indem Sie alle Einstellungen in Azure App Service und Azure Active Directory noch einmal gründlich überprüfen. Vorschläge:

* Stellen Sie nach dem Konfigurieren des Codes in einem Projekt sicher, dass Sie das richtige Projekt erneut bereitstellen, und nicht eines der anderen Projekte.
* Stellen Sie für die Einstellungen des Blatts **Anwendungseinstellungen** im Azure-Portal sicher, dass die richtige API-App oder Web-App ausgewählt war, als die Einstellungen eingegeben wurden.
* Achten Sie darauf, dass Sie in Ihrem Browser auf HTTPS-URLs zugreifen, nicht auf HTTP-URLs.
* Stellen Sie sicher, dass CORS für die API-App der mittleren Ebene noch aktiviert ist, sodass Aufrufe an die mittlere Ebene über die Front-End-HTTPS-URL möglich sind. Falls Sie unsicher sind, ob das Problem mit CORS zusammenhängt, können Sie versuchen, „*“ als zulässige Ursprungs-URL zu verwenden. **Wichtig:** In einigen Fehlermeldungen der Entwicklertools-Konsole wird unter Umständen auch ein CORS-Fehler gemeldet, wenn die Ursache mit der Authentifizierung auf der Datenebene zusammenhängt. Sie können überprüfen, ob dies der Fall ist, indem Sie die Authentifizierung für die API-App ToDoListDataAPI vorübergehend deaktivieren.
* Sorgen Sie dafür, dass Sie in Fehlermeldungen so viele Informationen wie möglich erhalten, indem Sie den [customErrors-Modus auf „Off“ festlegen](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Wenn andere Methoden fehlschlagen, können Sie es mit einer [Remotedebugsitzung](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) versuchen. Hierbei können Sie die Werte der Variablen untersuchen, die an den Code übergeben werden, mit dem das Bearertoken in ToDoListAPI abgerufen wird, und an den Code, mit dem die in ToDoListDataAPI empfangenen Azure AD-Werte überprüft werden. Beachten Sie, dass mit Ihrem Code die Konfigurationswerte aus vielen unterschiedlichen Quellen erfasst werden können, sodass Sie hier ggf. auch Überraschungen erleben. Wenn Sie beim Konfigurieren von App Service-Einstelllungen beispielsweise nicht den Namen `ida:ClientId` angeben, sondern `ida:ClientID`, kann Folgendes passieren: Der Code erhält den gesuchten Wert für `ida:ClientId` von der Datei „Web.config“ und ignoriert die App Service-Einstellung. 

## Nächste Schritte

Dies ist der letzte Artikel in der Serie zu den ersten Schritten für API-Apps.

Weitere Informationen zu Azure Active Directory finden Sie in den folgenden Ressourcen:

* [Azure AD Entwicklerhandbuch](http://aka.ms/aaddev)
* [Azure AD-Szenarien](http://aka.ms/aadscenarios)
* [Azure AD-Beispiele](http://aka.ms/aadsamples)

Informationen zu anderen Möglichkeiten der Bereitstellung von Visual Studio-Projekten für API-Apps mit Visual Studio oder durch die [Automatisierung der Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) über ein [Quellcodeverwaltungssystem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) finden Sie unter [Gewusst wie: Bereitstellen einer Azure App Service-App](web-sites-deploy.md).

<!---HONumber=AcomDC_0128_2016-->