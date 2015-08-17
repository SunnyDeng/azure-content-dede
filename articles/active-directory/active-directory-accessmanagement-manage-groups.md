<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="Covers how to sign up for Azure and first steps you can try with Azure AD."
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


#Verwalten von Sicherheitsgruppen in Azure Active Directory


##Erstellen und Verwalten einer Sicherheitsgruppe

**So erstellen Sie eine Gruppe im Azure-Verwaltungsportal**

1. Klicken Sie im Verwaltungsportal auf "Active Directory", und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf die Registerkarte **Gruppen**.
3. Klicken Sie auf der Seite "Gruppen" auf **Gruppe hinzufügen**.
4. Geben Sie im Fenster **Gruppe hinzufügen** den Namen und die Beschreibung einer Gruppe ein.
5. Diese Aufgabe kann entweder unter Verwendung des Office 365-Kontoportals, des Windows Intune-Kontoportals oder des Azure-Verwaltungsportals ausgeführt werden, je nachdem, welche Dienste Ihre Organisation abonniert hat. Weitere Informationen zur Verwendung von Portalen zum Verwalten von Azure Active Directory finden Sie unter "Verwalten Ihres Azure AD-Verzeichnisses".

## Zuweisen von Benutzern zu einer Sicherheitsgruppe oder Entfernen von Benutzern aus einer Sicherheitsgruppe

**So fügen Sie im Azure-Verwaltungsportal ein Mitglied zu einer Gruppe hinzu**

1. Klicken Sie im Verwaltungsportal auf "Active Directory", und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf die Registerkarte **Gruppen**.
3. Klicken Sie auf der Seite **Gruppen** auf den Namen der Gruppe, der Sie Mitglieder hinzufügen möchten. Standardmäßig wird hier die Registerkarte **Mitglieder** der ausgewählten Gruppe angezeigt.
4. Klicken Sie auf der Seite dieser Gruppe auf **Mitglieder hinzufügen**.
5. Klicken Sie auf der Seite **Mitglieder hinzufügen** auf den Namen des Benutzers oder einer Gruppe, den bzw. die Sie als Mitglied dieser Gruppe hinzufügen möchten, und stellen Sie sicher, dass dieser Name dem Bereich "Ausgewählt" hinzugefügt wird.


**So entfernen Sie im Azure-Verwaltungsportal ein Mitglied aus einer Gruppe**

1. Klicken Sie im Verwaltungsportal auf "Active Directory", und klicken Sie dann auf den Namen des Verzeichnisses Ihrer Organisation.
2. Klicken Sie auf die Registerkarte **Gruppen**.
3. Klicken Sie auf der Seite "Gruppen" auf den Namen der Gruppe, aus der Sie Mitglieder entfernen möchten.
4. Klicken Sie auf der Seite dieser Gruppe auf die Registerkarte **Mitglieder**.
5. Klicken Sie auf der Seite dieser Gruppe auf den Namen des Mitglieds, das Sie aus dieser Gruppe entfernen möchten, und klicken Sie dann auf**Entfernen**.
6. Überprüfen Sie, ob Sie dieses Mitglied aus der Gruppe entfernen möchten, indem Sie bei der Überprüfungsfrage für die Aktion auf **Ja** klicken.


##Verwenden einer Regel zur dynamischen Verwaltung der Mitglieder einer Sicherheitsgruppe
**Zur Aktivierung der dynamischen Mitgliedschaft für eine bestimmte Gruppe führen Sie die folgenden Schritte aus:**

1. Wählen Sie im Azure-Verwaltungsportal auf der Registerkarte **Gruppen** die Gruppe aus, die Sie bearbeiten möchten, und legen Sie dann auf der Registerkarte **Konfigurieren** für diese Gruppe die Option **Dynamische Mitgliedschaften aktivieren** auf **Ja** fest.
2. Jetzt können Sie eine einfache Regel für die Gruppe einrichten, um die Funktionsweise dynamischer Mitgliedschaften für diese Gruppe zu steuern. Stellen Sie sicher, dass das Optionsfeld **Benutzer hinzufügen, wobei** aktiviert ist, und wählen Sie dann aus dem Pulldownmenü eine Benutzereigenschaft (z. B. Abteilung, Position usw.) aus. 
3. Anschließend wählen Sie eine Bedingung (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) und geben zum Schluss den Wert für die ausgewählte Benutzereigenschaft an.
4. Beispiel: Wenn eine Gruppe einer SaaS-Anwendung zugewiesen ist (weitere Informationen hierzu finden Sie unter "Zuweisen des Zugriffs auf eine SaaS-Anwendung für eine Gruppe in Azure AD") und Sie die dynamische Mitgliedschaft für diese Gruppe aktivieren, indem Sie eine Regel einrichten, in der"Benutzer hinzufügen, wobei" auf die Position festgelegt ist, für die "Equals(-eq)Sales Rep" gilt, erhalten alle Benutzer in Ihrem Azure AD-Verzeichnis Zugriff auf diese SaaS-Anwendung, deren jobTitle-Eigenschaft auf "Sales Rep" festgelegt ist.

Hier finden Sie einige Themen, die zusätzliche Informationen zu Azure Active Directory bieten:

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->