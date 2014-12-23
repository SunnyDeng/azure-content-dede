<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Problembehandlung bei Azure-Websites in Visual Studio" metaKeywords="Problembehandlung Debuggen bei Azure-Website-Nachverfolgungsprotokollierung" description="Learn how to troubleshoot an Azure Website by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/13/2014" ms.author="tdykstra" />

# Problembehandlung von Azure-Websites in Visual Studio

In diesem Lernprogramm lernen Sie die Visual Studio-Tools zum Debuggen von Anwendungen kennen, während diese in einer Azure-Website ausgeführt werden. Sie führen die Anwendung entweder remote im [Debugmodus](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx)aus oder arbeiten mit Anwendungs- und Webserverprotokollen.

Sie lernen Folgendes:

* Welche Azure-Verwaltungsfunktionen sind in Visual Studio verfügbar?
* Verwenden der Remoteansicht in Visual Studio für schnelle Änderungen an Remotewebsites
* Ausführen des Debugmodus im Remotebetrieb, während ein Projekt in Azure ausgeführt wird.
* Erstellen von Anwendungs-Ablaufprotokollen und Live-Anzeige dieser Protokolle.
* Anzeige von Webserverprotokollen inklusive detaillierter Fehlermeldungen und Verfolgung fehlgeschlagener Anforderungen.
* Senden von Diagnoseprotokollen an ein Azure-Speicherkonto und Anzeige der Protokolle.

Wenn Sie über Visual Studio Ultimate verfügen, können Sie auch [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) zum Degbuggen verwenden. IntelliTrace wird in diesem Lernprogramm nicht behandelt.

### Teile des Lernprogramms

