<properties 
	pageTitle="Problembehandlung bei Application Insights auf Windows-Geräten" 
	description="Anleitung zur Problembehandlung sowie Fragen und Antworten." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Anleitung zur Problembehandlung sowie Fragen und Antworten zu Application Insights für Windows-Geräte

Fragen zu oder Probleme mit [Visual Studio Application Insights in Windows][windows]? Hier sind einige Tipps.



## Keine Daten 

*Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, sehe aber keine Daten im Portal.*

* Warten Sie eine Minute, und klicken Sie auf "Aktualisieren". Derzeit gibt es keine automatische Aktualisierung.
* Stellen Sie sicher, dass Sie einen Instrumentationsschlüssel in der Datei "ApplicationInsights.config" definiert haben und dass dieser mit dem Schlüssel im Application Insights-Portal identisch ist. Um den Schlüssel anzuzeigen, klicken Sie auf dem Blatt "Übersicht" auf "Essentials".
* Stellen Sie sicher, dass Ihre App [ausgehenden Netzwerkzugriff anfordert](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx).
* Gibt es eine Firewall zwischen dem Emulator oder Testgerät und dem Application Insights-Portal? Sie müssen möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.
* Schauen Sie sich auf der Startseite von Microsoft Azure die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.


#### Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.

* Überprüfen Sie den [Statusblog](http://blogs.msdn.com/b/applicationinsights-status/).
* Ist Ihr monatliches Kontingent an Datenpunkten erreicht? Öffnen Sie "Einstellungen – Kontingente und Preisübersicht", um es herauszufinden. Sie können in diesem Fall Ihren Plan aktualisieren oder zusätzliche Kapazität erwerben. Informationen hierzu finden Sie in der [Preisübersicht](http://azure.microsoft.com/pricing/details/application-insights/).


## Wie füge ich Application Insights einer universellen App hinzu?

Fügen Sie die NuGet-Pakete jedem Geräteprojekt in der Projektmappe manuell hinzu. Informationen hierzu finden Sie unter [Erste Schritte – Universelle Apps][universal].

## Deaktivieren der Telemetrie

*Wie kann ich die Telemetrieerfassung deaktivieren?*

Im Code:

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## Ändern der Zielressource

*Wie kann ich ändern, an welche Application Insights-Ressource mein Projekt Daten sendet?*

Auf dem neuen Blatt "Übersicht" in Application Insights öffnen Sie "Essentials" und kopieren den Instrumentationsschlüssel.

Fügen Sie den Schlüssel in der Datei "applicationinsights.config" im `<InstrumentationKey>`-Knoten ein.

Wenn Sie hingegen Ziele zur Laufzeit ändern möchten, verwenden Sie:

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## Wie überwache ich eine Client/Server-App?

Hierfür gibt es zwei Möglichkeiten:

* Erstellen Sie zwei Application Insights-Ressourcen (mit verschiedenen Instrumentationsschlüsseln) für den Client und für den Server. Erstellen Sie sie jedoch in derselben Azure-Ressourcengruppe. So können Sie einfach zwischen den beiden wechseln.
* Verwenden Sie eine Application Insights-Ressource, und legen Sie den Instrumentationsschlüssel sowohl im Client- als auch im Serverprojekt ab. Anschließend können Sie Metriken und Ereignisse aus den beiden Datenquellen zueinander in Beziehung setzen.

Um Ereignisse auf dem Client und auf dem Server einfacher in Beziehung setzen zu können, generieren Sie für jede Anforderung eine Vorgangs-ID. Übertragen Sie sie zwischen dem Client und dem Server, und fügen Sie sie auf beiden Seiten der Telemetrie hinzu:

    telemetry.Context.OperationId = opid;


## Der Azure-Startbildschirm

*Ich sehe mir das [Azure-Portal](http://portal.azure.com) an. Teilt mir die Karte etwas über meine App mit?*

Nein, sie zeigt die Integrität der Azure-Server auf der ganzen Welt.

*Wie finde ich im Azure-Startmenü (Startbildschirm) Daten über meine App?*

Wenn Sie Ihre App bereits für [Application Insights eingerichtet haben][windows], klicken Sie auf "Durchsuchen". Wählen Sie "Application Insights" und dann die Ressource aus, die Sie für Ihre App erstellt haben. Um in Zukunft schneller dorthin zu gelangen, können Sie Ihre Ressource an das Startmenü anheften.

## Beibehaltung von Daten 

*Wie lange werden Daten im Portal aufbewahrt? Ist Sicherheit gewährleistet?*

Informationen hierzu finden Sie unter [Datenaufbewahrung und Datenschutz][data].

## Nächste Schritte

*Ich habe Application Insights für meine Java-Server-App eingerichtet. Was kann ich sonst noch tun?*

* [Überwachen der Verfügbarkeit von Webseiten][availability]
* [Überwachen der Nutzung von Webseiten][usage]
* [Nachverfolgen der Nutzung und Diagnostizieren von Problemen in Ihren Geräte-Apps][platforms]
* [Schreiben von Code zum Nachverfolgen der Verwendung Ihrer App][track]
* [Erfassen von Diagnoseprotokollen][javalogs]


## Abrufen von Hilfe

* [Stapelüberlauf](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO3-->