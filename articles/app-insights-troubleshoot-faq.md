<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Problembehandlung sowie Fragen und Antworten - Application Insights in Microsoft Azure-Vorschau

-   [Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio][Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio]
-   [Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen][Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen]
-   [Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, aber ich sehe die Daten nicht im Portal][Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, aber ich sehe die Daten nicht im Portal]
-   [Ich sehe keine Daten in der Nutzungsanalyse][Ich sehe keine Daten in der Nutzungsanalyse]
-   [Ich befinde mich im Startboard der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?][Ich befinde mich im Startboard der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?]
-   [Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?][Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?]
-   [Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sieht es ganz anders aus als in den Screenshots][Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sieht es ganz anders aus als in den Screenshots]
-   [Kann ich Application Insights verwenden, um einen Intranet-Webserver zu überwachen?][Kann ich Application Insights verwenden, um einen Intranet-Webserver zu überwachen?]
-   [Wie kann ich Daten für Windows Phone oder Windows Store abrufen?][Wie kann ich Daten für Windows Phone oder Windows Store abrufen?]
-   [Wie sehe ich die Ereignisse und Seitenansichten, die ich in meinen Code eingegeben habe?][Wie sehe ich die Ereignisse und Seitenansichten, die ich in meinen Code eingegeben habe?]
-   [Warum gibt es zwei Versionen von Application Insights?][Warum gibt es zwei Versionen von Application Insights?]
-   [Was fehlt derzeit in der Azure-Version von Application Insights?][Was fehlt derzeit in der Azure-Version von Application Insights?]
-   [Wie bekomme ich die tollen Funktionen zurück, die ich in der Visual Studio Online-Version von Application Insights hatte?][Wie bekomme ich die tollen Funktionen zurück, die ich in der Visual Studio Online-Version von Application Insights hatte?]
-   [Welche Änderungen nimmt Application Insights in meinem Projekt vor?][Welche Änderungen nimmt Application Insights in meinem Projekt vor?]
-   [Wo finde ich meine Ergebnisse in Application Insights?][Wo finde ich meine Ergebnisse in Application Insights?]
-   [Nächste Schritte][Nächste Schritte]

## <a name="q01"></a>Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio

-   Stellen Sie sicher, dass Sie über [Visual Studio Update 3][Visual Studio Update 3] verfügen. Es wird mit Application Insights Tools vorinstalliert, und Sie sollten es im Erweiterungs-Manager sehen können.
-   Application Insights ist in der Microsoft Azure-Vorschau derzeit nur für ASP.NET-Webprojekte in C# oder Visual Basic verfügbar.
-   Wenn Sie ein vorhandenes Projekt haben, klicken Sie im Projektmappen-Explorer auf das Webprojekt (auf kein anderes Projekt oder die Projektmappe). Sie sollten eine Menüoption „Add Application Insights Telemetry to Project“ sehen.
-   Wenn Sie ein neues Projekt erstellen, öffnen Sie in Visual Studio „Datei \> Neues Projekt“, und wählen Sie „{Visual C#|Visual Basic} \> Web \> ASP.NET-Webanwendung“ aus. Dort sollte eine Option vorhanden sein, um Application Insights zum Projekt hinzuzufügen.

## <a name="q02"></a>Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen

Das kann vorkommen, wenn die Kommunikation mit dem Application Insights-Portal fehlgeschlagen ist oder wenn es ein Problem mit Ihrem Konto gibt.

-   Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben. Die Microsoft Azure-Anmeldeinformationen, die Sie im Dialogfeld „Neues Projekt“ sehen, können sich von den Visual Studio Online-Anmeldeinformationen oben rechts in Visual Studio unterscheiden.
-   Warten Sie einen Moment, und dann [fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu][fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu].
-   Wechseln Sie zu den Microsoft Azure-Kontoeinstellungen und überprüfen Sie eventuelle Beschränkungen. Testen Sie, ob Sie eine Application Insights-Anwendung manuell hinzufügen können.

## <a name="q03"></a>Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, aber ich sehe die Daten nicht im Portal

-   Wenn Sie auf Daten warten, schließen und öffnen Sie das jeweilige Fenster erneut. In der aktuellen Version wird der Fensterinhalt nicht automatisch aktualisiert.
-   Schauen Sie sich im Startboard der Microsoft Azure-Vorschau die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder OK anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
-   Möglicherweise müssen Sie in der Firewall des Webservers Port 443 für ausgehenden Verkehr öffnen.

## <a name="q04"></a>Ich sehe keine Daten in der Nutzungsanalyse

-   Die Daten kommen von Skripts auf den Webseiten. Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen][fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu].

