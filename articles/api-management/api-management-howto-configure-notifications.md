<properties 
	pageTitle="Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management" 
	description="Erfahren Sie, wie Sie Benachrichtigungen und E-Mail-Vorlagen in Azure API Management konfigurieren." 
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
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management

API Management bietet die Möglichkeit, Benachrichtigungen über spezifische Ereignisse sowie die E-Mail-Vorlagen zu konfigurieren, die zur Kommunikation mit Administratoren und Entwicklern einer API Management-Instanz verwendet werden. Dieses Thema beschreibt die Konfiguration von Benachrichtigungen für verfügbare Ereignisse und bietet eine Übersicht über die Konfiguration der E-Mail-Vorlagen für diese Ereignisse.

## <a name="publisher-notifications"> </a>Konfigurieren von Herausgeberbenachrichtigungen

Um Benachrichtigungen zu konfigurieren, klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**. Daraufhin gelangen Sie zum API Management-Herausgeberportal.

![Herausgeberportal][api-management-management-console]

>Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit API Management][].

Klicken Sie auf **Benachrichtigungen** im Menü **API Management** auf der linken Seite, um die verfügbaren Benachrichtigungen anzuzeigen.

![Herausgeber-Benachrichtigungen][api-management-publisher-notifications]

Die folgenden Ereignisse können für Benachrichtigungen konfiguriert werden.

-	**Abonnementanfragen (mit ausstehender Genehmigung)** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen über Abonnementanfragen für API-Anfragen mit ausstehender Genehmigung.
-	**Neue Abonnements** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen über neue API-Produktabonnements.
-	**Anwendungsgalerie-Anfragen** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn neue Anwendungen in die Anwendungsgalerie übermittelt werden.
-	**BCC** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten BCC-Kopien aller E-Mails, die an Entwickler verschickt werden.
-	**Neues Problem oder Kommentar** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn neue Probleme oder Kommentare im Entwicklerportal erstellt werden.
-	**Kontoschließung** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn Konten geschlossen werden.
-	**Kurz vor Abonnement-Kontingent** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn sich die Abonnementnutzung dem Nutzungskontingent annähert.

Für jedes Ereignis können Sie bestimmte E-Mail-Empfänger in das Textfeld E-Mail-Adresse eingeben oder Benutzer aus einer Liste auswählen.

Geben Sie die E-Mail-Adressen einfach in das Textfeld ein, um die zu benachrichtigenden E-Mail-Adressen zu konfigurieren. Trennen Sie mehrere E-Mail-Adressen mit Kommas.

![Benachrichtigungsempfänger][api-management-email-addresses]

Um die zu benachrichtigenden Benutzer zu konfigurieren, klicken Sie auf **Empfänger hinzufügen**, markieren Sie die Kontrollkästchen neben den entsprechenden Benutzern, und klicken Sie auf **OK**.

>Beachten Sie, dass nur Administratoren in der Liste angezeigt werden.

Klicken Sie nach der Konfiguration der Benachrichtigungsempfänger auf **Speichern**, um die Änderungen zu übernehmen.

>Wenn Sie die Registerkarte **Herausgeber-Benachrichtigungen** verlassen, warnt Sie das API Management-Portal über nicht gespeicherte Änderungen.

## <a name="email-templates"> </a>Konfigurieren von E-Mail-Vorlagen

API Management enthält E-Mail-Vorlagen für die E-Mail-Nachrichten, die im Rahmen von Verwaltung und Nutzung des Diensts verschickt werden. Die folgenden E-Mail-Vorlagen werden angeboten.

-	Übermittlung in Anwendungsgalerie genehmigt
-	Entwickler-Abschiedsbrief
-	Entwickler-Nutzungskontingent beinahe erreicht
-	Benutzer einladen
-	Neuer Kommentar zu einem Problem
-	Neues Problem erhalten
-	Neues Abonnement aktiviert
-	Abonnementerneuerung Bestätigung
-	Abonnementerneuerung abgelehnt
-	Abonnementerneuerung erhalten

Diese Vorlagen können nach Belieben angepasst werden.

Um die E-Mail-Vorlagen für Ihre API Management-Instanz anzuzeigen, klicken Sie auf **Benachrichtigungen** im Menü **API Management** auf der linken Seite, und wählen Sie die Registerkarte **E-Mail-Vorlagen** aus.

![E-Mail-Vorlagen][api-management-email-templates]

Wählen Sie eine Vorlage aus der **Vorlagen**-Dropdownliste aus, um diese anzuzeigen oder zu bearbeiten.

![E-Mail-Vorlagenliste][api-management-email-templates-list]

Jede E-Mail-Vorlage enthält einen Betreff im Nur-Text-Format und einen Text im HTML-Format. Jedes dieser Elemente kann nach Belieben angepasst werden.

![Editor für E-Mail-Vorlagen][api-management-email-template]

Die **Parameter** aus der Liste können in den Betreff oder den Text eingefügt werden und werden beim Versand der E-Mail durch den entsprechenden Wert ersetzt. Um einen Parameter einzufügen, platzieren Sie den Cursor an die gewünschte Stelle, und klicken Sie auf den Pfeil links neben dem Parameternamen.

Klicken Sie auf **Vorschau** oder auf **Test senden**, um eine Vorschau der E-Mail zu sehen oder eine Test-E-Mail zu verschicken.

>Beachten Sie, dass die Parameter in der Vorschau und beim Testversand nicht durch tatsächliche Werte ersetzt werden.
>
>Klicken Sie auf **Speichern**, um die Änderungen an der E-Mail-Vorlage zu speichern, oder auf **Abbrechen**, um die Änderungen zu verwerfen.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Erste Schritte mit API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance

<!---HONumber=Oct15_HO3-->