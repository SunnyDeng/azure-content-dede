<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Erste Schritte mit Azure-Websites und ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Hinweis</strong><p>Es ist eine <a href="/en-us/develop/net/tutorials/get-started/">neuere Version dieses Lernprogramms</a> verf&uuml;gbar. Sie k&ouml;nnen trotzdem diese Version ausf&uuml;hren, wenn Sie Visual Studio 2012 verwenden m&ouml;chten, sie enth&auml;lt jedoch nicht alle neuen Azure SDK-Funktionen.</p></div>

In diesem Lernprogramm wird die Bereitstellung einer ASP.NET-Webanwendung für eine Azure-Website mit dem Assistenten "Web veröffentlichen" in Visual Studio 2012 oder Visual Studio 2012 for Web Express gezeigt. Wenn Sie möchten, können Sie den Schritten des Lernprogramms mit Visual Studio 2010 oder Visual Web Developer Express 2010 folgen.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2012 verfügen, wird Visual Studio 2012 für Web Express automatisch durch das SDK installiert. Auf diese Weise können Sie völlig kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine einfache Webanwendung, die in der Cloud ausgeführt wird.

Sie lernen Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
-   Erstellen eines Visual Studio ASP.NET MVC 4-Projekts und Veröffentlichen dieses Projekts auf einer Azure-Website

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Beispiel-Website][Beispiel-Website]

<div class="dev-callout"><p><strong>Hinweis:</strong> Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm abzuschlie&szlig;en. Wenn Sie kein Konto haben, k&ouml;nnen Sie <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">Ihre MSDN-Abonnentenvorteile aktivieren</a> oder <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">sich f&uuml;r eine kostenlose Testversion registrieren</a>.</p></div>

### Teile des Lernprogramms

1.  [Einrichten der Entwicklungsumgebung][Einrichten der Entwicklungsumgebung]
2.  [Erstellen einer Website in Azure][Erstellen einer Website in Azure]
3.  [Erstellen einer ASP.NET MVC 4-Anwendung][Erstellen einer ASP.NET MVC 4-Anwendung]
4.  [Bereitstellen der Anwendung in Azure][Bereitstellen der Anwendung in Azure]
5.  [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

## <a name="setupwindowsazure"></a><span class="short-header">Erstellen einer Website</span>Erstellen einer Website

Im nächsten Schritt erstellen Sie die Azure-Website.

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf **Websites** und dann auf **Neu**.

![Neue Website][Neue Website]

1.  Klicken Sie auf **Schnellerfassung**.

	![Schnellerfassung][Schnellerfassung]

2.  Geben Sie im Schritt **Website erstellen** des Assistenten eine Zeichenfolge in das Feld **URL** ein, die als eindeutige URL der Anwendung dienen soll. Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und dem Suffix, das neben dem Textfeld aufgeführt wird. In der Abbildung wird **example1** dargestellt, aber falls jemand diese Zeichenfolge bereits für eine URL verwendet, müssen Sie einen anderen Wert eingeben.

3.  Wählen Sie in der Dropdownliste **Region** die am nächsten gelegene Region aus.
    Mit dieser Einstellung wird das Datencenter angegeben, in dem Ihre Website ausgeführt wird.

4.  Klicken Sie auf den Pfeil **Website erstellen**.

    ![Neue Website erstellen][Neue Website erstellen]

    Sie kehren zur Seite **Websites** im Verwaltungsportal zurück, und in der Spalte **Status** wird angegeben, dass die Website erstellt wird. Nach einer Weile (normalerweise weniger als eine Minute) wird in der Spalte **Status** angegeben, dass die Website erfolgreich erstellt wurde. In der linken Navigationsleiste wird die Anzahl der Websites in Ihrem Konto neben dem Symbol **Websites** angegeben.

    ![Seite "Websites" im Verwaltungsportal mit erstellter Website][Seite "Websites" im Verwaltungsportal mit erstellter Website]

## <a name="createmvc4app"></a><span class="short-header">Erstellen der Anwendung</span>Erstellen einer ASP.NET MVC 4-Anwendung

Sie haben nun eine Azure-Website erstellt, diese enthält jedoch noch keinen Inhalt. Zunächst erstellen Sie ein Visual Studio-Webanwendungsprojekt, das Sie auf Azure veröffentlichen.

### Erstellen des Projekts

1.  Öffnen Sie Visual Studio 2012 oder Visual Studio 2012 Express für das Web.

2.  Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

	!["Neues Projekt" im Menü "Datei"]["Neues Projekt" im Menü "Datei"]

1.  Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **C\#**, und wählen Sie unter **Installierte Vorlagen** die Option **Web** aus. Wählen Sie dann **ASP.NET MVC 4-Webanwendung** aus.

2.  Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.

3.  Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"][Dialogfeld "Neues Projekt"]

1.  Wählen Sie im Dialogfeld **Neues ASP.NET MVC 4-Projekt** die Vorlage **Internetanwendung** aus, und klicken Sie auf **OK**.

	![Dialogfeld "ASP.NET MVC 4-Projekt"][Dialogfeld "ASP.NET MVC 4-Projekt"]

### Lokales Ausführen der Anwendung

1.  Drücken Sie die Taste **STRG**+**F5**, um die Anwendung auszuführen. Die Startseite der Anwendung wird im Standardbrowser angezeigt.

	![Lokal ausgeführte Website][Lokal ausgeführte Website]

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um eine einfache Anwendung zu erstellen, die Sie in Azure bereitstellen können.

## <a name="deploytowindowsazure"></a><span class="short-header">Bereitstellen der Anwendung</span>Bereitstellen der Anwendung in Azure

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts]["Veröffentlichen" im Kontextmenü des Projekts]

	Der Assistent **Web veröffentlichen** wird geöffnet.

