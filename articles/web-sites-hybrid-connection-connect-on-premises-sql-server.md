<properties linkid="web-sites-hybrid-connection-getting-started" title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# Eine Verbindung mit einem lokalen SQL Server von einer Azure-Website mit Hybridverbindungen herstellen

## Einführung

Mit Hybridverbindungen können Microsoft Azure-Websites mit lokalen Ressourcen, die einen statischen TCP-Port verwenden, verbunden werden. Zu den unterstützten Ressourcen gehören Microsoft SQL Server, MySQL, HTTP Web APIs, Mobile Services und die meisten benutzerdefinierten Webdienste.

In diesem Lernprogramm erfahren Sie, wie Sie eine Website im Azure-Vorschauportal erstellen, die Website mit der neuen Funktion "Hybridverbindung" mit Ihrer lokalen SQL Server-Datenbank verbinden, eine einfache ASP.NET-Webanwendung erstellen, welche die Hybridverbindung verwendet, und die Anwendung auf der Azure-Website bereitstellen. Die fertige Website in Azure speichert Anmeldeinformationen in einer lokalen Mitgliedschaftsdatenbank. Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure oder ASP.NET haben.

> [WACOM.NOTE] Der Websites-Teil der Funktion "Hybridverbindungen" ist nur im [Azure-Vorschauportal][Azure-Vorschauportal] verfügbar. Informationen zum Herstellen einer Verbindung in BizTalk Services finden Sie unter [Hybrid Connections][Hybrid Connections].

## Voraussetzungen

Zum Durchführen des Lernprogramms benötigen Sie folgende Produkte: Alle Produkte sind als kostenlose Versionen verfügbar, sodass Sie kostenlos mit der Entwicklung für Azure beginnen können.

-   **Azure-Abonnement** - Informationen zu einem kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

-   **Visual Studio 2013** - Zum Herunterladen einer kostenlosen Testversion von Visual Studio 2013 besuchen Sie [Visual Studio Downloads][Visual Studio Downloads]. Installieren Sie diese Version, bevor Sie fortfahren.

-   **Microsoft .NET Framework 3.5 Service Pack 1** - Wenn Sie das Betriebssystem Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 oder Windows Server 2008 R2 verwenden, können Sie diese Komponente in Systemsteuerung \> "Programme und Funktionen" bzw. "Programme und Features" \> "Windows-Funktionen ein- oder ausschalten" aktivieren. Andernfalls können Sie sie vom [Microsoft Download Center][Microsoft Download Center] herunterladen.

-   **SQL Server 2014 Express with Tools** - Laden Sie Microsoft SQL Server Express kostenlos von der [Microsoft Web Platform Datenbank-Seite][Microsoft Web Platform Datenbank-Seite] herunter. Wählen Sie die Version **Express** (nicht LocalDB). Die Version **Express with Tools** enthält das Produkt SQL Server Management Studio, das Sie in diesem Lernprogramm verwenden werden.

-   **SQL Server Management Studio Express** - Dieses Produkt ist im oben angegebenen Download "SQL Server 2014 Express with Tools" enthalten, Sie müssen es jedoch getrennt installieren. Sie können es von der [Download-Seite für SQL Server Express][Microsoft Web Platform Datenbank-Seite] herunterladen und installieren.

In diesem Lernprogramm wird davon ausgegangen, dass Sie ein Azure-Abonnement besitzen, Visual Studio 2013 installiert haben und .NET Framework 3.5 installiert oder aktiviert haben. In diesem Lernprogramm wird gezeigt, wie Sie SQL Server 2014 Express in einer Konfiguration installieren, die sich gut für die Azure-Funktion "Hybridverbindungen" eignet (eine Standardinstanz mit einem statischen TCP-Port). Bevor Sie mit dem Lernprogramm beginnen, laden Sie "SQL Server 2014 Express with Tools" vom oben angegebenen Speicherort herunter, sofern Sie SQL Server nicht installiert haben.

### Hinweise

Die Verwendung einer lokalen SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung setzt voraus, dass TCP/IP an einem statischen Port aktiviert ist. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht.

