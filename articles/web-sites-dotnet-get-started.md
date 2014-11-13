<properties pageTitle="Erste Schritte mit Azure-Websites f&uuml;r ASP.NET" metaKeywords="" description="Dieses Lernprogramm zeigt Ihnen das Erstellen eines ASP-NET-Webprojekts in Visual Studio 2013 und die Bereitstellung f&uuml;r eine Azure-Website. In weniger als 15 Minuten ist eine App betriebsbereit und wird in der Cloud ausgef&uuml;hrt." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Erste Schritte mit Azure-Websites und ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="09/24/2014" ms.author="tdykstra" />

# Erste Schritte mit Azure-Websites und ASP.NET

In diesem Lernprogramm wird die Erstellung einer ASP.NET-Webanwendung und Bereitstellung für eine Windows Azure-Website mit Visual Studio 2013 oder Visual Studio 2013 for Web Express gezeigt. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie keine Erfahrung mit der Verwendung von Azure oder ASP.NET haben. Nach Abschluss des Lernprogramms verfügen Sie über eine einfache Webanwendung, die in der Cloud ausgeführt wird.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 für Web Express automatisch durch das SDK installiert. Auf diese Weise können Sie völlig kostenlos mit der Entwicklung für Azure beginnen.

Sie lernen Folgendes:

-   Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
-   Erstellen eines Visual Studio ASP.NET-Webprojekts und Bereitstellen für eine Azure-Website
-   Vornehmen von Änderungen am Webprojekt und erneute Bereitstellung der Anwendung
-   Verwenden des Azure-Verwaltungsportals zum Überwachen und Verwalten der Website

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Website-Startseite][Website-Startseite]

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Sie ben&ouml;tigen ein Windows Azure-Konto, um dieses Lernprogramm durchf&uuml;hren zu k&ouml;nnen.</h5>
  <ul>
    <li>Sie k&ouml;nnen <a href="/de-de/pricing/free-trial/?WT.mc_id=A261C142F">ein Azure-Konto kostenlos erstellen</a>: Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen k&ouml;nnen, und Sie k&ouml;nnen das Konto selbst dann behalten und die kostenlose Azure-Dienste nutzen, wenn das Guthaben aufgebraucht ist.</li>
    <li>Sie k&ouml;nnen Ihre <a href="/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">Vorteile f&uuml;r MSDN-Abonnenten aktivieren</a> - Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie f&uuml;r zahlungspflichtige Azure-Dienste verwenden k&ouml;nnen.</li>
  <ul>
</div>

### Teile des Lernprogramms

-   [Einrichten der Entwicklungsumgebung][Einrichten der Entwicklungsumgebung]
-   [Erstellen einer ASP.NET-Webanwendung in Visual Studio][Erstellen einer ASP.NET-Webanwendung in Visual Studio]
-   [Bereitstellen der Anwendung in Azure][Bereitstellen der Anwendung in Azure]
-   [Vornehmen von Änderungen und erneutes Bereitstellen][Vornehmen von Änderungen und erneutes Bereitstellen]
-   [Überwachen und Verwalten der Website im Azure-Verwaltungsportal][Überwachen und Verwalten der Website im Azure-Verwaltungsportal]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Erstellen einer ASP.NET-Webanwendung

Zunächst erstellen Sie ein Webanwendungsprojekt. Die Azure-Website, auf der Sie Ihr Projekt später veröffentlichen, wird automatisch von Visual Studio erstellt. In der folgenden Abbildung ist der Ablauf in zwei Schritten dargestellt.

![Abbildung zur Projekterstellung und Bereitstellung][Abbildung zur Projekterstellung und Bereitstellung]

1.  Öffnen Sie Visual Studio 2013 oder Visual Studio 2013 Express for Web.

2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.

3.  Klicken Sie im Dialogfeld **Neues Projekt** auf **C#** \> **Web** \> **ASP.NET-Webanwendung**. Bei Bedarf können Sie auch **Visual Basic** verwenden.

4.  Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5** ausgewählt ist.

5.  Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.

    ![Dialogfeld "Neues Projekt"][Dialogfeld "Neues Projekt"]

