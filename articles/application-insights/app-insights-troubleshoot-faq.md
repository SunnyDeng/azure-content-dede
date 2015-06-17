<properties 
	pageTitle="Problembehandlung und Fragen zu Application Insights" 
	description="Etwas in Visual Studio Application Insights unklar oder nicht funktioniert? Versuchen Sie es hier." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Problembehandlung und Fragen – Application Insights für ASP.NET

## Kann ich Application Insights mit ... verwenden?

[Siehe Plattformen][platforms]

## Das SDK hinzufügen

#### <a name="q01"></a>Ich nicht keine Option zum Hinzufügen von Application Insights zu meinem Projekt in Visual Studio angezeigt.

+ Stellen Sie sicher, dass [Visual Studio 2013 Update 3 oder höher](http://go.microsoft.com/fwlink/?LinkId=397827). Mit Application Insights-Tools vorinstalliert ist.
+ Obwohl die Tools nicht alle Arten von Anwendungen unterstützen, können Sie trotzdem Application Insights-SDK zu Ihrem Projekt manuell hinzufügen. Verwendung [dieses Verfahren][windows]. 


#### <a name="q02"></a>Mein neues Webprojekt erstellt wurde, aber das Hinzufügen von Application Insights ist fehlgeschlagen.

Das kann vorkommen, wenn die Kommunikation mit dem Application Insights-Portal fehlgeschlagen ist oder wenn es ein Problem mit Ihrem Konto gibt.

+ Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben. Die Microsoft Azure-Anmeldeinformationen, die Sie im Dialogfeld „Neues Projekt“ sehen, können sich von den Visual Studio Online-Anmeldeinformationen oben rechts in Visual Studio unterscheiden.
+ Warten Sie einen Moment, und dann [fügen Sie Application Insights zu Ihrem vorhandenen Projekt hinzu][start].
+ Wechseln Sie zu den Microsoft Azure-Kontoeinstellungen und überprüfen Sie eventuelle Beschränkungen. Testen Sie, ob Sie eine Application Insights-Anwendung manuell hinzufügen können.

#### <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.

Es scheint ein Fehler aufgetreten zu sein, während Sie Application Insights oder vielleicht einen Protokollierungsadapter installiert haben.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt.


#### <a name="q14"></a>Was ändern Application Insights in meinem Projekt?

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

+ (Nur neue Projekte – wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][start], müssen Sie dies manuell ausführen.) Fügt der Ausschnitte in den Client- und Code zum Initialisieren mit dem Application Insights-Ressourcen-ID Beispielsweise wird in einer MVC-app, Code in die Masterseite Views/Shared/_Layout.cshtml eingefügt

####<a name="NuGetBuild"></a> Ich erhalte "NuGet-Pakete fehlen" auf dem Server erstellen, obwohl alles OK auf meinem Entwicklungscomputer erstellt