Der Computer, auf dem Sie den lokalen Hybridverbindungs-Manager-Agent installieren:

-   Muss über Port 5671 eine Verbindung mit Azure herstellen können
-   Muss *hostname*:*portnummer* der lokalen Ressource erreichen können.

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.

Wenn Sie SQL Server bereits in einer Konfiguration und Umgebung installiert haben, welche die oben beschriebenen Kriterien erfüllen, können Sie gleich mit [Erstellen einer lokalen SQL Server-Datenbank][Erstellen einer lokalen SQL Server-Datenbank] beginnen.

## Themen in diesem Artikel

[A. SQL Server Express installieren, TCP/IP aktivieren und eine lokale SQL Server-Datenbank erstellen][A. SQL Server Express installieren, TCP/IP aktivieren und eine lokale SQL Server-Datenbank erstellen]

[B. Eine Website im Azure-Vorschauportal erstellen][B. Eine Website im Azure-Vorschauportal erstellen]

[C. Eine Hybridverbindung und einen BizTalk-Dienst erstellen][C. Eine Hybridverbindung und einen BizTalk-Dienst erstellen]

[D. Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren][D. Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren]

[E. Ein grundlegendes ASP.NET-Webprojekt erstellen, Datenbank-Verbindungszeichenfolge bearbeiten und das Projekt lokal ausführen][E. Ein grundlegendes ASP.NET-Webprojekt erstellen, Datenbank-Verbindungszeichenfolge bearbeiten und das Projekt lokal ausführen]

[F. Webanwendung in Azure veröffentlichen und testen][F. Webanwendung in Azure veröffentlichen und testen]

<a name="InstallSQL"></a>

## A. SQL Server Express installieren, TCP/IP aktivieren und eine lokale SQL Server-Datenbank erstellen

In diesem Abschnitt wird erläutert, wie Sie SQL Server Express installieren, TCP/IP aktivieren und eine Datenbank erstellen, damit Ihre Webanwendung in der Azure-Vorschauumgebung funktioniert.

### SQL Server Express installieren

1.  Um SQL Server Express zu installieren, führen Sie die Datei **SQLEXPRWT\_x64\_ENU.exe** oder **SQLEXPR\_x86\_ENU.exe** aus, die Sie heruntergeladen haben. Der SQL Server-Installationscenter-Assistent wird geöffnet.

    ![SQL Server-Installation][SQL Server-Installation]

2.  Wählen Sie **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation**. Befolgen Sie die Anweisungen, und übernehmen Sie die Standardeinstellungen, bis Sie zur Seite **Instanzkonfiguration** gelangen.

3.  Wählen Sie auf der Seite **Instanzkonfiguration** die Option **Standardinstanz**.

    ![Standardinstanz wählen][Standardinstanz wählen]

    Standardmäßig überwacht die Standardinstanz von SQL Server den statischen Port 1433 auf Anforderungen von SQL Server-Clients, und das ist das Verhalten, das von der Funktion "Hybridverbindungen" vorausgesetzt wird. Benannte Instanzen verwenden dynamische Ports und UDP, die von Hybridverbindungen nicht unterstützt werden.

4.  Übernehmen Sie die Standardeinstellungen auf der Seite **Serverkonfiguration**.

5.  Wählen Sie auf der Seite **Datenbankmodulkonfiguration** unter **Authentifizierungsmodus** die Option **Gemischter Modus (SQL Server-Authentifizierung und Windows-Authentifizierung)**, und geben Sie ein Kennwort an.

    ![Wählen Sie "Gemischter Modus"][Wählen Sie "Gemischter Modus"]

    In diesem Lernprogramm verwenden Sie die SQLServer-Authentifizierung. Merken Sie sich unbedingt das Kennwort, das Sie angeben, weil Sie es später benötigen werden.

6.  Bearbeiten Sie die restlichen Schritte des Assistenten, um die Installation abzuschließen.

### TCP/IP aktivieren

