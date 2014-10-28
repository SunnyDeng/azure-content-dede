<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mollybos"></tags>

# Wichtige Azure-Releases Frühjahr 2014

Dieser Artikel gibt einen Überblick über die neuen Tools, Features und Themen, die Azure .NET-Entwicklern auf der Konferenz zum Build 2014 präsentiert wurden und im Azure SDK für .NET 2.3, Visual Studio 2013 Update 2 und anderen im Frühjahr veröffentlichten Versionen verfügbar sind.

**Tools herunterladen:**

-   [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC]
-   [Azure SDK 2.3][Azure SDK 2.3]
-   [Windows Azure PowerShell][Windows Azure PowerShell]
-   [Azure Plattformübergreifende Befehlszeilen-Schnittstelle][Azure Plattformübergreifende Befehlszeilen-Schnittstelle]

Alle Details über die im Frühjahr 2014 veröffentlichten Tools finden Sie in [Versionshinweise zu Azure SDK für .NET 2.3][Versionshinweise zu Azure SDK für .NET 2.3] und auf der Seite [Visual Studio 2013-Produktaktualisierungen][Visual Studio 2013-Produktaktualisierungen].

[Sehen Sie sich Build-Videos an][Sehen Sie sich Build-Videos an] bedarfsgesteuertes Streaming.

## Inhaltsverzeichnis

-   [Webentwicklung und -veröffentlichung][Webentwicklung und -veröffentlichung]
-   [Diagnose und Debuggen][Diagnose und Debuggen]
-   [Verwalten von Azure-Diensten in Visual Studio][Verwalten von Azure-Diensten in Visual Studio]
-   [Automatisieren mit PowerShell][Automatisieren mit PowerShell]
-   [Mobile Entwicklung mit .NET][Mobile Entwicklung mit .NET]
-   [Speicherclientbibliothek 3.0 und neuer Speicheremulator][Speicherclientbibliothek 3.0 und neuer Speicheremulator]
-   [Ressourcen-Manager][Ressourcen-Manager]

## <span id="webdeploy"></span></a>Webentwicklung und -veröffentlichung

Azure SDK 2.3 und Visual Studio 2013 Update 2 RC umfassen verschiedene Updates, welche die Webentwicklung und das Veröffentlichen mit Azure optimieren. Sie können jetzt eine Azure-Website oder einen virtuellen Computer erstellen, wenn Sie eine neue Web-App erstellen. Wenn Sie soweit sind, dass Sie die Website veröffentlichen möchten, können Sie den aktualisierten Veröffentlichungsdialog oder PowerShell-Skripts verwenden, die der Lösung hinzugefügt wurden, sodass Sie die Site direkt auf einer Azure-Website oder einem virtuellen Computer bereitstellen können. In den folgenden Ressourcen finden Sie weitere Informationen und Lernprogramme, die erläutern, wie die neuen Features genutzt werden:

-   [Erste Schritte mit Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET]
-   [Erste Schritte mit den Azure Tools für Visual Studio][Erste Schritte mit den Azure Tools für Visual Studio]
-   [Creating ASP.NET Web Projects in Visual Studio 2013 (Erstellen von ASP.NET-Webprojekten in Visual Studio 2013, in englischer Sprache)][Creating ASP.NET Web Projects in Visual Studio 2013 (Erstellen von ASP.NET-Webprojekten in Visual Studio 2013, in englischer Sprache)]
-   [Build 2014: What's New for ASP.NET and Web in Visual Studio 2013 Update 2 and Beyond (Video in englischer Sprache)][Build 2014: What's New for ASP.NET and Web in Visual Studio 2013 Update 2 and Beyond (Video in englischer Sprache)]

## <span id="diagnostics"></span></a>Diagnose und Debuggen

Führen Sie eine Remotediagnose von Anwendungsproblemen mit der neuen Remotedebugging-Funktion für virtuelle Computer und der neuen Funktion für das Debuggen von systemeigenem Code durch:

