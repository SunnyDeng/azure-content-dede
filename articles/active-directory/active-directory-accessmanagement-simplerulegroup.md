<properties
	pageTitle="Erstellen einer einfachen Regel zum Konfigurieren von dynamischen Mitgliedschaften für eine Gruppe | Microsoft Azure"
	description="Erklärt, wie Sie eine einfache Regel zum Konfigurieren von dynamischer Mitgliedschaften für eine Gruppe erstellen."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Erstellen einer einfachen Regel zum Konfigurieren von dynamischen Mitgliedschaften für eine Gruppe

**Zur Aktivierung der dynamischen Mitgliedschaft für eine bestimmte Gruppe führen Sie die folgenden Schritte aus:**

1. Wählen Sie im Azure-Verwaltungsportal auf der Registerkarte **Gruppen** die Gruppe aus, die Sie bearbeiten möchten, und legen Sie dann auf der Registerkarte **Konfigurieren** für diese Gruppe die Option **Dynamische Mitgliedschaften aktivieren** auf **Ja** fest.


2. Jetzt können Sie eine einfache Regel für die Gruppe einrichten, um die Funktionsweise dynamischer Mitgliedschaften für diese Gruppe zu steuern. Stellen Sie sicher, dass das Optionsfeld **Benutzer hinzufügen, wobei** aktiviert ist, und wählen Sie dann aus dem Pulldownmenü eine Benutzereigenschaft (z. B. Abteilung, Position usw.) aus.

3. Anschließend wählen Sie eine Bedingung (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) und geben zum Schluss den Wert für die ausgewählte Benutzereigenschaft an. Beispiel: Wenn eine Gruppe einer SaaS-Anwendung zugewiesen ist und Sie die dynamische Mitgliedschaft für diese Gruppe aktivieren, indem Sie eine Regel einrichten, in der **Benutzer hinzufügen, wobei** auf die Position festgelegt ist, für die "Equals(-eq)Sales Rep" gilt, erhalten alle Benutzer in Ihrem Azure AD-Verzeichnis Zugriff auf diese SaaS-Anwendung, deren jobTitle-Eigenschaft auf "Sales Rep" festgelegt ist.

Hier finden Sie einige Themen, die zusätzliche Informationen zu Azure Active Directory bieten:

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->