Zum Aktivieren von TCP/IP verwenden Sie den SQL Server-Konfigurations-Manager, der zusammen mit SQL Server Express installiert wurde. Führen Sie zunächst die in [Aktivieren des TCP/IP-Netzwerkprotokolls für SQL Server][Aktivieren des TCP/IP-Netzwerkprotokolls für SQL Server] beschriebenen Schritte aus, bevor Sie fortfahren.

<a name="CreateSQLDB"></a>

### Eine lokale SQL Server-Datenbank erstellen

Für Ihre Visual Studio-Webanwendung benötigen Sie eine Mitgliedschaftsdatenbank, auf die Azure zugreifen kann. Hierzu ist eine SQL Server- oder SQL Server Express-Datenbank erforderlich (nicht die LocalDB-Datenbank, die von der MVC-Vorlage standardmäßig verwendet wird). Daher erstellen Sie als Nächstes die Mitgliedschaftsdatenbank.

1.  Stellen Sie in SQL Server Management Studio eine Verbindung mit der SQL Server-Instanz her, die Sie gerade installiert haben. (Wenn das Dialogfeld **Verbindung mit Server herstellen** nicht automatisch angezeigt wird, navigieren Sie im linken Bereich zu **Objekt-Explorer**, klicken Sie auf **Verbinden** und anschließend auf **Datenbankmodul**.)
    ![Verbindung mit Server herstellen][Verbindung mit Server herstellen]

    Wählen Sie unter **Servertyp** die Option **Datenbankmodul**. Als **Servername** können Sie **localhost** oder den Namen des Computers, den Sie benutzen, verwenden. Wählen Sie **SQL Server-Authentifizierung**, und melden Sie sich dann mit dem Benutzernamen und dem Kennwort des zuvor erstellten Benutzers an.

2.  Um eine neue Datenbank mit SQL Server Management Studio zu erstellen, klicken Sie in Objekt-Explorer mit der rechten Maustaste auf **Datenbanken** und anschließend auf **Neue Datenbank**.

    ![Neue Datenbank erstellen][Neue Datenbank erstellen]

3.  Geben Sie im Dialogfeld **Neue Datenbank** als Datenbankname "MembershipDB" ein, und klicken Sie dann auf **OK**.

    ![Datenbankname angeben][Datenbankname angeben]

    Beachten Sie, dass Sie im Moment keine Änderungen an der Datenbank vornehmen. Die Mitgliedschaftsinformationen werden automatisch später von Ihrer Webanwendung hinzugefügt, wenn Sie sie ausführen.

4.  Wenn Sie in Objekt-Explorer **Datenbanken** erweitern, sehen Sie, dass die Mitgliedschaftsdatenbank erstellt wurde.

    ![MembershipDB wurde erstellt][MembershipDB wurde erstellt]

<a name="CreateSite"></a>

## B. Eine Website im Azure-Vorschauportal erstellen

> [WACOM.NOTE] Wenn Sie im Azure-Vorschauportal bereits eine Website erstellt haben, die Sie für dieses Lernprogramm verwenden möchten, können Sie gleich mit [Eine Hybridverbindung und einen BizTalk-Dienst erstellen][C. Eine Hybridverbindung und einen BizTalk-Dienst erstellen] fortfahren.

1.  Klicken Sie in der unteren linken Ecke des [Azure-Vorschauportals][Azure-Vorschauportal] auf **Neu**, und wählen Sie dann **Website**.

    ![Neu (Schaltfläche)][Neu (Schaltfläche)]

    ![Neue Website][Neue Website]

2.  Geben Sie im Fenster **Website** einen Namen für die Website ein, und klicken Sie dann auf **Erstellen**.

    ![Name der Website][Name der Website]

3.  Nach einigen Sekunden wurde die Website erstellt, und das Website-Fenster wird angezeigt. Das Fenster ist ein vertikal bildlauffähiges Dashboard, mit dem Sie Ihre Website verwalten können.

    ![Website wird ausgeführt][Website wird ausgeführt]

