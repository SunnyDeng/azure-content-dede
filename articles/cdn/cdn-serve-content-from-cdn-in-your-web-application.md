<properties 
	pageTitle="Bereitstellen von Inhalt aus Azure CDN in einer Webanwendung" 
	description="Ein Lernprogramm, in dem Sie erfahren, wie Sie Inhalte von einem CDN verwenden, um die Leistung Ihrer Webanwendung zu verbessern." 
	services="cdn" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="cephalin"/>

# Bereitstellen von Inhalt aus Azure CDN in einer Webanwendung #

Dieses Lernprogramm zeigt, wie Sie von Azure CDN profitieren können, um die Reichweite und Leistung Ihrer Webanwendung zu verbessern. Azure CDN kann Ihnen in folgenden Fällen helfen, die Leistung Ihrer Webanwendung zu verbessern:

- Sie haben viele Links zu statischen oder halbstatischen Inhalten auf Ihren Seiten
- Clients greifen global auf Ihre Anwendung zu
- Sie möchten Datenverkehr von Ihrem Webserver auslagern
- Sie möchten die Anzahl gleichzeitiger Clientverbindungen zu Ihrem Webserver reduzieren (es gibt dazu eine großartige Diskussion unter [Bündelung und Minimierung](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)) 
- Sie möchten die erhaltene Lade-/Aktualisierungszeit Ihrer Seiten verbessern

## Sie lernen Folgendes ##

In diesem Lernprogramm lernen Sie Folgendes:

