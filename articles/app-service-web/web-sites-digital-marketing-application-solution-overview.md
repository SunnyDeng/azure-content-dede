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
	ms.date="09/29/2015" 
	ms.author="cephalin"/>

# Erstellen digitaler Marketingkampagnen in Azure App Service-Web-Apps
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps sind eine hervorragende Wahl für digitale Marketingkampagnen. Digitale Marketingkampagnen sind üblicherweise von kurzer Dauer, da sie kurzfristige Marketingziele fördern sollen. Zwei wichtige Szenarien müssen berücksichtigt werden. Beim ersten Szenario erstellt ein fremdes Marketingunternehmen die Kampagne für ihre Kunden und verwaltet diese während der Aktionsdauer. Beim zweiten Szenario erstellt das Marketingunternehmen die Kampagne und überträgt anschließend den Besitz der Ressourcen für die digitale Marketingkampagne auf ihren Kunden. Danach führt der Kunde die digitale Marketingkampagne aus und verwaltet diese selbst. Azure-Websites eignet sich für beide Szenarien.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Nachstehend sehen Sie ein Beispiel für eine globale, über mehrere Kanäle durchgeführte digitale Marketingkampagne unter Verwendung von App Service-Web-Apps. Dieses Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach App Service-Web-Apps mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]In diesem Leitfaden werden einige der häufigsten Themenbereiche und Aufgaben im Zusammenhang mit der Ausführung einer digitalen Marketingkampagne in Azure App Service-Web-Apps vorgestellt. Es gibt jedoch andere gängige Lösungen, die Sie in App Service-Web-Apps implementieren können. Weitere Informationen zu diesen Lösungen finden Sie in den Leitfäden [Globale Webpräsenz](web-sites-global-web-presence-solution-overview.md) und [Digitale Marketingkampagnen](web-sites-business-application-solution-overview.md).

## Neuerstellung oder Nutzen von vorhandenen Ressourcen

Sie können entweder mithilfe eines gängigen CMS aus dem Katalog neue Web-Apps erstellen, oder Sie verschieben Ihre vorhandenen Webressourcen in App Service-Web-Apps – in verschiedenen Sprachen und mit verschiedenen Frameworks.

Der Azure Marketplace bietet Vorlagen für gängige Website-CMS (Content Management System), wie z. B. [Orchard], [Umbraco], Drupal und [WordPress]. Sie können eine Web-App mit Ihrem bevorzugten CMS erstellen. Sie können aus verschiedenen Datenbank-Back-Ends auswählen, um Ihre Anforderungen zu erfüllen, darunter [Azure SQL-Datenbank] und [MySQL].

Ihre vorhandenen Webressourcen können in Web-Apps ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Sie können die Ressourcen mithilfe Ihrer bevorzugten [FTP]-Tools in Web-Apps verschieben. Wenn Sie häufig digitale Marketingkampagnen erstellen, verfügen Sie möglicherweise bereits über Webressourcen in einem Quellcodeverwaltungssystem. Web-Apps unterstützen eine direkte Bereitstellung aus gängigen Quellcodeverwaltungssystemen wie [Visual Studio], [Visual Studio Online] oder [Git] – lokal, GitHub, BitBucket, Dropbox, Mercurial usw.

## Bleiben Sie flexibel

Bleiben Sie flexibel, indem Sie eine kontinuierliche Veröffentlichung aus Ihrem vorhandenen Quellcodeverwaltungssystem und A/B-Tests in App Service-Web-Apps durchführen.

Während der Planung, beim Erstellen eines Prototyps und in der frühen Entwicklungsphase einer Web-App können Sie und Ihr Kunde sich voll funktionsfähige Versionen der Kampagnen-App ansehen, bevor diese durch [Bereitstellung in einem Stagingslot] Ihrer Web-App veröffentlicht wird. Dank der Integration der Quellcodeverwaltung in App Service-Web-Apps können Sie eine [kontinuierliche Veröffentlichung] in einem Stagingslot vornehmen und die Web-App von dort ohne Ausfallzeit in die Produktion übernehmen, sobald sie fertig ist.

Wenn Sie Änderungen an einer bereits veröffentlichten Web-App vornehmen möchten, können Sie mithilfe der Funktion zum Test in der Produktion durch das [Ausführen von A/B-Tests] geplante Aktualisierungen prüfen und das tatsächliche Benutzerverhalten analysieren. So können Sie fundierte Entscheidungen zum Anwendungsdesign treffen.


## Integration in soziale Netzwerke

