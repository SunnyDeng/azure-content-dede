<properties 
   pageTitle="Azure Mobile Engagement - Handbuch zur Problembehandlung - APIs" 
   description="Handbücher zur Problembehandlung für Azure Mobile Engagement" 
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

# Handbuch zur Behandlung von API-Problemen

Im Folgenden finden Sie mögliche Probleme, die hinsichtlich der Art, in der Administratoren mit Azure Mobile Engagement über die APIs interagieren, auftreten können.

## Syntaxfehler

### Problem
- Syntaxfelder bei Verwendung der API (oder unerwartetes Verhalten).

### Ursachen:

- Syntaxfehler:
    - Stellen Sie durch Prüfen der Syntax der jeweiligen API sicher, dass die Option zur Verfügung steht.
    - Ein häufiger Fehler bei der API-Verwendung besteht darin, dass Reach-API und Push-API verwechselt werden (die meisten Aufgaben sollten nicht mit der Push-API, sondern mit der Reach-API ausgeführt werden). 
    - Ein weiterer häufiger Fehler bei der SDK-Integration und der API-Verwendung ist der, dass SDK- und API-Schlüssel verwechselt werden.
    - Skripts, die eine Verbindung zu den APIs herstellen, müssen mindestens alle 10 Minuten Daten senden, ansonsten kommt es zu einer Zeitüberschreitung (besonders häufig in API-Überwachungsskripts, die auf Daten prüfen). Um Zeitüberschreitungen zu verhindern, sollte Ihr Skript alle 10 Minuten ein XMPP-Pingsignal senden, um die Verbindung mit dem Server aufrechtzuerhalten.

### Weitere Informationen
 
- [Konzepte - Glossar][Link 6], [API-Dokumentation][Link 4], [XMPP-Protokollinformationen](http://xmpp.org/extensions/xep-0199.html)
 
## Keine Möglichkeit zur Verwendung der API zur Durchführung derselben Aktion, die auch in der Azure Mobile Engagement-Benutzeroberfläche verfügbar ist

### Problem
- Eine Aktion, die mit der Azure Mobile Engagement-Benutzeroberfläche ausgeführt werden kann, funktioniert nicht in der entsprechenden Azure Mobile Engagement-API.

### Ursachen

- Wenn Sie dieselbe Aktion über die Azure Mobile Engagement-Benutzeroberfläche ausführen können, wurde diese Funktion von Azure Mobile Engagement mit dem SDK ordnungsgemäß integriert.

### Weitere Informationen
 
- [Dokumentation zur Benutzeroberfläche][Link 1]
 
## Fehlermeldungen

### Problem
- Bei Verwendung der API werden zur Laufzeit oder in Protokollen Fehlercodes angezeigt.

### Ursachen

- Nachfolgend finden Sie eine Zusammenstellung häufiger API-Statuscodes als Referenz und zur ersten Problembehandlung:

        200        Erfolg.
        200        Konto aktualisiert: Gerät registriert, zugeordnet, aktualisiert oder aus dem aktuellen Konto entfernt.
        200        Gibt eine Liste mit Projekten als JSON-Objekt zurück oder ein Authentifizierungstoken, das im Nachrichtentext der Antwort generiert und zurückgegeben wird.
        201        Konto erstellt.
        400        Ungültiger Parameter oder Überprüfungsausnahme (überprüfen Sie die Nutzlast auf Einzelheiten). Die der API oder dem Dienst bereitgestellten Parameter sind ungültig. In diesem Fall sind in der HTTP-Antwort weitere Details enthalten. Stellen Sie sicher, dass Sie den MIME-Typ der Antwort überprüfen, da die Nutzlast unformatierter Text oder ein JSON-Objekt sein kann.
        401        Authentifizierungsfehler. Zurzeit ist kein Benutzer authentifiziert oder verbunden (überprüfen Sie die AppID und den SDK-Schlüssel).
        402        Abrechnungssperre. Die Anwendung hat entweder ihre Kontingente überschritten, oder sie befindet sich im Moment in einem fehlerhaften Abrechnungszustand.
        403        Die Anwendung ist nicht aktiviert ist, oder die spezifische API ist für diese Anwendung deaktiviert.
        403        Nicht autorisierter Zugriff auf das Projekt oder die Anwendung, ungültiger Schlüsselzugriff (der Schlüssel muss mit dem bei der Erstellung zur Verfügung gestellten übereinstimmen).
        403        Kampagnenspezifische Fehler: Die Kampagne muss fertig gestellt sein (oder wurde bereits aktiviert), die Anhaltenaktion kann nur bei einer geplanten Kampagne ausgeführt werden, eine Kampagne, die aktuell nicht den Status "In Bearbeitung" hat, kann nicht fertig gestellt werden, die Kampagne muss "In Bearbeitung" und die Eigenschaft der Kampagne benannt sein, manuelles Push muss auf "true" festgelegt sein.
        403        Die E-Mail-Adresse ist bereits einem anderen Konto zugeordnet (z. B. einem Administrator). Es wird kein Authentifizierungstoken generiert.
        404        Anwendungs-, Geräte-, Kampagnen- oder Projektbezeichner nicht gefunden.
        404        Ein Abfrageparameter ist ungültiges JSON oder hat ein Feld mit einem unerwarteten Wert.
        404        Die E-Mail-Adresse ist keinem Konto zugeordnet. Erstellen Sie zuerst das Konto, oder aktualisieren Sie es.
        405        Ungültige HTTP-Methode (GET, POST usw.) oder Versuch der Bearbeitung eines schreibgeschützten Segments (d. h. Hinzufügen, Aktualisieren oder Löschen eines Kriteriums). Ein Segment wird erst schreibgeschützt, nachdem es zum ersten Mal berechnet wurde.
        409        Der Name ist bereits einer anderen Geräte-ID oder Kampagne zugeordnet.
        413        Zu viele Gerätebezeichner (aktueller Grenzwert ist 1.000), POST URL-codierte Entität ist größer als 2 MB, oder der Zeitraum ist zur Anzeige zu groß (der Server hat die Analyse nicht abgerufen, weil sich die Benutzeranforderung auf einen zu großen Zeitraum bezieht).
        503        Analyse noch nicht verfügbar (die angeforderten Informationen sind für eine Anwendung noch nicht berechnet).
        504         Der Server konnte Ihre Anforderung nicht in angemessener Zeit verarbeiten (wenn Sie mehrere Aufrufe an eine API sehr schnell vornehmen, versuchen Sie, immer nur einen Aufruf zu senden und die Aufrufe über einen Zeitraum zu verteilen).

### Weitere Informationen

- [API-Dokumentation - detaillierte Fehlerinformationen zu jeder angegebenen API][Link 4]
 
## Verborgene ("stille") Fehler

### Problem
- Eine API-Aktion wird nicht ausgeführt, es wird jedoch weder zur Laufzeit noch in den Protokollen eine Fehlermeldung angezeigt.

### Ursachen

- In der Azure Mobile Engagement-Benutzeroberfläche werden viele Elemente deaktiviert, wenn sie nicht ordnungsgemäß integriert wurden. Bei der API hingegen treten nur "stille" Fehler (ohne Fehlermeldung) auf. Testen Sie deshalb mithilfe der Benutzeroberfläche, ob dieselbe Funktionalität dort korrekt arbeitet.
- Azure Mobile Engagement und viele weitere Funktionen von Azure Mobile Engagement müssen einzeln mit dem SDK in Ihre App integriert werden, bevor sie genutzt werden können.

### Weitere Informationen

- [Handbuch zur Problembehandlung - SDK][Link 25], [SDK-Dokumentation][Link 5]
 
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