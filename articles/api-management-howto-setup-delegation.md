<properties 
	pageTitle="Delegieren von Benutzerregistrierung und Produktabonnierung" 
	description="Erfahren Sie, wie Sie die Benutzerregistrierung und Produktabonnements an einen Dritten in Azure API Management delegieren." 
	services="api-management" 
	documentationCenter="" 
	authors="antonba" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/14/2015" 
	ms.author="antonba"/>

# Delegieren von Benutzerregistrierung und Produktabonnierung

Mit der Delegierung können Sie Anmeldung, Registrierung und Produktabonnierung von Entwicklern mit Ihrer vorhandenen Website umsetzen, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. Auf diese Weise besitzt die Website die Benutzerdaten und kann die Prüfung dieser Schritte auf selbst definierte Weise durchführen.


## In diesem Thema

-   [Delegieren von Anmeldung und Registrierung für Entwickler][]
-   [Delegieren der Produktabonnierung][]

## <a name="delegate-signin-up"> </a>Delegieren von Anmeldung und Registrierung für Entwickler

Um die Anmeldung und Registrierung für Entwickler in Ihrer vorhandenen Website zu delegieren, müssen Sie einen speziellen Delegierungsendpunkt in Ihrer Website als Einstiegspunkt für diese aus dem Entwicklerportal für die API-Verwaltung gestellten Anfragen einrichten.

Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im Entwicklerportal für die API-Verwaltung
2. Der Browser wird zum Delegierungsendpunkt weitergeleitet
3. Der Delegierungsendpunkt leitet den Benutzer wiederum weiter oder zeigt eine Benutzeroberfläche für die Anmeldung bzw. Registrierung an
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im Entwicklerportal für die API-Verwaltung weitergeleitet, von der er ursprünglich gekommen ist


Richten Sie zunächst die Weiterleitung von Anfragen über Ihren Delegierungsendpunkt in der API-Verwaltung ein. Klicken Sie im Publisherportal der API-Verwaltung auf **Sicherheit** und dann auf die Registerkarte **Delegierung**. Klicken Sie auf das Kontrollkästchen zum Aktivieren von der Delegate-Anmeldung und -Registrierung.

![Delegation page][api-management-delegation-signin-up]

* Legen Sie die URL für Ihren speziellen Delegierungsendpunkt fest und geben Sie sie in das Feld **Delegierungsendpunkt-URL** ein. .

* Geben Sie im Feld **Schlüssel für delegierte Authentifizierung** einen geheimen Schlüssel ein. Dieser wird zur Erstellung einer Signatur verwendet, mit der sichergestellt wird, dass die Anfrage tatsächlich von der Azure API-Verwaltung stammt. Sie können auf die Schaltfläche **Generieren** klicken, damit die API-Verwaltung nach dem Zufallsprinzip ein Schlüssel für Sie generiert.

Anschließend müssen Sie den **Delegierungsendpunkt** erstellen. Dieser Endpunkt erfüllt mehrere Aufgaben:

1. Empfang einer Anforderung in der folgenden Form:

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sid={string}*

	Abfrageparameter für Anmeldung und Registrierung:
	- **Operation**: Gibt den Typ der Delegierungsanforderung an. In diesem Fall ist nur "SignIn" zulässig
	- **returnUrl**: die URL der Herkunftsseite, auf der der Benutzer auf einen Anmeldungs- oder Registrierungslink geklickt hat
	- **salt**: eine spezielle Salt-Zeichenfolge, mit der ein Sicherheits-Hash generiert wird
	- **sig**: ein generierter Sicherheits-Hash, den Sie mit Ihrem eigenen generierten Hash vergleichen können

2. Überprüfen, ob die Anfrage von der Azure API-Verwaltung stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

	* Generieren Sie einen HMAC-SHA512-Hash einer Zeichenfolge basierend auf den Abfrageparametern **returnUrl** und **salt** ([Beispielcode finden Sie unter]):
        > HMAC(**salt** + '\n' + **returnUrl**)
		 
	* Vergleichen Sie den generierten Hash mit dem Wert des **sig**-Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.

2. Prüfen Sie, ob es sich um eine Anmeldungs- oder Registrierungsanfrage handelt: Der **operation**-Abfrageparameter wird auf den Wert **SignIn** festgelegt.

3. Anzeigen der Benutzeroberfläche für Anmeldung oder Registrierung

4. Falls sich der Benutzer registriert, müssen Sie das entsprechende Konto in der API-Verwaltung erstellen. [Benutzer erstellen] mit der REST-API für die API-Verwaltung. Achten Sie dabei darauf, dass Sie die Benutzer-ID auf den Wert aus Ihrem Benutzerspeicher oder auf einen anderen für Sie nachverfolgbaren Wert festlegen.

5. Nach erfolgreicher Authentifizierung des Benutzers:

	* [Anfordern eines Tokens für einmaliges Anmelden (SSO)] mit der REST-API für die API-Verwaltung

	* Hängen Sie einen returnUrl-Abfrageparameter an die SSO-URL an, die Sie aus dem obigen API-Aufruf erhalten haben:
		> z. B. https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

	* Leiten Sie den Benutzer zu der oben generierten URL weiter


