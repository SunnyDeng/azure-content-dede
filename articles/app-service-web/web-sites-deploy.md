<properties
	pageTitle="Bereitstellen von Web-Apps in Azure App Service"
	description="Erfahren Sie, welche Methoden zum Bereitstellen von Inhalten in Web-Apps zur Verfügung stehen."
	services="app-service\web"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="tdykstra"/>

#Bereitstellen von Web-Apps in Azure App Service

## Übersicht

Für die Bereitstellung Ihrer eigenen Inhalte in [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) gibt es zahlreiche Optionen. Dieses Thema enthält eine kurze Übersicht über die jeweilige Option und Links, um weitere Informationen zu erhalten.


###<a name="cloud"></a>Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem

Für die Bereitstellung einer Web-App ist es am einfachsten, einen [Workflow für die kontinuierliche Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) einzurichten, der in Ihr [Quellcodeverwaltungssystem](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) integriert ist. Durch die Automatisierung ist der Entwicklungsprozess effizienter, und darüber hinaus können auch die Prozesse für die Sicherung und Wiederherstellung besser verwaltet werden und sind zuverlässiger.

Wenn Sie die Quellcodeverwaltung bisher noch nicht eingerichtet haben, ist es am einfachsten, wenn Sie dafür ein in der Cloud gehostetes Quellcodeverwaltungssystem verwenden.