## <a name="q05"></a>Ich befinde mich im Startboard der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?

Entweder:

-   Wählen Sie „Durchsuchen“, „Application Insights“, Ihren Projektnamen aus. Wenn noch keine Projekte vorhanden sind, [fügen Sie Application Insights zu Ihrem Webprojekt in Visual Studio hinzu][fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu].

-   Klicken Sie im Visual Studio Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt, und wählen Sie „Open Application Insights Portal“ aus.

## <a name="q06"></a>Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?

Nein! Sie zeigt den Status des Azure-Diensts an. Um die Ergebnisse des Webtests anzuzeigen, wählen Sie „Durchsuchen \> Application Insights \> (Ihre Anwendung)“ aus, dann sehen Sie die Webtest-Ergebnisse.

## <a name="q07"></a>Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sieht es ganz anders aus als in den Screenshots

Sie verwenden möglicherweise eine ältere Version von Application Insights Tools, die mit der Visual Studio Online-Version verbunden sind.

Die Hilfeseiten dagegen beziehen sich auf Application Insights für die Microsoft Azure-Vorschau, das bereits mit Visual Studio Update 3 läuft.

## <a name="q08"></a>Kann ich Application Insights verwenden, um einen Intranet-Webserver zu überwachen?

Ja, Sie können den Status und die Nutzung überwachen, wenn Ihr Server Daten an das öffentliche Internet senden kann.

Wenn Sie jedoch Webtests für den Dienst ausführen möchten, muss dieser über das öffentliche Internet zugänglich sein.

## <a name="q10"></a>Wie sehe ich die Ereignisse und Seitenansichten, die ich in meinen Code eingegeben habe?

Dies wird in der Microsoft Azure-Version noch nicht unterstützt. Es ist in Kürze verfügbar. Vorläufig könnten Sie es mit der [älteren Version][älteren Version] versuchen.

## <a name="q11"></a>Warum gibt es zwei Versionen von Application Insights?

Das ältere Portal ist Teil von Visual Studio Online. Wir nehmen keine erheblichen Änderungen mehr an dieser Version vor. Wenn Sie eine ältere Version von Application Insights Tools für Visual Studio verwenden, sind diese mit dem Visual Studio Online-Portal verbunden.

Visual Studio Update 3 wird mit einer vorinstallierten Version der neuen Application Insights Tools geliefert. Diese sind mit dem neuen Application Insights-Portal verbunden, einer Komponente der Microsoft Azure-Vorschau. Wir übertragen derzeit Application Insights in diese neue Umgebung. Die Arbeiten sind noch nicht abgeschlossen, daher gibt es eine Reihe von Einschränkungen.

## <a name="q12"></a>Was fehlt derzeit in der Azure-Version von Application Insights?

Es sind einige großartige Funktionen geplant.

Derzeit fehlen jedoch hauptsächlich: Unterstützung für Geräte-Apps wie Windows Phone und Windows Store; Berichte für benutzerdefinierte Telemetrie wie `trackEvent()` und `trackPageView()`.

## <a name="q13"></a>Wie bekomme ich alle Funktionen zurück, die ich in der Visual Studio Online-Version von Application Insights hatte?

1.  Wechseln Sie zum Erweiterungs-Manager von Visual Studio.
2.  Deinstallieren Sie Application Insights Tools.
3.  Führen Sie das [Installationsprogramm für die ältere Version der Tools][Installationsprogramm für die ältere Version der Tools] aus, und lesen Sie den [Leitfaden für die ersten Schritte][älteren Version].

## <a name="q14"></a>Welche Änderungen nimmt Application Insights in meinem Projekt vor?

+ Fügen Sie folgende Dateien zu Ihrem Projekt hinzu:

 + ApplicationInsights.config
 + ai.js

+ Diese NuGet-Pakete werden installiert:

 -  *Application Insights API* - die Haupt-API

 -  *Application Insights API for Web Applications* - zum Senden von Telemetrie vom Server

 -  *Application Insights API for JavaScript Applications* - zum Senden von Telemetrie vom Client

    Die Pakete umfassen folgende Assemblys:

 -  Microsoft.ApplicationInsights

 -  Microsoft.ApplicationInsights.Platform

+ (Nur neue Projekte – wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu], müssen Sie dies manuell ausführen.) Fügen Sie Codeausschnitte in den Client- und Servercode ein, um ihn mit der Application Insights Ressourcen-ID zu initialisieren. Beispielsweise wird in einer MVC-Anwendung Code an folgenden Stellen eingefügt:

 - „Views/Shared/\_Layout.cshtml“ auf der Masterseite

 - Web.config

 - packages.config

## <a name="q15"></a>Wo finde ich meine Ergebnisse in Application Insights?

1.  Öffnen Sie Microsoft Azure:

 - Klicken Sie in Visual Studio mit der rechten Maustaste auf das Webanwendungsprojekt, und wählen Sie **Open Azure Preview Portal** aus.
 - Oder öffnen Sie in einem Webbrowser Ihr Konto in der Microsoft Azure-Vorschau.

2.  Wählen Sie „Durchsuchen“, „Application Insights“, Ihr Projekt aus.

## <a name="next"></a>Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu]
-   [Überwachen eines Live-Webservers][Überwachen eines Live-Webservers]
-   [Erkunden von Metriken in Application Insights][Erkunden von Metriken in Application Insights]
-   [Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]
-   [Verfügbarkeitsüberwachung mit Webtests][Verfügbarkeitsüberwachung mit Webtests]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Fragen und Antworten sowie Problembehandlung]

<!--Link references-->

  [Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio]: #q01
  [Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen]: #q02
  [Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, aber ich sehe die Daten nicht im Portal]: #q03
  [Ich sehe keine Daten in der Nutzungsanalyse]: #q04
  [Ich befinde mich im Startboard der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?]: #q05
  [Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?]: #q06
  [Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sieht es ganz anders aus als in den Screenshots]: #q07
  [Kann ich Application Insights verwenden, um einen Intranet-Webserver zu überwachen?]: #q08
  [Wie kann ich Daten für Windows Phone oder Windows Store abrufen?]: #q09
  [Wie sehe ich die Ereignisse und Seitenansichten, die ich in meinen Code eingegeben habe?]: #q10
  [Warum gibt es zwei Versionen von Application Insights?]: #q11
  [Was fehlt derzeit in der Azure-Version von Application Insights?]: #q12
  [Wie bekomme ich die tollen Funktionen zurück, die ich in der Visual Studio Online-Version von Application Insights hatte?]: #q13
  [Welche Änderungen nimmt Application Insights in meinem Projekt vor?]: #q14
  [Wo finde ich meine Ergebnisse in Application Insights?]: #q15
  [Nächste Schritte]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu]: ../app-insights-monitor-application-health-usage/
  [älteren Version]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [Installationsprogramm für die ältere Version der Tools]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [Überwachen eines Live-Webservers]: ../app-insights-monitor-performance-live-website-now/
  [Erkunden von Metriken in Application Insights]: ../app-insights-explore-metrics/
  [Durchsuchen der Diagnoseprotokolle]: ../app-insights-search-diagnostic-logs/
  [Verfügbarkeitsüberwachung mit Webtests]: ../app-insights-monitor-web-app-availability/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-track-usage-custom-events-metrics/
  [Fragen und Antworten sowie Problembehandlung]: ../app-insights-troubleshoot-faq/
