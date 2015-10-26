<properties 
	pageTitle="Application Insights: Proaktive Erkennung von Anomalien" 
	description="Application Insights führt eine umfassende Analyse Ihrer App-Telemetrie durch und warnt Sie vor potenziellen Problemen." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="awills"/>

#  Application Insights: Proaktive Erkennung von Anomalien

*Application Insights befindet sich in der Vorschau.*


Application Insights führt eine umfassende Analyse Ihrer App-Telemetrie durch und kann Sie bei potenziellen Leistungsproblemen warnen. Sie lesen dies wahrscheinlich, da Sie eine unser Anomaliewarnungen per E-Mail erhalten haben.

## Informationen über die Anomaliewarnung

* *Warum habe ich diese Warnung erhalten?*
 * Application Insights analysiert Ihre Daten in regelmäßigen Abständen mit Mustererkennungsregeln. Dabei wird nach Anomalien gesucht, die auf Leistungsprobleme in Ihrer Anwendung hinweisen können.
* *Bedeutet die Benachrichtigung, dass ich definitiv ein Problem habe?*
 * Nein. Es handelt sich lediglich um Vorschlag über etwas, das Sie sich möglicherweise genauer ansehen möchten. 
* *Wie sollte ich vorgehen?*
 * [Sehen Sie sich die angezeigten Daten an](#responding-to-an-alert), und überlegen Sie, ob sie möglicherweise ein Problem darstellen. Ist dies nicht der Fall, ist alles in Ordnung.
* *Meine Daten werden also angesehen?*
 * Nein. Der Dienst ist vollständig automatisch. Nur Sie erhalten die Benachrichtigungen. Ihre Daten sind [privat](app-insights-data-retention-privacy.md).


## Der Erkennungsvorgang

* *Welche Arten von Anomalien werden erkannt?*
 * Muster, deren Überprüfung für Sie selbst zeitaufwendig wäre Beispiel: schlechte Leistung bei einer bestimmten Kombination aus Ort, Uhrzeit und Plattform
* *Kann ich meinen eigenen Erkennungsregeln für Anomalien erstellen?*
 * Nicht für diese Art der umfassenden Analyse. (Sie können jedoch [Warnungen einrichten](app-insights-alerts.md) die Sie informieren, wenn eine Metrik einen Schwellenwert überschreitet.)
* *Wie oft wird die Analyse ausgeführt?*
 * Wir führen keine Analysen von App-Ressourcen, die wenig Telemetrie aufweisen. Sie erhalten wahrscheinlich keine Warnungen zu Ihrem Debugsitzungen.


## Reagieren auf eine Warnung

Öffnen Sie den Anomaliebericht entweder über die E-Mail oder die Anomalienliste.

![](./media/app-insights-anomaly/02.png)

Hinweis:

* Die Beschreibung
* Aussage bezüglich der Auswirkungen, die angibt, wie viele oder wie häufig Benutzer betroffen sind

Klicken Sie auf ein Diagramm, um ein Blatt mit mehr Details zu öffnen.

Ändern Sie den Zeitraum und die Filter zum Durchsuchen der Telemetrie.




## E-Mail-Benachrichtigungen

* *Muss ich diesen Dienst abonnieren, um Benachrichtigungen zu erhalten?*
 * Nein. Unser Bot überprüft in regelmäßigen Abständen die Daten aller Application Insights-Benutzer und sendet Benachrichtigungen, wenn Probleme erkannt werden.
* *Kann ich das Abonnement abbestellen oder die Benachrichtigungen stattdessen an meine Kollegen senden lassen?*
 * Klicken Sie auf den Link in der Warnung oder in der E-Mail. Öffnen Sie die Einstellungen für Anomalien.
 
    ![](./media/app-insights-anomaly/01.png)

    Derzeit werden Sie an Personen gesendet, die über [Schreibzugriff für die Application Insights-Ressource](app-insights-resources-roles-access-control.md) verfügen.
* *Ich möchte nicht mit diesen Nachrichten überflutet werden.*
 * Die Nachrichten sind auf drei pro Tag beschränkt. Sie erhalten Nachrichten nicht mehrfach.
* *Erhalte ich eine Erinnerung, wenn ich nichts unternehme?*
 * Nein, Sie erhalten jeweils nur eine Nachricht zu einem Problem.
* *Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*
 * Klicken Sie in der Application Insights-Übersicht Ihrer App auf die Kachel **Anomalien**. 






 

<!---HONumber=Oct15_HO3-->