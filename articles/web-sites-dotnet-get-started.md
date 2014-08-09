<properties  linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Azure for ASP.NET" pageTitle="Get started with Azure for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Web Site. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Erste Schritte mit Azure und ASP.NET

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/de-de/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/de-de/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

 In diesem Lernprogramm wird die Erstellung einer ASP.NET-Webanwendung
und Bereitstellung für eine Windows Azure-Website mit Visual Studio 2013 oder Visual Studio 2013 for Web Express gezeigt. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie keine Erfahrung mit der Verwendung von Azure oder ASP.NET haben. Nach Abschluss des Lernprogramms verfügen Sie
über eine einfache Webanwendung, die in der Cloud ausgeführt wird.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht
über Visual Studio 2013 verfügen, wird Visual Studio 2013 for Web
Express automatisch durch das SDK installiert. Auf diese Weise können Sie völlig kostenlos mit der Entwicklung für Azure beginnen.

Sie lernen Folgendes:

* Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch
  Installieren des Azure SDK
* Erstellen eines Visual Studio ASP.NET-Webprojekts und Bereitstellen
  für eine Azure-Website
* Vornehmen von Änderungen an dem Projekt und erneute Bereitstellung

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Website-Startseite](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

> [WACOM.NOTE] Um dieses Lernprogramm abzuschließen, benötigen Sie ein
> Azure-Konto. Wenn Sie kein Konto haben, können Sie [Ihre
> MSDN-Abonnementenvorteile
> aktivieren](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oder [sich für eine kostenlose
> Testversion
> registrieren](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Teile des Lernprogramms

* [Einrichten der
  Entwicklungsumgebung](#set-up-the-development-environment)
* [Erstellen eines ASP.NET-Webprojekts in Visual
  Studio](#create-an-asp.net-web-project)
* [Bereitstellen der Anwendung für
  Azure](#deploy-the-application-to-azure)
* [Vornehmen von Änderungen und erneutes
  Bereitstellen](#make-a-change-and-redeploy)
* [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Erstellen einer ASP.NET-Webanwendung

Zunächst erstellen Sie ein Webanwendungsprojekt. Die Azure-Website, auf der Sie Ihr Projekt später veröffentlichen, wird automatisch von Visual Studio erstellt.

1.  Öffnen Sie Visual Studio 2013 oder Visual Studio 2013 Express for
    Web.

2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.
    
    !["Neues Projekt" im Menü
    "Datei"](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **C#**
    oder **Visual Basic**, und wählen Sie unter **Installierte
    Vorlagen** die Option **Web** aus. Wählen Sie dann
    **ASP.NET-Webanwendung** aus.

4.  Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5**
    ausgewählt ist.

5.  Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.
    
    ![Dialogfeld "Neues
    Projekt"](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  Wählen Sie im Dialogfeld **New ASP.NET Project** die Vorlage **MVC**
    oder **Web Forms** aus, und klicken Sie dann auf **Authentifizierung
    ändern**.
    
    [MVC und Web Forms][1] sind ASP.NET-Frameworks zum Erstellen von
    Websites. Wenn Sie keine Präferenz haben und andere
    Azure-Lernprogramme durcharbeiten möchten, empfiehlt sich MVC, da
    mehr Azure-Lernprogramme diese Option verwenden.
    
    ![Dialogfeld "New ASP.NET
    Project"](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine
    Authentifizierung** und dann auf **OK**.
    
    ![Keine
    Authentifizierung](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    Die erstellte Beispielanwendung enthält keine Features, die eine
    Benutzeranmeldung erfordern. Im Abschnitt [Nächste
    Schritte](#next-steps) am Ende des Lernprogramms gibt es eine
    Verknüpfung zu einem Lernprogramm, in dem Authentifizierung und
    Autorisierung implementiert wird.

8.  Lassen Sie das Kontrollkästchen im Dialogfeld unter **Azure**
    aktiviert, und ändern Sie die Einstellung **Website** im
    Dropdownfeld nicht.
    
    Die Überschrift des Kontrollkästchens kann **Host in the cloud**
    oder **Create remote resources** lauten. Die Wirkung ist in beiden
    Fällen die gleiche.
    
    Mit diesen Einstellungen wird angegeben, dass eine Azure-Website für
    das Webprojekt von Visual Studio erstellt wird. Sie stellen das
    Webprojekt für die neu erstellte Website bereit. (Alternativ können
    Sie die Aktivierung des Kontrollkästchens ändern, damit von Visual
    Studio ein virtueller Azure-Computer mit IIS erstellt wird. Dieses
    Lernprogramm enthält jedoch keine Anweisungen für diese Option.)

9.  Klicken Sie im Dialogfeld **New ASP.NET Project** auf **OK**.
    
    ![Dialogfeld "New ASP.NET
    Project"](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)
    
    Im Bildschirmfoto wird die ausgewählte MVC-Vorlage dargestellt. Wenn
    Sie **Web Forms** ausgewählt haben, ist diese Option ausgewählt.

10. Falls Sie sich noch nicht bei Azure angemeldet haben, werden Sie von
    Visual Studio dazu aufgefordert, dies nachzuholen. Klicken Sie auf
    **Anmelden**.
    
    ![Anmelden bei
    Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

11. Geben Sie die ID und das Kennwort des Kontos ein, mit dem Sie Ihr
    Azure-Abonnement verwalten.
    
    ![Anmelden bei
    Azure](./media/web-sites-dotnet-get-started-vs2013/signindb.png)
    
    Wenn Sie angemeldet sind, werden Sie im Dialogfeld **Configure Azure
    Site Settings** gefragt, welche Ressourcen Sie erstellen möchten.
    
    ![Bei Azure
    angemeldet](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

12. Visual Studio gibt eine Standardbezeichnung für **Websitename** an,
    die von Azure als Präfix der Anwendungs-URL verwendet wird. Geben
    Sie ggf. einen anderen Websitenamen ein.
    
    Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge
    und der Domäne, die neben dem Textfeld aufgeführt wird. Beispiel:
    Wenn der Websitename `MyExample6442` lautet, ist die URL
    `MyExample6442.azurewebsites.net`. Wenn eine andere Person die
    eingegebene URL bereits verwendet hat, wird rechts ein rotes
    Ausrufungszeichen anstelle eines grünen Häkchens angezeigt, und Sie
    müssen einen anderen Wert eingeben.

13. Wählen Sie in der Dropdownliste **Region** den am nächsten
    befindlichen Ort aus.
    
    Mit dieser Einstellung wird das Azure-Datencenter angegeben, in dem
    Ihre Website ausgeführt wird.

14. Verändern Sie die Datenbankfelder nicht.
    
    Für dieses Lernprogramm verwenden Sie keine Datenbank. Der Abschnitt
    [Nächste Schritte](#next-steps) am Ende des Lernprogramms ist mit
    einem Lernprogramm verknüpft, in dem die Verwendung einer Datenbank
    erläutert wird.

15. Klicken Sie auf **OK**.
    
    Das Webprojekt wird von Visual Studio innerhalb von wenigen Sekunden
    in dem angegebenen Ordner erstellt. Auch die Website wird in der
    angegebenen Azure-Region erstellt.
    
    Im Fenster **Projektmappen-Explorer** werden die Dateien und Ordner
    in dem neuen Projekt angezeigt. (Im Bildschirmfoto wird ein Web
    Forms-Projekt gezeigt, ein MVC-Projekt enthält andere Ordner und
    Dateien.)
    
    ![Projektmappen-Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)
    
    Im Fenster **Web Publish Activity** wird angegeben, dass die Website
    erstellt wurde.
    
    ![Erstellte
    Website](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

## Bereitstellen der Anwendung für Azure

1.  Klicken Sie im Fenster **Web Publish Activity** auf **Publish
    MyExample to this site now**.
    
    ![Erstellte
    Website](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)
    
    Wenige Sekunden später wird der Assistent **Web veröffentlichen**
    geöffnet. Mit dem Assistenten wird ein neues
    *Veröffentlichungsprofil* erstellt, das Einstellungen wie die
    Website-URL enthält, die Visual Studio benötigt, um das Projekt für
    Azure bereitzustellen. Das Profil wird automatisch gespeichert,
    damit das Projekt problemlos auf derselben Website erneut
    bereitgestellt werden kann, wenn Sie Änderungen daran vornehmen.

2.  Klicken Sie im Assistenten **Web veröffentlichen** auf der
    Registerkarte **Verbindung** auf **Validate Connection**, um
    sicherzustellen, dass von Visual Studio eine Verbindung mit Azure
    hergestellt und das Webprojekt bereitgestellt werden kann.
    
    ![Verbindung
    prüfen](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche
    **Validate Connection** ein grünes Häkchen angezeigt.

3.  Klicken Sie auf **Weiter**.
    
    ![Erfolgreich geprüfte
    Verbindung](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

4.  Klicken Sie auf der Registerkarte **Einstellungen** auf **Weiter**.
    
    ![Registerkarte
    "Einstellungen"](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    Sie können die Standardeinstellungen auf dieser Registerkarte
    annehmen. Sie stellen einen Build für die Veröffentlichung bereit
    und müssen keine Dateien auf dem Zielserver löschen, die Anwendung
    nicht vorkompilieren oder Dateien im Ordner **App\_Data**
    ausschließen. Im Abschnitt [Nächste Schritte](#next-steps) am Ende
    des Lernprogramms finden Sie eine Verknüpfung zu einem Lernprogramm,
    in dem ein Debugbuild bereitgestellt und demonstriert wird, wie
    Visual Studio remote im Debugmodus ausgeführt wird.

5.  Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau
    starten**.
    
    ![Schaltfläche "Vorschau starten" auf der
    Registerkarte
    "Vorschau"](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf
    den Server kopiert werden. Die Anzeige der Vorschau ist nicht
    erforderlich, um die Anwendung zu veröffentlichen, es handelt sich
    dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt.

6.  Klicken Sie auf **Veröffentlichen**.
    
    ![Dateiausgabe nach "Vorschau
    starten"](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    Die Dateien werden von Visual Studio auf den Azure-Server kopiert.
    
    In den Fenstern **Ausgabe** und **Web Publish Activity** wird
    angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es
    wird die erfolgreiche Durchführung der Bereitstellung gemeldet.
    
    ![Fenster "Ausgabe" mit Meldung der erfolgreichen
    Bereitstellung](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)
    
    Nach der erfolgreichen Bereitstellung wird die URL der
    bereitgestellten Website automatisch im Standardbrowser geöffnet,
    und die erstellte Anwendung wird jetzt in der Cloud ausgeführt. Die
    URL in der Adressleiste des Browsers zeigt, dass die Website aus dem
    Internet geladen wird.
    
    ![In Azure ausgeführte
    Website](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

7.  Schließen Sie den Browser.

## Vornehmen von Änderungen und erneutes Bereitstellen

In diesem optionalen Abschnitt des Lernprogramms können Sie das Webprojekt ändern, das Projekt lokal auf dem Entwicklungscomputer ausführen, um die Änderung zu prüfen, und sie dann für Azure bereitstellen.

1.  Wenn Sie ein MVC-Projekt erstellt haben, öffnen Sie die Datei
    *Views/Home/Index.cshtml* oder *.vbhtml* im Projektmappen-Explorer,
    ändern Sie die Überschrift **h1** von "ASP.NET" in "ASP.NET and
    Azure", und speichern Sie die Datei.
    
    ![MVC
    index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)
    
    ![Änderung von "h1" bei
    MVC](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

2.  Wenn Sie ein Web Forms-Projekt erstellt haben, öffnen Sie die Datei
    *Default.aspx* im Projektmappen-Explorer, ändern Sie die Überschrift
    **h1** von "ASP.NET" in "ASP.NET and Azure", und speichern Sie die
    Datei.
    
    ![Web Forms
    default.aspx](./media/web-sites-dotnet-get-started-vs2013/default.png)
    
    ![Änderung von "h1" bei Web
    Forms](./media/web-sites-dotnet-get-started-vs2013/wfandazure.png)

3.  Drücken Sie STRG+F5, um die Änderung zu testen, indem Sie die
    Website auf dem lokalen Computer ausführen.
    
    ![Lokal ausgeführte
    Website](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)
    
    Anhand der URL `http://localhost` können Sie erkennen, dass die
    Website auf dem lokalen Computer ausgeführt wird. Standardmäßig
    erfolgt die Ausführung in IIS Express, einer im Umfang
    eingeschränkten Version von IIS, die für die Entwicklung von
    Webanwendungen konzipiert wurde.

4.  Schließen Sie den Browser.

5.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf
    das Projekt, und wählen Sie **Veröffentlichen** aus.
    
    !["Veröffentlichen"
    auswählen](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)
    
    Die Registerkarte "Vorschau" im Assistenten **Web veröffentlichen**
    wird angezeigt. Wenn Sie Veröffentlichungseinstellungen ändern
    mussten, können Sie eine andere Registerkarte auswählen, nun ist es
    jedoch völlig ausreichend, die erneute Bereitstellung mit denselben
    Einstellungen auszuführen.

6.  Klicken Sie im Assistenten **Web veröffentlichen** auf
    **Veröffentlichen**.
    
    ![Auf "Veröffentlichen"
    klicken](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)
    
    Das Projekt wird von Visual Studio für Azure bereitgestellt, und die
    Website wird im Standardbrowser geöffnet.
    
    ![Geänderte Website
    bereitgestellt](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

Wenn Sie keine Veröffentlichungseinstellungen ändern, kann die erneute Bereitstellung sogar noch schneller über die Symbolleiste
**Webveröffentlichung mit einem Klick** erfolgen.

![Symbolleiste "Webveröffentlichung mit einem
Klick"](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

Die Symbolleiste ist standardmäßig nicht aktiviert, sie wird im Menü
**Ansicht -- Symbolleisten** aktiviert. Damit können Sie ein Profil
auswählen, auf eine Schaltfläche klicken, um zu veröffentlichen, oder auf eine Schaltfläche klicken, um den Assistenten **Web veröffentlichen** zu öffnen.

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine einfache Webanwendung erstellt und auf einer Azure-Website bereitgestellt wird. In den folgenden Themen und Ressourcen finden Sie weitere Informationen dazu:

* Andere Möglichkeiten für die Bereitstellung eines Webprojekts
* Verwalten und Skalieren einer Website und Beheben von Problemen
* Hinzufügen von Datenbank- und Autorisierungsfunktionalität
* Treffen einer Auswahl zwischen Websites, Cloud Services und VMs für
  Webanwendungen

### Andere Möglichkeiten für die Bereitstellung eines Webprojekts

In diesem Lernprogramm wurde die schnellste Methode zum Erstellen und Bereitstellen einer Website in einem einzigen Vorgang erläutert. Eine
Übersicht über andere Möglichkeiten der Bereitstellung mit Visual Studio
oder durch die [Automatisierung der Bereitstellung][2] über ein
[Quellcode-Verwaltungssystem][3] finden Sie unter [Bereitstellen einer
Azure-Website](/de-de/documentation/articles/web-sites-deploy/").

Die Bereitstellung kann u. a. durch die Verwendung von Windows PowerShell-Skripten automatisiert werden. Visual Studio und Azure vereinfachen diese Aufgabe durch die Generierung von PowerShell-Skripten, mit denen Sie dieselben Bereitstellungsvorgänge ausführen können wie in Visual Studio. Weitere Informationen finden Sie unter [Automate Everything (Building Real-World Cloud Apps with Azure)][4] (in englischer Sprache).

### Verwalten einer Website

Das [Azure-Verwaltungsportal](en-us/services/management-portal/) ist eine Weboberfläche, die das Verwalten und Überwachen Ihrer Azure-Dienste ermöglicht, z. B. Websites, Clouddienste, virtuelle Computer und Datenbanken. Informationen zu den Möglichkeiten des Portals finden Sie unter [https://manage.windowsazure.com](). Melden Sie sich mit dem Benutzernamen und Kennwort für ein Konto mit Administratorberechtigungen für Ihr Azure-Abonnement an. Weitere Informationen finden Sie unter [How to Manage Web Sites](/de-de/manage/services/web-sites/how-to-manage-websites/)
(Verwalten von Websites, in englischer Sprache).

Außerdem können Sie einige Website-Verwaltungsfunktionen direkt über den Server-Explorer in Visual Studio ausführen. Informationen zu den möglichen Aktionen im Server-Explorer finden Sie unter
[Problembehandlung von Azure-Websites in Visual
Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Skalieren einer Website

Wenn es sich um eine öffentliche Website handelt und nach und nach mehr Datenverkehr auftritt, können die Reaktionszeiten nachlassen. Um dies zu verhindern, können Sie auf der Registerkarte **Skalierung** im Verwaltungsportal einfach Serverressourcen hinzufügen. Weitere Informationen finden Sie unter [Skalieren von Websites](/de-de/manage/services/web-sites/how-to-scale-websites/). (Das Hinzufügen von Serverressourcen zum Skalieren einer Website ist nicht kostenfrei.)

### Problembehandlung für eine Website

Bei der Problembehandlung kann eine Einbeziehung der Ausgabe der Ablaufverfolgung oder Protokolle hilfreich sein. Visual Studio enthält integrierte Tools, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die Ausführung in Azure remote im Debugmodus möglich. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Hinzufügen von Datenbank- und Autorisierungsfunktionalität

Die meisten Produktionswebsites verwenden eine Datenbank und schränken einige Websitefunktionen auf bestimmte autorisierte Benutzer ein. Ein Lernprogramm mit den ersten Schritten bezüglich Datenbankzugriff, Authentifizierung und Autorisierung finden Sie unter [ Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website](/de-de/develop/net/tutorials/web-site-with-sql-database/).

### Entscheiden, ob die Anwendung in einem Clouddienst ausgeführt werden soll

In Azure können Sie Webanwendungen in Websites ausführen, die denen in diesem Lernprogramm ähneln, oder in Clouddiensten oder virtuellen Computern. Weitere Informationen finden Sie unter
[Azure-Ausführungsmodelle](/de-de/develop/net/fundamentals/compute/) und
[Azure-Websites, Cloud Services und VMs: Wann eignet sich welche
Komponente?](/de-de/manage/services/web-sites/choose-web-app-service/)



[1]: http://www.asp.net/get-started/websites
[2]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
[3]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything