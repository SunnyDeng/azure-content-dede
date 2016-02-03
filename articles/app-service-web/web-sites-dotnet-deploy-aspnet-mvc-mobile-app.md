<properties 
	pageTitle="Bereitstellen einer mobilen ASP.NET MVC 5-Web-App in Azure App Service." 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Web-App in Azure App Service mithilfe von mobilen Features in einer ASP.NET-MVC 5-Webanwendung bereitstellen." 
	services="app-service" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="cephalin;riande"/>


# Bereitstellen einer mobilen ASP.NET MVC 5-Web-App in Azure App Service.

In diesem Lernprogramm lernen Sie die grundlegenden Schritte kennen, mit denen Sie eine für Mobiltelefone optimierte ASP.NET MVC-5-Web-App erstellen und in Azure App Service bereitstellen. Für dieses Lernprogramm benötigen Sie [Visual Studio Express 2013 für Web][Visual Studio Express 2013] oder Visual Studio Professional, falls bereits vorhanden. Sie können [Visual Studio 2015] verwenden, allerdings unterscheiden sich die Screenshots und Sie müssen ASP.NET 4.x-Vorlagen nutzen.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Sie lernen Folgendes

Im Laufe dieses Lernprogramms fügen Sie mobile Funktionen zu einer einfachen Anwendung zur Auflistung von Konferenzdaten hinzu, die über das [Starterprojekt][StarterProject] bereitgestellt wird. Der folgende Screenshot zeigt die ASP.NET-Sitzungen in der fertigen Anwendung, so wie sie im Browseremulator in den Internet Explorer 11 F12-Entwicklertools angezeigt werden.

![][FixedSessionsByTag]

Sie können die Internet Explorer 11 F12-Entwicklertools und das [Fiddler-Tool][Fiddler] zum Debuggen Ihrer Anwendung verwenden.

## Erlernte Fertigkeiten

Folgendes können Sie lernen:

-	Verwenden von Visual Studio 2013 zum direkten Veröffentlichen Ihrer Webanwendung in einer Web-App in Azure App Service
-   Verwenden von ASP.NET MVC 5-Vorlagen mit dem CSS Bootstrap Framework, um die Anzeige auf mobilen Geräten zu verbessern
-   Erstellen von Ansichten, die auf die mobile Verwendung und spezielle mobile Browser wie iPhone und Android ausgerichtet sind
-   Erstellen reaktionsfähiger Ansichten (die auf verschiedene Browser unterschiedlicher Geräte reagieren)

## Einrichten der Entwicklungsumgebung

Richten Sie Ihre Entwicklungsumgebung ein, indem Sie das Azure SDK für .NET 2.5.1 oder höher installieren.

1. Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren. Wenn Sie Visual Studio 2013 noch nicht installiert haben, wird es über den Link installiert. Für dieses Lernprogramm ist Visual Studio 2013 erforderlich. [Azure SDK für Visual Studio 2013][AzureSDKVs2013]
1. Klicken Sie im Fenster "Webplattform-Installer" auf **Installieren**, und setzen Sie die Installation fort.

Sie werden auch einen Emulator für mobile Browser benötigen. Klicken Sie dazu auf einen der folgenden Links:

-   Browseremulator in [Internet Explorer 11 F12-Entwicklertools][EmulatorIE11] (wird in allen Screenshots mobiler Browser verwendet). Benutzer-Agent-Zeichenfolgen für Windows Phone 8, Windows Phone 7 und Apple iPad sind voreingestellt.
-	Browseremulator in [Google Chrome DevTools][EmulatorChrome]. Der Emulator enthält Voreinstellungen für zahlreiche Android-Geräte sowie für Apple iPhone, Apple iPad und Amazon Kindle Fire. Darüber hinaus emuliert er Touchereignisse.
-   [Emulator für Opera Mobile][EmulatorOpera]

Visual Studio-Projekte mit C#-Quellcode sind zur Ergänzung dieses Themas verfügbar:

-   [Herunterladen eines Starterprojekts][StarterProject]
-   [Herunterladen eines fertigen Projekts][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>Bereitstellen des Starterprojekts in einer Azure-Web-App

1.	Laden Sie das [Starterprojekt][StarterProject] für die Konferenzanwendung herunter.

2. 	Klicken Sie dann im Windows-Explorer mit der rechten Maustaste auf die heruntergeladene zip-Datei, und wählen Sie *Eigenschaften* aus.

3. 	Klicken Sie im Dialogfeld **Eigenschaften** auf die Schaltfläche **Blockierung aufheben**. (Durch diese Option wird eine Sicherheitswarnung verhindert, die angezeigt wird, wenn Sie versuchen, eine *.zip*-Datei zu verwenden, die Sie aus dem Internet heruntergeladen haben.)

4.	Klicken Sie mit der rechten Maustaste auf die zip-Datei, und wählen Sie **Alle extrahieren** aus, um die Datei zu entpacken.

5. 	Öffnen Sie in Visual Studio die Datei *C#\\Mvc5Mobile.sln*.

6.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.

	![][DeployClickPublish]

7.	Klicken Sie in „Web veröffentlichen“ auf **Microsoft Azure App Service**.

	![][DeployClickWebSites]

8.	Wenn Sie noch nicht bei Azure angemeldet sind, klicken Sie auf **Konto hinzufügen**.

	![][DeploySignIn]

9.	Folgen Sie den Aufforderungen auf dem Bildschirm, um sich bei Ihrem Azure-Konto anzumelden.

11. Die App Service-Dialogfeld sollte nach der Anmeldung angezeigt werden. Klicken Sie auf **Neu**.

	![][DeployNewWebsite]

12. Geben Sie im Feld **Web-App-Name** ein eindeutiges Präfix für den App-Namen ein. Der voll qualifizierte Web-App-Name lautet *&lt;Präfix>*.azurewebsites.net. Wählen Sie unter **Ressourcengruppe** eine neue oder vorhandene Ressourcengruppe aus. Klicken Sie dann auf **Neu**, um einen neuen App Service-Plan zu erstellen.

	![][DeploySiteSettings]

13. Konfigurieren Sie den neuen App Service-Plan, und klicken Sie auf **OK**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7a.png)

13. Klicken Sie im Dialogfeld „App Service erstellen“ auf **Erstellen**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7b.png)

13.	Nach dem Erstellen der Azure-Ressourcen enthält das Dialogfeld „Web veröffentlichen“ jetzt die Einstellungen für Ihre neue App. Klicken Sie auf **Veröffentlichen**.

	![][DeployPublishSite]

	Nachdem das Starterprojekt von Visual Studio in der Azure-Web-App veröffentlicht wurde, wird der Desktopbrowser mit der Live-Web-App angezeigt.

14.	Starten Sie Ihren Emulator für mobile Browser, kopieren Sie die URL für die Konferenzanwendung (*<prefix>*.azurewebsites.net) in den Emulator, klicken Sie dann auf die obere rechte Schaltfläche, und wählen Sie **Durchsuchen nach Markierung**. Wenn Sie Internet Explorer 11 als Standardbrowser verwenden, müssen Sie nur `F12` und dann `Ctrl+8` drücken und das Browserprofil anschließend in **Windows Phone** ändern. Das Bild unten zeigt die Ansicht *AllTags* im Hochformat (nach Auswahl von **Durchsuchen nach Markierung**).

	![][AllTags]

>[AZURE.TIP]Während Sie Ihre MVC 5-Anwendung in Visual Studio debuggen, können Sie Ihre Web-App erneut auf Azure veröffentlichen, um die Live-Web-App direkt in Ihrem mobilen Browser oder einem Browseremulator zu überprüfen.

Die Anzeige ist auf einem mobilen Gerät sehr gut lesbar. Sie können zudem bereits einige der vom Bootstrap CSS Framework angewendeten visuellen Effekte sehen. Klicken Sie auf den Link **ASP.NET**.

![][SessionsByTagASP.NET]

Die Ansicht mit den ASP.NET-Tags wird automatisch von Bootstrap auf Bildschirmgröße gezoomt. Sie können diese Ansicht für den mobilen Browser jedoch weiter optimieren. So ist z. B. die Spalte **Datum** sehr schwer zu lesen. Später in diesem Lernprogramm ändern Sie die Ansicht *AllTags*, um sie für die mobile Nutzung zu optimieren.

##<a name="bkmk_bootstrap"></a>Bootstrap CSS-Framework

Integrierte Bootstrap-Unterstützung ist eine neue Funktion in der MVC 5-Vorlage. Sie haben bereits gesehen, wie prompt die verschiedenen Ansichten in Ihrer Anwendung optimiert werden. Die Navigationsleiste am oberen Rand wird beispielsweise automatisch reduziert, wenn sich die Browserbreite verringert. Ändern Sie im Desktopbrowser die Größe des Browserfensters, und verfolgen Sie, wie sich das Erscheinungsbild der Navigationsleiste ändert. Das ist das in Bootstrap integrierte Responsive Webdesign.

