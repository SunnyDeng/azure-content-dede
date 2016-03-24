<properties 
	pageTitle="Proaktive NRT-Diagnose in Application Insights" 
	description="Die proaktive NRT-Diagnose benachrichtigt Sie automatisch, wenn die Serverantwortzeit ein ungewöhnliches Verhalten zeigt. Es ist keine Konfiguration erforderlich." 
	services="application-insights" 
    documentationCenter=""
	authors="yorac" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2016" 
	ms.author="awills"/>
 
# Proaktive Diagnose nahezu in Echtzeit (Near Real Time, NRT)

[Visual Studio Application Insights](app-insights-overview.md) benachrichtigt Sie automatisch nahezu in Echtzeit, wenn die Rate fehlerhafter Anforderungen in Ihrer Web-App sprunghaft ansteigt. Um Sie bei der Selektierung und Diagnose des Problems zu unterstützen, wird in der Benachrichtigung eine Analyse der Merkmale der Anforderungsfehler und der verknüpften Telemetriedaten angegeben. Außerdem werden Links zum Application Insights-Portal zur weiteren Diagnose bereitgestellt. Dieses Feature muss nicht eingerichtet oder konfiguriert werden, da es Machine Learning-Algorithmen verwendet, um die normale Fehlerrate als Baseline zu bestimmen. Eine bestimmte Mindestmenge von Datenverkehr ist nötig, damit das Feature funktioniert.

Dieses Feature funktioniert mit Java- und ASP.NET-Web-Apps, die in der Cloud oder auf Ihren eigenen Servern gehostet werden. Es funktioniert auch mit allen Apps, die Daten zur Anforderungstelemetrie generieren, z. B. mit einer Workerrolle, mit der [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request) aufgerufen wird.

Die proaktive Diagnose wird eingeschaltet, sobald Sie [Application Insights für Ihr Projekt](app-insights-get-started.md) einrichten, sofern Ihre App eine bestimmte Mindestmenge an Telemetriedaten generiert.

Hier sehen Sie eine Beispielwarnung:

![Beispiel für eine intelligente Warnung mit Clusteranalyse im Umfeld des Fehlers](./media/app-insights-nrt-proactive-diagnostics/010.png)

Sie enthält folgende Angaben:

* Anzahl der betroffenen Benutzer – so wissen Sie, wie ernst die Lage ist.
* Ein charakteristisches Muster für die Fehler. In diesem Beispiel handelt es sich um einen bestimmten Anforderungsnamen (angeforderte URL). So wissen Sie sofort, wo im Code Sie suchen müssen. Weitere Möglichkeiten sind bestimmte Browser oder Clientgeräte.
* Ausnahmen, Protokollablaufverfolgungen und Fehler in Abhängigkeiten (Datenbanken oder andere externe Komponenten), die mit den Anforderungsfehlern verknüpft zu sein scheinen.
* Direkte Links zu relevanten Suchvorgängen in den Telemetriedaten in Application Insights.

Normale [Metrikwarnungen](app-insights-alerts.md) informieren Sie, dass möglicherweise ein Problem vorliegt. Mit der proaktiven NRT-Diagnose wird dagegen die Diagnose für Sie gestartet und ein großer Teil der Analyse durchgeführt, die Sie ansonsten selbst durchzuführen hätten. Sie erhalten die Ergebnisse fein säuberlich verpackt und können so schnell zur Ursache des Problems vordringen.

## So funktioniert's

