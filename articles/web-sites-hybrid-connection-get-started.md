<properties title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybridverbindung: Verbinden einer Azure-Website mit einer lokalen Ressource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Verbinden einer Azure-Website mit einer lokalen Ressource mithilfe von Hybridverbindungen

Sie können eine Website auf Microsoft Azure mit jeder lokalen Ressource verbinden, die einen statischen TCP-Port verwendet, wie SQL Server, MySQL, HTTP Web-APIs, Mobile Services und die meisten benutzerdefinierten Webdienste. In diesem Artikel erfahren Sie, wie Sie eine Hybridverbindung zwischen einer Azure-Website und einer lokalen SQL Server-Datenbank herstellen.

> [WACOM.NOTE] Der Website-Teil der hybriden Verbindungsfunktion ist nur im [Azure-Vorschauportal] verfügbar(https://portal.azure.com). Informationen zum Erstellen einer Verbindung in BizTalk-Diensten finden Sie unter [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Voraussetzungen
- Ein Azure-Abonnement. Hinweise zum kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/de-de/pricing/free-trial/). 

- Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Es wird empfohlen, die Standardinstanz auf SQL Server zu verwenden, denn sie nutzt den statischen Port 1433. Informationen zum Erstellen einer ASP.NET-Webanwendung, die eine Hybridverbindung verwendet, finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen](http://go.microsoft.com/fwlink/?LinkID=397979).

- Der Computer, auf dem Sie den lokalen Hybrid Connection Manager-Agent installieren, wird weiter hinten in diesem Artikel beschrieben:

	- Sie müssen in der Lage sein, über Port 5671 eine Verbindung mit Azure herzustellen.
	- Sie müssen in der Lage sein, *Hostname*:*Portnummer* Ihrer lokalen Ressource zu erreichen. 

> [WACOM.NOTE] Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.


##Themen in diesem Artikel##


[Erstellen einer Website im Azure-Vorschauportal](#CreateSite)

[Erstellen einer Hybridverbindung und eines BizTalk-Diensts](#CreateHC)

[Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren](#InstallHCM)

[Nächste Schritte](#NextSteps)


## Erstellen einer Website im Azure-Vorschauportal ##

> [WACOM.NOTE] Wenn Sie bereits eine Website im Azure-Vorschauportal erstellt haben, die Sie für dieses Lernprogramm verwenden möchten, können Sie direkt zu [Erstellen einer Hybridverbindung und eines BizTalk-Diensts](#CreateHC) wechseln und dort beginnen.

1. Klicken Sie unten links in der Ecke des [Azure-Vorschauportals](https://portal.azure.com) auf **Neu**, und wählen Sie dann **Website**.
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. Geben Sie im Fensterbereich **Website** einen Namen für die Website an, und klicken Sie dann auf **Erstellen**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Nach einigen Augenblicken wird die Website erstellt und das entsprechende Website-Fenster angezeigt. Der Fensterbereich ist ein vertikal verschiebbares Dashboard, auf dem Sie die Website verwalten können.
	
	![Website running][WebSiteRunningBlade]
	
4. Um zu überprüfen, ob die Website live geschaltet ist, können Sie auf das Symbol **Durchsuchen** klicken, um die Standardseite anzuzeigen.
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
Anschließend erstellen Sie eine Hybridverbindung und einen BizTalk-Dienst für die Website.

<a name="CreateHC"></a>
## Erstellen einer Hybridverbindung und eines BizTalk-Diensts ##

1. Blättern Sie im Vorschauportal im Fensterbereich für Ihre Website nach unten, und wählen Sie **Hybrid connections** aus.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Klicken Sie unter "Hybrid connections" auf **Hinzufügen**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Der Fensterbereich **Add a hybrid connection** wird geöffnet.  Da es sich um die erste Hybridverbindung handelt, ist die Option **New hybrid connection** bereits ausgewählt, und der Fensterbereich **Create hybrid connection** wird geöffnet.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Geben Sie im Fensterbereich **Create hybrid connection blade** Folgendes ein:
	- Geben Sie unter **Name** einen Namen für die Verbindung ein.
	- Geben Sie unter **Hostname** den Namen des lokalen Computers an, der Ihre Ressource hostet.
	- Geben Sie unter **Port** die Portnummer ein, die Ihre lokale Ressource verwendet (1433 für eine SQL Server-Standardinstanz).
	- Klicken Sie auf **Biz Talk Service**


4. Der Fensterbereich **Create Biz Talk Service** wird geöffnet. Geben Sie einen Namen für den BizTalk-Dienst ein, und klicken Sie auf **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Das Fenster **Create Biz Talk Service** wird geschlossen, und Sie kehren zum Fensterbereich **Create hybrid connection** zurück.
	
5. Klicken Sie unter "Create hybrid connection" auf **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Sobald der Prozess abgeschlossen ist, werden Sie im Benachrichtigungsbereich des Portals informiert, dass die Verbindung erfolgreich erstellt wurde.
	
	![Success notification][CreateHCSuccessNotification]
	
7. Im Website-Fensterbereich zeigt das Symbol für **Hybridverbindungen** jetzt an, dass 1 Hybridverbindung erstellt wurde.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>
## Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren ##

1. Klicken Sie im Website-Fensterbereich auf das Symbol für Hybridverbindungen. 
	
	![Hybrid connections icon][HCIcon]
	
2. Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** zeigt die Spalte **Not connected** an. Klicken Sie auf die Verbindung, um sie zu konfigurieren.
	
	![Not connected][NotConnected]
	
	Der Fensterbereich "Hybrid connection" wird geöffnet.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Klicken Sie im Fensterbereich auf **Listener Setup**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Der Fensterbereich **Hybrid connection properties** wird geöffnet. Wählen Sie unter **On-premises Hybrid Connection Manager** die Option **Click here to install**.
	
	![Click here to install][ClickToInstallHCM]
	
5. Klicken Sie in der Sicherheitswarnung zur Anwendungsausführung auf **Run**, um fortzufahren.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	Klicken Sie im Dialogfeld **User Account Control** auf **Yes**.
	
	![Choose Yes][UAC]
	
7. Der Hybrid Connection Manager wird heruntergeladen und für Sie installiert. 
	
	![Installing][HCMInstalling]
	
8. Sobald die Installation abgeschlossen ist, klicken Sie auf **Close**.
	
	![Click Close][HCMInstallComplete]
	
	Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** jetzt **Connected** an. 
	
	![Connected Status][HCStatusConnected]

Nachdem die Hybridverbindungsinfrastruktur nun vollständig ist, können Sie eine hybride Anwendung erstellen, die diese verwendet. 

<a name="NextSteps"></a>
## Nächste Schritte ##

- Informationen zum Erstellen einer ASP.NET-Webanwendung, die eine Hybridverbindung verwendet, finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen](http://go.microsoft.com/fwlink/?LinkID=397979).

- Informationen zur Verwendung einer Hybridverbindung mit einem mobilen Dienst finden Sie unter [Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen](http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/).

###Zusätzliche Ressourcen

[Überblick über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Einführung von Josh Twist zu Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Website zu Hybridverbindungen](http://azure.microsoft.com/de-de/services/biztalk-services/)

[BizTalk Services: egisterkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"](http://azure.microsoft.com/de-de/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Erstellen einer echten hybriden Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

<!-- IMAGES -->
[Neu]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=35.1-->

<!--HONumber=35.1-->
