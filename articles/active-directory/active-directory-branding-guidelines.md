<properties
   pageTitle="Brandingrichtlinien für Anwendungen | Microsoft Azure"
   description="Eine umfassende Anleitung zu entwicklerorientierten Ressourcen für Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin"/>


# Brandingrichtlinien für Anwendungen


Dieses Thema behandelt die empfohlenen Brandingrichtlinien für die Entwicklung von Anwendungen mit Azure Active Directory. Die Richtlinien stellen sicher, dass sich Kunden, die sich über ihr in Azure AD verwaltetes Geschäfts- oder Schulkonto bei Ihrer Anwendung registrieren und anmelden möchten, besser zurechtfinden.

## Gegenüberstellung von persönlichen Konten und Geschäfts- oder Schulkonten von Microsoft

Microsoft verwaltet zwei Arten von Benutzerkonten:

- **Persönliche Konten** (ehemals Windows Live ID). Diese Konten stellen die Beziehung zwischen *einzelnen* Benutzern und Microsoft dar und werden für den Zugriff auf Verbrauchergeräte und -dienste von Microsoft verwendet. Konten dieser Art sind für den persönlichen Gebrauch vorgesehen.

- **Geschäfts- oder Schulkonten.** Diese Konten werden von Microsoft für Unternehmen verwaltet, die Azure Active Directory verwenden. Konten dieser Art dienen zur Anmeldung bei Office 365 und anderen Unternehmensdiensten von Microsoft.

Geschäfts- oder Schulkonten von Microsoft werden den Endbenutzern (Angestellten, Schülern/Studenten, Behördenmitarbeitern) üblicherweise von der jeweiligen Organisation (Unternehmen, Bildungseinrichtung, Behörde) zugewiesen. Diese Konten werden entweder direkt in der Cloud oder über die Azure AD-Plattform gemastert oder über ein lokales Verzeichnis (wie etwa Windows Server Active Directory) mit Azure AD synchronisiert. Microsoft fungiert als *Verwaltungsberechtigter* des Geschäfts- oder Schulkontos, die Konten gehören aber der Organisation und werden auch von dieser gesteuert.

## Verweisen auf Azure AD-Konten in Ihrer Anwendung

Microsoft verwendet die Markennamen „Azure“ und „Active Directory“ nicht gegenüber Endbenutzern – eine Regel, an die auch Sie sich halten sollten.

- Nach der Anmeldung eines Benutzers sollten Sie möglichst häufig den Namen und das Logo der Organisation verwenden. Das ist besser als die Verwendung generischer Formulierungen wie „Ihre Organisation“.

- Wenn der Benutzer nicht angemeldet sind, sollten Sie dessen Konto als „Geschäfts- oder Schulkonto“ bezeichnen und durch die Verwendung des Microsoft-Logos deutlich machen, dass das Konto von Microsoft verwaltet wird. Verwenden Sie keine Begriffe wie „Unternehmenskonto“, „Business-Konto“ oder „Firmenkonto“, um den Benutzer nicht zu verwirren.

## Piktogramm für Benutzerkonten
In einer früheren Version dieser Richtlinien haben wir die Verwendung eines blauen Badge-Piktogramms empfohlen. Aufgrund von Benutzer- und Entwicklerfeedback empfehlen wir inzwischen aber stattdessen die Verwendung des Microsoft-Logos. Dadurch wird den Benutzern besser vermittelt, dass sie sich bei Ihrer App mit dem gleichen Konto anmelden können, das sie auch für Office 365 und andere Unternehmensdienste von Microsoft verwenden.

## Registrieren und Anmelden mit Azure AD

Ihre Anwendung verwendet möglicherweise separate Vorgehensweisen für Registrierung und Anmeldung. Dies wird in den folgenden Abschnitten berücksichtigt.

**Ihre App unterstützt die Registrierung von Endbenutzern (etwa bei einer kostenlosen Testversion oder bei einem Freemium-Modell)**: Sie können eine **Registrierungsschaltfläche** anzeigen, mit deren Hilfe Benutzer über ihr Geschäfts - oder Schulkonto von Microsoft auf Ihre App zugreifen können. Beim ersten Zugriff auf Ihre App zeigt Azure AD eine Zustimmungsaufforderung an.

**Ihre App benötigt Berechtigungen, die die Zustimmung eines Administrators voraussetzen, oder Ihre App muss von der Organisation lizenziert werden**: Administratorzustimmung und Benutzeranmeldung sollten getrennt werden. Die **Schaltfläche zum Abrufen der App** leitet Administratoren zur Anmeldung weiter und fordert sie auf, im Namen von Benutzern in der Organisation ihre Zustimmung zu geben. Dies hat den zusätzlichen Vorteil, dass Zustimmungsaufforderungen für Endbenutzer Ihrer App unterdrückt werden.