* [Visual Studio Online](#vso)
* [Repositorywebsites über Git](#git)
* [Repositorywebsites über Mercurial](#mercurial)
* [Dropbox](#dropbox)

###<a name="ide"></a>Bereitstellen über eine IDE

Bei [Visual Studio](http://www.visualstudio.com/) und [WebMatrix](http://www.microsoft.com/web/webmatrix/) handelt es sich um Microsoft-IDEs (Integrated Development Environments = Integrierte Entwicklungsumgebungen), die Sie für die Webentwicklung verwenden können. Beide stellen integrierte Funktionen für die einfache Bereitstellung von Web-Apps zur Verfügung. [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) kann bei beiden Entwicklungsumgebungen zum Einsatz kommen, um zusätzliche Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, zu automatisieren. Bei diesen Aufgaben kann es sich um die Datenbankbereitstellung oder Änderungen hinsichtlich der Verbindungszeichenfolge handeln. Die Bereitstellung kann bei beiden Umgebungen auch mithilfe von [FTP oder FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) erfolgen.

WebMatrix kann schnell installiert werden und ist leicht erlernbar. Visual Studio bietet allerdings viele weitere Funktionen für die Arbeit mit Web-Apps. In der Visual Studio-IDE können Sie Web-Apps erstellen, anhalten, starten und löschen. Darüber hinaus können Sie Protokolle anzeigen, während diese in Echtzeit erstellt werden. Neben vielen weiteren Funktionen ist auch das Remotedebuggen möglich. Visual Studio kann auch mit Quellcodeverwaltungssystemen, wie beispielsweise [Visual Studio Online](#vso), [Team Foundation Server](#tfs) und [Git-Repositorys](#git), integriert werden.

* [Visual Studio](#vs)
* [WebMatrix](#webmatrix)

###<a name="ftp"></a>Bereitstellen über ein FTP-Dienstprogramm

Unabhängig davon, welche IDE Sie verwenden, können Sie Inhalte in Ihrer Web-App bereitstellen, indem Sie zum Kopieren von Dateien [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) verwenden. Sie können problemlos FTP-Anmeldeinformationen für eine Web-App erstellen und sie in einer beliebigen Anwendung verwenden, die FTP unterstützt, z. B. Internet Explorer und kostenlose Hilfsprogramme mit vollem Funktionsumfang wie [FileZilla](https://filezilla-project.org/). Web-Apps unterstützen auch das sicherere FTPS-Protokoll.

Obwohl es ziemlich einfach ist, die Dateien Ihrer Web-App mithilfe von FTP-Dienstprogrammen in Azure zu kopieren, berücksichtigen oder koordinieren diese Programme nicht automatisch die dazugehörigen Bereitstellungsaufgaben, wie beispielsweise das Bereitstellen einer Datenbank oder Ändern der Verbindungszeichenfolgen. Bei vielen FTP-Tools werden die Quell- und die Zieldateien nicht verglichen, sodass das Kopieren von unveränderten Dateien übersprungen werden könnte. Wenn bei umfangreichen Web-Apps stets alle Dateien kopiert werden, kann es selbst bei kleineren Updates zu langen Wartezeiten hinsichtlich der Bereitstellung kommen, da immer alle Dateien kopiert werden.

###<a name="onpremises"></a>Bereitstellen über ein lokales Quellcodeverwaltungssystem

Wenn Sie TFS, Git oder Mercurial in einem lokalen (nicht in einer Cloud gehosteten) Repository verwenden, kann die Bereitstellung direkt vom Repository in Web-Apps erfolgen.

* [Team Foundation Server (TFS)](#tfs)
* [Lokale Git- oder Mercurial-Repositorys](#onpremises)

###<a name="commandline"></a>Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API

Die Automatisierung Ihres Workflows für die Entwicklung ist immer die beste Wahl. Wenn Sie diese Aufgabe aber nicht direkt in Ihrem Quellcodeverwaltungssystem ausführen können, können Sie die Automatisierung mithilfe von Befehlszeilentools manuell einrichten. Im Allgemeinen sind dafür mehrere Tools oder Frameworks erforderlich, da bei der Bereitstellung oftmals Website-Verwaltungsfunktionen ausgeführt werden und Inhalte kopiert werden.

Mithilfe von Azure werden die für die Bereitstellung durchzuführenden Website-Verwaltungsaufgaben vereinfacht. Eine REST-Verwaltungs-API und mehrere Frameworks stehen zur Verfügung, sodass das Arbeiten mit der API einfacher ist.

* [FTP](#ftp)
* [MSBuild](#msbuild)
* [FTP-Skripte](#ftp2)
* [Windows PowerShell](#powershell)
* [.NET-Verwaltungs-API](#api)
* [Installieren der Azure-Befehlszeilenschnittstelle (Azure-CLI)](#cli)
* [Web Deploy-Befehlszeile](#webdeploy)
 
###<a name="octopus"></a>Octopus Deploy

[Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy) kann mit App Service-Web-Apps verwendet werden. Weitere Informationen finden Sie unter [Bereitstellen von ASP.NET-Anwendungen für Azure-Websites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).


##<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (zuvor Team Foundation Service) ist die cloudbasierte Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Für ein Team mit bis zu 5 Entwicklern ist dieser Dienst kostenlos. Sie können eine kontinuierliche Bereitstellung in einer App Service-Web-App gewährleisten und für das Repository entweder [Git oder TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) verwenden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und TFVC](../cloud-services-continuous-delivery-use-vso.md). In diesem Lernprogramm wird Schritt für Schritt gezeigt, wie die kontinuierliche Bereitstellung aus Visual Studio Online in einer Web-App mithilfe von TFVC eingerichtet wird. Bei TFVC handelt es sich um die zentralisierte Quellcodeverwaltungsoption. Git ist hingegen die dezentralisierte Quellcodeverwaltungsoption.
* [Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und Git](../cloud-services-continuous-delivery-use-vso-git.md). Dieses Lernprogramm ist vergleichbar mit dem vorangehenden Lernprogramm, verwendet jedoch anstelle von TFVC das Verwaltungstool Git.

##<a name="git"></a>Repositorywebsites über Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) ist ein beliebtes dezentralisiertes Quellcodeverwaltungssystem. Azure verfügt über integrierte Funktionen, mit denen Sie aus gängigen webbasierten Repositorywebsites mit Git-Repositorys, wie beispielsweise [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) und [BitBucket](https://bitbucket.org/), auf einfache Weise die Bereitstellung in einer Web-App automatisieren können. Ein Vorteil bei der Verwendung von Git für die Bereitstellung besteht darin, dass das Zurücksetzen auf eine frühere Bereitstellung relativ einfach ist.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung in Web-Apps mit Git](web-sites-publish-source-control.md). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus in einer Web-App zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Bereitstellen in Web-Apps mit GitHub unter Verwendung von Kudu](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Das Video von Scott Hanselman und David Ebbo zeigt, wie Sie eine Web-App direkt von GitHub in Web-Apps bereitstellen.
* [Schaltfläche zur Azure-Bereitstellung für Web-Apps](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog über eine Methode zum Auslösen der Bereitstellung aus einem Git-Repository.
* [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)

##<a name="mercurial"></a>Repositorywebsites über Mercurial

Wenn Sie [Mercurial](http://mercurial.selenic.com/) als Quellcodeverwaltungssystem nutzen und das Repository in [CodePlex](http://www.codeplex.com/) oder [BitBucket](https://bitbucket.org/) speichern, können Sie die integrierten Funktionen in Azure App Service verwenden, sodass Ihre Inhalte automatisch bereitgestellt werden.

Weitere Informationen zur Bereitstellung mithilfe von Mercurial finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung in Web-Apps mit Git](web-sites-publish-source-control.md). Obwohl in diesem Lernprogramm die Veröffentlichung eines Git-Repositorys gezeigt wird, ist der Vorgang bei Mercurial-Repositorys, die in CodePlex oder BitBucket gehostet werden, ähnlich.
* [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)

##<a name="dropbox"></a>Dropbox

[DropBox](https://www.dropbox.com/) stellt kein Quellcodeverwaltungssystem dar. Wenn Sie allerdings den Quellcode in DropBox speichern, können Sie die Bereitstellung über Ihr DropBox-Konto automatisieren.

* [Bereitstellen in Web-Apps aus Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Hier erfahren Sie, wie das [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) zum Einrichten der DropBox-Bereitstellung verwendet wird.
* [Dropbox-Bereitstellung in Web-Apps](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In diesem Video wird gezeigt, wie ein DropBox-Ordner mit einer Web-App verknüpft wird. Darüber hinaus wird erläutert, wie schnell Sie eine Web-App mit der einfachen Drag & Drop-Bereitstellung ausführen oder verwalten können.
* [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)

##<a name="vs"></a>Visual Studio

Weitere Informationen zur Bereitstellung von Visual Studio in Web-Apps finden Sie in den folgenden Ressourcen:

* [Erste Schritte mit Azure und ASP.NET](web-sites-dotnet-get-started.md). In dieser Ressource wird das Erstellen und Bereitstellen eines einfachen ASP.NET MVC-Webprojekts mithilfe von Visual Studio und Web Deploy erläutert.
* [Bereitstellen von Azure-Webaufträgen mit Visual Studio](websites-dotnet-deploy-webjobs.md). Hier erfahren Sie, wie Sie Konsolenanwendungsprojekte für die Bereitstellung als WebJobs konfigurieren.  
* [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank in Web-Apps](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). In dieser Ressource wird das Erstellen und Bereitstellen eines ASP.NET MVC-Webprojekts mit einer SQL-Datenbank unter Verwendung von Visual Studio, Web Deploy und Entity Framework Code First-Migrationen erläutert.
* [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Einstieg in die Webbereitstellung mithilfe von Visual Studio. Obwohl der Inhalt nicht mehr auf dem neuesten Stand ist, sind Informationen enthalten, die noch immer relevant sind. Dazu gehört eine Übersicht über die Optionen für die Bereitstellung einer Datenbank zusammen mit der Webanwendung und eine Liste mit zusätzlichen Bereitstellungsaufgaben, die Sie möglicherweise noch erledigen müssen oder die Sie manuell konfigurieren, sodass diese Aufgaben von Visual Studio ausgeführt werden. In diesem Thema wird die Bereitstellung allgemein erläutert und nicht nur die Bereitstellung in Web-Apps.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction) Dabei handelt es sich um eine Lernprogrammserie mit zwölf Teilen, in der die Bereitstellungsaufgaben vollständig vorgestellt werden. Seit dem Erscheinen des Lernprogramms sind einige Azure-Bereitstellungsfunktionen hinzugefügt worden. Hinweise erläutern, welche Informationen fehlen.
* [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). In diesem Artikel wird erläutert, wie ein ASP.NET-Webprojekt in Visual Studio bereitgestellt wird und wie das Git-Plug-In verwendet wird, um den Code für Git zu übernehmen und Azure mit dem Git-Repository zu verknüpfen. Visual Studio 2013 und höhere Versionen bieten eine integrierte Git-Unterstützung, deshalb ist die Installation eines Plug-Ins nicht erforderlich.

##<a name="webmatrix"></a>WebMatrix

Weitere Informationen zur Bereitstellung von WebMatrix in Web-Apps finden Sie in den folgenden Ressourcen:

* [Entwickeln und Bereitstellen von Web-Apps mit Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md). In dieser Ressource wird das Erstellen einer einfachen ASP.NET-Web-App unter Verwendung einer WebMatrix-Vorlage und das Bereitstellen in einer Web-App mithilfe von WebMatrix und Web Deploy erläutert.
* [Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix](web-sites-nodejs-use-webmatrix.md)
* [Erstellen und Bereitstellen einer PHP-MySQL-Web-App mithilfe von WebMatrix](web-sites-php-mysql-use-webmatrix.md).
* [WebMatrix 3: Integrated Git and Deployment to Azure (Integriertes Git und Bereitstellung für Azure, in englischer Sprache)](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). In diesem Artikel wird das Verwenden von WebMatrix zum Bereitstellen über ein Git-Repository für die Quellcodeverwaltung beschrieben.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erstellen einer PHP-MySQL-Web-App und Bereitstellen über FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Verwalten von Web-Apps](web-sites-manage.md#ftp-credentials). Diese Ressource verfügt über zusätzliche Informationen zum Festlegen von FTP-Anmeldeinformationen, die nicht im PHP-Lernprogramm enthalten sind.

##<a name="tfs"></a>Team Foundation Server (TFS)

Bei Team Foundation Server handelt es sich um die lokale Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Sie können TFS für die kontinuierliche Bereitstellung in einer Web-App einrichten.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Kontinuierliche Bereitstellung für Cloud Services in Azure](../cloud-services-dotnet-continuous-delivery.md). Dieses Dokument ist in erster Linie für einen Azure-Cloud-Dienst bestimmt. Einige Inhalte gelten allerdings auch für Web-Apps.

##<a name="gitmercurial"></a>Lokale Git- oder Mercurial-Repositorys

Sie können in Azure die URL eines beliebigen Repositorys eingeben, das Git oder Mercurial nutzt, um die Bereitstellung von diesem Standort aus durchzuführen. Außerdem werden direkte Push-Übertragungen aus einem lokalen Git-Repository an eine Web-App unterstützt.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung in Web-Apps mit Git](web-sites-publish-source-control.md). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus in einer Web-App zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites aus beliebigen git/hg-Repositorys, in englischer Sprache)](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog, der die Funktion "Externes Repository" in Web-Apps erläutert.
* [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)
* [Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Blogeintrag von Scott Hanselman.


##<a name="msbuild"></a>MSBuild

Wenn Sie die [Visual Studio IDE](#vs) für die Entwicklung verwenden, können Sie [MSBuild](http://msbuildbook.com/) zur Automatisierung sämtlicher Aufgaben nutzen, die Sie in Ihrer IDE ausführen können. Sie können MSBuild so konfigurieren, dass zum Kopieren der Dateien entweder [Web Deploy](#webdeploy) oder [FTP/FTPS](#ftp) verwendet wird. Mit Web Deploy können auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, automatisiert werden, wie beispielsweise das Bereitstellen von Datenbanken.

Weitere Informationen zur Befehlszeilen-Bereitstellung mithilfe von MSBuild finden Sie in den folgenden Ressourcen:

* [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilenbereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Der zehnte Teil in einer Lernprogrammserie, in dem die Bereitstellung in Azure mithilfe von Visual Studio erläutert wird. In diesem Lernprogramm wird gezeigt, wie die Befehlszeile für die Bereitstellung verwendet wird, nachdem Veröffentlichungsprofile in Visual Studio eingerichtet wurden.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build (In der Microsoft Build Engine: Verwenden von MSBuild und Team Foundation Build, in englischer Sprache)](http://msbuildbook.com/). Buch, in dem Kapitel zur Verwendung von MSBuild für die Bereitstellung enthalten sind.

##<a name="ftp2"></a>FTP-Skripte

Sie können auf einfache Weise [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)-Anmeldeinformationen für eine Web-App erstellen. Die Anmeldeinformationen können anschließend auch mit FTP-Batchskripts verwendet werden.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Verwenden von FTP-Stapelskripts](http://support.microsoft.com/kb/96269)

##<a name="powershell"></a>Windows PowerShell

Sie können MSBuild- oder FTP-Bereitstellungsfunktionen mithilfe von [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) ausführen. Wenn dies der Fall ist, können Sie auch eine Sammlung von Windows PowerShell-Cmdlets nutzen, mit denen die Azure-REST-Verwaltungs-API leicht aufgerufen werden kann.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure – Automatisierung, in englischer Sprache)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). In diesem E-Book-Kapitel wird erklärt, wie die im E-Book dargestellte Beispielanwendung Windows PowerShell-Skripte nutzt, um eine Azure-Testumgebung zu erstellen und wie die Bereitstellung für diese Umgebung erfolgt. Im Abschnitt [Ressourcen](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) finden Sie Links für die weiterführende Azure PowerShell-Dokumentation.
* [Veröffentlichung in Entwicklungs- und Testumgebungen mithilfe von Windows PowerShell-Skripts](http://msdn.microsoft.com/library/dn642480.aspx). Hier erfahren Sie, wie Sie Windows PowerShell-Bereitstellungsskripts verwenden, die von Visual Studio generiert werden.

##<a name="api"></a>.NET-Verwaltungs-API

Sie können C#-Code schreiben, um die MSBuild- oder FTP-Funktionen für die Bereitstellung auszuführen. Wenn Sie dies tun, können Sie auf die Azure-REST-Verwaltungs-API zugreifen, um die Website-Verwaltungsfunktionen auszuführen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Einführung in die .NET-Verwaltungs-API und Links für die weiterführende Dokumentation.

##<a name="cli"></a>Azure-Befehlszeilenschnittstelle (Azure-CLI)

Über die Befehlszeile können Sie auf Windows-, Mac- oder Linux-Computern FTP-basierte Bereitstellungen durchführen. In diesem Fall können Sie über die Azure-Befehlszeilenschnittstelle auch auf die Azure-REST-Verwaltungs-API zugreifen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Azure-Befehlszeilentools](/downloads/#cmd-line-tools). Portalseite auf Azure.com mit Informationen zu Befehlszeilentools.

##<a name="webdeploy"></a>Web Deploy-Befehlszeile

Bei [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) handelt es sich um Microsoft-Software für die Bereitstellung in IIS, wobei neben den Funktionen für die intelligente Dateisynchronisierung auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen und bei der Verwendung von FTP nicht automatisierbar sind, ausgeführt und koordiniert werden können. Mithilfe von Web Deploy können beispielsweise neue Datenbanken oder Datenbank-Updates in Ihrer Web-App bereitgestellt werden. Auch die Zeit, die zum Aktualisieren einer vorhandenen Website erforderlich ist, kann mit Web Deploy verringert werden, da dieses Programm auf intelligente Weise lediglich geänderte Dateien kopiert. Microsoft WebMatrix, Visual Studio, Visual Studio Online und Team Foundation Server verfügen über die integrierte Unterstützung für Web Deploy. Sie können Web Deploy aber auch direkt über die Befehlszeile verwenden, um die Bereitstellung zu automatisieren. Die Web Deploy-Befehle sind äußerst leistungsstark, stellen aber einen gewaltigen Lernprozess dar.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Simple Web Apps: Deployment (Einfache Web-Apps: Bereitstellung, in englischer Sprache)](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog von David Ebbo über ein von ihm geschriebenes Tool zur einfacheren Verwendung von Web Deploy.
* [Webbereitstellungstool](http://technet.microsoft.com/library/dd568996). Offizielle Dokumentation auf der Microsoft TechNet-Website. Der Inhalt ist nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
* [Verwenden von Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Offizielle Dokumentation auf der Microsoft IIS.NET-Website. Der Inhalt ist auch nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
* [StackOverflow](http://www.stackoverflow.com). Die beste Adresse, um aktuelle Informationen zur Verwendung von Web Deploy über die Befehlszeile zu erhalten.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilenbereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Bei MSBuild handelt es sich um das von Visual Studio verwendete Buildmodul, das auch über die Befehlszeile zum Bereitstellen von Webanwendungen in Web-Apps genutzt werden kann. Dieses Lernprogramm ist Bestandteil einer Serie, in der es in erster Linie um die Visual Studio-Bereitstellung geht.

##<a name="nextsteps"></a>Nächste Schritte

In einigen Szenarios möchten Sie ggf. in der Lage sein, bequem zwischen einer Staging- und Produktionsversion Ihrer Web-App zu wechseln. Weitere Informationen hierzu finden Sie unter [Stagingbereitstellung in Web-Apps](web-sites-staged-publishing.md).

Die Aufstellung eines Sicherungs- und Wiederherstellungsplans ist ein wesentlicher Bestandteil jedes Bereitstellungsworkflows. Weitere Informationen zu den Sicherungs- und Wiederherstellungsfunktionen für Web-Apps finden Sie unter [Sichern von Web-Apps](web-sites-backup.md).

Informationen zur Verwendung der rollenbasierten Zugriffssteuerung von Azure zum Verwalten des Zugriffs auf die Web-App-Bereitstellung finden Sie unter [RBAC and Web App Publishing](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing) (in englischer Sprache).

Informationen zu weiteren Bereitstellungsthemen finden Sie in den entsprechenden Abschnitten der [Web-App-Dokumentation](/documentation/services/web-sites/).

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->