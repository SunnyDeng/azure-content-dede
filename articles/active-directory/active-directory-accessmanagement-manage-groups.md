<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
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
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="curtand"/>


# Verwalten von Gruppen in Azure Active Directory

Eines der wichtigsten Features von Azure Active Directory (Azure AD) ist die Möglichkeit, den Zugriff auf Ressourcen zu verwalten. Bei diesen Ressourcen kann es sich um Objekte im Verzeichnis oder um Ressourcen handeln, die außerhalb des Verzeichnisses vorliegen, z.B. SaaS-Anwendungen, Azure-Dienste, SharePoint-Websites oder lokale Ressourcen. Darüber hinaus kann ein Ressourcenbesitzer für eine Azure AD-Gruppe den Zugriff auf eine Ressource zuweisen. Den Mitgliedern dieser Gruppe wird so Zugriff auf die Ressource gewährt. Der Besitzer der Gruppe verwaltet dann die Mitgliedschaft in der Gruppe. Der Besitzer der Ressource delegiert quasi die Berechtigung zum Zuweisen von Benutzern zu dieser Gruppe an den Besitzer der Gruppe.

## Wie erstelle ich eine Sicherheitsgruppe?

**So erstellen Sie eine Gruppe**

Diese Aufgabe kann unter Verwendung des Office 365-Kontoportals, des Windows Intune-Kontoportals oder des Azure-Portals ausgeführt werden – je nach den Diensten, die Ihre Organisation abonniert hat. Weitere Informationen zur Verwendung von Portalen zum Verwalten von Azure Active Directory finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](active-directory-administer.md).

1. Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) die Option **Active Directory**, und wählen Sie dann den Namen des Verzeichnisses Ihrer Organisation.

2. Wählen Sie die Registerkarte **Gruppen**.

3. Wählen Sie **Gruppe hinzufügen**.

4. Geben Sie im Fenster **Gruppe hinzufügen** den Namen und die Beschreibung einer Gruppe ein.


## Wie kann ich einzelne Benutzer einer Sicherheitsgruppe hinzufügen oder diese daraus entfernen?

**So fügen Sie einer Gruppe einen einzelnen Benutzer hinzu**

1. Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) die Option **Active Directory**, und wählen Sie dann den Namen des Verzeichnisses Ihrer Organisation.

2. Wählen Sie die Registerkarte **Gruppen**.

3. Öffnen Sie die Gruppe, der Sie Mitglieder hinzufügen möchten. Standardmäßig wird hier die Registerkarte **Mitglieder** der ausgewählten Gruppe angezeigt.

4. Wählen Sie **Mitglieder hinzufügen**.

5. Wählen Sie auf der Seite **Mitglieder hinzufügen** den Namen des Benutzers oder einer Gruppe, den bzw. die Sie als Mitglied dieser Gruppe hinzufügen möchten, und stellen Sie sicher, dass dieser Name dem Bereich **Ausgewählt** hinzugefügt wird.


**So entfernen Sie einen einzelnen Benutzer aus einer Gruppe**

1. Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) die Option **Active Directory**, und wählen Sie dann den Namen des Verzeichnisses Ihrer Organisation.

2. Wählen Sie die Registerkarte **Gruppen**.

3. Öffnen Sie die Gruppe, aus der Sie Mitglieder entfernen möchten.

4. Wählen Sie die Registerkarte **Mitglieder**, klicken Sie auf der Seite dieser Gruppe auf den Namen des Mitglieds, das Sie aus dieser Gruppe entfernen möchten, und klicken Sie dann auf **Entfernen**.

6. Bestätigen Sie in der Aufforderung, dass Sie dieses Mitglied aus der Gruppe entfernen möchten.


## Wie kann ich die Mitgliedschaft einer Gruppe dynamisch verwalten?

In Azure AD können Sie leicht eine einfache Regel einrichten (bei der nur ein einzelner Vergleich durchgeführt wird), um zu ermitteln, welche Benutzer Mitglieder der Gruppe sein sollen. Wenn eine Gruppe beispielsweise einer SaaS-Anwendung zugewiesen wird und Sie eine Regel einrichten, mit der Benutzer mit der Position „Vertriebsmitarbeiter“ hinzugefügt werden, haben alle Benutzer im Azure AD-Verzeichnis, die über diese Position verfügen, Zugriff auf die SaaS-Anwendung.

> [AZURE.NOTE] Sie können eine Regel für die dynamische Mitgliedschaft für Sicherheits- oder Office 365-Gruppen einrichten. Geschachtelte Gruppenmitgliedschaften werden für die gruppenbasierte Zuordnung zu Anwendungen derzeit nicht unterstützt. Für dynamische Gruppenmitgliedschaften ist es erforderlich, dass eine Azure AD Premium-Lizenz wie folgt zugewiesen ist:
>- Dem Administrator, der die Regel für eine Gruppe verwaltet
>- Allen Benutzern, die mit der Regel als Mitglied der Gruppe ausgewählt werden

**So aktivieren Sie die dynamische Mitgliedschaft für eine Gruppe**

1. Wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) die Option **Active Directory**, und wählen Sie dann den Namen des Verzeichnisses Ihrer Organisation.

2. Wählen Sie die Registerkarte **Gruppen**, und öffnen Sie die Gruppe, die Sie bearbeiten möchten.

3. Wählen Sie die Registerkarte **Konfigurieren**, und legen Sie dann **Dynamische Mitgliedschaften aktivieren** auf **Ja** fest.

4. Richten Sie eine einfache Regel für die Gruppe ein, um die Funktionsweise dynamischer Mitgliedschaften für diese Gruppe zu steuern. Stellen Sie sicher, dass die Option **Benutzer hinzufügen, wobei** ausgewählt ist, und wählen Sie dann aus der Liste eine Benutzereigenschaft (z. B. Abteilung, Position usw.) aus.

5. Anschließend wählen Sie eine Bedingung (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) und geben zum Schluss den Wert für die ausgewählte Benutzereigenschaft an.

Weitere Informationen zur Erstellung *erweiterter* Regeln (mit mehreren Vergleichen) für dynamische Gruppenmitgliedschaften finden Sie unter [Verwenden von Attributen zum Erstellen erweiterter Regeln](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Zusätzliche Informationen

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

* [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)

* [Was ist Azure Active Directory?](active-directory-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0330_2016-->