4.  Um zu überprüfen, ob die Website aktiv ist, können Sie auf das Symbol **Durchsuchen** klicken, um die Standardseite anzuzeigen.

    ![Klicken Sie auf "Durchsuchen", um die Website anzuzeigen][Klicken Sie auf "Durchsuchen", um die Website anzuzeigen]

    ![Standardseite der Website][Standardseite der Website]

Als Nächstes erstellen Sie eine Hybridverbindung und einen BizTalk-Dienst für die Website.

<a name="CreateHC"></a>

## C. Eine Hybridverbindung und einen BizTalk-Dienst erstellen

1.  Blättern Sie im Vorschauportal nach unten zum Fenster für Ihre Website, und wählen Sie **Hybridverbindungen**.

    ![Hybridverbindungen][Hybridverbindungen]

2.  Klicken Sie im Fenster "Hybridverbindungen" auf **Hinzufügen**.

    ![Hybridverbindung hinzufügen][Hybridverbindung hinzufügen]

3.  Das Fenster **Hybridverbindung hinzufügen** wird geöffnet. Da dies die erste Hybridverbindung ist, ist die Option **Neue Hybridverbindung** bereits ausgewählt, und das Fenster **Hybridverbindung erstellen** wird geöffnet.
	
	![Hybridverbindung erstellen][Hybridverbindung erstellen]
	
	Im Fenster **Hybridverbindung erstellen** gehen Sie wie folgt vor:
	- Geben Sie im Feld **Name** einen Namen für die Verbindung an.
	- Geben Sie als **Hostname** den Computernamen Ihres SQL Server-Hostcomputers ein.
	- Geben Sie bei **Port** 1433 ein (Standardport für SQL Server).
	- Klicken Sie auf **BizTalk-Dienst**


4.  Das Fenster **BizTalk-Dienst erstellen** wird geöffnet. Geben Sie einen Namen für den BizTalk-Dienst ein, und klicken Sie dann auf **OK**.

    ![BizTalk-Dienst erstellen][BizTalk-Dienst erstellen]

    Das Fenster **BizTalk-Dienst erstellen** wird geschlossen, und Sie gelangen zurück zum Fenster **Hybridverbindung erstellen**.

5.  Klicken Sie im Fenster "Hybridverbindung erstellen" auf **OK**.

    ![Klicken Sie auf "OK"][Klicken Sie auf "OK"]

6.  Nach Abschluss des Prozesses wird im Benachrichtigungsbereich des Portals angezeigt, dass die Verbindung erfolgreich erstellt wurde.

    ![Erfolgsbenachrichtigung][Erfolgsbenachrichtigung]

7.  Im Website-Fenster zeigt das Symbol **Hybridverbindungen** jetzt an, dass 1 Hybridverbindung erstellt wurde.

    ![Es wurde eine Hybridverbindung erstellt][Es wurde eine Hybridverbindung erstellt]

Jetzt haben Sie einen wichtigen Teil der Cloud-Hybridverbindungsinfrastruktur fertig gestellt. Als Nächstes erstellen Sie ein entsprechendes lokales Gegenstück.

<a name="InstallHCM"></a>

## D. Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren

1.  Klicken Sie im Website-Fenster auf das Symbol "Hybridverbindungen".

    ![Hybridverbindungssymbol][Hybridverbindungssymbol]

2.  Im Fenster **Hybridverbindungen** enthält die Spalte **Status** für den kürzlich hinzugefügten Endpunkt den Eintrag **Nicht verbunden**. Klicken Sie auf die Verbindung, um sie zu konfigurieren.

    ![Nicht verbunden][Nicht verbunden]

    Das Fenster "Hybridverbindung" wird geöffnet.

    ![NotConnectedBlade][NotConnectedBlade]

3.  Klicken Sie im Fenster auf **Listener einrichten**.

    ![Klicken Sie auf "Listener einrichten"][Klicken Sie auf "Listener einrichten"]

4.  Das Fenster **Eigenschaften von Hybridverbindung** wird geöffnet. Wählen Sie unter **Lokaler Hybridverbindungs-Manager** die Option **Klicken Sie hier, um die Installation zu starten**.

    ![Klicken Sie hier, um die Installation zu starten][Klicken Sie hier, um die Installation zu starten]

