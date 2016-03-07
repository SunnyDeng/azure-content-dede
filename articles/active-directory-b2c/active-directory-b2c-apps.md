<properties
	pageTitle="Azure AD B2C Preview | Microsoft Azure"
	description="Informationen zu den Anwendungstypen, die Sie in der Vorschauversion von Azure Active Directory B2C erstellen können."
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
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Azure Active Directory B2C-Vorschau: Anwendungstypen

Azure Active Directory (Azure AD) B2C unterstützt die Authentifizierung für eine Vielzahl von modernen App-Architekturen. Diese basieren alle auf den branchenüblichen Protokollen [OAuth 2.0](active-directory-b2c-reference-protocols.md) oder [OpenID Connect](active-directory-b2c-reference-protocols.md). Dieses Dokument beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform erstellen können. Außerdem verdeutlicht es die allgemeinen Szenarien, bevor Sie mit dem [Erstellen von Anwendungen beginnen](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Grundlagen
Jede App, die Azure AD B2C verwendet, muss über das [Azure-Portal](https://portal.azure.com/) im [B2C-Verzeichnis](active-directory-b2c-get-started.md) registriert werden. Beim App-Registrierungsprozess werden einige Werte gesammelt und der App zugewiesen:

- Eine **Anwendungs-ID**, die Ihre App eindeutig identifiziert.
- Einen **Umleitungs-URI**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Andere szenariospezifische Werte. Weitere Informationen finden Sie im Artikel zum [Registrieren von Apps](active-directory-b2c-app-registration.md).

Sobald die App registriert ist, kommuniziert sie mit Azure AD, indem sie Anforderungen an den v2.0-Endpunkt von Azure AD sendet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Für jede Anforderung, die an Azure AD B2C gesendet wird, wird eine **Richtlinie** angegeben. Eine Richtlinie steuert das Verhalten von Azure AD. Mit diesen Endpunkten können Sie auch einen extrem anpassbaren Satz von Benutzeroberflächen erstellen. Beispiele für allgemeine Richtlinien sind Anmelderichtlinien, Registrierungsrichtlinien und Profilbearbeitungsrichtlinien. Wenn Sie mit Richtlinien nicht vertraut sind, sollten Sie sich zuerst die Informationen zum [erweiterbaren Richtlinienframework](active-directory-b2c-reference-policies.md) von Azure AD B2C ansehen, bevor Sie weiterlesen.

Die Interaktion der einzelnen Apps mit einem v2.0-Endpunkt folgt einem ähnlichen allgemeinen Muster:

1. Die App leitet den Endbenutzer an den v2.0-Endpunkt weiter, um eine [Richtlinie](active-directory-b2c-reference-policies.md) auszuführen.
2. Der Benutzer schließt die Richtlinie gemäß Richtliniendefinition ab.
4. Die App erhält ein bestimmtes Sicherheitstoken vom v2.0-Endpunkt.
5. Die App verwendet das Sicherheitstoken für den Zugriff auf geschützte Informationen oder eine geschützte Ressource.
6. Der Ressourcenserver überprüft das Sicherheitstoken, um sicherzustellen, dass der Zugriff gewährt werden kann.
7. Die App aktualisiert das Sicherheitstoken in regelmäßigen Abständen.

<!-- TODO: Need a page for libraries to link to -->
Diese Schritte können sich je nach erstelltem App-Typ geringfügig unterscheiden. Mit Open Source-Bibliotheken können Sie die Details festlegen.

## Web-Apps
Für Web-Apps (einschließlich .NET, PHP, Java, Ruby, Python und Node), die auf einem Server gehostet werden und über einen Browser zugänglich sind, unterstützt Azure AD B2C [OpenID Connect](active-directory-b2c-reference-protocols.md) für alle Benutzeroberflächen. Hierzu gehören die Anmeldung, Registrierung und Profilverwaltung. In der Azure AD B2C-Implementierung von OpenID Connect initiiert Ihre Web-App diese Benutzeroberflächen, indem Authentifizierungsanforderungen für Azure AD ausgegeben werden. Das Ergebnis der Anforderung ist ein `id_token`. Dieses Sicherheitstoken stellt die Identität des Benutzers dar. Darüber hinaus werden Informationen über den Benutzer in Form von Ansprüchen bereitgestellt:

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

Informationen zu allen Arten von Token und Ansprüchen, die für eine App zur Verfügung stehen, finden Sie in der [B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md).

In einer Web-App sind für jede Ausführung einer [Richtlinie](active-directory-b2c-reference-policies.md) im Allgemeinen folgende Schritte erforderlich:

![Abbildung der Web-App-Verantwortlichkeitsbereiche](./media/active-directory-b2c-apps/webapp.png)

Die Überprüfung von `id_token` mithilfe eines von Azure AD erhaltenen öffentlichen Signaturschlüssels ist ausreichend, um die Identität des Benutzers zu überprüfen. Dadurch wird außerdem ein Sitzungscookie festgelegt, das zur Identifizierung des Benutzers in nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, können Sie eines der Web-App-Codebeispiele für die Anmeldung im Abschnitt [Erste Schritte](active-directory-b2c-overview.md#getting-started) testen.

Neben der Bereitstellung der einfachen Anmeldung benötigt eine Webserver-App unter Umständen auch Zugriff auf einen Back-End-Webdienst. In diesem Fall kann die Web-App einen etwas anderen [OpenID Connect-Ablauf](active-directory-b2c-reference-oidc.md) durchführen und Token anhand von Autorisierungscodes und Aktualisierungstoken beschaffen. Dieses Szenario ist im folgenden [Abschnitt zu Web-APIs](#web-apis) dargestellt.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web-APIs
Sie können Azure AD B2C zum Schützen von Webdiensten wie z. B. der RESTful-Web-API Ihrer App verwenden. Web-APIs können OAuth 2.0 zum Sichern ihrer Daten einsetzen. Sie können auch eingehende HTTP-Anforderungen anhand von Token authentifizieren. Der Aufrufer einer Web-API fügt ein Token im Autorisierungsheader einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API kann dann mit dem Token die Identität des API-Aufrufers überprüfen und Informationen über den Aufrufer aus Ansprüchen extrahieren, die im Token codiert sind. Informationen zu allen Arten von Token und zu den für eine App verfügbaren Ansprüchen finden Sie in der [Azure AD B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
	Die Vorschauversion von Azure AD B2C unterstützt derzeit nur Web-APIs, auf die von ihren eigenen bekannten Clients zugegriffen wird. Ihre vollständige App kann beispielsweise eine iOS-App, eine Android-App und eine Back-End-Web-API umfassen. Diese Architektur wird vollständig unterstützt. Das Erteilen des Zugriffs auf dieselbe Web-API an einen Partnerclient, beispielsweise eine andere iOS-App, wird derzeit nicht unterstützt. Alle Komponenten Ihrer vollständigen App müssen eine einzige Anwendungs-ID gemeinsam verwenden.

Eine Web-API kann Token von vielen Clienttypen empfangen, einschließlich Web-Apps, Desktop- und mobilen Apps, Single-Page-Apps, serverseitigen Daemons und anderen Web-APIs. Hier sehen Sie ein Beispiel für den vollständigen Ablauf für eine Web-App, die eine Web-API aufruft.

![Abbildung der Web-App-Web-API-Verantwortlichkeitsbereiche](./media/active-directory-b2c-apps/webapi.png)

Weitere Informationen zu Autorisierungscodes, Aktualisierungstoken und den Schritten zum Abrufen von Token finden Sie im [OAuth 2.0-Protokoll](active-directory-b2c-reference-oauth-code.md).

Weitere Informationen zum Schützen einer Web-API mit Azure AD B2C finden Sie in den Tutorials zur Web-API im [Abschnitt mit den ersten Schritten](active-directory-b2c-overview.md#getting-started).

## Mobile und native Apps
Auf Geräten installierte Apps, z. B. mobile Apps und Desktop-Apps, benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs im Auftrag von Benutzern. Sie können Ihren nativen Apps angepasste Oberflächen für die Identitätsverwaltung hinzufügen und Back-End-Dienste sicher aufrufen, indem Sie Azure AD B2C und den [Autorisierungscodeablauf von OAuth 2.0](active-directory-b2c-reference-oauth-code.md) verwenden.

Bei diesem Ablauf führt die App [Richtlinien](active-directory-b2c-reference-policies.md) aus und empfängt einen `authorization_code` von Azure AD, nachdem der Benutzer die Richtlinie abgeschlossen hat. Der `authorization_code` stellt die Berechtigung der App dar, Back-End-Dienste im Namen des derzeit angemeldeten Benutzers aufzurufen. Die App kann dann den `authorization_code` im Hintergrund für ein `id_token` und ein `refresh_token` austauschen. Mit dem `id_token` kann die App sich in HTTP-Anforderungen bei einer Back-End-Web-API authentifizieren. Sie kann auch das `refresh_token` zum Abrufen eines neuen `id_token` verwenden, wenn ein älteres abläuft.

> [AZURE.NOTE]
	Die Vorschau von Azure AD B2C unterstützt derzeit nur ID-Token, die für den Zugriff auf den eigenen Back-End-Webdienst einer App verwendet werden. Ihre vollständige App kann beispielsweise eine iOS-App, eine Android-App und eine Back-End-Web-API umfassen. Diese Architektur wird vollständig unterstützt. Das Gewähren des Zugriffs auf eine Partner-Web-API über OAuth 2.0-Zugriffstoken wird für Ihre iOS-App derzeit nicht unterstützt. Alle Komponenten Ihrer vollständigen App müssen eine einzige Anwendungs-ID gemeinsam verwenden.

![Abbildung der Verantwortlichkeitsbereiche systemeigener Apps](./media/active-directory-b2c-apps/native.png)

## Aktuelle Einschränkungen der Vorschau
Die folgenden Typen von Apps werden derzeit von der Vorschau von Azure AD B2C nicht unterstützt, sind jedoch in Planung, um bei allgemeiner Verfügbarkeit rechtzeitig unterstützt zu werden. Weitere Einschränkungen für die Vorschauversion von Azure AD B2C werden unter [Einschränkungen](active-directory-b2c-limitations.md) beschrieben.

### Single-Page-Apps (JavaScript)
Viele moderne Apps besitzen ein Single-Page-App-Front-End, das in erster Linie in JavaScript geschrieben ist. Sie verwenden häufig ein Framework wie AngularJS, Ember.js oder Durandal. Der allgemein verfügbare Azure AD-Dienst unterstützt diese Apps über den impliziten OAuth 2.0-Fluss. Dieser Ablauf ist in Azure AD B2C noch nicht verfügbar. Dies sollte jedoch in Kürze der Fall sein.

### Daemons/serverseitige Apps
Apps, die lang andauernde Prozesse enthalten oder ohne Benutzereingriff arbeiten, benötigen auch die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) sowie über den OAuth 2.0-Ablauf für Clientanmeldeinformationen die Authentifizierung durchführen und Token abrufen.

Der Ablauf wird derzeit von Azure AD B2C nicht unterstützt. Diese Apps können Token erst abrufen, nachdem ein interaktiver Benutzerablauf stattgefunden hat. Der Ablauf für Clientanmeldeinformationen wird in naher Zukunft hinzugefügt.

### Web-API-Ketten („Im Auftrag von“-Ablauf)
Viele Architekturen umfassen eine Web-API, von der eine andere Downstream-Web-API aufgerufen werden muss, wobei beide durch Azure AD B2C gesichert sind. Dieses Szenario wird häufig bei nativen Clients mit einem Web-API-Back-End eingesetzt. Dieses ruft dann einen Microsoft-Onlinedienst wie z. B. die Azure AD Graph-API auf.

Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als „Im Auftrag von“-Ablauf. Der „Im Auftrag von“-Ablauf ist in der Vorschau von Azure AD B2C derzeit noch nicht implementiert.

<!---HONumber=AcomDC_0224_2016-->