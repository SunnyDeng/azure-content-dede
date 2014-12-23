<properties pageTitle="Bereitstellen einer Azure Website" metaKeywords="Azure bereitstellen veröffentlichen Website" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

#Bereitstellen einer Azure-Website

Für die Bereitstellung Ihrer eigenen Inhalte auf einer Azure-Website gibt es viele Möglichkeiten. Dieses Thema enthält eine kurze Übersicht über die jeweilige Option und Links, um weitere Informationen zu erhalten.

* [Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem](#cloud)
	* Visual Studio Online (VSO)
	* Repositorywebsites über Git
	* Repositorywebsites über Mercurial
	* Dropbox
* [Bereitstellen über eine IDE](#ide)
	* Visual Studio
	* WebMatrix
* [Bereitstellen über ein FTP-Dienstprogramm](#ftp)
* [Bereitstellen über ein lokales Quellcodeverwaltungssystem](#onpremises)
	* Team Foundation Server (TFS)
	* Lokale Git- oder Mercurial-Repositorys
* [Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API](#commandline)
	* MSBuild
	* FTP-Skripte
	* Windows PowerShell
	* .NET-Verwaltungs-API
	* Plattformübergreifende Befehlszeile (xpat-cli)
	* Web Deploy-Befehlszeile


##<a name="cloud"></a>Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem

Für die Bereitstellung einer Website ist es am einfachsten, einen [Workflow für die kontinuierliche Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) einzurichten, der in Ihr [Quellcodeverwaltungssystem](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) integriert ist. Durch die Automatisierung ist der Entwicklungsprozess effizienter, und darüber hinaus können auch die Prozesse für die Sicherung und Wiederherstellung besser verwaltet werden und sind zuverlässiger. 

Wenn Sie die Quellcodeverwaltung bisher noch nicht eingerichtet haben, ist es am einfachsten, wenn Sie dafür ein in der Cloud gehostetes Quellcodeverwaltungssystem verwenden.

###<a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (zuvor Team Foundation Service) ist die cloudbasierte Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Für ein Team mit bis zu 5 Entwicklern ist dieser Dienst kostenlos. Sie können VSO einrichten, um die kontinuierliche Bereitstellung auf einer Azure-Website zu gewährleisten. Für das Repository kann entweder [Git oder TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs)verwendet werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Kontinuierliche Bereitstellung auf Azure mit VSO und TFVC](http://www.visualstudio.com/en-us/learn/continuous-delivery-in-vs). In diesem Lernprogramm wird Schritt für Schritt gezeigt, wie die kontinuierliche Bereitstellung von VSO auf einer Azure-Website mithilfe von TFVC eingerichtet wird. Bei TFVC handelt es sich um die zentralisierte Quellcodeverwaltungsoption in VSO. Git ist hingegen die dezentralisierte Quellcodeverwaltungsoption.
* [Kontinuierliche Bereitstellung auf Azure mit VSO und TFVC](/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso/). Ähnlich wie bei vorherigen Lernprogrammen durchlaufen Sie auch hier die Schritte zur Registrierung eines VSO-Kontos und zum Einchecken eines Projekts in die Quellcodeverwaltung.
* [Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und Git](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso-git/). Dieses Lernprogramm ist vergleichbar mit dem vorangehenden Lernprogramm, verwendet jedoch anstelle von TFVC das Verwaltungstool Git.

###<a name="git"></a>Repositorywebsites über Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) ist ein beliebtes dezentralisiertes Quellcodeverwaltungssystem. Azure verfügt über integrierte Funktionen, mit denen die automatische Bereitstellung auf einer Azure-Website über gängige webbasierte Repositorywebsites, in denen Git-Repositorys einschließlich [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) und [BitBucket](https://bitbucket.org/) gespeichert sind, im Handumdrehen möglich ist. Ein Vorteil bei der Verwendung von Git für die Bereitstellung besteht darin, dass das Zurücksetzen auf eine frühere Bereitstellung relativ einfach ist. 

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/en-us/documentation/articles/web-sites-publish-source-control/). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Bereitstellen auf Websites mit GitHub unter Verwendung von Kudu](/en-us/documentation/videos/deploying-to-azure-from-github/). Das Video von Scott Hanselman und David Ebbo zeigt, wie Sie eine Website direkt von GitHub auf einer Azure-Website bereitstellen.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Repositorywebsites über Mercurial

Wenn Sie [Mercurial](http://mercurial.selenic.com/) als Quellcodeverwaltungssystem nutzen und das Repository in [CodePlex](http://www.codeplex.com/) oder [BitBucket](https://bitbucket.org/) speichern, können Sie die integrierten Funktionen in Azure-Websites verwenden, sodass Ihre Inhalte automatisch bereitgestellt werden.

Weitere Informationen zur Bereitstellung mithilfe von Mercurial finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/en-us/documentation/articles/web-sites-publish-source-control/). Obwohl in diesem Lernprogramm die Veröffentlichung eines Git-Repositorys gezeigt wird, ist der Vorgang bei Mercurial-Repositorys, die in CodePlex oder BitBucket gehostet werden, ähnlich.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[DropBox](https://www.dropbox.com/) stellt kein Quellcodeverwaltungssystem dar. Wenn Sie allerdings den Quellcode in DropBox speichern, können Sie die Bereitstellung über Ihr DropBox-Konto automatisieren.

* [Deploy To Windows Azure Using Dropbox (Bereitstellen in Azure mithilfe von Dropbox, in englischer Sprache)](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Hier erfahren Sie, wie das Azure-Verwaltungsportal zum Einrichten der DropBox-Bereitstellung verwendet wird.
* [DropBox und Azure-Websites](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In diesem Video wird gezeigt, wie ein DropBox-Ordner mit einer Azure-Website verknüpft wird. Darüber hinaus wird demonstriert, wie schnell Sie eine Website mit der einfachen Drag & Drop-Bereitstellung einrichten oder verwalten können.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Bereitstellen über eine IDE

Bei [Visual Studio](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) und [WebMatrix](http://www.microsoft.com/web/webmatrix/) handelt es sich um Microsoft-IDEs (Integrated Development Environments = Integrierte Entwicklungsumgebungen), die Sie für die Webentwicklung verwenden können. Beide stellen integrierte Funktionen für die einfache Bereitstellung auf Azure-Websites zur Verfügung.  [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) kann bei beiden Entwicklungsumgebungen zum Einsatz kommen, um zusätzliche Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, zu automatisieren. Bei diesen Aufgaben kann es sich um die Datenbankbereitstellung oder Änderungen hinsichtlich der Verbindungszeichenfolge handeln. Die Bereitstellung kann bei beiden Umgebungen auch mithilfe von [FTP oder FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) erfolgen. 

WebMatrix kann schnell installiert werden und ist leicht erlernbar. Visual Studio bietet allerdings viele weitere Funktionen für die Arbeit mit Azure-Websites. In der Visual Studio-IDE können Sie Azure-Websites erstellen, anhalten, starten und löschen. Darüber hinaus können Sie Protokolle anzeigen, während diese in Echtzeit erstellt werden. Neben vielen weiteren Funktionen ist auch das Remotedebuggen möglich. Visual Studio kann auch mit Quellcodeverwaltungssystemen, wie beispielsweise [Visual Studio Online](#vso), [Team Foundation Server](#tfs) und [Git-Repositorys](#git), integriert werden.

###<a name="vs"></a>Visual Studio

Weitere Informationen zur Bereitstellung von Visual Studio auf Azure-Websites finden Sie in den folgenden Ressourcen:

* [Erste Schritte mit Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/). In dieser Ressource wird das Erstellen und Bereitstellen eines einfachen ASP.NET MVC-Webprojekts mithilfe von Visual Studio und Web Deploy erläutert.
* [Bereitstellen von Azure WebJobs auf Azure-Websites](/en-us/documentation/articles/websites-dotnet-deploy-webjobs/). Hier erfahren Sie, wie Sie Konsolenanwendungsprojekte für die Bereitstellung als WebJobs konfigurieren.  
* [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](/en-us/develop/net/tutorials/web-site-with-sql-database/). In dieser Ressource wird das Erstellen und Bereitstellen eines ASP.NET MVC-Webprojekts mit einer SQL-Datenbank unter Verwendung von Visual Studio, Web Deploy und Entity Framework Code First Migrations erläutert.
* [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET](http://msdn.microsoft.com/en-us/library/dd394698.aspx). Einstieg in die Webbereitstellung mithilfe von Visual Studio. Obwohl der Inhalt nicht mehr auf dem neuesten Stand ist, sind Informationen enthalten, die noch immer relevant sind. Dazu gehört eine Übersicht über die Optionen für die Bereitstellung einer Datenbank zusammen mit der Webanwendung und eine Liste mit zusätzlichen Bereitstellungsaufgaben, die Sie möglicherweise noch erledigen müssen oder die Sie manuell konfigurieren, sodass diese Aufgaben von Visual Studio ausgeführt werden. In diesem Thema wird die Bereitstellung allgemein erläutert und nicht nur die Bereitstellung auf Azure-Websites.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction) Dabei handelt es sich um eine Lernprogrammserie mit zwölf Teilen, in der die Bereitstellungsaufgaben vollständig vorgestellt werden. 
* [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). In diesem Artikel wird erläutert, wie ein ASP.NET-Webprojekt in Visual Studio bereitgestellt wird und wie das Git-Plug-In verwendet wird, um den Code für Git zu übernehmen und Azure mit dem Git-Repository zu verknüpfen.

###<a name="webmatrix"></a>WebMatrix

Weitere Informationen zur Bereitstellung von WebMatrix auf Azure-Websites finden Sie in den folgenden Ressourcen:

* [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-using-webmatrix/). Hier erfahren Sie, wie Sie eine einfache ASP.NET-Website unter Verwendung einer WebMatrix-Vorlage erstellen und mithilfe von WebMatrix und Web Deploy auf einer Azure-Website bereitstellen.
* [Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Integriertes Git und Bereitstellung für Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). In diesem Artikel wird das Verwenden von WebMatrix zum Bereitstellen über ein Git-Repository für die Quellcodeverwaltung beschrieben.

##<a name="ftp"></a>Bereitstellen über ein FTP-Dienstprogramm

Unabhängig davon, welche IDE Sie verwenden, können Sie Inhalte auf Ihrer Website bereitstellen, indem Sie zum Kopieren von Dateien [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) verwenden. Sie können problemlos FTP-Anmeldeinformationen für eine Azure-Website erstellen und sie in einer beliebigen Anwendung verwenden, die FTP unterstützt, z. B. Internet Explorer und kostenlose Hilfsprogramme mit vollem Funktionsumfang wie [FileZilla](https://filezilla-project.org/).  Das sicherheitsoptimierte FTPS-Protokoll wird ebenfalls von Azure-Websites unterstützt. 

Obwohl es ziemlich einfach ist, die Dateien Ihrer Website mithilfe von FTP-Hilfsprogrammen in Azure zu kopieren, berücksichtigen oder koordinieren diese Programme nicht automatisch die dazugehörigen Bereitstellungsaufgaben, wie beispielsweise das Bereitstellen einer Datenbank oder Ändern der Verbindungszeichenfolgen. Bei vielen FTP-Tools werden die Quell- und die Zieldateien nicht verglichen, sodass das Kopieren von unveränderten Dateien übersprungen werden könnte. Wenn bei umfangreichen Websites stets alle Dateien kopiert werden, kann es selbst bei kleineren Updates zu langen Wartezeiten hinsichtlich der Bereitstellung kommen, da immer alle Dateien kopiert werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP](/en-us/documentation/articles/web-sites-php-mysql-deploy-use-ftp/) 
* [Verwalten von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-manage#ftp-credentials). Diese Ressource verfügt über zusätzliche Informationen zum Festlegen von FTP-Anmeldeinformationen, die nicht im PHP-Lernprogramm enthalten sind. 


##<a name="onpremises"></a>Bereitstellen über ein lokales Quellcodeverwaltungssystem

Wenn Sie TFS, Git oder Mercurial in einem lokalen (nicht in der Cloud gehosteten) Repository verwenden, kann die Bereitstellung direkt vom Repository auf Azure-Websites erfolgen.

###<a name="tfs"></a>Team Foundation Server (TFS)

Bei Team Foundation Server handelt es sich um die lokale Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Sie können TFS für die kontinuierliche Bereitstellung auf einer Azure-Website einrichten.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Kontinuierliche Bereitstellung für Cloud Services in Azure](/en-us/develop/net/common-tasks/continuous-delivery/). Dieses Dokument ist in erster Linie für einen Azure-Cloud-Dienst bestimmt. Einige Inhalte gelten allerdings auch für Websites.

###<a name="gitmercurial"></a>Lokale Git- oder Mercurial-Repositorys

Sie können in Azure die URL eines beliebigen Repositorys eingeben, das Git oder Mercurial nutzt, um die Bereitstellung von diesem Standort aus durchzuführen. Außerdem werden direkte Push-Übertragungen aus einem lokalen Git-Repository an eine Azure-Website unterstützt. 

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/en-us/documentation/articles/web-sites-publish-source-control/). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites über git/hg-Repositorys, in englischer Sprache)](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog von David Ebbo, in dem das Feature "Externes Repository" in Azure-Websites erklärt wird.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Blogeintrag von Scott Hanselman.











##<a name="commandline"></a> Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API

Die Automatisierung Ihres Workflows für die Entwicklung ist immer die beste Wahl. Wenn Sie diese Aufgabe aber nicht direkt in Ihrem Quellcodeverwaltungssystem ausführen können, können Sie die Automatisierung mithilfe von Befehlszeilentools manuell einrichten. Im Allgemeinen sind dafür mehrere Tools oder Frameworks erforderlich, da bei der Bereitstellung oftmals Website-Verwaltungsfunktionen ausgeführt werden und Inhalte kopiert werden.

Mithilfe von Azure werden die für die Bereitstellung durchzuführenden Website-Verwaltungsaufgaben vereinfacht. Eine REST-Verwaltungs-API und mehrere Frameworks stehen zur Verfügung, sodass das Arbeiten mit der API einfacher ist.

###<a name=msbuild></a>MSBuild

If you use the [Visual Studio IDE](#vs) for development, you can use [MSBuild](http://msbuildbook.com/) to automate anything you can do in your IDE. You can configure MSBuild to use either [Web Deploy](#webdeploy) or [FTP/FTPS](#ftp) to copy files. Web Deploy can also automate many other deployment-related tasks, such as deploying databases.

For more information about command-line deployment using MSBuild, see the following resources:

* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Tenth in a series of tutorials about deployment to Azure using Visual Studio. Shows how to use the command line to deploy after setting up publish profiles in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Hard-copy book that includes chapters on how to use MSBuild for deployment.

###<a name="ftp2"></a>FTP-Skripte

Sie können auf einfache Weise [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)-Anmeldeinformationen für eine Azure-Website erstellen. Die Anmeldeinformationen können anschließend auch mit FTP-Batchskripts verwendet werden.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Verwenden von FTP-Stapelskripts](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

Sie können MSBuild- oder FTP-Bereitstellungsfunktionen mithilfe von [Windows PowerShell](http://msdn.microsoft.com/en-us/library/dd835506.aspx) ausführen. Wenn dies der Fall ist, können Sie auch eine Sammlung von Windows PowerShell-Cmdlets nutzen, mit denen die Azure-REST-Verwaltungs-API leicht aufgerufen werden kann.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure - Automatisierung, in englischer Sprache)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). In diesem E-Book-Kapitel wird erklärt, wie die im E-Book dargestellte Beispielanwendung Windows PowerShell-Skripte nutzt, um eine Azure-Testumgebung zu erstellen und wie die Bereitstellung für diese Umgebung erfolgt. Im Abschnitt [Ressourcen](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) finden Sie Links für die weiterführende Azure PowerShell-Dokumentation.

###<a name="api"></a>.NET-Verwaltungs-API

Sie können C#-Code schreiben, um die MSBuild- oder FTP-Funktionen für die Bereitstellung auszuführen. Wenn Sie dies tun, können Sie auf die Azure-REST-Verwaltungs-API zugreifen, um die Website-Verwaltungsfunktionen auszuführen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Einführung in die .NET-Verwaltungs-API und Links für die weiterführende Dokumentation.

###<a name="cli"></a>Plattformübergreifende Befehlszeile (xpat-cli)

Sie können die auf Mac- oder Linux-Computern vorhandene Befehlszeile verwenden, um die Bereitstellung über FTP durchzuführen. Wenn Sie dies tun, können Sie auch auf die Azure-REST-Verwaltungs-API zugreifen, indem Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (xpat-cli) verwenden. Die plattformübergreifende Befehlszeilenschnittstelle kann auch auf Windows-Computern zum Einsatz kommen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Befehlszeilentools](/en-us/downloads/#cmd-line-tools). Portal page in WindowsAzure.com for command line tool information.

###<a name="webdeploy"></a>Web Deploy command line

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) is Microsoft software for deployment to IIS that not only provides intelligent file sync features but also can perform or coordinate many other deployment-related tasks that can't be automated when you use FTP. For example, Web Deploy can deploy a new database or database updates along with your website. Web Deploy can also minimize the time required to update an existing site since it can intelligently copy only changed files. Microsoft WebMatrix, Visual Studio, Visual Studio Online, and Team Foundation Server have support for Web Deploy built-in, but you can also use Web Deploy directly from the command line to automate deployment. Web Deploy commands are very powerful but the learning curve can be steep.

For more information, see the following resource:

* [Web Deployment Tool](http://technet.microsoft.com/en-us/library/dd568996). Official documentation on the Microsoft TechNet site. Dated but still a good place to start.
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Official documentation on the Microsoft IIS.NET site. Also dated but a good place to start.
* [StackOverflow](http://www.stackoverflow.com). The best place to go for more current information about how to use Web Deploy from the command line.
* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild is the build engine used by Visual Studio, and it can also be used from the command line to deploy web applications to Azure Websites. This tutorial is part of a series that is mainly about Visual Studio deployment.

##<a name="nextsteps"></a>Next Steps

In some scenarios you might want to be able to easily switch back and forth between a staging and a production version of your website. You can do this with the Azure Websites staged deployment feature. For more information, see [Staged Deployment on Microsoft Azure Web Sites](/en-us/documentation/articles/web-sites-staged-publishing/).

Die Aufstellung eines Sicherungs- und Wiederherstellungsplans ist ein wesentlicher Bestandteil jedes Bereitstellungsworkflows. Weitere Informationen zu den Sicherungs- und Wiederherstellungsfunktionen für Azure-Websites finden Sie unter [Sicherungen von Azure-Websites](/en-us/documentation/articles/web-sites-backup/).  

Weitere Informationen zu weiteren Bereitstellungsthemen finden Sie in den entsprechenden Abschnitten der [Dokumentation für Azure-Websites](/en-us/documentation/services/web-sites/).
