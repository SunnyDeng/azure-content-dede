<properties 
   pageTitle="Azure Mobile Engagement - Handbuch zur Problembehandlung - Push/Reach" 
   description="Behandlung von Problemen bei der Benutzerinteraktion und -benachrichtigung in Azure Mobile Engagement" 
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


# Handbuch zur Behandlung von Problemen bei Push und Reach

Im Folgenden finden Sie mögliche Probleme, die hinsichtlich der Art, in der Azure Mobile Engagement Informationen an Ihre Benutzer sendet, auftreten können.
 
## Fehler bei Pushvorgängen

### Problem
- Pushvorgänge funktionieren nicht (App-intern, App-extern oder beides).

### Ursachen
- Häufig ist ein Pushfehler ein Hinweis dafür, dass Azure Mobile Engagement, Reach oder eine andere erweiterte Funktion von Azure Mobile Engagement nicht ordnungsgemäß integriert wurde, oder dass ein Upgrade im SDK erforderlich ist, um einen bekannten Fehler mit einem neuen Betriebssystem oder einer Geräteplattform zu beheben.
- Testen Sie nur einen App-internen Push und einen App-externen Push um zu ermitteln, ob es sich um einen Fehler innerhalb oder außerhalb der App handelt.
- Führen Sie den Test im Rahmen der Problembehandlung sowohl über die Benutzeroberfläche als auch über die API durch, und prüfen Sie, welche zusätzlichen Fehlerinformationen jeweils verfügbar sind.
- App-externe Pushvorgänge funktionieren nur dann, wenn sowohl Azure Mobile Engagement als auch Reach in das SDK integriert wurden.
- App-externe Pushvorgänge funktionieren nicht, wenn Zertifikate ungültig sind oder PROD bzw. DEV nicht richtig verwenden (nur iOS). (**Hinweis:** "App-externe" Pushbenachrichtigungen dürfen nicht an iOS zugestellt werden, wenn Sie sowohl die Entwicklungs- (DEV) als auch die Produktionsversion (PROD) Ihrer Anwendung auf demselben Gerät installiert haben, weil das Ihrem Zertifikat zugeordnete Sicherheitstoken von Apple ungültig gemacht werden kann. Um dieses Problem zu beheben, deinstallieren Sie die DEV- und die PROD-Version Ihrer Anwendung, und installieren Sie nur eine der Versionen auf Ihrem Gerät neu.)
- App-externe Pushvorgänge werden auf den verschiedenen Plattformen unterschiedlich verarbeitet (iOS zeigt weniger Informationen an als Android, wenn systemeigene Pushvorgänge auf einem Gerät deaktiviert werden, die API stellt mehr Informationen zu Pushstatistiken bereit als die Benutzeroberfläche).
- App-externe Pushvorgänge können von Kunden auf Betriebssystemebene blockiert werden (iOS und Android).
- App-externe Pushvorgänge werden in der Azure Mobile Engagement-Benutzeroberfläche als deaktiviert angezeigt, wenn sie nicht ordnungsgemäß integriert wurden. Bei der API hingegen treten nur "stille" Fehler (ohne Fehlermeldung) auf.
- App-interne Pushvorgänge funktionieren nur, wenn sowohl Azure Mobile Engagement als auch Reach in das SDK integriert wurden.
- GCM- und ADM-Pushvorgänge funktionieren nur, wenn Azure Mobile Engagement und der spezifische Server in das SDK integriert wurden (nur Android).
- App-interne und -externe Pushvorgänge sollten separat getestet werden, um festzustellen, ob es sich um ein Push- oder Reach-Problem handelt.
- App-interne Pushvorgänge erfordern, dass die App zum Empfang geöffnet ist.
- App-externe Pushvorgänge sind häufig über ein opt-in- oder opt-out-Infotag der App für die Filterung eingerichtet.
- Wenn Sie in Reach eine benutzerdefinierte Kategorie zur Anzeige interner Benachrichtigungen der App verwenden, müssen Sie dem richtigen Lebenszyklus der Benachrichtigung folgen. Ansonsten wird die Benachrichtigung möglicherweise nicht gelöscht, wenn der Benutzer sie schließt.
- Wenn Sie eine Kampagne ohne Enddatum starten und ein Gerät die App-interne Benachrichtigung erhält, diese aber noch nicht angezeigt wird, erhält der Benutzer die Benachrichtigung bei der nächsten Anmeldung an der App - selbst dann, wenn Sie die Kampagne manuell beenden.
- Bei Problemen mit der Push-API sollten Sie bestätigen, dass Sie tatsächlich die Push-API und nicht die Reach-API benötigen (die Reach-API wird häufiger verwendet), und dass keine Verwechslung der Parameter "payload" und "notifier" vorliegt.
- Testen Sie Ihre Pushkampagne sowohl mit einem Gerät, das über WIFI verbunden ist, als auch mit einem Gerät, das über 3G verbunden ist, um die Netzwerkverbindung als mögliche Problemquelle auszuschließen.

