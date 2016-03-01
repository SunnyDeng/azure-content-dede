<properties 
	pageTitle="Proaktive NRT-Diagnose in Application Insights" 
	description="Die proaktive NRT-Diagnose benachrichtigt Sie automatisch, wenn die Serverantwortzeit ein ungewöhnliches Verhalten zeigt. Es ist keine Konfiguration erforderlich." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# Proaktive Diagnose nahezu in Echtzeit (Near Real Time, NRT)

*Diese Funktion befindet sich im frühen Versuchsstadium.*

*Bitte senden Sie Feedback an:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

[Visual Studio Application Insights](app-insights-overview.md) benachrichtigt Sie automatisch nahezu in Echtzeit, wenn die Rate fehlerhafter Anforderungen in Ihrer Web-App sprunghaft ansteigt. Um Sie bei der Selektierung und Diagnose des Problems zu unterstützen, wird in der Benachrichtigung eine Analyse der Merkmale der Anforderungsfehler und der verknüpften Telemetriedaten angegeben. Außerdem werden Links zum Application Insights-Portal zur weiteren Diagnose bereitgestellt. Dieses Feature muss nicht eingerichtet oder konfiguriert werden, da es Machine Learning-Algorithmen verwendet, um die normale Fehlerrate als Baseline zu bestimmen. Eine bestimmte Mindestmenge von Datenverkehr ist nötig, damit das Feature funktioniert.

Hier sehen Sie eine Beispielwarnung:

![Beispiel für eine intelligente Warnung mit Clusteranalyse im Umfeld des Fehlers](./media/app-insights-nrt-proactive-diagnostics/010.png)

Um Fehlerwarnungen zu erhalten, müssen Sie [Application Insights für Ihre ASP.NET-](app-insights-asp-net.md) oder [Java Web-App](app-insights-java-get-started.md) einrichten.

## So funktioniert's

