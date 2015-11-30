<properties 
	pageTitle="Abhängigkeitsüberwachung in Application Insights" 
	description="Analysieren Sie die Auslastung, Verfügbarkeit und Leistung Ihrer lokalen oder Microsoft Azure-Webanwendung mit Application Insights." 
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
	ms.date="11/17/2015" 
	ms.author="awills"/>


# Einrichten von Application Insights: Abhängigkeitsüberwachung


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



Eine *Abhängigkeit* ist eine externe Komponente, die von Ihrer App aufgerufen wird. In der Regel handelt es sich um einen Dienst, der über HTTP oder eine Datenbank oder ein Dateisystem aufgerufen wird. In Visual Studio Application Insights können Sie leicht erkennen, wie lange die Anwendung auf Abhängigkeiten wartet und wie oft ein Abhängigkeitsaufruf nicht funktioniert.

![Beispieldiagramme](./media/app-insights-asp-net-dependencies/10-intro.png)

Der standardmäßig verfügbare Abhängigkeitsmonitor meldet derzeit Aufrufe an diese Abhängigkeitstypen:

* ASP.NET
 * SQL-Datenbanken
 * ASP.NET-Web- und WCF-Dienste, die HTTP-basierte Bindungen verwenden
 * Lokale oder Remote-HTTP-Aufrufe
 * Azure DocumentDb, Tabelle, Blobspeicher und Warteschlange
* Java
 * Aufrufe an eine Datenbank über einen [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)-Treiber, z. B. MySQL, SQL Server, PostgreSQL oder SQLite.