6.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **MVC** aus. Wenn Sie lieber mit ASP.NET-Webformularen arbeiten, können Sie auch die Vorlage **Webformulare** auswählen.

    [MVC und Webformulare][MVC und Webformulare] sind ASP.NET-Frameworks zum Entwickeln von Websites. In diesem Lernprogramm können Sie eines der beiden auswählen, wenn Sie jedoch Webformulare verwenden, müssen Sie später *Default.aspx* bearbeiten, wenn im Lernprogramm auf die Bearbeitung von *Index.cshtml* hingewiesen wird.

7.  Klicken Sie auf **Authentifizierung ändern**.

    ![Dialogfeld "Neues ASP.NET-Projekt"][Dialogfeld "Neues ASP.NET-Projekt"]

8.  Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

    ![Keine Authentifizierung][Keine Authentifizierung]

    Die erstellte Beispielanwendung ermöglicht keine Benutzeranmeldung. Im Abschnitt [Nächste Schritte][Nächste Schritte] gibt es eine Verknüpfung zu einem Lernprogramm, in dem Authentifizierung und Autorisierung implementiert wird.

9.  Lassen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Einstellungen für **Azure** unverändert, und klicken Sie dann auf **OK**.

    ![Dialogfeld "Neues ASP.NET-Projekt"][1]

    Mit den Standardeinstellungen wird angegeben, dass eine Azure-Website für das Webprojekt von Visual Studio erstellt wird. Im nächsten Abschnitt dieses Lernprogramms stellen Sie das Webprojekt für die neu erstellte Website bereit.

    (Die Überschrift des Kontrollkästchens kann **Host in the cloud** oder **Create remote resources** lauten. Die Wirkung ist in beiden Fällen die gleiche.)

10. Falls Sie sich noch nicht bei Azure angemeldet haben, werden Sie von Visual Studio dazu aufgefordert, dies nachzuholen. Klicken Sie auf **Anmelden**.

    ![Anmelden bei Azure][Anmelden bei Azure]

11. Geben Sie im Dialogfeld **Sign in to Azure** die ID und das Kennwort des Kontos ein, mit dem Sie Ihr Azure-Abonnement verwalten.

    Wenn Sie angemeldet sind, werden Sie im Dialogfeld **Configure Azure Site Settings** gefragt, welche Ressourcen Sie erstellen möchten.

    ![Bei Azure angemeldet][Bei Azure angemeldet]

12. Visual Studio gibt eine Standardbezeichnung für **Websitename** an, die von Azure als Präfix der Anwendungs-URL verwendet wird. Geben Sie ggf. einen anderen Websitenamen ein.

    Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und *.azurewebsites.net* (wie neben dem Textfeld **Sitename** angezeigt). Beispiel: Wenn der Websitename `MyExample6442` lautet, ist die URL `MyExample6442.azurewebsites.net`. Die URL muss eindeutig sein. Wenn eine andere Person die eingegebene URL bereits verwendet hat, wird rechts ein rotes Ausrufungszeichen anstelle eines grünen Häkchens angezeigt, und Sie müssen einen anderen Websitenamen eingeben.

13. Wählen Sie in der Dropdownliste **Region** den am nächsten befindlichen Ort aus.

    Mit dieser Einstellung wird das Azure-Datencenter angegeben, in dem Ihre Website ausgeführt wird. Für dieses Lernprogramm können Sie eine beliebige Region auswählen, ohne dass es einen spürbaren Unterschied gibt, aber für die Produktions-Website sollte sich der Webserver so nah wie möglich an den Browsern befinden, die auf die Website zugreifen, um die [Latenz][Latenz] zu minimieren.

14. Verändern Sie die Datenbankfelder nicht.

    Für dieses Lernprogramm verwenden Sie keine Datenbank. Der Abschnitt [Nächste Schritte][Nächste Schritte] am Ende des Lernprogramms ist mit einem Lernprogramm verknüpft, in dem die Verwendung einer Datenbank erläutert wird.

15. Klicken Sie auf **OK**.

    Das Webprojekt wird von Visual Studio innerhalb von wenigen Sekunden in dem angegebenen Ordner erstellt. Auch die Website wird in der angegebenen Azure-Region erstellt.

    Im Fenster **Projektmappen-Explorer** werden die Dateien und Ordner in dem neuen Projekt angezeigt.

    ![Projektmappen-Explorer][Projektmappen-Explorer]

    Im Fenster **Web Publish Activity** wird angegeben, dass die Website erstellt wurde.

    ![Erstellte Website][Erstellte Website]

    Und Sie können die Website im Server-Explorer sehen.

    ![Erstellte Website][2]

