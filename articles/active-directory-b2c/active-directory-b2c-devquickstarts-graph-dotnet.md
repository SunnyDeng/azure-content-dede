<properties
	pageTitle="Azure AD B2C Preview | Microsoft Azure"
	description="Sie erfahren, wie Sie die Graph-API für ein B2C-Verzeichnis aufrufen, indem Sie eine Anwendungsidentität zum Automatisieren des Prozesses verwenden."
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
	ms.date="09/01/2015"
	ms.author="dastrock"/>


# Azure AD B2C Preview: Verwenden der Graph-API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure AD B2C-Verzeichnisse sind normalerweise sehr groß. Dies bedeutet, dass viele allgemeine Aufgaben zur Verzeichnisverwaltung programmgesteuert durchgeführt werden müssen. Ein gutes Beispiel ist die Benutzerverwaltung. Unter Umständen müssen Sie einen vorhandenen Benutzerspeicher zu einem B2C-Verzeichnis migrieren, oder Sie möchten die Benutzerregistrierung auf Ihrer eigenen Seite hosten und Benutzerkonten in Azure AD im Hintergrund erstellen. Diese Arten von Aufgaben erfordern die Fähigkeit zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzerkonten. Hierfür können Sie die Azure AD Graph-API verwenden.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
Für B2C-Verzeichnisse gibt es hauptsächlich zwei Modi für die Kommunikation mit der Graph-API.

- Für interaktive Aufgaben mit einmaliger Ausführung werden Verwaltungsaufgaben normalerweise über das Administratorkonto im B2C-Verzeichnis durchgeführt. Dieser Modus erfordert, dass sich ein Administrator mit seinen Anmeldeinformationen anmeldet, bevor Aufrufe an die Graph-API erfolgen.
- Für automatisierte, kontinuierliche Aufgaben werden Verwaltungsaufgaben mit einer Art von Dienstkonto durchgeführt, dem Sie die benötigten Rechte gewähren. In Azure AD können Sie hierfür eine Anwendung registrieren und gegenüber Azure AD per „Anwendungsidentität“ authentifizieren, indem Sie die [Gewährung von OAuth 2.0-Clientanmeldeinformationen](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api) verwenden. In diesem Fall ruft die Anwendung die Graph-API im eigenen Namen auf, anstatt im Namen eines bestimmten Benutzers.  

In diesem Artikel wird gezeigt, wie Sie letzteren automatisierten Anwendungsfall durchführen. Zur Veranschaulichung erstellen wir einen .NET 4.5 „B2CGraphClient“, mit dem CRUD-Vorgänge für Benutzer durchgeführt werden. Zu Testzwecken verfügt der Client über eine Windows-Befehlszeilenschnittstelle, über die Sie verschiedene Methoden aufrufen können. Der Code ist aber so geschrieben, dass er sich auf nicht interaktive, automatisierte Weise verhält. Lassen Sie uns anfangen.

## Beschaffen eines B2C-fähigen Verzeichnisses

Bevor Sie Anwendungen oder Benutzer erstellen und mit Azure AD interagieren können, benötigen Sie ein B2C-fähiges Verzeichnis und ein Administratorkonto in diesem Verzeichnis. Folgen Sie der Anleitung unter [Erste Schritte mit Azure AD B2C](active-directory-b2c-get-started.md), falls Sie diese Voraussetzungen noch nicht erfüllen.

## Registrieren einer Dienstanwendung in Ihrem Verzeichnis

