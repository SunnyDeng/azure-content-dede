<properties 
   pageTitle="Azure Mobile Engagement – Handbuch zur Problembehandlung – Dienste" 
   description="Handbücher zur Problembehandlung für Azure Mobile Engagement" 
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


# Handbuch zur Problembehandlung bei Dienstproblemen

Im Folgenden finden Sie mögliche Probleme, die bei der Ausführung von Azure Mobile Engagement auftreten können.

## Dienstausfälle

### Problem
- Probleme, die offenbar von Dienstausfällen in Azure Mobile Engagement verursacht werden.

### Ursachen
- Probleme, die scheinbar von Azure Mobile Engagement-Dienstausfällen verursacht werden, können verschiedene Ursachen haben:
    - Isolierte Probleme, die ursprünglich systemisch in Azure Mobile Engagement auftreten
    - Bekannte Probleme, die durch Serverausfälle verursacht werden (nicht immer im Serverstatus angezeigt):
	- Verzögerungen bei der Planung, Fehler bei der Zielgruppenadressierung, Probleme bei der Badgeaktualisierung, keine Erfassung von Statistiken, Pushvorgänge funktionieren nicht mehr, APIs funktionieren nicht mehr, neue Apps oder Benutzer können nicht erstellt werden, DNS-Fehler, Zeitüberschreitungen in Benutzeroberfläche, API oder Apps auf einem Gerät.
    - Cloudabhängigkeitsfehler [Azure-Dienststatus](http://status.azure.com/), [Amazon Web Services (AWS)-Status](http://status.aws.amazon.com/) 
    - Abhängigkeitsfehler des Pushbenachrichtungsdienstes (PNS) [Google – Service](http://www.google.com/appsstatus#hl=en&v=status), [Apple – Service](http://www.apple.com/support/systemstatus/), [Android – Google GCM](http://developer.android.com/google/gcm/index.html), [Android – Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple – APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone – WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone – MPNS][LinkMPNS], [Windows – WNS](https://developer.windows.com/)
    - App Store-Ausfälle [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Um zu testen, ob es sich um ein systemisches Problem handelt, können Sie dieselbe Funktion von anderer Stelle ausführen:*

  - Azure Mobile Engagement integrated application
  - Test device
  - Test device OS version
  - Campaign
  - Administrator user account
  - Browser (IE, Firefox, Chrome, etc.)
  - Computer
*So testen Sie, ob das Problem nur die Benutzeroberfläche oder die APIs betrifft:*

  - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API-Dokumentation][Link 4], [Dokumentation zur Benutzeroberfläche][Link 1]

*So testen Sie, ob ein Problem mit dem Mobilfunknetz vorliegt:*

  - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
  - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*So testen Sie, ob ein Geräteproblem vorliegt:*

  - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
  - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
  - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Dokumentation zur Benutzeroberfläche – Einstellungen][Link 1]

*So testen Sie, ob ein Problem mit der App vorliegt:*

  - Install and test your application from an emulator instead of from a physical device:
  - Download and use Android SDK (includes an Android Emulator)
  - Download and use Apple Xcode (includes an iOS Simulator)
  - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

So testen Sie, ob ein Problem mit Betriebssystemupgrades auf Endbenutzergeräten vorliegt, für dessen Lösung ein SDK-Upgrade erforderlich ist:

  - Test your application on different devices with different versions of the OS.
  - Confirm that you are using the most recent version of the SDK.
[Handbuch zur Problembehandlung – SDK][Link 2]
 
## Konnektivitätsprobleme und falsche Informationen

### Problem
- Es treten bei der Anmeldung an der Azure Mobile Engagement-Benutzeroberfläche Probleme auf.
- Verbindungsfehler mit den Azure Mobile Engagement-APIs.
- Probleme beim Hochladen von Infotags der App über die Geräte-API.
- Probleme beim Herunterladen von Protokollen oder exportierten Daten aus Azure Mobile Engagement.
- Anzeige von falschen Informationen in der Azure Mobile Engagement-Benutzeroberfläche.
- Anzeige von falschen Informationen in Azure Mobile Engagement-Protokollen.

### Ursachen
- Bei Konnektivitätsproblemen mit Azure Mobile Engagement:
    - Vergewissern Sie sich, dass Ihr Benutzerkonto über ausreichende Berechtigungen zum Ausführen der Aufgabe verfügt.
    - Stellen Sie sicher, dass es sich nicht um ein isoliertes Problem mit einem Computer oder Ihrem lokalen Netzwerk handelt.
    - Vergewissern Sie sich, dass der Azure Mobile Engagement-Dienst keine Ausfälle meldet.
    - Stellen Sie sicher, dass Ihre App-Infotagdateien allen der folgenden Regeln entsprechen:
        - Verwenden Sie ausschließlich den UTF8-Zeichensatz (der ANSI-Zeichensatz wird nicht unterstützt).
        - Verwenden Sie als Trennzeichen ein Komma "," (Sie können über eine Dienstanfrage die Änderung des CSV-Trennzeichens von einem Komma "," in ein anderes Zeichen anfordern, beispielsweise ein Semikolon ";").
        - Verwenden Sie ausschließlich Kleinbuchstaben für die booleschen Werte "true" und "false".
        - Verwenden Sie eine Datei, die die maximale Größe von 35 MB nicht übersteigt.

### Weitere Informationen

[API-Dokumentation][Link 4], [Dokumentation zur Benutzeroberfläche – Startseite][Link 1]
 
## Funktionsanfragen

### Problem
- Die Funktion, die Sie verwenden möchten, ist in Azure Mobile Engagement noch nicht vorhanden.

### Ursachen

Sie können eine neue Funktion für Azure Mobile Engagement vorschlagen, wenn diese noch nicht vorhanden ist: - Senden Sie eine Azure Mobile Engagement-Serviceanfrage mit möglichst vielen Details zu der neuen Funktion, die Sie für Azure Mobile Engagement vorschlagen.

### Weitere Informationen

[Mobile Engagement Feedback/Funktionsanfragen](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
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
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54-->