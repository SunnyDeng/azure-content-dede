<properties 
	pageTitle="Erkennen und Diagnostizieren von Abstürzen in Windows Store- und Windows Phone-Apps mit Application Insights" 
	description="Analysieren von Leistungsproblemen in Ihrer Windows-Geräte-App mit Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

# Erkennen und Diagnostizieren von Abstürzen in Windows Store- und Windows Phone-Apps mit Application Insights

*Application Insights befindet sich in der Vorschau.*

Wenn Ihre App bei den Benutzern abstürzt, möchten Sie davon schnell erfahren und wünschen sich Details dazu, was passiert ist. Mit Application Insights können Sie überwachen, wie häufig Abstürze auftreten, Benachrichtigungen erhalten, wenn sie auftreten, und die Berichte zu den einzelnen Vorfällen untersuchen.

"Absturz" bedeutet, dass die Anwendung aufgrund einer nicht abgefangenen Ausnahme beendet wird. Wenn Ihre Anwendung eine Ausnahme abfängt, können Sie dies mithilfe der "TrackException\(\)"-API melden, jedoch die Ausführung fortsetzen. In diesem Fall wird sie nicht wie ein Absturz protokolliert.


## Überwachen der Häufigkeit von Abstürzen

Falls noch nicht geschehen, fügen Sie [Application Insights Ihrem App-Projekt hinzu][windows] und veröffentlichen es erneut.

Abstürze werden im [Application Insights-Portal][portal] auf dem Blatt "Übersicht" angezeigt.

![](./media/appinsights/appinsights-d018-oview.png)

Sie können den Zeitbereich bearbeiten, der vom Diagramm angezeigt wird.


## Festlegen einer Warnung beim Erkennen von Abstürzen

![Klicken Sie im Absturzdiagramm auf "Warnregeln" und dann auf "Warnung hinzufügen"](./media/appinsights/appinsights-d023-alert.png)

## Diagnostizieren von Abstürzen

Um herauszufinden, ob einige Versionen Ihrer App häufiger abstürzen als andere, klicken Sie sich durch das Absturzdiagramm und führen eine Segmentierung nach Anwendungsversion durch:

![](./media/appinsights/appinsights-d26crashSegment.png)


Öffnen Sie zum Ermitteln der Ausnahmen, die Abstürze verursachen, die Diagnosesuche. Es empfiehlt sich, andere Arten von Telemetriedaten zu entfernen, um sich auf die Ausnahmen zu konzentrieren:

![](./media/appinsights/appinsights-d26crashExceptions.png)

[Weitere Informationen zum Filtern bei der Diagnosesuche][diagnostic].
 

Klicken Sie auf eine Ausnahme, um ihre Details einzublenden, einschließlich der zugehörigen Eigenschaften und Stapelüberwachung.

![](./media/appinsights/appinsights-d26crash.png)

Sehen Sie sich die anderen Ausnahmen und Ereignisse an, die in der Nähe dieser Ausnahme aufgetreten sind:


![](./media/appinsights/appinsights-d26crashRelated.png)

## Einfügen von Ablaufverfolgungsprotokollen und Ereignissen

Um Probleme zu diagnostizieren, können Sie [Ablaufverfolgungsaufrufe einfügen und die Protokolle in Application Insights durchsuchen][diagnostic].

## <a name="debug"></a>Debug- im Vergleich zum Freigabemodus

#### Debuggen

Wenn Sie den Build im Debug-Modus erstellen, werden Ereignisse gesendet, sobald sie generiert werden. Wenn Sie die Internetverbindung verlieren und dann die Anwendung beenden, ehe die Verbindung wiederhergestellt ist, werden Offlinetelemetriedaten verworfen.

#### Freigabe

Wenn Sie den Build im Freigabemodus erstellen, werden Ereignisse auf dem Gerät gespeichert und gesendet, wenn die Anwendung fortgesetzt wird. Daten werden auch bei der ersten Verwendung der Anwendung gesendet. Wenn es beim Start keine Internetverbindung gibt, werden die vorherigen Telemetriedaten und auch die Telemetrie für den aktuellen Lebenszyklus gespeichert und beim nächsten Fortsetzen der Anwendung gesendet.

## <a name="next"></a>Nächste Schritte

[Erkennung, Eingrenzung und Diagnose von Problemen mit Application Insights][detect]

[Erfassen von Diagnoseprotokollen][trace]

[Problembehandlung](app-insights-windows-troubleshoot.md)




<!--Link references-->

[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54-->