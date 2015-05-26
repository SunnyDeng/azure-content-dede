<properties 
	pageTitle="Application Insights für Windows-Desktop-Apps" 
	description="Analysieren Sie die Nutzung und Leistung von Windows-Anwendungen mit Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="awills"/>

# Application Insights für Windows-Desktop-Apps

*Application Insights befindet sich in der Vorschau.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Mit Application Insights können Sie die Nutzung und Leistung Ihrer bereitgestellten Anwendung überwachen:

*Obwohl das Application Insights SDK für eine Desktop-App zum Einsatz kommen kann, ist dies ein Szenario, das wir derzeit nicht unterstützen. Aber wenn Sie damit experimentieren möchten, hier nun einige Tipps.*




## <a name="add"></a> Erstellen einer Application Insights-Ressource


1.  Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie als Anwendungstyp "ASP.NET-App" oder "Windows Store-App" aus. 

    ![Klicken Sie auf "Neu \> Application Insights"](./media/app-insights-windows-get-started/01-new.png)

    \(Die Auswahl des Anwendungstyps bestimmt den Inhalt des Blatts "Übersicht" und die im [Metrik-Explorer][metrics] verfügbaren Eigenschaften.\)

2.  Erstellen Sie eine Kopie des Instrumentationsschlüssels.

    ![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-windows-get-started/02-props.png)

## <a name="sdk"></a>Installieren des SDK in Ihrer Anwendung


1. Bearbeiten Sie die NuGet-Pakete Ihres Desktop-App-Projekts in Visual Studio.![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-windows-get-started/03-nuget.png)

2. Installieren Sie den Kern des Application Insights SDK.

    ![Wählen Sie **Online**, **Vorabversion einschließen** aus, und suchen Sie nach "Application Insights"](./media/app-insights-windows-get-started/04-ai-nuget.png)

    \(Alternativ können Sie das Application Insights SDK für Webanwendungen auswählen. Dieses bietet einige integrierte Leistungsindikatoren für Telemetrie.\)

3. Bearbeiten Sie die Datei "ApplicationInsights.config" \(die bei der NuGet-Installation hinzugefügt wurde\). Fügen Sie Folgendes direkt vor dem Endtag ein:

    &lt;InstrumentationKey&gt;\*den kopierten Schlüssel\*&lt;/InstrumentationKey&gt;

    Alternativ können Sie denselben Effekt mit folgendem Code erzielen:
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

4. Wenn Sie das Web-App-SDK installiert haben, sollten Sie auch die webbasierten Telemetriemodule aus "ApplicationInsights.config" auskommentieren.

## <a name="telemetry"></a>Einfügen von Telemetrieaufrufen

Erstellen Sie eine `TelemetryClient`-Instanz und [nutzen Sie sie anschließend, um Telemetriedaten zu senden][track].

Verwenden Sie `TelemetryClient.Flush` zum Senden von Nachrichten vor dem Schließen der App. \(Dies wird nicht für andere Arten von Apps empfohlen.\)

Beispielsweise können Sie in einer Windows Forms-Anwendung Folgendes schreiben:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps
            }
            base.OnClosing(e);
        }

```

Verwenden Sie eine der [Application Insights-APIs][track], um Telemetriedaten zu senden. In Windows-Desktopanwendungen werden Telemetriedaten nicht automatisch gesendet. In der Regel würden Sie Folgendes verwenden:

* "TrackPageView\(pageName\)" für umschaltbare Formulare, Seiten oder Registerkarten
* "TrackEvent\(eventName\)" für andere Benutzeraktionen
* "TrackTrace\(logEvent\)" für die [Diagnoseprotokollierung][diagnostic]
* "TrackException\(exception\)" in Catch-Klauseln
* "TrackMetric\(Name, Wert\)" bei einer Hintergrundaufgabe, um Berichte zu Metriken, die nicht bestimmten Ereignissen zugeordnet sind, regelmäßig zu senden.

Um die Anzahl von Benutzern und Sitzungen anzuzeigen, legen Sie einen Initialisiererkontext fest:

    class TelemetryInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = DateTime.Now.ToFileTime().ToString();
            context.Session.IsNewSession = true;
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new TelemetryInitializer());
            ...



## <a name="run"></a>Ausführen des Projekts

[Führen Sie die Anwendung durch Drücken von F5 aus](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx), und verwenden Sie sie, um einige Telemetriedaten zu generieren.

In Visual Studio sehen Sie eine Anzahl der gesendeten Ereignisse.

![](./media/appinsights/appinsights-09eventcount.png)



## <a name="monitor"></a>Anzeigen der Überwachungsdaten

Kehren Sie zum Blatt Ihrer Anwendung im Azure-Portal zurück.

Die ersten Ereignisse werden in der Diagnosesuche angezeigt.

Klicken Sie nach einigen Sekunden auf "Aktualisieren", wenn Sie mehr Daten erwarten.

Wenn Sie "TrackMetric" oder den Parameter "measurements" von "TrackEvent" verwendet haben, öffnen Sie im [Metrik-Explorer][metrics] das Blatt "Filter", auf dem die Metriken angezeigt werden.



## <a name="usage"></a>Nächste Schritte

[Nachverfolgen der Nutzung Ihrer App][track]

[Diagnoseprotokolle anlegen und durchsuchen][diagnostic]

[Problembehandlung][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->