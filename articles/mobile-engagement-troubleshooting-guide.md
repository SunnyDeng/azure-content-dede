<properties 
   pageTitle="Azure Mobile Engagement - Handbuch zur Problembehandlung" 
   description="Handbuch zur Problembehandlung für Azure Mobile Engagement" 
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

# Azure Mobile Engagement - Anweisungen zur Problembehandlung

## Einführung

Die folgenden Anweisungen zur Problembehandlung decken die meisten gängigen Probleme mit Azure Mobile Engagement ab, ausgenommen sind jedoch Fragen zur [Abrechnung][Link 11] und Unterstützung bei der Anmeldung für die [Vorschau][Link 7]. Wenn Ihr Problem mit den folgenden Anweisungen nicht gelöst wird, können Sie entweder eine Azure-Serviceanfrage einreichen - sofern Sie über eine [Azure-Supportvereinbarung](http://azure.microsoft.com/support/options/) verfügen - oder eine Frage im [MSDN-Forum][Link 8] stellen. Achten Sie beim Einreichen einer Serviceanfrage darauf, alle Problembehandlungsinformationen aus <a href="#SR" title="SR">Informationen in Serviceanfrage</a> einzuschließen, um den technischen Support bei der Untersuchung Ihres Problems zu unterstützen.

## <a name="#ANALYTICS">Analyse, Überwachung, Segmentierung und Dashboards</a>

Es liegen Probleme in Bezug auf die Erfassung von Informationen zu Anwendungen, Geräten und Benutzern durch Azure Mobile Engagement vor.

**Liste der Symptome:**

1. <a href="#ANALYTICS1">Informationen fehlen/werden verzögert angezeigt</a>
2. <a href="#ANALYTICS2">Elemente sind nicht in der Benutzeroberfläche auffindbar</a>
3. <a href="#ANALYTICS3">Problembehandlung von Abstürzen</a>
 
<a name="#ANALYTICS1">**Symptom:**
1.    Informationen fehlen/werden verzögert angezeigt:</a>

- Informationen werden in Analyse, Segmentierung oder Dashboard verzögert angezeigt.
- Informationen fehlen in der Überwachung.
- Informationen fehlen in Analyse, Segmentierung oder Dashboard.
- Segmentierungsgrenzwerte werden erreicht.

**Ursachen/Maßnahmen:**

- Sie können mithilfe der APIs für Analyse, Überwachung und Segmentierung prüfen, ob fehlende Daten in der Benutzeroberfläche über die APIs sichtbar sind.
- Wenn das Azure Mobile Engagement-SDK nicht ordnungsgemäß in Ihre App integriert ist, können in Analyse, Segmentierung, Überwachung oder Dashboards keine Informationen angezeigt werden.
- Segmente können nach der Erstellung nicht geändert, sondern nur "geklont" (kopiert) oder "zerstört" (gelöscht) werden.
Segmente können nur 10 Kriterien umfassen.
- Die beste Möglichkeit, auf fehlende Informationen in der Überwachung zu testen, besteht darin, ein Testgerät einzurichten und die App zu deinstallieren und/oder auf dem Testgerät neu zu installieren.
- Informationen für Analyse, Segmentierung oder Dashboards werden alle 24 Stunden aktualisiert.
- Informationen in neuen Segmenten werden möglicherweise erst 24 Stunden nach ihrer Erstellung angezeigt (selbst dann, wenn das Segment auf vorherigen Informationen basiert.
- Eine Filterung Ihrer Analysedaten in der Benutzeroberfläche zeigt alle Beispiele für diesen Typ, unabhängig von der Version Ihrer App (z. B. wird bei einer Filterung nach "Abstürze" und Name Version 1 und Version 2 Ihrer App angezeigt).
- Der Analysezeitraum basiert auf dem Datum in den Benutzergeräteeinstellungen. Wenn ein Benutzer also das Datum für sein Gerät falsch eingestellt hat, könnte dieses im falschen Zeitraum angezeigt werden.
- Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.

**Siehe auch:** 

- [Handbuch zur Problembehandlung - SDK][Link 2], [API-Dokumentation][Link 4], [Dokumentation zur Benutzeroberfläche - Segmente][Link 1]

<a name="#ANALYTICS2">**Symptom:**
2. Elemente sind nicht in der Benutzeroberfläche auffindbar</a>

- Segmente können nicht basierend auf bestimmten integrierten oder benutzerdefinierten Infotagkriterien der App erstellt werden.
- Bestimmte integrierte oder benutzerdefinierte Infotagkriterien der App sind in Analyse, Überwachung oder Dashboards nicht auffindbar.
- Daten in Analyse, Überwachung, Segmentierung oder Dashboards können nicht interpretiert werden.

**Ursachen/Maßnahmen:**

- Einige integrierte Elemente und Infotags der App sind nur als Pushkriterien verfügbar. Sie können weder einem Segment hinzugefügt werden, noch sind sie in Analyse, Überwachung oder Dashboard sichtbar. 
- Für integrierte Elemente und Infotags der App, die keinem Segment hinzugefügt werden können, müssen Sie in jeder Kampagne eine Liste der Zielkriterien einrichten, um eine wie auf Segmenten basierende Zielgruppenadressierung zu erreichen.
- Weitere Informationen und Anleitungen finden Sie in den Kontextmenüs von Analyse, Überwachung und Dashboards der Azure Mobile Engagement-Benutzeroberfläche.

**Siehe auch:** 

- [Dokumentation zur Benutzeroberfläche - Neues Reach-Pushkriterium für die Zielgruppenadressierung][Link 1]
 
<a name="#ANALYTICS3">**Symptom:**
3. Problembehandlung von Abstürzen</a>

- In Analyse, Überwachung oder Dashboard werden Anwendungsabstürze angezeigt.

**Ursachen/Maßnahmen:**

- Zur Problembehandlung von Anwendungsabstürzen, die in Analyse, Überwachung oder Dashboard angezeigt werden, prüfen Sie die Versionshinweise auf bekannte Probleme mit vorherigen Versionen des SDK.
- Generieren Sie zur erweiterten Problembehandlung von Anwendungsabstürzen ein Ereignis auf einem Testgerät, auf dem die Anwendung installiert ist. Suchen Sie anschließend im Abschnitt "Überwachen - Ereignisse" der Azure Mobile Engagement-Benutzeroberfläche nach Ihrer Geräte-ID. Lösen Sie dann das Ereignis aus, das Ihre Anwendung zum Absturz bringt, und prüfen Sie die zusätzlichen Informationen im Abschnitt "Überwachen - Absturz" der Azure Mobile Engagement-Benutzeroberfläche. 

**Siehe auch:** 

- [Konzepte - häufig gestellte Fragen (FAQ)][Link 6], [Konzepte - Glossar][Link 6], [Dokumentation zur Benutzeroberfläche][Link 1], [SDK-Dokumentation - Versionshinweise][Link 5], [SDK-Dokumentation - Anweisungen zu Upgrades][Link 5], [Vorgehensweisen - Geräte-ID-Registrierung][Link 3]

## <a name="#APIS">APIs</a>

Es liegen Probleme bezüglich der Interaktivität von Administratoren mit Azure Mobile Engagement über die APIs vor.

**Liste der Symptome:**

1. <a href="#APIS1">Syntaxfehler</a>
2. <a href="#APIS2">Keine Möglichkeit zur Verwendung der API zur Durchführung derselben Aktion, die auch in der Azure Mobile Engagement-Benutzeroberfläche verfügbar ist</a>
3. <a href="#APIS3">Fehlermeldungen</a>
4. <a href="#APIS4">Verborgene ("stille") Fehler</a>
 
<a name="#APIS1">**Symptom:**
1. Syntaxfehler:</a>

- Syntaxfelder bei Verwendung der API (oder unerwartetes Verhalten).

**Ursachen/Maßnahmen:**

- Syntaxfehler:
    - Stellen Sie durch Prüfen der Syntax der jeweiligen API sicher, dass die Option zur Verfügung steht.
    - Ein häufiger Fehler bei der API-Verwendung besteht darin, dass Reach-API und Push-API verwechselt werden (die meisten Aufgaben sollten nicht mit der Push-API, sondern mit der Reach-API ausgeführt werden). 
    - Ein weiterer häufiger Fehler bei der SDK-Integration und der API-Verwendung ist der, dass SDK- und API-Schlüssel verwechselt werden.
    - Skripts, die eine Verbindung zu den APIs herstellen, müssen mindestens alle 10 Minuten Daten senden, ansonsten kommt es zu einer Zeitüberschreitung (besonders häufig in API-Überwachungsskripts, die auf Daten prüfen). Um Zeitüberschreitungen zu verhindern, sollte Ihr Skript alle 10 Minuten ein XMPP-Pingsignal senden, um die Verbindung mit dem Server aufrechtzuerhalten.

**Siehe auch:** 
 
- [Konzepte - Glossar][Link 6], [API-Dokumentation][Link 4], [Informationen zum XMPP-Protokoll]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**Symptom:**
2. Keine Möglichkeit zur Verwendung der API zur Durchführung derselben Aktion, die auch in der Azure Mobile Engagement-Benutzeroberfläche verfügbar ist:</a>

- Eine Aktion, die mit der Azure Mobile Engagement-Benutzeroberfläche ausgeführt werden kann, funktioniert nicht in der entsprechenden Azure Mobile Engagement-API.

**Ursachen/Maßnahmen:**

- Wenn Sie dieselbe Aktion über die Azure Mobile Engagement-Benutzeroberfläche ausführen können, wurde diese Funktion von Azure Mobile Engagement mit dem SDK ordnungsgemäß integriert.

**Siehe auch:**
 
- [Dokumentation zur Benutzeroberfläche][Link 1]
 
<a name="#APIS3">**Symptom:**
3. Fehlermeldungen:</a>

- Bei Verwendung der API werden zur Laufzeit oder in Protokollen Fehlercodes angezeigt.

**Ursachen/Maßnahmen:**

- Nachfolgend finden Sie eine Zusammenstellung häufiger API-Statuscodes als Referenz und zur ersten Problembehandlung:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**Siehe auch:**

- [API-Dokumentation - detaillierte Fehlerinformationen zu jeder API][Link 4]
 
<a name="#APIS4">**Symptom:**
4. Verborgene ("stille") Fehler:</a>

- Eine API-Aktion wird nicht ausgeführt, es wird jedoch weder zur Laufzeit noch in den Protokollen eine Fehlermeldung angezeigt.

**Ursachen/Maßnahmen:**

- In der Azure Mobile Engagement-Benutzeroberfläche werden viele Elemente deaktiviert, wenn sie nicht ordnungsgemäß integriert wurden. Bei der API hingegen treten nur "stille" Fehler (ohne Fehlermeldung) auf. Testen Sie deshalb mithilfe der Benutzeroberfläche, ob dieselbe Funktionalität dort korrekt arbeitet.
- Azure Mobile Engagement und viele weitere Funktionen von Azure Mobile Engagement müssen einzeln mit dem SDK in Ihre App integriert werden, bevor sie genutzt werden können.

**Siehe auch:**

- [Handbuch zur Problembehandlung - SDK][Link 2], [SDK-Dokumentation][Link 5]
 
## <a name="#PUSH">Push/Reach</a>

Beim Senden von Informationen von Azure Mobile Engagement an Ihre Benutzer treten Probleme auf.
 
**Liste der Symptome:**

1. <a href="#PUSH1">Pushfehler</a>
2. <a href="#PUSH2">Probleme beim Testen von Pushvorgängen</a>
3. <a href="#PUSH3">Probleme beim Anpassen von Pushvorgängen</a>
4. <a href="#PUSH4">Probleme bei der Zielgruppenadressierung von Pushvorgängen</a>
5. <a href="#PUSH5">Planen von Pushvorgängen</a>
 
<a name="#PUSH1">**Symptom:**
1. Pushfehler:</a>

- Pushvorgänge funktionieren nicht (App-intern, App-extern oder beides).

**Ursachen/Maßnahmen:**

- Pushfehler:
    - Häufig ist ein Pushfehler ein Hinweis dafür, dass Azure Mobile Engagement, Reach oder eine andere erweiterte Funktion von Azure Mobile Engagement nicht ordnungsgemäß integriert wurde, oder dass ein Upgrade im SDK erforderlich ist, um einen bekannten Fehler mit einem neuen Betriebssystem oder einer Geräteplattform zu beheben.
    - Testen Sie nur einen App-internen Push und einen App-externen Push um zu ermitteln, ob es sich um einen Fehler innerhalb oder außerhalb der App handelt.
    - Führen Sie den Test im Rahmen der Problembehandlung sowohl über die Benutzeroberfläche als auch über die API durch, und prüfen Sie, welche zusätzlichen Fehlerinformationen jeweils verfügbar sind.
    - App-externe Pushvorgänge funktionieren nur dann, wenn sowohl Azure Mobile Engagement als auch Reach in das SDK integriert wurden.
    - App-externe Pushvorgänge funktionieren nicht, wenn Zertifikate ungültig sind oder PROD bzw. DEV nicht richtig verwenden (nur iOS).
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

**Siehe auch:**

- [Handbuch zur Problembehandlung - SDK][Link 2], [Handbuch zur Problembehandlung - Push][Link 2], [SDK-Dokumentation - iOS - Vorbereiten Ihrer Anwendung auf Apple-Pushbenachrichtigungen][Link 5]
 
<a name="#PUSH2">**Symptom:**
2. Probleme beim Testen von Pushvorgängen:</a>

- Pushvorgänge können basierend auf einer Geräte-ID an ein bestimmtes Gerät gesendet werden.

**Ursachen/Maßnahmen:**

- Testgeräte sind für jede Plattform anders eingerichtet. Wenn Sie jedoch auf einem Testgerät ein Ereignis in Ihrer App generieren, sollten Sie auf jeder Plattform in der Lage sein, im Portal Ihre Geräte-ID zu ermitteln.
- Testgeräte mit IDFA bzw. IDFV arbeiten unterschiedlich (nur iOS).

**Siehe auch:**

- [Dokumentation zur Benutzeroberfläche - Reach][Link 1]
 
<a name="#PUSH3">**Symptom:**
3. Probleme beim Anpassen von Pushvorgängen:</a>

- Erweiterte Pushinhaltselemente funktionieren nicht (Badge, Klingeln, Vibrieren, Bild usw.).
- Links in Pushes funktionieren nicht (App-extern, App-intern, Verweis auf Website, Speicherort in der App).
- Pushstatistiken zeigen, dass ein Push nicht an so viele Empfänger wie erwartet gesendet wurde (zu viele oder zu wenige).
- Push wird dupliziert und zweimal empfangen.
- Testgeräte für Azure Mobile Engagement-Pushvorgänge kann nicht registriert werden (mit eigener App für Prod oder DEV).

**Ursachen/Maßnahmen:**

- Für eine Verlinkung mit einem spezifischen App-internen Speicherort sind "categories" erforderlich (nur Android).
- Externe Imageserver müssen in der Lage sein, HTTP "GET" und "HEAD" zu verwenden, damit Pushvorgänge für große Bilder funktionieren (nur Android).
- Sie können in Ihrem Code den Azure Mobile Engagement-Agent deaktivieren, wenn die Tastatur geöffnet ist, und den Azure Mobile Engagement-Agent erneut aktivieren, wenn die Tastatur geschlossen wurde. So verhindern Sie, dass die Tastatur das Aussehen Ihrer Benachrichtigung beeinflusst (nur iOS).
- Einige Elemente funktionieren in Testsimulationen nicht, in realen Kampagnen schon (Badge, Klingeln, Vibrieren, Bild usw.).
- Es werden keine serverseitigen Daten protokolliert, wenn Sie die Schaltfläche zum Testen von Pushvorgängen verwenden. Daten werden nur für reale Pushkampagnen protokolliert.
- Um das Problem zu isolieren, führen Sie eine Testkampagne, eine simulierte Kampagne und eine reale Kampagne aus, da jede dieser Kampagnentypen leicht anders funktioniert.

**Siehe auch:**

- [Vorgehensweisen - Push][Link 3], [Handbuch zur Problembehandlung - Push][Link 2], [Informationen zum HTTP-Protokoll]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**Symptom:**
4. Probleme bei der Zielgruppenadressierung von Pushvorgängen:</a>

- Die integrierte Zielgruppenadressierung funktioniert nicht wie erwartet.
- Die Zielgruppenadressierung über Infotags der App funktioniert nicht wie erwartet.
- Die Zielgruppenadressierung über den geografischen Standort funktioniert nicht wie erwartet.
- Sprachoptionen funktionieren nicht wie erwartet.

**Ursachen/Maßnahmen:**

- Zielgruppenadressierung für Pushvorgänge:
    - Stellen Sie sicher, dass Sie Infotags der App über die Azure Mobile Engagement-Benutzeroberfläche oder -API hochgeladen haben.
    - Eine Drosselung der Pushgeschwindigkeit oder des Pushkontingents auf Anwendungsebene oder eine Einschränkung der Zielgruppe auf Kampagnenebene kann dazu führen, dass eine Person ein bestimmtes Pushereignis nicht empfängt, wenn sie andere Ihrer Kriterien für die Zielgruppenadressierung erfüllt. 
    - Das Festlegen einer "Sprache" unterscheidet sich von einer Zielgruppenadressierung basierend auf Land oder Gebietsschema. Diese wiederum unterscheidet sich von einer Zielgruppenadressierung basierend auf Geolocation nach Telefon- oder GPS-Standort.
    - Die Nachricht in der "Standardsprache" wird an beliebige Kunden gesendet, die ihr Gerät nicht auf eine der alternativen Sprachen eingestellt haben, die Sie angeben.

**Siehe auch:**

- [Dokumentation zur Benutzeroberfläche - Reach][Link 1], [Dokumentation zur Benutzeroberfläche - Einstellungen][Link 1], [API-Dokumentation - Reach][Link 4], [API-Dokumentation - Push][Link 4], [API-Dokumentation - Gerät][Link 4]
 
<a name="#PUSH5">**Symptom:**
5. Planen von Pushvorgängen:</a>

- Die Planung von Pushvorgängen funktioniert nicht wie erwartet (Pushes werden zu früh oder verzögert gesendet).

**Ursachen/Maßnahmen:**

- Zeitzonen können Probleme bei der Planung verursachen, insbesondere bei Verwendung der Zeitzone für die Endbenutzer.
- Erweiterte Pushfunktionen können Pushvorgänge verzögern.
- Eine Zielgruppenadressierung basierend auf Telefoneinstellungen (anstelle von Infotags der App) kann Pushvorgänge verzögern, da Azure Mobile Engagement möglicherweise in Echtzeit Daten vom Telefon anfordern muss, bevor ein Push gesendet wird.
- Für Kampagnen ohne Enddatum wird die Pushbenachrichtigung lokal auf dem Gerät gespeichert und beim nächsten Öffnen der App angezeigt - selbst dann, wenn die Kampagne manuell beendet wurde.
- Das gleichzeitige Starten mehrerer Kampagnen kann dazu führen, dass der Scanvorgang der Benutzerbasis länger dauert (versuchen Sie, maximal vier Kampagnen jeweils zeitversetzt zu starten, und beziehen Sie nur aktive Benutzer ein, sodass ältere Benutzer nicht gescannt werden müssen).
- Wenn Sie im Abschnitt "Kampagne" einer Reach-Kampagne die Option "Zielgruppe ignorieren, Push wird über die API an Benutzer gesendet" verwenden, wird die Kampagne NICHT automatisch gesendet. Sie müssen die Kampagne manuell über die Reach-API senden.
- Wenn Sie in Reach eine benutzerdefinierte Kategorie zur Anzeige interner Benachrichtigungen der App verwenden, müssen Sie dem richtigen Lebenszyklus einer Benachrichtigung folgen. Ansonsten wird die Benachrichtigung möglicherweise nicht gelöscht, wenn der Benutzer sie schließt.

**Siehe auch:**

- [Vorgehensweisen - Planung][Link 3], [Dokumentation zur Benutzeroberfläche - Neue Pushkampagne in Reach][Link 1]
 
## <a name="#SERVICE">Dienst</a>

Es treten Probleme mit der Ausführung von Azure Mobile Engagement auf.

**Liste der Symptome:**

1. <a href="#SERVICE1">Dienstausfälle</a>
2. <a href="#SERVICE2">Konnektivitätsprobleme und falsche Informationen</a>
3. <a href="#SERVICE3">Funktionsanfragen</a>
 
<a name="#SERVICE1">**Symptom:**
1. Dienstausfälle:</a>

**Ursachen/Maßnahmen:**

- Probleme, die scheinbar von Azure Mobile Service-Dienstausfällen verursacht werden, können verschiedene Ursachen haben:
    - Isolierte Probleme, die ursprünglich systemisch in Azure Mobile Engagement auftreten
    - Bekannte Probleme, die durch Serverausfälle verursacht werden (nicht immer im Serverstatus angezeigt):
o    Verzögerungen bei der Planung, Fehler bei der Zielgruppenadressierung, Probleme bei der Badgeaktualisierung, keine Erfassung von Statistiken, Pushvorgänge arbeiten nicht mehr, APIs arbeiten nicht mehr, neue Apps oder Benutzer können nicht erstellt werden, DNS-Fehler, Zeitüberschreitungen in Benutzeroberfläche, API oder Apps auf einem Gerät.
1.    Cloudabhängigkeitsfehler
<Azure-Dienststatus><AWS-Status (Amazon Web Services)>
2.    PNS-Abhängigkeitsfehler (Push Notification Services)
<Google - Dienst><Apple - Dienst><Android - Google GCM><Android - Amazon ADM><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    App Store-Ausfälle
<GooglePlay><iTunes><Windows Phone Store><Windows Store>

    - Cloudabhängigkeitsfehler
[Azure-Dienststatus]( http://azure.microsoft.com/status/), [AWS-Status (Amazon Web Services)]( http://status.aws.amazon.com/) 
    - PNS-Abhängigkeitsfehler (Push Notification Services)
[Google - Dienst](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Dienst]( http://www.apple.com/support/systemstatus/), [Android - Google GCM]( http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx), [Windows - WNS](https://developer.windows.com/)
    - App Store-Ausfälle
[GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Um zu testen, ob es sich um ein systemisches Problem handelt, können Sie dieselbe Funktion von anderer Stelle ausführen:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*So testen Sie, ob das Problem nur die Benutzeroberfläche oder die APIs betrifft:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API-Dokumentation][Link 4], [Dokumentation zur Benutzeroberfläche][Link 1]
	
*So testen Sie, ob ein Problem mit dem Mobilfunknetz vorliegt:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*So testen Sie, ob ein Geräteproblem vorliegt:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Dokumentation zur Benutzeroberfläche - Einstellungen][Link 1]

*So testen Sie, ob ein Problem mit der App vorliegt:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

So testen Sie, ob ein Problem mit Betriebssystemupgrades auf Endbenutzergeräten vorliegt, für dessen Lösung ein SDK-Upgrade erforderlich ist:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Handbuch zur Problembehandlung - SDK][Link 2]
 
<a name="#SERVICE2">**Symptom:**
2. Konnektivitätsprobleme und falsche Informationen:</a>

- Es treten bei der Anmeldung an der Azure Mobile Engagement-Benutzeroberfläche Probleme auf.
- Verbindungsfehler mit den Azure Mobile Engagement-APIs.
- Probleme beim Hochladen von Infotags der App über die Geräte-API.
- Probleme beim Herunterladen von Protokollen oder exportierten Daten aus Azure Mobile Engagement.
- Anzeige von falschen Informationen in der Azure Mobile Engagement-Benutzeroberfläche.
- Anzeige von falschen Informationen in Azure Mobile Engagement-Protokollen.

**Ursachen/Maßnahmen:**

- Bei Konnektivitätsproblemen mit Azure Mobile Engagement:
    - Vergewissern Sie sich, dass Ihr Benutzerkonto über ausreichende Berechtigungen zum Ausführen der Aufgabe verfügt.
    - Stellen Sie sicher, dass es sich nicht um ein isoliertes Problem mit einem Computer oder Ihrem lokalen Netzwerk handelt.
    - Vergewissern Sie sich, dass der Azure Mobile Engagement-Dienst keine Ausfälle meldet.
    - Stellen Sie sicher, dass Ihre App-Infotagdateien allen der folgenden Regeln entsprechen:
        - Verwenden Sie ausschließlich den UTF8-Zeichensatz (der ANSI-Zeichensatz wird nicht unterstützt).
        - Verwenden Sie als Trennzeichen ein Komma "," (Sie können über eine Dienstanfrage die Änderung des CSV-Trennzeichens von einem Komma "," in ein anderes Zeichen anfordern, beispielsweise ein Semikolon ";").
        - Verwenden Sie nur Großbuchstaben für die booleschen Werte "TRUE" und "FALSE".
        - Verwenden Sie eine Datei, die kleiner ist als die maximale Größe von 35 MB.

**Siehe auch:**

[API-Dokumentation][Link 4], [Dokumentation zur Benutzeroberfläche - Startseite][Link 1]
 
<a name="#SERVICE3">**Symptom:**
3. Funktionsanfragen:</a>

- Die Funktion, die Sie verwenden möchten, ist in Azure Mobile Engagement noch nicht vorhanden.

**Ursachen/Maßnahmen:**

Sie können eine neue Funktion für Azure Mobile Engagement vorschlagen, wenn diese noch nicht vorhanden ist:
    - Senden Sie eine Azure Mobile Engagement-Serviceanfrage mit möglichst vielen Details zu der neuen Funktion, die Sie für Azure Mobile Engagement vorschlagen.
 
## <a name="#SDK">SDK</a>

Es treten bei der Integration von Azure Mobile Engagement in Ihre Anwendung Probleme auf.

**Liste der Symptome:**

1. <a href="#SDK1">Probleme mit dem Azure Mobile Engagement-SDK äußern sich durch einen Fehler in einem anderen Bereich Ihrer Anwendung</a>
2. <a href="#SDK2">Fehler bei der erweiterten Codierung</a>
3. <a href="#SDK3">Probleme mit Anwendungsabstürzen</a>
4. <a href="#SDK4">Fehler bei App Store-Uploads</a>
 
<a name="#SDK1">**Symptom:**
1. Probleme mit dem Azure Mobile Engagement-SDK äußern sich durch einen Fehler in einem anderen Bereich Ihrer Anwendung:</a>

- Es treten Fehler bei der Erfassung von Benutzeroberflächendaten auf (in Analyse, Überwachung, Segmentierung oder Dashboards).
- Pushvorgänge funktionieren nicht (App-intern, App-extern oder beides).
- Es treten Fehler bei erweiterten Funktionen auf (Nachverfolgung, Geolocation oder plattformspezifische Pushes funktionieren nicht).
- API-Fehler (bei APIs treten oft "stille" Fehler ohne Fehlermeldung auf).
- Dienstfehler (Azure Mobile Engagement funktioniert nicht für Ihre Anwendung).

**Ursachen/Maßnahmen:**

- Die meisten Probleme in Bezug auf das Azure Mobile Engagement-SDK äußern sich durch einen Fehler in Ihrer Anwendung (beispielsweise Fehler bei der Erfassung von Benutzeroberflächendaten, Pushfehler, Fehler mit erweiterten Funktionen, API-Fehler, Anwendungsabstürze oder scheinbare Dienstausfälle).  
- Wenn eine bestimmte Funktion von Azure Mobile Engagement noch nie in Ihrer App funktioniert hat, müssen Sie eine Integration durchführen. 
- Wenn eine bestimmte Funktion von Azure Mobile Engagement funktioniert hat, aber jetzt Fehler auftreten, müssen Sie möglicherweise mit dem Azure Mobile Engagement-SDK ein Upgrade auf die neueste Version durchführen. Denken Sie daran, dass es unterschiedliche Versionen des Azure Mobile Engagement-SDK für jede von Azure Mobile Engagement unterstützte Plattform gibt (Android, iOS, Web, Windows und Windows Phone).

*SDK-Integration:* 

- Azure Mobile Engagement ist nicht ordnungsgemäß in das SDK integriert (Analyse).
- Reach ist nicht ordnungsgemäß in das SDK integriert (App-interne und App-externe Pushes).
- Zertifikate abgelaufen oder falsch konfiguriert, PROD bzw. DEV (nur iOS).
- GCM oder ADM sind nicht ordnungsgemäß in das SDK integriert (nur Android - dienstspezifische Pushes).
- Die Nachverfolgung ist nicht ordnungsgemäß in das SDK integriert (installieren Sie die Speicherverfolgung).
- Lazy Location oder GPS-Ortung sind nicht ordnungsgemäß in das SDK integriert (Zielgruppenadressierung durch Geolocation).
[SDK-Dokumentation - Anweisungen zur Integration][Link 5], [Handbuch zur Problembehandlung - Push][Link 2]

*SDK-Upgrade:*

- Es muss ein SDK-Upgrade durchgeführt werden, um Probleme mit älteren SDK-Versionen zu lösen (stehen häufig in Zusammenhang mit neueren Versionen des Gerätebetriebssystems).
- Deinstallieren Sie alle vorherigen Versionen Ihrer App vom Gerät, und installieren Sie die neueste Version Ihrer App. Registrieren Sie Ihre Geräte-ID anschließend erneut über die Azure Mobile Engagement-Benutzeroberfläche um sicherzustellen, dass Ihr Gerät die neueste Version der App verwendet.
[SDK-Dokumentation - Versionshinweise](http://go.microsoft.com/fwlink/?LinkId= 525554), [SDK-Dokumentation - Anweisungen zum Upgrade](http://go.microsoft.com/fwlink/?LinkId= 525554), [Dokumentation zur Benutzeroberfläche - Einstellungen][Link 1]

*Weitere SDK-Probleme:*

- Fehler in Codeabschnitten mit Bezug zu Azure Mobile Engagement können dazu führen, dass Azure Mobile Engagement nicht funktioniert.
- Fehler im Anwendungsmanifest "AndroidManifest.xml" kann dazu führen, dass Azure Mobile Engagement nicht funktioniert (nur Android).
- Ein häufiger Fehler bei SDK-Integration und 
- API-Verwendung ist der, dass SDK-Schlüssel und API-Schlüssel verwechselt werden.
[Konzepte - Glossar][Link 6]
 
<a name="#SDK2">**Symptom:**
2. Fehler bei der erweiterten Codierung:</a>

-  Plattformspezifischer Code, der nicht in direktem Zusammenhang mit Azure Mobile Engagement steht, kann zu Problemen auf iOS, Android und Windows Phone führen.

**Ursachen/Maßnahmen:**

- Viele Probleme mit Azure Mobile Engagement, die bei der erweiterten Codierung auftreten, werden durch fehlerhaften plattformspezifischen Code verursacht, der nicht in direktem Zusammenhang mit Azure Mobile Engagement steht. Neben der Azure Mobile Engagement-Dokumentation müssen Sie auch die Dokumentation für die Plattform zu Rate ziehen, für die Sie entwickeln (Android, iOS, Web, Windows und Windows Phone).
- Eine nicht ordnungsgemäße Konfiguration von "categories" verhindert das Verlinken aus einer Benachrichtigung mit einem anderen Speicherort, entweder App-intern oder App-extern (nur Android). 
- Wenn Sie "UIKit.framework" in Ihrem iOS-Code auf "optional" setzen, wird ein "Symbol nicht gefunden"-Fehler angezeigt und/oder es kommt auf älteren iOS-Geräten zu einem Absturz (nur iOS).
- Abgelaufene Zertifikate oder Zertifikate, die nicht die richtige DEV- oder Prod-Version des Zertifikats verwenden, verursachen Pushfehler (nur iOS).
- Es gelten einige Plattformeinschränkungen, die Azure Mobile Engagement nicht steuern kann (beispielsweise die Funktionsweise von System Center für App-externe Pushes in Android und iOS).
- Azure Mobile Engagement veröffentlicht als Referenz eine vollständige Liste der internen Pakete, die von Azure Mobile Engagement für iOS und Android verwendet werden. Beachten Sie, dass einige Funktionen von Azure Mobile Engagement plattformspezifisch sind (Android, iOS, Web, Windows und Windows Phone).
- Die SDKs für die verschiedenen Plattformen sind in den folgenden Programmiersprachen geschrieben:
    -     Android-SDK in Java
    -     iOS-SDK in Objective-C
    -     Web-SDK in JavaScript
    -     Windows-SDK in C# und JavaScript
    -     Windows Phone-SDK in C# und JavaScript

**Siehe auch:**

 - [Handbuch zur Problembehandlung - Push][Link 2], [SDK-Dokumentation - Versionshinweise][Link 5], [SDK-Dokumentation - Anweisungen zu Upgrades][Link 5], [SDK-Dokumentation - Android - Azure Mobile Engagement - Übersicht über die technische Dokumentation][Link 5], [SDK-Dokumentation - iOS - Übersicht über die technische Dokumentation][Link 5], [SDK-Dokumentation - iOS - Vorbereiten Ihrer Anwendung auf Apple-Pushbenachrichtigungen][Link 5], [Android-Entwickler](https://developer.android.com/), [iOS-Entwickler](https://developer.apple.com/), [Windows-Entwickler](https://developer.windows.com/) 
 
<a name="#SDK3">**Symptom:**
3.    Probleme mit Anwendungsabstürzen</a>

- Die Anwendung stürzt auf dem Endbenutzergerät ab.

**Ursachen/Maßnahmen:**

- In der Benutzeroberfläche oder API für die Analyse können Sie Absturzinformationen anzeigen.
- Sie können die Geräte-ID Ihres Testgeräts ermitteln und dieselbe Aktion ausführen, die bei einem Endbenutzer zum Absturz Ihrer Anwendung führt, um die Ursache des Absturzes zu identifizieren.
- Bekannte Probleme mit dem Azure Mobile Engagement-SDK, die zu Abstürzen der Anwendung führen, können manchmal durch ein Upgrade auf die neueste SDK-Version behoben werden. Stellen Sie daher sicher, die Versionshinweise zu Ihrer Plattform zu lesen, wenn Sie Abstürze untersuchen.

**Siehe auch:**

- [Konzepte - häufig gestellte Fragen (FAQ)][Link 6], [Konzepte - Glossar][Link 6], [API-Dokumentation - Analyse-API - Abstürze][Link 4], [Dokumentation zur Benutzeroberfläche - Analyse- Abstürze][Link 1], [Dokumentation zur Benutzeroberfläche - Einstellungen][Link 1], [SDK-Dokumentation - Versionshinweise][Link 5], [SDK-Dokumentation - Anweisungen zu Upgrades][Link 5]

<a name="#SDK4">**Symptom:**
4. Fehler bei App Store-Uploads</a>

- Es treten Fehler auf, wenn Sie die neueste Version Ihrer App in iTunes, GooglePlay oder den Windows- oder Windows Phone-Store hochladen.

**Ursachen/Maßnahmen:**

- App Stores blockieren gelegentlich Apps, bei denen bestimmte Funktionen aktiviert sind (iTunes Store verhindert die Verwendung von IDFV in Apps im Store, und GooglePlay verhindert die Freigabe von Anwendungsinformationen zwischen Apps). 
- Prüfen Sie die Versionshinweise zu Ihrer Plattform und dem aktuellen SDK, wenn Sie Probleme beim Hochladen einer App in den Store haben.

**Siehe auch:**

- [SDK-Dokumentation - Versionshinweise][Link 5], [SDK-Dokumentation - Anweisungen zu Upgrades][Link 5] 

## <a name="#SR">Informationen in Serviceanfragen zur Problembehandlung</a>

Stellen Sie die folgenden Informationen bereit, wenn Sie eine Azure Mobile Engagement-Serviceanfrage einreichen:
 
**IDs: Stellen Sie alle IDs zur Verfügung, die in Zusammenhang mit Ihrem Problem stehen:**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**Fehler: Stellen Sie alle Fehlerinformationen zur Verfügung, die in Zusammenhang mit Ihrem Problem stehen:**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [Anweisungen zur Problembehandlung](http://go.microsoft.com/fwlink/?LinkId=524382)


**Code: Stellen Sie alle relevanten Codierungsinformationen bereit, die in Zusammenhang mit Ihrem Problem stehen:**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

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
