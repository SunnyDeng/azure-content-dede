<properties
	pageTitle="Einrichten eines Windows 10-Geräts mit Azure AD in den Einstellungen| Microsoft Azure"
	description="Erläutert, wie Benutzer über das Menü „Einstellungen“ eine Verknüpfung mit Azure AD herstellen können."
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

# Einrichten eines Windows 10-Geräts mit Azure AD in den Einstellungen
Wenn Sie bereits Windows 7 oder Windows 8 verwenden und Ihr Computer oder Gerät auf Windows 10 aktualisiert wurde, können Sie über das Menü „Einstellungen“ eine Verknüpfung zu Azure Active Directory (Azure AD) herstellen.

## Herstellen einer Verknüpfung mit Azure AD über das Menü „Einstellungen“


1. Klicken Sie im Menü **Start** auf den Charm **Einstellungen**.
2. Wählen Sie unter **Einstellungen** die Optionen **System**->**Info**->**Azure AD beitreten** aus.
<center> ![Herstellen einer Verknüpfung zu Azure&#160;AD über das Menü „Einstellungen“](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Klicken Sie im Meldungsfenster „Azure AD beitreten“ auf **Weiter**.
<center> ![Meldungsfenster „Azure AD beitreten“](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Geben Sie Ihre Anmeldeinformationen ein. Die Anmeldeoberfläche enthält alle erforderlichen Schritte zum Abschließen der Authentifizierung. Wenn Sie einem Verbundmandanten angehören, stellt Ihnen Ihr Administrator die Verbundoberfläche bereit, die von Ihrer Organisation gehostet wird.
<center> ![Eingeben von Anmeldeinformationen](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Wenn Ihre Organisation Azure Multi-Factor Authentication für das Herstellen einer Verknüpfung mit Azure AD konfiguriert hat, müssen Sie vor dem Fortfahren die zweite Stufe bereitstellen.
6. Klicken Sie auf dem Bildschirm **Erlauben, dass dieses Gerät verwaltet wird** auf **Annehmen**.
7. Die Meldung „Das Gerät ist Ihrer Organisation in Azure AD beigetreten“ sollte angezeigt werden.


## Zusätzliche Informationen
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)

<!---HONumber=AcomDC_0302_2016-->