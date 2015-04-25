<properties 
	pageTitle="Erstellen einer digitalen Marketingkampagne mithilfe von Azure-Websites" 
	description="Dieser Leitfaden bietet einen technischen Überblick über das Erstellen von digitalen Marketingkampagnen mithilfe von Azure-Websites. Dazu gehören die Bereitstellung, die Integration von sozialen Medien, Skalierungsstrategien und die Überwachung." 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>

# Digitale Marketingkampagnen auf Azure-Websites
[Azure Websites] ist eine hervorragende Wahl für digitale Marketingkampagnen. Digitale Marketingkampagnen sind üblicherweise von kurzer Dauer, da sie kurzfristige Marketingziele fördern sollen. Zwei wichtige Szenarien müssen berücksichtigt werden. Beim ersten Szenario erstellt ein fremdes Marketingunternehmen die Kampagne für ihre Kunden und verwaltet diese während der Aktionsdauer. Beim zweiten Szenario erstellt das Marketingunternehmen die Kampagne und überträgt anschließend den Besitz der Ressourcen für die digitale Marketingkampagne auf ihren Kunden. Danach führt der Kunde die digitale Marketingkampagne aus und verwaltet diese selbst. Azure-Websites eignet sich für beide Szenarien. 

> [AZURE.NOTE] Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Nachstehend sehen Sie ein Beispiel für eine globale, über mehrere Kanäle durchgeführte digitale Marketingkampagne unter Verwendung von Azure-Websites. Diese Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach Azure-Websites mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> In diesem Leitfaden werden einige der häufigsten Themenbereiche und Aufgaben im Zusammenhang mit der Ausführung von digitalen Marketingkampagnen in Azure-Websites vorgestellt. Es gibt jedoch andere gängige Lösungen, die Sie in Azure-Websites implementieren können. Weitere Informationen zu diesen Lösungen finden Sie in den Leitfäden <a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">Globale Webpräsenz</a> und <a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">Digitale Marketingkampagnen</a>.

### Neuerstellung oder Nutzen von vorhandenen Ressourcen

Sie können entweder mithilfe eines gängigen CMS aus dem Katalog eine neue Website erstellen, oder Sie verschieben Ihre vorhandenen Webressourcen nach Azure-Websites - in verschiedenen Sprachen und mit verschiedenen Frameworks.

Der Azure-Katalog bietet Vorlagen für gängige Website-CMS (Content Management Systems), wie z. B. [Orchard], [Umbraco], [Drupal] und [WordPress]. Sie können eine Website mit Ihrem bevorzugten CMS erstellen. Sie können aus verschiedenen Datenbank-Back-Ends auswählen, um Ihre Anforderungen zu erfüllen, darunter [Azure SQL-Datenbank] und [MySQL].

Ihre vorhandenen Webressourcen können auf Azure-Websites ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Sie können die Ressourcen mithilfe Ihrer bevorzugten [FTP]-Tools nach Azure-Websites verschieben. Wenn Sie häufig digitale Marketingkampagnen erstellen, verfügen Sie möglicherweise bereits über Webressourcen in einem Quellcodeverwaltungssystem. Azure-Websites unterstützt eine direkte Bereitstellung aus gängigen Quellcodeverwaltungsystemen, wie z. B. [Visual Studio], [Visual Studio Online] und [Git] (lokal, GitHub, BitBucket, DropBox, Mercurial usw.).

### Bleiben Sie flexibel

Bleiben Sie flexibel, indem Sie eine kontinuierliche Veröffentlichung aus Ihrem vorhandenen Quellcodeverwaltungssystem und A/B-Tests in Azure-Websites durchführen. 

Während der Planung, beim Erstellen eines Prototyps und in der frühen Entwicklungsphase einer Website können Sie und Ihr Kunde voll funktionsfähige Versionen der Kampagnen-Website anschauen, bevor diese durch [Bereitstellung in einem Stagingslot] Ihrer Azure-Website veröffentlicht wird. Dank der Integration der Quellcodeverwaltung in Azure-Websites können Sie eine [kontinuierliche Veröffentlichung] in einem Stagingslot vornehmen und die Website von dort ohne Ausfallzeit in die Produktion übernehmen, sobald sie fertig ist. 

Wenn Sie Änderungen an einer bereits veröffentlichten Website vornehmen möchten, können Sie mithilfe der Funktion zum Test in der Produktion durch das [Ausführen von A/B-Tests] geplante Aktualisierungen prüfen und das tatsächliche Benutzerverhalten analysieren. So können Sie fundierte Entscheidungen zum Websitedesign treffen.


### Integration in soziale Netzwerke

Ihre digitale Marketingkampagne in Azure-Websites kann durch Authentifizierung bei gängigen Anbietern wie z. B. Facebook und Twitter in soziale Netzwerke integriert werden. Ein Beispiel für diesen Ansatz mit einer ASP.NET-Anwendung finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank auf einer Azure-Website]. 

Darüber hinaus werden auf den Websites sozialer Netzwerke üblicherweise Informationen zu weiteren Möglichkeiten der Integration aus .NET und vielen anderen Frameworks bereitgestellt.

### Verwenden von Rich-Media zum Erreichen aller Geräte

Bereichern Sie Ihre digitale Marketingkampagne mit anderen Azure-Diensten, wie z. B.:

-  Globales Hochladen und Streamen von Videos mit [Azure Media Services]
-  Senden von E-Mails an Benutzer mit dem [SendGrid-Dienst in Azure Marketplace]
-  Einrichten von Präsenzen auf Windows-, iOs- und Android-Geräten mit [Mobile Services]
-  Senden von Pushbenachrichtigungen an Millionen von Geräten mit [Notification Hub]

### Globale Präsenz

Zeigen Sie globale Präsenz durch die Bereitstellung regionaler Websites mit Azure Traffic Manager, und stellen Sie mit Azure CDN blitzschnell Inhalte bereit.

Bieten Sie globalen Kunden ein bestmögliches Benutzererlebnis, indem Sie Websitebesucher mithilfe von [Azure Traffic Manager] auf eine regionale Website umleiten und so eine optimale Leistung gewährleisten. Alternativ können Sie die Websitelast gleichmäßig auf mehrere Kopien Ihrer Website verteilen, die in verschiedenen Regionen gehostet werden.

Stellen Sie durch eine [Integration Ihrer Website in Azure CDN] statische Inhalte blitzschnell für Ihre globalen Benutzer bereit.  Azure CDN speichert statische Inhalte in dem [CDN-Knoten] zwischen, der dem Benutzer am nächsten ist. Auf diese Weise werden Latenz und Verbindungen mit Ihrer Website minimiert.

### Optimierung

Optimieren Sie Ihre Website über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure-Redis-Cache, die Ausführung von Hintergrundaufgaben mithilfe von Webaufträgen, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die Fähigkeit von Azure-Websites, eine flexible [Skalierung nach oben und unten] zu ermöglichen, ist perfekt für unvorhersehbare Arbeitsauslastungen wie bei digitalen Marketingkampagnen geeignet. Skalieren Sie Ihre Website manuell über das [Azure-Verwaltungsportal], programmgesteuert über die [Dienstverwaltungs-API] oder [PowerShell-Skripts], oder automatisch über die Funktion "Autoskalierung". Im Hostingplan **Standard** können Sie eine Website mithilfe von "Autoskalierung" basierend auf der CPU-Auslastung automatisch skalieren. Diese Funktion ermöglicht maximale Flexibilität bei minimalen Kosten, da die Website nur dann skaliert wird, wenn dies basierend auf der Benutzeraktivität erforderlich ist. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Erhöhen Sie die Reaktionsfähigkeit Ihrer Website mit [Azure-Redis-Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Stellen Sie mithilfe von [Azure Traffic Manager] eine hohe Verfügbarkeit Ihrer Website sicher. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

### Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Website. Empfangen Sie Warnungen zu kritischen Websiteereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight. 

Im Dashboard von Azure-Websites erhalten Sie [auf einen Blick] eine Übersicht über die aktuellen Leistungsmetriken und Ressourcenkontingente der Website. Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Alternativ können Sie ein Drittanbietertool wie [New Relic] nutzen, um weiterführende Überwachungsdaten für Ihre Websites zu erhalten.

Im Hostingplan **Standard** erhalten für die Websiteüberwachung verantwortliche Benutzer E-Mail-Benachrichtigungen, wenn die Website nicht mehr reagiert. Weitere Informationen finden Sie unter [Vorgehensweise: Empfangen von Warnungs-Benachrichtigungen und Verwaltung von Alarmregeln in Azure].

## Weitere Ressourcen

- [Dokumentation der Azure-Websites](/de-de/documentation/services/websites/)
- [Lernkonzept für Azure-Websites](websites-learning-map.md)
- [Azure-Web-Blog](/blog/topics/web/)


[Azure Websites]:/de-de/services/websites/

[Orchard]:/de-de/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/de-de/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/de-de/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/de-de/documentation/articles/web-sites-php-web-site-gallery/
  
[MySQL]:/de-de/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL-Datenbank]:/de-de/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/de-de/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/de-de/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/de-de/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/de-de/documentation/articles/web-sites-publish-source-control/

[Bereitstellung in einem Stagingslot]:/de-de/documentation/articles/web-sites-staged-publishing/ 
[Kontinuierliche Veröffentlichung]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[Ausführen von A/B-Tests]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank auf einer Azure-Website]:/de-de/develop/net/tutorials/web-site-with-sql-database/

[Azure Media Services]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid-Dienst in Azure Marketplace]:/de-de/documentation/articles/sendgrid-dotnet-how-to-send-email/
[Mobile Services]:/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[Notification Hub]:/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Integration Ihrer Website in Azure CDN]:/de-de/documentation/articles/cdn-websites-with-cdn/ 
[CDN-Knoten]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[Skalierung nach oben und unten]:/de-de/manage/services/web-sites/how-to-scale-websites/
[Azure-Verwaltungsportal]:http://manage.windowsazure.com/
[Dienstverwaltungs-API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell-Skripts]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure-Redis-Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET-Sitzungszustand]:https://msdn.microsoft.com/de-de/library/azure/dn690522.aspx
[Ausgabecache]:https://msdn.microsoft.com/de-de/library/azure/dn798898.aspx

[Auf einen Blick]:/de-de/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/de-de/develop/net/how-to-guides/new-relic/
[Vorgehensweise: Empfangen von Warnungs-Benachrichtigungen und Verwaltung von Alarmregeln in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  





<!--HONumber=42-->
