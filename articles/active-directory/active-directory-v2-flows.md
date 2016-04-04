<properties
	pageTitle="Typen des v2.0-Endpunkts | Microsoft Azure"
	description="App- und Szenariotypen, die vom Azure AD v2.0-Endpunkt unterstützt werden."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Typen des v2.0-Endpunkts
Der v2.0-Endpunkt unterstützt die Authentifizierung für eine Vielzahl moderner App-Architekturen, die alle auf den branchenüblichen Protokollen [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) und/oder [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) basieren. Dieses Dokument beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform erstellen können. Es hilft Ihnen beim Verständnis der Szenarien auf oberer Ebene, bevor Sie [sich direkt an den Code wagen](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
	Nicht alle Szenarios und Funktionen von Azure Active Directory werden vom v2.0-Endpunkt unterstützt. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den v2.0-Endpunkt verwenden sollten.

## Grundlagen
Jede App, die den v2.0-Endpunkt nutzt, muss unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com) registriert werden. Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendungs-ID**, die Ihre Anwendung eindeutig identifiziert.
- Einen **Umleitungs-URI**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Einige andere szenariospezifische Werte. Erfahren Sie nähere Einzelheiten, wie Sie [eine App registrieren](active-directory-v2-app-registration.md).

Nach dem Registrieren kommuniziert eine App mit Azure AD, indem Anforderungen an den Azure Active Directory v2.0-Endpunkt gesendet werden. Wir stellen Open Source-Frameworks und -Bibliotheken bereit, über die die Details dieser Anforderungen verarbeitet werden. Wahlweise können Sie auch selbst die Authentifizierungslogik implementieren, indem Sie Anforderungen an diese Endpunkte erstellen:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## Web-Apps
Bei Web-Apps (.NET, PHP, Java, Ruby, Python, Node usw.), auf die der Zugriff über einen Browser erfolgt, können Sie die Benutzeranmeldung mit [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) durchführen. In OpenID Connect empfängt die Web-App ein `id_token`, ein Sicherheitstoken, das die Identität des Benutzers überprüft und Informationen über den Benutzer in Form von Ansprüchen enthält:

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

