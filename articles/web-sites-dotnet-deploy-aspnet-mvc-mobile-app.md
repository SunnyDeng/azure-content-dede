<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle=".NET ASP.NET MVC 5 mobile website - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />

# Bereitstellen einer mobilen ASP.NET MVC 5-Webanwendung auf Azure-Websites

In diesem Lernprogramm lernen Sie die grundlegenden Schritte kennen, mit denen Sie eine für Mobiltelefone optimierte ASP.NET MVC 5
-Webanwendung erstellen und in Windows Azure bereitstellen können. Für dieses Lernprogramm benötigen Sie
[Visual Studio Express 2013 für Web][Visual Studio Express 2013 für Web]
 oder Visual Studio Professional, falls bereits vorhanden
.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### Sie lernen Folgendes

In diesem Lernprogramm fügen Sie einer einfachen Anwendung zur Auflistung von
Konferenzdaten mobile Funktionen hinzu. Die Anwendung wird dann über das [Starterprojekt][Starterprojekt] bereitgestellt. Der folgende Screenshot zeigt die ASP.NET-Sitzungen in der fertigen
Anwendung, so wie sie im Browseremulator in den Internet Explorer 11 F12-
Entwicklertools angezeigt werden.

![][0]

Sie können die Internet Explorer 11 F12-Entwicklertools und das [Fiddler
-Tool][Fiddler
-Tool] zum Debuggen Ihrer
Anwendung verwenden. 

### Erlernte Fertigkeiten

Folgendes können Sie lernen:

-   Verwenden von Visual Studio 2013 zum direkten Veröffentlichen Ihrer Webanwendung auf einer Windows Azure-Website
-   Verwenden von ASP.NET MVC 5-Vorlagen mit dem CSS Bootstrap Framework, um die
    Anzeige auf mobilen Geräten zu verbessern
-   Erstellen von Ansichten, die auf die mobile Verwendung und spezielle mobile
    Browser wie iPhone und Android ausgerichtet sind
-   Erstellen reaktionsfähiger Ansichten (die auf verschiedene
    Browser unterschiedlicher Geräte reagieren)

## Einrichten der Entwicklungsumgebung

Richten Sie Ihre Entwicklungsumgebung ein, indem Sie das Azure SDK für .NET Framework installieren.

1.  Klicken Sie auf den folgenden Link, um das Azure SDK für .NET zu installieren. Wenn Sie Visual Studio 2013 noch nicht installiert haben, wird es über den Link installiert. Für dieses Lernprogramm ist Visual Studio 2013 erforderlich. [Azure SDK für Visual Studio 2013][Azure SDK für Visual Studio 2013]
2.  Klicken Sie im Fenster "Webplattform-Installer" auf **Installieren**, und setzen Sie die Installation fort.

    ![Webplattform-Installer - Azure SDK für .NET][Webplattform-Installer - Azure SDK für .NET]

Sie werden auch einen Emulator für mobile Browser benötigen. Die folgenden Browser kommen
in Frage:

-   Browseremulator in [Internet Explorer 11 F12-Entwicklertools][Internet Explorer 11 F12-Entwicklertools] (wird in allen Screenshots mobiler
    Browser verwendet). Benutzer-Agent-Zeichenfolgen für Windows Phone 8, Windows Phone 7 und Apple iPad sind voreingestellt.
-   Browseremulator in [Google Chrome DevTools][Google Chrome DevTools]. Der Emulator enthält Voreinstellungen für zahlreiche Android-Geräte sowie für Apple iPhone, Apple iPad und Amazon Kindle Fire. Darüber hinaus emuliert er Touchereignisse.
-   [Opera Mobile Emulator][Opera Mobile Emulator]

Visual Studio-Projekte mit C#-Quellcode sind zur Ergänzung dieses Themas
verfügbar:

-   [Starterprojekt herunterladen][Starterprojekt]
-   [Fertiges Projekt herunterladen][Fertiges Projekt herunterladen]

## Schritte in diesem Lernprogramm

