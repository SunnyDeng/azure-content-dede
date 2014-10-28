<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Troubleshooting Azure Websites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Website by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra"></tags>

# Problembehandlung von Azure-Websites in Visual Studio

Bei Entwicklung und Tests von Webanwendungen können Sie für die Problembehandlung entweder [den Debugmodus][den Debugmodus] oder [IntelliTrace][IntelliTrace] verwenden. Sie können den Debugmodus entweder lokal in IIS Express oder remote in einer Azure-Website ausführen. Für Fehler, die nur in Produktion auftreten, können Sie hingegen die Protokolle betrachten, die vom Anwendungscode oder vom Webserver geschrieben werden. In diesem Lernprogramm lernen Sie die Visual Studio-Tools zum Debuggen von Anwendungen kennen, während diese in einer Azure-Website ausgeführt werden. Sie führen die Anwendung entweder remote im Debugmodus aus oder arbeiten mit Anwendungs- und Webserverprotokollen.

Sie lernen Folgendes:

-   Welche Azure-Verwaltungsfunktionen sind in Visual Studio verfügbar?
-   Verwenden der Remoteansicht in Visual Studio für schnelle Änderungen an Remotewebsites
-   Ausführen des Debugmodus im Remotebetrieb, während ein Projekt in einer Azure-Website ausgeführt wird
-   Erstellen von Anwendungs-Ablaufprotokollen und Live-Anzeige dieser Protokolle.
-   Anzeige von Webserverprotokollen inklusive detaillierter Fehlermeldungen und Verfolgung fehlgeschlagener Anforderungen.
-   Senden von Diagnoseprotokollen an ein Azure-Speicherkonto und Anzeige der Protokolle.

### Teile des Lernprogramms

1.  [Voraussetzungen][Voraussetzungen]
2.  [Konfigurieren und Verwalten von Websites][Konfigurieren und Verwalten von Websites]
3.  [Remoteansicht][Remoteansicht]
4.  [Remotedebuggen][Remotedebuggen]
5.  [Übersicht über Diagnoseprotokolle][Übersicht über Diagnoseprotokolle]
6.  [Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen][Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen]
7.  [Erstellen von Webserverprotokollen][Erstellen von Webserverprotokollen]
8.  [Anzeigen detaillierter Fehlermeldungsprotokolle][Anzeigen detaillierter Fehlermeldungsprotokolle]
9.  [Herunterladen von Dateisystemprotokollen][Herunterladen von Dateisystemprotokollen]
10. [Anzeigen von Speicherprotokollen][Anzeigen von Speicherprotokollen]
11. [Anzeigen von Protokollen für fehlgeschlagene Anforderungen][Anzeigen von Protokollen für fehlgeschlagene Anforderungen]
12. [Nächste Schritte][Nächste Schritte]

## <a name="prerequisites"></a>Voraussetzungen

Dieses Lernprogramm verwendet die Entwicklungsumgebung, das Webprojekt und die Azure-Website, die Sie unter [Erste Schritte mit Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET] eingerichtet haben. Die Codebeispiele in diesem Lernprogramm stammen aus einer C# MVC-Webanwendung. Die Prozeduren gelten jedoch auch für die Problembehandlung in Visual Basic- und Web Forms-Anwendungen.

Für das Remotedebuggen benötigen Sie Visual Studio 2013 oder Visual Studio 2012 mit Update 4. Die übrigen Features im Lernpgrogramm funktionieren auch mit Visual Studio 2013 Express für Web und Visual Studio 2012 Express für Web.

Die Streamingprotokoll-Funktion funktioniert nur für Anwendungen, die das .NET Framework 4 oder später verwenden.

## <a name="sitemanagement"></a>Konfigurieren und Verwalten von Websites

Visual Studio bietet Zugriff auf einen Teil der Site-Verwaltungsfunktionen und Konfigurationseinstellungen aus dem Verwaltungsportal. In diesem Abschnitt lernen Sie die verfügbaren Optionen kennen.

1.  Falls Sie noch nicht mit Visual Studio in Azure angemeldet sind, klicken Sie auf die Schaltfläche **Mit Azure verbinden** im **Server-Explorer**.

    Als Alternative können Sie ein Verwaltungszertifikat installieren, das den Zugriff auf Ihr Konto ermöglicht. Mit dem Verwaltungszertifikat erhält der Server-Explorer Zugriff auf zusätzliche Azure-Dienste (SQL-Datenbank und Mobile Dienste). Falls Sie ein Zertifikat installieren möchten, klicken Sie mit der rechten Maustaste auf den Knoten **Azure** im **Server-Explorer** und wählen Sie im Kontextmenü **Abonnements verwalten** aus. Klicken Sie im Dialogfeld **Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen und Importieren einer Abonnementdatei (auch *.publishsettings*-Datei genannt) für Ihr Azure-Konto.

    > [WACOM.NOTE]
    > Wenn Sie eine Abonnementdatei herunterladen, sollten Sie diese in einem Ordner außerhalb Ihrer Quellcodeverzeichnisse speichern (beispielsweise im Ordner "Downloads") und nach Abschluss des Importvorgangs löschen. Böswillige Benutzer, die Zugriff auf die Abonnementdatei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.

    Weitere Informationen für Verbindungen zwischen Visual Studio und Azure-Ressourcen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen][Verwalten von Konten, Abonnements und Administratorrollen].

2.  Erweitern Sie im **Server-Explorer** den Knoten **Azure** und anschließend den Knoten **Websites**.

3.  Klicken Sie mit der rechten Maustaste auf den Knoten der Website, die Sie in [Erste Schritte mit Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET] erstellt haben, und klicken Sie anschließend auf **Anzeigeeinstellungen**.

    ![Anzeigeeinstellungen im Server-Explorer][Anzeigeeinstellungen im Server-Explorer]

    Die Registerkarte **Azure-Website** wird angezeigt, und Sie sehen die in Visual Studio verfügbaren Aufgaben für die Verwaltung und Konfiguration von Websites.

    ![Fenster "Azure-Website"][Fenster "Azure-Website"]

    In diesem Lernprogramm werden Sie die Auswahllisten für Protokollierung und Ablaufverfolgung verwenden. Außerdem werden Sie Remotedebugging verwenden, allerdings wird diese Option auf andere Weise aktiviert.

    Weitere Informationen zu den Feldern für App-Einstellungen und Verbindungszeichenfolgen in diesem Fenster finden Sie unter [Azure-Websites: How Application Strings and Connection Strings Work][Azure-Websites: How Application Strings and Connection Strings Work] (Funktionsweise von Anwendungs- und Verbindungszeichenfolgen, in englischer Sprache).

    Falls Sie eine Verwaltungsaufgabe ausführen möchten, die in diesem Fenster nicht angeboten wird, können Sie alle Websiteeinstellungen anzeigen lassen, um das Verwaltungsportal in einem Browserfenster zu öffnen. Weitere Informationen finden Sie unter [Konfigurieren von Websites][Konfigurieren von Websites].

## <a name="remoteview"></a>Remoteansicht

Das `customErrors`-Kennzeichen in der Datei "Web.config" ist üblicherweise auf `On` oder `RemoteOnly` festgelegt. Das bedeutet, dass Sie bei einem Problem keine erklärende Fehlermeldung erhalten. Für viele Fehler bekommen Sie eine der folgenden Seiten angezeigt.

**Serverfehler in Anwendung '/':**
![Wenig hilfreiche Fehlermeldung][Wenig hilfreiche Fehlermeldung]

**Ein Fehler ist aufgetreten:**
![Wenig hilfreiche Fehlermeldung][1]

**Die Website kann diese Seite nicht anzeigen**
![Wenig hilfreiche Fehlermeldung][2]

Der einfachste Weg für die Suche nach der Fehlerursache ist die Aktivierung detaillierter Fehlermeldungen. Im ersten der vorigen Screenshots sehen Sie, welche Änderungen Sie dazu in der Web.config-Datei vornehmen müssen. Sie könnten die Datei *Web.config* im Projekt bearbeiten und das Projekt erneut bereitstellen oder eine [Web.config-Transformation][Web.config-Transformation] erstellen und ein Debugbuild bereitstellen. Es gibt jedoch eine schnellere Lösung: Im **Projektmappen-Explorer** können Sie Dateien mit der Funktion *Remoteansicht* direkt auf der Remotewebsite anzeigen und bearbeiten.

