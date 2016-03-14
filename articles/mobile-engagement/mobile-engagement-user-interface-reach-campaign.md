<properties 
   pageTitle="Azure Mobile Engagement – Benutzeroberfläche – Reichweitenkampagne" 
   description="Erfahren Sie, wie Sie mit Azure Mobile Engagement Kampagnen für Pushbenachrichtigungen erstellen und verwalten können" 
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
   ms.date="02/29/2016"
   ms.author="piyushjo"/>


# Erstellen und Verwalten von Kampagnen für Pushbenachrichtigungen
Sie können auf der Benutzeroberfläche im Abschnitt "Reichweite" eine neue Pushkampagne mit einer komplexen Formel erstellen, indem Sie alle Informationen angeben, die Sie zum Senden einer Pushbenachrichtigung benötigen. Die Optionen einer Pushkampagne variieren geringfügig abhängig von den vier Kampagnentypen: Ankündigungen, Umfragen, Datenpushes und Kacheln (nur Windows Phone).

### Option gilt für:
- Sprachen: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Kampagne: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Benachrichtigung: Ankündigungen, Umfragen
- Inhalt: Eindeutig für jeden Kampagnentyp
- Zielgruppe: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Zeitraum: Ankündigungen, Umfragen, Kacheln
- Test: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
 
![Reichweitenkampagne1][20]

## Sprachen
Über das Dropdownmenü "Sprachen" können Sie eine andere Version Ihres Pushvorgangs an Geräte senden, die für die Nutzung anderer Sprachen eingerichtet sind. Standardmäßig erhalten alle Geräte den gleichen Push unabhängig von der Sprache, die für sie festgelegt ist. Benutzer, deren Gerät auf eine andere Sprache festgelegt ist, erhalten die Version in der Standardsprache des Pushvorgangs. Viele der Optionen für Pushkampagnen ermöglichen Ihnen das Angeben alternativer Inhalte für jede weitere Sprache, die Sie auswählen.
 
![Reichweitenkampagne2][21]

### Sprachunterschiede gelten für:
- Sprachen: Neben der Standardsprache können andere Sprachen ausgewählt werden
- Kampagne: Für alle Sprachen gleich
- Benachrichtigung: Zusätzlich zur Standardsprache für jede Sprache eindeutig
- Inhalt: Zusätzlich zur Standardsprache für jede Sprache eindeutig
- Zielgruppe: Kann anhand eines gesonderten Sprachkriteriums gefiltert werden
- Zeitrahmen: Für alle Sprachen gleich
- Test: Kann gleichzeitig an alle Sprachen gesendet werden
 
### Unterstützte Sprachen:
- Arabisch (Ar) 
- Bulgarisch (bg) 
- Katalanisch (ca) 
- Chinesisch (zh) 
- Kroatisch (hr) 
- Tschechisch (cs) 
- Dänisch (da) 
- Niederländisch (nl) 
- Englisch (en) 
- Finnisch (fi) 
- Französisch (fr) 
- Deutsch (de) 
- Griechisch (el) 
- Hebräisch (he) 
- Hindi (hi) 
- Ungarisch (hu) 
- Indonesisch (id) 
- Italienisch (it) 
- Japanisch (ja) 
- Koreanisch (ko) 
- Lettisch (lv) 
- Litauisch (lt) 
- Malaiisch (Makrosprache) (ms) 
- Norwegisch, Bokmål (nb) 
- Polnisch (pl) 
- Portugiesisch (pt) 
- Rumänisch (ro) 
- Russisch (ru) 
- Serbisch (sr) 
- Slowakisch (sk) 
- Slowenisch (sl) 
- Spanisch (es) 
- Schwedisch (sv) 
- Tagalog (tl) 
- Thai (th) 
- Türkisch (tr) 
- Ukrainisch (uk) 
- Vietnamesisch (vi) 
 
## Kampagne
Im Abschnitt "Kampagne" können Sie den Namen und die Kategorie der Kampagne festlegen. Sie können auch planen, den Zielgruppenabschnitt einer Pushkampagne zu ignorieren und stattdessen diese Kampagne über die Reichweite-API (und einige Elemente der untergeordneten Push-API) zu senden. Kategorien können mit einer benutzerdefinierten Benachrichtigungsvorlage verwendet werden, um App-interne Benachrichtigungen basierend auf vordefinierten Einstellungen zu steuern. Über die Reichweite-API können Sie eine Liste Ihrer vorhandenen "Kategorien" abrufen.

