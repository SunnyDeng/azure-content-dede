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
	ms.date="06/10/2015" 
	ms.author="antonba"/>

# Delegieren von Benutzerregistrierung und Produktabonnierung

Mit der Delegierung können Sie Anmeldung, Registrierung und Produktabonnierung von Entwicklern mit Ihrer vorhandenen Website umsetzen, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. Auf diese Weise besitzt die Website die Benutzerdaten und kann die Prüfung dieser Schritte auf selbst definierte Weise durchführen.

Weitere Informationen zu Delegierung finden Sie im folgende Video.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

## <a name="delegate-signin-up"> </a>Delegieren der Developer-Anmeldung und Registrierung

Um die Anmeldung und Registrierung für Entwickler in Ihrer vorhandenen Website zu delegieren, müssen Sie einen speziellen Delegierungsendpunkt in Ihrer Website als Einstiegspunkt für diese aus dem Entwicklerportal für die API-Verwaltung gestellten Anfragen einrichten.

Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im Entwicklerportal für die API-Verwaltung
2. Der Browser wird zum Delegierungsendpunkt weitergeleitet
3. Der Delegierungsendpunkt leitet den Benutzer wiederum weiter oder zeigt eine Benutzeroberfläche für die Anmeldung bzw. Registrierung an
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im Entwicklerportal für die API-Verwaltung weitergeleitet, von der er ursprünglich gekommen ist


Richten Sie zunächst die Weiterleitung von Anfragen über Ihren Delegierungsendpunkt in der API-Verwaltung ein. Klicken Sie im Publisher-API Management-Portal auf **Security** und klicken Sie dann auf die **Delegierung** Registerkarte. Klicken Sie auf das Kontrollkästchen zum Aktivieren von der Delegate-Anmeldung und -Registrierung.

![Delegierungsseite][api-management-delegation-signin-up]

* Legen Sie die URL für Ihren speziellen Delegierungsendpunkt fest und geben Sie sie in das Feld **Delegierungsendpunkt-URL** ein. 

* Geben Sie im Feld **Schlüssel für delegierte Authentifizierung** einen geheimen Schlüssel ein. Dieser wird zur Erstellung einer Signatur verwendet, mit der sichergestellt wird, dass die Anfrage tatsächlich von der Azure API-Verwaltung stammt. Klicken Sie auf der **generieren** Schaltfläche API Managemnet nach dem Zufallsprinzip ein Schlüssel für Sie generiert haben.

Anschließend müssen Sie den **Delegierungsendpunkt** einrichten. Dieser Endpunkt erfüllt mehrere Aufgaben:

1. Empfang einer Anforderung in der folgenden Form:

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL der Quellseite} & salt = {String} & Sig = {String}*

	Abfrageparameter für den Fall Anmelde- / Anmeldung:- **Vorgang**: bezeichnet die Art der Anforderung der Delegierung ist - er ist nur möglich **Anmeldung** in diesem Fall - **ReturnUrl**: die URL der Seite, in dem der Benutzer geklickt, auf einen Link anmelden oder Anmeldung hat - **salt**: eine spezielle Saltzeichenfolge zur Berechnung einer Sicherheits Hash - **Sig**: ein berechnete Security-Hash für den Vergleich mit Ihren eigenen verwendet werden berechnete Hash

2. Überprüfen, ob die Anfrage von der Azure API-Verwaltung stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

	* Berechnen einen HMAC-SHA512-Hashwert einer Zeichenfolge auf Grundlage der **ReturnUrl** und **salt** Abfrageparameter ([unten dargestellten Beispielcode]):
        > **returnUrl**
		 
	* Vergleichen Sie den generierten Hash mit dem Wert des **sig**Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.

2. Stellen Sie sicher, dass Sie eine Anforderung für in Anmeldung/Anmeldung empfangen: die **Vorgang** Query-Parameter werden auf den "** SignIn **".

3. Anzeigen der Benutzeroberfläche für Anmeldung oder Registrierung

4. Falls sich der Benutzer registriert, müssen Sie das entsprechende Konto in der API-Verwaltung erstellen. [Benutzer erstellen] mit der REST-API für die API-Verwaltung. Achten Sie dabei darauf, dass Sie die Benutzer-ID auf den Wert aus Ihrem Benutzerspeicher oder auf einen anderen für Sie nachverfolgbaren Wert setzen.

5. Nach erfolgreicher Authentifizierung des Benutzers:

	* [Anfordern eines Tokens für einmaliges Anmelden (SSO)] mit der REST-API für die API-Verwaltung

	* Hängen Sie einen returnUrl-Abfrageparameter an die SSO-URL an, die Sie aus dem obigen API-Aufruf erhalten haben:
		> z. B. https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url

	* Leiten Sie den Benutzer zu der oben generierten URL weiter

Zusätzlich zu den **Anmeldung** Sie können auch Vorgang Kontenverwaltung durch die vorherigen Schritte ausführen und dann eine der folgenden Vorgänge.

