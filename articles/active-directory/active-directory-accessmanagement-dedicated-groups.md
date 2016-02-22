<properties
	pageTitle="Dedizierte Gruppen in Azure Active Directory | Microsoft Azure"
	description="Übersicht über die Funktionsweise dedizierter Gruppen in Azure Active Directory und ihrer Erstellung."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>

# Dedizierte Gruppen in Azure Active Directory

In Azure Active Directory werden dedizierte Gruppen automatisch erstellt, und die Gruppenmitgliedschaft für die dedizierten Gruppen erfolgt ebenfalls automatisch. Sie können keine Mitglieder zu dedizierten Gruppen hinzufügen oder daraus entfernen, weder über das Azure-Portal noch mit Windows PowerShell-Cmdlets oder programmgesteuert. Legen Sie zum Aktivieren dedizierter Gruppen im Azure-Portal auf der Registerkarte "Konfigurieren" den **Schalter "Zugeordnete Gruppen aktivieren" auf "Ja"** fest.

Sobald der Schalter "Zugeordnete Gruppen aktivieren" auf **Ja** festgelegt ist, können Sie das Verzeichnis für das automatische Erstellen der dedizierten Gruppe **"Alle Benutzer" aktivieren**, indem Sie den Schalter "Gruppe 'Alle Benutzer' aktivieren" auf** Ja** festlegen. Sie können den Namen dieser dedizierten Gruppe bearbeiten, indem Sie Text in das Feld **Anzeigename für Gruppe "Alle Benutzer "** eingeben.

Die dedizierte Gruppe "Alle Benutzer" ist hilfreich, wenn Sie allen Benutzer in Ihrem Verzeichnis die gleichen Berechtigungen zuweisen möchten. Beispielsweise können Sie allen Benutzern in Ihrem Verzeichnis Zugriff auf eine SaaS-Anwendung gewähren, indem Sie der dedizierten Gruppe "Alle Benutzer" Zugriff auf diese Anwendung zuweisen.

Diese Artikel enthalten zusätzliche Informationen zum Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
* [Was ist Azure Active Directory?](active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->