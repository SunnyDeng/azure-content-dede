<properties
	pageTitle="Azure Active Directory B2C Preview: Verwenden der Graph-API | Microsoft Azure"
	description="Aufrufen der Graph-API für einen B2C-Mandanten durch Verwenden einer Anwendungsidentität zum Automatisieren des Prozesses."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C Preview: Verwenden der Graph-API


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure Active Directory (Azure AD) B2C-Mandanten sind normalerweise sehr groß. Das bedeutet, dass viele allgemeine Aufgaben zur Mandantenverwaltung programmgesteuert durchgeführt werden müssen. Ein gutes Beispiel ist die Benutzerverwaltung. Unter Umständen müssen Sie einen vorhandenen Benutzerspeicher zu einem B2C-Mandanten migrieren. Sie möchten die Benutzerregistrierung vielleicht auf Ihrer eigenen Seite hosten und Benutzerkonten in Azure AD im Hintergrund erstellen. Diese Arten von Aufgaben erfordern die Fähigkeit zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzerkonten. Hierfür können Sie die Azure AD Graph-API verwenden.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Für B2C-Mandanten gibt es zwei primäre Modi für die Kommunikation mit der Graph-API.

- Für interaktive Aufgaben mit einmaliger Ausführung sollten Sie beim Ausführen der Aufgaben ein Administratorkonto im B2C-Mandanten verwenden. In diesem Modus muss sich ein Administrator mit Anmeldeinformationen anmelden, bevor er alle Aufrufe der Graph-API ausführen kann.
- Für automatisierte, kontinuierliche Aufgaben sollte eine Art von Dienstkonto verwendet werden, dem Sie die benötigten Rechte zum Ausführen von Verwaltungsaufgaben gewähren. In Azure AD registrieren Sie dazu eine Anwendung und authentifizieren sie bei Azure AD. Dies geschieht mithilfe einer **Anwendungs-ID**, die die [OAuth 2.0-Clientanmeldeinformationen](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api) verwendet. In diesem Fall verhält sich die Anwendung beim Aufrufen der Graph-API wie sie selbst und nicht wie ein Benutzer.

In diesem Artikel wird erläutert, wie Sie den automatisierten Anwendungsfall durchführen. Wir erstellen zu Demozwecken ein .NET 4.5-`B2CGraphClient`-Element, das Benutzervorgänge zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD) ausführt. Der Client verfügt über eine Windows-Befehlszeilenschnittstelle, über die Sie verschiedene Methoden aufrufen können. Der Code ist aber so geschrieben, dass er sich auf nicht interaktive, automatisierte Weise verhält.

## Erhalten eines Azure AD-B2C-Mandanten

Bevor Sie Anwendungen oder Benutzer erstellen und mit Azure AD interagieren können, benötigen Sie einen Azure AD B2C-Mandanten und ein globales Administratorkonto in diesem Mandanten. Falls Sie noch keinen Mandanten besitzen, führen Sie die [ersten Schritte mit Azure AD B2C](active-directory-b2c-get-started.md) aus.

## Registrieren einer Dienstanwendung in Ihrem Mandanten