5.  Wählen Sie im Sicherheitswarnungsdialogfeld "Anwendungsausführung" die Option **Ausführen**, um fortzufahren.

    ![Wählen Sie "Ausführen", um fortzufahren][Wählen Sie "Ausführen", um fortzufahren]

6.  Wählen Sie im Dialogfeld **Benutzerkontensteuerung** die Option **Ja**.

    ![Wählen Sie "Ja"][Wählen Sie "Ja"]

7.  Der Hybridverbindungs-Manager wird heruntergeladen und für Sie installiert.

    ![Installation][Installation]

8.  Nach Abschluss der Installation klicken Sie auf **Schließen**.

    ![Klicken Sie auf "Schließen"][Klicken Sie auf "Schließen"]

    Im Fenster **Hybridverbindungen** wird in der Spalte **Status** jetzt der Eintrag **Verbunden** angezeigt.

    ![Status "Verbunden"][Status "Verbunden"]

Nachdem die Hybridverbindungsinfrastruktur jetzt vollständig ist, werden Sie eine Webanwendung erstellen, welche diese verwendet.

<a name="CreateASPNET"></a>

## E. Ein grundlegendes ASP.NET-Webprojekt erstellen, Datenbank-Verbindungszeichenfolge bearbeiten und das Projekt lokal ausführen

### Ein grundlegendes ASP.NET-Projekt erstellen

1.  Erstellen Sie in Visual Studio über das Menü **Datei** ein neues Projekt:

    ![Neues Visual Studio-Projekt][Neues Visual Studio-Projekt]

2.  Wählen Sie im Abschnitt **Vorlagen** des Dialogfelds **Neues Projekt** die Option **Web** und anschließend **ASP.NET-Webanwendung**, und klicken Sie dann auf **OK**.

    ![Wählen Sie "ASP.NET-Webanwendung"][Wählen Sie "ASP.NET-Webanwendung"]

3.  Wählen Sie im Dialogfeld **New ASP.NET-Projekt** die Option **MVC**, und klicken Sie dann auf **OK**.

    ![Wählen Sie "MVC"][Wählen Sie "MVC"]

4.  Sobald das Projekt erstellt wurde, wird die Seite mit der Infodatei der Anwendung geöffnet. Führen Sie das Webprojekt noch nicht aus.

    ![Seite mit der Infodatei][Seite mit der Infodatei]

### Bearbeiten Sie die Datenbank-Verbindungszeichenfolge für die Anwendung

In diesem Schritt bearbeiten Sie die Verbindungszeichenfolge, der Ihre Anwendung entnimmt, wo sich die lokale SQL Server Express-Datenbank befindet. Die Verbindungszeichenfolge befindet sich in der Web.config-Datei der Anwendung, welche die Konfigurationsdaten der Anwendung enthält.

> [WACOM.NOTE] Um sicherzustellen, dass Ihre Anwendung die Datenbank, die Sie in SQL Server Express erstellt haben, und nicht die Datenbank aus der LocalDB-Standardinstanz von Visual Studio verwendet, müssen Sie diesen Schritt unbedingt fertigstellen, bevor Sie das Projekt ausführen.

1.  Doppelklicken Sie im Projektmappen-Explorer auf die Datei "Web.config".

    ![Web.config][Web.config]