1.  Erweitern Sie im **Server-Explorer** zunächst **Azure**, dann **Websites** und anschließend den Knoten der Website, auf der Sie bereitstellen möchten.

    Daraufhin werden Knoten angezeigt, über die Sie auf die Inhalts- und Protokolldateien der Website zugreifen können.

    ![Datei und Protokolldateien][Datei und Protokolldateien]

2.  Erweitern Sie den Knoten **Dateien** und doppelklicken Sie auf die Datei *Web.config*.

    ![Öffnen der Datei Web.config][Öffnen der Datei Web.config]

    Visual Studio öffnet die Datei Web.config auf dem Remotesystem und zeigt den Text [Remote] neben dem Dateinamen in der Titelleiste an.

3.  Fügen Sie dem `system.web`-Element die folgende Zeile hinzu:

    `<customErrors mode="off"></customErrors>`

    ![Bearbeiten der Datei Web.config][Bearbeiten der Datei Web.config]

4.  Aktualisieren Sie den Browser, der die wenig hilfreiche Fehlermeldung anzeigt, und Sie erhalten eine detailliertere Fehlermeldung, wie im folgenden Beispiel gezeigt:

    ![Detaillierte Fehlermeldung][Detaillierte Fehlermeldung]

    (Dieser Fehler wurde durch Hinzufügen der in rot angezeigten Zeile zu *Views\\Home\\Index.cshtml* erstellt.)

Die Bearbeitung der Datei "Web.config" ist nur eines der Szenarien, in denen das Lesen und Bearbeiten der Dateien auf der Azure-Website die Problembehandlung erleichtert.

## <a name="remotedebug"></a>Remotedebuggen

Falls die detaillierte Fehlermeldung nicht genügend Informationen liefert und sich der Fehler nicht lokal reproduzieren lässt, können Sie die Website remote im Debugmodus ausführen. Im Debugmodus können Sie Breakpoints setzen, den Speicher direkt manipulieren, Code schrittweise durchlaufen und sogar den Codepfad ändern.

Remotedebuggen funktioniert nicht in den Express-Editionen von Visual Studio.

Dieser Abschnitt zeigt, wie Sie das in [Erste Schritte mit Windows Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET] erstellte Projekt remote debuggen können.

1.  Öffnen Sie das Webprojekt, das Sie in [Erste Schritte mit Windows Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET] erstellt haben.

2.  Öffnen Sie die Datei *Controllers\\HomeController.cs*.

3.  Löschen Sie die `About()`-Methode, und fügen Sie stattdessen den folgenden Code ein.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

4.  [Setzen Sie einen Haltepunkt][den Debugmodus] in der Zeile `ViewBag.Message`.

5.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.

6.  Wählen Sie in der Dropdownliste **Profil** dasselbe Profil aus, das Sie in [Erste Schritte mit Windows Azure und ASP.NET][Erste Schritte mit Azure und ASP.NET] verwendet haben.

7.  Wechseln Sie zur Registerkarte **Einstellungen**, ändern Sie **Konfiguration** auf **Debug** und klicken Sie anschließend auf **Veröffentlichen**.

    ![Veröffentlichen im Debugmodus][Veröffentlichen im Debugmodus]

8.  Warten Sie, bis die Veröffentlichung abgeschlossen ist und Ihr Browser die Azure-URL Ihrer Website öffnet, und schließen Sie anschließend den Browser.

9.  Für Visual Studio 2013: Erweitern Sie im **Server-Explorer** zuerst **Azure** und anschließend **Websites**, klicken Sie mit der rechten Maustaste auf Ihre Website, und klicken Sie auf **Debugger anfügen**.

    ![Debugger anfügen][Debugger anfügen]

    Der Browser öffnet automatisch Ihre Startseite in Azure. Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Bei weiteren Ausführungen in den folgenden 48 Stunden tritt keine Verzögerung auf.

10. Für Visual Studio 2012 mit Update 4:<span id="vs2012"></span></a>

    -   Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Konfigurieren** für Ihre Website, und führen Sie einen Bildlauf nach unten zum Abschnitt **Website-Diagnose** durch.

    -   Stellen Sie **Remotedebuggen** auf **Ein** und stellen Sie **Visual Studio-Version für Remotedebuggen** auf **2012**.

    ![Aktivieren von Remotedebuggen im Verwaltungsportal][Aktivieren von Remotedebuggen im Verwaltungsportal]

    -   Klicken Sie im **Debug**-Menü in Visual Studio auf **An den Prozess anhängen**.

    -   Geben Sie in das Feld **Qualifizierer** die URL Ihrer Website ohne das Präfix `http://` ein.

    -   Wählen Sie **Prozesse aller Benutzer anzeigen** aus.

    -   Wenn Sie zur Eingabe der Anmeldeinformationen aufgefordert werden, geben Sie Benutzername und Kennwort des Benutzers ein, der zum Veröffentlichen der Website berechtigt ist. Sie erhalten diese Anmeldeinformationen, indem Sie die Registerkarte **Dashboard** für Ihre Website im Verwaltungsportal öffnen und auf **Veröffentlichungsprofil herunterladen** klicken. Wenn Sie die Datei in einem Text-Editor öffnen, finden Sie Benutzername und Passwort nach den ersten Vorkommnissen von **userName=** und **userPWD=**.

    -   Wenn die Prozesse in der Tabelle **Verfügbare Prozesse** angezeigt werden, wählen Sie **w3wp.exe** aus und klicken Sie auf **Anhängen**.

    -   Öffnen Sie die URL Ihrer Website in einem Browser.

    Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer Website im Debugmodus auf. Bei weiteren Ausführungen in den folgenden 48 Stunden tritt keine Verzögerung auf.

11. Klicken Sie im Menü auf **Info**.

    Visual Studio hält am Breakpoint an, wobei der Code nicht auf Ihrem lokalen Computer läuft, sondern unter Azure.

12. Zeigen Sie auf die Variable `currentTime`, um den Zeitwert anzuzeigen.

    ![Anzeigen von Variablen im Debugmodus in Azure][Anzeigen von Variablen im Debugmodus in Azure]

    Bei der angezeigten Zeit handelt es sich um die Azure-Serverzeit, deren Zeitzone sich von Ihrer lokalen Einstellung unterscheiden kann.

13. Geben Sie einen neuen Wert für die Variable `currentTime` ein, z. B. "Ausführung unter Azure".

14. Drücken Sie F5, um die Ausführung fortzusetzen.

    Die Info-Seite unter Azure zeigt daraufhin den neuen Wert an, den Sie für die Variable currentTime eingegeben haben.

    ![Info-Seite mit neuem Wert][Info-Seite mit neuem Wert]

### Hinweise zum Remotedebuggen

-   Vermeiden Sie es, den Debugmodus in Produktion einzusetzen. Falls Ihre Produktions-Website nicht auf mehrere Serverinstanzen verteilt ist, wird beim Debuggen der Netzwerkverkehr zu Ihrer einzigen Webserverinstanz gebunden. Falls Sie mehrere Webserverinstanzen betreiben, erhalten Sie beim Anhängen des Debuggers eine zufällige Instanz, und Sie können nicht garantieren, dass Ihre Browseranforderungen an die richtige Instanz gehen. Außerdem ist es unüblich, Debugversionen in Produktion bereitzustellen, und Compileroptimierungen für Releaseversionen verhindern die zeilenweise Anzeige der Vorgänge in Ihrem Quellcode. Die beste Möglichkeit zur Problembehandlung von Produktionsproblemen sind Ablaufverfolgung und Webserverprotokolle.

-   Vermeiden Sie es beim Remotedebuggen, lange an Breakpoints anzuhalten. Azure behandelt Prozesse, die länger als einige Minuten angehalten sind, als nicht reagierend und beendet diese.

-   Beim Debuggen schickt der Server Daten an Visual Studio und verursacht möglicherweise zusätzliche Kosten für Bandbreite. Weitere Informationen zu Bandbreitentarifen finden Sie unter [Azure Pricing][Azure Pricing] (Azure-Preisübersicht, in englischer Sprache).

-   Stellen Sie sicher, dass das Attribut `debug` im Element `compilation` in der Datei *Web.config* auf "true" festgelegt ist. Dieses Attribut ist beim Veröffentlichen einer Debug-Buildkonfiguration standardmäßig "true".

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

