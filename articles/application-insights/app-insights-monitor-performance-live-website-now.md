<properties 
	pageTitle="Diagnostizieren von Leistungsproblemen auf einer ausgeführten Website" 
	description="Überwachen Sie die Leistung einer Website, ohne sie erneut bereitzustellen. Zur eigenständigen Verwendung oder in Verbindung mit dem Application Insights-SDK zum Abrufen von Telemetriedaten zu Abhängigkeiten." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 

# Installieren des Application Insights-Statusmonitors zum Überwachen der Websiteleistung

*Application Insights befindet sich in der Vorschau.*

Mit dem Statusmonitor von Visual Studio Application Insights können Sie Ausnahmen und Leistungsprobleme in Webanwendungen diagnostizieren, die auf einem IIS-Server ausgeführt werden. Installieren Sie ihn einfach auf dem IIS-Webserver, und er nutzt die dort gefundenen ASP.NET-Web-Apps und sendet Daten an das Application Insights-Portal, die Sie durchsuchen und analysieren können. Er kann auf jedem physischen oder virtuellen Server installiert werden, auf den Sie Administratorzugriff besitzen.

![Beispieldiagramme](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Bei der Anwendung von Application Insights auf die IIS-Webanwendungen können Sie zwischen drei Möglichkeiten wählen:

* **Buildzeit:** [Fügen Sie Ihrem Web-App-Code das Application Insights-SDK hinzu][greenbrown]. Dadurch erhalten Sie Folgendes:
 * Eine Reihe von Standardtelemetriedaten zur Diagnose und Nutzung. 
 * Sie können zudem die [Application Insights-API][api] verwenden, wenn Sie eigene Telemetriedaten zum Nachverfolgen der Nutzung oder zum Diagnostizieren von Problemen schreiben möchten.
* **Laufzeit:** Verwenden Sie den Statusmonitor zum Instrumentieren Ihrer Web-App auf dem Server.
 * Überwachen von Web-Apps, die bereits ausgeführt werden. Es ist nicht erforderlich, sie neu zu erstellen oder erneut zu veröffentlichen.
 * Eine Reihe von Standardtelemetriedaten zur Diagnose und Nutzung.
 * Abhängigkeitsdiagnose – Ermitteln Sie Fehler oder eine schlechte Leistung, wenn Ihre Anwendung andere Komponenten wie z. B. Datenbanken, REST-APIs oder andere Dienste verwendet.
 * Beheben von Problemen mit der Telemetrie.
* **Beides:** Kompilieren Sie das SDK in Ihrem Web-App-Code, und führen Sie den Statusmonitor auf Ihrem Webserver aus. Die Vorteile beider Ansätze:
 * Standardtelemetriedaten zur Diagnose und Nutzung.
 * Abhängigkeitsdiagnose.
 * Schreiben benutzerdefinierter Telemetriedaten mithilfe der API.
 * Beheben von Problemen mit dem SDK und der Telemetrie.



> [AZURE.TIP]Ist Ihre App eine [Azure App Service-Web-App](../app-service-web/websites-learning-map.md)? [Fügen Sie das Application Insights-SDK hinzu][greenbrown], und fügen Sie dann [die Application Insights-Erweiterung](../insights-perf-analytics.md) über die Systemsteuerung der App in Azure hinzu.


## Installieren des Application Insights-Statusmonitors auf dem IIS-Webserver

1. Sie benötigen ein [Microsoft Azure](http://azure.com)-Abonnement. 

1. Melden Sie sich mit Administratorrechten auf Ihrem IIS-Webserver an.
2. Laden Sie den [Statusmonitor-Installer](http://go.microsoft.com/fwlink/?LinkId=506648) herunter, und starten Sie ihn.

4. Melden Sie sich im Installations-Assistenten bei Microsoft Azure an.

    ![Melden Sie sich mit den Anmeldeinformationen für ihr Microsoft-Konto bei Azure an.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. Wählen Sie die installierte Webanwendung oder Website aus, die Sie überwachen möchten, und konfigurieren Sie die Ressource, in der die Ergebnisse im Application Insights-Portal angezeigt werden sollen.

    ![Wählen Sie eine App und eine Ressource.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    In der Regel sollten Sie eine neue Ressource und [Ressourcengruppe][roles] konfigurieren.

    Eine vorhandene Ressource würden Sie verwenden, wenn Sie bereits [Webtests][availability] für Ihre Website oder die [Webclientüberwachung][client] eingerichtet haben.

6. Starten Sie IIS neu.

    ![Wählen Sie im oberen Bereich des Dialogfelds "Neustart".](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Der Webdienst wird für kurze Zeit unterbrochen.

6. Beachten Sie, dass die Datei "ApplicationInsights.config" in die Web-Apps eingefügt wurde, die Sie überwachen möchten.

    ![Suchen Sie die CONFIG-Datei bei den Codedateien der Web-App.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Außerdem wurden Änderungen an der Datei web.config vorgenommen.

### Möchten Sie die Konfiguration später ändern?

Nach Abschluss des Assistenten können Sie die Konfiguration des Agenten später jederzeit ändern. Sie können dies auch verwenden, wenn bei der ursprünglichen Einrichtung des Agenten ein Problem aufgetreten ist.

![Klicken Sie auf das Application Insights-Symbol in der Taskleiste](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Anzeigen von Leistungstelemetriedaten

Melden Sie sich beim [Azure-Vorschauportal](http://portal.azure.com) an, navigieren Sie zu Application Insights, und öffnen Sie die zuvor erstellte Ressource.

![Wählen Sie "Durchsuchen", "Application Insights" und dann Ihre App aus.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Öffnen Sie das Blatt "Leistung", um Abhängigkeiten und andere Daten anzuzeigen.

![Leistung](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Klicken Sie sich durch ein beliebiges Diagramm, um weitere Details anzuzeigen.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### Abhängigkeiten

Die Diagramme mit der Bezeichnung HTTP, SQL, AZUREBLOB zeigen die Antwortzeiten und die Anzahl der Aufrufe von Abhängigkeiten, also externen Diensten, die von Ihrer Anwendung verwendet werden.



#### Leistungsindikatoren

Klicken Sie auf ein Leistungsindikatordiagramm, um dessen Anzeige zu ändern. Sie können auch ein neues Diagramm hinzufügen.
 
#### Ausnahmen

![Klicken Sie sich durch das Diagramm der Serverausnahmen](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Sie können Detailinformationen zu bestimmten Ausnahmen (der letzten sieben Tage) anzeigen sowie Stapelüberwachung und Kontextdaten abrufen.


### Keine Telemetriedaten?

  * Verwenden Sie Ihre Website, um einige Daten zu generieren.
  * Warten Sie einige Minuten, bis die Daten eintreffen, und klicken Sie dann auf "Aktualisieren".
  * Öffnen Sie die "Diagnosesuche" (die Kachel "Search"), um einzelne Ereignisse anzuzeigen. Ereignisse werden häufig in der Diagnosesuche angezeigt, bevor Aggregatdaten in den Diagrammen dargestellt werden.
  * Öffnen Sie den Statusmonitor, und wählen Sie Ihre Anwendung auf der linken Seite aus. Prüfen Sie, ob für diese Anwendung Diagnosemeldungen im Abschnitt mit den Konfigurationsbenachrichtigungen vorliegen:
  
  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Stellen Sie sicher, dass die Firewall Ihres Servers auf Port 443 ausgehenden Datenverkehr an "dc.services.visualstudio.com" erlaubt. 
  * Wenn auf dem Server eine Meldung über "unzureichende Berechtigungen" angezeigt wird, gehen Sie folgendermaßen vor:
  * Wählen Sie im IIS-Manager den Anwendungspool aus, öffnen Sie "Erweiterte Einstellungen", und notieren Sie die Identität unter "Prozessmodell".
  * Fügen Sie in der Systemsteuerung "Computerverwaltung" diese Identität der Gruppe "Systemmonitorbenutzer" hinzu.
  * Informationen hierzu finden Sie unter [Problembehandlung][qna].

## Systemanforderungen

Betriebssystemunterstützung für den Application Insights-Statusmonitor auf dem Server:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

mit den neuesten Service Packs und .NET Framework 4.0 und 4.5

Auf der Clientseite: Windows 7, 8 und 8.1, ebenfalls mit .NET Framework 4.0 und 4.5

IIS-Unterstützung: IIS 7, 7.5, 8, 8.5 (IIS ist erforderlich)

## <a name="next"></a>Nächste Schritte

* [Erstellen Sie Webtests][availability], um sicherzustellen, dass Ihre Website live bleibt.
* [Durchsuchen Sie Ereignisse und Protokolle][diagnostic], um Probleme besser zu diagnostizieren.
* [Fügen Sie Webclienttelemetrie hinzu][usage], um Ausnahmen von Webseitencode anzuzeigen und Ablaufverfolgungsaufrufe einfügen zu können.
* [Fügen Sie Ihrem Webdienstcode das Application Insights-SDK hinzu][greenbrown], damit Sie die Ablaufverfolgungs- und Protokollaufrufe im Servercode einfügen können.

## Video

#### Leistungsüberwachung

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO4-->