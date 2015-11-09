<properties 
	pageTitle="collectd: Leistungsstatistiken für Java unter Unix in Application Insights" 
	description="Erweiterte Überwachung Ihrer Java-Website mit dem CollectD-Plug-In für Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2015" 
	ms.author="awills"/>
 
# collectd: Unix-Leistungsmetriken in Application Insights

*Application Insights befindet sich in der Vorschau.*

Um Leistungsmetriken für Unix-Systeme in [Application Insights](app-insights-overview.md) zu untersuchen, installieren Sie[collectd](http://collectd.org/) zusammen mit dem entsprechenden Application Insights-Plug-In. Diese Open Source-Lösung sammelt eine Vielzahl von System- und Netzwerkstatistiken.

In der Regel verwenden Sie collectd, wenn Sie [Ihren Java-Webdienst bereits mithilfe von Application Insights instrumentiert haben][java], sodass mehr Daten zur Verfügung stehen, um Ihnen beim Verbessern der Leistung Ihrer App oder beim Diagnostizieren von Problemen zu helfen.

![Beispieldiagramme](./media/app-insights-java-collectd/sample.png)

## Abrufen des Instrumentationsschlüssels

Öffnen Sie im [Microsoft Azure-Portal](http://portal.azure.com) die Ressource [Application Insights](start), in der die Daten angezeigt werden sollen. (Oder [erstellen Sie eine neue Ressource](app-insights-create-new-resource.md).)

Kopieren Sie den Instrumentationsschlüssel, der die Ressource identifiziert.

![Durchsuchen Sie alle, öffnen Sie die Ressource, und wählen Sie dann in der Dropdownliste "Essentials" den Instrumentationsschlüssel aus, und kopieren Sie ihn](./media/app-insights-java-collectd/02-props.png)



## Installieren von collectd und des Plug-Ins

Auf Unix-Servercomputern:

1. Installieren Sie [collectd](http://collectd.org/), Version 5.4.0 oder höher.
2. Laden Sie das [collectd-Writer-Plug-In für Application Insights](http://go.microsoft.com/fwlink/?LinkId=618634) herunter. Beachten Sie die Versionsnummer.
3. Kopieren Sie die Plug-In-JAR in `/usr/share/collectd/java`.
3. Bearbeiten Sie `/etc/collectd/collectd.conf`:
 * Stellen Sie sicher, dass [das Java-Plug-In](https://collectd.org/wiki/index.php/Plugin:Java) aktiviert ist.
 * Aktualisieren Sie den JVMArg-Wert für "java.class.path", sodass die folgende JAR-Datei enthalten ist. Aktualisieren Sie die Versionsnummer, sodass sie mit der übereinstimmt, die Sie heruntergeladen haben:
  * `/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar`
 * Fügen Sie diesen Codeausschnitt mit dem Instrumentationsschlüssel der Ressource hinzu:

```

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Er ist Teil der Beispielkonfigurationsdatei:

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
                
      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
. ...

Konfigurieren Sie andere [collectd-Plug-Ins](https://collectd.org/wiki/index.php/Table_of_Plugins), die eine Vielzahl von Daten aus verschiedenen Quellen sammeln können.

Starten Sie collectd gemäß dem [Handbuch](https://collectd.org/wiki/index.php/First_steps) neu.

## Anzeigen der Daten in Application Insights

Öffnen Sie in der Application Insights-Ressource den [Metrik-Explorer, und fügen Sie Diagramme hinzu][metrics], indem Sie die Metriken auswählen, die aus der benutzerdefinierten Kategorie angezeigt werden sollen.

![](./media/app-insights-java-collectd/result.png)

Standardmäßig werden die Metriken für alle Hostcomputer aggregiert, von denen die Metriken gesammelt wurden. Aktivieren Sie zum Anzeigen der Metriken pro Host auf dem Blatt mit Diagrammdetails "Gruppierung", und wählen Sie dann aus, dass nach "CollectD-Host" gruppiert werden soll.


## So schließen Sie den Upload bestimmter Statistiken aus

Standardmäßig sendet das Application Insights-Plug-In alle Daten, die von allen aktivierten "lesenden" collectd-Plug-Ins gesammelt wurden.

So schließen Sie Daten von bestimmten Plug-Ins oder Datenquellen aus:

* Bearbeiten Sie die Konfigurationsdatei. 
* Fügen Sie in `<Plugin ApplicationInsightsWriter>` folgende Direktivenzeilen hinzu:

Direktive | Effekt
---|---
`Exclude disk` | Schließen Sie alle vom `disk`-Plug-In gesammelten Daten aus.
`Exclude disk:read,write` | Schließen Sie die Quellen mit den Namen `read` und `write` aus dem `disk`-Plug-In aus.

Trennen Sie Direktiven mit einem Zeilenumbruch.


## Probleme?

*Daten werden im Portal nicht angezeigt.*

* Öffnen Sie [Search][diagnostic], um zu überprüfen, ob die Rohereignisse angekommen sind. Manchmal dauert es länger, bis sie im Metrik-Explorer angezeigt werden.
* Aktivieren Sie die Ablaufverfolgung im Application Insights-Plug-In. Fügen Sie diese Zeile in `<Plugin ApplicationInsightsWriter>` hinzu:
 *  `SDKLogger true`
* Öffnen Sie ein Terminal, und starten Sie collectd im ausführlichen Modus, um alle gemeldeten Probleme anzuzeigen:
 * `sudo collectd -f`




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=Nov15_HO1-->