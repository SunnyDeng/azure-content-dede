<properties 
	pageTitle="Integrieren einer Azure-Web-App mit Azure CDN" 
	description="Ein Lernprogramm, in dem Sie erfahren, wie Sie eine Azure-Web-App bereitstellen, die Inhalte mithilfe eines integrierten Azure CDN-Endpunkts zur Verfügung stellt." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="cephalin"/>

# Integrieren einer Azure-Web-App mit Azure CDN #
<a name="intro"></a>

Azure App Service kann in [Azure CDN](http://azure.microsoft.com/services/cdn/) integriert werden und die in App Service Web Apps integrierten globalen Skalierungsfunktionen erweitern, indem Ihre Web-App-Inhalte global von Serverknoten in der Nähe Ihrer Kunden zur Verfügung gestellt werden (eine aktualisierte Liste aller aktuellen Knotenstandorte finden Sie [hier](http://msdn.microsoft.com/library/azure/gg680302.aspx)). Diese Integration steigert die Leistung Ihrer Azure App Service-Web-App erheblich und sorgt für eine deutliche Verbesserung der weltweiten Benutzerfreundlichkeit der Web-App.

Die Integration von Azure-Web-Apps in Azure CDN bietet folgende Vorteile:

- Integration der Inhaltsbereitstellung (Bilder, Skripts und Stylesheets) als Teil des [kontinuierlichen Bereitstellungsprozesses](web-sites-publish-source-control.md) Ihrer Azure-Web-App
- Einfaches Durchführen von Upgrades für die NuGet-Pakete in Ihrer Web-App in Azure App Service, z. B. jQuery- oder Bootstrap-Versionen 
- Verwalten der Webanwendung und des vom CDN verarbeiteten Inhalts über dieselbe Visual Studio-Oberfläche
- Integrieren von ASP.NET-Bündelung und -Minimierung in Azure CDN

## Sie erstellen Folgendes ##

Sie stellen eine Web-App in Azure App Service unter Verwendung der ASP.NET-MVC-Standardvorlage in Visual Studio bereit, fügen Code hinzu, um Inhalt aus einem integrierten Azure CDN bereitzustellen, z. B. ein Bild, Ergebnisse von Controlleraktionen und die JavaScript- und CSS-Standarddateien. Außerdem schreiben Sie Code zum Konfigurieren des Fallbackmechanismus für verarbeitete Bundles für den Fall, dass das CDN offline ist.

## Sie benötigen Folgendes ##

Für dieses Lernprogramm ist Folgendes erforderlich:

-	Ein aktives [Microsoft Azure-Konto](http://azure.microsoft.com/account/)
-	Visual Studio 2013 mit [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen: + Sie können [kostenlos ein Azure-Konto erstellen](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F): – Sie erhalten ein Guthaben, das Sie zum Ausprobieren der kostenpflichtigen Azure-Dienste nutzen können. Sie können das Konto behalten und weiterhin kostenlose Azure-Dienste wie z. B. Web-App nutzen, wenn das Guthaben aufgebraucht ist. + Sie können von [Vorteilen für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) profitieren. – Über Ihr MSDN-Abonnement erhalten Sie jeden Monat Gutschriften, die Sie für kostenpflichtige Azure-Dienste einsetzen können.

<a name="deploy"></a>
## Bereitstellen einer Azure-Web-App mit einem integrierten CDN-Endpunkt ##

In diesem Abschnitt stellen Sie die standardmäßige ASP.NET-MVC-Anwendungsvorlage in Visual Studio 2013 für eine Azure-Web-App bereit und integrieren sie anschließend in einen neuen CDN-Endpunkt. Befolgen Sie die nachstehenden Anweisungen:

1. Erstellen Sie in Visual Studio 2013 eine neue ASP.NET-Webanwendung über die Menüleiste, indem Sie **Datei > Neu > Projekt > Web > ASP.NET-Webanwendung** aufrufen. Geben Sie einen Namen ein, und klicken Sie auf **OK**.

	![](media/app-service-with-cdn/1-new-project.png)

3. Wählen Sie **MVC** aus, und klicken Sie auf **Abonnements verwalten**.

	![](media/app-service-with-cdn/2-webapp-template.png)

4. Klicken Sie auf **Anmelden**.

	![](media/app-service-with-cdn/3-manage-subscription.png)

6. Melden Sie sich auf der Anmeldeseite mit dem Microsoft-Konto an, das Sie zum Aktivieren Ihres Azure-Kontos verwendet haben.
7. Nach der Anmeldung klicken Sie auf **Schließen**. Klicken Sie dann auf **OK**, um fortzufahren.

	![](media/app-service-with-cdn/4-signed-in.png)

8. Falls Sie noch keine Azure-Web-App erstellt haben, kann Visual Studio Sie bei der Erstellung unterstützen. Stellen Sie im Dialogfeld **Microsoft Azure-Website konfigurieren** sicher, dass der Name Ihrer Website eindeutig ist. Klicken Sie dann auf **OK**.

	![](media/app-service-with-cdn/5-create-website.png)

9. Nachdem Ihre ASP.NET-Anwendung erstellt wurde, veröffentlichen Sie sie im Webveröffentlichungs-Aktivitätsbereich von Azure, indem Sie auf **`<app name>` jetzt auf dieser Website veröffentlichen** klicken. Klicken Sie auf **Veröffentlichen**, um den Vorgang abzuschließen.

	![](media/app-service-with-cdn/6-publish-website.png)

	Wenn die Veröffentlichung abgeschlossen ist, wird Ihre veröffentlichte Web-App im Browser angezeigt.

1. Zum Erstellen eines CDN-Endpunkts melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.
2. Klicken Sie auf **Neu** > **App-Dienste** > **CDN** > **Schnellerfassung**. Wählen Sie **http://*&lt;sitename>*.azurewebsites.net/**, und klicken Sie auf **Erstellen**.

	![](media/app-service-with-cdn/7-create-cdn.png)

	> [AZURE.NOTE]Nachdem der CDN-Endpunkt erstellt wurde, werden im Azure-Portal dessen URL und die Ursprungsdomäne, in der er integriert ist, angezeigt. Es kann jedoch eine Weile dauern, bis die Konfiguration des neuen CDN-Endpunkts vollständig an alle CDN-Knotenstandorte weitergegeben wurde.

3. Klicken Sie im Azure-Portal auf der Registerkarte **CDN** auf den Namen des gerade erstellten CDN-Endpunkts.

	![](media/app-service-with-cdn/8-select-cdn.png)

3. Klicken Sie auf **Suchabfragezeichenfolge aktivieren**, um Abfragezeichenfolgen im CDN-Cache zu aktivieren. Sobald Sie diese Funktion aktivieren, wird derselbe Link, auf den Sie mit unterschiedlichen Abfragezeichenfolgen zugreifen, als separate Einträge zwischengespeichert.

	![](media/app-service-with-cdn/9-enable-query-string.png)

	>[AZURE.NOTE]Auch wenn die Aktivierung der Abfragezeichenfolge für diesen Teil des Lernprogramms nicht erforderlich ist, sollten Sie diesen Schritt so früh wie möglich durchführen, da die Verteilung einer hier vorgenommenen Änderung an alle CDN-Knoten einige Zeit dauert und Sie verhindern möchten, dass Inhalte, für die Abfragezeichenfolgen nicht aktiviert wurden, den CDN-Cache verstopfen (die Aktualisierung von CDN-Inhalten wird später erläutert).

2. Klicken Sie nun auf die CDN-Endpunktadresse. Wenn der Endpunkt bereit ist, sollte Ihre Web-App angezeigt werden. Wenn Sie einen **HTTP 404**-Fehler erhalten, ist der CDN-Endpunkt nicht bereit. Möglicherweise müssen Sie bis zu einer Stunde warten, bis die CDN-Konfiguration an alle Edgeknoten verteilt ist.

	![](media/app-service-with-cdn/11-access-success.png)

1. Versuchen Sie dann, auf die Datei **~/Content/bootstrap.css** in Ihrem ASP.NET-Projekt zuzugreifen. Wechseln Sie im Browser, zu **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css**. In meinem Setup lautet diese URL:

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	Dies entspricht der folgenden Ursprungs-URL am CDN-Endpunkt:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Wenn Sie zu **http://*&lt;cdnName>*.vo.msecnd.net/Content/bootstrap.css** navigieren, werden Sie zum Herunterladen der Datei "bootstrap.css" aufgefordert, die von der Web-App in Azure bereitgestellt wurde.

	![](media/app-service-with-cdn/12-file-access.png)

Auf die gleiche Weise können Sie direkt von Ihrem CDN-Endpunkt aus auf jede öffentlich zugängliche URL unter **http://*&lt;serviceName>*.cloudapp.net/** zugreifen. Beispiel:

-	Eine JS-Datei im Pfad "/Script"
-	Jede Inhaltsdatei im Pfad "/Content"
-	Jede Controlleraktion 
-	Sofern die Abfragezeichenfolge für den CDN-Endpunkt aktiviert ist, jede URL mit Abfragezeichenfolgen
-	Die gesamte Azure-Web-App, wenn alle Inhalte öffentlich sind

Beachten Sie, dass es (sofern überhaupt) vielleicht nicht in jedem Fall empfehlenswert ist, eine vollständige Azure-Web-App über Azure CDN zur Verfügung zu stellen. Es gibt auch einige Nachteile:

-	Für diesen Ansatz muss die gesamte Website öffentlich sein, da Azure CDN keine privaten Inhalte verarbeiten kann.
-	Falls der CDN-Endpunkt aus irgendeinem Grund – sei es wegen einer geplanten Wartung oder aufgrund eines Benutzerfehlers – offline geht, wird die gesamte Web-App offline geschaltet, es sei denn, die Kunden können an die Ursprungs-URL **http://*&lt;sitename>*.azurewebsites.net/** umgeleitet werden. 
-	Selbst bei Verwendung der benutzerdefinierten Cachesteuerungseinstellungen (siehe [Konfigurieren von Cacheoptionen für statische Dateien in Ihrer Azure-Web-App](#caching)) wird durch einen CDN-Endpunkt die Leistung von besonders dynamischem Inhalt nicht verbessert. Wenn Sie wie oben gezeigt versucht haben, die Startseite vom CDN-Endpunkt aus zu laden, beachten Sie, dass das Laden der Standardstartseite (einer ziemlich einfachen Seite) beim ersten Mal mindestens fünf Sekunden gedauert hat. Stellen Sie sich vor, diese Seite enthält dynamischen Inhalt, der im Minutentakt aktualisiert werden muss – das Ergebnis wäre nicht besonders benutzerfreundlich. Das Verarbeiten von dynamischem Inhalt von einem CDN-Endpunkt aus erfordert einen kurzen Cacheablauf, was zu häufigen Cachefehlern am CDN-Endpunkt führt. Dies beeinträchtigt die Leistung Ihrer Azure-Web-App und macht den Sinn und Zweck eines CDN zunichte.

Alternativ kann der von Azure CDN zu verarbeitende Inhalt von Fall zu Fall in Ihrer Azure-Web-App bestimmt werden. Zu diesem Zweck haben Sie bereits gelernt, wie Sie vom CDN-Endpunkt aus auf einzelne Inhaltsdateien zugreifen. Im Abschnitt [Bereitstellen von Inhalten aus Controlleraktionen über Azure CDN](#controller) zeige ich Ihnen, wie Sie eine bestimmte Controlleraktion vom CDN-Endpunkt aus verarbeiten.

<a name="caching"></a>
## Konfigurieren von Cacheoptionen für statische Dateien in Ihrer Azure-Web-App ##

Bei Integration von Azure CDN in Ihre Azure-Web-App können Sie angeben, wie statischer Inhalt im CDN-Endpunkt zwischengespeichert werden soll. Öffnen Sie dafür *Web.config* in Ihrem ASP.NET-Projekt (z. B. **cdnwebapp**), und fügen Sie ein `<staticContent>`-Element zu `<system.webServer>` hinzu. Mit der nachstehenden XML wird der Cache für einen Ablauf nach 3 Tagen konfiguriert. <pre class="prettyprint"> &lt;system.webServer&gt; <mark>&lt;staticContent&gt; &lt;clientCache cacheControlMode=";UseMaxAge"; cacheControlMaxAge=";3.00:00:00";/&gt; &lt;/staticContent&gt;</mark> ... &lt;/system.webServer&gt; </pre>

Nachdem Sie diese Konfiguration durchgeführt haben, wenden alle statischen Dateien in der Azure-Web-App die gleiche Regel im CDN-Cache an. Wenn Sie die Cacheeinstellungen genauer steuern möchten, fügen Sie eine *Web.config*-Datei in einen Ordner ein, und fügen Sie dieser Datei Ihre Einstellungen hinzu. Fügen Sie z. B. eine *Web.config*-Datei zum Ordner *\\Content* hinzu, und ersetzen Sie den Inhalt durch den folgenden XML-Code:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Diese Einstellung bewirkt, dass alle statischen Dateien im Ordner *\\Content* 15 Tage lang zwischengespeichert werden.

Weitere Informationen zum Konfigurieren des `<clientCache>`-Elements finden Sie unter [Client Cache &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache) (in englischer Sprache).

Im Abschnitt [Bereitstellen von Inhalten aus Controlleraktionen über Azure CDN](#controller) zeige ich Ihnen außerdem, wie Sie Cacheeinstellungen für Controlleraktionsergebnisse im CDN-Cache konfigurieren.

<a name="controller"></a>
## Verarbeiten von Inhalten von Controlleraktionen über Azure CDN ##

Wenn Sie eine Azure-Web-App in Azure CDN integrieren, ist es relativ einfach, Inhalte aus Controlleraktionen über das Azure CDN bereitzustellen. Wenn die gesamte Azure-Web-App über das CDN zur Verfügung gestellt werden soll, müssen Sie diesen Vorgang gar nicht durchführen, da alle Controlleraktionen bereits über das CDN erreichbar sind. Aber aus den bereits unter [Bereitstellen einer Azure-Web-App mit einem integrierten CDN-Endpunkt](#deploy) erwähnten Gründen entscheiden Sie sich jedoch möglicherweise dagegen und wählen stattdessen die Controlleraktion aus, die von Azure CDN bereitgestellt werden soll. [Maarten Balliauw](https://twitter.com/maartenballiauw) zeigt Ihnen unter [Reduzieren der Latenz im Web mit dem Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) mit einem lustigen MemeGenerator-Controller, wie Sie dabei vorgehen müssen. Ich werde den Controller hier einfach nur reproduzieren.

Angenommen, Sie möchten in Ihrer Web-App Meme auf Grundlage eines Bilds des jungen Chuck Norris generieren (Foto von [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)), ungefähr so:

![](media/app-service-with-cdn/cdn-5-memegenerator.PNG)

Sie verfügen über eine einfache `Index`-Aktion, mit der die Kunden die Superlative im Bild angeben können; anschließend wird das Mem generiert, sobald die Aktion gepostet wird. Da es sich um Chuck Norris handelt, können Sie davon ausgehen, dass diese Seite weltweit extrem beliebt wird. Dies ist ein gutes Beispiel für das Verarbeiten von halbdynamischem Inhalt mit Azure CDN.

Führen Sie die vorstehenden Schritte aus, um diese Controlleraktion einzurichten:

1. Erstellen Sie im Ordner *\\Controllers* eine neue CS-Datei namens *MemeGeneratorController.cs*, und ersetzen Sie den Inhalt durch den folgenden Code. Achten Sie darauf, den markierten Teil durch Ihren Dateipfad und CDN-Namen zu ersetzen.
	<pre class="prettyprint">
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Drawing;
using System.IO;
using System.Net;
using System.Web.Hosting;
using System.Web.Mvc;
using System.Web.UI;

namespace cdnwebapp.Controllers
{
    public class MemeGeneratorController : Controller
    {
        static readonly Dictionary&lt;string, Tuple&lt;string ,string>> Memes = new Dictionary&lt;string, Tuple&lt;string, string>>();

        public ActionResult Index()
        {
            return View();
        }

        [HttpPost, ActionName("Index")]
    	public ActionResult Index_Post(string top, string bottom)
        {
            var identifier = Guid.NewGuid().ToString();
            if (!Memes.ContainsKey(identifier))
            {
                Memes.Add(identifier, new Tuple&lt;string, string>(top, bottom));
            }

            return Content("&lt;a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme&lt;/a>");
        }

        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
        public ActionResult Show(string id)
        {
            Tuple&lt;string, string> data = null;
            if (!Memes.TryGetValue(id, out data))
            {
                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
            }

            if (Debugger.IsAttached) // Preserve the debug experience
            {
                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
            else // Get content from Azure CDN
            {
                return Redirect(string.Format("http://<mark>&lt;yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}", data.Item1, data.Item2));
            }
        }

        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
        public ActionResult Generate(string top, string bottom)
        {
            string imageFilePath = HostingEnvironment.MapPath("<mark>~/Content/chuck.bmp</mark>");
            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

            using (Graphics graphics = Graphics.FromImage(bitmap))
            {
                SizeF size = new SizeF();
                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                }
                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                {
                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                }
            }

            MemoryStream ms = new MemoryStream();
            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
            return File(ms.ToArray(), "image/png");
        }

        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
        {
            // Compute actual size, shrink if needed
            while (true)
            {
                size = g.MeasureString(text, font);

                // It fits, back out
                if (size.Height &lt; i.Height &amp;&amp;
                     size.Width &lt; i.Width) { return font; }

                // Try a smaller font (90% of old size)
                Font oldFont = font;
                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                oldFont.Dispose();
            }
        }
    }
}
</pre>

2. Klicken Sie mit der rechten Maustaste auf die `Index()`-Standardaktion, und wählen Sie **Ansicht hinzufügen**.

	![](media/app-service-with-cdn/cdn-6-addview.PNG)

3.  Akzeptieren Sie die folgenden Einstellungen, und klicken Sie auf **Hinzufügen**.

	![](media/app-service-with-cdn/cdn-7-configureview.PNG)

4. Öffnen Sie die neue Datei *Views\\MemeGenerator\\Index.cshtml*, und ersetzen Sie den Inhalt durch den folgenden einfachen HTML-Code zum Übermitteln der Superlative:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Veröffentlichen Sie die Azure-Web-App erneut, und navigieren Sie in Ihrem Browser zu **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**.

Wenn Sie die Formularwerte an `/MemeGenerator/Index` übermitteln, gibt die `Index_Post`-Aktionsmethode eine Link zur `Show`-Aktionsmethode mit der betreffenden Eingabe-ID zurück. Wenn Sie auf den Link klicken, gelangen Sie zum folgenden Code: <pre class="prettyprint"> OutputCache(VaryByParam = ";*";, Duration = 1, Location = OutputCacheLocation.Downstream) public ActionResult Show(string id) { Tuple&lt;string, string&gt; data = null; if (!Memes.TryGetValue(id, out data)) { return new HttpStatusCodeResult(HttpStatusCode.NotFound); }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(";/MemeGenerator/Generate?top={0}&bottom={1}";, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(";http://<mark>&lt;yourCDNName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}";, data.Item1, data.Item2));
    }
} </pre>

Wenn Ihr lokaler Debugger angefügt ist, erhalten Sie den standardmäßigen Debugvorgang mit einer lokalen Umleitung. Bei Ausführung in der Azure-Web-App erfolgt eine Umleitung zu folgendem Ziel:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dies entspricht der folgenden Ursprungs-URL am CDN-Endpunkt:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Mit der zuvor angewendeten URL Rewrite-Regel wird die folgende Datei im CDN-Endpunkt zwischengespeichert:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Anschließend können Sie mit dem `OutputCacheAttribute`-Attribut für die `Generate`-Methode angeben, wie das Aktionsergebnis zwischengespeichert werden soll; dies wird von Azure CDN berücksichtigt. Mit dem folgenden Code wird ein Cacheablauf von einer Stunde (3.600 Sekunden) angegeben.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Entsprechend können Sie Inhalt aus jeder beliebigen Controlleraktion über Azure CDN in Ihrer Azure-Web-App verarbeiten, indem Sie die gewünschte Cacheoption verwenden.

Im nächsten Abschnitt erfahren Sie, wie Sie die gebündelten und minimierten Skripts und CSS über Azure CDN verarbeiten.

<a name="bundling"></a>
## Integrieren von ASP.NET-Bündelung und -Minimierung in Azure CDN ##

Skripts und CSS-Stylesheets ändern sich häufig und sind daher die idealen Kandidaten für den Azure CDN-Cache. Die Verarbeitung der gesamten Web-App über Azure CDN ist die einfachste Möglichkeit, Bündelung und Minimierung in Azure CDN zu integrieren. Da Sie sich jedoch möglicherweise aus den unter [Integrieren eines Azure CDN-Endpunkts in Ihrer Azure-Web-App und Bereitstellen von statischem Inhalt in Webseiten über Azure CDN](#deploy) beschriebenen Gründen gegen diesen Ansatz entscheiden, zeige ich Ihnen, wie Sie die Integration unter Wahrung der gewünschten Entwicklerumgebung von ASP.NET-Bündelung und -Minimierung durchführen können, die Ihnen z. B. Folgendes bietet:

-	Hervorragende Benutzerfreundlichkeit des Debugmodus
-	Vereinfachte Bereitstellung
-	Sofortige Updates für Clients für Skript-/CSS-Versionsupgrades
-	Fallbackmechanismus bei einem Fehler des CDN-Endpunkts
-	Minimale Codeänderung

Öffnen Sie im ASP.NET-Projekt, das Sie in [Integrieren eines Azure CDN-Endpunkts in Ihrer Azure-Website und Verarbeiten von statischem Inhalt in Webseiten über Azure CDN](#deploy) erstellt haben, öffnen Sie *App_Start\\BundleConfig.cs*, und werfen Sie einen Blick auf die `bundles.Add()`-Methodenaufrufe.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

Die erste `bundles.Add()`-Anweisung fügt ein Skriptbundle im virtuellen Verzeichnis `~/bundles/jquery` hinzu. Öffnen Sie anschließend die Datei *Views\\Shared_Layout.cshtml*, um sich anzusehen, wie das Skriptbundle-Tag gerendert wird. Sie sollten die folgende Zeile mit Razor-Code finden:

    @Scripts.Render("~/bundles/jquery")

Wenn dieser Razor-Code in der Azure-Web-App ausgeführt wird, rendert er ein `<script>`-Tag für das Skriptbundle ähnlich dem folgenden:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Wenn Sie ihn jedoch in Visual Studio durch Drücken von `F5` ausführen, wird jede Skriptdatei im Bundle einzeln gerendert (das Bundle im vorstehenden Beispiel enthält nur eine Skriptdatei):

    <script src="/Scripts/jquery-1.10.2.js"></script>

So können Sie den JavaScript-Code in Ihrer Entwicklungsumgebung debuggen und in der Produktion gleichzeitige Clientverbindungen reduzieren (durch Bündelung) und die Leistung von Dateidownloads verbessern (durch Minimierung). Dies ist eine großartige Funktion, die es bei der Azure CDN-Integration aufrechtzuerhalten gilt. Da das gerenderte Bundle bereits eine automatisch generierte Versionszeichenfolge enthält, ist es außerdem empfehlenswert, diese Funktionalität zu replizieren, sodass bei jeder Aktualisierung der jQuery-Version durch NuGet die entsprechende Aktualisierung auf der Clientseite so schnell wie möglich erfolgen kann.

Führen Sie die folgenden Schritte aus, um ASP.NET-Bündelung und -Minimierung in Ihren CDN-Endpunkt zu integrieren.

1. Ändern Sie in der Datei *App_Start\\BundleConfig.cs* die `bundles.Add()`-Methoden so ab, dass sie einen anderen [Bundle-Konstruktor](http://msdn.microsoft.com/library/jj646464.aspx) verwenden, und zwar einen, der eine CDN-Adresse angibt. Ersetzen Sie hierzu die `RegisterBundles`-Methodendefinition durch den folgenden Code:  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    <mark>bundles.UseCdn = true;
    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
        .GetName().Version.ToString();
    var cdnUrl = "http://&lt;yourCDNName>.vo.msecnd.net/{0}?v=" + version;</mark>

    bundles.Add(new ScriptBundle("~/bundles/jquery"<mark>, string.Format(cdnUrl, "bundles/jquery")</mark>).Include(
                "~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval"<mark>, string.Format(cdnUrl, "bundles/jqueryval")</mark>).Include(
                "~/Scripts/jquery.validate*"));

    // Use the development version of Modernizr to develop with and learn from. Then, when you're
    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
    bundles.Add(new ScriptBundle("~/bundles/modernizr"<mark>, string.Format(cdnUrl, "bundles/modernizer")</mark>).Include(
                "~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap"<mark>, string.Format(cdnUrl, "bundles/bootstrap")</mark>).Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css"<mark>, string.Format(cdnUrl, "Content/css")</mark>).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}
</pre>Denken Sie daran, `<yourCDNName>` durch den Namen Ihres Azure CDN zu ersetzen.

	Einfach ausgedrückt, Sie legen `bundles.UseCdn = true` fest und haben jedem Bundle eine sorgfältig gebildete CDN-URL hinzugefügt. Beispielsweise ist der erste Konstruktor im Code:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	der gleiche wie:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Dieser Konstruktor weist die ASP.NET-Bündelung und -Minimierung an, beim lokalen Debuggen einzelne Skriptdateien zu rendern, aber mit der angegebenen CDN-Adresse auf das betreffende Skript zuzugreifen. Beachten Sie bei dieser sorgfältig gebildeten CDN-URL jedoch zwei wichtige Merkmale:
	
	-	Der Ursprung dieser CDN-URL ist `http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`. Hierbei handelt es sich um das virtuelle Verzeichnis des Skriptbundles in Ihrer Webanwendung.
	-	Da Sie einen CDN-Konstruktor verwenden, enthält das CDN-Skripttag für das Bundle nicht länger die automatisch generierte Versionszeichenfolge in der gerenderten URL. Sie müssen bei jeder Änderung des Skriptbundles manuell eine eindeutige Versionszeichenfolge generieren, um einen Cachefehler im Azure CDN zu erzwingen. Gleichzeitig muss diese eindeutige Versionszeichenfolge während der gesamten Lebensdauer der Bereitstellung konstant bleiben, um Cachetreffer im Azure CDN zu minimieren, nachdem das Bundle bereitgestellt wurde.
	-	Die Abfragezeichenfolge v=<W.X.Y.Z> führt Pullvorgänge aus *Properties\\AssemblyInfo.cs* in Ihrem ASP.NET-Projekt aus. Sie können den Bereitstellungsworkflow so konfigurieren, dass die Assemblyversion bei jeder Veröffentlichung in Azure schrittweise erhöht wird. Alternativ können Sie einfach *Properties\\AssemblyInfo.cs* in Ihrem Projekt so ändern, dass die Versionszeichenfolge bei jeder Erstellung automatisch schrittweise erhöht wird, indem Sie das Platzhalterzeichen "*" verwenden. Beispiel:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Sie können auch jede andere Strategie zur Vereinfachung der Generierung einer für die Lebensdauer einer Bereitstellung eindeutigen Zeichenfolge verwenden.

3. Veröffentlichen Sie die ASP.NET-Anwendung erneut, und greifen Sie auf die Startseite zu.
 
4. Zeigen Sie den HTML-Code für die Seite an. Die CDN-URL sollte bei jeder erneuten Veröffentlichung von Änderungen an Ihrer Azure-Web-App mit einer eindeutigen Versionszeichenfolge gerendert angezeigt werden. Beispiel:
	<pre class="prettyprint">
...

&lt;link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

&lt;script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449">&lt;/script>

...

&lt;script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449">&lt;/script>

&lt;script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449">&lt;/script>

...</pre>

5. Debuggen Sie die ASP.NET-Anwendung in Visual Studio durch Drücken von `F5`.

6. Zeigen Sie den HTML-Code für die Seite an. Jede Skriptdatei wird einzeln gerendert dargestellt, sodass Sie einen konsistenten Debugvorgang in Visual Studio erhalten.
	<pre class="prettyprint">
...

    &lt;link href="/Content/bootstrap.css" rel="stylesheet"/>
&lt;link href="/Content/site.css" rel="stylesheet"/>

    &lt;script src="/Scripts/modernizr-2.6.2.js">&lt;/script>

...

    &lt;script src="/Scripts/jquery-1.10.2.js">&lt;/script>

    &lt;script src="/Scripts/bootstrap.js">&lt;/script>
&lt;script src="/Scripts/respond.js">&lt;/script>

...    
</pre>

<a name="fallback"></a>
## Fallbackmechanismus für CDN-URLs ##

Bei einem Fehler des Azure CDN-Endpunkts (gleich welcher Ursache) soll Ihre Webseite so intelligent sein, dass sie als Fallbackoption zum Laden von JavaScript oder Bootstrap auf den ursprünglichen Webserver zugreift. Wenn aufgrund der Nichterreichbarkeit des CDN Bilder für die Web-App verloren gehen, ist dies eine Sache; deutlich schwerwiegender ist es jedoch, wesentliche Seitenfunktionen zu verlieren, die durch Skripts und Stylesheets zur Verfügung gestellt werden.

Die [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx)-Klasse enthält eine Eigenschaft namens [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), mit der Sie den Fallbackmechanismus für CDN-Fehler konfigurieren können. Zur Verwendung dieser Eigenschaft führen Sie die folgenden Schritte aus:

1. Öffnen Sie in Ihrem ASP.NET-Projekt die Datei *App_Start\\BundleConfig.cs*, in der Sie jedem [Bundle-Konstruktor](http://msdn.microsoft.com/library/jj646464.aspx) eine CDN-URL hinzugefügt haben, und nehmen Sie die folgenden hervorgehobenen Änderungen vor, um den Standardbündeln einen Fallbackmechanismus hinzuzufügen:  
	<pre class="prettyprint">
public static void RegisterBundles(BundleCollection bundles)
{
    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
        .GetName().Version.ToString();
    var cdnUrl = "http://cdnurl.vo.msecnd.net/.../{0}?" + version;
    bundles.UseCdn = true;

    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
				<mark>{ CdnFallbackExpression = "window.jquery" }</mark>
                .Include("~/Scripts/jquery-{version}.js"));

    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
				<mark>{ CdnFallbackExpression = "$.validator" }</mark>
            	.Include("~/Scripts/jquery.validate*"));

    // Use the development version of Modernizr to develop with and learn from. Then, when you're
    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
				<mark>{ CdnFallbackExpression = "window.Modernizr" }</mark>
				.Include("~/Scripts/modernizr-*"));

    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
				<mark>{ CdnFallbackExpression = "$.fn.modal" }</mark>
        		.Include(
	              		"~/Scripts/bootstrap.js",
	              		"~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                "~/Content/bootstrap.css",
                "~/Content/site.css"));
}</pre>Wenn `CdnFallbackExpression` nicht null ist, wird ein Skript in den HTML-Code eingefügt, um zu testen, ob das Bundle erfolgreich geladen wurde, und bei Bedarf direkt vom ursprünglichen Webserver aus auf das Bundle zuzugreifen. Diese Eigenschaft muss auf einen JavaScript-Ausdruck festgelegt werden, der testet, ob das entsprechende CDN-Bundle ordnungsgemäß geladen wurde. Welcher Ausdruck zum Testen der einzelnen Bundle erforderlich ist, hängt von deren Inhalt ab. Für die vorstehenden Standardbundles gilt:
	
	-	`window.jquery` ist in "jquery-{version}.js" definiert
	-	`$.validator` ist in "jquery.validate.js" definiert
	-	`window.Modernizr` ist in "modernizer-{version}.js" definiert
	-	`$.fn.modal` ist in "bootstrap.js" definiert
	
	Sie haben vielleicht bemerkt, dass ich "CdnFallbackExpression" für das `~/Cointent/css`-Bundle nicht festgelegt habe. Der Grund ist, dass derzeit ein [Fehler in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) vorliegt, der ein `<script>`-Tag für das Fallback-CSS anstelle des erwarteten `<link>`-Tags einfügt.
	
	Es wird jedoch ein gutes [Style Bundle Fallback](https://github.com/EmberConsultingGroup/StyleBundleFallback) von [Ember Consulting Group](https://github.com/EmberConsultingGroup) angeboten.

2. Um die Problemumgehung für CSS zu verwenden, erstellen Sie im Ordner *App_Start* Ihres ASP.NET-Projekts eine neue CS-Datei namens *StyleBundleExtensions.cs*, und ersetzen Sie deren Inhalt durch den [Code von GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. Benennen Sie in *App_Start\\StyleFundleExtensions.cs*, den Namespace in den Namespace Ihrer ASP.NET-Anwendung um (z. B. **cdnwebapp**).

3. Wechseln Sie zurück zu `App_Start\BundleConfig.cs`, und ändern Sie die letzte `bundles.Add`-Anweisung mit dem folgenden hervorgehobenen Code:
	<pre class="prettyprint">
bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
    .Include(
          "~/Content/bootstrap.css",
          "~/Content/site.css"));
</pre>Diese neue Erweiterungsmethode fügt ebenfalls ein Skript in den HTML-Code ein, um im DOM nach einem Klassennamen, einem Regelnamen und einem Regelwert zu suchen, die mit den im CSS-Bündel definierten Namen/Werten übereinstimmen; wird keine Übereinstimmung gefunden, erfolgt ein Fallback zum ursprünglichen Webserver.

4. Veröffentlichen Sie Ihre Azure-Web-App erneut, und greifen Sie auf die Startseite zu.
5. Zeigen Sie den HTML-Code für die Seite an. Es sollten eingefügte Skripts ähnlich den folgenden zu sehen sein:    
	<pre class="prettyprint">...

	&lt;link href="http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
<mark>&lt;script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i &lt; len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
                        var meta = document.createElement('meta');
                        meta.className = 'sr-only';
                        document.head.appendChild(meta);
                        var value = window.getComputedStyle(meta).getPropertyValue('width');
                        document.head.removeChild(meta);
                        if (value !== '1px') {
                            document.write('&lt;link href="/Content/css" rel="stylesheet" type="text/css" />');
                        }
                    }
                }
                return true;
            }())||document.write('&lt;script src="/Content/css">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.Modernizr)||document.write('&lt;script src="/bundles/modernizr">&lt;/script>');&lt;/script></mark>

...	

    &lt;script src="http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>(window.jquery)||document.write('&lt;script src="/bundles/jquery">&lt;/script>');&lt;/script></mark>

    &lt;script src="http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474">&lt;/script>
<mark>&lt;script>($.fn.modal)||document.write('&lt;script src="/bundles/bootstrap">&lt;/script>');&lt;/script></mark>

...
</pre>Beachten Sie, dass das eingefügte Skript für das CSS-Bundle immer noch den fehlgeleiteten Rest aus der `CdnFallbackExpression`-Eigenschaft in der folgenden Zeile enthält:

        }())||document.write('<script src="/Content/css"></script>');</script>

	Da jedoch der erste Teil des ||-Ausdrucks immer "true" zurückgibt (in der Zeile unmittelbar darüber), wird die document.write()-Funktion niemals ausgeführt.

6. Um zu testen, ob das Fallback-Skript funktioniert, gehen Sie zurück zum Dashboard Ihres CDN-Endpunkts, und klicken Sie auf **Endpunkt deaktivieren**.

	![](media/app-service-with-cdn/13-test-fallback.png)

7. Aktualisieren Sie Ihr Browserfenster für die Azure-Web-App. Jetzt sollte angezeigt werden, dass alle Skripts und Stylesheets ordnungsgemäß geladen sind.

## Weitere Informationen ##
- [Übersicht über das Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Verarbeiten von Inhalt aus Azure CDN in einer Webanwendung](../cdn-serve-content-from-cdn-in-your-web-application.md)
- [Integrieren eines Clouddiensts in Azure CDN](../cdn-cloud-service-with-cdn.md)
- [ASP.NET-Bündelung und -Minimierung](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Verwenden von CDN für Azure](../cdn-how-to-use.md)
 

<!---HONumber=July15_HO3-->