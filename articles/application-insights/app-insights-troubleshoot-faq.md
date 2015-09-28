<properties 
	pageTitle="Problembehandlung und Fragen zu Application Insights" 
	description="Ist Ihnen irgendetwas in Visual Studio Application Insights unklar oder funktioniert nicht wie erwartet? Versuchen Sie es hier." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2015" 
	ms.author="awills"/>
 
# Problembehandlung und Fragen zu Application Insights für ASP.NET

## Kann ich Application Insights mit ... verwenden?

[Informationen hierzu finden Sie unter "Plattformen"][platforms]

## Ist es kostenlos?

* Ja, wenn Sie sich für den [Tarif](app-insights-pricing.md) "Free" entscheiden. Sie erhalten Zugriff auf die meisten Funktionen sowie ein großzügiges Datenkontingent. 
* Sie müssen Ihre Kreditkartendaten angeben, um sich bei Microsoft Azure registrieren zu können. Es werden jedoch keinerlei Gebühren abgebucht, sofern Sie keinen kostenpflichtigen Azure-Dienst nutzen oder explizit auf einen kostenpflichtigen Tarif aktualisieren.
* Wenn Ihre App mehr Daten sendet, als in Ihrem Kontingent für den Free-Tarif enthalten sind, wird die Protokollierung eingestellt. In diesem Fall können Sie zu einem kostenpflichtigen Tarif wechseln oder warten, bis das Kontingent am Monatsende zurückgesetzt wird.
* Daten für die grundlegende Nutzung sowie Sitzungsdaten unterliegen keinen Kontingenten.
* Es gibt auch eine kostenlose 30-Tage-Testversion, in der Sie die Premium-Funktionen kostenlos nutzen können.
* Jede Anwendungsressource verfügt über ein eigenes Kontingent, und Sie können den Tarif für jede Ressource unabhängig von den anderen festlegen.

#### Was beinhalten die kostenpflichtigen Tarife?

