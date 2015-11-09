<properties 
	pageTitle="Stichprobenerstellung für Telemetriedaten in Application Insights" 
	description="So behalten Sie die Kontrolle über die Menge an erfassten Telemetriedaten." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="awills"/>

#  Erstellen von Stichproben in Application Insights

*Application Insights befindet sich in der Vorschau.*


Das Erstellen von Stichproben ist eine Option in Application Insights, mit der Sie einen eingeschränkten Satz an Telemetriedaten erfassen und speichern können, während gleichzeitig eine statistisch korrekte Analyse der Anwendungsdaten sichergestellt wird. Sie nutzen diese Option typischerweise, um den Datenverkehr zu verringern und eine [Drosselung](app-insights-pricing.md#data-rate) zu vermeiden. Die Daten werden so gefiltert, dass verwandte Elemente übertragen werden und Sie zu Diagnosezwecken eine kleinere Datenmenge untersuchen können. Zum Filtern der verwandten Elemente werden Client- und Serverseite automatisch koordiniert. Bei der Anzeige der Metrikergebnisse im Portal werden diese erneut normalisiert, um der Stichprobenerstellung Rechnung zu tragen und Auswirkungen auf die Statistiken zu minimieren.


Für das Erstellen von Stichproben ist gegenwärtig die Betaversion verfügbar. Für die Zukunft ist eine Überarbeitung dieser Version geplant.

## Konfigurieren des Erstellens von Stichproben für Ihre Anwendung

Das Erstellen von Stichproben ist gegenwärtig für das ASP.NET SDK oder [beliebige Webseiten](#other-web-pages) verfügbar.

### ASP.NET-Server

1. Aktualisieren Sie die NuGet-Pakete Ihres Projekts auf die neueste *Vorabversion* von Application Insights. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, wählen Sie „Manage NuGet Packages“ aus, aktivieren Sie **Include prerelease**, und suchen Sie nach „Microsoft.ApplicationInsights.Web“. 

2. Fügen Sie in „ApplicationInsights.config“ diesen Codeausschnitt hinzu

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

> [AZURE.NOTE]Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist). Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

<a name="other-web-pages"></a>
### Webseiten mit JavaScript

Sie können Webseiten für die Stichprobenerstellung auf einem beliebigen Server konfigurieren. Für ASP.NET-Server muss sowohl die Client- als auch die Serverseite konfiguriert werden.

Ändern Sie beim [Konfigurieren der Webseiten für Application Insights](app-insights-javascript.md) den Ausschnitt, den Sie im Application Insights-Portal erhalten haben. (In ASP.NET befindet sich dieser Ausschnitt in „\_Layout.cshtml“.) Fügen Sie vor dem Instrumentationsschlüssel eine Zeile wie `samplingPercentage: 10,` ein:

    <script>
	var appInsights= ... 
	}({ 

	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Geben Sie in JavaScript denselben Prozentsatz für die Stichprobenerstellung an wie auf der Serverseite.

[Weitere Informationen zur API](app-insights-api-custom-events-metrics.md)


### Alternative: Stichprobenerstellung in Servercode festlegen


Anstatt den Stichprobenerstellungsparameter in der config-Datei festzulegen, können Sie Code verwenden. Dadurch können Sie die Stichprobenerstellung ein- oder ausschalten.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // It's recommended to set SamplingPercentage in the .config file instead.

    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```


## Wann sollten Sie die Stichprobenerstellung nutzen?

Für die meisten kleinen und mittelgroßen Anwendungen ist keine Stichprobenerstellung erforderlich. Die nützlichsten Diagnoseinformationen und präzisesten Statistiken werden abgerufen, indem Sie Daten zu sämtlichen Benutzeraktivitäten erfassen.

 
Nachfolgend sind die Hauptgründe für die Verwendung der Stichprobenerstellung aufgeführt:


* Der Application Insights-Dienst senkt („drosselt“) Datenpunkte, wenn Ihre App in kurzen Abständen eine große Menge an Telemetriedaten sendet. 
* Das [Kontingent](app-insights-pricing.md) für Datenpunkte Ihres Tarifs soll nicht überschritten werden. 
* Zum Verringern des Netzwerkdatenverkehrs, der durch die Erfassung von Telemetriedaten verursacht wird. 

## Wie funktioniert die Stichprobenerstellung?

Aus Anwendungssicht ist die Erstellung von Stichproben ein Feature des Application Insights SDK. Sie geben an, welcher Prozentsatz aller Datenpunkte an den Application Insights-Dienst gesendet werden soll. Ab Version 2.0.0 des Application Insights SDK können Sie den Prozentsatz für die Stichprobenerstellung über Ihren Code steuern. (In zukünftigen Versionen des SDK wird es auch möglich sein, den Prozentsatz für die Stichprobenerstellung über die Datei „ApplicationInsights.config“ zu konfigurieren.)

Das SDK entscheidet, welche Telemetrieelemente verworfen und welche beibehalten werden. Diese Entscheidung im Hinblick auf die Stichprobenerstellung wird basierend auf verschiedenen Regeln getroffen, mit denen sichergestellt wird, dass alle zusammenhängenden Datenpunkte intakt bleiben. Dadurch sind die in Application Insights durchgeführten Diagnosen auch bei einer geringeren Datenmenge zuverlässig und ermöglichen es Ihnen, die erforderlichen Maßnahmen zu ergreifen. Wenn Ihre App beispielsweise zusätzliche Telemetrieelemente für eine Anforderung sendet, bei der ein Fehler aufgetreten ist (z. B. Ausnahmen und Ablaufverfolgungen, die für diese Anforderung protokolliert wurden), werden diese Anforderung und andere Telemetriedaten bei der Stichprobenerstellung nicht getrennt. Die Daten werden entweder vollständig beibehalten oder vollständig verworfen. Wenn Sie die Anforderungsdetails in Application Insights anzeigen, wird die Anforderung folglich immer mit den zugehörigen Telemetrieelementen angezeigt.

Bei Anwendungen, in denen Benutzer definiert sind (also die meisten typischen Webanwendungen), basiert die Entscheidung im Zusammenhang mit der Stichprobenerstellung auf dem Hash der Benutzer-ID. Das heißt, dass die Telemetriedaten für einen bestimmten Benutzer entweder vollständig beibehalten oder vollständig verworfen werden. Bei Anwendungen, in denen keine Benutzer definiert sind (z. B. Webdienste), basiert die Entscheidung bezüglich der Stichprobenerstellung auf der Vorgangs-ID der Anforderung. Für Telemetrieelemente, für die weder ein Benutzer noch eine Vorgangs-ID festgelegt wurde (z. B.Telemetrieelemente aus asynchronen Threads ohne HTTP-Kontext), wird bei der Stichprobenerstellung ein Prozentsatz von Telemetrieelementen der einzelnen Typen erfasst.

Wenn Ihnen die Telemetriedaten angezeigt werden, passt der Application Insights-Dienst die Metriken basierend auf dem Prozentsatz der Stichprobenerstellung an, der bei der Erfassung der Daten verwendet wurde. Auf diese Weise werden die fehlenden Datenpunkte kompensiert. Bei Betrachtung der Telemetriedaten in Application Insights werden folglich statistisch korrekte Annäherungen angezeigt, die den reellen Zahlen sehr nahe kommen.

Die Genauigkeit der Annäherung hängt weitgehend vom konfigurierten Prozentsatz für die Stichprobenerstellung ab. Zudem steigt die Genauigkeit bei Anwendungen, die eine große Anzahl von im Allgemeinen ähnlichen Anforderungen von einer großen Anzahl von Benutzern verarbeiten. Andererseits ist bei Anwendungen mit geringer Last keine Stichprobenerstellung erforderlich, da diese Anwendungen üblicherweise sämtliche Telemetriedaten senden können, ohne dass dabei das jeweilige Kontingent überschritten wird oder es durch die Drosselung zu Datenverlust kommt.

Beachten Sie, dass Application Insights keine Stichproben für die Telemetrietypen „Metriken“ und „Sitzungen“ erstellt, da bei diesen Typen eine Reduzierung der Genauigkeit nicht wünschenswert ist.

## Stichprobenerstellung und das JavaScript SDK

Das clientseitige (JavaScript) SDK führt die Stichprobenerstellung in Verbindung mit dem serverseitigen SDK aus. Die instrumentierten Seiten senden clientseitige Telemetriedaten lediglich von den Benutzern, für die serverseitig die Entscheidung zur Stichprobenerstellung getroffen wurde. Mithilfe dieser Logik wird die Integrität der Benutzersitzung auf Client- und Serverseite sichergestellt. So können Sie für ein bestimmtes Telemetrieelement in Application Insights nach allen anderen Telemetrieelementen für den jeweiligen Benutzer oder die jeweilige Sitzung suchen.

*Meine client- und serverseitigen Telemetriedaten weisen keine koordinierten Stichproben auf.*

* Überprüfen Sie, ob Sie die Stichprobenerstellung sowohl auf dem Server als auch auf dem Client aktiviert haben.
* Stellen Sie sicher, dass Sie die SDK-Version 2.0 oder höher verwenden.
* Stellen Sie sicher, dass Sie auf Client und Server denselben Prozentsatz für die Stichprobenerstellung festgelegt haben.


## Häufig gestellte Fragen 

*Weshalb erfolgt die Stichprobenerstellung nicht einfach nach dem Prinzip „X Prozent jedes Telemetrietyps erfassen“?*

 *  Wenngleich dieser Ansatz an die Stichprobenerstellung eine hohe Genauigkeit bei metrischen Annäherungen bietet, bestünde in diesem Fall keine Möglichkeit, Diagnosedaten für einzelne Benutzer, Sitzungen und Anforderungen in Zusammenhang zu bringen – ein äußerst wichtiger Aspekt bei der Durchführung von Diagnosen. Aus diesem Grund sind „alle Telemetrieelemente für X Prozent der App-Benutzer“ oder „alle Telemetriedaten für X Prozent der App-Anforderungen“ besser geeignete Ansätze an die Stichprobenerstellung. Für Telemetrieelemente, die nicht mit den Anforderungen verknüpft sind (z. B. die asychrone Hintergrundverarbeitung), wird auf den Ansatz „X Prozent aller Elemente für jeden Telemetrietyp erfassen“ zurückgegriffen. 

*Kann der Prozentsatz für die Stichprobenerstellung im Verlauf der Zeit geändert werden?*

 * Bei der heutigen Implementierung würden Sie den Prozentsatz für die Stichprobenerstellung üblicherweise nicht ändern, nachdem Sie ihn beim Anwendungsstart festgelegt haben. Auch wenn Sie den Prozentsatz für die Stichprobenerstellung steuern können, lässt sich nicht ermitteln, bei welchem Prozentsatz die ideale Menge an Daten erfasst wird, bevor der Datenverkehr gedrosselt wird oder das monatliche Datenkontingent erschöpft ist. Zukünftige Versionen des Application Insights SDK werden eine anpassbare Stichprobenerstellung bieten, bei welcher der Prozentsatz für die Stichprobenerstellung basierend auf der aktuellen Menge an Telemetriedaten und anderen Faktoren dynamisch angepasst wird. 

*Wie kann ich den idealen Prozentsatz für die Stichprobenerstellung für meine App ermitteln?*

* Aktuell können Sie diesbezüglich lediglich Vermutungen anstellen. Analysieren Sie Ihre aktuelle Nutzung der Telemetriedaten in Application Insights, beobachten Sie die verworfenen Daten im Zusammenhang mit der Drosselung, und schätzen Sie die Menge der erfassten Telemetriedaten. In Kombination mit dem ausgewählten Tarif geben diese drei Faktoren Auskunft darüber, um wie viel Sie die Menge der erfassten Telemetriedaten gegebenenfalls reduzieren sollten. Durch eine Änderung der Menge an Telemetriedaten kann ein optimal konfigurierter Prozentsatz für die Stichprobenerfassung jedoch unwirksam werden (z. B. durch die Steigerung der Benutzeranzahl). Sobald die anpassbare Stichprobenerstellung implementiert ist, wird basierend auf der beobachten Menge an Telemetriedaten automatisch der ideale Prozentsatz für die Stichprobenerstellung gewählt.

*Was geschieht, wenn ich einen zu geringen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Ein deutlich zu geringer Prozentsatz für die Stichprobenerstellung (übermäßige Stichprobenerstellung) wirkt sich negativ auf die Genauigkeit der Annäherungen aus, wenn Application Insights versucht, die Visualisierung der Daten für die reduzierte Datenmenge auszugleichen. Auch kann das Diagnoseergebnis beeinträchtigt sein, da einige der langsamen Anforderungen oder Anforderungen, bei denen es selten zu einem Fehler kommt, möglicherweise nicht erfasst werden.

*Was geschieht, wenn ich einen zu hohen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Wenn Sie einen zu hohen Prozentsatz für die Stichprobenerstellung konfigurieren (keine ausreichende Stichprobenerstellung), führt dies zu einer nicht ausreichenden Reduzierung der Menge an erfassten Telemetriedaten. Trotzdem kann es im Zusammenhang mit der Drosselung zum Verlust von Telemetriedaten kommen, und die Kosten für die Nutzung von Application Insights können die geplanten Kosten aufgrund von Überschreitungsgebühren übersteigen.

*Auf welchen Plattformen kann ich die Stichprobenerstellung verwenden?*

* Die Stichprobenerstellung ist gegenwärtig für beliebige Webseiten sowie für die Client- und die Serverseite von .NET-Webanwendungen verfügbar.

*Kann ich die Stichprobenerstellung mit Geräte-Apps verwenden (Windows Phone-, iOS-, Android- oder Desktop-Apps)?*

* Nein. Für Geräteanwendungen wird die Stichprobenerstellung gegenwärtig nicht unterstützt. 

<!---HONumber=Nov15_HO1-->