2. Bearbeiten Sie den Abschnitt **connectionStrings**, sodass er auf die SQL Server-Datenbank auf Ihrem lokalen Computer verweist, und halten Sie sich dabei an die Syntax im folgenden Beispiel:

	![Verbindungszeichenfolge][Verbindungszeichenfolge]

	Beachten Sie beim Erstellen der Verbindungszeichenfolge Folgendes:
	- Wenn Sie eine Verbindung mit einer benannten Instanz statt mit der Standardinstanz herstellen (beispielsweise IhrServer\\SQLEXPRESS), müssen Sie den SQL Server für die Verwendung statischer Ports konfigurieren. Informationen zum Konfigurieren statischer Ports finden Sie unter [Konfigurieren von SQL Server zum Abhören eines bestimmten Ports][Konfigurieren von SQL Server zum Abhören eines bestimmten Ports]. Standardmäßig verwenden benannte Instanzen UDP und dynamische Ports, was von Hybridverbindungen nicht unterstützt wird.
	- Es wird empfohlen, dass Sie den Port (standardmäßig 1433 wie im Beispiel) in der Verbindungszeichenfolge angeben, damit Sie sicher sein können, dass auf ihrem lokalen SQL Server TCP aktiviert ist und der richtige Port verwendet wird.
	- Denken Sie daran, die Verbindung unter Verwendung der SQL Server-Authentifizierung herzustellen und die Benutzer-ID und das Kennwort in der Verbindungszeichenfolge anzugeben.

3. Klicken Sie in Visual Studio auf **Speichern**, um die Datei "Web.config" zu speichern.

### Projekt lokal ausführen und einen neuen Benutzer registrieren

1.  Führen Sie Ihr Webprojekt jetzt lokal aus, indem Sie auf die Schaltfläche "Durchsuchen" unter "Debug" klicken. In diesem Beispiel wird Internet Explorer verwendet.

    ![Projekt ausführen][Projekt ausführen]

2.  Wählen Sie in der oberen rechten Ecke der Standardwebseite **Registrieren**, um ein neues Konto zu registrieren:

    ![Registrieren Sie ein neues Konto][Registrieren Sie ein neues Konto]

3.  Geben Sie einen Benutzernamen und ein Kennwort ein:

    ![Geben Sie Benutzernamen und Kennwort ein][Geben Sie Benutzernamen und Kennwort ein]

    Daraufhin wird automatisch auf dem lokalen SQL Server eine Datenbank erstellt, in der die Mitgliedschaftsinformationen für Ihre Anwendung gespeichert werden. Eine der Tabellen (**dbo.AspNetUsers**) enthält die Anmeldeinformationen der Websitebenutzer, wie die Anmeldeinformationen, die Sie gerade eingegeben haben. Sie werden diese Tabelle weiter hinten im Lernprogramm sehen.

4.  Schließen Sie das Browserfenster der Standardwebseite. Damit wird die Ausführung der Anwendung in Visual Studio beendet.

Sie sind jetzt bereit für den nächsten Schritt, in dem Sie die Anwendung für Azure veröffentlichen und testen.

<a name="PubNTest"></a>

## F. Webanwendung in Azure veröffentlichen und testen

Nun veröffentlichen Sie Ihre Anwendung auf Ihrer Website in Azure, und dann testen Sie sie, um zu sehen, wie die Hybridverbindung, die Sie zuvor konfiguriert haben, verwendet wird, um Ihre Website-Anwendung mit der Datenbank auf Ihrem lokalen Computer zu verbinden.

### Die Webanwendung veröffentlichen

1.  Sie können Ihr Veröffentlichungsprofil für die Website im Azure-Portal herunterladen. Wählen Sie im Fenster für Ihre Website **Veröffentlichungsprofil herunterladen**, und speichern Sie die Datei dann auf Ihrem Computer.

    ![Veröffentlichungsprofil herunterladen][Veröffentlichungsprofil herunterladen]

    ![Veröffentlichungsprofil im Downloads-Ordner][Veröffentlichungsprofil im Downloads-Ordner]

    Als Nächstes importieren Sie diese Datei in Ihre Visual Studio-Webanwendung.

2.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Veröffentlichen**.

    ![Wählen Sie "Veröffentlichen"][Wählen Sie "Veröffentlichen"]

3.  Wählen Sie im Dialogfeld **Web veröffentlichen** auf der Registerkarte **Profil** die Option **Importieren**.

    ![Importieren][Importieren]

4.  Navigieren Sie zu dem heruntergeladenen Veröffentlichungsprofil, markieren Sie es, und klicken Sie dann auf **OK**.

    ![Navigieren Sie zum Profil][Navigieren Sie zum Profil]

