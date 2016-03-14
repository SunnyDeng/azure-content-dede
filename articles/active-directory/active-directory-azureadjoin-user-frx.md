<properties
	pageTitle="Einrichten eines neuen Geräts mit Azure AD während des Setups| Microsoft Azure"
	description="In diesem Thema wird erklärt, wie Benutzer Azure AD Join während Ihres Eindrucks beim ersten Ausführen einrichten können."
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

# Einrichten eines neuen Geräts mit Azure AD während des Setups

In Windows 10 können Benutzer ihre Geräte während des Eindrucks beim ersten Ausführen (First Run Experience, FRX) mit Azure Active Directory (Azure AD) verknüpfen. Dadurch können Organisationen Ihren Mitarbeitern oder Schülern eingeschweißte Geräte aushändigen oder sie ihre eigenen Geräte auswählen lassen (CYOD). Wenn die Edition Windows 10 Professional oder Windows 10 Enterprise auf einem Gerät installiert ist, wird standardmäßig der Setupprozess für firmeneigene Geräte übernommen.

## Hinzufügen eines Geräts zu Azure AD


1. Wenn Sie das neue Gerät einschalten und den Setupprozess starten, sollte die Meldung **Vorbereitung** angezeigt werden. Befolgen Sie die Anweisungen, um Ihr Gerät einzurichten.
2. Passen Sie zuerst die Region und die Sprache an. Akzeptieren Sie anschließend die Microsoft-Software-Lizenzbedingungen. ![Für Ihre Region anpassen](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Wählen Sie das Netzwerk aus, das Sie für die Verbindung mit dem Internet verwenden möchten.
4. Wählen Sie aus, ob Sie ein persönliches oder ein firmeneigenes Gerät verwenden: Klicken Sie bei einem firmeneigenen Gerät auf **Dieses Gerät gehört meiner Organisation**. Azure AD Join wird nun gestartet. Im Folgenden sehen einen Bildschirm, der angezeigt wird, wenn Sie Windows 10 Professional verwenden.
<center>
![Bildschirm „Wem gehört dieser PC“](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.	Geben Sie die Anmeldeinformationen ein, die von Ihrer Organisation bereitgestellt wurden.
<center>
![Anmeldebildschirm](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.	Sobald Sie Ihren Benutzernamen eingegeben haben, wird ein passender Mandant in Azure AD gesucht. Wenn Sie in einer Verbunddomäne sind, werden Sie an Ihren lokalen Sicherheitstokendienst-Server (STS) umgeleitet, beispielsweise Active Directory-Verbunddienste (AD FS).
7. Wenn Sie ein Benutzer in einer nicht verbundenen Domäne sind, geben Sie Ihre Anmeldeinformationen direkt auf der von Azure AD gehosteten Seite ein. Wenn Unternehmensbranding konfiguriert wurde, erscheint außerdem das Logo Ihrer Organisation und der unterstützende Text.
8.	Sie werden zu einer mehrstufigen Authentifizierung aufgefordert. Diese Aufforderung kann vom IT-Administrator konfiguriert werden.
9.	Azure AD prüft, ob für den Benutzer oder das Gerät eine Anmeldung zur mobilen Geräteverwaltung erforderlich ist.
10.	Windows registriert das Gerät im Verzeichnis der Organisation in Azure AD und in der Mobilgeräteverwaltung (falls zutreffend).
11.	Wenn Sie ein verwalteter Benutzer sind, leitet Windows Sie über den automatischen Anmeldeprozess zum Desktop weiter.
12.	Wenn Sie ein Partnerbenutzer sind, werden Sie zum Windows-Anmeldebildschirm weitergeleitet und müssen Ihre Anmeldeinformationen eingeben.

> [AZURE.NOTE] Das Verknüpfen von lokalen Windows Server Active Directory-Domänen wird während des Eindrucks beim ersten Ausführen in Windows nicht unterstützt. Wenn Sie also einen Computer mit einer Domäne verknüpfen möchten, wählen Sie stattdessen den Link **Windows mit einem lokalen Konto einrichten** aus. Sie können dann die Domäne über die Einstellungen Ihres Computers verknüpfen, wie Sie dies bereits zuvor getan haben.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->