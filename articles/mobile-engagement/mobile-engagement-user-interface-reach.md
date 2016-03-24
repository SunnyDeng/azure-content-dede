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
   ms.date="03/08/2016"
   ms.author="piyushjo"/>


# Wie Sie die Benutzer Ihrer Anwendung mit Pushbenachrichtigungen erreichen

In diesem Artikel wird die Registerkarte **REACH** im Portal **Mobile Engagement** beschrieben. Verwenden Sie das Portal **Mobile Engagement**, um Ihre mobilen Apps zu überwachen und zu verwalten. Beachten Sie, dass Sie zur Verwendung des Portals zuerst ein **Azure Mobile Engagement**-Konto erstellen müssen. Weitere Informationen finden Sie unter [Erstellen eines Azure Mobile Engagement-Kontos](mobile-engagement-create.md).

Der Abschnitt "Reach" der Benutzeroberfläche ist das Verwaltungstool für Pushkampagnen, mit dem Sie Kampagnen und Merkmale von Pushbenachrichtigungen erstellen, bearbeiten, aktivieren, beenden und überwachen sowie entsprechende Statistiken abrufen können. Der Zugriff darauf ist auch über die Reach-API (und einige Elemente der untergeordneten Push-API) möglich. Vor der Verwendung von Reach-Kampagnen müssen Sie unabhängig davon, ob Sie die APIs oder Benutzeroberfläche verwenden, Azure Mobile Engagement und Reach für jede Plattform mit dem SDK in Ihre Anwendung integrieren.

>[AZURE.NOTE] Viele Abschnitte der Benutzeroberfläche des **Mobile Engagement**-Portals enthalten die Schaltfläche **HILFE ANZEIGEN**. Drücken Sie diese Schaltfläche, um weitere Kontextinformationen zu einem bestimmten Bereich zu erhalten.

## Vier Arten von Pushbenachrichtigungen
1.    Ankündigungen – Ermöglicht Ihnen das Senden von Werbebotschaften an Benutzer, über die diese zu einer anderen Stelle in Ihrer App umgeleitet oder zu einer Webseite oder einem Store außerhalb Ihrer App geleitet werden. 
2.    Umfragen - Sie können Informationen zu Endbenutzern erfassen, indem Sie ihnen Fragen stellen.
3.    Datenpushes - Ermöglicht Ihnen das Senden einer Binär- oder Base64-Datendatei. Die Informationen in einem Datenpush werden an Ihre Anwendung gesendet, um die aktuelle Benutzerumgebung in Ihrer App zu ändern. Die Anwendung muss die Daten in einem Datenpush verarbeiten können.

## Kampagnendetails

Sie können Kampagnen bearbeiten, klonen, löschen oder Kampagnen aktivieren, die noch nicht aktiviert wurden, indem Sie den Mauszeiger über deren Namen bewegen. Oder klicken Sie darauf, um sie zu öffnen. Sie können Kampagnen klonen, die bereits aktiviert wurden, indem Sie den Mauszeiger über deren Namen bewegen. Oder klicken Sie darauf, um sie zu öffnen. Eine einmal aktivierte Kampagne kann jedoch nicht geändert werden.
 
![Reach1][18]

## Reach-Feedback

Klicken Sie auf „**Statistiken**“, um die Details einer Reichweitenkampagne zu sehen. Die Ansicht „**Einfach**“ bietet eine visuelle Darstellung in Form eines Balkendiagramms, die zeigt, was passiert ist, nachdem eine Kampagne aktiviert wurde. Die Ansicht „**Erweitert**“ bietet detailliertere Informationen zur Push-Kampagne. Diese Details stehen nicht zur Verfügung, wenn Sie eine Testkampagne senden, wie z. B. einen Push an ein Testgerät. So sollten diese Details interpretiert werden:

1. **Übertragen** - Gibt die Anzahl der an die Geräte gesendeten Meldungen an. Diese Anzahl hängt von der Zielgruppe ab, die Sie beim Erstellen der Push-Kampagne angegeben haben. Wenn Sie keine Zielgruppe angeben, wird dieser Push an alle registrierten Geräte gesendet. Wie alle anderen Push-Dienste senden wir die Pushes nicht direkt an die Geräte, sondern übertragen sie zu den entsprechenden plattformspezifischen Pushbenachrichtigungsdiensten (PNS - APNS/GCM/WNS), damit diese die Benachrichtigungen an die Geräte senden können. 

2.	**Übermittelt** - Gibt die Anzahl der Meldungen an, die erfolgreich vom PNS an das Gerät gesendet und vom Mobile Engagement SDK als empfangen bestätigt wurden.
		
	*Gründe für eine geringere Anzahl bei „Übermittelt“ im Vergleich zu „Übertragen“:*
	
	1. Wenn der Benutzer die App auf dem Gerät deinstalliert hat und dies dem PNS zum Zeitpunkt, zu dem wir den Push an den PNS senden, nicht bekannt ist, wird die Meldung gelöscht.
	2. Wenn die App auf dem Gerät installiert ist, die Geräte selbst aber für längere Zeit offline waren, kann die Meldung nicht vom PNS an das Gerät übermittelt werden. 
	3. Wenn die Meldung an das Gerät übermittelt wird, das Mobile Engagement SDK in der Anwendung den Inhalt der Meldung jedoch nicht erkennt, wird die Meldung gelöscht. Dies kann passieren, wenn die Anpassung der Benachrichtigung in der App eine Ausnahme generiert, die wir im SDK erfassen und die Meldung somit löschen. Dies kann auch auftreten, wenn die App auf dem Gerät eine Version des Mobile Engagement SDK verwendet, die die von der Plattform gesendete neuere Version der Pushbenachrichtigung nicht verstehen kann. Dies tritt jedoch nur auf, wenn die App aktualisiert wurde, nachdem die Benachrichtigung von der Serviceplattform gesendet wurde. Die Registerkarte „**Erweitert**” zeigt an, wie viele Nachrichten gelöscht wurden. 
	4. Auf iOS-Geräten werden Nachrichten manchmal nicht zugestellt, wenn der Akkuladestand des Geräts niedrig ist oder wenn die App beim Verarbeiten von Remotebenachrichtigungen eine erhebliche Menge an Energie verbraucht. Dies ist eine Einschränkung der iOS-Geräte.   

