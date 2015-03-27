<properties 
   pageTitle="Azure Mobile Engagement - Anleitungen zur Vorgehensweise" 
   description="Anleitungen zur Vorgehensweise für Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Anleitungen zur Vorgehensweise

## Einführung

Für die folgenden Anleitungen zur Vorgehensweise für die Azure Mobile Engagement-Benutzeroberfläche wird angenommen, dass Sie mit den grundlegenden [Konzepten][Link 6] von Azure Mobile Engagement vertraut sind. Sie setzen zudem die Integration des Azure Mobile Engagement [SDKs][Link 5] in Ihre Anwendung voraus. Diese exemplarische Vorgehensweisen konzentrieren sich eher auf die Verwendung der [Benutzeroberfläche][Link 1] durch Standardbenutzer, anstatt auf die Verwendung der HTTP-REST-basierten [APIs][Link 4] durch Entwickler. Wenn Sie Probleme bei einer dieser [exemplarischen Vorgehensweisen][Link 3] haben, finden Sie weitere Informationen in der [Problembehandlung][Link 2] für Azure Mobile Engagement.

## <a name="#First"></a>Ausführen der ersten Pushbenachrichtigungskampagne
-    Bestätigen Sie, dass Reach mit dem SDK in Ihrer App integriert ist. 
-    Auswählen Ihrer Anwendung
 
![First1][1]

-    Wechseln Sie zum Abschnitt "Reach", und klicken Sie dann auf "Neue Ankündigung".
 
![First2][2]

-    Erstellen Sie eine neue Kampagne und benennen Sie sie.
 
 ![First3][3]

-    Wähle Sie die Art der Übermittlung für die Benachrichtigung aus: "Nur In-App".
 
![First4][4]

-    Erstellen Sie die per Push zu übertragende Nachricht.
 
![First5][5]

-    Sie können einen Titel für die Benachrichtigung eingeben (optional).
-    Geben Sie den Inhalt der Nachricht ein.
-    Sie können ein Bild hochladen. Beachten Sie, dass die Dateigröße 32.768 Bytes nicht überschreiten darf.
-    Zudem haben Sie die Möglichkeit, weitere Optionen auszuwählen, aber im Rahmen dieses Lernprogramms, werden diese später gezeigt.

-    Wählen Sie als Inhaltstyp "Nur Benachrichtigung" aus.
 
![First6][6]

-    Erstellen Sie Ihre Pushbenachrichtigung, damit diese in Ihrer Kampagnenliste angezeigt wird.
 
![First7][7]

## <a name="#Test"></a>Testen der Pushbenachrichtigungskampagne
 
![Test1][8]

-    Registrieren Sie Ihr Gerät.
-    Aktivieren Sie das Kontrollkästchen für das Gerät, an das die Pushübertragung gesendet werden soll.
-    Klicken Sie auf die Schaltfläche "Testen", um die Pushbenachrichtigung an das Gerät zu senden.
 
![Test2][9]

-    Aktivieren der Kampagne
 
![Test3][10]

-    Nachdem Sie die Kampagne erstellt haben, müssen Sie sie aktivieren, damit die Benachrichtigung per Pushübertragung an Ihre Benutzer gesendet wird.
 
## <a name="#Personalize"></a>Senden personalisierter Pushübertragungen
-    In diesem Beispiel wird eine Pushübertragung erstellt, bei der ein allgemeiner Rabattcode in die Pushbenachrichtigung eingegeben wird.
 
![Personalize1][11]

Die Personalisierung funktioniert durch Ersetzen einer Markierung durch ein App-Informationskennzeichen, daher müssen Sie zunächst sicherstellen, dass für den Benutzer die richtige App-Information definiert ist. In diesem Beispiel verfügt der Zielbenutzer über ein App-Informationskennzeichen namens "rebate-code defined".
Wie Sie oben sehen können, enthält der Inhalt der Pushbenachrichtigung die Markierung "${rebate_code}", die darauf hinweist, dass sie durch den eigentlichen Inhalt des App-Informationskennzeichens ersetzt wird.

> Warnung: Wenn das App-Informationskennzeichen für den Benutzer nicht definiert ist, erhält der Benutzer keine Pushübertragung.

-    Ergebnis
 
![Personalize2][12]

### Sie können den Text der Benachrichtigung weiter anpassen.
 
![Personalize3][13]

-    Das bezieht den Titel der Benachrichtigung
-    und den Inhalt der Nachricht mit ein.
-    Wählen Sie den Typ der Ankündigung aus (Textansicht oder Webansicht).
 
![Personalize4][14]

### Der Hauptteil der Ankündigung kann auch wie folgt angepasst werden:
-    Mithilfe der Aktions-URL, sofern Sie die Startseite anpassen möchten
-    Mithilfe des Titels
-    Mithilfe des Hauptteils der Nachricht
 
 
## <a name="#Differentiate"></a>Differenzieren Ihrer Pushbenachrichtigung (innerhalb oder außerhalb der App)

-    Wählen Sie den Typ der Pushbenachrichtigung und dann die Anwendung aus, wechseln Sie zum Bereich "Reach", wählen oder erstellen Sie anschließend eine Pushkampagne, und wechseln Sie dann zum Bereich "Benachrichtigung".
 
