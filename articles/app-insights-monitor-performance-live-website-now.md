<properties title="Diagnose performance issues on a running website" pageTitle="Diagnostizieren von Leistungsproblemen auf einer ausgeführten Website" description="Überwachen Sie die Leistung einer Website, ohne sie erneut bereitzustellen." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 

# Überwachen der Leistung auf einer Live-Website

*Application Insights befindet sich in der Vorschauphase.*

Haben Sie ein Problem mit dem Verhalten einer Webanwendung? Sie können Ausnahmen und Leistungsprobleme schnell und einfach diagnostizieren, ohne die Anwendung neu erstellen oder bereitstellen zu müssen. Installieren Sie den Application Insights-Agenten auf dem Server, um Leistungsengpässe zu suchen und Stapelüberwachungen für beliebige Ausnahmen zu erhalten. 


#### Wann sollte ich Application Insights mit dieser Methode einrichten?

Dieser Ansatz dient zur schnellen Diagnose von Leistungsproblemen in einer Live-IIS-Website.

Sie installieren einfach einen Agenten auf dem Server und können Leistungsdaten in Application Insights anzeigen.

- Diese Methode funktioniert für ASP.NET-Anwendungen in IIS-Servern.

- Sie benötigen ein [Microsoft Azure-Konto](http://azure.com), um Ihre Daten anzuzeigen.

- Sie benötigen Administratorzugriff auf dem Server, auf dem Ihre Webanwendung läuft. 

- Sie brauchen *keinen* Zugriff auf den Code der Webanwendung, und Sie müssen die Anwendung nicht neu erstellen oder bereitstellen. 

- Diese Methode verwendet die Anwendung in ihrem aktuellen Zustand. Sie fügen keinen Nachverfolgungs- oder Protokollierungscode ein. (Sie können dies jedoch bei Bedarf später tun.)

Wenn Sie Protokollierung oder Ablaufverfolgung konfigurieren möchten, sind Sie hier falsch. Fügen Sie stattdessen [Application Insights Ihrem Projekt hinzu][greenbrown], und stellen Sie das Projekt neu bereit. Lesen Sie für die vollständige Palette von Optionen [Application Insights - erste Schritte][start].

## Installieren des Application Insights-Agenten auf Ihrem Webserver

1. Melden Sie sich mit Administratorrechten auf Ihrem Webserver an.

2. Stellen Sie sicher, dass Sie mindestens Version 5.0 des [Webplattform-Installers](http://www.microsoft.com/web/downloads/platform.aspx) installiert haben.
3. Installieren Sie den Application Insights-Agenten über den Webplattform-Installer.

    ![](./media/appinsights/appinsights-031-wpi.png)
4. Melden Sie sich im Installations-Assistenten bei Microsoft Azure an.

    ![](./media/appinsights/appinsights-035-signin.png)
5. Wählen Sie die installierte Webanwendung oder Website aus, die Sie überwachen möchten, und konfigurieren Sie den Namen, unter dem die Ergebnisse im Application Insights-Portal angezeigt werden sollen. Klicken Sie zuletzt auf "Hinzufügen".

    ![](./media/appinsights/appinsights-036-configAIC.png)

    Normalerweise sollten Sie eine neue Ressource erstellen.

    Sie können jedoch auch vorhandene Ressourcen verwenden, wenn Sie z. B. bereits [Webtests][availability] für Ihre Website eingerichtet haben.  

6. Beachten Sie, dass die Datei ApplicationInsights.config in die Websites eingefügt wurde, die Sie überwachen möchten.

    ![](./media/appinsights/appinsights-034-aiconfig.png)

   Außerdem wurden Änderungen an der Datei web.config vorgenommen.

### Möchten Sie die Konfiguration später ändern?

Nach Abschluss des Assistenten können Sie die Konfiguration des Agenten später jederzeit ändern. Sie können dies auch verwenden, wenn bei der ursprünglichen Einrichtung des Agenten ein Problem aufgetreten ist.

![Click the Application Insights icon on the task bar](./media/appinsights/appinsights-033-aicRunning.png)

## Prüfen der Daten

Öffnen Sie Ihr Konto in Azure, navigieren Sie zu Application Insights und öffnen Sie die zuvor erstellte Ressource.

![](./media/appinsights/appinsights-08openApp.png)

Unter Anwendungszustand werden Daten angezeigt.

![](./media/appinsights/appinsights-037-results.png)

#### Sie sehen keine Daten?

  * Verwenden Sie Ihre Website, um einige Daten zu generieren.
  * Warten Sie einige Minuten, bis die Daten eintreffen.
  * Stellen Sie sicher, dass die Firewall Ihres Servers ausgehenden Datenverkehr auf Port 443 an dc.services.visualstudio.com erlaubt 

#### Welche Funktion haben die Application Health-Berichte?
 * [Grundlegendes zu Daten und Konfigurieren der Kacheln][perf]

## <a name="next"></a>Nächste Schritte


[Anzeigen der Daten][perf]

[Durchsuchen der Diagnoseprotokolle][diagnostic]

[Webtests][availability]

[Nutzungsüberwachung einrichten][start]

[Problembehandlung][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



