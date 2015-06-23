<properties 
   pageTitle="Azure Mobile Engagement - Handbuch zur Problembehandlung - Analysefunktionen" 
   description="Behandlung von Problemen bei Analyse, Überwachung, Segmentierung und mit dem Dashboard in Azure Mobile Engagement" 
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

# Handbuch zur Behandlung von Problemen bei Analyse, Überwachung, Segmentierung und mit dem Dashboard

Im Folgenden finden Sie mögliche Probleme, die hinsichtlich der Art, in der Azure Mobile Engagement Informationen über Ihre Anwendungen, Geräte und Benutzer sammelt, auftreten können.

## Fehlende/verzögerte Informationen

### Problem
- Informationen werden in Analyse, Segmentierung oder Dashboard verzögert angezeigt.
- Informationen fehlen in der Überwachung.
- Informationen fehlen in Analyse, Segmentierung oder Dashboard.
- Segmentierungsgrenzwerte werden erreicht.

### Ursachen:

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

### Weitere Informationen

- [Handbuch zur Problembehandlung - SDK][Link 25], [API-Dokumentation][Link 4], [Dokumentation zur Benutzeroberfläche - Segmente][Link 18]

## Elemente sind nicht in der Benutzeroberfläche auffindbar

### Problem
- Segmente können nicht basierend auf bestimmten integrierten oder benutzerdefinierten Infotagkriterien der App erstellt werden.
- Bestimmte integrierte oder benutzerdefinierte Infotagkriterien der App sind in Analyse, Überwachung oder Dashboards nicht auffindbar.
- Daten in Analyse, Überwachung, Segmentierung oder Dashboards können nicht interpretiert werden.

### Ursachen:

- Einige integrierte Elemente und Infotags der App sind nur als Pushkriterien verfügbar. Sie können weder einem Segment hinzugefügt werden, noch sind sie in Analyse, Überwachung oder Dashboard sichtbar. 
- Für integrierte Elemente und Infotags der App, die keinem Segment hinzugefügt werden können, müssen Sie in jeder Kampagne eine Liste der Zielkriterien einrichten, um eine wie auf Segmenten basierende Zielgruppenadressierung zu erreichen.
- Weitere Informationen und Anleitungen finden Sie in den Kontextmenüs von Analyse, Überwachung und Dashboards der Azure Mobile Engagement-Benutzeroberfläche.

### Weitere Informationen

- [Dokumentation zur Benutzeroberfläche - Neues Reach-Pushkriterium für die Zielgruppenadressierung][Link 28]
 
## Problembehandlung von Abstürzen

### Problem
- In Analyse, Überwachung oder Dashboard werden Anwendungsabstürze angezeigt.

### Ursachen:

- Zur Problembehandlung von Anwendungsabstürzen, die in Analyse, Überwachung oder Dashboard angezeigt werden, prüfen Sie die Versionshinweise auf bekannte Probleme mit vorherigen Versionen des SDK.
- Generieren Sie zur erweiterten Problembehandlung von Anwendungsabstürzen ein Ereignis auf einem Testgerät, auf dem die Anwendung installiert ist. Suchen Sie anschließend im Abschnitt "Überwachen - Ereignisse" der Azure Mobile Engagement-Benutzeroberfläche nach Ihrer Geräte-ID. Lösen Sie dann das Ereignis aus, das Ihre Anwendung zum Absturz bringt, und prüfen Sie die zusätzlichen Informationen im Abschnitt "Überwachen - Absturz" der Azure Mobile Engagement-Benutzeroberfläche. 

### Weitere Informationen

- [Konzepte - Häufig gestellte Fragen][Link 6], [Konzepte - Glossar][Link 6], [Dokumentation zur Benutzeroberfläche][Link 1], [SDK-Dokumentation - Anmerkungen zu dieser Version][Link 5], [SDK-Dokumentation - Upgradehandbücher][Link 5]

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