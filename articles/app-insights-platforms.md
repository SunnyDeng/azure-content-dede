<properties 
	pageTitle="Application Insights: Plattformen" 
	description="Kann ich Application Insights mit ... verwenden?" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights: Plattformen

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### Kann ich Application Insights mit ... verwenden?


## Sprachen

+ [C#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [JavaScript-Webseiten](app-insights-web-track-usage.md)
+ [Windows Store JavaScript-Apps](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## Plattformen

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Azure-Web-Apps und VMs](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Windows Desktop](app-insights-windows-desktop.md)


## Protokollierungsframeworks

+	[Log4Net, NLog oder System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J oder Logback](app-insights-java-trace-logs.md)


## Projekte

Besuchen Sie auch das [Application Insights SDK-Projekt auf GitHub](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>Cordova- und Windows Store JavaScript-Apps

Klicken Sie in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten**.

Wählen Sie **Online**, und suchen Sie nach Application Insights.

Installieren Sie die **Application Insights-API für JavaScript-Anwendungen**.

Verwenden Sie das standardmäßige clientseitige [Web-App-Skript](app-insights-web-track-usage.md), jedoch mit einer Änderung.

Nachdem Sie das Skript aus dem Application Insights-Portal abgerufen haben, fügen Sie nach dem Instrumentationsschlüssel eine Zeile hinzu:

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[Windows Store-Apps mit JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->