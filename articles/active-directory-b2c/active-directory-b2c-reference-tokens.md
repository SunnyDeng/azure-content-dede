<properties
	pageTitle="Azure AD B2C Preview | Microsoft Azure"
	description="Informationen zu den Typen der in der Preview-Version von Azure AD B2C ausgestellten Token."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C Preview: Tokenreferenz

Azure AD B2C stellt bei der Verarbeitung der einzelnen [Authentifizierungsflüsse](active-directory-b2c-apps.md) verschiedene Arten von Sicherheitstoken aus. In diesem Dokument sind das Format, die Sicherheitsmerkmale und der Inhalt aller Tokentypen beschrieben.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Tokentypen

Azure AD B2C unterstützt das [OAuth 2.0-Autorisierungsprotokoll](active-directory-b2c-reference-protocols.md), das Zugriffstoken und Aktualisierungstoken verwendet. Außerdem wird die Authentifizierung und Anmeldung über [OpenID Connect](active-directory-b2c-reference-protocols.md) unterstützt. Dabei wird ein dritter Tokentyp eingeführt: das ID-Token. Alle diese Token werden als Bearertoken dargestellt.

Ein Trägertoken ist ein einfaches Sicherheitstoken, das dem „Träger“ den Zugriff auf eine geschützte Ressource ermöglicht. In diesem Kontext ist der „Träger“ jede beliebige Partei, die das Token vorweisen kann. Um das Trägertoken zu erhalten, muss sich die Partei zwar zunächst bei Azure AD authentifizieren, falls jedoch keine Maßnahmen ergriffen werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer fremden Partei abgefangen und verwendet werden. Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine unbefugte Verwendung durch nicht autorisierte Parteien verhindert. Trägertoken besitzen dagegen keinen solchen Mechanismus und müssen über einen sicheren Kanal wie etwa Transport Layer Security (HTTPS) übertragen werden. Wird ein Trägertoken als Klartext gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abfangen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Trägertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert. Weitere Sicherheitsüberlegungen zu Trägertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).

