<properties 
	pageTitle="Verwalten einer Azure-Website" 
	description="Links zu Ressourcen für die Verwaltung einer Microsoft Azure-Website" 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2014" 
	ms.author="mwasson"/>

# Verwalten einer Azure-Website

Dieses Thema enthält Links zu Ressourcen für die Verwaltung einer Azure-Website. Die Verwaltung umfasst alle Aufgaben, die für eine reibungslose Ausführung Ihrer Website sorgen. 

Im Laufe der Lebensdauer einer Website werden Sie verschiedene Verwaltungsaufgaben ausführen, von der ersten Bereitstellung zum Normalbetrieb, inklusive Wartung und Updates.

- [Bevor Sie Ihre Website für die Produktion bereitstellen]
- [Während Ihre Website ausgeführt wird]
- [Wenn Sie Ihre Website aktualisieren]

Viele Aufgaben der Websiteverwaltung können im Azure-Portal ausgeführt werden. Weitere Informationen finden Sie unter [Verwalten von Websites über das Azure-Verwaltungsportal](http://azure.microsoft.com/documentation/articles/web-sites-manage/).

## Bevor Sie Ihre Website für die Produktion bereitstellen

### Wählen Sie eine Ebene aus:

Azure Websites wird in vier Ebenen angeboten: Free, Shared, Basic und Standard. Weitere Informationen zu Funktionen und der Preisgestaltung für jede Ebene finden Sie unter [Preisdetails](http://azure.microsoft.com/pricing/details/websites/). 

- Mit [Webhostingplänen](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) können Sie mehrere Websites unter der gleichen Ebene zusammenfassen.
- Sie können immer zwischen [Ebenen wechseln](http://azure.microsoft.com/documentation/articles/web-sites-scale/), nachdem Sie Ihre Website erstellt haben.

### Konfiguration

Verwenden Sie das [Azure-Verwaltungsportal](https://manage.windowsazure.com/), um verschiedene Konfigurationsoptionen einzustellen. Nähere Informationen finden Sie unter [Konfigurieren von Websites](http://azure.microsoft.com/documentation/articles/web-sites-configure/). Hier sehen Sie eine kurze Checkliste:

- Wählen Sie **Laufzeitversionen** für .NET, PHP, Java oder Python aus, falls erforderlich.
- Aktivieren Sie **WebSockets**, wenn Ihre Website das WebSocket-Protokoll verwendet. (Dies umfasst Anwendungen, die [ASP.NET SignalR](http://www.asp.net/signalr) oder [socket.io](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-chat-app-socketio/) verwenden.)
- Lassen Sie kontinuierliche WebJobs laufen? Falls ja, aktivieren Sie **Always On**.
- Legen Sie das **Standarddokument** fest, z. B. "index.html".

Zusätzlich zu diesen grundlegenden Konfigurationseinstellungen können Sie Folgendes festlegen:

- **Secure Socket Layer (SSL)**-Verschlüsselung. Um SSL mit einem benutzerdefinierten Domänennamen zu verwenden, müssen Sie ein SSL-Zertifikat anfordern und Ihre Website entsprechend konfigurieren. Siehe [Aktivieren von HTTPS für eine Azure-Website](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/).
- **Nenutzerdefinierter Domänenname .** hre Website verfügt automatisch über eine Unterdomäne unter "azurewebsites.net". Sie können einen benutzerdefinierten Domänennamen, wie z. B. "contoso.com" zuordnen. Weitere Einzelheiten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/).

Sprachspezifische Konfiguration:

- **PHP**: [Konfigurieren von PHP in Azure-Websites](http://azure.microsoft.com/documentation/articles/web-sites-php-configure/).
- **Python**: [Konfigurieren von Python mit Azure-Websites](http://azure.microsoft.com/documentation/articles/web-sites-python-configure/)


## Während Ihre Website ausgeführt wird

Beim Betrieb Ihrer Website müssen Sie sicherstellen, dass sie verfügbar und dem Benutzerverkehr entsprechend skaliert ist. Möglicherweise müssen Sie auch Fehler beheben.

### Überwachung

- Über das Verwaltungsportal können Sie [Leistungsmetriken hinzufügen](http://azure.microsoft.com/documentation/articles/web-sites-monitor), wie CPU-Auslastung und Anzahl der Clientanforderungen.
- Für einen tieferen Einblick verwenden Sie New Relic zur Leistungsüberwachung und -verwaltung. Siehe [Verwaltung der Anwendungsleistung mit New Relic in Azure Websites](http://azure.microsoft.com/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Skalieren Sie Ihre Website](http://azure.microsoft.com/documentation/articles/web-sites-scale/) entsprechend dem Datenverkehr. Abhängig von Ihrer Ebene können Sie die Anzahl von VMs und/oder die Größe der VM-Instanzen skalieren. Im Standardplan können Sie auch Autoskalierung einstellen, sodass Ihre Website automatisch skaliert, entweder nach einem festen Zeitplan oder entsprechend der Last.  
 
### Backups

- Legen Sie [automatische Sicherungen](http://azure.microsoft.com/documentation/articles/web-sites-backup/) Ihrer Website fest. [In diesem Video](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/) erfahren Sie mehr über Sicherungen.
- Erfahren Sie mehr über die Optionen für die [Datenbankwiederherstellung](http://msdn.microsoft.com/library/azure/hh852669.aspx) in der Azure SQL-Datenbank.

### Problembehandlung

- Wenn etwas nicht funktioniert, können Sie unter Verwendung von Diagnoseprotokollen und durch Live-Debugging in der Cloud [Probleme in Visual Studio behandeln](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug). 
- Außerhalb von Visual Studio gibt es verschiedene Möglichkeiten, Diagnoseprotokolle zu sammeln. Siehe [Aktivieren der Diagnoseprotokollierung für Azure Websites](http://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).
- Für Node.js-Anwendungen siehe [Debuggen einer Node.js-Anwendung in Azure Websites](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-debug/).

### Datenwiederherstellung

- Sie können eine Website [wiederherstellen](http://azure.microsoft.com/documentation/articles/web-sites-restore/), die zuvor gesichert wurde.


## Wenn Sie Ihre Website aktualisieren

Wenn Sie keine automatische Sicherungen aktiviert haben, können Sie eine [manuelle Sicherung](http://azure.microsoft.com/documentation/articles/web-sites-backup/) erstellen.

Ziehen Sie eine [gestaffelte Bereitstellung](http://azure.microsoft.com/documentation/articles/web-sites-staged-publishing/) in Betracht. Mit dieser Option können Sie Updates in Form einer gestaffelten Bereitstellung veröffentlichen, die parallel zu Ihrer Produktionsbereitstellung ausgeführt wird. 

Wenn Sie Visual Studio One verwenden, können Sie die fortlaufende Bereitstellung aus der Quellcodeverwaltung einstellen:

- [Verwenden von Team Foundation Version Control (TFVC)](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [Verwenden von Git](http://azure.microsoft.com/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Bevor Sie Ihre Website für die Produktion bereitstellen]: #before-you-deploy-your-site-to-production
[Während Ihre Website ausgeführt wird]: #while-your-website-is-running
[Wenn Sie Ihre Website aktualisieren]: #when-you-update-your-website

 



<!--HONumber=42-->
