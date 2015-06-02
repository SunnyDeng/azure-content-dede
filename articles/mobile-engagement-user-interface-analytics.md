<properties 
   pageTitle="Benutzeroberfläche von Azure Mobile Engagement - Analyse" 
   description="Informationen zum Analysieren von Verlaufsdaten zu Ihrer Anwendung mithilfe von Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# Gewusst wie: Analysieren von Verlaufsdaten zu Ihrer Anwendung
Der Abschnitt "Analyse" der Benutzeroberfläche enthält basierend auf Verlaufsdaten zusammengefasste Informationen zu Ihren Anwendungen, die alle 24 Stunden aktualisiert werden. Die Informationen werden auf verschiedenen Dashboards angezeigt, die aus Linien-/Balken-/Kreisdiagrammen, Rastern und Karten bestehen. Die Daten können auch als CSV-Dateien heruntergeladen werden. Die meisten dieser Informationen sind im Abschnitt "Überwachen" der Benutzeroberfläche in Echtzeit verfügbar. Ein Zugriff ist auch über die Analytics-API möglich. Das "Glossar" in "Konzepte" enthält die Definitionen der Begriffe und Abkürzungen in Analysen und Überwachung, wie z. B. die folgenden: Aktiver Benutzer, Neuer Benutzer, Benutzer beibehalten, Sitzung, Benutzerpfadgraph, Benutzer zuordnen, Nachverfolgen von URLs, Trends, Aktivität, Ereignis, Auftrag, Fehler, Zusätzliche Informationen, Absturz und App-Info.

### Weitere Informationen
-  [Konzepte - Glossar][Link 6], [Handbuch zur Problembehandlung - Analyse][Link 21]

## Standard- und benutzerdefinierte Analyse
Azure Mobile Service bietet eine Reihe grundlegender standardmäßiger Analyseinformationen zu Ihren Anwendungen, die grafisch dargestellt werden können, sobald Sie Ihre App mit dem SDK integrieren. Azure Mobile Engagement bietet auch die Möglichkeit, zusätzliche benutzerdefinierte Analyseinformationen zu sammeln, die Sie zum Verhalten Ihrer Endbenutzer wünschen. Sie erreichen dies durch Erstellen eines Tagplans mit benutzerdefinierten "App-Infotags", die im Abschnitt "Einstellungen" erstellt werden, sodass Azure Mobile Engagement diese zusätzlichen Daten für Sie sammeln kann.

### Weitere Informationen
-  [Dokumentation zur Benutzeroberfläche – Einstellungen][Link 20]
 
## Analyseüberschrift
- Elementname: Bezeichnet das zu zählende Element
- Hilfe anzeigen: Zeigt Kontextinformationen zu diesem Abschnitt
- Versionen: Ermöglicht Ihnen, verschiedene Analyseinformationen für jede Version Ihrer Anwendung oder Informationen für alle Versionen anzuzeigen. (Hinweis: Beim Filtern Ihre Analysedaten auf der Benutzeroberfläche werden alle Beispiele dieses Typs unabhängig von der Version Ihrer App angezeigt. "Abstürzt" nach Name gefiltert zeigt Ihnen beispielsweise Daten aus Version 1 und Version 2 der App.)
- Zeitraum: Letzte 7 Tage, Letzte 30 Tage, Gesamte Zeit, Benutzerdefiniert
- Rate: Pro Stunde, Tag, Woche, Monat
- Ansicht: Liniendiagramm, Raster, "Zum Dashboard senden", "CSV-Datei herunterladen"
 
![Analytics1][10]

