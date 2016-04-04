<properties
	pageTitle="Azure AD v2.0 – impliziter Fluss | Microsoft Azure"
	description="Erstellen von Webanwendungen mit der Azure AD v2.0-Implementierung des impliziten Flusses für Apps mit einer Seite."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# v2.0-Protokolle – SPAs unter Verwendung des impliziten Flusses
Mit dem v2.0-Endpunkt können Sie Benutzer sowohl mit persönlichen Konten als auch mit Geschäfts-, Schul- oder Unikonten von Microsoft bei Apps mit einer Seite anmelden. Bei einseitigen Apps und anderen JavaScript-Apps, die hauptsächlich im Browser ausgeführt werden, gibt es in Bezug auf die Authentifizierung einige interessante Herausforderungen:

- Die Sicherheitsmerkmale dieser Apps unterscheiden sich grundlegend von herkömmlichen serverbasierten Webanwendungen.
- Zahlreiche Autorisierungsserver und Identitätsanbieter unterstützen keine CORS-Anforderungen.
- Umleitungen auf ganzseitige Browserseiten stören die Benutzererfahrung erheblich.

Bei diesen Anwendungen (wie etwa AngularJS, Ember.js, React.js usw.) unterstützt Azure AD den Fluss für die implizite OAuth 2.0-Gewährung. Der implizite Fluss wird in der [OAuth 2.0-Spezifikation](http://tools.ietf.org/html/rfc6749#section-4.2) beschrieben. Der größte Vorteil besteht darin, dass die App Token aus Azure AD abrufen kann, ohne dass die Anmeldeinformationen für den Back-End-Server ausgetauscht werden müssen. Dadurch kann die App den Benutzer anmelden, die Sitzung aufrechterhalten und Token für andere Web-APIs abrufen. All dies geschieht innerhalb des Client-JavaScript-Codes. Bei der Verwendung des impliziten Flusses gibt es einige wichtige Sicherheitsaspekte zu beachten, insbesondere in Bezug auf [Clients](http://tools.ietf.org/html/rfc6749#section-10.3) und [Identitätsvortäuschung](http://tools.ietf.org/html/rfc6749#section-10.3).

Wenn Sie mit dem impliziten Fluss und Azure AD Ihrer JavaScript-App eine Authentifizierungsmöglichkeit hinzufügen möchten, sollten Sie unsere Open Source-JavaScript-Bibliothek [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) verwenden. [Hier](active-directory-appmodel-v2-overview.md#getting-started) finden Sie einige AngularJS-Tutorials für die ersten Schritte.

Wenn Sie jedoch in Ihrer einseitigen App keine Bibliothek verwenden und Protokollmeldungen selbst senden möchten, führen Sie die folgenden allgemeinen Schritte aus.

> [AZURE.NOTE]
	Nicht alle Szenarios und Funktionen von Azure Active Directory werden vom v2.0-Endpunkt unterstützt. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den v2.0-Endpunkt verwenden sollten.
    
## Protokolldiagramm
Der vollständige implizite Anmeldevorgang sieht in etwa wie folgt aus. Die einzelnen Schritte werden unten im Detail beschrieben.

![OpenId Connect-Verantwortlichkeitsbereiche](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## Senden der Anmeldeanforderung

Zur anfänglichen Anmeldung des Benutzers bei Ihrer App können Sie eine [OpenID Connect](active-directory-v2-protocols-oidc.md)-Autorisierungsanforderung senden und ein `id_token` vom v2.0-Endpunkt abrufen:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Fügen Sie die folgende Anforderung in einen Browser ein.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------- |
| tenant | erforderlich | Mit dem `{tenant}`-Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints). |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Kann auch den Antworttyp `token` enthalten. Mithilfe von `token` kann Ihre App ein Zugriffstoken direkt vom Autorisierungsendpunkt abrufen, ohne dass eine zweite Anforderung an den Autorisierungsendpunkt erforderlich ist. Wenn Sie den Antworttyp `token` verwenden, muss der Parameter `scope` einen Bereich enthalten, der angibt, für welche Ressource das Token ausgestellt wird. |
| redirect\_uri | empfohlen | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Für OpenID Connect muss der Bereich `openid` enthalten sein, der in der Berechtigung „Sie werden angemeldet“ in der Zustimmungsbenutzeroberfläche resultiert. Schließen Sie gegebenenfalls auch die [Bereiche](active-directory-v2-scopes.md) `email` oder `profile` mit ein, um Zugriff auf zusätzliche Benutzerdaten zu erhalten. Sie können in diese Anforderung auch andere Bereiche aufnehmen, um die Zustimmung für verschiedene Ressourcen anzufordern. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Muss für den impliziten Fluss `fragment` sein. |
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um [websiteübergreifende Anforderungsfälschungsangriffe zu verhindern](http://tools.ietf.org/html/rfc6749#section-10.12). Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| Eingabeaufforderung | optional | Gibt den Typ der erforderlichen Benutzerinteraktion an. Zu diesem Zeitpunkt sind die einzigen gültigen Werte „login“, „none“ und „consent“. `prompt=login` zwingt den Benutzer, die Anmeldeinformationen in dieser Anforderung einzugeben. Einmaliges Anmelden ist dadurch nicht möglich. `prompt=none` ist genau das Gegenteil: Dieser Wert stellt sicher, dass dem Benutzer keine interaktive Eingabeaufforderung angezeigt wird. Wenn die Anforderung nicht über einmaliges Anmelden im Hintergrund abgeschlossen werden kann, gibt der v2.0-Endpunkt einen Fehler zurück. `prompt=consent` löst nach der Anmeldung des Benutzers das OAuth-Zustimmungsdialogfeld aus, in dem der Benutzer aufgefordert wird, der App Berechtigungen zu gewähren. |
| login\_hint | optional | Dieser Wert kann verwendet werden, um das Feld für den Benutzernamen oder die E-Mail-Adresse auf der Anmeldeseite vorab für den Benutzer auszufüllen, wenn dessen Benutzername im Vorfeld bekannt ist. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, nachdem sie den Benutzernamen aus einer vorherigen Anmeldung mithilfe des Anspruchs `preferred_username` extrahiert haben. |
| domain\_hint | optional | Kann `consumers` oder `organizations` sein. Wenn dieser Parameter vorhanden ist, wird der E-Mail-basierte Ermittlungsvorgang übersprungen, den der Benutzer auf der v2.0-Anmeldeseite durchläuft, was die Benutzerfreundlichkeit verbessert. Apps verwenden diesen Parameter häufig für die wiederholte Authentifizierung, indem sie den Anspruch `tid` aus dem ID-Token extrahieren. Verwenden Sie `domain_hint=consumers`, wenn der Anspruch `tid` den Wert `9188040d-6c67-4c5b-b112-36a304b66dad` hat. Verwenden Sie andernfalls `domain_hint=organizations`. |

Zu diesem Zeitpunkt wird der Benutzer dazu aufgefordert, seine Anmeldeinformationen einzugeben und die Authentifizierung abzuschließen. Der V2.0-Endpunkt stellt auch sicher, dass der Benutzer den Berechtigungen zugestimmt hat, die im `scope`-Abfrageparameter angegeben sind. Wenn der Benutzer keiner Berechtigung zugestimmt hat, wird er dazu aufgefordert, den erforderlichen Berechtigungen zuzustimmen. Nähere Einzelheiten zu [Berechtigungen, Zustimmung und mehrinstanzenfähigen Apps erhalten Sie hier](active-directory-v2-scopes.md).

Sobald der Benutzer authentifiziert ist und seine Zustimmung erteilt hat, gibt der v2.0-Endpunkt mithilfe der Methode im festgelegten `response_mode`-Parameter eine Antwort auf dem angegebenen `redirect_uri` an Ihre App zurück.

#### Erfolgreiche Antwort

Eine erfolgreiche Antwort mithilfe von `response_mode=fragment` und `response_type=id_token+token` sieht wie folgt aus, wobei die Zeilenumbrüche der Lesbarkeit dienen:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Ist enthalten, wenn `token` in `response_type` enthalten ist. Das von der Anwendung angeforderte Zugriffstoken, in diesem Fall für Microsoft Graph. Das Zugriffstoken sollte nicht decodiert oder anderweitig untersucht werden, es kann als nicht transparente Zeichenfolge behandelt werden. |
| token\_type | Ist enthalten, wenn `token` in `response_type` enthalten ist. Ist immer `Bearer`. |
| expires\_in | Ist enthalten, wenn `token` in `response_type` enthalten ist. Gibt für die Zwischenspeicherung den Gültigkeitszeitraum des Tokens in Sekunden an. |
| Bereich | Ist enthalten, wenn `token` in `response_type` enthalten ist. Gibt die Bereiche an, für die das Zugriffstoken gültig ist. |
| id\_token | Das ID-Token, das die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie in der [Referenz für den v2.0-Endpunkttoken](active-directory-v2-tokens.md). |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |


#### Fehlerantwort
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

Sie können `id_token` auch im Clientcode überprüfen. Es ist jedoch eine bewährte Methode, `id_token` an einen Back-End-Server zu senden und die Überprüfung dort auszuführen. Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie noch einige Ansprüche überprüfen. Weitere Informationen finden Sie unter [v2.0-Tokenreferenz](active-directory-v2-tokens.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](active-directory-v2-tokens.md#validating-tokens) und wichtige Informationen zum Signaturschlüsselrollover ([Überprüfen der Signatur](active-directory-v2-tokens.md#validating-tokens)). Wir empfehlen, zum Analysieren und Überprüfen von Token eine Bibliothek zu verwenden. Für die meisten Sprachen und Plattformen ist mindestens eine Bibliothek verfügbar.
<!--TODO: Improve the information on this-->

Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

- Sicherstellen, dass sich der Benutzer/die Organisation für die App angemeldet hat.
- Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und Berechtigungen verfügt.
- Sicherstellen, dass eine bestimmte Stärke der Authentifizierung aufgetreten ist, z.B. die mehrstufige Authentifizierung.

Weitere Informationen zu den Ansprüchen in einem ID-Token finden Sie in der [Tokenreferenz zum v2.0-Endpunkt](active-directory-v2-tokens.md).

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie mit dem Benutzer eine Sitzung beginnen und die Ansprüche im ID-Token zum Abrufen von Informationen über den Benutzer in der App verwenden. Diese Informationen kann für die Anzeige, für Datensätze, für die Autorisierung usw. verwendet werden.

## Abrufen von Zugriffstoken

Nachdem Sie den Benutzer bei der einseitigen App angemeldet haben, können Sie Zugriffstoken zum Aufrufen der von Azure AD gesicherten Web-APIs abrufen, etwa [Microsoft Graph](https://graph.microsoft.io). Auch wenn Sie mithilfe des Antworttyps `token` bereits ein Token erhalten haben, können Sie diese Methode zum Abrufen von Token für zusätzliche Ressourcen verwenden, ohne den Benutzer zur erneuten Anmeldung umzuleiten.

Im herkömmlichen OpenID Connect/OAuth-Fluss senden Sie dazu eine Anforderung an den v2.0-Endpunkt `/token`. Der v2.0-Endpunkt unterstützt jedoch keine CORS-Anforderungen, daher kommen AJAX-Aufrufe zum Abrufen und Aktualisieren von Token nicht infrage. Stattdessen können Sie den impliziten Fluss in einem ausgeblendeten IFrame verwenden, um neue Token für andere Web-APIs zu erhalten:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Fügen Sie die folgende Anforderung in einen Browser ein. (Ändern Sie dabei jedoch zunächst die Werte „domain\_hint“ und „login\_hint“.)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint=organizations&login_hint=myuser@mycompany.com
```

| Parameter | | Beschreibung |
| ----------------------- | ------------------------------- | --------------- |
| tenant | erforderlich | Mit dem `{tenant}`-Wert im Pfad der Anforderung kann festgelegt werden, welche Benutzer sich bei der Anwendung anmelden können. Zulässige Werte sind `common`, `organizations`, `consumers` und Mandantenbezeichner. Weitere Informationen finden Sie in den [Grundlagen zu Protokollen](active-directory-v2-protocols.md#endpoints). |
| client\_id | erforderlich | Die Anwendungs-ID, die das Registrierungsportal ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) Ihrer Anwendung zugewiesen hat. |
| response\_type | erforderlich | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Es können auch andere Antworttypen enthalten sein, z. B. `code`. |
| redirect\_uri | empfohlen | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einer der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| Bereich | erforderlich | Eine durch Leerzeichen getrennte Liste von Bereichen. Beziehen Sie zum Abrufen von Token alle [Bereiche](active-directory-v2-scopes.md) ein, die für die entsprechende Ressource erforderlich sind. |
| response\_mode | empfohlen | Gibt die Methode an, die zum Senden des resultierenden Tokens zurück an Ihre App verwendet werden soll. Kann `query`, `form_post` oder `fragment` sein. |
| state | empfohlen | Ein in der Anforderung enthaltener Wert, der auch in der Antwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z. B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| nonce | erforderlich | Ein Wert in der Anforderung, der von der App erzeugt wird und im resultierenden ID-Token als Anspruch enthalten sein wird. Die App kann diesen Wert dann überprüfen, um die Gefahr von Tokenwiedergabeangriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| Eingabeaufforderung | erforderlich | Verwenden Sie `prompt=none` zum Aktualisieren und Abrufen von Token in einem ausgeblendeten IFrame, um sicherzustellen, dass das IFrame auf der v2.0-Anmeldeseite nicht hängt und direkt zurückgegeben wird. |
| login\_hint | erforderlich | Beziehen Sie zum Aktualisieren und Abrufen von Token in einem ausgeblendete IFrame den Benutzernamen des Benutzers in diesem Hinweis mit ein, damit zwischen verschiedenen Sitzungen, die der Benutzer möglicherweise ausführt, unterschieden werden kann. Sie können den Benutzernamen aus einer vorherigen Anmeldung mithilfe des Anspruchs `preferred_username` extrahieren. |
| domain\_hint | erforderlich | Kann `consumers` oder `organizations` sein. Schließen Sie zum Aktualisieren und Abrufen von Token in einem ausgeblendete IFrame „domain\_hint“ in die Anforderung mit ein. Extrahieren Sie den Anspruch `tid` aus dem ID-Token einer früheren Anmeldung, um festzulegen, welcher Wert verwendet werden soll. Verwenden Sie `domain_hint=consumers`, wenn der Anspruch `tid` den Wert `9188040d-6c67-4c5b-b112-36a304b66dad` hat. Verwenden Sie andernfalls `domain_hint=organizations`. |

Dank des Parameters `prompt=none` ist diese Anforderung entweder erfolgreich oder sie schlägt direkt fehl und kehrt zu Ihrer Anwendung zurück. Eine erfolgreiche Antwort wird an Ihre App an den angegebenen Umleitungs-URI (`redirect_uri`) gesendet. Dabei wird die im Parameter `response_mode` angegebene Methode verwendet.

#### Erfolgreiche Antwort
Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parameter | Beschreibung |
| ----------------------- | ------------------------------- |
| access\_token | Das von der App angeforderte Token |
| token\_type | Ist immer `Bearer`. |
| state | Wenn ein Statusparameter in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die Statuswerte in der Anforderung und in der Antwort identisch sind. |
| expires\_in | Gibt an, wie lange das Zugriffstoken (in Sekunden) gültig ist. |
| Bereich | Die Bereiche, für die das Zugriffstoken gültig ist. |

#### Fehlerantwort
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

Der OpenIdConnect-`end_session_endpoint` wird derzeit vom v2.0-Endpunkt nicht unterstützt. Dies bedeutet, dass Ihre App keine Anforderung an den v2.0-Endpunkt zum Beenden der Sitzung eines Benutzers und zum Löschen von Cookies, die vom v2.0-Endpunkt festgelegt wurden, senden kann. Um einen Benutzer abzumelden, kann eine App ganz einfach die eigene Sitzung mit dem Benutzer beenden, und die Sitzung des Benutzers mit dem v2.0-Endpunkt unverändert lassen. Wenn der Benutzer das nächste Mal versucht, sich anzumelden, wird eine Seite mit dem Hinweis "Konto auswählen" angezeigt, auf der die aktiv angemeldeten Konten aufgeführt werden. Auf dieser Seite kann der Benutzer sich von jedem Konto abmelden, sodass die Sitzung mit dem v2.0-Endpunkt beendet wird.

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

<!---HONumber=AcomDC_0323_2016-->