1.  Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Profil** auf **Importieren**.

    ![Veröffentlichungseinstellungen importieren][Veröffentlichungseinstellungen importieren]

	Das Dialogfeld **Veröffentlichungsprofil importieren** wird geöffnet.

1.  Falls Sie nicht bereits Ihr Azure-Abonnement zu Visual Studio hinzugefügt haben, führen Sie folgende Schritte aus. In diesen Schritten fügen Sie Ihr Abonnement hinzu, sodass die Dropdown-Liste unter **Aus einer Azure-Website importieren** Ihre Website enthält.
    - Klicken Sie im Dialogfeld **Veröffentlichungsprofil importieren** auf **Aus einer Azure-Website importieren** und dann auf **Azure-Abonnement hinzufügen**.
    
    	![Azure-Abonnement hinzufügen][Azure-Abonnement hinzufügen]
    - Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Download subscription file**.
    
    	![Abonnementdatei herunterladen][Abonnementdatei herunterladen]
    - Speichern Sie im Browserfenster die Datei *.publishsettings*.
    
    	![Datei .publishsettings herunterladen][Datei .publishsettings herunterladen]
    - Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Browse**, und navigieren Sie zur Datei *.publishsettings*.
    
    	![Abonnement herunterladen][Abonnement herunterladen]
    - Klicken Sie auf **Importieren**.
    
    	![Importieren][Importieren]

    > [WACOM.NOTE]
    > Die Datei ".publishsettings" enthält Ihre Anmeldeinformationen (unverschlüsselt), die verwendet werden, um Ihre Azure-Abonnements und -Dienste zu verwalten. Die bewährte Sicherheitsmethode für diese Datei besteht im vorübergehenden Speichern außerhalb Ihrer Quellcodeverzeichnisse (beispielsweise im Ordner "Libraries\\Documents"). Löschen Sie sie nach Abschluss des Importvorgangs. Böswillige Benutzer, die Zugriff auf die .publishsettings-Datei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.

2.  Wählen Sie im Dialogfeld **Veröffentlichungsprofil importieren** die Option **Aus einer Azure-Website importieren** und dann Ihre Website in der Dropdownliste aus, und klicken Sie auf **OK**.

    ![Import Publish Profile][Import Publish Profile]