## <a name="delegate-product-subscription"> </a>Delegieren der Produktabonnierung

Die Delegierung der Produktabonnierung funktioniert gleich wie die Delegierung der Anmeldung und Registrierung. Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler wählt ein Produkt im Entwicklerportal für die API-Verwaltung aus und klickt auf die Schaltfläche "Abonnieren"
2. Der Browser wird zum Delegierungsendpunkt weitergeleitet
3. Der Delegierungsendpunkt führt die erforderlichen Schritte für die Produktabonnierung aus. Dies ist frei konfigurierbar und kann z. B. eine Weiterleitung auf eine Seite zur Eingabe von Bezahlungsdaten oder zusätzlichen Fragen beinhalten, oder einfach die Speicherung der Daten ohne weiteres Eingreifen des Benutzers.
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im Entwicklerportal für die API-Verwaltung weitergeleitet, von der er ursprünglich gekommen ist

Klicken Sie auf der Seite **Delegierung** auf **Produktabonnierung delegieren**, um diese Funktion zu aktivieren.

Konfigurieren Sie anschließend die folgenden Aktionen für den Delegierungsendpunkt:


1. Empfang einer Anforderung in der folgenden Form:

	> *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sid={string}*

	Abfrageparameter für die Produktabonnierung:
	- **Operation**: Gibt den Typ der Delegierungsanforderung an. Für Produktabonnierungsanfragen sind die folgenden Optionen gültig:
		- "Subscribe": Abonnierung eines Produkts mit der angegebenen ID (siehe unten) für den Benutzer
		- "Unsubscribe": Anfrag eines Benutzers zum Beenden des Abonnements eines Produkts
		- "Renew": Benutzer möchte ein Abonnement erneuern (weil dieses z. B. demnächst abläuft)
	- **productId**: die ID des Produkts, das der Benutzer abonnieren möchte
	- **userId**: die ID des Benutzers, der die Anforderung gestellt hat
	- **salt**: eine spezielle Salt-Zeichenfolge, mit der ein Sicherheits-Hash generiert wird
	- **sig**: ein generierter Sicherheits-Hash, den Sie mit Ihrem eigenen generierten Hash vergleichen können


2. Überprüfen, ob die Anfrage von der Azure API-Verwaltung stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

	* Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den Abfrageparametern **productId**, **userId** und **salt**:
		> HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
		 
	* Vergleichen Sie den generierten Hash mit dem Wert des **sig**-Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.
	
3. Führen Sie Ihre für die Produktabonnierung erforderlichen Schritte anhand der im Parameter **operation** angeforderten Operation aus, z. B. Abrechnung, weitere Fragen usw.

4. Nachdem der Benutzer das Produkt auf Ihrer Seite erfolgreich abonniert hat, abonnieren Sie das Produkt in der API-Verwaltung für den Benutzer, indem Sie die [REST-API für die Produktabonnierung] aufrufen.

5. Leiten Sie den Benutzer zu der im Parameter **returnUrl** der Anfrage angegebenen URL weiter.

## <a name="delegate-example-code"> </a> Beispielcode ##

Diese Codebeispiele zeigen, wie Sie den *Schlüssel für die Delegierungsvalidierung*, der auf dem Delegierungsbildschirm im API-Verwaltungsportal festgelegt wird, zum Erstellen einer HMAC verwenden, der dann verwendet wird, um die Signatur zu überprüfen und die Gültigkeit der übergebenen "returnUrl" nachzuweisen. Der gleiche Code funktioniert für "productId" und "userID" mit geringfügigen Änderungen.

**C#-Code zum Generieren des Hashes von "returnUrl"**

	using System.Security.Cryptography;

	string key = "delegation validation key";
	string returnUrl = "returnUrl query parameter";
	string salt = "salt query parameter";
	string signature;
	using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
	{
		signature = encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl));
		// change to (salt + "\n" + productId + "\n" + userId) for point 2 above
	}

**NodeJS-Code zum Generieren des Hashes von "returnUrl"**

	var crypto = require('crypto');
	
	var key = 'delegation validation key'; 
	var returnUrl = 'returnUrl query parameter';
	var salt = 'salt query parameter';
	
	var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
	var digest = hmac.update(salt + '\n' + returnUrl).digest();
	// change to (salt + '\n' + productId + '\n' + userId) for point 2 above
	
	var signature = digest.toString('base64');

[Delegieren von Anmeldung und Registrierung für Entwickler]: #delegate-signin-up
[Delegieren der Produktabonnierung]: #delegate-product-subscription
[Anfordern eines SSO-Tokens (Single-Sign-On)]: http://go.microsoft.com/fwlink/?LinkId=507409
[Erstellen eines Benutzers]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[Aufrufen der REST-API für Produktabonnement]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Nächste Schritte]: #next-steps
[Beispielcode weiter unten]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png

<!--HONumber=46--> 
