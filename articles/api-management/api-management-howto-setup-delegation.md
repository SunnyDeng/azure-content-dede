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
	ms.date="06/18/2015" 
	ms.author="antonba"/>

# Delegieren von Benutzerregistrierung und Produktabonnierung

Mit der Delegierung können Sie Anmeldung, Registrierung und Produktabonnierung von Entwicklern mit Ihrer vorhandenen Website umsetzen, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. Auf diese Weise besitzt die Website die Benutzerdaten und kann die Prüfung dieser Schritte auf selbst definierte Weise durchführen.

## <a name="delegate-signin-up"> </a>Delegieren von Anmeldung und Registrierung für Entwickler

Um die Anmeldung und Registrierung für Entwickler in Ihrer vorhandenen Website zu delegieren, müssen Sie einen speziellen Delegierungsendpunkt in Ihrer Website als Einstiegspunkt für diese aus dem API Management-Entwicklerportal gestellten Anfragen einrichten.

Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im API Management-Entwicklerportal.
2. Der Browser wird zum Delegierungsendpunkt umgeleitet.
3. Der Delegierungsendpunkt wiederum leitet den Benutzer um oder zeigt eine Benutzeroberfläche für die Anmeldung bzw. Registrierung an.
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im API Management-Entwicklerportal umgeleitet, von der er ursprünglich gekommen ist.


Richten Sie zunächst in API Management die Weiterleitung von Anfragen über Ihren Delegierungsendpunkt ein. Klicken Sie im API Management-Herausgeberportal auf **Sicherheit** und dann auf die Registerkarte **Delegierung**. Klicken Sie auf das Kontrollkästchen zum Aktivieren von der Delegate-Anmeldung und -Registrierung.

![Delegierungsseite][api-management-delegation-signin-up]

* Legen Sie die URL für Ihren speziellen Delegierungsendpunkt fest und geben Sie sie in das Feld **Delegierungsendpunkt-URL** ein. 

* Geben Sie im Feld **Schlüssel für delegierte Authentifizierung** einen geheimen Schlüssel ein. Dieser wird zur Erstellung einer Signatur verwendet, mit der sichergestellt wird, dass die Anfrage tatsächlich von Azure API Management stammt. Sie können auf die Schaltfläche **Generieren** klicken, damit API Management nach dem Zufallsprinzip einen Schlüssel für Sie generiert.

Anschließend müssen Sie den **Delegierungsendpunkt** einrichten. Dieser Endpunkt erfüllt mehrere Aufgaben:

1. Empfang einer Anforderung in der folgenden Form:

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL der Quellseite}&salt={string}&sig={string}*

	Abfrageparameter für Anmeldung/Registrierung: – **operation**: Gibt an, um welche Art von Delegierungsanforderung es sich handelt; in diesem Fall ist nur **SignIn** möglich – **returnUrl**: Die URL der Seite, auf der der Benutzer auf einen Link für Anmeldung oder Registrierung geklickt hat – **salt**: Eine spezielle salt-Zeichenfolge, mit der ein Sicherheitshash berechnet wird – **sig**: Ein berechneter Sicherheitshash zum Vergleich mit dem von Ihnen generierten Hash

2. Überprüfen, ob die Anfrage von Azure API Management stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

	* Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge basierend auf den Abfrageparametern **returnUrl** und **salt** ([Beispielcode finden Sie unter]):
        > **returnUrl**
		 
	* Vergleichen Sie den generierten Hash mit dem Wert des **sig**-Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.

2. Überprüfen Sie, ob Sie eine Anfrage zur Anmeldung/Registrierung erhalten haben: Der **operation**-Abfrageparameter wird auf "SignIn" gesetzt.

3. Anzeigen der Benutzeroberfläche für Anmeldung oder Registrierung

4. Falls sich der Benutzer registriert, müssen Sie das entsprechende Konto in API Management erstellen. [Erstellen Sie einen Benutzer] mit der REST-API für API Management. Achten Sie dabei darauf, dass Sie die Benutzer-ID auf den Wert aus Ihrem Benutzerspeicher oder auf einen anderen für Sie nachverfolgbaren Wert setzen.

5. Nach erfolgreicher Authentifizierung des Benutzers:

	* [Fordern Sie ein Token für die einmalige Anmeldung an (SSO)]; verwenden Sie hierzu die REST-API von API Management

	* Hängen Sie einen returnUrl-Abfrageparameter an die SSO-URL an, die Sie aus dem obigen API-Aufruf erhalten haben:
		> z. B. https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url

	* Leiten Sie den Benutzer an die oben generierte URL um.

Zusätzlich zur **SignIn**-Operation können Sie auch eine Kontoverwaltung durchführen, indem Sie die vorherigen Schritte ausführen und eine der folgenden Operationen auswählen.

-	**ChangePassword**
-	**ChangeProfile**
-	**CloseAccount**

