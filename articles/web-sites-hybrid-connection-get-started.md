<properties 
	pageTitle="Hybridverbindung: Verbinden einer Azure-Website mit einer lokalen Ressource" 
	description="Erstellen einer Verbindung zwischen einer Azure-Website und einer lokalen Ressource, die einen statischen TCP-Port verwendet" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Verbinden einer Azure-Website mit einer lokalen Ressource mithilfe von Hybridverbindungen

Sie können eine Website auf Microsoft Azure mit jeder lokalen Ressource verbinden, die einen statischen TCP-Port verwendet, wie SQL Server, MySQL, HTTP Web-APIs, Mobile Services und die meisten benutzerdefinierten Webdienste. In diesem Artikel erfahren Sie, wie Sie eine Hybridverbindung zwischen einer Azure-Website und einer lokalen SQL Server-Datenbank herstellen.

> [AZURE.NOTE] Der Website-Teil der hybriden Verbindungsfunktion ist nur im [Azure-Vorschauportal](https://portal.azure.com) verfügbar. Informationen zum Erstellen einer Verbindung in BizTalk-Diensten finden Sie unter [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Voraussetzungen
- Ein Azure-Abonnement. Hinweise zum kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/). 

- Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Es wird empfohlen, die Standardinstanz auf SQL Server zu verwenden, denn sie nutzt den statischen Port 1433. Informationen zum Erstellen einer ASP.NET-Webanwendung, die eine Hybridverbindung verwendet, finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen](http://go.microsoft.com/fwlink/?LinkID=397979)

- Der Computer, auf dem Sie den lokalen Hybrid Connection Manager-Agent installieren, wird weiter hinten in diesem Artikel beschrieben:

	- Sie müssen in der Lage sein, über Port 5671 eine Verbindung mit Azure herzustellen.
	- Sie müssen in der Lage sein,  *hostname*:*Portnummer* Ihrer lokalen Ressource zu erreichen. 

> [AZURE.NOTE] Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.


##Themen in diesem Artikel


[Erstellen einer Website im Azure-Vorschauportal](#CreateSite)

[Erstellen einer Hybridverbindung und eines BizTalk-Diensts](#CreateHC)

[Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung](#InstallHCM)

[Nächste Schritte](#NextSteps)


## Erstellen einer Website im Azure-Vorschauportal

> [AZURE.NOTE] Wenn Sie bereits eine Website im Azure-Vorschauportal erstellt haben, die Sie für dieses Lernprogramm verwenden möchten, können Sie direkt zu [Erstellen einer Hybridverbindung und eines BizTalk-Diensts wechseln](#CreateHC) und dort beginnen.

1. Klicken Sie in der unteren linken Ecke des [Azure-Vorschauportals](https://portal.azure.com) auf **Neu**, und wählen Sie **Website** aus.
	
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
## Erstellen einer Hybridverbindung und eines BizTalk-Diensts

1. Führen Sie im Vorschauportal im Fensterbereich einen Bildlauf nach unten zu Ihrer Website aus, und wählen Sie **Hybridverbindungen** aus.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Klicken Sie unter "Hybridverbindungen" auf **Hinzufügen**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Der Fensterbereich **Hybridverbindung hinzufügen** wird geöffnet.  Da es sich um die erste Hybridverbindung handelt, ist die Option **Neue Hybridverbindung** bereits ausgewählt, und der Fensterbereich **Hybridverbindung erstellen** wird geöffnet.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Geben Sie im Fensterbereich **Hybridverbindung erstellen** Folgendes ein:
	- Geben Sie in **Name** einen Namen für die Verbindung ein.
	- Geben Sie in **Hostname** den Namen des lokalen Computers an, der Ihre Ressource hostet.
	- Geben Sie in **Port** die Portnummer ein, die Ihre lokale Ressource verwendet (1433 für eine SQL Server-Standardinstanz).
	- Klicken Sie auf **BizTalk-Dienst**.


4. Der Fensterbereich **BizTalk-Dienst erstellen** wird geöffnet. Geben Sie einen Namen für den BizTalk-Dienst ein, und klicken Sie auf **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Der Fensterbereiche **BizTalk-Dienst erstellen** wird geschlossen, und Sie kehren zum Fensterbereich **Hybridverbindung erstellen** zurück.
	
5. Klicken Sie im Fensterbereich "Hybridverbindung erstellen" auf **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Sobald der Prozess abgeschlossen ist, werden Sie im Benachrichtigungsbereich des Portals informiert, dass die Verbindung erfolgreich erstellt wurde.
	
	![Success notification][CreateHCSuccessNotification]
	
7. Im Website-Fensterbereich zeigt das Symbol **Hybridverbindungen** jetzt an, dass eine Hybridverbindung erstellt wurde.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>
## Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren ##

1. Klicken Sie im Website-Fensterbereich auf das Symbol für Hybridverbindungen. 
	
	![Hybrid connections icon][HCIcon]
	
2. Im Fensterbereich **Hybridverbindungen** zeigt die Spalte **Status** für den zuvor hinzugefügten Endpunkt **Nicht verbunden** an. Klicken Sie auf die Verbindung, um sie zu konfigurieren.
	
	![Not connected][NotConnected]
	
	Der Fensterbereich "Hybridverbindung" wird geöffnet.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Klicken Sie im Fensterbereich auf **Listenereinrichtung**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Der Fensterbereich **Eigenschaften von Hybridverbindunen** wird geöffnet. Wählen Sie unter **Lokaler Hybridverbindungs-Manager** die Option ** Klicken Sie hier, um die Installation zu starten**.
	
	![Click here to install][ClickToInstallHCM]
	
5. Klicken Sie in der Sicherheitswarnung zur Anwendungsausführung auf **Ausführen**, um fortzufahren.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	Klicken Sie im Dialogfeld **Benutzerkontensteuerung** auf **Ja**.
	
	![Choose Yes][UAC]
	
7. Der Hybridverbindungs-Manager wird heruntergeladen und für Sie installiert. 
	
	![Installing][HCMInstalling]
	
8. Sobald die Installation abgeschlossen ist, klicken Sie auf **Schließen**.
	
	![Click Close][HCMInstallComplete]
	
	Im Fensterbereich **Hybridverbindungen** zeigt die Spalte **Status** jetzt **Verbunden** an. 
	
	![Status "Verbunden"][HCStatusConnected]

Nachdem die Hybridverbindungsinfrastruktur nun vollständig ist, können Sie eine hybride Anwendung erstellen, die diese verwendet. 

<a name="NextSteps"></a>
## Nächste Schritte

- Informationen zum Erstellen einer ASP.NET-Webanwendung, die eine Hybridverbindung verwendet, finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen](http://go.microsoft.com/fwlink/?LinkID=397979).

- Informationen zur Verwendung einer Hybridverbindung mit einem mobilen Dienst finden Sie unter [Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/).

###Zusätzliche Ressourcen

[Übersicht über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Einführung von Josh Twist zu Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Website zu Hybridverbindungen](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"](http://azure.microsoft.com/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Erstellen einer echten hybriden Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
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




<!--HONumber=42-->
