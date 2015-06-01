<properties 
   pageTitle="Azure Mobile Engagement – Benutzeroberfläche – Reichweiteninhalt" 
   description="Erfahren Sie, wie Sie den eindeutigen Inhalt der verschiedenen Kampagnenarten für Pushbenachrichtigungen in Azure Mobile Engagement verwalten" 
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

# Verwaltung des eindeutigen Inhalts der verschiedenen Kampagnenarten für Pushbenachrichtigungen
 
Im Abschnitt "Inhalt" einer neuen Reichweitenkampagne können Sie den Inhalt Ihrer Ankündigungen, Umfragen, Datenpushes und Kacheln (nur Windows Phone) ändern. Die Einstellung "Inhalt" von Pushkampagnen ist spezifisch für den Typ der Kampagne.
 
### Inhaltstypen:
- Ankündigungen
- Umfragen
- Datenpush
- Kacheln (nur Windows Phone)
 
## Inhalt von Ankündigungen
 ![Reichweiteninhalt1][30]

### Wählen Sie den Typ der Ankündigung:
-    Nur Benachrichtigung: Dies ist eine einfache Standardbenachrichtigung. Das bedeutet, dass, wenn ein Benutzer darauf klickt, keine zusätzliche Ansicht angezeigt wird, sondern nur die zugeordnete Aktion erfolgt.
-    Textankündigung: Dies ist eine Benachrichtigung, die den Benutzer auffordert, einen Blick auf eine Textansicht zu werfen.
-    Web-Ankündigung: Dies ist eine Benachrichtigung, die den Benutzer auffordert, einen Blick auf eine Webansicht zu werfen.

### Weitere Informationen
- [Reichweite – Anleitungen – Ankündigungen][Link 3] 

### Informationen zu Ankündigungen von Webansichten:
Vorkommen des Musters "{deviceid}" im HTML- oder JavaScript-Code, den Sie hier bereitstellen, werden automatisch durch den Bezeichner des Geräts ersetzt, das die Ankündigung anzeigt. Dies ist eine einfache Möglichkeit zum Abrufen von Azure Mobile Engagement-Gerätekennungen in einem externen Webdienst, der in Ihrem Backoffice gehostet wird. Wenn Sie eine Vollbildwebansicht (ohne die Standardschaltflächen "Aktion" und "Beenden", die wir bereitstellen) erstellen möchten, können Sie die folgenden Funktionen aus dem JavaScript-Code Ihrer Ankündigung einer Webansicht verwenden:

-    Durchführen der Ankündigungsaktion: ReachContent.actionContent()
-    Beenden der Ankündigung: ReachContent.exitContent()
 
### Wählen Ihrer Aktion:

### Informationen zu Aktions-URLs:
Jede URL, die vom Betriebssystem eines Zielgeräts interpretiert werden kann, kann als Aktions-URL verwendet werden. Dedizierte URLs, die Ihre Anwendung ggf. unterstützt (z. B. damit Benutzer zu einem bestimmten Bildschirm wechseln), können auch in einer Aktions-URL verwendet werden. Jedes Vorkommen des Musters {deviceid} wird automatisch durch die Kennung des Geräts ersetzt, das die Aktion ausführt. Dies ist eine einfache Möglichkeit zum Abrufen von Azure Mobile Engagement-Gerätekennungen über einen externen Webdienst, der in Ihrem Backoffice gehostet wird.

- **Android + iOS-Aktionen**
    - Öffnen einer Webseite
    - http://[web-site-domain] 
    - Beispiel:http://www.azure.com
    - Senden einer E-Mail
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - Beispiel: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Senden einer SMS
    - sms:[phone-number] 
    - Beispiel: sms:2125551212
    - Wählen einer Telefonnummer
    - tel:[phone-number] 
    - Beispiel: tel:2125551212
- **Aktionen nur für Android**
    - Herunterladen einer Anwendung aus dem Play Store
    - market://details?id=[app package] 
    - Beispiel:market://details?id=com.microsoft.office.word
    - Starten einer Geolocationsuche
    - geo:0,0?q=[search query] 
    - Beispiel: geo:0,0?q=starbucks,paris