-   Falls der Debugger den gewünschten Code nicht schrittweise ausführt, müssen Sie möglicherweise die Einstellung "Nur eigenen Code" ändern. Weitere Informationen finden Sie unter [Schrittweises Durchlaufen für "Nur mein Code" beschränken][Schrittweises Durchlaufen für "Nur mein Code" beschränken].

-   Bei Aktivierung der Remotedebuggen-Funktion startet ein Timer auf dem Server, und die Funktion wird nach 48 Stunden automatisch abgeschaltet. Dieses Limit von 48 Stunden existiert aus Sicherheits- und Leistungsgründen. Sie können die Funktion jederzeit und beliebig oft aktivieren. Wenn Sie nicht aktiv debuggen, sollten Sie die Funktion jedoch deaktivieren.

-   Nun können Sie den Debugger manuell an beliebige Prozesse anhängen. Sie können nicht nur den Website-Prozess (w3wp.exe) debuggen, sondern auch andere Prozesse wie z. B. [WebJobs][WebJobs]. Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter [Debuggen in Visual Studio][Debuggen in Visual Studio].

## <a name="logsoverview"></a><span class="short-header">Übersicht über Protokolle</span>Übersicht über Diagnoseprotokolle

ASP.NET-Anwendungen in Azure-Websites können die folgenden Arten von Protokollen generieren:

-   **Ablaufverfolgungsprotokolle**
     Anwendungen erzeugen diese Protokolle, indem sie Methoden der Klasse [System.Diagnostics.Trace][System.Diagnostics.Trace] aufrufen.
-   **Webserver-Protokolle**
     Der Webserver erstellt einen Protokolleintrag für jede HTTP-Anforderung an die Site.
-   **Detaillierte Fehlerprotokolle**
     Der Webserver erstellt eine HTML-Seite mit zusätzlichen Informationen für fehlgeschlagene HTTP-Anforderungen (Anforderungen mit einem Statuscode 400 oder höher).
-   **Ablaufverfolgungs-Protokolle für fehlgeschlagene Anforderungen**
     Der Webserver erstellt eine XML-Datei mit detaillierten Ablaufverfolgungsinformationen für fehlgeschlagene HTTP-Anforderungen. Der Webserver liefert außerdem eine XSL-Datei zur Formatierung der XML-Datei in einem Browser.

Protokollierung kann die Leistung von Websites beeinträchtigen. Daher können Sie die verschiedenen Protokolltypen unter Azure bei Bedarf einzeln aktivieren und deaktivieren. Für Anwendungsprotokolle können Sie angeben, dass nur Protokolleinträge oberhalb eines bestimmten Schweregrads geschrieben werden sollen. Bei der Erstellung neuer Websites ist die gesamte Protokollierung standardmäßig deaktiviert.

Die Protokolle werden in den Ordner *LogFiles* im Dateisystem Ihrer Website geschrieben und sind über FTP zugänglich. Webserver- und Anwendungsprotokolle können auch in ein Azure-Speicherkonto geschrieben werden. Speicherkonten bieten mehr Kapazität für Protokolle als das Dateisystem. Protokolle im Dateisystem sind beschränkt auf 100 Megabyte. (Protokolle im Dateisystem werden nur für kurze Zeit aufbewahrt. Azure löscht alte Protokolldateien, um Platz für neue Dateien zu machen, wenn das Limit erreicht ist.)

## <a name="apptracelogs"></a><span class="short-header">Anwendungsprotokolle</span>Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen

In diesem Abschnitt werden Sie die folgenden Aufgaben ausführen:

-   Hinzufügen von Ablaufverfolgungs-Anweisungen im Webprojekt, das Sie [im vorigen Lernprogramm][Erste Schritte mit Azure und ASP.NET] erstellt haben.
-   Anzeigen der Protokolle, wenn Sie das Projekt lokal ausführen.
-   Anzeigen der Protokolle, während diese von der Anwendung unter Azure generiert werden.

### Hinzufügen von Ablaufverfolgungs-Anweisungen zur Anwendung

1.  Öffnen Sie *Controllers\\HomeController.cs*, und ersetzen Sie den Inhalt der Datei durch den folgenden Code, um `Trace`-Anweisungen und eine `using`-Anweisung für `System.Diagnostics` hinzuzufügen:

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

1.  Drücken Sie F5, um die Anwendung im Debugmodus auszuführen.

    Der Standard-Ablaufverfolgungs-Listener schreibt sämtliche Ausgaben in das **Ausgabefenster**, zusammen mit anderen Debugausgaben. Die folgende Abbildung zeigt die Ausgaben der Ablaufverfolgungsanweisungen, die Sie der `Index`-Methode hinzugefügt haben.

    ![Ablaufverfolgung im Debugfenster][Ablaufverfolgung im Debugfenster]

    Anhand der folgenden Schritte können Sie Ablaufverfolgungs-Ausgaben in einer Webseite anzeigen, ohne im Debugmodus zu kompilieren.

2.  Öffnen Sie die Web.config-Datei der Anwendung (die Datei im Projektordner), und fügen Sie das Element `<system.diagnostics>` am Ende der Datei direkt vor dem abschließenden `</configuration>`-Element hinzu:

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

    Über `WebPageTraceListener` können Sie die Ausgabe der Ablaufverfolgung anzeigen, indem Sie zu `/trace.axd` navigieren.

3.  Fügen Sie ein [trace][trace]-Element unter `<system.web>` in die Web.config-Datei ein, wie im folgenden Beispiel gezeigt:

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

4.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

