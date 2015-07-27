<properties 
	pageTitle="Erstellen einer Branchen-Web-App in Azure App Service" 
	description="Dieser Leitfaden bietet eine technische Übersicht über die Verwendung von Azure App Service-Web-Apps zum Erstellen von Intranetbranchenanwendungen. Dazu gehören Authentifizierungsstrategien, Service Bus-Relay und Überwachung." 
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
	ms.date="07/06/2015" 
	ms.author="cephalin"/>



# Erstellen einer Branchen-Web-App in Azure App Service

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps sind eine hervorragende Wahl für Branchenanwendungen. Hierbei handelt es sich um Intranetanwendungen für interne Geschäftszwecke, die geschützt werden müssen. Sie erfordern üblicherweise eine Authentifizierung – in der Regel mit einem Unternehmensverzeichnis – sowie Zugriff auf oder eine Integration in lokale Daten und Dienste.

Verschiedene wichtige Vorteile sprechen dafür, Branchenanwendungen in Azure App Service-Web-Apps zu verschieben, darunter:

-  Flexible Skalierbarkeit für dynamische Arbeitsauslastungen, z. B. eine Anwendung für jährlich durchgeführte Leistungsbeurteilungen. Während der Beurteilungsphase steigt der Datenverkehr in einem großen Unternehmen rasant an. Azure bietet Skalierungsoptionen, mit deren Hilfe das Unternehmen horizontal skalieren kann, um die Last während des verkehrsreichen Bewertungszeitraums zu verarbeiten. Gleichzeitig kann das Unternehmen Geld sparen, indem es die Kapazitäten dieser Anwendung für den Rest des Jahres wieder reduziert. 
-  Der Schwerpunkt liegt verstärkt auf der Anwendungsentwicklung und weniger auf Infrastrukturerwerb und -verwaltung.
-  Mehr Unterstützung für Mitarbeiter und Partner zur Verwendung der Anwendung von einem beliebigen Ort aus. Benutzer müssen nicht mit dem Unternehmensnetzwerk verbunden sein, um die Anwendung zu nutzen, und die IT-Abteilung muss keine komplexen Reverseproxy-Lösungen einsetzen. Es gibt verschiedene Authentifizierungsoptionen, um den Zugriff auf Unternehmensanwendungen zu schützen.

Im Folgenden finden Sie ein Beispiel einer in App Service-Web-Apps ausgeführten Branchenanwendung. Dieses Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach Web-Apps mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]In diesem Leitfaden werden einige der häufigsten Themenbereichen und Aufgaben im Zusammenhang mit Branchenanwendungen vorgestellt. Azure App Service-Web-Apps bieten jedoch noch weitere Funktionen, die Sie in Ihrer speziellen Implementierung nutzen können. Weitere Informationen zu diesen Funktionen finden Sie in den anderen Leitfäden unter [Globale Webpräsenz](web-sites-global-web-presence-solution-overview.md) und [Digitale Marketingkampagnen](web-sites-digital-marketing-application-solution-overview.md) (beides in englischer Sprache).

## Nutzen vorhandener Ressourcen

Nutzen Sie vorhandene Webressourcen in App Service-Web-Apps in den unterschiedlichsten Sprachen und Frameworks.

Ihre vorhandenen Webressourcen können in App Service-Web-Apps ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Sie können die Ressourcen mithilfe Ihrer bevorzugten [FTP]-Tools oder über Ihr System zur Quellcodeverwaltung in Web-Apps verschieben. Web-Apps unterstützen eine direkte Veröffentlichung aus gängigen Quellcodeverwaltungen, wie z. B. [Visual Studio], [Visual Studio Online] und [Git] – lokal, GitHub, BitBucket, Dropbox, Mercurial usw.

## Schützen Sie Ihre Ressourcen

Schützen Sie Ihre Ressourcen durch Verschlüsselung, Authentifizierung von Unternehmensbenutzern (ob lokal oder remote) und eine Autorisierung der Ressourcenverwendung.

Schützen Sie interne Ressourcen mit [HTTPS] vor Lauschangriffen. Der Domänenname ***.azurewebsites.net** schließt bereits ein SSL-Zertifikat ein, und wenn Sie eine benutzerdefinierte Domäne einsetzen, können Sie Ihr SSL-Zertifikat in App Service-Web-Apps weiterhin verwenden. Für jedes SSL-Zertifikat fällt eine monatliche Gebühr an (abgerechnet nach Stunden). Weitere Informationen finden Sie unter [App Service-Preisdetails].

[Authentifizieren von Benutzern] mit dem Unternehmensverzeichnis. App Service-Web-Apps können Benutzer mithilfe von lokalen Identitätsanbietern authentifizieren, beispielsweise mit den Active Directory-Verbunddiensten (AD FS), oder über einen Azure Active Directory-Mandanten, der mit Ihrer Active Directory-Unternehmensbereitstellung synchronisiert wurde. Benutzer können über eine einmalige Anmeldung auf Ihre Webpräsenzen in Web-Apps zugreifen – unabhängig davon, ob sie lokal arbeiten oder sich von unterwegs anmelden. Vorhandene Dienste, wie z. B. Office 365 oder Microsoft Intune, verwenden bereits Azure Active Directory. Über [Easy Auth] ist es sehr einfach, für Ihre Web-App eine Authentifizierung mit demselben Azure Active Directory-Mandanten zu aktivieren.

