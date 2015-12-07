<properties 
	pageTitle="Application Insights: Proaktive Erkennung" 
	description="Application Insights führt eine umfassende Analyse Ihrer App-Telemetrie durch und warnt Sie vor potenziellen Problemen." 
	services="application-insights" 
    documentationCenter="windows"
	authors="antonfrMSFT" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="awills"/>

#  Application Insights: Proaktive Erkennung

*Application Insights befindet sich in der Vorschau.*


Application Insights führt eine umfassende Analyse Ihrer App-Telemetrie durch und kann Sie bei potenziellen Leistungsproblemen warnen. Sie lesen dies wahrscheinlich, da Sie eine unserer proaktiven Warnungen per E-Mail erhalten haben.

## Was ist die proaktive Erkennung?

Bei der proaktiven Erkennung werden mithilfe von Machine Learning- und Data Mining-Algorithmen ungewöhnliche Muster erkannt, die sich auf die Leistung der Anwendung auswirken. Mit der proaktiven Erkennung werden die von Application Insights erfassten Leistungstelemetriedaten automatisch analysiert. Sie erhalten eine E-Mail zu jeder in der Anwendung erkannten anormalen Leistung. Sie müssen keine Regeln für Schwellenwerte festlegen. Die Benachrichtigungen der proaktiven Erkennung sind in die Analysefunktionen von Application Insights integriert, sodass eine schnelle Eingrenzung und Diagnose der Probleme ermöglicht wird.

Die proaktive Erkennung befindet sich in der Vorschauphase und ist noch nicht für alle Application Insights-Benutzer verfügbar. Wenn Sie sie ausprobieren möchten, wenden Sie sich an AppInsightsML@microsoft.com. Wir werden sie dann in Zusammenarbeit mit Ihnen einrichten.

## Informationen zur proaktiven Warnung

* *Warum habe ich diese E-Mail erhalten?*
 * Die von Ihrer Anwendung an Application Insights gesendeten Telemetriedaten wurden mit der proaktiven Erkennung analysiert. Dabei wurde ein Leistungsproblem in der Anwendung erkannt.
* *Bedeutet die Benachrichtigung, dass ich definitiv ein Problem habe?*
 * Nein. Es handelt sich lediglich um Vorschlag über etwas, das Sie sich möglicherweise genauer ansehen möchten. 