Da Sie jetzt über einen B2C-Mandanten verfügen, müssen Sie Ihre Dienstanwendung mit den Azure AD PowerShell-Cmdlets erstellen. Laden Sie zuerst den [Microsoft Online Services-Anmelde-Assistenten](http://go.microsoft.com/fwlink/?LinkID=286152) herunter, und installieren Sie ihn. Laden Sie anschließend das [Azure Active Directory-Modul für Windows PowerShell (64 Bit)](http://go.microsoft.com/fwlink/p/?linkid=236297) herunter, und installieren Sie es.

> [AZURE.NOTE]
Um die Graph-API mit Ihrem B2C-Mandanten zu verwenden, müssen Sie unter Verwendung von PowerShell eine dedizierte Anwendung registrieren. Folgen Sie dazu den Anweisung in diesem Artikel. Sie können Ihre bereits vorhandenen B2C-Anwendungen, die sie im Azure-Portal registriert haben, nicht wiederverwenden. Dies ist eine Einschränkung der Azure AD B2C-Vorschau, die in naher Zukunft entfernt werden soll. Dieser Artikel wird dann entsprechend aktualisiert.

Nachdem Sie das PowerShell-Modul installiert haben, öffnen Sie PowerShell und stellen eine Verbindung mit Ihrem B2C-Mandanten her. Nach dem Ausführen von `Get-Credential` werden Sie zur Eingabe des Benutzernamens und Kennworts aufgefordert. Geben Sie den Benutzernamen und das Kennwort des Administratorkontos für Ihren B2C-Mandanten ein.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Vor dem Erstellen der Anwendung müssen Sie einen neuen **geheimen Clientschlüssel** generieren. Die Anwendung verwendet den geheimen Clientschlüssel für die Authentifizierung gegenüber Azure AD und zum Abrufen von Zugriffstoken. Sie können in PowerShell einen gültigen geheimen Schlüssel generieren:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

Mit dem letzten angegebenen Befehl sollte Ihr neuer geheimer Clientschlüssel ausgegeben werden. Kopieren Sie ihn an einen sicheren Speicherort. Sie benötigen sie später. Jetzt können Sie Ihre Anwendung erstellen und den neuen geheimen Clientschlüssel als Anmeldeinformation für die App angeben:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Wenn die Anwendung erfolgreich erstellt wurde, sollten Eigenschaften der Anwendung ausgegeben werden, z. B. wie im obigen Beispiel. Sie benötigen `ObjectId` und `AppPrincipalId`, kopieren Sie diese Werte also ebenfalls.

Nach der Erstellung einer Anwendung in Ihrem B2C-Mandanten müssen Sie die Berechtigungen zuweisen, die für die Durchführung von CRUD-Benutzervorgängen erforderlich sind. Weisen Sie der Anwendung drei Rollen zu: „Verzeichnis lesen“ (zum Lesen von Benutzern), „Verzeichnis schreiben“ (zum Erstellen und Aktualisieren von Benutzern) und einen Benutzerkontoadministrator (zum Löschen von Benutzern). Diese Rollen verfügen über bekannte Bezeichner, sodass Sie den Parameter `-RoleMemberObjectId` durch den oben angegebenen Wert `ObjectId` ersetzen und die folgenden Befehle ausführen können. Führen Sie `Get-MsolRole` aus, um die Liste mit allen Verzeichnisrollen anzuzeigen.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Sie verfügen jetzt über eine Anwendung mit der Berechtigung zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzern in Ihrem B2C-Mandanten.

## Herunterladen, Konfigurieren und Erstellen des Beispielcodes

Laden Sie zunächst den Beispielcode herunter, und bereiten Sie ihn für die Ausführung vor. Dann sehen wir ihn uns genauer an. Sie können den [Beispielcode als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Sie können ihn auch in einem Verzeichnis Ihrer Wahl klonen:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Öffnen Sie die Visual Studio-Projektmappe `B2CGraphClient\B2CGraphClient.sln` in Visual Studio. Öffnen Sie im Projekt `B2CGraphClient` die Datei "`App.config`". Ersetzen Sie die drei App-Einstellungen durch Ihre eigenen Werte:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Klicken Sie anschließend mit der rechten Maustaste auf die Projektmappe `B2CGraphClient`, und erstellen Sie das Beispiel neu. War der Vorgang erfolgreich, sollte sich nun die ausführbare Datei `B2C.exe` unter `B2CGraphClient\bin\Debug` befinden.

## Erstellen von CRUD-Benutzervorgängen mithilfe der Graph-API

Öffnen Sie zum Verwenden von B2CGraphClient eine Windows-Befehlseingabeaufforderung (`cmd`), und wechseln Sie in das Verzeichnis `Debug`. Führen Sie anschließend den Befehl `B2C Help` aus.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Für jeden Befehl wird eine kurze Beschreibung angezeigt. Bei jedem Aufruf einer dieser Befehle sendet `B2CGraphClient` eine Anforderung an die Azure AD Graph-API.

### Abrufen eines Zugriffstokens

Für jede Anforderung der Graph-API ist ein Zugriffstoken für die Authentifizierung erforderlich. `B2CGraphClient` verwendet die Open Source Active Directory Authentication Library (ADAL) für die Beschaffung von Zugriffstoken. ADAL erleichtert die Tokenbeschaffung, indem eine einfache API bereitgestellt wird und einige wichtige Details abgedeckt werden, z. B. das Zwischenspeichern der Zugriffstoken. Sie müssen ADAL jedoch nicht zum Abrufen von Token verwenden. Sie können Token auch abrufen, indem Sie HTTP-Anforderungen erstellen.

> [AZURE.NOTE]
	In diesem Codebeispiel wird ADAL v2 verwendet, also die allgemein verfügbare Version von ADAL. Es wird nicht ADAL v4 verwendet, da dies eine Preview-Version für die Verwendung mit Azure AD B2C ist. Für die Preview-Version von Azure AD B2C müssen Sie ADAL v2 für die Kommunikation mit der Graph-API verwenden. Im Laufe der Zeit werden wir den Zugriff auf die Graph-API per ADAL v4 ermöglichen, damit Sie in Ihrer vollständigen Azure AD B2C-Lösung nicht zwei Versionen von ADAL nutzen müssen.

Bei der Ausführung von `B2CGraphClient` wird eine Instanz der `B2CGraphClient`-Klasse erstellt. Mit dem Konstruktor für diese Klasse wird das Authentifizierungsgerüst für ADAL eingerichtet:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are
	// provided to Azure AD in order to receive an access_token by using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Als Beispiel verwenden wir den Befehl `B2C Get-User`. Wenn `B2C Get-User` ohne zusätzliche Eingaben aufgerufen wird, ruft die Befehlszeilenschnittstelle die `B2CGraphClient.GetAllUsers(...)`-Methode auf. Diese Methode ruft `B2CGraphClient.SendGraphGetRequest(...)` auf, wodurch eine HTTP GET-Anforderung an die Graph-API gesendet wird. Bevor `B2CGraphClient.SendGraphGetRequest(...)` die GET-Anforderung sendet, wird per ADAL zuerst ein Zugriffstoken abgerufen:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Sie können ein Zugriffstoken für die Graph-API abrufen, indem Sie die `AuthenticationContext.AcquireToken(...)`-Methode von ADAL aufrufen. ADAL gibt anschließend ein `access_token` zurück, das die Identität der Anwendung darstellt.

### Lesen von Benutzern

Wenn Sie eine Liste mit Benutzern oder einen bestimmten Benutzer aus der Graph-API abrufen möchten, können Sie eine HTTP `GET`-Anforderung an den `/users`-Endpunkt senden. Eine Anforderung für alle Benutzer in einem Mandanten sieht folgendermaßen aus:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Um diese Anforderung anzuzeigen, führen Sie Folgendes aus:

 ```
 > B2C Get-User
 ```

Hierbei sind zwei wichtige Punkte zu beachten:

- Das über ADAL abgerufene Zugriffstoken wird dem Header `Authorization` über das Schema `Bearer` hinzugefügt.
- Für B2C-Mandanten müssen Sie den Abfrageparameter `api-version=beta` verwenden.


> [AZURE.NOTE]
	Die Beta-Version der Azure AD Graph-API bietet Preview-Funktionen. Details zur Beta-Version finden Sie in [diesem Teamblogbeitrag zur Graph-API](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).

Diese Details werden beide in der `B2CGraphClient.SendGraphGetRequest(...)`-Methode behandelt:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Erstellen von Benutzerkonten für Consumer

Beim Erstellen von Benutzerkonten in Ihrem B2C-Mandanten können Sie eine HTTP `POST`-Anforderung an den `/users`-Endpunkt senden:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Alle Eigenschaften in dieser Anforderung müssen Consumerbenutzer erstellen. Die Kommentare `//` wurden zur Veranschaulichung eingefügt. Fügen Sie sie nicht in einer tatsächlichen Anforderung ein.

Führen Sie zum Anzeigen der Anforderung einen der folgenden Befehle aus:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Der Befehl `Create-User` verwendet eine JSON-Datei als Eingabeparameter. Diese Datei enthält eine JSON-Darstellung eines Benutzerobjekts. Es gibt zwei JSON-Beispieldateien im Beispielcode: `usertemplate-email.json` und `usertemplate-username.json`. Sie können diese Dateien Ihren Anforderungen entsprechend ändern. Zusätzlich zu den obigen erforderlichen Feldern enthalten diese Dateien auch verschiedene optionale Felder, die Sie verwenden können. Details zu den optionalen Feldern finden Sie in der [Referenz zu Azure AD Graph-API-Entitäten](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Sie sehen, wie die POST-Anforderung in `B2CGraphClient.SendGraphPostRequest(...)` aufgebaut ist:

- An den Header `Authorization` der Anforderung wird ein Zugriffstoken angefügt.
- `api-version=beta` wird festgelegt.
- Das JSON-Benutzerobjekt wird in den Hauptteil der Anforderung eingefügt.

### Aktualisieren von Benutzerkonten für Consumer

Die Aktualisierung von Benutzerobjekten ähnelt dem Prozess zum Erstellen von Benutzerobjekten. Dieser Prozess verwendet jedoch die HTTP `PATCH`-Methode:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

Versuchen Sie, einen Benutzer zu aktualisieren, indem Sie die JSON-Dateien mit neuen Daten aktualisieren. Anschließend können Sie mithilfe von `B2CGraphClient` einen der folgenden Befehle ausführen:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Überprüfen Sie die `B2CGraphClient.SendGraphPatchRequest(...)`-Methode auf Details zum Senden dieser Anforderung.

### Löschen von Benutzern

Der Vorgang zum Löschen eines Benutzers ist ganz einfach. Erstellen Sie mithilfe der HTTP `DELETE`-Methode die URL mit der richtigen Objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Verwenden Sie zum Anzeigen eines Beispiels den folgenden Befehl, und sehen Sie sich die Löschanforderung an, die in der Konsole ausgegeben wird:

```
> B2C Delete-User <object-id-of-user>
```

Überprüfen Sie die `B2CGraphClient.SendGraphDeleteRequest(...)`-Methode auf Details zum Senden dieser Anforderung.

Sie können neben der Benutzerverwaltung zahlreiche weitere Aktionen mit der Azure AD Graph-API ausführen. Die [Referenz zur Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) enthält Details zu jeder Aktion und Beispielanforderungen.

## Verwenden von benutzerdefinierten Attributen

In den meisten Consumeranwendungen müssen bestimmte benutzerdefinierte Informationen zu einem Benutzerprofil gespeichert werden. Eine Möglichkeit dazu besteht im Definieren eines benutzerdefinierten Attributs in Ihrem B2C-Mandanten. Sie können dann dieses Attribut wie jede andere Eigenschaft eines Benutzerobjekts behandeln. Sie können das Attribut aktualisieren und löschen, nach dem Attribut abfragen, es in Anmeldetoken als Anspruch senden usw.

Informationen zum Definieren eines benutzerdefinierten Attributs in Ihrem B2C-Mandanten finden Sie unter [Benutzerdefinierte Attribute in der B2C-Vorschau – Referenz](active-directory-b2c-reference-custom-attr.md).

Sie können die benutzerdefinierten Attribute, die in Ihrem B2C-Mandanten definiert sind, mit `B2CGraphClient` anzeigen:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Die Ausgaben dieser Funktionen umfassen die Details der einzelnen benutzerdefinierten Attribute, z. B.:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Sie können den vollständigen Namen, z. B. `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, als Eigenschaft in Ihren Benutzerobjekten verwenden. Aktualisieren Sie die JSON-Datei mit der neuen Eigenschaft und einem Wert für die Eigenschaft, und führen Sie Folgendes aus:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Mit `B2CGraphClient` verfügen Sie über eine Dienstanwendung, mit der Ihre B2C-Mandantenbenutzer programmgesteuert verwaltet werden können. `B2CGraphClient` nutzt die eigene Anwendungsidentität für die Authentifizierung gegenüber der Azure AD Graph-API. Darüber hinaus werden Token mithilfe eines geheimen Clientschlüssels abgerufen. Beachten Sie beim Einbinden dieser Funktionalität in Ihre Anwendung einige wichtige Punkte für B2C-Apps:

- Sie müssen der Anwendung im Mandanten die richtigen Berechtigungen gewähren.
- Derzeit müssen Sie zum Abrufen von Zugriffstoken ADAL v2 verwenden. (Sie können Protokollnachrichten auch direkt ohne Verwendung einer Bibliothek senden.)
- Verwenden Sie beim Aufrufen der Graph-API [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Beim Erstellen und Aktualisieren von Consumerbenutzern sind wie oben beschrieben einige Eigenschaften erforderlich.

> [AZURE.IMPORTANT] Sie müssen die Replikationsmerkmale des Verzeichnisdiensts berücksichtigen, der dem Azure AD B2C-Dienst zugrunde liegt, wenn Sie die Azure AD Graph-API in Ihrer B2C-App verwenden. (Weitere Informationen finden Sie in [diesem Artikel](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx).) Nachdem sich ein Consumer mithilfe einer **Registrierungsrichtlinie** bei der B2C-App registriert hat, ist das Benutzerobjekt möglicherweise nicht verfügbar, wenn Sie sofort versuchen, es mit der Azure AD Graph-API in Ihrer App zu lesen. Sie müssen einige Sekunden warten, bis der Replikationsvorgang abgeschlossen ist. Konkretere Hilfe zur „Schreib-Lese-Konsistenzgarantie“ der Azure AD Graph-API und des Verzeichnisdiensts bei allgemeiner Verfügbarkeit wird zu einem späteren Zeitpunkt veröffentlicht.

Wenn Sie Fragen haben oder Aktionen anfordern möchten, die Sie mit der Graph-API in Ihrem B2C-Mandanten durchführen möchten, hinterlassen Sie einen Kommentar zu diesem Artikel, oder legen Sie im GitHub-Repository des Codebeispiels einen entsprechenden Eintrag an.

<!---HONumber=AcomDC_0302_2016-->