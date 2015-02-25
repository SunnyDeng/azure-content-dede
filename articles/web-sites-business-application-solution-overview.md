<properties 
	pageTitle="Erstellen einer Branchenanwendung auf Azure-Websites" 
	description="Dieser Leitfaden bietet eine technische Übersicht über die Verwendung von Azure-Websites zur Erstellung von Intranetbranchenanwendungen. Dies schließt Authentifizierungsstrategien, Service Bus Relay und Überwachung ein." 
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



# Erstellen einer Branchenanwendung auf Azure-Websites

[Azure-Websites] ist eine hervorragende Wahl für Branchenanwendungen. Hierbei handelt es sich um Intranetanwendungen für interne Geschäftszwecke, die geschützt werden müssen. Sie erfordern üblicherweise eine Authentifizierung - in der Regel mit einem Unternehmensverzeichnis - sowie Zugriff auf oder eine Integration in lokale Daten und Dienste. 

Verschiedene wichtige Vorteile sprechen dafür, Branchenanwendungen in Azure-Websites zu verschieben, darunter:

-  Flexible Skalierbarkeit für dynamische Arbeitsauslastungen, z. B. eine Anwendung für jährlich durchgeführte Leistungsbeurteilungen. Während der Beurteilungsphase steigt der Datenverkehr in einem großen Unternehmen rasant an. Azure bietet Skalierungsoptionen, mit deren Hilfe das Unternehmen horizontal skalieren kann, um die Last während des verkehrsreichen Bewertungszeitraums zu verarbeiten. Gleichzeitig kann das Unternehmen Geld sparen, indem es die Kapazitäten dieser Anwendung für den Rest des Jahres wieder reduziert. 
-  Der Schwerpunkt liegt verstärkt auf der Anwendungsentwicklung und weniger auf Infrastrukturerwerb und -verwaltung.
-  Mehr Unterstützung für Mitarbeiter und Partner zur Verwendung der Anwendung von einem beliebigen Ort aus. Benutzer müssen nicht mit dem Unternehmensnetzwerk verbunden sein, um die Anwendung zu nutzen, und die IT-Abteilung muss keine komplexen Reverseproxy-Lösungen einsetzen. Es gibt verschiedene Authentifizierungsoptionen, um den Zugriff auf Unternehmensanwendungen zu schützen.

Es folgt ein Beispiel einer auf Azure-Websites ausgeführten Branchenanwendung. Dieses Beispiel zeigt, was Sie erreichen können, wenn Sie bei minimalem technischen Aufwand einfach Azure-Websites mit anderen Diensten kombinieren. **Klicken Sie auf ein Element in der Topografie, um mehr zu erfahren.** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>Hinweis</strong>
<p>In diesem Leitfaden werden einige der häufigsten Themenbereichen und Aufgaben im Zusammenhang mit Branchenanwendungen vorgestellt. Allerdings gibt es weitere Funktionen von Azure-Websites, die Sie in Ihrer speziellen Implementierung verwenden können. Weitere Informationen zu diesen Funktionen finden Sie in den Leitfäden <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/global-web-presence-solution-overview/">Globale Webpräsenz</a> und <a href="http://www.windowsazure.com/de-de/manage/services/web-sites/digital-marketing-campaign-solution-overview">Digitale Marketingkampagnen</a>.</p>
</div>

### Nutzen vorhandener Ressourcen

Nutzen Sie vorhandene Webressourcen in  Azure-Websites - aus verschiedensten Sprachen und Frameworks.

Ihre vorhandenen Webressourcen können auf Azure-Websites ausgeführt werden, unabhängig davon, ob es sich um .NET, PHP, Java, Node.js oder Python handelt. Sie können die Ressourcen mithilfe Ihrer bevorzugten [FTP]-Tools oder über Ihr System zur Quellcodeverwaltung nach Azure-Websites verschieben. Azure-Websites unterstützt eine direkte Veröffentlichung aus gängigen Quellcodeverwaltungen, wie z. B. [Visual Studio], [Visual Studio Online] und [Git] (lokal, GitHub, BitBucket, DropBox, Mercurial usw.).

### Schützen Sie Ihre Ressourcen

