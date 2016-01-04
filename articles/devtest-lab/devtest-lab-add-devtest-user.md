    <properties
	pageTitle="Add owners and users to a DevTest Lab | Microsoft Azure"
	description="Securely add a user who is not in your subscription to your Azure DevTest Lab."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Hinzufügen von Besitzern und Benutzern zu einem DevTest Lab

## Übersicht

Zugriff auf ein DevTest Lab wird durch die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC) gesteuert. Suchen Sie im Azure-Vorschauportal nach [rollenbasierte Zugriffssteuerung in Azure (RBAC)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29), um mehr zu erfahren.

Sie gewähren über zwei Rollen Zugriff auf Ihr DevTest Lab:

 - **Besitzer**: Benutzer, denen die Rolle **Besitzer** auf der Ebene des Azure-Abonnements zugewiesen ist, haben vollständigen Zugriff auf das Lab, einschließlich der Verwaltungs- und Überwachungsfunktionen.

     >[AZURE.NOTE]Rollen vom Typ **Besitzer**, die auf anderen RBAC-Ebenen als der Abonnementebene zugewiesen sind, werden in DevTest Lab nicht unterstützt. Das Zuweisen eines Benutzers zur Rolle **Besitzer** wird in DevTest Lab nicht unterstützt.

 -  **DevTest Lab-Benutzer**: Benutzer, die der Rolle **DevTest Lab-Benutzer** zugewiesen sind, können virtuelle Computer im angegebenen Lab erstellen, aktualisieren und löschen. Benutzer können entweder *intern* (ein Mitglied von Azure Active Directory für das Abonnement) oder *extern* (ein Benutzer, der kein Mitglied von Azure AD ist, z. B. ein Mitglied einer Partnerorganisation) sein.
	-  Die Rolle **DevTest Lab-Benutzer** muss über die Kachel **Benutzer hinzufügen** des Labs zugewiesen werden.
	-  Benutzer mit der Rolle **DevTest Lab-Benutzer** können diese Vorgänge nur in dem Lab ausführen, dem sie zugewiesen sind. Beispiel: Ein **DevTest Lab-Benutzer** kann keinen virtuellen Computer mithilfe des Diensts für virtuelle Computer des Abonnements erstellen. Das Erstellen eines virtuellen Computers ist nur über das DevTest Lab-Konto zulässig.
	- *Externe* Benutzer müssen über ein Konto in einer der Microsoft-Kontodomänen verfügen (d. h. @hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com oder eine Variante für ein bestimmtes Land).

## Hinzufügen eines Besitzers zum Lab

Für DevTest Lab sind die Besitzer eines Azure-Abonnements, das Labs enthält, Besitzer dieser Labs. Zwar können Sie zusätzliche Besitzer für ein DevTest Lab über das Blatt für das Lab im Azure-Vorschauportal hinzufügen, dies wird derzeit aber nicht unterstützt.

Um einen Besitzer zu einem Azure-Abonnement hinzuzufügen, in dem Sie Labs bereits erstellt haben oder neue Labs erstellen werden, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Vorschauportal](http://portal.azure.com) an.

1. Tippen Sie im linken Navigationsbereich auf **Abonnements**.

	![Link zu Abonnements](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Tippen Sie auf das Abonnement, das die Labs enthalten soll.

1. Tippen Sie auf das Symbol **Zugriff**.

	![Auf Benutzer zugreifen](./media/devtest-lab-add-devtest-user/access-users.png)

1. Tippen Sie auf dem Blatt **Benutzer** auf **Hinzufügen**.

	![Benutzer hinzufügen](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Tippen Sie auf dem Blatt **Rolle auswählen** auf **Besitzer**.

1. Geben Sie in das Textfeld **Benutzer** die E-Mail-Adresse des Benutzers ein, den Sie als Besitzer hinzufügen möchten. Wenn der Benutzer nicht gefunden werden kann, erhalten Sie eine Fehlermeldung, die das Problem erklärt. Wenn der Benutzer gefunden wird, wird dieser Benutzer unter dem Textfeld **Benutzer** aufgeführt.

1. Tippen Sie auf den gefundenen Namen.

1. Tippen Sie auf **Auswählen**.

1. Tippen Sie auf **OK**, um das Blatt **Zugriff hinzufügen** zu schließen.

1. Wenn Sie zum Blatt **Benutzer** zurückkehren, sehen Sie, dass der Benutzer als Besitzer hinzugefügt wurde. Diese Person ist nun Besitzer aller Labs, die in diesem Abonnement erstellt werden, und sie kann daher die Aufgaben eines Besitzers ausführen.

## Hinzufügen eines DevTest Lab-Benutzers zum Lab

Um einen DevTest Lab-Benutzer zum Lab hinzuzufügen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Vorschauportal](http://portal.azure.com) an.

1. Tippen Sie auf **Durchsuchen**.

1. Tippen Sie auf **DevTest Labs**.

1. Tippen Sie in der Liste der Labs auf das gewünschte Lab.

1. Tippen Sie auf das Symbol **Zugriff**.

	![Benutzerzugriff](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Tippen Sie auf dem Blatt **Benutzer** auf **Hinzufügen**.

	![Benutzer hinzufügen](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Tippen Sie auf dem Blatt **Rolle auswählen** auf **DevTest Lab-Benutzer**.

1. Auf dem Blatt **Benutzer hinzufügen**:

	1. Auf dem Blatt **Benutzer hinzufügen** wird eine Liste der integrierten Benutzer angezeigt. Wenn der gewünschte Benutzer bereits in der Liste enthalten ist, können Sie einfach auf die Zeile mit dem Benutzer tippen, um ihn auszuwählen. Links neben dem Benutzer wird ein Häkchen angezeigt, um anzugeben, dass der Benutzer ausgewählt wurde. Um mehrere Benutzer auszuwählen, halten Sie **STRG** gedrückt, während Sie auf die einzelnen Benutzer klicken. Um einen Benutzer zu deaktivieren, halten Sie **STRG** gedrückt, und klicken Sie auf den Benutzer. Ein Zähler unten auf dem Blatt gibt die Anzahl der ausgewählten Benutzer an.

	1. Wenn der gewünschte Benutzer nicht in der Liste enthalten ist, geben Sie ein gültiges Microsoft-E-Mail-Konto in das Textfeld **Benutzer** ein. Wenn die E-Mail-Adresse gültig ist, wird der Benutzer unter dem Textfeld **Benutzer** angezeigt. Tippen Sie einfach darauf, um ihn auszuwählen.

	1. Nach der Auswahl der Benutzer, die Sie zum Lab hinzufügen möchten, tippen Sie auf **Auswählen**.

	1. Tippen Sie auf **OK**, um das Blatt **Zugriff hinzufügen** zu schließen.

	1. Auf dem Blatt **Benutzer** werden die hinzugefügten Rollen und Benutzer angezeigt.

<!---HONumber=Nov15_HO4-->