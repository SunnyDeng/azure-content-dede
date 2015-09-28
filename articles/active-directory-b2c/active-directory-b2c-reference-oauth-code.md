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
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Azure AD B2C-Vorschau: OAuth 2.0-Autorisierungcodefluss

Der OAuth 2.0-Autorisierungcodefluss kann in Apps verwendet werden, die auf einem Gerät installiert sind, um Zugriff auf geschützte Ressourcen wie Web-APIs zu gewähren. Mithilfe der Azure AD B2C-Implementierung von OAuth 2.0 können Sie Ihren Desktop- und mobilen Apps Aufgaben für Registrierung, Anmeldung und Identitätsverwaltung hinzufügen. Diese sprachunabhängige Anleitung beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer Open Source-Bibliotheken.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Der OAuth 2.0-Autorisierungcodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749) beschrieben. Er wird zum Ausführen der Authentifizierung und Autorisierung von der Mehrzahl der App-Typen genutzt, einschließlich [Web-Apps](active-directory-b2c-apps.md#web-apps) und [systemintern installierter Apps](active-directory-b2c-apps.md#mobile-and-native-apps). Durch den Codefluss können Apps **Zugriffstoken** sicher abrufen, die zum Zugriff auf Ressourcen verwendet werden können, die mithilfe eines [Autorisierungsservers](active-directory-b2c-reference-protocols.md#the-basics) geschützt werden. Diese Anleitung konzentriert sich auf einen speziellen Aspekt des OAuth 2.0-Autorisierungcodeflusses – die **öffentlichen Clients**. Ein öffentlicher Client ist jede Clientanwendung, der nicht bei der sicheren Verwaltung der Integrität von geheimen Kennwörtern vertraut werden kann. Dazu gehören mobile Apps, Desktop-Apps und nahezu jede Anwendung, die auf einem Gerät ausgeführt wird und Zugriffstoken abrufen muss. Wenn Sie in einer Web-App mit Azure AD B2C eine Identitätsverwaltung einfügen möchten, verwenden Sie [OpenID Connect](active-directory-b2c-reference-oidc.md) anstelle von OAuth 2.0.

Azure AD B2C erweitert den OAuth 2.0-Standardfluss, sodass mehr als nur eine einfache Authentifizierung und Autorisierung erfolgt. Dazu werden [**Richtlinienparameter**](active-directory-b2c-reference-poliices.md) eingeführt, mit denen Sie OAuth 2.0 zum Hinzufügen von Benutzeroberflächen für die Registrierung, Anmeldung und Profilverwaltung zu Ihrer App verwenden können. Hier zeigen wir, wie Sie mit OAuth 2.0 und Richtlinien diese Benutzeroberflächen in systemeigenen Anwendungen implementieren und Zugriffstoken für den Zugriff auf Web-APIs verwenden.

Die HTTP-Beispielanforderungen unten verwenden unser B2C-Beispielverzeichnis **fabrikamb2c.onmicrosoft.com** sowie unsere Beispielanwendung und die Richtlinien. Sie können selbst Anfragen mit diesen Werten testen oder eigene Werte verwenden. Erfahren Sie, wie Sie [eigene B2C-Verzeichnisse, -Anwendungen und -Richtlinien erstellen](#use-your-own-b2c-directory).

## 1\. Abrufen von Autorisierungscodes
Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize`-Endpunkt leitet. Dies ist der interaktive Teil des Datenflusses, bei dem der Benutzer tatsächlich Aktionen durchführt. In dieser Anforderung gibt der Client die Berechtigungen, die er vom Benutzer benötigt, im `scope`-Parameter an und die anzuwendende Richtlinie im `p`-Parameter. Unten sind drei Beispiele aufgeführt (mit Zeilenumbrüchen für bessere Lesbarkeit), die jeweils eine andere Richtlinie verwenden.

#### Verwenden einer Anmelderichtlinie

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### Verwenden einer Registrierungsrichtlinie

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### Verwenden einer Profilbearbeitungsrichtlinie

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com) Ihrer App zugewiesen hat. |
| response\_type | erforderlich | Muss `code` für den Autorisierungscodefluss enthalten. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für die Anmeldung des Benutzers und das Abrufen von Daten über den Benutzer in Form von **ID-Token** an (dies wird noch erläutert). Der `offline_access`-Bereich gibt an, dass Ihre App ein **Aktualisierungstoken** für den dauerhaften Zugriff auf Ressourcen benötigt. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch zum Codieren von Informationen über den Status des Benutzers in der App vor der Authentifizierungsanforderung verwendet, z. B. für Informationen zu der Seite, die der Benutzer besucht hat, oder zur ausgeführten Richtlinie. |
| p | erforderlich | Gibt die auszuführende Richtlinie an. Dies ist der Name einer in Ihrem B2C-Verzeichnis erstellten Richtlinie, deren Wert mit "b2c\_1\_" beginnen sollte. Weitere Informationen zu Richtlinien finden Sie [hier](active-directory-b2c-reference-policies.md). |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt ist der einzige gültige Wert 'Anmeldung', der den Benutzer zur Eingabe von Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht wirksam. |

An diesem Punkt wird der Benutzer aufgefordert, den Workflow der Richtlinie abzuschließen. Dafür muss der Benutzer z. B. den Benutzernamen und das Kennwort angeben, sich mit der Identität eines sozialen Netzwerks anmelden, sich für das Verzeichnis registrieren oder andere Schritte ausführen – je nachdem, wie die Richtlinie definiert ist. Sobald der Benutzer die Richtlinie abgeschlossen hat, gibt Azure AD mithilfe der Methode im angegebenen `response_mode`-Parameter eine Antwort über den angegebenen `redirect_uri` an Ihre App zurück. Die Antwort ist in den oben aufgeführten Fällen, unabhängig von der ausgeführten Richtlinie, immer gleich.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Code | Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für eine Zielressource verwenden. Autorisierungscodes sind sehr kurzlebig und laufen in der Regel nach etwa zehn Minuten ab. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |


## 2\. Abrufen von Token
Nachdem Sie einen Autorisierungscode erhalten haben, können Sie den `code` für ein Token für die gewünschte Ressource einlösen, indem Sie eine `POST`-Anforderung an den `/token`-Endpunkt senden: In der Azure AD B2C-Vorschau ist die einzige Ressource, für die Sie ein Token anfordern können, die Back-End-Web-API Ihrer App. Für das Anfordern eines Tokens für sich selbst wird der `openid`-Bereich verwendet:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------------- |
| p | erforderlich | Die Richtlinie, die zum Abrufen des Autorisierungscodes verwendet wurde. Sie können in dieser Anforderung keine andere Richtlinie verwenden. **Beachten Sie, dass dieser Parameter in der Abfragezeichenfolge hinzugefügt wird**, nicht im POST-Text. |
| client\_id | erforderlich | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com) Ihrer App zugewiesen hat. |
| grant\_type | erforderlich | Muss der `authorization_code` für den Autorisierungscodefluss sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für die Anmeldung des Benutzers und das Abrufen von Daten über den Benutzer in Form von **ID-Token** an. Damit können Sie Token an die Back-End-Web-API der App übermitteln, die durch dieselbe Anwendungs-ID wie der Client dargestellt wird. Der `offline_access`-Bereich gibt an, dass Ihre App ein **Aktualisierungstoken** für den dauerhaften Zugriff auf Ressourcen benötigt. |
| Code | erforderlich | Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |

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

> [AZURE.NOTE]Wenn Sie dann erfahren möchten, wo das Zugriffstoken ist, berücksichtigen Sie Folgendes. Wenn Sie den `openid`-Bereich anfordern, gibt Azure AD in der Antwort ein JWT-`id_token` aus. Obwohl dieses `id_token` technisch kein OAuth 2.0-Zugriffstoken ist, kann es bei der Kommunikation mit dem Back-End-Dienst der App als solches verwendet werden, da es durch dieselbe Client-ID wie der Client dargestellt wird. Das `id_token` ist immer noch ein signiertes JWT-Bearertoken, das in einem HTTP-Autorisierungsheader an eine Ressource gesendet und zum Autorisieren von Anforderungen verwendet werden kann. Der Unterschied besteht darin, dass ein `id_token` über keinen Mechanismus für die Bereichsdefinition für den Zugriff verfügt, den eine spezielle Clientanwendung eventuell besitzt. Wenn die Clientanwendung jedoch der einzige Client ist, der mit dem Back-End-Dienst kommunizieren kann (wie bei der aktuellen Azure AD B2C-Vorschau), besteht keine Notwendigkeit für einen solchen Mechanismus zur Bereichseingrenzung. Wenn der Azure AD B2C-Vorschau die Möglichkeit für Clients zur Kommunikation mit weiteren Erst- und Drittanbieterressourcen hinzugefügt wird, werden auch Zugriffstoken eingeführt. Allerdings wird auch dann noch die Verwendung von `id_tokens` für die Kommunikation mit dem Back-End-Dienst der App die empfohlene Vorgehensweise sein. Weitere Informationen zu Anwendungstypen, die Sie mit der Azure AD B2C-Vorschau erstellen können, finden Sie in [diesem Artikel](active-directory-b2c-apps.md).

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

## 3\. Verwenden des Tokens
Nachdem Sie ein `id_token` erhalten haben, können Sie das Token für Anfragen an die Back-End-Web-APIs verwenden, indem Sie es in den `Authorization`-Header einfügen:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. Aktualisieren des Tokens
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
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | -------- |
| p | erforderlich | Die Richtlinie, die zum Abrufen des ursprünglichen Aktualisierungstokens verwendet wurde. Sie können in dieser Anforderung keine andere Richtlinie verwenden. **Beachten Sie, dass dieser Parameter in der Abfragezeichenfolge hinzugefügt wird**, nicht im POST-Text. |
| client\_id | erforderlich | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com) Ihrer App zugewiesen hat. |
| grant\_type | erforderlich | Muss der `refresh_token` für diesen Abschnitt des Autorisierungscodeflusses sein. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für die Anmeldung des Benutzers und das Abrufen von Daten über den Benutzer in Form von **ID-Token** an. Damit können Sie Token an die Back-End-Web-API der App übermitteln, die durch dieselbe Anwendungs-ID wie der Client dargestellt wird. Der `offline_access`-Bereich gibt an, dass Ihre App ein **Aktualisierungstoken** für den dauerhaften Zugriff auf Ressourcen benötigt. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| refresh\_token | erforderlich | Das ursprüngliche Aktualisierungstoken, das Sie im zweiten Abschnitt des Vorgangs erhalten haben. |

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

## Verwenden eines eigenen B2C-Verzeichnisses

Wenn Sie diese Anfragen selbst ausprobieren möchten, müssen Sie zunächst diese drei Schritte ausführen und dann die Beispielwerte durch Ihren eigenen ersetzen:

- [Erstellen eines B2C-Verzeichnisses](active-directory-b2c-get-started.md) – verwenden Sie dann den Namen Ihres Verzeichnisses in den Anforderungen.
- [Erstellen einer Anwendung](active-directory-b2c-app-registration.md) zum Abrufen einer Anwendungs-ID und eines Umleitungs-URIs. Sie können Ihrer App einen **systemeigenen Client** hinzufügen.
- [Erstellen der Richtlinien](active-directory-b2c-reference-policies.md) zum Abrufen der Richtliniennamen.

<!---HONumber=Sept15_HO3-->