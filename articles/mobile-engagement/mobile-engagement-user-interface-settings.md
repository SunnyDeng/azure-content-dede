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
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# Verwaltung der globalen Einstellungen Ihrer Anwendung
Die verfügbaren Optionen im Menü "Einstellungen" für eine Anwendung variieren abhängig von der Plattform der Anwendung und den Berechtigungen, die Sie der Anwendung erteilt haben. Dazu zählen: Details, Projekte, Systemeigener Push, Pushgeschwindigkeit, SDK, Nachverfolgung, App-Info, Kommerzieller Druck und Berechtigungen. Nur die Menüoption "App-Info" im Abschnitt "Einstellungen" der Benutzeroberfläche enthält Elemente, die mit dem Feature "Tag" der Device-API verwaltet werden können. "Glossar" in "Konzepte" enthält die Definitionen der Begriffe und Abkürzungen, wie z. B. die folgenden: APNS, GCM, IDFA, API, SDK, API-Schlüssel, SDK-Schlüssel, Anwendungs-ID (App-ID), AppStore-ID, Tagplan, Benutzer-ID, Geräte-ID, App-Delegat, Stapelüberwachung und Deep Linking.

### Weitere Informationen
- [API-Dokumentation - Geräte-API][Link 4], [Konzepte - Glossar][Link 6], [Handbuch zur Problembehandlung - Dienst][Link 24]

  ![settings1][46]

## Details
Ermöglicht Ihnen, den Namen und die Beschreibung Ihrer Anwendung zu ändern sowie deren Besitzer und Ihre Rollenberechtigungen anzuzeigen. Analysekonfiguration: Ermöglicht das Anzeigen oder Ändern des Anfangstags der Woche und des Bindungszeitraums in Tagen.
 
  ![settings2][47]
 
## Projekte
Ermöglicht Ihnen die Auswahl aller Projekte, in denen die Anwendung angezeigt werden soll. Sie können auch ein Projekt suchen und den Namen, die Beschreibung, den Besitzer und Ihre Rollenberechtigungen für Projekte anzeigen, zu denen die Anwendung gehört.

### Weitere Informationen
-    [Dokumentation zur Benutzeroberfläche – Startseite][Link 13]
 
  ![settings3][48]

## Systemeigener Push
Ermöglicht das Registrieren eines neuen Zertifikats oder Löschen eines vorhandenen Zertifikats für die Verwendung mit systemeigenem Push. "Systemeigener Push" ermöglicht Azure Mobile Engagement Pushübertragungen an Ihre Anwendung zu jeder Zeit, selbst wenn sie nicht ausgeführt wird. Nach dem Angeben von Anmeldeinformationen oder Zertifikaten für mindestens einen systemeigenen Pushdienst können Sie beim Erstellen von Reach-Kampagnen "Jederzeit" auswählen und auch den Parameter "Notifier" in der PUSH-API verwenden.

### Weitere Informationen
- [API-Dokumentation - Reach-API][Link 4], [API-Dokumentation - Push-API][Link 4], [Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkampagne][Link 27]

### Apple Push Notification Service (APNS)
Zum Aktivieren des systemeigenen Pushs mithilfe von Apple Push Notification Service müssen Sie Ihr Zertifikat registrieren. Sie müssen den Typ des Zertifikats entweder als DEV (Entwicklung) oder PROD (Produktion) angeben. Anschließend müssen Sie Ihr Zertifikat und Kennwort hochladen.

### Weitere Informationen
- [SDK-Dokumentation - iOS - Vorbereiten der Anwendung für Apple-Pushbenachrichtigungen][Link 5]
 
![settings4][49]
 
### Windows-Pushbenachrichtigungsdienst (Windows Push Notification Service, WPNS)
Um systemeigenen Push mit dem Windows-Pushbenachrichtigungsdienst zu aktivieren, müssen Sie die Anmeldeinformationen Ihrer Anwendung bereitstellen. Sie benötigen die Paketsicherheits-ID (SID) und den geheimen Schlüssel.
 
![settings5][50]
 
### Google Cloud Messaging (GCM) für Android
Um systemeigenen Push über GCM zu aktivieren, müssen Sie den Anweisungen von Google folgen. Dann müssen einen "Server::Simple"-API-Schlüssel einfügen, der ohne IP-Einschränkungen konfiguriert ist. Erfordert Integration mit dem SDK für Android ab Version 1.12.0.

