<properties
	pageTitle="Azure Active Directory B2C-Vorschau | Microsoft Azure"
	description="Die Typen der in der Vorschauversion von Azure Active Directory B2C ausgestellten Token"
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


# Azure AD B2C-Vorschau: Tokenreferenz

Azure Active Directory (Azure AD) B2C stellt bei der Verarbeitung der einzelnen [Authentifizierungsflüsse](active-directory-b2c-apps.md) verschiedene Arten von Sicherheitstoken aus In diesem Dokument sind das Format, die Sicherheitsmerkmale und der Inhalt aller Tokentypen beschrieben.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Tokentypen

Azure AD B2C unterstützt das [OAuth 2.0-Autorisierungsprotokoll](active-directory-b2c-reference-protocols.md), das Zugriffstoken und Aktualisierungstoken verwendet. Es unterstützt darüber hinaus die Authentifizierung und Anmeldung über [OpenID Connect](active-directory-b2c-reference-protocols.md). Dabei wird ein dritter Tokentyp, das ID-Token, eingeführt. Alle diese Token werden als Bearertoken dargestellt.

Ein Bearertoken ist ein einfaches Sicherheitstoken, das dem „Träger“ den Zugriff auf eine geschützte Ressource ermöglicht. Der „Träger“ ist jede beliebige Partei, die das Token vorweisen kann. Azure AD muss eine Partei zunächst authentifizieren, damit sie ein Bearertoken erhalten kann. Falls jedoch nicht die erforderlichen Schritte ausgeführt werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer nicht vorgesehenen Partei abgefangen und verwendet werden. Einige Sicherheitstoken verfügen über einen integrierten Mechanismus, der eine Verwendung durch nicht autorisierte Parteien verhindert, Bearertoken besitzen diesen Mechanismus jedoch nicht. Sie und müssen über einen sicheren Kanal wie etwa Transport Layer Security (HTTPS) übertragen werden.

Wird ein Bearertoken außerhalb eines sicheren Kanals gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abrufen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Bearertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert.

