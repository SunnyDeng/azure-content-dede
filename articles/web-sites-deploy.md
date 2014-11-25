<properties linkid="manage-services-how-to-deploy-websites" pageTitle="How to deploy an Azure Website" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

# Bereitstellen einer Azure-Website

Für die Bereitstellung Ihrer eigenen Inhalte auf einer Azure-Website gibt es viele Möglichkeiten. Dieses Thema enthält eine kurze Übersicht über die jeweilige Option und Links, um weitere Informationen zu erhalten.

-   [Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem][Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem]

    -   Visual Studio Online (VSO)
    -   Repositorywebsites über Git
    -   Repositorywebsites über Mercurial
    -   Dropbox
-   [Bereitstellen über eine IDE][Bereitstellen über eine IDE]

    -   Visual Studio
    -   WebMatrix
-   [Bereitstellen über ein FTP-Dienstprogramm][Bereitstellen über ein FTP-Dienstprogramm]
-   [Bereitstellen über ein lokales Quellcodeverwaltungssystem][Bereitstellen über ein lokales Quellcodeverwaltungssystem]

    -   Team Foundation Server (TFS)
    -   Lokale Git- oder Mercurial-Repositorys
-   [Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API][Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API]

    -   MSBuild
    -   FTP-Skripte
    -   Windows PowerShell
    -   .NET-Verwaltungs-API
    -   Plattformübergreifende Befehlszeile (xpat-cli)
    -   Web Deploy-Befehlszeile

## <a name="cloud"></a>Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem

Für die Bereitstellung einer Website ist es am einfachsten, einen [Workflow für die kontinuierliche Bereitstellung][Workflow für die kontinuierliche Bereitstellung] einzurichten, der in Ihr [Quellcodeverwaltungssystem][Quellcodeverwaltungssystem] integriert ist. Durch die Automatisierung ist der Entwicklungsprozess effizienter, und darüber hinaus können auch die Prozesse für die Sicherung und Wiederherstellung besser verwaltet werden und sind zuverlässiger.

Wenn Sie die Quellcodeverwaltung bisher noch nicht eingerichtet haben, ist es am einfachsten, wenn Sie dafür ein in der Cloud gehostetes Quellcodeverwaltungssystem verwenden.

### <a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online][Visual Studio Online] (zuvor Team Foundation Service) ist die cloudbasierte Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Für ein Team mit bis zu 5 Entwicklern ist dieser Dienst kostenlos. Sie können VSO einrichten, um die kontinuierliche Bereitstellung auf einer Azure-Website zu gewährleisten. Für das Repository kann entweder [Git oder TFVC][Visual Studio Online] verwendet werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Kontinuierliche Bereitstellung auf Azure mit VSO und TFVC][Kontinuierliche Bereitstellung auf Azure mit VSO und TFVC]. In diesem Lernprogramm wird Schritt für Schritt gezeigt, wie die kontinuierliche Bereitstellung von VSO auf einer Azure-Website mithilfe von TFVC eingerichtet wird. Bei TFVC handelt es sich um die zentralisierte Quellcodeverwaltungsoption in VSO. Git ist hingegen die dezentralisierte Quellcodeverwaltungsoption.
-   [Kontinuierliche Bereitstellung auf Azure mit VSO und TFVC][1]. Ähnlich wie bei vorherigen Lernprogrammen durchlaufen Sie auch hier die Schritte zur Registrierung eines VSO-Kontos und zum Einchecken eines Projekts in die Quellcodeverwaltung.
-   [Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und Git][Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und Git]. Dieses Lernprogramm ist vergleichbar mit dem vorangehenden Lernprogramm, verwendet jedoch anstelle von TFVC das Verwaltungstool Git.

### <a name="git"></a>Repositorywebsites über Git

