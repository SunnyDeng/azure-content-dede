<properties title="Manage an Azure website" pageTitle="Manage an Azure website" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# Verwalten einer Azure-Website

Dieses Thema enthält Links zu Ressourcen für die Verwaltung einer Azure-Website. Die Verwaltung umfasst alle Aufgaben, die für eine reibungslose Ausführung Ihrer Website sorgen.

Im Laufe der Lebensdauer einer Website werden Sie verschiedene Verwaltungsaufgaben ausführen, von der ersten Bereitstellung zum Normalbetrieb, inklusive Wartung und Updates.

-   [Bevor Sie Ihre Website für die Produktion bereitstellen][Bevor Sie Ihre Website für die Produktion bereitstellen]
-   [Während Ihre Website ausgeführt wird][Während Ihre Website ausgeführt wird]
-   [Wenn Sie Ihre Website aktualisieren][Wenn Sie Ihre Website aktualisieren]

Viele Aufgaben der Websiteverwaltung können im Azure-Portal ausgeführt werden. Weitere Informationen finden Sie unter [Websites verwalten über das Azure-Verwaltungsportal][Websites verwalten über das Azure-Verwaltungsportal].

## Bevor Sie Ihre Website für die Produktion bereitstellen

### Wählen Sie eine Ebene aus:

Azure Websites wird in vier Ebenen angeboten: Free, Shared, Basic und Standard. Weitere Informationen zu Funktionen und der Preisgestaltung für jede Ebene finden Sie unter [Preisdetails][Preisdetails].

-   Mit [Webhostingplänen][Webhostingplänen] können Sie Websites unter der gleichen Ebene zusammenfassen.
-   Sie können immer [zwischen Ebenen wechseln][zwischen Ebenen wechseln], nachdem Sie Ihre Website erstellt haben.

### Konfiguration

Verwenden Sie das [Azure-Verwaltungsportal,][Azure-Verwaltungsportal,] um verschiedene Konfigurationsoptionen einzustellen. Nähere Informationen finden Sie unter [Konfigurieren von Websites][Konfigurieren von Websites]. Hier sehen Sie eine kurze Checkliste:

-   Wählen Sie **Laufzeitversionen** für .NET, PHP, Java oder Python aus, falls erforderlich.
-   Aktivieren Sie **WebSockets**, wenn Ihre Website das WebSocket-Protokoll verwendet. (Dies beinhaltet Anwendungen, die [ASP.NET SignalR][ASP.NET SignalR] oder [socket.io][socket.io] verwenden.)
-   Lassen Sie kontinuierliche WebJobs laufen? Falls dies der Fall ist, aktivieren Sie **Immer bereit**.
-   Stellen Sie das **Standarddokument** ein, beispielsweise index.html.

Zusätzlich zu diesen grundlegenden Konfigurationseinstellungen können Sie Folgendes festlegen:

-   **Secure Socket Layer (SSL)**-Verschlüsselung. Um SSL mit einem benutzerdefinierten Domänennamen zu verwenden, müssen Sie ein SSL-Zertifikat anfordern und Ihre Website entsprechend konfigurieren. Siehe [Aktivieren von HTTPS für eine Azure-Website][Aktivieren von HTTPS für eine Azure-Website].
-   **Benutzerdefinierte Domänennamen.** Zu Ihrer Website gibt es automatisch eine Unterdomäne unter azurewebsites.net. Sie können einen benutzerdefinierten Domänennamen zuweisen, beispielsweise contoso.com. Siehe [Konfigurieren eines benutzerdefinierten Domänennamens][Konfigurieren eines benutzerdefinierten Domänennamens].

Sprachspezifische Konfiguration:

-   **PHP**: [Konfigurieren von PHP mit Azure Websites][Konfigurieren von PHP mit Azure Websites].
-   **Python**: [Konfigurieren von Python mit Azure Websites][Konfigurieren von Python mit Azure Websites]

## Während Ihre Website ausgeführt wird

Beim Betrieb Ihrer Website müssen Sie sicherstellen, dass sie verfügbar und dem Benutzerverkehr entsprechend skaliert ist. Möglicherweise müssen Sie auch Fehler beheben.

### Überwachung

