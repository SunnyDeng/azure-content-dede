<properties
	pageTitle="Erstellen einer einfachen Regel zum Konfigurieren von dynamischen Mitgliedschaften für eine Gruppe | Microsoft Azure"
	description="Erklärt, wie Sie eine einfache Regel zum Konfigurieren von dynamischer Mitgliedschaften für eine Gruppe erstellen."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Erstellen einer einfachen Regel zum Konfigurieren von dynamischen Mitgliedschaften für eine Gruppe

Zur Aktivierung der dynamischen Mitgliedschaft für eine bestimmte Gruppe führen Sie die folgenden Schritte aus:

1. Wählen Sie im Azure-Portal auf der Registerkarte **Gruppen** die Gruppe aus, die Sie bearbeiten möchten, und legen Sie dann auf der Registerkarte **Konfigurieren** für diese Gruppe die Option **Dynamische Mitgliedschaften aktivieren** auf **Ja** fest.

2. Jetzt können Sie eine einfache Regel für die Gruppe einrichten, um die Funktionsweise dynamischer Mitgliedschaften für diese Gruppe zu steuern. Stellen Sie sicher, dass die Option **Benutzer hinzufügen, wobei** ausgewählt ist, und wählen Sie dann aus der Liste eine Benutzereigenschaft (z. B. Abteilung, Position usw.) aus.

3. Anschließend wählen Sie eine Bedingung (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) und geben zum Schluss den Wert für die ausgewählte Benutzereigenschaft an. Wenn beispielsweise eine Gruppe einer SaaS-Anwendung zugewiesen ist und Sie die dynamische Mitgliedschaft für diese Gruppe aktivieren, indem Sie eine Regel einrichten, in der **Benutzer hinzufügen, wobei** auf die Position festgelegt ist, für die "Equals(-eq)Sales Rep" gilt, erhalten alle Benutzer in Ihrem Azure AD-Verzeichnis Zugriff auf diese SaaS-Anwendung, deren Eigenschaft "Position" auf "Sales Rep" festgelegt ist. 4. Beachten Sie, dass Sie eine Regel für dynamische Mitgliedschaft für Sicherheits- oder Office-Gruppen einrichten können.

Diese Artikel enthalten zusätzliche Informationen zum Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
* [Was ist Azure Active Directory?](active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->