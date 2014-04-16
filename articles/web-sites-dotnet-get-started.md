<properties linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Windows Azure" pageTitle="Get started with Windows Azure for .NET" metaKeywords="" description="This tutorial shows you how to deploy an ASP.NET web site to Windows Azure using Visual Studio 2013. In less than 15 minutes you'll have an app up-and-running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Windows Azure and ASP.NET" authors=""  solutions="" writer="tdykstra" manager="wpickett" editor="mollybos"  />

# Erste Schritte mit Windows Azure und ASP.NET

<div class="dev-center-tutorial-selector sublanding">
[Visual Studio 2013](/en-us/develop/net/tutorials/get-started/ "Visual
Studio 2013"){: .current}[Visual Studio
2012](/en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio
2012")
</div>

In diesem Lernprogramm wird die Bereitstellung einer
ASP.NET-Webanwendung für eine Windows Azure-Website mit dem Assistenten
**Web veröffentlichen** in Visual Studio 2013
oder Visual Studio 2013 for Web Express gezeigt. (Wenn Sie Visual
Studio 2012 verwenden möchten, finden Sie weitere Informationen in der
[vorherigen Version dieses
Lernprogramms](/en-us/develop/net/tutorials/get-started-vs2012/).)

Sie können ein kostenloses Windows Azure-Konto erstellen. Wenn Sie noch
nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 for Web
Express automatisch durch das SDK installiert. Auf diese Weise können
Sie völlig kostenlos mit der Entwicklung für Windows Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine
Erfahrung mit der Verwendung von Windows Azure haben. Nach Abschluss
dieses Lernprogramms verfügen Sie über eine einfache Webanwendung, die
in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Ermöglichen der Windows Azure-Entwicklung auf Ihrem Computer durch
  Installieren des Windows Azure SDK
* Erstellen eines Visual Studio ASP.NET MVC 5-Projekts und
  Veröffentlichen dieses Projekts auf einer Windows Azure-Website

In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Website-Startseite](./media/web-sites-dotnet-get-started-vs2013/GS13homepage.png)

<div class="dev-callout" markdown="1">
**Hinweis**
Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm
auszuführen. Wenn Sie kein Konto haben, können Sie [Ihre
MSDN-Abonnementenvorteile
aktivieren](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F){:
data-morhtml="true" target="_blank"} oder [sich für eine kostenlose
Testversion
registrieren](/en-us/pricing/free-trial/?WT.mc_id=A261C142F){:
data-morhtml="true" target="_blank"}.


</div>

### Teile des Lernprogramms

