<properties 
	pageTitle="Application Insights-Datenmodell" 
	description="Beschreibt die Eigenschaften, die aus dem fortlaufenden Export in JSON exportiert und als Filter verwendet wurden." 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Application Insights-Exportdatenmodell

In dieser Tabelle sind die Eigenschaften der Telemetriedaten aufgelistet, die von [Application Insights](app-insights-overview.md) SDKs an das Portal gesendet werden. Sie sehen diese Eigenschaften in der Datenausgabe von [Fortlaufender Export](app-insights-export-telemetry.md). Sie werden darüber hinaus in den Eigenschaftsfiltern im [Metrik-Explorer](app-insights-metrics-explorer.md) und in der [Diagnosesuche](app-insights-diagnostic-search.md) angezeigt.

Beachten Sie Folgendes:

* `[0]` in diesen Tabellen bezeichnet Punkte im Pfad, an denen Sie einen Index einfügen müssen; es handelt sich jedoch nicht immer um 0.
* Zeitspannen werden als Zehntel einer Mikrosekunde angegeben, also 10000000 == 1 Sekunde.
* Datums- und Zeitangaben sind als UTC und im ISO-Format angegeben: `yyyy-MM-DDThh:mm:ss.sssZ`

Es gibt mehrere [Beispiele](app-insights-export-telemetry.md#code-samples), die ihre Verwendung veranschaulichen.



## Beispiel

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // Always 1
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## Kontext

Alle Telemetriedatentypen umfassen einen Kontextabschnitt. Nicht alle dieser Felder werden mit jedem Datenpunkt übertragen.



|Pfad|Typ|Hinweise|
|---|---|---|
| context.custom.dimensions [0] | object [ ] | Schlüssel-Wert-Paare, die über benutzerdefinierte Eigenschaftenparameter festgelegt werden. Maximale Länge des Schlüssels: 100, maximale Länge des Werts: 1024. Mehr als 100 eindeutige Werte, die Eigenschaft kann durchsucht, aber nicht zur Segmentierung verwendet werden. Maximal 200 Schlüssel pro „ikey“. |
| context.custom.metrics [0] | object [ ] | Schlüssel-Wert-Paare, die über benutzerdefinierte Messparameter und über TrackMetrics festgelegt werden. Maximale Länge des Schlüssels: 100, Werte können numerisch sein. |
| context.data.eventTime | string | UTC |
| context.data.isSynthetic | boolean | Anforderung scheint von einem Bot oder Webtest zu stammen. |
| context.data.samplingRate | number | Prozentsatz der vom SDK generierten Telemetriedaten, die an das Portal gesendet werden. Bereich liegt zwischen 0.0 und 100.0.|
| context.device | object | Clientgerät |
| context.device.deviceModel | string | |
| context.device.deviceName | string | |
| context.device.id | string | |
| context.device.locale | string | Beispielsweise en-GB, de-DE |
| context.device.network | string | |
| context.device.oemName | string | |
| context.device.osVersion | string | |
| context.device.roleInstance | string | |
| context.device.roleName | string | |
| context.device.type | string | |
| context.location | object | Abgeleitet von „clientip“. |
| context.location.city | string | |
| context.location.clientip | string | Letztes Oktagon wird als 0 anonymisiert. |
| context.location.continent | string | |
| context.location.country | string | |
| context.location.province | string | |
| context.operation.id | string | Elemente mit derselben Vorgangs-ID werden im Portal als verwandte Elemente angezeigt. Üblicherweise die Anforderungs-ID. |
| context.operation.parentId | string | Ermöglicht geschachtelte verwandte Elemente. |
| context.session.id | string | ID einer Gruppe von Vorgängen derselben Quelle. Ein Zeitraum von 30 Minuten ohne Vorgang signalisiert das Ende einer Sitzung. |
| context.session.isFirst | boolean | |
| context.user.accountAcquisitionDate | string | |
| context.user.anonAcquisitionDate | string | |
| context.user.anonId | string | |
| context.user.authAcquisitionDate | string | [Authentifizierter Benutzer](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | boolean | |
| internal.data.documentVersion | string | |
| internal.data.id | string | |



## Ereignisse

Von [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) generierte benutzerdefinierte Ereignisse.


|Pfad|Typ|Hinweise|
|---|---|---|
| event [0] count | integer | |
| event [0] name | string | Ereignisname, maximal 250 Zeichen. |
| event [0] url | string | |
| event [0] urlData.base | string | |
| event [0] urlData.host | string | |

## Ausnahmen

Melden [Ausnahmen](app-insights-asp-net-exceptions.md) auf dem Server und im Browser.


|Pfad|Typ|Hinweise|
|---|---|---|
| basicException [0] assembly | string | |
| basicException [0] count | integer | |
| basicException [0] exceptionGroup | string | |
| basicException [0] exceptionType | string | |string | |
| basicException [0] failedUserCodeMethod | string | |
| basicException [0] failedUserCodeAssembly | string | |
| basicException [0] handledAt | string | |
| basicException [0] hasFullStack | boolean | |
| basicException [0] id | string | |
| basicException [0] method | string | |
| basicException [0] message | string | Ausnahmemeldung, maximal 10.000 Zeichen.|
| basicException [0] outerExceptionMessage | string | |
| basicException [0] outerExceptionThrownAtAssembly | string | |
| basicException [0] outerExceptionThrownAtMethod | string | |
| basicException [0] outerExceptionType | string | |
| basicException [0] outerId | string | |
| basicException [0] parsedStack [0] assembly | string | |
| basicException [0] parsedStack [0] fileName | string | |
| basicException [0] parsedStack [0] level | integer | |
| basicException [0] parsedStack [0] line | integer | |
| basicException [0] parsedStack [0] method | string | |
| basicException [0] stack | string | Maximal 10.000|
| basicException [0] typeName | string | |



## Ablaufverfolgungsmeldungen

Gesendet von [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) und über die [Protokollierungsadapter](app-insights-asp-net-trace-logs.md).


|Pfad|Typ|Hinweise|
|---|---|---|
| message [0] loggerName | string ||
| message [0] parameters | string ||
| message [0] raw | string | Die Protokollmeldung, maximale Länge: 10.000. Sie können diese Zeichenfolgen im Portal suchen. |
| message [0] severityLevel | string | |



## Remoteabhängigkeit

Gesendet von TrackDependency. Werden zum Berichten von Leistung und Nutzung von [Aufrufen von abhängigen Komponenten](app-insights-asp-net-dependencies.md) auf dem Server sowie von AJAX-Aufrufen im Browser verwendet.

|Pfad|Typ|Hinweise|
|---|---|---|
| remoteDependency [0] async | boolean | |
| remoteDependency [0] baseName | string | |
| remoteDependency [0] commandName | string | Beispiel: asp „home/index“ |
| remoteDependency [0] count | integer | |
| remoteDependency [0] dependencyTypeName | string | HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value | number | Zeit vom Aufruf bis zum Abschließen der Antwort durch die Abhängigkeit. |
| remoteDependency [0] id | string | |
| remoteDependency [0] name | string | URL, maximal 250 Zeichen.|
| remoteDependency [0] resultCode | string | Aus HTTP-Abhängigkeit |
| remoteDependency [0] success | boolean | |
| remoteDependency [0] type | string | HTTP, SQL, ... |
| remoteDependency [0] url | string | Maximal 2.000 |
| remoteDependency [0] urlData.base | string | Maximal 2.000 |
| remoteDependency [0] urlData.hashTag | string | |
| remoteDependency [0] urlData.host | string | Max. 200|


## Anforderungen

Gesendet von [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Die Standardmodule verwenden TrackRequest zum Berichten der Serverantwortzeit, gemessen auf dem Server.


|Pfad|Typ|Hinweise|
|---|---|---|
| request [0] count | integer | |
| request [0] durationMetric.value | number | Zeit vom Empfang der Anforderung bis zur Antwort. 1e7 == 1s |
| request [0] id | string | Vorgangs-ID |
| request [0] name | string | GET/POST + URL-Basis, maximal 250 Zeichen. |
| request [0] responseCode | integer | HTTP-Antwort, die an den Client gesendet wird. |
| request [0] success | boolean | Standard == responseCode<400 |
| request [0] url | string | Host nicht eingeschlossen |
| request [0] urlData.base | string | |
| request [0] urlData.hashTag | string | |
| request [0] urlData.host | string | |


## Leistung der Seitenaufrufe

Gesendet vom Browser. Misst die Zeit zum Verarbeiten einer Seite, von der Initialisierung der Anforderung durch den Benutzer bis zur vollständigen Anzeige (asynchrone AJAX-Aufrufe ausgenommen).

Kontextwerte zeigen Clientbetriebssystem und Browserversion.


|Pfad|Typ|Hinweise|
|---|---|---|
| clientPerformance [0] clientProcess.value | integer | Zeit vom vollständigen Empfang der HTML-Daten bis zur Anzeige der Seite. |
| clientPerformance [0] name | string | |
| clientPerformance [0] networkConnection.value | integer | Benötigte Zeit zum Herstellen einer Netzwerkverbindung. |
| clientPerformance [0] receiveRequest.value | integer | Zeit vom vollständigen Senden der Anforderung bis zum Empfang der HTML-Daten in der Antwort. |
| clientPerformance [0] sendRequest.value | integer | Benötigte Zeit zum Senden der HTTP-Anforderung. |
| clientPerformance [0] total.value | integer | Zeit vom Sendebeginn der Anforderung bis zur Anzeige der Seite. |
| clientPerformance [0] url | string | URL dieser Anforderung. |
| clientPerformance [0] urlData.base | string | |
| clientPerformance [0] urlData.hashTag | string | |
| clientPerformance [0] urlData.host | string | |
| clientPerformance [0] urlData.protocol | string | |

## Seitenaufrufe

Gesendet von trackPageView() oder [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Pfad|Typ|Hinweise|
|---|---|---|
| view [0] count | integer | |
| view [0] durationMetric.value | integer | Wert optional in trackPageView() oder über start/stopTrackPage festgelegt. Ist nicht dasselbe wie die clientPerformance-Werte. |
| view [0] name | string | Seitentitel, maximal 250 Zeichen. |
| view [0] url | string | |
| view [0] urlData.base | string | |
| view [0] urlData.hashTag | string | |
| view [0] urlData.host | string | |



## Verfügbarkeit

Liefert Berichtdaten zu [Verfügbarkeitswebtests](app-insights-monitor-web-app-availability.md).

|Pfad|Typ|Hinweise|
|---|---|---|
| availability [0] availabilityMetric.name | string | availability |
| availability [0] availabilityMetric.value | number |1\.0 oder 0.0 |
| availability [0] count | integer | |
| availability [0] dataSizeMetric.name | string | |
| availability [0] dataSizeMetric.value | integer | |
| availability [0] durationMetric.name | string | |
| availability [0] durationMetric.value | number | Länge des Tests. 1e7==1s |
| availability [0] message | string | Fehlerdiagnose |
| availability [0] result | string | Erfolgreich/Fehler |
| availability [0] runLocation | string | Geoquelle der HTTP-Anforderung. |
| availability [0] testName | string | |
| availability [0] testRunId | string | |
| availability [0] testTimestamp | string | |




## Metriken

Generiert von TrackMetric().

Die Metrik befindet sich in context.custom.metrics[0].

Beispiel:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## Grundlegendes zu Metrikwerten

Informationen zu Metrikwerten werden, sowohl in Metrikberichten als auch an anderer Stelle, mit einer Standardobjektstruktur bereitgestellt. Beispiel:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Aktuell – dies kann sich in Zukunft ändern – sind in allen Werten, die von den SDK-Standardmodulen berichtet werden, `count==1` und nur die Felder `name` und `value` von Interesse. Dies wäre nur anders, wenn Sie eigene TrackMetric-Aufrufe schreiben, in denen Sie die weiteren Parameter festlegen.

Der Zweck der weiteren Felder besteht darin, Metriken im SDK zu aggregieren, um den Datenverkehr im Portal zu verringern. Sie könnten beispielsweise den Durchschnitt für mehrere aufeinanderfolgende Lesevorgänge ermitteln, bevor jeder Metrikbericht gesendet wird. Anschließend könnten Sie die Mindest-, Maximal- und Standardabweichung und den Aggregatwert (Summe oder Durchschnitt) berechnen und die Anzahl auf die Anzahl von Lesevorgängen festlegen, die der Bericht repräsentiert

In den obigen Tabellen wurden die selten verwendeten Felder „count“, „min“, „max“, „stdDev“ und „sampledValue“ ausgelassen.

Anstelle einer Vorabaggretation von Metriken können Sie die [Stichprobenerstellung](app-insights-sampling.md) nutzen, wenn Sie den Umfang der Telemetriedaten verringern möchten.


### Zeitspannen

Sofern nicht anders angegeben, wird die Dauer in Zehnteln einer Mikrosekunde angegeben, d. h. 10000000.0 entspricht 1 Sekunde.



## Weitere Informationen

* [Application Insights](app-insights-overview.md) 
* [Fortlaufender Export](app-insights-export-telemetry.md)
* [Codebeispiele](app-insights-export-telemetry.md#code-samples)

<!---HONumber=AcomDC_0323_2016-->