## Analyse
- Dashboard: Zeigt allgemeine Informationen zu neuen und aktiven Benutzern sowie deren Trends an.
- Benutzer: Benutzer werden anhand ihres Gerätebezeichners (ID) identifiziert. Diese ID ist eindeutig für jedes Gerät (ein neuer Benutzer ist tatsächlich ein neues Gerät). Ein Benutzer gilt in einem bestimmten Zeitintervall als neu, wenn er während dieses Intervalls seine erste Sitzung gestartet hat. Ein Benutzer gilt als vermerkt, wenn er während der letzten 7 Tage mindestens eine Sitzung durchgeführt hat. Aktive Benutzer sind Benutzer, die während eines bestimmten Zeitraums mindestens eine Sitzung durchgeführt haben. Sie können nach monatlichen, wöchentlichen, täglichen oder stündlichen Zeiträumen sortieren. Alle Diagramme sehen ähnlich aus, ermöglichen Ihnen jedoch das Filtern anhand verschiedener Merkmale, z. B. Version der Anwendung, und dann das Sortieren anhand eines Zeitraums. Die Standardinformationen, die durch Integrieren des SDK gesammelt werden können, sind u. a. folgende: Aktive Benutzer, Neuer Benutzer, Anzahl von Sitzungen, Dauer jeder Sitzung, technische Informationen zu Land, Gebietsschema, Ort, Sprache, Netzbetreiber, Geräten, Firmware, Netzwerk (WLAN) und zu der von Kunden verwendeten Version von App und SDK. Diese Informationen können im Abschnitt "Überwachen" in Echtzeit angezeigt werden. 
- "Nachverfolgung nach Quelle" zeigt die Anzahl der neuen Benutzer, die die App aufgrund einer bestimmten Werbekampagne heruntergeladen haben. Benutzer werden anhand ihres Gerätebezeichners (ID) identifiziert. Diese ID ist eindeutig für jedes Gerät (ein neuer Benutzer ist tatsächlich ein neues Gerät). Ein Benutzer gilt in einem bestimmten Zeitintervall als neu, wenn er während dieses Intervalls seine erste Sitzung gestartet hat.
- "Nachverfolgung nach Store" zeigt die Anzahl der neuen Benutzer, die die Anwendung aus einem bestimmten Store heruntergeladen haben. Benutzer werden anhand ihres Gerätebezeichners (ID) identifiziert. Diese ID ist eindeutig für jedes Gerät (ein neuer Benutzer ist tatsächlich ein neues Gerät). Ein Benutzer gilt in einem bestimmten Zeitintervall als neu, wenn er während dieses Intervalls seine erste Sitzung gestartet hat.

> Hinweis: Der Zeitraum basiert auf dem Datum in den Geräteeinstellungen des Benutzers, sodass ein Benutzer, auf dessen Telefon das Datum falsch festgelegt ist, im falschen Zeitraum angezeigt werden kann.

