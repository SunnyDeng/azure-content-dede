<properties 
   pageTitle="Azure Mobile Engagement - Benutzeroberfläche - Vorgehensweise Reach"
   description="Übersicht über die Benutzeroberfläche von Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Einführung">Einführung</a> <a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigation</a> <a href="../mobile-engagement-user-interface-home/" title="Home">Home</a> <a href="../mobile-engagement-user-interface-my-account" title="Mein Konto">Mein Konto</a> <a href="../mobile-engagement-user-interface-analytics" title="Analysen">Analysen</a> <a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitor</a> <a href="../mobile-engagement-user-interface-reach" title="Erreichen">Erreichen</a> <a href="../mobile-engagement-user-interface-reach-campaign" title="Reichweitenkampagne">Reichweitenkampagne</a> <a href="../mobile-engagement-user-interface-reach-criterion" title="Reichweitenkriterium">Reichweitenkriterium</a> <a href="../mobile-engagement-user-interface-reach-content" title="Reichweiteninhalt">Reichweiteninhalt</a> <a href="../mobile-engagement-how-tos" title="Vorgehensweise Reichweite">Vorgehensweise Reichweite</a> <a href="../mobile-engagement-user-interface-segments" title="Segmente">Segmente</a> <a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Dashboard</a> <a href="../mobile-engagement-user-interface-settings" title="Einstellungen">Einstellungen</a>
</div>

# Erste Schritte bei der  Verwendung und Verwaltung von Push-Vorgängen, um Ihre Endbenutzern zu erreichen.
Sobald das SDK vollständig in Ihrer App integriert ist, können Sie erste Schritte mit dem Abschnitt „Reichweite“ der Benutzeroberfläche machen, um Pushbenachrichtigungen an die Benutzer Ihrer App zu senden.

## Ausführen der ersten Pushbenachrichtigungskampagne
-    Bestätigen Sie, dass Reach mit dem SDK in Ihrer App integriert ist. 
-    Wählen Sie Ihre Anwendung.
 
![First1][1]

-    Wechseln Sie zum Abschnitt „Reach“, und klicken Sie dann auf „Neue Ankündigung“.
 
![First2][2]

-    Erstellen Sie eine neue Kampagne und benennen Sie sie.
 
 ![First3][3]

-    Wähle Sie die Art der Übermittlung für die Benachrichtigung aus: „Nur In-App“.
 
![First4][4]

-    Erstellen Sie die per Push zu übertragende Nachricht.
 
![First5][5]

-    Sie können einen Titel für die Benachrichtigung eingeben (optional).
-    Geben Sie den Inhalt der Nachricht ein.
-    Sie können ein Bild hochladen. Beachten Sie, dass die Dateigröße 32.768 Bytes nicht überschreiten darf.
-    Zudem haben Sie die Möglichkeit, weitere Optionen auszuwählen, aber im Rahmen dieses Lernprogramms, werden diese später gezeigt.

-    Wählen Sie als Inhaltstyp „Nur Benachrichtigung“ aus.
 
![First6][6]

-    Erstellen Sie Ihre Pushbenachrichtigung, damit diese in Ihrer Kampagnenliste angezeigt wird.
 
![First7][7]

## Testen der Pushbenachrichtigungskampagne
![Test1][8]

-    Registrieren Sie Ihr Gerät.
-    Aktivieren Sie das Kontrollkästchen für das Gerät, an das die Pushübertragung gesendet werden soll.
-    Klicken Sie auf die Schaltfläche „Testen“, um die Pushbenachrichtigung an das Gerät zu senden.
 
![Test2][9]

-    Aktivieren der Kampagne
 
![Test3][10]

-    Nachdem Sie die Kampagne erstellt haben, müssen Sie sie aktivieren, damit die Benachrichtigung per Pushübertragung an Ihre Benutzer gesendet wird.
 
## Senden personalisierter Pushübertragungen
-    In diesem Beispiel wird eine Pushübertragung erstellt, bei der ein allgemeiner Rabattcode in die Pushbenachrichtigung eingegeben wird.
 
![Personalisieren1][11]