[Git][Visual Studio Online] ist ein beliebtes dezentralisiertes Quellcodeverwaltungssystem. Azure verfügt über integrierte Funktionen, mit denen die automatische Bereitstellung auf einer Azure-Website über gängige webbasierte Repositorywebsites, in denen Git-Repositorys einschließlich [GitHub][GitHub], [CodePlex][CodePlex] und [BitBucket][BitBucket] gespeichert sind, im Handumdrehen möglich ist. Ein Vorteil bei der Verwendung von Git für die Bereitstellung besteht darin, dass das Zurücksetzen auf eine frühere Bereitstellung relativ einfach ist.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites][Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites]. Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
-   [Bereitstellen auf Websites mit GitHub unter Verwendung von Kudu][Bereitstellen auf Websites mit GitHub unter Verwendung von Kudu]. Das Video von Scott Hanselman und David Ebbo zeigt, wie Sie eine Website direkt von GitHub auf einer Azure-Website bereitstellen.
-   [Azure-Forum für Git, Mercurial und DropBox][Azure-Forum für Git, Mercurial und DropBox]

### <a name="mercurial"></a>Repositorywebsites über Mercurial

Wenn Sie [Mercurial][Mercurial] als Quellcodeverwaltungssystem nutzen und das Repository in [CodePlex][CodePlex] oder [BitBucket][BitBucket] speichern, können Sie die integrierten Funktionen in Azure-Websites verwenden, sodass Ihre Inhalte automatisch bereitgestellt werden.

Weitere Informationen zur Bereitstellung mithilfe von Mercurial finden Sie in den folgenden Ressourcen:

-   [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites][Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites]. Obwohl in diesem Lernprogramm die Veröffentlichung eines Git-Repositorys gezeigt wird, ist der Vorgang bei Mercurial-Repositorys, die in CodePlex oder BitBucket gehostet werden, ähnlich.
-   [Azure-Forum für Git, Mercurial und DropBox][Azure-Forum für Git, Mercurial und DropBox]

### <a name="dropbox"></a>Dropbox

[DropBox][DropBox] stellt kein Quellcodeverwaltungssystem dar. Wenn Sie allerdings den Quellcode in DropBox speichern, können Sie die Bereitstellung über Ihr DropBox-Konto automatisieren.

-   [Deploy To Windows Azure Using Dropbox (Bereitstellen in Azure mithilfe von Dropbox, in englischer Sprache)][Deploy To Windows Azure Using Dropbox (Bereitstellen in Azure mithilfe von Dropbox, in englischer Sprache)]. Hier erfahren Sie, wie das Azure-Verwaltungsportal zum Einrichten der DropBox-Bereitstellung verwendet wird.
-   [DropBox und Azure-Websites][DropBox und Azure-Websites]. In diesem Video wird gezeigt, wie ein DropBox-Ordner mit einer Azure-Website verknüpft wird. Darüber hinaus wird demonstriert, wie schnell Sie eine Website mit der einfachen Drag & Drop-Bereitstellung einrichten oder verwalten können.
-   [Azure-Forum für Git, Mercurial und DropBox][Azure-Forum für Git, Mercurial und DropBox]

## <a name="ide"></a>Bereitstellen über eine IDE

Bei [Visual Studio][Visual Studio] und [WebMatrix][WebMatrix] handelt es sich um Microsoft-IDEs (Integrated Development Environments = Integrierte Entwicklungsumgebungen), die Sie für die Webentwicklung verwenden können. Beide stellen integrierte Funktionen für die einfache Bereitstellung auf Azure-Websites zur Verfügung. [Web Deploy][Web Deploy] kann bei beiden Entwicklungsumgebungen zum Einsatz kommen, um zusätzliche Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, zu automatisieren. Bei diesen Aufgaben kann es sich um die Datenbankbereitstellung oder Änderungen hinsichtlich der Verbindungszeichenfolge handeln. Die Bereitstellung kann bei beiden Umgebungen auch mithilfe von [FTP oder FTPS][FTP oder FTPS] erfolgen.

WebMatrix kann schnell installiert werden und ist leicht erlernbar. Visual Studio bietet allerdings viele weitere Funktionen für die Arbeit mit Azure-Websites. In der Visual Studio-IDE können Sie Azure-Websites erstellen, anhalten, starten und löschen. Darüber hinaus können Sie Protokolle anzeigen, während diese in Echtzeit erstellt werden. Neben vielen weiteren Funktionen ist auch das Remotedebuggen möglich. Visual Studio kann auch mit Quellcodeverwaltungssystemen, wie beispielsweise [Visual Studio Online][2], [Team Foundation Server][Team Foundation Server] und [Git-Repositorys][Git-Repositorys], integriert werden.

