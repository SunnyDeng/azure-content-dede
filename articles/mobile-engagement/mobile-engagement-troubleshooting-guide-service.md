<properties 
   pageTitle="Azure Mobile Engagement – Handbuch zur Problembehandlung – Dienste" 
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
   ms.date="06/18/2015"
   ms.author="piyushjo"/>

# Handbuch zur Problembehandlung bei Dienstproblemen

Im Folgenden finden Sie mögliche Probleme, die bei der Ausführung von Azure Mobile Engagement auftreten können.

## Dienstausfälle

### Problem
- Probleme, die offenbar von Dienstausfällen in Azure Mobile Engagement verursacht werden.

### Ursachen
- Probleme, die scheinbar von Azure Mobile Engagement-Dienstausfällen verursacht werden, können verschiedene Ursachen haben:
    - Isolierte Probleme, die ursprünglich systemisch in Azure Mobile Engagement auftreten
    - Bekannte Probleme, die durch Serverausfälle verursacht werden (nicht immer im Serverstatus angezeigt):
	- Verzögerungen bei der Planung, Fehler bei der Zielgruppenadressierung, Probleme bei der Badgeaktualisierung, keine Erfassung von Statistiken, Pushvorgänge funktionieren nicht mehr, APIs funktionieren nicht mehr, neue Apps oder Benutzer können nicht erstellt werden, DNS-Fehler, Zeitüberschreitungen in Benutzeroberfläche, API oder Apps auf einem Gerät.
    - Cloudabhängigkeitsfehler [Azure-Dienststatus](http://status.azure.com/)
    - PNS-Abhängigkeitsfehler (Push Notification Services)
    - App Store-Ausfälle

1) Um zu testen, ob es sich um ein systemisches Problem handelt, können Sie dieselbe Funktion von anderer Stelle ausführen:
   
   - Integrierte Azure Mobile Engagement-Anwendung
   - Testgerät
   - Betriebssystemversion des Testgeräts
   - Kampagne
   - Administratorbenutzerkonto
   - Browser (IE, Firefox, Chrome usw.)
   - Computer

2) So testen Sie, ob das Problem nur die Benutzeroberfläche oder die APIs betrifft:

   - Testen Sie die gleiche Funktion über die Azure Mobile Engagement-Benutzeroberfläche und die Azure Mobile Engagement-APIs.

3) So testen Sie, ob ein Problem mit dem Mobilfunknetz vorliegt:

   - Testen Sie, während Sie über WIFI und über Ihr 3G-Mobilfunknetz mit dem Internet verbunden sind.
   - Vergewissern Sie sich, dass Ihre Firewall keine Azure Mobile Engagement-IP-Adressen oder -Ports blockiert.

4) So testen Sie, ob ein Geräteproblem vorliegt:

   - Testen Sie, ob Ihr Gerät mit einer anderen integrierten Azure Mobile Engagement-App eine Verbindung mit Azure Mobile Engagement herstellen kann.
   - Testen Sie, ob Sie Ereignisse, Aufträge und Abstürze über Ihr Telefon generieren können, die auf der Azure Mobile Engagement-Benutzeroberfläche angezeigt werden. 
   - Testen Sie, ob Sie Pushbenachrichtigungen von der Azure Mobile Engagement-Benutzeroberfläche basierend auf der Geräte-ID an Ihr Gerät senden können. 

5) So testen Sie, ob ein Problem mit der App vorliegt:

   - Installieren und testen Sie die Anwendung mit einem Emulator statt eines physischen Geräts:
   
6) So testen Sie, ob ein Problem mit Betriebssystemupgrades auf Endbenutzergeräten vorliegt, für dessen Lösung ein SDK-Upgrade erforderlich ist:

   - Testen Sie die Anwendung auf verschiedenen Geräten mit verschiedenen Versionen des Betriebssystems.
   - Vergewissern Sie sich, dass Sie die neueste Version des SDK verwenden.
 
## Konnektivitätsprobleme und falsche Informationen

### Problem
- Es treten bei der Anmeldung an der Azure Mobile Engagement-Benutzeroberfläche Probleme auf.
- Verbindungsfehler mit den Azure Mobile Engagement-APIs.
- Probleme beim Hochladen von Infotags der App über die Geräte-API.
- Probleme beim Herunterladen von Protokollen oder exportierten Daten aus Azure Mobile Engagement.
- Anzeige von falschen Informationen in der Azure Mobile Engagement-Benutzeroberfläche.
- Anzeige von falschen Informationen in Azure Mobile Engagement-Protokollen.

### Ursachen
* Vergewissern Sie sich, dass Ihr Benutzerkonto über ausreichende Berechtigungen zum Ausführen der Aufgabe verfügt.
* Stellen Sie sicher, dass es sich nicht um ein isoliertes Problem mit einem Computer oder Ihrem lokalen Netzwerk handelt.
* Vergewissern Sie sich, dass der Azure Mobile Engagement-Dienst keine Ausfälle meldet.
* Stellen Sie sicher, dass Ihre App-Infotagdateien allen der folgenden Regeln entsprechen:
	- Verwenden Sie ausschließlich den UTF8-Zeichensatz (der ANSI-Zeichensatz wird nicht unterstützt).
    - Verwenden Sie als Trennzeichen ein Komma "," (Sie können über eine Dienstanfrage die Änderung des CSV-Trennzeichens von einem Komma "," in ein anderes Zeichen anfordern, beispielsweise ein Semikolon ";").
    - Verwenden Sie ausschließlich Kleinbuchstaben für die booleschen Werte "true" und "false".
    - Verwenden Sie eine Datei, die kleiner ist als die maximale Größe von 35 MB.
 

<!---HONumber=Oct15_HO3-->