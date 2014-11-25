<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# Verbinden einer Azure-Website mit einer lokalen Ressource mithilfe von Hybridverbindungen

Sie können eine Website auf Microsoft Azure mit jeder lokalen Ressource verbinden, die einen statischen TCP-Port verwendet, wie SQL Server, MySQL, HTTP Web-APIs, Mobile Services und die meisten benutzerdefinierten Webdienste. In diesem Artikel erfahren Sie, wie Sie eine Hybridverbindung zwischen einer Azure-Website und einer lokalen SQL Server-Datenbank herstellen.

> [WACOM.NOTE] Der Website-Teil der hybriden Verbindungsfunktion ist nur im [Azure-Vorschauportal][Azure-Vorschauportal] verfügbar. Informationen zum Erstellen einer Verbindung in BizTalk-Diensten finden Sie unter [Hybridverbindungen][Hybridverbindungen].

## Voraussetzungen

-   Ein Azure-Abonnement. Hinweise zum kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

-   Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Die Verwendung einer Standardinstanz auf SQL Server wird empfohlen, da diese den statischen Port 1433 verwenden. Informationen zur Installation und Konfiguration von SQL Server Express zur Verwendung mit hybriden Verbindungen finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen][Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen].

-   Der Computer, auf dem Sie den lokalen Hybrid Connection Manager-Agent installieren, wird weiter hinten in diesem Artikel beschrieben:

    -   Sie müssen in der Lage sein, über Port 5671 eine Verbindung mit Azure herzustellen.
    -   Sie müssen in der Lage sein, *Hostname*:*Portnummer* Ihrer lokalen Ressource zu erreichen.

> [WACOM.NOTE] Bei den in diesem Artikel beschriebenen Schritten wird davon ausgegangen, dass Sie den Browser des Computers verwenden, der den lokalen Hybridverbindungsagent hostet.

## Themen in diesem Artikel

[Erstellen einer Website im Azure-Vorschauportal][Erstellen einer Website im Azure-Vorschauportal]

[Erstellen einer Hybridverbindung und eines BizTalk-Diensts][Erstellen einer Hybridverbindung und eines BizTalk-Diensts]

[Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen][Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen]

[Nächste Schritte][Nächste Schritte]

## Erstellen einer Website im Azure-Vorschauportal

> [WACOM.NOTE] Wenn Sie bereits eine Website im Azure-Vorschauportal erstellt haben, die Sie für dieses Lernprogramm verwenden möchten, können Sie direkt zu [Erstellen einer Hybridverbindung und eines BizTalk-Diensts][Erstellen einer Hybridverbindung und eines BizTalk-Diensts] wechseln und dort beginnen.

1.  Klicken Sie in der unteren linken Ecke des [Azure-Vorschauportals][Azure-Vorschauportal] auf **Neu**, und wählen Sie **Website** aus.

    ![Schaltfläche "Neu"][Schaltfläche "Neu"]

    ![Neue Website][Neue Website]

2.  Geben Sie im Fensterbereich **Website** einen Namen für die Website an, und klicken Sie dann auf **Erstellen**.

    ![Name der Website][Name der Website]

3.  Nach einigen Augenblicken wird die Website erstellt und das entsprechende Website-Fenster angezeigt. Der Fensterbereich ist ein vertikal verschiebbares Dashboard, auf dem Sie die Website verwalten können.

    ![Ausgeführte Website][Ausgeführte Website]

4.  Um zu überprüfen, ob die Website live geschaltet ist, können Sie auf das Symbol **Durchsuchen** klicken, um die Standardseite anzuzeigen.

    ![Zur Anzeige der Website auf "Durchsuchen" klicken][Zur Anzeige der Website auf "Durchsuchen" klicken]

    ![Standardseite der Website][Standardseite der Website]

Anschließend erstellen Sie eine Hybridverbindung und einen BizTalk-Dienst für die Website.

<a name="CreateHC"></a>

## Erstellen einer Hybridverbindung und eines BizTalk-Diensts

1.  Blättern Sie im Vorschauportal im Fensterbereich für Ihre Website nach unten, und wählen Sie **Hybrid connections** aus.

    ![Hybridverbindungen][1]

2.  Klicken Sie unter "Hybrid connections" auf **Hinzufügen**.

    ![Hinzufügen einer Hybridverbindung][Hinzufügen einer Hybridverbindung]

3. Der Fensterbereich **Add a hybrid connection** wird geöffnet. Da es sich um die erste Hybridverbindung handelt, ist die Option **New hybrid connection** bereits ausgewählt, und der Fensterbereich **Create hybrid connection** wird geöffnet.
	
	![Hybridverbindung erstellen][Hybridverbindung erstellen]
	
	Geben Sie im Fensterbereich **Create hybrid connection** Folgendes ein:
	- Geben Sie unter **Name** einen Namen für die Verbindung ein.
	- Geben Sie unter **Hostname** den Namen des lokalen Computers an, der Ihre Ressource hostet.
	- Geben Sie unter **Port** die Portnummer ein, die Ihre lokale Ressource verwendet (1433 für eine SQL Server-Standardinstanz).
	- Klicken Sie auf **BizTalk Service**.