Ihre digitale Marketingkampagne in App Service-Web-Apps kann durch Authentifizierung bei gängigen Anbietern wie Facebook und Twitter in soziale Netzwerke integriert werden. Ein Beispiel für diesen Ansatz mit einer ASP.NET-Anwendung finden Sie unter [Erstellen einer ASP.NET MVC-App mit Authentifizierung, SQL-Datenbank und Bereitstellung in Azure App Service].

Darüber hinaus werden auf den Websites sozialer Netzwerke üblicherweise Informationen zu weiteren Möglichkeiten der Integration aus .NET und vielen anderen Frameworks bereitgestellt.

## Verwenden von Rich-Media zum Erreichen aller Geräte

Bereichern Sie Ihre digitale Marketingkampagne mit anderen Azure-Diensten, wie z. B.:

-  Globales Hochladen und Streamen von Videos mit [Azure Media Services]
-  Senden von E-Mails an Benutzer mit dem [SendGrid-Dienst in Azure Marketplace]
-  Einrichten von Präsenzen auf Windows-, iOS- und Android-Geräten mit [Mobile Services]
-  Senden von Pushbenachrichtigungen an Millionen von Geräten mit [Notification Hub]

## Globale Präsenz

Zeigen Sie globale Präsenz durch die Bereitstellung regionaler Websites mit Azure Traffic Manager, und stellen Sie mit Azure CDN blitzschnell Inhalte bereit.

Bieten Sie globalen Kunden ein bestmögliches Benutzererlebnis, indem Sie Websitebesucher mithilfe von [Azure Traffic Manager] auf eine regionale Website umleiten und so eine optimale Leistung gewährleisten. Sie können auch die Websitelast gleichmäßig auf mehrere Kopien Ihrer Web-App verteilen, die in verschiedenen Regionen gehostet werden.

Stellen Sie durch das [Integrieren Ihrer Web-App in Azure CDN] statische Inhalte blitzschnell weltweit für Ihre Benutzer bereit. Azure CDN speichert statische Inhalte in dem [CDN-Knoten] zwischen, der dem Benutzer am nächsten ist. Auf diese Weise werden Latenz und Verbindungen mit Ihrer Web-App minimiert.

## Optimierung

Optimieren Sie Ihre Web-App über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure Redis Cache und die Ausführung von Hintergrundaufgaben mithilfe von WebJobs, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die Fähigkeit von App Service-Web-Apps, eine [flexible Skalierung] zu ermöglichen, ist perfekt für unvorhersehbare Workloads wie bei digitalen Marketingkampagnen geeignet. Skalieren Sie Ihre Web-App manuell über das [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715), programmgesteuert über die [Dienstverwaltungs-API] oder über [PowerShell-Skripts] oder automatisch über die Funktion "Autoskalierung". Im Modus **Standard** können Sie eine Web-App mithilfe von "Autoskalierung" basierend auf der CPU-Auslastung automatisch horizontal hochskalieren. Diese Funktion ermöglicht maximale Flexibilität bei minimalen Kosten, da die Web-App nur dann skaliert wird, wenn dies basierend auf der Benutzeraktivität erforderlich ist. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]\: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Erhöhen Sie die Reaktionsfähigkeit Ihrer Web-App mit [Azure Redis Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Stellen Sie mithilfe von [Azure Traffic Manager] eine hohe Verfügbarkeit Ihrer Web-App sicher. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

## Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Web-App. Empfangen Sie Warnungen zu kritischen Web-App-Ereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight.

Im Blatt der Web-App im [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) erhalten Sie eine [Übersicht] über die aktuellen Leistungskennzahlen und Ressourcenkontingente der Web-App. Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Sie können auch ein Drittanbietertool wie [New Relic] nutzen, um weiterführende Überwachungsdaten zu Ihren Web-Apps zu erhalten.

Im Modus **Standard** erhalten die für die Web-App-Überwachung verantwortlichen Benutzer E-Mail-Benachrichtigungen, wenn die Web-App nicht mehr reagiert. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure].

## Weitere Ressourcen

- [App Service-Web-Apps-Dokumentation](/services/app-service/web/)
- [Lernkonzept für Azure App Service-Web-Apps](websites-learning-map.md)
- [Azure-Web-Blog](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL-Datenbank]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[Bereitstellung in einem Stagingslot]: web-sites-staged-publishing.md
[kontinuierliche Veröffentlichung]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[Ausführen von A/B-Tests]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Erstellen einer ASP.NET MVC-App mit Authentifizierung, SQL-Datenbank und Bereitstellung in Azure App Service]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid-Dienst in Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md
[Mobile Services]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Notification Hub]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integrieren Ihrer Web-App in Azure CDN]: cdn-websites-with-cdn.md
[CDN-Knoten]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[flexible Skalierung]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[Dienstverwaltungs-API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell-Skripts]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
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
 

<!---HONumber=Oct15_HO3-->