Viele der von Azure AD B2C ausgestellten Token werden als JSON-Webtoken (JWTs) implementiert. Ein JWT stellt eine kompakte, URL-sichere Methode zum Übertragen von Informationen zwischen zwei Parteien dar. Die in JWTs enthaltenen Informationen werden als „Ansprüche“ oder Assertionen von Informationen zum Träger und Antragsteller des Tokens bezeichnet. Die Ansprüche in JWTs sind JSON-Objekte, die für die Übertragung codiert und serialisiert wurden. Da die von Azure AD B2C ausgestellten JWTs signiert, aber nicht verschlüsselt sind, können Sie den Inhalt eines JWTs problemlos für Debugzwecke untersuchen. Dafür stehen mehrere Tools zur Verfügung, beispielsweise [calebb.net](https://calebb.net). Weitere Informationen zu JWTs finden Sie in der [JWT-Spezifikation](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## ID-Token

ID-Token sind eine Form von Sicherheitstoken, die Ihre App von den `authorize`- und `token`-Endpunkten von Azure AD B2C empfängt. Sie werden als [JWTs](#types-of-tokens) dargestellt und enthalten Ansprüche, die Sie zur Identifizierung der Benutzer für die App verwenden können. Wenn ID-Token über den `authorize`-Endpunkt beschafft werden, werden sie häufig verwendet, um den Benutzer an einer Webanwendung anzumelden. Bei der Beschaffung über den `token`-Endpunkt können ID-Token in HTTP-Anforderungen gesendet werden, wenn zwischen zwei Komponenten derselben Anwendung oder desselben Diensts kommuniziert wird. Sie können die Ansprüche nach Bedarf in einem ID-Token verwenden. Im Allgemeinen werden Sie in einer App zum Anzeigen von Kontoinformationen oder zum Treffen von Entscheidungen hinsichtlich der Zugriffssteuerung eingesetzt.

ID-Token sind zu diesem Zeitpunkt zwar signiert, aber nicht verschlüsselt. Wenn Ihre App oder API ein ID-Token empfängt, muss sie [die Signatur überprüfen](#validating-tokens), um die Authentizität des Tokens nachzuweisen. Zudem müssen einige Ansprüche im Token überprüft werden, um seine Gültigkeit zu belegen. Die von einer App überprüften Ansprüche variieren je nach Szenarioanforderungen. Es gibt jedoch einige [allgemeine Anspruchsüberprüfungen](#validating-tokens), die Ihre App in jedem Szenario ausführen muss.

Vollständige Informationen zu den Ansprüchen in ID-Token sowie ein Beispiel für ein ID-Token sind nachfolgend aufgeführt. Beachten Sie, dass die Ansprüche in ID-Token nicht in einer bestimmten Reihenfolge zurückgegeben werden. Darüber hinaus können neue Ansprüche in ID-Token zu einem beliebigen Zeitpunkt eingeführt werden. Ihre App darf jedoch nicht unterbrochen werden, wenn neue Ansprüche eingeführt werden. Die folgende Liste enthält die Ansprüche, die Ihre App zum Zeitpunkt der Erstellung dieses Dokuments zuverlässig interpretieren kann. Überprüfen Sie zu Übungszwecken die Ansprüche im ID-Beispieltoken, indem Sie sie in [calebb.net](https://calebb.net) einfügen. Ausführlichere Informationen finden Sie ggf. in der [OpenID Connect-Spezifikation](http://openid.net/specs/openid-connect-core-1_0.html).

#### ID-Beispieltoken
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### Ansprüche in ID-Token

Mit Azure AD B2C verfüge Sie über eine präzise Kontrolle über den Inhalt Ihrer Token. Sie können [Richtlinien](active-directory-b2c-reference-policies.md) zum Senden einer bestimmten Gruppe von Benutzerdaten in Ansprüchen konfigurieren, die von der App für den Betrieb benötigt werden. Diese Ansprüche können Standardeigenschaften, z. B. `displayName` und `emailAddress` des Benutzers, sowie [benutzerdefinierte Benutzerattribute](active-directory-b2c-reference-custom-attr) enthalten, die Sie in Ihrem B2C-Verzeichnis definieren können. Jedes ID-Token, das Sie empfangen, enthält aber einen bestimmten Satz sicherheitsbezogener Ansprüche, die von Ihren Anwendungen genutzt werden können, um Benutzer und Anforderungen sicher zu authentifizieren. Unten sind die Ansprüche aufgeführt, von denen Sie erwarten, dass sie in jedem ID-Token enthalten sind, das von Azure AD B2C ausgestellt wird. Alle anderen Ansprüche werden anhand der Richtlinie bestimmt.

| Name | Anspruch | Beispielwert | Beschreibung |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Zielgruppe | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifiziert den vorgesehenen Empfänger des Tokens. In ID-Token ist die Zielgruppe die Anwendungs-ID der App, die Ihrer App im App-Registrierungsportal zugewiesen wurde. Ihre App sollte diesen Wert überprüfen und das Token ablehnen, wenn er nicht übereinstimmt. |
| Aussteller | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifiziert den Sicherheitstokendienst (Security Token Service, STS), der das Token und das Azure AD-Verzeichnis, in dem der Benutzer authentifiziert wurde, erstellt und zurückgibt. Ihre App sollte den Ausstelleranspruch überprüfen, um sicherzustellen, dass das Token vom Endpunkt mit der Version 2.0 stammt. |
| Ausgestellt um | `iat` | `1438535543` | Die Zeit, zu der das Token ausgestellt wurde (dargestellt als Epochenzeit) |
| Ablaufzeit | `exp` | `1438539443` | Die Zeit, zu der das Token ungültig wird (dargestellt als Epochenzeit). Ihre App sollte mit diesem Anspruch die Gültigkeit der Tokenlebensdauer überprüfen. |
| Nicht vor | `nbf` | `1438535543` | Die Zeit, zu der das Token gültig wird (dargestellt als Epochenzeit). Dieser Wert ist normalerweise mit dem Ausstellungszeitpunkt identisch. Ihre App sollte mit diesem Anspruch die Gültigkeit der Tokenlebensdauer überprüfen. |
| Version | `ver` | `1.0` | Die Version des ID-Tokens, wie in Azure AD definiert. |
| Codehash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Der Codehash ist nur in ID-Token enthalten, wenn das ID-Token zusammen mit einem OAuth 2.0-Autorisierungscode ausgestellt wird. Mit seiner Hilfe kann die Authentizität eines Autorisierungscodes überprüft werden. Weitere Informationen zum Ausführen dieser Überprüfung finden Sie in der [OpenID Connect-Spezifikation](http://openid.net/specs/openid-connect-core-1_0.html). |
| Zugriffstokenhash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Der Zugriffstokenhash ist nur in ID-Token enthalten, wenn das ID-Token zusammen mit einem OAuth 2.0-Zugriffstoken ausgestellt wird. Mit seiner Hilfe kann die Authentizität eines Zugriffstokens überprüft werden. Weitere Informationen zum Ausführen dieser Überprüfung finden Sie in der [OpenID Connect-Spezifikation](http://openid.net/specs/openid-connect-core-1_0.html). |
| Nonce | `nonce` | `12345` | Mithilfe der Nonce sollen Tokenwiedergabeangriffe abgewehrt werden. Ihre App kann eine Nonce in einer Autorisierungsanforderung mithilfe des `nonce`-Abfrageparameters angeben. Der in der Anforderung angegebene Wert wird (unverändert) im `nonce`-Anspruch des ID-Tokens ausgegeben. Dadurch kann Ihre App den Wert anhand des Werts überprüfen, der in der Anforderung angegeben ist. Dabei wird die Sitzung der App einem bestimmten ID-Token zugeordnet. Ihre App sollte diese Überprüfung während des Überprüfungsvorgangs des ID-Tokens ausführen. |
| Betreff | `sub` | `Not supported currently. Use oid claim.` | Der Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer App. Dieser Wert ist unveränderlich und kann nicht neu zugewiesen oder wiederverwendet werden. Daher kann er für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden, z. B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Der Anspruch „Betreff“ ist in der Preview-Version von Azure AD B2C aber noch nicht implementiert. Anstatt den Anspruch „Betreff“ für die Autorisierung zu verwenden, sollten Sie Ihre Richtlinien so konfigurieren, dass sie den Objekt-ID-`oid`-Anspruch enthalten, und dessen Wert zum Identifizieren von Benutzern verwenden. |
| Klassenreferenz des Anwendungskontexts | `acr` | `b2c_1_sign_in` | Der Name der Richtlinie, die zum Abrufen des ID-Tokens verwendet wurde. |
| Authentifizierungszeit | `auth_time | `1438535543` | Der Zeitpunkt (in Epochenzeit), zu dem der Benutzer seine Anmeldeinformationen zum letzten Mal eingegeben hat. |



## Zugriffstoken

Azure AD B2C stellt derzeit keine Zugriffstoken aus. Die Authentifizierung zwischen zwei unterschiedlichen Parteien wird in dieser frühen Preview-Phase nicht unterstützt. Sie können ID-Token aber verwenden, um zwischen Komponenten derselben Anwendung zu kommunizieren. Informationen zu den Anwendungen und Authentifizierungsszenarien, die von der öffentlichen Preview-Version von Azure AD B2C unterstützt werden, finden Sie im [Artikel zu B2C-Apps](active-directory-b2c-apps.md).

## Aktualisierungstoken

Aktualisierungstoken sind Sicherheitstoken, mit denen Ihre App neue ID-Token und Zugriffstoken in einem OAuth 2.0-Fluss abrufen kann. Dadurch erhält Ihre App langfristig Zugriff auf Ressourcen im Auftrag eines Benutzers, ohne dass ein Benutzereingriff erforderlich ist.

Um eine Aktualisierung bei einer Tokenantwort zu erhalten, muss Ihre App den `offline_acesss`-Bereich anfordern. Weitere Informationen zum `offline_access`-Bereich finden Sie in der [Azure AD B2C-Protokollreferenz](active-directory-b2c-reference-protocols.md).

Der Inhalt von Aktualisierungstoken ist für Ihre App niemals zugänglich. Sie werden von Azure AD ausgestellt und können nur von Azure AD untersucht und interpretiert werden. Sie sind zwar sehr lange gültig, in Ihrer App darf aber nicht von einer unbegrenzten Gültigkeitsdauer ausgegangen werden. Aktualisierungstoken können jederzeit aus unterschiedlichen Gründen ungültig werden. Die einzige Möglichkeit für Ihre App, die Gültigkeit eines Aktualisierungstokens zu überprüfen, besteht in der Einlösung des Tokens. Führen Sie dazu eine Tokenanforderung für Azure AD aus.

Wenn Sie ein Aktualisierungstoken für ein neues Token einlösen (und wenn Ihrer App der `offline_access`-Bereich zugeteilt wurde), erhalten Sie ein neues Aktualisierungstoken in der Tokenantwort. Speichern Sie das neu ausgestellte Aktualisierungstoken, und ersetzen Sie das zuvor in der Anforderung verwendete Token. Dadurch wird sichergestellt, dass die Aktualisierungstoken möglichst lange gültig bleiben.

## Überprüfen von Token

Zu diesem Zeitpunkt müssen Ihre Apps nur ID-Token überprüfen. Bei der Überprüfung eines ID-Tokens muss Ihre App sowohl die Signatur des ID-Tokens als auch die Ansprüche im ID-Token überprüfen.

<!-- TODO: Link -->
Viele Open Source-Bibliotheken sind für die Überprüfung von JWTs für unterschiedliche Sprachen verfügbar. Wir empfehlen die Verwendung einer dieser Optionen, anstatt eine eigene Validierungslogik zu implementieren. Diese Informationen sind hilfreich, um die richtige Verwendung dieser Bibliotheken zu erlernen.

#### Überprüfen der Signatur
Ein JWT enthält drei Segmente, die durch das Zeichen `.` getrennt sind. Das erste Segment wird als **Header**, das zweite als **Text** und das dritte als **Signatur** bezeichnet. Mit dem Signatursegment kann die Authentizität des ID-Tokens überprüft werden, sodass es für Ihre App als vertrauenswürdig eingestuft werden kann.

ID-Token werden mit branchenüblichen asymmetrischen Verschlüsselungsalgorithmen wie etwa RSA 256 signiert. Der Header des ID-Tokens enthält Informationen zum Schlüssel und zur Verschlüsselungsmethode, die zum Signieren des Tokens verwendet wird:

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Der `alg`-Anspruch gibt den Algorithmus an, mit dem das Token signiert wurde. Der Anspruch `kid` oder `x5t` hingegen bezeichnet den bestimmten öffentlichen Schlüssel, mit dem das Token signiert wurde.

Zu einem beliebigen Zeitpunkt signiert Azure AD unter Umständen ein ID-Token mithilfe eines bestimmten Satzes von Paaren aus öffentlichen und privaten Schlüsseln. Azure AD wechselt regelmäßig durch die möglichen Sätze von Schlüsseln. Ihre App muss also auf die automatische Verarbeitung dieser Schlüsseländerungen ausgelegt sein. Die von Azure AD verwendeten öffentlichen Schlüssel sollten alle 24 Stunden auf Änderungen überprüft werden.

Azure AD B2C verfügt über einen OpenID Connect-Metadatenendpunkt, mit dem die App Informationen über Azure AD B2C zur Laufzeit abrufen kann. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Es gibt ein JSON-Metadatendokument für jede Richtlinie in Ihrem B2C-Verzeichnis. Das Metadatendokument für die `b2c_1_sign_in`-Richtlinie in `fabrikamb2c.onmicrosoft.com` befindet sich z. B. unter:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Sie können die Signaturschlüsseldaten, die zum Überprüfen der Signatur erforderlich sind, mithilfe des OpenID Connect-Metadatendokuments abrufen. Dieses Dokument befindet sich hier:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

Hierbei ist `fabrikamb2c.onmicrosoft.com` das b2c-Verzeichnis, das zum Authentifizieren des Benutzers verwendet wird, und `b2c_1_sign_in` ist die Richtlinie, die zum Beschaffen des ID-Tokens verwendet wird. Es gibt zwei Möglichkeiten zu ermitteln, welche Richtlinie zum Signieren eines ID-Tokens verwendet wurde (und wo die Metadaten abgerufen werden können). Zunächst einmal ist der Richtlinienname im `acr`-Anspruch im ID-Token enthalten. Sie können Ansprüche aus dem Hauptteil des JWT analysieren, indem Sie eine Base64-Decodierung auf den Hauptteil anwenden und die sich ergebende JSON-Zeichenfolge deserialisieren. Der `acr`-Anspruch ist der Name der Richtlinie, die zum Ausstellen des ID-Tokens verwendet wurde. Eine andere Möglichkeit besteht darin, die Richtlinie beim Übermitteln der Anforderung im Wert des Parameters `state` zu verschlüsseln und später zu entschlüsseln, um die verwendete Richtlinie zu bestimmen. Beide Methoden sind zulässig.

Bei dem Metadatendokument handelt es sich um ein JSON-Objekt, das zahlreiche nützliche Informationen enthält, beispielsweise den Ort der verschiedenen Endpunkte, die zum Ausführen der OpenID Connect-Authentifizierung erforderlich sind. Darüber hinaus enthält es einen `jwks_uri`, der den Speicherort des Satzes von öffentlichen Schlüsseln zum Signieren von Token angibt. Dieser Speicherort ist nachfolgend angegeben. Es wird jedoch empfohlen, ihn dynamisch mithilfe des Metadatendokuments abzurufen und dabei den `jwks_uri` zu analysieren:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Das JSON-Dokument unter dieser URL enthält alle Informationen zu den zu diesem Zeitpunkt verwendeten öffentlichen Schlüsseln. Ihre App kann mit dem Anspruch `kid` oder `x5t` im JWT-Header auswählen, welcher öffentliche Schlüssel in diesem Dokument zum Signieren eines bestimmten Tokens verwendet wurde. Sie kann anschließend die Signaturüberprüfung mithilfe des korrekten öffentlichen Schlüssels und des angegebenen Algorithmus ausführen.

Die Signaturüberprüfung wird in diesem Dokument nicht erläutert. Es stehen jedoch zahlreiche Open Source-Bibliotheken mit hilfreichen Informationen zur Verfügung.

#### Überprüfen der Ansprüche
Wenn Ihre App oder API ein ID-Token empfängt, sollte sie auch die Ansprüche im ID-Token überprüfen. Das umfasst:

- Anspruch **Zielgruppe**: Zum Überprüfen, ob das ID-Token an Ihre App übergeben werden sollte.
- Ansprüche **Nicht vor** und **Ablaufzeit**: Zum Sicherstellen, dass das ID-Token nicht abgelaufen ist.
- Anspruch **Aussteller**: Zum Überprüfen, ob das Token tatsächlich von Azure AD für Ihre App ausgestellt wurde.
- **Nonce**: Zur Abwehr von Tokenwiedergabeangriffen.

Details zu den erwarteten Werten für diese Ansprüche finden Sie oben im Abschnitt [ID-Token](#id_tokens).

## Tokengültigkeitsdauer

Die folgenden Angaben zur Tokengültigkeitsdauer dienen ausschließlich zu Informationszwecken, da sie beim Entwickeln und Debuggen von Apps hilfreich sein können. In Ihren Apps darf nicht davon ausgegangen werden, dass die Gültigkeitsdauer konstant bleibt, da sich diese jederzeit ändern kann.

| Tokenverschlüsselung | Gültigkeitsdauer | Beschreibung |
| ----------------------- | ------------------------------- | ------------ |
| ID-Token | 1 Stunde | ID-Token gelten in der Regel eine Stunde. Ihre Web-App kann die gleiche Dauer für die Sitzung mit dem Benutzer verwenden (empfohlen) oder eine andere Sitzungsdauer wählen. Falls Ihre App ein neues ID-Token abrufen muss, muss sie lediglich eine neue Anmeldeanforderung an Azure AD senden. Wenn der Benutzer eine gültige Browsersitzung für Azure AD nutzt, müssen unter Umständen die Anmeldeinformationen nicht erneut eingegeben werden. |
| Aktualisierungstoken | Bis zu 14 Tage | Ein einzelnes Aktualisierungstoken ist maximal 14 Tage gültig. Das Aktualisierungstoken kann jedoch jederzeit aus verschiedenen Gründen ungültig werden. Ihre App muss daher ein Aktualisierungstoken verwenden, bis es fehlschlägt oder bis es von Ihrer App durch ein neues Aktualisierungstoken ersetzt wird. Ein Aktualisierungstoken wird außerdem ungültig, wenn der Benutzer das letzte Mal vor 90 Tagen seine Anmeldeinformationen eingegeben hat. |
| Autorisierungscodes | 5 Minuten | Autorisierungscodes haben absichtlich eine kurze Gültigkeitsdauer. Sie müssen umgehend für Zugriffs-, ID- und Aktualisierungstoken eingelöst werden, wenn sie empfangen werden. |

<!---HONumber=Oct15_HO3-->