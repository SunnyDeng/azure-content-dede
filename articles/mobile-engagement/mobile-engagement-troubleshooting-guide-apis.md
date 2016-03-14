<properties 
   pageTitle="Azure Mobile Engagement – Handbuch zur Problembehandlung – APIs" 
   description="Handbücher zur Problembehandlung für Azure Mobile Engagement – APIs" 
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
   ms.date="02/29/2016"
   ms.author="piyushjo"/>

# Handbuch zur Problembehandlung bei APIs

Im Folgenden finden Sie mögliche Probleme, die auftreten können, wenn Administratoren über die APIs mit Azure Mobile Engagement interagieren.

## Syntaxfehler

### Problem
- Syntaxfelder bei Verwendung der API (oder unerwartetes Verhalten).

### Ursachen

- Syntaxfehler:
    - Stellen Sie durch Prüfen der Syntax der jeweiligen API sicher, dass die Option zur Verfügung steht.
    - Ein häufiger Fehler bei der API-Verwendung besteht darin, dass Reach-API und Push-API verwechselt werden (die meisten Aufgaben sollten nicht mit der Push-API, sondern mit der Reach-API ausgeführt werden). 
    - Ein weiterer häufiger Fehler bei der SDK-Integration und der API-Verwendung ist der, dass SDK- und API-Schlüssel verwechselt werden.
    - Skripts, die eine Verbindung zu den APIs herstellen, müssen mindestens alle 10 Minuten Daten senden, ansonsten kommt es zu einer Zeitüberschreitung (besonders häufig in API-Überwachungsskripts, die auf Daten prüfen). Um Zeitüberschreitungen zu verhindern, sollte Ihr Skript alle 10 Minuten ein XMPP-Pingsignal senden, um die Verbindung mit dem Server aufrechtzuerhalten.

### Weitere Informationen
 
- [API-Dokumentation][Link 4]
- [Informationen zum XMPP-Protokoll](http://xmpp.org/extensions/xep-0199.html)
 
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

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### Weitere Informationen

- [API-Dokumentation – detaillierte Fehlerinformationen zu jeder API][Link 4]
 
## Verborgene ("stille") Fehler

### Problem
- Eine API-Aktion wird nicht ausgeführt, es wird jedoch weder zur Laufzeit noch in den Protokollen eine Fehlermeldung angezeigt.

### Ursachen

- In der Azure Mobile Engagement-Benutzeroberfläche werden viele Elemente deaktiviert, wenn sie nicht ordnungsgemäß integriert wurden. Bei der API hingegen treten nur "stille" Fehler (ohne Fehlermeldung) auf. Testen Sie deshalb mithilfe der Benutzeroberfläche, ob dieselbe Funktionalität dort korrekt arbeitet.
- Azure Mobile Engagement und viele weitere Funktionen von Azure Mobile Engagement müssen einzeln mit dem SDK in Ihre App integriert werden, bevor sie genutzt werden können.

### Weitere Informationen

- [Handbuch zur Problembehandlung – SDK][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=azuremobileengagement
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
 

<!---HONumber=AcomDC_0302_2016-->