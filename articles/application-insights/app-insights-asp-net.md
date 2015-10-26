<properties 
	pageTitle="Application Insights für ASP.NET" 
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
	ms.date="10/13/2015" 
	ms.author="awills"/>


# Einrichten von Application Insights für ASP.NET

*Application Insights befindet sich in der Vorschau.*

<a name="selector1"></a>

[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) überwacht Ihre Live-Anwendung. So können Sie [Leistungsprobleme und -ausnahmen erkennen und diagnostizieren][detect] und zudem [ermitteln, wie Ihre App verwendet wird][knowUsers]. Hierfür müssen Sie ein SDK in Ihrer App installieren. Das SDK sendet Telemetriedaten über Ihre App an den Application Insights-Dienst, mit dem Sie das Verhalten der App analysieren und darstellen können.


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Wenn Sie das SDK in Visual Studio zu Ihrer App hinzufügen, erhalten Sie Diagramme zu Serveranforderungen, Antwortzeiten und Fehlern.

![Beispiel für Leistungsüberwachungsdiagramm](./media/app-insights-asp-net/10-perf.png)

Sie können mit der API auch die Nutzung im Detail überwachen.

#### Vorbereitung

Erforderlich:

* Ein Abonnement für [Microsoft Azure](http://azure.com) Wenn Ihr Team oder Ihre Organisation über ein Azure-Abonnement verfügt, kann der Besitzer Sie mit Ihrem [Microsoft-Konto](http://live.com) hinzufügen.
* Visual Studio 2013, Update 3 oder höher.

## <a name="ide"></a> Hinzufügen von Application Insights zu Ihrem Projekt in Visual Studio

#### Falls es sich um ein neues Projekt handelt …

Wenn Sie in Visual Studio ein neues Projekt erstellen, achten Sie darauf, dass Application Insights ausgewählt ist.


![Erstellen eines ASP.NET-Projekts](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### … oder falls es sich um ein vorhandenes Projekt handelt

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen".

![Application Insights hinzufügen](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Setupoptionen

Falls Sie erstmals ein Projekt erstellen, werden Sie nach einer Anmeldung bei Microsoft Azure gefragt.

Wenn diese App Teil einer größeren Anwendung ist, empfiehlt es sich, sie mithilfe von **Einstellungen konfigurieren** in derselben Ressourcengruppe wie die anderen Komponenten abzulegen.


####<a name="land"></a> Welche Funktion hat "Application Insights hinzufügen"?

Mit dem Befehl wurden die folgenden Schritte ausgeführt (die Sie stattdessen auch [manuell ausführen](app-insights-start-monitoring-app-health-usage.md) können):

* Er erstellt eine Application Insights-Ressource im [Azure-Portal][portal]. Hier werden Ihre Daten angezeigt. Er ruft den *Instrumentationsschlüssel* ab, der die Ressource identifiziert.
* Er fügt das NuGet-Paket mit dem Application Insights-Web-SDK dem Projekt hinzu. Um es in Visual Studio anzuzeigen, klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie "NuGet-Pakete verwalten" aus.
* Er fügt den Instrumentationsschlüssel in `ApplicationInsights.config` ein.


## <a name="run"></a> Ausführen des Projekts

Starten Sie Ihre Anwendung mit F5, und probieren Sie es aus: Öffnen Sie verschiedene Seiten, um einige Telemetriedaten zu generieren.

In Visual Studio sehen Sie eine Anzahl der gesendeten Ereignisse.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Öffnen von Application Insights

Öffnen Sie die Application Insights-Ressource im [Azure-Portal][portal].

![Klicken Sie mit der rechten Maustaste auf Ihr Projekt und öffnen Sie das Azure-Portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### Metriken: Aggregierte Daten

Suchen Sie nach Daten in der Übersichtsdiagrammen. Zuerst sehen Sie lediglich einen oder zwei Punkte. Zum Beispiel:

![Klicken Sie, um weitere Daten anzuzeigen](./media/app-insights-asp-net/12-first-perf.png)

Klicken Sie sich durch ein beliebiges Diagramm, um ausführlichere Metriken anzuzeigen. [Weitere Informationen zu Metriken.][perf]

* *Keine Daten zu Benutzern oder Seiten?* - [Hinzufügen von Daten zu Benutzern und Seiten](../article/application-insights/app-insights-asp-net-client.md)

### Suche: Einzelne Ereignisse

Öffnen Sie die Suche, um einzelne Anforderungen und die zugeordneten Ereignisse zu untersuchen.

![](./media/app-insights-asp-net/21-search.png)

[Weitere Informationen zur Suche](app-insights-diagnostic-search.md)

* *Keine zugeordneten Ereignisse?* Richten Sie [Serverausnahmen](../article/application-insights/app-insights-asp-net-exception-mvc.md) und [Abhängigkeiten](../article/application-insights/app-insights-asp-net-dependencies.md) ein.

### Sie sehen keine Daten?

* Stellen Sie sicher, dass das richtige Element angezeigt wird. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, klicken Sie auf "Durchsuchen" > "Application Insights", und wählen Sie dann die App aus.
* Verwenden Sie die Anwendung, und öffnen Sie verschiedene Seiten, damit einige Telemetriedaten generiert werden.
* Öffnen Sie das Blatt [Suche][diagnostic], um einzelne Ereignisse anzuzeigen. Manchmal dauert es eine Weile, bis Ereignisse über die Metrikpipeline übertragen werden.
* Warten Sie einige Sekunden, und klicken Sie auf "Aktualisieren".
* Informationen hierzu finden Sie unter [Problembehandlung][qna].


## Veröffentlichen der App

Stellen Sie jetzt Ihre Anwendung bereit, und sehen Sie zu, wie Daten gesammelt werden.

Beim Betrieb im Debugmodus wird Telemetrie über die Pipeline geliefert, sodass Ihnen innerhalb von wenigen Sekunden Daten angezeigt werden. Sobald Sie Ihre Anwendung bereitstellen, sammeln sich die Daten langsamer an.

#### Probleme auf dem Buildserver?

Weitere Informationen finden Sie in [diesem Artikel zur Problembehandlung](app-insights-troubleshoot-faq.md#NuGetBuild).

## Nächste Schritte

- [Daten zu Seiten und Benutzern](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Exceptions](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Abhängigkeiten](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Verfügbarkeit](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## So upgraden Sie auf zukünftige SDK-Versionen

Für ein Upgrade auf eine [neue Version des SDK](app-insights-release-notes-dotnet.md) öffnen Sie wieder den NuGet-Paket-Manager und filtern die Ansicht nach installierten Paketen. Wählen Sie "Microsoft.ApplicationInsights.Web" und dann "Upgrade" aus.

Wenn Sie Anpassungen an der Datei "ApplicationInsights.config" vorgenommen haben, speichern Sie vor dem Upgrade eine Kopie davon. Sie können anschließend die Änderungen in die neue Version übernehmen.



## <a name="video"></a>Video

> [AZURE.VIDEO getting-started-with-application-insights]


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
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO3-->