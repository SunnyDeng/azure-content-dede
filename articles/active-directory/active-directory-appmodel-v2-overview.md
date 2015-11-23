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
	ms.date="11/06/2015"
	ms.author="dastrock"/>

# App-Modell, Version 2.0, Vorschau: Anmelden von Benutzern am Microsoft-Konto und bei Azure AD in einer einzigen Anwendung

> [AZURE.NOTE]Diese Informationen gelten für App-Modell, Version 2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

In der Vergangenheit musste ein App-Entwickler, der sowohl Unterstützung für Microsoft-Konten als auch für Azure Active Directory benötigte, die Integration für zwei separate Systeme bereitstellen. Mit dem App-Modell, Version 2.0 können Sie nun Benutzer mit beiden Kontotypen anmelden. Mit einer einzelnen Implementierung können Sie eine Zielgruppe erreichen, die Millionen von Benutzern sowohl mit privaten als auch geschäftlichen Konten umfasst.

Ihre Apps können auch auf einen [Satz von Office 365-REST-APIs](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) mit einem der Kontentypen zurückgreifen. Derzeit umfasst dies Outlook-APIs für E-Mail, Kontakte und Kalender. Demnächst werden weitere Dienste hinzugefügt.
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

Das App-Modell, Version 2.0 befindet sich in der Vorschau. Während der Vorschauphase nehmen wir gerne Feedback entgegen und freuen uns, wenn Sie uns Ihre Erfahrung beim Testen des neuen App-Modells mitteilen. Anhand dieses Feedbacks nehmen wir möglicherweise grundlegende Änderungen zur Verbesserung des Diensts vor. Sie sollten innerhalb dieses Zeitraums keine Produktions-App mithilfe des App-Modells, Version 2.0 veröffentlichen.
<!-- TODO: Get approval on how it looks  -->

## Erste Schritte
Es gibt zwei Möglichkeiten, eine eigene App mit dem App-Modell, Version 2.0 zu erstellen und auszuführen. Sie können Protokollnachrichten direkt mit [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) oder[Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) senden. Wahlweise können Sie auch unsere Bibliotheken dazu verwenden. Wählen Sie im Folgenden Ihre bevorzugte Plattform, und legen Sie los.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Neuerungen
Sie sollten diese Seite häufiger aufrufen, da Informationen über zukünftige Änderungen am App-Modell, Version 2.0 hier aufgeführt werden. Außerdem veröffentlichen wir Tweets zu Updates unter @AzureAD.

- Erfahren Sie mehr über die [App-Typen, die Sie mit dem App-Modell, Version 2.0 erstellen können](active-directory-v2-flows.md).
- Entwickler, die mit Azure Active Directory vertraut sind, sollten die Informationen zu [Updates für unsere Protokolle und Unterschiede im App-Modell, Version 2.0](active-directory-v2-compare.md) lesen.
- Aktuelle [Einschränkungen der Vorschau](active-directory-v2-limitations.md).

## Referenz
Die folgenden Links bieten ausführliche Informationen zur Plattform:

- Hilfe zu Stack Overflow mit [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory)- oder [adal](http://stackoverflow.com/questions/tagged/adal)-Tags.
- Teilen Sie uns Ihre Meinung zur Vorschau über [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) mit. Verwenden Sie den Ausdruck "AppModelv2:" im Titel Ihres Beitrags, damit wir ihn einfacher finden können.
- [App-Modell, Version 2.0 –Protokollreferenz](active-directory-v2-protocols.md)
- [App-Modell, Version 2.0 –Tokenreferenz](active-directory-v2-tokens.md)
- [Office 365-REST-API-Referenz](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Bereiche und Consent im v2-Endpunkt](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!---HONumber=Nov15_HO3-->