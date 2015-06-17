<properties 
	pageTitle="Problembehandlung bei Application Insights in einem Java-Webprojekt" 
	description="Anleitung zur Problembehandlung sowie Fragen und Antworten." 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2015" 
	ms.author="awills"/>
 
# Problembehandlung und Fragen und Antworten für Application Insights für Java

Fragen oder Probleme mit [Visual Studio Application Insights in Java][java]? Hier sind einige Tipps.


## Buildfehler

*In Eclipse, wenn Sie das Application Insights-SDK über Maven oder Gradle hinzufügen erhalte ich Build oder Prüfsumme Validierungsfehler.*

* Wenn die Abhängigkeit <version> Element wird mithilfe eines Musters mit Platzhalterzeichen (z. B. (Maven) `<version>[0.9,)</version>` oder (Gradle) `version:'0.9.+'`), geben Sie eine bestimmte Version stattdessen wie `0.9.3`.

## Keine Daten 

*Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, sehe aber keine Daten im Portal.*

* Warten Sie eine Minute, und klicken Sie auf "Aktualisieren". Derzeit gibt es keine automatische Aktualisierung.
* Prüfen Sie, ob Sie in der Datei "ApplicationInsights.xml" (im Ordner "Ressourcen" Ihres Projekts) einen Instrumentationsschlüssel definiert haben.
* Stellen Sie sicher, dass es ist keine `<DisableTelemetry>true</DisableTelemetry>` Knoten in der XML-Datei.
* In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.
* Schauen Sie sich auf der Startseite von Microsoft Azure die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
* Aktivieren Sie Protokollierung im Konsolenfenster der IDE durch Hinzufügen einer `<SDKLogger />` Element unter dem Stammknoten in der ApplicationInsights.xml Datei (im Ordner "Ressourcen" im Projekt), und suchen Sie nach Einträgen vorangestellt [Fehler].
* Stellen Sie sicher, dass die richtige ApplicationInsights.xml-Datei anhand der Konsole ausgegebenen Meldungen für eine Anweisung "die Konfigurationsdatei wurde erfolgreich gefunden wurde" erfolgreich mit dem Java-SDK geladen wurde.
* Wenn die Config-Datei nicht gefunden wird, überprüfen Sie die Ausgabemeldungen angezeigt, in die Config-Datei gesucht wird, und stellen Sie sicher, dass die ApplicationInsights.xml in einem der beiden Search-Speicherorte befindet. Als Faustregel gilt können Sie die Config-Datei in der Nähe der Application Insights SDK JAR-Dateien platzieren. Beispiel: in Tomcat, würde dies den WEB-INF/Lib-Ordner.



#### Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.

* Überprüfen Sie die [Status Blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Haben Sie Ihre monatliche Kontingent von Datenpunkten erreicht? Öffnen Sie Einstellungen-Kontingent und Preise herausfinden. Ist dies der Fall ist, können Sie den Plan aktualisieren oder bezahlen für zusätzliche Kapazität. Siehe die [Preise Schema](http://azure.microsoft.com/pricing/details/application-insights/).



## Keine Nutzungsdaten

*Daten zu Anforderungen und Antwortzeiten, aber keine Seitenansicht, Browser oder Benutzerdaten angezeigt.*

Sie haben Ihre App erfolgreich so eingerichtet, dass Telemetriedaten vom Server gesendet werden. Jetzt der nächste Schritt ist [Richten Sie Ihre Webseiten Telemetrie vom Webbrowser senden][usage].

Alternativ ist der Client eine Anwendung in einer [Telefon oder einem anderen Gerät][platforms], können Sie von dort aus Telemetriedaten senden.

Verwenden Sie den gleichen Instrumentationsschlüssel zum Einrichten der Telemetrie auf Client und Server. Die Daten werden in der gleichen Application Insights-Ressource angezeigt und können Ereignisse von Client und Server korrelieren.



## Deaktivieren der Telemetrie

*Wie kann ich die Telemetrieerfassung deaktivieren?*

Im Code:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Oder**

Aktualisieren Sie "ApplicationInsights.xml" (im Ordner "Ressourcen" in Ihrem Projekt). Fügen Sie unter dem Stammknoten Folgendes hinzu:

    <DisableTelemetry>true</DisableTelemetry>

Mithilfe der XML-Methode müssen Sie die Anwendung neu starten, nachdem Sie den Wert geändert haben.

## Ändern des Ziels

*Wie kann ich ändern, welche Azure Resource Mein Projekt Daten sendet?*

* [Den instrumentationsschlüssel der neuen Ressource abzurufen.][java]
* Wenn Sie das Projekt unter Verwendung der Azure-Toolkit für Eclipse Application Insights hinzugefügt haben, klicken Sie mit der rechten Maustaste auf das Webprojekt, wählen Sie **Azure**, **Konfigurieren Sie Application Insights**, und ändern Sie den Schlüssel.
* Aktualisieren Sie den Schlüssel andernfalls in "ApplicationInsights.xml" im Ordner "Ressourcen" in Ihrem Projekt.


## Der Azure-Startbildschirm

*Ich sehe mir das [Azure-Portal](http://portal.azure.com) an. Teilt mir die Karte etwas über meine App mit?*

Nein, sie zeigt die Integrität der Azure-Server auf der ganzen Welt.

*Wie finde ich im Azure-Startmenü (Startbildschirm) Daten über meine App?*

Wenn Sie [Richten Sie Ihre app für Application Insights][java], klicken Sie auf Durchsuchen, wählen Sie Application Insights, und wählen Sie die app-Ressource, die Sie für Ihre Anwendung erstellt haben. Um in Zukunft schneller dorthin zu gelangen, können Sie Ihre App an die Startseite anheften.

## Intranetserver

*Kann einen Server für mein Intranet werden überwacht?*

Ja, sofern Ihr Server über das öffentliche Internet Telemetriedaten an das Application Insights-Portal senden kann.

In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.

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
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->