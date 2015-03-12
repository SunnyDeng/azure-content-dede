<properties 
	pageTitle="Schrittweise Anleitungen für Hybridverbindungen: Verbinden mit einem lokalen SQL Server von einer Azure-Website aus" 
	description="Erstellen einer Website in Microsoft Azure und Herstellen einer Verbindung von der Website zu einer lokalen SQL Server-Datenbank" 
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

#Eine Verbindung mit einem lokalen SQL Server von einer Azure-Website mit Hybridverbindungen herstellen

##Einführung##
Mit Hybridverbindungen können Microsoft Azure-Websites mit lokalen Ressourcen, die einen statischen TCP-Port verwenden, verbunden werden. Zu den unterstützten Ressourcen gehören Microsoft SQL Server, MySQL, HTTP Web APIs, Mobile Services und die meisten benutzerdefinierten Webdienste. 

In diesem Lernprogramm erfahren Sie, wie Sie eine Website im Azure-Vorschauportal erstellen, die Website mit der neuen Funktion "Hybridverbindung" mit Ihrer lokalen SQL Server-Datenbank verbinden, eine einfache ASP.NET-Webanwendung erstellen, welche die Hybridverbindung verwendet, und die Anwendung auf der Azure-Website bereitstellen. Die fertige Website in Azure speichert Anmeldeinformationen in einer lokalen Mitgliedschaftsdatenbank. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure oder ASP.NET haben.

