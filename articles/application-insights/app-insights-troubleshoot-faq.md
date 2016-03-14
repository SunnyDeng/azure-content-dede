<properties 
	pageTitle="Problembehandlung und Fragen zu Application Insights" 
	description="Ist Ihnen irgendetwas in Visual Studio Application Insights unklar oder funktioniert nicht wie erwartet? Versuchen Sie es hier." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="awills"/>
 
# Fragen – Application Insights für ASP.NET

## Konfigurationsprobleme

*Ich benötige Hilfe beim Einrichten von Folgendem:*

* [.NET-App](app-insights-asp-net-troubleshoot-no-data.md)
* [Überwachen einer bereits ausgeführten App](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure-Diagnose](app-insights-azure-diagnostics.md)
* [Java-Web-Apps](app-insights-java-troubleshoot.md)
* [Andere Plattformen](app-insights-platforms.md)


## Kann ich Application Insights mit ... verwenden?

[Informationen hierzu finden Sie unter "Plattformen"][platforms]


## Ist es kostenlos?

* Ja, wenn Sie sich für den [Tarif](app-insights-pricing.md) "Free" entscheiden. Sie erhalten Zugriff auf die meisten Funktionen sowie ein großzügiges Datenkontingent. 
* Sie müssen Ihre Kreditkartendaten angeben, um sich bei Microsoft Azure registrieren zu können. Es werden jedoch keinerlei Gebühren abgebucht, sofern Sie keinen kostenpflichtigen Azure-Dienst nutzen oder explizit auf einen kostenpflichtigen Tarif aktualisieren.
* Wenn Ihre App mehr Daten sendet, als in Ihrem Kontingent für den Free-Tarif enthalten sind, wird die Protokollierung eingestellt. In diesem Fall können Sie zu einem kostenpflichtigen Tarif wechseln oder warten, bis das Kontingent am Monatsende zurückgesetzt wird.
* Daten für die grundlegende Nutzung sowie Sitzungsdaten unterliegen keinen Kontingenten.
* Es gibt auch eine kostenlose 30-Tage-Testversion, in der Sie die Premium-Funktionen kostenlos nutzen können.
* Jede Anwendungsressource verfügt über ein eigenes Kontingent, und Sie können den Tarif für jede Ressource unabhängig von den anderen festlegen.

#### Was beinhalten die kostenpflichtigen Tarife?

* Ein größeres [monatliches Datenkontingent](https://azure.microsoft.com/pricing/details/application-insights/).
* Eine Option zur "Überschreitung", um weiterhin Daten zu sammeln, obwohl das monatliche Kontingent erreicht ist. Wenn Ihre Daten das Kontingent überschreiten, wird Ihnen die Nutzung pro MB berechnet.
* [Fortlaufender Export](app-insights-export-telemetry.md)


## <a name="q14"></a>Welche Änderungen nimmt Application Insights in meinem Projekt vor?

Die Details hängen von der Art des Projekts ab. Für eine Webanwendung:


+ Fügen Sie folgende Dateien zu Ihrem Projekt hinzu:

 + ApplicationInsights.config
 + ai.js


+ Diese NuGet-Pakete werden installiert:

 -  *Application Insights API* - die Haupt-API

 -  *Application Insights API for Web Applications* - zum Senden von Telemetrie vom Server

 -  *Application Insights API for JavaScript Applications* - zum Senden von Telemetrie vom Client

    Die Pakete umfassen folgende Assemblys:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Fügt Elemente ein in:

 - Web.config

 - packages.config

+ (Nur neue Projekte – wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][start], müssen Sie dies manuell ausführen.) Fügen Sie Codeausschnitte in den Client- und Servercode ein, um diese mit der Application Insights-Ressourcen-ID zu initialisieren. Beispielsweise wird in einer MVC-App Code auf der Masterseite "Views/Shared/\_Layout.cshtml" eingefügt.


## Wie aktualisiere ich von älteren SDK-Versionen?

Informationen hierzu finden Sie in den [Versionshinweisen](app-insights-release-notes.md) für das SDK, das für Ihren Anwendungstyp geeignet ist.



## <a name="update"></a>Wie kann ich ändern, an welche Azure-Ressource mein Projekt Daten sendet?

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Sie können die Daten an eine vorhandene oder neue Ressource in Azure senden. Der Assistent ändert den Instrumentationsschlüssel in ApplicationInsights.config. Dadurch wird bestimmt, wohin das Server-SDK die Daten sendet. Wenn Sie "Alle aktualisieren" deaktivieren, wird auch der Anzeigeort des Schlüssels auf Ihren Webseiten geändert.


