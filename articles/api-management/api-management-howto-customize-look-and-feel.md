<properties 
	pageTitle="Anpassen von Aussehen und Verwalten des Entwicklerportals in Azure API Management" 
	description="Anpassen des Erscheinungsbilds für das Entwicklerportal in Azure API Management" 
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

# Anpassen von Aussehen und Verwalten des Entwicklerportals in Azure API Management

Farben, Schriftarten, Schriftgrößen und andere Aspekte in Bezug auf Erscheinungsbild und Verhalten des Entwicklerportals werden durch Stilregeln definiert. Solche Regeln gibt es für alle Strukturelemente einer Seite – Kopfzeile, Menü, Inhalt, Seitentitel usw. In dieser Anleitung erfahren Sie, wie Sie die Stilregeln bearbeiten können.

Um Stilregeln zu bearbeiten, klicken Sie im Menü **Entwicklerportal** des Herausgeberportals auf **Darstellung**. Klicken Sie anschließend auf **Anpassung beginnen**, um den Stil-Editor zu aktivieren.

Ihr Browser wechselt zu einer versteckten Seite im Entwicklerportal mit Beispielen für die Inhalte unter Anwendungen aller Stilregeln, die irgendwo auf der Seite verwendet werden. Um den Stil-Editor zu öffnen, bewegen Sie Ihren Mauszeiger über die dünne vertikale graue Linie ganz links auf der Seite. Die Symbolleiste des Editors sollte wie folgt angezeigt werden:

![Anpassungssymbolleiste][api-management-customization-toolbar]

Für die Bearbeitung der Stilregeln gibt es zwei Bearbeitungsmodi – **Alle Regeln bearbeiten** zeigt eine Liste aller im Portal verwendeten Stilregeln an; und mit **Element auswählen** können Sie ein Element auf der aktuellen Seite auswählen, um nur die Stilregeln für dieses Element anzuzeigen.

In diesem Abschnitt werden wir den Stil für ein bestimmtes Element ändern, z. B. die Kopfzeile der Seite. Klicken Sie in der Symbolleiste des Stil-Editors auf **Element auswählen** und anschließend auf **Anzupassendes Element auswählen**. Die Elemente werden nun hervorgehoben, wenn Sie den Mauszeiger darüber bewegen, um anzuzeigen, welche Stilregeln des Elements Sie mit einem Klick anzeigen können.

Bewegen Sie die Maus über den Text mit dem Seitentitel in der Kopfzeile, und klicken Sie darauf. Eine Liste mit benannten und kategorisierten Stilregeln wird daraufhin im Stil-Editor angezeigt.

Jede Regel steht für eine Stileigenschaft des ausgewählten Elements. Der ausgewählte Kopfzeilentext hat z. B. den Schriftgrad @font-size-h1, und der Name der Schriftart mit Alternativen befindet sich in @headings-font-family.

> Falls Sie mit [Bootstrap](http://getbootstrap.com/) vertraut sind, werden Sie feststellen, dass es sich bei diesen Regeln um [LESS-Variablen](http://getbootstrap.com/css/) aus dem Bootstrap-Design für das Entwicklerportal handelt.

Ändern wir nun die Farbe der Überschrift. Wählen Sie den Eintrag im Feld **@headings-color** aus, und geben Sie \#000000 ein. Dies ist der Hexadezimalcode für die Farbe Schwarz. Daraufhin sollte ein rechteckiger Farbindikator am Ende des Textfelds angezeigt werden. Wenn Sie diesen Indikator anklicken, können Sie mit einem Farbwähler eine Farbe auswählen.

![Farbauswahl][api-management-customization-toolbar-color-picker]

Wenn Sie Ihre Änderungen an den Stilregeln des ausgewählten Elements abgeschlossen haben, klicken Sie auf **Änderungen anzeigen**, um das Resultat auf dem Bildschirm anzuzeigen. Zu diesem Zeitpunkt sind die Änderungen nur für Administratoren sichtbar. Um die Änderungen für alle Benutzer sichtbar zu machen, klicken Sie auf **Veröffentlichen** im Stil-Editor und bestätigen Sie die Änderungen.

![Veröffentlichungsformular][api-management-customization-toolbar-publish-form]

> Um die Stilregeln der anderen Elemente auf der Seite zu ändern, führen Sie dieselben Schritte wie bei der Änderung der Kopfzeile aus. Klicken Sie auf **Element auswählen** im Stil-Editor, wählen Sie das gewünschte Element aus und ändern Sie die Werte der Stilregeln, die auf dem Bildschirm angezeigt werden.


[Next steps]: #next-steps

[Management Portal]: https://manage.windowsazure.com/

[api-management-customization-toolbar]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!---HONumber=GIT-SubDir_Tue_AM_dede-->