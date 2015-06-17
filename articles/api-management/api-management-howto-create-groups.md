<properties 
	pageTitle="Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung" 
	description="Erfahren Sie, wie Sie Entwicklerkonten in Azure API Management mithilfe von Konten verwalten." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung

Die API-Verwaltung verwendet Gruppen, um die Sichtbarkeit von Produkten für Entwickler zu verwalten. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind.

API Management umfasst die folgenden unveränderlichen Systemgruppen.

-	**Administratoren** – Azure-Abonnementadministratoren sind Mitglieder dieser Gruppe. Administratoren verwalten API Management-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
-	**Entwickler** – Zu dieser Gruppe gehören authentifizierte Benutzer des Entwicklerportals Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum Entwicklerportal und erstellen Anwendungen, die die Operationen einer API aufrufen.
-	**Gäste** – Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API Management-Instanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z. B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Zusätzlich zu diesen Systemgruppen können Administratoren benutzerdefinierte Gruppen erstellen oder [Nutzung von externen Gruppen in Azure Active Directory-Mandanten zugeordneten](api-management-howto-aad.md/#how-to-add-an-external-azure-active-directory-group). Benutzerdefinierte und externen Gruppen können neben Systemgruppen erteilen Entwickler Sichtbarkeit und den Zugriff auf API-Produkte verwendet werden. Sie könnten beispielsweise erstellen eine benutzerdefinierte Gruppe für Entwickler, die mit einem bestimmten Partnerunternehmen verbundenen und authentifiziertem Zugriff auf die APIs von einem Produkt mit nur die entsprechenden APIs. Ein Benutzer kann Mitglied mehrerer Gruppen sein.

Diese Anleitung beschreibt, wie Administrator einer API-Verwaltungsinstanz neue Gruppen hinzufügen und diese zu Produkten und Entwicklern zuordnen können.

## <a name="create-group"> </a>Eine Gruppe erstellen

Um eine neue Gruppe zu erstellen, klicken Sie auf **verwalten** in Azure-Portal für Ihre API Management-Dienst. Daraufhin gelangen Sie zum API Management-Herausgeberportal.

![Herausgeberportal][api-management-management-console]

>Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][].

Klicken Sie auf **Gruppen** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **Gruppe hinzufügen**.

![Neue Gruppe hinzufügen][api-management-add-group]

Geben Sie einen eindeutigen Namen und eine optionale Beschreibung für die Gruppe ein und klicken Sie auf **Speichern**.

![Neue Gruppe hinzufügen][api-management-add-group-window]

Die neue Gruppe wird auf der Registerkarte „Gruppen“ angezeigt. Klicken Sie auf den Namen der Gruppe in der Liste, um deren **Namen** oder **Beschreibung** zu bearbeiten. Um die Gruppe zu löschen, klicken Sie auf **Löschen**.

![Gruppe hinzugefügt][api-management-new-group]

Nachdem Sie die Gruppe erstellt haben, können Sie sie zu Produkten und Entwicklern zuordnen.

## <a name="associate-group-product"> </a>Zuordnen eine Gruppe mit einem Produkt

Um eine Gruppe zu einem Produkt zuzuordnen, klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite und anschließend auf den Namen des gewünschten Produkts.

![Sichtbarkeit einstellen][api-management-add-group-to-product]

Auf der Registerkarte **Sichtbarkeit** können Sie Gruppen hinzufügen oder entfernen und die aktuellen Gruppen für das Produkt anzeigen. Um Gruppen hinzuzufügen oder zu entfernen, markieren Sie die Kontrollkästchen für die jeweiligen Gruppen und klicken Sie auf **Speichern**.

![Sichtbarkeit einstellen][api-management-add-group-to-product-visibility]

>[AZURE.NOTE]Azure Active Directory-Gruppen finden Sie unter [das Autorisieren des entwicklerkonten mithilfe von Azure Active Directory in Azure API Management](api-management-howto-aad.md).
>
>Klicken Sie auf **Gruppen verwalten**, um Gruppen aus der Registerkarte **Sichtbarkeit** für ein Produkt zu konfigurieren.

Sobald ein Produkt zu einer Gruppe zugeordnet ist, können Entwickler in dieser Gruppe das Produkt anzeigen und abonnieren.

## <a name="associate-group-developer"> </a>Entwickler Gruppen zuordnen

Um Entwicklern Gruppen zuzuordnen, klicken Sie auf **Benutzer** aus der **API Management** im Menü auf der linken Seite und dann die Kontrollkästchen neben den Entwicklern, die Sie einer Gruppe zuordnen möchten.

![Entwickler zu einer Gruppe hinzufügen][api-management-add-group-to-developer]

Markieren Sie die gewünschten Entwickler und klicken Sie anschließend auf die entsprechende Gruppe in der Dropdownloste **Zu Gruppe hinzufügen**. Über die Dropdownliste **Aus Gruppe entfernen** können Sie Entwickler aus Gruppen entfernen.

![Entwickler][api-management-add-group-to-developer-saved]

Nachdem die Zuordnung zwischen dem Entwickler und der Gruppe hinzugefügt wurde, können sie in der **Benutzer** Registerkarte.

## <a name="next-steps"> </a>Nächste Schritte

Sobald ein Entwickler zu einer Gruppe hinzugefügt wurde, können diese die zu dieser Gruppe zugeordneten Produkte anzeigen und abonnieren. Weitere Informationen finden Sie unter [Erstellen und Veröffentlichen eines Produkts in der Azure API-Verwaltung][].


[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[Erstellen und Veröffentlichen eines Produkts in der Azure API-Verwaltung]: api-management-howto-add-products.md

[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance

<!---HONumber=GIT-SubDir_Tue_AM_dede-->