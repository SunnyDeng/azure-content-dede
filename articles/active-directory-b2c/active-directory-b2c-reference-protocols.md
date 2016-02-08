<properties
	pageTitle="Vorschau für Azure AD B2C | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie Apps direkt mit den von der Vorschau für Azure AD B2C unterstützten Protokollen erstellen."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Vorschau für Azure AD B2C: Authentifizierungsprotokolle

Azure AD B2C bietet Identity-as-a-Service (IDaaS) für Ihre Apps durch die Unterstützung zweier gängiger Standardprotokolle: OpenID Connect und OAuth 2.0. Auch wenn der Dienst standardkonform ist, kann es feine Unterschiede zwischen den beiden Implementierungen dieser Protokolle geben. Die hier bereitgestellten Informationen sind nützlich, wenn Sie Code direkt durch Senden und Verarbeiten von HTTP-Anforderungen schreiben, anstatt eine unserer Open Source-Bibliotheken zu nutzen. Sie sollten die Übersichtsinformationen auf dieser Seite lesen, bevor Sie sich mit den Details der einzelnen Protokolle befassen. Wenn Sie jedoch bereits mit Azure AD B2C vertraut sind, können Sie direkt mit den [Protokoll-Referenzhandbüchern](#protocols) fortfahren.

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Die Grundlagen
Jede App, die Azure AD B2C verwendet, muss im B2C-Verzeichnis im [Azure-Portal](https://portal.azure.com/) registriert werden. Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendungs-ID**, die Ihre App eindeutig identifiziert.
- Einen **Umleitungs-URI** oder **Paketbezeichner**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Einige andere szenariospezifische Werte. Weitere Informationen finden Sie im Artikel zum [Registrieren von Apps](active-directory-b2c-app-registration.md).

Nach dem Registrieren kommuniziert eine App mit Azure AD, indem Anforderungen an den v2.0-Endpunkt gesendet werden:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

In fast allen OAuth- und OpenID Connect-Vorgängen sind vier Beteiligte am Austausch involviert:

![OAuth 2.0-Rollen](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- Der **Autorisierungsserver** ist der Azure AD v2.0-Endpunkt. Er ist verantwortlich für das Sicherstellen der Identität des Benutzers, das Erteilen und Widerrufen des Zugriffs auf Ressourcen und das Ausstellen von Token. Er ist auch als Identitätsanbieter bekannt und verarbeitet auf sichere Weise alles im Zusammenhang mit den Informationen des Benutzers, dessen Zugriff und den Vertrauensstellungen zwischen den Beteiligten in einem Vorgang.
- Beim **Ressourcenbesitzer** handelt es sich normalerweise um den Endbenutzer. Diese Person besitzt die Daten und hat die Möglichkeit, Dritten den Zugriff auf die Daten oder die Ressource zu gewähren.
- Der **OAuth-Client** ist Ihre App, die durch ihre Anwendungs-ID identifiziert wird. Dies ist normalerweise der Beteiligte, mit dem der Endbenutzer interagiert, und der Token vom Autorisierungsserver anfordert. Der Client muss vom Besitzer der Ressource die Berechtigung zum Zugriff darauf erhalten.
- Der **Ressourcenserver** ist der Ort, an dem die Ressource oder die Daten abgelegt sind. Er vertraut dem Autorisierungsserver, dass der OAuth-Client sicher authentifizert und autorisiert wird, und verwendet Bearerzugriffstoken, um sicherzustellen, dass der Zugriff auf eine Ressource gewährt werden kann.

## Richtlinien

Die **Richtlinien** in Azure AD B2C sind wohl das wichtigste Feature des Diensts. Azure AD B2C erweitert die Standardprotokolle OAuth 2.0 und OpenID Connect durch die Einführung von Richtlinien, durch die in Azure AD B2C deutlich mehr möglich ist als einfache Authentifizierung und Autorisierung. Die Richtlinien beschreiben die Aspekte der Benutzeridentität, z. B. Registrierung, Anmelden oder Profilbearbeitung. Sie können in einer Administrator-Benutzeroberfläche definiert und mithilfe von speziellen Abfrageparametern in HTTP-Authentifizierungsanforderungen ausgeführt werden. Richtlinien sind kein Standardfeature von OAuth 2.0 und OpenID Connect, Sie sollten sich also die Zeit nehmen, sich damit vertraut zu machen. Weitere Informationen finden Sie im [Referenzhandbuch zu Azure AD B2C-Richtlinien](active-directory-b2c-reference-policies.md).


## Token
Die Implementierung von OAuth 2.0 und OpenID Connect in Azure AD B2C macht ausgiebig Gebrauch von Bearertoken (auch in Form von JWTs). Ein Trägertoken ist ein einfaches Sicherheitstoken, das dem „Träger“ den Zugriff auf eine geschützte Ressource ermöglicht. In diesem Kontext ist der „Träger“ jede beliebige Partei, die das Token vorweisen kann. Um das Trägertoken zu erhalten, muss sich die Partei zwar zunächst bei Azure AD authentifizieren, falls jedoch keine Maßnahmen ergriffen werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer fremden Partei abgefangen und verwendet werden. Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine unbefugte Verwendung durch nicht autorisierte Parteien verhindert. Trägertoken besitzen dagegen keinen solchen Mechanismus und müssen über einen sicheren Kanal wie etwa Transport Layer Security (HTTPS) übertragen werden. Wird ein Trägertoken als Klartext gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abfangen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Trägertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert. Weitere Sicherheitsüberlegungen zu Trägertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).

Weitere Informationen zu verschiedenen Tokentypen, die in Azure AD B2C verwendet werden, finden Sie in der [Referenz zu Azure AD-Tokens](active-directory-b2c-reference-tokens.md).

## Protokolle

Wenn Sie einige Beispielanforderungen sehen möchten, beginnen Sie mit einem der folgenden Lernprogramme. Jedes Lernprogramm entspricht einem bestimmten Szenario. Wenn Sie Hilfe bei der Bestimmung des richtigen Arbeitsablaufs benötigen, informieren Sie sich über die [App-Typen, die mit Azure AD B2C erstellt werden können](active-directory-b2c-apps.md).

- [Erstellen von mobilen und systemeigenen Anwendungen mit OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Erstellen von Web-Apps mit OpenID Connect](active-directory-b2c-reference-oidc.md)
- Erstellen von Single-Page-Anwendungen mit dem impliziten OAuth 2.0-Fluss (in Kürze verfügbar)
- Erstellen von Daemons oder serverseitigen Prozessen mit dem OAuth 2.0-Fluss für Client-Anmeldeinformationen (in Kürze verfügbar)
- Abrufen von Token mit Benutzername und Kennwort mit dem OAuth 2.0-Fluss für Ressourcenbesitzer-Kennwortberechtigungen(in Kürze verfügbar)
- Abrufen von Token in einer Web-API mit dem OAuth 2.0-Fluss "Im-Auftrag-von" (in Kürze verfügbar)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_0128_2016-->