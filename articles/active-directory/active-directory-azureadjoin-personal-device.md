

<properties 
	pageTitle="Verknüpfen eines persönlichen Geräts mit Ihrer Organisation| Microsoft Azure" 
	description="Erläutert, wie Benutzer ihren persönlichen Windows 10-Computer in ihrem Unternehmensnetzwerk registrieren können und wie die Bereitstellungsschritte für ein BYOD-Szenario aussehen." 
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
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Verknüpfen eines persönlichen Geräts mit Ihrer Organisation

So verknüpfen Sie ein Windows 10-Gerät mit Ihrer Organisation
--------------------------------------------------------------------------------------------
1.	Wählen Sie im Menü **Start** **Einstellungen**. 
2.	Wählen Sie **Konten**, und klicken Sie dann auf **Ihr Konto**.
3.	Klicken Sie auf **Geschäfts- oder Schulkonto hinzufügen**, und geben Sie das Konto Ihrer Organisation an.
4.	Sie werden dann zur Anmeldeseite Ihrer Organisation weitergeleitet. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, und klicken Sie auf **OK**.
5.	Sie werden dann nach der mehrstufigen Authentifizierung gefragt. Diese lässt sich von Ihrer IT-Abteilung konfigurieren.
6.	Azure AD prüft dann, ob für den Benutzer oder das Gerät eine Anmeldung zur mobilen Geräteverwaltung (Mobile Device Management, MDM) erforderlich ist. 
7.	Windows wird das Gerät anschließend im Verzeichnis der Organisation in Azure AD registrieren und es für das MDM anmelden.
8.	Sobald dies abgeschlossen ist, beendet Windows bei verwalteten Benutzern den Installationsvorgang, und der Benutzer wird über die automatische Anmeldung auf den Desktop geführt.
9.	Falls Sie ein Partnerbenutzer sind, gelangen Sie zum Anmeldebildschirm von Windows und müssen Ihre Anmeldeinformationen eingeben.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->