## Bereitstellen der Anwendung in Azure

1.  Klicken Sie im Fenster **Web Publish Activity** auf **Publish MyExample to this site now**.

    ![Erstellte Website][3]

    Wenige Sekunden später wird der Assistent **Web veröffentlichen** geöffnet.

    Die Einstellungen, die Visual Studio benötigt, um Ihr Projekt in Azure bereitzustellen, wurden in einem *Veröffentlichungsprofil* gespeichert. Mithilfe des Assistenten können Sie diese Einstellungen überprüfen und ändern.

2.  Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Verbindung** auf **Validate Connection**, um sicherzustellen, dass von Visual Studio eine Verbindung mit Azure hergestellt und das Webprojekt bereitgestellt werden kann.

    ![Verbindung prüfen][Verbindung prüfen]

    Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche **Verbindung prüfen** ein grünes Häkchen angezeigt.

3.  Klicken Sie auf **Weiter**.

    ![Erfolgreich geprüfte Verbindung][Erfolgreich geprüfte Verbindung]

4.  Klicken Sie auf der Registerkarte **Einstellungen** auf **Weiter**.

    ![Registerkarte "Einstellungen"][Registerkarte "Einstellungen"]

    Sie können die Standardwerte für **Konfiguration** und **File Publish Options** übernehmen.

    Mithilfe der Dropdownliste **Konfiguration** können Sie einen Debugbuild für das Remotedebuggen bereitstellen. Im Abschnitt [Nächste Schritte][Nächste Schritte] Sie eine Verknüpfung zu einem Lernprogramm, in dem demonstriert wird, wie Visual Studio remote im Debugmodus ausgeführt wird.

    Wenn Sie die **File Publish Options** erweitern, sehen Sie mehrere Einstellungen, mit deren Hilfe Sie Szenarien handhaben können, die in diesem Lernprogramm nicht erläutert sind:

    -   Zusätzliche Dateien am Zielort entfernen.

        Löscht Dateien auf dem Server, die sich nicht in Ihrem Projekt befinden. Dies kann hilfreich sein, wenn Sie ein Projekt auf einer Website bereitstellen, auf der Sie zuvor ein anderes Projekt bereitgestellt hatten.

    -   Während der Veröffentlichung vorkompilieren.

        Dies kann die Anlaufzeiten von ersten Anfragen für große Websites reduzieren.

    -   Dateien im Ordner "App\_Data" ausschließen.

        Zum Testen benötigen Sie manchmal eine SQL Server-Datenbankdatei in App\_Data, die Sie in der Produktion nicht bereitstellen möchten.

5.  Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau starten**.

    ![Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"][Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"]

    Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf den Server kopiert werden. Die Anzeige der Vorschau ist nicht erforderlich, um die Anwendung zu veröffentlichen, es handelt sich dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt.

6.  Klicken Sie auf **Veröffentlichen**.

    ![Dateiausgabe nach "Vorschau starten"][Dateiausgabe nach "Vorschau starten"]

    Die Dateien werden von Visual Studio auf den Azure-Server kopiert.

    In den Fenstern **Ausgabe** und **Web Publish Activity** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

    ![Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung][Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung]

    Nach der erfolgreichen Bereitstellung wird die URL der bereitgestellten Website automatisch im Standardbrowser geöffnet, und
    die erstellte Anwendung wird jetzt in der Cloud ausgeführt. Die URL in der Adressleiste des Browsers zeigt, dass die Website aus dem Internet geladen wird.

    ![In Azure ausgeführte Website][In Azure ausgeführte Website]

7.  Schließen Sie den Browser.

## Vornehmen von Änderungen und erneutes Bereitstellen

In diesem Abschnitt des Lernprogramms ändern Sie die Überschrift **h1** der Startseite, führen das Projekt lokal auf dem Entwicklungscomputer aus, um die Änderung zu prüfen, und stellen diese dann auf Azure bereit.

1.  Öffnen Sie die Datei *Views/Home/Index.cshtml* oder *.vbhtml* im **Projektmappen-Explorer**, ändern Sie die Überschrift **h1** von "ASP.NET" in "ASP.NET and Azure", und speichern Sie die Datei.

    ![MVC index.cshtml][MVC index.cshtml]

    ![Änderung von "h1" bei MVC][Änderung von "h1" bei MVC]