### Weitere Informationen

- [Handbuch zur Problembehandlung - SDK][Link 25], [Handbuch zur Problembehandlung - Push][Link 23], [SDK-Dokumentation - iOS - Vorbereiten Ihrer Anwendung für Apple-Pushbenachrichtigungen][Link 5]
 
## Testen von Pushvorgängen

### Problem
- Pushvorgänge können basierend auf einer Geräte-ID an ein bestimmtes Gerät gesendet werden.

### Ursachen

- Testgeräte sind für jede Plattform anders eingerichtet. Wenn Sie jedoch auf einem Testgerät ein Ereignis in Ihrer App generieren, sollten Sie auf jeder Plattform in der Lage sein, im Portal Ihre Geräte-ID zu ermitteln.
- Testgeräte mit IDFA bzw. IDFV arbeiten unterschiedlich (nur iOS).

### Weitere Informationen

- [Dokumentation zur Benutzeroberfläche - Reach][Link 17]
 
## Anpassung von Pushvorgängen

### Problem
- Erweiterte Pushinhaltselemente funktionieren nicht (Badge, Klingeln, Vibrieren, Bild usw.).
- Links in Pushes funktionieren nicht (App-extern, App-intern, Verweis auf Website, Speicherort in der App).
- Pushstatistiken zeigen, dass ein Push nicht an so viele Empfänger wie erwartet gesendet wurde (zu viele oder zu wenige).
- Push wird dupliziert und zweimal empfangen.
- Testgeräte für Azure Mobile Engagement-Pushvorgänge kann nicht registriert werden (mit eigener App für Prod oder DEV).

### Ursachen

- Für eine Verlinkung mit einem spezifischen App-internen Speicherort sind "categories" erforderlich (nur Android).
- Tief verknüpfende Schemas zur Umleitung von Benutzern an einen anderen Ort, nachdem sie auf eine Pushbenachrichtigung geklickt haben, müssen in Ihrer Anwendung und im Geräte-BS erstellt und mit diesen verwaltet werden, nicht direkt mit Mobile Service Engagement. (**Hinweis:** App-externe Benachrichtigungen können unter iOS nicht direkt mit App-Speicherorten verknüpft werden, im Gegensatz zu Android.)
- Externe Imageserver müssen in der Lage sein, HTTP "GET" und "HEAD" zu verwenden, damit Pushvorgänge für große Bilder funktionieren (nur Android).
- Sie können in Ihrem Code den Azure Mobile Engagement-Agent deaktivieren, wenn die Tastatur geöffnet ist, und den Azure Mobile Engagement-Agent erneut aktivieren, wenn die Tastatur geschlossen wurde. So verhindern Sie, dass die Tastatur das Aussehen Ihrer Benachrichtigung beeinflusst (nur iOS).
- Einige Elemente funktionieren in Testsimulationen nicht, in realen Kampagnen schon (Badge, Klingeln, Vibrieren, Bild usw.).
- Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.
- Um das Problem zu isolieren, führen Sie eine Testkampagne, eine simulierte Kampagne und eine reale Kampagne aus, da jede dieser Kampagnentypen leicht anders funktioniert.
- Die Dauer, für die die Ausführung Ihrer "App-internen" und "Jederzeit"-Kampagnen geplant ist kann sich auf die Zustellungszahlen auswirken, weil eine Kampagne nur an Benutzer zugestellt wird, die "App-intern" sind, während die Kampagne ausgeführt wird (und an Benutzer, die ihre Geräteeinstellungen so festgelegt haben, dass sie Benachrichtigungen "App-extern" empfangen).
- Die Unterschiede zwischen Android und iOS bei der Behandlungsweise von App-externen Benachrichtigungen erschweren den direkten Vergleich von Pushstatistiken zwischen der Android- und iOS-Version Ihrer Anwendung. Android bietet mehr Benachrichtigungsinformationen auf Betriebssystemebene als iOS. Android meldet, wenn eine systemeigene Benachrichtigung im Benachrichtigungscenter empfangen, darauf geklickt oder sie gelöscht wird. iOS meldet diese Informationen nicht, es sei denn, 
es wird auf die Benachrichtigung geklickt. 
- Der Hauptgrund für die Abweichung zwischen Pushzahlen und Zustellungszahlen für Reach-Kampagnen ist, dass "App-interne" und "App-externe" Benachrichtigungen unterschiedlich gezählt werden. "App-interne" Benachrichtigungen werden von Mobile Engagement verarbeitet, aber "App-externe" Benachrichtigungen werden vom Benachrichtigungscenter im Betriebssystem Ihres Geräts verarbeitet.

### Weitere Informationen

