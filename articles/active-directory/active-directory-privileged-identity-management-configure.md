<properties
	pageTitle="Azure AD Privileged Identity Management"
	description="Ein Thema, in dem erläutert wird, was Azure AD Privileged Identity Management ist und wie die Konfiguration erfolgt."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="inhenk"/>

# Azure AD Privileged Identity Management

Mithilfe von Azure AD Privileged Identity Management können Sie Ihre privilegierten Benutzeridentitäten und deren Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten wie Office 365 oder Microsoft Intune verwalten, steuern und überwachen.

Damit Benutzer privilegierte Vorgänge ausführen konnten, mussten Organisationen häufig vielen ihrer Benutzer einen dauerhaften privilegierten Zugriff auf Azure AD oder auf Azure- oder Office 365-Ressourcen oder andere SaaS-Apps erteilen. Für viele Kunden stellt dies ein größer werdendes Sicherheitsrisiko für ihre in der Cloud gehosteten Ressourcen dar, da sie die Aktionen dieser Benutzer mit ihren Administratorberechtigungen nicht ausreichend überwachen können. Darüber hinaus kann ein gefährdetes Benutzerkonto mit privilegiertem Zugriff Auswirkungen auf die Gesamtsicherheit der Cloud haben. Mit Azure AD Privileged Identity Management können Sie dieses Risiko in den Griff bekommen.

Azure AD Privileged Identity Management ermöglicht in dieser Vorschauversion Folgendes:

- Ermitteln, welche Benutzer Azure AD-Administratoren sind
- Aktivieren eines bedarfsabhängigen administrativen Zugriffs auf Verzeichnisressourcen
- Abrufen von Berichten zum Administratorzugriffsverlauf und zu Änderungen bei Zuweisungen von Administratoren
- Aktivieren von Benachrichtigungen zum Zugriff auf eine privilegierte Rolle

In dieser Vorschauversion kann Azure AD Privileged Identity Management die integrierten Azure Active Directory-Organisationsrollen verwalten:

- Globaler Administrator
- Abrechnungsadministrator
- Dienstadministrator  
- Benutzeradministrator
- Kennwortadministrator

## Bedarfsabhängiger Administratorzugriff

In der Vergangenheit konnten Sie einen Benutzer über das Azure-Verwaltungsportal oder Windows PowerShell einer Administratorrolle zuweisen. Dadurch wird der Benutzer zum **permanenten Administrator**, der in seiner zugeordneten Rolle stets aktiv bleibt. Diese Vorschauversion bietet Unterstützung für **temporäre Administratoren**. Dies sind Benutzer, die für die zugewiesene Rolle einen Aktivierungsprozess durchlaufen müssen. Der Aktivierungsprozess ändert die Zuweisung des Benutzers zu einer Rolle in Azure AD von "Inaktiv" in "Aktiv".

## Aktivieren von Privileged Identity Management für Ihr Verzeichnis

Über das [Microsoft Azure-Portal](https://portal.azure.com/) können Sie mit der Nutzung von Azure AD Privileged Identity Management beginnen. Im Moment wird Azure AD Privileged Identity Management nur im Microsoft Azure-Portal angezeigt. Sie müssen ein globaler Administrator sein, um Azure AD Privileged Identity Management für ein Verzeichnis zu aktivieren.

![][1]

Nach Initialisierung dieser Erweiterung werden Sie automatisch zum ersten **Sicherheitsadministrator** des Verzeichnisses. Nur ein Sicherheitsadministrator kann zur Verwaltung des Zugriffs für andere Administratoren auf diese Erweiterung zugreifen. Während der Initialisierung wird eine Kachel mit Azure AD Privileged Identity Management dem Startmenü im Azure-Vorschauportal hinzugefügt.

## Privileged Identity Management-Dashboard

Azure AD Privileged Identity Management bietet ein Dashboard, das wichtige Informationen enthält, wie z. B.:

- Anzahl der Benutzer, die jeder privilegierten Rolle zugewiesen sind  
- Anzahl temporärer und permanenter Administratoren
- Zugriffsverlauf des Administrators

![][2]

## Verwaltung privilegierter Rollen

Mit Azure AD Privileged Identity Management können Sie die Administratoren verwalten, indem Sie jeder Rolle permanente oder temporäre Administratoren hinzufügen oder diese entfernen.

![][3]

## Konfigurieren der Rollenaktivierungseinstellungen

Mithilfe der Rollenaktivierungseinstellungen können Sie die Eigenschaften der Aktivierung der temporären Rolle konfigurieren:

- Dauer des Rollenaktivierungszeitraums
- Benachrichtigung zur Rollenaktivierung
- Informationen, die ein Benutzers während des Rollenaktivierungsprozesses bereitstellen muss  

![][4]

## Rollenaktivierung  

Um eine Rolle zu aktivieren, muss ein temporärer Administrator eine zeitgebundene "Aktivierung" für die Rolle anfordern. Die Aktivierung kann über die Option **Meine Rolle aktivieren** in Azure AD Privileged Identity Management angefordert werden.

Ein Administrator, der eine Rolle aktivieren möchte, muss Azure AD Privileged Identity Management im Azure-Vorschauportal initialisieren.

Alle Administratortypen können Azure AD Privileged Identity Management zum Aktivieren ihrer Rolle nutzen.

Die Rollenaktivierung ist zeitlich gebunden. In den Rollenaktivierungseinstellungen können Sie die Länge der Aktivierung und auch die erforderlichen Informationen konfigurieren, die der Administrator angeben muss, um die Rolle zu aktivieren.

![][5]

## Verlauf der Rollenaktivierung

Mithilfe von Azure AD Privileged Identity Management können Sie auch Änderungen bei Zuweisungen privilegierter Rollen und den Rollenaktivierungsverlauf nachverfolgen. Dies kann mithilfe der Überwachungsprotokolloptionen erfolgen:

![][6]

## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=Oct15_HO3-->