Weitere Sicherheitsüberlegungen zu Bearertoken finden Sie unter [RFC 6750, Abschnitt 5](http://tools.ietf.org/html/rfc6750).

Viele der von Azure AD B2C ausgestellten Token werden als JSON-Webtoken (JWTs) implementiert. Ein JWT stellt eine kompakte, URL-sichere Methode zum Übertragen von Informationen zwischen zwei Parteien dar. JWTs enthalten Informationen, die als „Ansprüche“ bezeichnet werden. Dies sind Assertionen von Informationen zum Träger und zum Antragsteller des Tokens. Die Ansprüche in JWTs sind JSON-Objekte, die für die Übertragung codiert und serialisiert wurden. Da die von Azure AD B2C ausgestellten JWTs signiert, aber nicht verschlüsselt sind, können Sie den Inhalt eines JWTs problemlos untersuchen und debuggen. Dafür stehen mehrere Tools zur Verfügung, beispielsweise [calebb.net](https://calebb.net). Weitere Informationen zu JWTs, finden Sie in der [JWT-Spezifikationen](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### ID-Token

ID-Token sind eine Form von Sicherheitstoken, die Ihre App von den `authorize`- und `token`-Endpunkten von Azure AD B2C empfängt. ID-Token werden als [JWTs](#types-of-tokens) dargestellt und enthalten Ansprüche, die Sie zur Identifizierung der Benutzer für die App verwenden können. Vom `authorize`-Endpunkt abgerufene ID-Token werden häufig verwendet, um Benutzer an Webanwendungen anzumelden. Vom `token`-Endpunkt abgerufene ID-Token können während der Kommunikation zwischen zwei Komponenten derselben Anwendung oder desselben Diensts in HTTP-Anforderungen gesendet werden. Sie können die Ansprüche nach Bedarf in einem ID-Token verwenden. Im Allgemeinen werden Sie in einer App zum Anzeigen von Kontoinformationen oder zum Treffen von Entscheidungen hinsichtlich der Zugriffssteuerung eingesetzt.

ID-Token sind zu diesem Zeitpunkt zwar signiert, jedoch nicht verschlüsselt. Wenn Ihre App oder API ein ID-Token empfängt, muss sie die [Signatur überprüfen](#token-validation), um die Authentizität des Tokens nachzuweisen Ihre App bzw. API muss außerdem einige Ansprüche im Token überprüfen, um seine Gültigkeit zu belegen. Je nach Szenarioanforderungen können die von einer App überprüften Ansprüche variieren. Einige [allgemeine Anspruchsüberprüfungen](#token-validation) muss Ihre App jedoch in jedem Szenario durchführen.

Weitere Informationen zu den Ansprüchen in ID-Token das folgende ID-Beispieltoken finden Sie weiter unten in diesem Handbuch. Beachten Sie, dass die Ansprüche in ID-Token nicht in einer bestimmten Reihenfolge zurückgegeben werden. Darüber hinaus können jederzeit neue Ansprüche in ID-Token eingeführt werden Ihre App darf nicht unterbrochen werden, wenn neue Ansprüche eingeführt werden. Die Liste enthält die Ansprüche, die Ihre App zum Zeitpunkt der Erstellung dieses Dokuments zuverlässig interpretieren kann. Überprüfen Sie zu Übungszwecken die Ansprüche im ID-Beispieltoken, indem Sie sie in [calebb.net](https://calebb.net) einfügen. Weitere Informationen finden Sie in der [OpenID Connect-Spezifikation](http://openid.net/specs/openid-connect-core-1_0.html).

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

Bei der Verwendung von Azure AD B2C verfügen Sie über eine präzise Kontrolle über den Inhalt Ihrer Token. Sie können [Richtlinien](active-directory-b2c-reference-policies.md) zum Senden bestimmter Gruppen von Benutzerdaten in Ansprüchen konfigurieren, die von der App für den Betrieb benötigt werden. Diese Ansprüche können Standardeigenschaften wie z. B. `displayName` und `emailAddress` des Benutzers enthalten. Sie können auch [benutzerdefinierte Attribute](active-directory-b2c-reference-custom-attr.md) enthalten, die Sie in Ihrem B2C-Verzeichnis definieren. Jedes ID-Token, das Sie empfangen, enthält aber einen bestimmten Satz sicherheitsbezogener Ansprüche. Diese können von Ihren Anwendungen genutzt werden, um Benutzer und Anforderungen sicher zu authentifizieren. Im Folgenden finden Sie die Ansprüche, von denen Sie erwarten, dass sie in jedem von Azure AD B2C ausgestellten ID-Token enthalten sind. Alle weiteren Ansprüche werden anhand von Richtlinien bestimmt.

| Name | Anspruch | Beispielwert | Beschreibung |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Zielgruppe | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Ein „Audience“-Anspruch identifiziert den vorgesehenen Empfänger des Tokens. Für ID-Token ist die Zielgruppe die Anwendungs-ID der App, die Ihrer App im App-Registrierungsportal zugewiesen wurde. Ihre App sollte diesen Wert überprüfen und das Token ablehnen, wenn er nicht übereinstimmt. |
| Aussteller | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Dieser Anspruch identifiziert den Sicherheitstokendienst (STS), der das Token erstellt und zurückgibt. Er identifiziert auch das Azure AD-Verzeichnis, in dem der Benutzer authentifiziert wurde. Ihre App sollte den Ausstelleranspruch überprüfen, um sicherzustellen, dass das Token vom Endpunkt mit der Version 2.0 stammt. |
| Ausgestellt um | `iat` | `1438535543` | Dieser Anspruch ist die Zeit, zu der das Token ausgestellt wurde (dargestellt als Epochenzeit) |
| Ablaufzeit | `exp` | `1438539443` | Der Ablaufzeit-Anspruch ist die Zeit, zu der das Token ungültig wird (dargestellt als Epochenzeit) Ihre App sollte mit diesem Anspruch die Gültigkeit der Tokenlebensdauer überprüfen. |
| Nicht vor | `nbf` | `1438535543` | Dieser Anspruch ist die Zeit, zu der das Token gültig wird (dargestellt als Epochenzeit). Dieser Wert ist normalerweise mit dem Ausstellungszeitpunkt des Tokens identisch. Ihre App sollte mit diesem Anspruch die Gültigkeit der Tokenlebensdauer überprüfen. |
| Version | `ver` | `1.0` | Dies ist die Version des ID-Tokens, wie in Azure AD definiert. |
| Codehash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Ein Codehash ist nur in einem ID-Token enthalten, wenn das Token zusammen mit einem OAuth 2.0-Autorisierungscode ausgestellt wird. Mithilfe eines Codehashs kann die Authentizität eines Autorisierungscodes überprüft werden. Weitere Informationen zum Ausführen dieser Überprüfung finden Sie in der [OpenID Connect-Spezifikation](http://openid.net/specs/openid-connect-core-1_0.html). |
| Zugriffstokenhash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Ein Zugriffstokenhash ist nur in einem ID-Token enthalten, wenn das Token zusammen mit einem OAuth 2.0-Zugriffstoken ausgestellt wird. Mithilfe des Zugriffstokenhashs kann die Authentizität eines Zugriffstokens überprüft werden. Weitere Informationen zum Ausführen dieser Überprüfung finden Sie in der [OpenID Connect-Spezifikation](http://openid.net/specs/openid-connect-core-1_0.html). |
| Nonce | `nonce` | `12345` | Eine Nonce ist eine Strategie zum Abwehren von Tokenwiedergabeangriffe. Ihre App kann eine Nonce in einer Autorisierungsanforderung mithilfe des `nonce`-Abfrageparameters angeben. Der in der Anforderung angegebene Wert wird unverändert im `nonce`-Anspruch des ID-Tokens ausgegeben. Dadurch kann Ihre App den Wert anhand des Werts überprüfen, der in der Anforderung angegeben ist. Dabei wird die Sitzung der App einem bestimmten ID-Token zugeordnet. Ihre App sollte diese Überprüfung während des Überprüfungsvorgangs des ID-Tokens ausführen. |
| Betreff | `sub` | `Not supported currently. Use oid claim.` | Dies ist ein Prinzipal, für den das Token Informationen zusichert, z. B. der Benutzer einer App. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Er kann für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden, z. B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Der Anspruch „Betreff“ ist in der Preview-Version von Azure AD B2C aber noch nicht implementiert. Sie sollten Ihre Richtlinien so konfigurieren, dass sie den Objekt-ID-`oid`-Anspruch enthalten, und dessen Wert zum Identifizieren von Benutzern verwenden, anstatt den Anspruch „Betreff“ für die Autorisierung zu verwenden. |
| Klassenreferenz des Anwendungskontexts | `acr` | `b2c_1_sign_in` | Dies ist der Name der Richtlinie, die zum Abrufen des ID-Tokens verwendet wurde. |
| Authentifizierungszeit | `auth_time | `1438535543` | Dieser Anspruch ist die Uhrzeit (dargestellt als Epochenzeit), zu dem der Benutzer seine Anmeldeinformationen zum letzten Mal eingegeben hat. |



### Zugriffstoken

Azure AD B2C stellt derzeit keine Zugriffstoken aus. Die Authentifizierung zwischen zwei Parteien wird in der Vorschauphase nicht unterstützt. Sie können ID-Token jedoch verwenden, um zwischen Komponenten derselben Anwendung zu kommunizieren Erfahren Sie mehr über die [Anwendungen und Authentifizierungsszenarien, die von der Azure AD B2C-Vorschau unterstützt werden](active-directory-b2c-apps.md).

### Aktualisierungstoken

Aktualisierungstoken sind Sicherheitstoken, mit denen Ihre App neue ID-Token und Zugriffstoken in einem OAuth 2.0-Fluss abrufen kann. Sie ermöglichen Ihrer App langfristig Zugriff auf Ressourcen im Auftrag von Benutzern, ohne dass eine Interaktion mit diesen erforderlich ist.

Um bei einer Tokenantwort ein Aktualisierungstoken zu erhalten, muss Ihre App den `offline_acesss`-Bereich anfordern. Weitere Informationen zum `offline_access`-Bereich finden Sie in der [Azure AD B2C-Protokollreferenz](active-directory-b2c-reference-protocols.md).

Der Inhalt von Aktualisierungstoken ist für Ihre App niemals zugänglich. Sie werden von Azure AD ausgestellt und können nur von Azure AD untersucht und interpretiert werden. Obwohl sie lange gültig sind, darf beim Schreiben Ihrer App nicht von der bestimmten Gültigkeitsdauer eines Aktualisierungstokens ausgegangen werden. Aktualisierungstoken können jederzeit aus unterschiedlichen Gründen ungültig werden. Die einzige Möglichkeit für Ihre App, die Gültigkeit eines Aktualisierungstokens zu überprüfen, besteht in der Einlösung des Tokens. Führen Sie dazu eine Tokenanforderung für Azure AD aus.

Wenn Sie ein Aktualisierungstoken für ein neues Token einlösen (und wenn Ihrer App der `offline_access`-Bereich zugeteilt wurde), erhalten Sie ein neues Aktualisierungstoken in der Tokenantwort. Sie sollten das neu ausgestellte Aktualisierungstoken speichern und das bisher in der Anforderung verwendete Aktualisierungstoken dadurch ersetzen. Dadurch wird sichergestellt, dass die Aktualisierungstoken möglichst lange gültig bleiben.

## Tokenüberprüfung

Zu diesem Zeitpunkt sollten Ihre Apps nur ID-Token überprüfen müssen. Zum Überprüfen eines ID-Tokens muss Ihre App sowohl die Signatur als auch die Ansprüche des Tokens überprüfen.

<!-- TODO: Link -->
Für die Überprüfung von JWTs sind je nach gewünschter Sprache viele Open Source-Bibliotheken verfügbar. Wir empfehlen, diese Optionen zu erkunden, anstatt eine eigene Validierungslogik zu implementieren. Die Informationen in diesem Handbuch sind hilfreich für sie richtige Verwendung dieser Bibliotheken.

### Überprüfen der Signatur
Ein JWT enthält drei Segmente, die durch das Zeichen `.` getrennt sind. Das erste Segment ist der **Header**, das zweite der **Text** und das dritte die **Signatur**. Mit dem Signatursegment kann die Authentizität des ID-Tokens überprüft werden, sodass es für Ihre App als vertrauenswürdig eingestuft werden kann.

ID-Token werden mit branchenüblichen asymmetrischen Verschlüsselungsalgorithmen wie etwa RSA 256 signiert. Der Header des ID-Tokens enthält Informationen zum Schlüssel und zur Verschlüsselungsmethode, die zum Signieren des Tokens verwendet wird:

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Die `alg`-Anspruch gibt den Algorithmus an, mit dem das Token signiert wurde. Der Anspruch `kid` oder `x5t` bezeichnet den bestimmten öffentlichen Schlüssel, mit dem das Token signiert wurde.

Zu einem beliebigen Zeitpunkt signiert Azure AD unter Umständen ein ID-Token mithilfe eines bestimmten Satzes von Paaren aus öffentlichen und privaten Schlüsseln. Azure AD wechselt regelmäßig durch die möglichen Sätze von Schlüsseln. Ihre App muss also auf die automatische Verarbeitung dieser Schlüsseländerungen ausgelegt sein. Die von Azure AD verwendeten öffentlichen Schlüssel sollten alle 24 Stunden auf Änderungen überprüft werden.

Azure AD B2C verfügt über einen OpenID Connect-Metadatenendpunkt. Mit diesem kann die App Informationen über Azure AD B2C zur Laufzeit abrufen. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Ihr B2C-Verzeichnis enthält für jede Richtlinie ein JSON-Metadatendokument. Das Metadatendokument für die `b2c_1_sign_in`-Richtlinie in `fabrikamb2c.onmicrosoft.com` befindet sich z. B. unter:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Sie können die Signaturschlüsseldaten, die zum Überprüfen der Signatur erforderlich sind, mithilfe des OpenID Connect-Metadatendokuments abrufen. Dieses Dokument befindet sich unter:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` ist das B2C-Verzeichnis, das zum Authentifizieren des Benutzers verwendet wird, und `b2c_1_sign_in` ist die Richtlinie, die zum Abrufen des ID-Tokens verwendet wird. Sie haben zwei Optionen zum Ermitteln, welche Richtlinie zum Signieren eines ID-Tokens verwendet wurde (und wo die Metadaten abgerufen werden können). Zunächst einmal ist der Richtlinienname im `acr`-Anspruch im ID-Token enthalten. Sie können Ansprüche aus dem Hauptteil des JWT analysieren, indem Sie eine Base64-Decodierung auf den Hauptteil anwenden und die sich ergebende JSON-Zeichenfolge deserialisieren. Der `acr`-Anspruch ist der Name der Richtlinie, die zum Ausstellen des ID-Tokens verwendet wurde. Eine andere Möglichkeit besteht darin, die Richtlinie beim Übermitteln der Anforderung im Wert des Parameters `state` zu verschlüsseln und später zu entschlüsseln, um die verwendete Richtlinie zu bestimmen. Beide Methoden sind gültig.

Beim Metadatendokument handelt es sich um ein JSON-Objekt, das zahlreiche nützliche Informationen enthält, beispielsweise den Speicherort der Endpunkte, die zum Ausführen der OpenID Connect-Authentifizierung erforderlich sind. Außerdem ist ein `jwks_uri` enthalten, der den Speicherort des Satzes von öffentlichen Schlüsseln zum Signieren von Token angibt. Dieser Speicherort ist hier angegeben. Es wird jedoch empfohlen, ihn dynamisch mithilfe des Metadatendokuments abzurufen und dabei `jwks_uri` zu analysieren:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Das JSON-Dokument unter dieser URL enthält alle Informationen zu den zu einem bestimmten Zeitpunkt verwendeten öffentlichen Schlüsseln. Ihre App kann mit dem Anspruch `kid` oder `x5t` im JWT-Header auswählen, welcher öffentliche Schlüssel im JSON-Dokument zum Signieren eines bestimmten Tokens verwendet wird. Sie kann anschließend die Signaturüberprüfung mithilfe des korrekten öffentlichen Schlüssels und des angegebenen Algorithmus ausführen.

Die Beschreibung der Signaturüberprüfung geht über den Rahmen dieses Dokuments hinaus. Hilfreiche Informationen stehen jedoch bei Bedarf in zahlreiche Open Source-Bibliotheken zur Verfügung.

### Überprüfen der Ansprüche
Wenn Ihre App oder API ein ID-Token empfängt, sollte sie auch die Ansprüche im ID-Token überprüfen. Diese umfassen:

- Anspruch **Zielgruppe**: Zum Überprüfen, ob das ID-Token an Ihre App übergeben werden sollte.
- Ansprüche **Nicht vor** und **Ablaufzeit**: Zum Sicherstellen, dass das ID-Token nicht abgelaufen ist.
- Anspruch **Aussteller**: Zum Überprüfen, ob das Token von Azure AD für Ihre App ausgestellt wurde.
- **Nonce**: Dies ist eine Strategie zum Abwehren von Tokenwiedergabeangriffen.

Details zu den erwarteten Werten für diese Ansprüche finden Sie im vorhergehenden Abschnitt [ID-Token](#id-tokens).

## Tokengültigkeitsdauer

Die folgenden Angaben zur Tokengültigkeitsdauer dienen zu Informationszwecken. Sie können beim Entwickeln und Debuggen von Apps hilfreich sein. Beachten Sie, dass beim Schreiben Ihrer Apps nicht davon ausgegangen werden darf, dass die Gültigkeitsdauer konstant bleibt. Sie können und werden sich ändern.

| Tokenverschlüsselung | Gültigkeitsdauer | Beschreibung |
| ----------------------- | ------------------------------- | ------------ |
| ID-Token | Eine Stunde | ID-Token sind in der Regel eine Stunde lang gültig. Ihre Web-App kann die gleiche Dauer für Sitzungen mit Benutzern verwenden (empfohlen). Sie können auch eine andere Sitzungslebensdauer wählen. Falls Ihre App ein neues ID-Token abrufen muss, muss sie lediglich eine neue Anmeldeanforderung an Azure AD senden. Wenn ein Benutzer eine gültige Browsersitzung für Azure AD nutzt, müssen unter Umständen die Anmeldeinformationen nicht erneut eingegeben werden. |
| Aktualisierungstoken | Bis zu 14 Tage | Ein einzelnes Aktualisierungstoken ist maximal 14 Tage gültig. Ein Aktualisierungstoken kann jedoch jederzeit aus verschiedenen Gründen ungültig werden. Ihre App sollte weiterhin versuchen, ein Aktualisierungstoken zu verwenden, bis die Anforderung fehlschlägt oder bis es von Ihrer App durch ein neues Aktualisierungstoken ersetzt wird. Ein Aktualisierungstoken kann außerdem ungültig werden, wenn der Benutzer das letzte Mal vor 90 Tagen seine Anmeldeinformationen eingegeben hat. |
| Autorisierungscodes | Fünf Minuten | Autorisierungscodes haben absichtlich eine kurze Gültigkeitsdauer. Sie sollten umgehend für Zugriffs-, ID- oder Aktualisierungstoken eingelöst werden, wenn sie empfangen werden. |

<!---HONumber=AcomDC_0224_2016-->