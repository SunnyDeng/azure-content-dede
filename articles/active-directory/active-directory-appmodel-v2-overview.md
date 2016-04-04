<properties
	pageTitle="Übersicht über das App-Modell v2.0 | Microsoft Azure"
	description="Eine Einführung in die Entwicklung von Apps mit Microsoft-Konto- und Azure Active Directory-Anmeldung."
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
	ms.date="03/18/2016"
	ms.author="dastrock"/>

# Anmelden von Benutzern mit Microsoft-Konto und aus Azure AD bei einer einzelnen Anwendung

In der Vergangenheit musste ein App-Entwickler, der sowohl Unterstützung für Microsoft-Konten als auch für Azure Active Directory benötigte, die Integration für zwei separate Systeme bereitstellen. Wir haben jetzt eine neue Version der Authentifizierungs-API eingeführt, über die Sie Benutzer mit beiden Arten von Konten im Azure AD-System anmelden können. Dieses zusammengeführte Authentifizierungssystem wird als **v2.0-Endpunkt** bezeichnet. Mit dem v2.0-Endpunkt können Sie mit einer einzelnen Implementierung eine Zielgruppe erreichen, die Millionen von Benutzern sowohl mit privaten als auch Geschäfts-, Schul- oder Unikonten umfasst.

Apps, die den v2.0-Endpunkt verwenden, können auch REST-APIs aus [Microsoft Graph](https://graph.microsoft.io) und [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) mit beiden Kontotypen nutzen.

## Erste Schritte
Wählen Sie unten Ihre bevorzugte Plattform aus, um eine App mit unseren Open Source-Bibliotheken und -Frameworks zu erstellen. Alternativ können Sie unsere OAuth 2.0- und OpenID Connect-Protokolldokumentation verwenden, um Protokollmeldungen direkt ohne Verwendung einer Authentifizierungsbibliothek zu senden und zu empfangen.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Neuerungen
Anhand der folgenden konzeptuellen Informationen lässt sich besser verstehen, was mit dem v2.0-Endpunkt möglich ist und was nicht.

- Wenn Sie 2015 während des Vorschauzeitraums des v2.0-Endpunkts eine App erstellt haben, lesen Sie die [Informationen zu den Protokolländerungen](active-directory-v2-preview-oidc-changes.md), die wir erst kürzlich vorgenommen haben.
- Erfahren Sie mehr über die [App-Typen, die Sie mit dem v2.0-Endpunkt erstellen können](active-directory-v2-flows.md).
- Entwickler, die mit Azure Active Directory vertraut sind, sollten die Informationen zu [Updates für unsere Protokolle und Unterschiede im v2.0-Endpunkt](active-directory-v2-compare.md) lesen.
- Informieren Sie sich über die [Einschränkungen](active-directory-v2-limitations.md) des v2.0-Endpunkts.

## Referenz
Die folgenden Links bieten ausführliche Informationen zur Plattform:

- Hilfe zu Stack Overflow mit [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory)- oder [adal](http://stackoverflow.com/questions/tagged/adal)-Tags.
- [v2.0-Protokollreferenz](active-directory-v2-protocols.md)
- [v2.0-Tokenreferenz](active-directory-v2-tokens.md)
- [Bereiche, Berechtigungen und Zustimmung im v2.0-Endpunkt](active-directory-v2-scopes.md)
- [Microsoft App-Registrierungsportal](https://apps.dev.microsoft.com)
- [Office 365-REST-API-Referenz](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- Im Folgenden sind die Open Source-Clientbibliotheken und -Beispiele aufgeführt, die mit dem v2.0-Endpunkt getestet wurden.

  - [Java WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [iOS-OAuth2-Client](https://github.com/nxtbgthng/OAuth2Client)
  - [Android-OAuth2-Client](https://github.com/wuman/android-oauth-client)
  - [Android-OpenID Connect-Client](https://github.com/kalemontes/OIDCAndroidLib)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them!  Use the phrase "AppModelv2:" in the title of your post so we can find it.
-->

<!---HONumber=AcomDC_0323_2016-->