-   [Bereitstellen des Starterprojekts auf einer Windows Azure-Website][Bereitstellen des Starterprojekts auf einer Windows Azure-Website]
-   [Bootstrap CSS Framework][Bootstrap CSS Framework]
-   [Überschreiben der Ansichten, Layouts und Teilansichten][Überschreiben der Ansichten, Layouts und Teilansichten]
-   [Verbessern der Speakers-Liste][Verbessern der Speakers-Liste]
-   [Verbessern der Tags-Liste][Verbessern der Tags-Liste]
-   [Verbessern der Dates-Liste][Verbessern der Dates-Liste]
-   [Verbessern der SessionsTable-Ansicht][Verbessern der SessionsTable-Ansicht]
-   [Verbessern der SessionByCode-Ansicht][Verbessern der SessionByCode-Ansicht]

### <a name="bkmk_DeployStarterProject"></a>Bereitstellen des Starterprojekts auf einer Windows Azure-Website

1.  Laden Sie das [Starterprojekt][Starterprojekt] für die Konferenzanwendung herunter.

2.  Klicken Sie dann im Windows-Explorer mit der rechten Maustaste auf die Datei "Mvc5Mobile.zip", und wählen Sie *Eigenschaften* aus.

3.  Klicken Sie im Dialogfeld **Eigenschaften von Mvc5Mobile.zip** auf die
    Schaltfläche **Blockierung aufheben**. (Durch diese Option wird eine Sicherheitswarnung verhindert
    , die angezeigt wird, wenn Sie versuchen, eine *.zip*- Datei zu verwenden, die Sie aus dem Internet
    heruntergeladen haben.)

4.  Klicken Sie mit der rechten Maustaste auf die Datei *Mvc5Mobile.zip*, und wählen Sie **Alle extrahieren** aus
    , um die Datei zu extrahieren.

5.  Öffnen Sie in Visual Studio die Datei *Mvc5Mobile.sln*.

6.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.

    ![][1]

7.  Klicken Sie in **Web veröffentlichen** auf **Windows Azure-Websites**.

    ![][2]

8.  Klicken Sie auf **Anmelden**.

    ![][3]

9.  Geben Sie Ihren Windows Azure-Benutzernamen ein, und klicken Sie auf **Weiter**.

    ![][4]

10. Geben Sie Ihr Kennwort ein, und klicken Sie auf **Anmelden**.

    ![][5]

11. Im Dialogfeld **Vorhandene Website auswählen** sollte jetzt erkennbar sein, dass Sie angemeldet sind. Klicken Sie auf **Neu**.

    ![][6]

12. Geben Sie im Feld **Websitename** ein eindeutiges Präfix für den Websitenamen ein. Ihr vollqualifizierter Websitename lautet *\<Präfix\>*.azurewebsites.net. Wählen Sie außerdem im Feld **Region** eine Region aus. Klicken Sie dann auf **Erstellen**.

    ![][7]

13. Das Dialogfeld **Web veröffentlichen** enthält jetzt die Websiteeinstellungen für Ihre neue Website. Klicken Sie auf **Veröffentlichen**.

    ![][8]

    Nachdem das Starterprojekt von Visual Studio auf der Windows Azure-Website veröffentlicht wurde, wird der Desktopbrowser mit der Livewebsite angezeigt.

14. Starten Sie Ihren Emulator für mobile Browser, kopieren Sie die URL für
    die Konferenzanwendung (*<prefix>*.azurewebsites.net) in den Emulator, und klicken Sie dann auf
    die obere rechte Schaltfläche, und wählen Sie **Browse by tag**. Wenn Sie Internet
    Explorer 11 als Standardbrowser verwenden, müssen Sie nur `F12` und dann
    `Ctrl+8` drücken und das Browserprofil anschließend in **Windows Phone** ändern. Das
    Bild unten zeigt die Ansicht *AllTags* im Hochformat (nach Auswahl von
    **Browse by tag**).

    ![][9]

> [WACOM.NOTE] Während Sie Ihre MVC 5-Anwendung in Visual Studio debuggen, können Sie Ihre Website erneut auf Windows Azure veröffentlichen, um die Livewebsite direkt in Ihrem mobilen Browser oder einem Browseremulator zu überprüfen.

Die Anzeige ist auf einem mobilen Gerät sehr gut lesbar. Dabei sehen Sie bereits
einige visuelle Effekte, die vom Bootstrap CSS Framework angewendet werden.
 Klicken Sie auf den Link **ASP.NET**.

