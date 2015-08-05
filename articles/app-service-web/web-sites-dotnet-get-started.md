<properties
	pageTitle="Erstellen einer ASP.NET-Web-App in Azure App Service"
	description="In diesem Lernprogramm erfahren Sie, wie Sie ein ASP.NET-Webprojekt in Visual Studio 2013 erstellen und in einer Web-App in Azure App Service bereitstellen. In weniger als 15 Minuten ist eine App betriebsbereit und wird in der Cloud ausgeführt."
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="06/26/2015"
	ms.author="tdykstra"/>

# Erstellen von ASP.NET-Web-Apps in Azure App Service

## Übersicht

In diesem Lernprogramm wird die Erstellung einer ASP.NET-Webanwendung und deren Bereitstellung in einer [App Service-Web-App](http://go.microsoft.com/fwlink/?LinkId=529714) mit Visual Studio 2013 oder Visual Studio 2013 for Web Express gezeigt. Das Lernprogramm geht davon aus, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss des Lernprogramms verfügen Sie über eine einfache Webanwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
* Erstellen eines Visual Studio ASP.NET-Webprojekts und Bereitstellen für eine Azure-Web-App
* Vornehmen von Änderungen am Webprojekt und erneute Bereitstellung der Anwendung
* Verwenden des [Azure-Portals](http://go.microsoft.com/fwlink/?LinkId=529715) zum Überwachen und Verwalten der Web-App

Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können:

* Sie können[ kostenlos ein Azure-Konto erstellen](/pricing/free-trial/?WT.mc_id=A261C142F) - Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste (z. B. App Service-Web-Apps) nutzen, wenn das Guthaben aufgebraucht ist.
* Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste nutzen können.</li>

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Web-App-Startseite](./media/web-sites-dotnet-get-started/deployedandazure.png)

##<a name="video"></a>Anmelden bei Microsoft Azure (Video)

In diesem Video zeigt Scott Hanselman, wie einfach Sie sich für eine kostenlose Testversion von Microsoft Azure anmelden können. (Dauer: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

## Erstellen einer ASP.NET-Webanwendung

Zunächst erstellen Sie ein Webanwendungsprojekt. Die Azure-Web-App, in der Sie Ihr Projekt später veröffentlichen, wird automatisch von Visual Studio erstellt.

1. Öffnen Sie Visual Studio 2013 oder Visual Studio 2013 Express for Web.

2. Klicken Sie im Menü **Datei** auf **Neues Projekt**.

3. Klicken Sie im Dialogfeld **Neues Projekt** auf **C#** > **Web** > **ASP.NET-Webanwendung**. Bei Bedarf können Sie auch **Visual Basic** verwenden.

3. Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.

4.  [Application Insights](app-insights-overview.md) überwacht Ihre Web-App auf Verfügbarkeit, Leistung und Nutzung. Deaktivieren Sie das Kontrollkästchen **Add Application Insights to Project**, wenn Sie die Funktion nicht ausprobieren möchten.

4. Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **MVC** aus. Wenn Sie lieber mit ASP.NET-Webformularen arbeiten, können Sie auch die Vorlage **Webformulare** auswählen.

	[MVC und Webformulare](http://www.asp.net/get-started/websites) sind ASP.NET-Frameworks zum Entwickeln von Web-Apps. In diesem Lernprogramm können Sie eines der beiden auswählen, wenn Sie jedoch Webformulare verwenden, müssen Sie später *Default.aspx* bearbeiten, wenn im Lernprogramm auf die Bearbeitung von *Index.cshtml* hingewiesen wird.

7. Klicken Sie auf **Authentifizierung ändern**.

	![Dialogfeld "Neues ASP.NET-Projekt"](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

	![Keine Authentifizierung](./media/web-sites-dotnet-get-started/GS13noauth.png)

	Die von Ihnen erstellte Beispielanwendung erlaubt keine Anmeldung durch Benutzer. Der Abschnitt [Nächste Schritte](#next-steps) verlinkt auf ein Lernprogramm, in dem die Authentifizierung und Autorisierung implementiert werden.

5. Lassen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Einstellungen für **Microsoft Azure** unverändert, und klicken Sie dann auf **OK**.

	![Dialogfeld "New ASP.NET Project"](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	Mit den Standardeinstellungen wird angegeben, dass eine Azure-Web-App für das Webprojekt von Visual Studio erstellt wird. Im nächsten Abschnitt dieses Lernprogramms stellen Sie das Webprojekt für die neu erstellte Web-App bereit.

5. Falls Sie sich noch nicht bei Azure angemeldet haben, werden Sie von Visual Studio dazu aufgefordert, dies nachzuholen. Melden Sie sich mit der ID und dem Kennwort des Kontos an, mit dem Sie Ihr Azure-Abonnement verwalten.

	Wenn Sie angemeldet sind, werden Sie im Dialogfeld **Configure Microsoft Azure Web App Settings** gefragt, welche Ressourcen Sie erstellen möchten.

	![Bei Azure angemeldet](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. Belassen Sie im Dialogfeld **Configure Microsoft Azure Web App Settings** den Standardwert unverändert.

	Sie können bei Bedarf einen anderen **Web-App-Namen** angeben, dieser muss in der Domäne *azurewebsites.net* jedoch eindeutig sein. Der von Visual Studio bereitgestellte Standardname ist eindeutig.

	Dieser Name wird von Azure als Präfix für die URL Ihrer Anwendung verwendet. Die vollständige URL besteht aus diesem Namen und *.azurewebsites.net* (wie neben dem Textfeld **Web-App-Name** angezeigt). Für den Namen `MyExample6442` lautet die URL z. B. `MyExample6442.azurewebsites.net`. Die URL muss eindeutig sein. Wenn eine andere Person die eingegebene URL bereits verwendet hat, wird rechts ein rotes Ausrufungszeichen anstelle eines grünen Häkchens angezeigt, und Sie müssen einen anderen Namen eingeben.

4. Wählen Sie in der Dropdownliste **App Service plan** die Option **Create new App Service plan** aus.

	Der Abschnitt [Nächste Schritte](#next-steps) am Ende dieses Lernprogramms enthält Links zu weiteren Informationen über App Service-Pläne.

5. Geben Sie *MyExamplePlan* oder einen Namen Ihrer Wahl für den Plan ein.

6. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Option **Neue Ressourcengruppe erstellen** aus.

	Der Abschnitt [Nächste Schritte](#next-steps) enthält Links zu Informationen über Ressourcengruppen.

5. Geben Sie *MyExampleResourceGroup* oder einen anderen Namen für die Ressourcengruppe ein.

5. Wählen Sie in der Dropdownliste **Region** den am nächsten befindlichen Ort aus.

	Mit dieser Einstellung wird das Azure-Datencenter angegeben, in dem Ihre Web-App ausgeführt wird. In diesem Lernprogramm können Sie ohne spürbaren Unterschied eine beliebige Region auswählen, aber für eine Produktions-Web-App sollte sich der Webserver so nah wie möglich an den Browsern befinden, die auf die Website zugreifen, um die [Latenzzeiten](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) zu minimieren.

5. Verändern Sie das Datenbankfeld nicht.

	Für dieses Lernprogramm verwenden Sie keine Datenbank. Der Abschnitt [Nächste Schritte](#next-steps) am Ende des Lernprogramms ist mit einem Lernprogramm verknüpft, in dem die Verwendung einer Datenbank erläutert wird.

6. Klicken Sie auf **OK**.

	![Bei Azure angemeldet](./media/web-sites-dotnet-get-started/configuresitesettings2.png)

	Das Webprojekt wird von Visual Studio innerhalb weniger Sekunden in dem angegebenen Ordner erstellt. Auch die Web-App wird in der angegebenen Azure-Region erstellt.

	Im Fenster **Projektmappen-Explorer** werden die Dateien und Ordner in dem neuen Projekt angezeigt.

	![Projektmappen-Explorer](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	Im Fenster **Web Publish Activity** wird angegeben, dass die Web-App erstellt wurde.

	![Web-App erstellt](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	Und Sie können die Web-App im Server-Explorer sehen.

	![Web-App erstellt](./media/web-sites-dotnet-get-started/siteinse.png)

## Bereitstellen der Anwendung in Azure

7. Klicken Sie im Fenster **Web Publish Activity** auf **Publish MyExample to this Web App now**.

	![Web-App erstellt](./media/web-sites-dotnet-get-started/GS13sitecreated.png)

	Wenige Sekunden später wird der Assistent **Web veröffentlichen** geöffnet.

	Die Einstellungen, die Visual Studio benötigt, um Ihr Projekt in Azure bereitzustellen, wurden in einem *Veröffentlichungsprofil* gespeichert. Mithilfe des Assistenten können Sie diese Einstellungen überprüfen und ändern.

8. Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Verbindung** auf **Validate Connection**, um sicherzustellen, dass von Visual Studio eine Verbindung mit Azure hergestellt und das Webprojekt bereitgestellt werden kann.

	![Verbindung prüfen](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

	Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche **Validate Connection** ein grünes Häkchen angezeigt.

9. Klicken Sie auf **Weiter**.

	![Erfolgreich geprüfte Verbindung](./media/web-sites-dotnet-get-started/GS13ValidateConnectionSuccess.png)

10. Klicken Sie auf der Registerkarte **Einstellungen** auf **Weiter**.

	![Registerkarte "Einstellungen"](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

	Sie können die Standardwerte für **Konfiguration** und **File Publish Options** übernehmen.

	Mithilfe der Dropdownliste **Konfiguration** können Sie einen Debugbuild für das Remotedebuggen bereitstellen. Im Abschnitt [Nächste Schritte](#next-steps) Sie eine Verknüpfung zu einem Lernprogramm, in dem demonstriert wird, wie Visual Studio remote im Debugmodus ausgeführt wird.

	Wenn Sie die **File Publish Options** erweitern, sehen Sie mehrere Einstellungen, mit deren Hilfe Sie Szenarien handhaben können, die in diesem Lernprogramm nicht erläutert sind:

	* Zusätzliche Dateien am Zielort entfernen.

		Löscht Dateien auf dem Server, die sich nicht in Ihrem Projekt befinden. Dies kann hilfreich sein, wenn Sie ein Projekt in einer Web-App bereitstellen, in der Sie zuvor ein anderes Projekt bereitgestellt haben.

	* Während der Veröffentlichung vorkompilieren.

		Dies kann die Anlaufzeiten von ersten Anfragen für große Anwendungen reduzieren.

	* Dateien im Ordner "App_Data" ausschließen.

		Zum Testen benötigen Sie manchmal eine SQL Server-Datenbankdatei in App_Data, die Sie in der Produktion nicht bereitstellen möchten.

11. Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau starten**.

	![Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"](./media/web-sites-dotnet-get-started/GS13Preview.png)

	Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf den Server kopiert werden. Die Anzeige der Vorschau ist nicht erforderlich, um die Anwendung zu veröffentlichen, es handelt sich dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt.

12. Klicken Sie auf **Veröffentlichen**.

	![Dateiausgabe nach "Vorschau starten"](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Die Dateien werden von Visual Studio auf den Azure-Server kopiert.

	In den Fenstern **Ausgabe** und **Web Publish Activity** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

	![Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung](./media/web-sites-dotnet-get-started/PublishOutput.png)

	Nach der erfolgreichen Bereitstellung wird die URL der bereitgestellten Web-App automatisch im Standardbrowser geöffnet, und die erstellte Anwendung wird jetzt in der Cloud ausgeführt. Die URL in der Adressleiste des Browsers gibt an, dass die Web-App aus dem Internet geladen wird.

	![In Azure ausgeführte Web-App](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

13. Schließen Sie den Browser.

## Vornehmen von Änderungen und erneutes Bereitstellen

In diesem Abschnitt des Lernprogramms ändern Sie die Überschrift **h1** der Startseite, führen das Projekt lokal auf dem Entwicklungscomputer aus, um die Änderung zu prüfen, und stellen diese dann auf Azure bereit.

2. Öffnen Sie die Datei *Views/Home/Index.cshtml* oder *.vbhtml* im **Projektmappen-Explorer**, ändern Sie die Überschrift **h1** von "ASP.NET" in "ASP.NET and Azure", und speichern Sie die Datei.

	![MVC index.cshtml](./media/web-sites-dotnet-get-started/index.png)

	![Änderung von "h1" bei MVC](./media/web-sites-dotnet-get-started/mvcandazure.png)

1. Drücken Sie STRG+F5, um die geänderte Überschrift anzuzeigen, indem Sie die Web-App auf dem lokalen Computer ausführen.

	![Lokal ausgeführte Web-App](./media/web-sites-dotnet-get-started/localandazure.png)

	Die URL `http://localhost` zeigt an, dass die Web-App auf dem lokalen Computer ausgeführt wird. Standardmäßig erfolgt die Ausführung in IIS Express, einer im Umfang eingeschränkten Version von IIS, die für die Entwicklung von Webanwendungen konzipiert wurde.

1. Schließen Sie den Browser.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

	!["Veröffentlichen" auswählen](./media/web-sites-dotnet-get-started/choosepublish.png)

	Die Registerkarte "Vorschau" im Assistenten **Web veröffentlichen** wird angezeigt. Wenn Sie Veröffentlichungseinstellungen ändern mussten, können Sie eine andere Registerkarte auswählen, nun ist es jedoch völlig ausreichend, die erneute Bereitstellung mit denselben Einstellungen auszuführen.

2. Klicken Sie im Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

	![Auf "Veröffentlichen" klicken](./media/web-sites-dotnet-get-started/clickpublish.png)

	Das Projekt wird von Visual Studio für Azure bereitgestellt, und die Web-App wird im Standardbrowser geöffnet.

	![Geänderte Web-App bereitgestellt](./media/web-sites-dotnet-get-started/deployedandazure.png)

**Tipp:** Für eine noch schnellere Bereitstellung können Sie die Symbolleiste **Webveröffentlichung mit einem Klick** aktivieren. Klicken Sie auf **Ansicht** > **Symbolleisten**, und wählen Sie dann **Webveröffentlichung mit einem Klick** aus. Über diese Symbolleiste können Sie ein Profil auswählen, auf eine Schaltfläche klicken, um zu veröffentlichen, oder auf eine Schaltfläche klicken, um den Assistenten **Web veröffentlichen** zu öffnen.

![Symbolleiste "Webveröffentlichung mit einem Klick"](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Überwachen und Verwalten der Web-App im Azure-Verwaltungsportal

Das [Azure-Verwaltungsportal](/services/management-portal/) ist eine Weboberfläche, die das Verwalten und Überwachen Ihrer Azure-Dienste ermöglicht, z. B. der soeben erstellten Web-App. In diesem Abschnitt des Lernprogramms erfahren Sie, was Sie im Portal durchführen können.

1. Rufen Sie in Ihrem Browser [https://portal.azure.com]() auf, und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

2. Klicken Sie auf **Durchsuchen > Web-Apps** und dann auf den Namen der Web-App.

	Auf dem Blatt **Web-App** für Ihre Web-App wird eine Übersicht der Nutzungsstatistiken und Links zu häufig verwendeten Web-App-Verwaltungsfunktionen angezeigt.

	![Blatt der Web-App](./media/web-sites-dotnet-get-started/portaldashboard.png)-->

	Bisher gab es in Ihrer Web-App kaum Datenverkehr, daher wird in der Übersicht möglicherweise nichts angezeigt. Wenn Sie Ihre Anwendung aufrufen, aktualisieren Sie die Seite mehrmals, und aktualisieren Sie anschließend die Portalseite. Nun sollte eine Statistik angezeigt werden.

3. Klicken Sie auf **Alle Einstellungen**, um weitere Optionen zum Konfigurieren der Web-App anzuzeigen.

	Es wird eine Liste der verschiedenen Einstellungstypen angezeigt.

	![](./media/web-sites-dotnet-get-started/portalconfigure1.png)-->

4. Klicken Sie auf **Anwendungseinstellungen**, um ein Beispiel für die verschiedenen Einstellungen anzuzeigen, die Sie im Portal konfigurieren können.

	Sie können beispielsweise die für die Web-App verwendete .NET-Version festlegen, Funktionen wie [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) aktivieren, die [Werte für Verbindungszeichenfolgen](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) festlegen und vieles mehr.

	![Registerkarte "Konfigurieren" der Web-App im Portal](./media/web-sites-dotnet-get-started/portalconfigure2.png)

Dies sind nur einige Funktionen des Verwaltungsportals. Sie können neue Web-Apps erstellen, vorhandene Web-Apps löschen, Web-Apps anhalten und neu starten und andere Azure-Dienste verwalten, z. B. Datenbanken und virtuelle Computer.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine einfache Webanwendung erstellt und in einer Azure-Web-App bereitgestellt wird. In den folgenden Themen und Ressourcen finden Sie weitere Informationen dazu.

* Andere Möglichkeiten für die Bereitstellung eines Webprojekts

	In diesem Lernprogramm wurde die schnellste Methode zum Erstellen und Bereitstellen einer Web-App in einem einzigen Vorgang erläutert. Eine Übersicht über andere Möglichkeiten der Bereitstellung mit Visual Studio oder durch die [Automatisierung der Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) über ein [Quellcode-Verwaltungssystem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) finden Sie unter [Bereitstellen von Azure-Web-Apps](web-sites-deploy.md).

	Visual Studio kann auch Windows PowerShell-Skripte generieren, mit deren Hilfe Sie die Bereitstellung automatisieren können. Weitere Informationen finden Sie unter [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) (in englischer Sprache).

* Verwalten von Web-Apps in Visual Studio

	Informationen zu den verfügbaren Web-App-Verwaltungsfunktionen im **Server-Explorer** finden Sie unter [Problembehandlung von Azure-Web-Apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Problembehandlung für eine Web-App

	Visual Studio enthält Funktionen, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die Ausführung in Azure remote im Debugmodus möglich. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Web-Apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Hinzufügen von Datenbank- und Autorisierungsfunktionalität

	Ein Lernprogramm mit Anleitungen bezüglich Datenbankzugriff und Einschränkung einiger Anwendungsfunktionen auf autorisierte Benutzer finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Web-App](/develop/net/tutorials/web-site-with-sql-database/).

* So fügen Sie einen benutzerdefinierten Domänennamen und SSL hinzu

	Informationen zur Verwendung von SSL und einer eigenen Domäne (z. B. www.contoso.com statt contoso.azurewebsites.net) finden Sie in den folgenden Ressourcen:

	* [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](web-sites-custom-domain-name.md)
	* [Aktivieren von HTTPS für Azure-Web-Apps](web-sites-configure-ssl-certificate.md)

* Vermeiden von Wartezeiten nach Leerlaufzeiten

	Standardmäßig werden Web-Apps entladen, wenn sie einige Zeit im Leerlauf waren. Bei der ersten Anfrage danach muss gewartet werden, bis die Web-App neu geladen wurde. Zur Vermeidung von Wartezeiten können Sie die AlwaysOn-Funktion aktivieren. Weitere Informationen dazu finden Sie unter den Konfigurationsoptionen in [Konfigurieren von Web-Apps](web-sites-configure.md).

* Hinzufügen von Echtzeitfunktionen wie Chat

	Wenn Ihre Web-App Echtzeitfunktionen (Chat-Dienste, Spiele, Börsenticker usw.) enthalten soll, erhalten Sie die beste Performance durch die Verwendung von [ASP.NET SignalR](http://www.asp.net/signalr) mit der Transportmethode [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Weitere Informationen finden Sie unter [Using SignalR with Web Apps in Azure App Service](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites) (in englischer Sprache).

* Auswählen zwischen App Service, Cloud Services und VMs für Webanwendungen

	In Azure können Sie Webanwendungen in App Service-Web-Apps (wie in diesem Lernprogramm gezeigt), in Cloud Services oder in Virtual Machines ausführen. Weitere Informationen finden Sie unter [Azure-Ausführungsmodelle](/develop/net/fundamentals/compute/) und [Vergleich von Azure-Websites, Cloud Services und Virtual Machines](/manage/services/web-sites/choose-web-app-service/).

* [Auswählen oder Erstellen eines App Service-Plans](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

* [Auswählen oder Erstellen einer Ressourcengruppe](../azure-preview-portal-using-resource-groups.md)



## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO4-->