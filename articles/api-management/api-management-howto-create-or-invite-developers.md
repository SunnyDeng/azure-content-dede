<properties 
	pageTitle="Wie Verwalten von Benutzerkonten in Azure API Management" 
	description="Weitere Informationen zum Erstellen oder das Einladen von Benutzern in Azure API Management" 
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

# Verwalten von Benutzerkonten in Azure API Management

In API Management sind Entwickler die Benutzer der APIs, die Sie mit API Management verfügbar machen. Diese Anleitung beschreibt die Erstellung und Einladung von Entwicklern zur Nutzung Ihrer API und der Produkte, die Sie über die API Management-Instanz bereitgestellt haben.

## <a name="create-developer"> </a>Erstellen eines neuen Entwicklers

Klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**, um einen neuen Entwickler zu erstellen. Daraufhin gelangen Sie zum API Management-Herausgeberportal. Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][].

![Herausgeberportal][api-management-management-console]

Klicken Sie auf **Entwickler** im Menü **API Management** auf der linken Seite, und klicken Sie anschließend auf **Benutzer hinzufügen**.

![Entwickler erstellen][api-management-create-developer]

Geben Sie **E-Mail**, **Kennwort** und **Name** für den neuen Entwickler ein, und klicken Sie auf **Speichern**.

![Entwickler erstellen][api-management-add-new-user]

Neu erstellte Entwicklerkonten sind standardmäßig **Aktiv** und werden zur Gruppe **Entwickler** zugewiesen.

![Neuer Entwickler][api-management-new-developer]

Entwicklerkonten mit dem Status **Aktiv** können sämtliche APIs aufrufen, die sie abonniert haben. Informationen zum Zuordnen des neu erstellten Entwicklers zu weiteren Gruppen finden Sie unter [Zuordnen von Entwicklern zu Gruppen][].

## <a name="invite-developer"> </a>Einladen eines Entwicklers

Klicken Sie auf **Entwickler** im Menü **API Management** auf der linken Seite, und klicken Sie anschließend auf **Benutzer einladen**, um einen Entwickler einzuladen.

![Entwickler einladen][api-management-invite-developer]

Geben Sie Name und E-Mail-Adresse des Entwicklers ein, und klicken Sie auf **Einladen**.

![Entwickler einladen][api-management-invite-developer-window]

Eine Bestätigungsmeldung wird angezeigt. Der neu eingeladene Entwickler wird jedoch erst in der Liste angezeigt, wenn die Einladung angenommen wurde.

![Bestätigung der Einladung][api-management-invite-developer-confirmation]

Wenn Sie einen Entwickler einladen, wird eine E-Mail an diesen Entwickler geschickt. Diese E-Mail wird anhand einer Vorlage erstellt und ist konfigurierbar. Weitere Informationen finden Sie unter [E-Mail-Vorlagen konfigurieren][].

Nachdem die Einladung angenommen wurde, ist das Konto aktiv.

## <a name="block-developer"> </a>Deaktivieren oder erneutes Aktivieren eines Entwicklerkontos

Neu erstellte oder eingeladene Entwicklerkonten sind standardmäßig **Aktiv**. Klicken Sie auf **Blockieren**, um ein Entwicklerkonto zu deaktivieren. Klicken Sie auf **Aktivieren**, um ein blockiertes Entwicklerkonto erneut zu aktivieren. Blockierte Entwicklerkonten haben keinen Zugriff auf das Entwicklerportal und können keine APIs aufrufen.

![Entwickler blockieren][api-management-new-developer]

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie das Entwicklerkonto erstellt haben, können Sie das Konto zu Rollen zuweisen und Produkte und APIs abonnieren. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-]: ./media/api-management-howto-create-or-invite-developers/api-management-.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Erstellen und Verwenden von Gruppen]: api-management-howto-create-groups.md
[Zuordnen von Entwicklern zu Gruppen]: api-management-howto-create-groups.md#associate-group-developer

[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance
[E-Mail-Vorlagen konfigurieren]: api-management-howto-configure-notifications.md#email-templates

<!---HONumber=GIT-SubDir_Tue_AM_dede-->