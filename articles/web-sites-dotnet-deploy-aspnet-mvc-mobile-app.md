<properties urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle=".NET ASP.NET MVC 5 mobile Website - Azure-Lernprogramme" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />


# Bereitstellen einer mobilen ASP.NET MVC 5-Webanwendung auf Azure-Websites

In diesem Lernprogramm lernen Sie die grundlegenden Schritte kennen, mit denen Sie eine für Mobiltelefone optimierte ASP.NET MVC 5-Webanwendung erstellen und in Windows Azure bereitstellen können. Für dieses Lernprogramm benötigen Sie 
[Visual Studio Express 2013 für das Web][Visual Studio Express 2013] oder die professionelle Edition von Visual Studio, falls Sie diese
bereits haben.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### Sie lernen Folgendes

Im Laufe dieses Lernprogramms fügen Sie mobile Funktionen zu einer einfachen Anwendung zur Auflistung von Konferenzdaten hinzu, die über da [Starterprojekt][StarterProject]. Der folgende Screenshot zeigt die ASP.NET-Sitzungen in der fertigen Anwendung, so wie sie im Browseremulator in den Internet Explorer 11 F12-Entwicklertools angezeigt werden.

![][FixedSessionsByTag]

Sie können die Internet Explorer 11 F12-Entwicklertools und das [Fiddler-Tool][Fiddler] zum Debuggen Ihrer Anwendung verwenden. 

### Erlernte Fertigkeiten

Folgendes können Sie lernen:

-	Verwenden von Visual Studio 2013 zum direkten Veröffentlichen Ihrer Webanwendung auf einer Windows Azure-Website
-   Verwenden von ASP.NET MVC 5-Vorlagen mit dem CSS Bootstrap Framework, um die Anzeige auf mobilen Geräten zu verbessern
-   Erstellen von Ansichten, die auf die mobile Verwendung und spezielle mobile Browser wie iPhone und Android ausgerichtet sind
-   Erstellen reaktionsfähiger Ansichten (die auf verschiedene Browser unterschiedlicher Geräte reagieren)

<h2>Einrichten der Entwicklungsumgebung</h2>

Richten Sie Ihre Entwicklungsumgebung ein, indem Sie das Azure SDK für .NET Framework installieren. 

1. Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren. Wenn Sie Visual Studio 2013 noch nicht installiert haben, wird es über den Link installiert. Für dieses Lernprogramm ist Visual Studio 2013 erforderlich. [Azure SDK für Visual Studio 2013][AzureSDKVs2013]
1. Klicken Sie im Fenster "Webplattform-Installer" auf **Installieren**, und setzen Sie die Installation fort.

	![Web Platform Installer - Azure SDK for .NET][WebPIAzureSdk23NetVS13]

Sie werden auch einen Emulator für mobile Browser benötigen. Klicken Sie dazu auf einen der folgenden
Links:

-   Browseremulator in [Internet Explorer 11 F12-Entwicklertools][EmulatorIE11] (wird in allen Screenshots mobiler Browser verwendet). Benutzer-Agent-Zeichenfolgen für Windows Phone 8, Windows Phone 7 und Apple iPad sind voreingestellt.
-	Browseremulator in [Google Chrome DevTools][EmulatorChrome]. Der Emulator enthält Voreinstellungen für zahlreiche Android-Geräte sowie für Apple iPhone, Apple iPad und Amazon Kindle Fire. Darüber hinaus emuliert er Touchereignisse.
-   [Opera Mobile Emulator][EmulatorOpera]

Visual Studio-Projekte mit C#-Quellcode sind zur Ergänzung dieses
Themas verfügbar:

-   [Starterprojekt herunterladen][StarterProject]
-   [Fertiges Projekt herunterladen][CompletedProject]

<h2>Schritte in diesem Lernprogramm</h2>

- [Bereitstellen des Starterprojekts auf einer Windows Azure-Website][]
- [Bootstrap CSS Framework][]
- [Überschreiben der Ansichten, Layouts und Teilansichten][]
- [Verbessern der Speakers-Liste][]
- [Verbessern der Tags-Liste][]
- [Verbessern der Dates-Liste][]
- [Verbessern der SessionsTable-Ansicht][]
- [Verbessern der SessionByCode-Ansicht][]

<h3><a name="bkmk_DeployStarterProject"></a>Bereitstellen des Starterprojekts auf einer Windows Azure-Website</h3>

1.	Laden Sie das [Starterprojekt][StarterProject] für die Konferenzanwendung herunter.

2. 	Klicken Sie dann im Windows-Explorer mit der rechten Maustaste auf die Datei "Mvc5Mobile.zip", und wählen Sie *Eigenschaften* aus.

3. 	Wählen Sie im Dialogfeld **Mvc5Mobile.zip-Eigenschaften**
die Schaltfläche **Entsperren**. (Das Entsperren verhindert eine Sicherheitswarnung,
die angezeigt wird, wenn Sie versuchen, eine *.zip*-Datei zu verwenden,
die aus dem Web heruntergeladen wurde.)

4.	Klicken Sie mit der rechten Maustaste auf die Datei *Mvc5Mobile.zip*, und wählen Sie **Alle extrahieren** aus, um die Datei zu extrahieren. 

5. 	Öffnen Sie in Visual Studio die Datei *Mvc5Mobile.sln*.

6.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.

	![][DeployClickPublish]

7.	Klicken Sie in Web veröffentlichen auf **Windows Azure-Websites**.

	![][DeployClickWebSites]

8.	Klicken Sie auf **Anmelden**.

	![][DeploySignIn]

9.	Geben Sie Ihren Windows Azure-Benutzernamen ein, und klicken Sie auf **Weiter**.

	![][DeployUsername]

10.	Geben Sie Ihr Kennwort ein, und klicken Sie auf **Anmelden**.

	![][DeployPassword]

11. Im Dialogfeld Vorhandene Website auswählen sollte jetzt erkennbar sein, dass Sie angemeldet sind. Klicken Sie auf **Neu**.

	![][DeployNewWebsite]  

12. Geben Sie im Feld **Websitename** ein eindeutiges Präfix für den Websitenamen ein. Der voll qualifizierte Websitename lautet *&lt;prefix>*.azurewebsites.net. Wählen Sie außerdem im Feld **Region** eine Region. Klicken Sie dann auf **Erstellen**.

	![][DeploySiteSettings]

13.	Das Dialogfeld Web veröffentlichen enthält jetzt die Websiteeinstellungen für Ihre neue Website. Klicken Sie**Veröffentlichen**.

	![][DeployPublishSite]

	Nachdem das Starterprojekt von Visual Studio auf der Windows Azure-Website veröffentlicht wurde, wird der Desktopbrowser mit der Livewebsite angezeigt.

14.	Starten Sie Ihren Emulator für mobile Browser, kopieren Sie die URL für die Konferenzanwendung (*<prefix>*.azurewebsites.net) in den Emulator, und klicken Sie dann auf die obere rechte Schaltfläche, und wählen Sie **Browse by tag**. Wenn Sie Internet Explorer 11 als Standardbrowser verwenden, müssen Sie nur `F12` und dann `Ctrl+8` drücken und das Browserprofil anschließend in **Windows Phone** ändern. Das Bild unten zeigt die Ansicht *AllTags* im Hochformat (nach Auswahl von **Browse by tag**).

	![][AllTags]

>[WACOM.NOTE] Während Sie Ihre MVC 5-Anwendung in Visual Studio debuggen, können Sie Ihre Website erneut auf Windows Azure veröffentlichen, um die Livewebsite direkt in Ihrem mobilen Browser oder einem Browseremulator zu überprüfen.

Die Anzeige ist auf einem mobilen Gerät sehr gut lesbar. Dabei sehen Sie bereits einige visuelle Effekte, die vom Bootstrap CSS Framework angewendet werden. Klicken Sie auf den Link **ASP.NET**.

![][SessionsByTagASP.NET]

