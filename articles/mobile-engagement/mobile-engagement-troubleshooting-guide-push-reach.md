<properties 
   pageTitle="Azure Mobile Engagement – Handbuch zur Problembehandlung – Push/Reach" 
   description="Behandlung von Problemen bei Benutzerinteraktionen und Benachrichtigungen in Azure Mobile Engagement" 
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
   ms.date="06/18/2015"
   ms.author="piyushjo"/>


# Handbuch zur Problembehandlung bei Push und Reach

Im Folgenden finden Sie mögliche Probleme, die beim Senden von Informationen durch Azure Mobile Engagement an die Benutzer auftreten können.
 
## Pushfehler

### Problem
- Pushvorgänge funktionieren nicht (App-intern, App-extern oder beides).

### Ursachen
- Häufig ist ein Pushfehler ein Hinweis dafür, dass Azure Mobile Engagement, Reach oder eine andere erweiterte Funktion von Azure Mobile Engagement nicht ordnungsgemäß integriert wurde, oder dass ein Upgrade im SDK erforderlich ist, um einen bekannten Fehler mit einem neuen Betriebssystem oder einer Geräteplattform zu beheben.
- Testen Sie nur einen App-internen Push und einen App-externen Push um zu ermitteln, ob es sich um einen Fehler innerhalb oder außerhalb der App handelt.
- Führen Sie den Test im Rahmen der Problembehandlung sowohl über die Benutzeroberfläche als auch über die API durch, und prüfen Sie, welche zusätzlichen Fehlerinformationen jeweils verfügbar sind.
- App-externe Pushvorgänge funktionieren nur dann, wenn sowohl Azure Mobile Engagement als auch Reach in das SDK integriert wurden.
- Pushvorgänge werden nicht ausgeführt, wenn Zertifikate ungültig sind oder PROD bzw. DEV ordnungsgemäß (nur iOS). (**Hinweis:** „App-externe“ Pushbenachrichtigungen werden möglicherweise nicht an iOS übermittelt, wenn auf demselben Gerät die Entwicklungsversion (DEV) und Produktionsversion (PROD) der Anwendung installiert ist, da das Sicherheitstoken für das Zertifikat möglicherweise von Apple für ungültig erklärt wurde. Um dieses Problem zu beheben, deinstallieren Sie die DEV- und PROD-Version der Anwendung, und installieren Sie dann nur eine Version auf dem Gerät.)
- App-externe Pushvorgänge werden auf den verschiedenen Plattformen unterschiedlich verarbeitet (iOS zeigt weniger Informationen an als Android, wenn systemeigene Pushvorgänge auf einem Gerät deaktiviert werden, die API stellt mehr Informationen zu Pushstatistiken bereit als die Benutzeroberfläche).
- App-externe Pushvorgänge können von Kunden auf Betriebssystemebene blockiert werden (iOS und Android).
- App-externe Pushvorgänge werden in der Azure Mobile Engagement-Benutzeroberfläche als deaktiviert angezeigt, wenn sie nicht ordnungsgemäß integriert wurden. Bei der API hingegen treten nur "stille" Fehler (ohne Fehlermeldung) auf.
- App-interne Pushvorgänge funktionieren nur, wenn sowohl Azure Mobile Engagement als auch Reach in das SDK integriert wurden.
- GCM- und ADM-Pushvorgänge funktionieren nur, wenn Azure Mobile Engagement und der spezifische Server in das SDK integriert wurden (nur Android).
- App-interne und -externe Pushvorgänge sollten separat getestet werden, um festzustellen, ob es sich um ein Push- oder Reach-Problem handelt.
- App-interne Pushvorgänge erfordern, dass die App zum Empfang geöffnet ist.
- App-externe Pushvorgänge sind häufig über ein opt-in- oder opt-out-Infotag der App für die Filterung eingerichtet.
- Wenn Sie in Reach eine benutzerdefinierte Kategorie zur Anzeige interner Benachrichtigungen der App verwenden, müssen Sie dem richtigen Lebenszyklus der Benachrichtigung folgen. Ansonsten wird die Benachrichtigung möglicherweise nicht gelöscht, wenn der Benutzer sie schließt.
- Wenn Sie eine Kampagne ohne Enddatum starten und ein Gerät die App-interne Benachrichtigung erhält, diese aber noch nicht angezeigt wird, erhält der Benutzer die Benachrichtigung bei der nächsten Anmeldung an der App – selbst dann, wenn Sie die Kampagne manuell beenden.
- Bei Problemen mit der Push-API sollten Sie bestätigen, dass Sie tatsächlich die Push-API und nicht die Reach-API benötigen (die Reach-API wird häufiger verwendet), und dass keine Verwechslung der Parameter "payload" und "notifier" vorliegt.
- Testen Sie Ihre Pushkampagne sowohl mit einem Gerät, das über WIFI verbunden ist, als auch mit einem Gerät, das über 3G verbunden ist, um die Netzwerkverbindung als mögliche Problemquelle auszuschließen.

## Testen von Pushvorgängen

### Problem
- Pushvorgänge können basierend auf einer Geräte-ID an ein bestimmtes Gerät gesendet werden.

### Ursachen

- Testgeräte sind für jede Plattform anders eingerichtet. Wenn Sie jedoch auf einem Testgerät ein Ereignis in Ihrer App generieren, sollten Sie auf jeder Plattform in der Lage sein, im Portal Ihre Geräte-ID zu ermitteln.
- Testgeräte mit IDFA bzw. IDFV arbeiten unterschiedlich (nur iOS).


