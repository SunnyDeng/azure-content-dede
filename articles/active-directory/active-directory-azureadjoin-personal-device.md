

<properties
	pageTitle="Verknüpfen eines persönlichen Geräts mit Ihrer Organisation| Microsoft Azure"
	description="Erläutert, wie Benutzer ihre persönlichen Windows 10-Geräte in ihrem Unternehmensnetzwerk registrieren können und wie die Bereitstellungsschritte für ein BYOD-Szenario aussehen."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="femila"/>

# Verknüpfen eines persönlichen Geräts mit Ihrer Organisation

## So verknüpfen Sie ein Windows 10-Gerät mit Ihrer Organisation

1.	Wählen Sie im Menü **Start** **Einstellungen**.
2.	Wählen Sie **Konten**, und klicken Sie dann auf **Ihr Konto**.
3.	Klicken Sie auf **Geschäfts- oder Schulkonto hinzufügen**, und geben Sie das Konto Ihrer Organisation an.
4.	Geben Sie auf der Anmeldeseite für Ihre Organisation Ihren Benutzernamen und Ihr Kennwort ein, und klicken Sie dann auf **OK**.
5.	Sie werden zu einer Multi-Factor Authentication aufgefordert. (Diese Aufforderung kann vom IT-Administrator konfiguriert werden.)
6.	Azure Active Directory (Azure AD) prüft, ob eine Registrierung des Geräts in der Mobilgeräteverwaltung erforderlich ist.
7.	Windows registriert das Gerät im Verzeichnis der Organisation in Azure AD und in der Mobilgeräteverwaltung (falls zutreffend).
8.	Wenn Sie ein verwalteter Benutzer sind, führt Windows Sie über die automatische Anmeldung auf den Desktop.
9.	Wenn Sie ein Partnerbenutzer sind, werden Sie zu einem Windows-Anmeldebildschirm geführt und müssen Ihre Anmeldeinformationen eingeben.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->