Um das Erscheinungsbild der Web-App in Bootstrap zu überprüfen, öffnen Sie *App_Start\BundleConfig.cs* und kommentieren die Zeilen aus, die *bootstrap.js* und *bootstrap.css* enthalten. Der folgende Code zeigt die letzten beiden Anweisungen der `RegisterBundles`-Methode nach der Änderung:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Drücken Sie `Ctrl+F5`, um die Anwendung auszuführen.

Die reduzierbare Navigationsleiste wird jetzt als gewöhnliche unsortierte Liste dargestellt. Klicken Sie erneut auf **Durchsuchen nach Markierung** und dann auf **ASP.NET**. In der Emulatoransicht für mobile Browser sehen Sie jetzt, dass die Ansicht nicht mehr auf Bildschirmgröße gezoomt ist und Sie einen Bildlauf zur Seite durchführen müssen, um zur rechten Seite der Tabelle zu gelangen.

![][SessionsByTagASP.NETNoBootstrap]

Machen Sie die Änderungen rückgängig, und aktualisieren Sie den mobilen Browser, um sicherzustellen, dass die für Mobiltelefone optimierte Anzeige wiederhergestellt wurde.

Bootstrap ist nicht spezifisch für ASP.NET MVC 5. Diese Features lassen sich in beliebigen Webanwendungen verwenden. Bootstrap ist jetzt jedoch in die ASP.NET MVC 5-Projejktvorlage integriert, sodass Ihre MVC 5-Webanwendung Bootstrap standardmäßig nutzen kann.

Weitere Informationen über Bootstrap finden Sie auf der Website von [Bootstrap][BootstrapSite].

Im nächsten Abschnitt erfahren Sie, wie Sie Ansichten speziell für mobile Browser bereitstellen.

##<a name="bkmk_overrideviews"></a>Überschreiben der Ansichten, Layouts und Teilansichten

Sie können beliebige Ansichten (einschließlich Layouts und Teilansichten) für alle mobilen Browser, für einzelne mobile Browser oder für einen beliebigen spezifischen Browser überschreiben. Um eine mobiltaugliche Ansicht zu erstellen, können Sie eine Ansichtsdatei kopieren und *.Mobile* zum Dateinamen hinzufügen. Um beispielsweise eine mobile *Index*-Ansicht zu erstellen, können Sie *Views\Home\Index.cshtml* nach *Views\Home\Index.Mobile.cshtml* kopieren.

In diesem Abschnitt erstellen Sie eine Layoutdatei speziell für mobile Zwecke.

Kopieren Sie zunächst *Views\Shared\_Layout.cshtml* in *Views\Shared\_Layout.Mobile.cshtml*. Öffnen Sie *_Layout.Mobile.cshtml* und ändern Sie den Titel von **MVC5-Anwendung** in **MVC5-Anwendung (Mobile)**.

Entfernen Sie in jedem `Html.ActionLink`-Aufruf für die Navigationsleiste "Durchsuchen nach" in jedem *ActionLink*. Der folgende Code zeigt das fertige `<ul class="nav navbar-nav">`-Tag der mobilen Layoutdatei.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Kopieren Sie die Datei *Views\Home\AllTags.cshtml* in *Views\Home\AllTags.Mobile.cshtml*. Öffnen Sie die neue Datei, und ändern Sie das `<h2>` -Element von "Tags" in "Tags (M)":

    <h2>Tags (M)</h2>