## <a name="q06"></a>Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?

Nein! Sie zeigt den Status des Azure-Diensts an. Um die Ergebnisse des Webtests anzuzeigen, wählen Sie "Durchsuchen > Application Insights > (Ihre Anwendung)" aus. Sehen Sie sich dann die Webtest-Ergebnisse an.



#### <a name="data"></a>Wie lange werden Daten im Portal aufbewahrt? Ist Sicherheit gewährleistet?

Informationen hierzu finden Sie unter [Datenaufbewahrung und Datenschutz][data].

## Protokollierung

#### <a name="post"></a>Wie zeige ich POST-Daten in der Diagnosesuche an?

POST-Daten werden nicht automatisch protokolliert, Sie können jedoch einen TrackTrace-Aufruf verwenden und die Daten in den Nachrichtenparameter einfügen. Die Größenbegrenzung hierfür ist höher als bei Zeichenfolgeneigenschaften, Sie können jedoch nicht danach filtern.

## Sicherheit

#### Sind meine Daten im Portal sicher? Wie lange werden sie aufbewahrt?

Informationen hierzu finden Sie unter [Datenaufbewahrung und Datenschutz][data].


## <a name="q17"></a> Habe ich alles in Application Insights aktiviert?

<table border="1">
<tr><th>Diese Daten sollten angezeigt werden</th><th>So erhalten Sie die Daten</th><th>Deshalb benötigen Sie die Daten</th></tr>
<tr><td>Verfügbarkeitsdiagramme</td><td><a href="../app-insights-monitor-web-app-availability/">Webtests</a></td><td>Information, ob Ihre Web-App verfügbar ist</td></tr>
<tr><td>Leistung der Server-App: Antwortzeiten usw. ...
</td><td><a href="../app-insights-asp-net/">Hinzufügen von Application Insights zu Ihrem Projekt</a><br/>oder <br/><a href="../app-insights-monitor-performance-live-website-now/">Installieren Sie den AI-Statusmonitor auf dem Server</a> (oder schreiben Sie Ihren eigenen Code in die <a href="../app-insights-api-custom-events-metrics/#track-dependency">Nachverfolgung von Aufrufen</a>)</td><td>Erkennen von Leistungsproblemen</td></tr>
<tr><td>Telemetriedaten zu Abhängigkeiten</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installieren des AI-Statusmonitors auf dem Server</a></td><td>Diagnostizieren von Problemen mit Datenbanken oder anderen externen Komponenten</td></tr>
<tr><td>Abrufen von Stapelüberwachungen aus Ausnahmen</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Einfügen von TrackException-Aufrufen in Ihren Code</a> (einige werden jedoch automatisch gemeldet)</td><td>Erkennen und Diagnostizieren von Ausnahmen</td></tr>
<tr><td>Durchsuchen von Ablaufprotokollen</td><td><a href="../app-insights-search-diagnostic-logs/">Hinzufügen eines Protokollierungsadapters</a></td><td>Diagnostizieren von Ausnahmen und Leistungsproblemen</td></tr>
<tr><td>Grundlegende Clientnutzung: Seitenansichten, Sitzungen, ...</td><td><a href="../app-insights-javascript/">JavaScript-Initialisierer in Webseiten</a></td><td>Nutzungsanalyse</td></tr>
<tr><td>Benutzerdefinierte Metriken auf Clients</td><td><a href="../app-insights-api-custom-events-metrics/">Nachverfolgen von Aufrufen in Webseiten</a></td><td>Verbessern der Benutzerfreundlichkeit</td></tr>
<tr><td>Benutzerdefinierte Metriken auf Servern</td><td><a href="../app-insights-api-custom-events-metrics/">Nachverfolgen von Aufrufe im Servercode</a></td><td>Business Intelligence</td></tr>
</table>

Wenn Ihr Webdienst auf einem virtuellen Azure-Computer ausgeführt wird, erhalten Sie dort auch eine [Diagnose][azurediagnostic].

## Automation

Sie können [PowerShell-Skripts schreiben](app-insights-powershell.md), um Application Insights-Ressourcen zu erstellen und aktualisieren.

## Weitere Antworten

* [Application Insights-Forum](https://social.msdn.microsoft.com/Forums/vstudio/de-DE/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0302_2016-->