Die Personalisierung funktioniert durch Ersetzen einer Markierung durch ein App-Informationskennzeichen, daher müssen Sie zunächst sicherstellen, dass für den Benutzer die richtige App-Information definiert ist. In diesem Beispiel verfügt der Zielbenutzer über ein App-Informationskennzeichen namens „rebate-code defined“. Wie Sie oben sehen können, enthält der Inhalt der Pushbenachrichtigung die Markierung „${rebate_code}“, die darauf hinweist, dass sie durch den eigentlichen Inhalt des App-Informationskennzeichens ersetzt wird.

> Warnung: Wenn das App-Informationskennzeichen für den Benutzer nicht definiert ist, erhält der Benutzer keine Pushübertragung.

-    Ergebnis
 
![Personalisieren2][12]

### Sie können den Text der Benachrichtigung weiter anpassen.
![Personalisieren3][13]

-    Das bezieht den Titel der Benachrichtigung
-    und den Inhalt der Nachricht mit ein.
-    Wählen Sie den Typ der Ankündigung aus (Textansicht oder Webansicht).
 
![Personalisieren4][14]

### Der Hauptteil der Ankündigung kann auch wie folgt angepasst werden:
-    Mithilfe der Aktions-URL, sofern Sie die Startseite anpassen möchten
-    Mithilfe des Titels
-    Mithilfe des Hauptteils der Nachricht
 
 
## Differenzieren Ihrer Pushbenachrichtigung (innerhalb oder außerhalb der App)
-    Wählen Sie den Typ der Pushbenachrichtigung und dann die Anwendung aus, wechseln Sie zum Bereich „Reach“, wählen oder erstellen Sie anschließend eine Pushkampagne, und wechseln Sie dann zum Bereich „Benachrichtigung“.
 
-    Klicken Sie dann auf den gewünschten „Übermittlungsmodus“.
-    Aktivieren Sie das Kontrollkästchen „Aktivitäten einschränken“, wenn die Benachrichtigung für bestimmte Aktivitäten (Bildschirme) angezeigt werden soll.

![Differenzieren1][15]

### Übermittlungsmodus für „Nur außerhalb App“
![Differenzieren2][16]

Der Übermittlungsmodus für „Nur außerhalb App“ stellt Pushbenachrichtigungen bereit, wenn die Anwendung geschlossen ist. Dies ist die Standard-Pushbenachrichtigung. Wenn Sie die Option für „Nur außerhalb App“ auswählen, müssen Sie bereits die Zertifikate von der Plattform bereitgestellt haben, auf der Ihre Anwendung aufsetzt (APNS oder GCM).

