<properties 
	pageTitle="Azure App Service und seine Auswirkungen auf vorhandene Azure-Dienste" 
	description="Erläutert, wie sich der neue Azure App Service und seine Funktionen auf vorhandene Dienste in Azure auswirken." 
	authors="yochayk" 
	writer="yochayk" 
	editor="yochayk" 
	manager="nirma" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="yochayk"/>


# Azure App Service und vorhandene Azure-Dienste

Dieser Artikel beschreibt die Änderungen an vorhandenen Azure-Diensten im Rahmen der Änderung, für die mehrere Azure-Dienste zum [Azure App Service](http://azure.microsoft.com/services/app-service/), einem neuen integrierten Angebot, zusammengefasst werden.

## Übersicht 

[Azure App Service](http://azure.microsoft.com/services/app-service/) ist ein neuer, besonderer Cloud-Dienst, der Entwicklern ermöglicht, Web- und mobile Anwendungen für beliebige Plattformen und Geräte zu entwickeln. App Service ist eine integrierte Lösung für die Optimierung wiederholter Codieraufgaben, die Integration in Unternehmens- und SaaS-Systeme und Automatisierung von Geschäftsprozessen bei gleichzeitiger Erfüllung Ihrer Anforderungen an Sicherheit, Zuverlässigkeit und Skalierbarkeit.

App Service vereint die vorhandenen Azure-Dienste [Websites](http://azure.microsoft.com/services/websites/), [Mobile Services](http://azure.microsoft.com/services/mobile-services/) und [Biztalk Services](http://azure.microsoft.com/services/biztalk-services/) zu einem zentralen kombinierten Dienst, der leistungsfähige neue Funktionen bietet.  Mit App Service können Sie die folgenden App-Typen hosten: 

-   Web-Apps
-   Mobile Apps
-   API-Apps
-   Logik-Apps

In der folgenden Tabelle wird erläutert, wie vorhandene Azure-Dienste App Service und den darin verfügbaren App-Typen zugeordnet sind.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Vorhandener Azure-Dienst</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">Änderung</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure Websites</td>
<td align="left">Web-Apps</td>
<td align="left"><li>Für Azure Websites ist App Service rein auf die Änderung des Namens von  Websites in Web-Apps beschränkt.
<p><li>Alle Ihre vorhandenen Instanzen von Websites sind jetzt Web-Apps in App Service.</p>
<p><li>Sie können auf Ihre vorhandenen Websites über das <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure-Portal</a>zugreifen, wo Sie alle Ihre vorhandenen Websites <em>unter "Web-Apps" finden</em>.</p>
<p><li><em>Webhostingplan</em> ist jetzt <em>App Service-Plan</em>. Ein <em>App Service-Plan</em> kann alle App-Typen von App Service hosten, z. B. Web-, mobile, Logik- oder API-Apps.</p>
<p><li>Azure App Service Web Apps ist jetzt allgemein verfügbar.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Weitere Informationen zu Web-Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Azure Mobile Services</td>
<td align="left">Mobile Apps</td>
<td align="left"><p><li>Mobile Services steht weiterhin als eigenständiger Dienst zur Verfügung und wird weiter vollständig unterstützt.</p>
<p><li>Mobile Apps ist ein neuer App-Typ in App Service, der die gesamte Funktionalität von Mobile Services und vieles mehr integriert. Mobile Apps ist als öffentliche Vorschau verfügbar.</p>
<p><li>Es ist einfach, <a href="http://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview/">von Mobile Services zu Mobile Apps zu migrieren</a>. Da Mobile Apps sich immer noch in der Vorschauphase befindet, sollte dieser Dienst nicht für die Ausführung von Produktionsanwendungen genutzt werden.</p>
<p><li>Als Teil von App Service erhält Mobile Apps über Mobile Services hinausgehende neue Funktionen, wie z. B.  Integration mit lokalen und SaaS-Systemen, Stagingslots, WebJobs, bessere Optionen für die Skalierung und vieles mehr.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Weitere Informationen zu Mobile Apps</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API-Apps</td>
<td align="left">
<p><li>API-Apps ist ein neuer App-Typ in App Service, mit dem APIs in der Cloud einfach erstellt und genutzt werden können.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Weitere Informationen zu API-Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logik-Apps</td>
<td align="left">
<p><li>Logik-Apps ist ein neuer App-Typ in App Service, mit dem Sie Geschäftsprozesse mühelos automatisieren können.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Weitere Informationen zu Logik-Apps</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk Services</td>
<td align="left">BizTalk-API-Apps</td>
<td align="left">
<li><p>BizTalk Services steht weiterhin als eigenständiger Dienst zur Verfügung und wird weiter vollständig unterstützt.</p>
<li><p>Alle Funktionen von BizTalk Services sind in App Service als API-Apps integriert. Dies ermöglicht Benutzern die Integration von Unternehmensanwendungen und B2B-Integrationsszenarien mit beliebigen App-Typen in App Service.</p>
<li><p>Mit Logik-Apps können Sie nun Geschäftsprozesse mithilfe einer visuellen Entwurfsumgebung zum Erstellen von Workflows automatisieren.</p></td>
</tr>
</tbody>
</table>

Weitere Informationen finden Sie in der [App Service-Dokumentation](http://azure.microsoft.com/documentation/services/app-service/).


<!--HONumber=49-->