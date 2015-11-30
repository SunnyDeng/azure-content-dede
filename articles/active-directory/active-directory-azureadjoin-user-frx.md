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
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Einrichten eines neuen Geräts mit Azure AD während des Setups

In Windows 10 können Endbenutzer ihre Geräte selbst mit Azure AD während des Eindrucks beim ersten Ausführen (First Run Experience, FRX) verknüpfen. Dadurch können Organisationen Ihren Mitarbeitern oder Schülern eingeschweißte Geräte aushändigen oder sie ihre eigenen Geräte wählen lassen. Wenn Sie entweder die Professional- oder die Enterprise-SKU für Windows 10 installieren, wird standardmäßig das Setup für Geräte übernommen, die dem Unternehmen gehören.

Hinzufügen eines Geräts zu Azure AD
-----------------------------------------------------------------------

1. Nachdem Sie die **Vorbereitung** erledigt haben, geht es mit dem Setup-Assistenten weiter.
2. Starten Sie, indem Sie ihre Region und Sprache anpassen, den Endbenutzer-Lizenzvertrag akzeptieren und online gehen.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. Wählen Sie das Netzwerk für die Verbindung mit dem Internet.
4. Wählen Sie aus, ob das Gerät persönlich oder unternehmenseigen ist:
5. Klicken Sie auf **Dieses Gerät gehört meiner Organisation**. Azure AD Join wird nun gestartet. Im Folgenden sehen einen Bildschirm, der in der Windows 10 Professional-SKU angezeigt wird. 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	Geben Sie die Anmeldeinformationen ein, die von Ihrer Organisation bereitgestellt werden.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	Sobald Sie Ihren Benutzernamen eingegeben haben, gibt es einen übereinstimmenden Mandanten in Azure AD. Wenn Sie in einer Verbunddomäne sind, werden Sie auf Ihren lokalen Sicherheitstokendienst-Server (z. B. AD FS) umgeleitet. Wenn Sie ein Benutzer in einer nicht verbundenen Domänen sind, müssen Sie Ihre Anmeldeinformationen direkt auf der von Azure AD gehosteten Seite eingeben. Außerdem erscheint das Logo Ihrer Organisation und der Support-Text, wenn Unternehmensbranding konfiguriert wurde.
8.	Sie werden dann nach der mehrstufigen Authentifizierung gefragt. Diese lässt sich von Ihrer IT-Abteilung konfigurieren.
9.	Azure AD prüft dann, ob für den Benutzer oder das Gerät eine Anmeldung zur mobilen Geräteverwaltung (Mobile Device Management, MDM) erforderlich ist. 
10.	Windows wird das Gerät anschließend im Verzeichnis der Organisation in Azure AD registrieren und es für das MDM anmelden.
11.	Sobald dies abgeschlossen ist, beendet Windows bei verwalteten Benutzern den Installationsvorgang und der Benutzer wird über die automatische Anmeldung auf den Desktop geführt.
12.	Falls Sie ein Partnerbenutzer sind, gelangen Sie zum Anmeldebildschirm von Windows und müssen Ihre Anmeldeinformationen eingeben, um sich anzumelden.

> [AZURE.NOTE]Das Verknüpfen von lokalen Active Directory-Domänen wird während des Eindrucks beim ersten Ausführen in Windows nicht unterstützt. Wenn Sie also einen Computer mit einer Domäne verknüpfen möchten, wählen Sie stattdessen den Link "Einrichten von Windows mit einem lokalen Konto". Sie können dann die Domäne aus den PC-Einstellungen verknüpfen, wie Sie dies bereits zuvor getan haben.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->