Mit der [TrackDependency-API](app-insights-api-custom-events-metrics.md#track-dependency) können Sie eigene SDK-Aufrufe zum Überwachen anderer Abhängigkeiten schreiben.


## So richten Sie die Abhängigkeitsüberwachung ein

Sie benötigen ein [Microsoft Azure](http://azure.com)-Abonnement.

### Wenn Ihre App auf Ihrem IIS-Server ausgeführt wird

1. Melden Sie sich mit Administratorrechten auf Ihrem IIS-Webserver an.
2. Laden Sie den [Statusmonitor-Installer](http://go.microsoft.com/fwlink/?LinkId=506648) herunter, und starten Sie ihn.
4. Melden Sie sich im Installations-Assistenten bei Microsoft Azure an.

    ![Melden Sie sich mit den Anmeldeinformationen für ihr Microsoft-Konto bei Azure an.](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *Verbindungsfehler? Informationen hierzu finden Sie unter [Problembehandlung](#troubleshooting).*

5. Wählen Sie die installierte Webanwendung oder Website aus, die Sie überwachen möchten, und konfigurieren Sie die Ressource, in der die Ergebnisse im Application Insights-Portal angezeigt werden sollen.

    ![Wählen Sie eine App und eine Ressource.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    In der Regel konfigurieren Sie eine neue Ressource und [Ressourcengruppe][roles].

    Sie können andernfalls auch eine vorhandene Ressource verwenden, wenn Sie bereits [Webtests][availability] für Ihre Website oder die [Webclientüberwachung][client] eingerichtet haben.

6. Starten Sie IIS neu.

    ![Wählen Sie im oberen Bereich des Dialogfelds "Neustart".](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    Der Webdienst wird für kurze Zeit unterbrochen.

6. Beachten Sie, dass die Datei "ApplicationInsights.config" in die Web-Apps eingefügt wurde, die Sie überwachen möchten.

    ![Suchen Sie die CONFIG-Datei bei den Codedateien der Web-App.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   Außerdem wurden Änderungen an der Datei web.config vorgenommen.

#### Möchten Sie die Konfiguration später ändern?

Nach Abschluss des Assistenten können Sie die Konfiguration des Agenten später jederzeit ändern. Sie können dies auch verwenden, wenn bei der ursprünglichen Einrichtung des Agenten ein Problem aufgetreten ist.

![Klicken Sie auf das Application Insights-Symbol in der Taskleiste](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### Wenn Ihre App als Azure-Web-App ausgeführt wird

Fügen Sie in der Systemsteuerung Ihrer Azure-Web-App die Application Insights-Erweiterung hinzu.

![In der Web-App: Einstellungen, Erweiterungen, Hinzufügen, Application Insights](./media/app-insights-asp-net-dependencies/05-extend.png)


### Wenn es sich um ein Azure-Clouddienstprojekt handelt

[Fügen Sie Web- und Workerrollen Skripts hinzu](app-insights-cloudservices.md).

## <a name="diagnosis"></a> Diagnostizieren von Leistungsproblemen der Abhängigkeit

So bewerten Sie die Leistung der Anforderungen an den Server

![Klicken Sie in Ihrer Anwendung in Application Insights auf der Seite "Übersicht" auf die Kachel "Leistung"](./media/app-insights-asp-net-dependencies/01-performance.png)

Scrollen Sie nach unten, um das Raster der Anforderungen anzuzeigen:

![Liste der Anforderungen mit Mittelwerten und Anzahlen](./media/app-insights-asp-net-dependencies/02-reqs.png)

Die zuerst aufgeführte Instanz dauert sehr lange. Mal sehen, ob wir herausfinden, wo die Zeit beansprucht wird.

Klicken Sie auf diese Zeile, um einzelne Anforderungsereignisse anzuzeigen:


![Liste der Anforderungsinstanzen](./media/app-insights-asp-net-dependencies/03-instances.png)

Klicken Sie auf eine beliebige Instanz mit langer Ausführungsdauer, um diese näher zu überprüfen.

> [AZURE.NOTE]Scrollen Sie nach unten, um eine Instanz auszuwählen. Durch Wartezeiten in der Pipeline sind die Daten für die zuerst aufgeführten Instanzen möglicherweise unvollständig.

Scrollen Sie nach unten zu den Remoteabhängigkeitsaufrufen im Zusammenhang mit dieser Anforderung:

![Finden von Aufrufen von Remoteabhängigkeiten, Identifizieren ungewöhnlich langer Laufzeiten](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Der Großteil der Zeit zur Verarbeitung dieser Anforderung wurde für einen Aufruf eines lokalen Diensts aufgewendet.

Wählen Sie diese Zeile aus, um weitere Informationen zu erhalten:


![Klicken Sie sich durch diese Remoteabhängigkeit, um die Ursache herauszufinden](./media/app-insights-asp-net-dependencies/05-detail.png)

Dieses Detail enthält genügend Informationen, um das Problem zu diagnostizieren.



## Fehler

Wenn Anforderungsfehler vorhanden sind, klicken Sie auf das Diagramm.

![Klicken Sie auf das Diagramm mit Anforderungsfehlern](./media/app-insights-asp-net-dependencies/06-fail.png)

Klicken Sie sich durch einen Anforderungstyp und eine Anforderungsinstanz, um einen fehlerhaften Aufruf einer Remoteabhängigkeit zu finden.


![Klicken Sie auf den Anforderungstyp, klicken Sie auf die Instanz, um eine andere Ansicht derselben Instanz abzurufen, und klicken Sie darauf, um Details zur Ausnahme zu erhalten.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## Benutzerdefinierte Nachverfolgung von Abhängigkeiten

Das Standardmodul für die Nachverfolgung von Abhängigkeiten ermittelt automatisch externe Abhängigkeiten wie Datenbanken und REST-APIs. Es kann jedoch gewünscht sein, einige zusätzliche Komponenten auf die gleiche Weise zu behandeln.

Sie können Code schreiben, der Abhängigkeitsinformationen unter Verwendung der gleichen [TrackDependency-API](app-insights-api-custom-events-metrics.md#track-dependency) sendet, die von den Standardmodulen verwendet wird.

Beispiel: Wenn Sie Ihren Code mit einer Assembly erstellen, die Sie nicht selbst geschrieben haben, könnten Sie die Zeit aller Aufrufe ermitteln, um herauszufinden, welchen Beitrag sie an Ihren Reaktionszeiten hat. Um diese Daten in den Abhängigkeitsdiagrammen in Application Insights anzuzeigen, senden Sie sie mit `TrackDependency`.

```C#

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

## Nächste Schritte

- [Exceptions](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Daten zu Seiten und Benutzern](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Nov15_HO4-->