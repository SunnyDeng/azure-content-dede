<properties linkid="manage-services-how-to-deploy-websites" urlDisplayName="How to deploy an Azure Web Site" pageTitle="How to deploy an Azure Web Site" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Web Site." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Web Site" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Bereitstellen einer Azure-Website
=================================

Für die Bereitstellung Ihrer eigenen Inhalte auf einer Azure-Website gibt es zahlreiche Optionen. Dieses Thema enthält eine kurze Übersicht über die jeweilige Option und Links, um weitere Informationen zu erhalten.

-   [Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem](#cloud)
    -   Visual Studio Online (VSO)
    -   Repository-Websites unter Verwendung von Git
    -   Repository-Websites unter Verwendung von Mercurial
    -   Dropbox
-   [Bereitstellen über eine IDE](#ide)
    -   Visual Studio
    -   WebMatrix
-   [Bereitstellen über ein FTP-Dienstprogramm](#ftp)
-   [Bereitstellen über ein lokales Quellcodeverwaltungssystem](#onpremises)
    -   Team Foundation Server (TFS)
    -   Lokale Git- oder Mercurial-Repositorys
-   [Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API](#commandline)
    -   MSBuild
    -   FTP-Skripte
    -   Windows PowerShell
    -   .NET-Verwaltungs-API
    -   Plattformübergreifende Befehlszeile (xpat-cli)
    -   Web Deploy-Befehlszeile

Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem
-------------------------------------------------------------------------

Für die Bereitstellung einer Website ist es am einfachsten, einen [Workflow für die kontinuierliche Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) einzurichten, der in Ihrem [Quellcodeverwaltungssystem](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) integriert ist. Durch die Automatisierung ist der Entwicklungsprozess effizienter, und darüber hinaus können auch die Prozesse für die Sicherung und Wiederherstellung besser verwaltet werden und sind zuverlässiger.

Wenn Sie die Quellcodeverwaltung bisher noch nicht eingerichtet haben, ist es am einfachsten, wenn Sie dafür ein in der Cloud gehostetes Quellcodeverwaltungssystem verwenden.

### Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (zuvor Team Foundation Service) ist die cloudbasierte Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Für ein Team mit bis zu 5 Entwicklern ist dieser Dienst kostenlos. Sie können VSO einrichten, um die kontinuierliche Bereitstellung auf einer Azure-Website zu gewährleisten. Für das Repository kann entweder [Git oder TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) verwendet werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Kontinuierliche Bereitstellung](http://www.visualstudio.com/en-us/learn/continuous-delivery-in-vs). In diesem Lernprogramm wird Schritt für Schritt gezeigt, wie die kontinuierliche Bereitstellung von VSO an eine Azure-Website mithilfe von TFVC eingerichtet wird. Bei TFVC handelt es sich um die zentralisierte Quellcodeverwaltungsoption in VSO. Git ist hingegen die dezentralisierte Quellcodeverwaltungsoption.
-   [Kontinuierliche Bereitstellung für Azure mithilfe von Visual Studio Online](/en-us/documentation/articles/cloud-services-continuous-delivery-use-vso/). In diesem Lernprogramm wird gezeigt, wie Sie sich bei VSO anmelden, wie Sie ein Projekt in der Quellecodeverwaltung einchecken und wie Sie diese für die kontinuierliche Bereitstellung (automatische Bereitstellung) in Azure einrichten. Der Inhalt bezieht sich in erster Linie auf Clouddienste. Zahlreiche Schritte zum Einrichten einer Bereitstellung in Visual Studio Online gelten allerdings auch für Websites.

### Repository-Websites unter Verwendung von Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) ist ein beliebtes dezentralisiertes Quellcodeverwaltungssystem. Azure verfügt über integrierte Funktionen, mit denen die Automatisierung der Bereitstellung auf einer Azure-Website über beliebte webbasierte Repository-Websites, in denen Git-Repositorys, einschließlich [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) und [BitBucket](https://bitbucket.org/) gespeichert sind, kinderleicht ist. Ein Vorteil bei der Verwendung von Git für die Bereitstellung besteht darin, dass das Zurücksetzen auf eine frühere Bereitstellung relativ einfach ist.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/en-us/documentation/articles/web-sites-publish-source-control/). In dieser Ressource wird gezeigt, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Methode der Veröffentlichung "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
-   [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)

### Repository-Websites unter Verwendung von Mercurial

Wenn Sie [Mercurial](http://mercurial.selenic.com/) als Quellcodeverwaltungssystem nutzen und das Repository in [CodePlex](http://www.codeplex.com/) oder [BitBucket](https://bitbucket.org/) speichern, können Sie die integrierten Funktionen in Azure-Websites verwenden, sodass Ihre Inhalte automatisch bereitgestellt werden.

Weitere Informationen zur Bereitstellung mithilfe von Mercurial finden Sie in den folgenden Ressourcen:

-   [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/en-us/documentation/articles/web-sites-publish-source-control/). Obwohl in diesem Lernprogramm die Veröffentlichung eines Git-Repositorys gezeigt wird, ist der Vorgang bei Mercurial-Repositorys, die in CodePlex oder BitBucket gehostet werden, ähnlich.
-   [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)

### DropBox

[DropBox](https://www.dropbox.com/) stellt kein Quellcodeverwaltungssystem dar. Wenn Sie allerdings den Quellcode in DropBox speichern, können Sie die Bereitstellung über Ihr DropBox-Konto automatisieren.

-   [Deploy To Windows Azure Using Dropbox (Bereitstellen in Azure mithilfe von Dropbox, in englischer Sprache)](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). In diesem Artikel wird erläutert, wie das Azure-Verwaltungsportal zum Einrichten der DropBox-Bereitstellung verwendet wird.
-   [DropBox and Azure Web Sites (DropBox und Azure-Websites, in englischer Sprache)](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In diesem Video wird gezeigt, wie ein DropBox-Ordner mit einer Azure-Website verknüpft wird. Darüber hinaus wird erläutert, wie schnell Sie eine Website mit der einfachen Drag & Drop-Bereitstellung ausführen oder verwalten können.
-   [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)

Bereitstellen über eine IDE
---------------------------

Bei [Visual Studio](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) und [WebMatrix](http://www.microsoft.com/web/webmatrix/) handelt es sich um Microsoft-IDEs (Integrated Development Environments = Integrierte Entwicklungsumgebungen), die Sie für die Webentwicklung verwenden können. Bei beiden Entwicklungsumgebungen stehen integrierte Funktionen zur Verfügung, mit denen die Bereitstellung auf Azure-Websites kinderleicht ist. [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) kann bei beiden Entwicklungsumgebungen zum Einsatz kommen, um zusätzliche Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, zu automatisieren. Bei diesen Aufgaben kann es sich um die Datenbankbereitstellung oder Änderungen hinsichtlich der Verbindungszeichenfolge handeln. Die Bereitstellung kann bei beiden Umgebungen auch mithilfe von [FTP oder FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) erfolgen.

WebMatrix kann schnell installiert werden und ist leicht erlernbar. Visual Studio bietet allerdings viele weitere Funktionen für die Arbeit mit Azure-Websites. In der Visual Studio-IDE können Sie Azure-Websites erstellen, anhalten, starten und löschen. Darüber hinaus können Sie Protokolle anzeigen, da diese in Echtzeit erstellt werden. Das Remotedebugging ist neben vielen weiteren Funktionen auch möglich. Visual Studio kann auch mit Quellcodeverwaltungssystemen, wie beispielsweise [Visual Studio Online](#vso), [Team Foundation Server](#tfs) und [Git-Repositorys](#git), integriert werden.

### Visual Studio

Weitere Informationen zum Bereitstellen auf Azure-Websites aus Visual Studio finden Sie in den folgenden Ressourcen:

-   [Erste Schritte mit Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/). In dieser Ressource wird das Erstellen und Bereitstellen eines einfachen ASP.NET MVC-Webprojekts mithilfe von Visual Studio und Web Deploy erläutert.
-   [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](/en-us/develop/net/tutorials/web-site-with-sql-database/). In dieser Ressource wird das Erstellen und Bereitstellen eines ASP.NET MVC-Webprojekts mit einer SQL-Datenbank unter Verwendung von Visual Studio, Web Deploy und Entity Framework Code First-Migrationen erläutert.
-   [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET](http://msdn.microsoft.com/en-us/library/dd394698.aspx). Einstieg in die Webbereitstellung mithilfe von Visual Studio. Obwohl der Inhalt nicht mehr auf dem neuesten Stand ist, sind Informationen enthalten, die noch immer relevant sind. Dazu gehört eine Übersicht über die Optionen für die Bereitstellung einer Datenbank zusammen mit der Webanwendung und eine Liste mit zusätzlichen Bereitstellungsaufgaben, die Sie möglicherweise noch erledigen müssen oder die Sie manuell konfigurieren, sodass diese Aufgaben von Visual Studio ausgeführt werden. Dieses Thema handelt nicht nur von der Bereitstellung für Azure-Websites, sondern von der Bereitstellung im Allgemeinen.
-   [ASP.NET-Webbereitstellung mithilfe von Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction) Dabei handelt es sich um eine Lernprogrammserie mit zwölf Teilen, in der die Bereitstellungsaufgaben vollständig vorgestellt werden.
-   [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). In diesem Artikel wird erläutert, wie ein ASP.NET-Webprojekt in Visual Studio bereitgestellt wird und wie das Git-Plug-In verwendet wird, um den Code für Git zu übernehmen und Azure mit dem Git-Repository zu verknüpfen.

### WebMatrix

Weitere Informationen zum Bereitstellen auf Azure-Websites aus WebMatrix finden Sie in den folgenden Ressourcen:

-   [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-dotnet-using-webmatrix/). In dieser Ressource wird das Erstellen einer einfachen ASP.NET-Website unter Verwendung einer WebMatrix-Vorlage und das Bereitstellen dieser Website auf einer Azure-Website mithilfe von WebMatrix und Web Deploy erläutert.
-   [Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-nodejs-use-webmatrix/)
-   [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix](http://www.windowsazure.com/en-us/documentation/articles/web-sites-php-mysql-use-webmatrix/)
-   [WebMatrix 3: Integrated Git and Deployment to Azure (WebMatrix 3: Integriertes Git und Bereitstellung für Azure, in englischer Sprache)](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). In diesem Artikel wird das Verwenden von WebMatrix zum Bereitstellen über ein Git-Repository für die Quellcodeverwaltung beschrieben.

Bereitstellen über ein FTP-Dienstprogramm
-----------------------------------------

Unabhängig davon, welche IDE Sie verwenden, können Sie Inhalte auf Ihrer Website bereitstellen, indem Sie zum Kopieren von Dateien [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) verwenden. Das Erstellen von FTP-Anmeldeinformationen für eine Azure-Website ist kinderleicht. Darüber hinaus können Sie die Anmeldeinformationen für alle Anwendungen nutzen, die mit FTP verwendet werden, einschließlich Browser, wie beispielsweise Internet Explorer und kostenlose Dienstprogramme mit vollem Funktionsumfang, wie z. B. [FileZilla](https://filezilla-project.org/). Das als sicherer geltende FTPS-Protokoll wird ebenfalls von Azure-Websites unterstützt.

Obwohl es ziemlich einfach ist, die Dateien Ihrer Website mithilfe von FTP-Dienstprogrammen in Azure zu kopieren, berücksichtigen oder koordinieren diese Programme nicht automatisch die dazugehörigen Bereitstellungsaufgaben, wie beispielsweise das Bereitstellen einer Datenbank oder Ändern der Verbindungszeichenfolgen. Bei vielen FTP-Tools werden die Quell- und die Zieldateien nicht verglichen, sodass das Kopieren von unveränderten Dateien übersprungen werden könnte. Wenn bei umfangreichen Websites stets alle Dateien kopiert werden, kann es selbst bei kleineren Updates zu langen Wartezeiten hinsichtlich der Bereitstellung kommen, da immer alle Dateien kopiert werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP](/en-us/documentation/articles/web-sites-php-mysql-deploy-use-ftp/)
-   [Verwalten von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-manage/). Diese Ressource verfügt über zusätzliche Informationen zum Festlegen von FTP-Anmeldeinformationen, die nicht im PHP-Lernprogramm enthalten sind. Über die Kommentare unten auf der Seite erhalten Sie Informationen über das Abrufen der Anmeldeinformationen, die im Dokument selbst nicht vorhanden sind.

Bereitstellen über ein lokales Quellcodeverwaltungssystem
---------------------------------------------------------

Wenn Sie TFS, Git oder Mercurial in einem lokalen (nicht in einer Cloud gehosteten) Repository verwenden, kann die Bereitstellung direkt vom Repository auf Azure-Websites erfolgen.

### Team Foundation Server (TFS)

Bei Team Foundation Server handelt es sich um die lokale Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Sie können TFS so einrichten, dass die kontinuierliche Bereitstellung für eine Azure-Website erfolgt.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Kontinuierliche Bereitstellung für Cloud Services in Azure](/en-us/develop/net/common-tasks/continuous-delivery/). Dieses Dokument ist in erster Linie für einen Azure-Clouddienst bestimmt. Einige Inhalte gelten allerdings auch für Websites.

### Lokale Git- oder Mercurial-Repositorys

Sie können in Azure die URL eines beliebigen Repositorys eingeben, das Git oder Mercurial nutzt, um die Bereitstellung von diesem Standort aus durchzuführen. Das direkte Übertragen per Push aus einem lokalen Git-Repository auf eine Azure-Website ist auch möglich.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites](/en-us/documentation/articles/web-sites-publish-source-control/). In dieser Ressource wird gezeigt, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Methode der Veröffentlichung "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
-   [Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites über git/hg-Repositorys, in englischer Sprache)](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog von David Ebbo, in dem die Funktion "Externes Repository" in Azure-Websites erklärt wird.
-   [Azure-Forum für Git, Mercurial und DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit)
-   [Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). Blogeintrag von Scott Hanselman.

Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API
-------------------------------------------------------------------

Die Automatisierung Ihres Workflows für die Entwicklung ist immer die beste Wahl. Wenn Sie diese Aufgabe aber nicht direkt in Ihrem Quellcodeverwaltungssystem ausführen können, können Sie die Automatisierung mithilfe von Befehlszeilentools manuell einrichten. Im Allgemeinen sind dafür mehrere Tools oder Frameworks erforderlich, da bei der Bereitstellung oftmals Website-Verwaltungsfunktionen ausgeführt werden und Inhalte kopiert werden.

Mithilfe von Azure werden die für die Bereitstellung durchzuführenden Website-Verwaltungsaufgaben vereinfacht. Eine REST-Verwaltungs-API und mehrere Frameworks stehen zur Verfügung, sodass das Arbeiten mit der API einfacher ist.

### &lt;a name=msbuild\>&lt;/a\>MSBuild

Wenn Sie die [Visual Studio IDE](#vs) für die Entwicklung verwenden, können Sie [MSBuild](http://msbuildbook.com/) zur Automatisierung sämtlicher Aufgaben nutzen, die Sie in Ihrer IDE ausführen können. Sie können MSBuild so konfigurieren, dass zum Kopieren der Dateien entweder [Web Deploy](#webdeploy) oder [FTP/FTPS](#ftp) verwendet wird. Mit Web Deploy können auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, automatisiert werden, wie beispielsweise das Bereitstellen von Datenbanken.

Weitere Informationen zur Befehlszeilen-Bereitstellung mithilfe von MSBuild finden Sie in den folgenden Ressourcen:

-   [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Der zehnte Teil in einer Lernprogrammserie, in dem die Bereitstellung in Azure mithilfe von Visual Studio erläutert wird. In diesem Lernprogramm wird gezeigt, wie die Befehlszeile für die Bereitstellung verwendet wird, nachdem Veröffentlichungsprofile in Visual Studio eingerichtet wurden.
-   [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Buch, in dem Kapitel zur Verwendung von MSBuild für die Bereitstellung enthalten sind.

### FTP-Skripte

Die Erstellung von [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)-Anmeldeinformationen für eine Azure-Website ist kinderleicht. Die Anmeldeinformationen können anschließend auch für FTP-Stapelskripts verwendet werden.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Verwenden von FTP-Stapelskripts](http://support.microsoft.com/kb/96269)

### Windows PowerShell

Sie können MSBuild- oder FTP-Funktionen mithilfe von [Windows PowerShell](http://msdn.microsoft.com/en-us/library/dd835506.aspx) ausführen. Wenn dies der Fall ist, können Sie auch eine Sammlung von Windows PowerShell-Cmdlets nutzen, mit denen die Azure-REST-Verwaltungs-API leicht aufgerufen werden kann.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure – Automatisierung, in englischer Sprache)](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). In diesem E-Book-Kapitel wird erklärt, wie die im E-Book dargestellte Beispielanwendung Windows PowerShell-Skripte nutzt, um eine Azure-Testumgebung zu erstellen und wie die Bereitstellung für diese Umgebung erfolgt. Im Abschnitt [Ressourcen](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) finden Sie Links für die weiterführende Azure PowerShell-Dokumentation.

### .NET-Verwaltungs-API

Sie können C\#-Code schreiben, um die MSBuild- oder FTP-Funktionen für die Bereitstellung auszuführen. Wenn Sie dies tun, können Sie auf die Azure-REST-Verwaltungs-API zugreifen, um die Website-Verwaltungsfunktionen auszuführen.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Einführung in die .NET-Verwaltungs-API und Links für die weiterführende Dokumentation.

### Plattformübergreifende Befehlszeile (xpat-cli)

Sie können die auf Mac- oder Linux-Computern vorhandene Befehlszeile verwenden, um die Bereitstellung über FTP durchzuführen. Wenn Sie dies tun, können Sie auch auf die Azure-REST-Verwaltungs-API zugreifen, indem Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (xpat-cli) verwenden. Die plattformübergreifende Befehlszeilenschnittstelle kann auch auf Windows-Computern zum Einsatz kommen.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Befehlszeilentools](/en-us/downloads/#cmd-line-tools). Portalseite auf WindowsAzure.com mit Informationen zu Befehlszeilentools.

### Web Deploy-Befehlszeile

Bei [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) handelt es sich um Microsoft-Software für die Bereitstellung in IIS, wobei neben den Funktionen für die intelligente Dateisynchronisierung auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen und bei der Verwendung von FTP nicht automatisierbar sind, ausgeführt und koordiniert werden können. Mithilfe von Web Deploy können beispielsweise neue Datenbanken oder Datenbank-Updates auf Ihrer Website bereitgestellt werden. Auch die Zeit, die zum Aktualisieren einer vorhandenen Website erforderlich ist, kann mit Web Deploy verringert werden, da dieses Programm auf intelligente Weise lediglich geänderte Dateien kopiert. Microsoft WebMatrix, Visual Studio, Visual Studio Online und Team Foundation Server verfügen über die integrierte Unterstützung für Web Deploy. Sie können Web Deploy aber auch direkt über die Befehlszeile verwenden, um die Bereitstellung zu automatisieren. Die Web Deploy-Befehle sind äußerst leistungsstark, stellen aber einen gewaltigen Lernprozess dar.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Webbereitstellungstool](http://technet.microsoft.com/en-us/library/dd568996). Offizielle Dokumentation auf der Microsoft TechNet-Website. Der Inhalt ist nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
-   [Verwenden von Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Offizielle Dokumentation auf der Microsoft IIS.NET-Website. Der Inhalt ist auch nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
-   [StackOverflow](http://www.stackoverflow.com). Die beste Adresse, um aktuelle Informationen zur Verwendung von Web Deploy über die Befehlszeile zu erhalten.
-   [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Bei MSBuild handelt es sich um das von Visual Studio verwendete Erstellungsmodul, das auch über die Befehlszeile zum Bereitstellen von Webanwendungen auf Azure-Websites genutzt werden kann. Dieses Lernprogramm ist Bestandteil einer Serie, in der es in erster Linie um die Visual Studio-Bereitstellung geht.

Nächste Schritte
----------------

Weitere Informationen finden Sie im Abschnitt "Bereitstellen" in der [Dokumentation für Azure-Websites](/en-us/documentation/services/web-sites/).

