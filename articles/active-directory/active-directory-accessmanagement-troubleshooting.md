
<properties
	pageTitle="Problembehandlung bei dynamischen Mitgliedschaften in Gruppen | Microsoft Azure"
	description="Dieses Thema bietet Tipps zur Problembehandlung für dynamische Mitgliedschaften in Gruppen in Azure AD."
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


# Problembehandlung bei dynamischen Mitgliedschaften in Gruppen

| Symptom | Aktion |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ich habe eine Regel für eine Gruppe konfiguriert, die Mitgliedschaften in der Gruppe werden jedoch nicht aktualisiert. | Überprüfen Sie auf der Registerkarte "Konfigurieren", ob die Einstellung **Delegierte Gruppenverwaltung aktivieren** auf "Ja" festgelegt ist. Diese Einstellung ist für Sie nur sichtbar, wenn Sie als Benutzer angemeldet sind, dem eine Azure Active Directory Premium-Lizenz zugewiesen ist. Überprüfen Sie die Werte für Benutzerattribute in der Regel: Gibt es Benutzer, die die Regel erfüllen? |
| Ich habe eine Regel konfiguriert, aber jetzt werden die vorhandenen Mitglieder der Regel entfernt. | Dies entspricht dem erwarteten Verhalten. Vorhandene Mitglieder der Gruppe werden entfernt, wenn eine Regel aktiviert oder geändert wird. Die nach der Auswertung der Regel verbleibenden Benutzer werden der Gruppe als Mitglieder hinzugefügt. |
| Änderungen an der Mitgliedschaft werden nicht sofort angezeigt, wenn ich eine Regel hinzufüge oder ändere. Warum nicht? | Die dedizierte Auswertung von Mitgliedschaften erfolgt in regelmäßigen Abständen in einem asynchronen Hintergrundprozess. Die Dauer dieses Prozesses hängt von der Anzahl der Benutzer in Ihrem Verzeichnis und der Größe der Gruppe, die aufgrund der Regel erstellt wird, ab. Bei Verzeichnissen mit einer geringen Benutzeranzahl werden Änderungen der Gruppenmitgliedschaft üblicherweise innerhalb weniger Minuten angezeigt. Bei Verzeichnissen mit einer hohen Benutzeranzahl kann das Auffüllen 30 Minuten oder länger dauern. |

Diese Artikel enthalten zusätzliche Informationen zum Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)
* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
* [Was ist Azure Active Directory?](active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->