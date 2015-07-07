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

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps bieten alle Funktionen, die Sie zum Einrichten einer globalen Webpräsenz für Ihre Organisationswebsite benötigen. Unabhängig von der Größe Ihrer Organisation benötigen Sie eine stabile, sichere und skalierbare Plattform, um Ihr Unternehmen weiterzuentwickeln, die Bekanntheit Ihrer Marke zu steigern und die Kommunikation mit Ihren Kunden zu verbessern. App Service-Web-Apps können dazu beitragen, Ihre Marke auf Basis von Technologie für Geschäftskontinuität von Microsoft zu stärken.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Im Folgenden finden Sie ein Beispiel einer in App Service-Web-Apps ausgeführten Organisationswebsite (.com). Dieses Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach Web-Apps mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]In diesem Leitfaden werden einige der häufigsten Themenbereiche und Aufgaben im Zusammenhang mit der Ausführung einer öffentlich zugänglichen .com-Website in Azure App Service-Web-Apps vorgestellt. Es gibt jedoch andere gängige Lösungen, die Sie in Azure App Service-Web-Apps implementieren können. Diese Lösungen finden Sie in den anderen Leitfäden für [digitale Marketingkampagnen](web-sites-digital-marketing-application-solution-overview.md) und [Geschäftsanwendungen](web-sites-business-application-solution-overview.md).

## Neuerstellung oder Nutzen von vorhandenen Ressourcen

Sie können entweder mithilfe eines gängigen CMS aus dem Katalog neue Websites erstellen, oder Sie verschieben Ihre vorhandenen Webressourcen in App Service-Web-Apps – in verschiedenen Sprachen und mit verschiedenen Frameworks.

Der Azure Marketplace bietet Vorlagen für gängige Website-CMS (Content Management System), wie z. B. [Orchard], [Umbraco], [Drupal] und [WordPress]. Sie können eine Web-App mit Ihrem bevorzugten CMS erstellen. Sie können aus verschiedenen Datenbank-Back-Ends auswählen, um Ihre Anforderungen zu erfüllen, darunter [Azure SQL-Datenbank] und [MySQL].

Ihre vorhandenen Webressourcen können in App Service-Web-Apps ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Sie können die Ressourcen mithilfe Ihrer bevorzugten [FTP]-Tools oder über Ihr System zur Quellcodeverwaltung in Web-Apps verschieben. Web-Apps unterstützen eine direkte Veröffentlichung aus gängigen Quellcodeverwaltungen, wie z. B. [Visual Studio], [Visual Studio Online] und [Git] (lokal, GitHub, BitBucket, DropBox, Mercurial usw.).

## Zuverlässig veröffentlichen

Veröffentlichen Sie Ihre Website zuverlässig, indem Sie Ihre Inhalte direkt aus Ihrem vorhandenen Quellcodeverwaltungssystem veröffentlichen und live testen.

Während der Planung, beim Erstellen eines Prototyps und in der frühen Entwicklungsphase einer Website können Sie sich voll funktionsfähige Versionen der Website ansehen, bevor diese durch [Bereitstellung in einem Stagingslot] Ihrer Website in App Service-Web-Apps veröffentlicht wird. Dank der Integration einer Quellcodeverwaltung in Web-Apps können Sie eine [kontinuierliche Veröffentlichung] in einem Stagingslot vornehmen und von dort ohne Ausfallzeit in die Produktion übernehmen, wenn die Website fertig ist. Falls auf der Produktionswebsite etwas schief geht, können Sie die aktuelle Version sofort durch eine frühere Version der Website austauschen.

Wenn Sie Änderungen an einer bereits veröffentlichten Website vornehmen möchten, können Sie mithilfe der Funktion zum Test in der Produktion durch das [Ausführen von A/B-Tests] geplante Aktualisierungen prüfen und das tatsächliche Benutzerverhalten analysieren. So können Sie fundierte Entscheidungen zum Websitedesign treffen.

## Branding und Sicherheit

Verwenden Sie die kostenlose App Service-Web-App-Domäne, oder nehmen Sie zu eine Zuordnung zu Ihrem registrierten Domänennamen vor, und sorgen Sie anschließend für Sicherheit mit einem von einer Zertifizierungsstelle signierten SSL-Zertifikat.