> [AZURE.NOTE] Der Website-Teil der hybriden Verbindungsfunktion ist nur im [Azure-Vorschauportal](https://portal.azure.com) verfügbar. Informationen zum Erstellen einer Verbindung in BizTalk-Diensten finden Sie unter [Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

##Voraussetzungen##
Zum Durchführen des Lernprogramms benötigen Sie folgende Produkte: Alle Produkte sind als kostenlose Versionen verfügbar, sodass Sie kostenlos mit der Entwicklung für Azure beginnen können.

- **Azure-Abonnement** - Informationen zu einem kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

- **Visual Studio 2013** - Zum Herunterladen einer kostenlosen Testversion von Visual Studio 2013 besuchen Sie [Visual Studio Downloads](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installieren Sie diese Version, bevor Sie fortfahren.

- **Microsoft .NET Framework 3.5 Service Pack 1** - Wenn Sie das Betriebssystem Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 oder Windows Server 2008 R2 verwenden, können Sie diese Komponente in Systemsteuerung > "Programme und Funktionen" bzw. "Programme und Features" > "Windows-Funktionen ein- oder ausschalten" aktivieren. Andernfalls können Sie es aus dem [Microsoft Download Center](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22) herunterladen.

- **SQL Server 2014 Express with Tools** - Laden Sie Microsoft SQL Server Express kostenlos von der [Microsoft Web Platform Datenbank-Seite](http://www.microsoft.com/web/platform/database.aspx) herunter. Wählen Sie die Version **Express** (nicht LocalDB). Die Version **Express with Tools** enthält das Produkt SQL Server Management Studio, das Sie in diesem Lernprogramm verwenden werden.

- **SQL Server Management Studio Express** - Dieses Produkt ist im oben angegebenen Download "SQL Server 2014 Express with Tools" enthalten, Sie müssen es jedoch getrennt installieren. Sie können es von der [Download-Seite für SQL Server Express](http://www.microsoft.com/web/platform/database.aspx) herunterladen und installieren.

In diesem Lernprogramm wird davon ausgegangen, dass Sie ein Azure-Abonnement besitzen, Visual Studio 2013 installiert haben und .NET Framework 3.5 installiert oder aktiviert haben. In diesem Lernprogramm wird gezeigt, wie Sie SQL Server 2014 Express in einer Konfiguration installieren, die sich gut für die Azure-Funktion "Hybridverbindungen" eignet (eine Standardinstanz mit einem statischen TCP-Port). Bevor Sie mit dem Lernprogramm beginnen, laden Sie "SQL Server 2014 Express with Tools" vom oben angegebenen Speicherort herunter, sofern Sie SQL Server nicht installiert haben.

### Hinweise: ###
Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht. 

Der Computer, auf dem Sie den lokalen Hybridverbindungs-Manager-Agent installieren:

- Sie benötigen Outbound-Konnektivität zu Azure über:

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Grund</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Erforderlich</strong> für HTTP-Port für Zertifikatüberprüfung.</td>
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

- Sie müssen in der Lage sein,  *hostname*:*Portnummer* Ihrer lokalen Ressource zu erreichen. 

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.

Wenn Sie SQL Server bereits in einer Konfiguration und Umgebung installiert haben, welche die oben beschriebenen Kriterien erfüllen, können Sie gleich mit [Erstellen einer lokalen SQL Server-Datenbank](#CreateSQLDB) beginnen.

##Themen in diesem Artikel
[A. Installieren von SQL Server Express , Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank](#InstallSQL)

[B. Erstellen einer Website im Azure-Vorschauportal](#CreateSite)

[C. Erstellen einer Hybridverbindung und eines BizTalk-Diensts](#CreateHC)

[D. Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung](#InstallHCM)

[E. Erstellen eines grundlegenden ASP.NET-Webprojekts, Bearbeiten der Datenbank-Verbindungszeichenfolge und lokales Ausführen des Projekts](#CreateASPNET)

[F. Veröffentlichen und Testen der Webanwendung in Azure](#PubNTest)

<a name="InstallSQL"></a>
## A. Installieren von SQL Server Express , Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank

In diesem Abschnitt wird erläutert, wie Sie SQL Server Express installieren, TCP/IP aktivieren und eine Datenbank erstellen, damit Ihre Webanwendung in der Azure-Vorschauumgebung funktioniert.

###Installieren von SQL Server 2008 Express

1. Um SQL Server Express zu installieren, führen Sie die Datei **SQLEXPRWT_x64_ENU.exe** oder **SQLEXPR_x86_ENU.exe** aus, die Sie heruntergeladen haben. Der SQL Server-Installationscenter-Assistent wird geöffnet.
	
	![SQL Server Install][SQLServerInstall]
	
2. Wählen Sie **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation**. Befolgen Sie die Anweisungen, und übernehmen Sie die Standardeinstellungen, bis Sie zur Seite **Instanzkonfiguration** gelangen.
	
3. Wählen Sie auf der Seite **Instanzkonfiguration** die Option **Standardinstanz**.
	
	![Choose Default Instance][ChooseDefaultInstance]
	
	Standardmäßig überwacht die Standardinstanz von SQL Server den statischen Port 1433 auf Anforderungen von SQL Server-Clients, und das ist das Verhalten, das von der Funktion "Hybridverbindungen" vorausgesetzt wird. Benannte Instanzen verwenden dynamische Ports und UDP, die von Hybridverbindungen nicht unterstützt werden.
	
4. Übernehmen Sie die Standardeinstellungen auf der Seite **Serverkonfiguration**.
	
5. Wählen Sie auf der Seite **Datenbankmodulkonfiguration** unter **Authentifizierungsmodus** die Option **Gemischter Modus (SQL Server-Authentifizierung und Windows-Authentifizierung)**, und geben Sie ein Kennwort an.
	
	![Choose Mixed Mode][ChooseMixedMode]
	
	In diesem Lernprogramm verwenden Sie die SQL Server-Authentifizierung. Merken Sie sich unbedingt das Kennwort, das Sie angeben, weil Sie es später benötigen werden.
	
6. Bearbeiten Sie die restlichen Schritte des Assistenten, um die Installation abzuschließen.

###Aktivieren von TCP/IP
Zum Aktivieren von TCP/IP verwenden Sie den SQL Server-Konfigurations-Manager, der zusammen mit SQL Server Express installiert wurde. Führen Sie zunächst die in [Aktivieren des TCP/IP-Netzwerkprotokolls für SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) beschriebenen Schritte aus, bevor Sie fortfahren.

<a name="CreateSQLDB"></a>
###Erstellen einer lokalen SQL Server-Datenbank

Für Ihre Visual Studio-Webanwendung benötigen Sie eine Mitgliedschaftsdatenbank, auf die Azure zugreifen kann. Hierzu ist eine SQL Server- oder SQL Server Express-Datenbank erforderlich (nicht die LocalDB-Datenbank, die von der MVC-Vorlage standardmäßig verwendet wird). Daher erstellen Sie als Nächstes die Mitgliedschaftsdatenbank. 

1. Stellen Sie in SQL Server Management Studio eine Verbindung mit der SQL Server-Instanz her, die Sie gerade installiert haben. (Wenn der Dialog **Mit Server verbinden** nicht automatisch angezeigt wird, navigieren Sie zu **Objekt-Explorer** im linken Bereich. Klicken Sie auf **Verbinden**, und klicken Sie anschließend auf **Datenbankmodul**.) 	
	![Connect to Server][SSMSConnectToServer]
	
	Wählen Sie unter **Servertyp** die Option **Datenbankmodul**. Als **Servername** können Sie **localhost** oder den Namen des Computers, den Sie benutzen, verwenden. Wählen Sie **SQL Server-Authentifizierung**, und melden Sie sich dann mit dem sa-Benutzername und dem Kennwort an, das Sie zuvor erstellt haben. 
	
2. Um eine neue Datenbank mit SQL Server Management Studio zu erstellen, klicken Sie in Objekt-Explorer mit der rechten Maustaste auf **Datenbanken** und anschließend auf **Neue Datenbank**.
	
	![Create new database][SSMScreateNewDB]
	
3. Geben Sie im Dialogfeld **Neue Datenbank** als Datenbankname "MembershipDB" ein, und klicken Sie dann auf **OK**. 
	
	![Provide database name][SSMSprovideDBname]
	
	Beachten Sie, dass Sie im Moment keine Änderungen an der Datenbank vornehmen. Die Mitgliedschaftsinformationen werden automatisch später von Ihrer Webanwendung hinzugefügt, wenn Sie sie ausführen.
	
4. Wenn Sie in Objekt-Explorer **Datenbanken** erweitern, sehen Sie, dass die Mitgliedschaftsdatenbank erstellt wurde.
	
	![MembershipDB created][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## B. Erstellen einer Website im Azure-Vorschauportal

> [AZURE.NOTE] Wenn Sie bereits eine Website im Azure-Vorschauportal erstellt haben, die Sie für dieses Lernprogramm verwenden möchten, können Sie direkt zu [Erstellen einer Hybridverbindung und eines BizTalk-Diensts](#CreateHC) wechseln und dort beginnen.

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
## C. Eine Hybridverbindung und einen BizTalk-Dienst erstellen ##

1. Führen Sie im Vorschauportal im Fensterbereich einen Bildlauf nach unten zu Ihrer Website aus, und wählen Sie **Hybridverbindungen** aus.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Klicken Sie unter "Hybridverbindungen" auf **Hinzufügen**.
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. Der Fensterbereich **Hybridverbindung hinzufügen** wird geöffnet.  Da es sich um die erste Hybridverbindung handelt, ist die Option **Neue Hybridverbindung** bereits ausgewählt, und der Fensterbereich **Hybridverbindung erstellen** wird geöffnet.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Geben Sie im Fensterbereich **Hybridverbindung erstellen** Folgendes ein:
	- Geben Sie in **Name** einen Namen für die Verbindung ein.
	- Geben Sie als **Hostname** den Computernamen Ihres SQL Server-Hostcomputers ein.
	- Geben Sie bei **Port** 1433 ein (Standardport für SQL Server).
	- Klicken Sie auf **BizTalk-Dienst**.


4. Der Fensterbereich **BizTalk-Dienst erstellen** wird geöffnet. Geben Sie einen Namen für den BizTalk-Dienst ein, und klicken Sie auf **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Der Fensterbereiche **BizTalk-Dienst** erstellen wird geschlossen, und Sie kehren zum Fensterbereich **Hybridverbindung erstellen** zurück.
	
5. Klicken Sie im Fensterbereich "Hybridverbindung erstellen" auf **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Sobald der Prozess abgeschlossen ist, werden Sie im Benachrichtigungsbereich des Portals informiert, dass die Verbindung erfolgreich erstellt wurde.
	
	![Success notification][CreateHCSuccessNotification]
	
7. Im Website-Fensterbereich zeigt das Symbol **Hybridverbindungen** jetzt an, dass eine Hybridverbindung erstellt wurde.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>
## D. Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung

1. Klicken Sie im Website-Fensterbereich auf das Symbol für Hybridverbindungen. 
	
	![Hybrid connections icon][HCIcon]
	
2. Im Fensterbereich **Hybridverbindungen** zeigt die Spalte **Status** für den zuvor hinzugefügten Endpunkt **Nicht verbunden** an. Klicken Sie auf die Verbindung, um sie zu konfigurieren.
	
	![Not connected][NotConnected]
	
	Der Fensterbereich "Hybridverbindung" wird geöffnet.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Klicken Sie im Fensterbereich auf **Listenereinrichtung**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Der Fensterbereich **Eigenschaften von Hybridverbindunen** wird geöffnet. Wählen Sie unter **Lokaler Hybridverbindungs-Manager** die Option **Klicken Sie hier, um die Installation zu starten**.
	
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
	
	![Connected Status][HCStatusConnected]

Nachdem die Hybridverbindungsinfrastruktur jetzt vollständig ist, werden Sie eine Webanwendung erstellen, welche diese verwendet.

<a name="CreateASPNET"></a>
## E. Erstellen eines grundlegenden ASP.NET-Webprojekts, Bearbeiten der Datenbank-Verbindungszeichenfolge und lokales Ausführen des Projekts

###Erstellen eines grundlegenden ASP.NET-Webprojekts
1. Erstellen Sie in Visual Studio über das Menü **Datei** ein neues Projekt:
	
	![New Visual Studio project][HCVSNewProject]
	
2. Wählen Sie im Abschnitt **Vorlagen** des Dialogfelds **Neues Projekt** die Option **Web**, wählen Sie **ASP.NET-Webanwendung**, und klicken Sie dann auf **OK**.
	
	![Choose ASP.NET Web Application][HCVSChooseASPNET]
	
3. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Option **MVC**, und klicken Sie dann auf **OK**.
	
	![Choose MVC][HCVSChooseMVC]
	
4. Sobald das Projekt erstellt wurde, wird die Seite mit der Infodatei der Anwendung geöffnet. Führen Sie das Webprojekt noch nicht aus.
	
	![Readme page][HCVSReadmePage]

###Bearbeiten der Datenbank-Verbindungszeichenfolge für die Anwendung

In diesem Schritt bearbeiten Sie die Verbindungszeichenfolge, der Ihre Anwendung entnimmt, wo sich die lokale SQL Server Express-Datenbank befindet. Die Verbindungszeichenfolge befindet sich in der Web.config-Datei der Anwendung, welche die Konfigurationsdaten der Anwendung enthält. 

> [AZURE.NOTE] Um sicherzustellen, dass Ihre Anwendung die Datenbank, die Sie in SQL Server Express erstellt haben, und nicht die Datenbank aus der LocalDB-Standardinstanz von Visual Studio verwendet, müssen Sie diesen Schritt unbedingt fertigstellen, bevor Sie das Projekt ausführen.

1. Doppelklicken Sie im Projektmappen-Explorer auf die Datei "Web.config".
	
	![Web.config][HCVSChooseWebConfig]
	
2. Bearbeiten Sie den Abschnitt **connectionStrings** so, dass er auf die SQL Server-Datenbank auf Ihrem lokalen Computer verweist, und halten Sie sich dabei an die Syntax im folgenden Beispiel: 
	
	![Connection string][HCVSConnectionString]
	
	Beachten Sie beim Erstellen der Verbindungszeichenfolge Folgendes:
	
	- Wenn Sie eine Verbindung mit einer benannten Instanz statt mit der Standardinstanz herstellen (beispielsweise IhrServer\SQLEXPRESS), müssen Sie den SQL Server für die Verwendung statischer Ports konfigurieren. Informationen zum Konfigurieren statischer Ports finden Sie unter [Konfigurieren von SQL Server zum Abhören eines bestimmten Ports](http://support.microsoft.com/kb/823938). Standardmäßig verwenden benannte Instanzen UDP und dynamische Ports, was von Hybridverbindungen nicht unterstützt wird. 
	
	- Es wird empfohlen, dass Sie den Port (standardmäßig 1433 wie im Beispiel) in der Verbindungszeichenfolge angeben, damit Sie sicher sein können, dass auf ihrem lokalen SQL Server TCP aktiviert ist und der richtige Port verwendet wird. 
	
	- Denken Sie daran, die Verbindung unter Verwendung der SQL Server-Authentifizierung herzustellen und die Benutzer-ID und das Kennwort in der Verbindungszeichenfolge anzugeben.
	
3. Klicken Sie in Visual Studio auf **Speichern**, um die Datei "Web.config" zu speichern.

###Lokales Ausführen des Projekts und Registrieren eines neuen Benutzers

1. Führen Sie Ihr Webprojekt jetzt lokal aus, indem Sie auf die Schaltfläche "Durchsuchen" unter "Debug" klicken. In diesem Beispiel wird Internet Explorer verwendet.
	
	![Run project][HCVSRunProject]
	
2. Wählen Sie in der rechten oberen Ecke der Standardwebseite **Register**, um ein neues Konto zu registrieren:
	
	![Register a new account][HCVSRegisterLocally]
	
3. Geben Sie einen Benutzernamen und ein Kennwort ein:
	
	![Enter user name and password][HCVSCreateNewAccount]
	
	Daraufhin wird automatisch auf dem lokalen SQL Server eine Datenbank erstellt, in der die Mitgliedschaftsinformationen für Ihre Anwendung gespeichert werden. Eine der Tabellen (**dbo.AspNetUsers**) enthält Anmeldeinformationen für Websitebenutzer wie die Anmeldeinformationen, die Sie gerade eingegeben haben. Sie werden diese Tabelle weiter hinten im Lernprogramm sehen.
	
4. Schließen Sie das Browserfenster der Standardwebseite. Damit wird die Ausführung der Anwendung in Visual Studio beendet.

Sie sind jetzt bereit für den nächsten Schritt, in dem Sie die Anwendung für Azure veröffentlichen und testen.

<a name="PubNTest"></a>
## F. Webanwendung in Azure veröffentlichen und testen ##

Nun veröffentlichen Sie Ihre Anwendung auf Ihrer Website in Azure, und dann testen Sie sie, um zu sehen, wie die Hybridverbindung, die Sie zuvor konfiguriert haben, verwendet wird, um Ihre Website-Anwendung mit der Datenbank auf Ihrem lokalen Computer zu verbinden. 

###Die Webanwendung veröffentlichen###

1. Sie können Ihr Veröffentlichungsprofil für die Website im Azure-Portal herunterladen. Wählen Sie im Fensterbereich für Ihre Website **Veröffentlichungsprofil herunterladen**, und speichern Sie die Datei dann auf Ihrem Computer.
	
	![Download publish profile][PortalDownloadPublishProfile]
	
	![Publish profile in downloads folder][HCVSPublishProfileInDownloadsFolder]
	
	Als Nächstes importieren Sie diese Datei in Ihre Visual Studio-Webanwendung. 
	
2. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Veröffentlichen**.
	
	![Select publish][HCVSRightClickProjectSelectPublish]
	
3. Wählen Sie im Dialogfeld **Web veröffentlichen** auf der Registerkarte **Profil** die Option **Importieren**.
	
	![Import][HCVSPublishWebDialogImport]
	
4. Navigieren Sie zu dem heruntergeladenen Veröffentlichungsprofil, markieren Sie es, und klicken Sie dann auf **OK**.
	
	![Browse to profile][HCVSBrowseToImportPubProfile]
	
5. Ihre Veröffentlichungsinformationen werden importiert und auf der Registerkarte **Verbindung** im Dialogfeld angezeigt. 
	
	![Click Publish][HCVSClickPublish]
	
	Klicken Sie auf **Veröffentlichen**.
	
	Sobald die Veröffentlichung abgeschlossen ist, wird der Browser gestartet, und darin wird Ihre nunmehr bekannte Website angezeigt. Die Website befindet sich jetzt allerdings in der Azure-Cloud!

Als Nächstes verwenden Sie die Live-Webanwendung, um die Hybridverbindung zu überprüfen.

###Testen der fertiggestellten Webanwendung in Azure
1. Wählen Sie rechts oben auf der Webseite in Azure **Anmelden** aus.
	
	![Test log in][HCTestLogIn]
	
2. Ihre Azure-Website ist jetzt mit der Mitgliedschaftsdatenbank der Webanwendung auf dem lokalen Computer verbunden. Um dies zu überprüfen, melden Sie sich mit den gleichen Anmeldeinformationen ein, die Sie zuvor in die lokale Datenbank eingegeben haben.
	
	![Hello greeting][HCTestHelloContoso]
	
3. Um die neue Hybridverbindung weiter zu testen, melden Sie sich von Ihrer Azure-Webanwendung ab, und registrieren Sie sich als ein anderer Benutzer. Geben Sie einen anderen Benutzernamen und ein Kennwort ein, und klicken Sie auf **Registrieren**.
	
	![Test register another user][HCTestRegisterRelecloud]
	
4. Um zu überprüfen, ob die Anmeldeinformationen des neuen Benutzers über die Hybridverbindung in der lokalen Datenbank gespeichert wurden, öffnen Sie SQL Management Studio auf Ihrem lokalen Computer. Erweitern Sie in Objekt-Explorer die Datenbank **MembershipDB**, und erweitern Sie anschließend **Tabellen**. Klicken Sie mit der rechten Maustaste auf die Mitgliedschaftstabelle **dbo.AspNetUsers**, und wählen Sie **Select Top 1000 Rows** (Die ersten 1.000 Zeilen auswählen), um die Ergebnisse anzuzeigen.
	
	![View the results][HCTestSSMSTree]
	
5. Die lokale Mitgliedschaftstabelle enthält nun beide Konten: das Konto, das Sie lokal erstellt haben, und das Konto, das Sie in der Azure-Cloud erstellt haben. Das Konto, das Sie in der Cloud erstellt haben, wurde über die Azure-Funktion "Hybridverbindung" in der lokalen Datenbank gespeichert.
	
	![Registered users in on-premises database][HCTestShowMemberDb]
	
Sie haben jetzt eine ASP.NET-Webanwendung erstellt und bereitgestellt, die eine Hybridverbindung zwischen einer Website in der Azure-Cloud und einer lokalen SQL Server-Datenbank verwendet. Glückwunsch!

##Weitere Informationen ##
[Übersicht über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Einführung von Josh Twist zu Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Website zu Hybridverbindungen](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Konfigurieren" und "Hybridverbindungen"](http://azure.microsoft.com/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Erstellen einer echten hybriden Cloud mit nahtloser Anwendungsportabilität (Channel 9-Video](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)

[Verbindung mit einem lokalen SQL Server von einem mobilen Azure-Dienst mithilfe von Hybridverbindungen (Channel 9-Video)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[bersicht über ASP.NET Identity](http://www.asp.net/identity)

<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png




<!--HONumber=42-->
