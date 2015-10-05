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
	ms.date="09/23/2015" 
	ms.author="awills"/>


# Application Insights für ASP.NET

*Application Insights befindet sich in der Vorschau.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) überwacht Ihre Live-Anwendung. So können Sie [Leistungsprobleme und -ausnahmen erkennen und diagnostizieren][detect] und zudem [ermitteln, wie Ihre App verwendet wird][knowUsers]. Es kann mit einer Vielzahl von Anwendungstypen verwendet werden. Dies funktioniert für Apps, die auf den eigenen lokalen IIS-Servern oder Azure-VMs gehostet werden, sowie für Azure-Web-Apps. ([Dies funktioniert auch für Geräte-Apps und Java-Server][start].)

![Beispiel für Leistungsüberwachungsdiagramm](./media/app-insights-asp-net/10-perf.png)


#### Vorbereitung

Erforderlich:

* Ein Abonnement für [Microsoft Azure](http://azure.com) Wenn Ihr Team oder Ihre Organisation über ein Azure-Abonnement verfügt, kann der Besitzer Sie mit Ihrem [Microsoft-Konto](http://live.com) hinzufügen.
* Visual Studio 2013, Update 3 oder höher.

## <a name="ide"></a>Hinzufügen von Application Insights zu Ihrem Projekt in Visual Studio

#### Falls es sich um ein neues Projekt handelt …

Wenn Sie in Visual Studio ein neues Projekt erstellen, achten Sie darauf, dass Application Insights ausgewählt ist.


![Erstellen eines ASP.NET-Projekts](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### … oder falls es sich um ein vorhandenes Projekt handelt

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen".

![Application Insights hinzufügen](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Setupoptionen

Falls Sie erstmals ein Projekt erstellen, werden Sie nach einer Anmeldung bei Microsoft Azure Preview gefragt. (Dies ist nicht zu verwechseln mit Ihrem Visual Studio Online-Konto.)

Wenn diese App Teil einer größeren Anwendung ist, empfiehlt es sich, sie mithilfe von **Einstellungen konfigurieren** in derselben Ressourcengruppe wie die anderen Komponenten abzulegen.


####<a name="land"></a> Welche Funktion hat "Application Insights hinzufügen"?

Mit dem Befehl wurden die folgenden Schritte ausgeführt (die Sie stattdessen auch manuell ausführen könnten):

* Er erstellt eine Application Insights-Ressource im [Azure-Portal][portal]. Hier werden Ihre Daten angezeigt. Es ruft den *Instrumentationsschlüssel*ab, der die Ressource identifiziert.
* Er fügt das NuGet-Paket mit dem Application Insights-Web-SDK dem Projekt hinzu. Um es in Visual Studio anzuzeigen, klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie "NuGet-Pakete verwalten" aus.
* Fügt den Instrumentationsschlüssel in `ApplicationInsights.config` ein.


## <a name="run"></a> Ausführen des Projekts

Starten Sie Ihre Anwendung mit F5, und probieren Sie es aus: Öffnen Sie verschiedene Seiten, um einige Telemetriedaten zu generieren.

In Visual Studio sehen Sie eine Anzahl der gesendeten Ereignisse.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a>Öffnen von Application Insights

Öffnen Sie die Application Insights-Ressource im [Azure-Portal][portal].

![Klicken Sie mit der rechten Maustaste auf Ihr Projekt und öffnen Sie das Azure-Portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)


Suchen Sie nach Daten in der Übersichtsdiagrammen. Zuerst sehen Sie lediglich einen oder zwei Punkte. Zum Beispiel:

![Klicken Sie, um weitere Daten anzuzeigen](./media/app-insights-asp-net/12-first-perf.png)

Klicken Sie sich durch ein beliebiges Diagramm, um ausführlichere Metriken anzuzeigen. [Hier finden Sie weitere Informationen zu Metriken.][perf]

Stellen Sie jetzt Ihre Anwendung bereit, und sehen Sie zu, wie Daten gesammelt werden.


Beim Betrieb im Debugmodus wird Telemetrie über die Pipeline geliefert, sodass Ihnen innerhalb von wenigen Sekunden Daten angezeigt werden. Sobald Sie Ihre Anwendung bereitstellen, sammeln sich die Daten langsamer an.

#### Sie sehen keine Daten?

* Öffnen Sie die Kachel [Suche][diagnostic], um einzelne Ereignisse anzuzeigen.
* Verwenden Sie die Anwendung, und öffnen Sie verschiedene Seiten, damit einige Telemetriedaten generiert werden.
* Warten Sie einige Sekunden, und klicken Sie auf "Aktualisieren".
* Informationen hierzu finden Sie unter [Problembehandlung][qna].

#### Probleme auf dem Buildserver?

Weitere Informationen finden Sie in [diesem Artikel zur Problembehandlung](app-insights-troubleshoot-faq.md#NuGetBuild).


## Hinzufügen von Browserüberwachung

Durch Browser- oder clientseitige Überwachung erhalten Sie Daten zu Benutzern, Sitzungen, Seitenansichten und allen Ausnahmen oder Abstürzen, die im Browser auftreten.

![Wählen Sie "Neu", "Entwicklerdienste", Application Insights.](./media/app-insights-asp-net/16-page-views.png)

Sie können zudem eigenen Code schreiben, um nachzuverfolgen, wie Ihre Benutzer Ihre App verwenden – bis zur Detailebene der Mausklicks und Tastaturanschläge.

#### Wenn Ihre Clients Webbrowser sind

Wenn in Ihrer App Webseiten angezeigt werden, fügen Sie jeder Seite einen JavaScript-Codeausschnitt hinzu. Verwenden Sie den Code aus Ihrer Application Insights-Ressource:

![Öffnen Sie in Ihrer Web-App "Schnellstart", und klicken Sie auf die Option zum Abrufen von Code für die Überwachung von Webseiten](./media/app-insights-asp-net/02-monitor-web-page.png)

Beachten Sie, dass der Code den Instrumentationsschlüssel enthält, der Ihre Anwendungsressource identifiziert.

[Weitere Informationen zur Nachverfolgung von Webseiten.](app-insights-web-track-usage.md)

#### Wenn Ihre Clients Geräte-Apps sind

Wenn Ihre Anwendung für Clients wie Smartphones oder andere Geräte verwendet wird, fügen Sie Ihrer Geräte-App das [entsprechende SDK](app-insights-platforms.md) hinzu.

Wenn Sie das Client-SDK mit dem gleichen Instrumentationsschlüssel wie das Server-SDK konfigurieren, werden die beiden Datenströme integriert, damit Sie sie zusammen anzeigen können.

## Nutzungsverfolgung

Wenn Sie eine neue User Story bereitgestellt haben, möchten Sie wissen, wie viele Ihrer Kunden sie verwenden und ob sie ihre Ziele erreichen oder Schwierigkeiten haben. Sie erhalten ein genaues Bild der Benutzeraktivität durch Einfügen von TrackEvent() und anderen Aufrufen in Ihren Code auf dem Client und auf dem Server.

[Verwenden Sie die API zum Nachverfolgen der Verwendung.][api]


## Diagnoseprotokolle

[Erfassen Sie Protokollablaufverfolgungen][netlogs] aus Ihrem bevorzugten Protokollierungsframework, um Probleme zu diagnostizieren. Die Protokolleinträge werden in der [Diagnosesuche][diagnostic] zusammen mit Application Insights-Telemetrieereignissen angezeigt.

## Veröffentlichen der App

Wenn Sie Ihre App noch nicht veröffentlicht haben (seit Sie Application Insights hinzugefügt haben), holen Sie dies jetzt nach. Beobachten Sie die Datenzunahme in den Diagrammen, wenn Personen Ihre App verwenden.


#### Keine Daten nach dem Veröffentlichen auf Ihrem Server?

Öffnen Sie diese Ports für den ausgehenden Datenverkehr in der Firewall des Servers:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


## Entwickeln, Testen und Freigeben

Bei größeren Anwendungen ist es ratsam, Telemetriedaten von verschiedenen Stempeln (Debuggen, Testen und Produktionsbuilds) an [getrennte Ressourcen](app-insights-separate-resources.md) zu senden.

## Nachverfolgen der Anwendungsversion

Stellen Sie sicher, dass `buildinfo.config` von Ihrem Buildprozess generiert wird. Fügen Sie in Ihrer CSPROJ-Datei Folgendes hinzu:

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Wenn das Webmodul Application Insights über die Buildinformationen verfügt, fügt es jedem Telemetrieelement automatisch die **Anwendungsversion** als Eigenschaft hinzu. Dies ermöglicht es Ihnen, nach Version zu filtern, wenn Sie [Diagnosesuchen][diagnostic] ausführen oder [Metriken untersuchen][metrics].



## Hinzufügen der Nachverfolgung von Abhängigkeiten und von Systemleistungsindikatoren

[Abhängigkeitsmetriken](app-insights-dependencies.md) können sehr nützlich sein, um Leistungsprobleme zu diagnostizieren. Sie messen Aufrufe von Ihrer App an Datenbanken, REST-APIs und andere externe Komponenten.

![](./media/app-insights-asp-net/04-dependencies.png)

Dieser Schritt ermöglicht außerdem [Berichte zu Leistungsindikatoren](app-insights-web-monitor-performance.md#system-performance-counters) wie z. B. CPU, Arbeitsspeicher, Netzwerkauslastung.

#### Wenn Ihre App in IIS-Server ausgeführt wird

Melden Sie sich auf dem Server mit Administratorrechten an, und installieren Sie den [Application Insights-Statusmonitor](http://go.microsoft.com/fwlink/?LinkId=506648).

Sie müssen sicherstellen, dass einige [zusätzliche Ports in der Firewall des Servers geöffnet sind](app-insights-monitor-performance-live-website-now.md#troubleshooting).

#### Wenn Ihre App eine Azure-Web-App ist

Fügen Sie in der Systemsteuerung Ihrer Azure-Web-App die Application Insights-Erweiterung hinzu.

![In der Web-App: Einstellungen, Erweiterungen, Hinzufügen, Application Insights](./media/app-insights-asp-net/05-extend.png)

(Die Erweiterung unterstützt nur Apps, die mit dem SDK erstellt wurden. Im Gegensatz zum Statusmonitor kann sie keine vorhandene App instrumentieren.)

#### So überwachen Sie Azure Cloud Services-Rollen

Es gibt auch ein [manuelles Verfahren zum Hinzufügen des Statusmonitors](app-insights-cloudservices.md).

## Verfügbarkeitswebtests

[Richten Sie Webtests ein][availability], um von außen zu testen, ob Ihre Anwendung aktiv ist und reagiert.


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






## So aktualisieren Sie auf zukünftige SDK-Versionen

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
[start]: app-insights-get-started.md

 

<!---HONumber=Sept15_HO4-->