Die Domäne ***.azurewebsites.net** ist kostenlos, wenn Sie Ihre Website in Web-Apps ausführen. Alternativ können Sie Ihre Website einer [benutzerdefinierten Domäne] (z. B. „contoso.com“) zuordnen, die Sie von einer DNS-Registrierungsstelle (z. B. GoDaddy) bezogen haben.

Wenn Sie Benutzerinformationen erfassen, E-Commerce betreiben oder andere sensible Daten verwalten, können Sie den Ruf Ihres Unternehmens und Ihre Kunden mit [HTTPS] schützen. Der Domänenname ***.azurewebsites.net** umfasst bereits ein SSL-Zertifikat, und wenn Sie eine benutzerdefinierte Domäne nutzen, können Sie Ihr SSL-Zertifikat in Web-Apps weiterhin verwenden. Für jedes SSL-Zertifikat fällt eine monatliche Gebühr an (abgerechnet nach Stunden). Weitere Informationen finden Sie unter [App Service-Preisdetails].

## Globale Präsenz

Zeigen Sie globale Präsenz durch die Bereitstellung regionaler Websites mit Azure Traffic Manager, und stellen Sie mit Azure CDN blitzschnell Inhalte bereit.

Bieten Sie globalen Kunden ein bestmögliches Benutzererlebnis, indem Sie Websitebesucher mithilfe von [Azure Traffic Manager] auf eine regionale Website umleiten und so eine optimale Leistung gewährleisten. Alternativ können Sie die Websitelast gleichmäßig auf mehrere Kopien Ihrer Website verteilen, die in verschiedenen Regionen gehostet werden.

Stellen Sie durch das [Integrieren Ihrer Web-App in Azure CDN] statische Inhalte blitzschnell weltweit für Ihre Benutzer bereit. Azure CDN speichert statische Inhalte in dem [CDN-Knoten] zwischen, der dem Benutzer am nächsten ist. Auf diese Weise werden Latenz und Verbindungen mit Ihrer Website minimiert.

## Optimierung

Optimieren Sie Ihre Organisationswebsite über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure-Redis-Cache, die Ausführung von Hintergrundaufgaben mithilfe von Webaufträgen, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die [flexible Skalierbarkeit] von App Service-Web-Apps ist ideal für Ihre Organisationswebsite – unabhängig von Ihrem Workload. Skalieren Sie Ihre Website manuell über das [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715), programmgesteuert über die [Dienstverwaltungs-API] oder über [PowerShell-Skripts] oder automatisch über die Funktion „Autoskalierung“. Im Hostingplan **Standard** kann eine Website mithilfe der Autoskalierung abhängig von der CPU-Auslastung automatisch skaliert werden. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Erhöhen Sie die Reaktionsfähigkeit Ihrer Website mit [Azure Redis Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Stellen Sie mithilfe von [Azure Traffic Manager] eine hohe Verfügbarkeit Ihrer Website sicher. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

## Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Website. Empfangen Sie Warnungen zu kritischen Websiteereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight.

Im [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) finden Sie auf dem Blatt der Web-App eine [Übersicht] über die aktuellen Leistungskennzahlen und Ressourcenkontingente der Website. Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Alternativ können Sie ein Drittanbietertool wie [New Relic] nutzen, um weiterführende Überwachungsdaten für Ihre Websites zu erhalten.

Im Hostingplan **Standard** erhalten für die Websiteüberwachung verantwortliche Benutzer E-Mail-Benachrichtigungen, wenn die Website nicht mehr reagiert. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure].

## Verwenden von Rich-Media zum Erreichen aller Geräte

Erhöhen Sie den Anreiz Ihrer Website mit Rich Media:

-  Globales Hochladen und Streamen von Videos mit [Azure Media Services]
-  Senden von E-Mails an Benutzer mit dem [SendGrid-Dienst in Azure Marketplace]

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
[Azure SQL-Datenbank]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[Bereitstellung in einem Stagingslot]: web-sites-staged-publishing.md
[kontinuierliche Veröffentlichung]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[Ausführen von A/B-Tests]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[benutzerdefinierten Domäne]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[App Service-Preisdetails]: /pricing/details/app-service/#ssl-connections

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integrieren Ihrer Web-App in Azure CDN]: cdn-websites-with-cdn.md
[CDN-Knoten]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[flexible Skalierbarkeit]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[Dienstverwaltungs-API]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell-Skripts]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET-Sitzungszustand]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[Ausgabecache]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[Übersicht]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid-Dienst in Azure Marketplace]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=62-->