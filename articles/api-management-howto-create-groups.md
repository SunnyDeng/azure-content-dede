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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung

Die API-Verwaltung (Vorschau) verwendet Gruppen, um die Sichtbarkeit von Produkten für Entwickler zu verwalten. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind.

Die API-Verwaltung enthält die folgenden integrierten Gruppen.

-   **Administratoren** - Administratoren verwalten API-Verwaltungs-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
-   **Entwickler** - Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum Entwicklerportal und erstellen Anwendungen, die die Operationen einer API aufrufen.
-   **Gäste** - Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API-Verwaltungsinstanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z. B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Neben diesen integrierten Gruppen können Administratoren auch eigene Gruppen erstellen. Benutzerdefinierte Gruppen haben dieselben Berechtigungen wie die integrierte Gruppe der Entwickler und dienen zur Verwaltung unterschiedlicher Gruppen von Entwicklern. Sie können z. B. eine Gruppe für Entwickler erstellen, die die APIs eines bestimmten Produkts verwenden, und eine andere Gruppe für Entwickler, die die APIs eines anderen Produkts verwenden.

Diese Anleitung beschreibt, wie Administrator einer API-Verwaltungsinstanz neue Gruppen hinzufügen und diese zu Produkten und Entwicklern zuordnen können.

## In diesem Thema

-   [Erstellen einer Gruppe][Erstellen einer Gruppe]
-   [Zuordnen einer Gruppe zu einem Produkt][Zuordnen einer Gruppe zu einem Produkt]
-   [Zuordnen von Gruppen zu Entwicklern][Zuordnen von Gruppen zu Entwicklern]
-   [Nächste Schritte][Nächste Schritte]

## <a name="create-group"> </a>Erstellen einer Gruppe

Klicken Sie im Azure-Portal für Ihren API-Verwaltungsdienst auf **Verwaltungskonsole**, um eine neue Gruppe zu erstellen. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![API-Verwaltungskonsole][API-Verwaltungskonsole]

Klicken Sie auf **Gruppen** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **Gruppe hinzufügen**.

![Neue Gruppe hinzufügen][Neue Gruppe hinzufügen]

Geben Sie einen eindeutigen Namen und eine optionale Beschreibung für die Gruppe ein und klicken Sie auf **Speichern**.

![Neue Gruppe hinzufügen][1]

Die neue Gruppe wird auf der Registerkarte „Gruppen“ angezeigt. Klicken Sie auf den Namen der Gruppe in der Liste, um deren **Namen** oder **Beschreibung** zu bearbeiten. Klicken Sie auf **Löschen**, um die Gruppe zu löschen.

![Gruppe hinzugefügt][Gruppe hinzugefügt]

Nachdem Sie die Gruppe erstellt haben, können Sie sie zu Produkten und Entwicklern zuordnen.

## <a name="associate-group-product"> </a>Zuordnen einer Gruppe zu einem Produkt

Um eine Gruppe zu einem Produkt zuzuordnen, klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite und anschließend auf den Namen des gewünschten Produkts.

![Sichtbarkeit einstellen][Sichtbarkeit einstellen]

Auf der Registerkarte **Sichtbarkeit** können Sie Gruppen hinzufügen oder entfernen und die aktuellen Gruppen für das Produkt anzeigen. Um Gruppen hinzuzufügen oder zu entfernen, markieren Sie die Kontrollkästchen für die jeweiligen Gruppen und klicken Sie auf **Speichern**.

![Sichtbarkeit einstellen][2]

> Klicken Sie auf **Gruppen verwalten**, um Gruppen aus der Registerkarte **Sichtbarkeit** für ein Produkt zu konfigurieren.

Sobald ein Produkt zu einer Gruppe zugeordnet ist, können Entwickler in dieser Gruppe das Produkt anzeigen und abonnieren.

## <a name="associate-group-developer"> </a>Zuordnen von Gruppen zu Entwicklern

Um eine Gruppen zu Entwicklern zuzuordnen, klicken Sie auf **Entwickler** im Menü **API-Verwaltung** auf der linken Seite und markieren Sie die Kontrollkästchen neben den Entwicklern, die Sie zu der Gruppe zuordnen möchten.

![Entwickler zu einer Gruppe hinzufügen][Entwickler zu einer Gruppe hinzufügen]

Markieren Sie die gewünschten Entwickler und klicken Sie anschließend auf die entsprechende Gruppe in der Dropdownloste **Zu Gruppe hinzufügen**. Über die Dropdownliste **Aus Gruppe entfernen** können Sie Entwickler aus Gruppen entfernen.

![Entwickler][Entwickler]

Sobald Sie die Zuordnung zwischen Entwickler und Gruppe erstellt haben, können Sie diese auf der Registerkarte **Entwickler** anzeigen.

## <a name="next-steps"> </a>Nächste Schritte

Sobald ein Entwickler zu einer Gruppe hinzugefügt wurde, können diese die zu dieser Gruppe zugeordneten Produkte anzeigen und abonnieren. Weitere Informationen finden Sie unter [Erstellen und Veröffentlichen eines Produkts in der Azure API-Verwaltung][Erstellen und Veröffentlichen eines Produkts in der Azure API-Verwaltung].

  [Erstellen einer Gruppe]: #create-group
  [Zuordnen einer Gruppe zu einem Produkt]: #associate-group-product
  [Zuordnen von Gruppen zu Entwicklern]: #associate-group-developer
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [API-Verwaltungskonsole]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Neue Gruppe hinzufügen]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Gruppe hinzugefügt]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Sichtbarkeit einstellen]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Entwickler zu einer Gruppe hinzufügen]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [Entwickler]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
  [Erstellen und Veröffentlichen eines Produkts in der Azure API-Verwaltung]: ../api-management-howto-add-products

<!--HONumber=46--> 
