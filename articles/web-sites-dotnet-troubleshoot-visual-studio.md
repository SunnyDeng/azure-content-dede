<properties title="Troubleshooting Azure Web Sites in Visual Studio" pageTitle="Troubleshooting Azure Web Sites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Web Site by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter="Web Sites" authors="tdykstra" solutions="" />

Problembehandlung von Windows Azure-Websites in Visual Studio
=============================================================

Bei Entwicklung und Tests von Webanwendungen können Sie für die Problembehandlung entweder [den Debugmodus](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx) oder [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) verwenden. Sie können den Debugmodus entweder lokal in IIS Express oder remote in einer Azure-Websites ausführen. Für Fehler, die nur in Produktion auftreten, können Sie hingegen die Protokolle betrachten, die vom Anwendungscode oder vom Webserver geschrieben werden. In diesem Lernprogramm lernen Sie die Visual Studio-Tools kennen, mit denen Sie Anwendungen in Azure-Websites mithilfe des Debugmodus remote debuggen oder auch die Anwendungs- und Webserverprotokolle anzeigen können.

Sie lernen Folgendes:

-   Welche Azure-Verwaltungsfunktionen sind in Visual Studio verfügbar?
-   Verwenden der Remoteansicht in Visual Studio für schnelle Änderungen an Remote-Websites
-   Ausführen des Debugmodus remote, während ein Projekt in einer Azure-Website läuft.
-   Erstellen von Anwendungs-Ablaufprotokollen und Live-Anzeige dieser Protokolle.
-   Anzeige von Webserverprotokollen inklusive detaillierter Fehlermeldungen und Verfolgung fehlgeschlagener Anforderungen.
-   Senden von Diagnoseprotokollen an ein Azure-Speicherkonto und Anzeige der Protokolle.

### Teile des Lernprogramms