Navigieren Sie in einem Desktopbrowser und über einen Emulator für mobile Browser zur Tags-Seite. Der Emulator für mobile Browser zeigt die beiden Änderungen an (den Titel von *_Layout.Mobile.cshtml* und den Titel von *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Die Desktopanzeige (mit den Titeln von *_Layout.cshtml* und *AllTags.cshtml*) hat sich im Gegensatz dazu nicht geändert.

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a> Erstellen von Ansichten für spezielle Browser

Zusätzlich zu den Ansichten speziell für Mobiltelefone und den Desktop können Sie Ansichten für individuelle Browser erstellen. Beispielsweise können Sie Ansichten erstellen, die speziell auf den iPhone- oder Android-Browser ausgerichtet sind. In diesem Abschnitt erstellen Sie ein Layout für den iPhone-Browser und eine iPhone-Version der *AllTags*-Ansicht.

Öffnen Sie die Datei *Global.asax*, und fügen den folgenden Code am Ende der `Application_Start`-Methode hinzu.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Durch diesen Code wird der neue Anzeigemodus "iPhone" definiert, der mit jeder eingehenden Anforderung abgeglichen wird. Wenn die eingehende Anforderung die definierte Bedingung erfüllt (der Benutzer-Agent also die Zeichenfolge "iPhone" enthält), sucht ASP.NET MVC nach Ansichten, die das Suffix "iPhone" im Namen enthalten.

>[AZURE.NOTE]Beim Hinzufügen von Anzeigemodi für spezielle mobile Browser wie iPhone und Android müssen Sie für das erste Argument den Wert `0` festlegen (den Wert also am Anfang der Liste einfügen). So stellen Sie sicher, dass der browserspezifische Modus Vorrang vor der Vorlage für Mobiltelefone (*.Mobile.cshtml) hat. Wenn sich stattdessen die Vorlage für Mobiltelefone am Anfang der Liste befindet, wird diese vor dem eigentlich gewünschten Anzeigemodus ausgewählt (es wird immer die erste Übereinstimmung ausgewählt, und die Vorlage für Mobiltelefone passt für alle mobilen Browser).

Klicken Sie im Code mit der rechten Maustaste auf `DefaultDisplayMode`, wählen Sie **Auflösen** und dann `using System.Web.WebPages;`. Dadurch wird ein Verweis auf den `System.Web.WebPages`-Namespace hinzugefügt, in dem die Typen `DisplayModeProvider` und `DefaultDisplayMode` definiert sind.

![][ResolveDefaultDisplayMode]

Alternativ können Sie dem Abschnitt `using` der Datei die folgende Zeile manuell hinzufügen.

    using System.Web.WebPages;

Speichern Sie die Änderungen. Kopieren Sie die Datei *Views\Shared\_Layout.Mobile.cshtml* nach *Views\Shared\_Layout.iPhone.cshtml*. Öffnen Sie die neue Datei, und ändern Sie den Titel von `MVC5 Application (Mobile)` in `MVC5 Application (iPhone)`.

Kopieren Sie die Datei *Views\Home\AllTags.Mobile.cshtml* nach *Views\Home\AllTags.iPhone.cshtml*. Ändern Sie in der neuen Datei das `<h2>`-Element von "Tags (M)" in "Tags (iPhone)".

Führen Sie die Anwendung aus. Führen Sie einen Emulator für mobile Browser aus, stellen Sie sicher, dass für den zugehörigen Benutzer-Agent die Option "iPhone" festgelegt ist, und navigieren Sie zur Ansicht *AllTags*. Wenn Sie den Emulator in den Internet Explorer 11 F12-Entwicklertools verwenden, konfigurieren Sie die Emulation wie folgt:

-   Benutzerprofil = **Windows Phone**
-   Benutzer-Agent-Zeichenfolge = **Custom**
-   Benutzerdefinierte Zeichenfolge = **Apple-iPhone5C1/1001.525**

Der folgende Screenshot zeigt die Ansicht *AllTags*, die im Emulator der Internet Explorer 11 F12-Entwicklertools mit der benutzerdefinierten Benutzer-Agent-Zeichenfolge (einer iPhone 5C-Benutzer-Agent-Zeichenfolge) gerendert wird.

![][AllTagsIPhone_LayoutIPhone]

Wählen Sie im mobilen Browser den Link **Speakers** aus. Da es keine mobile Ansicht (*AllSpeakers.Mobile.cshtml*) gibt, wird die Speakers-Standardansicht (*AllSpeakers.cshtml*) mit der mobilen Layoutansicht (*_Layout.Mobile.cshtml*) gerendert. Wie im Folgenden angezeigt, ist der Titel **MVC5 Application (Mobile)** in *_Layout.Mobile.cshtml* definiert.

![][AllSpeakers_LayoutMobile]

Sie können eine standardmäßige (nicht mobile) Ansicht global deaktivieren, sodass sie in einem mobilen Layout nicht gerendert wird. Dazu legen Sie für `RequireConsistentDisplayMode` in der Datei *Views\_ViewStart.cshtml* den Wert `true` fest wie folgt:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Wenn Sie für `RequireConsistentDisplayMode` den Wert `true` festlegen, wird das mobile Layout (*_Layout.Mobile.cshtml*) nur für mobile Ansichten verwendet (die Ansichtsdatei hat also das Format ***ViewName**.Mobile.cshtml*). Sie können für `RequireConsistentDisplayMode` auch den Wert `true` festlegen, wenn Ihr mobiles Layout mit den nicht mobilen Ansichten nicht zufriedenstellend funktioniert. Der folgende Screenshot zeigt, wie die Seite *Speakers* gerendert wird, wenn für `RequireConsistentDisplayMode` der Wert `true` festgelegt wird (ohne die Zeichenfolge "(Mobile)" oben in der Navigationsleiste).

![][AllSpeakers_LayoutMobileOverridden]

Sie können den konsistenten Anzeigemodus in einer spezifischen Ansicht deaktivieren, indem Sie in der Ansichtsdatei für `RequireConsistentDisplayMode` den Wert `false` festlegen. Durch das folgende Markup in der Datei *Views\Home\AllSpeakers.cshtml* wird für `RequireConsistentDisplayMode` der Wert `false` festgelegt:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

In diesem Abschnitt haben wir gesehen, wie man mobile Layouts und Ansichten erstellt sowie wie man das für spezifische Geräte wie das iPhone tut. Der Hauptvorteil von Bootstrap CSS Framework ist jedoch das reaktionsfähige Layout, durch das ein einzelnes Stylesheet übergreifend auf den Desktop, das Mobiletelefon und Tabletbrowser angewendet werden kann, um ein konsistentes Erscheinungsbild zu erzielen. Im nächsten Abschnitt erfahren Sie, wie Sie Bootstrap zum Erstellen von Ansichten speziell für die mobile Verwendung nutzen.

##<a name="bkmk_Improvespeakerslist"></a>Verbessern der Speakers-Liste

Wie Sie gerade gesehen haben, ist die Ansicht *Speakers* gut lesbar, aber die Links sind klein und können auf mobilen Geräten schlecht angetippt werden. In diesem Abschnitt erstellen Sie die für Mobiltelefone optimierte Ansicht *AllSpeakers*, die große, einfach anzutippende Links sowie ein Suchfeld zum schnellen Auffinden der Speakers enthält.

Sie können den Bootstrap-Stil [linked list group][] verwenden, um die Ansicht *Speakers* zu verbessern. Ersetzen Sie in *Views\Home\AllSpeakers.cshtml* den Inhalt der Razor-Datei durch den folgenden Code.

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

Durch das `class="list-group"`- Attribut im `<div>`-Tag wird der Bootstrap-Listenstil und durch das `class="input-group-item"`-Attribut der Bootstrap-Listenelementstil auf jeden einzelnen Link angewendet.

Aktualisieren Sie den mobilen Browser. Die aktualisierte Ansicht sieht wie folgt aus:

![][AllSpeakersFixed]

Der Bootstrap-Stil [linked list group][] ermöglicht es, dass für jeden Link das gesamte Feld anklickbar ist, wodurch sich die Benutzererfahrung erheblich verbessert. Schalten Sie zur Desktopansicht um, um das konsistente Erscheinungsbild zu sehen.

![][AllSpeakersFixedDesktop]

Obwohl die Ansicht des mobilen Browsers optimiert wurde, ist es schwierig, zur langen Speaker-Liste zu navigieren. Bootstrap bietet standardmäßig keine Suchfilterfunktion. Sie können diese jedoch durch wenige Codezeilen hinzufügen. Zunächst fügen Sie der Ansicht ein Suchfeld hinzu und binden dann den JavaScript-Code für die Filterfunktion ein. Fügen Sie in *Views\Home\AllSpeakers.cshtml* direkt nach dem \<h2\> Tag ein \<form\> Tag hinzu, wie unten dargestellt:

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

Beachten Sie, dass auf die beiden Tags `<form>` und `<input>` der Bootstrap-Stil angewandt wird. Durch das Element `<span>` wird dem Suchfeld eine Bootstrap-[glyphicon][] hinzugefügt.

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

Außerdem müssen Sie "filter.js" in Ihre registrierten Bundle einschließen. Öffnen Sie *App_Start\BundleConfig.cs*, und ändern Sie die ersten Bundles. Ändern Sie die erste `bundles.Add`-Anweisung (für das**jquery**-Bundle), um *Scripts\filter.js* wie folgt einzuschließen:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Das **jquery**-Bundle wird bereits von der *_Layout*-Standardansicht gerendert. Später können Sie denselben JavaScript-Code verwenden, um die Filterfunktionalität auf andere Listenansichten anzuwenden.

Aktualisieren Sie den mobilen Browser, und wechseln Sie zur Ansicht *AllSpeakers*. Geben Sie im Suchfeld "sc" ein. Die Speakers-Liste sollte jetzt gemäß Ihrer Suchzeichenfolge gefiltert sein.

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a> Verbessern der Tags-Liste

Wie die *Speakers*-Ansicht ist auch die *Tags*-Ansicht gut lesbar, aber die Links sind klein und auf mobilen Geräten schwer anzutippen. Sie können die *Tags*-Ansicht auf die gleiche Weise reparieren wie die *Speakers*-Ansicht, indem Sie die weiter oben beschriebenen Codeänderungen vornehmen, wobei Sie allerdings die folgende `Html.ActionLink`-Methodensyntax in *Views\Home\AllTags.cshtml* verwenden:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Der aktualisierte Desktopbrowser sieht wie folgt aus:

![][AllTagsFixedDesktop]

Der aktualisierte mobile Browser sieht wie folgt aus:

![][AllTagsFixed]

>[AZURE.NOTE]Wenn Sie feststellen, dass die ursprüngliche Listenformatierung weiterhin im mobilen Browser angezeigt wird, und Sie sich fragen, was mit Ihren schönen Bootstrap-Stilen passiert ist, hat dies folgenden Grund: Sie sehen gerade das Ergebnis Ihrer früheren Aktion zur Erstellung spezieller mobiler Ansichten. Nachdem Sie jetzt jedoch das Bootstrap CSS Framework verwenden, um einen reaktionsfähigen Webentwurf zu erstellen, können Sie die auf die mobile Nutzung ausgerichteten Ansichten und Layoutansichten entfernen. Nachdem Sie das erledigt haben, wird im aktualisierten mobilen Browser der Bootstrap-Stil angezeigt.

##<a name="bkmk_improvedates"></a> Verbessern der Dates-Liste

Sie können die *Dates*-Ansicht auf die gleiche Weise verbessern wie die *Speakers*- und die *Tags*-Ansicht. Dazu verwenden Sie die oben beschriebenen Codeänderungen mit der folgenden `Html.ActionLink`-Methodensyntax in *Views\Home\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Daraufhin wird die folgende aktualisierte Ansicht im mobilen Browser angezeigt:

![][AllDatesFixed]

Sie können die *Dates*-Ansicht weiter verbessern, indem Sie die date-time-Werte nach Datum organisieren. Zu diesem Zweck kann der Bootstrap-Stil [panels][] verwendet werden. Ersetzen Sie den Inhalt der Datei *Views\Home\AllDates.cshtml* durch den folgenden Code:

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

Durch diesen Code wird ein separates `<div class="panel panel-primary">`-Tag für jedes Datum in der Liste erstellt, und für die entsprechenden Links wird wie zuvor [linked list group][] verwendet. Wenn Sie diesen Code ausführen, sieht der mobile Browser etwa wie folgt aus:

![][AllDatesFixed2]

Wechseln Sie zum Desktopbrowser. Auch hier sehen Sie ein konsistentes Erscheinungsbild.

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a> Verbessern der SessionsTable-Ansicht

In diesem Abschnitt optimieren Sie die *SessionsTable*-Ansicht für die mobile Nutzung. Die Änderung ist aufwändiger als die vorherigen Änderungen.

Tippen Sie im mobilen Browser auf die Schaltfläche **Tag**, und geben Sie dann `asp` in das Suchfeld ein.

![][AllTagsFixedSearchByASP]

Tippen Sie auf den Link **ASP.NET**.

![][SessionsTableTagASP.NET]

Wie Sie sehen, ist die Anzeige als Tabelle formatiert, die aktuell für die Anzeige im Desktopbrowser konzipiert ist. Die Tabelle ist in einem mobilen Browser jedoch etwas schwer zu lesen. Um dies zu beheben, öffnen Sie *Views\Home\SessionsTable.cshtml* und ersetzen dann den Inhalt der Datei durch den folgenden Code:

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

-   Er verwendet den Bootstrap-Stil [custom linked list group][], um die Sitzungsinformationen vertikal zu formatieren, sodass alle diese Informationen (mithilfe von Klassen wie "list-group-item-text") in einem mobilen Browser gelesen werden können.
-   Er wendet das [Rastersystem][] auf das Layout an, damit die Sitzungselemente horizontal im Desktopbrowser und vertikal im mobilen Browser ("col-md-4"-Klasse) fließen.
-   Er verwendet die [responsiven Dienstprogramme][], um die Sitzungstags bei der Anzeige im mobilen Browser ("hidden-xs"-Klasse) auszublenden.

Sie können auch auf einen Titellink tippen, um zur entsprechenden Sitzung zu wechseln. Das Bild unten zeigt die Codeänderungen.

![][FixedSessionsByTag]

Das automatisch angewendete Bootstrap-Rastersystem ordnet die Sitzungen vertikal im mobilen Browser an. Beachten Sie, dass die Tags nicht angezeigt werden. Wechseln Sie zum Desktopbrowser.

![][SessionsTableFixedTagASP.NETDesktop]

Im Desktopbrowser werden die Tags jetzt angezeigt. Außerdem stellen Sie fest, dass die Sitzungselemente durch das angewendete Bootstrap-Rastersystem in zwei Spalten angeordnet sind. Wenn Sie den Browser vergrößern, ändert sich die Anordnung in eine Ansicht mit drei Spalten.

##<a name="bkmk_improvesessionbycode"></a>Verbessern der SessionByCode-Ansicht

Zuletzt reparieren Sie die *SessionByCode*-Ansicht, um sie für die mobile Nutzung zu optimieren.

Tippen Sie im mobilen Browser auf die Schaltfläche **Tag**, und geben Sie dann `asp` in das Suchfeld ein.

![][AllTagsFixedSearchByASP]

Tippen Sie auf den Link **ASP.NET**. Die Sitzungen für das ASP.NET-Tag werden angezeigt.

![][FixedSessionsByTag]

Aktivieren Sie den Link **Erstellen einer Anwendung mit einer Seite mithilfe von ASP.NET und AngularJS**.

![][SessionByCode3-644]

Die Desktop-Standardansicht ist bereits einwandfrei, Sie können sie jedoch leicht verbessern, indem Sie einige Komponenten der Bootstrap-Benutzeroberfläche verwenden.

Öffnen Sie *Views\Home\SessionByCode.cshtml*, und ersetzen Sie den Inhalt durch das folgende Markup:

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

## Zusammenfassung und Überprüfung

In diesem Lernprogramm haben Sie erfahren, wie Sie mit ASP.NET MVC 5 Webanwendungen für die mobile Nutzung entwickeln. Das umfasst:

-	Bereitstellen einer ASP.NET MVC 5-Anwendung in einer App Service-Web-App
-   Verwenden von Bootstrap zum Erstellen eines reaktionsfähigen Weblayouts in der MVC 5-Anwendung
-   Überschreiben von Layouts, Ansichten und Teilansichten – global und für eine einzelne Ansicht
-   Layoutkontrolle und Durchsetzen partieller Überschreibungen mit der `RequireConsistentDisplayMode`-Eigenschaft
-   Erstellen von Ansichten speziell für bestimmte Browser wie den iPhone-Browser
-   Anwenden von Bootstrap-Stilen im Razor-Code

## Weitere Informationen

-   [9 Grundprinzipien des Responsive Webdesigns](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Bootstrap][BootstrapSite]
-   [Offizieller Bootstrap-Blog][]
-   [Twitter-Bootstrap-Lernprogramm von Tutorial Republic][]
-   [Der Bootstrap-Playground][]
-   [Von W3C empfohlene bewährte Methoden für mobile Webanwendungen][]
-   [W3C-Empfehlungskandidaten für Medienabfragen][]

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]: #bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
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
[Rastersystem]: http://getbootstrap.com/css/#grid
[responsiven Dienstprogramme]: http://getbootstrap.com/css/#responsive-utilities
[Offizieller Bootstrap-Blog]: http://blog.getbootstrap.com/
[Twitter-Bootstrap-Lernprogramm von Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Der Bootstrap-Playground]: http://www.bootply.com/
[Von W3C empfohlene bewährte Methoden für mobile Webanwendungen]: http://www.w3.org/TR/mwabp/
[W3C-Empfehlungskandidaten für Medienabfragen]: http://www.w3.org/TR/css3-mediaqueries/

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
 

<!---HONumber=AcomDC_0114_2016--->