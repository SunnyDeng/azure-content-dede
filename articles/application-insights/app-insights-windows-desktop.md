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
	ms.date="09/07/2015" 
	ms.author="awills"/>

# Application Insights für Windows-Desktop-Apps, -Dienste und -Workerrollen

*Application Insights befindet sich in der Vorschau.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Mit Application Insights können Sie die Nutzung und Leistung Ihrer bereitgestellten Anwendung überwachen:

Alle Windows-Anwendungen, einschließlich Desktop-Apps, Hintergrunddienste und Workerrollen, können das Application Insights Core SDK zum Senden von Telemetriedaten an Application Insights verwenden. Sie können das Applications Insights-SDK auch einem Klassenbibliotheksprojekt hinzufügen.

Das Core SDK stellt nur eine API bereit: Im Gegensatz zu den Web- oder Geräte-SDKs enthält es keine Module, die automatisch Daten erfassen. Daher müssen Sie Code schreiben, um Ihre eigenen Telemetriedaten zu senden. Einige der weiteren Pakete, z. B. die Leistungsindikatorerfassung, funktioniert auch in einer Desktop-App.


## <a name="add"></a> Erstellen einer Application Insights-Ressource


1.  Erstellen Sie im [Azure-Portal][portal] eine neue Application Insights-Ressource. Wählen Sie als Anwendungstyp "Windows Store-App" aus. 

    ![Klicken Sie auf "Neu > Application Insights".](./media/app-insights-windows-desktop/01-new.png)

    (Die Auswahl des Anwendungstyps bestimmt den Inhalt des Blatts "Übersicht" und die im [Metrik-Explorer][metrics] verfügbaren Eigenschaften.)