Da Sie jetzt über ein B2C-Verzeichnis verfügen, müssen Sie Ihre Dienstanwendung mit den Azure AD Powershell-Cmdlets erstellen. Laden Sie zuerst den [Microsoft Online Services-Anmelde-Assistenten](http://go.microsoft.com/fwlink/?LinkID=286152) herunter, und installieren Sie ihn. Anschließend können Sie das [Azure Active Directory-Modul für Windows PowerShell (64 Bit)](http://go.microsoft.com/fwlink/p/?linkid=236297) herunterladen und installieren.

Nachdem Sie das PowerShell-Modul installiert haben, öffnen Sie Powershell und stellen eine Verbindung mit Ihrem B2C-Verzeichnis her. Nach dem Ausführen von `Get-Credential` werden Sie zum Eingeben eines Benutzernamens und eines Kennworts aufgefordert. Geben Sie Benutzername und Kennwort des Administratorkontos Ihres B2C-Verzeichnisses ein.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
``` 

Vor dem Erstellen der Anwendung müssen Sie einen neuen „geheimen Clientschlüssel“ generieren. Die Anwendung verwendet den geheimen Clientschlüssel für die Authentifizierung gegenüber Azure AD und zum Abrufen von Zugriffstoken. Sie können in PowerShell einen gültigen geheimen Schlüssel generieren:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
``` 

Mit dem letzten oben angegebenen Befehl sollte Ihr neuer geheimer Clientschlüssel ausgegeben werden. Kopieren Sie ihn an einen sicheren Ort (bzw. notieren Sie ihn), da Sie ihn gleich benötigen. Jetzt können Sie Ihre Anwendung erstellen und den neuen geheimen Clientschlüssel als Anmeldeinformation für die App angeben:

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

Wenn das Erstellen der Anwendung erfolgreich ist, sollten einige Eigenschaften der Anwendung ausgegeben werden, z. B. wie im obigen Beispiel. Sie benötigen außerdem sowohl die `ObjectId` und die `AppPrincipalId`. Notieren Sie sich also auch diese Werte.

Da Sie jetzt eine Anwendung in Ihrem B2C-Verzeichnis erstellt haben, müssen Sie die Berechtigungen zuweisen, die für die Durchführung von CRUD-Vorgängen erforderlich sind. Sie müssen der Anwendung drei unterschiedliche Rollen zuweisen: „Directory Readers“ (zum Lesen von Benutzern), „Directory Writers“ (zum Erstellen und Aktualisieren von Benutzern) und „User Account Administrator“ (zum Löschen von Benutzern). Diese Rollen verfügen über bekannte Bezeichner, sodass Sie die unten angegebenen Befehle ausführen können, indem Sie den Parameter `-RoleMemberObjectId` durch die oben angezeigte `ObjectId` ersetzen. Versuchen Sie, `Get-MsolRole` auszuführen, um die Liste mit allen Verzeichnisrollen anzuzeigen.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

Sie verfügen jetzt über eine Anwendung mit der Berechtigung zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzern aus Ihrem B2C-Verzeichnis. Nun werden wir Code schreiben, um dies zu nutzen.

## Herunterladen, Konfigurieren und Erstellen des Beispielcodes

Zuerst laden wir den Beispielcode herunter und bereiten ihn für die Ausführung vor. Dann können wir uns ansehen, was hinter den Kulissen passiert. Sie können den [Beispielcode als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) oder ihn in ein Verzeichnis Ihrer Wahl klonen:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Öffnen Sie die Visual Studio-Projektmappe `B2CGraphClient\B2CGraphClient.sln` in Visual Studio. Öffnen Sie im Projekt `B2CGraphClient` die Datei `App.config`. Ersetzen Sie wie folgt die drei App-Einstellungen durch Ihre eigenen Werte:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

Klicken Sie nun mit der rechten Maustaste auf die Projektmappe `B2CGraphClient`, und erstellen Sie das Beispiel neu. Wenn dieser Vorgang erfolgreich ist, sollte in `B2CGraphClient\bin\Debug` jetzt die ausführbare Datei `B2C.exe` enthalten sein.

## Benutzer-CRUD mit der Graph-API

Öffnen Sie zum Verwenden von B2CGraphClient eine Windows-Befehlseingabeaufforderung, und wechseln Sie mit „cd“ in das Verzeichnis `Debug`. Führen Sie anschließend den Befehl `B2C Help` aus.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Für jeden Befehl wird eine kurze Beschreibung angezeigt. Wenn Sie einen dieser Befehle aufrufen, sendet der B2CGraphClient eine Anforderung an die Azure AD Graph-API.

### Abrufen eines Zugriffstokens

Alle Anforderungen an die Graph-API erfordern ein Zugriffstoken für die Authentifizierung. Der B2CGraphClient verwendet die Open Source Active Directory Authentication Library (ADAL) für die Beschaffung von Zugriffstoken. Sie müssen ADAL nicht unbedingt zum Abrufen von Token nutzen, sondern können Token auch erhalten, indem Sie HTTP-Anforderungen selbst erstellen. ADAL erleichtert die Tokenbeschaffung etwas, indem eine einfache API bereitgestellt wird und einige wichtige Details abgedeckt werden, z. B. das Zwischenspeichern der Zugriffstoken.

> [AZURE.NOTE]In diesem Codebeispiel wird absichtlich ADAL v2 verwendet, also die allgemein verfügbare Version von ADAL. Es wird NICHT ADAL v4 verwendet, da dies eine Preview-Version für die Verwendung mit Azure AD B2C ist. Für die Preview-Version von Azure AD B2C müssen Sie ADAL v2 für die Kommunikation mit der Graph-API verwenden. Im Laufe der Zeit werden wir den Zugriff auf die Graph-API per ADAL v4 ermöglichen, damit Sie in Ihrer vollständigen Azure AD B2C-Lösung nicht zwei unterschiedliche Versionen von ADAL nutzen müssen.

Wenn der B2CGraphClient ausgeführt wird, erstellt er eine Instanz der `B2CGraphClient`-Klasse. Mit dem Konstruktor für diese Klasse wird das Authentifizierungsgerüst für ADAL eingerichtet:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the directory to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are 
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Als Beispiel verwenden wir den Befehl `B2C Get-User`. Wenn `Get-User` ohne zusätzliche Eingaben aufgerufen wird, ruft die Befehlszeilenschnittstelle die `B2CGraphClient.GetAllUsers(...)`-Methode auf. Diese Methode ruft `B2CGraphClient.SendGraphGetRequest(...)` auf, wodurch eine HTTP GET-Anforderung an die Graph-API gesendet wird. Bevor die GET-Anforderung gesendet wird, wird per ADAL zuerst ein Zugriffstoken abgerufen:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	... 

```

Sie sehen, dass Sie ein Zugriffstoken für die Graph-API abrufen können, indem Sie die `AuthenticationContext.AcquireToken(...)`-Methode von ADAL aufrufen. ADAL gibt ein Zugriffstoken (access\_token) zurück, das für die Identität der Anwendung steht.

### Lesen von Benutzern

Wenn Sie eine Liste mit Benutzern aus der Graph-API oder einen bestimmten Benutzer abrufen möchten, können Sie eine HTTP GET-Anforderung an den `/users`-Endpunkt senden. Eine Anforderung für alle Benutzer in einem Verzeichnis sieht wie folgt aus:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

Führen Sie Folgendes aus, um diese Anforderung in Aktion zu sehen:

 ```
 > B2C Get-User
 ```
 
Hierbei sind zwei wichtige Punkte zu beachten:

- Das per ADAL beschaffte Zugriffstoken wurde über das Schema `Bearer` dem Header `Authorization` hinzugefügt.
- Für B2C-Verzeichnisse müssen Sie den Abfrageparameter `api-version=beta` verwenden.


> [AZURE.NOTE]Die Beta-Version der Azure AD Graph-API bietet Preview-Funktionen. Details zur Beta-Version finden Sie in [diesem Teamblog-Eintrag zur Graph-API](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).

Diese Details werden beide in der `B2CGraphClient.SendGraphGetRequest(...)`-Methode behandelt:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...
	
	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}
	
	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);
	
	... 
```
		
### Erstellen von Benutzerkonten für Consumer 

Beim Erstellen von Benutzerkonten in Ihrem B2C-Verzeichnis können Sie eine HTTP POST-Anforderung an den `/users`-Endpunkt senden:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.
	 
	"accountEnabled": false,                    // always set to false
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	}
}
```

Alle Eigenschaften, die in der obigen Anforderung enthalten sind, werden zum Erstellen von Consumerbenutzern benötigt. Die `//`-Kommentare wurden als Hilfe eingefügt. Verwenden Sie sie nicht in einer echten Anforderung.

