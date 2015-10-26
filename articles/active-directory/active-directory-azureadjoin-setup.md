<properties 
	pageTitle="Einrichten von Azure AD Join für Ihre Benutzer| Microsoft Azure" 
	description="Erläutert, wie Administratoren Azure AD Join für ihre Endbenutzer (Mitarbeiter, Studenten und andere Benutzer) in ihrer Organisation einrichten können." 
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
	ms.date="10/09/2015" 
	ms.author="femila"/>

# Einrichten von Azure AD Join in Ihrer Organisation

Vor dem Einrichten von Azure AD Join müssen Sie entweder das lokale Verzeichnis der Benutzer mit der Cloud synchronisieren oder verwaltete Konten in Azure AD manuell erstellen.

Detaillierte Informationen zum Synchronisieren lokaler Benutzer in Azure AD finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).


Um Benutzer in Azure AD manuell zu erstellen und zu verwalten, lesen Sie [Benutzerverwaltung in Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Einrichten der Geräteregistrierung 
1. Melden Sie sich beim Azure-Portal als Administrator an.
2. Wählen Sie im linken Bereich "Active Directory" aus.
3. Wählen Sie in der Registerkarte **Verzeichnis** Ihr Verzeichnis aus.
4. Wählen Sie die Registerkarte **Konfigurieren** aus.
5. Scrollen Sie zum Abschnitt **Geräte**.
6. Legen Sie auf der Registerkarte **Geräte** Folgendes fest:  
   * **Maximale Anzahl der Geräte pro Benutzer**: Wählen Sie die maximale Anzahl der Geräte, die ein Benutzer in Azure AD haben kann. Wenn ein Benutzer diese Anzahl erreicht, können keine zusätzlichen Geräte hinzugefügt werden, bis eines oder mehrere der vorhandenen Geräte entfernt werden.
   * **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erfordern**: Aktivieren, wenn Benutzer eine zweite Stufe der Authentifizierung bereitstellen, um ihr Gerät mit Azure AD zu verknüpfen. Weitere Informationen über die mehrstufige Authentifizierung finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication in der Cloud](multi-factor-authentication-get-started-cloud/).
   *  **Benutzer dürfen Geräte mit Azure AD verknüpfen**: Wählen Sie die Benutzer und Gruppen aus, die Geräte mit Azure AD verknüpfen dürfen.   
    * **Zusätzliche Administratoren für mit Azure AD verknüpfte Geräte**: Mit Azure AD Premium oder der Enterprise Mobility Suite (EMS) können Sie auswählen, welchen Benutzern lokale Administratorrechte auf dem Gerät erteilt werden. Globale Administratoren und der Gerätebesitzer erhalten standardmäßig lokale Administratorrechte.
   
>[AZURE.NOTE]Wenn bei den Benutzern der Fehler "**Limit für die Geräteregistrierung erreicht**" (Fehlercode: **0x801C000E - DSREG\_ E\_ DEVICE\_ REGISTRATION\_ QUOTA\_EXCCEEDED**) angezeigt wird, müssen Sie die Anzahl der maximal pro Benutzer zulässigen Geräte ändern. Wählen Sie im Abschnitt **Benutzer dürfen Geräte in Azure AD einbinden** die Option **Hinzufügen** aus, und legen Sie die Anzahl der pro Benutzer zulässigen Geräte fest.
      

    
<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
Nachdem Sie Azure AD Join für die Benutzer eingerichtet haben, können diese sich über ihre unternehmenseigenen oder persönlichen Geräte mit Azure AD verbinden.

Mithilfe der folgenden drei Szenarien können Sie es Ihren Benutzern ermöglichen, Azure AD Join einzurichten:

- Benutzer verknüpfen ein Unternehmensgerät direkt mit Azure AD.
- Benutzer verknüpfen ein unternehmenseigenes Gerät über eine Domäne mit dem lokalen Active Directory und erweitern dann auf Azure AD.
- Benutzer fügen auf einem persönlichen Gerät Arbeitskonten zu Windows hinzu. 

## Zusätzliche Informationen
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-overview.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Oct15_HO3-->