- [Verfahren - Erster Push][Link 27], [Handbuch zur Problembehandlung - Push][Link 23], [HTTP-Protokollinformationen](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
- [Apple-Entwickler - "Opt Out" Benutzeroberflächeninformationen](http://support.apple.com/kb/HT3576), [Apple-Entwickler - "Opt Out" Entwicklerinformationen](https://developer.apple.com/notifications/), [Apple-Entwickler - "Opt Out" Problembehandlung](https://developer.apple.com/library/ios/technotes/tn2265/), [Apple-Entwickler - URL-Schema](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html)
- [Android-Entwickler - "Opt Out" Benutzeroberflächeninformationen](http://developer.android.com/about/versions/jelly-bean.html), [Android-Entwickler - "Opt Out" Muster](http://developer.android.com/design/patterns/notifications.html), [Android-Entwickler - "Opt Out" Notifier](http://developer.android.com/guide/topics/ui/notifiers/notifications.html), [Android-Entwickler - Vorschau](https://developer.android.com/preview/notifications.html), [Android-Entwickler - "Opt Out" Referenz](http://developer.android.com/reference/android/app/Notification.html), [Android-Entwickler - "Intent-Filter"-Schema](http://developer.android.com/guide/components/intents-filters.html#DataTest), 
 [Android-Entwickler - "Intent-Filter"-Referenzhandbuch](http://developer.android.com/reference/android/content/Intent.html#toUri)

## Zielgruppenadressierung für Pushvorgänge

### Problem
- Die integrierte Zielgruppenadressierung funktioniert nicht wie erwartet.
- Die Zielgruppenadressierung über Infotags der App funktioniert nicht wie erwartet.
- Die Zielgruppenadressierung über den geografischen Standort funktioniert nicht wie erwartet.
- Sprachoptionen funktionieren nicht wie erwartet.

### Ursachen

- Stellen Sie sicher, dass Sie Infotags der App über die Azure Mobile Engagement-Benutzeroberfläche oder -API hochgeladen haben.
- Eine Drosselung der Pushgeschwindigkeit oder des Pushkontingents auf Anwendungsebene oder eine Einschränkung der Zielgruppe auf Kampagnenebene kann dazu führen, dass eine Person ein bestimmtes Pushereignis nicht empfängt, wenn sie andere Ihrer Kriterien für die Zielgruppenadressierung erfüllt. 
- Das Festlegen einer "Sprache" unterscheidet sich von einer Zielgruppenadressierung basierend auf Land oder Gebietsschema. Diese wiederum unterscheidet sich von einer Zielgruppenadressierung basierend auf Geolocation nach Telefon- oder GPS-Standort.
- Die Nachricht in der "Standardsprache" wird an beliebige Kunden gesendet, die ihr Gerät nicht auf eine der alternativen Sprachen eingestellt haben, die Sie angeben.

### Weitere Informationen

- [Dokumentation zur Benutzeroberfläche - Reach][Link 17], [Dokumentation zur Benutzeroberfläche - Einstellungen][Link 20], [API-Dokumentation - Reach][Link 4], [API-Dokumentation - Push][Link 4], [API-Dokumentation - Gerät][Link 4]
 
## Planen von Pushvorgängen

### Problem
- Die Planung von Pushvorgängen funktioniert nicht wie erwartet (Pushes werden zu früh oder verzögert gesendet).

### Ursachen

- Zeitzonen können Probleme bei der Planung verursachen, insbesondere bei Verwendung der Zeitzone für die Endbenutzer.
- Erweiterte Pushfunktionen können Pushvorgänge verzögern.
- Eine Zielgruppenadressierung basierend auf Telefoneinstellungen (anstelle von Infotags der App) kann Pushvorgänge verzögern, da Azure Mobile Engagement möglicherweise in Echtzeit Daten vom Telefon anfordern muss, bevor ein Push gesendet wird.
- Für Kampagnen ohne Enddatum wird die Pushbenachrichtigung lokal auf dem Gerät gespeichert und beim nächsten Öffnen der App angezeigt - selbst dann, wenn die Kampagne manuell beendet wurde.
- Das gleichzeitige Starten mehrerer Kampagnen kann dazu führen, dass der Scanvorgang der Benutzerbasis länger dauert (versuchen Sie, maximal vier Kampagnen jeweils zeitversetzt zu starten, und beziehen Sie nur aktive Benutzer ein, sodass ältere Benutzer nicht gescannt werden müssen).
- Wenn Sie im Abschnitt "Kampagne" einer Reach-Kampagne die Option "Zielgruppe ignorieren, Push wird über die API an Benutzer gesendet" verwenden, wird die Kampagne NICHT automatisch gesendet. Sie müssen die Kampagne manuell über die Reach-API senden.
- Wenn Sie in Reach eine benutzerdefinierte Kategorie zur Anzeige interner Benachrichtigungen der App verwenden, müssen Sie dem richtigen Lebenszyklus einer Benachrichtigung folgen. Ansonsten wird die Benachrichtigung möglicherweise nicht gelöscht, wenn der Benutzer sie schließt.

### Weitere Informationen

- [Verfahren für Reach - Kampagne planen][Link 3], [Dokumentation zur Benutzeroberfläche - Reach - Neue Pushkampagne][Link 27]
 
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

<!--HONumber=54-->