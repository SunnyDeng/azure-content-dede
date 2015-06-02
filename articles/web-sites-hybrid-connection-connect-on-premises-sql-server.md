<properties 
	pageTitle="Verbinden eines lokalen SQL-Servers von einer Web-App in Azure App Service über Hybridverbindungen" 
	description="Erstellen einer Web-App in Microsoft Azure und Herstellen einer Verbindung von der Web-App zu einer lokalen SQL Server-Datenbank"
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="cephalin"/>

# Verbinden eines lokalen SQL-Servers von einer Web-App in Azure App Service über Hybridverbindungen

Hybridverbindungen ermöglichen die Verbindung von [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps mit lokalen Ressourcen, die einen statischen TCP-Port verwenden. Zu den unterstützten Ressourcen gehören Microsoft SQL Server, MySQL, HTTP Web APIs, Mobile Services und die meisten benutzerdefinierten Webdienste.

In diesem Lernprogramm erfahren Sie, wie Sie eine App Service-Web-App im [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) erstellen, die Web-App mit der neuen Funktion "Hybridverbindung" mit Ihrer lokalen SQL Server-Datenbank verbinden, eine einfache ASP.NET-Anwendung erstellen, welche die Hybridverbindung verwendet, und die Anwendung auf der App Service-Web-App bereitstellen. Die fertige Web-App in Azure speichert Benutzeranmeldeinformationen in einer lokalen Mitgliedschaftsdatenbank. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure oder ASP.NET haben.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

> [AZURE.NOTE]Der Web-App-Teil der Funktion "Hybridverbindung" ist nur im [Azure-Vorschauportal](https://portal.azure.com) verfügbar. Informationen zum Erstellen einer Verbindung in BizTalk-Diensten finden Sie unter [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Voraussetzungen ##

Zum Durchführen des Lernprogramms benötigen Sie folgende Produkte: Alle Produkte sind als kostenlose Versionen verfügbar, sodass Sie kostenlos mit der Entwicklung für Azure beginnen können.

- **Azure-Abonnement** - Informationen zu einem kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion](/pricing/free-trial/). 

- **Visual Studio 2013** - Zum Herunterladen einer kostenlosen Testversion von Visual Studio 2013 besuchen Sie [Visual Studio Downloads](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installieren Sie diese Version, bevor Sie fortfahren.

- **Microsoft .NET Framework 3.5 Service Pack 1** - Wenn Sie das Betriebssystem Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 oder Windows Server 2008 R2 verwenden, können Sie diese Komponente in Systemsteuerung > "Programme und Funktionen" bzw. "Programme und Features" > "Windows-Funktionen ein- oder ausschalten" aktivieren. Andernfalls können Sie sie vom [Microsoft Download Center](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22) herunterladen.

- **SQL Server 2014 Express with Tools** - Laden Sie Microsoft SQL Server Express kostenlos von der [Microsoft Web Platform Datenbank-Seite](http://www.microsoft.com/web/platform/database.aspx) herunter. Wählen Sie die Version **Express** (nicht LocalDB). Die Version **Express with Tools** enthält das Produkt SQL Server Management Studio, das Sie in diesem Lernprogramm verwenden werden.

- **SQL Server Management Studio Express** - Dieses Produkt ist im oben angegebenen Download "SQL Server 2014 Express with Tools" enthalten, Sie müssen es jedoch getrennt installieren. Sie können es von der [Download-Seite für SQL Server Express](http://www.microsoft.com/web/platform/database.aspx) herunterladen und installieren.

In diesem Lernprogramm wird davon ausgegangen, dass Sie ein Azure-Abonnement besitzen, Visual Studio 2013 installiert haben und .NET Framework 3.5 installiert oder aktiviert haben. In diesem Lernprogramm wird gezeigt, wie Sie SQL Server 2014 Express in einer Konfiguration installieren, die sich gut für die Azure-Funktion "Hybridverbindungen" eignet (eine Standardinstanz mit einem statischen TCP-Port). Bevor Sie mit dem Lernprogramm beginnen, laden Sie "SQL Server 2014 Express with Tools" vom oben angegebenen Speicherort herunter, sofern Sie SQL Server nicht installiert haben.

### Hinweise ###
Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht.

Der Computer, auf dem Sie den lokalen Hybridverbindungs-Manager-Agent installieren:

- Sie benötigen Outbound-Konnektivität zu Azure über:

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Warum</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Erforderlich</strong> für HTTP-Port für Zertifikatüberprüfung und optional für Datenkonnektivität.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Optional</strong> für Datenkonnektivität. Wenn keine ausgehende Konnektivität zu 443 verfügbar ist, wird TCP-Port 80 verwendet.</td>
    </tr>
	<tr>
        <td>5671 und 9352</td>
        <td><strong>Empfohlen</strong>, jedoch für Datenkonnektivität optional. Beachten Sie, dass dieser Modus normalerweise für mehr Durchsatz sorgt. Wenn Outbound-Konnektivität zu diesen Ports nicht verfügbar ist, wird TCP-Port 443 verwendet.</td>
	</tr>
</table>

- Muss in der Lage sein, *Hostname*:*Portnummer*Ihrer lokalen Ressource zu erreichen. 

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.

Wenn Sie SQL Server bereits in einer Konfiguration und Umgebung installiert haben, welche die oben beschriebenen Kriterien erfüllen, können Sie gleich mit [Erstellen einer lokalen SQL Server-Datenbank](#CreateSQLDB) beginnen.

<a name="InstallSQL"></a>
## A: Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank ##

In diesem Abschnitt wird erläutert, wie Sie SQL Server Express installieren, TCP/IP aktivieren und eine Datenbank erstellen, damit Ihre Webanwendung in der Azure-Vorschauumgebung funktioniert.

### SQL Server Express installieren ###

1. Um SQL Server Express zu installieren, führen Sie die Datei **SQLEXPRWT_x64_ENU.exe** oder **SQLEXPR_x86_ENU.exe** aus, die Sie heruntergeladen haben. Der SQL Server-Installationscenter-Assistent wird geöffnet.
	
	![SQL Server-Installation][SQLServerInstall]
	
2. Wählen Sie **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation**. Befolgen Sie die Anweisungen, und übernehmen Sie die Standardeinstellungen, bis Sie zur Seite **Instanzkonfiguration** gelangen.
	
3. Wählen Sie auf der Seite **Instanzkonfiguration** die Option **Standardinstanz**.
	
	![Standardinstanz wählen][ChooseDefaultInstance]
	
	Standardmäßig überwacht die Standardinstanz von SQL Server den statischen Port 1433 auf Anforderungen von SQL Server-Clients, und das ist das Verhalten, das von der Funktion "Hybridverbindungen" vorausgesetzt wird. Benannte Instanzen verwenden dynamische Ports und UDP, die von Hybridverbindungen nicht unterstützt werden.
	
4. Übernehmen Sie die Standardeinstellungen auf der Seite **Serverkonfiguration**.
	
5. Wählen Sie auf der Seite **Datenbankmodulkonfiguration** unter **Authentifizierungsmodus** die Option **Gemischter Modus (SQL Server-Authentifizierung und Windows-Authentifizierung)**, und geben Sie ein Kennwort an.
	
	![Wählen Sie "Gemischter Modus"][ChooseMixedMode]
	
	In diesem Lernprogramm verwenden Sie die SQLServer-Authentifizierung. Merken Sie sich unbedingt das Kennwort, das Sie angeben, weil Sie es später benötigen werden.
	
6. Bearbeiten Sie die restlichen Schritte des Assistenten, um die Installation abzuschließen.

### TCP/IP aktivieren ###
Zum Aktivieren von TCP/IP verwenden Sie den SQL Server-Konfigurations-Manager, der zusammen mit SQL Server Express installiert wurde. Führen Sie zunächst die in [Aktivieren des TCP/IP-Netzwerkprotokolls für SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) beschriebenen Schritte aus, bevor Sie fortfahren.

<a name="CreateSQLDB"></a>
### Eine lokale SQL Server-Datenbank erstellen ###

Für Ihre Visual Studio-Webanwendung benötigen Sie eine Mitgliedschaftsdatenbank, auf die Azure zugreifen kann. Hierzu ist eine SQL Server- oder SQL Server Express-Datenbank erforderlich (nicht die LocalDB-Datenbank, die von der MVC-Vorlage standardmäßig verwendet wird). Daher erstellen Sie als Nächstes die Mitgliedschaftsdatenbank.

1. Stellen Sie in SQL Server Management Studio eine Verbindung mit der SQL Server-Instanz her, die Sie gerade installiert haben. (Wenn der Dialog **Mit Server verbinden** nicht automatisch angezeigt wird, navigieren Sie zu **Objekt-Explorer** im linken Bereich. Klicken Sie auf **Verbinden** und anschließend auf **Datenbankmodul**.) 	
	![Verbindung mit dem Server herstellen][SSMSConnectToServer]
	
	Wählen Sie unter **Servertyp** die Option **Datenbankmodul**. Als **Servername** können Sie **localhost** oder den Namen des Computers, den Sie benutzen, verwenden. Wählen Sie **SQL Server-Authentifizierung**, und melden Sie sich dann mit dem Benutzernamen und dem Kennwort des zuvor erstellten Benutzers an.
	
2. Um eine neue Datenbank mit SQL Server Management Studio zu erstellen, klicken Sie in Objekt-Explorer mit der rechten Maustaste auf **Datenbanken** und anschließend auf **Neue Datenbank**.
	
	![Neue Datenbank erstellen][SSMScreateNewDB]
	
3. Geben Sie im Dialogfeld **Neue Datenbank** als Datenbankname "MembershipDB" ein, und klicken Sie dann auf **OK**.
	
	![Datenbankname angeben][SSMSprovideDBname]
	
	Beachten Sie, dass Sie im Moment keine Änderungen an der Datenbank vornehmen. Die Mitgliedschaftsinformationen werden automatisch später von Ihrer Webanwendung hinzugefügt, wenn Sie sie ausführen.
	
4. Wenn Sie in Objekt-Explorer **Datenbanken** erweitern, sehen Sie, dass die Mitgliedschaftsdatenbank erstellt wurde.
	
	![MembershipDB wurde erstellt][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## B. Erstellen einer Web-App im Azure-Vorschauportal ##

> [AZURE.NOTE]Wenn Sie im Azure-Vorschauportal bereits eine Web-App erstellt haben, die Sie für dieses Lernprogramm verwenden möchten, können Sie gleich mit [Eine Hybridverbindung und einen BizTalk-Dienst erstellen](#CreateHC) fortfahren.

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com) auf **Neu** > **Web + Mobile** > **Web-App**.
	
	![Schaltfläche "Neu"][New]
	
2. Konfigurieren Sie Ihre Web-App, und klicken Sie dann auf **Erstellen**.
	
	![Name der Website][WebsiteCreationBlade]
	
3. Nach einigen Augenblicken wird die Web-App erstellt, und das Blatt der Web-App wird angezeigt. Das Fenster ist ein vertikal bildlauffähiges Dashboard, in dem Sie die Web-App verwalten können.
	
	![Ausgeführte Website][WebSiteRunningBlade]
	
	Um zu überprüfen, ob die Web-App aktiv ist, können Sie auf das Symbol **Durchsuchen** klicken, um die Standardseite anzuzeigen.
	
Als Nächstes erstellen Sie eine Hybridverbindung und einen BizTalk-Dienst für die Web-App.

<a name="CreateHC"></a>
## C. Erstellen einer Hybridverbindung und eines BizTalk-Diensts ##

1. Zurück im Portal blättern Sie im Blatt der Web-App nach unten und klicken auf **Hybridverbindungen**.
	
	![Hybridverbindungen][CreateHCHCIcon]
	
2. Klicken Sie im Blatt "Hybridverbindungen" auf **Hinzufügen** > **Neue Hybridverbindung**.
	
3. Geben Sie im Fensterbereich **Create hybrid connection** Folgendes ein:
	- Geben Sie unter **Name** einen Namen für die Verbindung ein.
	- Geben Sie als **Hostname** den Computernamen Ihres SQL Server-Hostcomputers ein.
	- Geben Sie bei **Port** 1433 ein (Standardport für SQL Server).
	- Klicken Sie auf **Biz Talk Service**, und geben Sie einen Namen für den BizTalk-Dienst ein.
	
	![Hybridverbindung erstellen][TwinCreateHCBlades]
		
5. Klicken Sie zweimal auf **OK**.

	Nach Abschluss des Prozesses leuchtet im Bereich **Benachrichtigungen** der Text **ERFOLG** in grüner Schrift, und das Blatt **Hybridverbindung** zeigt die neue Hybridverbindung mit dem Status **Nicht verbunden** an.
	
	![Eine Hybridverbindung wurde erstellt][CreateHCOneConnectionCreated]
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>
## D. Installieren des lokalen Hybrid Connection Manager, um die Verbindung zu vervollständigen ##

1. Klicken Sie im Blatt **Hybridverbindungen** auf die Hybridverbindung, die Sie gerade erstellt haben, und klicken Sie dann auf **Listener Setup**.
	
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

Nachdem die Hybridverbindungsinfrastruktur jetzt vollständig ist, werden Sie eine Webanwendung erstellen, welche diese verwendet.

<a name="CreateASPNET"></a>
## E. Ein grundlegendes ASP.NET-Webprojekt erstellen, die Datenbank-Verbindungszeichenfolge bearbeiten und das Projekt lokal ausführen ##

### Ein grundlegendes ASP.NET-Projekt erstellen ###
1. Erstellen Sie in Visual Studio über das Menü **Datei** ein neues Projekt:
	
	![Neues Visual Studio-Projekt][HCVSNewProject]
	
2. Wählen Sie im Abschnitt **Vorlagen** des Dialogfelds **Neues Projekt** die Option **Web** und anschließend **ASP.NET-Webanwendung**, und klicken Sie dann auf **OK**.
	
	![Wählen Sie "ASP.NET-Webanwendung"][HCVSChooseASPNET]
	
3. Wählen Sie im Dialogfeld **New ASP.NET-Projekt** die Option **MVC**, und klicken Sie dann auf **OK**.
	
	![Wählen Sie "MVC"][HCVSChooseMVC]
	
4. Sobald das Projekt erstellt wurde, wird die Seite mit der Infodatei der Anwendung geöffnet. Führen Sie das Webprojekt noch nicht aus.
	
	![Seite mit der Infodatei][HCVSReadmePage]

### Bearbeiten Sie die Datenbank-Verbindungszeichenfolge für die Anwendung ###

In diesem Schritt bearbeiten Sie die Verbindungszeichenfolge, der Ihre Anwendung entnimmt, wo sich die lokale SQL Server Express-Datenbank befindet. Die Verbindungszeichenfolge befindet sich in der Web.config-Datei der Anwendung, welche die Konfigurationsdaten der Anwendung enthält.

> [AZURE.NOTE]Um sicherzustellen, dass Ihre Anwendung die Datenbank, die Sie in SQL Server Express erstellt haben, und nicht die Datenbank aus der LocalDB-Standardinstanz von Visual Studio verwendet, müssen Sie diesen Schritt unbedingt fertigstellen, bevor Sie das Projekt ausführen.

1. Doppelklicken Sie im Projektmappen-Explorer auf die Datei "Web.config".
	
	![Web.config][HCVSChooseWebConfig]
	
2. Bearbeiten Sie den Abschnitt **connectionStrings**, sodass er auf die SQL Server-Datenbank auf Ihrem lokalen Computer verweist, und halten Sie sich dabei an die Syntax im folgenden Beispiel:
	
	![Verbindungszeichenfolge][HCVSConnectionString]
	
	Beachten Sie beim Erstellen der Verbindungszeichenfolge Folgendes:
	
	- Wenn Sie eine Verbindung mit einer benannten Instanz statt mit der Standardinstanz herstellen (beispielsweise IhrServer\\SQLEXPRESS), müssen Sie den SQL Server für die Verwendung statischer Ports konfigurieren. Informationen zum Konfigurieren statischer Ports finden Sie unter [Konfigurieren von SQL Server zum Abhören eines bestimmten Ports](http://support.microsoft.com/kb/823938). Standardmäßig verwenden benannte Instanzen UDP und dynamische Ports, was von Hybridverbindungen nicht unterstützt wird. 
	
	- Es wird empfohlen, dass Sie den Port (standardmäßig 1433 wie im Beispiel) in der Verbindungszeichenfolge angeben, damit Sie sicher sein können, dass auf ihrem lokalen SQL Server TCP aktiviert ist und der richtige Port verwendet wird.
	
	- Denken Sie daran, die Verbindung unter Verwendung der SQL Server-Authentifizierung herzustellen und die Benutzer-ID und das Kennwort in der Verbindungszeichenfolge anzugeben.
	
3. Klicken Sie in Visual Studio auf **Speichern**, um die Datei "Web.config" zu speichern.

### Projekt lokal ausführen und einen neuen Benutzer registrieren ###

1. Führen Sie Ihr Webprojekt jetzt lokal aus, indem Sie auf die Schaltfläche "Durchsuchen" unter "Debug" klicken. In diesem Beispiel wird Internet Explorer verwendet.
	
	![Projekt ausführen][HCVSRunProject]
	
2. Wählen Sie in der oberen rechten Ecke der Standardwebseite **Registrieren**, um ein neues Konto zu registrieren:
	
	![Registrieren Sie ein neues Konto][HCVSRegisterLocally]
	
3. Geben Sie einen Benutzernamen und ein Kennwort ein:
	
	![Geben Sie Benutzernamen und Kennwort ein][HCVSCreateNewAccount]
	
	Daraufhin wird automatisch auf dem lokalen SQL Server eine Datenbank erstellt, in der die Mitgliedschaftsinformationen für Ihre Anwendung gespeichert werden. Eine der Tabellen (**dbo.AspNetUsers**) enthält die Web-App-Benutzeranmeldeinformationen, wie die Anmeldeinformationen, die Sie gerade eingegeben haben. Sie werden diese Tabelle weiter hinten im Lernprogramm sehen.
	
4. Schließen Sie das Browserfenster der Standardwebseite. Damit wird die Ausführung der Anwendung in Visual Studio beendet.

Sie sind jetzt bereit für den nächsten Schritt, in dem Sie die Anwendung für Azure veröffentlichen und testen.

<a name="PubNTest"></a>
## F. Veröffentlichen und Testen der Webanwendung in Azure ##

Nun veröffentlichen Sie Ihre Anwendung auf Ihrer App Service-Web-App und testen sie anschließend, um zu sehen, wie die zuvor von Ihnen konfigurierte Hybridverbindung verwendet wird, um Ihre Web-App mit der Datenbank auf Ihrem lokalen Computer zu verbinden.

### Die Webanwendung veröffentlichen ###

1. Sie können Ihr Veröffentlichungsprofil für die App Service-Web-App im Azure-Vorschauportal herunterladen. Klicken Sie im Blatt für Ihre Web-App auf **Veröffentlichungsprofil herunterladen**, und speichern Sie die Datei dann auf Ihrem Computer.
	
	![Veröffentlichungsprofil herunterladen][PortalDownloadPublishProfile]
	
	Als Nächstes importieren Sie diese Datei in Ihre Visual Studio-Webanwendung.
	
2. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Veröffentlichen**.
	
	![Wählen Sie "Veröffentlichen"][HCVSRightClickProjectSelectPublish]
	
3. Wählen Sie im Dialogfeld **Web veröffentlichen** auf der Registerkarte **Profil** die Option **Importieren**.
	
	![Importieren][HCVSPublishWebDialogImport]
	
4. Navigieren Sie zu dem heruntergeladenen Veröffentlichungsprofil, markieren Sie es, und klicken Sie dann auf **OK**.
	
	![Navigieren Sie zum Profil][HCVSBrowseToImportPubProfile]
	
5. Ihre Veröffentlichungsinformationen werden importiert und auf der Registerkarte **Verbindung** im Dialog angezeigt.
	
	![Auf "Veröffentlichen" klicken][HCVSClickPublish]
	
	Klicken Sie auf **Veröffentlichen**.
	
	Sobald die Veröffentlichung abgeschlossen ist, wird der Browser gestartet und zeigt Ihre nunmehr bekannte ASP.NET-Anwendung an – die sich jetzt allerdings in der Azure-Cloud befindet!

Als Nächstes verwenden Sie die Live-Webanwendung, um die Hybridverbindung zu überprüfen.

### Die fertiggestellte Webanwendung in Azure testen ###

1. Wählen Sie oben rechts auf der Webseite in Azure **Anmelden** aus.
	
	![Anmeldung testen][HCTestLogIn]
	
2. Ihre App Service-Web-App ist jetzt mit der Mitgliedschaftsdatenbank der Webanwendung auf dem lokalen Computer verbunden. Um dies zu überprüfen, melden Sie sich mit den gleichen Anmeldeinformationen ein, die Sie zuvor in die lokale Datenbank eingegeben haben.
	
	![Begrüßung][HCTestHelloContoso]
	
3. Um die neue Hybridverbindung weiter zu testen, melden Sie sich von Ihrer Azure-Webanwendung ab, und registrieren Sie sich als ein anderer Benutzer. Geben Sie einen anderen Benutzernamen und ein Kennwort ein, und klicken Sie auf **Registrieren**.
	
	![Testregistrierung eines weiteren Benutzers][HCTestRegisterRelecloud]
	
4. Um zu überprüfen, ob die Anmeldeinformationen des neuen Benutzers über die Hybridverbindung in der lokalen Datenbank gespeichert wurden, öffnen Sie SQL Management Studio auf Ihrem lokalen Computer. Erweitern Sie in Objekt-Explorer die Datenbank **MembershipDB**, und erweitern Sie anschließend **Tabellen**. Klicken Sie mit der rechten Maustaste auf die Mitgliedschaftstabelle **dbo.AspNetUsers**, und wählen Sie **Select Top 1000 Rows** (Die ersten 1.000 Zeilen auswählen), um die Ergebnisse anzuzeigen.
	
	![Zeigen Sie die Ergebnisse an][HCTestSSMSTree]
	
5. Die lokale Mitgliedschaftstabelle enthält nun beide Konten: das Konto, das Sie lokal erstellt haben, und das Konto, das Sie in der Azure-Cloud erstellt haben. Das Konto, das Sie in der Cloud erstellt haben, wurde über die Azure-Funktion "Hybridverbindung" in der lokalen Datenbank gespeichert.
	
	![Registrierte Benutzer in der lokalen Datenbank][HCTestShowMemberDb]
	
Sie haben jetzt eine ASP.NET-Webanwendung erstellt und bereitgestellt, die eine Hybridverbindung zwischen einer Web-App in der Azure-Cloud und einer lokalen SQL Server-Datenbank verwendet. Glückwunsch!

## Weitere Informationen ##
[Überblick über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Einführung in Hybridverbindungen von Josh Twist (Channel 9-Video)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Überblick über Hybridverbindungen](/services/biztalk-services/)

[BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Skalieren", "Konfigurieren" und "Hybridverbindungen"](../biztalk-dashboard-monitor-scale-tabs/)

[Erstellen einer echten Hybrid-Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Verbinden mit einem lokalen SQL Server über einen mobilen Azure-Dienst mithilfe von Hybridverbindungen](mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Verbinden mit einem lokalen SQL Server über Azure Mobile Services mithilfe von Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Übersicht über ASP.NET Identity](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]
 

<!-- IMAGES -->
[SQLServerInstall]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!--HONumber=54-->