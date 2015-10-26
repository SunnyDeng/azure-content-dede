
<properties
	pageTitle="Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen | Microsoft Azure"
	description="Erfahren Sie, wie Sie in Azure AD Premium oder Basic Gruppen verwenden, um den Zugriff auf SaaS-Anwendungen zuzuweisen, die in Azure AD integriert sind."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na" 
	ms.topic="article"
	ms.date="10/09/2015"
	ms.author="femila"/>


# Verwenden einer Gruppe zum Verwalten des Zugriffs auf SaaS-Anwendungen

Mit Azure AD Premium können Sie Gruppen verwenden, um Zugriff auf eine SaaS-Anwendung zu gewähren, die in Azure AD integriert ist. Wenn Sie beispielsweise der Marketingabteilung Zugriff auf fünf verschiedene SaaS-Anwendungen erteilen möchten, können Sie eine Gruppe erstellen, die die Benutzer der Marketingabteilung enthält, und diese Gruppe dann diesen fünf SaaS-Anwendungen zuweisen, die von der Marketingabteilung benötigt werden. Auf diese Weise sparen Sie Zeit, da Sie die Mitgliedschaft für die Marketingabteilung zentral verwalten können. Benutzer werden der Anwendung zugewiesen, wenn sie als Mitglieder der Marketinggruppe hinzugefügt werden. Ebenso wird die Zuweisung der Benutzer zur Anwendung entfernt, wenn sie als Mitglieder aus der Marketinggruppe entfernt werden.

Diese Funktion kann für Hunderte von Anwendungen verwendet werden, die Sie aus dem Azure AD-Anwendungskatalog hinzufügen können.

**So weisen Sie einer Gruppe den Zugriff auf eine SaaS-Anwendung zu**


1. Öffnen Sie einen Browser Ihrer Wahl, und wechseln Sie zum Azure-Verwaltungsportal. Suchen Sie im Azure-Verwaltungsportal auf der Navigationsleiste links nach der Active Directory-Erweiterung. Klicken Sie auf der Registerkarte **Verzeichnis** auf das Verzeichnis, in dem Sie einer Gruppe den Zugriff auf eine SaaS-Anwendung zuweisen möchten.


2. Klicken Sie auf die Registerkarte "Anwendungen" für Ihr Verzeichnis. Klicken Sie auf eine Anwendung, die Sie aus dem Anwendungskatalog hinzugefügt haben, und klicken Sie dann auf die Registerkarte **Benutzer und Gruppen**.

3. Geben Sie auf der Registerkarte "Benutzer und Gruppen" im Feld "Beginnend mit" den Namen der Gruppe ein, der Sie Zugriff zuweisen möchten, und klicken Sie auf das Häkchen in der oberen rechten Ecke. Sie müssen nur den ersten Teil des Gruppennamens eingeben. Klicken Sie auf die Gruppe, um sie zu markieren, klicken Sie auf die Schaltfläche **Zugriff zuweisen**, und klicken Sie auf **Ja**, wenn die Bestätigungsmeldung angezeigt wird.


4. Sie können auch sehen, welche Benutzer der Anwendung entweder direkt oder durch die Mitgliedschaft in einer Gruppe zugewiesen sind. Ändern Sie hierzu die Anzeige der Dropdownliste **Gruppen** zu **Alle Benutzer**. Die Liste zeigt die Benutzer im Verzeichnis und zeigt an, ob ein Benutzer der Anwendung zugewiesen ist oder nicht. Die Liste zeigt außerdem an, ob die Benutzer der Anwendung direkt (angezeigter Zuweisungstyp "Direkt") oder aufgrund der Gruppenmitgliedschaft (angezeigter Zuweisungstyp "Geerbt") zugewiesen sind.


> [AZURE.NOTE]Die Registerkarte "Benutzer und Gruppen" wird nur angezeigt, wenn Sie Azure AD Premium oder Azure AD Basic aktiviert haben.

Hier sind einige Themen aufgeführt, die zusätzliche Informationen zu Azure Active Directory bieten.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->