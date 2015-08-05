<properties 
   pageTitle="Azure Mobile Engagement - Benutzeroberfläche – Reach" 
   description="Erfahren Sie, wie Sie die Benutzer Ihrer Anwendung mit Pushbenachrichtigungen mithilfe von Azure Mobile Engagement erreichen" 
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


# Wie Sie die Benutzer Ihrer Anwendung mit Pushbenachrichtigungen erreichen
Der Abschnitt "Reach" der Benutzeroberfläche ist das Verwaltungstool für Pushkampagnen, mit dem Sie Kampagnen und Merkmale von Pushbenachrichtigungen erstellen, bearbeiten, aktivieren, beenden und überwachen sowie entsprechende Statistiken abrufen können. Der Zugriff darauf ist auch über die Reach-API (und einige Elemente der untergeordneten Push-API) möglich. Vor der Verwendung von Reach-Kampagnen müssen Sie unabhängig davon, ob Sie die APIs oder Benutzeroberfläche verwenden, Azure Mobile Engagement und Reach für jede Plattform mit dem SDK in Ihre Anwendung integrieren.

### Weitere Informationen
-  [API-Dokumentation – Reach-API][Link 4], [API-Dokumentation – Push-API][Link 4], [Handbuch zur Problembehandlung – Push/Reach][Link 23]
-  [Reach – Kampagne][Link 27], [Reach – Kriterium][Link 28], [Reach – Inhalt][Link 29], [Reach – Gewusst wie][Link 3]
 
## Vier Arten von Pushbenachrichtigungen
1.    Ankündigungen – Ermöglicht Ihnen das Senden von Werbebotschaften an Benutzer, über die diese zu einer anderen Stelle in Ihrer App umgeleitet oder zu einer Webseite oder einem Store außerhalb Ihrer App geleitet werden. 
2.    Umfragen - Sie können Informationen zu Endbenutzern erfassen, indem Sie ihnen Fragen stellen.
3.    Datenpushes - Ermöglicht Ihnen das Senden einer Binär- oder Base64-Datendatei. Die Informationen in einem Datenpush werden an Ihre Anwendung gesendet, um die aktuelle Benutzerumgebung in Ihrer App zu ändern. Die Anwendung muss die Daten in einem Datenpush verarbeiten können.
4.    Kacheln (nur Windows Phone) – Ermöglicht Ihnen das Verwenden des Microsoft-Pushbenachrichtigungsdiensts (MPNS), um systemeigene Windows-Pushübertragungen mit XML-Daten zu senden. (Unterstützt ab der SDK-Version 0.9.0. Die endgültige Nutzlast für Kacheln darf 32 KB nicht überschreiten.)

### Weitere Informationen
-  [Konzepte – Glossar][Link 6]

## Es werden drei Kategorien von Echtzeitstatistiken für jede Kampagne gezeigt
1.    Anzahl der Pushvorgänge – Die Anzahl der Pushvorgänge, die anhand der in der Kampagne angegebenen Kriterien gesendet wurden. 
2.    Geantwortet – Anzahl der Benutzer, die auf die Benachrichtigung reagiert haben, indem sie sie entweder außerhalb der App geöffnet oder innerhalb der App geschlossen haben. 
3.    Aktion ausgeführt - Die Anzahl der Benutzer, die auf den Link in der Benachrichtigung geklickt haben, um zu einer neue Stelle in der App, zu einem Store oder zu einem Webbrowser umgeleitet zu werden. 

> Hinweis: Ausführlichere Kampagnenstatistiken stehen über die Statistiken der Reach-API zur Verfügung.

### Weitere Informationen
-  [Konzepte – Glossar][Link 6], [API-Dokumentation - Reach-API – Statistiken][Link 4]


## Kampagnendetails
Sie können Kampagnen bearbeiten, klonen, löschen oder Kampagnen aktivieren, die noch nicht aktiviert wurden, indem Sie den Mauszeiger über deren Namen bewegen. Oder klicken Sie darauf, um sie zu öffnen. Sie können Kampagnen klonen, die bereits aktiviert wurden, indem Sie den Mauszeiger über deren Namen bewegen. Oder klicken Sie darauf, um sie zu öffnen. Eine einmal aktivierte Kampagne kann jedoch nicht geändert werden.
 
![Reach1][18]

## Reach-Feedback
Sie können von der Detail- zur Statistikansicht einer geöffneten Kampagne wechseln, die bereits aktiviert wurde. Und Sie können von der einfachen zur erweiterten Ansicht der Statistiken wechseln, um (je nach Berechtigungen) detailliertere Informationen zu erhalten. Sie können auch die Reach-Feedbackinformationen aus einer vorherigen Kampagne als Zielkriterien in einer neuen Kampagne verwenden. Reach-Feedbackstatistiken können auch über die Reach-API mithilfe von "Stats" erfasst werden. Sie können auch die Zielgruppe Ihrer Pushkampagnen basierend auf früheren Kampagnen anpassen.


### Weitere Informationen 
-  [Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkampagne][27], [API-Dokumentation - Reach-API – Statistiken][Link 4]

![Reach2][19]

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
 

<!---HONumber=July15_HO4-->