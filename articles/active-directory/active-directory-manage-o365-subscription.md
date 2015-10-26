<properties
   pageTitle="Verwalten des Verzeichnisses für Ihr Office 365-Abonnement in Azure"
   description="Verwalten eines Office 365-Abonnement-Kontoverzeichnisses mithilfe von Azure Active Directory und dem Management-Portal"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="curtand"/>

#Verwalten des Verzeichnisses für Ihr Office 365-Abonnement in Azure

Dieser Artikel beschreibt, wie ein Verzeichnis verwaltet wird, das für ein Office 365-Abonnement im Azure-Verwaltungsportal erstellt wurde. Die Schritte zum Ausführen dieser Aufgabe variieren, je nachdem, ob Sie bereits über ein Azure-Abonnement verfügen. Sie müssen der Dienstadministrator oder Co-Administrator eines Azure-Abonnements sein, um sich am Azure-Verwaltungsportal anzumelden.

Wenn Sie noch nicht über ein Azure-Abonnement verfügen, müssen Sie sich lediglich mit Ihrem Geschäfts- oder Schulkonto anmelden, mit dem Sie sich auch bei Office 365 anmelden.

![](./media/active-directory-manage-o365-subscription/AAD_O365_01.png)

Ein entsprechendes Abonnement für Azure wird nicht gefunden werden, Sie können jedoch auf **Für Azure registrieren** klicken, sodass relevante Informationen aus dem Office 365-Konto wieder in das Anmeldeformular eingetragen werden. Das gleiche Konto wird standardmäßig der Rolle des Dienstadministrators zugewiesen.

![](./media/active-directory-manage-o365-subscription/AAD_O365_02.png)

Nach Abschluss des Azure-Abonnements können Sie sich am Azure-Verwaltungsportal anmelden und auf Azure-Dienste zugreifen. Klicken Sie auf die Active Directory-Erweiterung, um das gleiche Verzeichnis zu verwalten, das Ihre Office 365-Benutzer authentifiziert.

Wenn Sie bereits über ein Azure-Abonnement verfügen, ist der Prozess zum Verwalten eines weiteren Verzeichnisses ebenfalls ganz einfach. Das folgende Diagramm veranschaulicht diesen Prozess.

In diesem Beispiel hat Michael Smith ein Office 365-Abonnement für "contoso.com". Er hat ein Azure-Abonnement, für das er sich mit seinem Microsoft-Konto registriert hat, msmith@hotmail.com. In diesem Fall verwaltet er zwei Verzeichnisse.

| Abonnement | Office 365 | Azure |
|  -------------- | ------------- | ------------------------------- |
| Anzeigename | Contoso | Standardverzeichnis |
| Domänenname | contoso.com | msmithhotmail.onmicrosoft.com |

Er möchte die Benutzeridentitäten im Verzeichnis "Contoso" verwalten, während er mit seinem Microsoft-Konto bei Azure angemeldet ist, damit er Azure AD-Funktionen wie die mehrstufige Authentifizierung aktivieren kann.

![](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

In diesem Fall sind die beiden Verzeichnisse unabhängig voneinander.

##Zwei unabhängige Verzeichnisse verwalten
Damit Michael Smith beide Verzeichnisse verwalten kann, während er in Azure als msmith@hotmail.com angemeldet ist, muss er die folgenden Schritte ausführen:

> [AZURE.NOTE]Diese Schritte können nur ausgeführt werden, wenn ein Benutzer mit einem Microsoft-Konto angemeldet ist. Wenn der Benutzer mit einem Geschäfts- oder Schulkonto angemeldet ist, steht die Option **Vorhandenes Verzeichnis verwenden** nicht zur Verfügung. Ein Geschäfts- oder Schulkonto kann nur mithilfe seines Stammverzeichnisses authentifiziert werden (dies ist das Verzeichnis, in dem das Geschäfts- oder Schulkonto gespeichert wird, das im Besitz der Organisation oder Schule ist).

1.	Melden Sie sich beim Azure-Verwaltungsportal als msmith@hotmail.com an.
2.	Klicken Sie auf **Neu** -> **App Services** -> **Active Directory** -> **Verzeichnis** -> **Benutzerdefiniert erstellen**.
3.	Klicken Sie auf „Vorhandenes Verzeichnis verwenden“, und wählen Sie das Kontrollkästchen **Ich bin jetzt für die Abmeldung bereit**.
4.	Melden Sie sich beim Azure-Verwaltungsportal als globaler Administrator von Contoso.onmicrosoft.com (z. B. msmith@contoso.com)) an.
5.	Wenn Sie aufgefordert werden, **das Verzeichnis "Contoso" mit Azure zu verwenden**, klicken Sie auf **Weiter**.
6.	Klicken Sie auf **Jetzt abmelden**.
7.	Melden Sie sich im Verwaltungsportal als msmith@hotmail.com an. Das Verzeichnis "Contoso" und das Standardverzeichnis werden in der Active Directory-Erweiterung angezeigt.

Nach Abschluss dieser Schritte ist msmith@hotmail.com ein globaler Administrator im Verzeichnis "Contoso".

##Verwalten von Ressourcen als globaler Administrator
Nehmen wir nun an, dass John Doe sich am Azure-Verwaltungsportal anmelden und die Webseiten und Datenbankressourcen verwalten muss, die dem Azure-Abonnement von msmith@hotmail.com zugeordnet sind. Zu diesem Zweck muss Michael Smith folgende zusätzliche Schritte ausführen:

1.	Melden Sie sich mithilfe des Dienstadministratorkontos des Azure-Abonnements am Verwaltungsportal an (in diesem Beispiel msmith@hotmail.com)).
2.	Übertragen Sie das Abonnement in das Verzeichnis "Contoso": Klicken Sie auf **Einstellungen** > **Abonnements** > wählen Sie das Abonnement > **Verzeichnis bearbeiten** > wählen Sie **Contoso (Contoso.com)**. Alle Geschäfts- oder Schulonten, die Co-Administratoren des Abonnements sind, werden als Teil der Übertragung entfernt.
3.	Fügen Sie John Doe als Co-Administrator zum Abonnement hinzu: Klicken Sie auf **Einstellungen** > **Administratoren**> wählen Sie das Abonnement > **Hinzufügen** > Typ ****JohnDoe@Contoso.com**.

##Nächste Schritte
Weitere Informationen über die Beziehung zwischen Abonnements und Verzeichnissen finden Sie unter [Zuordnung zwischen einem Abonnement und einem Verzeichnis](active-directory-how-subscriptions-associated-directory.md).

<!---HONumber=Oct15_HO3-->