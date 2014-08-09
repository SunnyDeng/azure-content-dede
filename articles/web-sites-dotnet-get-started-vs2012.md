<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Erste Schritte mit Azure und ASP.NET
====================================

[Visual Studio 2013](/de-de/develop/net/tutorials/get-started/ "Visual Studio 2013")[Visual Studio 2012](/de-de/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012")

**Hinweis**

Es ist eine [neuere Version dieses Lernprogramms](/de-de/develop/net/tutorials/get-started/) verfügbar. Sie können trotzdem diese Version ausführen, wenn Sie Visual Studio 2012 verwenden möchten, sie enthält jedoch nicht alle neuen Azure SDK-Funktionen.

In diesem Lernprogramm wird die Bereitstellung einer ASP.NET-Webanwendung für eine Azure-Website mit dem Assistenten "Web veröffentlichen" in Visual Studio 2012 oder Visual Studio 2012 Express für das Web gezeigt. Wenn Sie möchten, können Sie den Schritten des Lernprogramms mit Visual Studio 2010 oder Visual Web Developer Express 2010 folgen.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2012 verfügen, wird Visual Studio 2012 Express für das Web automatisch durch das SDK installiert. Auf diese Weise können Sie völlig kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine einfache Webanwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
-   Erstellen eines Visual Studio ASP.NET MVC 4-Projekts und Veröffentlichen dieses Projekts auf einer Azure-Website

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Beispiel-Website](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

**Hinweis:** Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oder [sich für eine kostenlose Testversion registrieren](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Teile des Lernprogramms

1.  [Einrichten der Entwicklungsumgebung](#setupdevenv)
2.  [Erstellen einer Website in Azure](#setupwindowsazure)
3.  [Erstellen einer ASP.NET MVC 4-Anwendung](#createmvc4app)
4.  [Bereitstellen der Anwendung in Azure](#deploytowindowsazure)
5.  [Nächste Schritte](#nextsteps)

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

Erstellen einer WebsiteErstellen einer Website
----------------------------------------------

Im nächsten Schritt erstellen Sie die Azure-Website.

1.  Klicken Sie im [Azure-Verwaltungsportal](http://manage.windowsazure.com) auf **Websites** und dann auf **Neu**.

![Neue Website](./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png)

1.  Klicken Sie auf **Schnellerfassung**.

    ![Schnellerfassung](./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png)

2.  Geben Sie im Schritt **Website erstellen** des Assistenten eine Zeichenfolge in das Feld **URL** ein, die als eindeutige URL der Anwendung dienen soll. Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und dem Suffix, das neben dem Textfeld aufgeführt wird. In der Abbildung wird **example1** dargestellt, aber falls jemand diese Zeichenfolge bereits für eine URL verwendet, müssen Sie einen anderen Wert eingeben.

3.  Wählen Sie in der Dropdownliste **Region** den am nächsten befindlichen Ort aus. Mit dieser Einstellung wird das Datencenter angegeben, in dem Ihre Website ausgeführt wird.

4.  Klicken Sie auf den Pfeil **Website erstellen**.

    ![Neue Website erstellen](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

    Sie kehren zur Seite **Websites** im Verwaltungsportal zurück, und in der Spalte **Status** wird angegeben, dass die Website erstellt wird. Nach einer Weile (normalerweise weniger als eine Minute) wird in der Spalte **Status** angegeben, dass die Website erfolgreich erstellt wurde. In der linken Navigationsleiste wird die Anzahl der Websites in Ihrem Konto neben dem Symbol **Websites** angegeben.

    ![Seite "Websites" im Verwaltungsportal mit erstellter Website](./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png)

Erstellen der AnwendungErstellen einer ASP.NET MVC 4-Anwendung
--------------------------------------------------------------

Sie haben nun eine Azure-Website erstellt, diese enthält jedoch noch keinen Inhalt. Zunächst erstellen Sie ein Visual Studio-Webanwendungsprojekt, das Sie auf Azure veröffentlichen.

### Erstellen des Projekts

1.  Öffnen Sie Visual Studio 2012 oder Visual Studio 2012 Express für das Web.

2.  Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

	!["Neues Projekt" im Menü "Datei"](./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png)

1.  Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **C\#**, und wählen Sie unter **Installierte Vorlagen** die Option **Web** aus. Wählen Sie dann **ASP.NET MVC 4-Webanwendung** aus.

2.  Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.

3.  Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"](./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png)

1.  Wählen Sie im Dialogfeld **Neues ASP.NET MVC 4-Projekt** die Vorlage **Internetanwendung** aus, und klicken Sie auf **OK**.

	![Dialogfeld "ASP.NET MVC 4-Projekt"](./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png)

### Lokales Ausführen der Anwendung

1.  Drücken Sie die Taste **STRG**+**F5**, um die Anwendung auszuführen. Die Startseite der Anwendung wird im Standardbrowser angezeigt.

![Lokal ausgeführte Website](./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png)

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um eine einfache Anwendung zu erstellen, die Sie in Azure bereitstellen können.

Bereitstellen der AnwendungBereitstellen der Anwendung in Azure
---------------------------------------------------------------

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png)

	Der Assistent **Web veröffentlichen** wird geöffnet.

1.  Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Profil** auf **Importieren**.

    ![Veröffentlichungseinstellungen importieren](./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png)

	Das Dialogfeld **Veröffentlichungsprofil importieren** wird geöffnet.

1.  Falls Sie nicht bereits Ihr Azure-Abonnement zu Visual Studio hinzugefügt haben, führen Sie folgende Schritte aus. In diesen Schritten fügen Sie Ihr Abonnement hinzu, sodass die Dropdown-Liste unter **Aus einer Azure-Website importieren** Ihre Website enthält.

    -   Klicken Sie im Dialogfeld **Veröffentlichungsprofil importieren** auf **Aus einer Azure-Website importieren** und dann auf **Azure-Abonnement hinzufügen**.

    ![Azure-Abonnement hinzufügen](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)

    -   Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Download subscription file**.

    ![Abonnementdatei herunterladen](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)

    -   Speichern Sie im Browserfenster die Datei *.publishsettings*.

    ![Datei .publishsettings herunterladen](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

    -   Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Browse**, und navigieren Sie zur Datei *.publishsettings*.

    ![Abonnement herunterladen](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

    -   Klicken Sie auf **Importieren**.

    ![Importieren](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

    > [WACOM.NOTE] 
    > Die Datei ".publishsettings" enthält Ihre Anmeldeinformationen (unverschlüsselt), die verwendet werden, um Ihre Azure-Abonnements und -Dienste zu verwalten. Die bewährte Sicherheitsmethode für diese Datei besteht im vorübergehenden Speichern außerhalb Ihrer Quellcodeverzeichnisse (beispielsweise im Ordner "Libraries\\Documents"). Löschen Sie sie nach Abschluss des Importvorgangs. Böswillige Benutzer, die Zugriff auf die .publishsettings-Datei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.

2.  Wählen Sie im Dialogfeld **Veröffentlichungsprofil importieren** die Option **Aus einer Azure-Website importieren** und dann Ihre Website in der Dropdownliste aus, und klicken Sie auf **OK**.

    ![Import Publish Profile](./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png)

3.  Klicken Sie auf der Registerkarte **Verbindung** auf **Verbindung prüfen**, um sicherzustellen, dass die Einstellungen richtig sind.

    ![Verbindung prüfen](./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png)

4.  Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche **Verbindung prüfen** ein grünes Häkchen angezeigt. Klicken Sie auf **Weiter**.

    ![Erfolgreich geprüfte Verbindung](./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png)

5.  Deaktivieren Sie auf der Registerkarte **Einstellungen** die Option **Use this connection string at runtime**, da diese Anwendung keine Datenbank verwendet. Sie können die Standardeinstellungen für die restlichen Einstellungen auf dieser Seite übernehmen. Sie stellen eine Buildkonfiguration für die Veröffentlichung bereit und müssen keine Dateien auf dem Zielserver löschen, die Anwendung nicht vorkompilieren oder Dateien im Ordner "App\_Data" ausschließen. Klicken Sie auf **Weiter**.

    ![Registerkarte "Einstellungen"](./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png)

6.  Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau starten**.

    ![Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png)

    Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf den Server kopiert werden. Die Anzeige der Vorschau ist nicht erforderlich, um die Anwendung zu veröffentlichen, es handelt sich dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt. In diesem Fall können Sie die Dateiliste, die angezeigt wird, ignorieren.

    ![Dateiausgabe nach "Vorschau starten"](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png)

7.  Klicken Sie auf **Veröffentlichen**. Die Dateien werden von Visual Studio auf den Azure-Server kopiert.

8.  Im Fenster **Ausgabe** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

    ![Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung](./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png)

9.  Nach der erfolgreichen Bereitstellung wird die URL der bereitgestellten Website automatisch im Standardbrowser geöffnet. Die erstellte Anwendung wird nun in der Cloud ausgeführt.

    ![In Azure ausgeführte Website](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

Nächste SchritteNächste Schritte
--------------------------------

In diesem Lernprogramm haben Sie erfahren, wie eine einfache Webanwendung auf einer Azure-Website bereitgestellt wird. Es sind weitere Ressourcen verfügbar, mit denen Sie lernen können, die Site zu verwalten und zu skalieren sowie Siteprobleme zu behandeln, Datenbank-, Authentifizierungs- und Autorisierungsfunktionalität hinzuzufügen und zu entscheiden, ob die Anwendung in einem Azure-Clouddienst statt auf einer Azure-Website ausgeführt werden soll.

### Verwalten einer Website

Wenn Sie diese Website nicht mehr benötigen, können Sie sie löschen. Unter Umständen möchten Sie sie auch temporär offline nehmen oder die Websiteeinstellungen ändern. Sie können einige dieser Funktionen direkt über den **Server-Explorer** in Visual Studio ausführen.

![Azure-Websites im Server-Explorer](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Website-Konfiguration in Visual Studio](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

Sie können das Azure-Verwaltungsportal zum Löschen Ihrer Website verwenden. Auf dem folgenden Bildschirmfoto werden die Schaltflächen **Beenden**, **Neu starten** und **Löschen** auf der Registerkarte **Dashboard** des Verwaltungsportals angezeigt.

![Registerkarte "Dashboard" im Verwaltungsportal](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

Sie können viele andere Websiteeinstellungen auf der Registerkarte **Konfigurieren** ändern. Weitere Informationen finden Sie unter [Verwalten von Websites](/de-de/manage/services/web-sites/how-to-manage-websites/).

### Skalieren einer Website

Wenn es sich um eine öffentliche Website handelt und nach und nach mehr Datenverkehr auftritt, können die Reaktionszeiten nachlassen. Um dies zu verhindern, können Sie auf der Registerkarte **Skalierung** im Verwaltungsportal einfach Serverressourcen hinzufügen.

![Registerkarte "Skalierung" im Verwaltungsportal](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

Weitere Informationen finden Sie unter [Skalieren von Websites](/de-de/manage/services/web-sites/how-to-scale-websites/). (Das Hinzufügen von Serverressourcen zum Skalieren einer Website ist nicht kostenfrei.)

### Problembehandlung für eine Website

Bei der Problembehandlung kann eine Einbeziehung der Ausgabe der Ablaufverfolgung oder Protokolle hilfreich sein. Visual Studio enthält integrierte Tools, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden.

![Protokolle in Visual Studio](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

Weitere Informationen finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio](/de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Hinzufügen von Datenbank- und Autorisierungsfunktionalität

Die meisten Produktionswebsites verwenden eine Datenbank und schränken einige Websitefunktionen auf bestimmte autorisierte Benutzer ein. Ein Lernprogramm mit den ersten Schritten bezüglich Datenbankzugriff, Authentifizierung und Autorisierung finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website](/de-de/develop/net/tutorials/web-site-with-sql-database/).

### Entscheiden, ob die Anwendung in einem Clouddienst ausgeführt werden soll

In einigen Szenarien empfiehlt es sich, die Anwendung in einem Azure-Clouddienst auszuführen statt auf einer Azure-Website. Weitere Informationen finden Sie unter [Azure-Ausführungsmodelle](/de-de/develop/net/fundamentals/compute/) und [Azure-Websites, Cloud Services und VMs: Wann eignet sich welche Komponente?](http://msdn.microsoft.com/de-de/library/windowsazure/jj218759.aspx) Unter [.NET-Anwendung mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs](/de-de/develop/net/tutorials/multi-tier-web-site/1-overview/) finden Sie eine Lernprogrammreihe, in der die Erstellung einer ASP.NET-Webanwendung mit mehreren Ebenen und die Bereitstellung dieser Anwendung für einen Clouddienst gezeigt wird.