Die Ansicht mit den ASP.NET-Tags wird automatisch von Bootstrap auf Bildschirmgröße gezoomt. Sie können diese Ansicht für den mobilen Browser jedoch weiter optimieren. So ist z. B. die Spalte **Date** ehr schwer zu lesen. Später in diesem Lernprogramm ändern Sie die Ansicht *AllTags*, um sie für Mobiltelefone zu optimieren.

<h2><a name="bkmk_bootstrap"></a>Bootstrap CSS Framework</h2>

Integrierte Bootstrap-Unterstützung ist eine neue Funktion in der MVC 5-Vorlage. Sie haben bereits gesehen, wie prompt die verschiedenen Ansichten in Ihrer Anwendung optimiert werden. Die Navigationsleiste am oberen Rand wird beispielsweise automatisch reduziert, wenn sich die Browserbreite verringert. Ändern Sie im Desktopbrowser die Größe des Browserfensters, und verfolgen Sie, wie sich das Erscheinungsbild der Navigationsleiste ändert. Das ist das in Bootstrap integrierte Responsive Webdesign.

Um das Erscheinungsbild der Web-App in Bootstrap zu überprüfen, öffnen Sie *App\_Start\\BundleConfig.cs* und kommentieren die Zeilen aus, die *bootstrap.js* und *bootstrap.css* enthalten. Der folgende Code zeigt die letzten beiden Anweisungen der `RegisterBundles`-Methode nach der Änderung:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Drücken Sie `STRG+F5`, um die Anwendung auszuführen.
 Die reduzierbare Navigationsleiste wird jetzt als gewöhnliche unsortierte Liste dargestellt. Klicken Sie noch einmal auf **Browse by tag** und dann auf **ASP.NET**. In der Emulatoransicht für mobile Browser sehen Sie jetzt, dass die Ansicht nicht mehr auf Bildschirmgröße gezoomt ist und Sie einen Bildlauf zur Seite durchführen müssen, um zur rechten Seite der Tabelle zu gelangen.

![][SessionsByTagASP.NETNoBootstrap]

Machen Sie die Änderungen rückgängig, und aktualisieren Sie den mobilen Browser, um sicherzustellen, dass die für Mobiltelefone optimierte Anzeige wiederhergestellt wurde.

Bootstrap ist nicht spezifisch für ASP.NET MVC 5. Diese Features lassen sich in beliebigen Webanwendungen verwenden. Bootstrap ist jetzt jedoch in die ASP.NET MVC 5-Projejktvorlage integriert, sodass Ihre MVC 5-Webanwendung Bootstrap standardmäßig nutzen kann.

FWeitere Informationen über Bootstrap finden Sie auf der Website von [Bootstrap][BootstrapSite].

Im nächsten Abschnitt erfahren Sie, wie Sie Ansichten speziell für mobile Browser bereitstellen.

<h2><a name="bkmk_overrideviews"></a>Überschreiben der Ansichten, Layouts und Teilansichten</h2>

Sie können beliebige Ansichten (einschließlich Layouts und Teilansichten) für alle mobilen Browser, für einzelne mobile Browser oder für einen beliebigen spezifischen Browser überschreiben. Um eine mobiltaugliche Ansicht zu erstellen, können Sie eine Ansichtsdatei kopieren und *.Mobile* zum Dateinamen hinzufügen. Um beispielsweise eine mobile Indexansicht zu erstellen, kopieren Sie *Views\\Home\\Index.cshtml* in *Views\\Home\\Index.Mobile.cshtml*.

In diesem Abschnitt erstellen Sie eine Layoutdatei speziell für mobile Zwecke.

Kopieren Sie zu Anfang *Views\\Shared\\\_Layout.cshtml* in
*Views\\Shared\\\_Layout.Mobile.cshtml*. Öffnen Sie *\_Layout.Mobile.cshtml*
und ändern Sie den Titel von **MVC5 Application** in **MVC5 Application
(Mobile)**.