![][10]

Die Ansicht mit den ASP.NET-Tags wird automatisch von Bootstrap auf
Bildschirmgröße gezoomt. Sie können diese Ansicht für
den mobilen Browser jedoch weiter optimieren. So ist z. B. die Spalte**Date** sehr schwer zu
lesen. Später in diesem Lernprogramm ändern Sie die Ansicht *AllTags*, um sie für
Mobiltelefone zu optimieren.

## <a name="bkmk_bootstrap"></a>Bootstrap CSS Framework

Integrierte Bootstrap-Unterstützung ist eine neue Funktion in der MVC 5-Vorlage. Sie haben
bereits gesehen, wie prompt die verschiedenen Ansichten in Ihrer
Anwendung optimiert werden. Die Navigationsleiste am oberen Rand wird beispielsweise automatisch
reduziert, wenn sich die Browserbreite verringert. Ändern Sie im Desktopbrowser
die Größe des Browserfensters, und verfolgen Sie, wie sich das Erscheinungsbild der
Navigationsleiste ändert. Es handelt sich dabei um den reaktionsfähigen, in
Bootstrap integrierten Webentwurf.

Um das Erscheinungsbild der Web-App in Bootstrap zu überprüfen, öffnen Sie
*App\_Start\\BundleConfig.cs* und kommentieren die Zeilen aus, die
*bootstrap.js* und *bootstrap.css* enthalten. Der folgende Code zeigt die letzten
beiden Anweisungen der `RegisterBundles`-Methode nach der Änderung:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Drücken Sie `Ctrl+F5`, um die Anwendung auszuführen.

Die reduzierbare Navigationsleiste wird jetzt als gewöhnliche
unsortierte Liste dargestellt. Klicken Sie erneut auf **Browse by tag** und dann auf **ASP.NET**.
In der Emulatoransicht für mobile Browser sehen Sie jetzt, dass die Ansicht nicht mehr
auf Bildschirmgröße gezoomt ist und Sie einen Bildlauf zur Seite durchführen müssen, um zur
rechten Seite der Tabelle zu gelangen.

![][11]

Machen Sie die Änderungen rückgängig, und aktualisieren Sie den mobilen Browser
, um sicherzustellen, dass die für Mobiltelefone optimierte Anzeige wiederhergestellt wurde.

Bootstrap ist nicht spezifisch für ASP.NET MVC 5. Diese Features lassen sich in
beliebigen Webanwendungen verwenden. Bootstrap ist jetzt jedoch in die
ASP.NET MVC 5-Projejktvorlage integriert, sodass Ihre MVC 5-Webanwendung Bootstrap
standardmäßig nutzen kann.

Weitere Informationen über Bootstrap finden Sie auf der Website von
[Bootstrap][Bootstrap].

Im nächsten Abschnitt erfahren Sie, wie Sie Ansichten speziell für mobile Browser
bereitstellen.

## <a name="bkmk_overrideviews"></a>Überschreiben der Ansichten, Layouts und Teilansichten

Sie können beliebige Ansichten (einschließlich Layouts und Teilansichten) für
alle mobilen Browser, für einzelne mobile Browser oder für einen beliebigen
spezifischen Browser überschreiben. Um eine mobiltaugliche Ansicht zu erstellen, können Sie eine
Ansichtsdatei kopieren und dem Dateinamen *.Mobile* hinzufügen. Um beispielsweise eine mobile
*Indexansicht* zu erstellen, kopieren Sie *Views\\Home\\Index.cshtml* in
*Views\\Home\\Index.Mobile.cshtml*.

In diesem Abschnitt erstellen Sie eine Layoutdatei speziell für mobile Zwecke.

Kopieren Sie zunächst *Views\\Shared\\\_Layout.cshtml* in
*Views\\Shared\\\_Layout.Mobile.cshtml*. Öffnen Sie *\_Layout.Mobile.cshtml*
, und ändern Sie den Titel von **MVC5 Application** in **MVC5 Application
(Mobile)**.