1.  [Voraussetzungen](#prerequisites)
2.  [Sitekonfiguration und -Verwaltung](#sitemanagement)
3.  [Remoteansicht](#remoteview)
4.  [Remotedebuggen](#remotedebug)
5.  [Übersicht über Diagnoseprotokolle](#logsoverview)
6.  [Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen](#apptracelogs)
7.  [Erstellen von Webserverprotokollen](#webserverlogs)
8.  [Anzeigen detaillierter Fehlermeldungsprotokolle](#detailederrorlogs)
9.  [Herunterladen von Dateisystemprotokollen](#downloadlogs)
10. [Anzeigen von Speicherprotokollen](#storagelogs)
11. [Anzeigen von Protokollen für fehlgeschlagene Anforderungen](#failedrequestlogs)
12. [Nächste Schritte](#nextsteps)

Voraussetzungen
---------------

Dieses Lernprogramm verwendet Entwicklungsumgebung, Webprojekt und Azure-Website, die Sie unter [Erste Schritte mit Windows Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/) eingerichtet haben. Die Codebeispiele in diesem Lernprogramm stammen aus einer C\# MVC-Webanwendung. Die Prozeduren gelten jedoch auch für die Problembehandlung in Visual Basic- und Web Forms-Anwendungen.

Zum Remotedebuggen benötigen Sie Visual Studio 2013 oder Visual Studio 2012 mit Update 4. Die weiteren im Lernprogramm beschriebenen Features funktionieren auch unter Visual Studio 2013 Express für das Web und Visual Studio 2012 Express für das Web.

Die Streamingprotokoll-Funktion funktioniert nur für Anwendungen, die das .NET Framework 4 oder später verwenden.

Site-Konfiguration und -Verwaltung
----------------------------------

Visual Studio bietet Zugriff auf einen Teil der Site-Verwaltungsfunktionen und Konfigurationseinstellungen aus dem Verwaltungsportal. In diesem Abschnitt lernen Sie die verfügbaren Optionen kennen.

1.  Falls Sie noch nicht mit Visual Studio in Azure angemeldet sind, klicken Sie auf die Schaltfläche **Mit Azure verbinden** im **Server-Explorer**.

    Als Alternative können Sie ein Verwaltungszertifikat installieren, das den Zugriff auf Ihr Konto ermöglicht. Mit dem Verwaltungszertifikat erhält der Server-Explorer Zugriff auf zusätzliche Azure-Dienste (SQL-Datenbank und Mobile Dienste). Falls Sie ein Zertifikat installieren möchten, klicken Sie mit der rechten Maustaste auf den Knoten **Azure** im **Server-Explorer** und wählen Sie im Kontextmenü **Abonnements verwalten** aus. Klicken Sie im Dialogfeld **Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen und Importieren einer Abonnementdatei (auch *.publishsettings*-Datei genannt) für Ihr Azure-Konto.

    > [WACOM.NOTE]
    > Wenn Sie eine Abonnementdatei herunterladen, sollten Sie diese in einem Ordner außerhalb Ihrer Quellcodeverzeichnisse speichern (beispielsweise in den Ordner Downloads) und nach Abschluss des Importvorgangs löschen. Böswillige Benutzer, die Zugriff auf die Abonnementdatei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.

    Weitere Informationen für Verbindungen zwischen Visual Studio und Azure-Ressourcen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2.  Erweitern Sie im **Server-Explorer** den Knoten **Azure** und anschließend den Knoten **Websites**.

3.  Klicken Sie mit der rechten Maustaste auf den Knoten der Website, die Sie in [Erste Schritte mit Windows Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/) erstellt haben, und klicken Sie anschließend auf **Anzeigeeinstellungen**.

    ![Anzeigeeinstellungen im Server-Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Die Registerkarte **Azure-Website** wird angezeigt, und Sie sehen die in Visual Studio angebotenen Aufgaben für Siteverwaltung und -Konfiguration.

    ![Azure-Website-Fenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    In diesem Lernprogramm werden Sie die Auswahllisten für Protokollierung und Ablaufverfolgung verwenden. Außerdem werden Sie Remotedebugging verwenden, allerdings wird diese Option auf andere Weise aktiviert.

    Weitere Informationen zu den Feldern für App-Einstellungen und Verbindungszeichenfolgen in diesem Fenster finden Sie unter [Azure-Websites: How Application Strings and Connection Strings Work](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (Funktionsweise von Anwendungs- und Verbindungszeichenfolgen, in englischer Sprache).

    Falls Sie eine Verwaltungsaufgabe ausführen möchten, die in diesem Fenster nicht angeboten wird, können Sie auf **Alle Website-Einstellungen** klicken, um das Verwaltungsportal in einem Browserfenster zu öffnen. Weitere Informationen finden Sie unter [Konfigurieren von Websites](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Remoteansicht
-------------

Die `customErrors`-Kennzeichnung in der Datei Web.config ist üblicherweise auf `On` oder `RemoteOnly` eingestellt. Auf diese Weise erhalten Sie keine hilfreiche Fehlermeldung, wenn ein Problem auftritt. Für viele Fehler bekommen Sie eine der folgenden Seiten angezeigt.

**Serverfehler in '/'-Anwendung:**
![Wenig hilfreiche Fehlerseite](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ein Fehler ist aufgetreten:**
![Wenig hilfreiche Fehlerseite](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Die Website kann diese Seite nicht anzeigen**
![Wenig hilfreiche Fehlerseite](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Der einfachste Weg für die Suche nach der Fehlerursache ist die Aktivierung detaillierter Fehlermeldungen. Im ersten der vorigen Screenshots sehen Sie, welche Änderungen Sie dazu in der Web.config-Datei vornehmen müssen. Sie können die *Web.config*-Datei entweder im Projekt bearbeiten und das Projekt neu bereitstellen oder eine [Web.config-Transformation erstellen](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) und eine Debugversion bereitstellen, aber es gibt noch einen einfacheren Weg: Im **Projektmappen-Explorer** können Sie Dateien auf dem Remotesystem mithilfe der *Remoteansicht*-Funktion direkt anzeigen und bearbeiten.

1.  Erweitern Sie im **Server-Explorer** zunächst **Azure**, dann **Websites** und anschließend den Knoten der Website, die Sie bereitstellen möchten.

    Daraufhin werden Knoten angezeigt, mit denen Sie Zugriff auf die Inhalts- und Protokolldateien der Website erhalten.

    ![Datei und Protokolldateien](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2.  Erweitern Sie den Knoten **Dateien** und doppelklicken Sie auf die Datei *Web.config*.

    ![Öffnen der Datei Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio öffnet die Datei Web.config auf dem Remotesystem und zeigt den Text [Remote] neben dem Dateinamen in der Titelleiste an.

3.  Fügen Sie die folgende Zeile zum `system.web`-Element hinzu:

    `<customErrors mode="off"></customErrors>`

    ![Bearbeiten der Datei Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4.  Aktualisieren Sie den Browser, der die wenig hilfreiche Fehlermeldung anzeigt, und Sie erhalten eine detailliertere Fehlermeldung, wie im folgenden Beispiel gezeigt:

    ![Detaillierte Fehlermeldung](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Dieser Fehler wurde durch Hinzufügen der in rot angezeigten Zeile zu *Views\\Home\\Index.cshtml* erstellt.)

Änderungen an der Datei Web.config ist nur eines der Szenarien, in denen die Möglichkeit zum Lesen und Bearbeiten der Dateien in Ihrer Azure-Website sie Problembehandlung erleichtert.

Remotedebuggen
--------------

Falls die detaillierte Fehlermeldung nicht genügend Informationen liefert und sich der Fehler nicht lokal reproduzieren lässt, können Sie die Website remote im Debugmodus ausführen. Im Debugmodus können Sie Breakpoints setzen, den Speicher direkt manipulieren, Code schrittweise durchlaufen und sogar den Codepfad ändern.

Remotedebuggen funktioniert nicht in den Express-Editionen von Visual Studio.

Dieser Abschnitt zeigt, wie Sie das in [Erste Schritte mit Windows Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/) erstellte Projekt remote debuggen können.

1.  Öffnen Sie das Webprojekt, das Sie in [Erste Schritte mit Windows Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/) erstellt haben.

2.  Öffnen Sie die Datei *Controllers\\HomeController.cs*.

3.  Löschen Sie die `About()`-Methode und fügen Sie den folgenden Code an ihrer Stelle ein.

         public ActionResult About()
         {
             string currentTime = DateTime.Now.ToLongTimeString();
             ViewBag.Message = "Die aktuelle Zeit ist " + currentTime;
             return View();
         }

4.  [Setzen Sie einen Breakpoint](http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx) in der `ViewBag.Message`-Zeile.

5.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt und anschließend auf **Veröffentlichen**.

6.  Wählen Sie in der Dropdownliste **Profil** dasselbe Profil aus, das Sie in [Erste Schritte mit Windows Azure und ASP.NET](/en-us/develop/net/tutorials/get-started/) verwendet haben.

7.  Wechseln Sie zur Registerkarte **Einstellungen**, ändern Sie **Konfiguration** auf **Debug** und klicken Sie anschließend auf **Veröffentlichen**.

    ![Veröffentlichen im Debugmodus](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8.  Warten Sie, bis die Veröffentlichung abgeschlossen ist und Ihr Browser die Azure-URL Ihrer Website öffnet, und schließen Sie anschließend den Browser.

9.  Für Visual Studio 2013: Erweitern Sie im **Server-Explorer** zuerst **Azure** und anschließend **Websites**, klicken Sie mit der rechten Maustaste auf Ihre Website und klicken Sie auf **Debugger anfügen**.

    ![Debugger anfügen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Der Browser öffnet automatisch Ihre Startseite in Azure. Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Bei weiteren Ausführungen in den folgenden 48 Stunden tritt keine Verzögerung auf.

10. Für Visual Studio 2012 mit Update 4:

    -   Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Konfigurieren** für Ihre Website und blättern Sie nach unten bis zum Bereich **Site-Diagnose**.

    -   Stellen Sie **Remotedebuggen** auf **Ein** und stellen Sie **Visual Studio-Version für Remotedebuggen** auf **2012**.

    ![Aktivieren von Remotedebuggen im Verwaltungsportal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)

    -   Klicken Sie im **Debug**-Menü in Visual Studio auf **An den Prozess anhängen**.

    -   Geben Sie in das Feld **Qualifizierer** die URL Ihrer Website ohne das Präfix `http://` ein.

    -   Wählen Sie **Prozesse aller Benutzer anzeigen** aus.

    -   Wenn Sie zur Eingabe der Anmeldeinformationen aufgefordert werden, geben Sie Benutzername und Passwort des Benutzers ein, der die notwendigen Berechtigungen zum Veröffentlichen der Website hat. Sie erhalten diese Anmeldeinformationen, indem Sie die Dashboard-Registerkarte für Ihre Website im Verwaltungsportal öffnen und auf **Veröffentlichungsprofil herunterladen** klicken. Wenn Sie die Datei in einem Text-Editor öffnen, finden Sie Benutzername und Passwort nach den ersten Vorkommnissen von **userName=** und **userPWD=**.

    -   Wenn die Prozesse in der Tabelle **Verfügbare Prozesse** angezeigt werden, wählen Sie **w3wp.exe** aus und klicken Sie auf **Anhängen**.

    -   Öffnen Sie die URL Ihrer Website in einem Browser.

    Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Bei weiteren Ausführungen in den folgenden 48 Stunden tritt keine Verzögerung auf.

11. Klicken Sie im Menü auf **Info**.

    Visual Studio hält am Breakpoint an, wobei der Code nicht auf Ihrem lokalen Computer läuft, sondern unter Azure.

12. Zeigen Sie auf die Variable `currentTime`, um den Zeitwert anzuzeigen.

    ![Anzeigen von Variablen im Debugmodus in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Bei der angezeigten Zeit handelt es sich um die Azure-Serverzeit, deren Zeitzone sich von Ihrer lokalen Einstellung unterscheiden kann.

13. Geben Sie einen neuen Wert für die Variable `currentTime` ein, z. B. "Ausführung unter Azure".

14. Drücken Sie F5, um die Ausführung fortzusetzen.

    Die Info-Seite unter Azure zeigt daraufhin den neuen Wert an, den Sie für die Variable currentTime eingegeben haben.

    ![Info-Seite mit neuem Wert](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### Hinweise zum Remotedebuggen

-   Vermeiden Sie es, den Debugmodus in Produktion einzusetzen. Falls Ihre Produktions-Website nicht auf mehrere Serverinstanzen verteilt ist, wird beim Debuggen der Netzwerkverkehr zu Ihrer einzigen Webserverinstanz gebunden. Falls Sie mehrere Webserverinstanzen betreiben, erhalten Sie beim Anhängen des Debuggers eine zufällige Instanz, und Sie können nicht garantieren, dass Ihre Browseranforderungen an die richtige Instanz gehen. Außerdem ist es unüblich, Debugversionen in Produktion bereitzustellen, und Compileroptimierungen für Releaseversionen verhindern die zeilenweise Anzeige der Vorgänge in Ihrem Quellcode. Die beste Möglichkeit zur Problembehandlung von Produktionsproblemen sind Ablaufverfolgung und Webserverprotokolle.

-   Vermeiden Sie es beim Remotedebuggen, lange an Breakpoints anzuhalten. Azure behandelt Prozesse, die länger als einige Minuten angehalten sind, als nicht reagierend und beendet diese.

-   Beim Debuggen schickt der Server Daten an Visual Studio und verursacht möglicherweise zusätzliche Kosten für Bandbreite. Weitere Informationen zu Bandbreitentarifen finden Sie unter [Azure Pricing](/en-us/pricing/calculator/) (Azure-Preisübersicht, in englischer Sprache).

-   Stellen Sie sicher, dass das Attribut `debug` im Element `compilation` in der Datei *Web.config* auf "true" gesetzt ist. Dieses Attribut ist beim Veröffentlichen einer Debug-Buildkonfiguration standardmäßig "true".

          <system.web>
            <compilation debug="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
          </system.web>

-   Falls der Debugger den gewünschten Code nicht schrittweise ausführt, müssen Sie möglicherweise die Einstellung "Nur eigenen Code" ändern. Weitere Informationen finden Sie unter [Schrittweises Durchlaufen für "Nur mein Code" beschränken](http://msdn.microsoft.com/en-us/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

-   Bei Aktivierung der Remotedebuggen-Funktion startet ein Timer auf dem Server, und die Funktion wird nach 48 Stunden automatisch abgeschaltet. Dieses Limit von 48 Stunden existiert aus Sicherheits- und Leistungsgründen. Sie können die Funktion jederzeit und beliebig oft aktivieren. Wenn Sie nicht aktiv debuggen, sollten Sie die Funktion jedoch deaktivieren.

-   Nun können Sie den Debugger manuell an beliebige Prozesse anhängen. Sie können nicht nur den Website-Prozess (w3wp.exe) debuggen, sondern auch andere Prozesse wie z. B. [WebJobs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter [Debuggen in Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx).

Übersicht über ProtokolleÜbersicht über Diagnoseprotokolle
----------------------------------------------------------

ASP.NET-Anwendungen in Azure-Websites können die folgenden Arten von Protokollen generieren:

-   **Ablaufverfolgungsprotokolle**
     Anwendungen erzeugen diese Protokolle, indem sie Methoden der Klasse [System.Diagnostics.Trace](http://msdn.microsoft.com/en-us/library/system.diagnostics.trace.aspx) aufrufen.
-   **Webserver-Protokolle**
     Der Webserver erstellt einen Protokolleintrag für jede HTTP-Anforderung an die Site.
-   **Detaillierte Fehlerprotokolle**
     Der Webserver erstellt eine HTML-Seite mit zusätzlichen Informationen für fehlgeschlagene HTTP-Anforderungen (Anforderungen mit einem Statuscode 400 oder höher).
-   **Ablaufverfolgungs-Protokolle für fehlgeschlagene Anforderungen**
     Der Webserver erstellt eine XML-Datei mit detaillierten Ablaufverfolgungsinformationen für fehlgeschlagene HTTP-Anforderungen. Der Webserver liefert außerdem eine XSL-Datei zur Formatierung der XML-Datei in einem Browser.

Protokollierung kann die Leistung von Websites beeinträchtigen. Daher können Sie die verschiedenen Protokolltypen unter Azure bei Bedarf einzeln aktivieren und deaktivieren. Für Anwendungsprotokolle können Sie angeben, dass nur Protokolleinträge oberhalb eines bestimmten Schweregrads geschrieben werden sollen. Bei der Erstellung neuer Websites ist sämtliche Protokollierung standardmäßig deaktiviert.

Die Protokolle werden in den Ordner *LogFiles* im Dateisystem Ihrer Website geschrieben. Webserver- und Anwendungsprotokolle können auch in ein Azure-Speicherkonto geschrieben werden. Speicherkonten bieten mehr Kapazität für Protokolle als das Dateisystem. Protokolle im Dateisystem sind beschränkt auf 100 Megabyte. (Protokolle im Dateisystem werden nur für kurze Zeit aufbewahrt. Azure löscht alte Protokolldateien, um Platz für neue Dateien zu machen, wenn das Limit erreicht ist.)

AnwendungsprotokolleErstellen und Anzeigen von Anwendungs-Ablaufprotokollen
---------------------------------------------------------------------------

In diesem Abschnitt werden Sie die folgenden Aufgaben ausführen:

-   Hinzufügen von Ablaufverfolgungs-Anweisungen im Webprojekt, das Sie [im vorigen Lernprogramm](/en-us/develop/net/tutorials/get-started/) erstellt haben.
-   Anzeigen der Protokolle, wenn Sie das Projekt lokal ausführen.
-   Anzeigen der Protokolle, während diese von der Anwendung unter Azure generiert werden.

### Hinzufügen von Ablaufverfolgungs-Anweisungen zur Anwendung

1.  Öffnen Sie *Controllers\\HomeController.cs* und ersetzen Sie den Inhalt der Datei durch den folgenden Code, um `Ablaufverfolgungs`-Anweisungen und eine `using`-Anweisung für `System.Diagnostics` hinzuzufügen:

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
                     Trace.WriteLine("Beginn Index-Methode");
                     ViewBag.Message = "Ändern Sie dieses Template, um Ihre ASP.NET MVC-Anwendung zu starten.";
                     Trace.TraceInformation("Anzeige der Indexseite um " + DateTime.Now.ToLongTimeString());
                     Trace.WriteLine("Ende Index-Methode");
                     return View();
                 }
            
                 public ActionResult About()
                 {
                     Trace.WriteLine("Anfang About-Methode");
                     ViewBag.Message = "Die Beschreibungsseite Ihrer App.";
                     Trace.TraceWarning("Vorübergehender Fehler auf der About-Seite um " + DateTime.Now.ToShortTimeString());
                     Trace.WriteLine("Ende About-Methode");
                     return View();
                 }
            
                 public ActionResult Contact()
                 {
                     Trace.WriteLine("Anfang Contact-Methode");
                     ViewBag.Message = "Ihre Kontaktseite.";
                     Trace.TraceError("Schwerwiegender Fehler auf der Kontaktseite um " + DateTime.Now.ToLongTimeString());
                     Trace.WriteLine("Ende Contact-Methode");
                     return View();
                 }
             }
         }

### Lokale Anzeige der Ablaufverfolgungs-Ausgabe

1.  Drücken Sie F5, um die Anwendung im Debugmodus auszuführen.

    Der Standard-Ablaufverfolgungs-Listener schreibt sämtliche Ausgaben in das **Ausgabefenster**, zusammen mit anderen Debugausgaben. Die folgende Abbildung zeigt die Ausgaben der Ablaufverfolgungs-Anweisungen, die Sie zur `Index`-Methode hinzugefügt haben.

    ![Ablaufverfolgung im Debugfenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Anhand der folgenden Schritte können Sie Ablaufverfolgungs-Ausgaben in einer Webseite anzeigen, ohne im Debugmodus zu kompilieren.

2.  Öffnen Sie die Web.config-Datei der Anwendung (die Datei im Projektordner) und fügen Sie das Element `<system.diagnostics>` am Ende der Datei direkt vor dem abschließenden `</configuration>`-Element ein:

		<system.diagnostics\>
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

    Mit dem `WebPageTraceListener` können Sie die Ablaufverfolgungsausgabe unter `/trace.axd` anzeigen.

1.  Fügen Sie ein [trace](http://msdn.microsoft.com/en-us/library/vstudio/6915t83k(v=vs.100).aspx)-Element unter `<system.web>` in der Web.config-Datei ein, wie im folgenden Beispiel gezeigt:

         <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

2.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

3.  Fügen Sie *trace.axd* zu der URL in der Adressleiste hinzu und drücken Sie die Eingabetaste (die URL sollte in etwa so aussehen: http://localhost:53370/trace.axd).

4.  Klicken Sie in der Seite **Anwendungs-Ablaufverfolgung** auf **Details anzeigen**.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Daraufhin wird die Seite **Anforderungsdetails** angezeigt, und im Bereich **Überwachungsinformationen** sehen Sie die Ausgabe der Ablaufverfolgungs-Anweisungen, die Sie zur `Index`-Methode hinzugefügt haben.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    `trace.axd` ist standardmäßig nur lokal verfügbar. Sie können `localOnly="false"` zum `trace`-Element der *Web.config*-Datei hinzufügen, um diese Seite auch remote verfügbar zu machen, wie im folgenden Beispiel gezeigt:

         <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    `trace.axd` sollte jedoch in Produktionssites aus Sicherheitsgründen deaktiviert werden. In den folgenden Abschnitten lernen Sie einen einfacheren Weg kennen, um Ablaufverfolgungsprotokolle von Azure-Websites zu lesen.

### Anzeige der Ablaufverfolgungs-Ausgabe in Azure

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt und anschließend auf **Veröffentlichen**.

2.  Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.

    Nachdem Visual Studio Ihr Update veröffentlicht hat, öffnet sich ein Browserfenster zu Ihrer Startseite (sofern sie **Ziel-URL** in der Registerkarte **Verbindung** nicht gelöscht haben).

3.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf Ihre Website und wählen Sie **View Streaming Logs in Output Window** (Streamingprotokolle im Ausgabefenster anzeigen, in englischer Sprache).

    ![Anzeigen der Streamingprotokolle im Kontextmenü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    Das **Ausgabefenster** zeigt an, dass Sie mit dem Protokollstreamingdienst verbunden sind und fügt eine Benachrichtigungszeile für jede Minute hinzu, in der kein anzuzeigendes Protokoll eingeht.

    ![Anzeigen der Streamingprotokolle im Kontextmenü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4.  Klicken Sie im Browserfenster, in dem die Startseite Ihrer Anwendung geöffnet ist, auf **Contact**.

    Innerhalb weniger Sekunden erscheint das Ablaufverfolgungsprotokoll mit dem Schweregrad Fehler, das Sie zur `Contact`-Methode hinzugefügt haben, im **Ausgabefenster**.

    ![Fehler-Ablaufverfolgung im Ausgabefenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio zeigt standardmäßig nur Ablaufverfolgungsprotokolle mit dem Schweregrad Fehler an, wenn der Protokollüberwachungsdienst gestartet wird. Bei der Erstellung einer neuen Azure-Website ist sämtliche Protokollierung standardmäßig deaktiviert, wie Sie zuvor in den Siteeinstellungen gesehen haben:

    ![Anwendungsprotokollierung deaktiviert](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Als Sie jedoch **Streamingprotokolle im Ausgabefenster anzeigen** ausgewählt haben, hat Visual Studio **Anwendungsprotokollierung(Dateisystem)** automatisch auf **Fehler** eingestellt, und Protokolle mit dem Schweregrad Fehler werden gemeldet. Sie können diese Einstellung auf **Ausführlich** ändern, falls Sie sämtliche Ablaufverfolgungs-Protokolle sehen möchten. Wenn Sie einen Schweregrad unterhalb von Fehler auswählen, werden die Protokolleinträge der höheren Schweregrade ebenfalls geschrieben. Wenn Sie also ausführlich auswählen, werden die Protokolleinträge für Information, Warnung und Fehler geschrieben.

5.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Website und klicken Sie anschließend wie zuvor auf **Anzeigeeinstellungen**.

6.  Ändern Sie **Anwendungsprotokollierung (Dateisystem)** zu **Ausführlich** und klicken Sie auf **Speichern**.

    ![Einstellen des Ablaufverfolgungs-Schweregrads auf Ausführlich](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

7.  Klicken Sie im Browserfenster, in dem Ihre **Contact**-Seite angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

    Innerhalb weniger Sekunden wird im **Ausgabefenster** Ihre gesamte Ablaufverfolgungsausgabe angezeigt.

    ![Ablaufverfolgungs-Schweregrad Ausführlich](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    In diesem Abschnitt haben Sie die Protokollierung mithilfe der Azure-Websiteeinstellungen aktiviert bzw. deaktiviert. Sie können die Ablaufverfolgungs-Listener auch über die Datei Web.config aktivieren bzw. deaktivieren. Wenn Sie die Datei Web.config ändern, wird jedoch die Anwendungsdomäne neu gestartet, was bei der Aktivierung der Protokollierung über die Website nicht der Fall ist. Falls das Problem schwer zu reproduzieren ist oder nur zeitweilig auftritt, kann es passieren, dass das Problem beim Neustart der Domäne verschwindet und Sie warten müssen, bis es erneut auftritt. Beim Aktivieren der Diagnose in Azure ist dies nicht der Fall, und Sie können sofort mit der Erfassung der Fehlerinformationen beginnen.

### Funktionen des Ausgabefensters

Die Registerkarte **Azure-Protokolle** im **Ausgabefenster** enthält verschiedene Schaltflächen und ein Textfeld:

![Schaltflächen auf der Registerkarte Protokolle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Diese Schaltflächen bieten die folgenden Funktionen:

-   Löschen des **Ausgabefensters**.
-   Aktivieren bzw. deaktivieren des Zeilenumbruchs.
-   Starten bzw. Stoppen der Protokollüberwachung.
-   Angeben, welche Protokolle überwacht werden sollen.
-   Herunterladen von Protokollen.
-   Filtern von Protokollen anhand von Suchzeichenfolgen oder regulären Ausdrücken.
-   Schließen des **Ausgabefensters**.

Wenn Sie eine Suchzeichenfolge oder einen regulären Ausdruck eingeben, filtert Visual Studio die Protokollinformationen clientseitig. Daher können Sie die Kriterien eingeben, nachdem die Protokolle im **Ausgabefenster** angezeigt wurden, und Sie können die Filterkriterien ändern, ohne die Protokolle neu generieren zu müssen.

WebserverprotokolleAnzeigen von Webserverprotokollen
----------------------------------------------------

Webserverprotokolle zeichnen sämtliche HTTP-Aktivitäten der Website auf Sie müssen diese Protokolle für die Website aktivieren und Visual Studio mitteilen, dass Sie diese überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können

1.  Ändern Sie in der Registerkarte **Azure-Websitekonfiguration**, die Sie im **Server-Explorer** geöffnet haben, die Variable Webserverprotokollierung auf **Ein** und klicken Sie auf **Speichern**.

    ![Aktivieren der Webserverprotokollierung](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2.  Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Angeben, welche Azure-Protokolle überwacht werden sollen**.

    ![Angeben, welche Azure-Protokolle überwacht werden sollen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3.  Wählen Sie im Dialogfeld **Azure-Protokollierungsoptionen** die Option **Webserverprotokolle** aus und klicken Sie auf **OK**.

    ![Überwachen von Webserverprotokollen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4.  Klicken Sie im Browserfenster, in dem Ihre Website angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

    Normalerweise werden die Anwendungsprotokolle zuerst angezeigt, gefolgt von den Webserverprotokollen. Möglicherweise müssen Sie kurz warten, bis die Protokolle angezeigt werden.

    ![Webserverprotokolle im Ausgabefenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Bei der ersten Aktivierung der Webserverprotokolle in Visual Studio schreibt Azure die Protokolle standardmäßig in das Dateisystem. Alternativ können Sie im Verwaltungsportal angeben, dass die Webserverprotokolle in einen Blob-Container in einem Speicherkonto geschrieben werden sollen. Weitere Informationen finden Sie im Abschnitt **Diagnose** unter [Konfigurieren von Websites](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Wenn Sie die Webserverprotokollierung für ein Azure-Speicherkonto im Verwaltungsportal aktivieren und die Protokollierung anschließend in Visual Studio deaktivieren, werden die Speicherkontoeinstellungen bei der nächsten Aktivierung wiederhergestellt.

FehlerprotokolleAnzeigen detaillierter Fehlermeldungsprotokolle
---------------------------------------------------------------

Die detaillierten Fehlerprotokolle liefern zusätzliche Informationen über HTTP-Anforderungen, die zu einer Fehlerantwort geführt haben (400 oder höher). Sie müssen diese Protokolle für die Website aktivieren und Visual Studio mitteilen, dass Sie diese überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können

1.  Ändern Sie in der Registerkarte **Azure-Websitekonfiguration**, die Sie im **Server-Explorer** geöffnet haben, die Variable **Detaillierte Fehlermeldungen** auf **Ein** und klicken Sie auf **Speichern**.

    ![Aktivieren der detaillierten Fehlermeldungen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2.  Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Angeben, welche Azure-Protokolle überwacht werden sollen**.

3.  Klicken Sie im Dialogfeld **Azure-Protokollierungsoptionen** die Option **Alle Protokolle** aus und klicken Sie auf **OK**.

    ![Überwachen aller Protokolle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4.  Fügen Sie in der Adressleiste Ihres Browserfensters ein zusätzliches Zeichen an die URL an, um einen 404-Fehler zu provozieren (z. B. `http://localhost:53370/Home/Contactx`) und drücken Sie die Eingabetaste.

    Nach wenigen Sekunden erscheint das detaillierte Fehlerprotokoll im **Ausgabefenster** von Visual Studio.

    ![Detailliertes Fehlerprotokoll im Ausgabefenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Klicken Sie bei gedrückter STRG-Taste auf den Link, um das formatierte Protokoll im Browser anzuzeigen:

    ![Detailliertes Fehlerprotokoll im Browserfenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

Herunterladen von ProtokollenHerunterladen von Protokollen aus dem Dateisystem
------------------------------------------------------------------------------

Alle Protokolle, die Sie im **Ausgabefenster** überwachen können, lassen sich auch als *.zip*-Datei herunterladen.

1.  Klicken Sie im **Ausgabefenster** auf **Streamingprotokolle herunterladen**.

    ![Schaltflächen auf der Registerkarte Protokolle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Der Datei-Explorer öffnet Ihren *Downloads*-Ordner, und die heruntergeladene Datei ist ausgewählt.

    ![Heruntergeladene Datei](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2.  Wenn Sie die *.zip*-Datei extrahieren, sehen Sie die folgende Ordnerstruktur:

    ![Heruntergeladene Datei](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

    -   Ablaufverfolgungsprotokolle von Anwendungen befinden sich in *.txt*-Dateien im Ordner *LogFiles\\Application*.
    -   Webserverprotokolle befinden sich in *.log*-Dateien im Ordner *LogFiles\\http\\RawLogs*. Sie können diese Dateien mit Werkzeugen wie z. B. [Log Parser](http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659) anzeigen und bearbeiten.
    -   Detaillierte Fehlerprotokolle befinden sich in *.html*-Dateien im Ordner *LogFiles\\DetailedErrors*.

    (Der Ordner *deployments* enthält Dateien der Quellcodeverwaltung und hat nichts mit der Veröffentlichung in Visual Studio zu tun. Der Ordner *Git* enthält Ablaufverfolgungsprotokolle für die Quellcodeverwaltung und den Protokollstreamingdienst.)

SpeicherprotokolleAnzeigen von Speicherprotokollen
--------------------------------------------------

Ablaufverfolgungsprotokolle von Anwendungen können auch an Azure-Speicherkonten geschickt und anschließend in Visual Studio angezeigt werden. Dazu müssen Sie ein Speicherkonto erstellen, Speicherprotokolle im Verwaltungsportal aktivieren und können diese anschließend in der Registerkarte **Protokolle** im Fenster **Azure-Website** anzeigen.

Sie können die Protokolle an eines oder alle dieser drei Ziele schicken:

-   Das Dateisystem.
-   Speicherkonto-Tabellen.
-   Speicherkonto-Blobs.

Sie können unterschiedliche Schweregrade für die einzelnen Ziele angeben.

Tabellen erleichtern die Anzeige von Protokolldetails online und unterstützen die Streamingfunktion: Sie können Protokolle aus Tabellen anzeigen und sehen neue Protokolleinträge, sobald diese erstellt werden. Blobs erleichtern den Download der Protokolle in Dateiform und deren Analyse mithilfe von HDInsight, da HDInsight mit dem Blobformat umgehen kann. Weitere Informationen finden Sie im Abschnitt **Hadoop und MapReduce** unter [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options) (Datenspeicheroptionen (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache).

Der Schweregrad Ihrer Dateisystemprotokolle ist momentan auf Ausführlich eingestellt. In den folgenden Schritten lernen Sie, wie Sie Protokolle mit dem Schweregrad Information in Speicherkontotabellen leiten können. Der Schweregrad Information bedeutet, dass alle Protokolle aus Aufrufen von `Trace.TraceInformation`, `Trace.TraceWarning` und `Trace.TraceError` angezeigt werden, nicht jedoch die Protokolle aus Aufrufen von `Trace.WriteLine`.

Speicherkonten bieten mehr Speicherplatz und längere Aufbewahrungszeiten für Protokolle als das Dateisystem. Außerdem erhalten Sie bei der Aufbewahrung von Ablaufverfolgungsprotokollen in Speicherkonten zusätzliche Informationen, die bei Dateisystemprotokollen entfallen.

1.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Website und klicken Sie anschließend auf **Im Verwaltungsportal öffnen**.

2.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Datenspeicher** und anschließend auf **Speicherkonto erstellen**.

    ![Speicherkonto erstellen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png)

3.  Geben Sie die eindeutige URL des Speicherkontos ein und klicken Sie auf **Speicherkonto erstellen**.

    ![Geben Sie eine URL ein](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png)

4.  Klicken Sie in Visual Studio im Fenster **Azure-Website** auf die Registerkarte **Protokolle** und anschließend auf **Protokollierung konfigurieren**.

    ![Heruntergeladene Datei](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    Daraufhin öffnet sich im Verwaltungsportal die Registerkarte **Konfigurieren** für Ihre Website. Sie können diese Registerkarte auch öffnen, indem Sie auf die Registerkarte **Websites** und anschließend auf Ihre Website und auf die Registerkarte **Konfigurieren** klicken.

5.  Blättern Sie in der Registerkarte **Konfigurieren** im Verwaltungsportal nach unten bis zum Bereich Anwendungsdiagnose und ändern Sie den Wert von **Anwendungsprotokollierung (Tabellenspeicher)** auf **Ein**.

6.  Ändern Sie **Protokollierungsstufe** auf **Information**.

7.  Klicken Sie auf **Tabellenspeicher verwalten**.

    ![Klicken Sie auf Tabellenspeicher verwalten](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    Im Feld **Tabellenspeicher für Anwendungsdiagnose verwalten** können Sie Ihr Speicherkonto auswählen, falls Sie mehr als eines verwenden. Sie können eine neue Tabelle erstellen oder eine existierende Tabelle verwenden.

    ![Tabellenspeicher verwalten](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

8.  Setzen Sie das Häkchen im Feld **Tabellenspeicher für Anwendungsdiagnose verwalten**, um das Fenster zu schließen.

9.  Klicken Sie in der Registerkarte **Konfigurieren** im Verwaltungsportal auf **Speichern**.

10. Klicken Sie im Browserfenster, in dem Ihre Anwendungswebsite angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

    Die von diesen Webseiten produzierten Protokollierungsinformationen werden daraufhin in Ihr Speicherkonto geschrieben.

11. Klicken Sie in der Registerkarte **Protokolle** im Fenster **Azure-Website** in Visual Studio unter **Diagnosezusammenfassung** auf **Aktualisieren**.

    ![Klicken Sie auf Aktualisieren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

    Im Bereich **Diagnosezusammenfassung** werden standardmäßig die Protokolle der vergangenen 15 Minuten angezeigt. Sie können das Zeitintervall ändern, um weitere Protokolleinträge anzuzeigen.

    (Falls Sie den Fehler "Tabelle nicht gefunden" erhalten, vergewissern Sie sich, dass Sie die entsprechenden Seiten aufgerufen haben, nachdem Sie **Anwendungsprotokollierung (Tabellenspeicher)** aktiviert und auf **Speichern** geklickt haben.)

    ![Speicherprotokolle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

    In dieser Ansicht werden **Prozess-ID** und **Thread-ID** für die einzelnen Protokolle angezeigt. Diese Informationen sind in den Dateisystemprotokollen nicht enthalten. Sie können zusätzliche Felder anzeigen, indem Sie die Azure-Speichertabelle direkt öffnen.

12. Klicken Sie auf **Alle Anwendungsprotokolle anzeigen**.

    Daraufhin wird die Ablaufprotokolltabelle in der Azure-Speichertabellenansicht angezeigt.

    (Falls Sie den Fehler "Sequenz enthält keine Elemente" erhalten, öffnen Sie den **Server-Explorer**, erweitern Sie den Knoten Ihres Speicherkontos unterhalb des **Azure**-Knotens, klicken Sie mit der rechten Maustaste auf **Tabellen** und klicken Sie auf **Aktualisieren**.)

    ![Ablaufverfolgungstabelle im Server-Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

    ![Speicherprotokolle in der Tabellenansicht](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

    Diese Ansicht enthält zusätzliche Felder, die in keiner der anderen Ansichten angezeigt werden. Außerdem können Sie die Protokolle in dieser Ansicht mithilfe der Abfrage-Generator-GUI filtern. Weitere Informationen finden Sie im Abschnitt "Working with Table Resources - Filtering Entities" (Arbeiten mit Tabellenressourcen - Entitätenfilter" unter [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx) (Durchsuchen von Speicherressourcen im Server-Explorer, in englischer Sprache).

13. Klicken Sie mit der rechten Maustaste auf eine der Zeilen und klicken Sie anschließend auf **Bearbeiten**, um die Details für eine einzelne Zeile anzuzeigen.

    ![Ablaufverfolgungstabelle im Server-Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

Protokolle für fehlgeschlagene AnforderungenAnzeige von Protokollen für fehlgeschlagene Anforderungen
-----------------------------------------------------------------------------------------------------

Anhand der Protokolle für fehlgeschlagene Anforderungen können Sie im Detail herausfinden, wie IIS HTTP-Anforderungen bearbeitet, z. B. in Szenarien mit URL-Neuschreibung oder bei Authentifizierungsproblemen.

Azure-Websites verwenden die gleiche Funktion zum Verfolgen fehlgeschlagener Anforderungen, die in IIS 7.0 und höher verfügbar ist. Sie haben jedoch keinen Zugriff auf die IIS-Einstellungen, in denen festgelegt wird, welche Fehler protokolliert werden. Wenn Sie die Verfolgung fehlgeschlagener Anforderungen aktivieren, werden alle Fehler erfasst.

Sie können die Protokolle für fehlgeschlagene Anforderungen in Visual Studio aktivieren, allerdings lassen sich diese Protokolle nicht in Visual Studio anzeigen. Diese Protokolle liegen in Form von XML-Dateien vor. Der Streamingprotokolldienst überwacht nur Dateien, die im Nur-Text-Modus lesbar sind: *.txt-*, *.html-* und *.log-*Dateien.

Sie können die Protokolle für fehlgeschlagene Anforderungen entweder direkt über FTP im Browser anzeigen oder mit einem FTP-Client auf Ihren lokalen Computer herunterladen. In diesem Abschnitt werden Sie die Protokolle direkt im Browser anzeigen.

1.  Ändern Sie in der Registerkarte **Konfiguration** im Fenster **Azure-Website**, das Sie aus dem **Server-Explorer** heraus geöffnet haben, den Wert von **Verfolgung fehlgeschlagener Anforderungen** zu **Ein** und klicken Sie auf **Speichern**.

    ![Aktivieren der Verfolgung fehlgeschlagener Anforderungen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

2.  Fügen Sie ein zusätzliches Zeichen zu der URL Ihrer Website in der Adressleiste hinzu und drücken Sie die Eingabetaste, um einen 404-Fehler zu provozieren.

    Daraufhin wird ein Protokoll für die fehlgeschlagene Anforderung erstellt. In den folgenden Schritten lernen Sie, wie Sie dieses Protokoll anzeigen oder herunterladen können.

3.  Klicken Sie in Visual Studio in der Registerkarte **Konfiguration** im Fenster **Azure-Website** auf **Im Verwaltungsportal öffnen**.

4.  Klicken Sie im Verwaltungsportal auf **Dashboard** und anschließend auf **Zurücksetzen der Bereitstellungsanmeldeinformationen** im Bereich **Schnellansicht**.

    ![Zurücksetzen der FTP-Anmeldeinformationen im Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

5.  Geben Sie einen neuen Benutzernamen und ein neues Passwort ein.

    ![Neuer FTP-Benutzername und neues Passwort](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

6.  Drücken Sie im **Dashboard** im Verwaltungsportal auf F5, um die Seite zu aktualisieren, und blättern Sie nach unten zum Bereich **Bereitstellung / FTP-Benutzer**. Beachten Sie, dass der Name der Site dem Benutzernamen vorangestellt ist. **Bei der Anmeldung müssen Sie diesen kompletten Benutzernamen inklusive Name der Site verwenden, so wie hier angezeigt.**

7.  Öffnen Sie in einem neuen Browserfenster die URL, die unter **FTP-Hostname** in der Registerkarte **Dashboard** des Verwaltungsportals für Ihre Website angezeigt wird. **FTP-Hostname** befindet sich in der Nähe von **Bereitstellung / FTP-Benutzer** im Bereich **Schnellübersicht**.

8.  Melden Sie sich mit den zuvor erstellten FTP-Anmeldeinformationen an (inklusive vorangestelltem Namen der Site vor dem Benutzernamen).

    Daraufhin wird im Browser der Stammordner der Site angezeigt.

9.  Öffnen Sie den Ordner *LogFiles*.

    ![Öffnen Sie den Ordner LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

10. Öffnen Sie den Ordner mit dem Namen W3SVC plus nachgestelltem numerischem Wert.

    ![Öffnen Sie den Ordner W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Der Ordner enthält XML-Dateien für sämtliche protokollierten Fehler, nachdem Sie die Protokollierung fehlgeschlagener Anforderungen aktiviert haben, sowie eine XSL-Datei für die XML-Formatierung im Browser.

    ![Ordner W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

11. Klicken Sie auf die XML-Datei für die fehlgeschlagene Anforderung, deren Ablaufverfolgungsinformationen Sie anzeigen möchten.

    Die folgende Abbildung zeigt einen Teil der Ablaufverfolgungsinformationen für einen Beispielfehler.

    ![Verfolgung fehlgeschlagener Anforderungen im Browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

Nächste SchritteNächste Schritte
--------------------------------

Sie haben gelernt, wie Visual Studio die Anzeige der Protokolle von Azure-Websites erleichtert. Möglicherweise möchten Sie mehr über die Problembehandlung in Azure-Websites, die Ablaufverfolgung in ASP.NET-Anwendungen und die Analyse von Webserverprotokollen erfahren.

### Problembehandlung in Azure-Websites

Weitere Informationen zur Problembehandlung in Azure-Websites finden Sie in den folgenden Ressourcen:

-   [Problembehandlung in Azure](/en-us/develop/net/best-practices/troubleshooting/)
     Ein White Paper mit einer kurzen Einführung zu Azure-Websites.
-   [Problembehandlung in Websites](/en-us/develop/net/best-practices/troubleshooting-web-sites/)
     Eine Einleitung mit Fokus auf Azure-Websites.
-   [Aktivieren der Diagnoseprotokollierung für Azure-Websites](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
     Behandelt im Wesentlichen dieselben Informationen wie dieses Lernprogramm, konzentriert sich jedoch auf das Abrufen der Diagnoseprotokolle ohne Visual Studio.
-   [Überwachung von Websites](/en-us/manage/services/web-sites/how-to-monitor-websites/)
     Der Abschnitt [Konfigurieren von Diagnose und Herunterladen von Protokollen](/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics) enthält wichtige Informationen, die nicht in den Dokumenten zu Problembehandlung enthalten sind.

Falls Sie spezifische Fragen zur Problembehandlung haben, können Sie diese in einem der folgenden Foren stellen:

-   [Das Azure-Forum auf der ASP.NET-Website](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
-   [Das Azure-Forum auf MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
-   [StackOverflow.com](http://www.stackoverflow.com).

### Debuggen in Visual Studio

Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter dem MSDN-Thema [Debuggen in Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx) und unter [Debugging Tips with Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx) (Tipps zum Debuggen in Visual Studio 2010, in englischer Sprache).

Falls Ihre Website eine Azure-Web-API oder ein Mobile Services-Back-End verwendet und Sie diese Komponenten debuggen möchten, finden Sie weitere Informationen unter [Debugging .NET Backend in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx) (Debuggen von .NET-Back-Ends in Visual Studio, in englischer Sprache).

### Ablaufverfolgung in ASP.NET-Anwendungen

Momentan sind keine vollständigen und aktuellen Einführungen zur Ablaufverfolgung in ASP.NET im Internet verfügbar. Beginnen Sie daher mit den älteren Einführungen für Web Forms, als MVC noch nicht existierte, und ergänzen Sie diese Lektüre mit neueren Blogeinträgen zu speziellen Themen. Die folgenden Ressourcen bieten gute Einstiegspunkte:

-   [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry) (Überwachung und Telemetrie (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache).
	<br/>Dieses E-Book-Kapitel enthält Empfehlungen zur Ablaufverfolgung in Azure-Cloudanwendungen.
-   [ASP.NET Tracing](http://msdn.microsoft.com/en-us/library/ms972204.aspx) (Ablaufverfolgung in ASP.NET, in englischer Sprache).
	<br/>Ein älterer Artikel, der sich jedoch immer noch gut als Einstieg in das Thema eignet.
-   [Trace Listeners](http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx) (Ablaufverfolgungs-Listener, in englischer Sprache)
     <br/>Informationen über Ablaufverfolgungs-Listener, allerdings wird der [WebPageTraceListener](http://msdn.microsoft.com/en-us/library/system.web.webpagetracelistener.aspx) nicht behandelt.
-   [Exemplarische Vorgehensweise: Integrieren der ASP.NET-Ablaufverfolgung und der System.Diagnostics-Ablaufverfolgung](http://msdn.microsoft.com/en-us/library/b0ectfxd.aspx)
     <br/>Dieser Artikel ist ebenfalls älteren Datums, enthält jedoch zusätzliche Informationen, die in der Einführung nicht behandelt werden.
-   [Tracing in ASP.NET MVC Razor Views](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx) (Ablaufverfolgung in den ASP.NET MVC-Razoransichten, in englischer Sprache)
	<br/>Dieser Blogeintrag behandelt neben der Ablaufverfolgung in Razoransichten auch die Erstellung von Fehlerfiltern zur Protokollierung aller Ausnahmefehler in MVC-Anwendungen. Informationen zur Protokollierung aller Ausnahmefehler in Web Forms-Anwendungen finden Sie im Global.asax-Beispiel unter [Vollständiges Beispiel für Fehlerhandler](http://msdn.microsoft.com/en-us/library/bb397417.aspx) auf MSDN. Falls Sie in MVC oder Web Forms bestimmte Ausnahmen protokollieren möchten, diese Ausnahmen jedoch vom Standard-Framework behandelt werden sollen, können Sie diese wie im folgenden Beispiel abfangen und erneut auslösen:

          try
          {
             // Ihr Code, der möglicherweise eine Ausnahme verursacht.
          }
          catch (Exception ex)
          {
              Trace.TraceError("Ausnahme: " + ex.ToString());
              throw;
          } 

-   [Ablaufprotokollierung und Streamingdiagnose mit der Azure-Befehlszeile (plus Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)
     <br/>Verwenden der Befehlszeile für die Visual Studio-Aktionen aus diesem Lernprogramm. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) ist ein Tool zum Debuggen von ASP.NET-Anwendungen.
-   [Using Azure Web Site Logging and Diagnostics - with David Ebbo](http://www.windowsazure.com/en-us/documentation/videos/azure-web-site-logging-and-diagnostics/)
	(Websiteprotokollierung und Diagnose unter Azure mit David Ebbo, in englischer Sprache) und [Streaming Logs from Azure Web Sites - with David Ebbo](http://www.windowsazure.com/en-us/documentation/videos/log-streaming-with-azure-web-sites/)
     (Protokollstreaming aus Azure-Websites mit David Ebbo, in englischer Sprache): Videos von Scott Hanselman und David Ebbo.

Für die Fehlerprotokollierung können Sie Open Source-Protokollframeworks wie z. B. [ELMAH](http://nuget.org/packages/elmah/) verwenden, anstatt Ihren eigenen Ablaufverfolgungscode zu schreiben. Weitere Informationen finden Sie unter [Scott Hanselman's blog posts about ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx) (Blogeinträge von Scott Hanselmann zu ELMAH, in englischer Sprache).

Sie müssen nicht unbedingt die Ablaufverfolgung von ASP.NET oder System.Diagnostics verwenden, um Streamingprotokolle aus Azure zu erhalten. Der Streamingprotokolldienst von Azure-Websites ist in der Lage, beliebige *.txt-*, *.html-* oder *.log-*-Dateien aus dem Ordner *LogFiles* zu streamen. Sie können also Ihr eigenes Protokollsystem erstellen und in das Dateisystem der Website schreiben, und Ihre Dateien werden automatisch gestreamt und heruntergeladen. Dazu müssen Sie nur den Anwendungscode schreiben, der Dateien im Ordner *d:\\home\\logfiles* erstellt.

### Analyse von Webserverprotokollen

Weitere Informationen zur Analyse von Webserverprotokollen finden Sie in den folgenden Ressourcen:

-   [LogParser](http://www.microsoft.com/en-us/download/details.aspx?id=24659)
     Ein Tool zum Anzeigen von Daten in Webserverprotokollen (*.log*-Dateien).
-   [Troubleshooting IIS Performance Issues or Application Errors using LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser) (Problembehandlung von IIS-Leistungsproblemen oder Anwendungsfehlern mithilfe von LogParser, in englischer Sprache)
	<br/>Eine Einführung in das LogParser-Tool, das Sie bei der Analyse von Webserverprotokollen unterstützt.
-   [Blogeinträge von Robert McMurray zum Thema LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)
-   [HTTP-Statuscodes in IIS 7.0, IIS 7.5 und IIS 8.0](http://support.microsoft.com/kb/943891)

### Analyse der Ablaufverfolgung fehlgeschlagener Anforderungen

Die Microsoft TechNet-Website enthält einen Abschnitt zum Thema [Using Failed Request Tracing](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) (Ablaufverfolgung fehlgeschlagener Anforderungen, in englischer Sprache), der für das Verständnis dieser Protokolle hilfreich ist. Diese Dokumentation konzentriert sich jedoch hauptsächlich auf die Ablaufverfolgung fehlgeschlagener Anforderungen in IIS. Diese Option ist für Azure-Websites nicht verfügbar.

### Debuggen von Cloud-Diensten.

Informationen zum Debuggen von Azure Cloud Service im Gegensatz zu Websites finden Sie unter [Debugging Cloud Services](http://msdnstage.redmond.corp.microsoft.com/en-us/library/windowsazure/ee405479.aspx) (Debuggen von Cloud-Diensten, in englischer Sprache).



[GetStarted]: /en-us/develop/net/tutorials/get-started/