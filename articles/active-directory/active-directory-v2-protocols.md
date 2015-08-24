<properties
	pageTitle="App-Modell v2.0 | Microsoft Azure"
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 \(Vorschauversion\): Protokolle – OAuth 2.0 und OpenID Connect

Das App-Modell Version 2.0 bietet Identity-as-a-Service \(IDaaS\) für Ihre Apps durch die Unterstützung gängiger Standardprotokolle, OpenID Connect und OAuth 2.0. Während der Dienst standardkonform ist, kann es feine Unterschiede zwischen den beiden Implementierungen dieser Protokolle geben. Die hier bereitgestellten Informationen sind nützlich, wenn Sie Code direkt durch Senden und Verarbeiten von HTTP-Anforderungen schreiben, anstatt eine unserer Open Source-Bibliotheken zu nutzen. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 \(öffentliche Vorschauversion\). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Token
Die App-Modell v2.0-Implementation von OAuth 2.0 und OpenID Connect macht ausgiebig Gebrauch von Bearertoken \(auch in Form von JWTs\). Ein Trägertoken ist ein einfaches Sicherheitstoken, das dem „Träger“ den Zugriff auf eine geschützte Ressource ermöglicht. In diesem Kontext ist der „Träger“ jede beliebige Partei, die das Token vorweisen kann. Um das Trägertoken zu erhalten, muss sich die Partei zwar zunächst bei Azure AD authentifizieren, falls jedoch keine Maßnahmen ergriffen werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer fremden Partei abgefangen und verwendet werden. Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine unbefugte Verwendung durch nicht autorisierte Parteien verhindert. Trägertoken besitzen dagegen keinen solchen Mechanismus und müssen über einen sicheren Kanal wie etwa Transport Layer Security \(HTTPS\) übertragen werden. Wird ein Trägertoken als Klartext gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abfangen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die \(Zwischen-\)Speicherung von Trägertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert. Weitere Sicherheitsüberlegungen zu Trägertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).

Weitere Informationen zu verschiedenen Tokentypen, die im App-Modell v2.0 verwendet werden, finden Sie in [der App-Modell, Version 2.0 – Tokenreferenz](active-directory-v2-tokens.md).

## Die Grundlagen
Jede App, die das App-Modell v2.0 nutzt, muss unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com) registriert werden. Der Registrierungsprozess für die App sammelt einige Werte und weist ihr einige Werte zu:

- Eine **Anwendungs-ID**, die Ihre Anwendung eindeutig identifiziert.
- Einen **Umleitungs-URI** oder **Paketbezeichner**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
- Einige andere szenariospezifische Werte. Erfahren Sie für nähere Einzelheiten, wie Sie [eine App registrieren](active-directory-v2-app-registration.md).

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

