<properties 
	pageTitle="Diagnostizieren von Leistungsproblemen auf einer ausgeführten Website" 
	description="Überwachen Sie die Leistung einer Website, ohne sie erneut bereitzustellen. Verwenden von eigenständigen oder mit Application Insights-SDK Abhängigkeit Telemetrie abgerufen." 
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
 

# Installieren Sie Application Insights Status-Monitor zum Überwachen der Websiteleistung

*Application Insights befindet sich in der Vorschau.*

Der Status-Monitor von Visual Studio Application Insights können Sie die diagnose von Ausnahmen und Leistungsproblemen in Webanwendungen, die in einem IIS-Server ausgeführt. Installieren Sie es auf dem IIS-Webserver und wird es dort gefundenen Senden von Daten an Application Insights-Portal zu durchsuchen und Analysieren ASP.NET Web-apps instrumentieren. Sie können es auf jedem physischen oder virtuellen Server installieren, auf die Sie Administratorzugriff haben.

![Beispiel-Diagramme](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Sie können zwischen drei Möglichkeiten zum Anwenden von Application Insights auf die IIS-Webanwendungen:

* **Buildzeit:** [Hinzufügen von Application Insights-SDK][greenbrown] zum Web app-Code. So erhalten Sie:
 * Eine Reihe von standardmäßigen Telemetrie für Diagnose- und Nutzung. 
 * Und Sie können die [Application Insights API][api] Wenn zum Schreiben Ihrer eigenen Telemetrie zum Nachverfolgen der Verwendung oder Probleme zu diagnostizieren.
* **Laufzeit:** Status-Monitor zum Instrumentieren Ihrer Webanwendung auf dem Server verwenden.
 * Überwachen von Webanwendungen, die bereits ausgeführt werden: muss nicht neu erstellen oder veröffentlichen Sie sie erneut.
 * Eine Reihe von standardmäßigen Telemetrie für Diagnose- und Nutzung.
 * Abhängigkeit Diagnose – suchen Sie Fehler oder eine schlechte Leistung, bei denen Ihre Anwendung andere Komponenten wie z. B. Datenbanken, REST-APIs oder andere Dienste verwendet.
 * Beheben Sie alle Probleme mit Telemetrie.
* **Sowohl:** das SDK in Ihrem Web-app-Code kompilieren und -Überwachung auf dem Webserver ausgeführt. Das beste aus beiden Welten:
 * Standardmäßigen Telemetrie für Diagnose- und Nutzung.
 * Diagnose der Abhängigkeit.
 * Schreiben Sie benutzerdefinierten Telemetrie mithilfe der API.
 * Beheben Sie alle Probleme mit dem SDK und Telemetrie.



> [AZURE.TIP]Ist Ihre app ein [Anwendungsdiensts Azure Web App](../app-service-web/websites-learning-map.md)? [Application Insights-SDK hinzufügen][greenbrown] und [Fügen Sie die Application Insights-Erweiterung](../insights-perf-analytics.md) über die Systemsteuerung für die Anwendung in Azure.


## Installieren Sie Application Insights Status Monitor, auf dem IIS-Webserver

1. Sie müssen eine [Microsoft Azure](http://azure.com) Abonnement. 

1. Auf dem IIS-Webserver, melden Sie sich mit Administratoranmeldeinformationen.
2. Herunterladen und Ausführen der [Statusmonitor Installer](http://go.microsoft.com/fwlink/?LinkId=506648).

4. Melden Sie sich im Installations-Assistenten bei Microsoft Azure an.

    ![Melden Sie sich mit Ihren Microsoft-Anmeldeinformationen in Azure](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. Wählen Sie das installierte Webanwendung oder Website, die Sie überwachen möchten, und konfigurieren Sie die Ressource aus, in der die Ergebnisse in Application Insights-Portal angezeigt werden soll.

    ![Wählen Sie eine app und einer Ressource.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    In der Regel sollten Sie die Konfiguration einer neuen Ressource und [Ressourcengruppe][roles].

    Verwenden Sie eine vorhandene Ressource, wenn Sie bereits eingerichtet [Webtests][availability] für Ihre Website oder [WebClient Überwachung][client].

6. Starten Sie IIS neu.

    ![Wählen Sie am oberen Rand des Dialogfelds Neustart.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Der Webdienst wird für kurze Zeit unterbrochen.

6. Beachten Sie, dass die Datei "applicationinsights.config" in der Web-apps eingefügt wurde, die Sie überwachen möchten.

    ![Finden Sie die config-Datei zusammen mit den Code-Dateien der Webanwendung.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Außerdem wurden Änderungen an der Datei web.config vorgenommen.

### Möchten Sie die Konfiguration später ändern?

Nach Abschluss des Assistenten können Sie die Konfiguration des Agenten später jederzeit ändern. Sie können dies auch verwenden, wenn bei der ursprünglichen Einrichtung des Agenten ein Problem aufgetreten ist.

![Klicken Sie auf das Application Insights-Symbol in der Taskleiste](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Die Leistung Telemetrie anzeigen

Melden Sie sich bei [Azure-vorschauportal](http://portal.azure.com), Application Insights durchsuchen, und öffnen Sie die Ressource, die Sie erstellt haben.

![Wählen Sie durchsuchen, Application Insights, und wählen Sie dann Ihre app](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Öffnen Sie das Fenster "Leistung" um Abhängigkeiten und andere Daten finden Sie unter.

![Leistung](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Klicken Sie auf Diagramme um weitere Details anzuzeigen.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### Abhängigkeiten

Die Diagramme mit der Bezeichnung HTTP, SQL, AZUREBLOB zeigen die Antwortzeiten und die Anzahl der Aufrufe von Abhängigkeiten: d. h. externe Dienste, die Ihre Anwendung verwendet.



#### Leistungsindikatoren

Klicken Sie auf alle leistungsindikatordiagramm zum Ändern der Anzeige. Oder Sie können ein neues Diagramm hinzufügen.
 
#### Ausnahmen

![Klicken Sie sich durch das Diagramm des Server-Ausnahmen](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Sie können Detailinformationen für bestimmte Ausnahmen (aus der letzten sieben Tage) und Stapelüberwachung und Kontextdaten.


### Keine Telemetrie?

  * Verwenden Sie Ihre Website, um einige Daten zu generieren.
  * Warten Sie einige Minuten, damit die Daten empfangen können, und klicken Sie dann auf aktualisieren.
  * Öffnen Sie die Diagnose suchen (Search-Kachel) um einzelne Ereignisse anzuzeigen. Ereignisse werden häufig in diagnostische Suche angezeigt, bevor Aggregieren von Daten in den Diagrammen angezeigt wird.
  * Öffnen Sie Status-Monitor, und wählen Sie Ihre Anwendung auf der linken Seite. Überprüfen Sie, ob es Diagnosemeldungen für diese Anwendung im Abschnitt "Konfiguration von Benachrichtigungen gibt":
  
  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Stellen Sie sicher, dass die Serverfirewall ausgehenden Datenverkehr an Port 443 auf dc.services.visualstudio.com zulässt. 
  * Wenn auf dem Server eine Meldung über "unzureichende Berechtigungen" angezeigt wird:
  * Wählen Sie im IIS-Manager den Anwendungspool, erweiterte Einstellungen öffnen Sie und notieren Sie die Identität, unter Modell verarbeiten.
  * Fügen Sie in der Systemsteuerung des Computers Management diese Identität der Gruppe Systemmonitorbenutzer hinzu.
  * Informationen hierzu finden Sie unter [Problembehandlung][qna].

## Systemanforderungen

Unterstützte Betriebssysteme für Application Insights Status Monitor auf Server:

- WindowsServer 2008
- Windows Server 2008 R2
- Windows Server 2012
- WindowsServer 2012 R2

mit den neuesten Service Packs und .NET Framework 4.0 und 4.5

Auf der Clientseite Windows 7, 8 und 8.1 erneut mit .NET Framework 4.0 und 4.5

IIS-Unterstützung ist: IIS 7 und 7.5, 8, 8.5 (IIS erforderlich ist)

## <a name="next"></a>Nächste Schritte

* [Erstellen von Webtests][availability] sicherstellen, dass Ihre Website live bleibt.
* [Durchsuchen Sie Ereignisse und Protokolle][diagnostic], um Probleme besser zu diagnostizieren.
* [Web clienttelemetrie hinzufügen][usage] Ausnahmen von Webseitencode und Ablaufverfolgungsaufrufe einfügen können.
* [Hinzufügen von Application Insights-SDK auf Ihrem Webdienstcode][greenbrown] damit Sie die Ablaufverfolgung einfügen können und Protokoll im Servercode aufruft.

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

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->