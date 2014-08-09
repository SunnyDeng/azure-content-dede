<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 mobile website" pageTitle=".NET ASP.NET MVC 4 mobile web site - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 4,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure web site using mobile features in ASP.NET MVC 4 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Web Sites" authors="tdykstra" solutions="" manager="" editor="" />

Bereitstellen einer mobilen ASP.NET MVC-Webanwendung auf Azure-Websites
=======================================================================

***Von [Rick Anderson](https://twitter.com/RickAndMSFT), aktualisiert am 26. Juni 2013***

In diesem Lernprogramm erlernen Sie die Grundlagen für die Bereitstellung einer Webanwendung auf einer Azure-Website. Zum Zwecke dieses Lernprogramms arbeiten wir mit mobilen Funktionen in einer ASP.NET MVC 4-Webanwendung. Um die Schritte in diesem Lernprogramm auszuführen, können Sie Microsoft Visual Studio 2012 verwenden. Sie können auch [Visual Studio Express 2012](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products) verwenden, eine kostenlose Version von Microsoft Visual Studio.

Sie erhalten Informationen zu folgenden Themen:
-----------------------------------------------

-   Wie die ASP.NET MVC 4-Vorlagen das HTML5-Viewportattribut und adaptives Rendering verwenden, um die Anzeige auf mobilen Geräten zu verbessern
-   Erstellen von Ansichten speziell für die mobile Verwendung
-   Erstellen eines Umschalters zwischen Ansichten, über den Benutzer zwischen einer mobilen Ansicht und einer Desktopansicht der Anwendung wechseln können
-   Bereitstellen der Webanwendung in Azure

Im Laufe dieses Lernprogramms fügen Sie mobile Funktionen zu einer einfachen Anwendung zur Auflistung von Konferenzdaten hinzu, die über das Starterprojekt bereitgestellt wird. Der folgende Screenshot zeigt die Hauptseite der fertigen Anwendung im Windows 7 Phone Emulator.

![Hauptseite der MVC4-Konferenzanwendung](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Einrichten der Entwicklungsumgebung
-----------------------------------

Richten Sie Ihre Entwicklungsumgebung ein, indem Sie das Azure SDK für .NET Framework installieren.

1.  Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren. Wenn Sie Visual Studio 2012 noch nicht installiert haben, wird es über den Link installiert. Für dieses Lernprogramm ist Visual Studio 2012 erforderlich. [Azure SDK für Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Wenn Sie aufgefordert werden, die ausführbare Installationsdatei auszuführen oder zu speichern, klicken Sie auf **Ausführen**.
3.  Klicken Sie im Fenster "Webplattform-Installer" auf **Installieren**, und setzen Sie die Installation fort.

![Webplattform-Installer – Azure SDK für .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png)

Sie werden auch einen Emulator für mobile Browser benötigen. Klicken Sie dazu auf einen der folgenden Links:

-   [Windows 7 Phone Emulator](http://msdn.microsoft.com/de-de/library/ff402530(VS.92).aspx). (Dies ist der Emulator, der in den meisten Screenshots dieses Lernprogramms verwendet wird.)
-   Ändern Sie die Benutzeragentzeichenfolge zur Emulation eines iPhones. Eine Vorgehensweise fnden Sie in [diesem Blog-Eintrag](http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/).
-   [Opera Mobile Emulator](http://www.opera.com/developer/tools/mobile/)
-   [Apple Safari](http://www.apple.com/safari/download/), wobei der Benutzeragent auf iPhone festgelegt ist. Anweisungen zum Festlegen des Benutzeragenten in Safari auf "iPhone" finden Sie im Blog von David Alison unter [How to let Safari pretend it's IE](http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html) (Wie Safari seinen IE emuliert, in englischer Sprache).
-   [FireFox](http://www.bing.com/search?q=firefox+download) mit dem [FireFox User Agent Switcher](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/)

In diesem Tutorial wird Code in C\# gezeigt. Das Starterprojekt und das fertige Projekt werden aber auch in Visual Basic zur Verfügung stehen. Visual Studio-Projekte mit Visual Basic- und C\#-Quellcode sind zur Ergänzung dieses Themas verfügbar:

-   [Starterprojekt herunterladen](http://go.microsoft.com/fwlink/?LinkId=228307)
-   [Fertiges Projekt herunterladen](http://go.microsoft.com/fwlink/?LinkId=228306)

Schritte in diesem Lernprogramm
-------------------------------

-   [Erstellen einer Azure-Website](#bkmk_CreateWebSite)
-   [Einrichten des Starterprojekts](#bkmk_setupstarterproject)
-   [Überschreiben der Ansichten, Layouts und Teilansichten](#bkmk_overrideviews)
-   [Verwenden von jQuery Mobile zur Definition der mobilen Browseroberfläche](#bkmk_usejquerymobile)
-   [Verbessern der Speakers-Liste](#bkmk_Improvespeakerslis)
-   [Erstellen einer mobilen Speakers-Ansicht](#bkmk_mobilespeakersview)
-   [Verbessern der Tags-Liste](#bkmk_improvetags)
-   [Verbessern der Dates-Liste](#bkmk_improvedates)
-   [Verbessern der SessionsTable-Ansicht](#bkmk_improvesessionstable)
-   [Verbessern der SessionByCode-Ansicht](#bkmk_improvesessionbycode)
-   [Bereitstellen der Anwendung auf der Azure-Website](#bkmk_deployapplciation)

### Erstellen einer Website in Azure

Ihre Azure-Website wird in einer freigegebenen Hostingumgebung und damit auf virtuellen Computern (VMs) ausgeführt, die für andere Azure-Clients freigegeben wurden. Eine freigegebene Hostingumgebung ist eine kostengünstige Möglichkeit, mit der Verwendung der Cloud zu beginnen. Später kann die Anwendung skaliert werden, sobald der Webdatenverkehr zunimmt, um die Anforderungen durch die Ausführung dedizierter virtueller Maschinen zu erfüllen. Wenn Sie eine komplexere Architektur benötigen, können Sie eine Migration zu einem Azure-Clouddienst ausführen. Clouddienste werden auf dedizierten VMs ausgeführt, die Sie Ihren Anforderungen entsprechend konfigurieren können.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an. Klicken Sie im Verwaltungsportal auf **Neu**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png)

2.  Klicken Sie auf **Website** und dann auf **Quick Create**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png)

3.  Geben Sie im Schritt **Create a New Web Site** eine Zeichenfolge in das Feld **URL** ein, die als eindeutige URL der Anwendung dienen soll.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png)

    Die vollständige URL besteht aus der hier eingegebenen Zeichenfolge und dem Suffix, das unter dem Textfeld aufgeführt wird. Die Abbildung zeigt "MyMobileMVC4WebSite". Wenn jemand diese URL aber bereits benutzt, müssen Sie eine andere auswählen. Wählen Sie Ihre **REGION** aus.

4.  Klicken Sie auf das Häkchen unten im Feld, um anzugeben, dass Sie fertig sind.

Sie kehren zur Seite "Websites" im Verwaltungsportal zurück. In der Spalte "Status" wird angegeben, dass die Website erstellt wird. Nach einer Weile (normalerweise weniger als eine Minute) wird in der Spalte "Status" angegeben, dass die Website erfolgreich erstellt wurde. In der linken Navigationsleiste wird die Anzahl der Websites in Ihrem Konto neben dem Symbol "Websites" angegeben und die Anzahl der Datenbanken neben dem Symbol "SQL-Datenbanken".

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png)

### Einrichten des Starterprojekts

1.  Laden Sie das [Starterprojekt für die Konferenzanwendung](http://go.microsoft.com/fwlink/?LinkId=228307) herunter.

2.  Klicken Sie dann in Windows-Explorer mit der rechten Maustaste auf die Datei "MvcMobileStarterBeta.zip", und wählen Sie *Eigenschaften* aus.

3.  Wählen Sie im Dialogfeld "MvcMobileRTMStarter.zip" die Schaltfläche "Unlock" aus. (Durch diese Option wird eine Sicherheitswarnung verhindert, die angezeigt wird, wenn Sie versuchen, eine ZIP-Datei zu verwenden, die Sie aus dem Internet heruntergeladen haben.)

    ![Dialogfeld "Eigenschaften"](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png)

4.  Klicken Sie mit der rechten Maustaste auf die Datei "MvcMobile.zip", und wählen Sie "Extract All" aus, um die Datei zu extrahieren.

5.  Öffnen Sie die Datei "MvcMobile.sln" in Visual Studio.

### Ausführen des Starterprojekts

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen. Sie wird in Ihrem Desktopbrowser angezeigt.
2.  Starten Sie Ihren Emulator für mobile Browser, kopieren Sie die URL für die Konferenzanwendung in den Emulator, und klicken Sie dann auf den Link "Browse by tag".

    -   Wenn Sie Windows Phone Emulator verwenden, klicken Sie in die URL-Leiste, und drücken Sie die Pause-Taste, um Tastaturzugriff zu erhalten. Das Bild unten zeigt die Ansicht "AllTags" (durch Auswahl von "Browse by tag").

    ![Seite "Browse by tag"](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png)

Die Anzeige ist auf einem mobilen Gerät sehr gut lesbar. Wählen Sie den Link "ASP.NET" aus.

![Als ASP.NET markierte Sitzungen durchsuchen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png)

Die ASP.NET-Tag-Ansicht ist sehr unübersichtlich. So ist z. B. die Spalte "Date" sehr schwer zu lesen. Später im Lernprogramm werden Sie eine Version der Ansicht "AllTags" erstellen, die speziell für mobile Browser ausgelegt ist und die Anzeige besser lesbar macht.

Überschreiben der Ansichten, Layouts und Teilansichten
------------------------------------------------------

In diesem Abschnitt erstellen Sie eine Layoutdatei speziell für mobile Zwecke.

Eine wichtige neue Funktion in ASP.NET MVC 4 ist ein einfacher Mechanismus, mit dem Sie jede Ansicht (auch Layouts und Teilansichten) für mobile Browser im Allgemeinen, für einen einzelnen mobilen Browser oder für jeden spezifischen Browser überschreiben können. Um eine mobiltaugliche Ansicht zu erstellen, können Sie eine Ansichtsdatei kopieren und ".Mobile" zum Dateinamen hinzufügen. Um beispielsweise eine mobile Indexansicht zu erstellen, kopieren Sie *Views\\Home\\Index.cshtml* in *Views\\Home\\Index.Mobile.cshtml*.

Kopieren Sie zunächst *Views\\Shared\\\_Layout.cshtml* in *Views\\Shared\\\_Layout.Mobile.cshtml*. Öffnen Sie *\_Layout.Mobile.cshtml*, und ändern Sie den Titel von **MVC4 Conference** in **Conference (Mobile)**.

Entfernen Sie in jedem **Html.ActionLink**-Aufruf "Browse by" in jedem Link-ActionLink. Der folgende Code zeigt den fertigen Mittelabschnitt der mobilen Layoutdatei.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conference (Mobile)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

Kopieren Sie die Datei *Views\\Home\\AllTags.cshtml* in *Views\\Home\\AllTags.Mobile.cshtml*. Öffnen Sie die neue Datei, und ändern Sie das &lt;h2\>-Element von "Tags" in "Tags (M)":

     <h2>Tags (M)</h2>

Navigieren Sie in einem Desktopbrowser und über einen Emulator für mobile Browser zur Tags-Seite. Der Emulator für mobile Browser zeigt die beiden Änderungen an, die Sie vorgenommen haben.

![Änderungen an der Tags-Seite anzeigen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png)

Die Desktopansicht dagegen hat sich nicht verändert.

![Tags-Desktopansicht anzeigen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png)

Verwenden von jQuery Mobile zur Definition der mobilen Browseroberfläche
------------------------------------------------------------------------

In diesem Abschnitt installieren Sie das NuGet-Paket "jQuery.Mobile.MVC", wodurch jQuery Mobile und ein Widget zum Umschalten zwischen Ansichten installiert wird.

Die Bibliothek [jQuery Mobile](http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html) enthält ein UI-Framework, das für alle wichtigen mobilen Browser funktioniert. jQuery Mobile wendet progressive Erweiterungen auf mobile Browser an, die CSS und JavaScript unterstützen. Durch progressive Erweiterungen können alle Browser den grundlegenden Inhalt einer Webseite anzeigen, während leistungsfähigere Browser und Geräte eine umfassendere Anzeige ermöglichen. Die JavaScript- und CSS-Dateien, die in jQuery Mobile enthalten sind, formatieren viele Elemente so, dass sie für mobile Browser geeignet sind, ohne das Markupänderungen vorgenommen werden müssen.

1.  Löschen Sie die Datei *Shared\\\_Layout.Mobile.cshtml*, die Sie zuvor erstellt haben.

2.  Benennen Sie Datei *Views\\Home\\AllTags.Mobile.cshtml* in *Views\\Home\\AllTags.Mobile.cshtml.hide* um (Sie werden diese Datei später erneut verwenden.) Da die Datei keine CSHTML-Erweiterung mehr aufweist, wird sie nicht in der ASP.NET MVC-Laufzeit zum Rendern der Ansicht *AllTags* verwendet.

3.  Installieren Sie das NuGet-Paket "jQuery.Mobile.MVC" wie folgt:

    a. Wählen Sie im Menü **Extras** die Konsole **Package Manager** und danach **Library Package Manager** aus.

         ![Library package manager][jquery1]

    b. Geben Sie in der **Package Manager Console** *Install-Package jQuery.Mobile.MVC -version 1.0.0* ein.

         ![Package manager console][jquery2]

Durch das NuGet-Paket "jQuery.Mobile.MVC" wird Folgendes installiert:

-   Die Datei *App\_Start\\BundleMobileConfig.cs*, die erforderlich ist, um auf die hinzugefügten JavaScript- und CSS-Dateien von jQuery zu verweisen. Sie müssen die Anweisungen unten befolgen und auf das in dieser Datei definierte mobile Bundle verweisen.
-   CSS-Dateien von jQuery Mobile
-   Ein ViewSwitcher-Controller-Widget (*Controllers\\ViewSwitcherController.cs)*
-   JavaScript-Dateien von jQuery Mobile
-   Eine jQuery Mobile-formatierte Layoutdatei (*Views\\Shared\_Layout.Mobile.cshtml*)
-   Eine Teilansicht zum Umschalten von Ansichten (*MvcMobile\\Views\\Shared\_ViewSwitcher.cshtml*), die oben auf jeder Seite einen Link anbietet, über den von der Desktopansicht zur mobilen Ansicht und umgekehrt umgeschaltet werden kann.
-   Mehrere PNG- und GIF-Bilddateien im Ordner "Content\\images".

Öffnen Sie die Datei *Global.asax*, und fügen Sie den folgenden Code als letzte Zeile der Methode "Application\_Start" hinzu.

	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

Der folgende Code zeigt die fertige Datei "Global.asax":

    using System; 
    using System.Web.Http; 
    using System.Web.Mvc; 
    using System.Web.Optimization; 
    using System.Web.Routing; 
    using System.Web.WebPages; 
     
    namespace MvcMobile 
    { 
     
        public class MvcApplication : System.Web.HttpApplication 
        { 
            protected void Application_Start() 
            { 
                DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
                { 
                    ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
                        ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
                }); 
                AreaRegistration.RegisterAllAreas(); 
     
                WebApiConfig.Register(GlobalConfiguration.Configuration); 
                FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
                RouteConfig.RegisterRoutes(RouteTable.Routes); 
                BundleConfig.RegisterBundles(BundleTable.Bundles); 
                BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
            } 
        } 
    }

Öffnen Sie die Datei *MvcMobile\\Views\\Shared\\\_Layout.Mobile.cshtml*, und fügen Sie folgendes Markup direkt nach dem *Html.Partial*-Aufruf hinzu:

    <div data-role="header" align="center">
        @Html.ActionLink("Home", "Index", "Home")
        @Html.ActionLink("Date", "AllDates")
        @Html.ActionLink("Speaker", "AllSpeakers")
        @Html.ActionLink("Tag", "AllTags")
    </div>

Der gesamte Mittelabschnitt sieht wie folgt aus:

    <body>
        <div data-role="page" data-theme="a">
            @Html.Partial("_ViewSwitcher")
            <div data-role="header" align="center">
                @Html.ActionLink("Home", "Index", "Home")
                @Html.ActionLink("Date", "AllDates")
                @Html.ActionLink("Speaker", "AllSpeakers")
                @Html.ActionLink("Tag", "AllTags")
            </div>
            <div data-role="header">
                <h1>@ViewBag.Title</h1>
            </div>
            <div data-role="content">
                @RenderSection("featured", false)
                @RenderBody()
            </div>
        </div>
    </body>

Erstellen Sie die Anwendung, und navigieren Sie in Ihrem Emulator für mobile Browser zur Ansicht "AllTags". Sie sehen Folgendes:

![Nach Installation von jQuery über NuGet.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png)

**Hinweis**

Sie können den mobilspezifischen Code debuggen, indem Sie die Benutzeragentenzeichenfolge für IE oder Chrome auf iPhone festlegen und dann die F-12-Entwicklertools verwenden. Wenn in Ihrem mobilen Browser die Links **Home**, **Speaker**, **Tag** und **Date** nicht als Schaltflächen angezeigt werden, sind wahrscheinlich die Verweise auf die jQuery Mobile-Skripte und CSS-Dateien nicht korrekt.

Zusätzlich zu den Formatänderungen sehen Sie **Displaying mobile view** sowie einen Link, über den Sie von der mobilen Ansicht zur Desktopansicht wechseln können. Wählen Sie **Desktop view link** aus, und die Desktopansicht wird angezeigt.

Die Desktopansicht bietet keine Möglichkeit, direkt zurück zur mobilen Ansicht zu navigieren. Dies werden Sie nun beheben. Öffnen Sie die Datei *Views\\Shared\\\_Layout.cshtml*. Fügen Sie gleich unter dem Element &lt;body\> den folgenden Code ein, der das Widget zum Umschalten zwischen Ansichten rendert:

    @Html.Partial("_ViewSwitcher")

Hier der fertige Code:

    <body>
        @Html.Partial("_ViewSwitcher")

        <div id="title">
            <h1> MVC4 Conference </h1>
        </div>

        @*Items removed for clarity.*@
    </body>

Aktualisieren Sie die Ansicht **AllTags** im mobilen Browser. Sie können nun zwischen der Desktop- und mobilen Ansicht hin- und herschalten.

![Navigieren Sie zu den mobilen Ansichten.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png)

**Hinweis**

Sie können den folgenden Code am Ende von "Views\\Shared\\\_ViewSwitcher.cshtml" hinzufügen, um das Debuggen von Ansichten zu erleichtern, wenn ein Browser verwendet wird und die Benutzeragentenzeichenfolge auf ein mobiles Gerät eingestellt ist.

``` {}
    else 
    { 
         @:Not Mobile/Get 
    } 
```

Fügen Sie dann die folgende Überschrift zur Datei "Views\\Shared\\\_Layout.cshtml" hinzu:

``` {}
    <h1>Non Mobile Layout MVC4 Conference</h1>
```

Navigieren Sie in einem Desktopbrowser zur Seite "AllTags". Das Widget zum Umschalten zwischen Ansichten wird in einem Desktopbrowser nicht angezeigt, da es nur der mobilen Layoutseite hinzugefügt wird. Später in diesem Lernprogramm werden Sie erfahren, wie Sie das Widget zur Desktopansicht hinzufügen können.

![Desktopansicht anzeigen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png)

Verbessern der Speakers-Liste
-----------------------------

Wählen Sie im mobilen Browser den Link **Speakers** aus. Da es keine mobile Ansicht (*AllSpeakers.Mobile.cshtml*) gibt, wird die Standardanzeige mit den Rednern (*AllSpeakers.cshtml*) mit der mobilen Layoutansicht (*\_Layout.Mobile.cshtml*) gerendert.

![Mobile Rednerliste anzeigen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png)

Sie können eine standardmäßige (nicht mobile) Ansicht global deaktivieren, sodass sie in einem mobilen Layout nicht gerendert wird, indem Sie "RequireConsistentDisplayMode" in der Datei *Views\_ViewStart.cshtml* auf "true" setzen, siehe folgender Code:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png)

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }

Wenn *RequireConsistentDisplayMode* auf "true" gesetzt ist, wird das mobile Layout (*\_Layout.Mobile.cshtml*) nur für mobile Ansichten verwendet. (Das heißt, die Ansichtsdatei hat das Format von "ViewName.Mobile.cshtml".) Unter Umständen möchten Sie *RequireConsistentDisplayMode* auf "true" setzen, wenn Ihr mobiles Layout nicht gut mit Ihren nicht mobilen Ansichten funktioniert. Der Screenshot unten zeigt, wie die Seite "Speakers" gerendert wird, wenn *RequireConsistentDisplayMode* auf "true" gesetzt ist.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

Sie können den konsistenten Anzeigemodus in einer Ansicht deaktivieren, indem Sie in der Ansichtsdatei *RequireConsistentDisplayMode* auf "false" setzen. Das folgende Markup in der Datei *Views\\Home\\AllSpeakers.cshtml* setzt *RequireConsistentDisplayMode* auf "false":

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }

Erstellen einer mobilen Speakers-Ansicht
----------------------------------------

Wie Sie gerade gesehen haben, ist die Ansicht "Speakers" gut lesbar, aber die Links sind klein und können auf mobilen Geräten schlecht angetippt werden. In diesem Abschnitt erstellen Sie eine Speakers-Ansicht für mobile Zwecke, die wie eine moderne mobile Anwendung ausschaut, das heißt, sie zeigt große, einfach anzutippende Links an und enthält ein Suchfeld zum schnellen Auffinden der Redner.

1.  Kopieren Sie *AllSpeakers.cshtml* in *AllSpeakers.Mobile.cshtml*. Öffnen Sie die Datei *AllSpeakers.Mobile.cshtml*, und entfernen Sie das &lt;h2\>-Überschriftenelement.
2.  Fügen Sie im **&lt;ul\>**-Tag das Datenrollenattribut hinzu, und legen Sie dessen Wert auf *listview* fest. Wie andere Datenattribute macht "data-role" große Listeneinträge besser antippbar (*data-** attributes, \*data-role="listview"*). So schaut das fertige Markup aus:

         @model IEnumerable<string>
         @{
             ViewBag.Title = "All speakers";
         }
         <ul data-role="listview">
             @foreach(var speaker in Model) {
                 <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
             }
         </ul>

3.  Aktualisieren Sie den mobilen Browser. Die aktualisierte Ansicht sieht wie folgt aus:

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png)

4.  Fügen Sie im **&lt;ul\>**-Tag das Datenfilterattribut hinzu, und setzen Sie es auf "true". Der Code unten zeigt das UL-Markup.

        <ul data-role="listview" data-filter="true">

Das folgende Bild zeigt das Suchfilterfeld oben auf der Seite, das aus dem Datenfilterattribut resultiert.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

Während Sie die einzelnen Buchstaben in das Suchfeld eingeben, wird die Liste von jQuery Mobile wie im Bild unten gezeigt gefiltert.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png)

Verbessern der Tags-Liste
-------------------------

Wie die Speakers-Standardansicht ist auch die Tags-Ansicht gut lesbar, die Links sind aber klein und auf mobilen Geräten schwer anzutippen. In diesem Abschnitt optimieren Sie die Tags-Ansicht genau so wie die Speakers-Ansicht.

1.  Benennen Sie die Datei *Views\\Home\\AllTags.Mobile.cshtml.hide* in *Views\\Home\\AllTags.Mobile.cshtml* um. Öffnen Sie die umbenannte Datei, und entfernen Sie das **&lt;h2\>**-Element.

2.  Fügen Sie die Datenrollen- und Datenfilterattribute zum **&lt;ul\>**-Tag wie folgt hinzu:

         <ul data-role="listview" data-filter="true">

    Das Bild unten zeigt die Tags-Seite, gefiltert nach dem Buchstaben "J".

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png)

Verbessern der Dates-Liste
--------------------------

Sie können die Dates-Ansicht verbessern, wie Sie auch die Ansichten **Speakers** und **Tags** optimiert haben, damit sie einfacher auf einem mobilen Gerät verwendet werden kann.

1.  Kopieren Sie die Datei *Views\\Home\\AllDates.Mobile.cshtml* in *Views\\Home\\AllDates.Mobile.cshtml*.
2.  Öffnen Sie die neue Datei, und entfernen Sie das **&lt;h2\>**-Element.
3.  Fügen Sie *data-role="listview"* zum &lt;ul\>-Tag wie folgt hinzu:

         <ul data-role="listview">

Das Bild unten zeigt, wie die Seite **Date** mit dem Datenrollenattribut ausschaut.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png)

Ersetzen Sie den Inhalt der Datei *Views\\Home\\AllDates.Mobile.cshtml* durch folgenden Code:

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>

Dieser Code gruppiert alle Sitzungen nach Tagen. Durch den Code wird ein Listenteiler für jeden neuen Tag erstellt; unter einem Teiler werden für jeden Tag alle Sitzungen aufgeführt. Wenn Sie diesen Code ausführen, sieht das Ergebnis in etwa wie folgt aus:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png)

Verbessern der SessionsTable-Ansicht
------------------------------------

In diesem Abschnitt erstellen Sie eine Ansicht der Sitzungen speziell für die mobile Verwendung. Die Änderungen, die wir hier vornehmen, sind umfassender als in den anderen von uns erstellten Ansichten.

Tippen Sie im mobilen Browser auf die Schaltfläche **Speaker**, und geben Sie dann "Sc" in das Suchfeld ein.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png)

Tippen Sie auf den Link **Scott Hanselman**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Sie sehen, dass die Anzeige in einem mobilen Browser nur schwer lesbar ist. Die Date-Spalte ist kaum zu lesen, und die Tags-Spalte ist gar nicht sichtbar. Um dies zu beheben, kopieren Sie "Views\*Home\\SessionsTable.cshtml\*" in *Views\\Home\\SessionsTable.Mobile.cshtml* und ersetzen den Inhalt der Datei durch folgenden Code:

    @using MvcMobile.Models
    @model IEnumerable<Session>

    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>

Durch den Code werden die Room- und Tags-Spalten entfernt und Titel, Redner und Datum werden vertikal formatiert, sodass all diese Informationen in einem mobilen Browser lesbar sind. Das Bild unten zeigt die Codeänderungen.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png)

Verbessern der SessionByCode-Ansicht
------------------------------------

Schließlich erstellen Sie noch eine mobile Version der Ansicht **SessionByCode**. Tippen Sie im mobilen Browser auf die Schaltfläche **Speaker**, und geben Sie dann "Sc" in das Suchfeld ein.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png)

Tippen Sie auf den Link **Scott Hanselman**. Die Sitzungen von Scott Hanselman werden angezeigt.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Wählen Sie den Link **An Overview of the MS Web Stack of Love** aus.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png)

Die Standarddesktopansicht sieht gut aus, Sie können sie aber noch weiter verbessern.

Kopieren Sie *Views\\Home\\SessionByCode.cshtml* in *Views\\Home\\SessionByCode.Mobile.cshtml*, und ersetzen Sie den Inhalt der Datei *Views\\Home\\SessionByCode.Mobile.cshtml* durch folgendes Markup:

    @model MvcMobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>

    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>

Das neue Markup verwendet das Attribut **data-role**, um das Layout der Ansicht zu optimieren.

Aktualisieren Sie den mobilen Browser. Das folgende Bild zeigt die Codeänderungen, die Sie gerade vorgenommen haben:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png)

Bereitstellen der Anwendung auf der Azure-Website
-------------------------------------------------

1.  Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

    !["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png)

    Der Assistent **Web veröffentlichen** wird geöffnet.

2.  Klicken Sie im Assistenten **Web veröffentlichen** auf der Registerkarte **Profil** auf **Importieren**.

    ![Veröffentlichungseinstellungen importieren](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png)

    Das Dialogfeld **Veröffentlichungsprofil importieren** wird geöffnet.

3.  Wenn Sie Ihr Azure-Abonnement noch nicht in Visual Studio hinzugefügt haben, führen Sie die folgenden Schritte aus. Mit diesen Schritten fügen Sie Ihr Abonnement hinzu, sodass Ihre Website in der Dropdownliste **Aus einer Azure-Website importieren** enthalten ist.

    1.  Klicken Sie im Dialogfeld **Import Publish Profile** auf **Add Azure subscription**.

    ![Azure-Abo hinzufügen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

    1.  Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Download subscription file**.

    ![Abo herunterladen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Speichern Sie im Browserfenster die Datei *.publishsettings*.

    ![Veröffentlichungsdatei herunterladen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)] </br\>

    1.  Klicken Sie im Dialogfeld **Import Azure Subscriptions** auf **Browse**, und navigieren Sie zur Datei *.publishsettings*.

    ![Abo herunterladen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Klicken Sie auf **Importieren**.

    ![Importieren](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

    1.  Wählen Sie im Dialogfeld **Veröffentlichungsprofil importieren** die Option **Aus einer Azure-Website importieren** und dann Ihre Website in der Dropdownliste aus, und klicken Sie auf **OK**.

    ![Import Publish Profile](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png)

    1.  Klicken Sie auf der Registerkarte **Verbindung** auf **Verbindung prüfen**, um sicherzustellen, dass die Einstellungen richtig sind.

    ![Verbindung prüfen](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png)

    1.  Nachdem die Verbindung geprüft wurde, wird neben der Schaltfläche **Verbindung prüfen** ein grünes Häkchen angezeigt. ![Anzeige "Verbindung erfolgreich" und "Weiter" auf der Registerkarte "Verbindung"](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png)

    2.  Sie können die Standardeinstellungen auf dieser Seite übernehmen. Sie stellen eine Buildkonfiguration für die Veröffentlichung bereit und müssen keine Dateien auf dem Zielserver löschen. Der Eintrag **UsersContext (DefaultConnection)** unter **Databases** stammt von der *UsersContext:DbContext*-Klasse, die die DefaultConnection-Zeichenfolge verwendet. Klicken Sie auf **Weiter**.

    ![Anzeige "Verbindung erfolgreich" und "Weiter" auf der Registerkarte "Verbindung"](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png)

    1.  Klicken Sie auf der Registerkarte **Vorschau** auf **Vorschau starten**. Auf der Registerkarte wird eine Liste der Dateien angezeigt, die auf den Server kopiert werden. Die Anzeige der Vorschau ist nicht erforderlich, um die Anwendung zu veröffentlichen, es handelt sich dabei jedoch um eine nützliche Funktion, die zu kennen sich lohnt. In diesem Fall können Sie die Dateiliste, die angezeigt wird, ignorieren. Bei der nächsten Veröffentlichung werden nur die Dateien in der Dateivorschau angezeigt, die geändert wurden.

    ![Schaltfläche "Vorschau starten" auf der Registerkarte "Vorschau"](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png)

    1.  Klicken Sie auf **Veröffentlichen**.

    Die Dateien werden von Visual Studio auf den Azure-Server kopiert. Im Fenster **Ausgabe** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

    1.  Der Standard-Browser öffnet automatisch die URL der bereitgestellten Seite. Die erstellte Anwendung wird nun in der Cloud ausgeführt.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png)

Sie können Ihre live geschaltete Website mit dem Phone Emulator testen, indem Sie im mobilen Browser zur URL der Website navigieren.

