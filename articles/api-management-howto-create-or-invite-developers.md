<properties pageTitle="How manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to create or invite developers in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Verwalten von Entwicklerkonten in der Azure API-Verwaltung

Entwickler sind in der API-Verwaltung (Vorschau) die Benutzer der APIs, die Sie über die API-Verwaltung veröffentlicht haben. Diese Anleitung beschreibt die Erstellung und Einladung von Entwicklern zur Nutzung Ihrer API und der Produkte, die Sie über die API-Verwaltungsinstanz bereitgestellt haben.

## In diesem Thema

-   [Erstellen eines neuen Entwicklers][Erstellen eines neuen Entwicklers]
-   [Einladen eiens Entwicklers][Einladen eiens Entwicklers]
-   [Deaktivieren oder erneutes Aktivieren eines Entwicklerkontos][Deaktivieren oder erneutes Aktivieren eines Entwicklerkontos]
-   [Nächste Schritte][Nächste Schritte]

## <a name="create-developer"> </a>Erstellen eines neuen Entwicklers

Klicken Sie im Azure-Portal für Ihren API-Verwaltungsdienst auf **Verwaltungskonsole**, um einen neuen Entwickler zu erstellen. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![API-Verwaltungskonsole][API-Verwaltungskonsole]

Klicken Sie auf **Entwickler** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **Benutzer hinzufügen**.

![Entwickler erstellen][Entwickler erstellen]

Geben Sie **E-Mail**, **Kennwort** und **Name** für den neuen Entwickler ein und klicken Sie auf **Speichern**.

![Entwickler erstellen][1]

Neu erstellte Entwicklerkonten sind standardmäßig **Aktiv** und werden zur Gruppe **Entwickler** zugewiesen.

![Neuer Entwickler][Neuer Entwickler]

Entwicklerkonten mit dem Status **Aktiv** können sämtliche APIs aufrufen, die sie abonniert haben. Informationen zum Zuordnen des neu erstellten Entwicklers zu weiteren Gruppen finden Sie unter [Zuordnen von Entwicklern zu Gruppen][Zuordnen von Entwicklern zu Gruppen].

## <a name="invite-developer"> </a>Einladen eiens Entwicklers

Klicken Sie auf **Entwickler** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **Benutzer einladen**, um einen Entwickler einzuladen.

![Entwickler einladen][Entwickler einladen]

Geben Sie Name und E-Mail-Adresse des Entwicklers ein und klicken Sie auf **Einladen**.

![Entwickler einladen][2]

Eine Bestätigungsmeldung wird angezeigt. Der neu eingeladene Entwickler wird jedoch erst in der Liste angezeigt, wenn die Einladung angenommen wurde.

![Bestätigung der Einladung][Bestätigung der Einladung]

> Wenn Sie einen Entwickler einladen, wird eine E-Mail an diesen Entwickler geschickt. Diese E-Mail wird anhand einer Vorlage erstellt und ist konfigurierbar. Weitere Informationen finden Sie unter [E-Mail-Vorlagen konfigurieren][E-Mail-Vorlagen konfigurieren].

Nachdem die Einladung angenommen wurde, ist das Konto aktiv.

## <a name="block-developer"> </a>Deaktivieren oder erneutes Aktivieren eines Entwicklerkontos

Neu erstellte oder eingeladene Entwicklerkonten sind standardmäßig **Aktiv**. Klicken Sie auf **Blockieren**, um ein Entwicklerkonto zu deaktivieren. Klicken Sie auf **Aktivieren**, um ein blockiertes Entwicklerkonto erneut zu aktivieren. Blockierte Entwicklerkonten haben keinen Zugriff auf das Entwicklerportal und können keine APIs aufrufen.

![Entwickler blockieren][Neuer Entwickler]

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie das Entwicklerkonto erstellt haben, können Sie das Konto zu Rollen zuweisen und Produkte und APIs abonnieren. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen][Erstellen und Verwenden von Gruppen].

  [Erstellen eines neuen Entwicklers]: #create-developer
  [Einladen eiens Entwicklers]: #invite-developer
  [Deaktivieren oder erneutes Aktivieren eines Entwicklerkontos]: #block-developer
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [API-Verwaltungskonsole]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Entwickler erstellen]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [Neuer Entwickler]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [Zuordnen von Entwicklern zu Gruppen]: ../api-management-howto-create-groups/#associate-group-developer
  [Entwickler einladen]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Bestätigung der Einladung]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [E-Mail-Vorlagen konfigurieren]: ../api-management-howto-configure-notifications/#email-templates
  [Erstellen und Verwenden von Gruppen]: ../api-management-howto-create-groups