3.  Klicken Sie auf der Registerkarte **Verbindung** auf **Verbindung prüfen**, um sicherzustellen, dass die Einstellungen richtig sind.

    ![Verbindung prüfen][Verbindung prüfen]

4.  Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche **Verbindung prüfen** ein grünes Häkchen angezeigt. Klicken Sie auf **Weiter**.

    ![Erfolgreich geprüfte Verbindung][Erfolgreich geprüfte Verbindung]

5.  Deaktivieren Sie auf der Registerkarte **Einstellungen** die Option **Use this connection string at runtime**, da diese Anwendung keine Datenbank verwendet. Sie können die Standardeinstellungen für die restlichen Einstellungen auf dieser Seite übernehmen. Sie stellen eine Buildkonfiguration für die Veröffentlichung bereit und müssen keine Dateien auf dem Zielserver löschen, die Anwendung nicht vorkompilieren oder Dateien im Ordner "App\_Data" ausschließen.
    Klicken Sie auf **Weiter**.

    ![Registerkarte "Einstellungen"][Registerkarte "Einstellungen"]

6.  Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau starten**.

    ![Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"][Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"]

    Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf den Server kopiert werden. Die Anzeige der Vorschau ist nicht erforderlich, um die Anwendung zu veröffentlichen, es handelt sich dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt. In diesem Fall können Sie die Dateiliste, die angezeigt wird, ignorieren.

    ![Dateiausgabe nach "Vorschau starten"][Dateiausgabe nach "Vorschau starten"]

7.  Klicken Sie auf **Veröffentlichen**. Die Dateien werden von Visual Studio auf den Azure-Server kopiert.

8.  Im Fenster **Ausgabe** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

    ![Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung][Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung]

9.  Nach der erfolgreichen Bereitstellung wird die URL der bereitgestellten Website automatisch im Standardbrowser geöffnet.
    Die erstellte Anwendung wird jetzt in der Cloud ausgeführt.

    ![In Azure ausgeführte Website][Beispiel-Website]

## <a name="nextsteps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine einfache Webanwendung auf einer Azure-Website bereitgestellt wird. Es sind weitere Ressourcen verfügbar, mit denen Sie lernen können, die Site zu verwalten und zu skalieren sowie Siteprobleme zu behandeln, Datenbank-, Authentifizierungs- und Autorisierungsfunktionalität hinzuzufügen und zu entscheiden, ob die Anwendung in einem Azure-Clouddienst statt auf einer Azure-Website ausgeführt werden soll.

### Verwalten von Websites

Wenn Sie diese Website nicht mehr benötigen, können Sie sie löschen. Unter Umständen möchten Sie sie auch temporär offline nehmen oder die Websiteeinstellungen ändern. Sie können einige dieser Funktionen direkt über den **Server-Explorer** in Visual Studio ausführen.

![Azure-Websites im Server-Explorer][Azure-Websites im Server-Explorer]

![Website-Konfiguration in Visual Studio][Website-Konfiguration in Visual Studio]

Sie können das Azure-Verwaltungsportal zum Löschen Ihrer Website verwenden. Auf dem folgenden Bildschirmfoto werden die Schaltflächen **Beenden**, **Neu starten** und **Löschen** auf der Registerkarte **Dashboard** des Verwaltungsportals angezeigt.

![Registerkarte "Dashboard" im Verwaltungsportal][Registerkarte "Dashboard" im Verwaltungsportal]

Sie können viele andere Websiteeinstellungen auf der Registerkarte **Konfigurieren** ändern. Weitere Informationen finden Sie unter [Verwalten von Websites][Verwalten von Websites].

### Skalieren von Websites

Wenn es sich um eine öffentliche Website handelt und nach und nach mehr Datenverkehr auftritt, können die Reaktionszeiten nachlassen. Um dies zu verhindern, können Sie auf der Registerkarte **Skalierung** im Verwaltungsportal einfach Serverressourcen hinzufügen.

![Registerkarte "Skalierung" im Verwaltungsportal][Registerkarte "Skalierung" im Verwaltungsportal]