- Vermerkung: Ein Benutzer gilt in einem bestimmten Zeitintervall als vermerkt, wenn er während dieses Intervalls seine erste Sitzung ausgeführt hat. Sie können die Zeitintervalle, in denen vermerkte Benutzer (und neue Benutzer) gezählt werden, in Stunden, Tagen, Wochen oder Monate ändern. Die Analyse vermerkter Benutzer basiert auf sog. "Kohorten". Eine Kohorte ist eine Gruppe neuer Benutzer, die in einem bestimmten Zeitraum erkannt wurden (d. h. die Gruppe von Benutzern, die ihre erste Sitzung während dieses Zeitraums ausführen). Für Kohorten kann als Zeitraum 1 Tag, 2 Tage, 4 Tage, 7 Tage oder 1 Monat gewählt werden. Bei einer auf 1 Tag, 2 Tagen, 4 Tagen, 7 Tagen oder 1 Monat basierenden Kohorte berechnet Azure Mobile Engagement die Menge aller Benutzer, die zur Kohorte gehören und noch aktiv sind (d. h. die Menge von Benutzern, die im jeweiligen Zeitraum mindestens eine Sitzung durchgeführt hat). Diese Menge von Benutzern wird als Kohortenversion bezeichnet. (Azure Mobile Engagement zeigt Ihnen, wie viele Benutzer weiter Ihre App nutzen, doch nur über den plattformspezifischen Store, z. B. Google Play, iTunes, Windows Store usw., kann bestimmt werden, wie viele Benutzer Ihre App deinstalliert haben). 
- Sitzungen: Eine Nutzung der Anwendung durch einen Benutzer. Sitzungen werden anhand der Abfolge von Aktivitäten generiert, die Benutzer durchgeführt haben (eine Aktivität ist in der Regel mit der Nutzung eines Bildschirms der Anwendung verknüpft, was aber variieren kann, je nachdem, wie das SDK in die Anwendung integriert wurde). Ein Benutzer kann jeweils nur eine Aktivität gleichzeitig ausführen. Eine Sitzung startet, sobald der Benutzer seine erste Aktivität beginnt, und endet, wenn seine letzte Aktivität abgeschlossen ist. Wenn ein Benutzer mehr als ein paar Sekunden ohne Aktivität bleibt, wird seine Abfolge von Aktivitäten in zwei verschiedene Sitzungen aufgeteilt.
- Aktivitäten: Die Namen der einzelnen Bildschirme in Ihrer Anwendung und die Dauer, die Benutzer auf den einzelnen Bildschirmen verbringen. Aktivitäten sind eine benutzerdefinierte Analyseoption, die den Tags vom Typ "App-Info" entsprechen, die Sie für Ihre App eingerichtet haben:
- Benutzerpfad: Zeigt, wie Ihre Benutzer durch die Aktivitäten (Bildschirme) Ihrer Anwendung navigieren. Sie können den Schieberegler verschieben, um den Detailgrad anzupassen. Blaue Knoten stellen die Aktivitäten Ihrer Anwendung dar. Ihre Größe ist proportional zur Zeit, die Benutzer darin verbracht haben. Weiße Knoten stellen Start und Ende der Sitzung dar. Rote Knoten markieren Abstürze. Links repräsentieren Übergänge zwischen Aktivitäten Ihrer Anwendung (oder zwischen Aktivitäten und Abstürzen). Klicken Sie auf einen Knoten oder Link, um eine QuickInfo mit weiteren Informationen zu Ihren Daten anzuzeigen: die auf einem bestimmten Bildschirm verbrachte Zeit, die Anzahl der Übergänge und den Prozentsatz der Übergänge von der Quellaktivität zur Zielaktivität. (A ---60 % ---> B bedeutet, dass Benutzer in Aktivität A in 60 % der Fälle zu Aktivität B wechseln.) Sie können das Diagramm wie gewünscht neu organisieren, um Daten zu präzisieren. Bei jeder Änderung wird seine Position gespeichert. Sie können die Abstürze ein- oder ausblenden, um das Diagramm aufzuhellen.
- Ereignisse: Bestimmte Aktionen eines Benutzers in der Anwendung. Die Verteilung von Ereignissen wird als Anzahl von Ereignissen pro Benutzer pro Sitzung angezeigt. Ein Ereignis stellt eine unmittelbare Aktion dar, z. B. einen Klick auf eine Schaltfläche oder den Empfang einer Benachrichtigung. (Die Bedeutung von Ereignissen hängt davon ab, wie das SDK in die Anwendung integriert wurde.) Ein Ereignis kann während einer Sitzung oder eines Auftrags auftreten oder für sich allein stehen.
- Aufträge: Ähnlich wie Ereignisse, außer dass der Fokus auf der Länge der Aktion liegt. Beispielsweise können Sie anhand von Aufträgen technische Informationen bestimmen, z. B. wie lange das Laden von Inhalten oder Aufrufen eines Webdiensts dauern. Es kann auch ermittelt werden, wie lange ein Benutzer für das Ausfüllen eines Formulars, Erstellen eines Kontos oder einen Kaufvorgang braucht. Ein Auftrag stellt die Dauer eines Vorgangs dar, z. B. die Dauer eines Herunterladevorgangs oder der Anzeige eines Banners auf dem Bildschirm. (Die Bedeutung von Aufträgen hängt davon ab, wie das SDK in die Anwendung integriert wurde.) Aufträge stehen normalerweise im Zusammenhang mit Hintergrundaufgaben, die nicht im Rahmen einer Sitzung (d. h. ohne Benutzeraktivitäten) erfolgen.
- Technische Informationen: Technische Informationen zu den Geräten der Benutzer Ihrer App, die Sie nachverfolgen können, z. B. Gebietsschema, Netzbetreiber, Netzwerk, Gerät, Firmware und Bildschirmgröße der Benutzergeräte sowie die Version Ihrer App und die in ihr verwendete SDK-Version.
- Fehler: Informationen zu technischen Fehlern innerhalb der Anwendung, die nicht zum Absturz der Anwendung führen. Ein Fehler stellt ein unmittelbares Problem dar, z. B. einen Netzwerkausfall oder eine fehlerhafte Änderung. (Die Bedeutung von Ereignissen hängt davon ab, wie das SDK in die Anwendung integriert wurde.) Ein Fehler kann während einer Sitzung oder eines Auftrags auftreten oder für sich allein stehen.
- Abstürze: Informationen zu Fehlern, die den Absturz Ihrer Anwendung verursachen. Ein Absturz ist eine unerwartete Bedingung, bei der die Anwendung nicht mehr die erwarteten Funktionen bietet und beendet werden muss. Ein Absturz ist in der Regel die Folge eines Fehlers in der Anwendung.
 
