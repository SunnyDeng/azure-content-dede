<properties 
	pageTitle="Einrichten eines Windows 10-Geräts mit Azure AD in den Einstellungen| Microsoft Azure" 
	description="In diesem Thema wird erklärt, wie Benutzer über das Menü ";Einstellungen"; eine Verknüpfung zu Azure AD erstellen können." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Einrichten eines Windows 10-Geräts mit Azure AD in den Einstellungen
Wenn Sie bereits Windows 7 oder 8 nutzen und Ihren Computer auf Windows 10 aktualisiert haben, können Sie über das Menü "Einstellungen" mit Azure AD eine Verknüpfung herstellen.

Verknüpfen mit Azure AD im Menü "Einstellungen"
-----------------------------------------------------------------------------------------------

1. Klicken Sie im Startmenü auf den Charm "Einstellungen".
2. Aus den Einstellungen -> **System** -> **About** -> **Verknüpfen von Azure AD**
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Klicken Sie im Azure AD Join-Nachrichtenfenster auf **Weiter**.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Geben Sie Ihre Anmeldeinformationen ein. Die Anmeldeoberfläche enthält alle erforderlichen Schritte zum Abschließen der Authentifizierung. Wenn Sie einem Verbundsmandanten angehören, stellt Ihnen Ihr Administrator die Verbundserfahrung bereit, die von Ihrer Organisation gehostet wird.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Wenn Ihre Organisation die mehrstufige Authentifizierung für das Verknüpfen mit Azure AD konfiguriert hat, müssen Sie vor dem Fortfahren den zweiten Faktor bereitstellen.
6. Klicken Sie auf dem Bildschirm "Erlauben, dass dieses Gerät verwaltet wird" auf **Annehmen**.
7. Sie sollten die Nachricht "Das Gerät ist jetzt mit Ihrem Unternehmen in Azure AD verknüpft" angezeigt bekommen.


## Zusätzliche Informationen
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md)
* [Informationen zu Verwendungsszenarios und Bereitstellungsaspekte für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Oct15_HO3-->