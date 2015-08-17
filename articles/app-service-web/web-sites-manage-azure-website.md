<properties 
	pageTitle="Verwalten einer Web-App in Azure App Service" 
	description="Links zu Ressourcen für die Verwaltung einer Web-App in Azure App Service." 
	services="app-service\web" 
	documentationCenter="" 
	authors="erikre" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="erikre"/>

# Verwalten einer Web-App in Azure App Service

Dieses Thema enthält Links zu Ressourcen für die Verwaltung einer Web-App in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Die Verwaltung umfasst alle Aufgaben, die für eine reibungslose Ausführung Ihrer Web-App sorgen.

Im Laufe der Lebensdauer einer Web-App führen Sie verschiedene Verwaltungsaufgaben aus, von der ersten Bereitstellung zum Normalbetrieb, einschließlich Wartung und Aktualisierungen.

Viele Aufgaben der Web-App-Verwaltung können im Azure-Portal ausgeführt werden. Weitere Informationen finden Sie unter [Verwalten von Web-Apps mithilfe des Azure-Portals](web-sites-manage.md).

## Bevor Sie Ihre Web-App für die Produktion bereitstellen

### Wählen Sie eine Ebene aus:

Azure App Service wird in fünf Stufen angeboten: Kostenlos, Freigegeben, Basic, Standard und Premium. Weitere Informationen zu Funktionen und der Preisgestaltung für jede Ebene finden Sie unter [Preisdetails](/pricing/details/app-service/).

- Mit [App Service-Plänen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) können Sie mehrere Web-Apps unter der gleichen Ebene zusammenfassen.
- Sie können jederzeit [zwischen Ebenen wechseln](web-sites-scale.md), nachdem Sie Ihre Web-App erstellt haben.

### Konfiguration

Verwenden Sie das [Azure-Vorschauportal,](https://portal.azure.com/) um verschiedene Konfigurationsoptionen einzustellen. Weitere Informationen finden Sie unter [Konfigurieren von Web-Apps in Azure App Service](web-sites-configure.md). Hier sehen Sie eine kurze Checkliste:

- Wählen Sie **Laufzeitversionen** für .NET, PHP, Java oder Python aus, falls erforderlich.
- Aktivieren Sie **WebSockets**, wenn Ihre Web-App das WebSocket-Protokoll verwendet. (Dies beinhaltet Anwendungen, die [ASP.NET SignalR](http://www.asp.net/signalr) oder [socket.io](web-sites-nodejs-chat-app-socketio.md) verwenden.)
- Lassen Sie kontinuierliche WebJobs laufen? Falls dies der Fall ist, aktivieren Sie **Immer bereit**.
- Stellen Sie das **Standarddokument** ein, beispielsweise index.html.

Zusätzlich zu diesen grundlegenden Konfigurationseinstellungen können Sie Folgendes festlegen:

- **Secure Socket Layer (SSL)**-Verschlüsselung. Um SSL mit einem benutzerdefinierten Domänennamen zu verwenden, müssen Sie ein SSL-Zertifikat anfordern und Ihre Web-App entsprechend konfigurieren. Weitere Informationen finden Sie unter [Aktivieren von HTTPS für eine Web-App in Azure App Service](web-sites-configure-ssl-certificate.md).
- **Benutzerdefinierte Domänennamen.** Zu Ihrer Web-App gibt es automatisch eine Unterdomäne unter azurewebsites.net. Sie können einen benutzerdefinierten Domänennamen zuweisen, beispielsweise contoso.com. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service](web-sites-custom-domain-name.md).

Sprachspezifische Konfiguration:

- **PHP**: [Konfigurieren von PHP in Azure App Service-Web-Apps](web-sites-php-configure.md).
- **Python**: [Konfigurieren von Python in Azure App Service-Web-Apps](web-sites-python-configure.md).


## Während Ihre Web-App ausgeführt wird

Bei der Ausführung Ihrer Web-App müssen Sie sicherstellen, dass sie verfügbar und dem Benutzerverkehr entsprechend skaliert ist. Möglicherweise müssen Sie auch Fehler beheben.

### Überwachung

- Über das Azure-Vorschauportal können Sie [Leistungsmetriken hinzufügen](web-sites-monitor.md), wie z. B. CPU-Auslastung und Anzahl der Clientanforderungen.
- Für einen tieferen Einblick verwenden Sie New Relic zur Leistungsüberwachung und -verwaltung. Weitere Informationen finden Sie unter [.NET Web-App in Azure App Service mit Verwaltung der Anwendungsleistung mit New Relic](store-new-relic-web-sites-dotnet-application-performance-management.md).
- [Skalieren Sie Ihre Web-App](web-sites-scale.md) entsprechend dem Verkehr. Abhängig von Ihrer Ebene können Sie die Anzahl von VMs und/oder die Größe der VM-Instanzen skalieren. Auf den Stufen Standard oder Premium können Sie auch automatische Skalierung einrichten, sodass Ihre Website automatisch skaliert wird, entweder nach einem festen Zeitplan oder entsprechend der Last.  
 
### Backups

- Legen Sie [automatische Sicherungen](web-sites-backup.md) Ihrer Web-App fest. Erfahren Sie in [diesem Video](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/) mehr über Backups.
- Erfahren Sie mehr über die Optionen für die [Datenbankwiederherstellung](http://msdn.microsoft.com/library/azure/hh852669.aspx) in der Azure SQL-Datenbank.

### Problembehandlung

- Wenn etwas nicht funktioniert, können Sie [Probleme in Visual Studio behandeln](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), unter Verwendung von Diagnoseprotokollen und durch Live-Debuggen in der Cloud. 
- Außerhalb von Visual Studio gibt es verschiedene Möglichkeiten, Diagnoseprotokolle zu sammeln. Weitere Informationen finden Sie unter [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service](web-sites-enable-diagnostic-log.md).
- Für Node.js-Anwendungen siehe [Debuggen einer Node.js-Web-App in Azure App Service](web-sites-nodejs-debug.md).

### Datenwiederherstellung

- [Wiederherstellen](web-sites-restore.md) einer Web-App, die zuvor gesichert wurde.


## Wenn Sie Ihre Web-App aktualisieren

Wenn Sie automatische Sicherungen nicht aktiviert haben, können Sie eine [manuelle Sicherung](web-sites-backup.md) erstellen.

Ziehen Sie eine [gestaffelte Bereitstellung](web-sites-staged-publishing.md) in Betracht. Mit dieser Option können Sie Updates in Form einer gestaffelten Bereitstellung veröffentlichen, die parallel zu Ihrer Produktionsbereitstellung ausgeführt wird.

Wenn Sie Visual Studio One verwenden, können Sie die fortlaufende Bereitstellung aus der Quellcodeverwaltung einstellen:

- [Verwendung von Team Foundation Version Control (TFVC)](../cloud-services-continuous-delivery-use-vso.md) 
- [Verwendung von Git](../cloud-services-continuous-delivery-use-vso-git.md)
 
[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
<!-- Anchors. -->


[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  

<!---HONumber=August15_HO6-->