-	[Bereitstellen statischer Inhalte über einen Azure CDN-Endpunkt](#deploy)
-	[Automatisieren des Hochladens von Inhalten aus einer ASP.NET-Anwendung auf einen CDN-Endpunkt](#upload)
-	[Konfigurieren des CDN-Caches zur Wiedergabe der erwünschten Inhaltsaktualisierung](#update)
-	[Sofortiges Bereitstellen neuer Inhalte mithilfe von Abfragezeichenfolgen](#query)

## Sie benötigen Folgendes ##

Für dieses Lernprogramm ist Folgendes erforderlich:

-	Ein aktives [Microsoft Azure-Konto](/account/). Sie können sich für ein Testkonto anmelden.
-	Visual Studio 2013 mit [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) für die Blobverwaltungsoberfläche
-	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (verwendet von [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt](#upload))

> [AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. + Sie können [kostenlos ein Azure-Konto erstellen](/pricing/free-trial/?WT.mc_id=A261C142F): – Sie erhalten ein Guthaben, das Sie zum Ausprobieren der kostenpflichtigen Azure-Dienste nutzen können. Sie können das Konto behalten und weiterhin kostenlose Azure-Dienste wie z. B. Websites nutzen, wenn das Guthaben aufgebraucht ist. + Sie können von [Vorteilen für MSDN-Abonnenten profitieren](/pricing/member-offers/msdn-benefits-details/): – Über Ihr MSDN-Abonnement erhalten Sie jeden Monat Gutschriften, die Sie für kostenpflichtige Azure-Dienste einsetzen können.

<a name="static"></a>
## Bereitstellen statischer Inhalte über einen Azure CDN-Endpunkt ##

In diesem Abschnitt des Lernprogramms erfahren Sie, wie Sie ein CDN erstellen und dieses zum Verarbeiten statischer Inhalte verwenden. Die wichtigsten Schritte sind:

1. Speicherkonto erstellen
2. Erstellen eines mit dem Speicherkonto verknüpften CDN
3. Erstellen eines Blob-Containers im Speicherkonto
4. Hochladen von Inhalten zu Ihrem Blob-Container
5. Verknüpfen zum Inhalt, den Sie mithilfe der CDN-URL hochgeladen haben

Los geht's! Folgen Sie den Schritten unten, um mit der Verwendung des Azure CDN zu beginnen:

1. Zum Erstellen eines CDN-Endpunkts melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an. 
1. Erstellen Sie ein Speicherkonto, indem Sie auf **Neu > Datendienste > Speicher > Schnellerfassung** klicken. Geben Sie eine URL und einen Speicherort an, und klicken Sie auf **Speicherkonto erstellen**. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE]Ich verwende Ostasien als Region, da sich diese weit genug entfernt befindet, um mein CDN später von Nordamerika aus zu testen.

2. Sobald der Status des neuen Speicherkontos **Online** ist, erstellen Sie einen neuen CDN-Endpunkt, der mit dem erstellten Speicherkonto verknüpft ist. Klicken Sie auf **Neu > App-Dienste > CDN > Schnellerfassung**. Wählen Sie das erstellte Speicherkonto aus, und klicken Sie auf **Erstellen**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE]Sobald das CDN erstellt ist, wird im Azure-Portal dessen URL und die Ursprungsdomäne angezeigt, mit der es verknüpft ist. Jedoch kann es etwas dauern, bis die Konfiguration des CDN-Endpunkts vollständig an alle Knotenstandorte weitergegeben wurde.

3. Testen Sie, ob Ihr CDN-Endpunkt wirklich online ist, indem Sie einen Ping ausführen. Wenn Ihr CDN-Endpunkt nicht zu allen Knoten weitergegeben wurde, wird Ihnen eine ähnliche Nachricht wie unten angezeigt.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	Warten Sie eine Stunde, und testen Sie es dann erneut. Sobald der CDN-Endpunkt mit der Weitergabe an die Knoten fertig ist, sollte er auf Ihre Pings reagieren.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. An diesem Punkt können Sie bereits sehen, wo sich nach Meinung des CDN-Endpunkts der nächste CDN-Knoten befindet. Von meinem Desktopcomputer aus ist die antwortende IP-Adresse **93.184.215.201**. Wenn Sie diese bei einer Website wie [www.ip-address.org](http://www.ip-address.org) eingeben, erfahren Sie, dass sich der Server in Washington D.C. befindet.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	Eine Liste aller CDN-Knotenstandorte finden Sie unter [Standorte der Azure-CDN-Knoten (Content Delivery Network)](http://msdn.microsoft.com/library/azure/gg680302.aspx).

3. Klicken Sie im Azure-Portal auf der Registerkarte **CDN** auf den Namen des gerade erstellten CDN-Endpunkts.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. Klicken Sie auf **Suchabfragezeichenfolge aktivieren**, um Abfragezeichenfolgen im Azure CDN-Cache zu aktivieren. Sobald Sie diese Funktion aktivieren, wird derselbe Link, auf den Sie mit unterschiedlichen Abfragezeichenfolgen zugreifen, als separate Einträge zwischengespeichert.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE]Auch wenn die Aktivierung der Abfragezeichenfolge für diesen Teil des Lernprogramms nicht erforderlich ist, sollten Sie diesen Schritt so früh wie möglich durchführen, da die Verteilung einer hier vorgenommenen Änderung an die übrigen Knoten einige Zeit dauert und Sie verhindern möchten, dass Inhalte, für die Abfragezeichenfolgen nicht aktiviert wurden, den CDN-Cache verstopfen (die Aktualisierung von CDN-Inhalten wird später erläutert). Wie Sie davon profitieren können, erfahren Sie in [Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen](#query).

6. Klicken Sie in Visual Studio 2013 im Server-Explorer auf **Mit Microsoft Azure verbinden**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  Folgen Sie der Aufforderung, sich bei Ihrem Azure-Konto anzumelden.
8.  Erweitern Sie nach der Anmeldung **Microsoft Azure > Speicher > Ihr Speicherkonto**. Klicken Sie mit der rechten Maustaste auf **Blob**, und wählen Sie **Blob-Container erstellen**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	Geben Sie einen Namen für den Blob-Container an, und klicken Sie auf **OK**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	Doppelklicken Sie in Server-Explorer auf den erstellten Blob-Container, um diesen zu verwalten. Nun sollte die Verwaltungsoberfläche im mittleren Bereich angezeigt werden.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	Klicken Sie auf **Blob hochladen**, um Bilder, Skripte oder Stylesheets, die von Ihren Webseiten verwendet werden, in den Blob-Container hochzuladen. Der Hochladefortschritt wird im **Azure-Aktivitätsprotokoll** dargestellt, und die Blobs werden nach dem Hochladen in der Containeransicht angezeigt.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	Nach dem Hochladen des Blobs müssen Sie diese öffentlich machen, um darauf zuzugreifen. Klicken Sie in Server-Explorer mit der rechten Maustaste auf den Container und wählen Sie **Eigenschaften**. Legen Sie für die Eigenschaft **Öffentlicher Lesezugriff** die Option **Blob** fest.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	Testen Sie den öffentlichen Zugriff auf Ihre Blobs, indem Sie zur URL für einen der Blobs in einem Browser navigieren. Ich kann zum Beispiel das erste Bild in meiner hochgeladenen Liste mit `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png` testen.

	Ich verwende dafür nicht die HTTPS-Adresse, die in der Blob-Verwaltungsoberfläche in Visual Studio angegeben ist. Durch die Verwendung von HTTP testen Sie, ob der Inhalt öffentlich zugänglich ist, was für Azure CDN Voraussetzung ist.

13.	Wenn das Blob ordnungsgemäß im Browser ausgegeben wird, ändern Sie die URL von `http://<yourStorageAccountName>.blob.core.windows.net` in die URL Ihres Azure CDN. In meinem Fall verwende ich `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`, um das erste Bild auf meinem CDN-Endpunkt zu testen.

	>[AZURE.NOTE]Sie finden die URL des CDN-Endpunkts im Azure-Verwaltungsportal auf der Registerkarte "CDN".

	Wenn Sie die Leistung des direkten Blob-Zugriffs und CDN-Zugriffs vergleichen, können Sie den Leistungszuwachs durch die Verwendung von Azure CDN sehen. Unten finden Sie einen Screenshot der Internet Explorer 11 F12-Entwicklertools für den Blob-URL-Zugriff meines Bilds:

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	Sowie für den CDN-URL-Zugriff desselben Bilds

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	Achten Sie auf die Zahlen der **Anforderungszeit**. Dies ist die Zeit bis zum ersten Byte, bzw. die Zeit, die für das Senden der Anforderung und den Empfang der ersten Antwort des Servers benötigt wird. Wenn ich auf den Blob zugreife, der sich in der Region Ostasien befindet, dauert dies 266 ms, da die Anforderung den gesamten Pazifik überqueren muss, um zum Server zu gelangen. Wenn ich jedoch auf das Azure CDN zugreife, dauert es nur 16 ms. Dies ist fast ein **20-facher Leistungszuwachs**!
	
15.	Nun müssen Sie nur noch den neuen Link auf Ihrer Webseite verwenden. Ich kann zum Beispiel folgendes Bildtag hinzufügen:

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

In diesem Abschnitt haben Sie erfahren, wie Sie einen CDN-Endpunkt erstellen, Inhalte darauf hochladen, und diesen zu CDN-Inhalten einer beliebigen Webseite verknüpfen.

<a name="upload"></a>
## Automatisieren des Hochladens von Inhalten aus einer ASP.NET-Anwendung zu einem CDN-Endpunkt ##

Wenn Sie all Ihre statischen Inhalte Ihrer ASP.NET-Webanwendung zu einem CDN-Endpunkt hochladen möchten, oder wenn Sie die Webanwendung mit kontinuierlicher Zustellung bereitstellen (ein Beispiel finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md)), können Sie Azure PowerShell verwenden, um die Synchronisierung der neuesten Inhaltsdateien zu Azure-Blobs bei jeder Bereitstellung der Webanwendung zu automatisieren. Sie können zum Beispiel das Skript unter [Upload Content Files from ASP.NET Application to Azure Blobs](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) ausführen, um alle Inhaltsdateien in einer ASP.NET-Anwendung hochzuladen. So verwenden Sie dieses Skript:

4. Führen Sie im Menü **Start** **Microsoft Azure PowerShell** aus.
5. Führen Sie im Azure PowerShell-Fenster `Get-AzurePublishSettingsFile` aus, um eine Einstellungsveröffentlichungsdatei für Ihr Azure-Konto herunterzuladen.
6. Führen Sie nach dem Herunterladen der Einstellungsveröffentlichungsdatei Folgendes aus: 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE]Nach dem Importieren der Einstellungsveröffentlichungsdatei wird dies das Azure-Standardkonto, das für alle Azure PowerShell-Sitzungen verwendet wird. Dies bedeutet, dass Sie die Schritte oben nur einmal ausführen müssen.
	
1. Laden Sie das Skript von der [Downloadseite](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) herunter. Speichern Sie es im Projektordner der ASP.NET-Anwendung.
2. Klicken Sie mit der rechten Maustaste auf das heruntergeladene Skript, und klicken Sie auf **Eigenschaften**.
3. Klicken Sie auf **Nicht mehr blockieren**.
4. Öffnen Sie ein PowerShell-Fenster und führen Sie Folgendes aus:

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

Dieses Skript lädt alle Dateien aus den Ordnern *\Content* und *\Scripts* zum angegebenen Speicherkonto und Container hoch. Dies bietet folgende Vorteile:

-	Automatische Replikation der Dateistruktur des Visual Studio-Projekts
-	Automatisches Erstellen von Blob-Containern bei Bedarf
-	Wiederverwenden desselben Azure-Speicherkontos und CDN-Endpunkts für mehrere Webanwendungen, jeweils in einem separaten Blob-Container
-	Einfaches Aktualisieren des Azure CDN mit neuem Inhalt. Weitere Informationen zum Aktualisieren von Inhalten finden Sie unter [Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung](#update).

Für den `-StorageContainer`-Parameter ist es sinnvoll, den Namen Ihrer Webanwendung oder des Visual Studio-Projekts zu verwenden. Auch wenn ich zuvor das generische "cdn" als Containername verwendet habe, ermöglicht die Nutzung des Namens der Webanwendung, dass verwandte Inhalte im selben, leicht identifizierbaren Container angeordnet werden können.

Sobald die Inhalte hochgeladen sind, können Sie alle Elemente in den Ordnern *\Content* und *\Scripts* mithilfe von `http://<yourCDNName>.vo.msecnd.net/<containerName>` in Ihrem HTML-Code – zum Beispiel in Ihren CSHTML-Dateien – verknüpfen. Hier ist ein Beispiel von etwas, das ich in einer Razor-Ansicht verwenden kann:

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Ein Beispiel für die Integration von PowerShell-Skripten in Ihre kontinuierliche Zustellungskonfiguration finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md).

<a name="update"></a>
## Konfigurieren des CDN-Caches für die Wiedergabe der gewünschten Inhaltsaktualisierung ##

Angenommen, Sie möchten nach dem Hochladen der statischen Dateien von der Webanwendung in einen Blob-Container eine Änderung an einer der Dateien im Projekt vornehmen und diese anschließend erneut in den Blob-Container hochladen. Sie denken möglicherweise, dass dies automatisch am CDN-Endpunkt aktualisiert wird, und sind erstaunt, dass die Aktualisierung nicht angezeigt wird, wenn Sie auf die CDN-URL des Inhalts zugreifen.

Dies liegt daran, dass CDN nicht automatisch vom Blob-Speicher aktualisiert, sondern nur, wenn Sie eine Standard-7-Tage-Zwischenspeicherungsregel für den Inhalt anwenden. Dies bedeutet, dass dieser Inhalt, sobald ein CDN-Knoten Inhalte aus dem Blob-Speicher zieht, nicht mehr aktualisiert wird, bis er im Cache abläuft.

Die gute Nachricht ist, dass Sie den Cache-Ablauf anpassen können. Ähnlich wie die meisten Browser respektiert Azure CDN die Ablaufzeit, die im Cache-Control-Header angegeben wurde. Sie können einen benutzerdefinierten Wert für den Cache-Control-Header angeben, indem Sie zum Blob-Container im Azure-Portal navigieren und die Blob-Eigenschaften bearbeiten. Der Screenshot unten zeigt den auf 1 Stunde festgelegten Cache-Ablauf (3.600 Sekunden).

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

Sie können auch im PowerShell-Skript alle Cache-Control-Header des Blobs festlegen. Für das Skript in [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt](#upload) verwenden Sie folgenden Code-Ausschnitt:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

und ändern ihn wie folgt:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

Sie müssen möglicherweise auch mit dem neuen Cache-Control-Header weiterhin warten, bis die 7-tägige Zwischenspeicherung der Inhalte auf dem Azure CDN abläuft, bevor neue Inhalte abgerufen werden. Dies zeigt, dass benutzerdefinierte Zwischenspeicherungswerte nicht helfen, wenn Sie möchten, dass die Inhaltsaktualisierung sofort aktiv wird, zum Beispiel bei JavaScript- oder CSS-Aktualisierungen. Sie können dieses Problem jedoch umgehen, indem Sie die Dateien umbenennen oder deren Version durch Abfragezeichenfolgen verwalten. Weitere Informationen finden Sie unter [Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen](#query).

Es gibt natürlich berechtigte Zeiten und Orte für die Zwischenspeicherung. Angenommen, Sie haben zum Beispiel Inhalte, die keine häufige Aktualisierung erfordern, zum Beispiel anstehende WM-Spiele, die alle drei Stunden aktualisiert werden können, aber genug globalen Datenverkehr erhalten, der von Ihrem eigenen Webserver ausgelagert werden soll. Dies könnte ein guter Kandidat für die Azure CDN-Zwischenspeicherung sein.

<a name="query"></a>
## Sofortiges Bereitstellen neuer Inhalte mithilfe von Abfragezeichenfolgen ##

In Azure CDN können Sie Abfragezeichenfolgen aktivieren, sodass Inhalte von URLs mit bestimmten Abfragezeichenfolgen separat zwischengespeichert werden. Dies ist eine sehr nützliche Funktion, wenn Sie bestimmte Inhaltsaktualisierungen sofort an Clientbrowser weitergeben möchten, anstatt darauf zu warten, dass der zwischengespeicherte CDN-Inhalt abläuft. Nehmen wir an, ich veröffentliche meine Webseite mit einer Versionsnummer in der URL. <pre class="prettyprint"> &lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=3.0.0</mark>&quot; rel=&quot;stylesheet&quot;/&gt; </pre>

Wenn ich eine CSS-Aktualisierung veröffentliche und eine andere Versionsnummer in meiner CSS-URL verwende: <pre class="prettyprint"> &lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=3.1.1</mark>&quot; rel=&quot;stylesheet&quot;/&gt; </pre>

Für einen CDN-Endpunkt, für den Abfragezeichenfolgen aktiviert sind, sind die beiden URLs jeweils eindeutig, sodass dieser eine neue Anfrage an meinen Webserver machen wird, um die neue *bootstrap.css* abzurufen. Für einen CDN-Endpunkt, für den Abfragezeichenfolgen nicht aktiviert sind, sind dies identische URLs, sodass einfach die zwischengespeicherte *bootstrap.css* abgerufen wird.

Der Trick besteht darin, die Versionsnummer automatisch zu aktualisieren. In Visual Studio ist dies leicht zu bewerkstelligen. In einer CSHTML-Datei, in der ich den oben stehenden Link verwenden möchte, kann ich eine Versionsnummer basierend auf der Assemblynumber angeben. <pre class="prettyprint"> @{ <mark>var cdnVersion = System.Reflection.Assembly.GetAssembly( typeof(MyMvcApp.Controllers.HomeController)) .GetName().Version.ToString();</mark> }

...

&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=@cdnVersion</mark>&quot; rel=&quot;stylesheet&quot;/&gt; </pre>

Wenn Sie die Assemblynummer als Teil jedes Veröffentlichungszyklus ändern, können Sie sicher sein, dass Sie jedes Mal eine eindeutige Versionsnummer erhalten, wenn Sie die Webanwendung veröffentlichen. Diese bleibt bis zum nächsten Veröffentlichungszyklus identisch. Sie können Visual Studio auch die Assemblyversionsnummer jedes Mal automatisch erhöhen lassen, wenn die Webanwendung erstellt wird, indem Sie im Visual Studio-Projekt *Properties\AssemblyInfo.cs* öffnen und `*` in `AssemblyVersion` verwenden. Beispiel:

	[assembly: AssemblyVersion("1.0.0.*")]

## Wie sieht es mit gebündelten Skripts und Stylesheets in ASP.NET aus? ##

Mit [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) und [Azure Cloud Services](/services/cloud-services/) erhalten Sie die beste Azure CDN-Integration in die [ASP.NET-Bündelung und -Minimierung](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

Die Integration von Azure App Service oder Azure Cloud Services in Azure CDN bietet folgende Vorteile:

- Integration der Inhaltsbereitstellung (Bilder, Skripts und Stylesheets) als Teil des [kontinuierlichen Bereitstellungsprozesses](../web-sites-publish-source-control.md) Ihrer Azure-Web-App
- Einfaches Upgrade Ihrer vom CDN verarbeiteten NuGet-Pakete wie jQuery- oder Bootstrap-Versionen 
- Verwalten der Webanwendung und des vom CDN verarbeiteten Inhalts über dieselbe Visual Studio-Oberfläche

Zugehörige Lernprogramme finden Sie unter [Verwenden von Azure CDN in Azure App Service](../cdn-websites-with-cdn.md) und [Integrieren eines Clouddiensts in Azure CDN](cdn-cloud-service-with-cdn.md).

Ohne Integration in Azure App Service-Web-Apps oder Azure Cloud Services können Sie Azure CDN für Ihre Skriptbundles verwenden, allerdings mit den folgenden Nachteilen:

- Sie müssen die gebündelten Skripte manuell zum Blob-Speicher hochladen. Eine Programmierlösung wird auf [Stackoverflow](http://stackoverflow.com/a/13736433) vorgeschlagen.
- Wandeln Sie in den .cshtml-Dateien die gerenderten Skript-/CSS-Tags für die Verwendung von Azure CDN um. Beispiel:

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## Weitere Informationen ##
- [Übersicht über das Azure Content Delivery Network (CDN)](cdn-overview.md)
- [Verwenden von Azure CDN in Azure App Service](../cdn-websites-with-cdn.md)
- [Integrieren eines Clouddiensts in Azure CDN](cdn-cloud-service-with-cdn.md)
- [Zuordnen von CDN-Inhalten (Content Delivery Network) zu einer benutzerdefinierten Domäne](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Verwenden von CDN für Azure](cdn-how-to-use-cdn.md)
 

<!---HONumber=62-->