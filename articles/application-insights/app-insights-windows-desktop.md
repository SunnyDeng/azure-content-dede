<properties 
	pageTitle="Application Insights für Windows-Desktop-Apps und -Dienste" 
	description="Analysieren Sie die Nutzung und Leistung Ihrer Windows-Desktop-Apps mit Application Insights." 
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
	ms.date="08/19/2015" 
	ms.author="awills"/>

# Application Insights für Windows-Desktop-Apps, -Dienste und -Workerrollen

*Application Insights befindet sich in der Vorschau.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Mit Application Insights können Sie die Nutzung und Leistung Ihrer bereitgestellten Anwendung überwachen:

Alle Windows-Anwendungen, einschließlich Desktop-Apps, Hintergrunddienste und Workerrollen, können das Application Insights Core SDK zum Senden von Telemetriedaten an Application Insights verwenden. Das Core SDK stellt nur eine API bereit: Im Gegensatz zu den Web- oder Geräte-SDKs enthält es keine Module, die automatisch Daten erfassen. Daher müssen Sie Code schreiben, um Ihre eigenen Telemetriedaten zu senden.


## <a name="add"></a> Erstellen einer Application Insights-Ressource


1.  Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie als Anwendungstyp "Windows Store-App" aus. 

    ![Klicken Sie auf "Neu > Application Insights".](./media/app-insights-windows-desktop/01-new.png)

    (Die Auswahl des Anwendungstyps bestimmt den Inhalt des Blatts "Übersicht" und die im [Metrik-Explorer][metrics] verfügbaren Eigenschaften.)

2.  Erstellen Sie eine Kopie des Instrumentationsschlüssels.

    ![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Installieren des SDK in Ihrer Anwendung


1. Bearbeiten Sie die NuGet-Pakete Ihres Desktop-App-Projekts in Visual Studio.

    ![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-windows-desktop/03-nuget.png)

2. Installieren Sie das Application Insights Core API-Paket.

    ![Suchen Sie nach "Application Insights".](./media/app-insights-windows-desktop/04-core-nuget.png)

    Sie können andere Pakete wie beispielsweise das Leistungsindikator- oder das Protokollerfassungspaket installieren, wenn Sie ihre Funktionen verwenden möchten.

3. Legen Sie das "InstrumentationKey"-Element im Code fest, z. B. in "main()".

    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

*Weshalb gibt es die Datei "ApplicationInsights.config" nicht?*

* Die CONFIG-Datei wird vom Core-API-Paket nicht installiert, das nur verwendet wird, um Telemetriedatensammler zu konfigurieren. Sie müssen eigenen Code schreiben, um den Instrumentationsschlüssel festzulegen und Telemetriedaten zu senden.
* Wenn Sie eines der anderen Pakete installiert haben, besitzen Sie eine CONFIG-Datei. Sie können den Instrumentationsschlüssel dort einfügen, anstatt ihn im Code festzulegen.

*Kann ich ein anderes NuGet-Paket verwenden?*

* Ja, Sie können das Webserverpaket (Microsoft.ApplicationInsights.Web) verwenden, das Sammler für eine Vielzahl von Sammlungsmodulen wie z. B. Leistungsindikatoren installiert. Er würde eine CONFIG-Datei installieren, in der Sie den Instrumentationsschlüssel ablegen würden. Verwenden Sie [ApplicationInsights.config, um nicht gewünschte Module zu deaktivieren](app-insights-configuration-with-applicationinsights-config.md), etwa den HTTP-Anforderungssammler. 
* Wenn Sie das [Protokoll- oder Ablaufverfolgungssammler-Paket](app-insights-asp-net-trace-logs.md) verwenden möchten, beginnen Sie mit dem Webserverpaket. 

## <a name="telemetry"></a>Einfügen von Telemetrieaufrufen

Erstellen Sie eine `TelemetryClient`-Instanz und [nutzen Sie sie anschließend, um Telemetriedaten zu senden][api].


Beispielsweise können Sie in einer Windows Forms-Anwendung Folgendes schreiben:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
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

Verwenden Sie eine der [Application Insights-APIs][api], um Telemetriedaten zu senden. In Windows-Desktopanwendungen werden Telemetriedaten nicht automatisch gesendet. In der Regel würden Sie Folgendes verwenden:

* "TrackPageView(pageName)" für umschaltbare Formulare, Seiten oder Registerkarten
* "TrackEvent(eventName)" für andere Benutzeraktionen
* "TrackMetric(Name, Wert)" bei einer Hintergrundaufgabe, um Berichte zu Metriken, die nicht bestimmten Ereignissen zugeordnet sind, regelmäßig zu senden.
* "TrackTrace(logEvent)" für die [Diagnoseprotokollierung][diagnostic]
* "TrackException(exception)" in Catch-Klauseln


Um sicherzustellen, dass alle Telemetriedaten vor dem Schließen der App gesendet werden, verwenden Sie `TelemetryClient.Flush()`. In der Regel werden die Telemetriedaten zusammengefasst und in regelmäßigen Abständen gesendet. (Das Löschen wird nur empfohlen, wenn Sie lediglich die Haupt-API verwenden. Die Web- und Geräte-SDKs implementieren dieses Verhalten automatisch.)


#### Kontextinitialisierer

Um die Anzahl von Benutzern und Sitzungen anzuzeigen, können Sie die Werte für jede `TelemetryClient`-Instanz festlegen. Alternativ dazu können Sie einen Kontextinitialisierer verwenden, um dies für alle Clients hinzuzufügen:

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Ausführen des Projekts

[Führen Sie die Anwendung durch Drücken von F5 aus](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx), und verwenden Sie sie, um einige Telemetriedaten zu generieren.

In Visual Studio sehen Sie eine Anzahl der gesendeten Ereignisse.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Anzeigen der Überwachungsdaten

Kehren Sie zum Blatt Ihrer Anwendung im Azure-Portal zurück.

Die ersten Ereignisse werden in der Diagnosesuche angezeigt.

Klicken Sie nach einigen Sekunden auf "Aktualisieren", wenn Sie mehr Daten erwarten.

Wenn Sie "TrackMetric" oder den Parameter "measurements" von "TrackEvent" verwendet haben, öffnen Sie im [Metrik-Explorer][metrics] das Blatt "Filter", auf dem die Metriken angezeigt werden.



## <a name="usage"></a>Nächste Schritte

[Nachverfolgen der Nutzung Ihrer App][knowUsers]

[Diagnoseprotokolle anlegen und durchsuchen][diagnostic]

[Problembehandlung][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=August15_HO8-->