## Anpassen von Pushvorgängen

### Problem
- Erweiterte Pushinhaltselemente funktionieren nicht (Badge, Klingeln, Vibrieren, Bild usw.).
- Links in Pushes funktionieren nicht (App-extern, App-intern, Verweis auf Website, Speicherort in der App).
- Pushstatistiken zeigen, dass ein Push nicht an so viele Empfänger wie erwartet gesendet wurde (zu viele oder zu wenige).
- Push wird dupliziert und zweimal empfangen.
- Testgeräte für Azure Mobile Engagement-Pushvorgänge kann nicht registriert werden (mit eigener App für Prod oder DEV).

### Ursachen

- Für eine Verlinkung mit einem spezifischen App-internen Speicherort sind "categories" erforderlich (nur Android).
- Deep-Linking-Schemas zum Umleiten von Benutzern zu einem alternativen Speicherort nach dem Klicken auf eine Pushbenachrichtigung müssen von der Anwendung und dem Betriebssystem des Geräts statt direkt von Mobile Engagement verwaltet werden. (**Hinweis:** Unter iOS können App-externe Benachrichtigungen nicht wie unter Android direkt mit App-internen Speicherorten verlinkt werden.)
- Externe Imageserver müssen in der Lage sein, HTTP "GET" und "HEAD" zu verwenden, damit Pushvorgänge für große Bilder funktionieren (nur Android).
- Sie können in Ihrem Code den Azure Mobile Engagement-Agent deaktivieren, wenn die Tastatur geöffnet ist, und den Azure Mobile Engagement-Agent erneut aktivieren, wenn die Tastatur geschlossen wurde. So verhindern Sie, dass die Tastatur das Aussehen Ihrer Benachrichtigung beeinflusst (nur iOS).
- Einige Elemente funktionieren in Testsimulationen nicht, in realen Kampagnen schon (Badge, Klingeln, Vibrieren, Bild usw.).
- Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.
- Um das Problem zu isolieren, führen Sie eine Testkampagne, eine simulierte Kampagne und eine reale Kampagne aus, da jede dieser Kampagnentypen leicht anders funktioniert.
- Die geplante Zeitdauer von App-internen und zeitunabhängigen Kampagnen kann die Anzahl der Übermittlungen beeinflussen, da eine Kampagne nur an Benutzer übermittelt wird, die während der Durchführung der Kampagne die App geöffnet haben (und an Benutzer, die in den Geräteeinstellungen das App-externe Empfangen von Benachrichtigungen festgelegt haben).
- Aufgrund der unterschiedlichen Behandlung von App-externen Benachrichtigungen unter Android und iOS lassen sich die Pushstatistiken für die Android- und iOS-Version Ihrer Anwendung schwierig vergleichen. Android bietet mehr Benachrichtigungsinformationen auf Betriebssystemebene als iOS. Android meldet, wenn eine systemeigene Benachrichtigung empfangen wird, auf sie geklickt wird oder wenn sie in der Mitteilungszentrale gelöscht wird, iOS meldet dies jedoch nur, wenn auf die Benachrichtigung geklickt wird. 
- Der Hauptgrund für die Differenz zwischen der Anzahl der Pushvorgänge und der Anzahl der Übermittlungsvorgänge für Reach-Kampagnen ist die unterschiedliche Zählung von App-internen und App-externen Benachrichtigungen. App-interne Benachrichtigungen werden von Mobile Engagement gehandhabt, während App-externe Benachrichtigungen von der Mitteilungszentrale des Betriebssystems auf dem Gerät gehandhabt werden.

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


## Planen von Pushvorgängen

### Problem
- Die Planung von Pushvorgängen funktioniert nicht wie erwartet (Pushes werden zu früh oder verzögert gesendet).

### Ursachen

- Zeitzonen können Probleme bei der Planung verursachen, insbesondere bei Verwendung der Zeitzone für die Endbenutzer.
- Erweiterte Pushfunktionen können Pushvorgänge verzögern.
- Eine Zielgruppenadressierung basierend auf Telefoneinstellungen (anstelle von Infotags der App) kann Pushvorgänge verzögern, da Azure Mobile Engagement möglicherweise in Echtzeit Daten vom Telefon anfordern muss, bevor ein Push gesendet wird.
- Für Kampagnen ohne Enddatum wird die Pushbenachrichtigung lokal auf dem Gerät gespeichert und beim nächsten Öffnen der App angezeigt – selbst dann, wenn die Kampagne manuell beendet wurde.
- Das gleichzeitige Starten mehrerer Kampagnen kann dazu führen, dass der Scanvorgang der Benutzerbasis länger dauert (versuchen Sie, maximal vier Kampagnen jeweils zeitversetzt zu starten, und beziehen Sie nur aktive Benutzer ein, sodass ältere Benutzer nicht gescannt werden müssen).
- Wenn Sie im Abschnitt "Kampagne" einer Reach-Kampagne die Option "Zielgruppe ignorieren, Push wird über die API an Benutzer gesendet" verwenden, wird die Kampagne NICHT automatisch gesendet. Sie müssen die Kampagne manuell über die Reach-API senden.
- Wenn Sie in Reach eine benutzerdefinierte Kategorie zur Anzeige interner Benachrichtigungen der App verwenden, müssen Sie dem richtigen Lebenszyklus einer Benachrichtigung folgen. Ansonsten wird die Benachrichtigung möglicherweise nicht gelöscht, wenn der Benutzer sie schließt.

 

<!---HONumber=August15_HO6-->