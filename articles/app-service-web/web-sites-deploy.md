<properties
	pageTitle="Bereitstellen der App in Azure App Service"
	description="Erfahren Sie, wie Sie Ihre App für Azure App Service bereitstellen."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Bereitstellen der App in Azure App Service

Dieser Artikel hilft Ihnen beim Ermitteln der besten Option für die Bereitstellung der Dateien für Ihre Web-App, das Back-End der mobilen App oder der API-App für [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Er enthält auch Links zu den entsprechenden Artikeln und Blogs mit "Gewusst wie"-Anleitungen für die jeweils bevorzugte Option.

## <a name="overview"></a>Übersicht über die Bereitstellungsprozesse

Nachdem Sie in Azure App Service eine App erstellt oder bereitgestellt haben, beginnt Azure App Service für Sie mit der Verwaltung des Anwendungsframeworks (ASP.NET, PHP, Node.js usw.), bevor Sie Code dafür bereitstellen. Einige Frameworks sind standardmäßig aktiviert, während für andere, z. B. Java und Python, unter Umständen eine einfache Konfiguration durch das Setzen eines Häkchens erforderlich ist, um sie zu aktivieren. Außerdem können Sie Ihr Anwendungsframework anpassen, z. B. die PHP-Version oder die Bitanzahl der Laufzeit. Weitere Informationen finden Sie unter [Konfigurieren der App in Azure App Service](web-sites-configure.md).

Da Sie sich nicht um das Webserver- oder Anwendungsframework kümmern müssen, geht es bei der Bereitstellung Ihrer App für App Service nur darum, Ihren Code und die Binärdateien, Inhaltsdateien und entsprechende Verzeichnisstruktur [in Azure im Verzeichnis **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (bzw. für WebJobs im Verzeichnis **/Data/Jobs**) bereitzustellen. App Service unterstützt die drei unten angegebenen Bereitstellungsprozesse.

- [FTP oder FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): Verwenden Sie das gewünschte FTP- oder FTPS-fähige Tool, um die Dateien nach Azure zu verschieben – von [FileZilla](https://filezilla-project.org) bis hin zu IDEs mit vollem Funktionsumfang wie [NetBeans](https://netbeans.org). Genau genommen handelt es sich hierbei um das Hochladen von Dateien. Von App Service werden keine zusätzlichen Dienste bereitgestellt, wie Versionskontrolle, Dateistrukturverwaltung usw. 
- [Kudu (Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): Dies ist das [Bereitstellungsmodul](https://github.com/projectkudu/kudu/wiki) in App Service. Sie können Ihren Code direkt aus einem Repository an Kudu übertragen. Kudu stellt außerdem weitere Dienste bereit, wenn Code übertragen wird, z. B. Versionskontrolle, Paketwiederherstellung, MSBuild und [Webhooks](https://github.com/projectkudu/kudu/wiki/Web-hooks) für die kontinuierliche Bereitstellung und andere Automatisierungsaufgaben. Alle diese Dienste können angepasst werden und werden in folgenden Fällen ausgelöst: 
    - Bei jeder Ausführung von **git push** in einem konfigurierten Git-Repository
	- Bei jeder Ausführung von **hg push** in einem konfigurierten Mercurial-Repository 
    - Bei jeder Synchronisierung eines verknüpften Cloudspeichers wie Dropbox oder OneDrive mit App Service 
- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): Dies sind die gleichen Tools, mit denen die Bereitstellung für IIS-Server automatisiert wird. Sie können Code für App Service direkt aus Ihren bevorzugten Microsoft-Tools bereitstellen, z. B. Visual Studio, WebMatrix und Visual Studio Team Services. Dieses Tool unterstützt die Differenzbereitstellung (diff-only), Datenbankerstellung, Transformationen von Verbindungszeichenfolgen usw. Web Deploy unterscheidet sich von Kudu darin, dass Anwendungsbinärdateien erstellt werden, bevor sie für Azure bereitgestellt werden. Ähnlich wie bei FTP werden von App Service keine weiteren Dienste bereitgestellt.

Gängige Webentwicklungstools unterstützen einen oder mehrere dieser Bereitstellungsprozesse. Das von Ihnen gewählte Tool bestimmt zwar die Bereitstellungsprozesse, die Sie nutzen können, aber die eigentliche verfügbare DevOps-Funktionalität hängt von der Kombination aus Bereitstellungsprozess und den von Ihnen gewählten speziellen Tools ab. Wenn Sie den Web Deploy-Vorgang beispielsweise über [Visual Studio mit Azure SDK](#vspros) durchführen, obwohl die Automation über Kudu nicht möglich ist, können Sie die Paketwiederherstellung und MSBuild-Automation in Visual Studio nutzen. Das Azure SDK enthält auch einen einfachen Assistenten, der Sie beim Erstellen der benötigten Azure-Ressourcen direkt auf der Visual Studio-Benutzeroberfläche unterstützt.

>[AZURE.NOTE] Mit diesen Bereitstellungsprozessen werden nicht tatsächlich die [Azure-Ressourcen bereitgestellt](resource-group-portal), die für Ihre App ggf. erforderlich sind, z. B. App Service-Plan, App Service-App und SQL-Datenbank. In den meisten verlinkten Gewusst-wie-Artikeln wird aber ausführlich gezeigt, wie Sie die App UND den Code dafür bereitstellen. Zusätzliche Optionen für die Bereitstellung von Azure-Ressourcen finden Sie auch im Abschnitt [Automatisieren der Bereitstellung mit Befehlszeilentools](#automate).

## <a name="ftp"></a>Bereitstellen durch das manuelle Kopieren von Dateien nach Azure
Wenn Sie es gewohnt sind, Ihre Webinhalte manuell zu Web-Hostern zu kopieren (ein gängiger Workflow für PHP-Entwickler), können Sie zum Kopieren der Dateien ein [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol)-Hilfsprogramm verwenden, z. B. Windows-Explorer oder [FileZilla](https://filezilla-project.org/).

Beim manuellen Kopieren von Dateien wird das FTP-Protokoll für die Bereitstellung verwendet (siehe [Übersicht über die Bereitstellungsprozesse](#overview)).

Vorteile des manuellen Kopierens von Dateien:

- Vertrautheit mit FTP-Tools 
- Sie wissen genau, wo die Dateien landen
- Erhöhte Sicherheit mit FTPS
- Gute Bereitstellungslösung, wenn Sie für die Webentwicklung einen geringen Toolaufwand bevorzugen (z. B. Entwickeln von Web-Apps mit dem Editor)

Nachteile des manuellen Kopierens von Dateien:

- Sie sind für die Bereitstellung von Dateien in den richtigen Verzeichnissen in App Service verantwortlich
- Keine Versionskontrolle für Rollback beim Auftreten von Fehlern
- Viele FTP-Tools ermöglichen keine Differenzbereitstellung (diff-only), es werden einfach alle Dateien kopiert. Bei großen Apps führt dies auch für kleinere Updates zu längeren Bereitstellungsdauern.

### <a name="howtoftp"></a>Gewusst wie: Bereitstellen durch das manuelle Kopieren von Dateien nach Azure
Das Kopieren von Dateien nach Azure umfasst einige einfache Schritte:

1. Erstellen Sie Anmeldeinformationen für die Bereitstellung für Ihre App im [Azure-Portal](https://portal.azure.com). Klicken Sie hierfür im Blatt Ihrer Anwendung auf **Einstellungen** > **Anmeldeinformationen für Bereitstellung**.
2. Nachdem Sie die Anmeldeinformationen für die Bereitstellung konfiguriert haben, rufen Sie die FTP-Verbindungsinformationen wie folgt ab: Wählen Sie **Einstellungen** > **Eigenschaften**, und kopieren Sie die Werte unter **FTP-/Entwicklungsbenutzer**, **FTP-Hostname** und **FTPS-Hostname**.
3. Verwenden Sie im FTP-Client die gesammelten Verbindungsinformationen, um eine Verbindung mit Ihrer App herzustellen.
4. Kopieren Sie Ihre Dateien und die entsprechende Verzeichnisstruktur in das [Verzeichnis **/site/wwwroot** in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (bzw. das Verzeichnis **/Data/Jobs** für WebJobs).
5. Navigieren Sie zur URL Ihrer App, um sicherzustellen, dass die Anwendung richtig ausgeführt wird. 

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erstellen einer PHP-MySQL-Web-App und Bereitstellen über FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Verwenden von FTP-Stapelskripts](http://support.microsoft.com/kb/96269)

## <a name="dropbox"></a>Bereitstellen per Synchronisierung mit einem Cloudordner
Eine gute Alternative zum [manuellen Kopieren von Dateien](#ftp) ist das Synchronisieren von Dateien und Ordnern mit App Service über einen Cloudspeicherdienst wie OneDrive und Dropbox. Im [Azure-Portal](https://portal.azure.com) können Sie in Ihrem Cloudspeicher einen speziellen Ordner konfigurieren, mit Ihrem App-Code und den App-Inhalten in diesem Ordner arbeiten und per Klick auf eine Schaltfläche die Synchronisierung mit App Service durchführen.

Bei der Synchronisierung mit einem Cloudordner wird der Kudu-Prozess für die Bereitstellung verwendet (siehe [Übersicht über die Bereitstellungsprozesse](#overview)).

Vorteile der Synchronisierung mit einem Cloudordner:

- Einfache Bereitstellung. Dienste wie OneDrive und Dropbox verfügen über Desktopclients für die Synchronisierung, sodass Ihr lokales Arbeitsverzeichnis auch Ihr Bereitstellungsverzeichnis ist.
- Bereitstellung mit nur einem Klick
- Alle Funktionen von Kudu sind verfügbar (z. B. Versionsverwaltung für die Bereitstellung, Rollback, Paketwiederherstellung, Automation)
- Gute Bereitstellungslösung, wenn Sie für die Webentwicklung einen geringen Toolaufwand bevorzugen

Nachteile der Synchronisierung mit einem Cloudordner:

- Keine gute Lösung für ein Teamprojekt

### <a name="howtodropbox"></a>Gewusst wie: Bereitstellen per Synchronisierung mit einem Cloudordner
 
* [Bereitstellen in Web-Apps aus Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Hier erfahren Sie, wie das [Azure-Portal](https://portal.azure.com) zum Einrichten der DropBox-Bereitstellung verwendet wird.
* [Dropbox-Bereitstellung in Web-Apps](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In diesem Video wird gezeigt, wie ein DropBox-Ordner mit einer Web-App verknüpft wird. Darüber hinaus wird erläutert, wie schnell Sie eine Web-App mit der einfachen Drag & Drop-Bereitstellung ausführen oder verwalten können.
* [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)

## Bereitstellen über eine IDE
Wenn Sie bereits [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) mit einem [Azure SDK](https://azure.microsoft.com/downloads/) oder WebMatrix oder andere IDE Suites wie [Xcode](https://developer.apple.com/xcode/) und [Eclipse](https://www.eclipse.org) verwenden, können Sie die Bereitstellung für Azure direkt in der IDE durchführen. Diese Option ist ideal für allein arbeitende Entwickler.

Visual Studio unterstützt alle drei Bereitstellungsprozesse (je nach Bedarf FTP, Git und Web Deploy). Mit anderen IDEs ist die Bereitstellung für App Service möglich, wenn diese über eine FTP- oder Git-Integration verfügen (siehe [Übersicht über die Bereitstellungsprozesse](#overview)).

Vorteile der Bereitstellung mit einer IDE:

- Potenzielle Verringerung des Toolaufwands für Ihren End-to-End-Anwendungslebenszyklus. Entwickeln, Debuggen, Nachverfolgen und Bereitstellen Ihrer App für Azure ohne Verlassen Ihrer IDE. 

Nachteile der Bereitstellung mit einer IDE:

- Höhere Komplexität in Bezug auf Tools
- Für ein Teamprojekt ist weiterhin ein Quellcodeverwaltungssystem erforderlich

<a name="vspros"></a> Weitere Vorteile der Bereitstellung per Visual Studio mit Azure SDK:

- Das Azure SDK macht Azure-Ressourcen in Visual Studio zu bevorzugten Elementen. Erstellen, Löschen, Bearbeiten, Starten und Beenden von Apps, Abfragen der Back-End-SQL-Datenbank, Live-Debuggen der Azure-App und vieles mehr. 
- Live-Bearbeitung von Codedateien in Azure
- Live-Debuggen von Apps in Azure
- Integrierter Azure Explorer
- Differenzbereitstellung (diff-only) 

###<a name="vs"></a>Gewusst wie: Direktes Bereitstellen über Visual Studio

* [Erste Schritte mit Azure und ASP.NET](web-sites-dotnet-get-started.md). In dieser Ressource wird das Erstellen und Bereitstellen eines einfachen ASP.NET MVC-Webprojekts mithilfe von Visual Studio und Web Deploy erläutert.
* [Bereitstellen von Azure-Webaufträgen mit Visual Studio](websites-dotnet-deploy-webjobs.md). Hier erfahren Sie, wie Sie Konsolenanwendungsprojekte für die Bereitstellung als WebJobs konfigurieren.  
* [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank in Web-Apps](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). In dieser Ressource wird das Erstellen und Bereitstellen eines ASP.NET MVC-Webprojekts mit einer SQL-Datenbank unter Verwendung von Visual Studio, Web Deploy und Entity Framework Code First-Migrationen erläutert.
* [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Einstieg in die Webbereitstellung mithilfe von Visual Studio. Obwohl der Inhalt nicht mehr auf dem neuesten Stand ist, sind Informationen enthalten, die noch immer relevant sind. Dazu gehört eine Übersicht über die Optionen für die Bereitstellung einer Datenbank zusammen mit der Webanwendung und eine Liste mit zusätzlichen Bereitstellungsaufgaben, die Sie möglicherweise noch erledigen müssen oder die Sie manuell konfigurieren, sodass diese Aufgaben von Visual Studio ausgeführt werden. In diesem Thema wird die Bereitstellung allgemein erläutert und nicht nur die Bereitstellung in Web-Apps.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction) Dabei handelt es sich um eine Lernprogrammserie mit zwölf Teilen, in der die Bereitstellungsaufgaben vollständig vorgestellt werden. Seit dem Erscheinen des Lernprogramms sind einige Azure-Bereitstellungsfunktionen hinzugefügt worden. Hinweise erläutern, welche Informationen fehlen.
* [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). In diesem Artikel wird erläutert, wie ein ASP.NET-Webprojekt in Visual Studio bereitgestellt wird und wie das Git-Plug-In verwendet wird, um den Code für Git zu übernehmen und Azure mit dem Git-Repository zu verknüpfen. Visual Studio 2013 und höhere Versionen bieten eine integrierte Git-Unterstützung. Daher ist die Installation eines Plug-Ins nicht erforderlich.

###<a name="webmatrix"></a>Gewusst wie: Direktes Bereitstellen über WebMatrix

* [Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix](web-sites-nodejs-use-webmatrix.md)
* [WebMatrix 3: Integrated Git and Deployment to Azure (Integriertes Git und Bereitstellung für Azure, in englischer Sprache)](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). In diesem Artikel wird das Verwenden von WebMatrix zum Bereitstellen über ein Git-Repository für die Quellcodeverwaltung beschrieben.

## <a name="onprem"></a>Bereitstellen über ein lokales Quellcodeverwaltungssystem

Wenn Sie in einem Entwicklungsteam beliebiger Größe arbeiten und ein lokales Quellcodeverwaltungssystem wie [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) oder [Mercurial](http://mercurial.selenic.com/) verwenden, können Sie App Service so konfigurieren, dass die Anwendung in das Repository integriert ist. So können Sie die Bereitstellung für App Service direkt innerhalb Ihres Workflows für die Quellcodeverwaltung durchführen. Wenn Sie TFS verwenden, können Sie die Anwendung auch so konfigurieren, dass die Bereitstellung für App Service kontinuierlich erfolgt.

Bei TFS wird Web Deploy für die Bereitstellung für App Service verwendet, und bei der Bereitstellung aus Git/Mercurial-Repositorys wird Kudu genutzt (siehe [Übersicht über die Bereitstellungsprozesse](#overview)).

Vorteile der Bereitstellung über ein lokales Quellcodeverwaltungssystem:

- Unterstützung der Bereitstellung über alle Sprachenframeworks bzw. Git- oder Mercurial-Clients, z. B. [Xcode](https://developer.apple.com/xcode/) und [Eclipse](https://www.eclipse.org)
- Standortbezogene Bereitstellung und mögliche Bereitstellung unterschiedlicher Versionen für separate [Slots](web-sites-staged-publishing)
- Gut geeignet für Entwicklungsteams jeder Größe

Nachteile der Bereitstellung über ein lokales Quellcodeverwaltungssystem:

- Kenntnisse in Bezug auf das gewählte Quellcodeverwaltungssystem erforderlich
- Ggf. mehr Funktionen und Features als benötigt
- Fehlende fertige Lösungen für die kontinuierliche und standortbezogene Bereitstellung über Git- und Mercurial-Clienttools 

Weitere Vorteile der Bereitstellung mit TFS:

- Fortlaufende Integration (CI) für Buildvorgänge, Tests und Bereitstellung
- Integrierte Tools für die Zusammenarbeit, die für vorhandene IDEs oder Editoren geeignet sind
- Unterstützung von Git für die verteilte Versionskontrolle oder Team Foundation-Versionskontrolle (TFVC) zur Realisierung einer zentralisierten Versionskontrolle 
- Umfassende Tools für flexible Bereitstellung
- Fertige Integrationen für [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Azure Service Bus](/services/service-bus/) und vieles mehr 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) ist für Teams mit bis zu fünf Entwicklern kostenlos.

###<a name="tfs"></a>Gewusst wie: Kontinuierliches Bereitstellen mit TFS

* [Kontinuierliche Bereitstellung für Cloud Services in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md). Dieses Dokument ist in erster Linie für einen Azure-Cloud-Dienst bestimmt. Einige Inhalte gelten allerdings auch für Web-Apps.

###<a name="gitmercurial"></a>Gewusst wie: Bereitstellen aus einem lokalen Git- oder Mercurial-Repository

* [Veröffentlichen aus der Quellcodeverwaltung in Web-Apps mit Git](web-sites-publish-source-control.md). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus in einer Web-App zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites aus beliebigen git/hg-Repositorys, in englischer Sprache)](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog, der die Funktion "Externes Repository" in Web-Apps erläutert.
* [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)
* [Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Blogeintrag von Scott Hanselman.

## Bereitstellen über einen cloudbasierten Quellcodeverwaltungsdienst
Wenn Sie in einem Entwicklungsteam beliebiger Größe arbeiten und einen cloudbasierten Quellcodeverwaltungsdienst wie [Visual Studio Team Services](http://www.visualstudio.com/) (früher Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) und [Kiln](https://www.fogcreek.com/kiln/) verwenden, können Sie App Service so konfigurieren, dass die Anwendung in Ihr Repository integriert ist und eine kontinuierliche Bereitstellung durchgeführt wird.

Bei Visual Studio Team Services wird Web Deploy für die Bereitstellung für App Service verwendet, und bei der Bereitstellung aus Online-Repositorys wird Kudu genutzt (siehe [Übersicht über die Bereitstellungsprozesse](#overview)).

Vorteile der Bereitstellung über einen cloudbasierten Quellcodeverwaltungsdienst:

- Unterstützung aller Sprachen-Frameworks
- Kontinuierliche Bereitstellung für Git- und Mercurial-Repositorys ohne Konfiguration 
- Verzweigungsbezogene Bereitstellung zur Bereitstellung unterschiedlicher Verzweigungen für unterschiedliche [Slots](web-sites-staged-publishing)
- Gut geeignet für Entwicklungsteams jeder Größe

Nachteile der Bereitstellung über einen cloudbasierten Quellcodeverwaltungsdienst:

- Kenntnisse in Bezug auf den gewählten Quellcodeverwaltungsdienst erforderlich
- Ggf. mehr Funktionen und Features als benötigt

Weitere Vorteile der Bereitstellung über Visual Studio Team Services:

- Für ein Team mit bis zu fünf Entwicklern kostenlos
- Fortlaufende Integration (CI) für Buildvorgänge, Tests und Bereitstellung
- Integrierte Tools für die Zusammenarbeit, die für vorhandene IDEs oder Editoren geeignet sind
- Unterstützung von Git für die verteilte Versionskontrolle oder Team Foundation-Versionskontrolle (TFVC) zur Realisierung einer zentralisierten Versionskontrolle 
- Umfassende Tools für flexible Bereitstellung
- Fertige Integrationen für [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [Azure Service Bus](/services/service-bus/) und vieles mehr 
- Umfangreiche Dashboards für die einfache Berichterstellung mit [Verbindung mit Power BI](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs)

###<a name="vsts"></a>Gewusst wie: Fortlaufendes Bereitstellen mit Visual Studio Team Services

- [Continuous Delivery für Azure mithilfe von Visual Studio Team Services und TFVC](../cloud-services/cloud-services-continuous-delivery-use-vso.md). In diesem Tutorial erfahren Sie Schritt für Schritt, wie Continuous Delivery über Visual Studio Team Services für eine Web-App mithilfe von TFVC eingerichtet wird. 
- [Continuous Delivery für Azure mithilfe von Visual Studio Team Services und Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md) Dieses Lernprogramm ist vergleichbar mit dem vorangehenden Lernprogramm, verwendet jedoch anstelle von TFVC das Verwaltungstool Git.

###<a name="cloudgitmercurial"></a>Gewusst wie: Bereitstellen aus einem cloudbasierten Git- oder Mercurial-Repository

- [Veröffentlichen aus der Quellcodeverwaltung in Web-Apps mit Git](web-sites-publish-source-control.md). Sie erfahren, wie Sie die kontinuierliche Bereitstellung von Repositorys über GitHub, CodePlex oder BitBucket aktivieren. Obwohl in diesem Lernprogramm die Veröffentlichung eines Git-Repositorys gezeigt wird, ist der Vorgang bei Mercurial-Repositorys, die in CodePlex oder BitBucket gehostet werden, ähnlich.
- [Bereitstellen in Web-Apps mit GitHub unter Verwendung von Kudu](https://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Das Video von Scott Hanselman und David Ebbo zeigt, wie Sie eine Web-App direkt von GitHub für App Service bereitstellen.
- [Schaltfläche zur Azure-Bereitstellung für Web-Apps](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog über eine Methode zum Auslösen der Bereitstellung aus einem Git-Repository.
- [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)

Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Veröffentlichen aus der Quellcodeverwaltung in Web-Apps mit Git](web-sites-publish-source-control.md). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus in einer Web-App zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). 

## <a name="automate"></a>Automatisieren der Bereitstellung mit Befehlszeilentools

* [Automatisieren der Bereitstellung mit MSBuild](#msbuild)
* [Kopieren von Dateien mit FTP-Tools und Skripts](#ftp)
* [Automatisieren der Bereitstellung mit Windows PowerShell](#powershell)
* [Automatisieren der Bereitstellung mit .NET Verwaltungs-API](#api)
* [Bereitstellen über die Azure-Befehlszeilenschnittstelle (Azure-CLI)](#cli)
* [Bereitstellen über die Web Deploy-Befehlszeile](#webdeploy)
* [Verwenden von FTP-Stapelskripts](http://support.microsoft.com/kb/96269)
 
Eine weitere Möglichkeit der Bereitstellung ist die Nutzung eines cloudbasierten Diensts, wie z. B. [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy). Weitere Informationen finden Sie unter [Bereitstellen von ASP.NET-Anwendungen für Azure-Websites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatisieren der Bereitstellung mit MSBuild

Wenn Sie die [Visual Studio IDE](#vs) für die Entwicklung verwenden, können Sie [MSBuild](http://msbuildbook.com/) zur Automatisierung sämtlicher Aufgaben nutzen, die Sie in Ihrer IDE ausführen können. Sie können MSBuild so konfigurieren, dass zum Kopieren der Dateien entweder [Web Deploy](#webdeploy) oder [FTP/FTPS](#ftp) verwendet wird. Mit Web Deploy können auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, automatisiert werden, wie beispielsweise das Bereitstellen von Datenbanken.

Weitere Informationen zur Befehlszeilen-Bereitstellung mithilfe von MSBuild finden Sie in den folgenden Ressourcen:

* [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilenbereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Der zehnte Teil in einer Lernprogrammserie, in dem die Bereitstellung in Azure mithilfe von Visual Studio erläutert wird. In diesem Lernprogramm wird gezeigt, wie die Befehlszeile für die Bereitstellung verwendet wird, nachdem Veröffentlichungsprofile in Visual Studio eingerichtet wurden.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build (In der Microsoft Build Engine: Verwenden von MSBuild und Team Foundation Build, in englischer Sprache)](http://msbuildbook.com/). Buch, in dem Kapitel zur Verwendung von MSBuild für die Bereitstellung enthalten sind.

###<a name="powershell"></a>Automatisieren der Bereitstellung mit Windows PowerShell

Sie können MSBuild- oder FTP-Bereitstellungsfunktionen mithilfe von [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) ausführen. Wenn dies der Fall ist, können Sie auch eine Sammlung von Windows PowerShell-Cmdlets nutzen, mit denen die Azure-REST-Verwaltungs-API leicht aufgerufen werden kann.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Bereitstellen einer Web-App in einem GitHub-Repository](app-service-web-arm-from-github-provision.md)
* [Bereitstellen einer Web-App mit einer SQL-Datenbank](app-service-web-arm-with-sql-database-provision.md)
* [Vorhersagbares Bereitstellen von Microservices in Azure](app-service-deploy-complex-application-predictably.md)
* [Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure – Automatisierung, in englischer Sprache)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). In diesem E-Book-Kapitel wird erklärt, wie die im E-Book dargestellte Beispielanwendung Windows PowerShell-Skripte nutzt, um eine Azure-Testumgebung zu erstellen und wie die Bereitstellung für diese Umgebung erfolgt. Im Abschnitt [Ressourcen](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) finden Sie Links für die weiterführende Azure PowerShell-Dokumentation.
* [Veröffentlichung in Entwicklungs- und Testumgebungen mithilfe von Windows PowerShell-Skripts](http://msdn.microsoft.com/library/dn642480.aspx). Hier erfahren Sie, wie Sie Windows PowerShell-Bereitstellungsskripts verwenden, die von Visual Studio generiert werden.

###<a name="api"></a>Automatisieren der Bereitstellung mit der .NET-Verwaltungs-API

Sie können C#-Code schreiben, um die MSBuild- oder FTP-Funktionen für die Bereitstellung auszuführen. Wenn Sie dies tun, können Sie auf die Azure-REST-Verwaltungs-API zugreifen, um die Website-Verwaltungsfunktionen auszuführen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Einführung in die .NET-Verwaltungs-API und Links für die weiterführende Dokumentation.

###<a name="cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle (Azure-CLI)

Über die Befehlszeile können Sie auf Windows-, Mac- oder Linux-Computern FTP-basierte Bereitstellungen durchführen. In diesem Fall können Sie über die Azure-Befehlszeilenschnittstelle auch auf die Azure-REST-Verwaltungs-API zugreifen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Azure-Befehlszeilentools](/downloads/#cmd-line-tools). Portalseite auf Azure.com mit Informationen zu Befehlszeilentools.

###<a name="webdeploy"></a>Bereitstellen über die Web Deploy-Befehlszeile

Bei [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) handelt es sich um Microsoft-Software für die Bereitstellung in IIS, wobei neben den Funktionen für die intelligente Dateisynchronisierung auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen und bei der Verwendung von FTP nicht automatisierbar sind, ausgeführt und koordiniert werden können. Mithilfe von Web Deploy können beispielsweise neue Datenbanken oder Datenbank-Updates in Ihrer Web-App bereitgestellt werden. Auch die Zeit, die zum Aktualisieren einer vorhandenen Website erforderlich ist, kann mit Web Deploy verringert werden, da dieses Programm auf intelligente Weise lediglich geänderte Dateien kopiert. Bei Microsoft WebMatrix, Visual Studio, Visual Studio Team Services und Team Foundation Server ist die Web Deploy-Unterstützung bereits integriert. Sie können Web Deploy aber auch direkt über die Befehlszeile verwenden, um die Bereitstellung zu automatisieren. Die Web Deploy-Befehle sind äußerst leistungsstark, stellen aber einen gewaltigen Lernprozess dar.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Simple Web Apps: Deployment (Einfache Web-Apps: Bereitstellung, in englischer Sprache)](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog von David Ebbo über ein von ihm geschriebenes Tool zur einfacheren Verwendung von Web Deploy.
* [Webbereitstellungstool](http://technet.microsoft.com/library/dd568996). Offizielle Dokumentation auf der Microsoft TechNet-Website. Der Inhalt ist nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
* [Verwenden von Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Offizielle Dokumentation auf der Microsoft IIS.NET-Website. Der Inhalt ist auch nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
* [StackOverflow](http://www.stackoverflow.com). Die beste Adresse, um aktuelle Informationen zur Verwendung von Web Deploy über die Befehlszeile zu erhalten.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilenbereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Bei MSBuild handelt es sich um das von Visual Studio verwendete Buildmodul, das auch über die Befehlszeile zum Bereitstellen von Webanwendungen in Web-Apps genutzt werden kann. Dieses Lernprogramm ist Bestandteil einer Serie, in der es in erster Linie um die Visual Studio-Bereitstellung geht.

##<a name="nextsteps"></a>Nächste Schritte

In einigen Szenarios möchten Sie ggf. in der Lage sein, bequem zwischen einer Staging- und Produktionsversion Ihrer Web-App zu wechseln. Weitere Informationen hierzu finden Sie unter [Stagingbereitstellung in Web-Apps](web-sites-staged-publishing.md).

Die Aufstellung eines Sicherungs- und Wiederherstellungsplans ist ein wesentlicher Bestandteil jedes Bereitstellungsworkflows. Weitere Informationen zu den Sicherungs- und Wiederherstellungsfunktionen für Web-Apps finden Sie unter [Sichern von Web-Apps](web-sites-backup.md).

Informationen zur Verwendung der rollenbasierten Zugriffssteuerung von Azure zum Verwalten des Zugriffs auf die Web-App-Bereitstellung finden Sie unter [RBAC and Web App Publishing](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/) (in englischer Sprache).

Informationen zu weiteren Bereitstellungsthemen finden Sie in den entsprechenden Abschnitten der [Web-App-Dokumentation](/documentation/services/web-sites/).

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
 

<!---HONumber=AcomDC_0211_2016-->