-   Über das Verwaltungsportal können Sie [Leistungsmetriken hinzufügen,][Leistungsmetriken hinzufügen,] wie CPU-Auslastung und Anzahl der Clientanforderungen.
-   Für einen tieferen Einblick verwenden Sie New Relic zur Leistungsüberwachung und -verwaltung. Siehe [Verwaltung der Anwendungsleistung mit New Relic in Azure Websites][Verwaltung der Anwendungsleistung mit New Relic in Azure Websites].
-   [Skalieren Sie Ihre Website][zwischen Ebenen wechseln] entsprechend dem Verkehr. Abhängig von Ihrer Ebene können Sie die Anzahl von VMs und/oder die Größe der VM-Instanzen skalieren. Im Standardplan können Sie auch Autoskalierung einstellen, sodass Ihre Website automatisch skaliert, entweder nach einem festen Zeitplan oder entsprechend der Last.

### Backups

-   Einstellen von [automatischen Backups][automatischen Backups] Ihrer Website. Erfahren Sie in [diesem Video][diesem Video] mehr über Backups.
-   Erfahren Sie mehr über die Optionen für die [Datenbankwiederherstellung][Datenbankwiederherstellung] in der Azure SQL-Datenbank.

### Problembehandlung

-   Wenn etwas nicht funktioniert, können Sie [Probleme in Visual Studio behandeln][Probleme in Visual Studio behandeln], unter Verwendung von Diagnoseprotokollen und durch Live-Debuggen in der Cloud.
-   Außerhalb von Visual Studio gibt es verschiedene Möglichkeiten, Diagnoseprotokolle zu sammeln. Siehe [Aktivieren der Diagnoseprotokollierung für Azure Websites][Aktivieren der Diagnoseprotokollierung für Azure Websites].
-   Für Node.js-Anwendungen siehe [Debuggen einer Node.js-Anwendung in Azure Websites][Debuggen einer Node.js-Anwendung in Azure Websites].

### Datenwiederherstellung

-   [Wiederherstellen][Wiederherstellen] einer Website, die vorher gesichert wurde.

## Wenn Sie Ihre Website aktualisieren

Wenn Sie automatische Sicherungen nicht aktiviert haben, können Sie eine [manuelle Sicherung][automatischen Backups] erstellen.

Ziehen Sie eine [gestaffelte Bereitstellung][gestaffelte Bereitstellung] in Betracht. Mit dieser Option können Sie Updates in Form einer gestaffelten Bereitstellung veröffentlichen, die parallel zu Ihrer Produktionsbereitstellung ausgeführt wird.

Wenn Sie Visual Studio One verwenden, können Sie die fortlaufende Bereitstellung aus der Quellcodeverwaltung einstellen:

-   [Verwendung von Team Foundation Version Control (TFVC)][Verwendung von Team Foundation Version Control (TFVC)]
-   [Verwendung von Git][Verwendung von Git]



  [Bevor Sie Ihre Website für die Produktion bereitstellen]: #before-you-deploy-your-site-to-production
  [Während Ihre Website ausgeführt wird]: #while-your-website-is-running
  [Wenn Sie Ihre Website aktualisieren]: #when-you-update-your-website
  [Websites verwalten über das Azure-Verwaltungsportal]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-manage/
  [Preisdetails]: http://azure.microsoft.com/de-de/pricing/details/websites/
  [Webhostingplänen]: http://azure.microsoft.com/de-de/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
  [zwischen Ebenen wechseln]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-scale/
  [Azure-Verwaltungsportal,]: https://manage.windowsazure.com/
  [Konfigurieren von Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Aktivieren von HTTPS für eine Azure-Website]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure-ssl-certificate/
  [Konfigurieren eines benutzerdefinierten Domänennamens]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-custom-domain-name/
  [Konfigurieren von PHP mit Azure Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-php-configure/
  [Konfigurieren von Python mit Azure Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-python-configure/
  [Leistungsmetriken hinzufügen,]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-monitor
  [Verwaltung der Anwendungsleistung mit New Relic in Azure Websites]: http://azure.microsoft.com/de-de/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [automatischen Backups]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/
  [diesem Video]: http://azure.microsoft.com/de-de/documentation/videos/azure-websites-automatic-and-easy-backup/
  [Datenbankwiederherstellung]: http://msdn.microsoft.com/de-de/library/azure/hh852669.aspx
  [Probleme in Visual Studio behandeln]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [Aktivieren der Diagnoseprotokollierung für Azure Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-enable-diagnostic-log/
  [Debuggen einer Node.js-Anwendung in Azure Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-nodejs-debug/
  [Wiederherstellen]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-restore/
  [gestaffelte Bereitstellung]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-staged-publishing/
  [Verwendung von Team Foundation Version Control (TFVC)]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Verwendung von Git]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
