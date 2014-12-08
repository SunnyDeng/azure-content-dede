<properties urlDisplayName="Integrate a cloud service with Azure CDN" pageTitle="Integrieren eines Cloud-Diensts in Azure CDN" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, cloud service" description="A tutorial that teaches you how to deploy a cloud service that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn,cloud-services" documentationCenter=".NET" title="Integrate a cloud service with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/02/2014" ms.author="cephalin" />

<a name="intro"></a>
# Integrieren eines Cloud-Diensts in Azure CDN #

Ein Clouddienst kann in Azure CDN integriert werden, um beliebige Inhalte aus dem Pfad `~/CDN` des Clouddiensts zur Verfügung zu stellen. Dieser Ansatz bietet die folgenden Vorteile:

- Einfaches Bereitstellen und Aktualisieren von Bildern, Skripts und Stylesheets in den Projektverzeichnissen des Cloud-Diensts
- Einfaches Durchführen von Upgrades für die NuGet-Pakete im Cloud-Dienst, wie z. B. jQuery- oder Bootstrap-Versionen 
- Verwalten der Webanwendung und des vom CDN verarbeiteten Inhalts über dieselbe Visual Studio-Oberfläche
- Einheitlicher Bereitstellungsworkflow für die Webanwendung und den vom CDN verarbeiteten Inhalt
- Integrieren von ASP.NET-Bündelung und -Minimierung in Azure CDN

## Sie lernen Folgendes ##

In diesem Lernprogramm lernen Sie Folgendes:

-	[Integrieren eines Azure CDN-Endpunkts in einen Cloud-Dienst und Verarbeiten von statischem Inhalt in Webseiten über Azure CDN](#deploy)
-	[Konfigurieren von Cacheeinstellungen für statischen Inhalt im Cloud-Dienst](#caching)
-	[Verarbeiten von Inhalten von Controlleraktionen über Azure CDN](#controller)
-	[Verarbeiten von gebündeltem und minimiertem Inhalt über Azure CDN bei Beibehaltung des Skript-Debugvorgangs in Visual Studio](#bundling)
-	[Konfigurieren des Fallbacks für Skripts und CSS, wenn das Azure CDN offline ist](#fallback) 

## Sie erstellen Folgendes ##

Sie stellen eine Cloud-Dienst-Webrolle unter Verwendung der ASP.NET-MVC-Standardvorlage bereit, fügen Code hinzu, um Inhalt aus einem integrierten Azure CDN zu verarbeiten, z. B. ein Bild, Ergebnisse von Controlleraktionen und die JavaScript- und CSS-Standarddateien; außerdem schreiben Sie Code zum Konfigurieren des Fallbackmechanismus für verarbeitete Bündel für den Fall, dass das CDN offline ist.

## Sie benötigen Folgendes ##

Für dieses Lernprogramm ist Folgendes erforderlich:

-	Ein aktives [Microsoft Azure-Konto](http://azure.microsoft.com/de-de/account/)
-	Visual Studio 2013 mit [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Sie benötigen ein Windows Azure-Konto, um dieses Lernprogramm durchführen zu können:</h5>
  <ul>
    <li>Sie können <a href="http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A261C142F">ein Azure-Konto kostenlos erstellen</a>: Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlose Azure-Dienste nutzen, wenn das Guthaben aufgebraucht ist.</li>
    <li>Sie können Ihre <a href="http://azure.microsoft.com/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">Vorteile für MSDN-Abonnenten aktivieren</a> - Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.</li>
  <ul>
</div>

<a name="deploy"></a>
## Bereitstellen eines Cloud-Diensts mit einem integrierten CDN-Endpunkt ##

In diesem Abschnitt stellen Sie die standardmäßige ASP.NET-MVC-Anwendungsvorlage in Visual Studio 2013 für eine Cloud-Dienst-Webrolle bereit und integrieren sie anschließend in einen neuen CDN-Endpunkt. Befolgen Sie die nachstehenden Anweisungen:

1. Erstellen Sie in Visual Studio 2013 einen neuen Azure-Cloud-Dienst über die Menüleiste, indem Sie **Datei > Neu > Projekt > Cloud > Windows Azure-Cloud-Dienst** auswählen. Geben Sie einen Namen ein, und klicken Sie auf **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Wählen Sie **ASP.NET-Webrolle** aus, und klicken Sie auf die Schaltfläche **>**. Klicken Sie auf OK.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Wählen Sie **MVC** aus, und klicken Sie auf **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Veröffentlichen Sie diese Webrolle jetzt in einem Azure-Cloud-Dienst. Klicken Sie mit der rechten Maustaste auf das Cloud-Dienstprojekt, und wählen Sie **Veröffentlichen** aus.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Wenn Sie sich noch nicht bei Microsoft Azure angemeldet haben, klicken Sie auf die Schaltfläche **Anmelden**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Melden Sie sich auf der Anmeldeseite mit dem Microsoft-Konto an, das Sie zum Aktivieren Ihres Azure-Kontos verwendet haben.
7. Nach der Anmeldung klicken Sie auf **Weiter**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Davon ausgehend, dass Sie noch keinen Cloud-Dienst und kein Speicherkonto erstellt haben, unterstützt Visual Studio Sie bei der Erstellung dieser beiden Komponenten. Geben Sie im Dialogfeld **Cloud-Dienst und Konto erstellen** den gewünschten Dienstnamen ein. Klicken Sie dann auf **Erstellen**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Überprüfen Sie die Konfiguration auf der Seite mit den Veröffentlichungseinstellungen, und klicken Sie auf **Veröffentlichen**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[WACOM.NOTE] Das Veröffentlichen von Cloud-Diensten dauert sehr lange. Mit der Option "Web Deploy für alle Webrollen aktivieren" können Sie das Debuggen des Cloud-Diensts erheblich beschleunigen, da hiermit schnelle (allerdings temporäre) Updates für Ihre Webrollen zur Verfügung gestellt werden. Weitere Informationen zu dieser Option finden Sie unter [Veröffentlichen eines Cloud-Diensts mit Azure Tools](http://msdn.microsoft.com/de-de/library/ff683672.aspx).

	Wenn im **Microsoft Azure-Aktivitätsprotokoll** als Veröffentlichungsstatus **Abgeschlossen** angezeigt wird, erstellen Sie einen CDN-Endpunkt, der in diesen Cloud-Dienst integriert ist. 

1. Zum Erstellen eines CDN-Endpunkts melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an. 
2. Klicken Sie auf **Neu** > **Anwendungsdienste** > **CDN** > **Schnellerfassung**. Wählen Sie **http://*<Dienstname>*.cloudapp.net/cdn/** aus, und klicken Sie auf **Erstellen**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[WACOM.NOTE] Nachdem der CDN-Endpunkt erstellt wurde, werden im Azure-Portal dessen URL und die Ursprungsdomäne, in der er integriert ist, angezeigt. Es kann jedoch eine Weile dauern, bis die Konfiguration des neuen CDN-Endpunkts vollständig an alle CDN-Knotenstandorte weitergegeben wurde. 

	Beachten Sie, dass der CDN-Endpunkt an den Pfad **cdn/** Ihres Cloud-Diensts gebunden ist. Sie können entweder einen Ordner **cdn** im Projekt **WebRole1** erstellen oder URL Rewrite verwenden, um diesen Pfad aus alle eingehenden Links zu entfernen. In diesem Lernprogramm verwenden Sie die letztgenannte Methode.

3. Klicken Sie im Azure-Portal auf der Registerkarte **CDN** auf den Namen des gerade erstellten CDN-Endpunkts.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. Klicken Sie auf **Suchabfragezeichenfolge aktivieren**, um Abfragezeichenfolgen im CDN-Cache zu aktivieren. Sobald Sie diese Funktion aktivieren, wird derselbe Link, auf den Sie mit unterschiedlichen Abfragezeichenfolgen zugreifen, als separate Einträge zwischengespeichert.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[WACOM.NOTE] Auch wenn die Aktivierung der Abfragezeichenfolge für diesen Teil des Lernprogramms nicht erforderlich ist, sollten Sie diesen Schritt so früh wie möglich durchführen, da die Verteilung einer hier vorgenommenen Änderung an alle CDN-Knoten einige Zeit dauert und Sie verhindern möchten, dass Inhalte, für die Abfragezeichenfolgen nicht aktiviert wurden, den CDN-Cache verstopfen (die Aktualisierung von CDN-Inhalten wird später erläutert).

3. Pingen Sie den CDN-Endpunkt, um sicherzustellen, dass er an die CDN-Knoten weitergegeben wurde. Es kann bis zu einer Stunde dauern, bevor der Endpunkt auf Pings antwortet.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Öffnen Sie in Visual Studio 2013 die Datei **Web.config** im Projekt **WebRole1**, und fügen Sie den folgenden Code in das Tag `<system.webServer>` ein:  
	<pre class="prettyprint">
	<system.webServer>
	  <mark><rewrite>
	    <rules>
	      <rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
	        <match url=&quot;^cdn/(.*)$&quot;/>
	        <action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/>
	      </rule>
	    </rules>
	  </rewrite></mark>
      ...
	</system.webServer>
	</pre>

4. Veröffentlichen Sie den Cloud-Dienst erneut. Klicken Sie mit der rechten Maustaste auf das Cloud-Dienstprojekt, und wählen Sie **Veröffentlichen** aus.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. Wenn der Veröffentlichungsstatus **Abgeschlossen** lautet, öffnen Sie ein Browserfenster, und navigieren Sie zu **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css**. In meinem Setup lautet diese URL:

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	Dies entspricht der folgenden Ursprungs-URL am CDN-Endpunkt:

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	Nach Anwendung von URL Rewrite in meiner Web-App wird die folgende Datei in meinem CDN-Cache zwischengespeichert:

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	Wenn Sie zu **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css** navigieren, werden Sie zum Herunterladen der Datei bootstrap.css aufgefordert, die von der veröffentlichten Web-App bereitgestellt wurde. 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Auf die gleiche Weise können Sie direkt von Ihrem CDN-Endpunkt aus auf jede öffentlich zugreifbare URL unter **http://*<serviceName>*.cloudapp.net/** zugreifen. Beispiel:

-	Eine JS-Datei im Pfad "/Script"
-	Jede Inhaltsdatei im Pfad "/Content"
-	Jede Controlleraktion 
-	Sofern die Abfragezeichenfolge für den CDN-Endpunkt aktiviert ist, jede URL mit Abfragezeichenfolgen

Tatsächlich können Sie mit der vorstehenden Konfiguration den gesamten Cloud-Dienst von **http://*<cdnName>*.vo.msecnd.net/** aus hosten. Wenn ich zu **http://az632148.vo.msecnd.net/** navigiere, erhalte ich das Aktionsergebnis von Startseite/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Dies bedeutet jedoch nicht, dass es immer (oder generell) einen gute Idee ist, einen gesamten Cloud-Dienst über Azure CDN zu verarbeiten. Es gibt auch einige Nachteile:

-	Für diesen Ansatz muss die gesamte Website öffentlich sein, da Azure CDN keine privaten Inhalte verarbeiten kann.
-	Falls der CDN-Endpunkt aus irgendeinem Grund - sei es wegen einer geplanten Wartung oder aufgrund eines Benutzerfehlers - offline geht, wird der gesamte Cloud-Dienst offline geschaltet, es sei denn, die Kunden können an die Ursprungs-URL **http://*<serviceName>*.cloudapp.net/** umgeleitet werden. 
-	Selbst bei Verwendung der benutzerdefinierten Cachesteuerungseinstellungen (siehe [Konfigurieren von Cacheoptionen für statische Dateien im Cloud-Dienst](#caching)), wird durch einen CDN-Endpunkt die Leistung von besonders dynamischem Inhalt nicht verbessert. Wenn Sie wie oben gezeigt versucht haben, die Startseite vom CDN-Endpunkt aus zu laden, beachten Sie, dass das Laden der Standardstartseite (einer ziemlich einfachen Seite) beim ersten Mal mindestens fünf Sekunden gedauert hat. Stellen Sie sich vor, diese Seite enthält dynamischen Inhalt, der im Minutentakt aktualisiert werden muss - das Ergebnis wäre nicht besonders benutzerfreundlich. Das Verarbeiten von dynamischem Inhalt von einem CDN-Endpunkt aus erfordert einen kurzen Cacheablauf, was zu häufigen Cachefehlern am CDN-Endpunkt führt. Dies beeinträchtigt die Leistung des Cloud-Diensts und macht den Sinn und Zweck eines CDN zunichte.

Alternativ kann der von Azure CDN zu verarbeitende Inhalt von Fall zu Fall im Cloud-Dienst bestimmt werden. Zu diesem Zweck haben Sie bereits gelernt, wie Sie vom CDN-Endpunkt aus auf einzelne Inhaltsdateien zugreifen. Im Abschnitt [Verarbeiten von Inhalten von Controlleraktionen über Azure CDN](#controller) zeige ich Ihnen, wie Sie eine bestimmte Controlleraktion vom CDN-Endpunkt aus verarbeiten.

Sie können eine restriktivere URL Rewrite-Regel festlegen, um den Inhalt einzuschränken, auf den über den CDN-Endpunkt zugegriffen werden kann. Wenn Sie z. B. URL Rewrite auf den Ordner *\Scripts* beschränken möchten, ändern Sie die vorstehende Rewrite-Regel wie folgt:   
<pre class="prettyprint">
<rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;>
  <match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/>
  <action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/>
</rule>
</pre>

<a name="caching"></a>
## Konfigurieren von Cacheoptionen für statische Dateien im Cloud-Dienst ##

Bei Integration von Azure CDN in Ihren Cloud-Dienst können Sie angeben, wie statischer Inhalt im CDN-Endpunkt zwischengespeichert werden soll. Öffnen Sie hierzu die Datei *Web.config* aus Ihrem Webrollenprojekt (z. B. WebRole1), und fügen Sie ein `<staticContent>`-Element zu `<system.webServer>` hinzu. Mit dem folgenden XML-Code wird festgelegt, dass der Cache in drei Tagen abläuft.  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

Nachdem Sie dies konfiguriert haben, wenden alle statischen Dateien im Cloud-Dienst die gleiche Regel im CDN-Cache an. Wenn Sie die Cacheeinstellungen genauer steuern möchten, fügen Sie eine *Web.config*-Datei in einen Ordner ein, und fügen Sie dieser Datei Ihre Einstellungen hinzu. Fügen Sie z. B. eine *Web.config*-Datei zum Ordner *\Content* hinzu, und ersetzen Sie den Inhalt durch den folgenden XML-Code:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Diese Einstellung bewirkt, dass alle statischen Dateien im Ordner *\Content* 15 Tage lang zwischengespeichert werden.

Weitere Informationen zum Konfigurieren des Elements `<clientCache>` finden Sie im [Clientcache <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

Im Abschnitt [Verarbeiten von Inhalten von Controlleraktionen über Azure CDN](#controller) zeige ich Ihnen außerdem, wie Sie Cacheeinstellungen für Controlleraktionsergebnisse im CDN-Cache konfigurieren.

<a name="controller"></a>
## Verarbeiten von Inhalten von Controlleraktionen über Azure CDN ##

Wenn Sie eine Cloud-Dienst-Webrolle in Azure CDN integrieren, ist es relativ einfach, Inhalt von Controlleraktionen über Azure CDN zu verarbeiten. Statt den Cloud-Dienst direkt über Azure CDN zu verarbeiten (siehe oben), können Sie hierfür auch einen witzigen MemeGenerator-Controller verwenden, wie [Maarten Balliauw](https://twitter.com/maartenballiauw) in seinem Vortrag zum Thema [Verringern von Latenz im Web mit Microsoft Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) (Reducing latency on the web with the Windows Azure CDN, in englischer Sprache) zeigt. Ich werde den Controller hier einfach nur reproduzieren.

Angenommen, Sie möchten in Ihrem Cloud-Dienst Meme auf der Grundlage eines Bilds des jungen Chuck Norris generieren (Foto von [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)), ungefähr so:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Sie verfügen über eine einfache `Index`-Aktion, mit der die Kunden die Superlative im Bild angegeben können; anschließend wird das Mem generiert, sobald die Aktion gepostet wird. Da es sich um Chuck Norris handelt, können Sie davon ausgehen, dass diese Seite weltweit extrem beliebt wird. Dies ist ein gutes Beispiel für das Verarbeiten von halbdynamischem Inhalt mit Azure CDN. 

Führen Sie die vorstehenden Schritte aus, um diese Controlleraktion einzurichten:

1. Erstellen Sie im Ordner *\Controllers* eine neue CS-Datei namens *MemeGeneratorController.cs*, und ersetzen Sie den Inhalt durch den folgenden Code. Achten Sie darauf, den hervorgehobenen Teil durch den Namen Ihres CDN zu ersetzen.  
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
	
	namespace WebRole1.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string&gt;> Memes = new Dictionary<string, Tuple<string, string&gt;>();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark><yourCdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
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
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height < i.Height &&
	                     size.Width < i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. Klicken Sie mit der rechten Maustaste auf die `Index()`-Standardaktion, und wählen Sie **Ansicht hinzufügen** aus.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Akzeptieren Sie die folgenden Einstellungen, und klicken Sie auf **Hinzufügen**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Öffnen Sie die neue Datei *Views\MemeGenerator\Index.cshtml*, und ersetzen Sie den Inhalt durch den folgenden einfachen HTML-Code zum Übermitteln der Superlative:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Veröffentlichen Sie den Cloud-Dienst erneut, und navigieren Sie in Ihrem Browser zu **http://*<serviceName>*.cloudapp.net/MemeGenerator/Index**. 

Wenn Sie die Formularwerte `/MemeGenerator/Index` senden, gibt die Aktionsmethode `Index_Post` einen Link an die Aktionsmethode `Show` mit der entsprechenden Eingabe-ID zurück. Wenn Sie auf den Link klicken, erhalten Sie den folgenden Code:  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark><cdnName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Wenn Ihr lokaler Debugger angefügt ist, erhalten Sie den standardmäßigen Debugvorgang mit einer lokalen Umleitung. Bei Ausführung im Cloud-Dienst wird zu folgendem Ziel umgeleitet:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dies entspricht der folgenden Ursprungs-URL am CDN-Endpunkt:

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Mit der zuvor angewendeten URL Rewrite-Regel wird die folgende Datei im CDN-Endpunkt zwischengespeichert:

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Anschließend können Sie mit dem Attribut `OutputCacheAttribute` für die Methode `Generate` angeben, wie das Aktionsergebnis zwischengespeichert werden soll; dies wird von Azure CDN berücksichtigt. Mit dem folgenden Code wird ein Cacheablauf von einer Stunde (3.600 Sekunden) angegeben.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Entsprechend können Sie Inhalt von jeder beliebigen Controlleraktion über Azure CDN im Cloud-Dienst verarbeiten, indem Sie die gewünschte Cacheoption verwenden.

Im nächsten Abschnitt erfahren Sie, wie Sie die gebündelten und minimierten Skripts und CSS über Azure CDN verarbeiten. 

<a name="bundling"></a>
## Integrieren von ASP.NET-Bündelung und -Minimierung in Azure CDN ##

Skripts und CSS-Stylesheets ändern sich häufig und sind daher die idealen Kandidaten für den Azure CDN-Cache. Die Verarbeitung der gesamten Webrolle über Azure CDN ist die einfachste Möglichkeit, Bündelung und Minimierung in Azure CDN zu integrieren. Da Sie diese Methode jedoch möglicherweise nicht verwenden möchten, zeige ich Ihnen, wie Sie die Integration unter Wahrung der gewünschten Entwicklerumgebung von ASP.NET-Bündelung und -Minimierung durchführen können, die Ihnen z. B. Folgendes bietet:

-	Hervorragende Benutzerfreundlichkeit des Debugmodus
-	Vereinfachte Bereitstellung
-	Sofortige Updates für Clients für Skript-/CSS-Versionsupgrades
-	Fallbackmechanismus bei einem Fehler des CDN-Endpunkts
-	Minimale Codeänderung

Öffnen Sie im **WebRole1**-Projekt, das Sie in [Integrieren eines Azure CDN-Endpunkts in einen Cloud-Dienst und Verarbeiten von statischem Inhalt in Webseiten über Azure CDN](#deploy) erstellt haben, *App_Start\BundleConfig.cs*, und werfen Sie einen Blick auf die Methodenaufrufe `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

Mit der ersten `bundles.Add()`-Anweisung wird ein Skriptbündel im virtuellen Verzeichnis `~/bundles/jquery` hinzugefügt. Öffnen Sie anschließend die Datei *Views\Shared_Layout.cshtml*, um sich anzusehen, wie das Skriptbündel-Tag gerendert wird. Sie sollten die folgende Zeile mit Razor-Code finden:

    @Scripts.Render("~/bundles/jquery")

Wenn dieser Razor-Code in der Azure-Webrolle ausgeführt wird, rendert er ein `<script>`-Tag für das Skriptbündel ähnlich dem folgenden: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Wenn Sie ihn jedoch in Visual Studio durch Drücken von `F5` ausführen, wird jede Skriptdatei im Bündel einzeln gerendert (das Bündel im vorstehenden Beispiel enthält nur eine Skriptdatei):

    <script src="/Scripts/jquery-1.10.2.js"></script>

So können Sie den JavaScript-Code in Ihrer Entwicklungsumgebung debuggen und in der Produktion gleichzeitige Clientverbindungen reduzieren (durch Bündelung) und die Leistung von Dateidownloads verbessern (durch Minimierung). Dies ist eine großartige Funktion, die es bei der Azure CDN-Integration aufrechtzuerhalten gilt. Da das gerenderte Bündel bereits eine automatisch generierte Versionszeichenfolge enthält, ist es außerdem empfehlenswert, diese Funktionalität zu replizieren, sodass bei jeder Aktualisierung der jQuery-Version durch NuGet die entsprechende Aktualisierung auf der Clientseite so schnell wie möglich erfolgen kann.

Führen Sie die folgenden Schritte aus, um ASP.NET-Bündelung und -Minimierung in Ihren CDN-Endpunkt zu integrieren.

1. Ändern Sie in der Datei *App_Start\BundleConfig.cs* die `bundles.Add()`-Methoden so, dass sie einen anderen [Bundle-Konstruktor](http://msdn.microsoft.com/de-de/library/jj646464.aspx) verwenden, und zwar einen, der eine CDN-Adresse angibt. Ersetzen Sie hierzu die `RegisterBundles`-Methodendefinition durch den folgenden Code:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	Denken Sie daran, `<yourCDNName>` durch den Namen Ihres Azure CDN zu ersetzen.

	Einfach gesagt, Sie legen `bundles.UseCdn = true` fest und haben jedem Bündel eine sorgfältig gebildete CDN-URL hinzugefügt. Beispielsweise ist der erste Konstruktor im Code:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	der gleiche wie: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

		Dieser Konstruktor weist die ASP.NET-Bündelung und -Minimierung an, beim lokalen Debuggen einzelne Skriptdateien zu rendern, aber mit der angegebenen CDN-Adresse auf das betreffende Skript zuzugreifen. Beachten Sie bei dieser sorgfältig gebildeten CDN-URL jedoch zwei wichtige Merkmale:
	
	-	Der Ursprung für diese CDN-URL ist `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, was tatsächlich das virtuelle Verzeichnis des Skriptbündels in Ihrem Cloud-Dienst ist.
	-	Da Sie einen CDN-Konstruktor verwenden, enthält das CDN-Skripttag für das Bündel nicht länger die automatisch generierte Versionszeichenfolge in der gerenderten URL. Sie müssen bei jeder Änderung des Skriptbündels manuell eine eindeutige Versionszeichenfolge generieren, um einen Cachefehler im Azure CDN zu erzwingen. Gleichzeitig muss diese eindeutige Versionszeichenfolge während der gesamten Lebensdauer der Bereitstellung konstant bleiben, um Cachetreffer im Azure CDN zu minimieren, nachdem das Bündel bereitgestellt wurde.
	-	Die Abfragezeichenfolge v=<W.X.Y.Z> überträgt mithilfe von Pull aus *Properties\AssemblyInfo.cs* in Ihrem Webrollenprojekt. Sie können den Bereitstellungsworkflow so konfigurieren, dass die Assemblyversion bei jeder Veröffentlichung in Azure schrittweise erhöht wird. Alternativ können Sie einfach *Properties\AssemblyInfo.cs* in Ihrem Projekt so ändern, dass die Versionszeichenfolge bei jeder Erstellung automatisch schrittweise erhöht wird, indem Sie das Platzhalterzeichen "*" verwenden. Beispiel:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Sie können auch jede andere Strategie zur Vereinfachung der Generierung einer für die Lebensdauer einer Bereitstellung eindeutigen Zeichenfolge verwenden.

3. Veröffentlichen Sie den Cloud-Dienst erneut, und rufen Sie die Startseite auf.
 
4. Zeigen Sie den HTML-Code für die Seite an. Die CDN-URL sollte bei jeder erneuten Veröffentlichung von Änderungen im Cloud-Dienst mit einer eindeutigen Versionszeichenfolge gerendert angezeigt werden. Beispiel:  
	<pre class="prettyprint">
	...

    <link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;></script>

	...

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. Debuggen Sie den Cloud-Dienst in Visual Studio durch Drücken von `F5`., 

6. Zeigen Sie den HTML-Code für die Seite an. Jede Skriptdatei wird einzeln gerendert dargestellt, sodass Sie einen konsistenten Debugvorgang in Visual Studio erhalten.  
	<pre class="prettyprint">
	...
	
	    <link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/>
	<link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/>
	
	    <script src=&quot;/Scripts/modernizr-2.6.2.js&quot;></script>
	
	...
	
	    <script src=&quot;/Scripts/jquery-1.10.2.js&quot;></script>
	
	    <script src=&quot;/Scripts/bootstrap.js&quot;></script>
	<script src=&quot;/Scripts/respond.js&quot;></script>
	
	...    
	</pre>

<a name="fallback"></a>
## Fallbackmechanismus für CDN-URLs ##

Bei einem Fehler des Azure CDN-Endpunkts (gleich welcher Ursache) soll Ihre Webseite so intelligent sein, dass sie als Fallbackoption zum Laden von JavaScript oder Bootstrap auf den ursprünglichen Webserver zugreift. Wenn aufgrund von CDN-Nichtverfügbarkeit Bilder auf der Website verloren gehen, ist dies eine Sache, wesentliche Seitenfunktionen zu verlieren, die durch Skripts und Stylesheets zur Verfügung gestellt werden, ist jedoch eine andere Sache.

Die [Bundle](http://msdn.microsoft.com/de-de/library/system.web.optimization.bundle.aspx)-Klasse enthält eine Eigenschaft namens [CdnFallbackExpression](http://msdn.microsoft.com/de-de/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), mit der Sie den Fallbackmechanismus für CDN-Fehler konfigurieren können. Zur Verwendung dieser Eigenschaft führen Sie die folgenden Schritte aus:

1. 	Öffnen Sie in Ihrem Webrollenprojekt die Datei *App_Start\BundleConfig.cs*, in der Sie jedem [Bundle-Konstruktor](http://msdn.microsoft.com/de-de/library/jj646464.aspx) eine CDN-URL hinzugefügt haben, und nehmen Sie die folgenden hervorgehobenen Änderungen vor, um den Standardbündeln einen Fallbackmechanismus hinzuzufügen:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	Wenn `CdnFallbackExpression` nicht null ist, wird ein Skript in den HTML-Code eingefügt, um zu testen, ob das Bündel erfolgreich geladen wurde, und bei Bedarf direkt vom ursprünglichen Webserver aus auf das Bündel zuzugreifen. Diese Eigenschaft muss auf einen JavaScript-Ausdruck festgelegt werden, der testet, ob das entsprechende CDN-Bündel ordnungsgemäß geladen wurde. Welcher Ausdruck zum Testen der einzelnen Bündel erforderlich ist, hängt von deren Inhalt ab. Für die vorstehenden Standardbündel gilt:
	
	-	`window.jquery` ist in jquery-{version}.js definiert
	-	`$.validator` ist in jquery.validate.js definiert
	-	`window.Modernizr` ist in modernizer-{version}.js definiert
	-	`$.fn.modal` ist in in bootstrap.js definiert.
	
	Sie haben vielleicht bemerkt, dass ich CdnFallbackExpression für das `~/Cointent/css`-Bündel nicht festgelegt habe. Der Grund ist, dass derzeit ein [Fehler in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) besteht, der ein `<script>`-Tag für das Fallback-CSS anstelle des erwarteten `<link>`-Tags einfügt.
	
	Es wird jedoch ein gutes [Style Bundle Fallback](https://github.com/EmberConsultingGroup/StyleBundleFallback) von der [Ember Consulting Group](https://github.com/EmberConsultingGroup) angeboten. 

2. Um die Problemumgehung zu verwenden, erstellen Sie im Ordner *App_Start* Ihres Webrollenprojekts eine neue CS-Datei namens *StyleBundleExtensions.cs*, und ersetzen Sie deren Inhalt durch den [Code von GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. Benennen Sie in der Datei *App_Start\StyleFundleExtensions.cs* den Namespace gemäß dem Namen Ihrer Webrolle um (z. B. **WebRole1**). 

3. Wechseln Sie zurück zu `App_Start\BundleConfig.cs`, und ändern Sie die letzte `bundles.Add`-Anweisung mit dem hervorgehobenen Code:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Diese neue Erweiterungsmethode fügt ebenfalls ein Skript in den HTML-Code ein, um im DOM nach einem Klassennamen, einem Regelnamen und einem Regelwert zu suchen, die mit den im CSS-Bündel definierten Namen/Werten übereinstimmen; wird keine Übereinstimmung gefunden, erfolgt ein Fallback zum ursprünglichen Webserver.

4. Veröffentlichen Sie den Cloud-Dienst erneut, und rufen Sie die Startseite auf. 
5. Zeigen Sie den HTML-Code für die Seite an. Es sollten eingefügte Skripts ähnlich den folgenden zu sehen sein:    
	<pre class="prettyprint">...
	
		<link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/>
	<mark><script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i < len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;<link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; />&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;<script src=&quot;/Content/css&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;></script>
	<mark><script>(window.Modernizr)||document.write(&#39;<script src=&quot;/bundles/modernizr&quot;><\/script>&#39;);</script></mark>
	
	...	
	
	    <script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;></script>
	<mark><script>(window.jquery)||document.write(&#39;<script src=&quot;/bundles/jquery&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;></script>
	<mark><script>($.fn.modal)||document.write(&#39;<script src=&quot;/bundles/bootstrap&quot;><\/script>&#39;);</script></mark>
	
	...
	</pre>

	Beachten Sie, dass das eingefügte Skript für das CSS-Bündel immer noch den fehlgeleiteten Rest aus der `CdnFallbackExpression`-Eigenschaft in der folgenden Zeile enthält:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Da jedoch der erste Teil des ||-Ausdrucks immer "true" zurückgibt (in der Zeile unmittelbar darüber), wird die document.write()-Funktion niemals ausgeführt.

# Weitere Informationen #
- [Übersicht über das Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Verarbeiten von Inhalt aus Azure CDN in einer Webanwendung](http://azure.microsoft.com/de-de/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [Integrieren einer Azure-Website in Azure CDN](http://azure.microsoft.com/de-de/documentation/articles/cdn-websites-with-cdn/)
- [ASP.NET-Bündelung und -Minimierung](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Verwenden von CDN für Azure](http://azure.microsoft.com/de-de/documentation/articles/cdn-how-to-use/)