Sie müssen die folgenden Abfrageparameter für Operationen zur Kontoverwaltung übergeben.

-	**operation**: Gibt an, um welche Art von Delegierungsanforderung es sich handelt ("ChangePassword", "ChangeProfile" oder "CloseAccount")
-	**userId**: Die Benutzer-ID des Kontos, das verwaltet werden soll
-	**salt**: Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheitshash generiert wird
-	**sig**: Ein berechneter Sicherheitshash zum Vergleich mit dem von Ihnen generierten Hash

## <a name="delegate-product-subscription"> </a>Delegieren der Produktabonnierung

Die Delegierung der Produktabonnierung funktioniert gleich wie die Delegierung der Anmeldung und Registrierung. Der komplette Workflow sieht wie folgt aus:

1. Ein Entwickler wählt ein Produkt im API Management-Entwicklerportal aus und klickt auf die Schaltfläche "Abonnieren".
2. Der Browser wird zum Delegierungsendpunkt umgeleitet.
3. Der Delegierungsendpunkt führt die erforderlichen Schritte für die Produktabonnierung aus. Dies ist frei konfigurierbar und kann z. B. eine Umleitung auf eine Seite zur Eingabe von Zahlungsdaten oder zusätzlichen Fragen beinhalten, oder einfach die Speicherung der Daten ohne weiteres Eingreifen des Benutzers.
4. Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im API Management-Entwicklerportal umgeleitet, von der er ursprünglich gekommen ist.

Klicken Sie in der Seite **Delegierung** auf **Produktabonnierung delegieren**, um diese Funktion zu aktivieren.

Konfigurieren Sie anschließend die folgenden Aktionen für den Delegierungsendpunkt:


1. Empfang einer Anforderung in der folgenden Form:

	> *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product für Abonnement}&userId={Benutzer, der die Anforderung sendet}&salt={string}&sig={string}*

	Abfrageparameter für das Abonnieren von Produkten: – **operation**: Gibt an, um welche Art von Delegierungsanforderung es sich handelt. Für Anforderungen zur Produktabonnierung stehen die folgenden Optionen zur Verfügung: – "Subscribe": Eine Anforderung, ein Produkt mit der bereitgestellten ID für einen Benutzer zu abonnieren (siehe unten) – "Unsubscribe": Eine Anforderung, das Produktabonnement für einen Benutzer zu kündigen – "Renew": Eine Anforderung zum Verlängern eines Abonnements (wenn dieses z. B. ausläuft) – **productId**: Die ID des Produkts, das ein Benutzer abonnieren möchte – **userId**: Die ID des Benutzers, für den die Anforderung gesendet wird – **salt**: Eine spezielle salt-Zeichenfolge, mit der ein Sicherheitshash berechnet wird – **sig**: Ein berechneter Sicherheitshash zum Vergleich mit dem von Ihnen generierten Hash


2. Überprüfen, ob die Anfrage von Azure API Management stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

	* Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den Abfrageparametern **productId**, **redirectUrl** und **salt**:
		> **productId****userId**
		 
	* Vergleichen Sie den generierten Hash mit dem Wert des **sig**Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.
	
3. Führen Sie Ihre für die Produktabonnierung erforderlichen Schritte anhand der im Parameter **operation** angeforderten Operation aus, z. B. Abrechnung, weitere Fragen usw.

4. Nachdem der Benutzer das Produkt auf Ihrer Seite erfolgreich abonniert hat, abonnieren Sie das Produkt in API Management für den Benutzer, indem Sie die [REST-API für die Produktabonnierung] aufrufen.

5. Leiten Sie den Benutzer wieder an die im Parameter **returnUrl** der Anforderung angegebene URL um.

## <a name="delegate-example-code"> </a>Beispielcode ##

Diese Codebeispiele zeigen, wie Sie mit dem *Überprüfungsschlüssel für die Delegierung*, der im API Management-Portal auf dem Bildschirm für die Delegierung festgelegt wird, einen HMAC erstellen, mit dessen Hilfe anschließend die Signatur überprüft wird, um die Gültigkeit der übergebenen "returnUrl" nachzuweisen. Der gleiche Code funktioniert mit geringfügigen Änderungen auch für "productId" und "userID".

**C#-Code zum Generieren des Hash von "returnUrl"**

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


**NodeJS-Code zum Generieren des Hash von "returnUrl"**

	var crypto = require('crypto');
	
	var key = 'delegation validation key'; 
	var returnUrl = 'returnUrl query parameter';
	var salt = 'salt query parameter';
	
	var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
	var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
	
	var signature = digest.toString('base64');

## Nächste Schritte

Weitere Informationen zum Delegieren finden Sie im folgenden Video.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Fordern Sie ein Token für die einmalige Anmeldung an (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[Erstellen Sie einen Benutzer]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[REST-API für die Produktabonnierung]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[Beispielcode finden Sie unter]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png

<!---HONumber=July15_HO3-->