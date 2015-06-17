<properties 
	pageTitle="Erstellen einer globalen Webpräsenz in Azure App Service-Web-Apps" 
	description="Dieser Leitfaden bietet einen technischen Überblick über das Hosten Ihrer Organisationswebsite (.com) in Azure App Service-Web-App. Dies umfasst die Bereitstellung, benutzerdefinierte Domänen, SSL und die Überwachung." 
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


# Erstellen einer globalen Webpräsenz in Azure App Service-Web-Apps

[Azure Anwendungsdiensts](http://go.microsoft.com/fwlink/?LinkId=529714) Webanwendungen bietet alle Funktionen, die Sie eine globalen Webpräsenz für .COM-Website herstellen müssen. Unabhängig von der Größe Ihrer Organisation benötigen Sie eine stabile, sichere und skalierbare Plattform auf Ihr Unternehmen, Ihre Markenbewusstsein und die Kommunikation mit Kunden. App Service-Web-Apps können dazu beitragen, Ihre Marke auf Basis von Technologie für Geschäftskontinuität von Microsoft zu stärken.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Im Folgenden finden Sie ein Beispiel einer in App Service-Web-Apps ausgeführten Organisationswebsite (.com). Dieses Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach Web-Apps mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]In diesem Leitfaden werden einige der häufigsten Themenbereiche und Aufgaben im Zusammenhang mit der Ausführung einer öffentlich zugänglichen .com-Website in Azure App Service-Web-Apps vorgestellt. Es gibt jedoch andere gängige Lösungen, die Sie in Azure App Service-Web-Apps implementieren können. Diese Lösungen finden Sie in den anderen Leitfäden für [digitale Marketingkampagnen](web-sites-digital-marketing-application-solution-overview.md) und [Geschäftsanwendungen](web-sites-business-application-solution-overview.md).

## Neuerstellung oder Nutzen von vorhandenen Ressourcen

Sie können entweder mithilfe eines gängigen CMS aus dem Katalog neue Websites erstellen, oder Sie verschieben Ihre vorhandenen Webressourcen in App Service-Web-Apps – in verschiedenen Sprachen und mit verschiedenen Frameworks.

Azure Marketplace stellt Vorlagen von der beliebten Website Content Management System (CMS), wie z. B. [Orchard], [Umbraco], [Drupal], und [WordPress]. Sie können eine Web-App mit Ihrem bevorzugten CMS erstellen. Sie können aus verschiedenen Datenbank-Back-Ends für Ihre Anforderungen einschließlich [Azure SQL-Datenbank] und [MySQL].

Ihre vorhandenen Webressourcen können in App Service-Web-Apps ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Sie können die Ressourcen mithilfe Ihrer bevorzugten [FTP]-Tools oder über Ihr System zur Quellcodeverwaltung in Web-Apps verschieben. Web-Apps unterstützen eine direkte Veröffentlichung aus gängigen Quellcodeverwaltungen, wie z. B. [Visual Studio], [Visual Studio Online] und [Git] (lokal, GitHub, BitBucket, DropBox, Mercurial usw.).

## Zuverlässig veröffentlichen

Veröffentlichen Sie Ihre Website zuverlässig, indem Sie Ihre Inhalte direkt aus Ihrem vorhandenen Quellcodeverwaltungssystem veröffentlichen und live testen.

Während der Planung, Erstellung von Prototypen und frühen Entwicklungsphase einer Website, untersuchen Sie das tatsächliche Versionen der Website er vor der durch Aktivierung [Bereitstellen in staging-Steckplatz] Ihrer Site auf Webanwendungen für App-Dienst. Integrieren Web-Apps mit Quellcodeverwaltung, können Sie [Stetiges Veröffentlichen von] auf einem Staging-Steckplatz, und tauschen sie in der Produktionsumgebung ohne Ausfallzeiten, wenn Sie dazu bereit sind. Falls auf der Produktionswebsite etwas schief geht, können Sie die aktuelle Version sofort durch eine frühere Version der Website austauschen.

Außerdem bei der Planung von Änderungen an einer live-Website können Sie problemlos [Ausführen A / B-tests] vorgeschlagener Updates mithilfe des Tests in der Produktion feature in und Analysieren von echten Benutzerverhalten, damit Sie fundierte Entscheidungen auf Entwurf.

## Branding und Sicherheit

Verwenden Sie die kostenlose App Service-Web-App-Domäne, oder nehmen Sie zu eine Zuordnung zu Ihrem registrierten Domänennamen vor, und sorgen Sie anschließend für Sicherheit mit einem von einer Zertifizierungsstelle signierten SSL-Zertifikat.

Die ***. *.azurewebsites.NET** Domäne ist kostenlose, wenn Sie Ihre Website auf Web-Apps ausführen. Oder Sie können Ihre Website Zuordnen einer [benutzerdefinierte Domäne] (z. B. contoso.com), DNS-Registrierung, wie z. B. GoDaddy gewonnen.

Wenn Sie Benutzerinformationen erfassen, Commerce oder andere vertraulichen Daten verwalten, schützen, Ihrem Ruf und Ihre Kunden mit [HTTPS]. Die ***. *.azurewebsites.NET** Domänennamen enthält bereits ein SSL-Zertifikat, und die bei der Verwendung Ihrer benutzerdefinierten Domäne das SSL-Zertifikat für sie Webanwendungen. Für jedes SSL-Zertifikat fällt eine monatliche Gebühr an (abgerechnet nach Stunden). Weitere Informationen finden Sie unter [App Service-Preisdetails].

## Globale Präsenz

Zeigen Sie globale Präsenz durch die Bereitstellung regionaler Websites mit Azure Traffic Manager, und stellen Sie mit Azure CDN blitzschnell Inhalte bereit.

Um Kunden in den jeweiligen Regionen weltweit bereitzustellen, verwenden Sie [Azure Traffic Manager] Route Besuchern der Website auf einen regionalen Standort, die die beste Leistung bietet. Alternativ können Sie die Websitelast gleichmäßig auf mehrere Kopien Ihrer Website verteilen, die in verschiedenen Regionen gehostet werden.

Übermitteln Sie Ihre statischen Inhalt Blitz für Benutzer schnell Global von [Integrieren von Ihrer Anwendung in Azure CDN]. Azure CDN zwischengespeichert, statischen Inhalt in die [CDN-Knoten] die den Benutzer minimiert die Wartezeit und Verbindungen zu Ihrer Website am nächsten.

## Optimierung

Optimieren Sie Ihre Organisationswebsite über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure-Redis-Cache, die Ausführung von Hintergrundaufgaben mithilfe von Webaufträgen, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die Fähigkeit des App-Service-Webanwendungen zu [Skalieren heraus] erfüllt die Anforderungen Ihrer .COM-Website, unabhängig von der Größe Ihrer Arbeitsauslastung. Dezentrales Skalieren Ihrer Website manuell durch die [Azure Preview Portal](http://go.microsoft.com/fwlink/?LinkId=529715), programmgesteuert über die [Dienstverwaltungs-API] oder [PowerShell-Skripting], oder automatisch durch die automatische Skalierung. In **Standard** hosting Plan, automatische Skalierung ermöglicht das Skalieren einer Websites automatisch basierend auf der CPU-Auslastung. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Stellen Sie Ihre Website mit verbesserter Reaktionsfähigkeit der [Azure Redis Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Hohe Verfügbarkeit von der Website mit [Azure Traffic Manager]. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

## Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Website. Empfangen Sie Warnungen zu kritischen Websiteereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight.

Abrufen einer [auf einen Blick] von der Website aktuelle Leistungsmetriken und ressourcenkontingente in der Web-app-Blade in der [Azure Preview Portal](http://go.microsoft.com/fwlink/?LinkId=529715). Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Oder ein Drittanbieter-Tool wie [New Relic] zu bieten erweiterte Überwachungsdaten für Ihre Websites.

In der **Standard** hosting Plan Monitor Site Reaktionsfähigkeit e-Mail-Benachrichtigungen, wenn Ihre Website nicht mehr reagiert. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure].

## Verwenden von Rich-Media zum Erreichen aller Geräte

Erhöhen Sie den Anreiz Ihrer Website mit Rich Media:

-  Hochladen und Streamen von Videos Global mit [Azure Media Services]
-  Senden von e-Mails an Benutzer mit [SendGrid-Dienst in Azure Marketplace]

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

[benutzerdefinierte Domäne]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[App Service-Preisdetails]: /pricing/details/app-service/#ssl-connections

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integrieren von Ihrer Anwendung in Azure CDN]: cdn-websites-with-cdn.md
[CDN-Knoten]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[Skalieren heraus]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[Dienstverwaltungs-API]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell-Skripting]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET-Sitzungszustand]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[Ausgabecache]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[auf einen Blick]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid-Dienst in Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->