### <a name="vs"></a>Visual Studio

Weitere Informationen zur Bereitstellung von Visual Studio auf Azure-Websites finden Sie in den folgenden Ressourcen:

-   [Erste Schritte mit Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET]. In dieser Ressource wird das Erstellen und Bereitstellen eines einfachen ASP.NET MVC-Webprojekts mithilfe von Visual Studio und Web Deploy erläutert.
-   [Bereitstellen von Azure WebJobs auf Azure-Websites][Bereitstellen von Azure WebJobs auf Azure-Websites]. Hier erfahren Sie, wie Sie Konsolenanwendungsprojekte für die Bereitstellung als WebJobs konfigurieren.
-   [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website][Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website]. In dieser Ressource wird das Erstellen und Bereitstellen eines ASP.NET MVC-Webprojekts mit einer SQL-Datenbank unter Verwendung von Visual Studio, Web Deploy und Entity Framework Code First-Migrationen erläutert.
-   [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET][Übersicht über die Webbereitstellung für Visual Studio und ASP.NET]. Einstieg in die Webbereitstellung mithilfe von Visual Studio. Obwohl der Inhalt nicht mehr auf dem neuesten Stand ist, sind Informationen enthalten, die noch immer relevant sind. Dazu gehört eine Übersicht über die Optionen für die Bereitstellung einer Datenbank zusammen mit der Webanwendung und eine Liste mit zusätzlichen Bereitstellungsaufgaben, die Sie möglicherweise noch erledigen müssen oder die Sie manuell konfigurieren, sodass diese Aufgaben von Visual Studio ausgeführt werden. In diesem Thema wird die Bereitstellung allgemein erläutert und nicht nur die Bereitstellung auf Azure-Websites.
-   [ASP.NET-Webbereitstellung mithilfe von Visual Studio][ASP.NET-Webbereitstellung mithilfe von Visual Studio] Dabei handelt es sich um eine Lernprogrammserie mit zwölf Teilen, in der die Bereitstellungsaufgaben vollständig vorgestellt werden.
-   [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)][Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)]. In diesem Artikel wird erläutert, wie ein ASP.NET-Webprojekt in Visual Studio bereitgestellt wird und wie das Git-Plug-In verwendet wird, um den Code für Git zu übernehmen und Azure mit dem Git-Repository zu verknüpfen.

### <a name="webmatrix"></a>WebMatrix

Weitere Informationen zur Bereitstellung von WebMatrix auf Azure-Websites finden Sie in den folgenden Ressourcen:

-   [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix][Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix]. Hier erfahren Sie, wie Sie eine einfache ASP.NET-Website unter Verwendung einer WebMatrix-Vorlage erstellen und mithilfe von WebMatrix und Web Deploy auf einer Azure-Website bereitstellen.
-   [Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix][Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix]
-   [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix][Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix]
-   [WebMatrix 3: Integriertes Git und Bereitstellung für Azure][WebMatrix 3: Integriertes Git und Bereitstellung für Azure]. In diesem Artikel wird das Verwenden von WebMatrix zum Bereitstellen über ein Git-Repository für die Quellcodeverwaltung beschrieben.

## <a name="ftp"></a>Bereitstellen über ein FTP-Dienstprogramm

Unabhängig davon, welche IDE Sie verwenden, können Sie Inhalte auf Ihrer Website bereitstellen, indem Sie zum Kopieren von Dateien [FTP][FTP oder FTPS] verwenden. Sie können problemlos FTP-Anmeldeinformationen für eine Azure-Website erstellen und sie in einer beliebigen Anwendung verwenden, die FTP unterstützt, z. B. Internet Explorer und kostenlose Hilfsprogramme mit vollem Funktionsumfang wie [FileZilla][FileZilla]. Das sicherheitsoptimierte FTPS-Protokoll wird ebenfalls von Azure-Websites unterstützt.