Entfernen Sie in jedem `Html.ActionLink`-Aufruf für die Navigationsleite "Browse by" in jedem
*ActionLink*. Der folgende Code zeigt das fertige `<ul class="nav navbar-nav">`-Tag der mobilen Layoutdatei.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Kopieren Sie die Datei *Views\\Home\\AllTags.cshtml* in
*Views\\Home\\AllTags.Mobile.cshtml*. Öffnen Sie die neue Datei, und ändern Sie das
`<h2>`-Element von "Tags" in "Tags (M)":

    <h2>Tags (M)</h2>

Navigieren Sie in einem Desktopbrowser und über einen Emulator für mobile Browser zur
Seite **Tags**. Der Emulator für mobile Browser zeigt die beiden Änderungen an,
(den Titel von *\_Layout.Mobile.cshtml* und den Titel von
*AllTags.Mobile.cshtml*).

![][12]

Die Desktopanzeige (mit den Titeln von *\_Layout.cshtml* und
*AllTags.cshtml*) hat sich im Gegensatz dazu nicht geändert.

![][13]

## <a name="bkmk_browserviews"></a>Erstellen von Ansichten für spezielle Browser

Zusätzlich zu den Ansichten speziell für Mobiltelefone und den Desktop können Sie
Ansichten für individuelle Browser erstellen. Beispielsweise können Sie
Ansichten erstellen, die speziell auf den iPhone- oder Android-Browser ausgerichtet sind. In diesem Abschnitt
erstellen Sie ein Layout für den iPhone-Browser und eine iPhone-Version der
Ansicht *AllTags*.

Öffnen Sie die Datei *Global.asax*, und fügen Sie den folgenden Code am Ende der
`Application_Start`-Methode hinzu.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Durch diesen Code wird der neue Anzeigemodus "iPhone" definiert, der mit jeder
eingehenden Anforderung abgeglichen wird. Wenn die eingehende Anforderung die definierte
Bedingung erfüllt (der Benutzer-Agent also die Zeichenfolge
"iPhone" enthält), sucht ASP.NET MVC nach Ansichten, die das Suffix
"iPhone" im Namen enthalten.

> [WACOM.NOTE] Beim Hinzufügen von Anzeigemodi für spezielle mobile Browser wie iPhone und Android müssen Sie für das erste Argument den Wert `0` festlegen (den Wert also am Anfang der Liste einfügen). So stellen Sie sicher, dass der browserspezifische Modus Vorrang vor der Vorlage für Mobiltelefone (\*.Mobile.cshtml) hat. Wenn sich stattdessen die Vorlage für Mobiltelefone am Anfang der Liste befindet, wird diese vor dem eigentlich gewünschten Anzeigemodus ausgewählt (es wird immer die erste Übereinstimmung ausgewählt, und die Vorlage für Mobiltelefone passt für alle mobilen Browser).

Klicken Sie im Code mit der rechten Maustaste auf `DefaultDisplayMode`, wählen Sie **Auflösen** und
dann `using System.Web.WebPages;`. Dadurch wird ein Verweis auf den
`System.Web.WebPages`-Namespace hinzugefügt, in dem die Typen
`DisplayModeProvider` und `DefaultDisplayMode` definiert sind.

![][14]

Alternativ können Sie dem Abschnitt
`using` der Datei die folgende Zeile manuell hinzufügen.

    using System.Web.WebPages;

Speichern Sie die Änderungen. Kopieren Sie die Datei
*Views\\Shared\\\_Layout.Mobile.cshtml* in
*Views\\Shared\\\_Layout.iPhone.cshtml*. Öffnen Sie die neue Datei,
 und ändern Sie den Titel von `MVC5 Application (Mobile)` in
`MVC5 Application (iPhone)`.

Kopieren Sie die Datei *Views\\Home\\AllTags.Mobile.cshtml* in
*Views\\Home\\AllTags.iPhone.cshtml*. Ändern Sie in der neuen Datei
das `<h2>`-Element von "Tags (M)" in "Tags (iPhone)".

Führen Sie die Anwendung aus. Führen Sie einen Emulator für mobile Browser aus, stellen Sie sicher, dass für den zugehörigen Benutzer
-Agent die Option "iPhone" festgelegt ist, und navigieren Sie zur Ansicht *AllTags*. Wenn Sie
den Emulator in den Internet Explorer 11 F12-Entwicklertools verwenden,
 konfigurieren Sie die Emulation wie folgt:

-   Benutzerprofil = **Windows Phone**
-   Benutzer-Agent-Zeichenfolge = **Custom**
-   Benutzerdefinierte Zeichenfolge = **Apple-iPhone5C1/1001.525**

Der folgende Screenshot zeigt die Ansicht *AllTags*, die im
Emulator der Internet Explorer 11 F12-Entwicklertools mit der benutzerdefinierten Benutzer-Agent-Zeichenfolge (einer iPhone 5C-Benutzer-Agent-Zeichenfolge) gerendert wird.

![][15]

Wählen Sie im mobilen Browser den Link **Speakers** aus. Da es keine
mobile Ansicht (*AllSpeakers.Mobile.cshtml*) gibt, wird die Speakers-Standardansicht
(*AllSpeakers.cshtml*) mit der mobilen Layoutansicht
(*\_Layout.Mobile.cshtml*) gerendert. Wie im Folgenden angezeigt, ist der Titel **MVC5 Application
(Mobile)** in *\_Layout.Mobile.cshtml* definiert.

![][16]

Sie können eine standardmäßige (nicht mobile) Ansicht global deaktivieren,
sodass sie in einem mobilen Layout nicht gerendert wird. Dazu legen Sie für `RequireConsistentDisplayMode` in der Datei *Views\\\_ViewStart.cshtml* wie folgt den Wert
`true` fest:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Wenn Sie für `RequireConsistentDisplayMode` den Wert `true` festlegen, wird das mobile Layout
(*\_Layout.Mobile.cshtml*) nur für mobile Ansichten verwendet (die
Ansichtsdatei hat also das Format ***ViewName**.Mobile.cshtml*). Sie können für
`RequireConsistentDisplayMode` auch den Wert `true` festlegen, wenn Ihr mobiles Layout
mit den nicht mobilen Ansichten nicht zufriedenstellend funktioniert. Der folgende Screenshot zeigt,
wie die Seite *Speakers* gerendert wird, wenn für `RequireConsistentDisplayMode` der
 Wert `true` (ohne die Zeichenfolge "(Mobile)" oben in der Navigationsleiste) festgelegt wird.

![][17]

Sie können den konsistenten Anzeigemodus in einer spezifischen Ansicht deaktivieren, indem Sie in der Ansichtsdatei für
`RequireConsistentDisplayMode` den Wert `false` festlegen. Durch das
folgende Markup in der Datei *Views\\Home\\AllSpeakers.cshtml* wird für
`RequireConsistentDisplayMode` der Wert `false` festgelegt:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

In diesem Abschnitt haben wir erläutert, wie Sie mobile Layouts und Ansichten
sowie Layouts und Ansichten für bestimmte Geräte wie iPhone erstellen.
 Der Hauptvorteil von Bootstrap CSS Framework ist jedoch das
reaktionsfähige Layout, durch das ein einzelnes Stylesheet übergreifend auf
den Desktop, das Mobiletelefon und Tabletbrowser angewendet werden kann, um ein konsistentes Erscheinungsbild zu
erzielen. Im nächsten Abschnitt erfahren Sie, wie Sie Bootstrap zum Erstellen
von Ansichten speziell für die mobile Verwendung nutzen.

## <a name="bkmk_Improvespeakerslist"></a> Verbessern der Speakers-Liste

Wie Sie gerade gesehen haben, ist die Ansicht *Speakers* gut lesbar, aber die Links sind
klein und können auf mobilen Geräten schlecht angetippt werden. In diesem Abschnitt
erstellen Sie die für Mobiltelefone optimierte Ansicht *AllSpeakers*, die
große, einfach anzutippende Links sowie ein Suchfeld zum schnellen Auffinden der
Speakers enthält.

Sie können den Bootstrap-Stil [linked list group][linked list group] verwenden, um
die Ansicht *Speakers* zu verbessern. Ersetzen Sie in *Views\\Home\\AllSpeakers.cshtml*
 den Inhalt der Razor-Datei durch den folgenden Code.

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

Durch das `class="list-group"`-Attribut im `<div>`-Tag wird der
Bootstrap-Listenstil und durch das `class="input-group-item"`-Attribut
der Bootstrap-Listenelementstil auf jeden einzelnen Link angewendet.

Aktualisieren Sie den mobilen Browser. Die aktualisierte Ansicht sieht wie folgt aus:

![][18]

Der Bootstrap-Stil [linked list group][linked list group] macht es möglich, dass für jeden Link
 das gesamte Feld anklickbar ist, wodurch sich die Benutzererfahrung erheblich verbessert. Schalten Sie zur
Desktopansicht um, um das konsistente Erscheinungsbild zu sehen.

![][19]

Obwohl die Ansicht des mobilen Browsers optimiert wurde, ist es schwierig, zur
langen Speaker-Liste zu navigieren. Bootstrap bietet standardmäßig keine
Suchfilterfunktion. Sie können diese jedoch durch
wenige Codezeilen hinzufügen. Zunächst fügen Sie der Ansicht ein Suchfeld hinzu und
binden dann den JavaScript-Code für die Filterfunktion ein. Fügen Sie in
*Views\\Home\\AllSpeakers.cshtml* ein \\<form\> -Tag direkt nach dem \\<h2\> -Tag ein, wie im Folgenden dargestellt:

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

Beachten Sie, dass Bootstrap-Stile sowohl auf das `<form>`-Tag als auch auf das `<input>`-Tag angewendet
wurden. Durch das `<span>`-Element wird dem Suchfeld eine Bootstrap-
[Glyphicon][Glyphicon]
hinzugefügt.

Fügen Sie im Ordner *Scripts* die JavaScript-Datei *filter.js* hinzu. Öffnen Sie
die Datei, und fügen Sie den folgenden Code ein:

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

Außerdem müssen Sie "filter.js" in Ihre registrierten Bundle einschließen. Öffnen Sie
*App\_Start\\BundleConfig.cs*, und ändern Sie die ersten Bundles. Ändern Sie die
erste `bundles.Add`-Anweisung (für das **jquery**-Bundle), um
*Scripts\\filter.js* wie folgt einzuschließen:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Das **jquery**-Bundle wird bereits von der *\_Layout*
-Standardansicht gerendert. Später können Sie denselben JavaScript-Code verwenden, um die
Filterfunktionalität auf andere Listenansichten anzuwenden.

Aktualisieren Sie den mobilen Browser, und wechseln Sie zur Ansicht *AllSpeakers*. Geben Sie im
Suchfeld "sc" ein. Die Speakers-Liste sollte jetzt
gemäß Ihrer Suchzeichenfolge gefiltert sein.

![][20]

## <a name="bkmk_improvetags"></a> Verbessern der Tags-Liste

Wie die *Speakers*-Ansicht ist auch die *Tags*-Ansicht gut lesbar, die
Links sind aber klein und auf mobilen Geräten schwer anzutippen. Sie können die *Tags*-Ansicht auf die gleiche Weise reparieren wie die *Speakers*-Ansicht, indem Sie die oben beschriebenen Codeänderungen vornehmen, wobei Sie allerdings die folgende `Html.ActionLink`-Methodensyntax in *Views\\Home\\AllTags.cshtml* verwenden:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Der aktualisierte Desktopbrowser sieht wie folgt aus:

![][21]

Der aktualisierte mobile Browser sieht wie folgt aus:

![][22]

> [WACOM.NOTE] Wenn Sie feststellen, dass die ursprüngliche Listenformatierung weiterhin im mobilen Browser angezeigt wird, und Sie sich fragen, was mit Ihren schönen Bootstrap-Stilen passiert ist, hat dies folgenden Grund: Sie sehen gerade das Ergebnis Ihrer früheren Aktion zur Erstellung spezieller mobiler Ansichten. Nachdem Sie jetzt jedoch das Bootstrap CSS Framework verwenden, um einen reaktionsfähigen Webentwurf zu erstellen, können Sie die auf die mobile Nutzung ausgerichteten Ansichten und Layoutansichten entfernen. Nachdem Sie das erledigt haben, wird im aktualisierten mobilen Browser der Bootstrap-Stil angezeigt.

## <a name="bkmk_improvedates"></a> Verbessern der Dates-Liste

Sie können die *Dates*-Ansicht auf die gleiche Weise verbessern wie die *Speakers*- und die
*Tags*-Ansicht. Dazu verwenden Sie die oben beschriebenen Codeänderungen mit der folgenden `Html.ActionLink`-Methodensyntax in *Views\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Daraufhin wird die folgende aktualisierte Ansicht im mobilen Browser angezeigt:

![][23]

Sie können die *Dates*-Ansicht weiter verbessern, indem Sie die date-time
-Werte nach Datum organisieren. Zu diesem Zweck kann der Bootstramp-Stil
[panels][panels] verwendet werden. Ersetzen Sie
den Inhalt der Datei *Views\\Home\\AllDates.cshtml* durch den
folgenden Code:

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

Durch diesen Code wird ein separates `<div class="panel panel-primary">`-Tag für
jedes Datum in der Liste erstellt, und für die entsprechenden Links wird wie zuvor [linked list group][linked list group]
verwendet. Wenn Sie diesen Code ausführen, sieht der mobile Browser
etwa wie folgt aus:

![][24]

Wechseln Sie zum Desktopbrowser. Auch hier sehen Sie ein konsistentes Erscheinungsbild.

![][25]

## <a name="bkmk_improvesessionstable"></a> Verbessern der SessionsTable-Ansicht

In diesem Abschnitt optimieren Sie die *SessionsTable*-Ansicht für die
mobile Nutzung. Die Änderung ist aufwändiger als die vorherigen Änderungen.

Tippen Sie im mobilen Browser auf die Schaltfläche **Tag**, und geben Sie dann `asp` in
das Suchfeld ein.

![][26]

Tippen Sie auf den Link **ASP.NET**.

![][27]

Wie Sie sehen, ist die Anzeige als Tabelle formatiert, die aktuell
für die Anzeige im Desktopbrowser konzipiert ist. Die Tabelle ist in einem mobilen Browser
jedoch etwas schwer zu lesen. Um dies zu beheben, öffnen Sie
*Views\\Home\\SessionsTable.cshtml* und ersetzen dann den Inhalt der
Datei durch den folgenden Code:

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

-   Er verwendet den Bootstrap-Stil [custom linked list group][custom linked list group]
    , um die Sitzungsinformationen vertikal zu formatieren, sodass alle diese
    Informationen (mithilfe von Klassen wie "list-group-item-text") in einem mobilen Browser gelesen werden können.
-   Er wendet das [grid system][grid system] auf das
    Layout an, damit die Sitzungselemente horizontal im Desktopbrowser
    und vertikal im mobilen Browser ("col-md-4"-Klasse) fließen.
-   Er verwendet die [responsive utilities][responsive utilities], um
    die Sitzungstags bei der Anzeige im mobilen Browser ("hidden-xs"-Klasse) auszublenden.

Sie können auch auf einen Titellink tippen, um zur entsprechenden Sitzung zu wechseln. Das Bild
unten zeigt die Codeänderungen.

![][0]

Das automatisch angewendete Bootstrap-Rastersystem ordnet die
Sitzungen vertikal im mobilen Browser an. Beachten Sie, dass die Tags
nicht angezeigt werden. Wechseln Sie zum Desktopbrowser.

![][28]

Im Desktopbrowser werden die Tags jetzt angezeigt. Außerdem stellen Sie fest, dass die Sitzungselemente durch das angewendete Bootstrap-Rastersystem
in zwei Spalten angeordnet sind. Wenn Sie den
Browser vergrößern, ändert sich die Anordnung in eine Ansicht mit drei Spalten.

## <a name="bkmk_improvesessionbycode"></a>Verbessern der SessionByCode-Ansicht

Zuletzt reparieren Sie die *SessionByCode*-Ansicht, um sie für die mobile Nutzung zu optimieren.

Tippen Sie im mobilen Browser auf die Schaltfläche **Tag**, und geben Sie dann `asp` in
das Suchfeld ein.

![][26]

Tippen Sie auf den Link **ASP.NET**. Die Sitzungen für das ASP.NET-Tag werden angezeigt.

![][0]

Aktivieren Sie den Link **Building a Single Page Application with ASP.NET and
AngularJS**.

![][29]

Die Desktop-Standardansicht ist bereits einwandfrei, Sie können sie jedoch leicht verbessern, indem Sie einige Komponenten der Bootstrap-Benutzeroberfläche verwenden.

Öffnen Sie *Views\\Home\\SessionByCode.cshtml*, und ersetzen Sie den Inhalt durch
das folgende Markup:

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

