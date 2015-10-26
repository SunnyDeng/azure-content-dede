<properties 
   pageTitle="Azure Mobile Engagement - Benutzeroberfläche – Überwachen" 
   description="Informationen zum Überwachen von Echtzeitdaten zu Ihrer Anwendung mithilfe von Azure Mobile Engagement" 
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
   ms.date="08/10/2015"
   ms.author="piyushjo"/>

# Überwachen von Echtzeitdaten zu Ihrer Anwendung
Der Abschnitt "Überwachen" der Benutzeroberfläche enthält Informationen für Echtzeitanalysen und ermöglicht das Festlegen von Warnungen, wenn Schwellenwerte für die meisten der Informationen erreicht werden, die auch im Abschnitt "Analyse" der Benutzeroberfläche verfügbar sind. Das "Glossar" in "Konzepte" enthält die Definitionen der Begriffe und Abkürzungen in Analysen und Überwachung, wie z. B. die folgenden: Aktiver Benutzer, Neuer Benutzer, Benutzer beibehalten, Sitzung, Benutzerpfadgraph, Benutzer zuordnen, Nachverfolgen von URLs, Trends, Aktivität, Ereignis, Auftrag, Fehler, Zusätzliche Informationen, Absturz und App-Info.

### Weitere Informationen
-  [Konzepte - Glossar][Link 6], [Handbuch zur Problembehandlung - Analyse][Link 21]

## Überwachen - Sitzungen, Aufträge, Ereignisse, Fehler und Abstürze
Sie können erkennen, wie viele Benutzer sich derzeit in einer Sitzung und auf bestimmten Bildschirmen befinden oder bestimmte Aktionen ausführen. Sie können die Benutzeraktivität unterteilt in Sitzungen, Aufträge, Ereignisse, Fehler und Abstürze anzeigen. Sie können die aktuellen Informationen und die Informationen der letzten Stunde, des letzten Tags oder der letzten Woche anzeigen. Sie können alle Informationen in jeder Kategorie anzeigen oder eine Sortierung nach Sitzung, Auftrag, Ereignis, Fehler und Absturz vornehmen. Die Live-Überwachung ist z. B. während eines Ereignisses wie einer Pushkampagne hilfreich, um zu prüfen, ob nach dem Senden Ihrer Pushbenachrichtigung ein Aktivitätsanstieg zu verzeichnen ist.
 
![Monitor1][14]

## Problembehandlung mit "Überwachen - Ereignisse – Details"
Das Generieren eines Ereignisses in Ihrer Anwendung auf dem Testgerät, um es anschließend in "Überwachen - Ereignisse – Details" anzuzeigen, ist eine der einfachsten Möglichkeiten, die Geräte-ID Ihres Testgeräts zu bestimmen und zu bestätigen, dass die Azure Mobile Engagement-Integration von "Analyse", "Überwachen" und "Segmente" in Ihrer Anwendung funktioniert. Sobald Sie die Geräte-ID Ihres Testgeräts bestimmt haben, können Sie sie Ihren Testgeräten in "Mein Konto – Geräte" hinzufügen. Wenn Sie kein Ereignis generieren können, prüfen Sie, ob Azure Mobile Engagement ordnungsgemäß mit dem SDK in Ihre Android-/iOS-/Web-/Windows-/Windows Phone-App integriert wurde.

### Weitere Informationen
-  [SDK-Dokumentation][Link 5]

![Monitor2][15]

## Problembehandlung mit "Überwachen - Absturz - Details"
Sie können Absturzinformationen zu Ihrer App in "Überwachen - Absturz - Details" überprüfen, um zu ermitteln, warum die App abstürzt. Sie sollten auch bekannte Probleme bei jeder Version des SDK in den Versionshinweisen für jede Version des SDK für Android/iOS/Web/Windows/Windows Phone nachsehen.

### Weitere Informationen
-  [SDK-Dokumentation – Versionshinweise][Link 5]

![Monitor3][16]

## Überwachen - Warnungen
Sie können auch Bedingungen für Warnungen angeben, die automatisch per E-Mail oder SMS an Sie gesendet werden. (XMPP-kompatible Dienste wie Google GTalk oder Apple iChat werden unterstützt). Warnungen basieren auf einem vordefinierten Schwellenwert für die Erkennung größer als (>) oder kleiner als (<) einer bestimmten Anzahl von Sitzungen, Aufträgen, Ereignissen, Fehlern oder Abstürzen pro Sekunde, Minute oder Stunde. Warnungen können alle Aktivitäten eines bestimmten Typs oder nur einen bestimmten Auftrag, ein bestimmtes Ereignis oder eine bestimmte Fehleraktivität überwachen. Sie können auch eine Mindesterkennungsrate angeben, wobei es sich um den Mindestzeitraum handelt, der zwei Benachrichtigungen für dieselbe Warnung voneinander trennt, um sicherzustellen, dass Sie beim Auslösen einer Warnung alle x Minuten nicht mehr als eine Benachrichtigung erhalten.
 
![Monitor4][17]

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
 

<!---HONumber=Oct15_HO3-->