Schützen Sie Ihre Ressourcen durch Verschlüsselung, Authentifizierung von Unternehmensbenutzern (ob lokal oder remote) und eine Autorisierung der Ressourcenverwendung. 

Schützen Sie interne Ressourcen mit [HTTPS] vor Lauschangriffen. Der Domänenname **\*.azurewebsites.net** umfasst bereits ein SSL-Zertifikat, und wenn Sie eine benutzerdefinierte Domäne einsetzen, können Sie Ihr SSL-Zertifikat in Azure-Websites weiterhin verwenden. Für jedes SSL-Zertifikat fällt eine monatliche Gebühr an (abgerechnet nach Stunden). Weitere Informationen finden Sie unter [Preisübersicht für Websites].

[Authentifizieren von Benutzern] mit dem Unternehmensverzeichnis. Azure-Websites kann Benutzer mithilfe von lokalen Identitätsanbietern authentifizieren, beispielsweise mit den Active Directory-Verbunddiensten (AD FS), oder über einen Azure Active Directory-Mandanten, der mit Ihrer Active Directory-Unternehmensbereitstellung synchronisiert wurde. Benutzer können über eine einmalige Anmeldung auf Ihre Webpräsenzen in Azure-Websites zugreifen - unabhängig davon, ob sie lokal arbeiten oder sich von unterwegs anmelden. Vorhandene Dienste, wie z. B. Office 365 oder Windows Intune, verwenden bereits Azure Active Directory. Über [Easy Auth] ist es sehr einfach, für Ihre Website eine Authentifizierung mit demselben Azure Active Directory-Mandanten zu aktivieren. 

[Autorisieren Sie Benutzer] zur Verwendung von Webpräsenzen. Mit minimalem zusätzlichen Code, beispielsweise mit dem `[Authorize]` -Decorator-Element, können Sie dieselben lokalen ASP.NET-Codierungsmuster in Azure-Websites einbringen. Sie behalten dieselbe Flexibilität zur Optimierung der Zugriffssteuerung wie für Anwendungen, die Sie lokal verwalten.

### Verbindungsherstellung mit lokalen Ressourcen ###

Verbinden Sie Ihre Website mit Daten oder Ressourcen - unabhängig davon, ob diese aus Leistungsgründen in der Cloud oder aus Compliance-Gründen lokal gespeichert werden. Weitere Informationen zum Speichern von Daten in Azure finden Sie im [Azure Trust Center]. 

Sie können in Azure aus verschiedenen Datenbank-Back-Ends auswählen, um die Anforderungen Ihrer Website zu erfüllen, darunter [Azure SQL-Datenbank] und [MySQL]. Die sichere Speicherung Ihrer Daten in Azure sorgt für geografische Nähe zu Ihrer Website und optimiert deren Leistung.

Möglicherweise ist es für Ihr Unternehmen jedoch erforderlich, die zugehörigen Daten lokal zu speichern. Mit Azure-Websites können Sie auf einfache Weise eine [Hybridverbindung] mit Ihren lokalen Ressourcen herstellen, z. B. mit einem Datenbank-Back-End. Wenn Sie Ihre lokalen Verbindungen einheitlich verwalten möchten, können Sie viele Azure-Websites in ein [Azure Virtual Network] integrieren, das ein Website-zu-Website-VPN bietet. Der Zugriff auf lokale Ressourcen erfolgt anschließend so, als ob Ihre Azure-Websites lokal gespeichert wären. [Enterprise Pizza - Verbindungsherstellung zwischen Websites und lokalen Ressourcen über Service Bus][enterprisepizza]

### Optimierung

Optimieren Sie Ihre Branchenanwendung über eine automatische Skalierung mit "Autoskalierung", eine Zwischenspeicherung mit Azure-Redis-Cache, die Ausführung von Hintergrundaufgaben mithilfe von Webaufträgen, und sorgen Sie mit Azure Traffic Manager für eine gleichbleibend hohe Verfügbarkeit.

