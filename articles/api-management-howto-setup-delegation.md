<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba"></tags>

# Delegieren von Benutzerregistrierung und Produktabonnierung

Mit der Delegierung können Sie Anmeldung, Registrierung und Produktabonnierung von Entwicklern mit Ihrer vorhandenen Website umsetzen, anstatt die integrierte Funktion im Entwicklerportal zu verwenden. Auf diese Weise besitzt die Website die Benutzerdaten und kann die Prüfung dieser Schritte auf selbst definierte Weise durchführen.

## In diesem Thema

-   [Delegieren von Anmeldung und Registrierung für Entwickler][Delegieren von Anmeldung und Registrierung für Entwickler]
-   [Delegieren der Produktabonnierung][Delegieren der Produktabonnierung]

## <a name="delegate-signin-up"> </a>Delegieren von Anmeldung und Registrierung für Entwickler

Um die Anmeldung und Registrierung für Entwickler in Ihrer vorhandenen Website zu delegieren, müssen Sie einen speziellen Delegierungsendpunkt in Ihrer Website als Einstiegspunkt für diese aus dem Entwicklerportal für die API-Verwaltung gestellten Anfragen einrichten.

Der komplette Workflow sieht wie folgt aus:

1.  Ein Entwickler klickt auf den Anmeldungs- oder Registrierungslink im Entwicklerportal für die API-Verwaltung
2.  Der Browser wird zum Delegierungsendpunkt weitergeleitet
3.  Der Delegierungsendpunkt leitet den Benutzer wiederum weiter oder zeigt eine Benutzeroberfläche für die Anmeldung bzw. Registrierung an
4.  Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im Entwicklerportal für die API-Verwaltung weitergeleitet, von der er ursprünglich gekommen ist

Richten Sie zunächst die Weiterleitung von Anfragen über Ihren Delegierungsendpunkt in der API-Verwaltung ein. Klicken Sie im Veröffentlichungsportal für die API-Verwaltung unter dem Eintrag **Entwicklerportal** im Menü auf der linken Seite auf **Delegierung** und anschließend auf **Anmeldung und Registrierung delegieren**.

![Delegierungsseite][Delegierungsseite]

-   Legen Sie die URL für Ihren speziellen Delegierungsendpunkt fest und geben Sie sie in das Feld **Delegierungsendpunkt-URL** ein.

-   Geben Sie im Feld **Schlüssel für delegierte Authentifizierung** einen geheimen Schlüssel ein. Dieser wird zur Erstellung einer Signatur verwendet, mit der sichergestellt wird, dass die Anfrage tatsächlich von der Azure API-Verwaltung stammt.

Anschließend müssen Sie den **Delegierungsendpunkt** einrichten. Dieser Endpunkt erfüllt mehrere Aufgaben:

1.  Empfang einer Anforderung in der folgenden Form:

    > *[http://www.IhreWebseite.com/apimdelegation?operation=SignIn&redirectUrl][http://www.IhreWebseite.com/apimdelegation?operation=SignIn&redirectUrl]={URL der Herkunftsseite}&salt={Zeichenfolge}&sid={Zeichenfolge}*

    Abfrageparameter für Anmeldung und Registrierung:

    -   **operation**: Gibt den Typ der Delegierungsanforderung an. In diesem Fall ist nur „SignIn“ zulässig
    -   **redirectUrl**: Die URL der Herkunftsseite, in der der Benutzer auf einen Anmeldungs- oder Registrierungslink geklickt hat
    -   **salt**: Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheits-Hash generiert wird
    -   **sig**: Ein generierter Sicherheits-Hash, den Sie mit Ihrem eigenen generierten Hash vergleichen können

2.  Überprüfen, ob die Anfrage von der Azure API-Verwaltung stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

    -   Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den **redirectUrl**- und **salt**-Abfrageparametern:

        > HMAC(**redirectUrl** + '\\n' + **salt**)

    -   Vergleichen Sie den generierten Hash mit dem Wert des **sig**Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.

3.  Prüfen Sie, ob es sich um eine Anmeldungs- oder Registrierungsanfrage handelt: Der **operation**-Abfrageparameter hat den Wert **SignIn**.

4.  Anzeigen der Benutzeroberfläche für Anmeldung oder Registrierung

5.  Falls sich der Benutzer registriert, müssen Sie das entsprechende Konto in der API-Verwaltung erstellen. [Benutzer erstellen][Benutzer erstellen] mit der REST-API für die API-Verwaltung. Achten Sie dabei darauf, dass Sie die Benutzer-ID auf den Wert aus Ihrem Benutzerspeicher oder auf einen anderen für Sie nachverfolgbaren Wert setzen.

6.  Nach erfolgreicher Authentifizierung des Benutzers:

    -   [Anfordern eines Tokens für einmaliges Anmelden (SSO)][Anfordern eines Tokens für einmaliges Anmelden (SSO)] mit der REST-API für die API-Verwaltung

    -   Hängen Sie einen returnUrl-Abfrageparameter an die SSO-URL an, die Sie aus dem obigen API-Aufruf erhalten haben:

        > z. B. <https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url>

    -   Leiten Sie den Benutzer zu der oben generierten URL weiter

## <a name="delegate-product-subscription"> </a>Delegieren der Produktabonnierung

Die Delegierung der Produktabonnierung funktioniert gleich wie die Delegierung der Anmeldung und Registrierung. Der komplette Workflow sieht wie folgt aus:

1.  Ein Entwickler wählt ein Produkt im Entwicklerportal für die API-Verwaltung aus und klickt auf die Schaltfläche „Abonnieren“
2.  Der Browser wird zum Delegierungsendpunkt weitergeleitet
3.  Der Delegierungsendpunkt führt die erforderlichen Schritte für die Produktabonnierung aus. Dies ist frei konfigurierbar und kann z. B. eine Weiterleitung auf eine Seite zur Eingabe von Bezahlungsdaten oder zusätzlichen Fragen beinhalten, oder einfach die Speicherung der Daten ohne weiteres Eingreifen des Benutzers.
4.  Im Erfolgsfall wird der Benutzer anschließend wieder auf die Seite im Entwicklerportal für die API-Verwaltung weitergeleitet, von der er ursprünglich gekommen ist

Klicken Sie in der Seite **Delegierung** auf **Produktabonnierung delegieren**, um diese Funktion zu aktivieren.

Konfigurieren Sie anschließend die folgenden Aktionen für den Delegierungsendpunkt:

1.  Empfang einer Anforderung in der folgenden Form:

    > *[http://www.IhreWebseite.com/apimdelegation?operation][http://www.IhreWebseite.com/apimdelegation?operation]={Operation}&productId={zu abonnierendes Produkt}&userId={Benutzer}&salt={Zeichenfolge}&sid={Zeichenfolge}*

    Abfrageparameter für die Produktabonnierung:

    -   **Operation**: Gibt den Typ der Delegierungsanforderung an. Für Produktabonnierungsanfragen sind die folgenden Optionen gültig:

        -   „Subscribe“: Abonnierung eines Produkts mit der angegebenen ID (siehe unten) für den Benutzer
        -   „Unsubscribe“: Benutzers beendet das Abonnement eines Produkts
        -   „Renew“: Benutzer möchte ein Abonnement erneuern (weil dieses z. B. demnächst abläuft)
    -   **productId**: Die ID des Produkts, das der Benutzer abonnieren möchte
    -   **userId**: Die ID des Benutzers, der die Anforderung gestellt hat
    -   **salt**: Eine spezielle Salt-Zeichenfolge, mit der ein Sicherheits-Hash generiert wird
    -   **sig**: Ein generierter Sicherheits-Hash, den Sie mit Ihrem eigenen generierten Hash vergleichen können

2.  Überprüfen, ob die Anfrage von der Azure API-Verwaltung stammt (optional, aus Sicherheitsgründen jedoch dringend empfohlen)

    -   Generieren Sie einen HMAC-SHA512-Hash für eine Zeichenfolge aus den Abfrageparametern **productId**, **redirectUrl** und **salt**:

        > HMAC(**productId** + '\\n' + **userId** + '\\n' + **salt**)

    -   Vergleichen Sie den generierten Hash mit dem Wert des **sig**Abfrageparameters. Fahren Sie mit dem nächsten Schritt fort, wenn die Hashes übereinstimmen. Lehnen Sie die Anfrage andernfalls ab.

3.  Führen Sie Ihre für die Produktabonnierung erforderlichen Schritte anhand der im Parameter **operation** angeforderten Operation aus, z. B. Abrechnung, weitere Fragen usw.

4.  Nachdem der Benutzer das Produkt auf Ihrer Seite erfolgreich abonniert hat, abonnieren Sie das Produkt in der API-Verwaltung für den Benutzer, indem Sie die [REST-API für die Produktabonnierung][REST-API für die Produktabonnierung] aufrufen.

5.  Leiten Sie den Benutzer zu der im Parameter **redirectUrl** der Anfrage angegebenen URL weiter.

  [Delegieren von Anmeldung und Registrierung für Entwickler]: #delegate-signin-up
  [Delegieren der Produktabonnierung]: #delegate-product-subscription
  [Delegierungsseite]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
  [http://www.IhreWebseite.com/apimdelegation?operation=SignIn&redirectUrl]: http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl
  [Benutzer erstellen]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
  [Anfordern eines Tokens für einmaliges Anmelden (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
  [http://www.IhreWebseite.com/apimdelegation?operation]: http://www.yourwebsite.com/apimdelegation?operation
  [REST-API für die Produktabonnierung]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