Weitere Informationen finden Sie unter [Skalieren von Websites][Skalieren von Websites]. (Das Hinzufügen von Serverressourcen zum Skalieren einer Website ist nicht kostenfrei.)

### Problembehandlung für eine Website

Bei der Problembehandlung kann eine Einbeziehung der Ausgabe der Ablaufverfolgung oder Protokolle hilfreich sein. Visual Studio enthält integrierte Tools, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden.

![Protokolle in Visual Studio][Protokolle in Visual Studio]

Weitere Informationen finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio][Problembehandlung von Azure-Websites in Visual Studio].

### Hinzufügen von Datenbank- und Autorisierungsfunktionalität

Die meisten Produktionswebsites verwenden eine Datenbank und schränken einige Websitefunktionen auf bestimmte autorisierte Benutzer ein. Ein Lernprogramm mit den ersten Schritten bezüglich Datenbankzugriff, Authentifizierung und Autorisierung finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website][Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website].

### Entscheiden, ob die Anwendung in einem Clouddienst ausgeführt werden soll

In einigen Szenarien empfiehlt es sich, die Anwendung in einem Azure-Clouddienst auszuführen statt auf einer Azure-Website. Weitere Informationen finden Sie unter [Azure-Ausführungsmodelle][Azure-Ausführungsmodelle] und [Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure][Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure]. Unter [.NET-Anwendung mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs][.NET-Anwendung mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs] finden Sie eine Lernprogrammreihe, in der die Erstellung einer ASP.NET-Webanwendung mit mehreren Ebenen und die Bereitstellung dieser Anwendung für einen Clouddienst gezeigt wird.

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [neuere Version dieses Lernprogramms]: /en-us/develop/net/tutorials/get-started/
  [Beispiel-Website]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
  [Ihre MSDN-Abonnentenvorteile aktivieren]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [sich für eine kostenlose Testversion registrieren]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Einrichten der Entwicklungsumgebung]: #setupdevenv
  [Erstellen einer Website in Azure]: #setupwindowsazure
  [Erstellen einer ASP.NET MVC 4-Anwendung]: #createmvc4app
  [Bereitstellen der Anwendung in Azure]: #deploytowindowsazure
  [Nächste Schritte]: #nextsteps
  [install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Neue Website]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
  [Schnellerfassung]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
  [Neue Website erstellen]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
  [Seite "Websites" im Verwaltungsportal mit erstellter Website]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
  ["Neues Projekt" im Menü "Datei"]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
  [Dialogfeld "Neues Projekt"]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
  [Dialogfeld "ASP.NET MVC 4-Projekt"]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
  [Lokal ausgeführte Website]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
  ["Veröffentlichen" im Kontextmenü des Projekts]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
  [Veröffentlichungseinstellungen importieren]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
  [Azure-Abonnement hinzufügen]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
  [Abonnementdatei herunterladen]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
  [Datei .publishsettings herunterladen]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
  [Abonnement herunterladen]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
  [Importieren]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
  [Import Publish Profile]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
  [Verbindung prüfen]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
  [Erfolgreich geprüfte Verbindung]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
  [Registerkarte "Einstellungen"]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
  [Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
  [Dateiausgabe nach "Vorschau starten"]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
  [Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
  [Azure-Websites im Server-Explorer]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
  [Website-Konfiguration in Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
  [Registerkarte "Dashboard" im Verwaltungsportal]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
  [Verwalten von Websites]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [Registerkarte "Skalierung" im Verwaltungsportal]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
  [Skalieren von Websites]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [Protokolle in Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
  [Problembehandlung von Azure-Websites in Visual Studio]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Azure-Ausführungsmodelle]: /en-us/develop/net/fundamentals/compute/
  [Vergleich von Websites, Clouddiensten und virtuellen Computern in Azure]: http://azure.microsoft.com/de-de/documentation/articles/choose-web-site-cloud-service-vm/
  [.NET-Anwendung mit mehreren Ebenen mithilfe von Speichertabellen, Warteschlangen und Blobs]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