Obwohl es ziemlich einfach ist, die Dateien Ihrer Website mithilfe von FTP-Hilfsprogrammen in Azure zu kopieren, berücksichtigen oder koordinieren diese Programme nicht automatisch die dazugehörigen Bereitstellungsaufgaben, wie beispielsweise das Bereitstellen einer Datenbank oder Ändern der Verbindungszeichenfolgen. Bei vielen FTP-Tools werden die Quell- und die Zieldateien nicht verglichen, sodass das Kopieren von unveränderten Dateien übersprungen werden könnte. Wenn bei umfangreichen Websites stets alle Dateien kopiert werden, kann es selbst bei kleineren Updates zu langen Wartezeiten hinsichtlich der Bereitstellung kommen, da immer alle Dateien kopiert werden.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP][Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP]
-   [Verwalten von Websites][Verwalten von Websites]. Diese Ressource verfügt über zusätzliche Informationen zum Festlegen von FTP-Anmeldeinformationen, die nicht im PHP-Lernprogramm enthalten sind.

## <a name="onpremises"></a>Bereitstellen über ein lokales Quellcodeverwaltungssystem

Wenn Sie TFS, Git oder Mercurial in einem lokalen (nicht in der Cloud gehosteten) Repository verwenden, kann die Bereitstellung direkt vom Repository auf Azure-Websites erfolgen.

### <a name="tfs"></a>Team Foundation Server (TFS)

Bei Team Foundation Server handelt es sich um die lokale Lösung von Microsoft für die Quellcodeverwaltung und die Teamzusammenarbeit. Sie können TFS für die kontinuierliche Bereitstellung auf einer Azure-Website einrichten.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Kontinuierliche Bereitstellung für Cloud Services in Azure][Kontinuierliche Bereitstellung für Cloud Services in Azure]. Dieses Dokument ist in erster Linie für einen Azure-Cloud-Dienst bestimmt. Einige Inhalte gelten allerdings auch für Websites.

### <a name="gitmercurial"></a>Lokale Git- oder Mercurial-Repositorys

Sie können in Azure die URL eines beliebigen Repositorys eingeben, das Git oder Mercurial nutzt, um die Bereitstellung von diesem Standort aus durchzuführen. Außerdem werden direkte Push-Übertragungen aus einem lokalen Git-Repository an eine Azure-Website unterstützt.

Weitere Informationen finden Sie in den folgenden Ressourcen:

-   [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites][Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites]. Hier erfahren Sie, wie Sie Git verwenden, um direkt von Ihrem lokalen Computer aus auf einer Azure-Website zu veröffentlichen (in Azure wird diese Veröffentlichungsmethode "Lokales Git" genannt). Außerdem erfahren Sie, wie Sie die kontinuierliche Bereitstellung von Git-Repositorys über GitHub, CodePlex oder BitBucket aktivieren.
-   [Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites über git/hg-Repositorys, in englischer Sprache)][Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites über git/hg-Repositorys, in englischer Sprache)]. Blog von David Ebbo, in dem das Feature "Externes Repository" in Azure-Websites erklärt wird.
-   [Azure-Forum für Git, Mercurial und DropBox][Azure-Forum für Git, Mercurial und DropBox]
-   [Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)][Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)]. Blogeintrag von Scott Hanselman.

## <a name="commandline"></a>Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API

Die Automatisierung Ihres Workflows für die Entwicklung ist immer die beste Wahl. Wenn Sie diese Aufgabe aber nicht direkt in Ihrem Quellcodeverwaltungssystem ausführen können, können Sie die Automatisierung mithilfe von Befehlszeilentools manuell einrichten. Im Allgemeinen sind dafür mehrere Tools oder Frameworks erforderlich, da bei der Bereitstellung oftmals Website-Verwaltungsfunktionen ausgeführt werden und Inhalte kopiert werden.

Mithilfe von Azure werden die für die Bereitstellung durchzuführenden Website-Verwaltungsaufgaben vereinfacht. Eine REST-Verwaltungs-API und mehrere Frameworks stehen zur Verfügung, sodass das Arbeiten mit der API einfacher ist.

### <a name="msbuild"></a>MSBuild

