<properties title="Manage an Azure website" pageTitle="Verwalten einer Azure-Website" description="Links zu Ressourcen für die Verwaltung einer Microsoft Azure-Website" services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# Verwalten einer Azure-Website

Dieses Thema enthält Links zu Ressourcen für die Verwaltung einer Azure-Website. Die Verwaltung umfasst alle Aufgaben, die für eine reibungslose Ausführung Ihrer Website sorgen. 

Im Laufe der Lebensdauer einer Website werden Sie verschiedene Verwaltungsaufgaben ausführen, von der ersten Bereitstellung zum Normalbetrieb, inklusive Wartung und Updates.

- [Bevor Sie Ihre Website für die Produktion bereitstellen]
- [Während Ihre Website ausgeführt wird]
- [Wenn Sie Ihre Website aktualisieren]

Viele Aufgaben der Websiteverwaltung können im Azure-Portal ausgeführt werden. Weitere Informationen finden Sie unter [Websites verwalten über das Azure-Verwaltungsportal](http://azure.microsoft.com/en-us/documentation/articles/web-sites-manage/).

## Bevor Sie Ihre Website für die Produktion bereitstellen

### Wählen Sie eine Ebene aus:

Azure Websites wird in vier Ebenen angeboten: Free, Shared, Basic und Standard. Weitere Informationen zu Funktionen und der Preisgestaltung für jede Ebene finden Sie unter [Preisdetails](http://azure.microsoft.com/en-us/pricing/details/websites/). 

Mit - [Webhostingplänen](http://azure.microsoft.com/en-us/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview) können Sie mehrere Websites unter der gleichen Ebene zusammenfassen.
- Sie können jederzeit [zwischen Ebenen wechseln](http://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/), nachdem Sie Ihre Website erstellt haben.

### Konfiguration

Verwenden Sie das [Azure-Verwaltungsportal](https://manage.windowsazure.com/), um verschiedene Konfigurationsoptionen einzustellen. Nähere Informationen finden Sie unter [Konfigurieren von Websites](http://azure.microsoft.com/en-us/documentation/articles/web-sites-configure/). Hier sehen Sie eine kurze Checkliste:

- Wählen Sie **Laufzeitversionen** für .NET, PHP, Java oder Python aus, falls erforderlich.
- Aktivieren Sie **WebSockets**, wenn Ihre Website das WebSocket-Protokoll verwendet. (Dies beinhaltet Anwendungen, die [ASP.NET SignalR](http://www.asp.net/signalr) oder [socket.io](http://azure.microsoft.com/en-us/documentation/articles/web-sites-nodejs-chat-app-socketio/) verwenden.)
- Lassen Sie kontinuierliche WebJobs laufen? Falls dies der Fall ist, aktivieren Sie **Immer bereit**.
- Stellen Sie das **Standarddokument** ein, beispielsweise "index.html".

Zusätzlich zu diesen grundlegenden Konfigurationseinstellungen können Sie Folgendes festlegen:

- **Secure Socket Layer (SSL)**-Verschlüsselung. Um SSL mit einem benutzerdefinierten Domänennamen zu verwenden, müssen Sie ein SSL-Zertifikat anfordern und Ihre Website entsprechend konfigurieren. Siehe [Aktivieren von HTTPS für eine Azure-Website](http://azure.microsoft.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).
- **Benutzerdefinierte Domänennamen.**Ihre Website verfügt automatisch über eine Unterdomäne unter "azurewebsites.net". Sie können einen benutzerdefinierten Domänennamen, wie z. B. "contoso.com" zuordnen. Weitere Einzelheiten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens](http://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/).

Sprachspezifische Konfiguration:

- **PHP**: [Konfigurieren von PHP mit Azure Websites](http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-configure/).
- **Python**: [Konfigurieren von Python mit Azure-Websites](http://azure.microsoft.com/en-us/documentation/articles/web-sites-python-configure/)


## Während Ihre Website ausgeführt wird

Beim Betrieb Ihrer Website müssen Sie sicherstellen, dass sie verfügbar und dem Benutzerverkehr entsprechend skaliert ist. Möglicherweise müssen Sie auch Fehler beheben.

### Überwachung

- Über das Verwaltungsportal können Sie [Leistungsmetriken hinzufügen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-monitor), wie CPU-Auslastung und Anzahl der Clientanforderungen.
- Für einen tieferen Einblick verwenden Sie New Relic zur Leistungsüberwachung und -verwaltung. Siehe [Verwaltung der Anwendungsleistung mit New Relic in Azure Websites](http://azure.microsoft.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
- [Skalieren Sie Ihre Website](http://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/) entsprechend dem Verkehr. Abhängig von Ihrer Ebene können Sie die Anzahl von VMs und/oder die Größe der VM-Instanzen skalieren. Im Standardplan können Sie auch Autoskalierung einstellen, sodass Ihre Website automatisch skaliert, entweder nach einem festen Zeitplan oder entsprechend der Last. 
 
### Backups

- Stellen Sie [automatische Sicherungen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-backup/) Ihrer Website ein. Erfahren Sie in [diesem Video](http://azure.microsoft.com/en-us/documentation/videos/azure-websites-automatic-and-easy-backup/) mehr über Sicherungen.
- Erfahren Sie mehr über die Optionen für die [Datenbankwiederherstellung](http://msdn.microsoft.com/en-us/library/azure/hh852669.aspx) in der Azure SQL-Datenbank.

### Problembehandlung

- Wenn etwas nicht funktioniert, können Sie [Probleme in Visual Studio behandeln](http://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug), unter Verwendung von Diagnoseprotokollen und durch Live-Debuggen in der Cloud. 
- Außerhalb von Visual Studio gibt es verschiedene Möglichkeiten, Diagnoseprotokolle zu sammeln. Siehe [Aktivieren der Diagnoseprotokollierung für Azure Websites](http://azure.microsoft.com/en-us/documentation/articles/web-sites-enable-diagnostic-log/).
- Für Node.js-Anwendungen siehe [Debuggen einer Node.js-Anwendung in Azure Websites](http://azure.microsoft.com/en-us/documentation/articles/web-sites-nodejs-debug/).

### Datenwiederherstellung

- [Wiederherstellen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-restore/) einer Website, die vorher gesichert wurde.


## Wenn Sie Ihre Website aktualisieren

Wenn Sie automatische Sicherungen nicht aktiviert haben, können Sie eine [manuelle Sicherung](http://azure.microsoft.com/en-us/documentation/articles/web-sites-backup/) erstellen.

Ziehen Sie eine [gestaffelte Bereitstellung](http://azure.microsoft.com/en-us/documentation/articles/web-sites-staged-publishing/) in Betracht. Mit dieser Option können Sie Updates in Form einer gestaffelten Bereitstellung veröffentlichen, die parallel zu Ihrer Produktionsbereitstellung ausgeführt wird. 

Wenn Sie Visual Studio One verwenden, können Sie die fortlaufende Bereitstellung aus der Quellcodeverwaltung einstellen:

- [Verwendung von Team Foundation Version Control (TFVC)](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [Verwendung von Git](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[Bevor Sie Ihre Website für die Produktion bereitstellen]: #before-you-deploy-your-site-to-production
[Während Ihre Website ausgeführt wird]: #while-your-website-is-running
[Wenn Sie Ihre Website aktualisieren]: #when-you-update-your-website

 