Bei der proaktiven NRT-Diagnose (Near Real Time) werden die von Ihrer App erhaltenen Telemetriedaten und insbesondere die Rate von Anforderungsfehlern überwacht. Diese Metrik gibt in der Regel die Anzahl von HTTP-Anforderungen an, die einen Antwortcode von mindestens 400 zurückgegeben haben (es sei denn, Sie haben benutzerdefinierten Code zum [Filtern](app-insights-api-filtering-sampling.md#filtering) oder Generieren Ihrer eigenen [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)-Aufrufe geschrieben).

Basierend auf dem vorherigen Verhalten dieser Metrik sagt der Dienst voraus, welcher Wertebereich zu erwarten ist. Wenn der tatsächliche Wert deutlich höher ist, wird eine Warnung generiert.

Beim Auslösen einer Warnung führt der Dienst eine Clusteranalyse für mehrere Dimensionen der Anforderungen aus, um ein Muster von Werten zu ermitteln, durch die sich die Fehler charakterisieren lassen. Im obigen Beispiel wurde durch die Analyse ermittelt, dass die meisten Fehler zu einem bestimmten Anforderungsnamen gehören. Es wurde aber festgestellt, dass die Fehler unabhängig von der Host- oder Serverinstanz auftreten.

Die Analyse ermittelt dann Ausnahmen und Abhängigkeitsfehler, die Anforderungen im identifizierten Cluster zuzuordnen sind, sowie ein Beispiel für Ablaufverfolgungsprotokolle, die diesen Anforderungen zugeordnet sind.

Das Analyseergebnis wird Ihnen als E-Mail gesendet, sofern Sie nichts anderes konfiguriert haben.

Wie bei [manuell festgelegten Warnungen](app-insights-alerts.md) können Sie den Status der Warnung prüfen und die Warnung auf dem Blatt „Warnungen“ in Ihrer Application Insights-Ressource konfigurieren. Im Gegensatz zu anderen Warnungen müssen Sie die adaptive Fehlerwarnung jedoch nicht einrichten oder konfigurieren. Wenn Sie möchten, können Sie sie deaktivieren oder die Ziel-E-Mail-Adressen ändern.

## Bei Erhalt einer Warnung

Eine Warnung gibt an, dass bei der Rate der Anforderungsfehler eine Anomalie erkannt wurde. Wahrscheinlich liegt ein Problem mit Ihrer App oder deren Umgebung vor. Möglicherweise funktioniert die soeben hochgeladene neue Version nicht richtig; oder vielleicht weist eine Abhängigkeit, z. B. eine Datenbank oder eine externe Ressource, Funktionsfehler auf.

Anhand des Prozentsatzes der Anforderungen und der Anzahl der betroffenen Benutzer können Sie entscheiden, wie dringend das Problem ist.

In vielen Fällen können Sie das Problem über den Anforderungsnamen, Ausnahmen, Abhängigkeiten und andere Angaben schnell diagnostizieren.

Wenn Sie jedoch eine genauere Untersuchung durchführen müssen, gelangen Sie über die Links in den einzelnen Abschnitten direkt zu einer [Suchseite](app-insights-diagnostic-search.md), die auf die entsprechenden Anforderungen bzw. die Ausnahme, Abhängigkeit oder Ablaufverfolgung gefiltert ist. Alternativ können Sie das [Azure-Portal](https://portal.azure.com) öffnen, zur Application Insights-Ressource für Ihre App wechseln und das Blatt „Fehler“ öffnen.

## Überprüfen aktueller Warnungen

Um Warnungen im Portal zu überprüfen, öffnen Sie **Einstellungen > Operative Ereignisse**.

![Zusammenfassung von Warnungen](./media/app-insights-nrt-proactive-diagnostics/040.png)

Klicken Sie auf eine Warnung, um vollständige Details anzuzeigen.


## Konfigurieren von Warnungen 

> **Die UX-Konfiguration ist noch nicht verfügbar.**
> 
> Senden Sie stattdessen eine E-Mail an [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).
>
> So könnte dies funktionieren:

Öffnen Sie die Seite „Warnungen“. Die adaptive Fehlerwarnung wird zusammen mit allen Warnungen aufgeführt, die Sie eventuell manuell festgelegt haben, und Sie können sehen, ob sie sich derzeit im Status „Warnung“ befindet.

![Klicken Sie auf der Seite „Übersicht“ auf die Kachel „Warnungen“. Klicken Sie alternativ dazu auf einer beliebigen Seite „Metriken“ auf die Schaltfläche „Warnungen“.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Klicken Sie auf die Warnung, um sie zu konfigurieren.

![Konfiguration](./media/app-insights-nrt-proactive-diagnostics/030.png)

Beachten Sie, dass Sie die adaptive Fehlerwarnung deaktivieren, aber nicht löschen (oder eine neue erstellen) können.


## Wo liegt der Unterschied?

Die proaktive NRT-Diagnose ergänzt andere ähnliche, aber doch verschiedene Features von Application Insights.

* [Metrikwarnungen](app-insights-alerts.md) werden von Ihnen festgelegt und können eine Vielzahl von Metriken überwachen, z. B. die CPU-Belegung, Anforderungsraten, Seitenladezeiten usw. Sie können sie z. B. einsetzen, um rechtzeitig benachrichtigt zu werden, wenn weitere Ressourcen hinzugefügt werden müssen. Im Gegensatz dazu deckt die proaktive NRT-Diagnose einen kleinen Bereich kritischer Metriken ab (zurzeit nur die Rate von Anforderungsfehlern), durch die Sie nahezu in Echtzeit benachrichtigt werden, sobald die Rate der Anforderungsfehler für Ihre Web-App im Vergleich zum normalen Verhalten der Web-App drastisch ansteigt.
* Die [proaktive Erkennung](app-insights-proactive-detection.md) verwendet zudem Maschinenintelligenz, um ungewöhnliche Muster in Ihren Metriken zu ermitteln, und muss nicht von Ihnen konfiguriert werden. Im Gegensatz zur proaktiven NRT-Diagnose besteht der Zweck der proaktiven Erkennung jedoch darin, Segmente in Ihrem Nutzungsangebot zu ermitteln, die (beispielsweise durch bestimmte Seiten in einem bestimmten Browsertyp) nicht zufriedenstellend verarbeitet werden. Die Analyse wird täglich ausgeführt, und falls ein Ergebnis gefunden wird, ist dies wahrscheinlich wesentlich weniger dringend als eine Warnung. Im Gegensatz dazu wird die Analyse für die proaktive NRT-Diagnose an eingehenden Telemetriedaten kontinuierlich ausgeführt, und Sie werden innerhalb von Minuten benachrichtigt, wenn die Serverfehlerraten höher als erwartet ausfallen.


## Geben Sie uns Feedback!

*Diese Funktion befindet sich im frühen Versuchsstadium. Wir sind sehr an Ihrem Feedback interessiert. Bitte senden Sie Feedback an:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com), *oder klicken Sie in der Fehlermeldung auf den Feedbacklink.*

<!---HONumber=AcomDC_0218_2016-->