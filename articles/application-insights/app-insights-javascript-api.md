<properties 
	pageTitle="Application Insights-SDK – JavaScript-API" 
	description="Referenzen" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights-SDK – JavaScript-API

Das JavaScript-SDK wird in Ihre Webseite geladen, wenn Sie die [Nachverfolgung der Webseite](app-insights-javascript.md) in [Application Insights](https://azure.microsoft.com/services/application-insights/) einrichten.

[Übersicht über die API und Beispiele](app-insights-api-custom-events-metrics.md)

## AppInsights-Klasse

Mit dieser SDK-Klasse werden die Telemetriedaten an Application Insights gesendet.

Auf einer Webseite, auf der Sie [die Nachverfolgung der Webseite eingerichtet haben](app-insights-javascript.md), können Sie die Instanz `appInsights` verwenden. Beispiel:
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Protokolliert, dass eine Seite oder ein ähnlicher Container für den Benutzer angezeigt wurde.

 | | 
---|---|---
`name` | `? string` | Der Name, der zum Identifizieren der Seite im Portal verwendet wird. Der Standardwert ist der Titel des Dokuments.
`url` | `? string` | Eine relative oder absolute URL, mit der die Seite oder ein ähnliches Element identifiziert wird. Der Standardwert ist die Position des Fensters.
`properties` | `? {[string]:string}` | Zusätzliche Daten, die zum Filtern von Seiten und Metriken im Portal verwendet werden. Der Standardwert ist leer.
`measurements` | `? {[string]:number}` | Metriken im Zusammenhang mit dieser Seite, die im Metrik-Explorer im Portal angezeigt werden. Der Standardwert ist leer.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

Protokolliert eine Benutzeraktion oder ein anderes Vorkommen.

Im Portal können Sie Ereignisse anhand des Namens auswählen und [Diagramme anzeigen, die sie zählen oder zugehörige Messungen enthalten](app-insights-metrics-explorer.md).

Sie können auch nach [einzelnen Ereignissen suchen und sie anzeigen](app-insights-diagnostic-search.md).

 | | 
---|---|---
 `name` | `string` | Identifiziert das Ereignis. Ereignisse mit demselben Namen werden gezählt und können im [Metrik-Explorer](app-insights-metrics-explorer.md) dargestellt werden.
`properties` | `? {[string]:string}` | Zusätzliche Daten, die zum Filtern von Seiten und Metriken im Portal verwendet werden. Der Standardwert ist leer.
`measurements` | `? {[string]:number}` | Metriken im Zusammenhang mit dieser Seite, die im Metrik-Explorer im Portal angezeigt werden. Der Standardwert ist leer.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


Protokolliert einen numerischen Wert, der keinem bestimmten Ereignis zugeordnet ist. Wird normalerweise verwendet, um regelmäßige Berichte über Leistungsindikatoren zu senden.

Im Portal können Sie Metriken anhand des Namens auswählen, um [ihre Werte im Zeitverlauf darzustellen](app-insights-metrics-explorer.md). Einzelne trackMetric-Aufrufe können Sie nicht suchen oder anzeigen.

Verwenden Sie nur die ersten beiden Parameter, um eine Messung zu senden. Wenn Sie sehr häufig Messungen vornehmen, können Sie die Telemetriebandbreite reduzieren, indem Sie mehrere Messungen aggregieren und den resultierenden Durchschnitt in Intervallen senden.

 | | 
---|---|---
`name` | `string` | Eine Zeichenfolge, die die Metrik identifiziert. Im Portal können Sie Metriken für die Anzeige nach Namen auswählen.
`average` | ` number` | Eine Messung oder der Durchschnitt von verschiedene Messungen.
`sampleCount` | `? number` | Die Anzahl der Messungen, die der Durchschnitt dargestellt. Der Standardwert lautet 1.
`min` | `? number` | Die kleinste Messung in der Stichprobe. Der Standardwert ist der Durchschnitt.
`max` | `? number` | Die größte Messung in der Stichprobe. Der Standardwert ist der Durchschnitt.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

Protokolliert eine Ausnahme, die Sie abgefangen haben. (Vom Browser abgefangene Ausnahmen werden ebenfalls protokolliert.)

Im Portal können Sie [Ausnahmetypen suchen und](app-insights-diagnostic-search.md) den Typ, die Meldung und die Stapelüberwachung einzelner Instanzen anzeigen.

 | | 
---|---|---
`exception` | `Error` | Ein Fehler einer Catch-Klausel.  
`handledAt` | `? string` | Der Standardwert ist "unhandled".
`properties` | `? {[string]:string}` | Zusätzliche Daten, die zum Filtern von Seiten und Metriken im Portal verwendet werden. Der Standardwert ist leer.
`measurements` | `? {[string]:number}` | Metriken im Zusammenhang mit dieser Seite, die im Metrik-Explorer im Portal angezeigt werden. Der Standardwert ist leer.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

Protokolliert ein Diagnoseereignis wie das Aufrufen oder Verlassen einer Methode.

Im Portal können Sie den Meldungsinhalt durchsuchen und [einzelne trackTrace-Ereignisse anzeigen](app-insights-diagnostic-search.md). (Im Gegensatz zu `trackEvent` können Sie im Portal nicht nach dem Meldungsinhalt filtern.)

 | | 
---|---|---
`message` | `string` | Diagnosedaten. Kann erheblich länger als ein Name sein.

### flush

    flush()

Sendet alle Telemetriedaten in der Warteschlange sofort.

Verwenden Sie diese Option beim Schließen des Fensters.


### config

    config: IConfig

Werte, die steuern, wie die Telemetriedaten gesendet werden.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Legen Sie diese Werte im [Codeausschnitt](app-insights-javascript-api.md) fest, den Sie in Ihre Webseiten einfügen. Suchen Sie nach dieser Zeile, und fügen Sie weitere Elemente hinzu:

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

Informationen über das Gerät, den Standort und den Benutzer, die das SDK aus der Umgebung extrahiert.


## TelemetryContext-Klasse




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## Open Source-Code

Lesen Sie den [Code für das SDK](https://github.com/Microsoft/ApplicationInsights-js), und ergänzen Sie ihn.

<!---HONumber=July15_HO4-->