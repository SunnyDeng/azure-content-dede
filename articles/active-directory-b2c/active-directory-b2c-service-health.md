<properties
	pageTitle="Preview-Version von Azure Active Directory B2C: Dienstintegrität | Microsoft Azure"
	description="Enthält Benachrichtigungen zu kleineren Problemen, Status und Lösungen unter Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Preview-Version von Azure Active Directory B2C: Dienstintegrität

Diese Seite enthält Benachrichtigungen zu kleineren Problemen, Status und Lösungen. Sie können ein Lesezeichen für diese Seite erstellen, um in Zukunft einfacher darauf zugreifen zu können. Verfolgen Sie den Stand außerdem weiter im [Azure-Statusdashboard](https://azure.microsoft.com/status/).

### 22\.03.2016: SSO-Fehler in B2C-Mandanten

Ein SSO-Fehler (Single Sign-On) wurde am 17.03.2016 um 13 Uhr PST erkannt und am 18.03.2016 um 10 Uhr PST behoben. Während dieses Zeitraums waren hiervon weniger als 100 Benutzer betroffen. Wir werden dies weiter genau überwachen. Bedingungen der Benutzer, bei denen dieser Fehler aufgetreten ist:

1. Sie haben eine Anmelderichtlinie eingerichtet, für die „lokale Konten“ als einziger Identitätsanbieter konfiguriert war.
2. Ein Benutzer meldet sich beim ersten Versuch erfolgreich an Azure AD B2C an.
3. Wenn der Benutzer versucht, sich erneut anzumelden, ist aber kein SSO möglich, sondern es wird eine Fehlermeldung angezeigt.

Die einzige Problemumgehung für Benutzer (nach Schritt 3) bestand darin, den Browser zu schließen und wieder zu öffnen und sich erneut zu authentifizieren.

<!---HONumber=AcomDC_0323_2016-->