> Warnung: Wenn Sie im Abschnitt "Kampagne" einer Reichweitenkampagne die Option "Zielgruppe ignorieren, Push wird Benutzern über die API gesendet" verwenden, wird die Kampagne NICHT automatisch gesendet. Sie müssen die Kampagne manuell über die Reichweite-API senden.
 
![Reichweitenkampagne3][22]
 
### Option gilt für:
- Name: Alle
- Kategorie: Ankündigungen, Umfragen
- Zielgruppe ignorieren, Push wird Benutzern über die API gesendet: Alle
 
## Benachrichtigung
Im Abschnitt "Benachrichtigung" können Sie grundlegende Einstellungen für Ihren Pushvorgang festlegen, z. B.: Den Titel des Pushvorgangs, die Nachricht, ein App-internes Bild der App oder ob sie abweisbar ist. Viele Benachrichtigungseinstellungen sind spezifisch für die Plattform Ihres Geräts. Sie können auswählen, ob Ihr Pushvorgang "App-intern" oder "App-extern" oder in beiden Formen gesendet wird. (Beachten Sie, dass Benutzer auf der Betriebssystemebene ihrer Geräte Pushvorgänge "abonnieren" bzw. diese "Abonnements kündigen" können. Azure Mobile Engagement hat keine Möglichkeit, diese Einstellung außer Kraft zu setzen. Beachten Sie außerdem, dass die Reichweite-API Pushvorgänge "App-intern" und "App-extern" verarbeitet. Die Push-API kann verwendet werden, um auch App-externe Pushvorgänge zu verarbeiten.) Pushvorgänge können mit Bildern oder HTML-Inhalt angepasst werden, einschließlich Deep Links zum Verknüpfen mit Elementen außerhalb Ihrer App oder mit einer anderen Stelle in Ihrer App (Intent-Kategorien von Android-SDK 2.1.0 oder höher erforderlich). Sie können das Symbol oder iOS-Badge ändern und entweder Text oder Webinhalte (ein Popup mit HTML-Inhalt, URL-Link zu einer anderen Stelle innerhalb oder außerhalb der App) senden. Sie können Android-Geräte auch bei der Pushbenachrichtigung klingeln oder vibrieren lassen. (Denken Sie daran, dass Sie die ordnungsgemäßen SDK-Berechtigungen in Ihrer Android-Manifestdatei benötigen, damit ein Gerät klingelt oder vibriert.) Derzeit gibt es keinen Standard für Android-Bilder in Übergrößen, da die Bildschirmgrößen auf jedem Gerät anders sind, aber 400x100-Bilder funktionieren bei nahezu jeder Bildschirmgröße.

### Übermittlungstypen:
-    Nur App-extern: die Benachrichtigung wird übermittelt, wenn der Benutzer die Anwendung nicht verwendet.
- Die ausschließlich App-externe Benachrichtigung erfordert ein Zertifikat von Apple oder Google (APNS- oder GCM-Zertifikat).
- Nur App-intern: Die Benachrichtigung wird nur angezeigt, wenn die Anwendung ausgeführt wird.
- Die Benachrichtigung verwendet das Capptain-Übermittlungssystem, um den Benutzer zu erreichen. Sie können Layout und Anzeige Ihres Pushvorgangs vollständig visuell anpassen.
- Immer: Diese Option stellt sicher, dass Sie eine Benachrichtigung unabhängig davon senden, ob die Anwendung ausgeführt wird oder nicht.

 
![Reichweitenkampagne4][23]

### Option gilt für:
- Benachrichtigung: Ankündigungen, Umfragen
 
## Inhalt
Im Abschnitt "Inhalt" können Sie den Inhalt Ihrer Ankündigungen, Umfragen, Datenpushes und Kacheln (nur Windows Phone) ändern. Die Einstellung "Inhalt" von Pushkampagnen ist spezifisch für den Typ der Kampagne.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Pushübertragung von Inhalten][Link 29]
 
![Reichweitenkampagne5][24]

