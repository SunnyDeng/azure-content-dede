<properties 
   pageTitle="Azure Mobile Engagement – Benutzeroberfläche - Segmente" 
   description="Erfahren Sie, wie Sie Segmente von Benutzern mithilfe von Azure Mobile Engagement erstellen und verwalten" 
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

# Erstellen und Verwalten von Segmenten von Benutzern zur Identifikation von Verwendungsmustern

In diesem Artikel wird die Registerkarte **SEGMENTE** im Portal **Mobile Engagement** beschrieben. Verwenden Sie das Portal **Mobile Engagement**, um Ihre mobilen Apps zu überwachen und zu verwalten. Beachten Sie, dass Sie zur Verwendung des Portals zuerst ein **Azure Mobile Engagement**-Konto erstellen müssen. Weitere Informationen finden Sie unter [Erstellen eines Azure Mobile Engagement-Kontos](mobile-engagement-create-account.md).

Der Abschnitt "Segmente" der Benutzeroberfläche ermöglicht Ihnen das Segmentieren Ihrer Benutzer basierend auf den verschiedenen Verhalten und Analysen, die Sie von der Anwendung und auch über die Segments-API erhalten können. Segmente werden erstmals 24 Stunden nach ihrer Erstellung berechnet und dann alle 24 Stunden basierend auf den neuesten Analyseinformationen neu berechnet. Sobald ein Segment berechnet ist, zeigt es jeden Tag ein Diagramm vom Typ "Tag-zu-Tag-Verlauf" an.


>[AZURE.NOTE]Viele Abschnitte der Benutzeroberfläche des **Mobile Engagement**-Portals enthalten die Schaltfläche **HILFE ANZEIGEN**. Drücken Sie diese Schaltfläche, um weitere Kontextinformationen zu einem bestimmten Bereich zu erhalten.

## Erstellen von Segmenten
Sie können im Abschnitt "Analyse" ein Segment basierend auf bis zu 10 Kriterien für einen bestimmten Zeitraum von bis zu 60 Tagen in der Vergangenheit erstellen. Beispielsweise können Sie ein Segment basierend auf Personen erstellen, die innerhalb der letzten 10 Tage in Ihrer App bestimmte Seiten besucht oder nach bestimmten Inhalten gesucht haben. Diese Informationen sind im Abschnitt "Analyse" verfügbar. Sie können sie nutzen, um ein Segment zu erstellen und anschließend eine Pushbenachrichtigung an diese Teilmenge von Benutzern einrichten, um sie zum Zurückkehren zur App zu bewegen.
 
> Hinweis: Sobald ein Segment berechnet wurde, kann es nicht bearbeitet werden. Es kann nur geklont (kopiert) oder entfernt (gelöscht) werden. Ein Segment kann innerhalb der gleichen Anwendung (mit der gleichen App-ID) und auch in andere Anwendungen (mit einer anderen App-ID) kopiert werden.
 
 ![segments1][35]

## Beispielsegmente
 ![segments2][36]

Mithilfe von Segmenten können Sie die Endbenutzer Ihrer Anwendung segmentieren. Das Segmentieren von Benutzern ist eine wichtige Marketingstrategie. Azure Mobile Engagement ermöglicht Ihnen das Abrufen von Verlaufsdaten und Erstellen benutzerdefinierter Segmente. Mit diesem leistungsfähigen Tool können Sie sich mit der Erfahrung Ihrer Kunden in Ihrer Anwendung vertraut machen. Sie können problemlos die Segmente analysieren und Ihre Segmente als Pushziele nutzen. Ein gängiger Anwendungsfall ist das Senden einer Pushbenachrichtigung zum Anregen Ihrer Endbenutzer, Ihre Anwendung im Store zu bewerten. Anstatt eine Benachrichtigung an alle Ihre Endbenutzer zu senden, können Sie ein Segment erstellen, das nur Benutzer angibt, die Ihre Anwendung im letzten Monat jeden Tag verwendet haben und eine ausgezeichnete Benutzererfahrung hatten. Wenn Sie weniger, überaus zielgerichtete Pushbenachrichtigungen senden, ist der Nutzen größer.
 
 ![segments3][37]