4. Der Fensterbereich **Create Biz Talk Service** wird geöffnet. Geben Sie einen Namen für den BizTalk-Dienst ein, und klicken Sie auf **OK**.

    ![BizTalk-Dienst erstellen][BizTalk-Dienst erstellen]

    Das Fenster **Create Biz Talk Service** wird geschlossen, und Sie kehren zum Fensterbereich **Create hybrid connection** zurück.

5.  Klicken Sie unter "Create hybrid connection" auf **OK**.

    ![OK klicken][OK klicken]

6.  Sobald der Prozess abgeschlossen ist, werden Sie im Benachrichtigungsbereich des Portals informiert, dass die Verbindung erfolgreich erstellt wurde.

    ![Erfolgsmeldung][Erfolgsmeldung]

7.  Im Website-Fensterbereich zeigt das Symbol für **Hybridverbindungen** jetzt an, dass 1 Hybridverbindung erstellt wurde.

    ![Eine Hybridverbindung wurde erstellt][Eine Hybridverbindung wurde erstellt]

An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>

## Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen

1.  Klicken Sie im Website-Fensterbereich auf das Symbol für Hybridverbindungen.

    ![Symbol für Hybridverbindungen][Symbol für Hybridverbindungen]

2.  Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** für den kürzlich hinzugefügten Endpunkt **Not connected** an. Klicken Sie auf die Verbindung, um sie zu konfigurieren.

    ![Nicht verbunden][Nicht verbunden]

    Der Fensterbereich "Hybrid connection" wird geöffnet.

    ![Fensterbereich "Nicht verbunden"][Fensterbereich "Nicht verbunden"]

3.  Klicken Sie im Fensterbereich auf **Listener Setup**.

    ![Listener-Einrichtung][Listener-Einrichtung]

4.  Der Fensterbereich **Hybrid connection properties** wird geöffnet. Wählen Sie unter **On-premises Hybrid Connection Manager** die Option **Click here to install**.

    ![Zum Installieren hier klicken][Zum Installieren hier klicken]

5.  Klicken Sie in der Sicherheitswarnung zur Anwendungsauführung auf **Run**, um fortzufahren.

    ![Auf "Run" klicken, um fortzufahren][Auf "Run" klicken, um fortzufahren]

6.  Klicken Sie im Dialogfeld **User Account Control** auf **Yes**.

    ![Klicken Sie auf "Yes"][Klicken Sie auf "Yes"]

7.  Der Hybrid Connection Manager wird heruntergeladen und für Sie installiert.

    ![Installation][Installation]

8.  Sobald die Installation abgeschlossen ist, klicken Sie auf **Close**.

    ![Klicken Sie auf "Schließen"][Klicken Sie auf "Schließen"]

    Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** jetzt **Connected** an.

    ![Status Verbunden][Status Verbunden]

Nachdem die Hybridverbindungsinfrastruktur nun vollständig ist, können Sie eine hybride Anwendung erstellen, die diese verwendet.

<a name="NextSteps"></a>

## Nächste Schritte

-   Informationen zum Erstellen einer ASP.NET-Webanwendung, die eine Hybridverbindung verwendet, finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen][Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen].

-   Informationen zur Verwendung einer Hybridverbindung mit einem mobilen Dienst finden Sie unter [Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen][Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen].

### Zusätzliche Ressourcen

[Überblick über Hybridverbindungen][Hybridverbindungen]

[Einführung von Josh Twist zu Hybridverbindungen (Channel 9-Video)][Einführung von Josh Twist zu Hybridverbindungen (Channel 9-Video)]

[Website zu Hybridverbindungen][Website zu Hybridverbindungen]

[BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"][BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"]

[Erstellen einer echten hybriden Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video)][Erstellen einer echten hybriden Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video)]

[Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen (Channel 9-Video)][Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen (Channel 9-Video)]

<!-- IMAGES -->

  [Azure-Vorschauportal]: https://portal.azure.com
  [Hybridverbindungen]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Erstellen einer Website im Azure-Vorschauportal]: #CreateSite
  [Erstellen einer Hybridverbindung und eines BizTalk-Diensts]: #CreateHC
  [Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen]: #InstallHCM
  [Nächste Schritte]: #NextSteps
  [Schaltfläche "Neu"]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [Neue Website]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Name der Website]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Ausgeführte Website]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Zur Anzeige der Website auf "Durchsuchen" klicken]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Standardseite der Website]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [1]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Hinzufügen einer Hybridverbindung]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Hybridverbindung erstellen]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [BizTalk-Dienst erstellen]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [OK klicken]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Erfolgsmeldung]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [Eine Hybridverbindung wurde erstellt]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Symbol für Hybridverbindungen]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Nicht verbunden]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [Fensterbereich "Nicht verbunden"]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Listener-Einrichtung]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Zum Installieren hier klicken]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Auf "Run" klicken, um fortzufahren]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Klicken Sie auf "Yes"]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [Installation]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Klicken Sie auf "Schließen"]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Status Verbunden]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Einführung von Josh Twist zu Hybridverbindungen (Channel 9-Video)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Website zu Hybridverbindungen]: http://azure.microsoft.com/de-de/services/biztalk-services/
  [BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"]: http://azure.microsoft.com/de-de/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Erstellen einer echten hybriden Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen (Channel 9-Video)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