Bei der proaktiven NRT-Diagnose (Near Real Time) werden die von Ihrer App erhaltenen Telemetriedaten und insbesondere die Rate von Anforderungsfehlern überwacht. Diese Metrik gibt in der Regel die Anzahl von HTTP-Anforderungen an, die einen Antwortcode von mindestens 400 zurückgegeben haben (es sei denn, Sie haben benutzerdefinierten Code zum [Filtern](app-insights-api-filtering-sampling.md#filtering) oder Generieren Ihrer eigenen [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)-Aufrufe geschrieben).

Die Leistung Ihrer App weist ein typisches Verhaltensmuster auf. Einige Anforderungen sind anfälliger für Fehler als andere, und die gesamte Fehlerrate kann mit zunehmender Auslastung steigen. Bei der proaktiven NRT-Diagnose werden diese Korrelationen mithilfe von maschinellem Lernen ermittelt.

Wenn Telemetriedaten aus Ihrer Web-App in Application Insights eingehen, vergleicht die proaktive NRT-Diagnose das aktuelle Verhalten mit den Mustern der letzten Tage. Wenn die Fehlerrate im Vergleich zur Erwartung aus früheren Leistungen erheblich ansteigt, wird eine Warnung ausgelöst.

Beim Auslösen einer Warnung führt der Dienst eine Clusteranalyse für mehrere Dimensionen der Anforderungen aus, um ein Muster von Werten zu ermitteln, durch die sich die Fehler charakterisieren lassen. Im obigen Beispiel wurde durch die Analyse ermittelt, dass die meisten Fehler zu einem bestimmten Anforderungsnamen gehören. Es wurde aber festgestellt, dass die Fehler unabhängig von der Host- oder Serverinstanz auftreten.

Die Analyse ermittelt dann Ausnahmen und Abhängigkeitsfehler, die Anforderungen im identifizierten Cluster zuzuordnen sind, sowie ein Beispiel für Ablaufverfolgungsprotokolle, die diesen Anforderungen zugeordnet sind.

Das Analyseergebnis wird Ihnen als E-Mail gesendet, sofern Sie nichts anderes konfiguriert haben.

Wie bei [manuell festgelegten Warnungen](app-insights-alerts.md) können Sie den Status der Warnung prüfen und die Warnung auf dem Blatt „Warnungen“ in Ihrer Application Insights-Ressource konfigurieren. Im Gegensatz zu anderen Warnungen müssen Sie die proaktive NRT-Diagnose jedoch nicht einrichten oder konfigurieren. Wenn Sie möchten, können Sie sie deaktivieren oder die Ziel-E-Mail-Adressen ändern.

## Selektierung und Diagnose einer Warnung

Eine Warnung gibt an, dass bei der Rate der Anforderungsfehler eine Anomalie erkannt wurde. Wahrscheinlich liegt ein Problem mit Ihrer App oder deren Umgebung vor.

Anhand des Prozentsatzes der Anforderungen und der Anzahl der betroffenen Benutzer können Sie entscheiden, wie dringend das Problem ist. Im Beispiel oben steht die Fehlerrate von 15 % einer normalen Fehlerrate von 1,3 % gegenüber, sodass ganz offensichtlich Probleme vorliegen. Bei 22 verschiedenen Benutzern sind bei einem bestimmten Vorgang Fehler aufgetreten. Wenn es sich dabei um Ihre App handeln würde, könnten Sie beurteilen, wie schwerwiegend die Fehler sind.

In vielen Fällen können Sie das Problem über den Anforderungsnamen, Ausnahmen, Abhängigkeiten und Ablaufverfolgungsdaten schnell diagnostizieren.

Es gibt einige andere Anhaltspunkte. Beispielsweise entspricht die Abhängigkeitsfehlerrate in diesem Beispiel der Ausnahmerate (89,3 %). Dies legt nahe, dass die Ausnahme sich direkt aus dem Abhängigkeitsfehler ergibt, sodass Sie genau wissen, wo im Code Sie suchen müssen.

Zur weiteren Untersuchung gelangen Sie über die Links in den einzelnen Abschnitten direkt zu einer [Suchseite](app-insights-diagnostic-search.md), die auf die entsprechenden Anforderungen bzw. die Ausnahme, Abhängigkeit oder Ablaufverfolgung gefiltert ist. Alternativ können Sie das [Azure-Portal](https://portal.azure.com) öffnen, zur Application Insights-Ressource für Ihre App wechseln und das Blatt „Fehler“ öffnen.

In diesem Beispiel wird durch Klicken auf den Suchlink das Application Insights-Blatt „Suchen“ für die SQL-Anweisung mit der Ursache geöffnet: In Pflichtfeldern wurden NULL-Werte angegeben, und die Überprüfung während des Speichervorgangs ist negativ ausgefallen.


![Diagnosesuche](./media/app-insights-nrt-proactive-diagnostics/050.png)

## Überprüfen aktueller Warnungen

Um Warnungen im Portal zu überprüfen, öffnen Sie **Einstellungen > Überwachungsprotokolle**.

![Zusammenfassung von Warnungen](./media/app-insights-nrt-proactive-diagnostics/040.png)

Klicken Sie auf eine Warnung, um vollständige Details anzuzeigen.


## Konfigurieren von Warnungen 

Öffnen Sie die Seite „Warnungen“. Die adaptive Fehlerwarnung wird zusammen mit allen Warnungen aufgeführt, die Sie eventuell manuell festgelegt haben, und Sie können sehen, ob sie sich derzeit im Status „Warnung“ befindet.

![Klicken Sie auf der Seite „Übersicht“ auf die Kachel „Warnungen“. Klicken Sie alternativ dazu auf einer beliebigen Seite „Metriken“ auf die Schaltfläche „Warnungen“.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Klicken Sie auf die Warnung, um sie zu konfigurieren.

![Konfiguration](./media/app-insights-nrt-proactive-diagnostics/030.png)

Beachten Sie, dass Sie die adaptive Fehlerwarnung deaktivieren, aber nicht löschen (oder eine neue erstellen) können.


## Wo liegt der Unterschied?

Die proaktive NRT-Diagnose ergänzt andere ähnliche, aber doch verschiedene Features von Application Insights.

* [Metrikwarnungen](app-insights-alerts.md) werden von Ihnen festgelegt und können eine Vielzahl von Metriken überwachen, z. B. die CPU-Belegung, Anforderungsraten, Seitenladezeiten usw. Sie können sie z. B. einsetzen, um rechtzeitig benachrichtigt zu werden, wenn weitere Ressourcen hinzugefügt werden müssen. Im Gegensatz dazu deckt die proaktive NRT-Diagnose einen kleinen Bereich kritischer Metriken ab (zurzeit nur die Rate von Anforderungsfehlern), durch die Sie nahezu in Echtzeit benachrichtigt werden, sobald die Rate der Anforderungsfehler für Ihre Web-App im Vergleich zum normalen Verhalten der Web-App drastisch ansteigt.

    Bei der proaktiven NRT-Diagnose wird der Schwellenwert automatisch an die aktuellen Bedingungen angepasst.

    Mit der proaktiven NRT-Diagnose wird die Diagnose für Sie gestartet. 
* Die [proaktive Erkennung](app-insights-proactive-detection.md) verwendet zudem Maschinenintelligenz, um ungewöhnliche Muster in Ihren Metriken zu ermitteln, und muss nicht von Ihnen konfiguriert werden. Im Gegensatz zur proaktiven NRT-Diagnose besteht der Zweck der proaktiven Erkennung jedoch darin, Segmente in Ihrem Nutzungsangebot zu ermitteln, die (beispielsweise durch bestimmte Seiten in einem bestimmten Browsertyp) nicht zufriedenstellend verarbeitet werden. Die Analyse wird täglich ausgeführt, und falls ein Ergebnis gefunden wird, ist dies wahrscheinlich wesentlich weniger dringend als eine Warnung. Im Gegensatz dazu wird die Analyse für die proaktive NRT-Diagnose an eingehenden Telemetriedaten kontinuierlich ausgeführt, und Sie werden innerhalb von Minuten benachrichtigt, wenn die Serverfehlerraten höher als erwartet ausfallen.

## Bei Erhalt einer Warnung durch die proaktive NRT-Diagnose

*Warum habe ich diese Warnung erhalten?*

*	Im Vergleich zu den normalen Grundwerten des vorhergehenden Zeitraums wurde ein anomaler Anstieg der Anforderungsfehler festgestellt. Nach der Analyse der Fehler und zugeordneten Telemetriedaten ist davon auszugehen, dass ein Problem vorliegt, das Sie untersuchen sollten. 

*Bedeutet die Benachrichtigung, dass ich definitiv ein Problem habe?*

*	Wir versuchen, bei Störungen oder Beeinträchtigungen der App eine Warnung zu senden. Nur Sie können jedoch die Semantik oder die Auswirkungen auf die App oder Benutzer vollständig einschätzen.

*Meine Daten werden also angesehen?*

*	Nein. Der Dienst ist vollständig automatisch. Nur Sie erhalten die Benachrichtigungen. Ihre Daten sind [privat](app-insights-data-retention-privacy.md).

*Muss ich diese Benachrichtigung abonnieren?*

*	Nein. Diese Warnungsregel ist für jede Anwendung festgelegt, die Anforderungstelemetriedaten sendet.

*Kann ich das Abonnement abbestellen oder die Benachrichtigungen stattdessen an meine Kollegen senden lassen?*

*	Ja, klicken Sie unter „Warnungsregeln“ auf die Regel für die proaktive NRT-Diagnose, um sie zu konfigurieren. Sie können die Warnung deaktivieren oder die Empfänger für die Warnung ändern. 

*Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*

*	In den Überwachungsprotokollen. Klicken Sie auf „Einstellungen“, auf „Überwachungsprotokolle“ und dann auf eine Warnung, um die vollständigen Details anzuzeigen.

*Einige Warnungen betreffen bekannte Probleme. Diese Warnungen möchte ich nicht erhalten.*

*	Wir arbeiten an einer Funktion zur Warnungsunterdrückung.


## Geben Sie uns Feedback!

*Wir sind sehr an Ihrer Meinung interessiert. Bitte senden Sie Ihr Feedback an:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).

<!---HONumber=AcomDC_0309_2016-->