Finden Sie in [NuGet Package Restore](http://docs.nuget.org/Consume/Package-Restore) und [Automatic Restore-Paket](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a> Erhalte ich "Projekt verweist auf NuGet-Pakete, die auf meinem Computer fehlen" beim Versuch, nach der Aktualisierung auf 0,17 oder höher der NuGet-Pakete zu erstellen.

Wenn nach dem Aktualisieren auf die 0,17 Dollar oder neuere NuGet-Pakete die Fehlermeldung oben angezeigt wird, müssen Sie die Proj-Datei bearbeiten und entfernen die BCL-Ziele, die zurückgelassen wurden.

Gehen Sie dazu folgendermaßen vor:

1. Mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie Projekt entladen.
2. Mit der rechten Maustaste auf das Projekt erneut, und wählen Sie bearbeiten *yourProject.csproj* 
3. Öffnen Sie am Ende der Projektdatei und entfernen die BCL-Ziele ähnelt: "" <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> \`\`\`
4. Speichern Sie die Datei .
5. Mit der rechten Maustaste auf das Projekt, und wählen Sie zum erneuten Laden *yourProject.csproj*

## Keine Daten

#### <a name="q03"></a>Application Insights erfolgreich hinzugefügt, und ich führte Meine app aus, aber es nie vorgekommen Daten im Portal.

+ Klicken Sie auf der Übersichtsseite auf der Kachel suchen, um Diagnose suchen zu öffnen. Daten werden hier zuerst angezeigt.
+ Klicken Sie auf die Schaltfläche "Aktualisieren". In der aktuellen Version wird der Fensterinhalt nicht automatisch aktualisiert.
+ Schauen Sie sich im Startboard der Microsoft Azure-Vorschau die Dienststatusübersicht an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
+ Prüfen Sie auch [unserem Blog Status](http://blogs.msdn.com/b/applicationinsights-status/archive/2015/04/14/data-latency-and-data-access-issue-with-data-storage-service-4-14-investigating.aspx).
+ In Ihrer Firewall müssen Sie möglicherweise die TCP-Ports 80 und 443 für ausgehenden Datenverkehr zu "dc.services.visualstudio.com" und "f5.services.visualstudio.com" öffnen.
+ Wenn Sie einen Proxy verwenden, aus dem Unternehmensnetzwerk zu senden, legen Sie [DefaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in der Datei Web.config
+ WindowsServer 2008: Stellen Sie sicher, dass Sie die folgenden Updates installiert haben: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

#### <a name="q04"></a>Meine Website finden keine Daten unter Verwendungsanalyse

+ Die Daten kommen aus Skripts auf den Webseiten. Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen][start].
+ Stellen Sie sicher, dass Internet Explorer Ihre Website im Kompatibilitätsmodus angezeigt.
+ Mithilfe des Browsers Debug-Funktion (in einigen Browsern F12 drücken Netzwerk) sicher, dass Daten an dc.services.visualstudio.com gesendet wird.

#### <a name="q08"></a>Kann ich Application Insights verwenden, um einen internen Webserver zu überwachen?

Ja, Sie können den Status und die Nutzung überwachen, wenn Ihr Server Daten an das öffentliche Internet senden kann.

Aber wenn Sie Webtests für Ihren Dienst ausführen möchten, muss über das öffentliche Internet an Port 80 sein.

#### Kann ich einem Intranetwebserver überwachen, die keinen Zugriff auf das öffentliche Internet hat?

Sie müssten einen Proxy anzuordnen, der Https POST-Aufrufe an dc.services.visualstudio.com weiterleiten können

#### Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.

* Überprüfen Sie die [Status Blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Haben Sie Ihre monatliche Kontingent von Datenpunkten erreicht? Öffnen Sie die Einstellungen-Kontingent und Preise herausfinden. Ist dies der Fall ist, können Sie den Plan aktualisieren oder bezahlen für zusätzliche Kapazität. Siehe die [Preise Schema](http://azure.microsoft.com/pricing/details/application-insights/).



## Das Portal

#### <a name="q05"></a>Ich freue mich beim Start Vorstand Microsoft Azure Preview. Wo finde ich meine Daten in Application Insights?

Entweder:

* Wählen Sie „Durchsuchen“, „Application Insights“, Ihren Projektnamen aus. Wenn noch keine Projekte vorhanden sind, [fügen Sie Application Insights zu Ihrem Webprojekt in Visual Studio hinzu][start].

* Klicken Sie im Visual Studio Projektmappen-Explorer mit der rechten Maustaste auf das Webprojekt, und wählen Sie „Open Application Insights Portal“ aus.


#### <a name="update"></a>Wie kann ich ändern, welche Azure Resource Mein Projekt Daten sendet?

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Sie können die Daten an eine vorhandene oder neue Ressource in Azure senden. Der Assistent ändert den Instrumentationsschlüssel in ApplicationInsights.config. Dadurch wird bestimmt, wohin das Server-SDK die Daten sendet. Wenn Sie "Alle aktualisieren" deaktivieren, wird auch der Anzeigeort des Schlüssels auf Ihren Webseiten geändert.


#### <a name="q06"></a>Auf dem Startbildschirm des Microsoft Azure Preview zeigt diese Zuordnung den Status meiner Anwendung?

Nein! Sie zeigt den Status des Azure-Diensts an. Um die Ergebnisse des Webtests anzuzeigen, wählen Sie "Durchsuchen > Application Insights > (Ihre Anwendung)" aus. Sehen Sie sich dann die Webtest-Ergebnisse an.


#### <a name="q07"></a>Bei der Verwendung von meiner Anwendung Application Insights hinzufügen, und öffnen Sie Application Insights-Portal, alles völlig anders aussieht als Ihrer Screenshots.

Möglicherweise verwenden Sie [die ältere Version von Application Insights-SDK](http://msdn.microsoft.com/library/dn793604.aspx), der eine Verbindung herstellt, auf dem Visual Studio Online-Version.

Sie sehen Hilfeseiten finden Sie unter [Application Insights für Microsoft Azure Preview][start], kommt bereits eingeschaltet in Visual Studio 2013 Update 3 oder höher.

#### <a name="data"></a>Wie lange ist die Daten im Portal beibehalten? Ist Sicherheit gewährleistet?

Schauen Sie sich [Datenaufbewahrung und Datenschutz][data].

## Protokollierung

#### <a name="post"></a>Wie zeige ich die POST-Daten in Diagnose suchen?

Wir keine POST-Daten automatisch anmelden, jedoch können Sie einen Aufruf TrackTrace: Fügen Sie die Daten im Message-Parameter. Dies hat Größenbegrenzung längere als die Grenzwerte für String-Eigenschaften, obwohl es gefiltert werden können.

## Sicherheit

#### Sind meine Daten sicher im Portal? Wie lange bleibt?

Finden Sie unter [Daten und][data].


## <a name="q17"></a> Aktiviert ich alles in Application Insights haben?

<table border="1">
<tr><th>Was sollte angezeigt werden</th><th>Gewusst wie: Abrufen</th><th>Warum sollen</th></tr>
<tr><td>Verfügbarkeit von Diagrammen</td><td><a href="../app-insights-monitor-web-app-availability/">Webtests</a></td><td>Wissen Sie, dass Ihre Webanwendung aktiviert ist.</td></tr>
<tr><td>Server app-bezogen: Antwortzeiten,...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Application Insights zu Ihrem Projekt hinzufügen.</a><br/>oder <br/><a href="../app-insights-monitor-performance-live-website-now/">AI-Überwachung auf Server installieren</a></td><td>Perf-Probleme zu erkennen.</td></tr>
<tr><td>Abhängigkeit Telemetrie</td><td><a href="../app-insights-monitor-performance-live-website-now/">AI-Überwachung auf Server installieren</a></td><td>Diagnostizieren von Problemen mit Datenbanken oder andere externen Komponenten</td></tr>
<tr><td>Rufen Sie Stapelüberwachungen von Ausnahmen</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">TrackException legen Sie in Ihrem Code ruft</a> (jedoch einige automatisch gemeldet werden)</td><td>Erkennen und Diagnostizieren von Ausnahmen</td></tr>
<tr><td>Search-Protokoll-Ablaufverfolgungen</td><td><a href="../app-insights-search-diagnostic-logs/">Hinzufügen eines Adapters für die Protokollierung</a></td><td>Ausnahmen Perf-Probleme zu diagnostizieren.</td></tr>
<tr><td>Grundlagen der Client-Nutzung: Seitenaufrufe, Sitzungen,...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">JavaScript-Initialisierer in Webseiten</a></td><td>Nutzungsanalyse</td></tr>
<tr><td>Benutzerdefinierte Client-Metriken</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Nachverfolgen von Aufrufen in Webseiten</a></td><td>Verbessern der Benutzerfreundlichkeit</td></tr>
<tr><td>Benutzerdefinierte Metriken für Server</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Nachverfolgung Aufrufe im Servercode</a></td><td>Business Intelligence</td></tr>
</table>

Wenn Ihr Webdienst in einer Azure-VM ausgeführt wird, können Sie auch [Diagnose abrufen][azurediagnostic] vorhanden.



<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->