* Ein größeres [monatliches Datenkontingent](http://azure.microsoft.com/pricing/details/application-insights/).
* Eine Option zur "Überschreitung", um weiterhin Daten zu sammeln, obwohl das monatliche Kontingent erreicht ist. Wenn Ihre Daten das Kontingent überschreiten, wird Ihnen die Nutzung pro MB berechnet.
* [Fortlaufender Export](app-insights-export-telemetry.md)

## Hinzufügen des SDK

#### <a name="q01"></a>Ich finde keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio

+ Stellen Sie sicher, dass Sie über [Visual Studio 2013 Update 3 oder höher](http://go.microsoft.com/fwlink/?LinkId=397827) verfügen. Dies ist bei den Application Insights-Tools vorinstalliert.
+ Obwohl die Tools nicht alle Anwendungstypen unterstützen, können Sie Ihrem Projekt wahrscheinlich manuell ein Application Insights SDK hinzufügen. Verwenden Sie [dieses Verfahren][windows]. 


#### <a name="q02"></a>Mein neues Webprojekt wurde erstellt, aber ich konnte Application Insights nicht hinzufügen.

Dies ist in folgenden Situationen möglich:

* Es liegt ein Fehler bei der Kommunikation mit dem Application Insights-Portal vor.
* Es gibt ein Problem mit Ihrem Konto.
* Sie verfügen nur über [Lesezugriff auf das Abonnement oder die Gruppe, in dem oder der Sie die neue Ressource erstellen möchten](app-insights-resources-roles-access-control.md).

Lösung:

+ Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben. In einigen früheren Versionen des Tools können sich die Microsoft Azure-Anmeldeinformationen, die Sie im Dialogfeld "Neues Projekt" sehen, von den Visual Studio Online-Anmeldeinformationen oben rechts in Visual Studio unterscheiden.
+ Überprüfen Sie in Ihrem Browser, ob Sie auf das [Azure-Portal](https://portal.azure.com) zugreifen können. Öffnen Sie "Einstellungen", und stellen Sie fest, ob eine Einschränkung besteht.
+ [Fügen Sie Application Insights einem vorhandenen Projekt hinzu][start]\: Klicken Sie mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie "Application Insights hinzufügen" aus.
+ Wenn es immer noch nicht funktioniert, führen Sie das [manuelle Verfahren](app-insights-start-monitoring-app-health-usage.md)aus, um eine Ressource im Portal hinzuzufügen, und fügen Sie anschließend das SDK zum Projekt hinzu. 

#### <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Es scheint ein Fehler aufgetreten zu sein, während Sie Application Insights oder vielleicht einen Protokollierungsadapter installiert haben.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt.


#### <a name="q14"></a>Welche Änderungen nimmt Application Insights in meinem Projekt vor?

Die Details hängen von der Art des Projekts ab. Für eine Webanwendung:


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

+ (Nur neue Projekte – wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][start], müssen Sie dies manuell ausführen.) Fügen Sie Codeausschnitte in den Client- und Servercode ein, um diese mit der Application Insights-Ressourcen-ID zu initialisieren. Beispielsweise wird in einer MVC-App Code auf der Masterseite "Views/Shared/\_Layout.cshtml" eingefügt.

####<a name="NuGetBuild"></a> Ich erhalte eine Fehlermeldung, dass auf meinem Buildserver NuGet-Pakete fehlen, obwohl die Erstellung auf meinen Entwicklungscomputern fehlerfrei funktioniert.

Informationen hierzu finden Sie unter [NuGet-Paketwiederherstellung](http://docs.nuget.org/Consume/Package-Restore) und [Automatische Paketwiederherstellung](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a> Ich erhalte eine Fehlermeldung, dass NuGet-Pakete mit Projektverweisen auf meinem Computer fehlen, wenn ich nach der Aktualisierung auf die NuGet-Paketversion 0.17 oder höher versuche, einen Build zu erstellen.

Wenn Sie diese Fehlermeldung nach der Aktualisierung auf die NuGet-Paketversion 0.17 oder höher erhalten, müssen Sie die Projektdatei bearbeiten und die noch vorhandenen BCL-Ziele entfernen.

Gehen Sie dazu folgendermaßen vor:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie "Projekt entladen" aus.
2. Klicken Sie erneut mit der rechten Maustaste auf das Projekt, und wählen Sie *yourProject.csproj* zur Bearbeitung aus. 
3. Entfernen Sie am Ende der Projektdatei die BCL-Ziele, die wie folgt aussehen: ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. Speichern Sie die Datei .
5. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie *yourProject.csproj* zum erneuten Laden aus.

## Wie aktualisiere ich von älteren SDK-Versionen?

Informationen hierzu finden Sie in den [Versionshinweisen](app-insights-release-notes.md) für das SDK, das für Ihren Anwendungstyp geeignet ist.


## Keine Daten

#### <a name="q03"></a>Ich habe Application Insights erfolgreich hinzugefügt und meine App ausgeführt, sehe aber keine Daten im Portal.

+ Klicken Sie auf der Übersichtsseite auf die Kachel "Suchen", um die Diagnosesuche zu öffnen. Hier werden zunächst Daten angezeigt.
+ Klicken Sie auf die Schaltfläche "Aktualisieren". Das Blatt aktualisiert sich in regelmäßigen Abständen selbst, doch Sie können es auch manuell aktualisieren. Das Aktualisierungsintervall für größere Zeiträume ist länger.
+ Schauen Sie sich im Startmenü der Microsoft Azure-Vorschau die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, schließen Sie das Blatt Ihrer Application Insights-Anwendung, und öffnen Sie es erneut.
+ Schauen Sie auch in [unserem Statusblog](http://blogs.msdn.com/b/applicationinsights-status/) nach.

#### Keine Daten, seitdem die App auf meinem Server veröffentlicht wurde.

+ In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.
+ Wenn Sie einen Proxy verwenden müssen, um Daten aus Ihrem Unternehmensnetzwerk zu senden, legen Sie [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in der Datei "Web.config" fest.
+ Windows Server 2008: Stellen Sie sicher, dass folgende Updates installiert sind: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

#### <a name="q04"></a>Ich sehe keine Daten in der Nutzungsanalyse für meine Website

+ Die Daten kommen aus Skripts auf den Webseiten. Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen][start].
+ Stellen Sie sicher, dass Internet Explorer Ihre Website nicht im Kompatibilitätsmodus anzeigt.
+ Verwenden Sie die Debugfunktion Ihres Browsers (bei einigen Browsern drücken Sie F12 und wählen anschließend "Netzwerk" aus), um sicherzustellen, dass Daten an dc.services.visualstudio.com gesendet werden.

#### <a name="q08"></a>Kann ich Application Insights verwenden, um einen Intranetwebserver zu überwachen?

Ja, Sie können den Status und die Nutzung überwachen, wenn Ihr Server Daten an das öffentliche Internet senden kann. Öffnen Sie in der Firewall die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com".

Wenn Sie jedoch Webtests für den Dienst ausführen möchten, muss dieser über das öffentliche Internet über Port 80 zugänglich sein.

#### Kann ich einen Intranetwebserver überwachen, der keinen Zugriff auf das öffentliche Internet hat?

Sie müssen einen Proxy einrichten, der HTTPS-POST-Aufrufe an dc.services.visualstudio.com übertragen kann.

#### Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.

* Überprüfen Sie den [Statusblog](http://blogs.msdn.com/b/applicationinsights-status/).
* Ist Ihr monatliches Kontingent an Datenpunkten erreicht? Öffnen Sie "Einstellungen – Kontingente und Preisübersicht", um es herauszufinden. Wenn dies der Fall ist, können Sie Ihren Plan aktualisieren oder zusätzliche Kapazität erwerben. Informationen hierzu finden Sie in der [Preisübersicht](http://azure.microsoft.com/pricing/details/application-insights/).

## Statusmonitor funktioniert nicht

Siehe [Problembehandlung für den Statusmonitor](app-insights-monitor-performance-live-website-now.md#troubleshooting). Das häufigste Problem sind Firewallports.

## Das Portal

#### <a name="q05"></a>Ich befinde mich im Startmenü der Microsoft Azure-Vorschau. Wo finde ich meine Daten in Application Insights?

Entweder:

* Wählen Sie "Durchsuchen", "Application Insights" und den Namen Ihres Projekts aus. Wenn noch keine Projekte vorhanden sind, [fügen Sie Application Insights zu Ihrem Webprojekt in Visual Studio hinzu][start].

* Klicken Sie im Visual Studio Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt, und wählen Sie "Application Insights-Portal öffnen" aus.


#### <a name="update"></a>Wie kann ich ändern, an welche Azure-Ressource mein Projekt Daten sendet?

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Sie können die Daten an eine vorhandene oder neue Ressource in Azure senden. Der Assistent ändert den Instrumentationsschlüssel in ApplicationInsights.config. Dadurch wird bestimmt, wohin das Server-SDK die Daten sendet. Wenn Sie "Alle aktualisieren" deaktivieren, wird auch der Anzeigeort des Schlüssels auf Ihren Webseiten geändert.


#### <a name="q06"></a>Zeigt die Übersicht auf dem Startbildschirm der Microsoft Azure-Vorschau den Status meiner Anwendung?

Nein! Sie zeigt den Status des Azure-Diensts an. Um die Ergebnisse des Webtests anzuzeigen, wählen Sie "Durchsuchen > Application Insights > (Ihre Anwendung)" aus. Sehen Sie sich dann die Webtest-Ergebnisse an.


#### <a name="q07"></a>Wenn ich Application Insights zu meiner Anwendung hinzufüge und das Application Insights-Portal öffne, sehe ich eine ganz andere Ansicht als in den Screenshots.

Sie verwenden möglicherweise eine [ältere Version des Application Insights SDK](http://msdn.microsoft.com/library/dn793604.aspx), die zur Visual Studio Online-Version gehört.

Die Hilfeseiten dagegen beziehen sich auf [Application Insights für die Microsoft Azure-Vorschau][start], das bereits auf Visual Studio 2013 Update 3 und höher ausgerichtet ist.

#### <a name="data"></a>Wie lange werden Daten im Portal aufbewahrt? Ist Sicherheit gewährleistet?

Informationen hierzu finden Sie unter [Datenaufbewahrung und Datenschutz][data].

## Protokollierung

#### <a name="post"></a>Wie zeige ich POST-Daten in der Diagnosesuche an?

POST-Daten werden nicht automatisch protokolliert, Sie können jedoch einen TrackTrace-Aufruf verwenden und die Daten in den Nachrichtenparameter einfügen. Die Größenbegrenzung hierfür ist höher als bei Zeichenfolgeneigenschaften, Sie können jedoch nicht danach filtern.

## Sicherheit

#### Sind meine Daten im Portal sicher? Wie lange werden sie aufbewahrt?

Informationen hierzu finden Sie unter [Datenaufbewahrung und Datenschutz][data].


## <a name="q17"></a> Habe ich alles in Application Insights aktiviert?

<table border="1">
<tr><th>Diese Daten sollten angezeigt werden</th><th>So erhalten Sie die Daten</th><th>Deshalb benötigen Sie die Daten</th></tr>
<tr><td>Verfügbarkeitsdiagramme</td><td><a href="../app-insights-monitor-web-app-availability/">Webtests</a></td><td>Information, ob Ihre Web-App verfügbar ist</td></tr>
<tr><td>Leistung der Server-App: Antwortzeiten usw. ...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Hinzufügen von Application Insights zu Ihrem Projekt</a><br/>oder <br/><a href="../app-insights-monitor-performance-live-website-now/">Installieren Sie den AI-Statusmonitor auf dem Server</a> (oder schreiben Sie Ihren eigenen Code in die <a href="../app-insights-api-custom-events-metrics/#track-dependency">Nachverfolgung von Aufrufen</a>)</td><td>Erkennen von Leistungsproblemen</td></tr>
<tr><td>Telemetriedaten zu Abhängigkeiten</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installieren des AI-Statusmonitors auf dem Server</a></td><td>Diagnostizieren von Problemen mit Datenbanken oder anderen externen Komponenten</td></tr>
<tr><td>Abrufen von Stapelüberwachungen aus Ausnahmen</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Einfügen von TrackException-Aufrufen in Ihren Code</a> (einige werden jedoch automatisch gemeldet)</td><td>Erkennen und Diagnostizieren von Ausnahmen</td></tr>
<tr><td>Durchsuchen von Ablaufprotokollen</td><td><a href="../app-insights-search-diagnostic-logs/">Hinzufügen eines Protokollierungsadapters</a></td><td>Diagnostizieren von Ausnahmen und Leistungsproblemen</td></tr>
<tr><td>Grundlegende Clientnutzung: Seitenansichten, Sitzungen, ...</td><td><a href="../app-insights-javascript/">JavaScript-Initialisierer in Webseiten</a></td><td>Nutzungsanalyse</td></tr>
<tr><td>Benutzerdefinierte Metriken auf Clients</td><td><a href="../app-insights-api-custom-events-metrics/">Nachverfolgen von Aufrufen in Webseiten</a></td><td>Verbessern der Benutzerfreundlichkeit</td></tr>
<tr><td>Benutzerdefinierte Metriken auf Servern</td><td><a href="../app-insights-api-custom-events-metrics/">Nachverfolgen von Aufrufe im Servercode</a></td><td>Business Intelligence</td></tr>
</table>

Wenn Ihr Webdienst auf einem virtuellen Azure-Computer ausgeführt wird, erhalten Sie dort auch eine [Diagnose][azurediagnostic].

## Automation

Sie können ein [PowerShell-Skript schreiben](app-insights-powershell-script-create-resource.md), um eine Application Insights-Ressource zu erstellen.

## Weitere Antworten

* [Application Insights-Forum](https://social.msdn.microsoft.com/Forums/vstudio/de-DE/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Sept15_HO3-->