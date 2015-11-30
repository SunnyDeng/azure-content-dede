<properties 
	pageTitle="Einrichten von Azure AD Join für Ihre Benutzer| Microsoft Azure" 
	description="Hier wird erklärt, wie Administratoren Azure AD Join für das lokale Verzeichnis und die Geräteregistrierung einrichten können."
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

# Einrichten von Azure AD Join in Ihrer Organisation

Vor dem Einrichten von Azure AD Join müssen Sie entweder das lokale Verzeichnis der Benutzer mit der Cloud synchronisieren oder verwaltete Konten in Azure AD manuell erstellen.

Detaillierte Informationen zum Synchronisieren lokaler Benutzer in Azure AD finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


Um Benutzer in Azure AD manuell zu erstellen und zu verwalten, lesen Sie [Benutzerverwaltung in Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Einrichten der Geräteregistrierung 
1. Melden Sie sich als Administrator beim Azure-Portal an.
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Wählen Sie in der Registerkarte **Verzeichnis** Ihr Verzeichnis aus.
4. Wählen Sie die Registerkarte **Konfigurieren** aus.
5. Scrollen Sie zum Abschnitt **Geräte**.
6. Legen Sie auf der Registerkarte **Geräte** Folgendes fest:  
   * **Maximale Anzahl der Geräte pro Benutzer**: Wählen Sie die maximale Anzahl der Geräte, die ein Benutzer in Azure AD haben kann. Wenn ein Benutzer diese Anzahl erreicht, können keine zusätzlichen Geräte hinzugefügt werden, bis eines oder mehrere der vorhandenen Geräte entfernt werden.
   * **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erfordern**: Aktivieren, wenn Benutzer eine zweite Stufe der Authentifizierung bereitstellen, um ihr Gerät mit Azure AD zu verknüpfen. Weitere Informationen über die mehrstufige Authentifizierung finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication in der Cloud](multi-factor-authentication-get-started-cloud/).
   * **Benutzer dürfen Geräte mit Azure AD verknüpfen**: Wählen Sie die Benutzer und Gruppen, die Geräte mit Azure AD verknüpfen dürfen.
   * **Zusätzliche Administratoren für mit Azure AD verknüpfte Geräte**: Mit Azure AD Premium oder der Enterprise Mobility Suite (EMS) können Sie auswählen, welchen Benutzern lokale Administratorrechte auf dem Gerät erteilt werden. Globale Administratoren und der Gerätebesitzer erhalten standardmäßig lokale Administratorrechte.

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
Nachdem Sie Azure AD Join für die Benutzer eingerichtet haben, können diese sich über ihre unternehmenseigenen oder persönlichen Geräte mit Azure AD verbinden.

Mithilfe der folgenden drei Szenarien können Sie es Ihren Benutzern ermöglichen, Azure AD Join einzurichten:

- Benutzer verknüpfen ein Unternehmensgerät direkt mit Azure AD.
- Benutzer verknüpfen ein unternehmenseigenes Gerät über eine Domäne mit dem lokalen Active Directory und erweitern dann auf Azure AD.
- Benutzer fügen auf einem persönlichen Gerät Arbeitskonten zu Windows hinzu. 

## Zusätzliche Informationen

* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von einer Domäne beigetretenen Geräten mit Azure AD für Windows 10-Benutzeroberflächen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->