<properties
	pageTitle="App-Modell v2.0-Protokolle | Microsoft Azure"
	description="Die Protokolle, die von Azure AD-App-Modell v2.0 (öffentliche Vorschauversion) unterstützt werden."
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
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): Protokolle – OAuth 2.0 und OpenID Connect

Das App-Modell Version 2.0 bietet Identity-as-a-Service (IDaaS) für Ihre Apps durch die Unterstützung gängiger Standardprotokolle, OpenID Connect und OAuth 2.0. Während der Dienst standardkonform ist, kann es feine Unterschiede zwischen den beiden Implementierungen dieser Protokolle geben. Die hier bereitgestellten Informationen sind nützlich, wenn Sie Code direkt durch Senden und Verarbeiten von HTTP-Anforderungen schreiben, anstatt eine unserer Open Source-Bibliotheken zu nutzen. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Die Grundlagen
Jede App, die das App-Modell v2.0 nutzt, muss unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com) registriert werden. Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendungs-ID**, die Ihre App eindeutig identifiziert.
- Einen **Umleitungs-URI** oder **Paketbezeichner**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Einige andere szenariospezifische Werte. Erfahren Sie nähere Einzelheiten, wie Sie [eine App registrieren](active-directory-v2-app-registration.md).

Sobald die App registriert ist, kommuniziert sie mit Azure AD, indem sie Anforderungen an den v2.0-Endpunkt sendet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

In fast allen OAuth- und OpenID Connect-Vorgängen sind vier Beteiligte am Austausch involviert:

![OAuth 2.0-Rollen](../media/active-directory-v2-flows/protocols_roles.png)

- Der **Autorisierungsserver** ist der v2.0-Endpunkt. Er ist verantwortlich für das Sicherstellen der Identität des Benutzers, das Erteilen und Widerrufen des Zugriffs auf Ressourcen und das Ausstellen von Token. Er ist auch als Identitätsanbieter bekannt und verarbeitet auf sichere Weise alles im Zusammenhang mit den Informationen des Benutzers, dessen Zugriff und den Vertrauensstellungen zwischen den Beteiligten in einem Vorgang.
- Beim **Ressourcenbesitzer** handelt es sich normalerweise um den Endbenutzer. Diese Person besitzt die Daten und hat die Möglichkeit, Dritten den Zugriff auf die Daten oder die Ressource zu gewähren.
- Der **OAuth-Client** ist Ihre App, die durch ihre Anwendungs-ID identifiziert wird. Dies ist normalerweise der Beteiligte, mit dem der Endbenutzer interagiert, und der Token vom Autorisierungsserver anfordert. Der Client muss vom Besitzer der Ressource die Berechtigung zum Zugriff darauf erhalten.
- Der **Ressourcenserver** ist der Ort, an dem die Ressource oder die Daten abgelegt sind. Er vertraut dem Autorisierungsserver, dass der OAuth-Client sicher authentifizert und autorisiert wird, und verwendet Bearerzugriffstoken, um sicherzustellen, dass der Zugriff auf eine Ressource gewährt werden kann.


## Token
Die App-Modell v2.0-Implementation von OAuth 2.0 und OpenID Connect macht ausgiebig Gebrauch von Bearertoken (auch in Form von JWTs). Ein Trägertoken ist ein einfaches Sicherheitstoken, das dem „Träger“ den Zugriff auf eine geschützte Ressource ermöglicht. In diesem Kontext ist der „Träger“ jede beliebige Partei, die das Token vorweisen kann. Um das Trägertoken zu erhalten, muss sich die Partei zwar zunächst bei Azure AD authentifizieren, falls jedoch keine Maßnahmen ergriffen werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer fremden Partei abgefangen und verwendet werden. Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine unbefugte Verwendung durch nicht autorisierte Parteien verhindert. Trägertoken besitzen dagegen keinen solchen Mechanismus und müssen über einen sicheren Kanal wie etwa Transport Layer Security (HTTPS) übertragen werden. Wird ein Trägertoken als Klartext gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abfangen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Trägertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert. Weitere Sicherheitsüberlegungen zu Trägertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).

Weitere Informationen zu verschiedenen Tokentypen, die im App-Modell v2.0 verwendet werden, finden Sie in [der App-Modell, Version 2.0 – Tokenreferenz](active-directory-v2-tokens.md).

## Protokolle

Wenn Sie einige Beispielanforderungen sehen möchten, beginnen Sie mit einem der folgenden Lernprogramme. Jedes Lernprogramm entspricht einem bestimmten Szenario. Wenn Sie Hilfe bei der Bestimmung des richtigen Arbeitsablaufs benötigen, informieren Sie sich über die [App-Typen, die mit Version 2.0 des App-Modells erstellt werden können](active-directory-v2-flows.md).

- [Erstellen von mobilen und systemeigenen Anwendungen mit OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Erstellen von Web-Apps mit OpenID Connect](active-directory-v2-protocols-oidc.md)
- [Erstellen von Apps mit einer einzigen Seite mit dem impliziten OAuth 2.0-Fluss](active-directory-v2-protocols-implicit.md)
- Erstellen von Daemons oder serverseitigen Prozessen mit dem OAuth 2.0-Fluss für Client-Anmeldeinformationen (in Kürze verfügbar)
- Abrufen von Token in einer Web-API mit dem OAuth 2.0-Fluss "Im-Auftrag-von" (in Kürze verfügbar)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) -->
<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_1203_2015-->