5.  Ihre Veröffentlichungsinformationen werden importiert und auf der Registerkarte **Verbindung** im Dialog angezeigt.

    ![Auf "Veröffentlichen" klicken][Auf "Veröffentlichen" klicken]

    Klicken Sie auf **Veröffentlichen**.

    Sobald die Veröffentlichung abgeschlossen ist, wird der Browser gestartet, und darin wird Ihre nunmehr bekannte Website angezeigt. Die Website befindet sich jetzt allerdings in der Azure-Cloud!

Als Nächstes verwenden Sie die Live-Webanwendung, um die Hybridverbindung zu überprüfen.

### Die fertiggestellte Webanwendung in Azure testen

1.  Wählen Sie oben rechts auf der Webseite in Azure **Anmelden** aus.

    ![Anmeldung testen][Anmeldung testen]

2.  Ihre Azure-Website ist jetzt mit der Mitgliedschaftsdatenbank der Webanwendung auf dem lokalen Computer verbunden. Um dies zu überprüfen, melden Sie sich mit den gleichen Anmeldeinformationen ein, die Sie zuvor in die lokale Datenbank eingegeben haben.

    ![Begrüßung][Begrüßung]

3.  Um die neue Hybridverbindung weiter zu testen, melden Sie sich von Ihrer Azure-Webanwendung ab, und registrieren Sie sich als ein anderer Benutzer. Geben Sie einen anderen Benutzernamen und ein Kennwort ein, und klicken Sie auf **Registrieren**.

    ![Testregistrierung eines weiteren Benutzers][Testregistrierung eines weiteren Benutzers]

4.  Um zu überprüfen, ob die Anmeldeinformationen des neuen Benutzers über die Hybridverbindung in der lokalen Datenbank gespeichert wurden, öffnen Sie SQL Management Studio auf Ihrem lokalen Computer. Erweitern Sie in Objekt-Explorer die Datenbank **MembershipDB**, und erweitern Sie anschließend **Tabellen**. Klicken Sie mit der rechten Maustaste auf die Mitgliedschaftstabelle **dbo.AspNetUsers**, und wählen Sie **Select Top 1000 Rows** (Die ersten 1.000 Zeilen auswählen), um die Ergebnisse anzuzeigen.

    ![Zeigen Sie die Ergebnisse an][Zeigen Sie die Ergebnisse an]

5.  Die lokale Mitgliedschaftstabelle enthält nun beide Konten: das Konto, das Sie lokal erstellt haben, und das Konto, das Sie in der Azure-Cloud erstellt haben. Das Konto, das Sie in der Cloud erstellt haben, wurde über die Azure-Funktion "Hybridverbindung" in der lokalen Datenbank gespeichert.

    ![Registrierte Benutzer in der lokalen Datenbank][Registrierte Benutzer in der lokalen Datenbank]

Sie haben jetzt eine ASP.NET-Webanwendung erstellt und bereitgestellt, die eine Hybridverbindung zwischen einer Website in der Azure-Cloud und einer lokalen SQL Server-Datenbank verwendet. Glückwunsch!

## Weitere Informationen

[Übersicht über Hybridverbindungen][Hybrid Connections]

[Josh Twist introduces hybrid connections (Channel 9 video)][Josh Twist introduces hybrid connections (Channel 9 video)]

[Hybridverbindungen-Website][Hybridverbindungen-Website]

[BizTalk Services: Dashboard, Monitor, Scale, Configure, and Hybrid Connection tabs][BizTalk Services: Dashboard, Monitor, Scale, Configure, and Hybrid Connection tabs]

[Building a Real-World Hybrid Cloud with Seamless Application Portability (Channel 9 video)][Building a Real-World Hybrid Cloud with Seamless Application Portability (Channel 9 video)]

[Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen][Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen]

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (Channel 9 video)][Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (Channel 9 video)]

[ASP.NET Identity - Übersicht][ASP.NET Identity - Übersicht]

