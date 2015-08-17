<properties 
	pageTitle="Dedizierte Gruppen in Azure Active Directory | Microsoft Azure" 
	description="Ein Thema mit Erläuterungen zum Verwalten von Gruppen in Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

# Dedizierte Gruppen in Azure Active Directory

In Azure Active Directory werden dedizierte Gruppen automatisch erstellt, und die Gruppenmitgliedschaft für die dedizierten Gruppen erfolgt ebenfalls automatisch. Sie können keine Mitglieder zu dedizierten Gruppen hinzufügen oder daraus entfernen, weder über das Azure-Verwaltungsportal noch mit Windows PowerShell-Cmdlets oder programmgesteuert. Legen Sie zum Aktivieren dedizierter Gruppen im Azure-Verwaltungsportal auf der Registerkarte "Konfigurieren" den Schalter "Zugeordnete Gruppen aktivieren" auf "Ja" fest.

Sobald in der aktuellen öffentlichen Vorschauversion der Schalter "Zugeordnete Gruppen aktivieren" auf "Ja" festgelegt ist, können Sie das Verzeichnis für das automatische Erstellen der dedizierten Gruppe "Alle Benutzer" aktivieren, indem Sie den Schalter "Gruppe 'Alle Benutzer' aktivieren" auf "Ja" festlegen. Sie können den Namen dieser dedizierten Gruppe bearbeiten, indem Sie Text in das Feld "Anzeigename für Gruppe 'Alle Benutzer'" eingeben.

Die dedizierte Gruppe "Alle Benutzer" ist hilfreich, wenn Sie allen Benutzer in Ihrem Verzeichnis die gleichen Berechtigungen zuweisen möchten. Beispielsweise können Sie allen Benutzern in Ihrem Verzeichnis Zugriff auf eine SaaS-Anwendung gewähren, indem Sie der dedizierten Gruppe "Alle Benutzer" Zugriff auf diese Anwendung zuweisen.

Hier finden Sie einige Themen, die zusätzliche Informationen zu Azure Active Directory bieten:

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->