1.  [Einrichten der Entwicklungsumgebung](#setupdevenv)
2.  [Erstellen einer ASP.NET MVC 5-Anwendung](#createapp)
3.  [Bereitstellen der Anwendung für Windows
    Azure](#deploytowindowsazure)
4.  [Nächste Schritte](#nextsteps)

\[WACOM.INCLUDE
[install-sdk-2013-only](../includes/install-sdk-2013-only.md)\]

<h2><a name="createapp" /><span class="short-header">Erstellen der Anwendung</span>Erstellen einer ASP.NET MVC 5-Anwendung</h2>


Zunächst erstellen Sie ein Visual Studio-Webanwendungsprojekt, das Sie
auf Windows Azure veröffentlichen.

### Erstellen des Projekts

1.  Öffnen Sie Visual Studio 2013 oder Visual Studio 2013 Express for
    Web.

2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.
    
    ![&quot;Neues Projekt&quot; im Men&uuml;
    &quot;Datei&quot;](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **C#**,
    und wählen Sie unter **Installierte Vorlagen** die Option **Web**
    aus. Wählen Sie dann **ASP.NET-Webanwendung** aus.

4.  Stellen Sie sicher, dass als Zielframework **.NET Framework 4.5**
    ausgewählt ist.

5.  Nennen Sie die Anwendung **MyExample**, und klicken Sie auf **OK**.
    
    ![Dialogfeld &quot;Neues
    Projekt&quot;](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage
    **MVC** aus, und klicken Sie dann auf **Authentifizierung ändern**.
    
    ![Dialogfeld &quot;Neues
    ASP.NET-Projekt&quot;](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine
    Authentifizierung** und dann auf **OK**.
    
    ![Keine
    Authentifizierung](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    Die erstellte Beispielanwendung enthält keine Features, die eine
    Benutzeranmeldung erfordern. Informationen zum Implementieren von
    Authentifizierungs- und Autorisierungsfeatures finden Sie im
    Abschnitt [Nächste Schritte](#nextsteps) am Ende dieses
    Lernprogramms.

8.  Klicken Sie im Dialogfeld **Neues ASP.Net-Projekt** auf **OK**.
    
    ![Dialogfeld &quot;Neues
    ASP.NET-Projekt&quot;](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### Lokales Ausführen der Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen.
    
    Die Startseite der Anwendung wird im Standardbrowser angezeigt.
    
    ![Lokal ausgef&uuml;hrte
    Website](./media/web-sites-dotnet-get-started-vs2013/GS13homepage.png)
    
    Anhand der URL `http://localhost` können Sie erkennen, dass die
    Website auf dem lokalen Computer ausgeführt wird. Standardmäßig
    erfolgt die Ausführung in IIS Express, einer im Umfang
    eingeschränkten Version von IIS, die für die Entwicklung von
    Webanwendungen konzipiert wurde.
    
    Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um eine
    einfache Anwendung zu erstellen, die Sie für Windows Azure
    bereitstellen können.

<h2><a name="deploytowindowsazure" /><span class="short-header">Bereitstellen der Anwendung</span>Bereitstellen der Anwendung für Windows Azure</h2>


1.  Schließen Sie den Browser.

2.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der
    rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü
    **Veröffentlichen** aus.
    
    ![&quot;Ver&ouml;ffentlichen&quot; im Kontextmen&uuml; des
    Projekts](./media/web-sites-dotnet-get-started-vs2013/GS13publish.png)
    
    Der Assistent **Web veröffentlichen** wird geöffnet.

3.  Klicken Sie im Assistenten **Web veröffentlichen** auf der
    Registerkarte **Profil** auf **Importieren**.
    
    ![Ver&ouml;ffentlichungseinstellungen
    importieren](./media/web-sites-dotnet-get-started-vs2013/ImportPublishSettings.png)
    
    Das Dialogfeld **Veröffentlichungsprofil importieren** wird
    geöffnet.

4.  Verwenden Sie eine der folgenden Methoden, damit von Visual Studio
    eine Verbindung mit Ihrem Windows Azure-Konto hergestellt werden
    kann.
    
    * Klicken Sie auf **Anmelden**, und geben Sie die
      Anmeldeinformationen für Ihr Windows Azure-Konto ein.
      
      Diese Methode ist schneller und einfacher, dabei werden die
      Windows Azure SQL-Datenbank oder Windows Azure Mobile Services
      jedoch nicht im Fenster **Server-Explorer** angezeigt.
    
    * Klicken Sie auf **Abonnements verwalten**, um ein
      Verwaltungszertifikat zu installieren, das den Zugriff auf Ihr
      Konto ermöglicht.
      
      Klicken Sie im Dialogfeld **Windows Azure-Abonnements verwalten**
      auf die Registerkarte **Zertifikate** und dann auf
      **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen
      und Importieren einer Abonnementdatei (die Datei trägt auch den
      Namen *.publishsettings*) für Ihr Windows Azure-Konto.
      
      > \[WACOM.NOTE\] Laden Sie die Abonnementdatei in einen Ordner
      > außerhalb Ihrer Quellcodeverzeichnisse herunter (beispielsweise
      > in den Ordner **Downloads**), und löschen Sie sie nach Abschluss
      > des Importvorgangs. Böswillige Benutzer, die Zugriff auf die
      > Abonnementdatei erlangen, können Windows Azure-Services für Sie
      > bearbeiten, erstellen und löschen.
      
      Weitere Informationen finden Sie unter [Verwalten von Konten,
      Abonnements und Administratorrollen][1].

5.  Aktivieren Sie im Dialogfeld **Veröffentlichungseinstellungen
    importieren** das Optionsfeld **Aus einer Windows Azure-Website
    importieren**, und klicken Sie dann auf **Neu**.
    
    ![Neue Site
    hinzuf&uuml;gen](./media/web-sites-dotnet-get-started-vs2013/GS13NewSite.png)

6.  Geben Sie im Dialogfeld **Site auf Windows Azure erstellen** eine
    Zeichenfolge in das Feld **Sitename** ein, die als eindeutige URL
    für die Anwendung dient.
    
    Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge
    und dem Suffix, das neben dem Textfeld aufgeführt wird. Wenn eine
    andere Person die eingegebene URL bereits verwendet hat, wird rechts
    ein rotes Ausrufungszeichen anstelle eines grünen Häkchens
    angezeigt, und Sie müssen einen anderen Wert eingeben.

7.  Wählen Sie in der Dropdownliste **Ort** den am nächsten befindlichen
    Ort aus.
    
    Mit dieser Einstellung wird das Datencenter angegeben, in dem Ihre
    Website ausgeführt wird.

8.  Verändern Sie die Datenbankfelder nicht.
    
    Für dieses Lernprogramm verwenden Sie keine Datenbank. Der Abschnitt
    [Nächste Schritte](#nextsteps) am Ende des Lernprogramms ist mit
    einem Lernprogramm verknüpft, in dem die Verwendung einer Datenbank
    erläutert wird.

9.  Klicken Sie auf **Erstellen**.  
    
    
    ![Neue Website
    erstellen](./media/web-sites-dotnet-get-started-vs2013/GS13createsite.png)
    
    Die Website ist in wenigen Sekunden erstellt. Wenn Sie zum
    Dialogfeld **Veröffentlichungseinstellungen importieren**
    zurückkehren, ist die neue Website in der Dropdownliste ausgewählt.

10. Klicken Sie auf **OK**.
    
    ![Erstellte
    Website](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

11. Klicken Sie im Assistenten **Web veröffentlichen** auf der
    Registerkarte **Verbindung** auf **Verbindung prüfen**, um
    sicherzustellen, dass die Einstellungen richtig sind.
    
    ![Verbindung
    pr&uuml;fen](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche
    **Verbindung prüfen** ein grünes Häkchen angezeigt.

12. Klicken Sie auf **Weiter**.
    
    ![Erfolgreich gepr&uuml;fte
    Verbindung](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

13. Klicken Sie auf der Registerkarte **Einstellungen** auf **Weiter**.
    
    ![Registerkarte
    &quot;Einstellungen&quot;](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    Sie können die Standardeinstellungen auf dieser Registerkarte
    annehmen. Sie stellen eine Buildkonfiguration für die
    Veröffentlichung bereit und müssen keine Dateien auf dem Zielserver
    löschen, die Anwendung nicht vorkompilieren oder Dateien im Ordner
    **App\_Data** ausschließen.

14. Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau
    starten**.
    
    ![Schaltfl&auml;che &quot;Vorschau starten&quot; auf der
    Registerkarte
    &quot;Vorschau&quot;](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf
    den Server kopiert werden. Die Anzeige der Vorschau ist nicht
    erforderlich, um die Anwendung zu veröffentlichen, es handelt sich
    dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt.

15. Klicken Sie auf **Veröffentlichen**.
    
    ![Dateiausgabe nach &quot;Vorschau
    starten&quot;](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    Die Dateien werden von Visual Studio auf den Windows Azure-Server
    kopiert.

16. Im Fenster **Ausgabe** wird angezeigt, welche
    Bereitstellungsaktionen ausgeführt wurden, und es wird die
    erfolgreiche Durchführung der Bereitstellung gemeldet.
    
    ![Fenster &quot;Ausgabe&quot; mit Meldung der erfolgreichen
    Bereitstellung](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

17. Nach der erfolgreichen Bereitstellung wird die URL der
    bereitgestellten Website automatisch im Standardbrowser geöffnet.
    
    Die erstellte Anwendung wird nun in der Cloud ausgeführt.
    
    ![In Windows Azure ausgef&uuml;hrte
    Website](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

<h2><a name="nextsteps" /><span class="short-header">Nächste Schritte</span>Nächste Schritte</h2>


In diesem Lernprogramm haben Sie erfahren, wie eine einfache
Webanwendung auf einer Windows Azure-Website bereitgestellt wird. Es
sind weitere Ressourcen verfügbar, mit denen Sie lernen können, die Site
zu verwalten und zu skalieren sowie Siteprobleme zu behandeln,
Datenbank-, Authentifizierungs- und Autorisierungsfunktionalität
hinzuzufügen und zu entscheiden, ob die Anwendung in einem Windows
Azure-Clouddienst statt auf einer Windows Azure-Website ausgeführt
werden soll.

<h3>Verwalten einer Website</h3>


Das [Windows Azure-Verwaltungsportal][2] ist eine Weboberfläche, über
die Sie alle Ihre Windows Azure-Dienste verwalten und überwachen können.

Sie können neue Websites, Clouddienste, virtuelle Computer, Datenbanken
usw. erstellen. Sie können Open Source-Anwendungen über den Katalog
erstellen. Und Sie können von Ihnen erstellte Dienste verwalten.
Beispielsweise werden auf dem folgenden Bildschirmfoto die Schaltflächen
**Beenden**, **Neu starten** und **Löschen** für eine Windows
Azure-Website auf der Registerkarte **Dashboard** des Verwaltungsportals
angezeigt. Im Dashboard werden außerdem Leistungsstatistiken wie die
benötigte CPU-Zeit, Anzahl der Anforderungen, Datenein- und -ausgabe und
ggf. aufgetretene Fehler angezeigt.

![Registerkarte &quot;Dashboard&quot; im
Verwaltungsportal](./media/web-sites-dotnet-get-started-vs2013/MPStopStartDelete.png)

Sie können viele andere Websiteeinstellungen auf der Registerkarte
**Konfigurieren** ändern. Weitere Informationen finden Sie unter
[Verwalten von
Websites](/en-us/manage/services/web-sites/how-to-manage-websites/).

Außerdem können Sie einige Website-Verwaltungsfunktionen direkt über den
Server-Explorer in Visual Studio ausführen. Informationen zu den
möglichen Aktionen im Server-Explorer finden Sie unter
[Problembehandlung von Windows Azure-Websites in Visual
Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

<h3>Skalieren einer Website</h3>


Wenn es sich um eine öffentliche Website handelt und nach und nach mehr
Datenverkehr auftritt, können die Reaktionszeiten nachlassen. Um dies zu
verhindern, können Sie auf der Registerkarte **Skalierung** im
Verwaltungsportal einfach Serverressourcen hinzufügen. Weitere
Informationen finden Sie unter [Skalieren von
Websites](/en-us/manage/services/web-sites/how-to-scale-websites/). (Das
Hinzufügen von Serverressourcen zum Skalieren einer Website ist nicht
kostenfrei.)

<h3>Problembehandlung für eine Website</h3>


Bei der Problembehandlung kann eine Einbeziehung der Ausgabe der
Ablaufverfolgung oder Protokolle hilfreich sein. Visual Studio enthält
integrierte Tools, die die Anzeige von Windows Azure-Protokollen
vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die
Ausführung in Windows Azure remote im Debugmodus möglich. Weitere
Informationen finden Sie unter [Problembehandlung von Windows
Azure-Websites in Visual
Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

<h3>Hinzufügen von Datenbank- und Autorisierungsfunktionalität</h3>


Die meisten Produktionswebsites verwenden eine Datenbank und schränken
einige Websitefunktionen auf bestimmte autorisierte Benutzer ein. Ein
Lernprogramm mit den ersten Schritten bezüglich Datenbankzugriff,
Authentifizierung und Autorisierung finden Sie unter [Bereitstellen
einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschaft, OAuth und
einer SQL-Datenbank für eine Windows
Azure-Website](/en-us/develop/net/tutorials/web-site-with-sql-database/).

<h3>Entscheiden, ob die Anwendung in einem Clouddienst ausgeführt werden soll</h3>


In einigen Szenarien empfiehlt es sich, die Anwendung in einem Windows
Azure-Clouddienst auszuführen statt auf einer Windows Azure-Website.
Weitere Informationen finden Sie unter [Windows
Azure-Ausführungsmodelle](/en-us/develop/net/fundamentals/compute/) und
[Windows Azure-Websites, Cloud Services und VMs: Wann eignet sich welche
Komponente?][3] Unter [.NET-Anwendung mit mehreren Ebenen mithilfe von
Speichertabellen, Warteschlangen und
Blobs](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)
finden Sie eine Lernprogrammreihe, in der die Erstellung einer
ASP.NET-Webanwendung mit mehreren Ebenen und die Bereitstellung dieser
Anwendung für einen Clouddienst gezeigt wird.



[1]: http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert
[2]: http://manage.windowsazure.com
[3]: http://www.windowsazure.com/en-us/manage/services/web-sites/choose-web-app-service/