## OAuth2-Autorisierungscodefluss
Der OAuth 2.0-Autorisierungcodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749) beschrieben. Er wird zum Ausführen der Authentifizierung und Autorisierung von der Mehrzahl der App-Typen genutzt, einschließlich [Web-Apps](active-directory-v2-flows.md#web-apps) und [systemintern installierten Apps](active-directory-v2-flows.md#mobile-and-native-apps). Durch den Codefluss können Apps Zugriffstoken sicher abrufen, die zum Zugriff auf Ressourcen verwendet werden können, die mithilfe des App-Modells v2. 0 geschützt werden.

Im Folgenden finden Sie den gesamten Vorgang für eine systemeigene Anwendung. Jede Anforderung wird in den nächsten Abschnitten beschrieben: ![OAuth-Autorisierungscodefluss](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### Anfordern eines Autorisierungscodes
Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize`-Endpunkt leitet. In dieser Anforderung gibt der Client die Berechtigungen an, die er vom Benutzer abrufen muss:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal \([apps.dev.microsoft.com](https://apps.dev.microsoft.com)\) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss `code` für den Autorisierungscodefluss enthalten. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert zeigt dem v2.0-Endpunkt sowohl die Ressource als auch die Berechtigungen für diese angeforderte Ressource an. Bereiche nehmen die Form einer `<app identifier URI>/<scope value>` an. Im obigen Beispiel wird der App-Bezeichner für die Azure AD Graph-API verwendet, `https://graph.windows.net`, und zwei Berechtigungen werden angefordert: `directory.read` und `directory.write`. Eine ausführlichere Erläuterung von Bereichen finden Sie in c. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt ist der einzige gültige Wert 'Anmeldung', der den Benutzer zur Eingabe von Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht wirksam. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Code | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| session\_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

#### Anfordern eines Zugriffstokens
Nun, da Sie einen Autorisierungscode erworben und die Berechtigung vom Benutzer erhalten haben, können Sie den `code` für ein `access_token` auf die gewünschte Ressource einlösen, indem Sie eine `POST`-Anforderung an den `/token`-Endpunkt senden:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal \([apps.dev.microsoft.com](https://apps.dev.microsoft.com)\) Ihrer Anwendung zugewiesen hat. |
| grant\_type | erforderlich | Muss den `authorization_code` für den Autorisierungscodefluss enthalten. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ersten Abschnitt angefordert wurden. Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt der v2.0-Endpunkt ein Token für die im ersten Bereich angegebene Ressource zurück. Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md). |
| Code | erforderlich | Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| client\_secret | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben. Er sollte nicht in einer systemeigenen App verwendet werden, da geheime Client-Schlüssel nicht zuverlässig auf Geräten gespeichert werden können. Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung auf geschützten Ressourcen verwenden, wie z. B. eine Web-API. |
| token\_type | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt. |
| expires\_in | Gibt an, wie lange das Zugriffstoken \(in Sekunden\) gültig ist. |
| expires\_on | Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Zeit wird als die Anzahl der Sekunden aus der Epochenzeit dargestellt. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |
| refresh\_token | Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um zusätzliche Zugriffstoken nach Ablauf der aktuellen Zugriffstoken zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Details finden Sie in der [v2.0-Tokenreferenz](active-directory-v2-tokens.md). |
| id\_token | Eine unsigniertes JSON-Webtoken \(JWT\). Die App kann die Segmente dieses Tokens mit einer base64-URL decodieren, um Informationen über den angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich jedoch nicht für Autorisierungs- und Sicherheitsgrenzen auf sie verlassen. Weitere Informationen zu ID-Token finden Sie in der [App-Modell v2.0-Tokenreferenz](active-directory-v2-tokens.md). |

Fehlerantworten sehen wie folgt aus:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

#### Verwenden des Zugriffstokens
Nun, da Sie erfolgreich ein `access_token` erworben haben, können Sie das Token für Anfragen an Web-APIs verwenden, indem Sie es im `Authorization`-Header einschließen:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### Aktualisieren des Zugriffstokens
Zugriffstoken sind kurzlebig. Daher müssen Sie sie nach Ablauf aktualisieren, um weiterhin auf Ressourcen zuzugreifen. Dazu übermitteln Sie eine weitere `POST`-Anforderung an den `/authorize`-Endpunkt, dieses Mal unter Angabe des `refresh_token` statt des `code`:

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | -------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal \([apps.dev.microsoft.com](https://apps.dev.microsoft.com)\) Ihrer Anwendung zugewiesen hat. |
| grant\_type | erforderlich | Muss den `refresh_token` für diesen Abschnitt des Autorisierungsvorgangcodeflusses enthalten. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ursprünglichen Autorisierungscode-Abschnitt angefordert wurden. Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt der v2.0-Endpunkt ein Token für die im ersten Bereich angegebene Ressource zurück. Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md). |
| refresh\_token | erforderlich | Das Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben. |
| client\_secret | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben. Er sollte nicht in einer systemeigenen App verwendet werden, da geheime Client-Schlüssel nicht zuverlässig auf Geräten gespeichert werden können. Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung auf geschützten Ressourcen verwenden, wie z. B. eine Web-API. |
| token\_type | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt. |
| expires\_in | Gibt an, wie lange das Zugriffstoken \(in Sekunden\) gültig ist. |
| expires\_on | Die Uhrzeit, zu der das Zugriffstoken abläuft. Das Zeit wird als die Anzahl der Sekunden aus der Epochenzeit dargestellt. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |
| refresh\_token | Ein neues Aktualisierungstoken von OAuth 2.0. Ersetzen Sie das alte Aktualisierungstoken durch das neu erworbene, um sicherzustellen, dass Ihre Aktualisierungstoken so lange wie möglich gültig bleiben. |
| id\_token | Eine unsigniertes JSON-Webtoken \(JWT\). Die App kann die Segmente dieses Tokens mit einer base64-URL decodieren, um Informationen über den angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich jedoch nicht für Autorisierungs- und Sicherheitsgrenzen auf sie verlassen. Weitere Informationen zu ID-Token finden Sie in der [App-Modell v2.0-Tokenreferenz](active-directory-v2-tokens.md). |

Fehlerantworten sehen wie folgt aus:

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |






## OpenID Connect-Anmeldevorgang
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) erweitert das OAuth 2.0-Protokoll für die *Autorisierung* zur Verwendung als Protokoll für die *Authentifizierung*, sodass Sie das einmalige Anmelden mithilfe von OAuth durchführen können. Dabei wird das Konzept eines `id_token` eingeführt, ein Sicherheitstoken, mit dem der Client die Identität des Benutzers überprüfen und grundlegende Profilinformationen über den Benutzer erhalten kann.

OpenID Connect ist für das App-Modell v2. 0 die empfohlene Methode zum Implementieren der Anmeldung für eine [Web-App](active-directory-v2-flows.md#web-apps). Der grundlegendste Anmeldevorgang umfasst die folgenden Schritte:

![OpenId Connect-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### Senden der Anmeldeanforderung
Wenn die Web-App den Benutzer authentifizieren muss, kann sie ihn direkt an den `/authorize`-Endpunkt weiterleiten. Diese Anforderung ähnelt dem ersten Abschnitt des [OAuth 2.0-Autorisierungscodeflusses](#oauth2-authorization-code-flow) mit ein paar wichtigen Unterschieden: - Die Anforderung muss den Bereich `openid` im `scope`-Parameter einschließen. - Der `response_type`-Parameter muss `id_token` enthalten. - Die Anforderung muss den `nonce`-Parameter enthalten.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal \([apps.dev.microsoft.com](https://apps.dev.microsoft.com)\) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Der Parameter kann auch andere Antworttypen enthalten, wie im Abschnitt [OpenID Connect mit OAuth-Codefluss](#OpenID-Connect-with-OAuth-Code-Flow) beschrieben. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Für OpenID Connect muss der Bereich `openid` enthalten sein, der sich auf die Berechtigung "Anmelden im Profil und Lesen des Profils" in der Zustimmungsbenutzeroberfläche übersetzt. Sie können auch andere Bereiche in dieser Anforderung für die anfordernde Zustimmung aufnehmen, wie im Abschnitt [OpenID Connect mit OAuth-Codefluss](#OpenID-Connect-with-OAuth-Code-Flow) beschrieben. |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.  
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt ist der einzige gültige Wert 'Anmeldung', der den Benutzer zur Eingabe von Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht wirksam. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| id\_token | Das ID-Token, das die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [Referenz für den v2.0-Endpunkttoken](active-directory-v2-tokens.md). |
| session\_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

#### Überprüfen des ID-Tokens
Das Empfangen eines ID-Tokens allein reicht nicht aus, um den Benutzer zu authentifizieren. Sie müssen die Signatur des ID-Tokens validieren und die Ansprüche im Token gemäß der App-Anforderungen überprüfen. Der v2.0-Endpunkt verwendet [JSON-Webtoken \(JSTs\)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen.

Das App-Modell v2.0 verfügt über einen OpenID Connect-Metadatenendpunkt, mit dem die App Informationen über das App-Modell v2.0 in Laufzeit abrufen kann. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Der Metadatenendpunkt enthält ein JSON-Dokument, das Sie hier finden:

`https://login.microsoftonline.com/common/v2.0/.well-known/configuration`

Eine der Eigenschaften dieses Konfigurationsdokuments ist der `jwks_uri`, dessen Wert für das App-Modell v2.0 wie folgt lautet:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Sie können die öffentlichen RSA256-Schlüssel an diesem Endpunkt zum Überprüfen der Signatur des ID-Tokens verwenden. Es gibt mehrere Schlüssel an diesem Endpunkt zu einem bestimmten Zeitpunkt. Sie werden jeweils durch ein `kid` identifiziert. Der Header des ID-Tokens enthält außerdem einen `kid`-Anspruch, der anzeigt, welcher Schlüssel zum Signieren des ID-Tokens verwendet wurde.

Weitere Informationen finden Sie in der [Tokenreferenz für das App-Modell v2.0](active-directory-v2-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](active-directory-v2-tokens.md#validating-tokens) und [Wichtige Informationen zum Signaturschlüsselrollover](active-directory-v2-tokens.md#validating-tokens).<!--TODO: Improve the information on this-->

Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen:

- Sie sollten den `nonce`-Anspruch überprüfen, um Tokenwiedergabeangriffe zu verhindern. Dessen Wert sollte dem in der Anmeldeanforderung angegebenen Wert entsprechen.
- Sie sollten den `aud`-Anspruch überprüfen, um sicherzustellen, dass das ID-Token für Ihre App ausgestellt wurde. Dessen Wert sollte der `client_id` der App entsprechen.
- Sie sollten die Ansprüche `iat` und `exp` überprüfen, um sicherzustellen, dass das ID-Token noch nicht abgelaufen ist.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

- Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
- Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
- Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die mehrstufige Authentifizierung.

Weitere Informationen Ansprüchen in einem ID-Token finden Sie in der [App-Modell v2.0-Tokenreferenz](active-directory-v2-tokens.md).

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen kann für die Anzeige, für Datensätze, für die Autorisierung usw. verwendet werden.

#### Senden einer Abmeldungsanforderung

Der OpenIdConnect-`end_session_endpoint` wird derzeit von der Vorschauversion des App-Modells v2.0 nicht unterstützt. Dies bedeutet, dass Ihre App keine Anforderung an den v2.0-Endpunkt zum Beenden der Sitzung eines Benutzers und zum Löschen von Cookies, die vom v2.0-Endpunkt festgelegt wurden, senden kann. Um einen Benutzer abzumelden, kann eine App ganz einfach die eigene Sitzung mit dem Benutzer beenden, und die Sitzung des Benutzers mit dem v2.0-Endpunkt unverändert lassen. Wenn der Benutzer das nächste Mal versucht, sich anzumelden, wird eine Seite mit dem Hinweis "Konto auswählen" angezeigt, auf der die aktiv angemeldeten Konten aufgeführt werden. Auf dieser Seite kann der Benutzer sich von jedem Konto abmelden, sodass die Sitzung mit dem v2.0-Endpunkt beendet wird.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## OpenID Connect mit OAuth-Codefluss
Viele Web-Apps müssen den Benutzer anmelden und dann mithilfe von OAuth für diesen Benutzer auf einen Webdienst zugreifen. Dieses Szenario kombiniert die beiden obigen Abschnitte – es verwendet OpenID Connect für die Benutzerauthentifizierung und erhält gleichzeitig einen Autorisierungscode, der mithilfe des OAuth-Autorisierungscodeflusses Zugriffstoken abrufen kann:

![OpenId Connect-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Dieser Codefluss unterscheidet sich nur geringfügig von den oben aufgeführten Abschnitten in der Art und Weise, wie die Anmeldeanforderung gesendet wird.

#### Senden der Anmeldeanforderung
Wenn Ihre Web-App den Benutzer authentifizieren und die erforderlichen Berechtigungen erhalten muss, um auf die Ressourcen zuzugreifen, kann sie den Benutzer auf den `/authorize`-Endpunkt verweisen. In diesem Fall muss die App sowohl ein `id_token` als auch einen `code` in der Antwort anfordern:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal \([apps.dev.microsoft.com](https://apps.dev.microsoft.com)\) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss ein `id_token` und einen `code` für dieses Szenario enthalten. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Für OpenID Connect muss der Bereich `openid` enthalten sein, der sich auf die Berechtigung "Anmelden im Profil und Lesen des Profils" in der Zustimmungsbenutzeroberfläche übersetzt. Sie müssen auch die Bereiche für Ressourcen angeben, auf die Ihre App Zugriff erfordert. Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md). |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.  
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt ist der einzige gültige Wert 'Anmeldung', der den Benutzer zur Eingabe von Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht wirksam. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| id\_token | Das ID-Token, das die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [Referenz für den v2.0-Endpunkttoken](active-directory-v2-tokens.md). |
| Code | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| session\_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

#### Überprüfen des ID-Tokens
Dieser Prozess ist genau der gleiche wie oben in [OpenID Connect-Anmeldevorgang](#OpenID-Connect-Sign-In-Flow) beschrieben.

#### Senden einer Abmeldungsanforderung
Dieser Prozess ist genau der gleiche wie oben in [OpenID Connect-Anmeldevorgang](#OpenID-Connect-Sign-In-Flow) beschrieben.

#### Anfordern eines Zugriffstokens
Dieser Prozess ist genau der gleiche wie oben in [OAuth 2.0-Autorisierungscodefluss](#oauth2-authorization-code-flow) beschrieben.

#### Verwenden des Zugriffstokens
Dieser Prozess ist genau der gleiche wie oben in [OAuth 2.0-Autorisierungscodefluss](#oauth2-authorization-code-flow) beschrieben.

#### Aktualisieren des Zugriffstokens
Dieser Prozess ist genau der gleiche wie oben in [OAuth 2.0-Autorisierungscodefluss](#oauth2-authorization-code-flow) beschrieben.





## Vorgang für OAuth 2.0-Client-Anmeldeberechtigungen
OAuth 2.0-Client-Anmeldeberechtigungen werden in der [OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749#section-4.4) beschrieben. Dieser Vorgang ist nützlich für lang andauernde Prozesse und andere Server-zu-Server-Szenarien, in denen sich die App auf eine Ressource mit einer eigenen "Anwendungsidentität" authentifizieren kann, anstatt die delegierte Identität des Benutzers zu verwenden.

Der Vorgang wird derzeit von der Vorschauversion des App-Modells v2.0 nicht unterstützt. Um zu sehen, wie dies im allgemein verfügbaren Azure AD-Dienst funktioniert, schauen Sie sich [dieses Azure AD-Codebeispiel](https://github/com/AzureADSamples/Daemon-DotNet) an.

## Impliziter OAuth 2.0-Fluss
Der implizite OAuth 2.0-Fluss wird in der [OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749#section-4.2) beschrieben. Er wird häufig für Single Page-/Javascript-Apps verwendet, die in einem Browser ausgeführt werden.

Der Vorgang wird derzeit von der Vorschauversion des App-Modells v2.0 nicht unterstützt. Um zu sehen, wie dies im allgemein verfügbaren Azure AD-Dienst funktioniert, schauen Sie sich [dieses Azure AD-Codebeispiel](active-directory-devquickstarts-angular.md) an.

## Anmeldeberechtigungen für das OAuth 2.0-Ressourcenbesitzer-Kennwort
Die Anmeldeberechtigungen für das OAuth 2.0-Ressourcenbesitzer-Kennwort werden in der [OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749#section-4.3) beschrieben. Dadurch kann eine App Zugriffstoken erhalten, indem der Benutzername und dessen Kennwort in einer Tokenanforderung bereitgestellt werden.

Der Vorgang wird derzeit von der Vorschauversion des App-Modells v2.0 nicht unterstützt. Um zu sehen, wie dies im allgemein verfügbaren Azure AD-Dienst funktioniert, schauen Sie sich [dieses Azure AD-Codebeispiel](https://github.com/AzureADSamples/NativeClient-Headless-DotNet) an.

## OAuth 2.0-“Im-Auftrag-von“-Vorgang
Der „Im-Auftrag-von-Vorgang“ oder die JWT-Bearer-Anmeldeinformationsberechtigung wird in der [OAuth 2.0-Berechtigungserweiterung \(Entwurf\)](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12) beschrieben. Dadurch kann eine Web-API, die ein Zugriffstoken erhält, dieses Token als Anmeldeinformation für den Erhalt von Zugriffstoken auf andere Ressourcen nutzen. Dadurch kann die Web-API sicher eine andere nachgelagerte Web-API aufrufen.

Der Vorgang wird derzeit von der Vorschauversion des App-Modells v2.0 nicht unterstützt. Um zu sehen, wie dies im allgemein verfügbaren Azure AD-Dienst funktioniert, schauen Sie sich [dieses Azure AD-Codebeispiel](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) an.

<!---HONumber=August15_HO7-->