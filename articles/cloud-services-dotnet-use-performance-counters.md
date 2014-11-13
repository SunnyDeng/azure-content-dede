<properties urlDisplayName="Performance Profiling" pageTitle="Verwenden von Leistungsindikatoren in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Hier erfahren Sie, wie Sie Daten aus Leistungsindikatoren in Azure-Anwendungen aktivieren und erfassen. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Verwenden von Leistungsindikatoren in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Verwenden von Leistungsindikatoren in Azure

Sie können in einer Azure-Anwendung Leistungsindikatoren
verwenden, um Daten zu sammeln, mit deren Hilfe
Systemengpässe ermittelt und die System- sowie Anwendungsleistung optimiert werden können. In Windows Server 2008, Windows Server 2012, IIS und ASP.NET verfügbare Leistungsindikatoren können erfasst und verwendet werden, um die Intaktheit der Azure-Anwendung zu ermitteln.

Leistungsindikatoren lassen sich mithilfe des Azure SDK 2.0 oder höher vor der Bereitstellung oder zur Laufzeit in Visual Studio 2012 oder Visual Studio 2013 konfigurieren. Weitere Informationen finden Sie unter [Configuring Azure Diagnostics][Configuring Azure Diagnostics] (Konfigurieren von Azure-Diagnose, in englischer Sprache). Informationen zur manuellen Konfiguration von Leistungsindikatoren in der Anwendung finden Sie unter [Konfigurieren von Leistungsindikatoren][Konfigurieren von Leistungsindikatoren].

Weitere ausführliche Anweisungen zum Erstellen einer Protokollierungs- und Nachverfolgungsstrategie und Verwenden von Diagnose und anderen Methoden zum Beheben von Problemen finden Sie unter [Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen][Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen].

## <a name="nextsteps"> </a>Nächste Schritte

Folgen Sie diesen Links, um zu erfahren, wie komplexere Problembehandlungsszenarien implementiert werden.

-   [Lokales Testen der Leistung eines Cloud-Diensts][Lokales Testen der Leistung eines Cloud-Diensts]
-   [Troubleshooting Best Practices for Developing Azure Applications (Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen, in englischer Sprache)][Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen]
-   [Überwachung von Cloud-Diensten][Überwachung von Cloud-Diensten]
-   [Verwenden des Anwendungsblocks für die automatische Skalierung][Verwenden des Anwendungsblocks für die automatische Skalierung]
-   [Building Elastic and Resilient Cloud Apps (Erstellen elastischer und robuster Cloud-Apps)][Building Elastic and Resilient Cloud Apps (Erstellen elastischer und robuster Cloud-Apps)]

## <a name="additional"> </a>Zusätzliche Ressourcen

-   [Aktivieren der Diagnose in Azure][Aktivieren der Diagnose in Azure]
-   [Sammeln von Protokollierungsdaten mit der Azure-Diagnose][Sammeln von Protokollierungsdaten mit der Azure-Diagnose]
-   [Debuggen von Cloud-Diensten][Debuggen von Cloud-Diensten]

  [Configuring Azure Diagnostics]: http://msdn.microsoft.com/de-de/library/windowsazure/dn186185.aspx
  [Konfigurieren von Leistungsindikatoren]: http://msdn.microsoft.com/de-de/library/azure/dn535595.aspx
  [Bewährte Methoden zur Problembehandlung bei der Entwicklung von Azure-Anwendungen]: http://msdn.microsoft.com/de-de/library/windowsazure/hh771389.aspx
  [Lokales Testen der Leistung eines Cloud-Diensts]: http://msdn.microsoft.com/de-de/library/azure/hh369930.aspx
  [Überwachung von Cloud-Diensten]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-how-to-monitor/
  [Verwenden des Anwendungsblocks für die automatische Skalierung]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [Building Elastic and Resilient Cloud Apps (Erstellen elastischer und robuster Cloud-Apps)]: http://msdn.microsoft.com/de-de/library/hh680949(PandP.50).aspx
  [Aktivieren der Diagnose in Azure]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-dotnet-diagnostics/
  [Sammeln von Protokollierungsdaten mit der Azure-Diagnose]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433048.aspx
  [Debuggen von Cloud-Diensten]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405479.aspx