Neben der einfachen Anmeldung benötigt eine Webserver-App möglicherweise auch den Zugriff auf einige andere Webdienste wie z. B. eine REST-API. In diesem Fall kann die Webserver-App in einem kombinierten OpenID Connect- und OAuth 2.0-Vorgang ausgeführt werden, indem der [OAuth 2.0-Autorisierungscodefluss](active-directory-v2-protocols.md#oauth2-authorization-code-flow) verwendet wird. Dieses Szenario wird in den [ersten Schritten zu Web-Apps und Web-APIs](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md) abgedeckt.

## Web-APIs
Sie können den v2.0-Endpunkt auch zum Schützen von Webdiensten verwenden, z. B. der REST-Web-API Ihrer App. Anstelle von ID-Token und Sitzungscookies verwenden Web-APIs OAuth 2.0-Zugriffstoken zum Sichern ihrer Daten und zum Authentifizieren eingehender Anforderungen. Der Aufrufer einer Web-API fügt ein Zugriffstoken in den Autorisierungs-Header einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API kann dann das Zugriffstoken zum Überprüfen der Identität des API-Aufrufers verwenden, und Informationen über den Aufrufer aus Ansprüchen extrahieren, die in den Zugriffstoken codiert sind. Alle Arten von Token und Ansprüchen, die für eine App zur Verfügung stehen, sind im [v2.0-Tokenverweis](active-directory-v2-tokens.md) dargestellt.

Eine Web-API kann Benutzern die Möglichkeit geben, sich für oder gegen bestimmte Funktionen oder Daten zu entschieden, indem sie Berechtigungen erhalten, die auch [Bereiche](active-directory-v2-scopes.md) genannt werden. Damit eine aufrufende App Berechtigungen für einen Bereich erhält, muss der Benutzer für den Bereich während eines Ablaufs seine Zustimmung erteilen. Der v2.0-Endpunkt kümmert sich darum, den Benutzer um Erlaubnis zu fragen, und zeichnet die Berechtigungen in allen Zugriffstoken auf, die die Web-API empfängt. Die Web-API muss nur noch die erhaltenen Zugriffstoken überprüfen, die sie bei jedem Aufruf erhält, und die entsprechenden Autorisierungsprüfungen durchführen.

Eine Web-API kann Zugriffstoken von allen Apptypen empfangen, einschließlich Webserver-Apps, Desktop-Apps, mobilen Apps, Single Page-Apps, serverseitigen Daemons und sogar anderen Web-APIs. Der allgemeine Ablauf bei der Authentifizierung einer Web-API ist im Folgenden skizziert:

![Abbildung der Web-API-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Weitere Informationen zu Autorisierungscodes, Aktualisierungstoken und die detaillierten Schritte zum Abrufen von Zugriffstoken finden Sie im [OAuth 2.0-Protokoll](active-directory-v2-protocols-oauth-code.md).

Informationen zum Schützen einer Web-API mit OAuth2-Zugriffstoken finden Sie in den Web-API-Codebeispielen im [Abschnitt „Erste Schritte“](active-directory-appmodel-v2-overview.md#getting-started).


## Mobile und native Apps
Auf einem Gerät installierte Apps wie mobile und Desktop-Apps benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs, die Daten speichern und verschiedene Funktionen im Auftrag eines Benutzers ausführen. Diese Apps können sich mithilfe des [OAuth 2.0-Autorisierungscodeflusses](active-directory-v2-protocols-oauth-code.md) auf Back-End-Diensten anmelden und die Autorisierung hinzufügen.

Bei diesem Vorgang empfängt die App bei der Anmeldung des Benutzers einen Autorisierungscode vom v2.0-Endpunkt, der die Berechtigung für die App darstellt, Back-End-Dienste für den derzeit angemeldeten Benutzer aufzurufen. Die App kann den Autorisierungscode dann in den Hintergrund treten lassen und gegen ein OAuth 2.0-Zugriffstoken und ein -Aktualisierungstoken austauschen. Die App kann mithilfe des Zugriffstokens Web-APIs in HTTP-Anforderungen authentifizieren und kann das Aktualisierungstoken verwenden, um neue Zugriffstoken zu erhalten, wenn die älteren abgelaufen sind.

![Abbildung der Verantwortlichkeitsbereiche systemeigener Apps](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Single Page-Apps (JavaScript)
Viele moderne Anwendungen verfügen über ein Single-Page-Application (SPA)-Front-End, das in erster Linie in JavaScript geschrieben ist und häufig Frameworks verwendet, z. B. AngularJS, Ember.js, Durandal usw. Der Azure AD v2.0-Endpunkt unterstützt diese Apps mithilfe des [impliziten OAuth 2.0-Flusses](active-directory-v2-protocols-implicit.md).

In diesem Datenfluss empfängt die App Token vom Autorisierungsendpunkt der Version 2.0 direkt, ohne dass eine Kommunikation zwischen Back-End-Server und Server stattfindet. Dadurch können sämtliche Authentifizierungslogik und die Verarbeitung der Sitzung vollständig im JavaScript-Client erfolgen, ohne Seitenumleitungen durchzuführen.

![Abbildung der Verantwortlichkeitsbereiche impliziter Abläufe](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Um dieses Szenario in Aktion zu sehen, testen Sie eines der Codebeispiele für die App mit einer einzigen Seite im Abschnitt [Erste Schritte](active-directory-appmodel-v2-overview.md#getting-started).

## Aktuelle Einschränkungen
Diese Typen von Apps werden derzeit vom v2.0-Endpunkt nicht unterstützt, dies ist jedoch in Planung. Weitere Einschränkungen für den v2.0-Endpunkt sind im [Artikel zu den Einschränkungen](active-directory-v2-limitations.md) beschrieben.

### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) die Authentifizierung durchführen und Token erhalten. Dies funktioniert über den OAuth 2.0-Client-Anmeldeinformationsfluss.

Der Ablauf für die Client-Anmeldeinformationen wird derzeit im v2.0-Endpunkt nicht unterstützt. Informationen zu diesem Vorgang im allgemein verfügbaren Azure AD-Dienst finden Sie im [Daemon-Codebeispiel auf GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### Verkettete Web-APIs (Im-Auftrag-von)
Viele Architekturen umfassen eine Web-API, die eine andere Downstream-Web-API aufrufen muss. Beide sind jeweils durch den v2.0-Endpunkt gesichert. Dieses Szenario kommt häufig in systemeigenen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst aufruft, wie z. B. Office 365 oder die Graph-API.

Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als [Im-Auftrag-von-Vorgang](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Der Im-Auftrag-von-Vorgang ist im v2.0-Endpunkt derzeit jedoch noch nicht implementiert. Um sich die Funktionsweise dieses Vorgangs im allgemein verfügbaren Azure AD-Dienst anzusehen, wechseln Sie zum [Im-Auftrag-von-Codebeispiel auf GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_0323_2016-->