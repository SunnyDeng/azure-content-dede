<properties 
	pageTitle="Application Insights: Proaktive Erkennung von Anomalien" 
	description="Application Insights führt eine umfassende Analyse Ihrer App-Telemetrie durch und warnt Sie vor potenziellen Problemen." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="awills"/>

#  Application Insights: Proaktive Warnungen

*Application Insights befindet sich in der Vorschau.*


Application Insights führt eine umfassende Analyse Ihrer App-Telemetrie durch und kann Sie bei potenziellen Leistungsproblemen warnen. Sie lesen dies wahrscheinlich, da Sie eine unserer proaktiven Warnungen per E-Mail erhalten haben.

## Informationen zur proaktiven Warnung

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
 * Bisher nicht. Sie können aber Folgendes tun:
 * [Einrichten von Warnungen](app-insights-alerts.md), die Sie informieren, wenn eine Metrik einen Schwellenwert überschreitet.
 * [Exportieren von Telemetrie](app-insights-export-telemetry.md) in eine [Datenbank](app-insights-code-sample-export-sql-stream-analytics.md) oder [Power BI](app-insights-export-power-bi.md) oder [andere](app-insights-code-sample-export-telemetry-sql-database.md) Tools, mit denen Sie selbst eine Analyse durchführen können.
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

## Es ist gut, diese Warnungen zu erhalten. Aber was kann ich tun, um die Leistung zu verbessern?

Langsame und fehlgeschlagene Antworten sind für Websitebenutzer eines der größten Ärgernisse, was Sie sicherlich aus eigener Erfahrung bestätigen können. Daher ist es wichtig, diese Probleme zu beheben.

### Eingrenzung

Erstens: Ist es wirklich ein Problem? Wenn eine Seite immer langsam geladen wird, aber nur 1 % der Benutzer Ihrer Website diese Seite anzeigen müssen, gibt es vielleicht wichtigere Dinge, mit denen Sie sich beschäftigen müssen. Falls nur 1 % der Benutzer darauf zugreifen, aber jedes Mal Ausnahmen ausgelöst werden, kann es erforderlich sein, dies zu untersuchen.

Verwenden Sie die Aussage zur Auswirkung in der E-Mail als allgemeinen Anhaltspunkt, aber seien Sie sich bewusst, dass damit nicht alles abgedeckt wird. Sammeln Sie zur Bestätigung weitere Beweise.

Sehen Sie sich die Parameter des Problems an. Falls eine geografische Abhängigkeit besteht, sollten Sie [Verfügbarkeitstests](app-insights-monitor-web-app-availability.md) für diese Region einrichten. Vielleicht liegen in diesem Bereich lediglich Netzwerkprobleme vor.

### Diagnostizieren des langsamen Ladens von Seiten 

Wo liegt das Problem? Reagiert der Server langsam, ist die Seite sehr lang oder muss der Browser viel Aufwand betreiben, um die Seite anzuzeigen?

Öffnen Sie das Blatt mit den Browsermetriken. Die [segmentierte Anzeige der Browser-Seitenladezeit](app-insights-javascript.md#explore-your-data) gibt an, wo Zeit verbraucht wird.

* Wenn die **Zeit für das Senden von Anforderungen** hoch ist, reagiert entweder der Server langsam, oder die Anforderung umfasst eine größere Datenmenge. Sehen Sie sich die [Leistungsmetriken](app-insights-web-monitor-performance.md#metrics) an, um die Reaktionszeiten zu prüfen. 
* Richten Sie die [Abhängigkeitsüberwachung](app-insights-dependencies.md) ein, um ermitteln zu können, ob die Langsamkeit mit externen Diensten oder Ihrer Datenbank zusammenhängt.
* Wenn die **Zeit für das Empfangen von Antworten** vorherrscht, sind Ihre Seite und die abhängigen Teile lang – JavaScript, CSS, Bilder usw. (aber keine asynchron geladenen Daten). Richten Sie einen [Verfügbarkeitstest](app-insights-monitor-web-app-availability.md) ein, und legen Sie die Option zum Laden abhängiger Teile fest. Wenn Sie einige Ergebnisse erhalten, öffnen Sie die Details eines Ergebnisses und erweitern diese, um die Ladezeiten für unterschiedliche Dateien anzuzeigen.
* Eine hohe **Clientverarbeitungszeit** deutet darauf hin, dass die Ausführung der Skripts lange dauert. Falls die Ursache nicht offensichtlich ist, können Sie erwägen, Zeitsteuerungscode hinzuzufügen und die Zeiten über trackMetric-Aufrufe zu senden.

### Verbessern langsamer Seiten

Da Sie im Web viele Ratschläge zur Verbesserung der Serverreaktionszeit und Seitenladezeiten finden, soll dies hier nicht alles wiederholt werden. Sie erhalten aber einige Tipps, die Sie vielleicht bereits kennen, aber die hier noch einmal in Erinnerung gerufen werden sollen:

* Langsames Laden aufgrund von großen Dateien: Laden Sie die Skripts und anderen Teile asynchron. Verwenden Sie die Skriptbündelung. Unterteilen Sie die Hauptseite in Widgets, für die die Daten separat geladen werden. Kein Senden von reinen „alten“ HTML-Daten für lange Tabellen: Verwenden Sie ein Skript, um die Daten als JSON oder in einem anderen kompakten Format anzufordern, und füllen Sie anschließend die Tabelle mit Daten. Es gibt gute Frameworks, die hierfür hilfreich sind. (Die natürlich mit großen Skripts verbunden sind.)
* Langsame Serverabhängigkeiten: Sehen Sie sich die geografischen Standorte der Komponenten an. Stellen Sie bei Verwendung von Azure beispielsweise sicher, dass sich der Webserver und die Datenbank in derselben Region befinden. Werden bei Abfragen mehr Informationen als nötig abgerufen? Würde das Zwischenspeichern oder eine Batchverarbeitung helfen?
* Kapazitätsprobleme: Sehen Sie sich die Servermetriken von Reaktionszeiten und die Anforderungsanzahl an. Wenn für die Antwortzeiten unverhältnismäßige Spitzen gegenüber den Spitzen bei der Anzahl der Anforderungen zu beobachten sind, ist die Wahrscheinlichkeit hoch, dass Ihre Server überlastet sind. 


## E-Mail-Benachrichtigungen

* *Muss ich diesen Dienst abonnieren, um Benachrichtigungen zu erhalten?*
 * Nein. Unser Bot überprüft in regelmäßigen Abständen die Daten aller Application Insights-Benutzer und sendet Benachrichtigungen, wenn Probleme erkannt werden.
* *Kann ich das Abonnement abbestellen oder die Benachrichtigungen stattdessen an meine Kollegen senden lassen?*
 * Klicken Sie auf den Link in der Warnung oder in der E-Mail. Öffnen Sie die Einstellungen für Anomalien.
 
    ![](./media/app-insights-anomaly/01.png)

    Derzeit werden sie an Personen gesendet, die über [Schreibzugriff für die Application Insights-Ressource](app-insights-resources-roles-access-control.md) verfügen.
* *Ich möchte nicht mit diesen Nachrichten überflutet werden.*
 * Die Nachrichten sind auf drei pro Tag beschränkt. Sie erhalten Nachrichten nicht mehrfach.
* *Erhalte ich eine Erinnerung, wenn ich nichts unternehme?*
 * Nein, Sie erhalten jeweils nur eine Nachricht zu einem Problem.
* *Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*
 * Klicken Sie in der Application Insights-Übersicht Ihrer App auf die Kachel **Anomalien**. 






 

<!---HONumber=Nov15_HO2-->