2.  Drücken Sie STRG+F5, um die geänderte Überschrift anzuzeigen, indem Sie die Website auf dem lokalen Computer ausführen.

    ![Lokal ausgeführte Website][Lokal ausgeführte Website]

    Die URL `http://localhost` zeigt an, dass die Website auf dem lokalen Computer ausgeführt wird. Standardmäßig erfolgt die Ausführung in IIS Express, einer im Umfang eingeschränkten Version von IIS, die für die Entwicklung von Webanwendungen konzipiert wurde.

3.  Schließen Sie den Browser.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

    !["Veröffentlichen" auswählen]["Veröffentlichen" auswählen]

    Die Registerkarte "Vorschau" im Assistenten **Web veröffentlichen** wird angezeigt. Wenn Sie Veröffentlichungseinstellungen ändern mussten, können Sie eine andere Registerkarte auswählen, nun ist es jedoch völlig ausreichend, die erneute Bereitstellung mit denselben Einstellungen auszuführen.

5.  Klicken Sie im Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

    ![Auf "Veröffentlichen" klicken][Auf "Veröffentlichen" klicken]

    Das Projekt wird von Visual Studio für Azure bereitgestellt, und die Website wird im Standardbrowser geöffnet.

    ![Geänderte Website bereitgestellt][Website-Startseite]

**Tipp:** Sie können die Symbolleiste **Webveröffentlichung mit einem Klick** für noch schnellere Bereitstellung aktivieren. Klicken Sie auf **Ansicht** \> **Symbolleisten**, und wählen Sie dann **Webveröffentlichung mit einem Klick** aus. Über diese Symbolleiste können Sie ein Profil auswählen, auf eine Schaltfläche klicken, um zu veröffentlichen, oder auf eine Schaltfläche klicken, um den Assistenten **Web veröffentlichen** zu öffnen.

![Symbolleiste "Webveröffentlichung mit einem Klick"][Symbolleiste "Webveröffentlichung mit einem Klick"]

## Überwachen und Verwalten der Website im Azure-Verwaltungsportal

Das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] ist eine Weboberfläche, die das Verwalten und Überwachen Ihrer Azure-Dienste ermöglicht, z. B. die soeben erstellte Website. In diesem Abschnitt des Lernprogramms erfahren Sie, was Sie im Portal durchführen können.

1.  Rufen Sie in Ihrem Browser [][]<http://manage.windowsazure.com></a> auf, und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

    Im Portal wird eine Liste Ihrer Azure-Dienste angezeigt.

2.  Klicken Sie auf den Namen Ihrer Website.

    ![Portal-Startseite mit Auflistung der neuen Website][Portal-Startseite mit Auflistung der neuen Website]

3.  Klicken Sie auf die Registerkarte **Dashboard**.

    Auf der Registerkarte **Dashboard** wird eine Übersicht der Nutzungsstatistik sowie ein Link zu einer Reihe häufig verwendeter Website-Verwaltungsfunktionen angezeigt. Unter **Schnelleinsicht** finden Sie außerdem einen Link zur Startseite der Anwendung.

    ![Registerkarte "Dashboard" der Portal-Website][Registerkarte "Dashboard" der Portal-Website]

    Bisher gab es auf Ihrer Website kaum Datenverkehr, daher wird in der Übersicht möglicherweise nichts angezeigt. Wenn Sie Ihre Anwendung aufrufen, aktualisieren Sie die Seite mehrmals, und aktualisieren Sie anschließend die Seite **Dashboard** im Portal. Nun sollte eine Statistik angezeigt werden. Sie können weitere Details auf der Registerkarte **Überwachen** anzeigen.

4.  Klicken Sie auf die Registerkarte **Konfigurieren**.

    Auf der Registerkarte [Configure][Configure] können Sie die für die Website verwendete .NET-Version kontrollieren, Funktionen wie [WebSockets][WebSockets] und [Diagnoseprotokollierung][Diagnoseprotokollierung] aktivieren, [Verbindungszeichenfolgen][Verbindungszeichenfolgen] festlegen usw.

    ![Registerkarte "Configure" der Portal-Website][Registerkarte "Configure" der Portal-Website]

5.  Klicken Sie auf die Registerkarte **Scale**.

    Für die bezahlten Ebenen des Website-Diensts können Sie auf der Registerkarte [Scale][Scale] die Größe und Anzahl der Computer bestimmen, die Ihre Webanwendung unterstützen, um Datenverkehrsschwankungen abzufangen.

    Sie können manuell skalieren oder Kriterien bzw. Zeitpläne für die automatische Skalierung konfigurieren.

    ![Registerkarte "Scale" der Portal-Website][Registerkarte "Scale" der Portal-Website]

