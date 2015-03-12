<properties 
	pageTitle="Bereitstellen einer Azure-Website" 
	description="Erfahren Sie, welche Methoden zum Bereitstellen von Inhalten auf einer Azure-Website zur Verfügung stehen." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="tdykstra"/>

#Bereitstellen einer Azure-Website

Für die Bereitstellung Ihrer eigenen Inhalte auf einer Azure-Website gibt es viele Möglichkeiten. Dieses Thema enthält eine kurze Übersicht über die jeweilige Option und Links, um weitere Informationen zu erhalten.

* [Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem](#cloud)
	* Visual Studio Online
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

###<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (früher Team Foundation Service) ist die cloudbasierte Microsoft-Lösung für Quellcodeverwaltung und Teamzusammenarbeit. Für ein Team mit bis zu 5 Entwicklern ist dieser Dienst kostenlos. Sie können eine kontinuierliche Bereitstellung auf einer Azure-Website gewährleisten und für das Repository entweder [Git oder TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) verwenden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Kontinuierliche Bereitstellung in Azure mithilfe von Visual Studio Online und TFVC](../cloud-services-continuous-delivery-use-vso/). Lernprogramm mit Schrittanleitungen, in dem gezeigt wird, wie Sie die kontinuierliche Bereitstellung auf einer Azure-Website mit Visual Studio Online und TFVC einrichten. Bei TFVC handelt es sich um die zentralisierte Quellcodeverwaltungsoption. Git ist hingegen die dezentralisierte Quellcodeverwaltungsoption.
* [Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und Git](../cloud-services-continuous-delivery-use-vso-git/). Dieses Lernprogramm ist vergleichbar mit dem vorangehenden Lernprogramm, verwendet jedoch anstelle von TFVC das Verwaltungstool Git.

###<a name="git"></a>Repositorywebsites mit Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) ist ein beliebtes dezentralisiertes Quellcodeverwaltungssystem. Azure verfügt über integrierte Funktionen, mit denen Sie aus gängigen webbasierten Repositorywebsites mit Git-Repositorys, wie beispielsweise [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) und [BitBucket](https://bitbucket.org/), auf einfache Weise die Bereitstellung auf einer Azure-Website automatisieren können. Ein Vorteil bei der Verwendung von Git für die Bereitstellung besteht darin, dass das Zurücksetzen auf eine frühere Bereitstellung relativ einfach ist. 

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites mit Git](../web-sites-publish-source-control/). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Bereitstellen auf Websites mit GitHub unter Verwendung von Kudu](/de-de/documentation/videos/deploying-to-azure-from-github/). Das Video von Scott Hanselman und David Ebbo zeigt, wie Sie eine Website direkt von GitHub auf einer Azure-Website bereitstellen.
* [Schaltfläche zur Azure-Bereitstellung für Azure-Websites](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog über eine Methode zum Auslösen der Bereitstellung aus einem Git-Repository.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Repositorywebsites mit Mercurial

Wenn Sie [Mercurial](http://mercurial.selenic.com/) als Quellcodeverwaltungssystem nutzen und das Repository in [CodePlex](http://www.codeplex.com/) oder [BitBucket](https://bitbucket.org/) speichern, können Sie die integrierten Funktionen in Azure-Websites verwenden, sodass Ihre Inhalte automatisch bereitgestellt werden.

Weitere Informationen zur Bereitstellung mithilfe von Mercurial finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites mit Git](../web-sites-publish-source-control/). Obwohl in diesem Lernprogramm die Veröffentlichung eines Git-Repositorys gezeigt wird, ist der Vorgang bei Mercurial-Repositorys, die in CodePlex oder BitBucket gehostet werden, ähnlich.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) stellt kein Quellcodeverwaltungssystem dar. Wenn Sie allerdings den Quellcode in DropBox speichern, können Sie die Bereitstellung über Ihr DropBox-Konto automatisieren.

* [Bereitstellen auf Azure-Websites aus Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Hier erfahren Sie, wie das Azure-Verwaltungsportal zum Einrichten der DropBox-Bereitstellung verwendet wird.
* [Dropbox-Bereitstellung auf Azure-Websites](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In diesem Video wird gezeigt, wie ein DropBox-Ordner mit einer Azure-Website verknüpft wird. Darüber hinaus wird demonstriert, wie schnell Sie eine Website mit der einfachen Drag & Drop-Bereitstellung einrichten oder verwalten können.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Bereitstellen über eine IDE

[Visual Studio](http://www.visualstudio.com/) und [WebMatrix](http://www.microsoft.com/web/webmatrix/) sind Microsoft-IDEs (Integrated Development Environments, integrierte Entwicklungsumgebungen), die Sie für die Webentwicklung verwenden können. Beide stellen integrierte Funktionen für die einfache Bereitstellung auf Azure-Websites zur Verfügung.  [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) kann in beiden Entwicklungsumgebungen für zusätzliche Aufgaben in Bezug auf die Bereitstellung verwendet werden, beispielsweise für die Datenbankbereitstellung oder die Änderung von Verbindungszeichenfolgen. Beide ermöglichen eine Bereitstellung über [FTP oder FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)). 

WebMatrix kann schnell installiert werden und ist leicht erlernbar. Visual Studio bietet allerdings viele weitere Funktionen für die Arbeit mit Azure-Websites. In der Visual Studio-IDE können Sie Azure-Websites erstellen, anhalten, starten und löschen. Darüber hinaus können Sie Protokolle anzeigen, während diese in Echtzeit erstellt werden. Neben vielen weiteren Funktionen ist auch das Remotedebuggen möglich. Visual Studio bietet die Möglichkeit zur Integration in Quellcodeverwaltungssysteme wie [Visual Studio Online](#vso),[Team Foundation Server](#tfs) und [Git-Repositorys](#git).

###<a name="vs"></a>Visual Studio

Weitere Informationen zur Bereitstellung von Visual Studio auf Azure-Websites finden Sie in den folgenden Ressourcen:

* [Erste Schritte mit Azure und ASP.NET](../web-sites-dotnet-get-started/). In dieser Ressource wird das Erstellen und Bereitstellen eines einfachen ASP.NET MVC-Webprojekts mithilfe von Visual Studio und Web Deploy erläutert.
* [Bereitstellen von Azure-Webaufträgen auf Azure-Websites](../websites-dotnet-deploy-webjobs/). Hier erfahren Sie, wie Sie Konsolenanwendungsprojekte für die Bereitstellung als WebJobs konfigurieren.  
* [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank auf einer Azure-Website](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). In dieser Ressource wird das Erstellen und Bereitstellen eines ASP.NET MVC-Webprojekts mit einer SQL-Datenbank unter Verwendung von Visual Studio, Web Deploy und Entity Framework Code First-Migrationen erläutert.
* [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Einstieg in die Webbereitstellung mithilfe von Visual Studio. Obwohl der Inhalt nicht mehr auf dem neuesten Stand ist, sind Informationen enthalten, die noch immer relevant sind. Dazu gehört eine Übersicht über die Optionen für die Bereitstellung einer Datenbank zusammen mit der Webanwendung und eine Liste mit zusätzlichen Bereitstellungsaufgaben, die Sie möglicherweise noch erledigen müssen oder die Sie manuell konfigurieren, sodass diese Aufgaben von Visual Studio ausgeführt werden. In diesem Thema wird die Bereitstellung allgemein erläutert und nicht nur die Bereitstellung auf Azure-Websites.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Dabei handelt es sich um eine Lernprogrammserie mit zwölf Teilen, in der die Bereitstellungsaufgaben vollständig vorgestellt werden. Seit dem Erscheinen des Lernprogramms sind einige Azure-Bereitstellungsfunktionen hinzugefügt worden. Hinweise erläutern, welche Informationen fehlen. 
* [Direktes Bereitstellen einer ASP.NET-Website auf  Azure in Visual Studio 2012 über ein Git-Repository](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). In diesem Artikel wird erläutert, wie ein ASP.NET-Webprojekt in Visual Studio bereitgestellt wird und wie das Git-Plug-In verwendet wird, um den Code für Git zu übernehmen und Azure mit dem Git-Repository zu verknüpfen. Visual Studio 2013 und höhere Versionen bieten eine integrierte Git-Unterstützung, deshalb ist die Installation eines Plug-Ins nicht erforderlich.

###<a name="webmatrix"></a>WebMatrix

Weitere Informationen zur Bereitstellung von WebMatrix auf Azure-Websites finden Sie in den folgenden Ressourcen:

* [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-using-webmatrix/). Hier erfahren Sie, wie Sie eine einfache ASP.NET-Website unter Verwendung einer WebMatrix-Vorlage erstellen und mithilfe von WebMatrix und Web Deploy auf einer Azure-Website bereitstellen.
* [Erstellen einer Node.js-Website und Bereitstellen auf Azure mit WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website mithilfe von WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Integriertes Git und Bereitstellung auf Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). In diesem Artikel wird das Verwenden von WebMatrix zum Bereitstellen über ein Git-Repository für die Quellcodeverwaltung beschrieben.

##<a name="ftp"></a>Bereitstellen über ein FTP-Dienstprogramm

Unabhängig davon, welche IDE Sie verwenden, können Sie Inhalte auf Ihrer Website bereitstellen, indem Sie zum Kopieren von Dateien [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) verwenden. Sie können problemlos FTP-Anmeldeinformationen für eine Azure-Website erstellen und sie in einer beliebigen Anwendung verwenden, die FTP unterstützt, z. B. Internet Explorer und kostenlose Hilfsprogramme mit vollem Funktionsumfang wie [FileZilla](https://filezilla-project.org/).  Das sicherheitsoptimierte FTPS-Protokoll wird ebenfalls von Azure-Websites unterstützt. 

Obwohl es ziemlich einfach ist, die Dateien Ihrer Website mithilfe von FTP-Hilfsprogrammen in Azure zu kopieren, berücksichtigen oder koordinieren diese Programme nicht automatisch die dazugehörigen Bereitstellungsaufgaben, wie beispielsweise das Bereitstellen einer Datenbank oder Ändern der Verbindungszeichenfolgen. Bei vielen FTP-Tools werden die Quell- und die Zieldateien nicht verglichen, sodass das Kopieren von unveränderten Dateien übersprungen werden könnte. Wenn bei umfangreichen Websites stets alle Dateien kopiert werden, kann es selbst bei kleineren Updates zu langen Wartezeiten hinsichtlich der Bereitstellung kommen, da immer alle Dateien kopiert werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP](../web-sites-php-mysql-deploy-use-ftp/).
* [Verwalten von Websites](../web-sites-manage/#ftp-credentials). Diese Ressource verfügt über zusätzliche Informationen zum Festlegen von FTP-Anmeldeinformationen, die nicht im PHP-Lernprogramm enthalten sind. 


##<a name="onpremises"></a>Bereitstellen über ein lokales Quellcodeverwaltungssystem

Wenn Sie TFS, Git oder Mercurial in einem lokalen (nicht in der Cloud gehosteten) Repository verwenden, kann die Bereitstellung direkt vom Repository auf Azure-Websites erfolgen.

###<a name="tfs"></a>Team Foundation Server (TFS)

Bei Team Foundation Server handelt es sich um die lokale Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Sie können TFS für die kontinuierliche Bereitstellung auf einer Azure-Website einrichten.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Kontinuierliche Bereitstellung für Clouddienste in Azure](../cloud-services-dotnet-continuous-delivery/). Dieses Dokument ist in erster Linie für einen Azure-Cloud-Dienst bestimmt. Einige Inhalte gelten allerdings auch für Websites.

###<a name="gitmercurial"></a>Lokale Git- oder Mercurial-Repositorys

Sie können in Azure die URL eines beliebigen Repositorys eingeben, das Git oder Mercurial nutzt, um die Bereitstellung von diesem Standort aus durchzuführen. Außerdem werden direkte Push-Übertragungen aus einem lokalen Git-Repository an eine Azure-Website unterstützt. 

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites mit Git](../web-sites-publish-source-control/). Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
* [Publishing to Azure Web Sites from any git/hg repo](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html) (Veröffentlichen auf Azure-Websites aus beliebigen git/hg-Repositorys, in englischer Sprache). Blog, der die Funktion "Externes Repository" in Azure-Websites erläutert.
* [Azure-Forum für Git, Mercurial und Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Deploying TWO websites to Azure from one Git Repository](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx) (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache). Blogeintrag von Scott Hanselman.











##<a name="commandline"></a> Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API

Die Automatisierung Ihres Workflows für die Entwicklung ist immer die beste Wahl. Wenn Sie diese Aufgabe aber nicht direkt in Ihrem Quellcodeverwaltungssystem ausführen können, können Sie die Automatisierung mithilfe von Befehlszeilentools manuell einrichten. Im Allgemeinen sind dafür mehrere Tools oder Frameworks erforderlich, da bei der Bereitstellung oftmals Website-Verwaltungsfunktionen ausgeführt werden und Inhalte kopiert werden.

Mithilfe von Azure werden die für die Bereitstellung durchzuführenden Website-Verwaltungsaufgaben vereinfacht. Eine REST-Verwaltungs-API und mehrere Frameworks stehen zur Verfügung, sodass das Arbeiten mit der API einfacher ist.

###<a name=msbuild></a>MSBuild

Wenn Sie die [Visual Studio IDE](#vs) für die Entwicklung verwenden, können Sie [MSBuild](http://msbuildbook.com/) zur Automatisierung sämtlicher Aufgaben nutzen, die Sie in Ihrer IDE ausführen können. Sie können MSBuild so konfigurieren, dass zum Kopieren der Dateien entweder [Web Deploy](#webdeploy) oder [FTP/FTPS](#ftp) verwendet wird. Mit Web Deploy können auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, automatisiert werden, wie beispielsweise das Bereitstellen von Datenbanken.

Weitere Informationen zur Befehlszeilen-Bereitstellung mithilfe von MSBuild finden Sie in den folgenden Ressourcen:

* [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilenbereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Der zehnte Teil in einer Lernprogrammserie, in dem die Bereitstellung in Azure mithilfe von Visual Studio erläutert wird. In diesem Lernprogramm wird gezeigt, wie die Befehlszeile für die Bereitstellung verwendet wird, nachdem Veröffentlichungsprofile in Visual Studio eingerichtet wurden.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Buch, in dem Kapitel zur Verwendung von MSBuild für die Bereitstellung enthalten sind.

###<a name="ftp2"></a>FTP-Skripts

Sie können auf einfache Weise [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) -Anmeldeinformationen für eine Azure-Website erstellen. Die Anmeldeinformationen können anschließend auch mit FTP-Batchskripts verwendet werden.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Verwenden von FTP-Batchskripts](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

Sie können MSBuild- oder FTP-Bereitstellungsfunktionen mithilfe von [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) ausführen. Wenn dies der Fall ist, können Sie auch eine Sammlung von Windows PowerShell-Cmdlets nutzen, mit denen die Azure-REST-Verwaltungs-API leicht aufgerufen werden kann.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Building Real-World Cloud Apps with Azure - Automate Everything](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) (Erstellen von Cloud-Anwendungen mit Azure - Automatisierung, in englischer Sprache). In diesem E-Book-Kapitel wird erklärt, wie die im E-Book dargestellte Beispielanwendung Windows PowerShell-Skripte nutzt, um eine Azure-Testumgebung zu erstellen und wie die Bereitstellung für diese Umgebung erfolgt. Im Abschnitt [Ressourcen](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) finden Sie Links für die weiterführende Azure PowerShell-Dokumentation.
* [Veröffentlichung in Entwicklungs- und Testumgebungen mithilfe von Windows PowerShell-Skripts](http://msdn.microsoft.com/library/dn642480.aspx). Hier erfahren Sie, wie Sie Windows PowerShell-Bereitstellungsskripts verwenden, die von Visual Studio generiert werden.

###<a name="api"></a>.NET-Verwaltungs-API

Sie können C#-Code schreiben, um die MSBuild- oder FTP-Funktionen für die Bereitstellung auszuführen. Wenn Sie dies tun, können Sie auf die Azure-REST-Verwaltungs-API zugreifen, um die Website-Verwaltungsfunktionen auszuführen.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Automating everything with the Azure Management Libraries and .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx) (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache). Einführung in die .NET-Verwaltungs-API und Links für die weiterführende Dokumentation.

###<a name="cli"></a>Plattformübergreifende Befehlszeile (xplat-cli)

Sie können die auf Mac- oder Linux-Computern vorhandene Befehlszeile verwenden, um die Bereitstellung über FTP durchzuführen. Wenn Sie dies tun, können Sie auch auf die Azure-REST-Verwaltungs-API zugreifen, indem Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (xpat-cli) verwenden. Die plattformübergreifende Befehlszeilenschnittstelle kann auch auf Windows-Computern genutzt werden.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Befehlszeilentools](/de-de/downloads/#cmd-line-tools). Portalseite auf WindowsAzure.com mit Informationen zu Befehlszeilentools.

###<a name="webdeploy"></a>Web Deploy-Befehlszeile

Bei [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) handelt es sich um Microsoft-Software für die Bereitstellung in IIS, wobei neben den Funktionen für die intelligente Dateisynchronisierung auch viele weitere Bereitstellungsaufgaben, die über FTP nicht automatisierbar sind, ausgeführt oder koordiniert werden können. Mithilfe von Web Deploy können beispielsweise neue Datenbanken oder Datenbankupdates mit Ihrer Website bereitgestellt werden. Auch die Zeit, die zum Aktualisieren einer vorhandenen Website erforderlich ist, kann mit Web Deploy verringert werden, da dieses Programm auf intelligente Weise lediglich geänderte Dateien kopiert. Microsoft WebMatrix, Visual Studio, Visual Studio Online und Team Foundation Server verfügen über die integrierte Unterstützung für Web Deploy. Sie können Web Deploy aber auch direkt über die Befehlszeile verwenden, um die Bereitstellung zu automatisieren. Die Web Deploy-Befehle sind äußerst leistungsstark, stellen aber einen gewaltigen Lernprozess dar.

Weitere Informationen finden Sie in der folgenden Ressource:

* [Simple Azure Websites: Deployment](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/) (Einfache Bereitstellung von Azure-Websites, in englischer Sprache). Blog von David Ebbo über ein von ihm geschriebenes Tool zur einfacheren Verwendung von Web Deploy.
* [Webbereitstellungstool](http://technet.microsoft.com/library/dd568996). Offizielle Dokumentation auf der Microsoft TechNet-Website. Der Inhalt ist nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
* [Verwenden von Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Offizielle Dokumentation auf der Microsoft IIS.NET-Website. Der Inhalt ist auch nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
* [StackOverflow](http://www.stackoverflow.com). Die beste Adresse, um aktuelle Informationen zur Verwendung von Web Deploy über die Befehlszeile zu erhalten.
* [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilenbereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Bei MSBuild handelt es sich um das von Visual Studio verwendete Buildmodul, das auch über die Befehlszeile zum Bereitstellen von Webanwendungen auf Azure-Websites genutzt werden kann. Dieses Lernprogramm ist Bestandteil einer Serie, in der es in erster Linie um die Visual Studio-Bereitstellung geht.

##<a name="nextsteps"></a>Nächste Schritte

In einigen Szenarien möchten Sie ggf. in der Lage sein, bequem zwischen einer Staging- und Produktionsversion Ihrer Website zu wechseln. Dazu steht Ihnen die Funktion für die Stagingbereitstellung von Azure-Websites zur Verfügung. Weitere Informationen finden Sie unter [Stagingbereitstellung auf Microsoft Azure-Websites](../web-sites-staged-publishing/).

Die Aufstellung eines Sicherungs- und Wiederherstellungsplans ist ein wesentlicher Bestandteil jedes Bereitstellungsworkflows. Weitere Informationen zu den Sicherungs- und Wiederherstellungsfunktionen für Azure-Websites finden Sie unter [Sichern von Azure-Websites](../web-sites-backup/).  

Informationen zur Verwendung der rollenbasierten Zugriffssteuerung von Azure zum Verwalten des Zugriffs auf die Websitebereitstellung finden Sie unter [RBAC und Azure-Websitebereitstellung](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Informationen zu weiteren Bereitstellungsthemen finden Sie in den entsprechenden Abschnitten der [Dokumentation der Azure-Websites](/de-de/documentation/services/web-sites/).




<!--HONumber=42-->
