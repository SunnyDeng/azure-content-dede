<properties
	pageTitle="Vorschau der Azure Active Directory-Domänendienste: Erste Schritte | Microsoft Azure"
	description="Erste Schritte mit Azure Active Directory-Domänendiensten"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)* – Erste Schritte

Dieser Artikel erläutert die erforderlichen Konfigurationsschritte zum Aktivieren von Azure AD-Domänendiensten für Ihren Azure AD-Mandanten.

## Schritt 1: Erstellen der Gruppe "AAD DC Administrators"
Im ersten Schritt wird eine administrative Gruppe in Ihrem Azure Active Directory-Mandanten erstellt. Diese spezielle administrative Gruppe heißt **AAD DC Administrators**. Mitgliedern dieser Gruppe werden Administratorrechte auf Computern erteilt, die in die Domäne eingebunden sind, die Sie mit den Azure Active Directory-Domänendiensten einrichten. Nach dem Beitritt zu einer Domäne wird diese Gruppe der Gruppe "Administratoren" auf diesen Computern der Domäne hinzugefügt. Mitglieder dieser Gruppe können darüber hinaus auch mithilfe von Remotedesktop Remoteverbindungen mit den in die Domäne eingebundenen Computern herstellen.

> [AZURE.NOTE]Domänenadministrator- oder Unternehmensadministratorberechtigungen innerhalb der Domäne, die mithilfe der Azure Active Directory-Domänendienste erstellt wurde, können nicht genutzt werden. Da dies eine verwaltete Domäne ist, sind diese Berechtigungen durch den Dienst reserviert und stehen Benutzern innerhalb des Mandanten nicht zur Verfügung. Sie können jedoch mithilfe dieser speziellen Administratorgruppe, die in diesem Konfigurationsschritt erstellt wurde, einige privilegierte Vorgänge durchführen, wie z. B. das Hinzufügen von Computern zur Domäne, das Nutzen der Zugehörigkeit zur Administratorgruppe auf in die Domäne eingebundenen Computern, das Konfigurieren der Gruppenrichtlinie usw.

In diesem Konfigurationsschritt erstellen Sie die Gruppe und fügen der Gruppe einen oder mehrere Benutzer in Ihrem Mandanten hinzu. Führen Sie die folgenden Schritte durch, um die Administratorgruppe für Azure Active Directory-Domänendienste zu erstellen:

1. Navigieren Sie zum **Azure-Verwaltungsportal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Wählen Sie den Knoten **Active Directory** im linken Bereich aus.
3. Wählen Sie den Azure AD-Mandanten (Verzeichnis) aus, für den Sie Azure AD-Domänendienste aktivieren möchten. Beachten Sie, dass Sie für jedes Azure AD-Verzeichnis nur eine Domäne erstellen können.

    ![Auswählen des Azure AD-Verzeichnisses](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Klicken Sie auf die Registerkarte **Gruppen**.
5. Klicken Sie im Aufgabenbereich am unteren Rand der Seite auf **Gruppe hinzufügen**, um Ihrem Verzeichnis eine Gruppe hinzuzufügen.
6. Erstellen Sie eine Gruppe mit dem Namen **AAD DC Administrators**.

    > [AZURE.WARNING]Sie müssen eine Gruppe mit exakt diesem Namen erstellen, um den Zugriff innerhalb von Azure Active Directory-Domänendiensten zu aktivieren.

	![Erstellen der Administratorgruppe](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Sie können eine Beschreibung für diese Gruppe hinzufügen, damit andere Benutzer innerhalb Ihres Azure AD-Mandanten wissen, dass diese Gruppe zum Erteilen von Administratorberechtigungen innerhalb der Azure Active Directory-Domänendienste verwendet wird.
8. Nachdem die Gruppe erstellt wurde, klicken Sie auf den Namen der Gruppe, um deren Eigenschaften anzuzeigen. Klicken Sie im unteren Bereich auf die Schaltfläche **Mitglieder hinzufügen**, um Benutzer als Mitglieder dieser Gruppe hinzuzufügen.
9. Wählen Sie im Dialogfeld **Mitglieder hinzufügen** die Benutzer aus, die Mitglieder dieser Gruppe werden sollen, und wählen Sie das Häkchen aus, wenn Sie fertig sind.

    ![Hinzufügen von Benutzern zur Administratorgruppe](./media/active-directory-domain-services-getting-started/add-group-members.png)

---
[**Nächster Schritt: Erstellen oder Auswählen eines virtuellen Azure-Netzwerks.**](active-directory-ds-getting-started-vnet.md)

<!---HONumber=Oct15_HO3-->