<!-- IMAGES -->

  [Azure-Vorschauportal]: https://portal.azure.com
  [Hybrid Connections]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Visual Studio Downloads]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Microsoft Download Center]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22
  [Microsoft Web Platform Datenbank-Seite]: http://www.microsoft.com/web/platform/database.aspx
  [Erstellen einer lokalen SQL Server-Datenbank]: #CreateSQLDB
  [A. SQL Server Express installieren, TCP/IP aktivieren und eine lokale SQL Server-Datenbank erstellen]: #InstallSQL
  [B. Eine Website im Azure-Vorschauportal erstellen]: #CreateSite
  [C. Eine Hybridverbindung und einen BizTalk-Dienst erstellen]: #CreateHC
  [D. Lokalen Hybridverbindungs-Manager zum Herstellen der Verbindung installieren]: #InstallHCM
  [E. Ein grundlegendes ASP.NET-Webprojekt erstellen, Datenbank-Verbindungszeichenfolge bearbeiten und das Projekt lokal ausführen]: #CreateASPNET
  [F. Webanwendung in Azure veröffentlichen und testen]: #PubNTest
  [SQL Server-Installation]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
  [Standardinstanz wählen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
  [Wählen Sie "Gemischter Modus"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
  [Aktivieren des TCP/IP-Netzwerkprotokolls für SQL Server]: http://technet.microsoft.com/de-de/library/hh231672%28v=sql.110%29.aspx
  [Verbindung mit Server herstellen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
  [Neue Datenbank erstellen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
  [Datenbankname angeben]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
  [MembershipDB wurde erstellt]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
  [Neu (Schaltfläche)]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
  [Neue Website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
  [Name der Website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
  [Website wird ausgeführt]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
  [Klicken Sie auf "Durchsuchen", um die Website anzuzeigen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
  [Standardseite der Website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
  [Hybridverbindungen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
  [Hybridverbindung hinzufügen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
  [Hybridverbindung erstellen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
  [BizTalk-Dienst erstellen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
  [Klicken Sie auf "OK"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
  [Erfolgsbenachrichtigung]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
  [Es wurde eine Hybridverbindung erstellt]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
  [Hybridverbindungssymbol]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
  [Nicht verbunden]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
  [Klicken Sie auf "Listener einrichten"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
  [Klicken Sie hier, um die Installation zu starten]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
  [Wählen Sie "Ausführen", um fortzufahren]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
  [Wählen Sie "Ja"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
  [Installation]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
  [Klicken Sie auf "Schließen"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
  [Status "Verbunden"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
  [Neues Visual Studio-Projekt]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
  [Wählen Sie "ASP.NET-Webanwendung"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
  [Wählen Sie "MVC"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
  [Seite mit der Infodatei]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
  [Web.config]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
  [Verbindungszeichenfolge]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
  [Konfigurieren von SQL Server zum Abhören eines bestimmten Ports]: http://support.microsoft.com/kb/823938
  [Projekt ausführen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
  [Registrieren Sie ein neues Konto]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
  [Geben Sie Benutzernamen und Kennwort ein]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
  [Veröffentlichungsprofil herunterladen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
  [Veröffentlichungsprofil im Downloads-Ordner]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
  [Wählen Sie "Veröffentlichen"]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
  [Importieren]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
  [Navigieren Sie zum Profil]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
  [Auf "Veröffentlichen" klicken]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
  [Anmeldung testen]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
  [Begrüßung]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
  [Testregistrierung eines weiteren Benutzers]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
  [Zeigen Sie die Ergebnisse an]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
  [Registrierte Benutzer in der lokalen Datenbank]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
  [Josh Twist introduces hybrid connections (Channel 9 video)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Hybridverbindungen-Website]: http://azure.microsoft.com/de-de/services/biztalk-services/
  [BizTalk Services: Dashboard, Monitor, Scale, Configure, and Hybrid Connection tabs]: http://azure.microsoft.com/de-de/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Building a Real-World Hybrid Cloud with Seamless Application Portability (Channel 9 video)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Verbinden eines mobilen Azure-Diensts mit einem lokalen SQL Server über Hybridverbindungen]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (Channel 9 video)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
  [ASP.NET Identity - Übersicht]: http://www.asp.net/identity