## Zielgruppe
Im Abschnitt "Zielgruppe" können Sie eine Standardliste von Elementen zum Beschränken Ihrer Kampagne definieren. Sie können die Kampagne auch basierend auf angepassten Kriterien beschränken. Der Standardsatz von Optionen zum Einschränken der Zielgruppe ermöglicht Ihnen einen Push an entweder alte oder neue Benutzer oder nur an systemeigene Pushbenutzer. Sie können auch ein Kontingent festlegen, um die Anzahl der Benutzer zu beschränken, die den Push empfangen. Sie können den Ausdruck bearbeiten, wie Ihre Kampagne gefiltert wird, um mindestens ein Kriterium für Zielbenutzer einzuschließen. Sie können einen Ausdruck für die Zielgruppe manuell eingeben. Ein solcher Ausdruck muss explizit das Verhältnis zwischen Kriterien definieren. Ein Kriterium ist ein Bezeichner, der mit einem Großbuchstaben beginnen muss und keine Leerzeichen enthalten darf. Die Beziehung zwischen den Kriterien kann mit den Operatoren 'und', 'oder', 'nicht' und als '(',')' beschrieben werden. Beispiel: "Kriterium1 oder (Kriterium1 und nicht Kriterium2)".

> Hinweis: Bei einer großen Zielgruppe für Kampagnen kann die Zielsuche auf Serverseite langsam sein, insbesondere dann, wenn Sie versuchen, mehrere Kampagnen gleichzeitig zu starten.

- Starten Sie, sofern möglich, immer nur eine Kampagne gleichzeitig.
- Maximal sollten nicht mehr als vier Kampagnen gleichzeitig gestartet werden.
- Übertragen Sie den Push nur an Ihre aktiven Benutzer (Kontrollkästchen "Nur Benutzer erfassen, die über systemeigenen Push erreicht werden können" und "Nur aktive Benutzer erfassen"), damit nur die Benutzer, die die App noch installiert haben und nutzen, durchsucht werden müssen. Nachdem die Zielgruppe definiert wurde, können Sie auf die Schaltfläche "Simulieren" klicken, um herauszufinden, wie viele Benutzer diesen Push empfangen. Dadurch wird die Anzahl bekannter Benutzer berechnet, die potenziell zu dieser Zielgruppe gehören (hierbei handelt es sich um eine Schätzung, die auf einer Zufallsstichprobe von Benutzern basiert). Denken Sie daran, dass Benutzer, die die Anwendung deinstalliert haben, ebenfalls Teil dieser Zielgruppe sind, aber nicht erreicht werden können.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Neue Pushkriterien][Link 28]

![Reichweitenkampagne6][25]

### Bearbeitungsausdruck
![Reichweitenkampagne7][26]
 
### Die Option "Zielgruppe begrenzen" gilt für:
- Nur eine Teilmenge der Benutzer erfassen: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur vorhandene Benutzer erfassen: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur neue Benutzer erfassen: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur Benutzer im Leerlauf erfassen: Ankündigungen, Umfragen, Kacheln
- Nur aktive Benutzer erfassen: Alle (Ankündigungen, Umfragen, Datenpushes, Kacheln)
- Nur Benutzern mit aktiviertem systemeigenen Push erfassen: Ankündigungen, Umfragen
 
## Zeitraum
Sie können im Abschnitt "Zeitraum" festlegen, wann der Push gesendet werden soll. Oder Sie können den Zeitraum leer lassen, um die Kampagne sofort zu starten. Denken Sie daran, dass bei Verwenden der Zeitzone der Endbenutzer die Kampagne für Ihre Endbenutzer in Asien einen Tag früher als erwartet starten kann. Senden Sie nacheinander kleine Batches von Pushdaten, bis alle Zeitzonen in der Welt mit dem Zeitraum übereinstimmen, den Sie für die Kampagne festgelegt haben. Das Verwenden der Zeitzone der Endbenutzer kann auch Verzögerungen bei Kampagnen verursachen, da vor dem Starten des Pushvorgangs die Uhrzeit vom Telefon abgerufen werden muss.

> Hinweis: Kampagnen ohne Enddatum können Pushes lokal zwischenspeichern und sie weiter anzeigen, nachdem Sie Kampagnen manuell abgeschlossen haben. Um dieses Verhalten zu vermeiden, geben Sie eine spezifische Endzeit für Kampagnen an.

### Weitere Informationen
- [Reichweite – Vorgehensweisen – Zeitplanung][Link 3] 
 
![Reichweitenkampagne8][27]

### Einstellungen gelten für:
- Zeitraum: Ankündigungen, Umfragen, Kacheln
 
## Test
Sie können im Abschnitt "Test" diesen Pushvorgang an Ihr eigenes Testgerät senden, bevor Sie die Kampagne speichern. Wenn Sie bestimmte Sprachen für diese Kampagne konfiguriert haben, können Sie den Push in der jeweiligen Sprache testen. Sie können ein Testgerät in "Mein Konto" einrichten.
> Hinweis: Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Mein Konto][Link 14]
 
![Reichweitenkampagne9][28]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=AcomDC_0302_2016-->