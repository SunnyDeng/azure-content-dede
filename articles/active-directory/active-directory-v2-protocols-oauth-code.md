
<properties
	pageTitle="App-Modell v2.0: OAuth-Protokoll | Microsoft Azure"
	description="Erstellen von Webanwendungen mit der Azure AD-Implementierung des OAuth 2.0-Authentifizierungsprotokolls."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 Vorschauversion: Protokolle – OAuth 2.0-Autorisierungscodefluss

Der OAuth 2.0-Autorisierungcodefluss kann in Apps verwendet werden, die auf einem Gerät installiert sind, um Zugriff auf geschützte Ressourcen wie Web-APIs zu gewähren. Die Implementierung von OAuth 2.0 im App-Modell v2.0 ermöglicht es Ihnen, sich bei mobilen Apps und Desktop-Apps anzumelden und über APIs darauf zuzugreifen. Diese sprachunabhängige Anleitung beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer Open Source-Bibliotheken.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

Der OAuth 2.0-Autorisierungcodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749) beschrieben. Er wird zum Ausführen der Authentifizierung und Autorisierung von der Mehrzahl der App-Typen genutzt, einschließlich [Web-Apps](active-directory-v2-flows.md#web-apps) und [systemintern installierten Apps](active-directory-v2-flows.md#mobile-and-native-apps). Durch den Codefluss können Apps Zugriffstoken sicher abrufen, die zum Zugriff auf Ressourcen verwendet werden können, die mithilfe des App-Modells v2.0 geschützt werden.



## Anfordern eines Autorisierungscodes
Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize`-Endpunkt leitet. In dieser Anforderung gibt der Client die Berechtigungen an, die er vom Benutzer abrufen muss:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		  // Your registered Application Id
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob 	  // Your registered Redirect Uri, url encoded
&response_mode=query							        // 'query', 'form_post', or 'fragment'
&scope=											     // See table below
openid%20
offline_access%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				   // Any value provided by your app
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss `code` für den Autorisierungscodefluss enthalten. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert zeigt dem v2.0-Endpunkt sowohl die Ressource als auch die Berechtigungen für diese angeforderte Ressource an. Bereiche nehmen die Form einer `<app identifier URI>/<scope value>` an. Im obigen Beispiel wird der App-Bezeichner für die Azure AD Graph-API verwendet, `https://graph.windows.net`, und zwei Berechtigungen werden angefordert: `directory.read` und `directory.write`. Eine ausführlichere Erläuterung von Bereichen finden Sie in der [Bereichsreferenz zum App-Modell v2.0](active-directory-v2-scopes.md). |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt sind die einzigen gültigen Werte „login“, „none“ und „consent“. `prompt=login` zwingt den Benutzer, die Anmeldeinformationen in dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. `prompt=none` ist genau das Gegenteil: Dieser Wert stellt sicher, dass dem Benutzer keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt der v2.0-Endpunkt einen Fehler zurück. `prompt=consent` löst nach der Anmeldung des Benutzers das OAuth-Zustimmungsdialogfeld aus, in dem der Benutzer aufgefordert wird, der App Berechtigungen zu gewähren. |
| login\_hint | optional | Dieser Wert kann dazu verwendet werden, das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												      // the value provided in the request
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Code | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für die Zielressource verwenden. Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| session\_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## Anfordern eines Zugriffstokens
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
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| grant\_type | erforderlich | Muss der `authorization_code` für den Autorisierungscodefluss sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ersten Abschnitt angefordert wurden. Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt der v2.0-Endpunkt ein Token für die im ersten Bereich angegebene Ressource zurück. Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md). |
| Code | erforderlich | Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| client\_secret | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben. Er sollte nicht in einer systemeigenen App verwendet werden, da geheime Client-Schlüssel nicht zuverlässig auf Geräten gespeichert werden können. Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung auf geschützten Ressourcen verwenden, wie z. B. eine Web-API. |
| token\_type | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt. |
| expires\_in | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |
| refresh\_token | Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um zusätzliche Zugriffstoken nach Ablauf der aktuellen Zugriffstoken zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Details finden Sie in der [v2.0-Tokenreferenz](active-directory-v2-tokens.md). |
| id\_token | Ein unsigniertes JSON-Webtoken (JWT). Die App kann die Segmente dieses Tokens mit einer base64-URL decodieren, um Informationen über den angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich jedoch nicht für Autorisierungs- und Sicherheitsgrenzen auf sie verlassen. Weitere Informationen zu ID-Token finden Sie in der [App-Modell v2.0-Tokenreferenz](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |


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

## Verwenden des Zugriffstokens
Nachdem Sie erfolgreich ein `access_token` abgerufen haben, können Sie das Token für Anforderungen an Web-APIs verwenden, indem Sie es in den `Authorization`-Header einschließen:

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Aktualisieren des Zugriffstokens
Zugriffstoken sind kurzlebig. Daher müssen Sie sie nach Ablauf aktualisieren, um weiterhin auf Ressourcen zuzugreifen. Dazu übermitteln Sie eine weitere `POST`-Anforderung an den `/token`-Endpunkt, dieses Mal unter Angabe des `refresh_token` statt des `code`:

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
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| grant\_type | erforderlich | Muss der `refresh_token` für diesen Abschnitt des Autorisierungscodeflusses sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Die in diesem Abschnitt angeforderten Bereiche müssen den Bereichen entsprechen oder eine Teilmenge der Bereiche sein, die im ursprünglichen Autorisierungscode-Abschnitt angefordert wurden. Wenn die in dieser Anforderung angegebenen Bereiche mehrere Ressourcenserver umfassen, gibt der v2.0-Endpunkt ein Token für die im ersten Bereich angegebene Ressource zurück. Eine ausführlichere Erläuterung von Bereichen finden Sie in [Berechtigungen, Zustimmung und Bereiche](active-directory-v2-scopes.md). |
| refresh\_token | erforderlich | Das Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben. |
| client\_secret | erforderlich für Web-Apps | Der geheime App-Schlüssel, den Sie im App-Registrierungsportal für Ihre App erstellt haben. Er sollte nicht in einer systemeigenen App verwendet werden, da geheime Client-Schlüssel nicht zuverlässig auf Geräten gespeichert werden können. Er ist erforderlich für Web-Apps und Web-APIs, die die Möglichkeit haben, den geheimen Client-Schlüssel sicher auf dem Server zu speichern. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung auf geschützten Ressourcen verwenden, wie z. B. eine Web-API. |
| token\_type | Gibt den Wert des Tokentyps an. Der Bearertyp ist der einzige Typ, den Azure AD unterstützt. |
| expires\_in | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |
| refresh\_token | Ein neues Aktualisierungstoken von OAuth 2.0. Ersetzen Sie das alte Aktualisierungstoken durch das neu erworbene, um sicherzustellen, dass Ihre Aktualisierungstoken so lange wie möglich gültig bleiben. |
| id\_token | Ein unsigniertes JSON-Webtoken (JWT). Die App kann die Segmente dieses Tokens mit einer base64-URL decodieren, um Informationen über den angemeldeten Benutzer abzurufen. Die App kann die Werte zwischenspeichern und sie anzeigen, sollte sich jedoch nicht für Autorisierungs- und Sicherheitsgrenzen auf sie verlassen. Weitere Informationen zu ID-Token finden Sie in der [App-Modell v2.0-Tokenreferenz](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |

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

## Zusammenfassung
Allgemein sieht der gesamte Authentifizierungsablauf für eine systemeigene oder mobile Anwendung etwa wie folgt aus:

![OAuth-Autorisierungscodefluss](../media/active-directory-v2-flows/convergence_scenarios_native.png)

<!---HONumber=AcomDC_1203_2015-->