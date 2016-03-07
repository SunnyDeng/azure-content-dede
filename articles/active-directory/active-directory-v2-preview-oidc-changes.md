<properties
	pageTitle="Änderungen am Azure AD v2.0-Endpunkt | Microsoft Azure"
	description="Enthält eine Beschreibung der Änderungen, die an den Protokollen des App-Modells v2. 0 (öffentliche Vorschau) vorgenommen werden."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Wichtige Updates für die v2.0-Authentifizierungsprotokolle
Entwickler aufgepasst! In den nächsten zwei Wochen werden wir einige Updates an unseren v2.0-Authentifizierungsprotokollen vornehmen. Dies kann zu beeinträchtigenden Änderungen für Apps führen, die Sie während des Vorschauzeitraums geschrieben haben.

## Was Apps sind hiervon betroffen?
Alle Apps, die so erstellt wurden, dass der konvergierte v2.0-Authentifizierungsendpunkt genutzt wird.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Weitere Informationen zum v2.0-Endpunkt finden Sie [hier](active-directory-appmodel-v2-overview.md).

Sie sollten Ihre Projekte testen und bei Bedarf Änderungen vornehmen, wenn Folgendes gilt: Sie haben eine App mit Verwendung des v2.0-Endpunkts erstellt, indem Sie das v2.0-Protokoll direkt im Code genutzt haben, und eine OpenID Connect- oder OAuth-Web-Middleware oder andere Bibliotheken von Drittanbietern zum Durchführen der Authentifizierung verwendet.

## Welche Apps sind nicht betroffen?
Alle Apps, für die bei der Erstellung der Azure AD-Authentifizierungsendpunkt für die Produktion verwendet wurde.

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Dieses Protokoll ist in Stein gemeißelt und weist daher keinerlei Änderungen auf.

Wenn Sie für Ihre App zum Durchführen der Authentifizierung **nur** unsere ADAL-Bibliothek nutzen, sind keine Änderungen erforderlich. Durch ADAL ist Ihre App praktisch vor Änderungen geschützt.

## Welche Änderungen werden durchgeführt?
### Entfernen des Werts „x5t“ aus JWT-Headern
Für den v2.0-Endpunkt werden JWT-Token in hohem Umfang genutzt. Sie enthalten einen Headerparameterabschnitt mit relevanten Metadaten zum Token. Wenn Sie den Header von einem unserer aktuellen JWTs decodieren, ergibt sich ungefähr Folgendes:

```
{ 
	"type": "JWT",
	"alg": "RS256",
	"x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
	"kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Mit der Eigenschaft „x5t“ und „kid“ wird jeweils der öffentliche Schlüssel identifiziert, der zum Überprüfen der Signatur eines Tokens verwendet wird, die vom OpenID Connect-Metadaten-Endpunkt abgerufen wird.

Hier nehmen wir die Änderung vor, dass die Eigenschaft „x5t“ entfernt wird. Sie können weiter die gleichen Mechanismen zum Überprüfen von Token verwenden, aber Sie sollten nur die Eigenschaft „kid“ einsetzen, um den richtigen öffentlichen Schlüssel abzurufen. Dies ist im OpenID Connect-Protokoll entsprechend angegeben.

> [AZURE.IMPORTANT] **Ihre Aufgabe: Stellen Sie sicher, dass Ihre App nicht vom Vorhandensein des Werts „x5t“ abhängig ist.**

### Entfernen von „profile\_info“
Bisher wurde vom v2.0-Endpunkt ein JSON-Objekt mit base64-Codierung in Tokenantworten mit der Bezeichnung `profile_info` zurückgegeben. Beim Anfordern von Zugriffstoken vom v2.0-Endpunkt wurde eine Anforderung an das folgende Ziel gesendet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Die Antwort sieht wie das folgende JSON-Objekt aus: ```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Der Wert `profile_info` enthielt Informationen zum Benutzer, der sich an der App anmeldet: anmeldende Person, Anzeigename, Vorname, Nachname, E-Mail-Adresse, Bezeichner usw. `profile_info` wurde hauptsächlich für das Token-Caching und zu Anzeigezwecken verwendet.

Wir entfernen nun den Wert `profile_info`. Machen Sie sich aber keine Sorgen: Diese Informationen stellen wir für Entwickler weiterhin bereit. Nur der Ort ändert sich etwas. Anstelle von `profile_info` gibt der v2.0-Endpunkt jetzt in jeder Tokenantwort das Element `id_token` zurück:

