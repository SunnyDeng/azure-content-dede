<properties 
   pageTitle="Azure Mobile Engagement - Benutzeroberfläche – Mein Konto" 
   description="Erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement Ihr Konto-Profil verwalten und Geräte testen" 
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

# Verwalten Ihres Konto-Profils und Testen der Geräte 
Im Abschnitt "Mein Konto" der Benutzeroberfläche können Sie die Einstellungen im Zusammenhang mit Ihrem Konto anzeigen und ändern, einschließlich Ihrer Profileinstellungen und Testgeräte-IDs. Diese Einstellungen enthalten Elemente, auf die auch über die Device-API zugegriffen werden kann.

### Weitere Informationen
-  [Handbuch zur Problembehandlung - Dienst][Link 24]

![MyAccount1][7]

## Profil:
Sie können alle Einstellungen Ihres Kontos anzeigen oder ändern: Kennwort, Vorname, Nachname, Unternehmen, Telefonnummer, Zeitzone oder An- oder Abmelden für E-Mail-Updates. Sie können auch auf der "Startseite" anderen Benutzern die Berechtigung zur Nutzung Ihrer Anwendung basierend auf ihrer E-Mail-Adresse zuweisen.

### Weitere Informationen
-  [Dokumentation zur Benutzeroberfläche - Startseite][Link 13]

![MyAccount2][8]

## Geräte:
Sie können IDs der Testgeräte, die Sie verwenden können, um Ihre Reach- oder Push-Kampagnen zu testen, anzeigen, hinzufügen oder entfernen. Wenn Sie auf "Neues Gerät" klicken, werden kontextbezogene Anweisungen für das Bestimmen der Geräte-ID von Geräten für jede Plattform (iOS, Android, Windows Phone usw.) angezeigt.
 
![MyAccount3][9]
 
Zum Verwenden der Push-API oder Device-API müssen Sie die eindeutige Gerätekennung (den "deviceid"-Parameter) Ihrer Benutzer kennen. Es gibt mehrere Möglichkeiten, diese abzurufen:
 
1. In Ihrem Back-End können Sie die "Get"-Funktion der Device-API verwenden, um eine vollständige Liste der Geräte-IDs zu erhalten.
2. In Ihrer App können Sie zum Abruf das SDK nutzen. (Rufen Sie für Android die "getDeviceID()"-Funktion der "Agent"-Klasse und für iOS die "deviceid-Eigenschaft" der "Agent"-Klasse auf.)
3. Wenn bei einer Reach-Ankündigung die der Ankündigung zugeordnete Aktions-URL das {deviceid}-Muster enthält, wird es automatisch durch die ID des Geräts ersetzt, das die Aktion auslöst. http://<example>.com/registeruser?deviceid={deviceid}&otherparam=myparamdata wird ersetzt durch: http://<example>.com/registeruser?deviceid=XXXXXXXXXXXXXXXX&otherparam=myparamdata 
4. Wenn bei einer Reach-Webankündigung der HTML-Code der Ankündigung das {deviceid}-Muster enthält, wird es automatisch durch die ID des Geräts ersetzt, das die Webankündigung auslöst. Hier ist meine Geräte-ID: {deviceid} wird ersetzt durch: Hier ist meine Geräte-ID: XXXXXXXXXXXXXXXX
5.  Öffnen Sie die Anwendung auf Ihrem Gerät, und lösen Sie ein Ereignis in Ihrer App aus, das mit einem Tag versehen wurde. In "Benutzeroberfläche - Ihre App - Überwachen - Ereignisse - Details" finden Sie das ausgelöste Ereignis in der Liste. Klicken Sie im Abschnitt "Überwachen" auf dieses Ereignis. Ihre Geräte-ID sollte in der Liste der Geräte vorhanden sein, auf denen dieses Ereignis erfolgt ist. Anschließend können Sie diese Geräte-ID kopieren und in "Benutzeroberfläche - Mein Konto - Geräte - Neues Gerät - Geräteplattform auswählen" registrieren.
>(Bedenken Sie, dass wenn IDFA für iOS deaktiviert ist, die Geräte-ID sich mit der Zeit ändern kann, wenn Sie die App deinstallieren und neu installieren.)

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


 
 

<!---HONumber=August15_HO7-->