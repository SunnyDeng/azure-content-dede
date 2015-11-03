<properties
	pageTitle="Anpassen des Entwicklerportals in Azure API Management | Microsoft Azure"
	description="Erfahren Sie, wie Sie das Entwicklerportal in Azure API Management anpassen."
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
	ms.topic="get-started-article"
	ms.date="10/27/2015"
	ms.author="sdanie"/>

# Anpassen des Entwicklerportals in Azure API Management

Diese Anleitung beschreibt, wie Sie das Erscheinungsbild des Entwicklerportals in Azure API Management an Ihre Marke anpassen können.

## <a name="change-page-headers"> </a>Ändern des Texts oder Logos in der Kopfzeile

Einer der Schlüsselaspekte der Portalanpassung ist die Möglichkeit, den Text am oberen Rand aller Seiten durch Ihren Firmennamen oder Ihr Logo zu ersetzen.

Die Inhalte im Entwicklerportal werden über das Herausgeberportal geändert, das im Azure-Portal aufgerufen werden kann. Um auf das API-Herausgeberportal zuzugreifen, klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**.

![Herausgeberportal][api-management-management-console]

Das Entwicklerportal basiert auf einem Content Management-System oder CMS. Bei der auf jeder Seite angezeigten Kopfzeile handelt es sich um einen Inhaltstyp, der auch Widget genannt wird. Um den Inhalt des Widgets zu bearbeiten, klicken Sie auf **Widgets** im Menü **Entwicklerportal** auf der linken Seite, und wählen Sie das Widget **Header** in der Liste aus.

![Kopfzeilen-Widget][api-management-widgets-header]

Sie können den Inhalt der Fußzeile im Feld **Body** bearbeiten. Ändern Sie den Text in "Fabrikam-Entwicklerportal", und klicken Sie dann unten in der Seite auf **Speichern**.

Die neue Kopfzeile wird jetzt auf allen Seiten im Entwicklerportal angezeigt.

> Klicken Sie in der oberen Leiste auf **Entwicklerportal**, um das Entwicklerportal aus dem Herausgeberportal zu öffnen.

## <a name="change-headers-styling"> </a>Ändern des Stils der Kopfzeilen

Farben, Schriftarten, Schriftgrößen, Abstände und andere stilverwandte Elemente aller Portalseiten werden durch Stilregeln definiert. Aktivieren Sie zum Bearbeiten der Stile den Stil-Editor, indem Sie im Menü **Entwicklerportal** des Herausgeberportals auf **Darstellung** und dann auf **Anpassung beginnen** klicken.

Ihr Browser wechselt zu einer versteckten Seite im Entwicklerportal mit Beispielen für die Inhalte unter Anwendung aller Stilregeln, die irgendwo auf der Website verwendet werden. Um den Stileditor zu öffnen, bewegen Sie Ihren Mauszeiger über die dünne vertikale graue Linie ganz links auf der Seite. Die Editor-Symbolleiste sollte angezeigt werden.

![Anpassungssymbolleiste][api-management-customization-toolbar]

Es gibt zwei Hauptmodi der Bearbeitung von Stilregeln: Mit **Alle Regeln bearbeiten** wird eine Liste sämtlicher an beliebiger Stelle verwendeten Stilregeln angezeigt. Mit **Element auswählen** können Sie hingegen ein Element auf der aktuellen Seite auswählen, um nur Stile für dieses Element anzuzeigen.

In diesem Abschnitt werden wir nur den Stil für die Kopfzeile ändern. Klicken Sie in der Symbolleiste des Stil-Editors auf **Element auswählen** und anschließend auf **Anzupassendes Element auswählen**. Die Elemente werden nun hervorgehoben, wenn Sie den Mauszeiger darüber bewegen, um das Element anzugeben, dessen Stile Sie bearbeiten, wenn Sie darauf klicken. Bewegen Sie die Maus über den Text mit dem Firmennamen in der Kopfzeile ("Fabrikam-Entwicklerportal", wenn Sie die Anweisungen im vorigen Abschnitt ausgeführt haben), und klicken Sie darauf. Eine Liste mit benannten und kategorisierten Stilregeln wird daraufhin im Stil-Editor angezeigt.