Dies sind nur einige Funktionen des Verwaltungsportals. Sie können außerdem neue Websites erstellen, vorhandene Websites löschen, Websites anhalten und neu starten sowie andere Arten von Azure-Diensten wie Datenbanken und virtuelle Computer verwalten.

**Tipp:** Es gibt ein neues Verwaltungsportal in der Vorschau, welches das von Ihnen bisher verwendete ersetzen wird. Weitere Informationen finden Sie unter [Azure-Vorschauportal][Azure-Vorschauportal].

## Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie eine einfache Webanwendung erstellt und auf einer Azure-Website bereitgestellt wird. In den folgenden Themen und Ressourcen finden Sie weitere Informationen dazu.

-   Andere Möglichkeiten für die Bereitstellung eines Webprojekts

    In diesem Lernprogramm wurde die schnellste Methode zum Erstellen und Bereitstellen einer Website in einem einzigen Vorgang erläutert. Eine Übersicht über andere Möglichkeiten der Bereitstellung mit Visual Studio oder durch die [Automatisierung der Bereitstellung][Automatisierung der Bereitstellung] über ein [Quellcode-Verwaltungssystem][Quellcode-Verwaltungssystem] finden Sie unter [Bereitstellen einer Azure-Website][Bereitstellen einer Azure-Website].

    Visual Studio kann auch Windows PowerShell-Skripte generieren, mit deren Hilfe Sie die Bereitstellung automatisieren können. Weitere Informationen finden Sie unter [Automate Everything (Building Real-World Cloud Apps with Azure)][Automate Everything (Building Real-World Cloud Apps with Azure)] (in englischer Sprache).

-   Verwalten von Websites in Visual Studio

    Informationen zu den verfügbaren Website-Verwaltungsfunktionen im **Server-Explorer** finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio][Problembehandlung von Azure-Websites in Visual Studio].

-   Problembehandlung für eine Website

    Visual Studio enthält Funktionen, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die Ausführung in Azure remote im Debugmodus möglich. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Websites in Visual Studio][Problembehandlung von Azure-Websites in Visual Studio].

-   Hinzufügen von Datenbank- und Autorisierungsfunktionalität

    Ein Lernprogramm mit Anleitungen bezüglich Datenbankzugriff und Einschränkung einiger Websitefunktionen auf autorisierte Benutzer finden Sie unter [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website][Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website].

-   So fügen Sie einen benutzerdefinierten Domänennamen und SSL hinzu

    Informationen zur Verwendung von SSL und einer eigenen Domäne (z. B. www.contoso.com statt contoso.azurewebsites.net) finden Sie in den folgenden Ressourcen:

    -   [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website][Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website]
    -   [Aktivieren von HTTPS für eine Azure-Website][Aktivieren von HTTPS für eine Azure-Website]
-   Vermeiden von Wartezeiten nach Leerlaufzeiten

    Standardmäßig werden Websites entladen, wenn sie einige Zeit im Leerlauf waren. Danach muss die erste Anfrage warten, bis die Website neu geladen wurde. Zur Vermeidung von Wartezeiten können Sie die AlwaysOn-Funktion aktivieren. Weitere Informationen dazu finden Sie unter den Konfigurationsoptionen in [Konfigurieren von Websites][Konfigurieren von Websites].

-   Hinzufügen von Echtzeitfunktionen wie Chat

    Falls Ihre Website Echtzeitfunktionen (wie Chat-Dienste, Spiele, Börsenticker usw.) enthalten soll, erhalten Sie die beste Performance durch die Verwendung von [ASP.NET SignalR][ASP.NET SignalR] mit der Transportmethode [WebSockets][WebSockets]. Weitere Informationen finden Sie unter [Verwenden von SignalR mit Windows Azure-Websites][Verwenden von SignalR mit Windows Azure-Websites].