Aktualisieren Sie den mobilen Browser. Das folgende Bild zeigt die Codeänderungen,
die Sie gerade vorgenommen haben:

![][30]

## Zusammenfassung und Überprüfung

In diesem Lernprogramm haben Sie erfahren, wie Sie mit ASP.NET MVC 5
Webanwendungen für die mobile Nutzung entwickeln. Das umfasst:

-   Bereitstellen einer ASP.NET MVC 5-Anwendung auf einer Windows Azure-Website
-   Verwenden von Bootstrap zum Erstellen eines reaktionsfähigen Weblayouts in der MVC 5
    -Anwendung
-   Überschreiben von Layouts, Ansichten und Teilansichten – global und für eine
    einzelne Ansicht
-   Layoutkontrolle und Durchsetzen partieller Überschreibungen mit der
    `RequireConsistentDisplayMode`-Eigenschaft
-   Erstellen von Ansichten speziell für bestimmte Browser wie den iPhone
    -Browser
-   Anwenden von Boostrap-Stilen im Razor-Code

## Weitere Informationen

-   [Bootstrap][Bootstrap]-Website
-   [Offizieller Bootstrap-Blog][Offizieller Bootstrap-Blog]
-   [Bootstrap-Lernprogramm von Tutorial Republic auf Twitter][Bootstrap-Lernprogramm von Tutorial Republic auf Twitter]
-   [Der Bootstrap-Playground][Der Bootstrap-Playground]
-   [Von W3C empfohlene bewährte Methoden für mobile Webanwendungen][Von W3C empfohlene bewährte Methoden für mobile Webanwendungen]
-   [W3C-Empfehlungskandidaten für Medienabfragen][W3C-Empfehlungskandidaten für Medienabfragen]

<!-- Internal Links --> <!-- External Links --> <!-- Images -->

  [Visual Studio Express 2013 für Web]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Starterprojekt]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
  [0]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
  [Azure SDK für Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Webplattform-Installer - Azure SDK für .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
  [Internet Explorer 11 F12-Entwicklertools]: http://msdn.microsoft.com/de-de/library/ie/dn255001.aspx
  [Google Chrome DevTools]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
  [Opera Mobile Emulator]: http://www.opera.com/developer/tools/mobile/
  [Fertiges Projekt herunterladen]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
  [Bereitstellen des Starterprojekts auf einer Windows Azure-Website]: #bkmk_DeployStarterProject
  [Bootstrap CSS Framework]: #bkmk_bootstrap
  [Überschreiben der Ansichten, Layouts und Teilansichten]: #bkmk_overrideviews
  [Verbessern der Speakers-Liste]: #bkmk_Improvespeakerslist
  [Verbessern der Tags-Liste]: #bkmk_improvetags
  [Verbessern der Dates-Liste]: #bkmk_improvedates
  [Verbessern der SessionsTable-Ansicht]: #bkmk_improvesessionstable
  [Verbessern der SessionByCode-Ansicht]: #bkmk_improvesessionbycode
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
  [Bootstrap]: http://getbootstrap.com/
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
  [15]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
  [16]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
  [17]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
  [linked list group]: http://getbootstrap.com/components/#list-group-linked
  [18]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
  [19]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
  [Glyphicon]: http://getbootstrap.com/components/#glyphicons
  [20]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
  [21]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
  [22]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
  [23]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
  [panels]: http://getbootstrap.com/components/#panels
  [24]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
  [25]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
  [26]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
  [27]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
  [custom linked list group]: http://getbootstrap.com/components/#list-group-custom-content
  [grid system]: http://getbootstrap.com/css/#grid
  [responsive utilities]: http://getbootstrap.com/css/#responsive-utilities
  [28]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
  [29]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
  [30]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
  [Offizieller Bootstrap-Blog]: http://blog.getbootstrap.com/
  [Bootstrap-Lernprogramm von Tutorial Republic auf Twitter]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
  [Der Bootstrap-Playground]: http://www.bootply.com/
  [Von W3C empfohlene bewährte Methoden für mobile Webanwendungen]: http://www.w3.org/TR/mwabp/
  [W3C-Empfehlungskandidaten für Medienabfragen]: http://www.w3.org/TR/css3-mediaqueries/