![Analytics2][11]

## Zugreifen auf die Bindungsübersicht
![Analytics3][12]

Die Bindungsübersicht wird in der Mitte in mehrere Karten aufgeteilt, die jeweils eine Übersicht für einen bestimmten Bindungszeitraum anzeigen. Der Bindungszeitraum von 2 Tagen wird im Beispiel gezeigt. Die anderen Karten zeigen Bindungszeiträume von 4 und 7 Tagen.

## Grundlegendes zu den Bindungsübersichtskarten
![Analytics4][13]

### Jede Karte besteht aus drei Hauptteilen:
1. 1: Die Kohorte und der berücksichtigte Zeitraum
2. 2-4: Die Bindung im aktuellen Zeitraum
3. 5: Eine Sparkline des Verlaufs

### Hier nun ausführliche Informationen zu den einzelnen Elementen:
1.    Kohorte und Zeitraum: Diese Überschrift gibt den Typ der Kohorte an. Hier bedeutet "Zeitraum von 2 Tagen", dass wir uns das Verhalten von Benutzern über 2 Tage ansehen: Benutzer, die über einen Zeitraum von 2 Tagen bei Ihrer App gelandet sind, und ob sie in den folgenden 2-Tage-Blöcken eine Verbindung hergestellt haben. Im obigen Beispiel werden die Aktivitäten von Benutzern vom 21. bis 22. November berücksichtigt.
2.    Dies liefert die Bindungsrate für den 21. und 22. November für die Benutzer, die am 19. und 20. November bei der App gelandet sind. Hier hatten wir 1 aktiven Benutzer am 21. und 22. im Vergleich zu 3, die am 19. und 20. neue Benutzer waren.
3.    Dieser visuelle Indikator bietet dieselben Informationen wie oben grafisch dargestellt. (Die 3 im Kreis gehört zum Wert 33 %.) Die Farbe gibt zusätzliche Informationen an: Grün gibt an, dass diese Zahl im Vergleich zur vorherigen Berechnung wächst. Gelb bedeutet stabil, Rot verringern.
4.    Hiermit werden die für die Berechnung verwendeten Werte angegeben.
5.    Dies ist eine Sparkline des Verlaufs der Bindungswerte. Sie können die Werte in der Vergangenheit prüfen, um sich einen umfassenden Einblick in die Entwicklung zu verschaffen.

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-how-tos-first-push.md
[Link 28]: mobile-engagement-how-tos-test-campaign.md
[Link 29]: mobile-engagement-how-tos-personalize-push.md
[Link 30]: mobile-engagement-how-tos-differentiate-push.md
[Link 31]: mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: mobile-engagement-how-tos-text-view.md
[Link 33]: mobile-engagement-how-tos-web-view.md

<!--HONumber=54-->