[Autorisieren Sie Benutzer] zur Verwendung von Webeigenschaften. Mit minimalem zusätzlichen Code, beispielsweise mit der `[Authorize]`-Dekoration, können Sie dieselben lokalen ASP.NET-Codierungsmuster in App Service-Web-Apps einbringen. Sie behalten dieselbe Flexibilität zur Optimierung der Zugriffssteuerung wie für Anwendungen, die Sie lokal verwalten.

## Verbindungsherstellung mit lokalen Ressourcen ##

Verbinden Sie Ihre Web-App mit Daten oder Ressourcen – unabhängig davon, ob diese aus Leistungsgründen in der Cloud oder aus Compliance-Gründen lokal gespeichert werden. Weitere Informationen zum Speichern von Daten in Azure finden Sie im [Azure Trust Center].

Sie können in Azure zwischen verschiedenen Datenbank-Back-Ends auswählen, um die Anforderungen Ihrer Web-App zu erfüllen, z. B. [Azure SQL-Datenbank] und [MySQL]. Die sichere Speicherung Ihrer Daten in Azure sorgt für geografische Nähe zu Ihrer Web-App und optimiert deren Leistung.

Möglicherweise ist es für Ihr Unternehmen jedoch erforderlich, die zugehörigen Daten lokal zu speichern. Mit App Service-Web-Apps können Sie auf einfache Weise eine [Hybridverbindung] mit Ihren lokalen Ressourcen herstellen, z. B. mit einem Datenbank-Back-End. Wenn Sie Ihre lokalen Verbindungen einheitlich verwalten möchten, können Sie viele Web-Apps in ein [Azure Virtual Network] integrieren, das ein Standort-zu-Standort-VPN bietet. Der Zugriff auf lokale Ressourcen erfolgt anschließend so, als ob Ihre Web-Apps lokal gespeichert wären.

## Optimierung

Optimieren Sie Ihre Branchenanwendung über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure Redis Cache, die Ausführung von Hintergrundaufgaben mithilfe von WebJobs, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die Fähigkeit von App Service-Web-Apps, eine [vertikale und horizontale Skalierung] zu bieten, ist optimal für Ihre Branchenanwendung geeignet, unabhängig von der Arbeitsauslastung. Skalieren Sie Ihre Web-App manuell über das [Azure-Verwaltungsportal], programmgesteuert über die [Dienstverwaltungs-API] oder [PowerShell-Skripts] oder automatisch über die Funktion "Autoskalierung". Im Modus **Standard** können Sie eine Web-App mithilfe von "Autoskalierung" basierend auf der CPU-Auslastung automatisch horizontal hochskalieren. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Erhöhen Sie die Reaktionsfähigkeit Ihrer Web-App mit [Azure Redis Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Stellen Sie mithilfe von [Azure Traffic Manager] eine hohe Verfügbarkeit Ihrer Web-App sicher. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

## Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Web-App. Empfangen Sie Warnungen zu kritischen Web-App-Ereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight.

Im Blatt der Web-App im [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) erhalten Sie eine [Übersicht] über die aktuellen Leistungskennzahlen und Ressourcenkontingente der Web-App. Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Sie können auch ein Drittanbietertool wie [New Relic] nutzen, um weiterführende Überwachungsdaten zu Ihren Web-Apps zu erhalten.

Im Modus **Standard** erhalten die für die Web-App-Überwachung verantwortlichen Benutzer E-Mail-Benachrichtigungen, wenn die Anwendung nicht mehr reagiert. Weitere Informationen finden Sie unter [Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure].

## Weitere Ressourcen

- [App Service-Web-Apps-Dokumentation](/services/app-service/web/)
- [Lernkonzept für Azure App Service-Web-Apps](websites-learning-map.md)
- [Azure-Web-Blog](/blog/topics/web/)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]



[Azure App Service]: /services/app-service/web/

[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[HTTPS]: web-sites-configure-ssl-certificate.md
[App Service-Preisdetails]: /pricing/details/app-service/#ssl-connections
[Authentifizieren von Benutzern]: web-sites-authentication-authorization.md
[Easy Auth]: /blog/2014/11/13/azure-websites-authentication-authorization/
[Autorisieren Sie Benutzer]: web-sites-authentication-authorization.md

[Azure Trust Center]: /support/trust-center/
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL-Datenbank]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[Hybridverbindung]: web-sites-hybrid-connection-get-started.md
[Azure Virtual Network]: web-sites-integrate-with-vnet.md

[vertikale und horizontale Skalierung]: web-sites-scale.md
[Azure-Verwaltungsportal]: http://manage.windowsazure.com/
[Dienstverwaltungs-API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell-Skripts]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET-Sitzungszustand]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[Ausgabecache]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[Übersicht]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[Empfangen von Warnbenachrichtigungen und Verwalten von Warnregeln in Azure]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

 

<!---HONumber=July15_HO3-->