Entfernen Sie in jedem `Html.ActionLink`-Aufruf für die Navigationsleite "Browse by" in jedem *ActionLink*. Der folgende Code zeigt den fertigen `<ul class="nav navbar-nav">`-Tag der mobilen Layoutdatei.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Kopieren Sie die Datei *Views\\Home\\AllTags.cshtml* in
*Views\\Home\\AllTags.Mobile.cshtml*. Öffnen Sie die neue Datei, und ändern Sie das Element
`<h2>` von "Tags" in "Tags (M)":

    <h2>Tags (M)</h2>

Navigieren Sie in einem Desktopbrowser und über einen Emulator für mobile Browser zur Tags-Seite. Der Emulator für mobile Browser zeigt die beiden Änderungen an, (den Titel von *\_Layout.Mobile.cshtml* und den Titel von *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Die Desktopanzeige (mit den Titeln von *\_Layout.cshtml* und 
*AllTags.cshtml*) hat sich im Gegensatz dazu nicht geändert.

![][AllTagsMobile_LayoutMobileDesktop]

<h2><a name="bkmk_browserviews"></a>Erstellen von Ansichten für spezielle Browser</h2>

Zusätzlich zu den Ansichten speziell für Mobiltelefone und den Desktop können Sie Ansichten für individuelle Browser erstellen. Beispielsweise können Sie Ansichten erstellen, die speziell auf den iPhone- oder Android-Browser ausgerichtet sind. In diesem Abschnitt erstellen Sie ein Layout für den iPhone-Browser und eine iPhone-Version der Ansicht *AllTags*.

Öffnen Sie die Datei *Global.asax*, und fügen Sie den folgenden Code am Ende der `Application_Start`-Methode hinzu.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Durch diesen Code wird der neue Anzeigemodus "iPhone" definiert, der mit jeder eingehenden Anforderung abgeglichen wird. Wenn die eingehende Anforderung die definierte Bedingung erfüllt (der Benutzer-Agent also die Zeichenfolge "iPhone" enthält), sucht ASP.NET MVC nach Ansichten, die das Suffix "iPhone" im Namen enthalten.

>[WACOM.NOTE] Beim Hinzufügen von Anzeigemodi für spezielle mobile Browser wie iPhone und Android müssen Sie für das erste Argument den Wert `0` festlegen (den Wert also am Anfang der Liste einfügen). So stellen Sie sicher, dass der browserspezifische Modus Vorrang vor der Vorlage für Mobiltelefone (*.Mobile.cshtml) hat. Wenn sich stattdessen die Vorlage für Mobiltelefone am Anfang der Liste befindet, wird diese vor dem eigentlich gewünschten Anzeigemodus ausgewählt (es wird immer die erste Übereinstimmung ausgewählt, und die Vorlage für Mobiltelefone passt für alle mobilen Browser). 

Klicken Sie im Code mit der rechten Maustaste auf `DefaultDisplayMode`, wählen Sie **Auflösen**, und wählen Sie dann `using System.Web.WebPages;`. Dadurch wird ein Verweis auf den `System.Web.WebPages`-Namespace hinzugefügt, in dem die Typen `DisplayModeProvider` und `DefaultDisplayMode` definiert sind.

![][ResolveDefaultDisplayMode]

Alternativ können Sie dem Abschnitt `using` der Datei die folgende Zeile manuell hinzufügen.

    using System.Web.WebPages;

Speichern Sie die Änderungen. Kopieren Sie die Datei *Views\\Shared\\\_Layout.Mobile.cshtml* in *Views\\Shared\\\_Layout.iPhone.cshtml*. Öffnen Sie die neue Datei und ändern Sie den Titel von `MVC5 Application (Mobile)` auf `MVC5 Application (iPhone)`.

Kopieren Sie die Datei Views\Home\AllTags.Mobile.cshtml in Views\Home\AllTags.iPhone.cshtml. Ändern Sie in der neuen Datei das Element `<h2>` von "Tags (M)" in "Tags (iPhone)".

Führen Sie die Anwendung aus. Führen Sie einen Emulator für mobile Browser aus, stellen Sie sicher, dass für den zugehörigen Benutzer-Agent die Option "iPhone" festgelegt ist, und navigieren Sie zur Ansicht *AllTags*. Wenn Sie den Emulator in den Internet Explorer 11 F12-Entwicklertools verwenden, konfigurieren Sie die Emulation wie folgt:

-   Benutzerprofil = **Windows Phone**
-   Benutzer-Agent-Zeichenfolge = **Custom**
-   Benutzerdefinierte Zeichenfolge = **Apple-iPhone5C1/1001.525**

Der folgende Screenshot zeigt die Ansicht *AllTags*, die im Emulator der Internet Explorer 11 F12-Entwicklertools mit der benutzerdefinierten Benutzer-Agent-Zeichenfolge (einer iPhone 5C-Benutzer-Agent-Zeichenfolge) gerendert wird.

![][AllTagsIPhone_LayoutIPhone]

Wählen Sie im mobilen Browser den Link **Speakers** aus. Da es keine mobile Ansicht (*AllSpeakers.Mobile.cshtml*) gibt, wird die Speakers-Standardansicht (*AllSpeakers.cshtml*) mit der mobilen Layoutansicht (*\_Layout.Mobile.cshtml*) gerendert. Wie im Folgenden angezeigt, ist der Titel **MVC5 Application (Mobile)** in *\_Layout.Mobile.cshtml* definiert.

![][AllSpeakers_LayoutMobile]

Sie können eine standardmäßige (nicht mobile) Ansicht global deaktivieren, sodass sie in einem mobilen Layout nicht gerendert wird, indem Sie `RequireConsistentDisplayMode` in der Datei *Views\\\_ViewStart.cshtml* auf `true` setzen, siehe folgender Code:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Wenn Sie für `RequireConsistentDisplayMode` den Wert `true` festlegen, wird das mobile Layout (*\_Layout.Mobile.cshtml*)nur für mobile Ansichten verwendet (die Ansichtsdatei hat also das Format ***ViewName**.Mobile.cshtml*). Sie können für `RequireConsistentDisplayMode` auch den Wert `true` festlegen, wenn Ihr mobiles Layout mit den nicht mobilen Ansichten nicht zufriedenstellend funktioniert. Der folgende Screenshot zeigt, wie die Seite *Speakers* gerendert wird, wenn für `RequireConsistentDisplayMode` der Wert `true` (ohne die Zeichenfolge "(Mobile)" oben in der Navigationsleiste) festgelegt wird.

![][AllSpeakers_LayoutMobileOverridden]

Sie können den konsistenten Anzeigemodus in einer spezifischen Ansicht deaktivieren, indem Sie in der Ansichtsdatei `RequireConsistentDisplayMode` auf `false` setzen. Das folgende Markup in der Datei *Views\\Home\\AllSpeakers.cshtml* setzt `RequireConsistentDisplayMode` auf `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

In diesem Abschnitt haben wir gesehen, wie man mobile Layouts und Ansichten erstellt sowie wie man das für spezifische Geräte wie das iPhone tut. Der Hauptvorteil des Bootstrap CSS-Frameworks liegt allerdings im Responsive Layout, denn damit lässt sich ein einziges Stylesheet for Desktop-, Handy- und Tabletbrowser verwenden und damit ein konsistentes Erscheinungsbild erreichen. Im nächsten Abschnitt erfahren Sie, wie Sie Bootstrap zum Erstellen von Ansichten speziell für die mobile Verwendung nutzen.

<h2><a name="bkmk_Improvespeakerslist"></a> Verbessern der Speakers-Liste</h2>

Wie Sie gerade gesehen haben, ist die Ansicht *Speakers* gut lesbar, aber die Links sind klein und können auf mobilen Geräten schlecht angetippt werden. In diesem Abschnitt erstellen Sie die für Mobiltelefone optimierte Ansicht AllSpeakers, die große, einfach anzutippende Links sowie ein Suchfeld zum schnellen Auffinden der Speakers enthält.

Sie können den Bootstrap-Stil [linked list group][] verwenden, um die Ansicht *Speakers* zu verbessern. Ersetzen Sie in *Views\\Home\\AllSpeakers.cshtml* den Inhalt der Razor-Datei durch den folgenden Code.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

Das Attribut `class="list-group"` im `<div>`-Tag wendet den Listenstil von Bootstrap, und das Attribut `class="input-group-item"` wendet den Bootstrap-Stil für Listenobjekte auf jeden Link an.

Aktualisieren Sie den mobilen Browser. Die aktualisierte Ansicht sieht wie folgt aus:

![][AllSpeakersFixed]

Der Bootstrap-Stil [linked list group][] macht es möglich, dass für jeden Link das gesamte Feld anklickbar ist, wodurch sich die Benutzererfahrung erheblich verbessert. Schalten Sie zur Desktopansicht um, um das konsistente Erscheinungsbild zu sehen.

![][AllSpeakersFixedDesktop]

Obwohl die Ansicht des mobilen Browsers optimiert wurde, ist es schwierig, zur langen Speaker-Liste zu navigieren. Bootstrap bietet standardmäßig keine Suchfilterfunktion. Sie können diese jedoch durch wenige Codezeilen hinzufügen. Zunächst fügen Sie der Ansicht ein Suchfeld hinzu und binden dann den JavaScript-Code für die Filterfunktion ein. Fügen Sie in *Views\\Home\\AllSpeakers.cshtml* einen \<form\> -Tag direkt nach dem \<h2\> -Tag ein, wie unten dargestellt:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Beachten Sie, dass auf die beiden Tags `<form>` und `<input>` der Bootstrap-Stil angewandt wird. Durch das `<span>`-Element wird dem Suchfeld eine Bootstrap-[glyphicon][] hinzugefügt.

Fügen Sie im Ordner *Scripts* die JavaScript-Datei *filter.js* hinzu. Öffnen Sie die Datei, und fügen Sie den folgenden Code ein:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

Außerdem müssen Sie "filter.js" in Ihre registrierten Bundle einschließen. Öffnen Sie *App\_Start\\BundleConfig.cs*, und ändern Sie die ersten Bundles. Ändern Sie die erste `bundles.Add` -Anweisung (für das **jquery**-Bundle), um *Scripts\\filter.js* wie folgt einzuschließen:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Das **jquery**-Bundle wird bereits von der *\_Layout*-Standardansicht gerendert. Später können Sie denselben JavaScript-Code verwenden, um die Filterfunktionalität auf andere Listenansichten anzuwenden.

Aktualisieren Sie den mobilen Browser, und wechseln Sie zur Ansicht *AllSpeakers*. Geben Sie im Suchfeld "sc" ein. Die Speakers-Liste sollte jetzt gemäß Ihrer Suchzeichenfolge gefiltert sein.

![][AllSpeakersFixedSearchBySC]

<h2><a name="bkmk_improvetags"></a> Verbessern der Tags-Liste</h2>

Wie die *Speakers*-Ansicht ist auch die *Tags*-Ansicht gut lesbar, die Links sind aber klein und auf mobilen Geräten schwer anzutippen. Sie können die *Tags*-Ansicht auf die gleiche Weise reparieren wie die *Speakers*-Ansicht, indem Sie die oben beschriebenen Codeänderungen vornehmen, wobei Sie allerdings die folgende `Html.ActionLink`-Methodensyntax in *Views\\Home\\AllTags.cshtml* verwenden:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Der aktualisierte Desktopbrowser sieht wie folgt aus:

![][AllTagsFixedDesktop]

Der aktualisierte mobile Browser sieht wie folgt aus: 

![][AllTagsFixed]

>[WACOM.NOTE] Wenn Sie feststellen, dass die ursprüngliche Listenformatierung weiterhin im mobilen Browser angezeigt wird, und Sie sich fragen, was mit Ihren schönen Bootstrap-Stilen passiert ist, hat dies folgenden Grund: Sie sehen gerade das Ergebnis Ihrer früheren Aktion zur Erstellung spezieller mobiler Ansichten. Nachdem Sie jetzt jedoch das Bootstrap CSS Framework verwenden, um einen reaktionsfähigen Webentwurf zu erstellen, können Sie die auf die mobile Nutzung ausgerichteten Ansichten und Layoutansichten entfernen. Nachdem Sie das erledigt haben, wird im aktualisierten mobilen Browser der Bootstrap-Stil angezeigt.

<h2><a name="bkmk_improvedates"></a> Verbessern der Dates-Liste</h2>

Sie können die Dates-Ansicht auf die gleiche Weise verbessern wie die Speakers- und die Tags-Ansicht. Dazu verwenden Sie die oben beschriebenen Codeänderungen mit der folgenden `Html.ActionLink`-Methodensyntax in *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Daraufhin wird die folgende aktualisierte Ansicht im mobilen Browser angezeigt:

![][AllDatesFixed]

Sie können die *Dates*-Ansicht weiter verbessern, indem Sie die date-time-Werte nach Datum organisieren. Zu diesem Zweck kann der Bootstrap-Stil [panels][] verwendet werden. Ersetzen Sie den Inhalt der Datei *Views\\Home\\AllDates.cshtml* durch den folgenden Code:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Dieser Code erstellt einen separaten `<div class="panel panel-primary">` -Tag für jedes Datum in der Liste erstellt, und für die entsprechenden Links wird wie zuvor [linked list group][] verwendet. Wenn Sie diesen Code ausführen, sieht der mobile Browser etwa wie folgt aus:

![][AllDatesFixed2]

Wechseln Sie zum Desktopbrowser. Auch hier sehen Sie ein konsistentes Erscheinungsbild.

![][AllDatesFixed2Desktop]

<h2><a name="bkmk_improvesessionstable"></a> Verbessern der SessionsTable-Ansicht</h2>

In diesem Abschnitt optimieren Sie die *SessionsTable*-Ansicht für die mobile Nutzung. Die Änderung ist aufwändiger als die vorherigen Änderungen.

Tippen Sie im mobilen Browser auf die Schaltfläche **Tag**, und geben Sie dann `asp` in das Suchfeld ein.

![][AllTagsFixedSearchByASP]

Tippen Sie auf den Link **ASP.NET**.

![][SessionsTableTagASP.NET]

Wie Sie sehen, ist die Anzeige als Tabelle formatiert, die aktuell für die Anzeige im Desktopbrowser konzipiert ist. Die Tabelle ist in einem mobilen Browser jedoch etwas schwer zu lesen. Um dies zu beheben, öffnen Sie *Views\\Home\\SessionsTable.cshtml* und ersetzen dann den Inhalt der Datei durch den folgenden Code:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

Der Code erfüllt drei Aufgaben:

-   Verwendet den Bootstrap-Stil [custom linked list group][], um die Sitzungsinformationen vertikal zu formatieren, sodass alle diese Informationen (mithilfe von Klassen wie "list-group-item-tex") in einem mobilen Browser gelesen werden können.
-   Wendet das [grid system][] auf das Layout an, damit die Sitzungselemente horizontal im Desktopbrowser und vertikal im mobilen Browser ("col-md-4"-Klasse) fließen.
-   Verwendet die [responsive utilities][], um die Sitzungstags bei der Anzeige im mobilen Browser ("hidden-xs"-Klasse) auszublenden.

Sie können auch auf einen Titellink tippen, um zur entsprechenden Sitzung zu wechseln. Das Bild unten zeigt die Codeänderungen.

![][FixedSessionsByTag]

Das automatisch angewendete Bootstrap-Rastersystem ordnet die Sitzungen vertikal im mobilen Browser an. Beachten Sie, dass die Tags nicht angezeigt werden. Wechseln Sie zum Desktopbrowser.

![][SessionsTableFixedTagASP.NETDesktop]

Im Desktopbrowser werden die Tags jetzt angezeigt. Sie können außerdem sehen, dass von Ihnen angewandte Bootstrap-Rastersystem
die Sitzungslemente in zwei Spalten anordnet. Wenn Sie den Browser vergrößern, ändert sich die Anordnung in eine Ansicht mit drei Spalten.

<h2><a name="bkmk_improvesessionbycode"></a> Verbessern der SessionByCode-Ansicht</h2>

Zuletzt reparieren Sie die *SessionByCode*-Ansicht, um sie für die mobile Nutzung zu optimieren.

Tippen Sie im mobilen Browser auf die Schaltfläche **Tag**, und geben Sie dann `asp` in das Suchfeld ein.

![][AllTagsFixedSearchByASP]

Tippen Sie auf den Link **ASP.NET**. Die Sitzungen für das ASP.NET-Tag werden angezeigt.

![][FixedSessionsByTag]

Aktivieren Sie den Link **Building a Single Page Application with ASP.NET and
AngularJS**.

![][SessionByCode3-644]

Die Desktop-Standardansicht ist bereits einwandfrei, Sie können sie jedoch leicht verbessern, indem Sie einige Komponenten der Bootstrap-Benutzeroberfläche verwenden.

Öffnen Sie *Views\\Home\\SessionByCode.cshtml* und ersetzen Sie den Inhalt durch das folgende Markup:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

Das neue Markup verwendet den Bootstrap-Stil "Panels", um die mobile Ansicht zu optimieren. 

Aktualisieren Sie den mobilen Browser. Das folgende Bild zeigt die Codeänderungen, die Sie gerade vorgenommen haben:

![][SessionByCodeFixed3-644]

Zusammenfassung und Überprüfung
------------------

In diesem Lernprogramm haben Sie erfahren, wie Sie mit ASP.NET MVC 5 Webanwendungen für die mobile Nutzung entwickeln. Das umfasst:

-	Bereitstellen einer ASP.NET MVC 5-Anwendung auf einer Windows Azure-Website
-   Verwenden von Bootstrap zum Erstellen eines reaktionsfähigen Weblayouts in der MVC 5-Anwendung
-   Überschreiben von Layouts, Ansichten und Teilansichten - global und für eine einzelne Ansicht
-   Layoutkontrolle und Durchsetzen partieller Überschreibungen mit der `RequireConsistentDisplayMode`-Eigenschaft
-   Erstellen von Ansichten speziell für bestimmte Browser wie den iPhone-Browser
-   Anwenden von Boostrap-Stilen im Razor-Code

Siehe auch
--------

-   [9 Grundprinzipien des Responsive Webdesign][http://blog.froont.com/9-basic-principles-of-responsive-web-design/]
-   [Bootstrap][BootstrapSite]-Website
-   [Offizieller Bootstrap-Blog][]
-   [Bootstrap-Lernprogramm von Tutorial Republic auf Twitter][]
-   [Der Bootstrap-Playground][]
-   [Von W3C empfohlene bewährte Methoden für mobile Webanwendungen][]
-   [WW3C-Empfehlungskandidaten für Medienabfragen][]

<!-- Internal Links -->
[Bereitstellen des Starterprojekts auf einer Windows Azure-Website]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Überschreiben der Ansichten, Layouts und Teilansichten]: #bkmk_overrideviews
[Erstellen von Ansichten für spezielle Browser]:#bkmk_browserviews
[Verbessern der Speakers-Liste]: #bkmk_Improvespeakerslist
[Verbessern der Tags-Liste]: #bkmk_improvetags
[Verbessern der Dates-Liste]: #bkmk_improvedates
[Verbessern der SessionsTable-Ansicht]: #bkmk_improvesessionstable
[Verbessern der SessionByCode-Ansicht]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/de-de/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[linked list group]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[panels]: http://getbootstrap.com/components/#panels
[custom linked list group]: http://getbootstrap.com/components/#list-group-custom-content
[grid system]: http://getbootstrap.com/css/#grid
[responsive utilities]: http://getbootstrap.com/css/#responsive-utilities
[Offizieller Bootstrap-Blog]: http://blog.getbootstrap.com/
[Bootstrap-Lernprogramm von Tutorial Republic auf Twitter]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Der Bootstrap-Playground]: http://www.bootply.com/
[Von W3C empfohlene bewährte Methoden für mobile Webanwendungen]: http://www.w3.org/TR/mwabp/
[WW3C-Empfehlungskandidaten für Medienabfragen]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png

<!--HONumber=35.1-->

<!--HONumber=35.1-->
