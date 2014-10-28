<properties title="Search diagnostic logs with Application Insights" pageTitle="Search diagnostic logs" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Durchsuchen der Diagnoseprotokolle mit Application Insights

Sie können Diagnosedaten aus System.Diagnostics.Trace, NLog und Log4Net erfassen und durchsuchen. Application Insights stellt ein effizientes, benutzerfreundliches Tool zum Erfassen und Überprüfen von protokollierten Ereignissen aus einer oder mehreren Quellen zur Verfügung, um die Features zur Überwachung des Anwendungszustands zu ergänzen.

Die überwachte Webanwendung kann vor Ort oder auf einem virtuellen Computer gehostet werden oder sich auf einer Microsoft Azure-Website befinden.

1. [Hinzufügen eines Protokollierungsadapters][Hinzufügen eines Protokollierungsadapters]
+ [Konfigurieren der Diagnoseerfassung][Konfigurieren der Diagnoseerfassung]
+ [Einfügen von Protokollanweisungen, Erstellen und Bereitstellen][Einfügen von Protokollanweisungen, Erstellen und Bereitstellen]
+ [Anzeigen von Protokolldaten][Anzeigen von Protokolldaten]
+ [Durchsuchen der Daten][Durchsuchen der Daten]
+ [Nächste Schritte][Nächste Schritte]

## <a name="add"></a>1. Hinzufügen eines Protokollierungsadapters

1.  [Fügen Sie Application Insights zum Webdienstprojekt][Fügen Sie Application Insights zum Webdienstprojekt] in Visual Studio hinzu, falls noch nicht geschehen.

    Wenn Sie Application Insights hinzufügen, nachdem Sie Protokollierung zum Projekt hinzugefügt haben, werden Sie feststellen, dass der Protokollierungsadapter bereits eingerichtet und konfiguriert wurde – Sie müssen einfach das [Projekt erneut bereitstellen][Einfügen von Protokollanweisungen, Erstellen und Bereitstellen] und die [Daten anzeigen][Anzeigen von Protokolldaten].

2.  Wählen Sie im Projektmappen-Explorer im Kontextmenü des Projekts **Manage NuGet Packages**.
3.  Klicken Sie auf „Online \> All“, wählen Sie **Include Prerelease** aus, und suchen Sie nach „Microsoft.ApplicationInsights“.

    ![Vorabversion des entsprechenden Adapters auswählen][Vorabversion des entsprechenden Adapters auswählen]

4.  Wählen Sie die Vorabversion den entsprechenden Pakets aus - eines der Folgenden:

  + Microsoft.ApplicationInsights.TraceListener
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

## <a name="configure"></a>2. Konfigurieren der Diagnoseerfassung

### Für System.Diagnostics.Trace

Fügen Sie in Web.config den folgenden Code in den Abschnitt `<configuration>` ein:

    <system.diagnostics>
     <trace autoflush="true" indentsize="0">
      <listeners>
       <add name="myAppInsightsListener"  
          type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, 
         Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
     </trace>
    </system.diagnostics> 

### Für NLog

Fügen Sie in Nlog.config die folgenden Codeausschnitte in die Abschnitte `<extensions>`, `<targets>` und `<rules>` ein. Erstellen Sie die Abschnitte bei Bedarf.

    <extensions> 
     <add  assembly="Microsoft.ApplicationInsights.NLogTarget" /> 
    </extensions> 

    <targets>
     <target xsi:type="ApplicationInsightsTarget" name="aiTarget"/>
    </targets>

    <rules>
     <logger name="*" minlevel="Trace" writeTo="aiTarget"/>
    </rules>

### Für Log4Net

Fügen Sie in Web.config die folgenden Codeausschnitte in die Abschnitte `<configsections>` und `<log4net>` ein:

    <configSections>
      <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
    </configSections>

    <log4net>
     <root>
      <level value="ALL" /> <appender-ref ref="aiAppender" />
     </root>
     <appender name="aiAppender" type="Microsoft.ApplicationInsights.Log4NetAppender.ApplicationInsightsAppender, Microsoft.ApplicationInsights.Log4NetAppender">
      <layout type="log4net.Layout.PatternLayout">
       <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
     </appender>
    </log4net>

## <a name="deploy"></a>3. Einfügen von Protokollanweisungen, Erstellen und Bereitstellen

Fügen Sie Ereignisprotokollierungsaufrufe mithilfe Ihres bevorzugten Protokollframeworks ein. Wenn Sie beispielsweise Log4Net verwenden, könnte der Aufruf wie folgt aussehen:

    log.Warn("Slow response - database01");

Protokollierte Ereignisse werden sowohl während der Entwicklung als auch im Betrieb an Application Insights gesendet.

## <a name="view"></a>4. Anzeigen von Protokolldaten

Öffnen Sie in Application Insights die Diagnosesuche.

![Diagnosesuche öffnen][Diagnosesuche öffnen]

Wählen Sie ein Protokollereignis aus, um Details dazu anzuzeigen.

![Diagnosesuche öffnen][1]

Die verfügbaren Felder hängen vom Protokollframework und den im Aufruf verwendeten Parametern ab.

Sie können Klartext-Zeichenfolgen (ohne Platzhalter) verwenden, um die Felddaten in einem Objekt zu filtern.

## <a name="search"></a>5. Durchsuchen der Daten

Legen Sie einen Zeitbereich fest, und suchen Sie nach Begriffen. Das Suchen innerhalb eines kürzeren Zeitbereichs geht schneller.

![Diagnosesuche öffnen][2]

