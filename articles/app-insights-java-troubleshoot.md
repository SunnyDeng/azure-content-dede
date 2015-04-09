<properties 
	pageTitle="Problembehandlung bei Application Insights in einem Java-Webprojekt" 
	description="Anleitung zur Problembehandlung sowie Fragen und Antworten." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Anleitung zur Problembehandlung sowie Fragen und Antworten zu Application Insights für Java

Fragen oder Probleme mit [Visual Studio Application Insights in Java][java]? Hier sind einige Tipps.


## Buildfehler

*Wenn ich in Eclipse das Application Insights SDK über Maven oder Gradle hinzufüge, erhalte ich Build- oder Prüfsummenvalidierungsfehler.*

* Wenn das Abhängigkeitselement <version> ein Muster mit Platzhalterzeichen verwendet (z. B. <Version> [0.9,) </version>), geben Sie stattdessen eine bestimmte Version wie <version>0.9.1</version> an.

## Keine Daten 

*Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, sehe aber keine Daten im Portal.*

* Warten Sie eine Minute, und klicken Sie auf "Aktualisieren". Derzeit gibt es keine automatische Aktualisierung.
* Prüfen Sie, ob Sie in der Datei "ApplicationInsights.xml" (im Ordner "Ressourcen" Ihres Projekts) einen Instrumentationsschlüssel definiert haben.
* Stellen Sie sicher, dass es in der XML-Datei keinen Knoten `<DisableTelemetry>true</DisableTelemetry>` gibt.
* In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.
* Schauen Sie sich auf der Startseite von Microsoft Azure die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
* Aktivieren Sie die Protokollierung im IDE-Konsolenfenster durch Hinzufügen eines <SDKLogger/>-Elements unter dem Stammknoten in der Datei "ApplicationInsights.xml" (im Ordner "Ressourcen" Ihres Projekts), und suchen Sie nach Einträgen, denen [Fehler] vorangestellt ist.


## Keine Nutzungsdaten

*Ich sehe Daten zu Anforderungen und Antwortzeiten, aber keine Seitenansicht, Browser- oder Benutzerdaten.*

Sie haben Ihre App erfolgreich so eingerichtet, dass Telemetriedaten vom Server gesendet werden. Jetzt ist der nächste Schritt das [Einrichten Ihrer Webseiten für das Senden von Telemetrie aus dem Webbrowser][usage].

Wenn der Client eine Anwendung auf einem [Smartphone oder anderem Gerät][platforms] ist, können Sie Telemetriedaten von dort aus senden. 

Verwenden Sie den gleichen Instrumentationsschlüssel zum Einrichten der Telemetrie auf Client und Server. Die Daten werden in der gleichen Application Insights-Ressource angezeigt und können Ereignisse von Client und Server korrelieren.



## Deaktivieren der Telemetrie

*Wie kann ich die Telemetrie-Erfassung deaktivieren?*

Im Code:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Oder** 

Aktualisieren Sie "ApplicationInsights.xml" (im Ordner "Ressourcen" in Ihrem Projekt). Fügen Sie unter dem Stammknoten Folgendes hinzu:

    <DisableTelemetry>true</DisableTelemetry>

Mithilfe der XML-Methode müssen Sie die Anwendung neu starten, nachdem Sie den Wert geändert haben.

## Ändern des Ziels

*Wie kann ich ändern, an welche Azure-Ressource mein Projekt Daten sendet?*

* [Rufen Sie den Instrumentationsschlüssel der neuen Ressource ab.][java]
* Wenn Sie Ihrem Projekt Application Insights mit dem Azure-Toolkit für Eclipse hinzugefügt haben, klicken Sie mit der rechten Maustaste auf das Webprojekt. Wählen Sie erst **Azure** und dann **Application Insights konfigurieren** aus, und ändern Sie den Schlüssel.
* Aktualisieren Sie den Schlüssel andernfalls in "ApplicationInsights.xml" im Ordner "Ressourcen" in Ihrem Projekt.


## Der Azure-Startbildschirm

*Ich sehe mir das [ Azure-Portal](http://portal.azure.com) an. Teilt mir die Karte etwas über meine Anwendung mit?*

Nein, sie zeigt die Integrität der Azure-Server auf der ganzen Welt.

*Wie finde ich im auf der Azure-Startseite Daten über meine App?*

Wenn Sie [Ihre App für Application Insights eingerichtet haben][java], klicken Sie auf "Durchsuchen". Wählen Sie "Application Insights" und dann die App-Ressource aus, die Sie für Ihre Anwendung erstellt haben. Um in Zukunft schneller dorthin zu gelangen, können Sie Ihre App an die Startseite anheften.

## Intranetserver

*Kann ich einen Server in meinem Intranet überwachen?*

Ja, sofern Ihr Server über das öffentliche Internet Telemetriedaten an das Application Insights-Portal senden kann. 

In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.

## Beibehaltung von Daten 

*Wie lange werden Daten im Portal beibehalten? Ist Sicherheit gewährleistet?*

Siehe [Datenaufbewahrung und Datenschutz][data].

## Nächste Schritte

*Ich habe Application Insights für meine Java-Server-App eingerichtet. Was kann ich sonst noch tun?*

* [Überwachen der Verfügbarkeit von Webseiten][availability]
* [Überwachen der Nutzung von Webseiten][usage]
* [Nachverfolgen der Nutzung und Diagnostizieren von Problemen in Ihren Geräteanwendungen][platforms]
* [Schreiben von Code zum Nachverfolgen der Verwendung Ihrer App][track]
* [Erfassen von Diagnoseprotokollen][javalogs]


## Abrufen von Hilfe

* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=49-->