3.	**Angezeigt**: Gibt die Anzahl der Meldungen an, die dem App-Benutzer erfolgreich auf dem Gerät angezeigt wurden – entweder in Form einer System-/Out-of-App-Benachrichtigung im Notification Center oder einer In-App-Benachrichtigung in der mobilen App. Die Registerkarte **Erweitert** zeigt an, wie viele der Benachrichtigungen System- und wie viele In-App-Benachrichtigungen waren.
	
	*Warum die angezeigte Anzahl kleiner als die übermittelte Anzahl ist (die noch angezeigt werden muss)*
	
	1. Wenn für die Benachrichtigungskampagne ein Enddatum festgelegt wurde, wurde die Benachrichtigung möglicherweise übermittelt. Sie wurde jedoch nie angezeigt, wenn sie bereits abgelaufen war, als der App-Benutzer sie öffnen wollte.   
	2. Wenn es sich um ein In-App-Benachrichtigung handelt, wird die Benachrichtigung nur angezeigt, wenn der App-Benutzer die Anwendung öffnet. Falls der App-Benutzer die Anwendung nicht geöffnet hat, meldet das SDK bis zum Öffnen der Anwendung, dass die Benachrichtigung übermittelt aber noch nicht angezeigt wurde. 
	2. Wenn es sich um eine In-App-Benachrichtigung handelt, die bei einer bestimmten Aktivität oder in einem bestimmten Bildschirm angezeigt werden soll, wird auch hier die Benachrichtigung bis zum Öffnen der Anwendung im jeweiligen Bildschirm als übermittelt aber noch nicht geöffnet gemeldet. 
	
4.	**Benutzerinteraktionen**: Gibt die Anzahl der Meldungen an, mit denen der App-Benutzer interagiert hat, und enthält die Meldungen, die entweder geschlossen wurden oder für die eine Aktion ausgeführt wurde.

	- *Der App-Benutzer kann eine Benachrichtigung auf eine der folgenden Weisen öffnen:*
			
		1. Wenn es sich bei der Benachrichtigung um eine System-/Out-of-App-Benachrichtigung oder eine als reine Benachrichtigung gesendete In-App-Benachrichtigung handelt und der App-Benutzer auf die Benachrichtigung klickt.
		2. Wenn es sich bei der Benachrichtigung um eine In-App-Benachrichtigung mit Text, Webansicht oder Umfragen handelt und der App-Benutzer in der Benachrichtigung auf aktivieren klickt.
		3. Wenn es sich bei der Benachrichtigung um eine In-App-Benachrichtigung mit Webansicht handelt und der Benutzer auf eine URL in der Webansicht klickt [nur Android].
	
	- *Der App-Benutzer kann eine Benachrichtigung auf eine der folgenden Weisen schließen:*
	
		1. Durch Klicken auf die Schaltfläche „Schließen“, direkt in der Benachrichtigung. 
		2. Durch Wischen mit dem Finger oder Löschen der Benachrichtigung. 
		3. In-App-Benachrichtigungen mit Text-/Webinhalten und Umfragen werden dem App-Benutzer in der Regel in einem zweistufigen Prozess angezeigt. Zunächst wird dem Benutzer eine Benachrichtigung angezeigt und sobald er darauf klickt, sieht er die weiteren Text-/Web-/Umfrageninhalte. Der App-Benutzer kann eine Benachrichtigung während einem dieser Schritte schließen und dies wird in den Details in der Ansicht „Erweitert“ erfasst. 

5.	**Aktion durchgeführt**: Gibt die Anzahl der Meldungen an, für die der App-Benutzer explizit eine Aktion durchgeführt hat. Dies ist die interessanteste Zahl, da sie angibt, wie viele App-Benutzer an der Meldung, die Sie in der Benachrichtigung gesendet haben, interessiert waren.
 
> [AZURE.NOTE] Bei IOS- und Windows-Plattformen ist es möglich, dass die Out-of-App- und die In-App-Benachrichtigungen beide gleichzeitig angezeigt werden, wenn der Benutzer die App geöffnet hat und es sich bei der Kampagne um eine „Jederzeit“-Kampagne handelte. Dies könnte zur Folge haben, dass die Anzahl bei „Angezeigt“ höher ist als bei „Übermittelt“. Wenn der Benutzer mit der Benachrichtigung interagiert oder sie öffnet, könnte sogar die Anzahl bei „Benutzerinteraktionen/Geöffnet“ höher sein als bei „Übermittelt“.


![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=AcomDC_0309_2016-->