-   Treffen einer Auswahl zwischen Azure-Websites, Cloud Services und VMs für Webanwendungen

    In Azure können Sie Webanwendungen in Websites ausführen, die denen in diesem Lernprogramm ähneln, oder in Clouddiensten oder virtuellen Computern. Weitere Informationen finden Sie unter [Azure-Ausführungsmodelle][Azure-Ausführungsmodelle] und [Azure-Websites, Cloud Services und VMs: Vergleich][Azure-Websites, Cloud Services und VMs: Vergleich]

  [Website-Startseite]: ./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png
  [Einrichten der Entwicklungsumgebung]: #set-up-the-development-environment
  [Erstellen einer ASP.NET-Webanwendung in Visual Studio]: #create-an-aspnet-web-application
  [Bereitstellen der Anwendung in Azure]: #deploy-the-application-to-azure
  [Vornehmen von Änderungen und erneutes Bereitstellen]: #make-a-change-and-redeploy
  [Überwachen und Verwalten der Website im Azure-Verwaltungsportal]: #monitor-and-manage-the-site-in-the-management-portal
  [Nächste Schritte]: #next-steps
  [Abbildung zur Projekterstellung und Bereitstellung]: ./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png
  [Dialogfeld "Neues Projekt"]: ./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png
  [MVC und Webformulare]: http://www.asp.net/get-started/websites
  [Dialogfeld "Neues ASP.NET-Projekt"]: ./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png
  [Keine Authentifizierung]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [1]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [Anmelden bei Azure]: ./media/web-sites-dotnet-get-started-vs2013/signin.png
  [Bei Azure angemeldet]: ./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png
  [Latenz]: http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090
  [Projektmappen-Explorer]: ./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png
  [Erstellte Website]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png
  [2]: ./media/web-sites-dotnet-get-started-vs2013/siteinse.png
  [3]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png
  [Verbindung prüfen]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png
  [Erfolgreich geprüfte Verbindung]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png
  [Registerkarte "Einstellungen"]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"]: ./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png
  [Dateiausgabe nach "Vorschau starten"]: ./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png
  [Fenster "Ausgabe" mit Meldung der erfolgreichen Bereitstellung]: ./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png
  [In Azure ausgeführte Website]: ./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png
  [MVC index.cshtml]: ./media/web-sites-dotnet-get-started-vs2013/index.png
  [Änderung von "h1" bei MVC]: ./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png
  [Lokal ausgeführte Website]: ./media/web-sites-dotnet-get-started-vs2013/localandazure.png
  ["Veröffentlichen" auswählen]: ./media/web-sites-dotnet-get-started-vs2013/choosepublish.png
  [Auf "Veröffentlichen" klicken]: ./media/web-sites-dotnet-get-started-vs2013/clickpublish.png
  [Symbolleiste "Webveröffentlichung mit einem Klick"]: ./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png
  [Azure-Verwaltungsportal]: /de-de/services/management-portal/
  []: 
  [Portal-Startseite mit Auflistung der neuen Website]: ./media/web-sites-dotnet-get-started-vs2013/portalhome.png
  [Registerkarte "Dashboard" der Portal-Website]: ./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png
  [Configure]: /de-de/documentation/articles/web-sites-configure//
  [WebSockets]: /blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/
  [Diagnoseprotokollierung]: /de-de/documentation/articles/web-sites-enable-diagnostic-log/
  [Verbindungszeichenfolgen]: /blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Registerkarte "Configure" der Portal-Website]: ./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png
  [Scale]: /de-de/documentation/articles/web-sites-scale/
  [Registerkarte "Scale" der Portal-Website]: ./media/web-sites-dotnet-get-started-vs2013/portalscale.png
  [Azure-Vorschauportal]: /de-de/overview/preview-portal/
  [Automatisierung der Bereitstellung]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [Quellcode-Verwaltungssystem]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Bereitstellen einer Azure-Website]: /de-de/documentation/articles/web-sites-deploy/"
  [Automate Everything (Building Real-World Cloud Apps with Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Problembehandlung von Azure-Websites in Visual Studio]: /de-de/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank für eine Azure-Website]: /de-de/develop/net/tutorials/web-site-with-sql-database/
  [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website]: /de-de/documentation/articles/web-sites-custom-domain-name/
  [Aktivieren von HTTPS für eine Azure-Website]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure-ssl-certificate/
  [Konfigurieren von Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [Verwenden von SignalR mit Windows Azure-Websites]: http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites
  [Azure-Ausführungsmodelle]: /de-de/develop/net/fundamentals/compute/
  [Azure-Websites, Cloud Services und VMs: Vergleich]: /de-de/manage/services/web-sites/choose-web-app-service/
