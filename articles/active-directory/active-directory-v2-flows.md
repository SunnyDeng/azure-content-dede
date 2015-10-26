<properties
	pageTitle="App-Modell v2.0: App-Typen | Microsoft Azure"
	description="Die App- und Szenariotypen, die vom Azure AD-App-Modell v2.0 (öffentliche Vorschaufunktion) unterstützt werden."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): App-Typen
Das App-Modell v2.0 unterstützt die Authentifizierung für eine Vielzahl moderner App-Architekturen, die alle auf den branchenüblichen Protokollen [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) und/oder [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) basieren. Dieses Dokument beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform erstellen können. Es hilft Ihnen beim Verständnis der Szenarien auf oberer Ebene, bevor Sie [sich direkt an den Code wagen](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Die Grundlagen
Jede App, die das App-Modell v2.0 nutzt, muss unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com) registriert werden. Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendungs-ID**, die Ihre Anwendung eindeutig identifiziert.
- Einen **Umleitungs-URI**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Einige andere szenariospezifische Werte. Erfahren Sie nähere Einzelheiten, wie Sie [eine App registrieren](active-directory-v2-app-registration.md).

Sobald die App registriert ist, kommuniziert sie mit Azure AD, indem sie Anforderungen an den v2.0-Endpunkt sendet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Jede Interaktion der App mit dem v2.0-Endpunkt folgt einem ähnlichen Muster auf oberer Ebene:

1. Die App leitet den Endbenutzer zur Anmeldung an den v2.0-Endpunkt.
2. Der Benutzer authentifiziert sich, indem er beispielsweise Benutzernamen und Kennwort eingibt.
3. Der Benutzer autorisiert die App, an seiner Stelle zu agieren, indem er der App die angeforderten Berechtigungen erteilt.
4. Die Anwendung erhält ein bestimmtes Sicherheitstoken vom v2.0-Endpunkt.
5. Die App verwendet das Sicherheitstoken für den Zugriff auf vertrauliche Informationen oder eine Ressource.
6. Der Ressourcenserver überprüft das Sicherheitstoken, um sicherzustellen, dass der Zugriff gewährt werden kann.
7. Die Anwendung aktualisiert das Sicherheitstoken in regelmäßigen Abständen.

<!-- TODO: Need a page for libraries to link to -->
Jeder dieser sieben Schritte unterscheidet sich je nach Typ der erstellten Anwendung geringfügig. In unseren Open Source-Bibliotheken werden die Einzelheiten abgedeckt. Weitere Informationen finden Sie unter [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md) und in den konkreten Beispielen.

## Web-Apps
Für Web-Apps (.NET, PHP, Java, Ruby, Python, Knoten usw.), auf die der Zugriff über einen Browser erfolgt, unterstützt das App-Modell v2.0 die Benutzeranmeldung mit [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). In OpenID Connect empfängt die Web-App ein `id_token`, ein Sicherheitstoken, das die Identität des Benutzers überprüft und Informationen über den Benutzer in Form von Ansprüchen enthält:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

Alle Arten von Token und Ansprüchen, die für eine App zur Verfügung stehen, sind im [v2.0-Tokenverweis](active-directory-v2-tokens.md) dargestellt.

In Webserver-Apps werden beim Authentifizierungsablauf für die Anmeldung folgende Schritte auf hoher Ebenen durchgeführt:

![Abbildung der Web-App-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Die Überprüfung des ID-Tokens mit einem öffentlichen Signaturschlüssel, der vom v2.0-Endpunkt empfangen wird, ist ausreichend, um die Benutzeridentität zu validieren und ein Sitzungscookie festzulegen, das zur Identifizierung des Benutzers bei nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, testen Sie eines der Web-App-Codebeispiele für die Anmeldung im Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started).

