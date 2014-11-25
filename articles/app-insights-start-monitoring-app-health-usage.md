<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights – Beginnen Sie damit, Integrität und Nutzung Ihrer Anwendung zu überwachen

*Application Insights befindet sich in der Vorschau.*

Mit Application Insights können Sie Ihre Live-Anwendung auf folgende Parameter hin überwachen:

-   **Verfügbarkeit** – Wir testen Ihre URLs im Abstand von wenigen Minuten von weltweiten Standorten aus.
-   **Leistung** – Probleme und Ausnahmen hinsichtlich der Leistung werden erkannt und diagnostiziert.
-   **Nutzung** – Stellen Sie fest, wie Benutzer mit Ihrer Anwendung umgehen, damit Sie die Anwendung verbessern können.

Die Konfiguration ist sehr einfach und liefert nach wenigen Minuten Resultate. Derzeit unterstützen wir ASP.NET-Webanwendungen (auf Ihrem eigenen Server oder auf Azure).

Sie benötigen ein Konto für [Microsoft Azure](http://azure.com) (es gibt eine kostenlose Testperiode).

Es gibt zwei Möglichkeiten, um Application Insights einzusetzen:

-   [Application Insights Ihrem App-Projekt in Visual Studio hinzufügen](#add), um die Leistung und Nutzung der Anwendung zu überwachen (empfohlen).
-   [Agent ohne erneute Bereitstellung auf Ihrem Server installieren][redfield] – Eine Live-Website überwachen, ohne Ihren Quellcode neu bereitzustellen oder zu verändern. Mit dieser Option können Sie Leistung und Ausnahmefehler überwachen. Eine Nutzungsüberwachung können Sie später hinzufügen.

> [WACOM.NOTE] Es gibt eine [ältere Version von Application Insights](http://msdn.microsoft.com/de-de/library/dn481095.aspx)in Visual Studio Online, die eine größere Palette von Anwendungen unterstützt. Wir entwickeln sie von Grund auf neu als Teil von Microsoft Azure, und über diese neue Version informieren Sie sich gerade.

## <a name="add"></a>Application Insights Ihrem Projekt hinzufügen

Sie benötigen [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (oder höher).

### Falls es sich um ein neues Projekt handelt …

Wenn Sie in Visual Studio 2013 ein neues Projekt erstellen, achten Sie darauf, dass Application Insights ausgewählt ist.

![Erstellen eines ASP.NET-Projekts](./media/appinsights/appinsights-01-vsnewp1.png)

Falls Sie erstmals ein Projekt erstellen, werden Sie nach einer Anmeldung bei Microsoft Azure Preview gefragt. (Dies ist nicht zu verwechseln mit Ihrem Visual Studio Online-Konto.)

Falls Sie der Azure-Ressource einen anderen Namen als ihrem Projekt zuweisen möchten oder die Ressource in derselben Gruppe wie ein anderes Projekt erscheinen soll, verwenden Sie **Einstellungen konfigurieren**.

*Keine Option "Application Insights" vorhanden? Prüfen Sie, ob Sie Visual Studio 2013 Update 3 verwenden, ob Application Insights-Tools unter Erweiterungen und Updates aktiviert wurden und Sie ein Web-, Windows Store- oder Windows Phone-Projekt erstellen.*

### … oder falls es sich um ein vorhandenes Projekt handelt

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Application Insights hinzufügen".

!["Application Insights hinzufügen" wählen](./media/appinsights/appinsights-03-addExisting.png)

*Zur Einrichtung von Webnutzungsanalysen ist es noch ein weiterer Schritt, aber lassen Sie uns zunächst einige Daten betrachten …*

### <a name="run"></a>2. Ausführen des Projekts

Starten Sie Ihre Anwendung mit F5, und probieren Sie sie aus – öffnen Sie verschiedene Seiten.

In Visual Studio sehen Sie eine Anzahl der empfangenen Ereignisse.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Anzeigen von Überwachungsdaten

Öffnen Sie Application Insights von Ihrem Projekt aus.

![Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und öffnen Sie das Azure-Portal](./media/appinsights/appinsights-04-openPortal.png)

Suchen Sie Daten in den **Application health**-Tiles. Zuerst sehen Sie lediglich einen oder zwei Punkte. Beispiel:

![Zu mehr Daten durchklicken](./media/appinsights/appinsights-41firstHealth.png)

Klicken Sie auf ein Tile, um weitere Details anzuzeigen. Sie können die in den Berichten angezeigten Daten ändern. [Erfahren Sie mehr über das Konfigurieren von Application Health-Berichten.][perf]

### <a name="webclient"></a>4. Einrichten der Webnutzungsanalysen

Falls Sie Application Insights einem *vorhandenen* Web-Projekt hinzugefügt haben, wird Ihnen in den Nutzungsanalyse-Tiles noch nichts angezeigt. Es ist noch ein weiterer Schritt erforderlich.

Wählen Sie in Application Insights "Schnellstart", "Instrumentieren Ihrer Webseite".

![In Ihrem Projekt in Application Insights auf "Schnellstart", "Instrumentieren Ihrer Webseite" klicken und den Code kopieren](./media/appinsights/appinsights-06webcode.png)

Fügen Sie den JavaScript-Code auf den zu überwachenden Webseiten direkt vor dem schließenden Tag \</head\> ein. In einem ASP.NET-Projekt besteht eine gute Methode zum Überwachen aller Webseiten darin, den Code in die Hauptseite zu kopieren – normalerweise mit `_SiteLayout` oder `Views\Shared\_Layout` benannt. Beachten Sie, dass der Code den Application Insights-Schlüssel Ihrer Anwendung enthält.

Starten Sie Ihre Anwendung erneut. Daraufhin werden unter **Nutzungsanalyse** Daten angezeigt.

![Zu mehr Daten durchklicken](./media/appinsights/appinsights-05-usageTiles.png)

[Durch Diagramme klicken, um weitere Details anzuzeigen.][perf]

### <a name="deploy"></a>5. Bereitstellen Ihrer Anwendung

Stellen Sie die Anwendung bereit, und lassen Sie die Daten ansammeln.

Sobald Sie über eine Live-Anwendung verfügen, [richten Sie Webtests ein][availability], um sicherzustellen, dass sie live bleibt.

![Beispiel: Anwendungsüberwachung in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### Möchten Sie den Namen Ihrer Anwendung im Portal ändern?

Sie können die Ressource ändern, an die Ihr Projekt Telemetriedaten sendet.

(Die 'Ressource' ist das benannte Application Insights-Fenster, in dem Ihre Ergebnisse erscheinen. Sie können mehr als eine Ressource in einer Gruppe anordnen – beispielsweise, wenn Sie über mehrere Projekte verfügen, die Teil einer Geschäftsanwendung sind.)

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf "ApplicationInsights.config", und wählen Sie **Aktualisieren von Application Insights**. Dadurch wird ein Assistent geöffnet, in dem Sie eine andere vorhandene Ressource wählen oder eine neue erstellen können.

Wechseln Sie danach zurück zum Portal, und löschen Sie die alte Ressource.

## <a name="video"></a>Videos

### Einführung

> [AZURE.VIDEO Application-Insights-Einführung]

### Erste Schritte

> [AZURE.VIDEO Erste-Schritte-mit-Application-Insights]

## <a name="next"></a>Nächste Schritte

[Nutzung Ihrer Webanwendung nachverfolgen][usage]

[Leistung in Webanwendungen überwachen][perf]

[Diagnoseprotokolle anlegen und durchsuchen][diagnostic]

[Problembehandlung][qna]

## Weitere Informationen

-   [Application Insights – erste Schritte][start]
-   [Live-Webserver jetzt überwachen][redfield]
-   [Leistung in Webanwendungen überwachen][perf]
-   [Diagnoseprotokolle durchsuchen][diagnostic]
-   [Nachverfolgung der Verfügbarkeit mit Webtests][availability]
-   [Nutzung nachverfolgen][usage]
-   [Fragen und Antworten, Problembehandlung][qna]

<!--Link references-->

[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/