```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Sie können das Element „id\_token“ decodieren und analysieren, um die gleichen Informationen abzurufen, die Sie über „profile\_info“ erhalten haben. Das Element „id\_token“ ist ein JSON-Webtoken (JWT), und der Inhalt wird über OpenID Connect angegeben. Der hierfür verwendete Code sollte eine hohe Ähnlichkeit aufweisen. Sie müssen lediglich das mittlere Segment (den Hauptteil) des Elements „id\_token“ extrahieren und eine base64-Decodierung dafür durchführen, um auf das darin enthaltene JSON-Objekt zuzugreifen.

Im Laufe der nächsten beiden Wochen sollten Sie Ihre App so codieren, dass die Benutzerinformationen entweder per `id_token` oder `profile_info` abgerufen werden (abhängig von der jeweiligen Verfügbarkeit). Ihre App kann nach dem Vornehmen der Änderung den Übergang von `profile_info` zu `id_token` dann nahtlos ohne Unterbrechung bewältigen.

> [AZURE.IMPORTANT] **Ihre Aufgabe: Stellen Sie sicher, dass Ihre App nicht vom Vorhandensein des Werts `profile_info` abhängig ist.**

### Entfernen von „id\_token\_expires\_in“
Ähnlich wie `profile_info` entfernen wir auch den Parameter `id_token_expires_in` aus Antworten. Bisher hat der v2.0-Endpunkt einen Wert für `id_token_expires_in` zusammen mit jeder id\_token-Antwort zurückgegeben, z. B. in einer Autorisierungsantwort:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Oder in einer Tokenantwort:

```
{ 
	"token_type": "Bearer",
	"id_token_expires_in": 3599,
	"scope": "openid",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Mit dem Wert `id_token_expires_in` wurde in Sekunden angegeben, wie lange das „id\_token“ gültig war. Nun entfernen wir den Wert `id_token_expires_in` vollständig. Stattdessen können Sie die standardmäßigen OpenID Connect-Ansprüche `nbf` und `exp` verwenden, um die Gültigkeit des Elements „id\_token“ zu untersuchen. Weitere Informationen zu diesen Ansprüchen finden Sie in der [v2.0-Tokenreferenz](active-directory-v2-tokens.md).

> [AZURE.IMPORTANT] **Ihre Aufgabe: Stellen Sie sicher, dass Ihre App nicht vom Vorhandensein des Werts `id_token_expires_in` abhängig ist.**


### Ändern der von „scope=openid“ zurückgegebenen Ansprüche
Dies ist die wichtigste Änderung. Sie wirkt sich auf fast alle Apps aus, für die der v2.0-Endpunkt verwendet wird. Viele Anwendungen senden Anforderungen an den v2.0-Endpunkt, indem sie den Bereich `openid` verwenden. Beispiel:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Wenn der Benutzer heutzutage seine Zustimmung zum Bereich `openid` gibt, erhält Ihre App im sich ergebenden „id\_token“ viele Informationen zum Benutzer. Diese Ansprüche können Name, bevorzugten Benutzernamen, E-Mail-Adresse, Objekt-ID und mehr umfassen.

Bei diesem Update ändern wir die Informationen, auf die für Ihre App über den Bereich `openid` Zugriff besteht, um eine bessere Einhaltung der OpenID Connect-Spezifikation zu erzielen. Der Bereich `openid` erlaubt Ihrer App nur das Anmelden des Benutzers und das Erhalten eines App-spezifischen Bezeichners im Anspruch `sub` des Elements „id\_token“. Die Ansprüche in einem „id\_token“, für das nur der Bereich `openid` gewährt wird, weisen keine personenbezogenen Informationen auf. Beispiele für „id\_token“-Ansprüche:

```
{ 
	"aud": "580e250c-8f26-49d0-bee8-1c078add1609",
	"iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
	"iat": 1449520283,
	"nbf": 1449520283,
	"exp": 1449524183,
	"nonce": "12345",
	"sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
	"tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
	"ver": "2.0",
}
```

Falls Sie personenbezogene Informationen (Personally Identifiable Information, PII) über den Benutzer in Ihrer App erhalten möchten, müssen Sie über Ihre App zusätzliche Berechtigungen vom Benutzer einholen. Wir führen die Unterstützung für zwei neue Bereiche aus der OpenID Connect-Spezifikation ein, mit denen dies möglich ist: `email` und `profile`.

- Der Bereich `email` ist sehr einfach aufgebaut. Ihre App hat damit über den Anspruch `email` im „id\_token“ Zugriff auf die primäre E-Mail-Adresse des Benutzers. Beachten Sie, dass der Anspruch `email` im Element „id\_token“ nicht immer vorhanden ist. Er wird nur eingefügt, wenn er im Profil des Benutzers verfügbar ist.
- Der Bereich `profile` ermöglicht Ihrer App den Zugriff auf alle anderen grundlegenden Informationen zum Benutzer: Name, bevorzugter Benutzername, Objekt-ID usw.

Hiermit können Sie für Ihre App den Weg der minimalen Offenlegung einschlagen. Sie können Benutzer nur um die Daten bitten, die für die Funktionsweise der App erforderlich sind. Falls Sie weiterhin den gesamten Satz an Benutzerinformationen erhalten möchten, der mit Ihrer App derzeit abgerufen wird, sollten Sie alle drei Bereiche in Ihre Autorisierungsanforderungen einbinden:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Ihre App kann sofort mit dem Senden der Bereiche `email` und `profile` beginnen. Der v2.0-Endpunkt akzeptiert diese beiden Bereiche und beginnt damit, je nach Bedarf Berechtigungen von Benutzern anzufordern. Die Änderung der Interpretation des Bereichs `openid` wird aber erst in einigen Wochen wirksam.

> [AZURE.IMPORTANT] **Ihre Aufgabe: Fügen Sie die Bereiche `profile` und `email` hinzu, falls Ihre App Informationen zum Benutzer benötigt.** Beachten Sie, dass beide Berechtigungen bei der ADAL standardmäßig in Anforderungen integriert sind.

### Entfernen des nachgestellten Schrägstrichs für Aussteller
Bisher hatte der Ausstellerwert, der in Token vom v2.0-Endpunkt angezeigt wird, folgende Form:

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Die GUID war die tenantId des Azure AD-Mandanten, von dem das Token ausgestellt wurde. Mit diesen Änderungen lautet der Ausstellerwert wie folgt:

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

(in beiden Token und im OpenID Connect Discovery-Dokument)

> [AZURE.IMPORTANT] **Ihre Aufgabe: Stellen Sie sicher, dass Ihre App den Ausstellerwert bei der Überprüfung des Ausstellers sowohl mit als auch ohne nachgestellten Schrägstrich akzeptiert.**

## Was ist der Grund für diese Änderungen?
Der Hauptgrund der Einführung dieser Änderungen ist die Einhaltung der Vorgaben aus der OpenID Connect-Standardspezifikation. Wir hoffen, durch die Sicherstellung der OpenID Connect-Kompatibilität die Differenzen zwischen der Integration in Microsoft-Identitätsdienste und andere Identitätsdienste der Branche zu reduzieren. Wir möchten es Entwicklern ermöglichen, ihre bevorzugten Open Source-Authentifizierungsbibliotheken zu verwenden, ohne dass die Bibliotheken aufgrund von Differenzen zum Microsoft-Ansatz geändert werden müssen.

## Was können Sie tun?
Sie können heute damit beginnen, alle oben beschriebenen Änderungen vorzunehmen. Diese Schritte sollten Sie sofort ausführen:

1.	**Entfernen Sie alle Abhängigkeiten des Headerparameters `x5t`.**
2.	**Führen Sie den Übergang von `profile_info` zu `id_token` in Tokenantworten wie beschrieben durch.**
3.  **Entfernen Sie alle Abhängigkeiten des Antwortparameters `id_token_expires_in`.**
3.	**Fügen Sie Ihrer App die Bereiche `profile` und `email` hinzu, falls für Ihre App grundlegende Benutzerinformationen erforderlich sind.**
4.	**Sorgen Sie dafür, dass Ausstellerwerte in Token mit und ohne nachgestellten Schrägstrich akzeptiert werden.**

Unsere [Dokumentation zum v2.0-Protokoll](active-directory-v2-protocols.md) wurde bereits aktualisiert, um diese Änderungen widerzuspiegeln. Sie können sie also als Referenz beim Aktualisieren Ihres Codes verwenden.

Falls Sie weitere Fragen zum Umfang der Änderungen haben, erreichen Sie uns bei Twitter unter „@AzureAD“.

## Wie häufig werden Protokolländerungen durchgeführt?
Im Moment stehen keine weiteren beeinträchtigenden Änderungen von Authentifizierungsprotokollen an. Wir fassen diese Änderungen bewusst unter einer Version zusammen, damit Sie diese Art von Aktualisierungsprozess nicht in Kürze noch einmal durchführen müssen. Natürlich werden wir dem konvergierten v2.0-Authentifizierungsdienst auch weiterhin Features hinzufügen, die Sie nutzen können. Diese Änderungen sind voraussichtlich aber ergänzender Art und sollten nicht zu einer Beeinträchtigung des Codes führen.

Abschließend möchten wir Ihnen dafür danken, dass Sie während der Vorschauphase viele verschiedene Dinge ausprobiert haben. Die Erkenntnisse und Erfahrungen unserer „Early Adopters“ sind schon jetzt sehr nützlich, und wir hoffen, dass Sie uns Ihre Meinungen und Ideen auch weiterhin mitteilen.

Viel Spaß beim Programmieren!

Microsoft Identity Division

<!---HONumber=AcomDC_0224_2016-->