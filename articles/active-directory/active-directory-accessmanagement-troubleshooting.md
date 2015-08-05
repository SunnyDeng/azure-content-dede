
<properties 
	pageTitle="Problembehandlung bei dynamischen Mitgliedschaften in Gruppen | Microsoft Azure" 
	description="Dieses Thema bietet Tipps zur Problembehandlung für dynamische Mitgliedschaften in Gruppen in Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor="Curtis"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Problembehandlung bei dynamischen Mitgliedschaften in Gruppen

| Symptom | Aktion |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ich habe eine Regel für eine Gruppe konfiguriert, die Mitgliedschaften in der Gruppe werden jedoch nicht aktualisiert. | Überprüfen Sie auf der Registerkarte "Konfigurieren", ob die Einstellung "Delegierte Gruppenverwaltung aktivieren" auf "Ja" festgelegt ist. Beachten Sie, dass diese Einstellung nur angezeigt wird, wenn Sie mit einem Konto angemeldet sind, dem eine Azure Active Directory Premium-Lizenz zugewiesen ist. Überprüfen Sie die Werte für Benutzerattribute in der Regel – gibt es Benutzer, die die Regel erfüllen? |
| Ich habe eine Regel konfiguriert, aber jetzt werden die vorhandenen Mitglieder der Regel entfernt. | Dies ist ein erwartetes Verhalten: Vorhandene Mitglieder der Gruppe werden entfernt, wenn eine Regel aktiviert oder geändert wird. Die aus der Auswertung der Regel resultierenden Benutzer werden der Gruppe als Mitglieder hinzugefügt. |
| Änderungen an der Mitgliedschaft werden nicht sofort angezeigt, wenn ich eine Regel hinzufüge oder ändere. Warum nicht? | Die dedizierte Auswertung von Mitgliedschaften erfolgt in regelmäßigen Abständen in einem asynchronen Hintergrundprozess. Die Anzahl der Benutzer in Ihrem Mandanten und die Größe der Gruppe, die aufgrund der Regel erstellt wird, wirken sich darauf aus, wie lange dieser Prozess dauert. Bei Mandanten mit einer geringen Benutzeranzahl werden Änderungen der Gruppenmitgliedschaft üblicherweise innerhalb weniger Minuten angezeigt. Bei Mandanten mit einer hohen Benutzeranzahl kann das Auffüllen 30 Minuten oder länger dauern. |

Hier finden Sie einige Themen, die zusätzliche Informationen zu Azure Active Directory bieten:

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->