<properties 
	pageTitle="Erste Schritte mit Azure-Websites für ASP.NET" 
	description="Dieses Lernprogramm zeigt Ihnen das Erstellen eines ASP-NET-Webprojekts in Visual Studio 2013 und die Bereitstellung für eine Azure-Website. In weniger als 15 Minuten ist eine App betriebsbereit und wird in der Cloud ausgeführt." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/12/2014" 
	ms.author="tdykstra"/>

# Erste Schritte mit Azure-Websites und ASP.NET

In diesem Lernprogramm wird die Erstellung einer ASP.NET-Webanwendung und Bereitstellung für eine Microsoft Azure-Website mit Visual Studio 2013 oder Visual Studio 2013 for Web Express gezeigt. Das Lernprogramm geht davon aus, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss des Lernprogramms verfügen Sie über eine einfache Webanwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
* Erstellen eines Visual Studio ASP.NET-Webprojekts und Bereitstellen für eine Azure-Website
* Vornehmen von Änderungen am Webprojekt und erneute Bereitstellung der Anwendung
* Verwenden des Azure-Verwaltungsportals zum Überwachen und Verwalten der Website

Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm durchführen zu können:

* Sie können [ein Azure-Konto kostenlos erstellen](/de-de/pricing/free-trial/?WT.mc_id=A261C142F) - Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlose Azure-Dienste (z. B. Websites) nutzen, wenn das Guthaben aufgebraucht ist.
* Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.</li>

> [AZURE.NOTE]
> Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen.

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### Teile des Lernprogramms

