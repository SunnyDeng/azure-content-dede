<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

<!-- Required end of Sprint 69 - AUX48 -->

# Überwachen der Leistung auf einer Live-Website

*Application Insights befindet sich in der Vorschauphase.*

Haben Sie ein Problem mit dem Verhalten einer Webanwendung? Sie können Ausnahmen und Leistungsprobleme schnell und einfach diagnostizieren, ohne die Anwendung neu erstellen oder bereitstellen zu müssen. Installieren Sie den Application Insights-Agenten auf dem Server, um Leistungsengpässe zu suchen und Stapelüberwachungen für beliebige Ausnahmen zu erhalten.

#### Wann sollte ich Application Insights mit dieser Methode einrichten?

Dieser Ansatz dient zur schnellen Diagnose von Leistungsproblemen in einer Live-IIS-Website.

Sie installieren einfach einen Agenten auf dem Server und können Leistungsdaten in Application Insights anzeigen.

-   Diese Methode funktioniert für ASP.NET-Anwendungen in IIS-Servern.

-   Sie benötigen ein [Microsoft Azure-Konto][Microsoft Azure-Konto], um Ihre Daten anzuzeigen.

-   Sie benötigen Administratorzugriff auf dem Server, auf dem Ihre Webanwendung läuft.

-   Sie brauchen *keinen* Zugriff auf den Code der Web App, und Sie müssen die App nicht neu erstellen oder bereitstellen.

-   Diese Methode verwendet die App in ihrem aktuellen Zustand. Sie fügen keinen Nachverfolgungs- oder Protokollierungscode ein. (Sie können dies jedoch bei Bedarf später tun.)

Wenn Sie Protokollierung oder Ablaufverfolgung konfigurieren möchten, sind Sie hier falsch. Fahren Sie stattdessen mit [Application Insights zu Ihrem Projekt hinzufügen][vorhanden] fort und stellen Sie das Projekt neu bereit.

## Installieren des Application Insights-Agenten auf Ihrem Webserver

1.  Melden Sie sich mit Administratorrechten auf Ihrem Webserver an.

2.  Stellen Sie sicher, dass Sie mindestens Version 5.0 des [Webplattform-Installers][Webplattform-Installers] installiert haben.
3.  Installieren Sie den Application Insights-Agenten über den Webplattform-Installer.

    ![][0]

4.  Melden Sie sich im Installations-Assistenten bei Microsoft Azure an.

    ![][1]

5.  Wählen Sie die installierte Webanwendung oder Website aus, die Sie überwachen möchten, und konfigurieren Sie den Namen, unter dem die Ergebnisse im Application Insights-Portal angezeigt werden sollen. Klicken Sie zuletzt auf „Hinzufügen“.

    ![][2]

    Normalerweise sollten Sie eine neue Ressource erstellen.

    Sie können jedoch auch vorhandene Ressourcen verwenden, wenn Sie z. B. bereits [Web-Tests][Web-Tests] für Ihre Website eingerichtet haben.

6.  Beachten Sie, dass die Datei ApplicationInsights.config in die Websites eingefügt wurde, die Sie überwachen möchten.

    ![][3]

Außerdem wurden Änderungen an der Datei web.config vorgenommen.

### Möchten Sie die Konfiguration später ändern?

Nach Abschluss des Assistenten können Sie die Konfiguration des Agenten später jederzeit ändern. Sie können dies auch verwenden, wenn bei der ursprünglichen Einrichtung des Agenten ein Problem aufgetreten ist.

![Klicken Sie auf das Application Insights-Symbol in der Taskleiste][Klicken Sie auf das Application Insights-Symbol in der Taskleiste]

## Prüfen der Daten

Öffnen Sie Ihr Konto in Azure, navigieren Sie zu Application Insights und öffnen Sie die zuvor erstellte Ressource.

![][4]

Unter Anwendungszustand werden Daten angezeigt.

![][5]

#### Sie sehen keine Daten?

-   Verwenden Sie Ihre Website, um einige Daten zu generieren.
-   Warten Sie einige Minuten, bis die Daten eintreffen.
-   Stellen Sie sicher, dass die Firewall Ihres Servers ausgehenden Datenverkehr auf Port 443 an dc.services.visualstudio.com erlaubt

## <a name="next"></a>Nächste Schritte

[Anzeigen der Daten][Anzeigen der Daten]

[Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]

[Webtests][Web-Tests]

[Nutzungsüberwachung einrichten][Nutzungsüberwachung einrichten]

[Problembehandlung][Problembehandlung]

## Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][Nutzungsüberwachung einrichten]
-   [Überwachen eines Live-Webservers][Überwachen eines Live-Webservers]
-   [Erkunden von Metriken in Application Insights][Anzeigen der Daten]
-   [Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]
-   [Verfügbarkeitsüberwachung mit Webtests][Web-Tests]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Problembehandlung]

<!--Link references-->

  [Microsoft Azure-Konto]: http://azure.com
  [Webplattform-Installers]: http://www.microsoft.com/web/downloads/platform.aspx
  [0]: ./media/appinsights/appinsights-031-wpi.png
  [1]: ./media/appinsights/appinsights-035-signin.png
  [2]: ./media/appinsights/appinsights-036-configAIC.png
  [Web-Tests]: ../app-insights-monitor-web-app-availability/
  [3]: ./media/appinsights/appinsights-034-aiconfig.png
  [Klicken Sie auf das Application Insights-Symbol in der Taskleiste]: ./media/appinsights/appinsights-033-aicRunning.png
  [4]: ./media/appinsights/appinsights-08openApp.png
  [5]: ./media/appinsights/appinsights-037-results.png
  [Anzeigen der Daten]: ../app-insights-explore-metrics/
  [Durchsuchen der Diagnoseprotokolle]: ../app-insights-search-diagnostic-logs/
  [Nutzungsüberwachung einrichten]: ../app-insights-monitor-application-health-usage/
  [Problembehandlung]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Überwachen eines Live-Webservers]: ../app-insights-monitor-performance-live-website-now/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-track-usage-custom-events-metrics/