- **Aktionen nur für iOS**
    - Herunterladen einer Anwendung aus dem App Store
    - http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
    - Beispiel:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows-Aktionen
    - Öffnen einer Webseite
    - http://[web-site-domain] 
    - Beispiel:http://www.azure.com
    - Senden einer E-Mail
    - mailto:[e-mail-recipient]?subject=[subject]&body=[message] 
    - Beispiel: mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Senden einer SMS (Skype Store App erforderlich)
    - sms:[phone-number] 
    - Beispiel: sms:2125551212
    - Wählen einer Telefonnummer (Skype Store App erforderlich)
    - tel:[phone-number] 
    - Beispiel: tel:2125551212
    - Herunterladen einer Anwendung aus dem Play Store
    - ms-windows-store:PDP?PFN=[app package ID] 
    - Beispiel: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Starten einer Bingmaps-Suche
    - bingmaps:?q=[search query] 
    - Beispiel: bingmaps:?q=starbucks,paris
    - Verwenden eines benutzerdefinierten Schemas
    - [custom scheme]://[custom scheme params] 
    - Beispiel:myCustomProtocol://myCustomParams
    - Verwenden von Paketdaten (Store App für das Lesen von Erweiterungen erforderlich)
    - [folder][data].[extension] 
    - Beispiel: MeineOrdnerDaten.txt
 
### Erstellen einer Nachverfolgungs-URL:
-    Im Abschnitt "Einstellungen" der Dokumentation zur Benutzeroberfläche finden Sie Anweisungen zum Erstellen einer Nachverfolgungs-URL, mit deren Hilfe Benutzer eine Ihrer anderen Anwendungen herunterladen können.
 
### Festlegen der Texte Ihrer Ankündigung
Füllen Sie den Titel, Inhalt und die Schaltflächentexte Ihrer Ankündigung aus. Sie können eine Zielgruppe einer künftigen Kampagne basierend auf dem Feedback zur Reichweite dazu adressieren, wie Benutzer auf diese Kampagne geantwortet haben. Die Zielgruppenadressierung kann basieren auf dem Feedback, dass diese Kampagne einfach nur per Push übertragen, beantwortet, umgesetzt oder beendet wurde.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Neue Pushkriterien][Link 28]

## Inhalt von Umfragen
![Reichweiteninhalt2][31] Füllen Sie Titel, Beschreibung und Schaltflächentext der Ankündigung aus. Fügen Sie dann Optionen und Antworten zum Beantworten Ihrer Fragen hinzu. Sie können eine Zielgruppe einer künftigen Kampagne basierend auf dem Feedback zur Reichweite dazu adressieren, wie Benutzer auf diese Kampagne geantwortet haben. Die Zielgruppenadressierung kann darauf basieren, ob diese Kampagne einfach nur per Push übertragen, beantwortet, umgesetzt oder beendet wurde. Die Zielgruppenadressierung kann auch auf Antwortfeedback auf eine Umfrage basieren, wobei die Fragen und Antwortoptionen als Kriterien verwendet werden.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Neue Pushkriterien][Link 28]
 
## Inhalt von Datenpushes
![Reichweiteninhalt3][32]

### Auswählen des Datentyps:
- Text
- Binärdaten
- Base64-Daten

### Definieren des Inhalts Ihrer Daten
- Wenn Sie die Pushübertragung von Textdaten ausgewählt haben, kopieren Sie den Text, und fügen Sie in das Feld "Inhalt" ein.
- Wenn Sie die Pushübertragung von Binär- oder Base64-Daten ausgewählt haben, verwenden Sie die Schaltfläche "Datei hochladen" zum Hochladen Ihrer Datei.
- Sie können eine Zielgruppe einer künftigen Kampagne basierend auf dem Feedback zur Reichweite dazu adressieren, wie Benutzer auf diese Kampagne geantwortet haben. Die Zielgruppenadressierung kann darauf basieren, ob diese Kampagne einfach nur per Push übertragen, beantwortet, umgesetzt oder beendet wurde.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Neue Pushkriterien][Link 28]

## Inhalt von Kacheln (nur Windows Phone)
![Reichweiteninhalt4][33]

### Definieren des Inhalts Ihrer Kachel
Die Kachelnutzlast ist der Text, der auf der Kachel Ihrer App auf Windows Phone-Geräten angezeigt wird. Ein Kachelpushvorgang ist die MPNS-Version (Microsoft Push Notification Service, Pushbenachrichtigungsdienst) eines systemeigenen Push für Windows Phone. Der Kachelpushtyp ist der einzige Pushtyp, der keine Antwort liefert, weshalb die Zielgruppe künftiger Kampagnen nicht auf den Ergebnissen einer Kachelpushkampagne basieren kann.

### Weitere Informationen
- [API-Dokumentation – Reichweiten-API – Systemeigenener Push][Link 4]

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

<!--HONumber=54-->