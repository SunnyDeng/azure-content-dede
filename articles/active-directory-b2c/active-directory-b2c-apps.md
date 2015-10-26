<properties
	pageTitle="Azure AD B2C Preview | Microsoft Azure"
	description="Informationen zu den Anwendungstypen, die Sie in der Preview-Version von Azure AD B2C erstellen können."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Azure AD B2C Preview: Anwendungstypen

Azure AD B2C unterstützt die Authentifizierung für eine Vielzahl moderner App-Architekturen, die alle auf den branchenüblichen Protokollen [OAuth 2.0](active-directory-b2c-reference-protocols.md) und/oder [OpenID Connect](active-directory-b2c-reference-protocols.md) basieren. Dieses Dokument beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform erstellen können. Es hilft Ihnen beim allgemeinen Verständnis der Szenarien, bevor Sie [sich direkt an den Code wagen](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Die Grundlagen
Jede App, die Azure AD B2C verwendet, muss im [B2C-Verzeichnis](https://portal.azure.com) im [Azure-Vorschauportal](active-directory-b2c-get-started.md) registriert werden. Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendungs-ID**, die Ihre Anwendung eindeutig identifiziert.
- Einen **Umleitungs-URI**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Einige andere szenariospezifische Werte. Ausführlichere Informationen erhalten Sie unter [Registrieren einer App](active-directory-b2c-app-registration.md).

Sobald die App registriert ist, kommuniziert sie mit Azure AD, indem sie Anforderungen an den Azure AD v2.0-Endpunkt sendet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Für jede Anforderung, die an Azure AD B2C gesendet wird, wird eine **Richtlinie** angegeben. Eine Richtlinie steuert das Verhalten von Azure AD und ermöglicht Ihnen die Verwendung dieser Endpunkte, um einen stark anpassbaren Satz von Benutzeroberflächen zu erstellen. Beispiele für allgemeine Richtlinien sind Registrierungsrichtlinien, Anmelderichtlinien und Profilbearbeitungsrichtlinien. Wenn Sie mit Richtlinien nicht vertraut sind, sollten Sie sich zuerst die Informationen zum [erweiterbaren Richtlinien-Framework](active-directory-b2c-reference-policies.md) von Azure AD B2C ansehen, bevor Sie weiterlesen.

Jede Interaktion der App mit dem v2.0-Endpunkt folgt einem ähnlichen Muster auf oberer Ebene:

1. Die App leitet den Endbenutzer zur Anmeldung an den v2.0-Endpunkt weiter, um eine [Richtlinie](active-directory-b2c-reference-policies.md) auszuführen.
2. Der Benutzer schließt die Richtlinie gemäß Richtliniendefinition ab.
4. Die Anwendung erhält ein bestimmtes Sicherheitstoken vom v2.0-Endpunkt.
5. Die App verwendet das Sicherheitstoken für den Zugriff auf vertrauliche Informationen oder eine Ressource.
6. Der Ressourcenserver überprüft das Sicherheitstoken, um sicherzustellen, dass der Zugriff gewährt werden kann.
7. Die Anwendung aktualisiert das Sicherheitstoken in regelmäßigen Abständen.

<!-- TODO: Need a page for libraries to link to -->
Jeder dieser Schritte unterscheidet sich je nach Typ der erstellten Anwendung geringfügig. In unseren Open Source-Bibliotheken werden die Einzelheiten abgedeckt.

## Web-Apps
Für Web-Apps (.NET, PHP, Java, Ruby, Python, Node usw.), die auf einem Server gehostet werden und auf die mit einem Browser zugegriffen wird, unterstützt Azure AD B2C [OpenID Connect](active-directory-b2c-reference-protocols.md) für alle Benutzeroberflächen, einschließlich Anmeldung, Registrierung und Profilverwaltung. In der Azure AD B2C-Implementierung von OpenID Connect initiiert Ihre Web-App diese Benutzeroberflächen, indem Authentifizierungsanforderungen für Azure AD ausgegeben werden. Das Ergebnis der Anforderung ist ein `id_token`. Dies ist ein Sicherheitstoken, das die Identität des Benutzers darstellt und die Informationen zum Benutzer in Form von Ansprüchen bereitstellt:

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

Informationen zu allen Arten von Token und Ansprüchen, die für eine App zur Verfügung stehen, finden Sie in der [Referenz zu B2C-Token](active-directory-b2c-reference-tokens.md).

In Web-Apps erfordert jede Ausführung einer [Richtlinie](active-directory-b2c-reference-policies.md) im Allgemeinen diese Schritte:

![Abbildung der Web-App-Verantwortlichkeitsbereiche](./media/active-directory-b2c-apps/webapp.png)

Die Überprüfung des ID-Tokens mit einem öffentlichen Signaturschlüssel, der von Azure AD empfangen wird, ist ausreichend, um die Benutzeridentität zu validieren und ein Sitzungscookie festzulegen, das zur Identifizierung des Benutzers bei nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, können Sie eines der Web-App-Codebeispiele für die Anmeldung im Abschnitt [Erste Schritte](active-directory-b2c-overview.md#getting-started) testen.

Neben der einfachen Anmeldung benötigt eine Webserver-App unter Umständen auch den Zugriff auf einige Back-End-Webdienste. In diesem Fall kann die Web-App einen etwas anderen [OpenID Connect-Ablauf](active-directory-b2c-reference-oidc.md) durchführen und Token beschaffen, indem Autorisierungstoken und Aktualisierungstoken verwendet werden. Dieses Szenario ist unten im [Abschnitt zu Web-APIs](#web-apis) dargestellt.

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web-APIs
Sie können Azure AD B2C auch zum Schützen von Webdiensten verwenden, z. B. der REST-Web-API Ihrer App. Web-APIs können OAuth 2.0 verwenden, um ihre Daten zu schützen, und eingehende HTTP-Anforderungen per Token authentifizieren. Der Aufrufer einer Web-API fügt ein Token in den Autorisierungs-Header einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API kann dann das Token zum Überprüfen der Identität des API-Aufrufers verwenden und Informationen über den Aufrufer aus Ansprüchen extrahieren, die im Token codiert sind. Informationen zu allen Arten von Token und Ansprüchen, die für eine App zur Verfügung stehen, finden Sie unter [Azure AD B2C-Token – Referenz](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]Die Preview-Version von Azure AD B2C unterstützt derzeit nur Web-APIs, auf die von ihren eigenen bekannten Clients zugegriffen wird. Ihre vollständige App kann beispielsweise eine iOS-App, eine Android-App und eine Back-End-Web-API umfassen. Diese Architektur wird vollständig unterstützt. Folgendes wird derzeit nicht unterstützt: Dass ein Drittanbieter-Client, z. B. eine andere iOS-App, ebenfalls auf dieselbe Web-API zugreift. Also müssen praktisch alle Komponenten Ihrer vollständigen App eine einzelne Anwendungs-ID gemeinsam nutzen.

Eine Web-API kann Token von allen Clienttypen empfangen, z. B. Web-Apps, Desktop- und mobilen Apps, Single Page-Apps, serverseitigen Daemons und sogar anderen Web-APIs. Als Beispiel sehen wir uns den vollständigen Vorgang für eine Web-App an, die eine Web-API aufruft.

![Abbildung der Web-App-Web-API-Verantwortlichkeitsbereiche](./media/active-directory-b2c-apps/webapi.png)

Weitere Informationen zu Autorisierungscodes, Aktualisierungstoken und den detaillierten Schritten zum Abrufen von Token finden Sie im [OAuth 2.0-Protokoll](active-directory-b2c-reference-oauth-code.md).

Weitere Informationen zum Schützen einer Web-API mit Azure AD B2C finden Sie in den Lernprogrammen zur Web-API im [Abschnitt mit den ersten Schritten](active-directory-b2c-overview.md#getting-started).
	
## Mobile und systemeigene Apps
Auf einem Gerät installierte Apps, z. B. mobile Apps und Desktop-Apps, benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs im Auftrag eines Benutzers. Sie können Ihren systemeigenen Apps angepasste Oberflächen für die Identitätsverwaltung hinzufügen und Back-End-Dienste sicher aufrufen, indem Sie Azure AD B2C und den [Autorisierungscodeablauf von OAuth 2.0](active-directory-b2c-reference-oauth-code.md) verwenden.

Bei diesem Ablauf führt die App [Richtlinien](active-directory-b2c-reference-policies.md) aus und empfängt ein Autorisierungscodeelement von Azure AD, nachdem der Benutzer die Richtlinie abgeschlossen hat. Das Autorisierungscodeelement stellt die Berechtigung der App dar, Back-End-Dienste im Namen des derzeit angemeldeten Benutzers aufzurufen. Die App kann den Autorisierungscode im Hintergrund dann gegen ein ID-Token und ein Aktualisierungstoken austauschen. Die App kann mithilfe des ID-Tokens eine Back-End-Web-API in HTTP-Anforderungen authentifizieren und das Aktualisierungstoken verwenden, um neue ID-Token zu erhalten, wenn die älteren abgelaufen sind.

> [AZURE.NOTE]Die Preview-Version von Azure AD B2C unterstützt derzeit nur das Abrufen von ID-Token, die zum Zugreifen auf den eigenen Back-End-Webdienst der App verwendet werden. Ihre vollständige App kann beispielsweise eine iOS-App, eine Android-App und eine Back-End-Web-API umfassen. Diese Architektur wird vollständig unterstützt. Folgendes wird derzeit nicht unterstützt: Dass Ihre iOS-App per OAuth 2.0-Zugriffstoken auf eine Drittanbieter-Web-API zugreift. Also müssen praktisch alle Komponenten Ihrer vollständigen App eine einzelne Anwendungs-ID gemeinsam nutzen.

![Abbildung der Verantwortlichkeitsbereiche systemeigener Apps](./media/active-directory-b2c-apps/native.png)

## Aktuelle Einschränkungen der Vorschau
Diese Typen von Apps werden derzeit von der Preview-Version von Azure AD B2C nicht unterstützt, sind jedoch in Planung, um bei allgemeiner Verfügbarkeit rechtzeitig unterstützt zu werden. Weitere Einschränkungen für die Preview-Version von Azure AD B2C sind im [Artikel zu den Einschränkungen](active-directory-b2c-limitations.md) beschrieben.

### Singe Page-Apps (Javascript)
Viele moderne Apps verfügen über ein Single Page-App-Front-End, das in erster Linie in Javascript geschrieben ist und häufig SPA-Frameworks wie z. B. AngularJS, Ember.js oder Durandal verwendet. Der allgemein verfügbare Azure AD-Dienst unterstützt diese Apps mithilfe des impliziten OAuth 2.0-Ablaufs. Dieser Ablauf steht jedoch für Azure AD B2C noch nicht zur Verfügung. Dies wird sich in Kürze ändern.

### Daemons und serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne das Vorhandensein eines Benutzers arbeiten, benötigen ebenfalls die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) die Authentifizierung durchführen und Token erhalten. Dies funktioniert über den OAuth 2.0-Client-Anmeldeinformationsfluss.

Dieser Ablauf wird derzeit von Azure AD B2C nicht unterstützt, d. h., Apps können nur dann Token abrufen, nachdem ein interaktiver Benutzervorgang aufgetreten ist. Der Vorgang für die Client-Anmeldeinformationen wird in naher Zukunft hinzugefügt.

### Web-API-Ketten (Im-Auftrag-von)
Viele Architekturen umfassen eine Web-API, von der eine andere Downstream-Web-API aufgerufen werden muss, wobei beide durch Azure AD B2C gesichert sind. Dieses Szenario kommt häufig in systemeigenen Clients mit Web-API-Back-End vor, das wiederum einen Microsoft Online-Dienst aufruft, z. B. die Azure AD Graph-API.

Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als „Im-Auftrag-von-Vorgang“. Der Im-Auftrag-von-Vorgang ist in der Preview-Version von Azure AD B2C derzeit noch nicht implementiert.

<!---HONumber=Oct15_HO3-->