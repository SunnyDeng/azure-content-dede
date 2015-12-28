<properties
	pageTitle="App-Modell v2.0 – impliziter Fluss | Microsoft Azure"
	description="Erstellen von Webanwendungen mit der Azure AD-Implementierung des impliziten Flusses für Apps mit einer Seite."
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
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): Protokolle – impliziter Fluss
Mit dem App-Modell v2.0 von Azure AD können Sie Benutzer sowohl mit persönlichen Konten als auch mit Geschäfts- oder Schulkonten von Microsoft bei Apps mit einer Seite anmelden. Bei einseitigen Apps und anderen JavaScript-Apps, die hauptsächlich im Browser ausgeführt werden, gibt es in Bezug auf die Authentifizierung einige interessante Herausforderungen:

- Die Sicherheitsmerkmale dieser Apps unterscheiden sich grundlegend von herkömmlichen serverbasierten Webanwendungen.
- Zahlreiche Autorisierungsserver und Identitätsanbieter unterstützen aus gut dokumentierten Sicherheitsgründen keine CORS-Anforderungen.
- Umleitungen auf ganzseitige Browserseiten stören die Benutzererfahrung erheblich.

Bei diesen Anwendungen (etwa AngularJS, Ember.js, React.js usw.) unterstützt Azure AD den Fluss für die implizite OAuth 2.0-Gewährung. Der implizite Fluss wird in der [OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749#section-4.2) beschrieben. Der größte Vorteil besteht darin, dass die App Token aus Azure AD abrufen kann, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Dadurch kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes. Bei der Verwendung des impliziten Flusses gibt es einige wichtige Sicherheitsaspekte zu beachten, insbesondere in Bezug auf [Clients](http://tools.ietf.org/html/rfc6749#section-10.3) und [Identitätsvortäuschung](http://tools.ietf.org/html/rfc6749#section-10.3).

Wenn Sie mit dem impliziten Fluss und Azure AD Ihrer JavaScript-App eine Authentifizierungsmöglichkeit hinzufügen möchten, sollten Sie unsere Open Source-JavaScript-Bibliothek [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) verwenden. [Hier](active-directory-appmodel-v2-overview.md#getting-started) finden Sie einige AngularJS-Tutorials für die ersten Schritte.

Wenn Sie jedoch in Ihrer einseitigen App keine Bibliothek verwenden und Protokollmeldungen selbst senden möchten, führen Sie die folgenden allgemeinen Schritte aus.

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Senden der Anmeldeanforderung

Zur anfänglichen Anmeldung des Benutzers bei Ihrer App können Sie eine [OpenID Connect](active-directory-v2-protocols-oidc.md)-Autorisierungsanforderung senden und ein `id_token` vom v2.0-Endpunkt abrufen:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=openid%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
&state=12345
&nonce=678910
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Es können auch andere Antworttypen enthalten sein, z. B. `code`. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Für OpenID Connect muss der Bereich `openid` enthalten sein, der sich auf die Berechtigung "Anmelden im Profil und Lesen des Profils" in der Zustimmungsbenutzeroberfläche übersetzt. Sie können in diese Anforderung auch andere Bereiche für das Anfordern der Zustimmung aufnehmen. |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.  
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt sind die einzigen gültigen Werte „login“, „none“ und „consent“. `prompt=login` zwingt den Benutzer, die Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. `prompt=none` ist genau das Gegenteil: Dieser Wert stellt sicher, dass dem Benutzer keine Aufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt der v2.0-Endpunkt einen Fehler zurück. `prompt=consent` löst nach der Anmeldung des Benutzers das OAuth-Zustimmungsdialogfeld aus und fordert den Benutzer dabei auf, der App Berechtigungen zu gewähren. |
| login\_hint | optional | Dieser Wert kann dazu verwendet werden, das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```
GET https://localhost/myapp/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3600

```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| id\_token | Das ID-Token, das die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [Referenz für den v2.0-Endpunkttoken](active-directory-v2-tokens.md). |
| session\_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |
| id\_token\_expires\_in | Die Gültigkeitsdauer des ID-Tokens (in Sekunden). |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## Überprüfen des ID-Tokens
Das Empfangen eines ID-Tokens allein reicht nicht aus, um den Benutzer zu authentifizieren. Sie müssen die Signatur des ID-Tokens validieren und die Ansprüche im Token gemäß der App-Anforderungen überprüfen. Der v2.0-Endpunkt verwendet [JSON-Webtoken (JSTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen.

Das App-Modell v2.0 verfügt über einen OpenID Connect-Metadatenendpunkt, mit dem die App Informationen über das App-Modell v2.0 in Laufzeit abrufen kann. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Der Metadatenendpunkt enthält ein JSON-Dokument, das Sie hier finden:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Eine der Eigenschaften dieses Konfigurationsdokuments ist der `jwks_uri`, dessen Wert für das App-Modell v2.0 wie folgt lautet:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Sie können die öffentlichen RSA256-Schlüssel an diesem Endpunkt zum Überprüfen der Signatur des ID-Tokens verwenden. Es gibt mehrere Schlüssel an diesem Endpunkt zu einem bestimmten Zeitpunkt. Sie werden jeweils durch ein `kid` identifiziert. Der Header des ID-Tokens enthält außerdem einen `kid`-Anspruch, der anzeigt, welcher Schlüssel zum Signieren des ID-Tokens verwendet wurde.

Weitere Informationen finden Sie in der [Tokenreferenz für das App-Modell v2.0](active-directory-v2-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](active-directory-v2-tokens.md#validating-tokens) und [Wichtige Informationen zum Signaturschlüsselrollover](active-directory-v2-tokens.md#validating-tokens).<!--TODO: Improve the information on this-->

Sie können das `id_token` auch im Clientcode überprüfen. Es ist jedoch eine bewährte Methode, das `id_token` an einen Back-End-Server zu senden und die Überprüfung dort auszuführen. Zum Analysieren und Überprüfen von JWT-Token stehen zahlreiche Bibliotheken zur Verfügung. Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen:

- Sie sollten den `nonce`-Anspruch überprüfen, um Token-Replay-Angriffe zu verhindern. Dessen Wert sollte dem in der Anmeldeanforderung angegebenen Wert entsprechen.
- Sie sollten den `aud`-Anspruch überprüfen, um sicherzustellen, dass das ID-Token für Ihre App ausgestellt wurde. Dessen Wert sollte der `client_id` der App entsprechen.
- Sie sollten die Ansprüche `iat` und `exp` überprüfen, um sicherzustellen, dass das ID-Token noch nicht abgelaufen ist.

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

- Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
- Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
- Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die mehrstufige Authentifizierung.

Weitere Informationen Ansprüchen in einem ID-Token finden Sie in der [App-Modell v2.0-Tokenreferenz](active-directory-v2-tokens.md).

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen kann für die Anzeige, für Datensätze, für die Autorisierung usw. verwendet werden.

## Abrufen von Zugriffstoken

Nachdem Sie den Benutzer bei der einseitigen App angemeldet haben, können Sie Zugriffstoken zum Aufrufen der von Azure AD gesicherten Web-APIs abrufen, etwa [Microsoft Graph](https://graph.microsoft.io). Im herkömmlichen OpenID Connect/OAuth-Fluss senden Sie dazu eine Anforderung an den v2.0-Endpunkt `/token`. Der v2.0-Endpunkt unterstützt jedoch keine CORS-Anforderungen, daher kommen AJAX-Aufrufe zum Abrufen und Aktualisieren von Token nicht infrage. Stattdessen können Sie den impliziten Fluss in einem ausgeblendeten IFrame verwenden, um neue Token für andere Web-APIs zu erhalten:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read  // The space separated scope(s) you want an access token for
&state=12345
&prompt=none
&login_hint=joe.user@outlook.com
&domain_hint=consumers
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss für den impliziten Fluss `token` sein. |
| redirect\_uri | erforderlich | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen, die im Zugriffstoken erforderlich sind |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet werden soll. Kann entweder "Abfrage", "form\_post" oder "Fragment" sein.  
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| Eingabeaufforderung | erforderlich | Wir geben hier mithilfe von `prompt=none` an, dass Azure AD keine UI enthalten soll. Da diese Anforderung in einem ausgeblendeten IFrame erfolgt, müssen Sie sicherstellen, dass Azure AD den Benutzer nicht zum Anmelden auffordert, sondern einen Fehler zurückgibt, falls das Token nicht im Hintergrund abgerufen werden kann. |
| login\_hint | erforderlich | Für die Authentifizierung im Hintergrund erforderlich. Mit diesem Hinweis kann Azure AD zwischen mehreren Konten unterscheiden, wenn der Benutzer eine aktive Sitzung mit mehreren Konten geöffnet hat. Der hier angegebene Wert kann exakt der Wert sein, den Sie im `preferred_uesrname`-Anspruch des ID-Tokens erhalten. |
| domain\_hint | erforderlich | Für die Authentifizierung im Hintergrund erforderlich. Für diesen Fluss kann für „domain\_hint“ einer der folgenden zwei Werte angegeben sein: `consumers` oder `organizations`. Falls der Wert des `tid`-Anspruchs im erhaltenen `id_token` genau „9188040d-6c67-4c5b-b112-36a304b66dad“ entsprach, verwenden Sie `consumers` für „domain\_hint“. Verwenden Sie andernfalls `organizations`. |

Dank des Parameters `prompt=none` ist diese Anforderung entweder erfolgreich oder sie schlägt direkt fehl und kehrt zu Ihrer Anwendung zurück. Eine erfolgreiche Antwort wird an Ihre App an den angegebenen Umleitungs-URI (`redirect_uri`) gesendet. Dabei wird die im Parameter `response_mode` angegebene Methode verwendet.

Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the access_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&expires_in=3600
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read

```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Das von der App angeforderte Token |
| session\_state | Ein eindeutiger Wert, der die aktuelle Benutzersitzung identifiziert. Dieser Wert ist eine GUID, sollte jedoch als opaker Wert behandelt werden, der ohne Prüfung übergeben wird. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |
| expires\_in | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |

Fehlerantworten können auch an den `redirect_uri` gesendet werden, damit die App diese angemessen behandeln kann: Im Fall von `prompt=none` wird folgender Fehler erwartet:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| Fehler | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| error\_description | Eine spezifische Fehlermeldung, mit der Entwickler die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

Wenn Sie diesen Fehler in der IFrame-Anforderung erhalten, muss sich der Benutzer erneut anmelden, um ein neues Token abzurufen. Diesen Fall können Sie so behandeln, wie es für Ihre Anwendung am sinnvollsten ist.

## Aktualisieren von Token

`id_token`s und `access_token`s laufen nach kurzer Zeit ab. Ihre App muss daher diese Token in regelmäßigen Abständen aktualisieren. Zum Aktualisieren beider Tokentypen können Sie die oben erwähnte verborgene IFrame-Anforderung unter Verwendung des Parameters `prompt=none` ausführen, um das Verhalten von Azure AD zu steuern. Wenn Sie ein neues `id_token` erhalten möchten, verwenden Sie unbedingt `response_type=id_token` und `scope=openid`, sowie den Parameter `nonce`.


## Senden einer Abmeldungsanforderung

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

<!---HONumber=AcomDC_1217_2015-->