Wenn Sie die [Visual Studio IDE][Visual Studio IDE] für die Entwicklung verwenden, können Sie [MSBuild][MSBuild] zur Automatisierung sämtlicher Aufgaben nutzen, die Sie in Ihrer IDE ausführen können. Sie können MSBuild so konfigurieren, dass zum Kopieren der Dateien entweder [Web Deploy][3] oder [FTP/FTPS][Bereitstellen über ein FTP-Dienstprogramm] verwendet wird. Mit Web Deploy können auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen, automatisiert werden, wie beispielsweise das Bereitstellen von Datenbanken.

Weitere Informationen zur Befehlszeilen-Bereitstellung mithilfe von MSBuild finden Sie in den folgenden Ressourcen:

-   [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung][ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung]. Der zehnte Teil in einer Lernprogrammserie, in dem die Bereitstellung in Azure mithilfe von Visual Studio erläutert wird. In diesem Lernprogramm wird gezeigt, wie die Befehlszeile für die Bereitstellung verwendet wird, nachdem Veröffentlichungsprofile in Visual Studio eingerichtet wurden.
-   [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build][MSBuild]. Buch, in dem Kapitel zur Verwendung von MSBuild für die Bereitstellung enthalten sind.

### <a name="ftp2"></a>FTP-Skripte

Sie können auf einfache Weise [FTP/FTPS][FTP oder FTPS]-Anmeldeinformationen für eine Azure-Website erstellen. Die Anmeldeinformationen können anschließend auch mit FTP-Batchskripts verwendet werden.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Verwenden von FTP-Stapelskripts][Verwenden von FTP-Stapelskripts]

### <a name="powershell"></a>Windows PowerShell

Sie können MSBuild- oder FTP-Bereitstellungsfunktionen mithilfe von [Windows PowerShell][Windows PowerShell] ausführen. Wenn dies der Fall ist, können Sie auch eine Sammlung von Windows PowerShell-Cmdlets nutzen, mit denen die Azure-REST-Verwaltungs-API leicht aufgerufen werden kann.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure – Automatisierung, in englischer Sprache)][Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure – Automatisierung, in englischer Sprache)]. In diesem E-Book-Kapitel wird erklärt, wie die im E-Book dargestellte Beispielanwendung Windows PowerShell-Skripte nutzt, um eine Azure-Testumgebung zu erstellen und wie die Bereitstellung für diese Umgebung erfolgt. Im Abschnitt [Ressourcen][Ressourcen] finden Sie Links für die weiterführende Azure PowerShell-Dokumentation.

### <a name="api"></a>.NET-Verwaltungs-API

Sie können C#-Code schreiben, um die MSBuild- oder FTP-Funktionen für die Bereitstellung auszuführen. Wenn Sie dies tun, können Sie auf die Azure-REST-Verwaltungs-API zugreifen, um die Website-Verwaltungsfunktionen auszuführen.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)][Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)]. Einführung in die .NET-Verwaltungs-API und Links für die weiterführende Dokumentation.

### <a name="cli"></a>Plattformübergreifende Befehlszeile (xpat-cli)

Sie können die auf Mac- oder Linux-Computern vorhandene Befehlszeile verwenden, um die Bereitstellung über FTP durchzuführen. Wenn Sie dies tun, können Sie auch auf die Azure-REST-Verwaltungs-API zugreifen, indem Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (xpat-cli) verwenden. Die plattformübergreifende Befehlszeilenschnittstelle kann auch auf Windows-Computern zum Einsatz kommen.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Befehlszeilentools][Befehlszeilentools]. Portalseite auf WindowsAzure.com mit Informationen zu Befehlszeilentools.

### <a name="webdeploy"></a>Web Deploy-Befehlszeile

