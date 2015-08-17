<properties
   pageTitle="Einrichten von Application Insights für Service-Fabric-Anwendungen"
   description="Erhalten Sie Service Fabric-Ereignisse für Ihre Anwendung in Application Insights."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2015"
   ms.author="mattrow"/>

# Einrichten von Application Insights für Service-Fabric-Anwendungen
 Dieser Artikel führt Sie durch die Aktivierung von Application Insights für Ihre Service Fabric-Anwendung.

## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie die Service Fabric-Anwendung bereits in Visual Studio erstellt haben. Informationen zum Erstellen einer Anwendung finden Sie [hier](service-fabric-reliable-services-quick-start.md).

## Installieren des NuGet-Pakets
Das NuGet-Paket wurde als Teil des Service Fabric-SDK veröffentlicht und ist eine Vorabversion des NuGet-Pakets Microsoft.ServiceFabric.Telemetry.ApplicationInsights. Dieses Paket verbindet die Service Fabric-EventSource-Ereignisse mit Application Insights, um eine automatisierte Instrumentation der Service Fabric-Anwendung verfügbar zu machen. Das Paket wird fortlaufend mit neuen Ereignissen aktualisiert, die automatisch von Ihrer Anwendung ausgegeben werden.

Führen Sie zum Installieren des Pakets die folgenden Schritte aus:

1. Öffnen Sie den NuGet-Paket-Manager für Ihre Service Fabric-Anwendung. Klicken Sie hierzu in Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie "NuGet-Pakete verwalten" aus.
2. Wählen Sie "Microsoft Azure Service Fabric" als Paketquelle aus, um Pakete auflisten, die in dem Service Fabric-SDK enthalten sind. ![VS2015 NuGet-Paket-Manager](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. Wählen Sie auf der linken Seite das Microsoft.ServiceFabric.Telemetry.ApplicationInsights-Paket aus.
4. Klicken Sie auf "Installieren", um den Installationsvorgang zu starten.
5. Lesen und akzeptieren Sie den Endbenutzer-Lizenzvertrag.

## Aktivieren von Service Fabric-Ereignissen
Um Service Fabric-Ereignisse automatisch in Application Insights zu empfangen, müssen Sie den Listener aktivieren. Fügen Sie hierzu die folgende Codezeile in Ihre Anwendung ein.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### Beispiel für StatefulActor\\Program.cs:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

Informationen zu den Ereignissen, die von der Reliable Actors-Laufzeit ausgegeben werden, finden Sie [hier](service-fabric-reliable-actors-diagnostics.md). Informationen zu den Ereignissen, die von der Reliable Services-Laufzeit ausgegeben werden, finden Sie [hier](service-fabric-reliable-services-diagnostics.md).

Beachten Sie, dass zum Empfangen von Methodenaufrufen der Reliable Actors-Laufzeit EventLevel.Verbose verwendet werden muss (siehe Beispiele oben).

## Einrichten von Application Insights
Die Verbindung der Service Fabric-Anwendung mit Ihrer Application Insights-Ressource erfolgt über einen Instrumentationsschlüssel. Informationen zum Abrufen des Instrumentationsschlüssels finden Sie im [Application Insight-Handbuch](../app-insights-create-new-resource.md#create-an-application-insights-resource). Wählen Sie beim Erstellen von Ressourcen den Anwendungstyp "Andere" aus.

![Auswahl von "Andere" als Anwendungstyp in Application Insights](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

Sobald Sie über den Instrumentationsschlüssel verfügen, fügen Sie ihn in die Datei "applicationinsights.config" wie folgt ein:

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## Anzeigen von Daten
Sie können das [Application Insights-Blatt an Ihre Anforderungen anpassen](../app-insights-metrics-explorer.md). Die meisten Service Fabric-Ereignisse werden als "Benutzerdefinierte Ereignisse" angezeigt. Fabric Actors-Methodenaufrufe und RunAsync()-Dienstaufrufe werden dagegen als Anforderungen angezeigt. Wenn Sie diese Ereignisse als Anforderungen modellieren, können Sie die Dimension "request name" und die Metrik "request duration" beim Erstellen von Diagrammen verwenden. Es werden fortlaufend neue Diagramme, Metriken und Ereignisse hinzugefügt, die Sie zu einem späteren Zeitpunkt nutzen können.

## Nächste Schritte
Erfahren Sie mehr über die Verwendung von Application Insights zum Instrumentieren von Service Fabric-Apps.

- [Erste Schritte mit Application Insights](../app-insights-get-started.md)
- [Erstellen eigener benutzerdefinierter Ereignisse und Metriken](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=August15_HO6-->