2.  Erstellen Sie eine Kopie des Instrumentationsschlüssels.

    ![Klicken Sie auf "Eigenschaften", wählen Sie den Schlüssel aus, und drücken Sie STRG+C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Installieren des SDK in Ihrer Anwendung


1. Bearbeiten Sie die NuGet-Pakete Ihres Desktop-App-Projekts in Visual Studio.

    ![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten".](./media/app-insights-windows-desktop/03-nuget.png)

2. Installieren Sie das Application Insights-Core-API-Paket: Microsoft.ApplicationInsights.

    ![Suchen Sie nach "Application Insights".](./media/app-insights-windows-desktop/04-core-nuget.png)

    *Kann ich andere Pakete verwenden?*

    Ja, Sie können andere Pakete wie beispielsweise die Pakete für Leistungsindikator- oder Abhängigkeitserfassung installieren, wenn Sie deren Module verwenden möchten. "Microsoft.ApplicationInsights.Web" umfasst verschiedene solcher Pakete. Wenn Sie [Pakete für Protokoll- oder Ablaufverfolgungserfassung](app-insights-asp-net-trace-logs.md) verwenden möchten, beginnen Sie mit dem Webserverpaket.

    (Verwenden Sie jedoch nicht "Microsoft.ApplicationInsights.Windows", dieses ist für Windows Store-Apps vorgesehen.)

3. Legen Sie Ihren "InstrumentationKey" fest.

    * Wenn Sie nur das Core-API-Paket "Microsoft.ApplicationInsights" installiert haben, müssen Sie den Schlüssel im Code festlegen, beispielsweise in "main()": 

     `TelemetryConfiguration.Active.InstrumentationKey = "`*Ihr Schlüssel*`";`

    * Wenn Sie eines der anderen Pakete installiert haben, können Sie den Schlüssel entweder im Code oder in "ApplicationInsights.config" festlegen:
 
     `<InstrumentationKey>`*Ihr Schlüssel*`</InstrumentationKey>`



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

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

Verwenden Sie eine der [Application Insights-APIs][api], um Telemetriedaten zu senden. In Windows-Desktopanwendungen werden Telemetriedaten nicht automatisch gesendet. In der Regel würden Sie Folgendes verwenden:

* `TrackPageView(pageName)` für wechselnde Formulare, Seiten oder Registerkarten
* `TrackEvent(eventName)` für andere Benutzeraktionen
* `TrackMetric(name, value)` in einer Hintergrundaufgabe, um reguläre Berichte über Metriken zu senden, die nicht an bestimmte Ereignisse gekoppelt sind.
* `TrackTrace(logEvent)` für die [Diagnoseprotokollierung][diagnostic]
* `TrackException(exception)` in Catch-Klauseln
* Um sicherzustellen, dass alle Telemetriedaten vor dem Schließen der App gesendet werden, verwenden Sie `Flush()`. Verwenden Sie diese nur, wenn Sie lediglich die Core-API verwenden (Microsoft.ApplicationInsights). Die Web- und Geräte-SDKs implementieren dieses Verhalten automatisch. (Wenn Ihre Apps in einem Kontext ausgeführt werden, in dem nicht immer eine Internetverbindung verfügbar ist, siehe auch [Persistenzkanal](#persistence-channel).)


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

Die ersten Ereignisse werden in der [Diagnosesuche](app-insights-diagnostic-search.md) angezeigt.

Klicken Sie nach einigen Sekunden auf "Aktualisieren", wenn Sie mehr Daten erwarten.

Wenn Sie "TrackMetric" oder den Parameter "measurements" von "TrackEvent" verwendet haben, öffnen Sie im [Metrik-Explorer][metrics] das Blatt "Filter". Dort sollten Ihre Metriken angezeigt werden. Gelegentlich kann es jedoch etwas dauern, bis die Anzeige aktuell ist. Schließen Sie ggf. das Blatt "Filter", warten Sie einen Moment, und aktualisieren Sie die Anzeige.



## Persistenzkanal 

Wenn Ihre Apps in einem Kontext ausgeführt werden, in dem nicht immer eine Internetverbindung oder nur eine langsame Internetverbindung verfügbar ist, erwägen Sie die Verwendung eines Persistenzkanals anstelle des standardmäßigen In-Memory-Kanals.

Der standardmäßige In-Memory-Kanal verliert alle Telemetriedaten, die nicht vor dem Schließen der App gesendet wurden. Wenngleich Sie mithilfe von `Flush()` versuchen können, alle im Puffer verbleibenden Daten zu senden, gehen dennoch Daten verloren, wenn keine Internetverbindung besteht oder wenn die App heruntergefahren wird, bevor die Übertragung abgeschlossen ist.

Im Gegensatz dazu puffert der Persistenzkanal Telemetriedaten in einer Datei, bevor diese an das Portal gesendet werden. `Flush()` stellt sicher, dass Daten in der Datei gespeichert werden. Wenn beim Schließen der App keine Daten gesendet werden, verbleiben diese in der Datei. Wenn die App erneut gestartet wird, werden die Daten gesendet, sofern eine Internetverbindung besteht. Daten werden so lange in der Datei gesammelt, bis eine Internetverbindung verfügbar ist.

### Verwenden des Persistenzkanals

1. Importieren Sie das NuGet-Paket [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel).
2. Fügen Sie diesen Code an geeigneter Stelle für die Initialisierung in Ihre App ein:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Verwenden Sie `telemetryClient.Flush()`, bevor Ihre App geschlossen wird. So stellen Sie sicher, dass die Daten entweder an das Portal gesendet oder in der Datei gespeichert werden.

    Beachten Sie, dass Flush() synchron für den Persistenzkanal, aber asynchron für andere Kanäle ist.

 
Der Persistenzkanal ist für Geräteszenarien optimiert, in denen die Anwendung relativ wenige Ereignisse generiert und die Internetverbindung häufig unzuverlässig ist. Dieser Kanal schreibt Ereignisse zunächst in zuverlässigen Speicher auf den Datenträger zurück und versucht dann, die Daten zu senden.

#### Beispiel

Angenommen, Sie möchten Ausnahmefehler überwachen. Sie abonnieren das `UnhandledException`-Ereignis. Im Rückruf schließen Sie einen Flush-Befehl ein und stellen so sicher, dass die Telemetriedaten dauerhaft gespeichert werden.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Wenn die App geschlossen wird, sehen Sie eine Datei in `%LocalAppData%\Microsoft\ApplicationInsights`, die die komprimierten Ereignisse enthält.
 
Beim nächsten Start der Anwendung prüft der Kanal diese Datei und sendet die Telemetriedaten, sofern möglich, an Application Insights.

#### Testbeispiel

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Den Code für den Persistenzkanal finden Sie auf [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/master/src/TelemetryChannels/PersistenceChannel).


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
 

<!---HONumber=Oct15_HO1-->