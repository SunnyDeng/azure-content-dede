<properties 
	pageTitle="Erste Schritte mit Application Insights" 
	description="Analysieren Sie die Auslastung, Verfügbarkeit und Leistung Ihrer lokalen oder Microsoft Azure-Webanwendung mit Application Insights." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-10" 
	ms.author="awills"/>

# Application Insights - Beginnen Sie damit, Integrität und Nutzung Ihrer Anwendung zu überwachen.

*Application Insights befindet sich in der Vorschauphase.*

Mit Application Insights können Sie Ihre Live-Anwendung auf folgende Parameter hin überwachen:

* **Verfügbarkeit** - Wir testen Ihre URLs im Abstand von wenigen Minuten von weltweiten Standorten aus.
* **Leistung** - Probleme und Ausnahmen hinsichtlich der Leistung werden erkannt und diagnostiziert.
* **Nutzung** - Stellen Sie fest, wie Benutzer mit Ihrer Anwendung umgehen, damit Sie die Anwendung verbessern können.

Die Konfiguration ist sehr einfach und liefert nach wenigen Minuten Resultate. Derzeit unterstützen wir ASP.NET-Webanwendungen (auf Ihrem eigenen Server oder auf Azure).


## Erste Schritte

Beginnen Sie mit einer beliebigen Kombination aus Einstiegspunkten in beliebiger Reihenfolge auf der linken Seite des Diagramms. Wählen Sie die Routen aus, die für Sie funktionieren. Wenn Sie eine ASP.NET-Webanwendung entwickeln, starten Sie mit dem Hinzufügen von Application Insights zum Webprojekt - es ist einfach, die anderen Teile zu einem späteren Zeitpunkt hinzuzufügen.

Sie benötigen ein Konto in [Microsoft Azure](http://azure.com) (es sei denn, Sie verwenden die VSO-Version).

<table >
<tr valign="top"><th>Was benötigt wird</th><th colspan="2">Was zu tun ist</th><th>Was daraus entsteht</th></tr>
<tr valign="top"><td>Erhalt von Leistungs- und Nutzungsanalysen für die ASP.NET-App</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">Hinzufügen von Application Insights zum Webprojekt</a></td><td>Leistungsmetriken: Auslastungen, Antwortzeiten, ...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Senden von Ereignissen und Kennzahlen aus Ihrem Servercode</a></td><td>Benutzerdefinierte Geschäftsanalyse</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Senden von Ablaufverfolgungs- und Ausnahmetelemetriedaten von Ihrem Server oder Aufzeichnen von Protokolldaten von Dritten</td><td>Serveranwendungsdiagnose. Suchen und Filtern von Protokolldaten</a></td></tr>
<tr valign="top"><td>Abrufen der Nutzungsanalyse für Webseiten (auf allen Plattformen)</td><td colspan="2"><a href="../app-insights-web-track-usage/">Einfügen des AI-Skripts in Ihre Webseiten</a></td><td>Nutzungsanalyse: Seitenaufrufe, zurückkehrende Benutzer, Sitzungsanzahl</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Schreiben von Ereignis- und Metrikaufrufen in Ihren Webseitenskripts</a></td><td>Benutzerdefinierte Benutzererfahrungsanalysen</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">Schreiben von Ablaufverfolgungs und Diagnoseaufrufen in Ihren Webseitenskripts</a></td><td>Suchen und Filtern von Protokolldaten</td></tr>
<tr valign="top"><td>Diagnostizieren von Problemen in einer ASP.NET-Anwendung, die bereits auf dem Webserver ausgeführt wird</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">Installieren des Statusmonitors auf dem Webserver</a></td><td>Dauer und Anzahl von Abhängigkeitsaufrufen; CPU-, Arbeitsspeicher- und Netzwerk-Leistungsindikatoren; Auslastungen, Antwortzeiten</td></tr>
<tr valign="top"><td>Überwachen der Verfügbarkeit von Webseiten</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Einrichten von Webtests in Application Insights</a></td><td>Verfügbarkeitsüberwachung und -warnungen</td></tr>
<tr valign="top"><td>Abrufen von Leistungs- und Nutzungsanalysen für Windows Phone-Apps, Windows Store-Apps oder Java-Websites</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">Verwenden Sie vorerst die ältere VSO-Version von Application Insights</a></td><td>Nutzungs- und Leistungsanalyse. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Wir erstellen allmählich Funktionen in der Azure-Version.</a></td></tr>
</table>


## <a name="video"></a>Videos

#### Einführung

> [AZURE.VIDEO application-insights-introduction]

#### Erste Schritte

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