* *Wie sollte ich vorgehen?*
 * [Sehen Sie sich die dargestellten Daten an](#responding-to-an-alert). Verwenden Sie den Metrik-Explorer, um die Leistung im Zeitverlauf zu überprüfen und zusätzliche Metriken anzuzeigen. Verwenden Sie die Suche, um bestimmte Ereignisse herauszufiltern, mit denen Sie die Ursache ermitteln können. 
* *Meine Daten werden also angesehen?*
 * Nein. Der Dienst ist vollständig automatisch. Nur Sie erhalten die Benachrichtigungen. Ihre Daten sind [privat](app-insights-data-retention-privacy.md).


## Der Erkennungsvorgang

* *Welche Arten von Anomalien werden erkannt?*
 * Muster, deren Überprüfung für Sie selbst zeitaufwendig wäre Beispiel: schlechte Leistung bei einer bestimmten Kombination aus Ort, Uhrzeit und Plattform
* *Werden alle der von Application Insights erfassten Daten analysiert?*
 * Derzeit nicht. Gegenwärtig werden Antwortzeiten auf Anforderungen und Seitenladezeiten analysiert. Die Analyse weiterer Metriken wird in Kürze zur Verfügung stehen. 
* *Kann ich meinen eigenen Erkennungsregeln für Anomalien erstellen?*
 * Bisher nicht. Sie können aber Folgendes tun:
 * [Einrichten von Warnungen](app-insights-alerts.md), die Sie informieren, wenn eine Metrik einen Schwellenwert überschreitet.
 * [Exportieren von Telemetriedaten](app-insights-export-telemetry.md) in eine [Datenbank](app-insights-code-sample-export-sql-stream-analytics.md) oder [in Power BI](app-insights-export-power-bi.md) oder [andere](app-insights-code-sample-export-telemetry-sql-database.md) Tools, mit denen Sie selbst eine Analyse durchführen können.
* *Wie oft wird die Analyse ausgeführt?*
 * Die Analyse der Telemetriedaten vom Vortag wird täglich ausgeführt.
* **Ersetzt dies also die [Metrikwarnungen](app-insights-alerts.md)?
 * Nein. Wir können nicht dafür sorgen, dass jedes Verhalten erkannt wird, das Sie als ungewöhnlich erachten könnten.

## Untersuchen von Problemen, die sich bei der proaktiven Erkennung ergeben haben

Öffnen Sie den Anomaliebericht entweder über die E-Mail oder die Anomalienliste.

![](./media/app-insights-anomaly/03.png)


* **Wann** gibt die Zeit an, zu der das Problem erkannt wurde.
* **Was** beschreibt Folgendes:
 * Das erkannte Problem
 * Die Merkmale der gefundenen Gruppe von Ereignissen, die das problematische Verhalten aufwiesen
* In der Tabelle wird diese Gruppe mit beeinträchtigter Leistung mit dem durchschnittlichen Verhalten aller anderen Ereignisse verglichen.

Klicken Sie auf die Links, um den Metrik-Explorer und die Suche für die relevanten Berichte zu öffnen, die nach Zeit und Eigenschaften der Gruppe mit beeinträchtigter Leistung gefiltert sind.

Ändern Sie den Zeitraum und die Filter zum Durchsuchen der Telemetrie.

## Wie kann ich die Leistung verbessern?

Langsame und fehlgeschlagene Antworten sind für Websitebenutzer eines der größten Ärgernisse, was Sie sicherlich aus eigener Erfahrung bestätigen können. Daher ist es wichtig, diese Probleme zu beheben.

### Eingrenzung

Erstens: Ist es wirklich ein Problem? Wenn eine Seite immer langsam geladen wird, aber nur 1 % der Benutzer Ihrer Website diese Seite anzeigen müssen, gibt es vielleicht wichtigere Dinge, mit denen Sie sich beschäftigen müssen. Falls nur 1 % der Benutzer darauf zugreifen, aber jedes Mal Ausnahmen ausgelöst werden, kann es erforderlich sein, dies zu untersuchen.

Verwenden Sie die Aussage zur Auswirkung in der E-Mail als allgemeinen Anhaltspunkt, aber seien Sie sich bewusst, dass damit nicht alles abgedeckt wird. Sammeln Sie zur Bestätigung weitere Beweise.

Sehen Sie sich die Parameter des Problems an. Falls eine geografische Abhängigkeit besteht, sollten Sie [Verfügbarkeitstests](app-insights-monitor-web-app-availability.md) für diese Region einrichten. Möglicherweise liegen in diesem Umfeld lediglich Netzwerkprobleme vor.

### Diagnostizieren des langsamen Ladens von Seiten 

Wo liegt das Problem? Reagiert der Server langsam, ist die Seite sehr lang oder muss der Browser viel Aufwand betreiben, um die Seite anzuzeigen?

Öffnen Sie das Blatt mit den Browsermetriken. Die [segmentierte Anzeige der Browser-Seitenladezeit](app-insights-javascript.md#explore-your-data) gibt an, wo Zeit verbraucht wird.

* Wenn die **Zeit für das Senden von Anforderungen** hoch ist, reagiert entweder der Server langsam, oder die Anforderung umfasst eine größere Datenmenge. Sehen Sie sich die [Leistungsmetriken](app-insights-web-monitor-performance.md#metrics) an, um die Reaktionszeiten zu prüfen. 
* Richten Sie die [Abhängigkeitsüberwachung](app-insights-dependencies.md) ein, um ermitteln zu können, ob die Langsamkeit mit externen Diensten oder Ihrer Datenbank zusammenhängt.
* Wenn die **Zeit für das Empfangen von Antworten** vorherrscht, sind Ihre Seite und die abhängigen Teile lang, z. B. JavaScript, CSS, Bilder usw. (jedoch nicht asynchron geladene Daten). Richten Sie einen [Verfügbarkeitstest](app-insights-monitor-web-app-availability.md) ein, und legen Sie die Option zum Laden abhängiger Teile fest. Wenn Sie einige Ergebnisse erhalten, öffnen Sie die Details eines Ergebnisses und erweitern diese, um die Ladezeiten für unterschiedliche Dateien anzuzeigen.
* Eine hohe **Clientverarbeitungszeit** deutet darauf hin, dass die Ausführung von Skripts lange dauert. Falls die Ursache nicht offensichtlich ist, können Sie erwägen, Zeitsteuerungscode hinzuzufügen und die Zeiten über trackMetric-Aufrufe zu senden.

### Verbessern langsamer Seiten

Da Sie im Web viele Ratschläge zur Verbesserung der Serverreaktionszeit und Seitenladezeiten finden, soll dies hier nicht alles wiederholt werden. Sie erhalten aber einige Tipps, die Sie vielleicht bereits kennen, aber die hier noch einmal in Erinnerung gerufen werden sollen:

* Langsames Laden aufgrund von großen Dateien: Laden Sie die Skripts und anderen Teile asynchron. Verwenden Sie die Skriptbündelung. Unterteilen Sie die Hauptseite in Widgets, für die die Daten separat geladen werden. Kein Senden von reinen „alten“ HTML-Daten für lange Tabellen: Verwenden Sie ein Skript, um die Daten als JSON oder in einem anderen kompakten Format anzufordern, und füllen Sie anschließend die Tabelle mit Daten. Es gibt gute Frameworks, die hierfür hilfreich sind. (Die natürlich mit großen Skripts verbunden sind.)
* Langsame Serverabhängigkeiten: Sehen Sie sich die geografischen Standorte der Komponenten an. Stellen Sie bei Verwendung von Azure beispielsweise sicher, dass sich der Webserver und die Datenbank in derselben Region befinden. Werden bei Abfragen mehr Informationen als nötig abgerufen? Würde das Zwischenspeichern oder eine Batchverarbeitung helfen?
* Kapazitätsprobleme: Sehen Sie sich die Servermetriken von Reaktionszeiten und die Anforderungsanzahl an. Wenn für die Antwortzeiten unverhältnismäßige Spitzen gegenüber den Spitzen bei der Anzahl der Anforderungen zu beobachten sind, ist die Wahrscheinlichkeit hoch, dass Ihre Server überlastet sind. 


## E-Mail-Benachrichtigungen

* *Muss ich diesen Dienst abonnieren, um Benachrichtigungen zu erhalten?*
 * Nein. Unser Bot überprüft in regelmäßigen Abständen die Daten aller Application Insights-Benutzer und sendet Benachrichtigungen, wenn Probleme erkannt werden.
* *Kann ich das Abonnement abbestellen oder die Benachrichtigungen stattdessen an meine Kollegen senden lassen?*
 * Klicken Sie auf den Link zum Abbestellen des Abonnements in der Warnung oder in der E-Mail. 
 
    Derzeit werden Sie an Personen gesendet, die über [Schreibzugriff für die Application Insights-Ressource](app-insights-resources-roles-access-control.md) verfügen.
* *Ich möchte nicht mit diesen Nachrichten überflutet werden.*
 * Sie erhalten maximal eine Nachricht am Tag. Sie erhalten Nachrichten nicht mehrfach.
* *Erhalte ich eine Erinnerung, wenn ich nichts unternehme?*
 * Nein, Sie erhalten jeweils nur eine Nachricht zu einem Problem.
* *Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*
 * Klicken Sie in der Application Insights-Übersicht Ihrer App auf die Kachel **Proaktive Erkennung**. 


## Verwandte Artikel

* [Erkennung, Eingrenzung und Diagnose](app-insights-detect-triage-diagnose.md)
* [Einrichten von Metrikwarnungen](app-insights-alerts.md)
* [Metrik-Explorer](app-insights-metrics-explorer.md)
* [Suchexplorer](app-insights-diagnostic-search.md)
 

<!---HONumber=AcomDC_1125_2015-->