* [Anmelden bei Microsoft Azure (Video)](#video)
* [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment)
* [Erstellen einer ASP.NET-Webanwendung in Visual Studio](#create-an-aspnet-web-application)
* [Bereitstellen der Anwendung in Azure](#deploy-the-application-to-azure)
* [Vornehmen von Änderungen und erneutes Bereitstellen](#make-a-change-and-redeploy)
* [Überwachen und Verwalten der Website im Azure-Verwaltungsportal](#monitor-and-manage-the-site-in-the-management-portal)
* [Nächste Schritte](#next-steps)

##<a name="video"></a>Anmelden bei Microsoft Azure (Video)

In diesem Video zeigt Scott Hanselman, wie einfach die Anmeldung für einen kostenlosen Test von Microsoft Azure ist. (Dauer: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Erstellen einer ASP.NET-Webanwendung

Zunächst erstellen Sie ein Webanwendungsprojekt. Die Azure-Website, auf der Sie Ihr Projekt später veröffentlichen, wird automatisch von Visual Studio erstellt. In der folgenden Abbildung ist der Ablauf in zwei Schritten dargestellt.

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. Öffnen Sie Visual Studio 2013 oder Visual Studio 2013 Express for Web.

2. Klicken Sie im Menü **Datei** auf **Neues Projekt**.

3. Klicken Sie im Dialogfeld **Neues Projekt** auf **C#** > **Web** > **ASP.NET-Webanwendung**. Bei Bedarf können Sie auch **Visual Basic** verwenden.

3. Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.

4. Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **MVC** aus. Wenn Sie lieber mit ASP.NET Web Forms arbeiten, können Sie auch die Vorlage **Web Forms** auswählen. 

	[MVC und Web Forms](http://www.asp.net/get-started/websites) sind ASP.NET-Frameworks zum Entwickeln von Websites. In diesem Lernprogramm können Sie eines der beiden auswählen, wenn Sie jedoch Web Forms verwenden, müssen Sie später *Default.aspx* bearbeiten, wenn im Lernprogramm auf die Bearbeitung von *Index.cshtml* hingewiesen wird.

7. Klicken Sie auf **Authentifizierung ändern**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	Die von Ihnen erstellte Beispielanwendung erlaubt keine Anmeldung durch Benutzer. Der Abschnitt [Nächste Schritte](#next-steps) verlinkt auf ein Lernprogramm, in dem die Authentifizierung und Autorisierung implementiert wird.

5. Lassen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Einstellungen unter **Azure** unverändert, und klicken Sie dann auf **OK**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	Mit den Standardeinstellungen wird angegeben, dass eine Azure-Website für das Webprojekt von Visual Studio erstellt wird. Im nächsten Abschnitt dieses Lernprogramms stellen Sie das Webprojekt für die neu erstellte Website bereit.

	(Die Überschrift des Kontrollkästchens kann **Host in der Cloud** oder **Remoteressourcen erstellen** lauten. Die Wirkung ist in beiden Fällen die gleiche.)
	
5. Falls Sie sich noch nicht bei Azure angemeldet haben, werden Sie von Visual Studio dazu aufgefordert, dies nachzuholen. Klicken Sie auf **Anmelden**.

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. Geben Sie im Dialogfeld **Bei Azure anmelden** die ID und das Kennwort des Kontos ein, mit dem Sie Ihr Azure-Abonnement verwalten.
	
	Wenn Sie angemeldet sind, werden Sie im Dialogfeld **Azure-Websiteeinstellungen konfigurieren** gefragt, welche Ressourcen Sie erstellen möchten.

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio gibt eine Standardbezeichnung für **Websitename** an, die von Azure als Präfix der Anwendungs-URL verwendet wird. Geben Sie ggf. einen anderen Websitenamen ein.

	Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und *.azurewebsites.net* (wie neben dem Textfeld **Websitename** angezeigt). Wenn der Websitename z. B. `MyExample6442`, the URL will be `MyExample6442.azurewebsites.net` lautet. Die URL muss eindeutig sein. Wenn eine andere Person die eingegebene URL bereits verwendet hat, wird rechts ein rotes Ausrufungszeichen anstelle eines grünen Häkchens angezeigt, und Sie müssen einen anderen Websitenamen eingeben.

4. Wählen Sie in der Dropdownliste **Region** den am nächsten befindlichen Ort aus.

	Mit dieser Einstellung wird das Azure-Datencenter angegeben, in dem Ihre Website ausgeführt wird. Für dieses Lernprogramm können Sie eine beliebige Region auswählen, ohne dass es einen spürbaren Unterschied gibt, aber für eine Produktionswebsite sollte sich der Webserver so nah wie möglich an den Browsern befinden, die auf die Website zugreifen, um die [Latenzzeiten](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) zu minimieren.

5. Verändern Sie die Datenbankfelder nicht.

	Für dieses Lernprogramm verwenden Sie keine Datenbank. Der Abschnitt [Nächste Schritte](#next-steps) am Ende des Lernprogramms ist mit einem Lernprogramm verknüpft, in dem die Verwendung einer Datenbank erläutert wird.

6. Klicken Sie auf **OK**.

	Das Webprojekt wird von Visual Studio innerhalb von wenigen Sekunden in dem angegebenen Ordner erstellt. Auch die Website wird in der angegebenen Azure-Region erstellt.  

	Im Fenster **Projektmappen-Explorer** werden die Dateien und Ordner in dem neuen Projekt angezeigt.

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	Im Fenster **Webveröffentlichungsaktivität** wird angegeben, dass die Website erstellt wurde.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	Und Sie können die Website im Server-Explorer sehen.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Bereitstellen der Anwendung in Azure

7. Klicken Sie im Fenster **Webveröffentlichungsaktivität** auf **MyExample jetzt auf dieser Website veröffentlichen**.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	Wenige Sekunden später wird der Assistent **Web veröffentlichen** geöffnet. 

	Die Einstellungen, die Visual Studio benötigt, um Ihr Projekt in Azure bereitzustellen, wurden in einem Veröffentlichungsprofil, *publish profile*, gespeichert. Mithilfe des Assistenten können Sie diese Einstellungen überprüfen und ändern.

8. Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Verbindung** auf **Verbindung überprüfen**, um sicherzustellen, dass Visual Studio eine Verbindung mit Azure herstellen und das Webprojekt bereitstellen kann.

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Nachdem die Verbindung überprüft wurde, wird neben der Schaltfläche **Verbindung überprüfen** ein grünes Häkchen angezeigt. 

9. Klicken Sie auf **Weiter**.

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. Klicken Sie auf der Registerkarte **Einstellungen** auf **Weiter**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Sie können die Standardwerte für **Konfiguration** und **Dateiveröffentlichungsoptionen** übernehmen.

	Mithilfe der Dropdownliste **Konfiguration** können Sie einen Debugbuild für das Remotedebuggen bereitstellen. Im Abschnitt [Nächste Schritte](#next-steps) finden Sie eine Verknüpfung zu einem Lernprogramm, in dem demonstriert wird, wie Visual Studio remote im Debugmodus ausgeführt wird.

	Wenn Sie die **Dateiveröffentlichungsoptionen** erweitern, sehen Sie mehrere Einstellungen, mit deren Hilfe Sie Szenarien handhaben können, die in diesem Lernprogramm nicht erläutert sind:
 
	* Zusätzliche Dateien am Zielort entfernen.
	  
		Löscht Dateien auf dem Server, die sich nicht in Ihrem Projekt befinden. Dies kann hilfreich sein, wenn Sie ein Projekt auf einer Website bereitstellen, auf der Sie zuvor ein anderes Projekt bereitgestellt hatten.

	* Während der Veröffentlichung vorkompilieren. 
	 
		Dies kann die Anlaufzeiten von ersten Anfragen für große Websites reduzieren.

	* Dateien im Ordner "App_Data" ausschließen. 
	 
		Zum Testen benötigen Sie manchmal eine SQL Server-Datenbankdatei in App_Data, die Sie in der Produktion nicht bereitstellen möchten.
	
11. Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau starten**.

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf den Server kopiert werden. Die Anzeige der Vorschau ist nicht erforderlich, um die Anwendung zu veröffentlichen, es handelt sich dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt.

12. Klicken Sie auf **Veröffentlichen**.

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Die Dateien werden von Visual Studio auf den Azure-Server kopiert.

	In den Fenstern **Ausgabe** und **Webveröffentlichungsaktivität** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Nach der erfolgreichen Bereitstellung wird die URL der bereitgestellten Website automatisch im Standardbrowser geöffnet.
	Die erstellte Anwendung wird nun in der Cloud ausgeführt. Die URL in der Adressleiste des Browsers zeigt, dass die Website aus dem Internet geladen wird.

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Schließen Sie den Browser.

## Vornehmen von Änderungen und erneutes Bereitstellen

In diesem Abschnitt des Lernprogramms ändern Sie die Überschrift **h1** der Startseite, führen das Projekt lokal auf dem Entwicklungscomputer aus, um die Änderung zu prüfen, und stellen diese dann auf Azure bereit.

2. Öffnen Sie die Datei *Views/Home/Index.cshtml* oder *.vbhtml* im **Projektmappen-Explorer**, ändern Sie die Überschrift **h1** von "ASP.NET" in "ASP.NET and Azure", und speichern Sie die Datei. 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Drücken Sie STRG+F5, um die geänderte Überschrift anzuzeigen, indem Sie die Website auf dem lokalen Computer ausführen.

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	Die URL  `http://localhost` zeigt an, dass die Website auf dem lokalen Computer ausgeführt wird. Standardmäßig erfolgt die Ausführung in IIS Express, einer im Umfang eingeschränkten Version von IIS, die für die Entwicklung von Webanwendungen konzipiert wurde.


1. Schließen Sie den Browser.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie anschließend **Veröffentlichen**.

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	Die Registerkarte "Vorschau" im Assistenten **Web veröffentlichen** wird angezeigt. Wenn Sie Veröffentlichungseinstellungen ändern mussten, können Sie eine andere Registerkarte auswählen, nun ist es jedoch völlig ausreichend, die erneute Bereitstellung mit denselben Einstellungen auszuführen.

2. Klicken Sie im Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Das Projekt wird von Visual Studio für Azure bereitgestellt, und die Website wird im Standardbrowser geöffnet.

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Tipp:** Für eine noch schnellere Bereitstellung können Sie die Symbolleiste **Webveröffentlichung mit einem Klick** aktivieren. Klicken Sie auf **Ansicht** > **Symbolleisten**, und wählen Sie dann **Webveröffentlichung mit einem Klick**. Auf der Symbolleiste können Sie ein Profil auswählen oder auf eine Schaltfläche zum Veröffentlichen bzw. zum Öffnen des Assistenten **Web veröffentlichen** klicken.

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Überwachen und Verwalten der Website im Azure-Verwaltungsportal

Das [Azure-Verwaltungsportal](/de-de/services/management-portal/) ist eine Weboberfläche, die das Verwalten und Überwachen Ihrer Azure-Dienste ermöglicht, z. B. der soeben erstellten Website. In diesem Abschnitt des Lernprogramms erfahren Sie, was Sie im Portal durchführen können.

1. Rufen Sie in Ihrem Browser [http://manage.windowsazure.com](), auf, und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

	Im Portal wird eine Liste Ihrer Azure-Dienste angezeigt.

2. Klicken Sie auf den Namen Ihrer Website.

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. Klicken Sie auf die Registerkarte **Dashboard**.

	Auf der Registerkarte **Dashboard** wird eine Übersicht der Nutzungsstatistik sowie ein Link zu einer Reihe häufig verwendeter Websiteverwaltungsfunktionen angezeigt. Unter **Auf einen Blick** finden Sie außerdem einen Link zur Startseite der Anwendung.

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	Bisher gab es auf Ihrer Website kaum Datenverkehr, daher wird in der Übersicht möglicherweise nichts angezeigt. Wenn Sie Ihre Anwendung aufrufen, aktualisieren Sie die Seite mehrmals, und aktualisieren Sie anschließend die Seite **Dashboard** im Portal. Nun sollte eine Statistik angezeigt werden. Sie können weitere Details auf der Registerkarte **Überwachen** anzeigen.

4. Klicken Sie auf die Registerkarte **Konfigurieren**.

	Auf der Registerkarte [Konfigurieren](/de-de/documentation/articles/web-sites-configure//) können Sie die für die Website verwendete .NET-Version kontrollieren, Funktionen wie [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) und [Diagnoseprotokollierung](web-sites-enable-diagnostic-log.md) aktivieren, Werte für Verbindungszeichenfolgen ([connection string values](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)) festlegen usw. 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. Klicken Sie auf die Registerkarte **Skalieren**.

	Für die bezahlten Ebenen des Websitediensts können Sie auf der Registerkarte [Skalieren](web-sites-scale.md) die Größe und Anzahl der Computer bestimmen, die Ihre Webanwendung unterstützen, um Datenverkehrsschwankungen abzufangen.

	Sie können manuell skalieren oder Kriterien bzw. Zeitpläne für die automatische Skalierung konfigurieren.

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

Dies sind nur einige Funktionen des Verwaltungsportals. Sie können außerdem neue Websites erstellen, vorhandene Websites löschen, Websites anhalten und neu starten sowie andere Arten von Azure-Diensten wie Datenbanken und virtuelle Computer verwalten.  

**Tipp:** Demnächst ist ein neues Verwaltungsportal geplant, welches das von Ihnen zurzeit verwendete ersetzen wird. Weitere Informationen finden Sie im [Azure-Vorschauportal](/de-de/overview/preview-portal/).

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine einfache Webanwendung erstellt und auf einer Azure-Website bereitgestellt wird. In den folgenden Themen und Ressourcen finden Sie weitere Informationen dazu.

* Andere Möglichkeiten für die Bereitstellung eines Webprojekts

	In diesem Lernprogramm wurde die schnellste Methode zum Erstellen und Bereitstellen einer Website in einem einzigen Vorgang erläutert. Eine Übersicht über andere Möglichkeiten der Bereitstellung mit Visual Studio oder durch die [Automatisierung der Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) über ein [Quellcode-Verwaltungssystem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) finden Sie unter [Bereitstellen einer Azure-Website](/de-de/documentation/articles/web-sites-deploy/"). 

	Visual Studio kann auch Windows PowerShell-Skripte generieren, mit deren Hilfe Sie die Bereitstellung automatisieren können. Weitere Informationen finden Sie unter [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Verwalten von Websites in Visual Studio

	Informationen zu den verfügbaren Websiteverwaltungsfunktionen im **Server-Explorer** finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Problembehandlung für eine Website

	Visual Studio enthält Funktionen, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die Ausführung in Azure remote im Debugmodus möglich. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Hinzufügen von Datenbank- und Autorisierungsfunktionalität

	Ein Lernprogramm mit Anleitungen bezüglich Datenbankzugriff und Einschränkung einiger Websitefunktionen auf autorisierte Benutzer finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website](/de-de/develop/net/tutorials/web-site-with-sql-database/).

* So fügen Sie einen benutzerdefinierten Domänennamen und SSL hinzu

	Informationen zur Verwendung von SSL und einer eigenen Domäne (z. B. www.contoso.com statt contoso.azurewebsites.net) finden Sie in den folgenden Ressourcen:

	* [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](web-sites-custom-domain-name.md). 
	* [Aktivieren von HTTPS für eine Azure-Website](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)

* Vermeiden von Wartezeiten nach Leerlaufzeiten 

	Standardmäßig werden Websites entladen, wenn sie einige Zeit im Leerlauf waren. Danach muss die erste Anfrage warten, bis die Website neu geladen wurde. Zur Vermeidung von Wartezeiten können Sie die AlwaysOn-Funktion aktivieren. Weitere Informationen dazu finden Sie unter den Konfigurationsoptionen in [Konfigurieren von Websites](http://azure.microsoft.com/documentation/articles/web-sites-configure/).

* Hinzufügen von Echtzeitfunktionen wie Chat

	Falls Ihre Website Echtzeitfunktionen (wie Chat-Dienste, Spiele, Börsenticker usw.) enthalten soll, erhalten Sie die beste Performance durch die Verwendung von [ASP.NET SignalR](http://www.asp.net/signalr) mit der Transportmethode [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Weitere Informationen finden Sie unter [Verwenden von SignalR mit Microsoft Azure-Websites](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites). 

* Treffen einer Auswahl zwischen Azure-Websites, Cloud Services und VMs für Webanwendungen

	In Azure können Sie Webanwendungen in Websites ausführen, die denen in diesem Lernprogramm ähneln, oder in Clouddiensten oder virtuellen Computern. Weitere Informationen finden Sie unter [Azure-Ausführungsmodelle](/de-de/develop/net/fundamentals/compute/) und [Azure-Websites, Cloud Services und VMs: Vergleich?](/de-de/manage/services/web-sites/choose-web-app-service/).





<!--HONumber=42-->
