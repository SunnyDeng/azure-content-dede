<properties 
	pageTitle="Problembehandlung und Fragen und Antworten zu Application Insights" 
	description="Ist Ihnen etwas unklar, oder funktioniert etwas nicht? Versuchen Sie es hier." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="awills"/>
 
# Problembehandlung sowie Fragen und Antworten - Application Insights in Microsoft Azure-Vorschau

+ [Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio](#q01)
+ [Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen](#q02)
+ [Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, aber ich sehe die Daten nicht im Portal](#q03)
+ [Ich sehe keine Daten in der Nutzungsanalyse](#q04)
+ [Ich befinde mich im Startboard der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?](#q05)
+ [Wie kann ich die Azure-Ressource ändern, unter der meine Daten angezeigt werden?](#update)
+ [Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.](#emptykey)
+ [Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?](#q06)
+ [Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sieht es ganz anders aus als in den Screenshots](#q07)
+ [Kann ich Application Insights verwenden, um einen Intranet-Webserver zu überwachen?](#q08)
+ [Wie kann ich Daten für Windows Phone oder Windows Store abrufen?](#q09)
+ [Wie sehe ich die Ereignisse und Seitenansichten, die ich in meinen Code eingegeben habe?](#q10)
+ [Warum gibt es zwei Versionen von Application Insights?](#q11)
+ [Wie bekomme ich die Funktionen zurück, die ich in der Visual Studio Online-Version von Application Insights hatte?](#q13)
+ [Welche Änderungen nimmt Application Insights in meinem Projekt vor?](#q14)
+ [Wo finde ich meine Ergebnisse in Application Insights?](#q15)
+ [Welche Ports muss ich in meiner Firewall öffnen?](#q16)
+ [Habe ich alles in Application Insights aktiviert?](#q17)
+ [Weitere Informationen](#next)



## <a name="q01"></a>Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio

+ Stellen Sie sicher, dass Sie über [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827) verfügen. Es wird mit Application Insights Tools vorinstalliert, und Sie sollten es im Erweiterungs-Manager sehen können.
+ Application Insights ist in der Microsoft Azure-Vorschau derzeit nur für ASP.NET-Webprojekte in C# oder Visual Basic verfügbar.
+ Wenn Sie ein vorhandenes Projekt haben, klicken Sie im Projektmappen-Explorer auf das Webprojekt (auf kein anderes Projekt oder die Projektmappe). Sie sollten eine Menüoption "Add Application Insights Telemetry to Project" sehen.
+ Wenn Sie ein neues Projekt erstellen, öffnen Sie in Visual Studio "Datei > Neues Projekt", und wählen Sie "{Visual C#|Visual Basic} > Web > ASP.NET-Webanwendung" aus. Dort sollte eine Option vorhanden sein, um Application Insights zum Projekt hinzuzufügen.

## <a name="q02"></a>Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen

Das kann vorkommen, wenn die Kommunikation mit dem Application Insights-Portal fehlgeschlagen ist oder wenn es ein Problem mit Ihrem Konto gibt.

+ Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben. Die Microsoft Azure-Anmeldeinformationen, die Sie im Dialogfeld "Neues Projekt" sehen, können sich von den Visual Studio Online-Anmeldeinformationen oben rechts in Visual Studio unterscheiden.
+ Warten Sie einen Moment, und [fügen Sie dann Application Insights zu Ihrem vorhandenen Projekt hinzu][start].
+ Wechseln Sie zu den Microsoft Azure-Kontoeinstellungen und überprüfen Sie eventuelle Beschränkungen. Testen Sie, ob Sie eine Application Insights-Anwendung manuell hinzufügen können.


## <a name="q03"></a>Ich habe Application Insights erfolgreich hinzugefügt und meine Anwendung ausgeführt, aber ich sehe die Daten nicht im Portal

+ Wenn Sie auf Daten warten, schließen und öffnen Sie das jeweilige Fenster erneut. In der aktuellen Version wird der Fensterinhalt nicht automatisch aktualisiert.
+ Schauen Sie sich im Startboard der Microsoft Azure-Vorschau die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder OK anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
+ In Ihrer Firewall müssen Sie möglicherweise TCP-Ports 80 und 443 für den ausgehenden Datenverkehr zu dc.services.visualstudio.com und f5.services.visualstudio.com öffnen.

## <a name="q04"></a>Ich sehe keine Daten in der Nutzungsanalyse

+ Die Daten kommen aus Skripts auf den Webseiten. Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen][start].


## <a name="q05"></a>Ich befinde mich im Startboard der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?

Entweder:

* Wählen Sie "Durchsuchen", "Application Insights" und Ihren Projektnamen aus. Wenn noch keine Projekte vorhanden sind, [fügen Sie Application Insights zu Ihrem Webprojekt in Visual Studio hinzu][start].

* Klicken Sie im Visual Studio Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt, und wählen Sie "Open Application Insights Portal" aus.

## <a name="update"></a>Wie kann ich ändern, an welche Azure-Ressource mein Projekt Daten sendet?

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Aktualisieren von Application Insights** aus. Sie können die Daten an eine vorhandene oder neue Ressource in Azure senden. Der Assistent ändert den Instrumentationsschlüssel in ApplicationInsights.config. Dadurch wird bestimmt, wohin das Server-SDK die Daten sendet. Wenn Sie "Alle aktualisieren" deaktivieren, wird auch der Anzeigeort des Schlüssels auf Ihren Webseiten geändert.

## <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Es scheint ein Fehler aufgetreten zu sein, während Sie Application Insights oder vielleicht einen Protokollierungsadapter installiert haben.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Aktualisieren von Application Insights** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt.

## <a name="q06"></a>Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?

Nein! Sie zeigt den Status des Azure-Diensts an. Um die Ergebnisse des Webtests anzuzeigen, wählen Sie "Durchsuchen > Application Insights > (Ihre Anwendung)" aus. Sehen Sie sich dann die Webtest-Ergebnisse an. 


## <a name="q07"></a>Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sieht es ganz anders aus als in den Screenshots

Sie verwenden möglicherweise eine ältere [Version von Application Insights Tools](http://msdn.microsoft.com/library/dn793604.aspx), die mit der Visual Studio Online-Version verbunden sind.

Die Hilfeseiten dagegen beziehen sich auf [Application Insights für die Microsoft Azure-Vorschau][start], das bereits mit Visual Studio Update 3 läuft. 

## <a name="q08"></a>Kann ich Application Insights verwenden, um einen Intranet-Webserver zu überwachen?

Ja, Sie können den Status und die Nutzung überwachen, wenn Ihr Server Daten an das öffentliche Internet senden kann.

Wenn Sie jedoch Webtests für den Dienst ausführen möchten, muss dieser über das öffentliche Internet zugänglich sein.

## <a name="q09"></a>Wie kann ich Daten für Windows Phone oder Windows Store abrufen?

Noch nicht in der Microsoft Azure-Version unterstützt. Verwenden Sie die [Visual Studio Online-Version][older].


## <a name="q10"></a>Wie sehe ich die Ereignisse und Seitenansichten, die ich in meinen Code eingegeben habe?

Dies wird in der Microsoft Azure-Version noch nicht unterstützt. Es ist in Kürze verfügbar. Vorläufig könnten Sie es mit der [älteren Version][older] versuchen.


## <a name="q11"></a>Warum gibt es zwei Versionen von Application Insights?

Das ältere Portal ist Teil von Visual Studio Online. Wir nehmen keine erheblichen Änderungen mehr an dieser Version vor. Wenn Sie eine ältere Version von Application Insights Tools für Visual Studio verwenden, sind diese mit dem Visual Studio Online-Portal verbunden.

Visual Studio Update 3 wird mit einer vorinstallierten Version der neuen Application Insights Tools geliefert. Diese sind mit dem neuen Application Insights-Portal verbunden, einer Komponente der Microsoft Azure-Vorschau. Wir übertragen derzeit Application Insights in diese neue Umgebung. Die Arbeit ist noch nicht vollständig.

## <a name="q13"></a>Wie bekomme ich alle Funktionen zurück, die ich in der Visual Studio Online-Version von Application Insights hatte?

1. Wechseln Sie zum Erweiterungs-Manager von Visual Studio. 
2. Deinstallieren Sie Application Insights Tools.
3. Führen Sie das [Installationsprogramm für die ältere Version der Tools](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) aus, und lesen Sie den [Leitfaden für die ersten Schritte][older].

## <a name="q14"></a>Welche Änderungen nimmt Application Insights in meinem Projekt vor?

Die Details hängen von der Art des Projekts ab. Für eine Webanwendung: +


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

+ (Nur neue Projekte - wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][start], müssen Sie dies manuell ausführen.) Fügen Sie Codeausschnitte in den Client- und Servercode ein, um ihn mit der Application Insights Ressourcen-ID zu initialisieren. Beispielsweise wird in einer MVC-Anwendung Code in die Hauptseite Views/Shared/_Layout.cshtml eingefügt.


## <a name="q15"></a>Wo finde ich meine Ergebnisse in Application Insights?
1. Öffnen Sie Microsoft Azure:
 - Klicken Sie in Visual Studio mit der rechten Maustaste auf das Webanwendungsprojekt, und wählen Sie **Open Azure Preview Portal** aus.
 - Oder öffnen Sie in einem Webbrowser Ihr Konto in der Microsoft Azure-Vorschau.

2. Wählen Sie "Durchsuchen", "Application Insights", Ihr Projekt aus.

## <a name="q16"></a>Eine Firewall befindet sich zwischen meinem Server oder Entwicklercomputer und dem öffentlichen Internet. Welchen Datenverkehr sollte ich zulassen, um Application Insights zu aktivieren?

Leistungs- und Verwendungsdaten werden an TCP-Ports 80 und 443 auf dc.services.visualstudio.com und f5.services.visualstudio.com gesendet.

Wenverfügbarkeitstests richten sich nach den eingehenden Zugriff auf Ihren Webserver auf Port 80.

## <a name="q17"></a> Habe ich alles in Application Insights aktiviert?

<table border="1">
<tr><th>Was Sie sehen sollten</th><th>Wie Sie es erhalten</th><th>Warum Sie es benötigen</th></tr>
<tr><td>Verfügbarkeitsdiagramme</td><td><a href="../app-insights-monitor-web-app-availability/">Webtests</a></td><td>Wissen, dass die Webapp aktiv ist</td></tr>
<tr><td>Serveranwendungsleistung: Antwortzeiten, ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Application Insights zum Projekt hinzufügen</a><br/> oder <br/><a href="../app-insights-monitor-performance-live-website-now/">AI-Statusmonitor auf Server installieren</a></td><td>Leistungsprobleme erkennen</td></tr>
<!-- ####future#### <tr><td>Abhängigkeitstelemetrie</td><td><a href="../app-insights-monitor-performance-live-website-now/">AI-Statusmonitor auf Server installieren</a></td><td>Probleme mit Datenbanken oder anderen externen Komponenten diagnostizieren</td></tr> -->
<!-- #####74.1#### <tr><td>Global für Server: CPU, Arbeitsspeicher, ... </td><td><a href="../app-insights-monitor-performance-live-website-now/">AI-Statusmonitor auf Server installieren</a></td><td>Kapazitätsprobleme diagnostizieren</td></tr> --> 
<tr><td>Protokollablaufverfolgungen suchen</td><td><a href="../app-insights-search-diagnostic-logs/">Protokollierungsadapter hinzufügen</a></td><td>Ausnahmen, Leistungsprobleme diagnostizieren</td></tr>
<tr><td>Grundlagen der Clientnutzung: Seitenaufrufe, Rückgaben, ... </td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">JavaScript-Initialisierer auf Webseiten</a></td><td>Nutzungsanalyse</td></tr>
<tr><td>Benutzerdefinierte Clientmetriken</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Aufrufe auf Webseiten nachverfolgen</a></td><td>Benutzererfahrung verbesserm</td></tr>
<tr><td>Benutzerdefinierte Servermetriken</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Aufrufe im Servercode nachverfolgen</a></td><td>Business Intelligence</td></tr>
</table>

Wenn Ihr Webdienst auf einem virtuellen Azure-Computer ausgeführt wird, erhalten Sie dort auch eine [Diagnose][azurediagnostic].



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


<!--HONumber=46--> 
