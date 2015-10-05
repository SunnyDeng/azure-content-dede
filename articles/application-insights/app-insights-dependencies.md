<properties 
	pageTitle="Diagnostizieren von Problemen mit Abhängigkeiten in Application Insights" 
	description="Finden von Fehlern und Leistungseinbußen, die von Abhängigkeiten verursacht werden" 
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
	ms.date="09/17/2015" 
	ms.author="awills"/>
 
# Diagnostizieren von Problemen mit Abhängigkeiten in Application Insights


Eine *Abhängigkeit* ist eine externe Komponente, die von Ihrer App aufgerufen wird. In der Regel handelt es sich um einen Dienst, der über HTTP oder eine Datenbank oder ein Dateisystem aufgerufen wird. In Visual Studio Application Insights können Sie leicht erkennen, wie lange die Anwendung auf Abhängigkeiten wartet und wie oft ein Abhängigkeitsaufruf nicht funktioniert.

## Einsatzgebiete

Aktuell steht die integrierte Abhängigkeitsüberwachung für folgende Apps und Dienste zur Verfügung:

* ASP.NET-Web-Apps und Dienste, die auf einem IIS-Server oder auf Azure ausgeführt werden
* [Java-Web-Apps](app-insights-java-agent.md)

Für andere Typen wie Geräte-Apps können Sie mithilfe der [TrackDependency-API](app-insights-api-custom-events-metrics.md#track-dependency) Ihre eigene Überwachung schreiben.

Der standardmäßig verfügbare Abhängigkeitsmonitor meldet derzeit Aufrufe an diese Abhängigkeitstypen:

* ASP.NET
 * SQL-Datenbanken
 * ASP.NET-Web- und WCF-Dienste, die HTTP-basierte Bindungen verwenden
 * Lokale oder Remote-HTTP-Aufrufe
 * Azure DocumentDb, Tabelle, Blobspeicher und Warteschlange
* Java
 * Aufrufe an eine Datenbank über einen [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)-Treiber, z. B. MySQL, SQL Server, PostgreSQL oder SQLite.

In diesem Fall könnten Sie Ihre eigenen SDK-Aufrufe zum Überwachen anderer Abhängigkeiten schreiben.

## So richten Sie die Abhängigkeitsüberwachung ein

Installieren Sie den entsprechenden Agent für den Hostserver.

Plattform | Installieren
---|---
IIS-Server | [Statusmonitor](app-insights-monitor-performance-live-website-now.md)
Azure-Web-App | [Application Insights-Erweiterung](../azure-portal/insights-perf-analytics.md)
Java-Webserver | [Java-Web-Apps](app-insights-java-agent.md)

Für den Statusmonitor für IIS-Server müssen Sie das Quellprojekt nicht mit dem Application Insights-SDK neu erstellen.

## <a name="diagnosis"></a> Diagnostizieren von Leistungsproblemen der Abhängigkeit

So bewerten Sie die Leistung der Anforderungen an den Server

![Klicken Sie in Ihrer Anwendung in Application Insights auf der Seite "Übersicht" auf die Kachel "Leistung"](./media/app-insights-dependencies/01-performance.png)

Scrollen Sie nach unten, um das Raster der Anforderungen anzuzeigen:

![Liste der Anforderungen mit Mittelwerten und Anzahlen](./media/app-insights-dependencies/02-reqs.png)

Die zuerst aufgeführte Instanz dauert sehr lange. Mal sehen, ob wir herausfinden, wo die Zeit beansprucht wird.

Klicken Sie auf diese Zeile, um einzelne Anforderungsereignisse anzuzeigen:


![Liste der Anforderungsinstanzen](./media/app-insights-dependencies/03-instances.png)

Klicken Sie auf eine beliebige Instanz mit langer Ausführungsdauer, um diese näher zu überprüfen.

> [AZURE.NOTE]Scrollen Sie nach unten, um eine Instanz auszuwählen. Durch Wartezeiten in der Pipeline sind die Daten für die zuerst aufgeführten Instanzen möglicherweise unvollständig.

Scrollen Sie nach unten zu den Remoteabhängigkeitsaufrufen im Zusammenhang mit dieser Anforderung:

![Finden von Aufrufen von Remoteabhängigkeiten, Identifizieren ungewöhnlich langer Laufzeiten](./media/app-insights-dependencies/04-dependencies.png)

Der Großteil der Zeit zur Verarbeitung dieser Anforderung wurde für einen Aufruf eines lokalen Diensts aufgewendet.

Wählen Sie diese Zeile aus, um weitere Informationen zu erhalten:


![Klicken Sie sich durch diese Remoteabhängigkeit, um die Ursache herauszufinden](./media/app-insights-dependencies/05-detail.png)

Dieses Detail enthält genügend Informationen, um das Problem zu diagnostizieren.



## Fehler

Wenn Anforderungsfehler vorhanden sind, klicken Sie auf das Diagramm.

![Klicken Sie auf das Diagramm mit Anforderungsfehlern](./media/app-insights-dependencies/06-fail.png)

Klicken Sie sich durch einen Anforderungstyp und eine Anforderungsinstanz, um einen fehlerhaften Aufruf einer Remoteabhängigkeit zu finden.


![Klicken Sie auf den Anforderungstyp, klicken Sie auf die Instanz, um eine andere Ansicht derselben Instanz abzurufen, und klicken Sie darauf, um Details zur Ausnahme zu erhalten.](./media/app-insights-dependencies/07-faildetail.png)


## Benutzerdefinierte Nachverfolgung von Abhängigkeiten

Das Standardmodul für die Nachverfolgung von Abhängigkeiten ermittelt automatisch externe Abhängigkeiten wie Datenbanken und REST-APIs. Es kann jedoch gewünscht sein, einige zusätzliche Komponenten auf die gleiche Weise zu behandeln.

Sie können Code schreiben, der Abhängigkeitsinformationen unter Verwendung der gleichen [TrackDependency-API](app-insights-api-custom-events-metrics.md#track-dependency) sendet, die von den Standardmodulen verwendet wird.

Beispiel: Wenn Sie Ihren Code mit einer Assembly erstellen, die Sie nicht selbst geschrieben haben, könnten Sie die Zeit aller Aufrufe ermitteln, um herauszufinden, welchen Beitrag sie an Ihren Reaktionszeiten hat. Um diese Daten in den Abhängigkeitsdiagrammen in Application Insights anzuzeigen, senden Sie sie mit `TrackDependency`.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Wenn Sie das Standardmodul für die Nachverfolgung von Abhängigkeiten deaktivieren möchten, entfernen Sie den Verweis auf "DependencyTrackingTelemetryModule" in [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

<!--Link references-->

<!---HONumber=Sept15_HO4-->