## Darstellungsleitfaden für den App-Erwerb

Über den Link zum Abrufen der App muss der Benutzer auf die Zugriffsgewährungs- bzw. Autorisierungsseite von Azure AD weitergeleitet werden, damit ein Organisationsadministrator Ihrer App den Zugriff auf die von Microsoft gehosteten Organisationsdaten gewähren kann. Ausführliche Informationen zum Anfordern von Zugriff finden Sie im Artikel [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md).

Nach der Zustimmung des Administrators kann dieser entscheiden, ob Ihre App dem App-Startfeld von Office 365 hinzugefügt werden soll, sodass die Benutzer über das Startfeld oder über [https://portal.office.com/myapps](https://portal.office.com/myapps) darauf zugreifen können. Wenn Sie auf diese Funktion hinweisen möchten, können Sie beispielsweise eine Formulierung wie „App zur Organisation hinzufügen“ und die folgende Schaltfläche verwenden:

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/add-to-my-org.png)

Es empfiehlt sich jedoch, eine Erläuterung in Textform zu verwenden, anstatt nur auf Schaltflächen zu setzen. Beispiel:
> *Wenn Sie bereits Office 365 oder andere Unternehmensdienste von Microsoft verwenden, können Sie <your_app_name> einfach Zugriff auf die Daten Ihrer Organisation gewähren. Dadurch können Benutzer mit ihren bereits vorhandenen Geschäftskonten auf <your_app_name> zugreifen.*


## Darstellungsleitfaden für die Anmeldung
Ihre App sollte eine Anmeldeschaltfläche besitzen, über die der Benutzer an den Anmelde-Endpunkt mit dem Protokoll weitergeleitet wird, das Sie für die Azure AD-Integration verwenden. Im folgenden Abschnitt erfahren Sie, wie diese Schaltfläche auszusehen hat.

### Piktogramm und „Geschäfts- oder Schulkonto“
Durch die Kombination aus Microsoft-Logo und generischer Terminologie (Geschäfts- oder Schulkonto) hebt sich Azure AD von den anderen Identitätsanbietern ab, die Ihre App möglicherweise unterstützt. Sollte für „Geschäfts- oder Schulkonto“ nicht genügen Platz zur Verfügung stehen, können Sie den Begriff zu „Geschäftskonto“ verkürzen.

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/work-or-school-account.png)

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/work-account.png)

Darüber hinaus können Sie zusätzlichen Text bereitstellen, damit Endbenutzer sofort wissen, ob sie diese Schaltfläche verwenden können:

![Anwendungstypen und -szenarien](./media/active-directory-branding-guidelines/work-account-with-explaination.png)

## Brandingempfehlungen
Verwenden Sie „Geschäfts- oder Schulkonto“ in Kombination mit dem Microsoft-Logo, um auf Anmeldungen mit Azure AD hinzuweisen. Bei begrenztem Platzangebot kann „Geschäftskonto“ verwendet werden. Verwenden Sie aber **keine anderen Begriffe** wie „Unternehmenskonto“, „Business-Konto“ oder „Firmenkonto“.

„Office 365-ID“ und „Azure-ID“ dürfen **nicht** verwendet werden. Office 365 ist auch der Name eines Verbraucherangebots von Microsoft, bei dem Azure AD nicht für die Authentifizierung verwendet wird.

Das Microsoft-Logo darf **nicht** verändert werden.

Endbenutzer sollen **nicht** mit den Marken Azure und Active Directory in Berührung kommen. Gegenüber Entwicklern, IT-Experten und Administratoren können diese Begriffe dagegen problemlos verwendet werden.

## Navigationsempfehlungen

Stellen Sie den Benutzern eine Funktion zur Verfügung, über die sie sich abmelden und das Benutzerkonto wechseln können. Die meisten Benutzer besitzen zwar nur ein einzelnes persönliches Konto von Microsoft/Facebook/Google/Twitter, gehören aber häufig mehreren Organisationen an. Die Unterstützung mehrerer angemeldeter Benutzer folgt in Kürze.

## Unterstützen von Azure AD- und Microsoft-Konten in Ihrer App

Wenn Ihre App sowohl Azure AD- als auch Microsoft-Konten unterstützt, müssen Sie in Ihrer App zwei separate Schaltflächen bereitstellen. Wir arbeiten derzeit an einem Update, durch das Sie nach einmaliger Integration sowohl persönliche Konten als auch Geschäftskonten von Microsoft unterstützen können. Nach dem Update können Sie in Ihrer App eine einzelne Microsoft-Anmeldeschaltfläche verwenden.

<!---HONumber=AcomDC_0114_2016-->