### Beispiele, die Sie basierend auf den wichtigsten Azure Mobile Engagement-Elementen erstellen können:
- Ereignis: Erstellen Sie ein Segment, das sich an einem bestimmten Ereignis in der Anwendung orientiert, das mehr als zweimal pro Woche aufgetreten ist. 
- Sitzung: Erstellen Sie ein Segment mit Benutzern, die die Anwendung letzte Woche mehr als fünf Mal verwendet haben.
- Aktivität: Erstellen Sie ein Segment von Benutzern, die eine Seite oder einen Inhalt letzten Monat mehr oder weniger als 10 Mal verwendet haben.
- Auftrag: Erstellen Sie ein Segment von Benutzern, die einen Auftrag mehr als zweimal täglich ausgeführt haben.
- Absturz: Erstellen Sie ein Segment aller Benutzer, bei denen letzte Woche ein Absturz mehr als 10 Mal aufgetreten ist. (Sie könnten an dieses Segment eine Pushbenachrichtigung mit einer Entschuldigung oder sogar einem Gutschein senden!)
- Fehler: Erstellen Sie ein Segment aller Benutzer, bei denen in den letzten 3 Tagen ein Fehler mehr als 100 Mal aufgetreten ist.
- App-Info: Erstellen Sie ein Segment, das sich an einer benutzerdefinierten App-Info ausrichtet, die während der letzten 25 Tage aufgetreten ist.
 
 ![segments4][38]

Um Segmente optimal zu nutzen, muss eine angepasste Integration des SDK in Ihre Anwendung mit einem Taggingplan mit "App-Info"-Tags erfolgt sein. Wechseln Sie dann zur Startseite der Benutzeroberfläche, wählen Sie die gewünschte Anwendung aus, und klicken Sie auf den Abschnitt "Segmente".

1. Wählen Sie den Abschnitt "Segmente" aus.
2. Klicken Sie auf die Schaltfläche "Neues Segment", um ein neues Segment zu erstellen.

## Ein Beispiel aus der Praxis: Erstellen Sie ein einfaches Segment basierend auf Informationen in "Sitzung"
Erstellen Sie ein Segment aller Endbenutzer, die Ihre App in der letzten Woche mindestens 50 Mal verwendet haben. Suchen Sie anschließend nur die Endbenutzer, die mindestens 30 Sekunden in Ihrer App pro Sitzung verbracht haben. Dadurch werden alle Endbenutzer angezeigt, die bislang eine positive Erfahrung mit der App gemacht haben. Dann könnte das erstellte Segment verwendet werden, um eine Pushbenachrichtigung an diese Endanwender mit der Bitte zu senden, Ihre App im Store zu bewerten.
 
 ![segments5][39]

1. Versehen Sie Ihr Segment mit einem Namen, um es schnell in der Liste „Segmente“ zu finden.
2. Klicken Sie auf die Schaltfläche "Erstellen".
 
 ![segments6][40]

Wählen Sie "Sitzung" aus.
 
 ![segments7][41]

1. Wählen Sie den Zeitraum "Letzte Woche" aus.
2. Klicken Sie auf "Weiter".
 
 ![segments8][42]

1. Wählen Sie den Operator aus, der in der Liste relevant ist: =; ≥, ≤.
2. Geben Sie die gewünschte Anzahl ein.
3. Wählen Sie das gewünschte Vorkommen aus. 
4. Klicken Sie auf "Weiter". Dieses Beispiel ist so eingerichtet, dass Benutzer in der letzten Woche gefunden werden sollen, die über 50 Sitzungen hatten.
 
 ![segments9][43]

Für die Segmentierung "Sitzung" können Sie die Dauer pro Sitzung als Kriterium auswählen.

1. Wählen Sie den Operator in der Liste aus.
2. Geben Sie die Dauer pro Sitzung an.
3. Klicken Sie auf Weiter. Bei diesem Beispiel werden aus allen Sitzungen, die gemäß des Abschnitts "Vorkommen" segmentiert wurden, nur die Benutzer ausgewählt, deren Sitzung jeweils länger als 30 Sekunden gedauert haben.
 
 ![segments10][44]

Benennen Sie Ihr Kriterium, um es aus dem Trichter "Abgeschlossen" abrufen zu können, und klicken Sie auf "Fertig stellen".
 
 ![segments11][45]

Wenn Sie Ihr Kriterium eingerichtet haben, wird es im Trichter "Segment" angezeigt. Da ein Segment auf Analysedaten basiert, werden Segmente einmal pro Tag berechnet. Bei diesem Beispiel stimmen 47,7 % der Endbenutzer mit dem Kriterium überein. Dies sollten die Benutzer mit einer guten Erfahrung sein, die Ihnen wahrscheinlich eine höhere Bewertung geben, wenn Sie Ihnen eine Pushbenachrichtigung mit einer Bitte zur Bewertung Ihrer App im Store senden.


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