Führen Sie einen der folgenden Befehle aus, um diese Anforderung in Aktion zu sehen:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Für den Befehl `Create-User` wird die Datei `.json` als Eingabeparameter verwendet, die eine JSON-Darstellung eines Benutzerobjekts enthält. Der Beispielcode enthält zwei `.json`-Beispieldateien (`usertemplate-email.json` und `usertemplate-username.json`), die Sie an Ihre Bedürfnisse anpassen können. Zusätzlich zu den obigen erforderlichen Feldern enthalten diese Dateien auch optionale Felder, die Sie verwenden können. Details zu diesen anderen Feldern finden Sie in der [Referenz zu Azure AD Graph-API-Entitäten](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Sie sehen, wie diese POST-Anforderung in `B2CGraphClient.SendGraphPostRequest(...)` aufgebaut ist:

- An den Header `Authorization` der Anforderung wird ein Zugriffstoken angefügt.
- `api-version=beta` wird festgelegt.
- Fügt das JSON-Benutzerobjekt in den Hauptteil der Anforderung ein.

### Aktualisieren von Benutzerkonten für Consumer

Das Aktualisieren von Benutzerobjekten ähnelt dem Erstellen von Benutzerobjekten stark, aber bei diesem Vorgang wird das HTTP PATCH-Verb verwendet:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

Sie können versuchen, einen Benutzer zu aktualisieren, indem Sie Ihre JSON-Dateien mit neuen Daten aktualisieren und den B2CGraphClient verwenden, um einen der folgenden Befehle auszuführen:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```
	
Überprüfen Sie die `B2CGraphClient.SendGraphPatchRequest(...)`-Methode auf Details zum Senden dieser Anforderung.

### Löschen von Benutzern

Das Löschen eines Benutzers ist nicht schwierig. Verwenden Sie einfach das HTTP DELETE-Verb, und erstellen Sie die URL mit der richtigen Objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Verwenden Sie zum Anzeigen eines Beispiels den unten angegebenen Befehl, und sehen Sie sich die DELETE-Anforderung an, die in der Konsole ausgegeben wird:

```
> B2C Delete-User <object-id-of-user>
```

Überprüfen Sie die `B2CGraphClient.SendGraphDeleteRequest(...)`-Methode auf Details zum Senden dieser Anforderung.

Es gibt viele weitere Aktionen, die Sie über die Benutzerverwaltung hinaus mit der Azure AD Graph-API ausführen können. Die [Referenz zur Azure AD Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) enthält die Details zu jeder Aktion und Beispielanforderungen.


## Verwenden von benutzerdefinierten Attributen

In nahezu jeder Consumeranwendung müssen bestimmte benutzerdefinierte Informationen zu einem Benutzerprofil gespeichert werden. Eine Möglichkeit ist das Definieren eines benutzerdefinierten Attributs in Ihrem B2C-Verzeichnis, damit Sie dieses Attribut genau wie jede andere Eigenschaft eines Benutzerobjekts behandeln können. Sie können das Attribut aktualisieren und löschen, nach dem Attribut abfragen, es in Anmeldetoken als Anspruch senden usw.

Informationen zum Definieren eines benutzerdefinierten Attributs in Ihrem B2C-Verzeichnis finden Sie unter [Benutzerdefinierte Attribute in B2C Preview – Referenz](active-directory-b2c-reference-custom-attr.md).

Sie können die benutzerdefinierten Attribute, die in Ihrem B2C-Verzeichnis definiert sind, mit dem B2CGraphClient anzeigen:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Die Ausgaben dieser Funktionen umfassen die Details der einzelnen benutzerdefinierten Attribute, z. B.:

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

Sie können den vollständigen Namen verwenden, z. B. `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number` als Eigenschaft in Ihren Benutzerobjekten. Aktualisieren Sie einfach die `.json`-Datei mit der neuen Eigenschaft und einem Wert für die Eigenschaft, und führen Sie Folgendes aus:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Das ist schon alles. Mit dem B2CGraphClient verfügen Sie jetzt über eine Dienstanwendung, mit der Ihre B2C-Verzeichnisbenutzer programmgesteuert verwaltet werden können. Sie nutzt ihre eigene Anwendungsidentität für die Authentifizierung gegenüber der Azure AD Graph-API und beschafft Token mithilfe eines geheimen Clientschlüssels. Beachten Sie beim Einbinden dieser Funktionalität in Ihre eigene Anwendung einige wichtige Punkte für B2C-Apps:

- Sie müssen der Anwendung im Verzeichnis die richtigen Berechtigungen gewähren.
- Vorläufig müssen Sie ADAL v2 zum Beschaffen von Zugriffstoken verwenden (oder Sie können Protokollnachrichten direkt ohne Bibliothek senden).
- Verwenden Sie beim Aufrufen der Graph-API [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Beim Erstellen und Aktualisieren von Consumerbenutzern müssen einige erforderliche Eigenschaften beachtet werden, die oben beschrieben sind.

Wenn Sie Fragen haben oder Aktionen anfordern möchten, die Sie mit der Graph-API in Ihrem B2C-Verzeichnis durchführen möchten, können Sie sich gern an uns wenden. Geben Sie einen Kommentar zu einem Artikel ein, oder legen Sie im GitHub-Repository des Codebeispiels einen entsprechenden Eintrag an.

<!---HONumber=Sept15_HO3-->