Beachten Sie, dass Sie nach Begriffen suchen, nicht nach Teilzeichenfolgen. Begriffe sind alphanumerische Zeichenfolgen, die Zeichen wie '.' und '\_' enthalten können. Beispiel:

<table>
  <tr><th>Begriff</th><th>stimmt NICHT überein mit</th><th>stimmt überein mit</th></tr>
  <tr><td>HomeController.Info</td><td>info<br/>home</td><td>h*info<br/>home*</td></tr>
  <tr><td>IstLokal</td><td>lokal<br/>ist<br/>*lokal</td><td>istl*<br/>istlokal<br/>i*l</td></tr>
  <tr><td>Neue Stornierung</td><td>e s</td><td>neue<br/>stornierung<br/>n* AND s*</td></tr>
</table>

Dies sind einige Suchausdrücke, die Sie verwenden können:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><p>Beispielabfrage</p></th>
<th align="left"><p>Effekt</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><span class="code">langsam</span></p></td>
<td align="left"><p>Findet alle Ereignisse im Datumsbereich, deren Felder den Begriff „langsam“ enthalten</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">Datenbank??</span></p></td>
<td align="left"><p>Findet Datenbank01, DatenbankAB, ...</p>
<p>? ist zu Beginn eines Suchbegriffs nicht zulässig.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">Datenbank*</span></p></td>
<td align="left"><p>Findet Datenbank, Datenbank01, DatenbankNNNN</p>
<p>* ist zu Beginn eines Suchbegriffs nicht zulässig.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">Apfel UND Banane</span></p></td>
<td align="left"><p>Findet Ereignisse, die beide Begriffe enthalten. Verwenden Sie „AND“ in Großbuchstaben, nicht „and“.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><span class="code">Apfel OR Banane</span></p>
<p><span class="code">Apfel Banane</span></p></td>
<td align="left"><p>Findet Ereignisse, die einen der beiden Begriffe enthalten. Verwenden Sie „OR“ in Großbuchstaben, nicht „or“.</p>
<p>Kurzform.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">Apfel NOT Banane</span></p>
<p><span class="code">Apfel -Banane</span></p></td>
<td align="left"><p>Findet Ereignisse, die den einen Begriff enthalten, aber nicht den anderen.</p>
<p>Kurzform.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Apf* AND Banane NOT (Traube OR Birne)</p>
<p><span class="code">Apf* AND Banane -(Traube Birne)</span></p></td>
<td align="left"><p>Logische Operatoren und Klammern.</p>
<p>Verkürzte Form.</p></td>
</tr>
<tr class="even">
<td align="left"><p><span class="code">message:langsam</span></p>
<p><span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span></p>
<p><span class="code">properties.logEventInfo.level:Error</span></p></td>
<td align="left"><p>Übereinstimmung mit dem angegebenen Feld ist erforderlich. Standardmäßig werden alle Felder durchsucht. Um zu sehen, welche Felder verfügbar sind, rufen Sie die Details für ein Ereignis auf.</p></td>
</tr>
</tbody>
</table>

## <a name="add"></a>Nächste Schritte

-   [Hinzufügen von Application Insights zu Ihrem Projekt][Fügen Sie Application Insights zum Webdienstprojekt]
-   [Einrichten von Tests zu Verfügbarkeit und Reaktionszeit][Einrichten von Tests zu Verfügbarkeit und Reaktionszeit]
-   [Problembehandlung][Problembehandlung]

## Weitere Informationen

-   [Application Insights][Application Insights]
-   [Hinzufügen von Application Insights zu Ihrem Projekt][Fügen Sie Application Insights zum Webdienstprojekt]
-   [Überwachen eines Live-Webservers][Überwachen eines Live-Webservers]
-   [Erkunden von Metriken in Application Insights][Erkunden von Metriken in Application Insights]
-   [Durchsuchen der Diagnoseprotokolle][Durchsuchen der Diagnoseprotokolle]
-   [Verfügbarkeitsüberwachung mit Webtests][Einrichten von Tests zu Verfügbarkeit und Reaktionszeit]
-   [Nutzungsverfolgung mit Ereignissen und Metriken][Nutzungsverfolgung mit Ereignissen und Metriken]
-   [Fragen und Antworten sowie Problembehandlung][Problembehandlung]

<!--Link references-->

  [Hinzufügen eines Protokollierungsadapters]: #add
  [Konfigurieren der Diagnoseerfassung]: #configure
  [Einfügen von Protokollanweisungen, Erstellen und Bereitstellen]: #deploy
  [Anzeigen von Protokolldaten]: #view
  [Durchsuchen der Daten]: #search
  [Nächste Schritte]: #next
  [Fügen Sie Application Insights zum Webdienstprojekt]: ../app-insights-monitor-application-health-usage/
  [Vorabversion des entsprechenden Adapters auswählen]: ./media/appinsights/appinsights-36nuget.png
  [Diagnosesuche öffnen]: ./media/appinsights/appinsights-30openDiagnostics.png
  [1]: ./media/appinsights/appinsights-32detail.png
  [2]: ./media/appinsights/appinsights-31search.png
  [Einrichten von Tests zu Verfügbarkeit und Reaktionszeit]: ../app-insights-monitor-web-app-availability/
  [Problembehandlung]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Überwachen eines Live-Webservers]: ../app-insights-monitor-performance-live-website-now/
  [Erkunden von Metriken in Application Insights]: ../app-insights-explore-metrics/
  [Durchsuchen der Diagnoseprotokolle]: ../app-insights-search-diagnostic-logs/
  [Nutzungsverfolgung mit Ereignissen und Metriken]: ../app-insights-track-usage-custom-events-metrics/
