<properties
	pageTitle="Azure AD B2C-Vorschau | Microsoft Azure"
	description="Erstellen von Webanwendungen mit der Azure AD-Implementierung des OpenID Connect-Authentifizierungsprotokolls."
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
	ms.date="02/18/2016"
	ms.author="dastrock"/>

# Azure AD B2C-Vorschau: Webanmeldungen mit OpenID Connect

OpenID Connect ist ein Authentifizierungsprotokoll auf Grundlage von OAuth 2.0, mit dem Benutzer sicher bei Webanwendungen angemeldet werden können. Mithilfe der Azure AD B2C-Implementierung von OpenID Connect können Sie die Registrierung, Anmeldung und Identitätsverwaltung Ihrer Webanwendungen nach Azure AD auslagern. Diese sprachunabhängige Anleitung beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer Open Source-Bibliotheken.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) erweitert das OAuth 2.0-Protokoll für die *Autorisierung* zur Verwendung als Protokoll für die *Authentifizierung*, sodass Sie das einmalige Anmelden mithilfe von OAuth durchführen können. Dabei wird das Konzept eines `id_token` eingeführt, eines Sicherheitstokens, mit dem der Client die Identität des Benutzers überprüfen und grundlegende Profilinformationen über den Benutzer erhalten kann. Durch die Erweiterung von OAuth 2.0 können Apps **Zugriffstoken** sicher abrufen, die zum Zugriff auf Ressourcen verwendet werden können, die mithilfe eines [Autorisierungsservers](active-directory-b2c-reference-protocols.md#the-basics) geschützt werden. OpenID Connect wird für Webanwendungen empfohlen, die auf einem Server gehostet und auf die über einen Browser zugegriffen wird. Wenn Sie in mobilen oder Desktopanwendungen mit Azure AD B2C eine Identitätsverwaltung einfügen möchten, verwenden Sie [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) anstelle von OpenID Connect.

Azure AD B2C erweitert das OpenID Connect-Standardprotokoll, sodass mehr als nur eine einfache Authentifizierung und Autorisierung erfolgt. Dazu werden [**Richtlinienparameter**](active-directory-b2c-reference-policies.md) eingeführt, mit denen Sie OpenID Connect zum Hinzufügen von Benutzeroberflächen für die Registrierung, Anmeldung und Profilverwaltung zu Ihrer App verwenden können. Hier zeigen wir, wie Sie mit OpenID Connect und Richtlinien diese Benutzeroberflächen in Webanwendungen implementieren und Zugriffstoken für den Zugriff auf Web-APIs verwenden.

Die HTTP-Beispielanforderungen unten verwenden unser B2C-Beispielverzeichnis **fabrikamb2c.onmicrosoft.com** sowie unsere Beispielanwendung **https://aadb2cplayground.azurewebsites.net** und die Richtlinien. Sie können selbst Anfragen mit diesen Werten testen oder eigene Werte verwenden. Erfahren Sie, wie Sie [eigene B2C-Mandanten, -Anwendungen und -Richtlinien erstellen](#use-your-own-b2c-directory).

## Übermitteln von Authentifizierungsanforderungen
Wenn die Web-App Benutzer authentifizieren und eine Richtlinie ausführen muss, kann sie die Benutzer an den `/authorize`-Endpunkt weiterleiten. Dies ist der interaktive Teil des Datenflusses, bei dem der Benutzer je nach Richtlinie tatsächlich Aktionen durchführt. In dieser Anforderung gibt der Client die Berechtigungen, die er vom Benutzer benötigt, im `scope`-Parameter an und die anzuwendende Richtlinie im `p`-Parameter. Unten sind drei Beispiele aufgeführt (mit Zeilenumbrüchen für bessere Lesbarkeit), die jeweils eine andere Richtlinie verwenden. Um ein Gefühl für die Funktionsweise der einzelnen Anforderung zu erhalten, fügen Sie sie in einem Browser ein und führen sie aus.

#### Verwenden einer Anmelderichtlinie

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### Verwenden einer Registrierungsrichtlinie

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### Verwenden einer Profilbearbeitungsrichtlinie

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer App zugewiesen hat. |
| response\_type | erforderlich | Muss das `id_token` für OpenID Connect enthalten. Wenn Ihre Web-App auch Token für den Aufruf einer Web-API benötigt, können Sie wie hier `code+id_token` verwenden. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für die Anmeldung des Benutzers und das Abrufen von Daten über den Benutzer in Form von **ID-Token** an (dies wird noch erläutert). Der `offline_access`-Bereich ist für Web-Apps optional. Er gibt an, dass Ihre App ein **Aktualisierungstoken** für den dauerhaften Zugriff auf Ressourcen benötigt. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein. |
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch zum Codieren von Informationen über den Status des Benutzers in der App vor der Authentifizierungsanforderung verwendet, z. B. für Informationen zu der Seite, die der Benutzer besucht hat. |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| p | erforderlich | Gibt die auszuführende Richtlinie an. Dies ist der Name einer in Ihrem B2C-Mandanten erstellten Richtlinie, deren Wert mit „b2c\_1\_“ beginnen sollte. Weitere Informationen zu Richtlinien finden Sie [hier](active-directory-b2c-reference-policies.md). |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt ist der einzige gültige Wert 'Anmeldung', der den Benutzer zur Eingabe von Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht wirksam. |

An diesem Punkt wird der Benutzer aufgefordert, den Workflow der Richtlinie abzuschließen. Dafür muss der Benutzer z. B. den Benutzernamen und das Kennwort angeben, sich mit der Identität eines sozialen Netzwerks anmelden, sich für das Verzeichnis registrieren oder andere Schritte ausführen – je nachdem, wie die Richtlinie definiert ist. Sobald der Benutzer die Richtlinie abgeschlossen hat, gibt Azure AD mithilfe der Methode im angegebenen `response_mode`-Parameter eine Antwort über den angegebenen `redirect_uri` an Ihre App zurück. Die Antwort ist in den oben aufgeführten Fällen, unabhängig von der ausgeführten Richtlinie, immer gleich.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| id\_token | Das ID-Token, das die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [Azure AD B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md). |
| Code | Der Autorisierungscode, den die App angefordert hat, sofern `response_type=code+id_token` verwendet wurde. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für eine Zielressource verwenden. Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |


## Überprüfen des ID-Tokens
Das Empfangen eines ID-Tokens allein reicht nicht aus, um den Benutzer zu authentifizieren. Sie müssen die Signatur des ID-Tokens validieren und die Ansprüche im Token gemäß der App-Anforderungen überprüfen. Azure AD B2C verwendet [JSON-Webtoken (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen. Viele Open Source-Bibliotheken sind für die Überprüfung von JWTs für unterschiedliche Sprachen verfügbar. Wir empfehlen die Verwendung einer dieser Optionen, anstatt eine eigene Validierungslogik zu implementieren. Diese Informationen sind hilfreich, um die ordnungsgemäße Verwendung dieser Bibliotheken zu erlernen.

Azure AD B2C verfügt über einen OpenID Connect-Metadatenendpunkt, mit dem die App zur Laufzeit Informationen über Azure AD B2C abrufen kann. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Es gibt ein JSON-Metadatendokument für jede Richtlinie in Ihrem B2C-Mandanten. Das Metadatendokument für die `b2c_1_sign_in`-Richtlinie in `fabrikamb2c.onmicrosoft.com` befindet sich z. B. unter:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Eine der Eigenschaften dieses Konfigurationsdokuments ist der `jwks_uri`, dessen Wert für diese Richtlinie wie folgt lautet:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Es gibt zwei Möglichkeiten, zu ermitteln, welche Richtlinie zum Signieren eines ID-Tokens verwendet wurde (und wo die Metadaten abgerufen werden können). Zunächst einmal ist der Richtliniennamen im `acr`-Anspruch im ID-Token enthalten. Informationen zum Analysieren von Ansprüchen nach einem ID-Token finden Sie in der [Azure AD B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md). Eine andere Möglichkeit besteht darin, die Richtlinie beim Übermitteln der Anforderung im Wert des `state`-Parameters zu verschlüsseln und später zu entschlüsseln, um die verwendete Richtlinie zu bestimmen. Beide Methoden sind gleichwertig.

Nachdem Sie das Metadatendokument aus dem OpenID Connect-Metadatenendpunkt erhalten haben, können Sie die öffentlichen RSA256-Schlüssel an diesem Endpunkt zum Überprüfen der Signatur des ID-Tokens verwenden. Es gibt möglicherweise zu einem bestimmten Zeitpunkt mehrere Schlüssel an diesem Endpunkt. Sie werden jeweils durch eine `kid` identifiziert. Der Header des ID-Tokens enthält außerdem einen `kid`-Anspruch, der anzeigt, welcher Schlüssel zum Signieren des ID-Tokens verwendet wurde. Weitere Informationen finden Sie in der [Azure AD B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](active-directory-b2c-reference-tokens.md#validating-tokens) und [Wichtige Informationen zum Signaturschlüsselrollover](active-directory-b2c-reference-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen:

- Sie sollten den `nonce`-Anspruch überprüfen, um Token-Replay-Angriffe zu verhindern. Dessen Wert sollte dem in der Anmeldeanforderung angegebenen Wert entsprechen.
- Sie sollten den `aud`-Anspruch überprüfen, um sicherzustellen, dass das ID-Token für Ihre App ausgestellt wurde. Der Wert sollte der Anwendungs-ID der App entsprechen.
- Sie sollten die Ansprüche `iat` und `exp` überprüfen, um sicherzustellen, dass das ID-Token noch nicht abgelaufen ist.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

- Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
- Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
- Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die Multi-Factor Authentication.

Weitere Informationen zu Ansprüchen in einem ID-Token finden Sie in der [Azure AD B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md).

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen können für die Anzeige, für Aufzeichnungen, für die Autorisierung usw. verwendet werden.

## Abrufen von Token
Wenn Ihre Web-App lediglich Richtlinien ausführen muss, können Sie die nächsten Abschnitte überspringen. Diese Abschnitte gelten nur für Web-Apps, die authentifizierte Aufrufe an eine Web-API durchführen müssen, die auch von Azure AD B2C geschützt werden sollen.

Sie können den erhaltenen Autorisierungscode (mit `response_type=code+id_token`) für ein Token für die gewünschte Ressource einlösen, indem Sie eine `POST`-Anforderung an den `/token`-Endpunkt senden. In der Azure AD B2C-Vorschau ist die einzige Ressource, für die Sie ein Token anfordern können, die Back-End-Web-API Ihrer App. Für das Anfordern eines Tokens für sich selbst wird der `openid`-Bereich verwendet:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------------- |
| p | erforderlich | Die Richtlinie, die zum Abrufen des Autorisierungscodes verwendet wurde. Sie können in dieser Anforderung keine andere Richtlinie verwenden. **Beachten Sie, dass dieser Parameter in der Abfragezeichenfolge hinzugefügt wird**, nicht im POST-Text. |
| client\_id | erforderlich | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer App zugewiesen hat. |
| grant\_type | erforderlich | Muss der `authorization_code` für den Autorisierungscodefluss sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für die Anmeldung des Benutzers und das Abrufen von Daten über den Benutzer in Form von **ID-Token** an. Damit können Sie Token an die Back-End-Web-API der App übermitteln, die durch dieselbe Anwendungs-ID wie der Client dargestellt wird. Der `offline_access`-Bereich gibt an, dass Ihre App ein **Aktualisierungstoken** für den dauerhaften Zugriff auf Ressourcen benötigt. |
| Code | erforderlich | Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| client\_secret | erforderlich | Der geheime Schlüssel der Anwendung, den Sie im [Azure-Portal](https://portal.azure.com/) generiert haben. Dieser geheime Schlüssel der Anwendung ist ein wichtiges Sicherheitsfeature und sollte sicher auf dem Server gespeichert werden. Sie sollten auch darauf achten, diesen geheimen Clientschlüssel in regelmäßigen Abständen zu rotieren. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| not\_before | Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token\_type | Gibt den Wert des Tokentyps an. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| id\_token | Das signierte JWT-Token, das Sie angefordert haben. |
| Bereich | Die Bereiche, für die das Token gilt. Dieser kann für das Zwischenspeichern von Token zur späteren Verwendung verwendet werden. |
| id\_token\_expires\_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |
| profile\_info | Eine Base64-codierte JSON-Zeichenfolge, die eventuell nützliche Informationen zum Benutzer enthält, die in Ihrer systemeigenen Anwendung angezeigt werden können. Der genaue Inhalt hängt von den Anwendungsansprüchen ab, die Sie in Ihrer Richtlinie konfiguriert haben. |
| refresh\_token | Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Token zusätzliche Token zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Details finden Sie in der [B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md). Beachten Sie, dass Sie den `offline_access`-Bereich sowohl für die Autorisierung als auch die Tokenanforderungen verwendet haben müssen, um ein Aktualisierungstoken zu erhalten. |
| refresh\_token\_expires\_in | Die maximal mögliche Gültigkeitsdauer für ein Aktualisierungstoken (in Sekunden). Das Aktualisierungstoken kann jedoch jederzeit ungültig werden. |

> [AZURE.NOTE]
	Wenn Sie dann erfahren möchten, wo das Zugriffstoken ist, berücksichtigen Sie Folgendes. Wenn Sie den `openid`-Bereich anfordern, gibt Azure AD in der Antwort ein JWT-`id_token` aus. Obwohl dieses `id_token` technisch kein OAuth 2.0-Zugriffstoken ist, kann es bei der Kommunikation mit dem Back-End-Dienst der App als solches verwendet werden, da es durch dieselbe Client-ID wie der Client dargestellt wird. Das `id_token` ist immer noch ein signiertes JWT-Bearertoken, das in einem HTTP-Autorisierungsheader an eine Ressource gesendet und zum Autorisieren von Anforderungen verwendet werden kann. Der Unterschied besteht darin, dass ein `id_token` über keinen Mechanismus für die Bereichsdefinition für den Zugriff verfügt, den eine spezielle Clientanwendung eventuell besitzt. Wenn die Clientanwendung jedoch der einzige Client ist, der mit dem Back-End-Dienst kommunizieren kann (wie bei der aktuellen Azure AD B2C-Vorschau), besteht keine Notwendigkeit für einen solchen Mechanismus zur Bereichseingrenzung. Wenn der Azure AD B2C-Vorschau die Möglichkeit für Clients zur Kommunikation mit weiteren Erst- und Drittanbieterressourcen hinzugefügt wird, werden auch Zugriffstoken eingeführt. Allerdings wird auch dann noch die Verwendung von `id_tokens` für die Kommunikation mit dem Back-End-Dienst der App die empfohlene Vorgehensweise sein. Weitere Informationen zu Anwendungstypen, die Sie mit der Azure AD B2C-Vorschau erstellen können, finden Sie in [diesem Artikel](active-directory-b2c-apps.md).

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

## Verwenden des Tokens
Nachdem Sie ein `id_token` erhalten haben, können Sie das Token für Anfragen an die Back-End-Web-APIs verwenden, indem Sie es in den `Authorization`-Header einfügen:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Aktualisieren des Tokens
ID-Token sind kurzlebig. Daher müssen Sie sie nach Ablauf aktualisieren, um weiterhin auf Ressourcen zuzugreifen. Dazu übermitteln Sie eine weitere `POST`-Anforderung an den `/token`-Endpunkt, dieses Mal unter Angabe des `refresh_token` anstelle des `code`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | -------- |
| p | erforderlich | Die Richtlinie, die zum Abrufen des ursprünglichen Aktualisierungstokens verwendet wurde. Sie können in dieser Anforderung keine andere Richtlinie verwenden. **Beachten Sie, dass dieser Parameter in der Abfragezeichenfolge hinzugefügt wird**, nicht im POST-Text. |
| client\_id | erforderlich | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer App zugewiesen hat. |
| grant\_type | erforderlich | Muss der `refresh_token` für diesen Abschnitt des Autorisierungscodeflusses sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für die Anmeldung des Benutzers und das Abrufen von Daten über den Benutzer in Form von **ID-Token** an. Damit können Sie Token an die Back-End-Web-API der App übermitteln, die durch dieselbe Anwendungs-ID wie der Client dargestellt wird. Der `offline_access`-Bereich gibt an, dass Ihre App ein **Aktualisierungstoken** für den dauerhaften Zugriff auf Ressourcen benötigt. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| refresh\_token | erforderlich | Das ursprüngliche Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben. Beachten Sie, dass Sie den `offline_access`-Bereich sowohl für die Autorisierung als auch die Tokenanforderungen verwendet haben müssen, um ein Aktualisierungstoken zu erhalten. |
| client\_secret | erforderlich | Der geheime Schlüssel der Anwendung, den Sie im [Azure-Portal](https://portal.azure.com/) generiert haben. Dieser geheime Schlüssel der Anwendung ist ein wichtiges Sicherheitsfeature und sollte sicher auf dem Server gespeichert werden. Sie sollten auch darauf achten, diesen geheimen Clientschlüssel in regelmäßigen Abständen zu rotieren. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| not\_before | Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token\_type | Gibt den Wert des Tokentyps an. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| id\_token | Das signierte JWT-Token, das Sie angefordert haben. |
| Bereich | Die Bereiche, für die das Token gilt. Dieser kann für das Zwischenspeichern von Token zur späteren Verwendung verwendet werden. |
| id\_token\_expires\_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |
| profile\_info | Eine Base64-codierte JSON-Zeichenfolge, die eventuell nützliche Informationen zum Benutzer enthält, die in Ihrer systemeigenen Anwendung angezeigt werden können. Der genaue Inhalt hängt von den Anwendungsansprüchen ab, die Sie in Ihrer Richtlinie konfiguriert haben. |
| refresh\_token | Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Token zusätzliche Token zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Details finden Sie in der [B2C-Tokenreferenz](active-directory-b2c-reference-tokens.md). |
| refresh\_token\_expires\_in | Die maximal mögliche Gültigkeitsdauer für ein Aktualisierungstoken (in Sekunden). Das Aktualisierungstoken kann jedoch jederzeit ungültig werden. |

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


<!--

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## Senden einer Abmeldungsanforderung

Wenn Sie den Benutzer bei der App abmelden möchten, reicht es nicht, die Cookies der App zu löschen oder die Sitzung mit dem Benutzer auf andere Weise zu beenden. Sie müssen den Benutzer außerdem für die Abmeldung zu Azure AD umleiten. Wenn Sie dies nicht tun, kann sich der Benutzer erneut für Ihre Anwendung authentifizieren, ohne die Anmeldeinformationen erneut einzugeben, da diese über eine gültige SSO-Sitzung bei Azure AD verfügen.

Sie können den Benutzer einfach an den `end_session_endpoint` umleiten, der im gleichen OpenID Connect-Metadatendokument aufgeführt wird, das [oben](#validate-the-id-token) beschrieben wird:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ------------ |
| p | erforderlich | Die Richtlinie, die der Benutzer zuletzt für die Anmeldung bei der Anwendung verwendet hat. |
| post\_logout\_redirect\_uri | empfohlen | Die URL, an die der Benutzer nach erfolgreicher Abmeldung umgeleitet werden soll. Wenn keine Angabe erfolgt, wird dem Benutzer eine generische Meldung von Azure AD B2C angezeigt. |

> [AZURE.NOTE]
	Beim Weiterleiten des Benutzers an den `end_session_endpoint` wird zwar ein Teil des SSO-Zustands des Benutzers bei Azure AD B2C gelöscht, der Benutzer wird jedoch nicht von seiner Sitzung beim Identitätsanbieter eines sozialen Netzwerks abgemeldet. Wenn der Benutzer bei einer nachfolgenden Anmeldung denselben Identitätsanbieter auswählt, wird der Benutzer ohne erneute Eingabe seiner Anmeldeinformationen authentifiziert. Wenn ein Benutzer sich von der B2C-Anwendung abmelden möchte, bedeutet dies nicht unbedingt, dass er sich auch vollständig von seinem Facebook-Konto abmelden möchte. Bei lokalen Konten wird die Sitzung des Benutzers jedoch ordnungsgemäß beendet.

## Verwenden eines eigenen B2C-Mandanten

Wenn Sie diese Anfragen selbst ausprobieren möchten, müssen Sie zunächst diese drei Schritte ausführen und dann die Beispielwerte durch Ihren eigenen ersetzen:

- [Erstellen eines B2C-Mandanten](active-directory-b2c-get-started.md) und Verwenden des Namens Ihres Mandanten in den Anforderungen.
- [Erstellen einer Anwendung](active-directory-b2c-app-registration.md) zum Abrufen einer Anwendungs-ID und eines Umleitungs-URIs. Sie sollten eine **Web-App/Web-API** in die App einfügen und optional auch einen **geheimen Schlüssel für die Anwendung** erstellen.
- [Erstellen der Richtlinien](active-directory-b2c-reference-policies.md) zum Abrufen der Richtliniennamen.

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=AcomDC_0224_2016-->
