<properties 
	pageTitle="Integrieren eines Clouddiensts in Azure CDN" 
	description="Ein Lernprogramm, in dem Sie erfahren, wie Sie einen Clouddienst bereitstellen, der Inhalte von einem integrierten Azure CDN-Endpunkt zur Verfügung stellt." 
	services="cdn, cloud-services" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor="tysonn"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# <a name="intro"></a> Integrieren eines Clouddiensts in Azure CDN 

Ein Clouddienst kann in Azure CDN integriert werden, um beliebige Inhalte vom Speicherort des Clouddiensts zur Verfügung zu stellen. Dieser Ansatz bietet die folgenden Vorteile:

- Einfaches Bereitstellen und Aktualisieren von Bildern, Skripts und Stylesheets in den Projektverzeichnissen des Clouddiensts
- Einfaches Durchführen von Upgrades für die NuGet-Pakete im Clouddienst, wie z. B. jQuery- oder Bootstrap-Versionen 
- Verwalten der Webanwendung und des vom CDN verarbeiteten Inhalts über dieselbe Visual Studio-Oberfläche
- Einheitlicher Bereitstellungsworkflow für die Webanwendung und den vom CDN verarbeiteten Inhalt
- Integrieren von ASP.NET-Bündelung und -Minimierung in Azure CDN

## Sie lernen Folgendes ##

In diesem Lernprogramm lernen Sie Folgendes:

-	[Integrieren eines Azure CDN-Endpunkts in einen Clouddienst und Bereitstellen von statischen Inhalten in Webseiten aus Azure CDN](#deploy)
-	[Konfigurieren von Cacheeinstellungen für statische Inhalte im Clouddienst](#caching)
-	[Verarbeiten von Inhalten aus Controlleraktionen über Azure CDN](#controller)
-	[Verarbeiten von gebündelten und minimierten Inhalten über Azure CDN bei gleichzeitiger Beibehaltung des Skriptdebugvorgangs in Visual Studio](#bundling)
-	[Konfigurieren des Fallbacks für Skripts und CSS, wenn Ihr Azure CDN offline ist](#fallback) 

## Was Sie erstellen ##

Sie stellen eine Clouddienst-Webrolle unter Verwendung der ASP.NET-MVC-Standardvorlage bereit, fügen Code hinzu, um Inhalt aus einem integrierten Azure CDN zu verarbeiten, z. B. ein Bild, Ergebnisse von Controlleraktionen und die JavaScript- und CSS-Standarddateien; außerdem schreiben Sie Code zum Konfigurieren des Fallbackmechanismus für verarbeitete Bundles für den Fall, dass das CDN offline ist.

## Sie benötigen Folgendes ##

Für dieses Lernprogramm ist Folgendes erforderlich:

-	Ein aktives [Microsoft Azure-Konto](/account/)
-	Visual Studio 2015 mit [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. + Sie können [kostenlos ein Azure-Konto erstellen](/pricing/free-trial/): – Sie erhalten ein Guthaben, das Sie zum Ausprobieren der kostenpflichtigen Azure-Dienste nutzen können. Sie können das Konto behalten und weiterhin kostenlose Azure-Dienste wie z. B. Websites nutzen, wenn das Guthaben aufgebraucht ist. + Sie können von [Vorteilen für MSDN-Abonnenten profitieren](/pricing/member-offers/msdn-benefits-details/): – Über Ihr MSDN-Abonnement erhalten Sie jeden Monat Gutschriften, die Sie für kostenpflichtige Azure-Dienste einsetzen können.

<a name="deploy"></a>
## Bereitstellen eines Cloud-Diensts ##

In diesem Abschnitt stellen Sie die standardmäßige ASP.NET-MVC-Anwendungsvorlage in Visual Studio 2015 für eine Clouddienst-Webrolle bereit und integrieren sie anschließend in einen neuen CDN-Endpunkt. Befolgen Sie die nachstehenden Anweisungen:

1. Erstellen Sie in Visual Studio 2015 einen neuen Azure-Clouddienst über die Menüleiste, indem Sie **Datei > Neu > Projekt > Cloud > Azure-Clouddienst** wählen. Geben Sie einen Namen ein, und klicken Sie auf **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Wählen Sie **ASP.NET-Webrolle** aus, und klicken Sie auf die Schaltfläche **>**. Klicken Sie auf OK.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Wählen Sie **MVC** aus, und klicken Sie auf **OK**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Veröffentlichen Sie diese Webrolle jetzt in einem Azure-Clouddienst. Klicken Sie mit der rechten Maustaste auf das Clouddienstprojekt, und wählen Sie **Veröffentlichen** aus.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Wenn Sie sich noch nicht bei Microsoft Azure angemeldet haben, klicken Sie auf die Dropdownliste **Konto hinzufügen** und dann auf die Menüoption **Konto hinzufügen**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Melden Sie sich auf der Anmeldeseite mit dem Microsoft-Konto an, das Sie zum Aktivieren Ihres Azure-Kontos verwendet haben.
7. Sobald Sie angemeldet sind, klicken Sie auf **Weiter**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Davon ausgehend, dass Sie noch keinen Clouddienst und kein Speicherkonto erstellt haben, unterstützt Visual Studio Sie bei der Erstellung dieser beiden Komponenten. Geben Sie im Dialogfeld **Create Cloud Service and Account** den gewünschten Dienstnamen ein, und wählen Sie die Region aus. Klicken Sie dann auf **Erstellen**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Überprüfen Sie die Konfiguration auf der Seite mit den Veröffentlichungseinstellungen, und klicken Sie auf **Veröffentlichen**.

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE]Das Veröffentlichen von Clouddiensten dauert sehr lange. Mit der Option "Web Deploy für alle Webrollen aktivieren" können Sie das Debuggen des Clouddiensts erheblich beschleunigen, da hiermit schnelle (allerdings temporäre) Updates für Ihre Webrollen zur Verfügung gestellt werden. Weitere Informationen zu dieser Option finden Sie unter [Veröffentlichen eines Clouddiensts mit Azure Tools](http://msdn.microsoft.com/library/ff683672.aspx).

	Wenn im **Microsoft Azure-Aktivitätsprotokoll** als Veröffentlichungsstatus **Abgeschlossen** angezeigt wird, erstellen Sie einen CDN-Endpunkt, der in diesen Clouddienst integriert ist.
	
	>[AZURE.WARNING]Falls der bereitgestellte Clouddienst nach der Veröffentlichung eine Fehlermeldung anzeigt, verwendet der bereitgestellte Clouddienst wahrscheinlich ein [Gastbetriebssystem ohne .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates). Sie können dieses Problem durch das [Bereitstellen von .NET 4.5.2 als Startaufgabe](../cloud-services/cloud-services-dotnet-install-dotnet.md) umgehen.

## Erstellen eines neuen CDN-Profils

Ein CDN-Profil ist eine Sammlung von CDN-Endpunkten. Jedes Profil enthält mindestens einen CDN-Endpunkt. Sie können mehrere Profile verwenden, um Ihre CDN-Endpunkte nach Internetdomäne, Webanwendung oder anderen Kriterien zu organisieren.

> [AZURE.TIP]Wenn Sie bereits über ein CDN-Profil verfügen, das Sie für dieses Tutorial verwenden möchten, fahren Sie mit [Erstellen eines neuen CDN-Endpunkts](#create-a-new-cdn-endpoint) fort.

**So erstellen Sie ein neues CDN-Profil**

1. Klicken Sie im [Azure-Verwaltungsportal](https://portal.azure.com) oben links auf **Neu**. Wählen Sie auf dem Blatt **Neu** erst **Medien + CDN** und dann **CDN** aus.

    Das Blatt für das neue CDN-Profil wird angezeigt.
    
    ![Neues CDN-Profil][new-cdn-profile]

2. Geben Sie einen Namen für das CDN-Profil ein.

3. Wählen Sie einen **Tarif** aus, oder verwenden Sie den Standardtarif.

4. Wählen Sie eine **Ressourcengruppe** aus, oder erstellen Sie eine. Dies muss nicht dieselbe Ressourcengruppe wie beim Speicherkonto sein.

5. Wählen Sie das **Abonnement** für dieses CDN-Profil aus. Dies muss für dieses Tutorial dasselbe Abonnement wie für das Speicherkonto sein.

6. Wählen Sie einen **Speicherort** aus. Dies ist der Azure-Speicherort, an dem Ihre CDN-Profilinformationen gespeichert werden. Dies hat keine Auswirkung auf die Speicherorte von CDN-Endpunkten. Es muss nicht derselbe Speicherort wie für das Speicherkonto sein.

7. Klicken Sie auf die Schaltfläche **Erstellen**, um das neue Profil zu erstellen.

## Erstellen eines neuen CDN-Endpunkts

**So erstellen Sie einen neuen CDN-Endpunkt für das Speicherkonto**

1. Navigieren Sie im [Azure-Verwaltungsportal](https://portal.azure.com) zu Ihrem CDN-Profil. Eventuell haben Sie es im vorherigen Schritt an das Dashboard angeheftet. Andernfalls können Sie es ermitteln, indem Sie auf **Durchsuchen**, auf **CDN-Profile** und dann auf das Profil klicken, dem Sie den Endpunkt hinzufügen möchten.

    Das Blatt für das CDN-Profil wird angezeigt.
    
    ![CDN-Profil][cdn-profile-settings]
    
2. Klicken Sie auf die Schaltfläche **Endpunkt hinzufügen**.

    ![Schaltfläche „Endpunkt hinzufügen“][cdn-new-endpoint-button]

    Das Blatt **Endpunkt hinzufügen** wird angezeigt.
    
    ![Blatt „Endpunkt hinzufügen“][cdn-add-endpoint]

3. Geben Sie einen **Namen** für diesen CDN-Endpunkt ein. Dieser Name wird für den Zugriff auf die zwischengespeicherten Ressourcen in der Domäne `<EndpointName>.azureedge.net` verwendet.

4. Wählen Sie in der Dropdownliste **Ursprungstyp** die Option *Clouddienst* aus.

5. Wählen Sie in der Dropdownliste **Origin hostname** Ihren Clouddienst aus.

6. Behalten Sie die Standardwerte für **Ursprünglicher Pfad**, **Header des Ursprungshosts** und **Protocol/Origin port** bei. Sie müssen mindestens ein Protokoll (HTTP oder HTTPS) angeben.

7. Klicken Sie auf die Schaltfläche **Hinzufügen**, um den neuen Endpunkt zu erstellen.

8. Sobald der Endpunkt erstellt wurde, wird er in einer Liste von Endpunkten für das Profil angezeigt. In der Listenansicht werden der URL für den Zugriff auf zwischengespeicherte Inhalte sowie die Ursprungsdomäne angezeigt.

    ![CDN-Endpunkt][cdn-endpoint-success]

    > [AZURE.NOTE]Der Endpunkt kann nicht sofort verwendet werden. Es dauert bis zu 90 Minuten, bis die Registrierung über das CDN-Netzwerk weitergegeben wurde. Benutzer, die den CDN-Domänennamen sofort zu verwenden versuchen, sehen u. U. den Statuscode 404, bis die Inhalte über das CDN verfügbar sind.
	
## Testen des CDN-Endpunkts

Wenn der Veröffentlichungsstatus **Abgeschlossen** lautet, öffnen Sie ein Browserfenster, und navigieren Sie zu **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. In meinem Setup lautet diese URL:

	http://camservice.azureedge.net/Content/bootstrap.css

Dies entspricht der folgenden Ursprungs-URL am CDN-Endpunkt:

	http://camcdnservice.cloudapp.net/Content/bootstrap.css

Wenn Sie zu **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css** navigieren, werden Sie je nach Browser zum Herunterladen oder Öffnen der Datei „bootstrap.css“ aufgefordert, die von der veröffentlichten Web-App bereitgestellt wurde.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Auf die gleiche Weise können Sie direkt von Ihrem CDN-Endpunkt aus auf jede öffentlich zugängliche URL unter **http://*&lt;serviceName>*.cloudapp.net/** zugreifen. Beispiel:

-	Eine JS-Datei im Pfad "/Script"
-	Jede Inhaltsdatei im Pfad "/Content"
-	Jede Controlleraktion 
-	Sofern die Abfragezeichenfolge für den CDN-Endpunkt aktiviert ist, jede URL mit Abfragezeichenfolgen

Tatsächlich können Sie mit der vorstehenden Konfiguration den gesamten Clouddienst von **http://*&lt;cdnName>*.azureedge.net/** aus hosten. Wenn man zu ****http://camservice.azureedge.net/** navigiert, erhält man das Aktionsergebnis von "Startseite/Index".

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Dies bedeutet jedoch nicht, dass es immer (oder generell) einen gute Idee ist, einen gesamten Clouddienst über Azure CDN zu verarbeiten. Es gibt auch einige Nachteile:

-	Für diesen Ansatz muss die gesamte Website öffentlich sein, da Azure CDN zurzeit noch keine privaten Inhalte verarbeiten kann.
-	Falls der CDN-Endpunkt aus irgendeinem Grund – sei es wegen einer geplanten Wartung oder aufgrund eines Benutzerfehlers – offline geht, wird der gesamte Clouddienst offline geschaltet, es sei denn, die Kunden können an die Ursprungs-URL **http://*&lt;serviceName>*.cloudapp.net/** umgeleitet werden. 
-	Selbst bei Verwendung der benutzerdefinierten Cachesteuerungseinstellungen (siehe [Konfigurieren von Cacheoptionen für statische Dateien im Clouddienst](#caching)) wird durch einen CDN-Endpunkt die Leistung von besonders dynamischem Inhalt nicht verbessert. Wenn Sie wie oben gezeigt versucht haben, die Startseite vom CDN-Endpunkt aus zu laden, beachten Sie, dass das Laden der Standardstartseite (einer ziemlich einfachen Seite) beim ersten Mal mindestens fünf Sekunden gedauert hat. Stellen Sie sich vor, diese Seite enthält dynamischen Inhalt, der im Minutentakt aktualisiert werden muss – das Ergebnis wäre nicht besonders benutzerfreundlich. Das Verarbeiten von dynamischem Inhalt von einem CDN-Endpunkt aus erfordert einen kurzen Cacheablauf, was zu häufigen Cachefehlern am CDN-Endpunkt führt. Dies beeinträchtigt die Leistung des Clouddiensts und macht den Sinn und Zweck eines CDN zunichte.

Alternativ kann der von Azure CDN zu verarbeitende Inhalt von Fall zu Fall im Clouddienst bestimmt werden. Zu diesem Zweck haben Sie bereits gelernt, wie Sie vom CDN-Endpunkt aus auf einzelne Inhaltsdateien zugreifen. Im Abschnitt [Bereitstellen von Inhalten aus Controlleraktionen über Azure CDN](#controller) zeige ich Ihnen, wie Sie eine bestimmte Controlleraktion vom CDN-Endpunkt aus verarbeiten.

<a name="caching"></a>
## Konfigurieren von Cacheoptionen für statische Dateien im Clouddienst ##

Bei Integration von Azure CDN in Ihren Clouddienst können Sie angeben, wie statischer Inhalt im CDN-Endpunkt zwischengespeichert werden soll. Öffnen Sie hierzu die Datei *Web.config* aus Ihrem Webrollenprojekt (z. B. "WebRole1"), und fügen Sie ein `<staticContent>`-Element zu `<system.webServer>` hinzu. Mit dem folgenden XML-Code wird festgelegt, dass der Cache in drei Tagen abläuft.

	<system.webServer>
	  <staticContent>
	    <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
	  </staticContent>
	  ...
	</system.webServer>

Nachdem Sie dies konfiguriert haben, wenden alle statischen Dateien im Clouddienst die gleiche Regel im CDN-Cache an. Wenn Sie die Cacheeinstellungen genauer steuern möchten, fügen Sie eine *Web.config*-Datei in einen Ordner ein, und fügen Sie dieser Datei Ihre Einstellungen hinzu. Fügen Sie z. B. eine *Web.config*-Datei zum Ordner *\\Content* hinzu, und ersetzen Sie den Inhalt durch den folgenden XML-Code:

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

Wenn Sie eine Clouddienst-Webrolle in Azure CDN integrieren, ist es relativ einfach, Inhalt von Controlleraktionen über Azure CDN zu verarbeiten. Statt den Clouddienst direkt über Azure CDN bereitzustellen (siehe oben), können Sie hierfür auch einen witzigen MemeGenerator-Controller verwenden, wie [Maarten Balliauw](https://twitter.com/maartenballiauw) in seinem Vortrag zum Thema [Verringern von Latenz im Web mit Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) zeigt. Ich werde den Controller hier einfach nur reproduzieren.

Angenommen, Sie möchten in Ihrem Clouddienst Meme auf der Grundlage eines Bilds des jungen Chuck Norris generieren (Foto von [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)), ungefähr so:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Sie verfügen über eine einfache `Index`-Aktion, mit der die Kunden die Superlative im Bild angeben können; anschließend wird das Mem generiert, sobald die Aktion gepostet wird. Da es sich um Chuck Norris handelt, können Sie davon ausgehen, dass diese Seite weltweit extrem beliebt wird. Dies ist ein gutes Beispiel für das Verarbeiten von halbdynamischem Inhalt mit Azure CDN.

Führen Sie die vorstehenden Schritte aus, um diese Controlleraktion einzurichten:

1. Erstellen Sie im Ordner *\\Controllers* eine neue CS-Datei namens *MemeGeneratorController.cs*, und ersetzen Sie den Inhalt durch den folgenden Code. Achten Sie darauf, den hervorgehobenen Teil durch den Namen Ihres CDN zu ersetzen.  

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
		        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
		
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
		                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
		            }
		
		            return Content("<a href="" + Url.Action("Show", new {id = identifier}) + "">here's your meme</a>");
		        }
		
		        [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
		        public ActionResult Show(string id)
		        {
		            Tuple<string, string> data = null;
		            if (!Memes.TryGetValue(id, out data))
		            {
		                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
		            }
		
		            if (Debugger.IsAttached) // Preserve the debug experience
		            {
		                return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		            }
		            else // Get content from Azure CDN
		            {
		                return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		            }
		        }
		
		        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
		        public ActionResult Generate(string top, string bottom)
		        {
		            string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
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

2. Klicken Sie mit der rechten Maustaste auf die `Index()`-Standardaktion, und wählen Sie **Ansicht hinzufügen**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Akzeptieren Sie die folgenden Einstellungen, und klicken Sie auf **Hinzufügen**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Öffnen Sie die neue Datei *Views\\MemeGenerator\\Index.cshtml*, und ersetzen Sie den Inhalt durch den folgenden einfachen HTML-Code zum Übermitteln der Superlative:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Veröffentlichen Sie den Clouddienst erneut, und navigieren Sie in Ihrem Browser zu **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index**.

Wenn Sie die Formularwerte an `/MemeGenerator/Index` übermitteln, gibt die `Index_Post`-Aktionsmethode eine Link zur `Show`-Aktionsmethode mit der betreffenden Eingabe-ID zurück. Wenn Sie auf den Link klicken, erhalten Sie den folgenden Code:

	[OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
	public ActionResult Show(string id)
	{
		Tuple<string, string> data = null;
		if (!Memes.TryGetValue(id, out data))
		{
			return new HttpStatusCodeResult(HttpStatusCode.NotFound);
		}

		if (Debugger.IsAttached) // Preserve the debug experience
		{
			return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		}
		else // Get content from Azure CDN
		{
			return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
		}
	}
	
Wenn Ihr lokaler Debugger angefügt ist, erhalten Sie den standardmäßigen Debugvorgang mit einer lokalen Umleitung. Bei Ausführung im Clouddienst wird zu folgendem Ziel umgeleitet:

	http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Dies entspricht der folgenden Ursprungs-URL am CDN-Endpunkt:

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Anschließend können Sie mit dem `OutputCacheAttribute`-Attribut für die `Generate`-Methode angeben, wie das Aktionsergebnis zwischengespeichert werden soll; dies wird von Azure CDN berücksichtigt. Mit dem folgenden Code wird ein Cacheablauf von einer Stunde (3.600 Sekunden) angegeben.

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Entsprechend können Sie Inhalt von jeder beliebigen Controlleraktion über Azure CDN im Clouddienst verarbeiten, indem Sie die gewünschte Cacheoption verwenden.

Im nächsten Abschnitt erfahren Sie, wie Sie die gebündelten und minimierten Skripts und CSS über Azure CDN verarbeiten.

<a name="bundling"></a>
## Integrieren von ASP.NET-Bündelung und -Minimierung in Azure CDN ##

Skripts und CSS-Stylesheets ändern sich häufig und sind daher die idealen Kandidaten für den Azure CDN-Cache. Die Verarbeitung der gesamten Webrolle über Azure CDN ist die einfachste Möglichkeit, Bündelung und Minimierung in Azure CDN zu integrieren. Da Sie diese Methode jedoch möglicherweise nicht verwenden möchten, zeige ich Ihnen, wie Sie die Integration unter Wahrung der gewünschten Entwicklerumgebung von ASP.NET-Bündelung und -Minimierung durchführen können, die Ihnen z. B. Folgendes bietet:

-	Hervorragende Benutzerfreundlichkeit des Debugmodus
-	Vereinfachte Bereitstellung
-	Sofortige Updates für Clients für Skript-/CSS-Versionsupgrades
-	Fallbackmechanismus bei einem Fehler des CDN-Endpunkts
-	Minimale Codeänderung

Öffnen Sie *App\_Start\\BundleConfig.cs* in dem **WebRole1**-Projekt, das Sie in [Integrieren eines Azure CDN-Endpunkts in Ihre Azure-Website und Verarbeiten von statischem Inhalt in Webseiten über Azure CDN](#deploy) erstellt haben, und werfen Sie einen Blick auf die `bundles.Add()`-Methodenaufrufe.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

Die erste `bundles.Add()`-Anweisung fügt ein Skriptbundle im virtuellen Verzeichnis `~/bundles/jquery` hinzu. Öffnen Sie anschließend die Datei *Views\\Shared\_Layout.cshtml*, um sich anzusehen, wie das Skriptbundle-Tag gerendert wird. Sie sollten die folgende Zeile mit Razor-Code finden:

    @Scripts.Render("~/bundles/jquery")

Wenn dieser Razor-Code in der Azure-Webrolle ausgeführt wird, rendert er ein `<script>`-Tag für das Skriptbundle ähnlich dem folgenden:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Wenn Sie ihn jedoch in Visual Studio durch Drücken von `F5` ausführen, wird jede Skriptdatei im Bundle einzeln gerendert (das Bundle im vorstehenden Beispiel enthält nur eine Skriptdatei):

    <script src="/Scripts/jquery-1.10.2.js"></script>

So können Sie den JavaScript-Code in Ihrer Entwicklungsumgebung debuggen und in der Produktion gleichzeitige Clientverbindungen reduzieren (durch Bündelung) und die Leistung von Dateidownloads verbessern (durch Minimierung). Dies ist eine großartige Funktion, die es bei der Azure CDN-Integration aufrechtzuerhalten gilt. Da das gerenderte Bundle bereits eine automatisch generierte Versionszeichenfolge enthält, ist es außerdem empfehlenswert, diese Funktionalität zu replizieren, sodass bei jeder Aktualisierung der jQuery-Version durch NuGet die entsprechende Aktualisierung auf der Clientseite so schnell wie möglich erfolgen kann.

Führen Sie die folgenden Schritte aus, um ASP.NET-Bündelung und -Minimierung in Ihren CDN-Endpunkt zu integrieren.

1. Ändern Sie in der Datei *App\_Start\\BundleConfig.cs* die `bundles.Add()`-Methoden so ab, dass sie einen anderen [Bundle-Konstruktor](http://msdn.microsoft.com/library/jj646464.aspx) verwenden, und zwar einen, der eine CDN-Adresse angibt. Ersetzen Sie hierzu die `RegisterBundles`-Methodendefinition durch den folgenden Code:  

		public static void RegisterBundles(BundleCollection bundles)
		{
		    bundles.UseCdn = true;
		    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
		        .GetName().Version.ToString();
		    var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
		
		    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
		                "~/Scripts/jquery-{version}.js"));
		
		    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
		                "~/Scripts/jquery.validate*"));
		
		    // Use the development version of Modernizr to develop with and learn from. Then, when you're
		    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
		                "~/Scripts/modernizr-*"));
		
		    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
		                "~/Scripts/bootstrap.js",
		                "~/Scripts/respond.js"));
		
		    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
		                "~/Content/bootstrap.css",
		                "~/Content/site.css"));
		}

	Denken Sie daran, `<yourCDNName>` durch den Namen Ihres Azure CDN zu ersetzen.

	Einfach ausgedrückt, Sie legen `bundles.UseCdn = true` fest und haben jedem Bundle eine sorgfältig gebildete CDN-URL hinzugefügt. Beispielsweise ist der erste Konstruktor im Code:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	der gleiche wie:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

	Dieser Konstruktor weist die ASP.NET-Bündelung und -Minimierung an, beim lokalen Debuggen einzelne Skriptdateien zu rendern, aber mit der angegebenen CDN-Adresse auf das betreffende Skript zuzugreifen. Beachten Sie bei dieser sorgfältig gebildeten CDN-URL jedoch zwei wichtige Merkmale:
	
	-	Der Ursprung dieser CDN-URL ist `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`. Hierbei handelt es sich um das virtuelle Verzeichnis des Skriptbundles in Ihrem Clouddienst.
	-	Da Sie einen CDN-Konstruktor verwenden, enthält das CDN-Skripttag für das Bündel nicht länger die automatisch generierte Versionszeichenfolge in der gerenderten URL. Sie müssen bei jeder Änderung des Skriptbundles manuell eine eindeutige Versionszeichenfolge generieren, um einen Cachefehler im Azure CDN zu erzwingen. Gleichzeitig muss diese eindeutige Versionszeichenfolge während der gesamten Lebensdauer der Bereitstellung konstant bleiben, um Cachetreffer im Azure CDN zu minimieren, nachdem das Bündel bereitgestellt wurde.
	-	Die Abfragezeichenfolge v=<W.X.Y.Z> überträgt mithilfe von Pull aus *Properties\\AssemblyInfo.cs* in Ihrem Webrollenprojekt. Sie können den Bereitstellungsworkflow so konfigurieren, dass die Assemblyversion bei jeder Veröffentlichung in Azure schrittweise erhöht wird. Alternativ können Sie einfach *Properties\\AssemblyInfo.cs* in Ihrem Projekt so ändern, dass die Versionszeichenfolge bei jeder Erstellung automatisch schrittweise erhöht wird, indem Sie das Platzhalterzeichen "*" verwenden. Beispiel:

			[assembly: AssemblyVersion("1.0.0.*")]
	
		Sie können auch jede andere Strategie zur Vereinfachung der Generierung einer für die Lebensdauer einer Bereitstellung eindeutigen Zeichenfolge verwenden.

3. Veröffentlichen Sie den Clouddienst erneut, und rufen Sie die Startseite auf.
 
4. Zeigen Sie den HTML-Code für die Seite an. Die CDN-URL sollte bei jeder erneuten Veröffentlichung von Änderungen im Clouddienst mit einer eindeutigen Versionszeichenfolge gerendert angezeigt werden. Beispiel:

		...
		
		<link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
		
		<script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
		
		...
		
		<script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
		
		<script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
		
		...

5. Debuggen Sie den Clouddienst in Visual Studio, indem Sie `F5` drücken.

6. Zeigen Sie den HTML-Code für die Seite an. Jede Skriptdatei wird einzeln gerendert dargestellt, sodass Sie einen konsistenten Debugvorgang in Visual Studio erhalten.

		...
		
		    <link href="/Content/bootstrap.css" rel="stylesheet"/>
		<link href="/Content/site.css" rel="stylesheet"/>
		
		    <script src="/Scripts/modernizr-2.6.2.js"></script>
		
		...
		
		    <script src="/Scripts/jquery-1.10.2.js"></script>
		
		    <script src="/Scripts/bootstrap.js"></script>
		<script src="/Scripts/respond.js"></script>
		
		...   

<a name="fallback"></a>
## Fallbackmechanismus für CDN-URLs ##

Bei einem Fehler des Azure CDN-Endpunkts (gleich welcher Ursache) soll Ihre Webseite so intelligent sein, dass sie als Fallbackoption zum Laden von JavaScript oder Bootstrap auf den ursprünglichen Webserver zugreift. Wenn aufgrund der Nichterreichbarkeit des CDN Bilder auf der Website verloren gehen, ist dies eine Sache, wesentliche Seitenfunktionen zu verlieren, die durch Skripts und Stylesheets zur Verfügung gestellt werden, ist jedoch eine andere Sache.

Die [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx)-Klasse enthält eine Eigenschaft namens [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx), mit der Sie den Fallbackmechanismus für CDN-Fehler konfigurieren können. Zur Verwendung dieser Eigenschaft führen Sie die folgenden Schritte aus:

1. Öffnen Sie in Ihrem Webrollenprojekt die Datei *App\_Start\\BundleConfig.cs*, in der Sie jedem [Bundlekonstruktor](http://msdn.microsoft.com/library/jj646464.aspx) eine CDN-URL hinzugefügt haben, und nehmen Sie die folgenden hervorgehobenen Änderungen vor, um den Standardbundles einen Fallbackmechanismus hinzuzufügen:  

		public static void RegisterBundles(BundleCollection bundles)
		{
		    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
		        .GetName().Version.ToString();
		    var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
		    bundles.UseCdn = true;
		
		    bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")) 
						{ CdnFallbackExpression = "window.jquery" }
		                .Include("~/Scripts/jquery-{version}.js"));
		
		    bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")) 
						{ CdnFallbackExpression = "$.validator" }
		            	.Include("~/Scripts/jquery.validate*"));
		
		    // Use the development version of Modernizr to develop with and learn from. Then, when you're
		    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
		    bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")) 
						{ CdnFallbackExpression = "window.Modernizr" }
						.Include("~/Scripts/modernizr-*"));
		
		    bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")) 	
						{ CdnFallbackExpression = "$.fn.modal" }
		        		.Include(
			              		"~/Scripts/bootstrap.js",
			              		"~/Scripts/respond.js"));
		
		    bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
		                "~/Content/bootstrap.css",
		                "~/Content/site.css"));
		}

	Wenn `CdnFallbackExpression` nicht null ist, wird ein Skript in den HTML-Code eingefügt, um zu testen, ob das Bundle erfolgreich geladen wurde, und bei Bedarf direkt vom ursprünglichen Webserver aus auf das Bundle zuzugreifen. Diese Eigenschaft muss auf einen JavaScript-Ausdruck festgelegt werden, der testet, ob das entsprechende CDN-Bundle ordnungsgemäß geladen wurde. Welcher Ausdruck zum Testen der einzelnen Bundle erforderlich ist, hängt von deren Inhalt ab. Für die vorstehenden Standardbundles gilt:
	
	-	`window.jquery` ist in "jquery-{version}.js" definiert
	-	`$.validator` ist in "jquery.validate.js" definiert
	-	`window.Modernizr` ist in "modernizer-{version}.js" definiert
	-	`$.fn.modal` ist in "bootstrap.js" definiert
	
	Sie haben vielleicht bemerkt, dass ich "CdnFallbackExpression" für das `~/Cointent/css`-Bundle nicht festgelegt habe. Der Grund ist, dass derzeit ein [Fehler in System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) vorliegt, der ein `<script>`-Tag für das Fallback-CSS anstelle des erwarteten `<link>`-Tags einfügt.
	
	Es wird jedoch ein gutes [Style Bundle Fallback](https://github.com/EmberConsultingGroup/StyleBundleFallback) von [Ember Consulting Group](https://github.com/EmberConsultingGroup) angeboten.

2. Um die Problemumgehung für CSS zu verwenden, erstellen Sie im Ordner *App\_Start* Ihres Webrollenprojekts eine neue CS-Datei namens *StyleBundleExtensions.cs*, und ersetzen Sie deren Inhalt durch den [Code von GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. Benennen Sie in der Datei *App\_Start\\StyleFundleExtensions.cs* den Namespace entsprechend dem Namen Ihrer Webrolle um (z. B. **WebRole1**).

3. Wechseln Sie zurück zu `App_Start\BundleConfig.cs`, und ändern Sie die letzte `bundles.Add`-Anweisung mit dem folgenden hervorgehobenen Code:

		bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
		    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
		    .Include(
		          "~/Content/bootstrap.css",
		          "~/Content/site.css"));

	Diese neue Erweiterungsmethode fügt ebenfalls ein Skript in den HTML-Code ein, um im DOM nach einem Klassennamen, einem Regelnamen und einem Regelwert zu suchen, die mit den im CSS-Bündel definierten Namen/Werten übereinstimmen; wird keine Übereinstimmung gefunden, erfolgt ein Fallback zum ursprünglichen Webserver.

4. Veröffentlichen Sie den Clouddienst erneut, und rufen Sie die Startseite auf.

5. Zeigen Sie den HTML-Code für die Seite an. Es sollten eingefügte Skripts ähnlich den folgenden zu sehen sein:

		...
		
	    <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
		<script>(function() {
		                var loadFallback,
		                    len = document.styleSheets.length;
		                for (var i = 0; i < len; i++) {
		                    var sheet = document.styleSheets[i];
		                    if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
		                        var meta = document.createElement('meta');
		                        meta.className = 'sr-only';
		                        document.head.appendChild(meta);
		                        var value = window.getComputedStyle(meta).getPropertyValue('width');
		                        document.head.removeChild(meta);
		                        if (value !== '1px') {
		                            document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
		                        }
		                    }
		                }
		                return true;
		            }())||document.write('<script src="/Content/css"><\/script>');</script>
		
		    <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
		<script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
		
		... 
		
		    <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
		<script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
		
		    <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
		<script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
		
		...


	Beachten Sie, dass das eingefügte Skript für das CSS-Bundle immer noch den fehlgeleiteten Rest aus der `CdnFallbackExpression`-Eigenschaft in der folgenden Zeile enthält:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Da jedoch der erste Teil des ||-Ausdrucks immer "true" zurückgibt (in der Zeile unmittelbar darüber), wird die document.write()-Funktion niemals ausgeführt.

## Weitere Informationen ##
- [Übersicht über das Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Verarbeiten von Inhalt aus Azure CDN in einer Webanwendung](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Integrieren einer Azure-Website in Azure CDN](cdn-websites-with-cdn.md)
- [ASP.NET-Bündelung und -Minimierung](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Verwenden von CDN für Azure](cdn-how-to-use.md)


[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png

<!---HONumber=AcomDC_1203_2015-->