### Weitere Informationen
- [SDK-Dokumentation - Android - Integrieren von GCM][Link 5], [Google Developer - Handbuch für GCM](http://developer.android.com/guide/google/gcm/gs.html), [Google Developer - GCM-Dokumentation](http://developer.android.com/google/gcm/index.html)
 
### Amazon Device Messaging (ADM) für Android
Zum Aktivieren von systemeigenem Push mit ADM müssen Sie Amazon-<OAuth credentials> angeben, bestehend aus einer Client-ID und einem Clientschlüssel (erfordert die Integration mit dem SDK für Android ab Version 2.1.0).

### Weitere Informationen
- [SDK-Dokumentation - Android - Integrieren von ADM][Link 5], [Amazon Developer - ADM-Dokumentation](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## Pushgeschwindigkeit
Zeigt die aktuelle Pushgeschwindigkeit Ihrer Anwendung und ermöglicht Ihnen, die Pushgeschwindigkeit der Anwendung festzulegen. Die Pushgeschwindigkeit definiert die maximale Anzahl von Pushvorgängen pro Sekunde, die das Reach-Modul ausführt. Dies kann in Situationen hilfreich sein, in denen Ihre eigenen Server nach Aktivierung einer Kampagne durch zu viele Anforderungen pro Sekunde überlastet werden.
 
  ![settings7][52]

## Nachverfolgung
Das Feature für die Nachverfolgung dient zum Verfolgen der Ursprünge der Installationen Ihrer iOS- und Android-Anwendungen. Es informiert Sie, wo die Benutzer Ihre Anwendung heruntergeladen haben (d. h. aus welchem Store für Apps) und welche Quelle sie hier her gebracht hat (d. h. Anzeigenkampagne, Blog, Website, E-Mail, SMS usw.). Das Feature zur Nachverfolgung von Azure Mobile Engagement muss als separater Schritt aus dem SDK in Ihre Anwendung integriert werden.

### Weitere Informationen
- [SDK-Dokumentation - Android - Integrieren][Link 5], [SDK-Dokumentation - iOS - Integrieren][Link 5]
 
### Stores
Ermöglicht die Registrierung aller Stores, aus denen die Anwendung heruntergeladen werden kann, basierend auf deren Namen und zugehörigen Download-URLs. Dadurch können Sie Speicherorte zum Hosten von Nachverfolgungs-URLs erstellen. Stores können auf dieser Seite erstellt oder gelöscht werden. Durch Klicken auf das Symbol zum Erstellen einer neuen Nachverfolgungs-URL gelangen Sie zur weiter unten erläuterten Seite zu Nachverfolgung-URLs. Es gibt mehrere Möglichkeiten zu verfolgen, wo Ihre Benutzer Ihre App herunterladen:

-    Verwenden eines Adservers eines Drittanbieters (Azure Mobile Engagement unterstützt derzeit [SmartAd](http://smartadserver.fr/) und [Surikate](http://www.surikate.com/)).
-    Verwenden eines Zuweisungsdiensts eines Drittanbieters (Azure Mobile Engagement unterstützt derzeit [Mobile App Tracking](http://www.mobileapptracking.com/); [Trademob](http://www.trademob.com/) wird in Kürze voraussichtlich ebenfalls unterstützt.)
-    Verwenden eines Install Referrers eines Drittanbieters (Azure Mobile Engagement unterstützt derzeit [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/) - Nur Android).
-    Verwenden manuell erstellter Berichte.
 
  ![settings8][53]
 
### Anzeigenkampagnen
Ermöglichen Ihnen die Erstellung neuer Anzeigenkampagnen bestehend aus einem Adservernamen, einer Kampagne-ID und der Quelle, aus der die Anwendung heruntergeladen werden kann.
 
  ![settings9][54]
 
### Nachverfolgungs-URLs
Ermöglicht das Erstellen von Nachverfolgungs-URLs zum Verwenden als Ziel-URLs in Ihren Quellen (Anzeigenkampagne, Blog, Website, E-Mail, SMS usw.), die Benutzer zu den Stores umleiten können, in denen sie Ihre Anwendung herunterladen können. Ermöglicht das Anzeigen aller Nachverfolgungs-URLs, die Sie bereits erstellt haben. Eine Nachverfolgungs-URL kann als Aktions-URL einer Anzeigenkampagne oder Reach-Ankündigung verwendet werden, um Ihre Benutzer zum Herunterladen einer Ihrer Apps in einer anderen App einzuladen. Eine Nachverfolgungs-URL ermöglicht das Umleiten zur Download-URL, die dem ausgewählten Store zugeordnet ist. Zugleich ermöglicht sie Ihrem Nachverfolgungssystem das Protokollieren des Stores, aus dem die Anwendung zum Installationszeitpunkt heruntergeladen wird. Wenn eine Quelle angegeben ist, zeichnet unser Nachverfolgungssystem auch diese auf, sodass Sie zwischen den einzelnen Anzeigenkampagnen unterscheiden können, die Sie für Ihre Anwendung erstellen.

Das Erstellen einer neuen Nachverfolgungs-URL erfordert das Angeben eines Stores und der Quelle als entweder "Keine", "Benutzerdefiniert" oder "Adserver".

-    Die Quelle "Keine" erzeugt eine Standardnachverfolgungs-URL.
-    Die Quelle "Benutzerdefiniert" ermöglicht Ihnen das Angeben einer URL auf einem externen Server zum Herunterladen der Anwendung.
-    Die Quelle "Adserver" erzeugt eine Standardnachverfolgungs-URL in einem Adserver mit Standardnamen.
 
### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Neue Pushkampagne][Link 27] 
 
### Erstellen einer Nachverfolgungs-URL
Sie können auch eine Nachverfolgungs-URL erstellen, die Benutzern das Herunterladen einer Ihrer Anwendungen aus dem Inhaltsabschnitt einer neuen Reach-Kampagne ermöglicht.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Pushübertragung von Inhalten][Link 29]

![settings10][55]

## App-Info
Ermöglicht das Registrieren zusätzlicher Informationen, die den Benutzern Ihrer Anwendung zugeordnet sind. Diese Informationen können über Ihre Anwendung (mithilfe des SDK) oder Ihr Back-Ends (mithilfe der Device-API) eingefügt werden.

### Weitere Informationen
- [API-Dokumentation - Device-API][Link 4]

Durch Registrieren von Anwendungsinformationstags können Sie Ihre Reach-Kampagnen segmentieren, indem Sie darauf basierend bestimmte Reach-Zielgruppenkriterien erstellen. Sie können Namen und Typ der vorhandenen App-Infotags anzeigen oder neue App-Infotags basierend auf dem Namen und Typ (String, Date, Integer oder Boolean) hinzufügen.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche – Reichweite – Neue Pushkampagne][Link 27]
 
![settings11][56]
 
## Kommerzieller Druck
Pushkontingente ermöglichen das Definieren, wie häufig ein Pushvorgang an ein Gerät in einem bestimmten Zeitraum maximal erfolgen darf. Pushkontingente werden nur von Reach-Kampagnen verwendet, bei denen die Option "Pushkontingente anwenden" aktiviert ist. Pushkontingente können entweder nach Segment oder standardmäßig verwaltet werden. Kontingente können so festgelegt werden, dass eine maximale Anzahl von Pushvorgängen nur über einen gleitenden Zeitraum der letzten Stunde, des letzten Tags, der letzten Woche oder des letzten Monats gesendet werden.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkampagne][Link 27], [Dokumentation zur Benutzeroberfläche - Segmente][Link 18]

### Kontingente:
- Standardmäßiges Kontingent: Dieses Kontingent gilt für Benutzer, die mit keinem Segment im nachstehenden Abschnitt „Kontingent nach Segment“ übereinstimmen. Standardmäßig ist kein Kontingent festgelegt.
- Kontingent nach Segment: Wenn ein Kontingent für eine Gruppe von Benutzern gelten soll, müssen Sie ein Segment erstellen, um die Benutzer zu bestimmen, für die ein Kontingent gelten soll. Es kann sich um ein Segment vom Typ „Aktive Benutzer“, das anhand der Anzahl der gestarteten Sitzungen definiert wird, oder einen anderen für Sie interessanten Aspekt zum Definieren der Benutzergruppe handeln. Wenn ein Gerät mit mehreren Segmenten mit einem definierten Kontingent übereinstimmt, gilt nur das Kontingent mit der höchsten maximalen Anzahl von Pushvorgängen pro Stunde.

![settings12][57]
 
## Berechtigungen
Ermöglicht das Suchen und Anzeigen von E-Mail-Adresse, Name, Unternehmen und Berechtigungsstufe von Benutzern Ihrer Anwendung. Das Konzept von Berechtigungen ist eine Ergänzung der Projektrolle. Es ermöglicht Ihnen das Zuordnen einer Gruppe von Berechtigungen zu einem bestimmten Benutzer, der Zugriff auf Ihre Anwendung hat.

### Derzeit gibt es die folgenden Berechtigungsstufen:
-    "Reach-Kampagnenersteller" (ein Benutzer, der Reach-Kampagnen erstellen darf) 
-    "Reach-Kampagnenverwalter" (ein Benutzer, der Reach-Kampagnen erstellen, aktivieren, anhalten, beenden und löschen darf)

> Hinweis: Wenn ein Benutzer eine Projektrolle inne hat und über eine Gruppe von Berechtigungen für eine bestimmte Anwendung verfügt, wird das weniger restriktive Konzept verwendet. Daher wird empfohlen, dass bei Verwendung von Berechtigungen die Projektrolle Ihrer Benutzer auf "Anzeigender Benutzer" (die restriktivste Rolle) festgelegt wird und weitere weniger restriktive Berechtigungen auf Anwendungsebene hinzugefügt werden.

### Weitere Informationen
- [Dokumentation zur Benutzeroberfläche - Startseite][Link 13]  
 
![settings13][58]

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
 

<!---HONumber=July15_HO2-->