Jede Regel steht für eine Stileigenschaft des ausgewählten Elements. Der ausgewählte Kopfzeilentext hat z. B. den Schriftgrad @font-size-h1, und der Name der Schriftart mit Alternativen befindet sich in @headings-font-family.

> Falls Sie mit [Bootstrap][] vertraut sind, werden Sie feststellen, dass es sich bei diesen Regeln um [LESS-Variablen][] aus dem Bootstrap-Design für das Entwicklerportal handelt.

Ändern Sie nun die Farbe der Überschrift. Wählen Sie den Eintrag im Feld **@headings-color** aus, und geben Sie **#000000** ein. Dies ist der Hexadezimalcode für die Farbe Schwarz. Daraufhin wird am Ende des Textfelds ein rechteckiger Farbindikator angezeigt. Wenn Sie auf diesen Indikator klicken, können Sie in einer Farbauswahl eine Farbe auswählen.

![Farbauswahl][api-management-customization-toolbar-color-picker]

Wenn Sie die Änderungen an den Stilen des ausgewählten Elements abgeschlossen haben, klicken Sie auf **Vorschau der Änderungen anzeigen**, um das Resultat auf dem Bildschirm anzuzeigen. Zu diesem Zeitpunkt sind die Änderungen nur für Administratoren sichtbar. Um die Änderungen für alle Benutzer sichtbar zu machen, klicken Sie im Stil-Editor auf **Veröffentlichen**, und bestätigen Sie die Änderungen.

![Veröffentlichungsmenü][api-management-customization-toolbar-publish-form]

> Wenn Sie die Stilregeln für ein anderes Element auf der Seite ändern möchten, gehen Sie auf die gleiche Weise wie für die Kopfzeile vor. Klicken Sie im Stil-Editor auf **Element auswählen**, wählen Sie das betreffende Element aus, und ändern Sie die Werte der auf dem Bildschirm angezeigten Stilregeln.

## <a name="edit-page-contents"> </a>Bearbeiten der Seiteninhalte

Das Entwicklerportal besteht aus automatisch generierten Seiten, z. B. "APIs", "Produkte", "Anwendungen", "Probleme" und manuell erstellten Inhalten. Da das Portal auf einem Content Management-System basiert, können Sie diese Inhalte jederzeit erstellen.

Klicken Sie im Menü **Entwicklerportal** des Herausgeberportals auf **Inhalte**, um eine Liste aller vorhandenen Inhaltsseiten anzuzeigen.

![Inhalt verwalten][api-management-customization-manage-content]

Klicken Sie auf die Willkommensseite, um den Inhalt zu bearbeiten, der auf der Startseite des Entwicklerportals angezeigt wird. Nehmen Sie die gewünschten Änderungen vor, zeigen Sie die Änderungen in einer Vorschau an, und klicken Sie anschließend auf **Jetzt veröffentlichen**, um sie für alle Benutzer sichtbar zu machen.

> Die Startseite verwendet ein spezielles Layout, mit dem ein Banner am oberen Rand angezeigt werden kann. Dieses Banner kann nicht im Abschnitt **Inhalt** bearbeitet werden. Um dieses Banner zu bearbeiten, klicken Sie im **Entwicklerportal** auf **Widgets**, wählen Sie in der Dropdownliste **Aktuelle Ebene** die Ebene **Startseite** aus, und öffnen Sie dann im Abschnitt **Ausgewählte** das Element **Banner**. Sie können den Inhalt dieses Widgets ebenso wie alle anderen Seiten bearbeiten.

## <a name="next-steps"> </a>Nächste Schritte

-	Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][].

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Erste Schritte bei der erweiterten API-Konfiguration]: api-management-get-started-advanced.md
[bootstrap]: http://getbootstrap.com/
[LESS-Variablen]: http://getbootstrap.com/css/

<!---HONumber=Nov15_HO1-->