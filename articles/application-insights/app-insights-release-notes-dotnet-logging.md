<properties 
	pageTitle="Versionshinweise für Application Insights-Protokollierungsadapter" 
	description="Die neuesten Updates." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# Versionshinweise für Protokollierungsadapter für .NET

Wenn Sie bereits ein Protokollierungsframework wie log4net, NLog oder System.Diagnostics.Trace nutzen, können Sie diese Protokolle erfassen und an [Application Insights](http://azure.microsoft.com/services/application-insights/) senden. Dort können Sie Protokollablaufverfolgungen mit Benutzeraktionen, Ausnahmen und anderen Ereignissen in Beziehung setzen.


#### Erste Schritte

Siehe [Protokolle, Ausnahmen und benutzerdefinierte Diagnose für ASP.NET in Application Insights](app-insights-search-diagnostic-logs.md).

## Version 1.2.6

- Fehlerbehebungen
- log4Net: log4net-Eigenschaften werden als benutzerdefinierte Eigenschaften gesammelt. UserName ist keine benutzerdefinierte Eigenschaft mehr (sie wird als telemetry.Context.User.Id gesammelt). Timestamp ist keine benutzerdefinierte Eigenschaft mehr.
- NLog: NLog-Eigenschaften werden als benutzerdefinierte Eigenschaften gesammelt. SequenceID ist keine benutzerdefinierte Eigenschaft mehr (sie wird als telemetry.Sequence gesammelt). Timestamp ist keine benutzerdefinierte Eigenschaft mehr. 

## Version 1.2.5
- Erste Open Source-Version: Verweist auf die Application Insights-API-Version 1.2.3 oder höher.

<!---HONumber=AcomDC_0107_2016-->