-    Klicken Sie dann auf den gewünschten "Übermittlungsmodus".
-    Aktivieren Sie das Kontrollkästchen "Aktivitäten einschränken", wenn die Benachrichtigung für bestimmte Aktivitäten (Bildschirme) angezeigt werden soll.

![Differentiate1][15]

### Übermittlungsmodus für "Nur außerhalb App"
 
![Differentiate2][16]

Der Übermittlungsmodus für "Nur außerhalb App" stellt Pushbenachrichtigungen bereit, wenn die Anwendung geschlossen ist. Dies ist die Standard-Pushbenachrichtigung.
Wenn Sie die Option für "Nur außerhalb App" auswählen, müssen Sie bereits die Zertifikate von der Plattform bereitgestellt haben, auf der Ihre Anwendung aufsetzt (APNS oder GCM).

**Weitere Informationen:** 

-  [Apple-Pushbenachrichtigungsdienst - Zertifikate](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud Messaging - Zertifikate](http://developer.android.com/google/gcm/index.html) 

### Übermittlungsmodus für "Nur in App"
 
![Differentiate3][17]

Der Übermittlungsmodus für "Nur in App" stellt Pushbenachrichtigungen bereit, wenn die Anwendung aktiv ist.
Für diese Benachrichtigung müssen Sie das APNS- und GCM-System nicht durchlaufen.
Sie können das In-App-Übermittlungssystem verwenden, um die Benutzer zu erreichen.
Sie können die Benachrichtigung vollständig anpassen und entscheiden, in welcher Aktivität (Bildschirm) die Benachrichtigung angezeigt wird.

### Übermittlungsmodus für "Immer"
Sie können den Übermittlungsmodus für "Immer" auswählen, um sicherzustellen, dass Sie die Benutzer jederzeit erreichen, unabhängig davon, ob die Anwendung aktiv oder geschlossen ist.
Wenn Sie die Option für "Immer" auswählen, müssen Sie bereits die Zertifikate von der Plattform bereitgestellt haben, auf der Ihre Anwendung aufsetzt (APNS oder GCM). 
 
## <a name="#Schedule"></a>Planen einer Pushkampagne
 
### Planen des Kampagnenstarts
 
![Shedule1][18]

Angenommen, es ist der 21. März und Sie haben eine Ankündigung zu machen, die für den 22. März um Mitternacht geplant ist. 
Sie müssen für eine Pushbenachrichtigung nicht direkt vor dem Gerät sitzen! Sie können den Sendezeitpunkt für eine Benachrichtigung auf die Minute genau planen.
-    Deaktivieren Sie das Kontrollkästchen "Keine", und wählen Sie eine Startzeit aus. 
-    Wählen Sie das gewünschte Datum und die Uhrzeit zum Starten der Pushkampagne aus.
### Planen des Kampagnenendes
 
![Shedule2][19]

Sie möchten, dass die Kampagne am 25. März um 15 Uhr beendet wird, aber Sie wissen schon, dass Sie zu diesem Zeitpunkt nicht vor Ort sind, um diesen Vorgang auszuführen.
Sie müssen für eine Pushbenachrichtigung nicht direkt vor dem Gerät sitzen! Sie können den Zeitpunkt zum Beenden einer Kampagne auf die Minute genau planen.
-    Aktivieren Sie das Kontrollkästchen "Keine", oder wählen Sie eine Endzeit aus.
-    Wählen Sie das gewünschte Datum und die Uhrzeit zum Beenden der Pushkampagne aus.
### Manuelles Beenden einer Kampagne
 
![Shedule3][20]

Die Kontrollkästchen "Keine" sind standardmäßig aktiviert.
Das bedeutet, dass die Kampagne gestartet wird, sobald Sie sie im Bereich "Reach" aktivieren. Sie wird dann beendet, wenn Sie sie im Bereich "Reach" beenden.
 
> Hinweis: Bei Kampagnen, die ohne Enddatum erstellt werden, wird die Pushbenachrichtigung lokal auf dem Gerät gespeichert und beim nächsten Öffnen der App angezeigt, auch wenn die Kampagne manuell beendet wird.

## <a name="#TextView"></a>Erweitern einer Pushbenachrichtigung mit einer Textansicht

### Was ist eine Textansicht?
 
![TextView1][21]

Eine Textansicht ist ein Popupfenster mit Textinhalt. Dieses Popupfenster wird angezeigt, nachdem der Benutzer auf die Pushbenachrichtigung geklickt hat.
Eine Textansicht ermöglicht es Ihnen, dem Benutzer weitere Inhalte anzuzeigen. Das bietet Ihnen auch die Möglichkeit, einen Aktionsaufruf darzustellen, z. B. den Wechsel zu einer Seite der App, die Umleitung zu einem Store, das Öffnen einer Webseite, das Senden einer E-Mail, das Starten einer geografischen Suche usw.

### Beispiel: Textansicht
-    Erstellen Sie Ihre Pushbenachrichtigungskampagne im Bereich "Reach", und weisen Sie der Kampagne einen Namen zu.
 
![TextView2][22]

-    Geben Sie die Nachricht ein, die in der Benachrichtigung angezeigt wird.
-    Wählen Sie als Inhaltstyp für die Ankündigung "Text" aus.
 
![TextView3][23]

> Hinweis: Wenn Sie eine Textansicht per Push übertragen, erfolgt immer zuerst eine Benachrichtigung. 

- Legen Sie den Text fest (nachdem Sie den Inhalt der Textankündigung ausgewählt haben, wird der Unterbereich angezeigt, in dem Sie den anzuzeigenden Text festlegen können).
 
![TextView4][24]

-    Geben Sie den Titel ein, der am oberen Rand der Nachricht angezeigt wird.
-    Geben Sie den Hauptinhalt der Textansicht ein.
-    Geben Sie den Inhalt ein, der auf der Aktionsschaltfläche angezeigt wird (mithilfe einer Aktionsschaltfläche kann die Anwendung eine bestimmte Aktion ausführen, z. B. eine Seite der Anwendung öffnen, zu einem App-Store oder einer beliebigen anderen Ressource weiterleiten, die Sie bereitstellen können).
-    Geben Sie den Inhalt ein, der auf der Schaltfläche zum Beenden angezeigt wird (wenn Sie auf die Schaltfläche zum Beenden klicken, wird die Textansicht geschlossen).
 
-    Erstellen Sie Ihre Pushbenachrichtigungskampagne, damit sie in der Kampagnenliste angezeigt wird.
 
![TextView5][25]

-    Aktivieren Sie Ihre Pushbenachrichtigungskampagne, um die Textansicht an Ihre Benutzer zu senden.
 
![TextView6][26]

-    Ergebnis
 
![TextView7][27]

-    Der Benutzer erhält die Benachrichtigung, auf die er anschließend klickt.
-    Die Textansicht wird als Popupfenster angezeigt und bietet dem Benutzer Möglichkeiten zur Interaktion.

## <a name="#WebView"></a>Erweitern einer Pushbenachrichtigung mit einer Webansicht

### Was ist eine Webansicht?
 
![WebView1][28]

Eine Webansicht ist ein Popupfenster mit Webinhalt. Dieses Popupfenster wird angezeigt, nachdem der Benutzer auf die Pushbenachrichtigung geklickt hat.
Eine Webansicht ermöglicht es Ihnen, dem Benutzer weitere Interaktionen zu ermöglichen.
Das bietet Ihnen auch die Möglichkeit, einen Aktionsaufruf darzustellen, z. B. die Umleitung zu einem App-Store, das Öffnen einer Webseite, das Senden einer E-Mail, das Starten einer geografischen Suche usw.

### Beispiel: Webansicht

-    Erstellen Sie Ihre Pushkampagne im Bereich "Reach", und weisen Sie der Kampagne einen Namen zu.
 
![WebView2][29]

-    Geben Sie die Nachricht ein, die in der Benachrichtigung angezeigt wird.
-    Wählen Sie als Inhaltstyp für die Ankündigung "Web" aus.
 
![WebView3][30]

**Informationen zu Ankündigungstypen:**

- Nur Benachrichtigung: Hierbei handelt es sich um eine einfache Standardbenachrichtigung. Wenn der Benutzer darauf klickt, wird keine zusätzliche Ansicht geöffnet, sondern nur die ihr zugeordnete Aktion ausgeführt.
- Textankündigung: Hierbei handelt es sich um eine Benachrichtigung, die den Benutzer zum Betrachten einer Textansicht auffordert.
- Webankündigung: Hierbei handelt es sich um eine Benachrichtigung, die den Benutzer zum Betrachten einer Webansicht auffordert.
Wählen Sie den Inhalt "Webankündigung" aus.

> Hinweis: Wenn Sie eine Webansicht per Pushübertragung senden, erfolgt zunächst immer eine Benachrichtigung.

- Legen Sie den Webinhalt fest (nachdem Sie den Inhalt der Webankündigung ausgewählt haben, wird der Unterbereich angezeigt, in dem Sie den anzuzeigenden Webinhalt festlegen können).

 
![WebView4][31]

-    Geben Sie den Titel ein, der am oberen Rand der Nachricht angezeigt wird (optional).
-    Geben Sie hier Ihren HTML-Code ein.
-    Klicken Sie auf die Schaltfläche für die Bearbeitung im Quellmodus, um die Edition zu wechseln und das Layout zu betrachten.
-    Geben Sie den Inhalt ein, der auf der Aktionsschaltfläche angezeigt wird (mithilfe einer Aktionsschaltfläche kann die Anwendung eine bestimmte Aktion ausführen, z. B. eine Seite der Anwendung öffnen, zu einem Store oder einer beliebigen anderen Ressource weiterleiten, die Sie bereitstellen können).
-    Geben Sie den Inhalt ein, der auf der Schaltfläche zum Beenden angezeigt wird (wenn Sie auf die Schaltfläche zum Beenden klicken, wird die Webansicht geschlossen).
 
-    Ergebnis
 
![WebView5][32]

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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
