<properties 
	pageTitle="Zugreifen auf lokale Ressourcen über Hybridverbindungen in Azure App Service" 
	description="Herstellen einer Verbindung zwischen einer Web-App in Azure App Service und einer lokalen Ressource, die einen statischen TCP-Port verwendet" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="cephalin"/>

#Zugreifen auf lokale Ressourcen über Hybridverbindungen in Azure App Service

Sie können eine Azure App Service-App mit allen lokalen Ressourcen verbinden, die einen statischen TCP-Port verwenden, einschließlich SQL Server, MySQL, HTTP Web-APIs und der meisten benutzerdefinierten Webdienste. In diesem Artikel erfahren Sie, wie Sie eine Hybridverbindung zwischen App Service und einer lokalen SQL Server-Datenbank herstellen.

> [AZURE.NOTE] Der Web-Apps-Teil der Funktion "Hybridverbindungen" ist nur im [Azure-Portal](https://portal.azure.com) verfügbar. Informationen zum Erstellen einer Verbindung in BizTalk-Diensten finden Sie unter [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274).
> 
> Dieser Inhalt gilt auch für Mobile Apps in Azure App Service.

## Voraussetzungen
- Ein Azure-Abonnement. Hinweise zum kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
 
	Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

- Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Es wird empfohlen, die Standardinstanz auf SQL Server zu verwenden, denn sie nutzt den statischen Port 1433. Informationen zum Installieren und Konfigurieren von SQL Server Express zur Verwendung mit Hybridverbindungen finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen](http://go.microsoft.com/fwlink/?LinkID=397979).

- Der Computer, auf dem Sie den lokalen Hybrid Connection Manager-Agent installieren, wird weiter hinten in diesem Artikel beschrieben:

	- Sie müssen in der Lage sein, über Port 5671 eine Verbindung mit Azure herzustellen.
	- Sie müssen in der Lage sein, *Hostname*:*Portnummer* Ihrer lokalen Ressource zu erreichen. 

> [AZURE.NOTE] Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.


## Erstellen einer Web-App im Azure-Portal ##

> [AZURE.NOTE] Wenn Sie bereits ein Web-App- oder Mobile App-Back-End im Azure-Portal erstellt haben, das Sie für dieses Tutorial verwenden möchten, können Sie direkt zu [Erstellen einer Hybridverbindung und eines BizTalk-Diensts](#CreateHC) wechseln und dort beginnen.

1. Klicken Sie oben links im [Azure-Portal](https://portal.azure.com) auf **Neu** > **Web und mobil** > **Web-App**.
	
	![Neue Web-App][NewWebsite]
	
2. Geben Sie im Blatt der **Web-App** eine URL an, und klicken Sie auf **Erstellen**.
	
	![Name der Website][WebsiteCreationBlade]
	
3. Nach einigen Augenblicken wird die Web-App erstellt, und das zugehörige Blatt der Web-App wird angezeigt. Der Fensterbereich ist ein vertikal verschiebbares Dashboard, auf dem Sie die Website verwalten können.
	
	![Ausgeführte Website][WebSiteRunningBlade]
	
4. Um zu überprüfen, ob die Website live geschaltet ist, können Sie auf das Symbol **Durchsuchen** klicken, um die Standardseite anzuzeigen.
	
	![Zur Anzeige der Web-App auf "Durchsuchen" klicken][Browse]
	
	![Web-App-Standardseite][DefaultWebSitePage]
	
Anschließend erstellen Sie eine Hybridverbindung und einen BizTalk-Dienst für die Web-App.

<a name="CreateHC"></a>
## Erstellen einer Hybridverbindung und eines BizTalk-Diensts ##

1. Klicken Sie im Blatt Ihrer Web-App auf **Alle Einstellungen** > **Netzwerk** > **Configure your hybrid connection endpoints**.
	
	![Hybridverbindungen][CreateHCHCIcon]
	
2. Klicken Sie unter "Hybrid connections" auf **Hinzufügen**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Der Fensterbereich **Add a hybrid connection** wird geöffnet. Da es sich um die erste Hybridverbindung handelt, ist die Option **New hybrid connection** bereits ausgewählt, und der Fensterbereich **Create hybrid connection** wird geöffnet.
	
	![Hybridverbindung erstellen][TwinCreateHCBlades]
	
	Im Blatt **Hybridverbindung erstellen**:
	- Geben Sie für **Name** einen Namen für die Verbindung an.
	- Geben Sie für **Hostname** den Namen des lokalen Computers ein, der Ihre Ressourcen hostet.
	- Geben Sie für **Port** die Portnummer ein, die Ihre lokale Ressource verwendet (1433 für eine Standardinstanz von SQL Server).
	- Klicken Sie auf **Biz Talk Service**.


4. Das Blatt **BizTalk Service erstellen** wird geöffnet. Geben Sie einen Namen für den BizTalk-Dienst ein, und klicken Sie auf **OK**.
	
	![BizTalk-Dienst erstellen][CreateHCCreateBTS]
	
	Das Blatt **BizTalk Service erstellen** wird geschlossen, und Sie kehren zum Blatt **Hybridverbindung erstellen** zurück.
	
5. Klicken Sie unter "Create hybrid connection" auf **OK**.
	
	![OK klicken][CreateBTScomplete]
	
6. Sobald der Prozess abgeschlossen ist, werden Sie im Benachrichtigungsbereich des Portals informiert, dass die Verbindung erfolgreich erstellt wurde.
	<!--- TODO

    Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the classic portal
	(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
	Create hybrid conn step, you get the following error
	Failed to create hybrid connection RelecIoudHC. The 
	resource type could not be found in the namespace 
	'Microsoft.BizTaIkServices for api version 2014-06-01'.
	
	The error indicates it couldn't find the type, not the instance.
	![Success notification][CreateHCSuccessNotification]
	-->
7. Im Blatt der Web-App zeigt das Symbol **Hybridverbindungen** jetzt an, dass 1 Hybridverbindung erstellt wurde.
	
	![Eine Hybridverbindung wurde erstellt][CreateHCOneConnectionCreated]
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>
## Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung ##

1. Klicken Sie im Blatt der Web-App auf **Alle Einstellungen** > **Netzwerk** > **Configure your hybrid connection endpoints**. 
	
	![Symbol für Hybridverbindungen][HCIcon]
	
2. Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** für den kürzlich hinzugefügten Endpunkt **Not connected** an. Klicken Sie auf die Verbindung, um sie zu konfigurieren.
	
	![Nicht verbunden][NotConnected]
	
	Der Fensterbereich "Hybrid connection" wird geöffnet.
	
	![Fensterbereich "Nicht verbunden"][NotConnectedBlade]
	
3. Klicken Sie im Fensterbereich auf **Listener Setup**.
	
	![Listener-Einrichtung][ClickListenerSetup]
	
4. Der Fensterbereich **Hybrid connection properties** wird geöffnet. Wählen Sie unter **On-premises Hybrid Connection Manager** die Option **Click here to install**.
	
	![Zum Installieren hier klicken][ClickToInstallHCM]
	
5. Klicken Sie in der Sicherheitswarnung zur Anwendungsausführung auf **Run**, um fortzufahren.
	
	![Auf "Run" klicken, um fortzufahren][ApplicationRunWarning]
	
6.	Klicken Sie im Dialogfeld **User Account Control** auf **Yes**.
	
	![Klicken Sie auf "Yes"][UAC]
	
7. Der Hybrid Connection Manager wird heruntergeladen und für Sie installiert.
	
	![Installation][HCMInstalling]
	
8. Sobald die Installation abgeschlossen ist, klicken Sie auf **Close**.
	
	![Klicken Sie auf "Schließen"][HCMInstallComplete]
	
	Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** jetzt **Connected** an.
	
	![Status Verbunden][HCStatusConnected]

Nachdem die Hybridverbindungsinfrastruktur nun vollständig ist, können Sie eine hybride Anwendung erstellen, die diese verwendet.

>[AZURE.NOTE]Die folgenden Abschnitte zeigen, wie Sie eine Hybridverbindung mit einem Mobile Apps-.NET-Back-End-Projekt verwenden.

## Konfigurieren des Mobile App-.NET-Back-End-Projekts zum Herstellen einer Verbindung mit der SQL Server-Datenbank

In App Service ist ein Mobile Apps-.NET-Back-End-Projekt nur eine ASP.NET-Web-App mit einem zusätzlich installierten und initialisierten Mobile Apps-SDK. Um Ihre Web-App als Mobile Apps-Back-End zu verwenden, müssen Sie das [Mobile Apps-.NET-Back-End-SDK herunterladen und initialisieren](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).

Für Mobile Apps müssen Sie in diesem Schritt außerdem eine Verbindungszeichenfolge für die lokale Datenbank definieren und das Back-End zur Verwendung dieser Verbindung konfigurieren.

1. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei „Web.config“ für Ihr Mobile App-.NET-Back-End, suchen Sie den Abschnitt **connectionStrings**, und fügen Sie einen neuen SqlClient-Eintrag, der auf die lokale SQL Server-Datenbank verweist, wie folgt hinzu:

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	Ersetzen Sie `<**secure_password**>` in dieser Zeichenfolge durch das Kennwort, das Sie für *HybridConnectionLogin* erstellt haben.

3. Klicken Sie in Visual Studio auf **Speichern**, um die Datei "Web.config" zu speichern.

	> [AZURE.NOTE]Diese Verbindungseinstellung wird bei Ausführung auf dem lokalen Computer verwendet. Bei der Ausführung in Azure wird diese Einstellung durch die Verbindungseinstellung überschrieben, die im Portal definiert wird.

4. Erweitern Sie im den Ordner **Modelle**, und öffnen Sie die Datenmodelldatei, deren Name mit *Context.cs* endet.

6. Ändern Sie den **DbContext**-Instanzkonstruktor zum Übergeben des Werts `OnPremisesDBConnection` an den **DbContext**-Basiskonstruktor (ähnlich wie im folgenden Codeausschnitt):

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	Der Dienst verwendet jetzt die neue Verbindung zur SQL Server-Datenbank.

## Aktualisieren des Mobile App-Back-Ends für die Verwendung lokaler Verbindungszeichenfolgen

Als Nächstes müssen Sie eine App-Einstellung für diese neue Verbindungszeichenfolge hinzufügen, damit sie von Azure verwendet werden kann.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) im Web-App-Back-End-Code für die mobile App auf **Alle Einstellungen** und dann auf **Anwendungseinstellungen**.

3. Führen Sie im Blatt **Web-App-Einstellungen** einen Bildlauf nach unten zu **Verbindungszeichenfolgen** durch, und fügen Sie eine neue **SQL Server**-Verbindungszeichenfolge mit dem Namen `OnPremisesDBConnection` mit einem Wert wie `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>` hinzu.

	Ersetzen Sie `<**secure_password**>` durch das sichere Kennwort für Ihre lokale Datenbank.

	![Verbindungszeichenfolge für die lokale Datenbank](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. Klicken Sie auf **Speichern**, um die Hybridverbindung und die soeben erstellte Verbindungszeichenfolge zu speichern.

An diesem Punkt können Sie das Serverprojekt erneut veröffentlichen und die neue Verbindung mit Ihren vorhandenen Mobile Apps-Clients testen. Die Daten werden unter Verwendung der Hybridverbindung aus der lokalen Datenbank gelesen und in diese geschrieben.

<a name="NextSteps"></a>
## Nächste Schritte ##

- Informationen zum Erstellen einer ASP.NET-Webanwendung, die eine Hybridverbindung verwendet, finden Sie unter [Verbindung mit einem lokalen SQL Server von einer Azure-Website mithilfe von Hybridverbindungen](http://go.microsoft.com/fwlink/?LinkID=397979). 

### Zusätzliche Ressourcen

[Überblick über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Einführung in Hybridverbindungen von Josh Twist (Channel 9-Video)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Website zu Hybridverbindungen](https://azure.microsoft.com/services/biztalk-services/)

[BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Skalieren", "Konfigurieren" und "Hybridverbindungen"](../biztalk-dashboard-monitor-scale-tabs/)

[Erstellen einer echten Hybrid-Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Verbindung mit einem lokalen SQL Server über Azure Mobile Services mithilfe von Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=AcomDC_0211_2016-->