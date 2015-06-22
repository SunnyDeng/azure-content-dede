<properties 
	pageTitle="Verwalten von Gruppen in Azure AD" 
	description="Ein Thema mit Erläuterungen zum Verwalten von Gruppen in Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Verwalten von Gruppen in Azure AD

Eine Gruppe ist eine Sammlung von Benutzern und Gruppen, die als Einheit verwaltet werden kann. Benutzer und Gruppen, die zu einer bestimmten Gruppe gehören, werden als Gruppenmitglieder bezeichnet. Mithilfe von Gruppen kann die Verwaltung vereinfacht werden, indem eine Zusammenstellung von Berechtigungen und Rechten vielen Konten auf einmal zugewiesen wird, anstatt jedem Konto einzeln Berechtigungen und Rechte zuzuweisen.

Derzeit können Sie in Azure AD nur Sicherheitsgruppen erstellen.

Gruppen bietet eine bequeme Möglichkeit, Benutzern den Zugriff auf Anwendungen in Fällen zu gewähren, in denen Sie viele Benutzer derselben Anwendung zuweisen müssen. Sie können Gruppen auch beim Konfigurieren der Zugriffsverwaltung anderer Onlinedienste verwenden, die den Zugriff auf Ressourcen steuern, z. B. SharePoint Online.

Wenn Sie die Verzeichnissynchronisierung konfiguriert haben, können Sie Gruppen sehen, die aus Ihrem lokalen Windows Server Active Directory synchronisiert wurden. Diese enthalten in der Eigenschaft "Erstellt aus" den Wert "Lokales Active Directory". Sie müssen diese Gruppen weiter in Ihrem lokalen Active Directory verwalten. Diese Gruppen können im Azure-Verwaltungsportal nicht verwaltet oder gelöscht werden.

Wenn Sie über Office 365 verfügen, können Sie Verteilergruppen und E-Mail-aktivierte Sicherheitsgruppen sehen, die im Exchange Admin Center in Office 365 erstellt und verwaltet wurden. Diese Gruppen haben den Wert "Office 365" in der Eigenschaft "Erstellt aus" und müssen weiterhin im Exchange Admin Center verwaltet werden.

Sie können über den Zugriffsbereich auch einheitliche Gruppen erstellen. Legen Sie unter "Gruppenrichtlinienverwaltung" auf der Registerkarte "Konfigurieren" das Widget **Benutzer können O365-Gruppen erstellen** auf **Ja** fest. Wenn Sie über einheitliche Office 365-Gruppen verfügen, die im Zugriffsbereich oder in Office 365 erstellt werden, ist die Eigenschaft "Erstellt aus" dieser Gruppen auf "Azure Active Directory" festgelegt, weshalb sie über den Zugriffsbereich verwaltet werden können.

Wenn Sie die Self-Service-Gruppenverwaltung für Ihre Benutzer aktiviert haben (weitere Informationen finden Sie unter "Self-Service-Gruppenverwaltung für Benutzer in Azure AD"), können Sie als Mandantenadministrator diese Gruppen auch über das Azure-Verwaltungsportal verwalten. Sie können Gruppenmitglieder hinzufügen und entfernen, Gruppenbesitzer hinzufügen und entfernen, Gruppeneigenschaften bearbeiten und den Verlaufsaktivitätsbericht von Gruppen anzeigen, der die in der Gruppe ausgeführte Aktion, denjenigen, der die Aktion ausgeführt hat, sowie die Uhrzeit anzeigt, wann die Aktion ausgeführt wurde.

> [AZURE.NOTE]Sie müssen Azure AD Premium verwenden, damit Sie eine Gruppe einer Anwendung zuweisen können. Wenn Sie über Azure AD Premium verfügen, können Sie Gruppen auch verwenden, um den Zugriff auf SaaS-Anwendungen zuzuweisen, die in Azure AD integriert sind. Weitere Informationen finden Sie unter "Zuweisen des Zugriffs auf eine SaaS-Anwendung für eine Gruppe in Azure AD".

## Nächste Schritte

- [Verwalten von Azure AD](active-directory-administer.md)
- [Erstellen oder Bearbeiten von Benutzern in Azure AD](active-directory-create-users.md)
- [Verwalten von Kennwörtern in Azure AD](active-directory-manage-passwords.md)

<!---HONumber=58--> 