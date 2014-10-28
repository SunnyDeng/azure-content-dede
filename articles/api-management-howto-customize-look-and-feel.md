<properties pageTitle="How to customize the look and feel of the developer portal in Azure API Management" metaKeywords="" description="How to customize the look and feel of the developer portal in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to customize the look and feel of the developer portal in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Anpassen des Erscheinungsbilds für das Entwicklerportal in der Azure API-Verwaltung

Farben, Schriftarten, Schriftgrößen und andere stilverwandte Elemente im Erscheinungsbild des Entwicklerportals werden durch Stilregeln definiert. Solche Regeln existieren für alle Strukturelemente einer Seite - Kopfzeile, Menü, Inhalt, Seitentitel usw.. In dieser Anleitung erfahren Sie, wie Sie die Stilregeln bearbeiten können.

Klicken Sie auf **Darstellung** im Menü **Entwicklerportal** im Veröffentlichungsportal, um die Stilregeln zu bearbeiten. Klicken Sie anschließend auf **Anpassung beginnen**, um den Stileditor zu aktivieren.

Ihr Browser wechselt zu einer versteckten Seite im Entwicklerportal mit Beispielen für die Inhalte unter Anwendungen aller Stilregeln, die irgendwo auf der Seite verwendet werden. Um den Stileditor zu öffnen, bewegen Sie Ihren Mauszeiger über die dünne vertikale graue Linie ganz links auf der Seite. Die Editor-Symbolleiste sollte angezeigt werden.

![Anpassungssymbolleiste][Anpassungssymbolleiste]

Für die Bearbeitung der Stilregeln gibt es zwei Bearbeitungsmodi - **Alle Regeln bearbeiten** zeigt eine Liste aller im Portal verwendeten Stilregeln an; und mit **Element auswählen** können Sie ein Element auf der aktuellen Seite auswählen, um nur die Stilregeln für dieses Element anzuzeigen.

In diesem Abschnitt werden wir den Stil für ein bestimmtes Element ändern, z. B. die Kopfzeile der Seite. Klicken Sie in der Stileditor-Symbolleiste auf **Element auswählen** und anschließend auf **Anzupassendes Element auswählen**. Die Elemente werden nun hervorgehoben, wenn Sie den Mauszeiger darüber bewegen, um anzuzeigen, welche Stilregeln des Elements Sie mit einem Klick anzeigen können.

Bewegen Sie die Maus über den Text mit dem Seitentitel in der Kopfzeile, und klicken Sie darauf. Eine Liste mit benannten und kategorisierten Stilregeln wird daraufhin im Stileditor angezeigt.

Jede Regel steht für eine Stileigenschaft des ausgewählten Elements. Der ausgewählte Kopfzeilentext hat z. B. den Schriftgrad @font-size-h1, und der Name der Schriftart mit Alternativen befindet sich in @headings-font-family.

> Falls Sie mit [bootstrap][<http://getbootstrap.com/>] vertraut sind, werden Sie feststellen, dass es sich bei diesen Regeln um [LESS-Variablen][<http://getbootstrap.com/css/>] aus dem Bootstrap-Design für das Entwicklerportal handelt.

Ändern Sie nun die Farbe der Überschrift. Wählen Sie den Eintrag im Feld <**@headings-color*>\* aus und geben Sie \#000000 ein. Dies ist der Hexadezimalcode für die Farbe Schwarz. Daraufhin sollte ein rechteckiger Farbindikator am Ende des Textfelds angezeigt werden. Wenn Sie diesen Indikator anklicken, können Sie mit einem Farbwähler eine Farbe auswählen.

![Farbauswahl][Farbauswahl]

Wenn Sie Ihre Änderungen an den Stilregeln des ausgewählten Elements abgeschlossen haben, klicken Sie auf **Änderungen anzeigen**, um das Resultat auf dem Bildschirm anzuzeigen. Zu diesem Zeitpunkt sind die Änderungen nur für Administratoren sichtbar. Um die Änderungen für alle Benutzer sichtbar zu machen, klicken Sie auf **Veröffentlichen** im Stileditor und bestätigen Sie die Änderungen.

![Veröffentlichungsformular][Veröffentlichungsformular]

> Um die Stilregeln der anderen Elemente auf der Seite zu ändern, führen Sie dieselben Schritte wie bei der Änderung der Kopfzeile aus. Klicken Sie auf **Element auswählen** im Stileditor, wählen Sie das gewünschte Element aus und ändern Sie die Werte der Stilregeln, die auf dem Bildschirm angezeigt werden.

  [Anpassungssymbolleiste]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
  [Farbauswahl]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
  [Veröffentlichungsformular]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png