-	**Kennwort ändern**
-	**ChangeProfile**
-	**CloseAccount**

Sie müssen die folgenden Abfrageparameter für Konto Verwaltungsvorgänge übergeben.

-	**Vorgang**: bezeichnet die Art der Delegierung-Anforderung ist (ChangePassword, ChangeProfile oder CloseAccount)
-	**Benutzer-ID**: die Benutzer-Id des Kontos verwalten
-	**salt**: eine besondere salt-Zeichenfolge, die zur Berechnung des Hash Sicherheit
-	**Sig**: ein berechnete Security-Hash für den Vergleich mit Ihren eigenen verwendet werden berechnete Hash

## <a name="delegate-product-subscription"> </a>Delegieren der Produkt-Abonnements

Die Delegierung der Produktabonnierung funktioniert gleich wie die Delegierung der Anmeldung und Registrierung. Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler wählt ein Produkt im Entwicklerportal für die API-Verwaltung aus und klickt auf die Schaltfläche „Abonnieren“
2. Der Browser wird zum Delegierungsendpunkt weitergeleitet
3. Der Delegierungsendpunkt führt die erforderlichen Schritte für die Produktabonnierung aus. Dies ist frei konfigurierbar und kann z. B. eine Weiterleitung auf eine Seite zur Eingabe von Bezahlungsdaten oder zusätzlichen Fragen beinhalten, oder einfach die Speicherung der Daten ohne weiteres Eingreifen des Benutzers.
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im Entwicklerportal für die API-Verwaltung weitergeleitet, von der er ursprünglich gekommen ist

Klicken Sie in der Seite **Delegierung** auf **Produktabonnierung delegieren**, um diese Funktion zu aktivieren.

Konfigurieren Sie anschließend die folgenden Aktionen für den Delegierungsendpunkt:


1. Empfang einer Anforderung in der folgenden Form:

	> *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product zum Abonnieren} & Benutzer-ID = {User antragstellende} & salt = {String} & Sig = {String}*

	Abfrageparameter für den Fall des Produkts Abonnement:- **Vorgang**: identifiziert, welche Art von Delegierung Anforderung ist. Für Produkt Abonnement Anforderungen gültigen Optionen sind:-"Subscribe": eine Anforderung zum Abonnieren des Benutzers auf ein bestimmtes Produkt mit bereitgestellten ID (siehe unten) - "Unsubscribe": eine Anforderung zum Abmelden eines Benutzers von einer Produkt - "Erneuern": ein haben, erneuern ein Abonnements (z. B. die läuft ab) - **ProductId**: die ID des Produkts, die vom Benutzer angeforderte abonnieren - **Benutzer-ID**: die ID des Benutzers, für den die Anforderung erfolgt - **salt**: eine spezielle Saltzeichenfolge zur Berechnung einer Sicherheits Hash - **Sig**: ein berechnete Security-Hash für den Vergleich mit Ihren eigenen verwendet werden berechnete Hash


2. Überprüfen, ob die Anfrage von der Azure API-Verwaltung stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

	* Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den Abfrageparametern **productId**, **redirectUrl** und **salt**:
		> **productId****Benutzer-ID**
		 
	* Vergleichen Sie den generierten Hash mit dem Wert des **sig**Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.
	
3. Führen Sie Ihre für die Produktabonnierung erforderlichen Schritte anhand der im Parameter **operation** angeforderten Operation aus, z. B. Abrechnung, weitere Fragen usw.

4. Nachdem der Benutzer das Produkt auf Ihrer Seite erfolgreich abonniert hat, abonnieren Sie das Produkt in der API-Verwaltung für den Benutzer, indem Sie die [REST-API für die Produktabonnierung] aufrufen.

5. Der Benutzer zurück zum Umleiten der **ReturnUrl** bereitgestellt, wenn die Anforderung empfängt.

## <a name="delegate-example-code"> </a> Beispielcode ##

Diese Codebeispiele zeigen, wie Sie die *Schlüssel für die Überprüfung von Delegierung*, die auf dem Bildschirm Delegierung von API-Verwaltungsportal festgelegt ist, erstellen Sie einen HMAC die dann verwendet wird, um die Signatur zu überprüfen die Gültigkeit der übergebenen ReturnUrl nachweisen. Der gleiche Code funktioniert für "productId" und "userID" mit geringfügigen Änderungen.

**C#-Code zum Generieren des Hashwerts der returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**NodeJS Code ReturnUrl-Hash generiert.**

	var crypto = require('crypto');
	
	var key = 'delegation validation key'; 
	var returnUrl = 'returnUrl query parameter';
	var salt = 'salt query parameter';
	
	var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
	var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
	
	var signature = digest.toString('base64');

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Anfordern eines Tokens für einmaliges Anmelden (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[Benutzer erstellen]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[REST-API für die Produktabonnierung]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[unten dargestellten Beispielcode]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png

<!---HONumber=GIT-SubDir_Tue_AM_dede-->