-   [Debuggen eines Cloud-Diensts oder eines virtuellen Computers in Visual Studio][Debuggen eines Cloud-Diensts oder eines virtuellen Computers in Visual Studio]

Emulator Express ist der neue schlankere lokale Emulator für Cloud-Dienste. Hier lernen Sie, wie Sie den Emulator zum Testen von Cloud-Diensten auf dem lokalen Computer verwenden können:

-   [Verwenden von Emulator Express zum lokalen Ausführen und Debuggen eines Cloud-Diensts][Verwenden von Emulator Express zum lokalen Ausführen und Debuggen eines Cloud-Diensts]

Mit installiertem Azure SDK 2.3 können Sie jetzt Website-Dateien direkt von Server-Explorer aus remote anzeigen und bearbeiten sowie die Protokolldateien Ihrer Websites einsehen. Wenn Sie eine bearbeitete Datei speichern, wird diese ohne die Notwendigkeit des Veröffentlichens auf Ihrer Website gespeichert. Einzelheiten dazu finden Sie hier:

-   [Problembehandlung von Azure-Websites in Visual Studio][Problembehandlung von Azure-Websites in Visual Studio]

## <span id="service-management"></span></a>Verwalten von Azure-Diensten in Visual Studio

Nutzen Sie die verbesserte Verwaltung virtueller Computer in Visual Studio einschließlich der Fähigkeit, virtuelle Computer innerhalb der integrierten Entwicklungsumgebung zu erstellen:

-   [Erstellen virtueller Azure-Computer in Visual Studio][Erstellen virtueller Azure-Computer in Visual Studio]
-   [Zugreifen auf virtuelle Computer in Azure über den Server-Explorer][Zugreifen auf virtuelle Computer in Azure über den Server-Explorer]

Es gibt außerdem verschiedene Verbesserungen, mit deren Hilfe Sie andere Azure-Dienste effektiver in Server-Explorer verwalten können. Einzelheiten dazu finden Sie hier:

-   [Durchsuchen von Service Bus-Ressourcen mit dem Server-Explorer von Visual Studio][Durchsuchen von Service Bus-Ressourcen mit dem Server-Explorer von Visual Studio]
-   [Durchsuchen von Speicherressourcen mit Server-Explorer][Durchsuchen von Speicherressourcen mit Server-Explorer]

## <span id="automation"></span></a>Automatisieren mit PowerShell und APIs

Installieren von Azure PowerShell, um neue Cmdlets für Websites, WebJobs usw. zu nutzen. Speichern weiterer benutzerdefinierten Automatisierung mithilfe der Dienstverwaltung-API für .NET. Einzelheiten dazu finden Sie hier:

-   [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell]
-   [Azure PowerShell][Azure PowerShell]
-   [Build 2014: Automation Everywhere with New SDKs, Tools, and Services in Azure (Video in englischer Sprache)][Build 2014: Automation Everywhere with New SDKs, Tools, and Services in Azure (Video in englischer Sprache)]

Erstellen Sie PowerShell-Skripts direkt in Visual Studio, und verwenden Sie sie zur Automatisierung der Umgebungserstellung:

-   [Veröffentlichung in Entwicklungs- und Testumgebungen mithilfe von Windows PowerShell-Skripts][Veröffentlichung in Entwicklungs- und Testumgebungen mithilfe von Windows PowerShell-Skripts]

## <span id="mobile"></span></a>Mobile Entwicklung mit .NET

Azure Mobile Services verfügen nun über eine Option zur Verwendung von .NET-basierten Back-Ends für Ihre mobilen Anwendungen, die für mobile Plattformen wie Windows Store, Windows Phone, iOS und Android vorgesehen sind. Sehen Sie sich Folgendes an, und erfahren Sie mehr:

-   [Cloud Cover: The Azure Mobile Services .NET Backend (Video in englischer Sprache)][Cloud Cover: The Azure Mobile Services .NET Backend (Video in englischer Sprache)]
-   [Mobile Dev Center (Mobiles Entwicklungscenter, in englischer Sprache)][Mobile Dev Center (Mobiles Entwicklungscenter, in englischer Sprache)]

Visual Studio 2013 Update 2 enthält auch neue Unterstützung für die mobile Entwicklung einschließlich Remotedebuggen und Benachrichtigungs-Hubs-Integration in Server-Explorer. Weitere Informationen finden Sie unter:

-   [Schnellstart: Hinzufügen eines mobilen Dienstes][Schnellstart: Hinzufügen eines mobilen Dienstes]
-   [So wird's gemacht: Senden von Pushbenachrichtigungen an eine ausgeführte App mit Visual Studio][So wird's gemacht: Senden von Pushbenachrichtigungen an eine ausgeführte App mit Visual Studio]
-   [So wird's gemacht: Erstellen benutzerdefinierter APIs und geplanter Aufträge in mobilen Diensten][So wird's gemacht: Erstellen benutzerdefinierter APIs und geplanter Aufträge in mobilen Diensten]

## <span id="storage"></span></a>Storage Client Library 3.0

Azure SDK 2.3 enthält einen aktualisierten Speicheremulator. Außerdem ist die Storage Client Library 3.0 in die Projektvorlagen integriert, die im SDK enthalten sind.

Weitere Informationen finden Sie unter:

-   [Azure Storage Client Library 3.0][Azure Storage Client Library 3.0]
-   [Einführung in Azure Storage][Einführung in Azure Storage]
-   [Build 2014: Microsoft Azure Storage – What's New, Best Practices and Patterns (Video in englischer Sprache)][Build 2014: Microsoft Azure Storage – What's New, Best Practices and Patterns (Video in englischer Sprache)]
-   [Microsoft Azure Storage @ BUILD 2014][Microsoft Azure Storage @ BUILD 2014]

## <span id="arm"></span></a>Ressourcen-Manager

Ressourcen-Manager ist ein neues Framework zum ressourcenübergreifenden Bereitstellen und Verwalten von Anwendungen. Experimentieren Sie mit Ressourcen-Manager, indem Sie den neuen JSON-Editor, PowerShell-Cmdlets und die Unterstützung der Befehlszeilenschnittstelle verwenden. Weitere Informationen finden Sie hier:

-   [Using Windows PowerShell with Resource Manager (Verwenden von Windows PowerShell mit Ressourcen-Manager, in englischer Sprache)][Using Windows PowerShell with Resource Manager (Verwenden von Windows PowerShell mit Ressourcen-Manager, in englischer Sprache)]
-   [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]
-   [Build 2014: Azure Resource Group Model: Modern Management for Modern Cloud (Video in englischer Sprache)][Build 2014: Azure Resource Group Model: Modern Management for Modern Cloud (Video in englischer Sprache)]

  [Visual Studio 2013 Update 2 RC]: http://aka.ms/vs2013update2rc
  [Azure SDK 2.3]: http://www.windowsazure.com/de-de/downloads/
  [Windows Azure PowerShell]: http://go.microsoft.com/?linkid=9811175
  [Azure Plattformübergreifende Befehlszeilen-Schnittstelle]: http://go.microsoft.com/?linkid=9828653
  [Versionshinweise zu Azure SDK für .NET 2.3]: http://go.microsoft.com/fwlink/p/?LinkId=393548
  [Visual Studio 2013-Produktaktualisierungen]: http://go.microsoft.com/fwlink/?LinkId=272487
  [Sehen Sie sich Build-Videos an]: http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409
  [Webentwicklung und -veröffentlichung]: #webdeploy
  [Diagnose und Debuggen]: #diagnostics
  [Verwalten von Azure-Diensten in Visual Studio]: #service-management
  [Automatisieren mit PowerShell]: #automation
  [Mobile Entwicklung mit .NET]: #mobile
  [Speicherclientbibliothek 3.0 und neuer Speicheremulator]: #storage
  [Ressourcen-Manager]: #arm
  [Erste Schritte mit Azure und ASP.NET]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-dotnet-get-started/
  [Erste Schritte mit den Azure Tools für Visual Studio]: http://msdn.microsoft.com/de-de/library/azure/ff687127.aspx
  [Creating ASP.NET Web Projects in Visual Studio 2013 (Erstellen von ASP.NET-Webprojekten in Visual Studio 2013, in englischer Sprache)]: http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio
  [Build 2014: What's New for ASP.NET and Web in Visual Studio 2013 Update 2 and Beyond (Video in englischer Sprache)]: http://channel9.msdn.com/Events/Build/2014/3-602
  [Debuggen eines Cloud-Diensts oder eines virtuellen Computers in Visual Studio]: http://msdn.microsoft.com/de-de/library/azure/ff683670.aspx
  [Verwenden von Emulator Express zum lokalen Ausführen und Debuggen eines Cloud-Diensts]: http://msdn.microsoft.com/de-de/library/windowsazure/dn339018.aspx
  [Problembehandlung von Azure-Websites in Visual Studio]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio
  [Erstellen virtueller Azure-Computer in Visual Studio]: http://msdn.microsoft.com/de-de/library/windowsazure/dn569263.aspx
  [Zugreifen auf virtuelle Computer in Azure über den Server-Explorer]: http://msdn.microsoft.com/de-de/library/windowsazure/jj131259.aspx
  [Durchsuchen von Service Bus-Ressourcen mit dem Server-Explorer von Visual Studio]: http://msdn.microsoft.com/de-de/library/windowsazure/jj149828.aspx
  [Durchsuchen von Speicherressourcen mit Server-Explorer]: http://msdn.microsoft.com/de-de/library/windowsazure/ff683677.aspx
  [Installieren und Konfigurieren von Azure PowerShell]: http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156055.aspx
  [Build 2014: Automation Everywhere with New SDKs, Tools, and Services in Azure (Video in englischer Sprache)]: http://channel9.msdn.com/Events/Build/2014/3-621
  [Veröffentlichung in Entwicklungs- und Testumgebungen mithilfe von Windows PowerShell-Skripts]: http://msdn.microsoft.com/de-de/library/windowsazure/dn642480.aspx
  [Cloud Cover: The Azure Mobile Services .NET Backend (Video in englischer Sprache)]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-137-The-Azure-Mobile-Services-NET-Backend-with-Yavor-Georgiev
  [Mobile Dev Center (Mobiles Entwicklungscenter, in englischer Sprache)]: /de-de/develop/mobile/
  [Schnellstart: Hinzufügen eines mobilen Dienstes]: http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn629482.aspx
  [So wird's gemacht: Senden von Pushbenachrichtigungen an eine ausgeführte App mit Visual Studio]: http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn614131.aspx
  [So wird's gemacht: Erstellen benutzerdefinierter APIs und geplanter Aufträge in mobilen Diensten]: http://msdn.microsoft.com/de-de/library/windows/apps/xaml/dn614130.aspx
  [Azure Storage Client Library 3.0]: http://go.microsoft.com/fwlink/?LinkId=394927
  [Einführung in Azure Storage]: /de-de/documentation/articles/storage-introduction/
  [Build 2014: Microsoft Azure Storage – What's New, Best Practices and Patterns (Video in englischer Sprache)]: http://channel9.msdn.com/Events/Build/2014/3-628
  [Microsoft Azure Storage @ BUILD 2014]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/08/microsoft-azure-storage-build-2014.aspx
  [Using Windows PowerShell with Resource Manager (Verwenden von Windows PowerShell mit Ressourcen-Manager, in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=394767
  [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]: /de-de/documentation/articles/xplat-cli-azure-resource-manager/
  [Build 2014: Azure Resource Group Model: Modern Management for Modern Cloud (Video in englischer Sprache)]: http://channel9.msdn.com/Events/Build/2014/2-607