Neben der einfachen Anmeldung benötigt eine Webserver-App möglicherweise auch den Zugriff auf einige andere Webdienste wie z. B. eine REST-API. In diesem Fall kann die Webserver-App in einem kombinierten OpenID Connect- und OAuth 2.0-Vorgang ausgeführt werden, indem der [OAuth 2.0-Autorisierungscodefluss](active-directory-v2-protocols.md#oauth2-authorization-code-flow) verwendet wird. Dieses Szenario wird weiter unten im [Abschnitt zu den Web-APIs](#web-apis) und im [Thema „Erste Schritte mit Web-Apps/Web-APIs“](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md) abgedeckt.

## Web-APIs
Sie können das App-Modell v2.0 auch zum Absichern von Webdiensten verwenden, wie z. B. die RESTful-Web-API Ihrer App. Anstelle von ID-Token und Sitzungscookies verwenden Web-APIs OAuth 2.0-Zugriffstoken zum Sichern ihrer Daten und zum Authentifizieren eingehender Anforderungen. Der Aufrufer einer Web-API fügt ein Zugriffstoken in den Autorisierungs-Header einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API kann dann das Zugriffstoken zum Überprüfen der Identität des API-Aufrufers verwenden, und Informationen über den Aufrufer aus Ansprüchen extrahieren, die in den Zugriffstoken codiert sind. Alle Arten von Token und Ansprüchen, die für eine App zur Verfügung stehen, sind im [v2.0-Tokenverweis](active-directory-v2-tokens.md) dargestellt.

Eine Web-API kann Benutzern die Möglichkeit geben, sich für oder gegen bestimmte Funktionen oder Daten zu entschieden, indem sie Berechtigungen erhalten, die auch [Bereiche](active-directory-v2-scopes.md) genannt werden. Damit eine aufrufende App Berechtigungen für einen Bereich erhält, muss der Benutzer für den Bereich während eines Ablaufs seine Zustimmung erteilen. Der v2.0-Endpunkt kümmert sich darum, den Benutzer um Erlaubnis zu fragen, und zeichnet die Berechtigungen in allen Zugriffstoken auf, die die Web-API empfängt. Die Web-API muss nur noch die erhaltenen Zugriffstoken überprüfen, die sie bei jedem Aufruf erhält, und die entsprechenden Autorisierungsprüfungen durchführen.

Eine Web-API kann Zugriffstoken von allen Apptypen empfangen, einschließlich Webserver-Apps, Desktop-Apps, mobilen Apps, Single Page-Apps, serverseitigen Daemons und sogar anderen Web-APIs. Als Beispiel sehen wir uns den vollständigen Vorgang für eine Webserver-App an, die eine Web-API aufruft.

![Abbildung der Web-App-Web-API-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Weitere Informationen zu Autorisierungscodes, Aktualisierungstoken und die detaillierten Schritte zum Abrufen von Zugriffstoken finden Sie im [OAuth 2.0-Protokoll](active-directory-v2-protocols.md#oauth2-authorization-code-flow).

Weitere Informationen zum Sichern einer Web-API mit den App-Modell v2.0- und OAuth 2.0-Zugriffstoken finden Sie in den Web-API-Codebeispielen im [Abschnitt "Erste Schritte"](active-directory-appmodel-v2-overview.md#getting-started).


## Mobile und systemeigene Apps
Auf einem Gerät installierte Apps wie mobile und Desktop-Apps benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs, die Daten speichern und verschiedene Funktionen im Auftrag eines Benutzers ausführen. Diese Apps können sich auf Back-End-Diensten anmelden und die Autorisierung hinzufügen, und zwar mithilfe des v2.0-Modells und des [OAuth 2.0-Autorisierungscodeflusses](active-directory-v2-protocols.md#oauth2-authorization-code-flow).

Bei diesem Vorgang empfängt die App bei der Anmeldung des Benutzers einen Autorisierungscode vom v2.0-Endpunkt, der die Berechtigung für die App darstellt, Back-End-Dienste für den derzeit angemeldeten Benutzer aufzurufen. Die App kann den Autorisierungscode dann in den Hintergrund treten lassen und gegen ein OAuth 2.0-Zugriffstoken und ein -Aktualisierungstoken austauschen. Die App kann mithilfe des Zugriffstokens Web-APIs in HTTP-Anforderungen authentifizieren und kann das Aktualisierungstoken verwenden, um neue Zugriffstoken zu erhalten, wenn die älteren abgelaufen sind.

![Abbildung der Verantwortlichkeitsbereiche systemeigener Apps](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Aktuelle Einschränkungen der Vorschau
Diese Typen von Apps werden derzeit von der App-Modell v2.0-Vorschau nicht unterstützt, sind jedoch in Planung, um bei allgemeiner Verfügbarkeit rechtzeitig unterstützt zu werden. Zusätzliche Einschränkungen für die öffentliche Vorschauversion von App-Modell v2.0 werden im [Artikel „Einschränkungen der v2.0-Vorschau“](active-directory-v2-limitations.md) beschrieben.

### Singe Page-Apps (Javascript)
Viele moderne Apps verfügen über ein Single Page-App-Front-End, das in erster Linie in Javascript geschrieben ist und häufig SPA-Frameworks wie z. B. AngularJS, Ember.js oder Durandal verwendet. Der allgemein verfügbare Azure AD-Dienst unterstützt diese Apps mithilfe des [impliziten OAuth 2.0-Ablaufs](active-directory-v2-protocols.md#oauth2-implicit-flow). Dieser Ablauf steht jedoch für das App-Modell v2.0 noch nicht zur Verfügung. Dies wird sich in Kürze ändern.

Wenn Sie darauf gespannt sind, wie eine SPA mit dem App-Modell v2.0 funktioniert, können Sie die Authentifizierung mit dem [Webserver-Appvorgang](#web-apps) wie oben beschrieben implementieren. Dies ist allerdings nicht die empfohlene Vorgehensweise, und die Dokumentation für dieses Szenario ist begrenzt. Wenn Sie ein Gefühl für das SPA-Szenario bekommen möchten, sehen Sie sich das [allgemein verfügbare Azure AD-SPA-Codebeispiel](active-directory-devquickstarts-angular.md) an.

### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) die Authentifizierung durchführen und Token erhalten. Dies funktioniert über den [OAuth 2.0-Client-Anmeldeinformationsfluss](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Dieser Vorgang wird derzeit nicht vom App-Modell v2.0 unterstützt, d. h. Apps können nur dann Token abrufen, nachdem ein interaktiver Benutzeranmeldevorgang aufgetreten ist. Der Vorgang für die Client-Anmeldeinformationen wird in naher Zukunft hinzugefügt. Falls die Client-Anmeldeinformationen an den allgemein verfügbaren Azure AD-Service übertragen werden sollen, sehen Sie sich das [Daemon-Beispiel auf GitHub](https://github.com/AzureADSamples/Daemon-DotNet) an.

### Verkettete Web-APIs (Im-Auftrag-von)
Viele Architekturen umfassen eine Web-API, die eine andere heruntergestreamte Web-API aufrufen muss. Beide werden vom App-Modell v2.0 gesichert. Dieses Szenario kommt häufig in systemeigenen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst aufruft, wie z. B. Office 365 oder die Graph-API.

Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als [Im-Auftrag-von-Vorgang](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Der Im-Auftrag-von-Vorgang ist in der Vorschaufunktion des App-Modells v2.0 derzeit noch nicht implementiert. Um sich die Funktionsweise dieses Vorgangs im allgemein verfügbaren Azure AD-Dienst anzusehen, wechseln Sie zum [Im-Auftrag-von-Codebeispiel auf GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=Oct15_HO3-->