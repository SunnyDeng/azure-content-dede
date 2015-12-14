<properties 
   pageTitle="Azure Mobile Engagement – Benutzeroberfläche - Einstellungen" 
   description="Informationen zum Verwalten der globalen Einstellungen Ihrer Anwendung mithilfe von Azure Mobile Engagement" 
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
   ms.date="11/29/2015"
   ms.author="piyushjo"/>

# Verwaltung der globalen Einstellungen Ihrer Anwendung

Die verfügbaren Optionen im Menü **Einstellungen** für eine Anwendung variieren abhängig von der Plattform der Anwendung und den Berechtigungen, die Sie der Anwendung erteilt haben. Dazu zählen: Details, Projekte, Systemeigener Push, Pushgeschwindigkeit, Tag (App-Info) und Kommerzieller Druck. Die Menüoption „Tag“ (App-Info) im Abschnitt „Einstellungen“ kann von der Anwendung (mithilfe des SDK) oder von Ihrem Back-End (mithilfe der Device-API) verwaltet werden.


>[AZURE.NOTE]Viele Abschnitte der Benutzeroberfläche des **Mobile Engagement**-Portals enthalten die Schaltfläche **HILFE ANZEIGEN**. Drücken Sie diese Schaltfläche, um weitere Kontextinformationen zu einem bestimmten Bereich zu erhalten.

## Details

Ermöglicht Ihnen, den Namen und die Beschreibung Ihrer Anwendung zu ändern sowie deren Besitzer und Ihre Rollenberechtigungen anzuzeigen.

Analysekonfiguration: Ermöglicht das Anzeigen oder Ändern des Anfangstags der Woche und des Bindungszeitraums in Tagen.
 
  ![settings1][46]
 
## Projekte

Ermöglicht Ihnen die Auswahl aller Projekte, in denen die Anwendung angezeigt werden soll.

Sie können auch ein Projekt suchen und den Namen, die Beschreibung, den Besitzer und Ihre Rollenberechtigungen für Projekte anzeigen, zu denen die Anwendung gehört.

Weitere Informationen finden Sie unter [Dokumentation zur Benutzeroberfläche – Startseite][Link 13].
 
  ![settings3][48]

## Systemeigener Push

Ermöglicht das Registrieren eines neuen Zertifikats oder Löschen eines vorhandenen Zertifikats für die Verwendung mit systemeigenem Push. "Systemeigener Push" ermöglicht Azure Mobile Engagement Pushübertragungen an Ihre Anwendung zu jeder Zeit, selbst wenn sie nicht ausgeführt wird.

Nach dem Angeben von Anmeldeinformationen oder Zertifikaten für mindestens einen systemeigenen Pushdienst können Sie beim Erstellen von Reach-Kampagnen "Jederzeit" auswählen und auch den Parameter "Notifier" in der PUSH-API verwenden.



### Apple Push Notification Service (APNS)

Zum Aktivieren des systemeigenen Pushs mithilfe von Apple Push Notification Service müssen Sie Ihr Zertifikat registrieren. Sie müssen den Typ des Zertifikats entweder als DEV (Entwicklung) oder PROD (Produktion) angeben. Anschließend müssen Sie Ihr Zertifikat und Kennwort hochladen.

Weitere Informationen finden Sie unter [SDK-Dokumentation – iOS – Vorbereiten der Anwendung für Apple-Pushbenachrichtigungen][Link 5]
 
![settings4][49]
 
### Windows-Pushbenachrichtigungsdienst (Windows Push Notification Service, WPNS)

Um systemeigenen Push mit dem Windows-Pushbenachrichtigungsdienst zu aktivieren, müssen Sie die Anmeldeinformationen Ihrer Anwendung bereitstellen. Sie benötigen die Paketsicherheits-ID (SID) und den geheimen Schlüssel.
 
![settings5][50]
 
### Google Cloud Messaging (GCM) für Android

Um systemeigenen Push über GCM zu aktivieren, müssen Sie den Anweisungen von Google folgen. Dann müssen einen "Server::Simple"-API-Schlüssel einfügen, der ohne IP-Einschränkungen konfiguriert ist. Erfordert Integration mit dem SDK für Android ab Version 1.12.0.

Weitere Informationen finden Sie unter:

- [SDK-Dokumentation – Android – Integration von GCM][Link 5]
- [Google Developer-Handbuch für GCM](http://developer.android.com/guide/google/gcm/gs.html)
 
### Amazon Device Messaging (ADM) für Android

Zum Aktivieren von systemeigenem Push mit ADM müssen Sie Amazon-<OAuth credentials> angeben, bestehend aus einer Client-ID und einem Clientschlüssel (erfordert die Integration mit dem SDK für Android ab Version 2.1.0).

Weitere Informationen finden Sie unter:

- [SDK-Dokumentation – Android – Integration von ADM][Link 5]
- [Amazon Developer-ADM-Dokumentation](https://developer.amazon.com/sdk/adm/credentials.html#Getting)
 
![settings6][51]

## Pushgeschwindigkeit

Zeigt die aktuelle Pushgeschwindigkeit Ihrer Anwendung und ermöglicht Ihnen, die Pushgeschwindigkeit der Anwendung festzulegen.
 
  ![settings7][52]

## Tag (App-Info)

![settings11][56]
  
## Kommerzieller Druck


![settings12][57]


## Weitere Informationen

- [Konzepte][Link 6]
- [Handbuch zur Problembehandlung – Dienst][Link 24]

 

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 

<!---HONumber=AcomDC_1203_2015-->