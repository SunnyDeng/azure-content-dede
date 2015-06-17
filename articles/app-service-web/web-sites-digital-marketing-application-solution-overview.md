<properties 
	pageTitle="Erstellen digitaler Marketingkampagnen in Azure App Service-Web-Apps" 
	description="Dieser Leitfaden bietet eine technische Übersicht über die Verwendung von Azure App Service-Web-Apps zum Erstellen von digitaler Marketingkampagnen. Dazu gehören die Bereitstellung, die Integration von sozialen Medien, Skalierungsstrategien und die Überwachung." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="cephalin"/>

# Erstellen digitaler Marketingkampagnen in Azure App Service-Web-Apps
[Azure Anwendungsdiensts](http://go.microsoft.com/fwlink/?LinkId=529714) Web-Apps ist eine gute Wahl für digitale Marketingkampagnen. Digitale Marketingkampagnen sind üblicherweise von kurzer Dauer, da sie kurzfristige Marketingziele fördern sollen. Zwei wichtige Szenarien müssen berücksichtigt werden. Beim ersten Szenario erstellt ein fremdes Marketingunternehmen die Kampagne für ihre Kunden und verwaltet diese während der Aktionsdauer. Beim zweiten Szenario erstellt das Marketingunternehmen die Kampagne und überträgt anschließend den Besitz der Ressourcen für die digitale Marketingkampagne auf ihren Kunden. Danach führt der Kunde die digitale Marketingkampagne aus und verwaltet diese selbst. Azure-Websites eignet sich für beide Szenarien.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Nachstehend sehen Sie ein Beispiel für eine globale, über mehrere Kanäle durchgeführte digitale Marketingkampagne unter Verwendung von App Service-Web-Apps. Dieses Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach App Service-Web-Apps mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]In diesem Leitfaden werden einige der häufigsten Themenbereiche und Aufgaben im Zusammenhang mit der Ausführung einer digitalen Marketingkampagne in Azure App Service-Web-Apps vorgestellt. Es gibt jedoch andere gängige Lösungen, die Sie in App Service-Web-Apps implementieren können. Diese Lösungen finden Sie in den anderen Leitfäden für [globalen Webpräsenz](web-sites-global-web-presence-solution-overview.md) und [Geschäftsanwendungen](web-sites-business-application-solution-overview.md).

## Neuerstellung oder Nutzen von vorhandenen Ressourcen

Sie können entweder mithilfe eines gängigen CMS aus dem Katalog neue Web-Apps erstellen, oder Sie verschieben Ihre vorhandenen Webressourcen in App Service-Web-Apps – in verschiedenen Sprachen und mit verschiedenen Frameworks.

Azure Marketplace stellt Vorlagen von der beliebten Website Content Management System (CMS), wie z. B. [Orchard], [Umbraco], [Drupal], und [WordPress]. Sie können eine Web-App mit Ihrem bevorzugten CMS erstellen. Sie können aus verschiedenen Datenbank-Back-Ends für Ihre Anforderungen einschließlich [Azure SQL-Datenbank] und [MySQL].

Ihre vorhandenen Webressourcen können in Web-Apps ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Können Sie diese in Webanwendungen mithilfe der vertrauten verschieben [FTP] Tools. Wenn Sie häufig digitale Marketingkampagnen erstellen, verfügen Sie möglicherweise bereits über Webressourcen in einem Quellcodeverwaltungssystem. Zu Bereitstellen von Webanwendungen direkt von gängigen Quellcodeverwaltungsoptionen, wie z. B. [Visual Studio], [Visual Studio Online], und [Git] (lokal, GitHub, BitBucket, DropBox, Mercurial, etc..).

## Bleiben Sie flexibel

Bleiben Sie flexibel, indem Sie eine kontinuierliche Veröffentlichung aus Ihrem vorhandenen Quellcodeverwaltungssystem und A/B-Tests in App Service-Web-Apps durchführen.

Während der Planung, Erstellung von Prototypen und frühen Entwicklungsphase einer Webanwendung, Sie und Ihre Kunden sehen tatsächliche Versionen der app Kampagne er vor der durch Aktivierung [Bereitstellen in staging-Steckplatz] Ihrer Webanwendung. Durch die Integration von Quellcodeverwaltung App Service-Webanwendungen, können Sie [Stetiges Veröffentlichen von] auf einem Staging-Steckplatz, und tauschen sie in der Produktionsumgebung ohne Ausfallzeiten, wenn er bereit ist.

Außerdem bei der Planung von Änderungen an einer live-Web-app können Sie problemlos [Ausführen A / B-tests] zu den vorgeschlagenen Updates mithilfe des Tests in der Produktion feature in und Analysieren von echten Benutzerverhalten, damit Sie fundierte Entscheidungen auf app-Design.


## Integration in soziale Netzwerke

Ihre digitale Marketingkampagne in App Service-Web-Apps kann durch Authentifizierung bei gängigen Anbietern wie Facebook und Twitter in soziale Netzwerke integriert werden. Ein Beispiel dieses Ansatzes mit einer ASP.NET-Anwendung finden Sie unter [eine ASP.NET MVC-app mit Authentifizierung und SQL-Datenbank erstellen und Bereitstellen für Azure-App-Dienst].

Darüber hinaus werden auf den Websites sozialer Netzwerke üblicherweise Informationen zu weiteren Möglichkeiten der Integration aus .NET und vielen anderen Frameworks bereitgestellt.

## Verwenden von Rich-Media zum Erreichen aller Geräte

Bereichern Sie Ihre digitale Marketingkampagne mit anderen Azure-Diensten, wie z. B.:

-  Hochladen und Streamen von Videos Global mit [Azure Media Services]
-  Senden von e-Mails an Benutzer mit [SendGrid-Dienst in Azure Marketplace]
-  Einrichten der Anwesenheit Windows-, IOS- und Android-Geräte mit [Mobile Services]
-  Senden von Pushbenachrichtigungen an Millionen von Geräten mit [Benachrichtigungshub]

## Globale Präsenz

Zeigen Sie globale Präsenz durch die Bereitstellung regionaler Websites mit Azure Traffic Manager, und stellen Sie mit Azure CDN blitzschnell Inhalte bereit.

Um Kunden in den jeweiligen Regionen weltweit bereitzustellen, verwenden Sie [Azure Traffic Manager] Route Besuchern der Website auf einen regionalen Standort, die die beste Leistung bietet. Sie können auch die Websitelast gleichmäßig auf mehrere Kopien Ihrer Web-App verteilen, die in verschiedenen Regionen gehostet werden.

Übermitteln Sie Ihre statischen Inhalt Blitz für Benutzer schnell Global von [Integrieren von Ihrer Anwendung in Azure CDN]. Azure CDN zwischengespeichert, statischen Inhalt in die [CDN-Knoten] die den Benutzer minimiert die Wartezeit und Verbindungen zu Ihrer Webanwendung am nächsten.

## Optimierung

Optimieren Sie Ihre Web-App über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure-Redis-Cache und die Ausführung von Hintergrundaufgaben mithilfe von Webaufträgen, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die Fähigkeit des App-Service-Webanwendungen zu [Skalieren heraus] ist perfekt für unvorhersehbaren Arbeitslasten bei digitalen Marketingkampagnen. Skalieren Sie Ihre Web-app manuell durch die [Azure Preview-Portal](http://go.microsoft.com/fwlink/?LinkId=529715), programmgesteuert über die [Dienstverwaltungs-API] oder [PowerShell-Skripting], oder automatisch durch die automatische Skalierung. Im Modus **Standard** können Sie eine Web-App mithilfe von „Autoskalierung“ basierend auf der CPU-Auslastung automatisch horizontal hochskalieren. Diese Funktion ermöglicht maximale Flexibilität bei minimalen Kosten, da die Web-App nur dann skaliert wird, wenn dies basierend auf der Benutzeraktivität erforderlich ist. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Erhöhen Sie die Reaktionsfähigkeit Ihrer Web-App mit [Azure Redis Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Stellen Sie mithilfe von [Azure Traffic Manager] eine hohe Verfügbarkeit Ihrer Web-App sicher. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

## Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Web-App. Empfangen Sie Warnungen zu kritischen Web-App-Ereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight.

Im Blatt der Web-App im [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) erhalten Sie eine [Übersicht] über die aktuellen Leistungskennzahlen und Ressourcenkontingente der Web-App. Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Sie können auch ein Drittanbietertool wie [New Relic] nutzen, um weiterführende Überwachungsdaten zu Ihren Web-Apps zu erhalten.

In der **Standard** Ebene, app-Reaktionsfähigkeit Monitor e-Mail-Benachrichtigungen, wenn Ihre Webanwendung nicht mehr reagiert. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure].

## Weitere Ressourcen

- [App Service-Web-Apps-Dokumentation](/services/app-service/web/)
- [Lernkonzept für Azure App Service-Web-Apps](websites-learning-map.md)
- [Azure-Web-Blog](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[Drupal]: web-sites-php-migrate-drupal.md
[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL-Datenbank]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[Bereitstellen in staging-Steckplatz]: web-sites-staged-publishing.md
[Stetiges Veröffentlichen von]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[Ausführen A / B-tests]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[eine ASP.NET MVC-app mit Authentifizierung und SQL-Datenbank erstellen und Bereitstellen für Azure-App-Dienst]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid-Dienst in Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md
[Mobile Services]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Benachrichtigungshub]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integrieren von Ihrer Anwendung in Azure CDN]: cdn-websites-with-cdn.md
[CDN-Knoten]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[Skalieren heraus]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[Dienstverwaltungs-API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell-Skripting]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET-Sitzungszustand]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[Ausgabecache]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[Übersicht]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->