### Weitere Informationen
-  [Apple-Pushbenachrichtigungsdienst – Zertifikate](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging – Zertifikate](http://developer.android.com/google/gcm/index.html) 

### Übermittlungsmodus für „Nur in App“
![Differenzieren3][17]

Der Übermittlungsmodus für „Nur in App“ stellt Pushbenachrichtigungen bereit, wenn die Anwendung aktiv ist. Für diese Benachrichtigung müssen Sie das APNS- und GCM-System nicht durchlaufen. Sie können das In-App-Übermittlungssystem verwenden, um die Benutzer zu erreichen. Sie können die Benachrichtigung vollständig anpassen und entscheiden, in welcher Aktivität (Bildschirm) die Benachrichtigung angezeigt wird.

### Übermittlungsmodus für „Immer“
Sie können den Übermittlungsmodus für „Immer“ auswählen, um sicherzustellen, dass Sie die Benutzer jederzeit erreichen, unabhängig davon, ob die Anwendung aktiv oder geschlossen ist. Wenn Sie die Option für „Immer“ auswählen, müssen Sie bereits die Zertifikate von der Plattform bereitgestellt haben, auf der Ihre Anwendung aufsetzt (APNS oder GCM).
 
## Planen einer Pushkampagne
### Planen des Kampagnenstarts
![Planen1][18]

Angenommen, es ist der 21. März und Sie haben eine Ankündigung zu machen, die für den 22. März um Mitternacht geplant ist. Sie müssen für eine Pushbenachrichtigung nicht direkt vor dem Gerät sitzen! Sie können die genaue Uhrzeit vorplanen, zu der die Benachrichtigungen gesendet werden. 
-    Deaktivieren Sie das Kontrollkästchen „Keine“. 
-    Wählen Sie Datum und Uhrzeit für den Start der Push-Kampagne aus.

### Planen des Kampagnenendes
![Planen2][19]

Sie möchten, dass die Kampagne am 25. März um 15 Uhr beendet wird, aber Sie wissen schon, dass Sie zu diesem Zeitpunkt nicht vor Ort sind, um diesen Vorgang auszuführen. Sie müssen für eine Pushbenachrichtigung nicht direkt vor dem Gerät sitzen! Sie können die genaue Uhrzeit vorplanen, zu der die Kampagne beendet wird. 
-    Deaktivieren Sie das Kontrollkästchen „Keine“. 
-    Wählen Sie Datum und Uhrzeit für die Beendung der Push-Kampagne aus.

### Manuelles Beenden einer Kampagne
![Planen3][20]

Die Kontrollkästchen „Keine“ sind standardmäßig aktiviert. Das bedeutet, dass die Kampagne gestartet wird, sobald Sie sie im Bereich „Reach“ aktivieren. Sie wird dann beendet, wenn Sie sie im Bereich „Reach“ beenden.
 
> Hinweis: Für Kampagnen ohne Enddatum wird die Pushbenachrichtigung lokal auf dem Gerät gespeichert und beim nächsten Öffnen der App angezeigt – selbst dann, wenn die Kampagne manuell beendet wurde.

## Erweitern einer Pushbenachrichtigung mit einer Textansicht
### Was ist eine Textansicht?
![Textansicht1][21]

Eine Textansicht ist ein Popupfenster mit Textinhalt. Dieses Popupfenster wird angezeigt, nachdem der Benutzer auf die Pushbenachrichtigung geklickt hat. Eine Textansicht ermöglicht es Ihnen, dem Benutzer weitere Inhalte anzuzeigen. Das bietet Ihnen auch die Möglichkeit, einen Aktionsaufruf darzustellen, z. B. den Wechsel zu einer Seite der App, die Umleitung zu einem Store, das Öffnen einer Webseite, das Senden einer E-Mail, das Starten einer geografischen Suche usw.

### Beispiel: Textansicht
-    Erstellen Sie Ihre Pushbenachrichtigungskampagne im Bereich „Reach“, und weisen Sie der Kampagne einen Namen zu.
 
![Textansicht2][22]

-    Geben Sie die Nachricht ein, die in der Benachrichtigung angezeigt wird.
-    Wählen Sie als Inhaltstyp für die Ankündigung „Text“ aus.
 
![Textansicht3][23]

> Hinweis: Wenn Sie eine Textansicht per Push übertragen, erfolgt immer zuerst eine Benachrichtigung.

- Legen Sie den Text fest (nachdem Sie den Inhalt der Textankündigung ausgewählt haben, wird der Unterbereich angezeigt, in dem Sie den anzuzeigenden Text festlegen können).
 
![Textansicht4][24]

-    Geben Sie den Titel ein, der am oberen Rand der Nachricht angezeigt wird.
-    Geben Sie den Hauptinhalt der Textansicht ein.
-    Geben Sie den Inhalt ein, der auf der Aktionsschaltfläche angezeigt wird (mithilfe einer Aktionsschaltfläche kann die Anwendung eine bestimmte Aktion ausführen, z. B. eine Seite der Anwendung öffnen, zu einem App-Store oder einer beliebigen anderen Ressource weiterleiten, die Sie bereitstellen können).
-    Geben Sie den Inhalt ein, der auf der Schaltfläche zum Beenden angezeigt wird (wenn Sie auf die Schaltfläche zum Beenden klicken, wird die Textansicht geschlossen).
 
-    Erstellen Sie Ihre Pushbenachrichtigungskampagne, damit sie in der Kampagnenliste angezeigt wird.
 
![Textansicht5][25]

-    Aktivieren Sie Ihre Pushbenachrichtigungskampagne, um die Textansicht an Ihre Benutzer zu senden.
 
![Textansicht6][26]

-    Ergebnis
 
![Textansicht7][27]

-    Der Benutzer erhält die Benachrichtigung, auf die er anschließend klickt.
-    Die Textansicht wird als Popupfenster angezeigt und bietet dem Benutzer Möglichkeiten zur Interaktion.

## Erweitern einer Pushbenachrichtigung mit einer Webansicht
### Was ist eine Webansicht?
![Webansicht1][28]

Eine Webansicht ist ein Popupfenster mit Webinhalt. Dieses Popupfenster wird angezeigt, nachdem der Benutzer auf die Pushbenachrichtigung geklickt hat. Eine Webansicht ermöglicht es Ihnen, dem Benutzer weitere Interaktionen zu ermöglichen. Das bietet Ihnen auch die Möglichkeit, einen Aktionsaufruf darzustellen, z. B. die Umleitung zu einem App-Store, das Öffnen einer Webseite, das Senden einer E-Mail, das Starten einer geografischen Suche usw.

### Beispiel: Webansicht
-    Erstellen Sie Ihre Pushkampagne im Bereich „Reach“, und weisen Sie der Kampagne einen Namen zu.
 
![Webansicht2][29]

-    Geben Sie die Nachricht ein, die in der Benachrichtigung angezeigt wird.
-    Wählen Sie als Inhaltstyp für die Ankündigung „Web“ aus.
 
![Webansicht3][30]

### Informationen zu Ankündigungstypen:
- Nur Benachrichtigung: Dies ist eine einfache Standardbenachrichtigung. Das bedeutet, dass, wenn ein Benutzer darauf klickt, keine zusätzliche Ansicht angezeigt wird, sondern nur die zugeordnete Aktion erfolgt.
- Textankündigung: Dies ist eine Benachrichtigung, die den Benutzer auffordert, einen Blick auf eine Textansicht zu werfen.
- Web-Ankündigung: Dies ist eine Benachrichtigung, die den Benutzer auffordert, einen Blick auf eine Webansicht zu werfen. Wählen Sie den Inhalt „Webankündigung“ aus.

> Hinweis: Wenn Sie eine Webansicht per Pushübertragung senden, erfolgt zunächst immer eine Benachrichtigung.

- Legen Sie den Webinhalt fest (nachdem Sie den Inhalt der Webankündigung ausgewählt haben, wird der Unterbereich angezeigt, in dem Sie den anzuzeigenden Webinhalt festlegen können).

 
![Webansicht4][31]

-    Geben Sie den Titel ein, der am oberen Rand der Nachricht angezeigt wird (optional).
-    Geben Sie hier Ihren HTML-Code ein.
-    Klicken Sie auf die Schaltfläche für die Bearbeitung im Quellmodus, um die Edition zu wechseln und das Layout zu betrachten.
-    Geben Sie den Inhalt ein, der auf der Aktionsschaltfläche angezeigt wird (mithilfe einer Aktionsschaltfläche kann die Anwendung eine bestimmte Aktion ausführen, z. B. eine Seite der Anwendung öffnen, zu einem Store oder einer beliebigen anderen Ressource weiterleiten, die Sie bereitstellen können).
-    Geben Sie den Inhalt ein, der auf der Schaltfläche zum Beenden angezeigt wird (wenn Sie auf die Schaltfläche zum Beenden klicken, wird die Webansicht geschlossen).
 
-    Ergebnis
 
![Webansicht5][32]

-    Der Benutzer erhält die Benachrichtigung, auf die er anschließend klickt.
-    Die Textansicht wird als Popupfenster angezeigt und bietet dem Benutzer Möglichkeiten zur Interaktion.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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
[Link 27]: mobile-engagement-how-tos-first-push.md
[Link 28]: mobile-engagement-how-tos-test-campaign.md
[Link 29]: mobile-engagement-how-tos-personalize-push.md
[Link 30]: mobile-engagement-how-tos-differentiate-push.md
[Link 31]: mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: mobile-engagement-how-tos-text-view.md
[Link 33]: mobile-engagement-how-tos-web-view.md

<!--HONumber=54-->