5.  Fügen Sie der URL in der Adressleiste des Browserfensters *trace.axd* hinzu, und drücken Sie die EINGABETASTE (die URL sollte in etwa so aussehen: <http://localhost:53370/trace.axd>).

6.  Klicken Sie in der Seite **Anwendungs-Ablaufverfolgung** auf **Details anzeigen**.

    ![trace.axd][trace.axd]

    Daraufhin wird die Seite **Anforderungsdetails** angezeigt, und im Bereich **Überwachungsinformationen** sehen Sie die Ausgabe der Ablaufverfolgungs-Anweisungen, die Sie der `Index`-Methode hinzugefügt haben.

    ![trace.axd][3]

    `trace.axd` ist standardmäßig nur lokal verfügbar. Sie können `localOnly="false"` dem `trace`-Element der *Web.config*-Datei hinzufügen, um die Datei von einer Remotewebsite verfügbar zu machen, wie im folgenden Beispiel gezeigt:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    `trace.axd` sollte jedoch in Produktionswebsites aus Sicherheitsgründen deaktiviert werden. In den folgenden Abschnitten lernen Sie einen einfacheren Weg kennen, um Ablaufverfolgungsprotokolle in Azure-Websites zu lesen.

### Anzeige der Ablaufverfolgungs-Ausgabe in Azure

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt und anschließend auf **Veröffentlichen**.

2.  Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.

    Nachdem Visual Studio Ihr Update veröffentlicht hat, öffnet sich ein Browserfenster zu Ihrer Startseite (sofern sie **Ziel-URL** in der Registerkarte **Verbindung** nicht gelöscht haben).

3.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf Ihre Website, und wählen Sie **Streamingprotokolle im Ausgabefenster anzeigen**.

    ![Anzeigen der Streamingprotokolle im Kontextmenü][Anzeigen der Streamingprotokolle im Kontextmenü]

    Das **Ausgabefenster** zeigt an, dass Sie mit dem Protokollstreamingdienst verbunden sind und fügt eine Benachrichtigungszeile für jede Minute hinzu, in der kein anzuzeigendes Protokoll eingeht.

    ![Anzeigen der Streamingprotokolle im Kontextmenü][4]

4.  Klicken Sie im Browserfenster, in dem die Startseite Ihrer Anwendung geöffnet ist, auf **Contact**.

    Innerhalb weniger Sekunden wird die Ablaufverfolgung mit dem Schweregrad "Fehler", die Sie der `Contact`-Methode hinzugefügt haben, im **Ausgabefenster** angezeigt.

    ![Fehler-Ablaufverfolgung im Ausgabefenster][Fehler-Ablaufverfolgung im Ausgabefenster]

    Visual Studio zeigt standardmäßig nur Ablaufverfolgungsprotokolle mit dem Schweregrad Fehler an, wenn der Protokollüberwachungsdienst gestartet wird. Bei der Erstellung einer neuen Azure-Website ist sämtliche Protokollierung standardmäßig deaktiviert, wie Sie zuvor beim Öffnen der Seite mit den Websiteeinstellungen gesehen haben:

    ![Anwendungsprotokollierung deaktiviert][Anwendungsprotokollierung deaktiviert]

    Als Sie jedoch **Streamingprotokolle im Ausgabefenster anzeigen** ausgewählt haben, hat Visual Studio **Anwendungsprotokollierung(Dateisystem)** automatisch auf **Fehler** eingestellt, und Protokolle mit dem Schweregrad Fehler werden gemeldet. Sie können diese Einstellung auf **Ausführlich** ändern, falls Sie sämtliche Ablaufverfolgungs-Protokolle sehen möchten. Wenn Sie einen Schweregrad unterhalb von Fehler auswählen, werden die Protokolleinträge der höheren Schweregrade ebenfalls geschrieben. Wenn Sie also ausführlich auswählen, werden die Protokolleinträge für Information, Warnung und Fehler geschrieben.

5.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Website, und klicken Sie anschließend wie oben auf **Anzeigeeinstellungen**.

6.  Ändern Sie **Anwendungsprotokollierung (Dateisystem)** zu **Ausführlich** und klicken Sie auf **Speichern**.

    ![Einstellen des Ablaufverfolgungs-Schweregrads auf Ausführlich][Einstellen des Ablaufverfolgungs-Schweregrads auf Ausführlich]

7.  Klicken Sie im Browserfenster, in dem Ihre **Contact**-Seite angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

    Innerhalb weniger Sekunden wird im **Ausgabefenster** Ihre gesamte Ablaufverfolgungsausgabe angezeigt.

    ![Ablaufverfolgungs-Schweregrad Ausführlich][Ablaufverfolgungs-Schweregrad Ausführlich]

    In diesem Abschnitt haben Sie die Protokollierung mithilfe der Azure-Websiteeinstellungen aktiviert bzw. deaktiviert. Sie können die Ablaufverfolgungs-Listener auch über die Datei Web.config aktivieren bzw. deaktivieren. Wenn Sie die Datei Web.config ändern, wird jedoch die Anwendungsdomäne neu gestartet, was bei der Aktivierung der Protokollierung über die Website nicht der Fall ist. Falls das Problem schwer zu reproduzieren ist oder nur zeitweilig auftritt, kann es passieren, dass das Problem beim Neustart der Domäne verschwindet und Sie warten müssen, bis es erneut auftritt. Beim Aktivieren der Diagnose in Azure ist dies nicht der Fall, und Sie können sofort mit der Erfassung der Fehlerinformationen beginnen.

### Funktionen des Ausgabefensters

Die Registerkarte **Azure-Protokolle** im **Ausgabefenster** enthält verschiedene Schaltflächen und ein Textfeld:

![Schaltflächen auf der Registerkarte Protokolle][Schaltflächen auf der Registerkarte Protokolle]

Diese Schaltflächen bieten die folgenden Funktionen:

-   Löschen des **Ausgabefensters**.
-   Aktivieren bzw. deaktivieren des Zeilenumbruchs.
-   Starten bzw. Stoppen der Protokollüberwachung.
-   Angeben, welche Protokolle überwacht werden sollen.
-   Herunterladen von Protokollen.
-   Filtern von Protokollen anhand von Suchzeichenfolgen oder regulären Ausdrücken.
-   Schließen des **Ausgabefensters**.

Wenn Sie eine Suchzeichenfolge oder einen regulären Ausdruck eingeben, filtert Visual Studio die Protokollinformationen clientseitig. Daher können Sie die Kriterien eingeben, nachdem die Protokolle im **Ausgabefenster** angezeigt wurden, und Sie können die Filterkriterien ändern, ohne die Protokolle neu generieren zu müssen.

## <a name="webserverlogs"></a><span class="short-header">Webserverprotokolle</span>Anzeigen von Webserverprotokollen

Webserverprotokolle zeichnen sämtliche HTTP-Aktivitäten der Website auf Sie müssen diese Protokolle für die Website aktivieren und Visual Studio mitteilen, dass Sie diese überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können

1.  Ändern Sie auf der Registerkarte **Azure-Websitekonfiguration**, die Sie im **Server-Explorer** geöffnet haben, die Webserverprotokollierung auf **Ein**, und klicken Sie auf **Speichern**.

    ![Aktivieren der Webserverprotokollierung][Aktivieren der Webserverprotokollierung]

2.  Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Angeben, welche Azure-Protokolle überwacht werden sollen**.

    ![Angeben, welche Azure-Protokolle überwacht werden sollen][Angeben, welche Azure-Protokolle überwacht werden sollen]

3.  Wählen Sie im Dialogfeld **Azure-Protokollierungsoptionen** die Option **Webserverprotokolle** aus und klicken Sie auf **OK**.

    ![Überwachen von Webserverprotokollen][Überwachen von Webserverprotokollen]

4.  Klicken Sie im Browserfenster, in dem Ihre Website angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

    Normalerweise werden die Anwendungsprotokolle zuerst angezeigt, gefolgt von den Webserverprotokollen. Möglicherweise müssen Sie kurz warten, bis die Protokolle angezeigt werden.

    ![Webserverprotokolle im Ausgabefenster][Webserverprotokolle im Ausgabefenster]

Bei der ersten Aktivierung der Webserverprotokolle in Visual Studio schreibt Azure die Protokolle standardmäßig in das Dateisystem. Alternativ können Sie im Verwaltungsportal angeben, dass die Webserverprotokolle in einen Blob-Container in einem Speicherkonto geschrieben werden sollen. Weitere Informationen finden Sie im Abschnitt **Diagnose** unter [Konfigurieren von Websites][Konfigurieren von Websites].

Wenn Sie die Webserverprotokollierung für ein Azure-Speicherkonto im Verwaltungsportal aktivieren und die Protokollierung anschließend in Visual Studio deaktivieren, werden die Speicherkontoeinstellungen bei der nächsten Aktivierung wiederhergestellt.

## <a name="detailederrorlogs"></a><span class="short-header">Fehlerprotokolle</span>Anzeigen detaillierter Fehlermeldungsprotokolle

Die detaillierten Fehlerprotokolle liefern zusätzliche Informationen über HTTP-Anforderungen, die zu einer Fehlerantwort geführt haben (400 oder höher). Sie müssen diese Protokolle für die Website aktivieren und Visual Studio mitteilen, dass Sie diese überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können

1.  Ändern Sie auf der Registerkarte **Azure-Websitekonfiguration**, die Sie im **Server-Explorer** geöffnet haben, die Option **Detaillierte Fehlermeldungen** in **Ein**, und klicken Sie auf **Speichern**.

    ![Aktivieren der detaillierten Fehlermeldungen][Aktivieren der detaillierten Fehlermeldungen]

2.  Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Angeben, welche Azure-Protokolle überwacht werden sollen**.

3.  Klicken Sie im Dialogfeld **Azure-Protokollierungsoptionen** die Option **Alle Protokolle** aus und klicken Sie auf **OK**.

    ![Überwachen aller Protokolle][Überwachen aller Protokolle]

4.  Fügen Sie in der Adressleiste Ihres Browserfensters ein zusätzliches Zeichen an die URL an, um einen 404-Fehler zu verursachen (z. B. `http://localhost:53370/Home/Contactx`), und drücken Sie die EINGABETASTE.

    Nach wenigen Sekunden erscheint das detaillierte Fehlerprotokoll im **Ausgabefenster** von Visual Studio.

    ![Detailliertes Fehlerprotokoll im Ausgabefenster][Detailliertes Fehlerprotokoll im Ausgabefenster]

    Klicken Sie bei gedrückter STRG-Taste auf den Link, um das formatierte Protokoll im Browser anzuzeigen:

    ![Detailliertes Fehlerprotokoll im Browserfenster][Detailliertes Fehlerprotokoll im Browserfenster]

## <a name="downloadlogs"></a><span class="short-header">Herunterladen von Protokollen</span>Herunterladen von Protokollen aus dem Dateisystem

Alle Protokolle, die Sie im **Ausgabefenster** überwachen können, lassen sich auch als *.zip*-Datei herunterladen.

1.  Klicken Sie im **Ausgabefenster** auf **Streamingprotokolle herunterladen**.

    ![Schaltflächen auf der Registerkarte Protokolle][5]

    Der Datei-Explorer öffnet Ihren *Downloads*-Ordner, und die heruntergeladene Datei ist ausgewählt.

    ![Heruntergeladene Datei][Heruntergeladene Datei]

2.  Wenn Sie die *.zip*-Datei extrahieren, sehen Sie die folgende Ordnerstruktur:

    ![Heruntergeladene Datei][6]

    -   Ablaufverfolgungsprotokolle von Anwendungen befinden sich in *.txt*-Dateien im Ordner *LogFiles\\Application*.
    -   Webserverprotokolle befinden sich in *.log*-Dateien im Ordner *LogFiles\\http\\RawLogs*. Sie können diese Dateien mit Werkzeugen wie z. B. [Log Parser][Log Parser] anzeigen und bearbeiten.
    -   Detaillierte Fehlerprotokolle befinden sich in *.html*-Dateien im Ordner *LogFiles\\DetailedErrors*.

    (Der Ordner *deployments* enthält Dateien der Quellcodeverwaltung und hat nichts mit der Veröffentlichung in Visual Studio zu tun. Der Ordner *Git* enthält Ablaufverfolgungsprotokolle für die Quellcodeverwaltung und den Protokollstreamingdienst.)

## <a name="storagelogs"></a><span class="short-header">Speicherprotokolle</span>Anzeigen von Speicherprotokollen

Ablaufverfolgungsprotokolle von Anwendungen können auch an Azure-Speicherkonten geschickt und anschließend in Visual Studio angezeigt werden. Dazu müssen Sie ein Speicherkonto erstellen, Speicherprotokolle im Verwaltungsportal aktivieren und diese anschließend auf der Registerkarte **Protokolle** im Fenster **Azure-Website** anzeigen.

Sie können die Protokolle an eines oder alle dieser drei Ziele schicken:

-   Das Dateisystem.
-   Speicherkonto-Tabellen.
-   Speicherkonto-Blobs.

Sie können unterschiedliche Schweregrade für die einzelnen Ziele angeben.

Tabellen erleichtern die Anzeige von Protokolldetails online und unterstützen die Streamingfunktion: Sie können Protokolle aus Tabellen anzeigen und sehen neue Protokolleinträge, sobald diese erstellt werden. BLOBs erleichtern den Download der Protokolle in Dateiform und deren Analyse mithilfe von HDInsight, da HDInsight mit dem BLOB-Format umgehen kann. Weitere Informationen finden Sie im Abschnitt **Hadoop und MapReduce** unter [Data Storage Options (Building Real-World Cloud Apps with Azure)][Data Storage Options (Building Real-World Cloud Apps with Azure)] (Datenspeicheroptionen (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache).

Der Schweregrad Ihrer Dateisystemprotokolle ist momentan auf Ausführlich eingestellt. In den folgenden Schritten lernen Sie, wie Sie Protokolle mit dem Schweregrad Information in Speicherkontotabellen leiten können. Der Schweregrad "Information" bedeutet, dass alle Protokolle angezeigt werden die durch den Aufruf `Trace.TraceInformation`, `Trace.TraceWarning`, und `Trace.TraceError` erstellt werden. Dies gilt nicht für Protokolle, die durch den Aufruf von `Trace.WriteLine` erstellt werden.

Speicherkonten bieten mehr Speicherplatz und längere Aufbewahrungszeiten für Protokolle als das Dateisystem. Außerdem erhalten Sie bei der Aufbewahrung von Ablaufverfolgungsprotokollen in Speicherkonten zusätzliche Informationen, die bei Dateisystemprotokollen entfallen.

1.  Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Website, und klicken Sie anschließend auf **Im Verwaltungsportal öffnen**.

2.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Datenspeicher** und anschließend auf **Speicherkonto erstellen**.

    ![Speicherkonto erstellen][Speicherkonto erstellen]

3.  Geben Sie die eindeutige URL des Speicherkontos ein und klicken Sie auf **Speicherkonto erstellen**.

    ![Geben Sie eine URL ein][Geben Sie eine URL ein]

4.  Klicken Sie in Visual Studio im Fenster **Azure-Website** auf die Registerkarte **Protokolle** und anschließend auf **Protokollierung konfigurieren**.

    ![Heruntergeladene Datei][7]

    Daraufhin öffnet sich im Verwaltungsportal die Registerkarte **Konfigurieren** für Ihre Website. Sie können diese Registerkarte auch öffnen, indem Sie auf die Registerkarte **Websites** und anschließend auf Ihre Website und auf die Registerkarte **Konfigurieren** klicken.

5.  Blättern Sie in der Registerkarte **Konfigurieren** im Verwaltungsportal nach unten bis zum Bereich Anwendungsdiagnose und ändern Sie den Wert von **Anwendungsprotokollierung (Tabellenspeicher)** auf **Ein**.

6.  Ändern Sie **Protokollierungsstufe** auf **Information**.

7.  Klicken Sie auf **Tabellenspeicher verwalten**.

    ![Klicken Sie auf Tabellenspeicher verwalten][Klicken Sie auf Tabellenspeicher verwalten]

    Im Feld **Tabellenspeicher für Anwendungsdiagnose verwalten** können Sie Ihr Speicherkonto auswählen, falls Sie mehr als eines verwenden. Sie können eine neue Tabelle erstellen oder eine existierende Tabelle verwenden.

    ![Tabellenspeicher verwalten][Tabellenspeicher verwalten]

8.  Setzen Sie das Häkchen im Feld **Tabellenspeicher für Anwendungsdiagnose verwalten**, um das Fenster zu schließen.

9.  Klicken Sie in der Registerkarte **Konfigurieren** im Verwaltungsportal auf **Speichern**.

10. Klicken Sie im Browserfenster, in dem Ihre Anwendungswebsite angezeigt wird, auf **Home**, anschließend auf **About** und dann auf **Contact**.

    Die von diesen Webseiten produzierten Protokollierungsinformationen werden daraufhin in Ihr Speicherkonto geschrieben.

11. Klicken Sie in Visual Studio auf der Registerkarte **Protokolle** im Fenster **Azure-Website** unter **Diagnosezusammenfassung** auf **Aktualisieren**.

    ![Klicken Sie auf Aktualisieren][Klicken Sie auf Aktualisieren]

    Im Bereich **Diagnosezusammenfassung** werden standardmäßig die Protokolle der vergangenen 15 Minuten angezeigt. Sie können das Zeitintervall ändern, um weitere Protokolleinträge anzuzeigen.

    (Falls Sie den Fehler "Tabelle nicht gefunden" erhalten, vergewissern Sie sich, dass Sie die entsprechenden Seiten aufgerufen haben, nachdem Sie **Anwendungsprotokollierung (Tabellenspeicher)** aktiviert und auf **Speichern** geklickt haben.)

    ![Speicherprotokolle][Speicherprotokolle]

    In dieser Ansicht werden **Prozess-ID** und **Thread-ID** für die einzelnen Protokolle angezeigt. Diese Informationen sind in den Dateisystemprotokollen nicht enthalten. Sie können zusätzliche Felder anzeigen, indem Sie die Azure-Speichertabelle direkt öffnen.

12. Klicken Sie auf **Alle Anwendungsprotokolle anzeigen**.

    Daraufhin wird die Ablaufprotokolltabelle in der Azure-Speichertabellenansicht angezeigt.

    (Falls Sie den Fehler "Sequenz enthält keine Elemente" erhalten, öffnen Sie den **Server-Explorer**, erweitern Sie den Knoten Ihres Speicherkontos unterhalb des **Azure**-Knotens, klicken Sie mit der rechten Maustaste auf **Tabellen** und klicken Sie auf **Aktualisieren**.)

    ![Ablaufverfolgungstabelle im Server-Explorer][Ablaufverfolgungstabelle im Server-Explorer]

    ![Speicherprotokolle in der Tabellenansicht][Speicherprotokolle in der Tabellenansicht]

    Diese Ansicht enthält zusätzliche Felder, die in keiner der anderen Ansichten angezeigt werden. Außerdem können Sie die Protokolle in dieser Ansicht mithilfe der Abfrage-Generator-GUI filtern. Weitere Informationen finden Sie im Abschnitt "Working with Table Resources - Filtering Entities" (Arbeiten mit Tabellenressourcen - Entitätenfilter" unter [Browsing Storage Resources with Server Explorer][Browsing Storage Resources with Server Explorer] (Durchsuchen von Speicherressourcen im Server-Explorer, in englischer Sprache).

13. Klicken Sie mit der rechten Maustaste auf eine der Zeilen und klicken Sie anschließend auf **Bearbeiten**, um die Details für eine einzelne Zeile anzuzeigen.

    ![Ablaufverfolgungstabelle im Server-Explorer][8]

## <a name="failedrequestlogs"></a><span class="short-header">Protokolle für fehlgeschlagene Anforderungen</span>Anzeige von Protokollen für fehlgeschlagene Anforderungen

Anhand der Protokolle für fehlgeschlagene Anforderungen können Sie im Detail herausfinden, wie IIS HTTP-Anforderungen bearbeitet, z. B. in Szenarien mit URL-Neuschreibung oder bei Authentifizierungsproblemen.

Azure-Websites verwenden die gleiche Funktion zum Verfolgen fehlgeschlagener Anforderungen, die in IIS 7.0 und höher verfügbar ist. Sie haben jedoch keinen Zugriff auf die IIS-Einstellungen, in denen festgelegt wird, welche Fehler protokolliert werden. Wenn Sie die Verfolgung fehlgeschlagener Anforderungen aktivieren, werden alle Fehler erfasst.

Sie können die Protokolle für fehlgeschlagene Anforderungen in Visual Studio aktivieren, allerdings lassen sich diese Protokolle nicht in Visual Studio anzeigen. Diese Protokolle liegen in Form von XML-Dateien vor. Der Streamingprotokolldienst überwacht nur Dateien, die im Nur-Text-Modus lesbar sind: *.txt-*, *.html-* und *.log-*Dateien.

Sie können die Protokolle für fehlgeschlagene Anforderungen entweder direkt über FTP im Browser anzeigen oder mit einem FTP-Client auf Ihren lokalen Computer herunterladen. In diesem Abschnitt werden Sie die Protokolle direkt im Browser anzeigen.

1.  Ändern Sie auf der Registerkarte **Konfiguration** im Fenster **Azure-Website**, das Sie vom **Server-Explorer** aus geöffnet haben, die Option **Ablaufverfolgung für Anforderungsfehler** in **Ein**, und klicken Sie auf **Speichern**.

    ![Aktivieren der Verfolgung fehlgeschlagener Anforderungen][Aktivieren der Verfolgung fehlgeschlagener Anforderungen]

2.  Fügen Sie in der Adressleiste des Browserfensters, in dem die Website angezeigt wird, der URL ein zusätzliches Zeichen hinzu, und drücken Sie die EINGABETASTE, um einen 404-Fehler zu verursachen.

    Daraufhin wird ein Protokoll für die fehlgeschlagene Anforderung erstellt. In den folgenden Schritten lernen Sie, wie Sie dieses Protokoll anzeigen oder herunterladen können.

3.  Klicken Sie in Visual Studio auf der Registerkarte **Konfiguration** im Fenster **Azure-Website** auf **Im Verwaltungsportal öffnen**.

4.  Klicken Sie im Verwaltungsportal auf **Dashboard** und anschließend auf **Zurücksetzen der Bereitstellungsanmeldeinformationen** im Bereich **Schnellansicht**.

    ![Zurücksetzen der FTP-Anmeldeinformationen im Dashboard][Zurücksetzen der FTP-Anmeldeinformationen im Dashboard]

5.  Geben Sie einen neuen Benutzernamen und ein neues Passwort ein.

    ![Neuer FTP-Benutzername und neues Passwort][Neuer FTP-Benutzername und neues Passwort]

6.  Drücken Sie im **Dashboard** im Verwaltungsportal auf F5, um die Seite zu aktualisieren, und blättern Sie nach unten zum Bereich **Bereitstellung / FTP-Benutzer**. Beachten Sie, dass der Name der Site dem Benutzernamen vorangestellt ist. **Bei der Anmeldung müssen Sie diesen kompletten Benutzernamen inklusive Name der Site verwenden, so wie hier angezeigt.**

7.  Öffnen Sie in einem neuen Browserfenster die URL, die unter **FTP-Hostname** in der Registerkarte **Dashboard** des Verwaltungsportals für Ihre Website angezeigt wird. **FTP-Hostname** befindet sich in der Nähe von **Bereitstellung / FTP-Benutzer** im Bereich **Schnellübersicht**.

8.  Melden Sie sich mit den zuvor erstellten FTP-Anmeldeinformationen an (inklusive vorangestelltem Namen der Site vor dem Benutzernamen).

    Daraufhin wird im Browser der Stammordner der Site angezeigt.

9.  Öffnen Sie den Ordner *LogFiles*.

    ![Öffnen Sie den Ordner LogFiles][Öffnen Sie den Ordner LogFiles]

10. Öffnen Sie den Ordner mit dem Namen W3SVC plus nachgestelltem numerischem Wert.

    ![Öffnen Sie den Ordner W3SVC][Öffnen Sie den Ordner W3SVC]

    Der Ordner enthält XML-Dateien für sämtliche protokollierten Fehler, nachdem Sie die Protokollierung fehlgeschlagener Anforderungen aktiviert haben, sowie eine XSL-Datei für die XML-Formatierung im Browser.

    ![Ordner W3SVC][Ordner W3SVC]

11. Klicken Sie auf die XML-Datei für die fehlgeschlagene Anforderung, deren Ablaufverfolgungsinformationen Sie anzeigen möchten.

    Die folgende Abbildung zeigt einen Teil der Ablaufverfolgungsinformationen für einen Beispielfehler.

    ![Verfolgung fehlgeschlagener Anforderungen im Browser][Verfolgung fehlgeschlagener Anforderungen im Browser]

## <a name="nextsteps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

Sie haben gelernt, wie Visual Studio die Anzeige der von Azure-Websites erstellten Protokolle erleichtert. Möglicherweise möchten Sie mehr über die Problembehandlung in Azure-Websites, die Ablaufverfolgung in ASP.NET-Anwendungen und die Analyse von Webserverprotokollen erfahren.

### Problembehandlung von Azure-Websites

Weitere Informationen zur Problembehandlung in Azure-Websites finden Sie in den folgenden Ressourcen:

-   [Problembehandlung in Azure][Problembehandlung in Azure]
     Ein White Paper mit einer kurzen Einführung zu Azure-Websites.
-   [Problembehandlung in Websites][Problembehandlung in Websites]
     Eine Einleitung mit Fokus auf Azure-Websites.
-   [Aktivieren der Diagnoseprotokollierung für Azure-Websites][Aktivieren der Diagnoseprotokollierung für Azure-Websites]
     Behandelt im Wesentlichen dieselben Informationen wie dieses Lernprogramm, konzentriert sich jedoch auf das Abrufen der Diagnoseprotokolle ohne Visual Studio.
-   [Überwachung von Websites][Überwachung von Websites]
     Der Abschnitt [Konfigurieren von Diagnose und Herunterladen von Protokollen][Konfigurieren von Diagnose und Herunterladen von Protokollen] enthält wichtige Informationen, die nicht in den Dokumenten zu Problembehandlung enthalten sind.
-   [Untersuchen von Speicherlecks in Azure-Websites mit Visual Studio 2013][Untersuchen von Speicherlecks in Azure-Websites mit Visual Studio 2013]. Microsoft ALM-Blogbeiträge über Visual Studio-Funktionen für die Untersuchung von Problemen mit verwaltetem Speicher.
-   [Onlinetools für Windows Azure-Websites, die Sie kennen sollten][Onlinetools für Windows Azure-Websites, die Sie kennen sollten]. Blogbeitrag von Amit Apple.

Falls Sie spezifische Fragen zur Problembehandlung haben, können Sie diese in einem der folgenden Foren stellen:

-   [Das Azure-Forum auf der ASP.NET-Website][Das Azure-Forum auf der ASP.NET-Website].
-   [Das Azure-Forum auf MSDN][Das Azure-Forum auf MSDN].
-   [StackOverflow.com][StackOverflow.com].

### Debuggen in Visual Studio

Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter dem MSDN-Thema [Debuggen in Visual Studio][Debuggen in Visual Studio] und unter [Debugging Tips with Visual Studio 2010][Debugging Tips with Visual Studio 2010] (Tipps zum Debuggen in Visual Studio 2010, in englischer Sprache).

Weitere Informationen zum Remotedebuggen in Azure-Websites finden Sie in den folgenden Ressourcen:

-   [Einführung in das Remotedebuggen für Azure-Websites][Einführung in das Remotedebuggen für Azure-Websites].
-   [Einführung in das Remotedebuggen von Azure-Websites, Teil 2 - Einblick in das Remotedebuggen][Einführung in das Remotedebuggen von Azure-Websites, Teil 2 - Einblick in das Remotedebuggen]
-   [Einführung in das Remotedebuggen von Azure-Websites, Teil 3 - Mehrinstanzenumgebung und GIT][Einführung in das Remotedebuggen von Azure-Websites, Teil 3 - Mehrinstanzenumgebung und GIT]

Falls Ihre Website eine Azure-Web-API oder ein Mobile Services-Back-End verwendet und Sie dieses debuggen möchten, finden Sie weitere Informationen unter [Debuggen des .NET-Back-Ends in Visual Studio][Debuggen des .NET-Back-Ends in Visual Studio].

### Ablaufverfolgung in ASP.NET-Anwendungen

Momentan sind keine vollständigen und aktuellen Einführungen zur Ablaufverfolgung in ASP.NET im Internet verfügbar. Beginnen Sie daher mit den älteren Einführungen für Web Forms, als MVC noch nicht existierte, und ergänzen Sie diese Lektüre mit neueren Blogeinträgen zu speziellen Themen. Die folgenden Ressourcen bieten gute Einstiegspunkte:

-   [Überwachung und Telemetrie (Erstellen realer Cloud-Apps mit Azure)][Überwachung und Telemetrie (Erstellen realer Cloud-Apps mit Azure)].
     Dieses E-Book-Kapitel enthält Empfehlungen zur Ablaufverfolgung in Azure-Cloudanwendungen.
-   [ASP.NET Tracing][ASP.NET Tracing]
     (Ablaufverfolgung in ASP.NET, in englischer Sprache). Ein älterer Artikel, der sich jedoch immer noch gut als Einstieg in das Thema eignet.
-   [Trace Listeners][Trace Listeners]
     (Ablaufverfolgungs-Listener, in englischer Sprache) Informationen über Ablaufverfolgungs-Listener, allerdings wird der [WebPageTraceListener][WebPageTraceListener] nicht behandelt.
-   [Exemplarische Vorgehensweise: Integrieren der ASP.NET-Ablaufverfolgung und der System.Diagnostics-Ablaufverfolgung][Exemplarische Vorgehensweise: Integrieren der ASP.NET-Ablaufverfolgung und der System.Diagnostics-Ablaufverfolgung]
     Dieser Artikel ist ebenfalls älteren Datums, enthält jedoch zusätzliche Informationen, die in der Einführung nicht behandelt werden.
-   [Tracing in ASP.NET MVC Razor Views][Tracing in ASP.NET MVC Razor Views]
     (Ablaufverfolgung in den ASP.NET MVC-Razoransichten, in englischer Sprache) Dieser Blogeintrag behandelt neben der Ablaufverfolgung in Razoransichten auch die Erstellung von Fehlerfiltern zur Protokollierung aller Ausnahmefehler in MVC-Anwendungen. Informationen zur Protokollierung aller Ausnahmefehler in Web Forms-Anwendungen finden Sie im Global.asax-Beispiel unter [Vollständiges Beispiel für Fehlerhandler][Vollständiges Beispiel für Fehlerhandler] auf MSDN. Falls Sie in MVC oder Web Forms bestimmte Ausnahmen protokollieren möchten, diese Ausnahmen jedoch vom Standard-Framework behandelt werden sollen, können Sie diese wie im folgenden Beispiel abfangen und erneut auslösen:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

-   [Ablaufprotokollierung und Streamingdiagnose mit der Azure-Befehlszeile (plus Glimpse!)][Ablaufprotokollierung und Streamingdiagnose mit der Azure-Befehlszeile (plus Glimpse!)]
    Ausführen der Visual Studio-Aktionen aus diesem Lernprogramm über die Befehlszeile. [Glimpse][Glimpse] ist ein Tool zum Debuggen von ASP.NET-Anwendungen.
-   [Using Azure Web Site Logging and Diagnostics - with David Ebbo][Using Azure Web Site Logging and Diagnostics - with David Ebbo] (Websiteprotokollierung und Diagnose unter Azure mit David Ebbo, in englischer Sprache) und [Streaming Logs from Azure Web Sites - with David Ebbo][Streaming Logs from Azure Web Sites - with David Ebbo]
     (Protokollstreaming aus Azure-Websites mit David Ebbo, in englischer Sprache): Videos von Scott Hanselman und David Ebbo.

Für die Fehlerprotokollierung können Sie Open Source-Protokollframeworks wie z. B. [ELMAH][ELMAH] verwenden, anstatt Ihren eigenen Ablaufverfolgungscode zu schreiben. Weitere Informationen finden Sie unter [Scott Hanselman's blog posts about ELMAH][Scott Hanselman's blog posts about ELMAH] (Blogeinträge von Scott Hanselmann zu ELMAH, in englischer Sprache).

Sie müssen nicht unbedingt die Ablaufverfolgung von ASP.NET oder System.Diagnostics verwenden, um Streamingprotokolle aus Azure zu erhalten. Der Streamingprotokolldienst für Azure-Websites kann beliebige *.txt*-, *.html*- oder *.log*-Dateien aus dem Ordner *LogFiles* streamen. Sie können also Ihr eigenes Protokollierungssystem erstellen, das in das Dateisystem der Website schreibt: Ihr Datei wird dann automatisch gestreamt und heruntergeladen. Dazu müssen Sie nur den Anwendungscode schreiben, der Dateien im Ordner *d:\\home\\logfiles* erstellt.

### Analyse von Webserverprotokollen

Weitere Informationen zur Analyse von Webserverprotokollen finden Sie in den folgenden Ressourcen:

-   [LogParser][LogParser]
     Ein Tool zum Anzeigen von Daten in Webserverprotokollen (*.log*-Dateien).
-   [Troubleshooting IIS Performance Issues or Application Errors using LogParser][Troubleshooting IIS Performance Issues or Application Errors using LogParser]
     (Problembehandlung von IIS-Leistungsproblemen oder Anwendungsfehlern mithilfe von LogParser, in englischer Sprache) Eine Einführung in das LogParser-Tool, das Sie bei der Analyse von Webserverprotokollen unterstützt.
-   [Blogeinträge von Robert McMurray zum Thema LogParser][Blogeinträge von Robert McMurray zum Thema LogParser]
-   [HTTP-Statuscodes in IIS 7.0, IIS 7.5 und IIS 8.0][HTTP-Statuscodes in IIS 7.0, IIS 7.5 und IIS 8.0]

### Analyse der Ablaufverfolgung fehlgeschlagener Anforderungen

Die Microsoft TechNet-Website enthält einen Abschnitt zum Thema [Ablaufverfolgung fehlgeschlagener Anforderungen][Ablaufverfolgung fehlgeschlagener Anforderungen], der für das Verständnis dieser Protokolle hilfreich ist. Diese Dokumentation konzentriert sich jedoch hauptsächlich auf die Ablaufverfolgung fehlgeschlagener Anforderungen in IIS. Diese Option ist für Azure-Websites nicht verfügbar.

### Debuggen von Cloud-Diensten.

Informationen darüber, wie Sie statt einer Website einen Azure-Cloud-Dienst debuggen, finden Sie unter [Debuggen von Cloud-Diensten][Debuggen von Cloud-Diensten].

  [den Debugmodus]: http://www.visualstudio.com/de-de/get-started/debug-your-app-vs.aspx
  [IntelliTrace]: http://msdn.microsoft.com/library/vstudio/dd264915.aspx
  [Voraussetzungen]: #prerequisites
  [Konfigurieren und Verwalten von Websites]: #sitemanagement
  [Remoteansicht]: #remoteview
  [Remotedebuggen]: #remotedebug
  [Übersicht über Diagnoseprotokolle]: #logsoverview
  [Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen]: #apptracelogs
  [Erstellen von Webserverprotokollen]: #webserverlogs
  [Anzeigen detaillierter Fehlermeldungsprotokolle]: #detailederrorlogs
  [Herunterladen von Dateisystemprotokollen]: #downloadlogs
  [Anzeigen von Speicherprotokollen]: #storagelogs
  [Anzeigen von Protokollen für fehlgeschlagene Anforderungen]: #failedrequestlogs
  [Nächste Schritte]: #nextsteps
  [Erste Schritte mit Azure und ASP.NET]: /de-de/develop/net/tutorials/get-started/
  [Verwalten von Konten, Abonnements und Administratorrollen]: http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert
  [Anzeigeeinstellungen im Server-Explorer]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png
  [Fenster "Azure-Website"]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png
  [Azure-Websites: How Application Strings and Connection Strings Work]: http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx
  [Konfigurieren von Websites]: /de-de/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig
  [Wenig hilfreiche Fehlermeldung]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png
  [1]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png
  [2]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png
  [Web.config-Transformation]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations
  [Datei und Protokolldateien]: ./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png
  [Öffnen der Datei Web.config]: ./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png
  [Bearbeiten der Datei Web.config]: ./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png
  [Detaillierte Fehlermeldung]: ./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png
  [Veröffentlichen im Debugmodus]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png
  [Debugger anfügen]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png
  [Aktivieren von Remotedebuggen im Verwaltungsportal]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png
  [Anzeigen von Variablen im Debugmodus in Azure]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png
  [Info-Seite mit neuem Wert]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png
  [Azure Pricing]: /de-de/pricing/calculator/
  [Schrittweises Durchlaufen für "Nur mein Code" beschränken]: http://msdn.microsoft.com/de-de/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code
  [WebJobs]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Debuggen in Visual Studio]: http://msdn.microsoft.com/de-de/library/vstudio/sc65sadd.aspx
  [System.Diagnostics.Trace]: http://msdn.microsoft.com/de-de/library/system.diagnostics.trace.aspx
  [Ablaufverfolgung im Debugfenster]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png
  [trace]: http://msdn.microsoft.com/de-de/library/vstudio/6915t83k(v=vs.100).aspx
  [trace.axd]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png
  [3]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png
  [Anzeigen der Streamingprotokolle im Kontextmenü]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png
  [4]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png
  [Fehler-Ablaufverfolgung im Ausgabefenster]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png
  [Anwendungsprotokollierung deaktiviert]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png
  [Einstellen des Ablaufverfolgungs-Schweregrads auf Ausführlich]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png
  [Ablaufverfolgungs-Schweregrad Ausführlich]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png
  [Schaltflächen auf der Registerkarte Protokolle]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png
  [Aktivieren der Webserverprotokollierung]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png
  [Angeben, welche Azure-Protokolle überwacht werden sollen]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png
  [Überwachen von Webserverprotokollen]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png
  [Webserverprotokolle im Ausgabefenster]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png
  [Aktivieren der detaillierten Fehlermeldungen]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png
  [Überwachen aller Protokolle]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png
  [Detailliertes Fehlerprotokoll im Ausgabefenster]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png
  [Detailliertes Fehlerprotokoll im Browserfenster]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png
  [5]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png
  [Heruntergeladene Datei]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png
  [6]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png
  [Log Parser]: http://www.microsoft.com/de-de/download/details.aspx?displaylang=en&id=24659
  [Data Storage Options (Building Real-World Cloud Apps with Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options
  [Speicherkonto erstellen]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png
  [Geben Sie eine URL ein]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png
  [7]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png
  [Klicken Sie auf Tabellenspeicher verwalten]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png
  [Tabellenspeicher verwalten]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png
  [Klicken Sie auf Aktualisieren]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png
  [Speicherprotokolle]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png
  [Ablaufverfolgungstabelle im Server-Explorer]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png
  [Speicherprotokolle in der Tabellenansicht]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png
  [Browsing Storage Resources with Server Explorer]: http://msdn.microsoft.com/de-de/library/windowsazure/ff683677.aspx
  [8]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png
  [Aktivieren der Verfolgung fehlgeschlagener Anforderungen]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png
  [Zurücksetzen der FTP-Anmeldeinformationen im Dashboard]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png
  [Neuer FTP-Benutzername und neues Passwort]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png
  [Öffnen Sie den Ordner LogFiles]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png
  [Öffnen Sie den Ordner W3SVC]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png
  [Ordner W3SVC]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png
  [Verfolgung fehlgeschlagener Anforderungen im Browser]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png
  [Problembehandlung in Azure]: /de-de/develop/net/best-practices/troubleshooting/
  [Problembehandlung in Websites]: /de-de/develop/net/best-practices/troubleshooting-web-sites/
  [Aktivieren der Diagnoseprotokollierung für Azure-Websites]: /de-de/develop/net/common-tasks/diagnostics-logging-and-instrumentation/
  [Überwachung von Websites]: /de-de/manage/services/web-sites/how-to-monitor-websites/
  [Konfigurieren von Diagnose und Herunterladen von Protokollen]: /de-de/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [Untersuchen von Speicherlecks in Azure-Websites mit Visual Studio 2013]: http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx
  [Onlinetools für Windows Azure-Websites, die Sie kennen sollten]: /blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/
  [Das Azure-Forum auf der ASP.NET-Website]: http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET
  [Das Azure-Forum auf MSDN]: http://social.msdn.microsoft.com/Forums/windowsazure/
  [StackOverflow.com]: http://www.stackoverflow.com
  [Debugging Tips with Visual Studio 2010]: http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx
  [Einführung in das Remotedebuggen für Azure-Websites]: /blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/
  [Einführung in das Remotedebuggen von Azure-Websites, Teil 2 - Einblick in das Remotedebuggen]: /blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/
  [Einführung in das Remotedebuggen von Azure-Websites, Teil 3 - Mehrinstanzenumgebung und GIT]: /blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/
  [Debuggen des .NET-Back-Ends in Visual Studio]: http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx
  [Überwachung und Telemetrie (Erstellen realer Cloud-Apps mit Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry
  [ASP.NET Tracing]: http://msdn.microsoft.com/de-de/library/ms972204.aspx
  [Trace Listeners]: http://msdn.microsoft.com/de-de/library/4y5y10s7.aspx
  [WebPageTraceListener]: http://msdn.microsoft.com/de-de/library/system.web.webpagetracelistener.aspx
  [Exemplarische Vorgehensweise: Integrieren der ASP.NET-Ablaufverfolgung und der System.Diagnostics-Ablaufverfolgung]: http://msdn.microsoft.com/de-de/library/b0ectfxd.aspx
  [Tracing in ASP.NET MVC Razor Views]: http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx
  [Vollständiges Beispiel für Fehlerhandler]: http://msdn.microsoft.com/de-de/library/bb397417.aspx
  [Ablaufprotokollierung und Streamingdiagnose mit der Azure-Befehlszeile (plus Glimpse!)]: http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx
  [Glimpse]: http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx
  [Using Azure Web Site Logging and Diagnostics - with David Ebbo]: http://www.windowsazure.com/de-de/documentation/videos/azure-web-site-logging-and-diagnostics/
  [Streaming Logs from Azure Web Sites - with David Ebbo]: http://www.windowsazure.com/de-de/documentation/videos/log-streaming-with-azure-web-sites/
  [ELMAH]: http://nuget.org/packages/elmah/
  [Scott Hanselman's blog posts about ELMAH]: http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx
  [LogParser]: http://www.microsoft.com/de-de/download/details.aspx?id=24659
  [Troubleshooting IIS Performance Issues or Application Errors using LogParser]: http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser
  [Blogeinträge von Robert McMurray zum Thema LogParser]: http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/
  [HTTP-Statuscodes in IIS 7.0, IIS 7.5 und IIS 8.0]: http://support.microsoft.com/kb/943891
  [Ablaufverfolgung fehlgeschlagener Anforderungen]: http://www.iis.net/learn/troubleshoot/using-failed-request-tracing
  [Debuggen von Cloud-Diensten]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/windowsazure/ee405479.aspx