Bei [Web Deploy][Web Deploy] handelt es sich um Microsoft-Software für die Bereitstellung in IIS, wobei neben den Funktionen für die intelligente Dateisynchronisierung auch viele andere Aufgaben, die mit der Bereitstellung in Zusammenhang stehen und bei der Verwendung von FTP nicht automatisierbar sind, ausgeführt und koordiniert werden können. Mithilfe von Web Deploy können beispielsweise neue Datenbanken oder Datenbankupdates mit Ihrer Website bereitgestellt werden. Auch die Zeit, die zum Aktualisieren einer vorhandenen Website erforderlich ist, kann mit Web Deploy verringert werden, da dieses Programm auf intelligente Weise lediglich geänderte Dateien kopiert. Microsoft WebMatrix, Visual Studio, Visual Studio Online und Team Foundation Server verfügen über die integrierte Unterstützung für Web Deploy. Sie können Web Deploy aber auch direkt über die Befehlszeile verwenden, um die Bereitstellung zu automatisieren. Die Web Deploy-Befehle sind äußerst leistungsstark, stellen aber einen gewaltigen Lernprozess dar.

Weitere Informationen finden Sie in der folgenden Ressource:

-   [Webbereitstellungstool][Webbereitstellungstool]. Offizielle Dokumentation auf der Microsoft TechNet-Website. Der Inhalt ist nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
-   [Verwenden von Web Deploy][Verwenden von Web Deploy]. Offizielle Dokumentation auf der Microsoft IIS.NET-Website. Der Inhalt ist auch nicht mehr auf dem neuesten Stand, aber für den Anfang gut geeignet.
-   [StackOverflow][StackOverflow]. Die beste Adresse, um aktuelle Informationen zur Verwendung von Web Deploy über die Befehlszeile zu erhalten.
-   [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung][ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung]. Bei MSBuild handelt es sich um das von Visual Studio verwendete Buildmodul, das auch über die Befehlszeile zum Bereitstellen von Webanwendungen auf Azure-Websites genutzt werden kann. Dieses Lernprogramm ist Bestandteil einer Serie, in der es in erster Linie um die Visual Studio-Bereitstellung geht.

## <a name="nextsteps"></a>Nächste Schritte

In einigen Szenarien möchten Sie ggf. in der Lage sein, bequem zwischen einer Staging- und Produktionsversion Ihrer Website zu wechseln. Dazu steht Ihnen die Funktion für die Stagingbereitstellung von Azure-Websites zur Verfügung. Weitere Informationen finden Sie unter [Stagingbereitstellung für Microsoft Azure-Websites][Stagingbereitstellung für Microsoft Azure-Websites].

Die Aufstellung eines Sicherungs- und Wiederherstellungsplans ist ein wesentlicher Bestandteil jedes Bereitstellungsworkflows. Weitere Informationen zu den Sicherungs- und Wiederherstellungsfunktionen für Azure-Websites finden Sie unter [Sicherungen von Azure-Websites][Sicherungen von Azure-Websites].

