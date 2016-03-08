<properties
	pageTitle="Einrichten von Azure AD Join für Ihre Benutzer| Microsoft Azure"
	description="Erläutert, wie Administratoren Azure AD Join für das lokale Verzeichnis und die Geräteregistrierung einrichten können."
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
	ms.topic="get-started-article"
	ms.date="02/26/2016"
	ms.author="femila"/>

# Einrichten von Azure AD Join in Ihrer Organisation

Vor dem Einrichten von Azure Active Directory Join (Azure AD Join) müssen Sie entweder das lokale Verzeichnis der Benutzer mit der Cloud synchronisieren oder verwaltete Konten in Azure AD manuell erstellen.

Detaillierte Informationen zum Synchronisieren lokaler Benutzer in Azure AD finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


Um Benutzer in Azure AD manuell zu erstellen und zu verwalten, lesen Sie [Benutzerverwaltung in Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Einrichten der Geräteregistrierung
1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich **Active Directory** aus.
3. Wählen Sie auf der Registerkarte **Verzeichnis** Ihr Verzeichnis aus.
4. Wählen Sie die Registerkarte **Konfigurieren** aus.
5. Wechseln Sie zum Abschnitt **Geräte**.
6. Legen Sie auf der Registerkarte **Geräte** Folgendes fest:  
   * **MAXIMALE ANZAHL VON GERÄTEN PRO BENUTZER:** Wählen Sie die maximale Anzahl der Geräte aus, die ein Benutzer in Azure AD haben kann. Wenn ein Benutzer diese Anzahl erreicht, können keine zusätzlichen Geräte hinzugefügt werden, bis eines oder mehrere der vorhandenen Geräte entfernt werden.
   * **MULTI-FACTOR AUTH ZUM HINZUFÜGEN VON GERÄTEN ERFORDERLICH:** Legen Sie fest, ob Benutzer eine zweite Stufe der Authentifizierung bereitstellen müssen, um ihr Gerät mit Azure AD zu verknüpfen. Weitere Informationen über Azure Multi-Factor Authentication finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication in der Cloud](multi-factor-authentication-get-started-cloud/).
   * **BENUTZER DÜRFEN GERÄTE IN AZURE AD EINBINDEN:** Wählen Sie die Benutzer und Gruppen aus, die Geräte mit Azure AD verknüpfen dürfen.
   * **WEITERE ADMINISTRATOREN FÜR IN AZURE AD EINGEBUNDENE GERÄTE:** Mit Azure AD Premium oder der Enterprise Mobility Suite (EMS) können Sie auswählen, welchen Benutzern lokale Administratorrechte für das Gerät erteilt werden. Globale Administratoren und Gerätebesitzer erhalten standardmäßig lokale Administratorrechte.

<center>![Einrichten der Geräteregistrierung](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

Nachdem Sie Azure AD Join für die Benutzer eingerichtet haben, können diese sich über ihre unternehmenseigenen oder persönlichen Geräte mit Azure AD verbinden.

Die folgenden drei Szenarios können Sie verwenden, um Benutzern das Einrichten von Azure AD Join zu ermöglichen:

- Benutzer verknüpfen ein unternehmenseigenes Gerät direkt mit Azure AD.
- Benutzer verknüpfen ein unternehmenseigenes Gerät über eine Domäne mit dem lokalen Active Directory und erweitern das Gerät dann auf Azure AD.
- Benutzer fügen auf einem persönlichen Gerät Geschäfts-, Schul- oder Unikonten zu Windows hinzu.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Benutzererfahrungen beim Verknüpfen von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->