- [Voraussetzungen](#prerequisites)
- [Konfigurieren und Verwalten von Websites](#sitemanagement)
- [Access-Websitedateien im Server-Explorer](#remoteview)
- [Remotedebuggen](#remotedebug)
	- Remotedebuggen von Websites
	- Remotedebuggen von Webaufträgen
	- Hinweise zum Remotedebuggen 
- [Übersicht über Diagnoseprotokolle](#logsoverview)
- [Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen ](#apptracelogs)
- [Erstellen von Webserverprotokollen](#webserverlogs)
- [Anzeigen detaillierter Fehlermeldungsprotokolle](#detailederrorlogs)
- [Herunterladen von Dateisystemprotokollen](#downloadlogs)
- [Anzeigen von Speicherprotokollen](#storagelogs)
- [Anzeigen von Protokollen für fehlgeschlagene Anforderungen](#failedrequestlogs)
- [Nächste Schritte](#nextsteps)

<h2><a name="prerequisites"></a>Voraussetzungen</h2>

Dieses Lernprogramm verwendet die Entwicklungsumgebung, das Webprojekt und die Azure-Website, die Sie unter [Erste Schritte mit Azure und ASP.NET][GetStarted] eingerichtet haben. Für die Abschnitte zu Webaufträgen benötigen Sie die Anwendung, die Sie in [Erste Schritte mit dem Azure WebJobs SDK][GetStartedWJ] erstellen.

Die Codebeispiele in diesem Lernprogramm stammen aus einer C# MVC-Webanwendung. Die Prozeduren gelten jedoch auch für die Problembehandlung in Visual Basic- und Web Forms-Anwendungen.

Remotedebuggen erfordert Visual Studio 2013 oder Visual Studio 2012 Update 4. Die Remotedebuggen- und **Server-Explorer**-Funktionen für Webaufträge benötigen [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) oder höher. Die weiteren im Lernprogramm beschriebenen Features funktionieren auch unter Visual Studio 2013 Express für das Web und Visual Studio 2012 Express für das Web. 

Die Streamingprotokoll-Funktion funktioniert nur für Anwendungen, die das .NET Framework 4 oder später verwenden.

<h2><a name="sitemanagement"></a>Konfigurieren und Verwalten von Websites</h2>

Visual Studio bietet Zugriff auf einen Teil der Site-Verwaltungsfunktionen und Konfigurationseinstellungen aus dem Verwaltungsportal. In diesem Abschnitt lernen Sie die verfügbaren Optionen kennen.

1. Falls Sie noch nicht mit Visual Studio in Azure angemeldet sind, klicken Sie auf die Schaltfläche **Mit Azure verbinden** im **Server-Explorer**.

Als Alternative können Sie ein Verwaltungszertifikat installieren, das den Zugriff auf Ihr Konto ermöglicht. Mit dem Verwaltungszertifikat erhält der **Server-Explorer** Zugriff auf zusätzliche Azure-Dienste (SQL-Datenbank und Mobile Dienste). Falls Sie ein Zertifikat installieren möchten, klicken Sie mit der rechten Maustaste auf den Knoten **Azure** im **Server-Explorer** und wählen Sie im Kontextmenü **Abonnements verwalten** aus. Klicken Sie im Dialogfeld **Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen und Importieren einer Abonnementdatei (auch *.publishsettings*-Datei genannt) für Ihr Azure-Konto.

	> [WACOM.NOTE]
	> Wenn Sie eine Abonnementdatei herunterladen, sollten Sie diese in einem Ordner außerhalb Ihrer Quellcodeverzeichnisse speichern (beispielsweise in den Ordner Downloads) und nach Abschluss des Importvorgangs löschen. Böswillige Benutzer, die Zugriff auf die Abonnementdatei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.

Weitere Informationen für Verbindungen zwischen Visual Studio und Azure-Ressourcen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. Erweitern Sie im **Server-Explorer** den Knoten **Azure** und anschließend den Knoten **Websites**.

3. Klicken Sie mit der rechten Maustaste auf den Knoten der Website, die Sie in [Erste Schritte mit Azure und ASP.NET][GetStarted] erstellt haben, und klicken Sie anschließend auf ][Anzeigeeinstellungen].

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

Die Registerkarte **Azure-Website** wird angezeigt, und Sie sehen die in Visual Studio verfügbaren Aufgaben für die Verwaltung und Konfiguration von Websites.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

In diesem Lernprogramm werden Sie die Auswahllisten für Protokollierung und Ablaufverfolgung verwenden.	Außerdem werden Sie Remotedebugging verwenden, allerdings wird diese Option auf andere Weise aktiviert.
   
Weitere Informationen zu den Feldern für App-Einstellungen und Verbindungszeichenfolgen in diesem Fenster finden Sie unter [Azure-Websites: How Application Strings and Connection Strings Work](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

Falls Sie eine Verwaltungsaufgabe ausführen möchten, die in diesem Fenster nicht angeboten wird, können Sie auf **Alle Website-Einstellungen** klicken, um das Verwaltungsportal in einem Browserfenster zu öffnen. Weitere Informationen finden Sie unter [Konfigurieren von Websites](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Access-Websitedateien im Server-Explorer</h2>

Das "customErrors"-Flag in der Datei "Web.config" ist üblicherweise auf "On" oder "RemoteOnly" festgelegt. Das bedeutet, dass Sie bei einem Problem keine erklärende Fehlermeldung erhalten. Für viele Fehler wird eine der folgenden Seiten angezeigt.

**Serverfehler in '/'-Anwendung:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ein Fehler ist aufgetreten:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Die Website kann die Seite nicht anzeigen.**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Der einfachste Weg für die Suche nach der Fehlerursache ist die Aktivierung detaillierter Fehlermeldungen. Im ersten der vorigen Screenshots sehen Sie, welche Änderungen Sie dazu in der Datei "web.config" vornehmen müssen. Sie könnten die Datei *web.config* im Projekt bearbeiten und das Projekt erneut bereitstellen oder eine [Web.config-Transformation](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) erstellen und ein Debugbuild bereitstellen. Es gibt jedoch eine schnellere Lösung: Im **Projektmappen-Explorer** können Sie Dateien mit der Funktion *Remoteansicht* direkt auf der Remotewebsite anzeigen und bearbeiten.

1. Erweitern Sie im **Server-Explorer** zunächst **Azure**, dann **Websites** und anschließend den Knoten der Website, auf der Sie bereitstellen möchten.

	Daraufhin werden Knoten angezeigt, über die Sie auf die Inhalts- und Protokolldateien der Website zugreifen können.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Erweitern Sie den Knoten **Dateien** und doppelklicken Sie auf die Datei *web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

Visual Studio öffnet die Datei "web.config" auf dem Remotesystem und zeigt den Text [Remote] neben dem Dateinamen in der Titelleiste an.

3. Fügen Sie die folgende Zeile zum "system.web"-Element hinzu:

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Aktualisieren Sie den Browser, der die wenig hilfreiche Fehlermeldung anzeigt, und Sie erhalten eine detailliertere Fehlermeldung, wie im folgenden Beispiel gezeigt:

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(Dieser Fehler wurde durch Hinzufügen der in rot angezeigten Zeile zu *Views\Home\Index.cshtml* erstellt.)

Die Bearbeitung der Datei "Web.config" ist nur eines der Szenarien, in denen das Lesen und Bearbeiten der Dateien auf der Azure-Website die Problembehandlung erleichtert.

<h2><a name="remotedebug"></a>Remotedebuggen</h2>

Falls die detaillierte Fehlermeldung nicht genügend Informationen liefert und sich der Fehler nicht lokal reproduzieren lässt, können Sie die Website remote im Debugmodus ausführen. Im Debugmodus können Sie Breakpoints setzen, den Speicher direkt manipulieren, Code schrittweise durchlaufen und sogar den Codepfad ändern. 

Remotedebuggen funktioniert nicht in den Express-Editionen von Visual Studio.

### Remotedebuggen von Websites

Dieser Abschnitt zeigt, wie Sie das in [Erste Schritte mit Azure und ASP.NET][GetStarted] erstellte Projekt remote debuggen können.

1. Öffnen Sie das Webprojekt, das Sie in [Erste Schritte mit Azure und ASP.NET][GetStarted] erstellt haben.

1. Öffnen Sie *Controllers\HomeController.cs*.

2. Löschen Sie die "About()"-Methode und fügen Sie den folgenden Code an ihrer Stelle ein.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Setzen Sie einen Breakpoint](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx) in der "ViewBag.Message"-Zeil.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Veröffentlichen**.

2. Wählen Sie in der Dropdownliste **Profil** dasselbe Profil aus, das Sie in [Erste Schritte mit Azure und ASP.NET][GetStarted] verwendet haben.

3. Klicken Sie auf der Registerkarte **Einstellungen**, und ändern Sie **Konfiguration** auf **Debuggen**. Klicken Sie dann auf **Veröffentlichen**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Warten Sie, bis die Veröffentlichung abgeschlossen ist und Ihr Browser die Azure-URL Ihrer Website öffnet, und schließen Sie anschließend den Browser.

5. Für Visual Studio 2013: Erweitern Sie im **Server-Explorer** zuerst **Azure** und anschließend **Websites**, klicken Sie mit der rechten Maustaste auf Ihre Website, und klicken Sie auf **Debugger anfügen**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

Der Browser öffnet automatisch Ihre Startseite in Azure. Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Bei weiteren Ausführungen in den folgenden 48 Stunden tritt keine Verzögerung auf.

6. Für Visual Studio 2012 mit Update 4:<a id="vs2012"></a>

	* Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Konfigurieren** für Ihre Website, und führen Sie einen Bildlauf nach unten zum Abschnitt **Website-Diagnose** durch.

	* Legen Sie 	* Remotedebuggen** auf **Ein** fest und legen Sie **Visual Studio-Version für Remotedebuggen** auf **2012** fest.

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* Klicken Sie im **Debug**-Menü in Visual Studio auf **An den Prozess anhängen**.

	* Geben Sie in das Feld **Qualifizierer** die URL Ihrer Website ohne das Präfix "http://" ein. 

	* Wählen Sie **Prozesse aller Benutzer anzeigen** aus.

	* Wenn Sie zur Eingabe der Anmeldeinformationen aufgefordert werden, geben Sie Benutzername und Kennwort des Benutzers ein, der zum Veröffentlichen der Website berechtigt ist. Sie erhalten diese Anmeldeinformationen, indem Sie die Registerkarte **Dashboard** für Ihre Website im Verwaltungsportal öffnen und auf **Veröffentlichungsprofil herunterladen** klicken. Wenn Sie die Datei in einem Text-Editor öffnen, finden Sie Benutzername und Passwort nach den ersten Vorkommnissen von **userName=** und **userPWD=**. 

	* Wenn die Prozesse in der Tabelle **Verfügbare Prozesse** angezeigt werden, wählen Sie **w3wp.exe** aus und klicken auf **Anhängen**.

	* Öffnen Sie die URL Ihrer Website in einem Browser.

Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Bei weiteren Ausführungen in den folgenden 48 Stunden tritt keine Verzögerung auf.

6. Klicken Sie im Menü auf **Info**.

	Visual Studio hält am Breakpoint an, wobei der Code nicht auf Ihrem lokalen Computer läuft, sondern unter Azure.

7. Zeigen Sie auf die "currentTime"-Variable, um den Zeitwert anzuzeigen.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	Bei der angezeigten Zeit handelt es sich um die Azure-Serverzeit, deren Zeitzone sich von Ihrer lokalen Einstellung unterscheiden kann.

8. Geben Sie einen neuen Wert für die "currentTime"-Variable ein, zum Beispiel "Ausführung unter Azure".

5. Drücken Sie F5, um die Ausführung fortzusetzen.

	Die Info-Seite unter Azure zeigt daraufhin den neuen Wert an, den Sie für die Variable currentTime eingegeben haben.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Remotedebuggen von Webaufträgen

In diesem Abschnitt wird gezeigt, wie Sie mithilfe des Projekts und der Website, die Sie in [Erste Schritte mit dem Azure WebJobs SDK](../websites-dotnet-webjobs-sdk) erstellt haben, remote debuggen können. Die in diesem Abschnitt gezeigten Features sind nur in Visual Studio 2013 mit Update 4 verfügbar.

1. Öffnen Sie das Webprojekt, das Sie ihn [Erste Schritte mit dem Azure WebJobs SDK][GetStartedWJ] erstellt haben.

1. Öffnen Sie *Functions.cs* im Projekt "ContosoAdsWebJob".

2. [Legen Sie einen Haltepunkt](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx) bei der ersten Anweisung in der Methode "GnerateThumbnail" fest.

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt (nicht das WebJob-Projekt), und klicken Sie auf **Veröffentlichen**.

2. Wählen Sie in der Dropdownliste **Profil** das gleiche Profil aus, das Sie in [Erste Schritte mit dem Azure WebJobs SDK](../websites-dotnet-webjobs-sdk) verwendet haben.

3. Klicken Sie auf der Registerkarte **Einstellungen**, und ändern Sie **Konfiguration** auf **Debuggen**. Klicken Sie dann auf **Veröffentlichen**.

	Visual Studio stellt die Web- und Webauftragsprojekte bereit, und Ihr Browser öffnet die Azure-URL der Website.

5. Erweitern Sie im **Server-Explorer** **Azure** > **Websites** > Ihre Website > **Webaufträge** > **Fortlaufend**, und klicken Sie dann mit der rechten Maustaste auf **ContosoAdsWebJob**.

7. Klicken Sie auf **Debugger anfügen**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

Der Browser öffnet automatisch Ihre Startseite in Azure. Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Fall Sie innerhalb von 48 Stunden den Debugger wieder anfügen, gibt es keine Verzögerung.

6. Erstellen Sie in einem Webbrowser, in dem die Contoso-Startseite geöffnet wird, eine neue Anzeige. 

Durch das Erstellen einer Anzeige wird eine Warteschlangennachricht erstellt, die vom Webauftrag übernommen und verarbeitet wird. Wenn das WebJobs SDK die Funktion zur Verarbeitung der Warteschlangennachricht aufruft, erreicht der Code den Haltepunkt.

7. Wenn der Debugger am Haltepunkt unterbrochen wird, können Sie Variablenwerte untersuchen und ändern, während das Programm in der Cloud ausgeführt wird. In der folgenden Abbildung zeigt der Debugger den Inhalt des "blobInfo"-Objekts, das an die "GenerateThumbnail"-Methode übergeben wurde.

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Drücken Sie F5, um die Ausführung fortzusetzen.

	Die "GenerateThumbnail"-Methode schließt die Erstellung der Miniaturansicht ab.

6. Aktualisieren Sie im Browser die Indexseite, um die Miniaturansicht anzuzeigen.

6. Drücken Sie in Visual Studio UMSCHALT + F5, um das Debuggen zu beenden.

7. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Knoten "ContosoAdsWebJob", und klicken Sie dann auf **Dashboard anzeigen**.

8. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an, und klicken Sie dann auf den Namen des Webauftrags, um zur Seite für den Webauftrag zu wechseln. 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Das Dashboard zeigt, dass die "GenerateThumbnail"-Funktion vor kurzem ausgeführt wurde.

	(Wenn Sie nächstes Mal auf **Dashboard anzeigen klicken**, müssen Sie sich nicht anmelden, und der Browser wechselt direkt zur Seite für den Webauftrag.)

9. Klicken Sie auf den Funktionsnamen, um Details zur Ausführung der Funktion anzuzeigen.

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Wenn die Funktion [Protokolle geschrieben hat](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs), können Sie zum Anzeigen auf **ToggleOutput** klicken.

### Hinweise zum Remotedebuggen

* Vermeiden Sie es, den Debugmodus in Produktion einzusetzen. Wenn Ihre Produktionswebsite nicht auf mehrere Serverinstanzen dezentral skaliert wird, verhindert das Debuggen des Webservers Reaktionen auf andere Anforderungen. Falls Sie mehrere Webserverinstanzen betreiben, erhalten Sie beim Anhängen des Debuggers eine zufällige Instanz, und Sie können nicht garantieren, dass Ihre Browseranforderungen an die richtige Instanz gehen. Außerdem ist es unüblich, Debugversionen in Produktion bereitzustellen, und Compileroptimierungen für Releaseversionen verhindern die zeilenweise Anzeige der Vorgänge in Ihrem Quellcode. Die beste Möglichkeit zur Problembehandlung von Produktionsproblemen sind Ablaufverfolgung und Webserverprotokolle.

* Vermeiden Sie es beim Remotedebuggen, lange an Breakpoints anzuhalten. Azure behandelt Prozesse, die länger als einige Minuten angehalten sind, als nicht reagierend und beendet diese.

* Beim Debuggen schickt der Server Daten an Visual Studio und verursacht möglicherweise zusätzliche Kosten für Bandbreite. Weitere Informationen zu Bandbreitentarifen finden Sie unter [Azure Pricing](/en-us/pricing/calculator/).

* Stellen sicher, dass das "Debug"-Attribut des "Compilation"-Elements in der Datei *Web.config* auf "true" festgelegt ist. Dieses Attribut ist beim Veröffentlichen einer Debug-Buildkonfiguration standardmäßig "true".

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Falls der Debugger den gewünschten Code nicht schrittweise ausführt, müssen Sie möglicherweise die Einstellung "Nur eigenen Code" ändern.  Weitere Informationen finden Sie unter [Schrittweises Durchlaufen für "Nur mein Code" beschränken](http://msdn.microsoft.com/en-us/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Bei Aktivierung der Remotedebuggen-Funktion startet ein Timer auf dem Server, und die Funktion wird nach 48 Stunden automatisch abgeschaltet. Dieses Limit von 48 Stunden existiert aus Sicherheits- und Leistungsgründen. Sie können die Funktion jederzeit und beliebig oft aktivieren. Wenn Sie nicht aktiv debuggen, sollten Sie die Funktion jedoch deaktivieren.

* Sie können den Debugger manuell an jeden Prozess anfügen, nicht nur an den Websiteprozess (w3wp.exe). Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter [Debuggen in Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a>Übersicht über Diagnoseprotokolle</h2>

ASP.NET-Anwendungen in Azure-Websites können die folgenden Arten von Protokollen generieren:

* **Anwendungsnachverfolgungsprotokolle**<br/>
  Die Anwendung erstellt diese Protokolle durch Aufrufen von Methoden der [System.Diagnostics.Trace](http://msdn.microsoft.com/en-us/library/system.diagnostics.trace.aspx)-Klasse.
* **Webserverprotokolle**<br/>
  Der Webserver erstellt einen Protokolleintrag für jede HTTP-Anforderung an die Website.
* **Detaillierte Fehlerprotokolle**<br/>
Der Webserver erstellt eine HTML-Seite mit zusätzlichen Informationen für fehlgeschlagene HTTP-Anforderungen (Anforderungen mit einem Statuscode 400 oder höher). 
* **Fehlgeschlagene Anforderungsnachverfolgungsprotokolle**<br/>
Der Webserver erstellt eine XML-Datei mit detaillierten Ablaufverfolgungsinformationen für fehlgeschlagene HTTP-Anforderungen. Der Webserver liefert außerdem eine XSL-Datei zur Formatierung der XML-Datei in einem Browser.
  
Protokollierung kann die Leistung von Websites beeinträchtigen. Daher können Sie die verschiedenen Protokolltypen unter Azure bei Bedarf einzeln aktivieren und deaktivieren. Für Anwendungsprotokolle können Sie angeben, dass nur Protokolleinträge oberhalb eines bestimmten Schweregrads geschrieben werden sollen. Bei der Erstellung neuer Websites ist die gesamte Protokollierung standardmäßig deaktiviert.

Die Protokolle werden in den Ordner *LogFiles* im Dateisystem Ihrer Website geschrieben und sind über FTP zugänglich. Webserver- und Anwendungsprotokolle können auch in ein Azure-Speicherkonto geschrieben werden. Speicherkonten bieten mehr Kapazität für Protokolle als das Dateisystem. Protokolle im Dateisystem sind beschränkt auf 100 Megabyte. (Protokolle im Dateisystem werden nur für kurze Zeit aufbewahrt. Azure löscht alte Protokolldateien, um Platz für neue Dateien zu machen, wenn das Limit erreicht ist.)  

<h2><a name="apptracelogs"></a>Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen </h2>

In diesem Abschnitt werden Sie die folgenden Aufgaben ausführen:

* Fügen Sie dem Webprojekt, das Sie in [Erste Schritte mit Azure und ASP.NET][GetStarted] erstellt haben, Ablaufverfolgungsanweisungen hinzu.
* Anzeigen der Protokolle, wenn Sie das Projekt lokal ausführen.
* Zeigen Sie die Protokolle an, während diese von der Anwendung in Azure generiert werden. 

Informationen zum Erstellen von Anwendungsprotokollen in Webaufträgen finden Sie unter [Verwenden des Azure-Warteschlangenspeichers mithilfe des WebJobs SDK - Schreiben von Protokollen](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). Die folgenden Anweisungen für das Anzeigen von Protokollen und das Speichern in Azure gelten auch für Anwendungsprotokolle, die von Webaufträgen erstellt werden. 

### Hinzufügen von Ablaufverfolgungs-Anweisungen zur Anwendung

1. Öffnen Sie *Controllers\HomeController.cs* und ersetzen Sie den Inhalt der Datei durch den folgenden Code, um "Trace"-Anweisungen und eine "using"-Anweisung für "System.Diagnostics" hinzuzufügen:

		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
		    public class HomeController : Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Entering Index method");
		            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
		            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Index method");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entering About method");
		            ViewBag.Message = "Your app description page.";
		            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Leaving About method");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entering Contact method");
		            ViewBag.Message = "Your contact page.";
		            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Contact method");
		            return View();
		        }
		    }
		}
		
				
### Lokale Anzeige der Ablaufverfolgungs-Ausgabe

3. Drücken Sie F5, um die Anwendung im Debugmodus auszuführen.

Der Standard-Ablaufverfolgungs-Listener schreibt sämtliche Ausgaben in das **Ausgabefenster**, zusammen mit anderen Debugausgaben. Die folgende Abbildung zeigt die Ausgabe der Ablaufverfolgungs-Anweisungen, die Sie zur "Index"-Methode hinzugefügt haben.

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	Anhand der folgenden Schritte können Sie Ablaufverfolgungs-Ausgaben in einer Webseite anzeigen, ohne im Debugmodus zu kompilieren.

2. Öffnen Sie die Web.config-Datei der Anwendung (die Datei im Projektordner), und fügen Sie das Element "<system.diagnostics>" am Ende der Datei direkt vor dem abschließenden "</configuration>"-Element hinzu:

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>

	Der "WebPageTraceListener" ermöglicht Ihnen die Anzeige der Ablaufverfolgungsausgabe durch Wechseln zu "/trace.axd".

3. Fügen Sie ein <a href="http://msdn.microsoft.com/en-us/library/vstudio/6915t83k(v=vs.100).aspx">trace</a>-Element unter "<system.web>" in die Datei "Web.config" ein, wie im folgenden Beispiel gezeigt:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Drücken Sie STRG+F5, um die Anwendung auszuführen.

4. Fügen Sie *trace.axd* zu der URL in der Adressleiste hinzu, und drücken Sie die Eingabetaste (die URL sollte in etwa so aussehen: http://localhost:53370/trace.axd).

5. Klicken Sie auf der Seite **Anwendungsablaufverfolgung** auf **Details anzeigen** in der ersten Zeile (nicht in der BrowserLink-Zeile).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

Daraufhin wird die Seite **Anforderungsdetails** angezeigt, und im Bereich **Überwachungsinformationen** sehen Sie die Ausgabe der Ablaufverfolgungs-Anweisungen, die Sie zur "Index"-Methode hinzugefügt haben.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

"trace.axd" ist standardmäßig nur lokal verfügbar. Sie können "localOnly="false"" zum "trace"-Element der "Web.config"-Datei hinzufügen, um diese Seite auch remote verfügbar zu machen, wie im folgenden Beispiel gezeigt:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	"trace.axd" sollte jedoch in Produktionswebsites aus Sicherheitsgründen deaktiviert werden. In den folgenden Abschnitten lernen Sie einen einfacheren Weg kennen, um Ablaufverfolgungsprotokolle in Azure-Websites zu lesen.

### Anzeige der Ablaufverfolgungs-Ausgabe in Azure

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt, und klicken Sie auf **Veröffentlichen**.

2. Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.

Nachdem Visual Studio Ihr Update veröffentlicht hat, öffnet sich ein Browserfenster zu Ihrer Startseite (sofern sie **Ziel-URL** in der Registerkarte **Verbindung** nicht gelöscht haben).

3. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf Ihre Website, und wählen Sie **Streamingprotokolle im Ausgabefenster anzeigen**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

Das **Ausgabefenster** zeigt an, dass Sie mit dem Protokollstreamingdienst verbunden sind und fügt eine Benachrichtigungszeile für jede Minute hinzu, in der kein anzuzeigendes Protokoll eingeht.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Klicken Sie im Browserfenster, in dem die Startseite Ihrer Anwendung geöffnet ist, auf **Contact**.

Innerhalb weniger Sekunden wird die Ablaufverfolgung mit dem Schweregrad "Fehler", die Sie der "Contact"-Methode hinzugefügt haben, im **Ausgabefenster** angezeigt.

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

Visual Studio zeigt standardmäßig nur Ablaufverfolgungsprotokolle mit dem Schweregrad Fehler an, wenn der Protokollüberwachungsdienst gestartet wird. Bei der Erstellung einer neuen Azure-Website ist sämtliche Protokollierung standardmäßig deaktiviert, wie Sie zuvor beim Öffnen der Seite mit den Websiteeinstellungen gesehen haben:

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


Als Sie jedoch **Streamingprotokolle im Ausgabefenster anzeigen** ausgewählt haben, hat Visual Studio **Anwendungsprotokollierung(Dateisystem)** automatisch auf **Fehler** eingestellt, und Protokolle mit dem Schweregrad Fehler werden gemeldet. Sie können diese Einstellung auf **Ausführlich** ändern, falls Sie sämtliche Ablaufverfolgungs-Protokolle sehen möchten. Wenn Sie einen Schweregrad unterhalb von Fehler auswählen, werden die Protokolleinträge der höheren Schweregrade ebenfalls geschrieben. Wenn Sie also ausführlich auswählen, werden die Protokolleinträge für Information, Warnung und Fehler geschrieben.  

4. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Website, und klicken Sie anschließend wie oben auf **Anzeigeeinstellungen**.

5. Ändern Sie **Anwendungsprotokollierung (Dateisystem)** zu **Ausführlich** und klicken Sie auf **Speichern**.
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Klicken Sie im Browserfenster, in dem Ihre **Contact**-Seite angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

Innerhalb weniger Sekunden wird im **Ausgabefenster** Ihre gesamte Ablaufverfolgungsausgabe angezeigt.

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

In diesem Abschnitt haben Sie die Protokollierung mithilfe der Azure-Websiteeinstellungen aktiviert bzw. deaktiviert. Sie können die Ablaufverfolgungs-Listener auch über die Datei Web.config aktivieren bzw. deaktivieren. Wenn Sie die Datei Web.config ändern, wird jedoch die Anwendungsdomäne neu gestartet, was bei der Aktivierung der Protokollierung über die Website nicht der Fall ist. Falls das Problem schwer zu reproduzieren ist oder nur zeitweilig auftritt, kann es passieren, dass das Problem beim Neustart der Domäne verschwindet und Sie warten müssen, bis es erneut auftritt. Beim Aktivieren der Diagnose in Azure ist dies nicht der Fall, und Sie können sofort mit der Erfassung der Fehlerinformationen beginnen.

### Funktionen des Ausgabefensters

Die Registerkarte **Azure-Protokolle** im **Ausgabefenster** enthält verschiedene Schaltflächen und ein Textfeld:

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Diese Schaltflächen bieten die folgenden Funktionen:

* Löschen des **Ausgabefensters**.
* Aktivieren bzw. Deaktivieren des Zeilenumbruchs.
* Starten bzw. Stoppen der Protokollüberwachung.
* Angeben, welche Protokolle überwacht werden sollen.
* Herunterladen von Protokollen.
* Filtern von Protokollen anhand von Suchzeichenfolgen oder regulären Ausdrücken.
* Schließen Sie das **Ausgabefenster**.

Wenn Sie eine Suchzeichenfolge oder einen regulären Ausdruck eingeben, filtert Visual Studio die Protokollinformationen clientseitig. Daher können Sie die Kriterien eingeben, nachdem die Protokolle im **Ausgabefenster** angezeigt wurden, und Sie können die Filterkriterien ändern, ohne die Protokolle neu generieren zu müssen.

<h2><a name="webserverlogs"></a>Erstellen von Webserverprotokollen</h2>

Webserverprotokolle zeichnen sämtliche HTTP-Aktivitäten der Website auf. Sie müssen diese Protokolle für die Website aktivieren und Visual Studio mitteilen, dass Sie diese überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können 

1. Ändern Sie auf der Registerkarte **Azure-Websitekonfiguration**, die Sie im **Server-Explorer** geöffnet haben, die Webserverprotokollierung auf **Ein**, und klicken Sie auf **Speichern**.

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Angeben, welche Azure-Protokolle überwacht werden sollen**.
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Wählen Sie im Dialogfeld **Azure-Protokollierungsoptionen** die Option **Webserverprotokolle** aus und klicken Sie auf **OK**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Klicken Sie im Browserfenster, in dem Ihre Website angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

Normalerweise werden die Anwendungsprotokolle zuerst angezeigt, gefolgt von den Webserverprotokollen. Möglicherweise müssen Sie kurz warten, bis die Protokolle angezeigt werden. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Bei der ersten Aktivierung der Webserverprotokolle in Visual Studio schreibt Azure die Protokolle standardmäßig in das Dateisystem. Alternativ können Sie im Verwaltungsportal angeben, dass die Webserverprotokolle in einen Blob-Container in einem Speicherkonto geschrieben werden sollen. Weitere Informationen finden Sie im Abschnitt **Diagnose** unter [Konfigurieren von Websites](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). .

If you use the management portal to enable web server logging to an Azure storage account, and then disable logging in Visual Studio, when you re-enable logging in Visual Studio your storage account settings are restored. 

<h2><a name="detailederrorlogs"></a>View detailed error message logs</h2>

Detailed error logs provide some additional information about HTTP requests that result in error response codes (400 or above). In order to see them in the **Output** window, you have to enable them on the site and tell Visual Studio that you want to monitor them.

1. In the **Azure Website Configuration** tab that you opened from **Server Explorer**, change **Detailed Error Messages** to **On**, and then click **Save**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. In the **Output** Window, click the **Specify which Azure logs to monitor** button.

3. In the **Azure Logging Options** dialog box, click **All logs**, and then click **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. In the address bar of the browser window, add an extra character to the URL to cause a 404 error (for example, `http://localhost:53370/Home/Contactx`), and press Enter.

	After several seconds the detailed error log appears in the Visual Studio **Output** window.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Control+click the link to see the log output formatted in a browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Download file system logs</h2>

Any logs that you can monitor in the **Output** window can also be downloaded as a *.zip* file. 

1. In the **Output** window, click **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	File Explorer opens to your *Downloads* folder with the downloaded file selected.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extract the *.zip* file, and you see the following folder structure:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Application tracing logs are in *.txt* files in the *LogFiles\Application* folder.
	* Web server logs are in *.log* files in the *LogFiles\http\RawLogs* folder. You can use a tool such as [Log Parser](http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659) to view and manipulate these files.
	* Detailed error message logs are in *.html* files in the *LogFiles\DetailedErrors* folder.

	(The *deployments* folder is for files created by source control publishing; it doesn't have anything related to Visual Studio publishing. The *Git* folder is for traces related to source control publishing and the log file streaming service.)  

<h2><a name="storagelogs"></a>View storage logs</h2>

Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the management portal, and view them in the **Logs** tab of the **Azure Website** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination. 

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

5. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. In the **Create Storage Account** dialog, enter a name for the storage account. 

	The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

	The URL to access your storage account will be *{name}*.core.windows.net. 

5. Set the **Region or Affinity Group** drop-down list to the region closest to you.

	This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production site you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The website (which you'll create later) should be as close as possible to the browsers accessing your site in order to minimize latency.

6. Set the **Replication** drop-down list to **Locally redundant**. 

	When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [How To Manage Storage Accounts](/en-us/documentation/articles/storage-manage-storage-account/).

5. Klicken Sie auf **Erstellen**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. Klicken Sie in Visual Studio im Fenster **Azure-Website** auf die Registerkarte **Protokolle** und anschließend im Verwaltungsportal auf **Protokollierung konfigurieren**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

Daraufhin öffnet sich im Verwaltungsportal die Registerkarte **Konfigurieren** für Ihre Website. Sie können diese Registerkarte auch öffnen, indem Sie auf die Registerkarte **Websites** und anschließend auf Ihre Website und auf die Registerkarte **Konfigurieren** klicken.

2. Blättern Sie in der Registerkarte **Konfigurieren** im Verwaltungsportal nach unten bis zum Bereich Anwendungsdiagnose und ändern Sie den Wert von **Anwendungsprotokollierung (Tabellenspeicher)** auf **Ein**.

3. Ändern Sie die **Protokollierungsebene** auf **Informationen**.

4. Klicken Sie auf **Tabellenspeicher verwalten**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

Im Feld **Tabellenspeicher für Anwendungsdiagnose verwalten** können Sie Ihr Speicherkonto auswählen, falls Sie mehr als eines verwenden. Sie können eine neue Tabelle erstellen oder eine vorhandene Tabelle verwenden.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Aktivieren Sie das Kontrollkästchen **Tabellenspeicher für Anwendungsdiagnose verwalten**, um das Feld zu schließen.

6. Klicken Sie auf der Registerkarte **Konfigurieren** im Verwaltungsportal auf **Speichern**.

7. Klicken Sie im Browserfenster, in dem Ihre Anwendungswebsite angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

	Die von diesen Webseiten produzierten Protokollierungsinformationen werden daraufhin in Ihr Speicherkonto geschrieben.

8. Klicken Sie in Visual Studio auf der Registerkarte **Protokolle** im Fenster **Azure-Website** unter **Diagnosezusammenfassung** auf **Aktualisieren**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

Im Bereich **Diagnosezusammenfassung** werden standardmäßig die Protokolle der vergangenen 15 Minuten angezeigt. Sie können das Zeitintervall ändern, um weitere Protokolleinträge anzuzeigen. 

(Falls Sie den Fehler "Tabelle nicht gefunden" erhalten, vergewissern Sie sich, dass Sie die entsprechenden Seiten aufgerufen haben, nachdem Sie **Anwendungsprotokollierung (Tabellenspeicher)** aktiviert und auf **Speichern** geklickt haben.)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

In dieser Ansicht werden **Prozess-ID** und **Thread-ID** für die einzelnen Protokolle angezeigt. Diese Informationen sind in den Dateisystemprotokollen nicht enthalten. Sie können zusätzliche Felder anzeigen, indem Sie die Azure-Speichertabelle direkt öffnen.

8. Klicken Sie auf **Alle Anwendungsprotokolle anzeigen**.

	Daraufhin wird die Ablaufprotokolltabelle in der Azure-Speichertabellenansicht angezeigt.
   
(Falls Sie den Fehler "Sequenz enthält keine Elemente" erhalten, öffnen Sie den **Server-Explorer**, erweitern Sie den Knoten Ihres Speicherkontos unterhalb des **Azure**-Knotens, klicken Sie mit der rechten Maustaste auf **Tabellen** und klicken Sie auf **Aktualisieren**.)

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

Diese Ansicht enthält zusätzliche Felder, die in keiner der anderen Ansichten angezeigt werden. Außerdem können Sie die Protokolle in dieser Ansicht mithilfe der Abfrage-Generator-GUI filtern. Weitere Informationen finden Sie im Abschnitt "Working with Table Resources - Filtering Entities" (Arbeiten mit Tabellenressourcen - Entitätenfilter" unter [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx) (Durchsuchen von Speicherressourcen im Server-Explorer, in englischer Sprache).

7. Um die Details für eine einzelne Zeile anzuzeigen, doppelklicken Sie auf eine der Zeilen.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>Anzeigen von fehlgeschlagenen Anforderungsablaufverfolgungsprotokollen</h2>

Anhand der Protokolle für fehlgeschlagene Anforderungen können Sie im Detail herausfinden, wie IIS HTTP-Anforderungen bearbeitet, z. B. in Szenarien mit URL-Neuschreibung oder bei Authentifizierungsproblemen. 

Azure-Websites verwenden die gleiche Funktion zum Verfolgen fehlgeschlagener Anforderungen, die in IIS 7.0 und höher verfügbar ist. Sie haben jedoch keinen Zugriff auf die IIS-Einstellungen, in denen festgelegt wird, welche Fehler protokolliert werden. Wenn Sie die Verfolgung fehlgeschlagener Anforderungen aktivieren, werden alle Fehler erfasst. 

Sie können die Protokolle für fehlgeschlagene Anforderungen in Visual Studio aktivieren, allerdings lassen sich diese Protokolle nicht in Visual Studio anzeigen. Diese Protokolle liegen in Form von XML-Dateien vor. Der Streamingprotokolldienst überwacht nur Dateien, die im Nur-Text-Modus lesbar sind:  *.txt*-, *.html*- und *.log*-Dateien.

Sie können die Protokolle für fehlgeschlagene Anforderungen entweder direkt über FTP im Browser anzeigen oder mit einem FTP-Client auf Ihren lokalen Computer herunterladen. In diesem Abschnitt werden Sie die Protokolle direkt im Browser anzeigen.

1. Ändern Sie auf der Registerkarte **Konfiguration** im Fenster **Azure-Website**, das Sie vom **Server-Explorer** aus geöffnet haben, die Option **Ablaufverfolgung für Anforderungsfehler** in **Ein**, und klicken Sie auf **Speichern**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Fügen Sie in der Adressleiste des Browserfensters, in dem die Website angezeigt wird, der URL ein zusätzliches Zeichen hinzu, und drücken Sie die EINGABETASTE, um einen 404-Fehler zu verursachen.

	Daraufhin wird ein Protokoll für die fehlgeschlagene Anforderung erstellt. In den folgenden Schritten lernen Sie, wie Sie dieses Protokoll anzeigen oder herunterladen können.

2. Klicken Sie in Visual Studio auf der Registerkarte **Konfiguration** im Fenster **Azure-Website** auf **Im Verwaltungsportal öffnen**.

3. Klicken Sie im Verwaltungsportal auf **Dashboard** und anschließend auf **Zurücksetzen der Bereitstellungsanmeldeinformationen** im Bereich **Schnellansicht**.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Geben Sie einen neuen Benutzernamen und ein neues Passwort ein.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Drücken Sie im **Dashboard** im Verwaltungsportal auf F5, um die Seite zu aktualisieren, und blättern Sie nach unten zum Bereich **Bereitstellung / FTP-Benutzer**. Beachten Sie, dass der Name der Site dem Benutzernamen vorangestellt ist. **Bei der Anmeldung müssen Sie diesen kompletten Benutzernamen inklusive Name der Site verwenden, so wie hier angezeigt.**

5. Öffnen Sie in einem neuen Browserfenster die URL, die unter **FTP-Hostname** in der Registerkarte **Dashboard** des Verwaltungsportals für Ihre Website angezeigt wird. **FTP-Hostname** befindet sich in der Nähe von **Bereitstellung / FTP-Benutzer** im Bereich **Schnellübersicht**.

6. Melden Sie sich mit den zuvor erstellten FTP-Anmeldeinformationen an (inklusive vorangestelltem Namen der Site vor dem Benutzernamen).

	Daraufhin wird im Browser der Stammordner der Site angezeigt.

6. Öffnen Sie den Ordner *LogFiles*.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Öffnen Sie den Ordner mit dem Namen W3SVC plus nachgestelltem numerischem Wert.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	Der Ordner enthält XML-Dateien für sämtliche protokollierten Fehler, nachdem Sie die Protokollierung fehlgeschlagener Anforderungen aktiviert haben, sowie eine XSL-Datei für die XML-Formatierung im Browser.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Klicken Sie auf die XML-Datei für die fehlgeschlagene Anforderung, deren Ablaufverfolgungsinformationen Sie anzeigen möchten.

	Die folgende Abbildung zeigt einen Teil der Ablaufverfolgungsinformationen für einen Beispielfehler.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Nächste Schritte</h2>

Sie haben gelernt, wie Visual Studio die Anzeige der von Azure-Websites erstellten Protokolle erleichtert. Die folgenden Abschnitte enthalten Links zu weiteren Ressourcen zu verwandten Themen:

* Problembehandlung von Azure-Websites
* Debuggen in Visual Studio 
* Remotedebuggen in Azure
* Ablaufverfolgung in ASP.NET-Anwendungen
* Analyse von Webserverprotokollen
* Analyse der Ablaufverfolgung fehlgeschlagener Anforderungen
* Debuggen von Cloud-Diensten.

### Problembehandlung von Azure-Websites

Weitere Informationen zur Problembehandlung in Azure-Websites finden Sie in den folgenden Ressourcen:

* [Überwachen von Websites](/en-us/manage/services/web-sites/how-to-monitor-websites/)
* [Untersuchen von Speicherlecks in Azure-Websites mit Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Microsoft ALM-Blogbeiträge über Visual Studio-Funktionen für die Untersuchung von Problemen mit verwaltetem Speicher.
* [Onlinetools für Windows Azure-Websites, die Sie kennen sollten](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blogbeitrag von Amit Apple.

Falls Sie spezifische Fragen zur Problembehandlung haben, können Sie diese in einem der folgenden Foren stellen:

* [Das Azure-Forum auf der ASP.NET-Website](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Das Azure-Forum auf MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Debuggen in Visual Studio 

Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter dem MSDN-Thema [Debuggen in Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx) und unter [Debugging Tips with Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx) (Tipps zum Debuggen in Visual Studio 2010, in englischer Sprache).

### Remotedebuggen in Azure

Weitere Informationen zum Remotedebuggen für Azure-Websites und Webaufträge finden Sie in den folgenden Ressourcen:

* [Einführung in das Remotedebuggen für Azure-Websites](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Einführung in das Remotedebuggen von Azure-Websites, Teil 2 - Einblick in das Remotedebuggen](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Einführung in das Remotedebuggen von Azure-Websites, Teil 3 - Mehrinstanzenumgebung und GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debuggen von Webaufträgen (Video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Falls Ihre Website eine Azure-Web-API oder ein Mobile Services-Back-End verwendet und Sie dieses debuggen möchten, finden Sie weitere Informationen unter [Debuggen des .NET-Back-Ends in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Ablaufverfolgung in ASP.NET-Anwendungen

Momentan sind keine vollständigen und aktuellen Einführungen zur Ablaufverfolgung in ASP.NET im Internet verfügbar. Beginnen Sie daher mit den älteren Einführungen für Web Forms, als MVC noch nicht existierte, und ergänzen Sie diese Lektüre mit neueren Blogeinträgen zu speziellen Themen. Die folgenden Ressourcen bieten gute Einstiegspunkte:

* [Überwachung und Telemetrie (Erstellen realer Cloud-Apps mit Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Dieses E-Book-Kapitel enthält Empfehlungen zur Ablaufverfolgung in Azure-Cloudanwendungen.
* [ASP.NET-Ablaufverfolgung](http://msdn.microsoft.com/en-us/library/ms972204.aspx)<br/>
  Ein älterer Artikel, der sich jedoch immer noch gut als Einstieg in das Thema eignet.
* [Ablaufverfolgungslistener](http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx)<br/>
Informationen zu Ablaufverfolgungslistenern, erwähnen aber nicht [WebPageTraceListener](http://msdn.microsoft.com/en-us/library/system.web.webpagetracelistener.aspx).
* [Exemplarische Vorgehensweise: Integrieren der ASP.NET-Ablaufverfolgung mit der "System.Diagnostics"-Ablaufverfolgung](http://msdn.microsoft.com/en-us/library/b0ectfxd.aspx)<br/>
  Dieser Artikel ist zu alt, enthält jedoch einige zusätzliche Informationen, die der einführende Artikel nicht enthält.
* [Ablaufverfolgung in ASP.NET MVC-Razoransichten](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
Dieser Blogeintrag behandelt neben der Ablaufverfolgung in Razoransichten auch die Erstellung von Fehlerfiltern zur Protokollierung aller Ausnahmefehler in MVC-Anwendungen. Informationen zur Protokollierung aller Ausnahmefehler in Web Forms-Anwendungen finden Sie im Global.asax-Beispiel unter [Vollständiges Beispiel für Fehlerhandler](http://msdn.microsoft.com/en-us/library/bb397417.aspx) auf MSDN. Falls Sie in MVC oder Web Forms bestimmte Ausnahmen protokollieren möchten, diese Ausnahmen jedoch vom Standard-Framework behandelt werden sollen, können Sie diese wie im folgenden Beispiel abfangen und erneut auslösen:

        try
        {
           // Der Code, durch den eine Ausnahme verursacht werden kann.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Ablaufprotokollierung und Streamingdiagnose mit der Azure-Befehlszeile (plus Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
Ausführen der Visual Studio-Aktionen aus diesem Lernprogramm über die Befehlszeile. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) ist ein Tool zum Debuggen von ASP.NET-Anwendungen. 
* [Using Azure Web Site Logging and Diagnostics - with David Ebbo](http://www.windowsazure.com/en-us/documentation/videos/azure-web-site-logging-and-diagnostics/) und [Streaming Logs from Azure Web Sites - with David Ebbo](http://www.windowsazure.com/en-us/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videos von Scott Hanselman und David Ebbo.

Für die Fehlerprotokollierung können Sie Open Source-Protokollframeworks wie z. B. [ELMAH](http://nuget.org/packages/elmah/) verwenden, anstatt Ihren eigenen Ablaufverfolgungscode zu schreiben. Weitere Informationen finden Sie unter [Scott Hanselman's blog posts about ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Sie müssen nicht unbedingt die Ablaufverfolgung von ASP.NET oder System.Diagnostics verwenden, um Streamingprotokolle aus Azure zu erhalten. Der Streamingprotokolldienst für Azure-Websites kann beliebige *.txt*-, *.html*- oder *.log*-Dateien streamen, die er im Ordner *LogFiles* findet. Sie können also Ihr eigenes Protokollierungssystem erstellen, das in das Dateisystem der Website schreibt: Ihr Datei wird dann automatisch gestreamt und heruntergeladen. Dazu müssen Sie nur den Anwendungscode schreiben, der Dateien im Ordner *d:\home\logfiles* erstellt. 

### Analyse von Webserverprotokollen

Weitere Informationen zur Analyse von Webserverprotokollen finden Sie in den folgenden Ressourcen:

* [LogParser](http://www.microsoft.com/en-us/download/details.aspx?id=24659)<br/>
  Ein Tool zum Anzeigen von Daten in Webserverprotokollen (*.log*-Dateien).
* [Problembehandlung bei IIS-Leistungsproblemen oder Anwendungsfehlern mithilfe von LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Eine Einführung in das LogParser-Tool, mit denen Sie Webserverprotokolle analysieren können.
* [Blogeinträge von Robert McMurray zum Thema LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [HTTP-Statuscodes in IIS 7.0, IIS 7.5 und IIS 8.0](http://support.microsoft.com/kb/943891)

### Analyse der Ablaufverfolgung fehlgeschlagener Anforderungen

Die Microsoft TechNet-Website enthält einen Abschnitt zum Thema [Ablaufverfolgung fehlgeschlagener Anforderungen](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing), der für das Verständnis dieser Protokolle hilfreich ist. Diese Dokumentation konzentriert sich jedoch hauptsächlich auf die Ablaufverfolgung fehlgeschlagener Anforderungen in IIS. Diese Option ist für Azure-Websites nicht verfügbar.

### Debuggen von Cloud-Diensten.

Informationen darüber, wie Sie statt einer Website einen Azure-Cloud-Dienst debuggen, finden Sie unter [Debuggen von Cloud-Diensten](http://msdn.microsoft.com/en-us/library/windowsazure/ee405479.aspx).




[Erste Schritte]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/