Weitere Informationen zu weiteren Bereitstellungsthemen finden Sie in den entsprechenden Abschnitten der [Dokumentation für Azure-Websites][Dokumentation für Azure-Websites].

  [Bereitstellen über ein in der Cloud gehostetes Quellcodeverwaltungssystem]: #cloud
  [Bereitstellen über eine IDE]: #ide
  [Bereitstellen über ein FTP-Dienstprogramm]: #ftp
  [Bereitstellen über ein lokales Quellcodeverwaltungssystem]: #onpremises
  [Verwenden von Befehlszeilentools und der Azure-REST-Verwaltungs-API]: #commandline
  [Workflow für die kontinuierliche Bereitstellung]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [Quellcodeverwaltungssystem]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Visual Studio Online]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs
  [Kontinuierliche Bereitstellung auf Azure mit VSO und TFVC]: http://www.visualstudio.com/de-de/learn/continuous-delivery-in-vs
  [1]: /de-de/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Kontinuierliche Bereitstellung aus Azure mit Visual Studio Online und Git]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
  [GitHub]: http://www.github.com
  [CodePlex]: http://www.codeplex.com/
  [BitBucket]: https://bitbucket.org/
  [Veröffentlichen aus der Quellcodeverwaltung auf Azure-Websites]: /de-de/documentation/articles/web-sites-publish-source-control/
  [Bereitstellen auf Websites mit GitHub unter Verwendung von Kudu]: /de-de/documentation/videos/deploying-to-azure-from-github/
  [Azure-Forum für Git, Mercurial und DropBox]: http://social.msdn.microsoft.com/Forums/windowsazure/de-de/home?forum=azuregit
  [Mercurial]: http://mercurial.selenic.com/
  [DropBox]: https://www.dropbox.com/
  [Deploy To Windows Azure Using Dropbox (Bereitstellen in Azure mithilfe von Dropbox, in englischer Sprache)]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx
  [DropBox und Azure-Websites]: http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites
  [Visual Studio]: http://www.visualstudio.com/de-de/downloads/download-visual-studio-vs.aspx
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [Web Deploy]: http://www.iis.net/downloads/microsoft/web-deploy
  [FTP oder FTPS]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [2]: #vso
  [Team Foundation Server]: #tfs
  [Git-Repositorys]: #git
  [Erste Schritte mit Azure und ASP.NET]: /de-de/develop/net/tutorials/get-started/
  [Bereitstellen von Azure WebJobs auf Azure-Websites]: /de-de/documentation/articles/websites-dotnet-deploy-webjobs/
  [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website]: /de-de/develop/net/tutorials/web-site-with-sql-database/
  [Übersicht über die Webbereitstellung für Visual Studio und ASP.NET]: http://msdn.microsoft.com/de-de/library/dd394698.aspx
  [ASP.NET-Webbereitstellung mithilfe von Visual Studio]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction
  [Deploying an ASP.NET Website to Azure in Visual Studio 2012 from a Git Repository directly (Direktes Bereitstellen einer ASP.NET-Website auf Azure in Visual Studio 2012 über ein Git-Repository, in englischer Sprache)]: http://www.dotnetcurry.com/ShowArticle.aspx?ID=881
  [Entwickeln und Bereitstellen einer Website mit Microsoft WebMatrix]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-dotnet-using-webmatrix/
  [Erstellen einer Node.js-Website und Bereitstellen für Azure mit WebMatrix]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-php-mysql-use-webmatrix/
  [WebMatrix 3: Integriertes Git und Bereitstellung für Azure]: http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD
  [FileZilla]: https://filezilla-project.org/
  [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP]: /de-de/documentation/articles/web-sites-php-mysql-deploy-use-ftp/
  [Verwalten von Websites]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-manage#ftp-credentials
  [Kontinuierliche Bereitstellung für Cloud Services in Azure]: /de-de/develop/net/common-tasks/continuous-delivery/
  [Publishing to Azure Web Sites from any git/hg repo (Veröffentlichen auf Azure-Websites über git/hg-Repositorys, in englischer Sprache)]: http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html
  [Deploying TWO websites to Azure from one Git Repository (Bereitstellen von TWO-Websites in Azure über ein Git-Repository, in englischer Sprache)]: http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx
  [Visual Studio IDE]: #vs
  [MSBuild]: http://msbuildbook.com/
  [3]: #webdeploy
  [ASP.NET-Webbereitstellung mithilfe von Visual Studio: Befehlszeilen-Bereitstellung]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment
  [Verwenden von FTP-Stapelskripts]: http://support.microsoft.com/kb/96269
  [Windows PowerShell]: http://msdn.microsoft.com/de-de/library/dd835506.aspx
  [Building Real-World Cloud Apps with Azure - Automate Everything (Erstellen von Cloud-Anwendungen mit Azure – Automatisierung, in englischer Sprache)]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Ressourcen]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources
  [Automating everything with the Azure Management Libraries and .NET (Automatisierung mithilfe der Azure-Verwaltungsbibliotheken und .NET, in englischer Sprache)]: http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx
  [Befehlszeilentools]: /de-de/downloads/#cmd-line-tools
  [Webbereitstellungstool]: http://technet.microsoft.com/de-de/library/dd568996
  [Verwenden von Web Deploy]: http://www.iis.net/learn/publish/using-web-deploy
  [StackOverflow]: http://www.stackoverflow.com
  [Stagingbereitstellung für Microsoft Azure-Websites]: /de-de/documentation/articles/web-sites-staged-publishing/
  [Sicherungen von Azure-Websites]: /de-de/documentation/articles/web-sites-backup/
  [Dokumentation für Azure-Websites]: /de-de/documentation/services/web-sites/