Die Fähigkeit von Azure-Websites, flexibel [nach oben und unten zu skalieren], ist optimal für Ihre Branchenanwendung geeignet, unabhängig von der Arbeitsauslastung. Skalieren Sie Ihre Website manuell über das [Azure-Verwaltungsportal], programmgesteuert über die [Dienstverwaltungs-API] oder [PowerShell-Skripts] oder automatisch über die Funktion "Autoskalierung". Im Hostingplan **Standard** können Sie eine Website mithilfe von "Autoskalierung" basierend auf der CPU-Auslastung automatisch skalieren. Bewährte Methoden finden Sie auf der Website von [Troy Hunt]: [10 things I learned about rapidly scaling websites with Azure] (10 Dinge, die ich über eine schnelle Skalierung von Websites mit Azure gelernt habe, in englischer Sprache).

Erhöhen Sie die Reaktionsfähigkeit Ihrer Website mit [Azure Redis Cache]. Nutzen Sie diese Funktion zum Zwischenspeichern von Daten aus Back-End-Datenbanken und anderen Daten, beispielsweise [ASP.NET-Sitzungszustand] und [Ausgabecache].

Stellen Sie mithilfe von [Azure Traffic Manager] eine hohe Verfügbarkeit Ihrer Website sicher. Bei Verwendung der **Failover**-Methode leitet Traffic Manager Datenverkehr automatisch an eine sekundäre Website um, wenn ein Problem mit der primären Website vorliegt.

### Überwachung und Analyse

Sorgen Sie mit Azure oder Tools von Drittanbietern für eine optimale Leistung Ihrer Website. Empfangen Sie Warnungen zu kritischen Websiteereignissen. Erhalten Sie problemlos Benutzereinblick über Application Insights oder eine Webprotokollanalyse aus HDInsight. 

Im Dashboard von Azure-Websites erhalten Sie [auf einen Blick] eine Übersicht über die aktuellen Leistungsmetriken und Ressourcenkontingente der Website. Eine 360-Grad-Ansicht Ihrer Anwendung, einschließlich von Daten zu Verfügbarkeit, Leistung und Auslastung, erhalten Sie mit [Azure Application Insights]. So erhalten Sie schnell aussagekräftige Erkenntnisse zu Problembehandlung, Diagnose und Nutzung. Alternativ können Sie ein Drittanbietertool wie [New Relic] nutzen, um weiterführende Überwachungsdaten für Ihre Websites zu erhalten.

Im Hostingplan **Standard** erhalten für die Websiteüberwachung verantwortliche Benutzer E-Mail-Benachrichtigungen, wenn die Website nicht mehr reagiert. Weitere Informationen finden Sie unter [Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure].

## Weitere Ressourcen

- [Dokumentation der Azure-Websites](/de-de/documentation/services/websites/)
- [Lernkonzept für Azure-Websites](/de-de/documentation/articles/websites-learning-map/)
- [Azure-Web-Blog](/blog/topics/web/)



[Azure-Websites]:/de-de/services/websites/

[FTP]:/de-de/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/de-de/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/de-de/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/de-de/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/de-de/documentation/articles/web-sites-configure-ssl-certificate/
[Preisübersicht für Websites]:/de-de/pricing/details/web-sites/#service-ssl
[Authentifizieren von Benutzern]:/de-de/documentation/articles/web-sites-authentication-authorization/
[Easy Auth]:/blog/2014/11/13/azure-websites-authentication-authorization/
[Autorisieren Sie Benutzer]:/de-de/documentation/articles/web-sites-authentication-authorization/

[Azure Trust Center]:/de-de/support/trust-center/
[MySQL]:/de-de/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL-Datenbank]:/de-de/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[Hybridverbindung]:/de-de/documentation/articles/web-sites-hybrid-connection-get-started/
[Azure Virtual Network]:/de-de/documentation/articles/web-sites-integrate-with-vnet/

[Skalierung nach oben und unten]:/de-de/manage/services/web-sites/how-to-scale-websites/
[Azure-Verwaltungsportal]:http://manage.windowsazure.com/
[Dienstverwaltungs-API]:http://msdn.microsoft.com/de-de/library/windowsazure/ee460799.aspx
[PowerShell-Skripts]:http://msdn.microsoft.com/de-de/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET-Sitzungszustand]:https://msdn.microsoft.com/de-de/library/azure/dn690522.aspx
[Ausgabecache]:https://msdn.microsoft.com/de-de/library/azure/dn798898.aspx

[Auf einen Blick]:/de-de/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/de-de/develop/net/how-to-guides/new-relic/
[Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx





<!--HONumber=42-->
