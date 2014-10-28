<properties linkid="cdn-serve-content-from-cdn-in-your-web-application" urlDisplayName="Use Content from a CDN in Your Web Application" pageTitle="Use Content from a CDN in Your Web Application" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN" description="A tutorial that teaches you how to use content from a CDN to improve the performance of your Web application." metaCanonical="" services="cdn" documentationCenter=".NET" title="Use Content from a CDN in Your Web Application" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Verarbeiten von Inhalt aus Azure CDN in einer Webanwendung

***Aktualisiert am 23. Juli 2014***

Dieses Lernprogramm zeigt, wie Sie von Azure CDN profitieren können, um die Reichweite und Leistung Ihrer Webanwendung zu verbessern. Azure CDN kann Ihnen in folgenden Fällen helfen, die Leistung Ihrer Webanwendung zu verbessern:

-   Sie haben viele Links zu statischen oder halbstatischen Inhalten auf Ihren Seiten
-   Clients greifen global auf Ihre Anwendung zu
-   Sie möchten Datenverkehr von Ihrem Webserver auslagern
-   Sie möchten die Anzahl gleichzeitiger Clientverbindungen zu Ihrem Webserver reduzieren (es gibt dazu eine großartige Diskussion unter [Bundling and Minification][Bundling and Minification] (Bündelung und Minimierung, in englischer Sprache)
-   Sie möchten die erhaltene Lade-/Aktualisierungszeit Ihrer Seiten verbessern

## Sie lernen Folgendes

In diesem Lernprogramm lernen Sie Folgendes:

-   [Verarbeiten statischer Inhalte von einem Azure CDN-Endpunkt][Verarbeiten statischer Inhalte von einem Azure CDN-Endpunkt]
-   [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt][Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt]
-   [Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung][Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung]
-   [Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen][Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen]

## Sie benötigen Folgendes

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Ein aktives [Microsoft Azure-Konto][Microsoft Azure-Konto]. Sie können sich für ein Testkonto anmelden
-   Visual Studio 2013 mit [Azure SDK][Azure SDK]
-   Eine einfache ASP.NET MVC-Anwendung, um CDN-URLs zu testen. [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt][Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt] verwendet als Beispiel eine ASP.NET MVC-Anwendung.
-   [Azure PowerShell][Azure PowerShell] (verwendet von [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt][Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt])

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Sie ben&ouml;tigen ein Windows Azure-Konto, um dieses Lernprogramm durchf&uuml;hren zu k&ouml;nnen.</h5>
  <ul>
    <li>Sie k&ouml;nnen <a href="http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A261C142F">ein Azure-Konto kostenlos erstellen</a>: Sie erhalten ein Guthaben, das Sie zum Ausprobieren der zahlungspflichtigen Azure-Dienste nutzen k&ouml;nnen, und Sie k&ouml;nnen das Konto selbst dann behalten und die kostenlose Azure-Dienste nutzen, wenn das Guthaben aufgebraucht ist.</li>
    <li>Sie k&ouml;nnen Ihre <a href="http://azure.microsoft.com/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">Vorteile f&uuml;r MSDN-Abonnenten aktivieren</a> - Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie f&uuml;r zahlungspflichtige Azure-Dienste verwenden k&ouml;nnen.</li>
  <ul>
</div>

<a name="static"></a>

## Verarbeiten statischer Inhalte von einem Azure CDN-Endpunkt

In diesem Abschnitt des Lernprogramms erfahren Sie, wie Sie ein CDN erstellen und dieses zum Verarbeiten statischer Inhalte verwenden. Die wichtigsten Schritte sind:

1.  Speicherkonto erstellen
2.  Erstellen eines mit dem Speicherkonto verknüpften CDN
3.  Erstellen eines Blob-Containers im Speicherkonto
4.  Hochladen von Inhalten zu Ihrem Blob-Container
5.  Verknüpfen zum Inhalt, den Sie mithilfe der CDN-URL hochgeladen haben

Los geht's! Folgen Sie den Schritten unten, um mit der Verwendung des Azure CDN zu beginnen:

1.  Zum Erstellen eines CDN-Endpunkts melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Erstellen Sie ein Speicherkonto, indem Sie auf **Neu \> Datendienste \> Speicher \> Schnellerfassung** klicken. Geben Sie eine URL und einen Speicherort an, und klicken Sie auf **Speicherkonto erstellen**.

    ![][]

    > [WACOM.NOTE] Ich verwende Ostasien als Region, da sich diese weit genug entfernt befindet, um mein CDN später von Nordamerika aus zu testen.

3.  Sobald der Status des neuen Speicherkontos **Online** ist, erstellen Sie einen neuen CDN-Endpunkt, der mit dem erstellten Speicherkonto verknüpft ist. Klicken Sie auf **Neu \> App-Dienste \> CDN \> Schnellerfassung**. Wählen Sie das erstellte Speicherkonto aus, und klicken Sie auf **Erstellen**.

    ![][1]

    > [WACOM.NOTE] Sobald das CDN erstellt ist, wird im Azure-Portal dessen URL und die Ursprungsdomäne, mit der es verknüpft ist, angezeigt. Jedoch kann es etwas dauern, bis die Konfiguration des CDN-Endpunkts vollständig an alle Knotenstandorte weitergegeben wurde.

4.  Testen Sie, ob Ihr CDN-Endpunkt wirklich online ist, indem Sie einen Ping ausführen. Wenn Ihr CDN-Endpunkt nicht zu allen Knoten weitergegeben wurde, wird Ihnen eine ähnliche Nachricht wie unten angezeigt.

    ![][2]

    Warten Sie eine Stunde, und testen Sie es dann erneut. Sobald der CDN-Endpunkt mit der Weitergabe an die Knoten fertig ist, sollte er auf Ihre Pings reagieren.

    ![][3]

5.  An diesem Punkt können Sie bereits sehen, wo sich nach Meinung des CDN-Endpunkts der nächste CDN-Knoten befindet. Von meinem Desktopcomputer aus ist die antwortende IP-Adresse **93.184.215.201**. Wenn Sie diese bei einer Website wie [www.ip-address.org][www.ip-address.org] eingeben, erfahren Sie, dass sich der Server in Washington D.C. befindet.

    ![][4]

    Eine Liste aller CDN-Knotenstandorte finden Sie unter [Standorte der Azure-CDN-Knoten (Content Delivery Network)][Standorte der Azure-CDN-Knoten (Content Delivery Network)].

6.  Klicken Sie im Azure-Portal auf der Registerkarte **CDN** auf den Namen des gerade erstellten CDN-Endpunkts.

    ![][5]

7.  Klicken Sie auf **Suchabfragezeichenfolge aktivieren**, um Abfragezeichenfolgen im Azure CDN-Cache zu aktivieren. Sobald Sie diese Funktion aktivieren, wird derselbe Link, auf den Sie mit unterschiedlichen Abfragezeichenfolgen zugreifen, als separate Einträge zwischengespeichert.

    ![][6]

    > [WACOM.NOTE] Auch wenn die Aktivierung der Abfragezeichenfolge für diesen Teil des Lernprogramms nicht erforderlich ist, sollten Sie diesen Schritt so früh wie möglich durchführen, da die Verteilung einer hier vorgenommenen Änderung an die übrigen Knoten einige Zeit dauert und Sie verhindern möchten, dass Inhalte, für die Abfragezeichenfolgen nicht aktiviert wurden, den CDN-Cache verstopfen (die Aktualisierung von CDN-Inhalten wird später erläutert). Wie Sie davon profitieren können, erfahren Sie in [Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen][Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen].

8.  Klicken Sie in Visual Studio 2013 in Server-Explorer auf **Mit Windows Azure verbinden**.

    ![][7]

9.  Folgen Sie der Aufforderung, sich bei Ihrem Azure-Konto anzumelden.
10. Erweitern Sie nach der Anmeldung **Windows Azure \> Speicher \> Ihr Speicherkonto**. Klicken Sie mit der rechten Maustaste auf **Blob**, und wählen Sie **Blob-Container erstellen**.

    ![][8]

11. Geben Sie einen Namen für den Blob-Container an, und klicken Sie auf **OK**.

    ![][9]

12. Doppelklicken Sie in Server-Explorer auf den erstellten Blob-Container, um diesen zu verwalten. Nun sollte die Verwaltungsoberfläche im mittleren Bereich angezeigt werden.

    ![][10]

13. Klicken Sie auf **Blob hochladen**, um Bilder, Skripte oder Stylesheets, die von Ihren Webseiten verwendet werden, in den Blob-Container hochzuladen. Der Hochladevorgang wird im **Microsoft Azure-Aktivitätsprotokoll** dargestellt, und die Blobs werden nach dem Hochladen in der Containeransicht angezeigt.

    ![][11]

14. Nach dem Hochladen des Blobs müssen Sie diese öffentlich machen, um darauf zuzugreifen. Klicken Sie in Server-Explorer mit der rechten Maustaste auf den Container und wählen Sie **Eigenschaften**. Legen Sie für die Eigenschaft **Öffentlicher Lesezugriff** die Option **Blob** fest.

    ![][12]

15. Testen Sie den öffentlichen Zugriff auf Ihre Blobs, indem Sie zur URL für einen der Blobs in einem Browser navigieren. Ich kann zum Beispiel das erste Bild in meiner hochgeladenen Liste mit `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png` testen.

    Ich verwende dafür nicht die HTTPS-Adresse, die in der Blobverwaltungsoberfläche in Visual Studio angegeben ist. Durch die Verwendung von HTTP testen Sie, ob der Inhalt öffentlich zugänglich ist, was für Azure CDN Voraussetzung ist.

16. Wenn Ihnen der Blob korrekt im Browser ausgegeben wird, ändern Sie die URL von `http://<yourStorageAccountName>.blob.core.windows.net` zur URL des Azure CDN. In meinem Fall würde ich `http://az623979.vo.msecnd.net/cdn/cephas_lin.png` verwenden, um das erste Bild auf meinem CDN-Endpunkt zu testen.

    > [WACOM.NOTE] Sie finden die URL des CDN-Endpunkts im Azure-Verwaltungsportal auf der Registerkarte „CDN“.

    Wenn Sie die Leistung des direkten Blobzugriffs und CDN-Zugriffs vergleichen, können Sie den Leistungszuwachs durch die Verwendung von Azure CDN sehen. Unten finden Sie einen Screenshot der Internet Explorer 11 F12-Entwicklertools für den Blob-URL-Zugriff meines Bilds:

    ![][13]

    Sowie für den CDN-URL-Zugriff desselben Bilds

    ![][14]

    Achten Sie auf die Zahlen der **Anforderungszeit**. Dies ist die Zeit bis zum ersten Byte, bzw. die Zeit, die für das Senden der Anforderung und den Empfang der ersten Antwort des Servers benötigt wird. Wenn ich auf den Blob zugreife, der sich in der Region Ostasien befindet, dauert dies 266 ms, da die Anforderung den gesamten Pazifik überqueren muss, um zum Server zu gelangen. Wenn ich jedoch auf das Azure CDN zugreife, dauert es nur 16 ms. Dies ist fast ein **20-facher Leistungszuwachs**!

17. Nun müssen Sie nur noch den neuen Link auf Ihrer Webseite verwenden. Ich kann zum Beispiel folgendes Bildtag hinzufügen:

        <img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

In diesem Abschnitt haben Sie erfahren, wie Sie einen CDN-Endpunkt erstellen, Inhalte darauf hochladen, und diesen zu CDN-Inhalten einer beliebigen Webseite verknüpfen.

<a name="upload"></a>

## Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt

Wenn Sie all Ihre statischen Inhalte Ihrer ASP.NET-Webanwendung zu einem CDN-Endpunkt hochladen möchten, oder wenn Sie die Webanwendung mit kontinuierlicher Zustellung bereitstellen (ein Beispiel finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure][Kontinuierliche Zustellung für Cloud Services in Azure]), können Sie Azure PowerShell verwenden, um die Synchronisierung der neuesten Inhaltsdateien zu Azure-Blobs bei jeder Bereitstellung der Webanwendung zu automatisieren. Sie können zum Beispiel das Skript unter [Upload Content Files from ASP.NET Application to Azure Blobs][Upload Content Files from ASP.NET Application to Azure Blobs] ausführen, um alle Inhaltsdateien in einer ASP.NET-Anwendung hochzuladen. So verwenden Sie dieses Skript:

1.  Klicken Sie im Menü **Start** auf **Windows Azure PowerShell**.
2.  Führen Sie im Azure PowerShell-Fenster `Get-AzurePublishSettingsFile` aus, um eine Einstellungsveröffentlichungsdatei für Ihr Azure-Konto herunterzuladen.
3.  Führen Sie nach dem Herunterladen der Einstellungsveröffentlichungsdatei Folgendes aus:

        Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

    > [WACOM.NOTE] Nach dem Importieren der Einstellungsveröffentlichungsdatei wird dies das Standard-Azure-Konto, das für alle Azure PowerShell-Sitzungen verwendet wird. Dies bedeutet, dass Sie die Schritte oben nur einmal ausführen müssen.

4.  Laden Sie das Skript von der [Downloadseite]((<http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a>) herunter. Speichern Sie es im Projektordner der ASP.NET-Anwendung.
5.  Klicken Sie mit der rechten Maustaste auf das heruntergeladene Skript, und klicken Sie auf **Eigenschaften**.
6.  Klicken Sie auf **Nicht mehr blockieren**.
7.  Öffnen Sie ein PowerShell-Fenster und führen Sie Folgendes aus:

        cd <ProjectFolder>
        .\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

Dieses Skript lädt alle Dateien aus den Ordnern *\\Content* und *\\Scripts* zum angegebenen Speicherkonto und Container hoch. Dies bietet folgende Vorteile:

-   Automatische Replikation der Dateistruktur des Visual Studio-Projekts
-   Automatisches Erstellen von Blob-Containern bei Bedarf
-   Wiederverwenden desselben Azure-Speicherkontos und CDN-Endpunkts für mehrere Webanwendungen, jeweils in einem separaten Blob-Container
-   Einfaches Aktualisieren des Azure CDN mit neuem Inhalt. Weitere Informationen zum Aktualisieren von Inhalten finden Sie unter [Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung][Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung].

Für den Parameter `-StorageContainer` ist es sinnvoll, den Namen der Webanwendung oder den Namen des Visual Studio-Projekts zu verwenden. Auch wenn ich zuvor das generische „cdn“ als Containername verwendet habe, ermöglicht die Nutzung des Namens der Webanwendung, dass verwandte Inhalte im selben, leicht identifizierbaren Container angeordnet werden können.

Sobald die Inhalte fertig hochgeladen sind, können Sie zu allen Elementen im Ordner *\\Content* und *\\Scripts* im HTML-Code, zum Beispiel in .cshtml-Dateien, mit `http://<yourCDNName>.vo.msecnd.net/<containerName>` verlinken. Hier ist ein Beispiel von etwas, das ich in einer Razor-Ansicht verwenden kann:

    <img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Ein Beispiel für die Integration von PowerShell-Skripten in Ihre kontinuierliche Zustellungskonfiguration finden Sie unter [Kontinuierliche Zustellung für Cloud Services in Azure][Kontinuierliche Zustellung für Cloud Services in Azure].

<a name="update"></a>

## Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung

Angenommen, Sie möchten nach dem Hochladen der statischen Dateien von der Webanwendung in einen Blob-Container eine Änderung an einer der Dateien im Projekt vornehmen und diese anschließend erneut in den Blob-Container hochladen. Sie denken möglicherweise, dass dies automatisch am CDN-Endpunkt aktualisiert wird, und sind erstaunt, dass die Aktualisierung nicht angezeigt wird, wenn Sie auf die CDN-URL des Inhalts zugreifen.

Dies liegt daran, dass CDN nicht automatisch vom Blob-Speicher aktualisiert, sondern nur, wenn Sie eine Standard-7-Tage-Zwischenspeicherungsregel für den Inhalt anwenden. Dies bedeutet, dass dieser Inhalt, sobald ein CDN-Knoten Inhalte aus dem Blob-Speicher zieht, nicht mehr aktualisiert wird, bis er im Cache abläuft.

Die gute Nachricht ist, dass Sie den Cache-Ablauf anpassen können. Ähnlich wie die meisten Browser respektiert Azure CDN die Ablaufzeit, die im Cache-Control-Header angegeben wurde. Sie können einen benutzerdefinierten Wert für den Cache-Control-Header angeben, indem Sie zum Blob-Container im Azure-Portal navigieren und die Blob-Eigenschaften bearbeiten. Der Screenshot unten zeigt den auf 1 Stunde festgelegten Cache-Ablauf (3.600 Sekunden).

![][15]

Sie können auch im PowerShell-Skript alle Cache-Control-Header des Blobs festlegen. Für das Skript in [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt][Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt] verwenden Sie folgenden Code-Ausschnitt:

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
        -Properties @{ContentType=$contentType, CacheControl="public, max-age=3600"} `
        -Force

Sie müssen möglicherweise auch mit dem neuen Cache-Control-Header weiterhin warten, bis die 7-tägige Zwischenspeicherung der Inhalte auf dem Azure CDN abläuft, bevor neue Inhalte abgerufen werden. Dies zeigt, dass benutzerdefinierte Zwischenspeicherungswerte nicht helfen, wenn Sie möchten, dass die Inhaltsaktualisierung sofort aktiv wird, zum Beispiel bei JavaScript- oder CSS-Aktualisierungen. Sie können dieses Problem jedoch umgehen, indem Sie die Dateien umbenennen oder deren Version durch Abfragezeichenfolgen verwalten. Weitere Informationen finden Sie unter [Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen][Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen].

Es gibt natürlich berechtigte Zeiten und Orte für die Zwischenspeicherung. Angenommen, Sie haben zum Beispiel Inhalte, die keine häufige Aktualisierung erfordern, zum Beispiel anstehende WM-Spiele, die alle drei Stunden aktualisiert werden können, aber genug globalen Datenverkehr erhalten, der von Ihrem eigenen Webserver ausgelagert werden soll. Dies könnte ein guter Kandidat für die Azure CDN-Zwischenspeicherung sein.

<a name="query"></a>

## Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen

In Azure CDN können Sie Abfragezeichenfolgen aktivieren, sodass Inhalte von URLs mit bestimmten Abfragezeichenfolgen separat zwischengespeichert werden. Dies ist eine wunderbare Funktion, wenn Sie bestimmte Inhaltsaktualisierungen sofort an Clientbrowser weitergeben möchten, anstatt darauf zu warten, dass das zwischengespeicherte CDN abläuft. Nehmen wir an, dass ich meine Webseite mit einer Versionsnummer in der URL veröffentliche.

<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
Wenn ich eine CSS-Aktualisierung veröffentliche und eine andere Versionsnummer in meiner CSS-URL verwende:

<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
Für einen CDN-Endpunkt, für den Abfragezeichenfolgen aktiviert sind, sind die beiden URLs jeweils eindeutig, sodass dieser eine neue Anfrage an meinen Webserver machen wird, um die neue *bootstrap.css* abzurufen. Für einen CDN-Endpunkt, für den Abfragezeichenfolgen nicht aktiviert sind, sind dies identische URLs, sodass einfach die zwischengespeicherte *bootstrap.css* abgerufen wird.

Der Trick besteht darin, die Versionsnummer automatisch zu aktualisieren. In Visual Studio ist dies ganz einfach. In einer .cshtml-Datei, in der ich den Link oben verwenden würde, kann ich eine Versionsnummer basierend auf der Assembly-Nummer angeben.

<pre class="prettyprint">
@{ var cdnVersion = System.Reflection.Assembly.GetAssembly( typeof(MyMvcApp.Controllers.HomeController)) .GetName().Version.ToString();}
...
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
Wenn Sie die Assembly-Nummer als Teil jedes Veröffentlichungszyklus ändern, können Sie sicher sein, dass Sie jedes Mal eine einzigartige Versionsnummer erhalten, wenn Sie die Webanwendung veröffentlichen. Diese bleibt bis zum nächsten Veröffentlichungszyklus identisch. Sie können Visual Studio auch dazu bringen, jedes Mal automatisch die Assembly-Versionsnummer zu erhöhen, wenn die Webanwendung erstellt wird, indem Sie *Properties\\AssemblyInfo.cs* im Visual Studio-Projekt öffnen und `*` in `AssemblyVersion` verwenden. Beispiel:

    [assembly: AssemblyVersion("1.0.0.*")]

## Was ist mit gebündelten Skripten und CSS?

Derzeit befindet sich der einzige Ort, an dem Sie eine adäquate Integration zwischen ASP.NET-Bündelung und Azure CDN finden, in [Azure Cloud Services][Azure Cloud Services]. Ohne Azure Cloud Services können Sie Azure CDN für Skriptbündel mit folgenden Vorbehalten verwenden:

-   Sie müssen die gebündelten Skripte manuell zum Blob-Speicher hochladen. Eine Programmierlösung wird auf [Stackoverflow][Stackoverflow] vorgeschlagen.
-   Wandeln Sie in den .cshtml-Dateien die gerenderten Skript-/CSS-Tags für die Verwendung von Azure CDN um. Beispiel:

        @Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

Weitere Informationen zur Integration von Azure CDN in Azure Cloud Services finden Sie unter [Integrieren einer Cloudanwendung in Azure CDN][Integrieren einer Cloudanwendung in Azure CDN].

# Weitere Informationen

-   [Übersicht über das Azure Content Delivery Network (CDN)][Übersicht über das Azure Content Delivery Network (CDN)]
-   [Integrieren einer Cloudanwendung in Azure CDN][16]
-   [Verwenden von CDN für Azure][Verwenden von CDN für Azure]

  [Bundling and Minification]: http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification
  [Verarbeiten statischer Inhalte von einem Azure CDN-Endpunkt]: #deploy
  [Automatisieren des Hochladens von Inhalten von einer ASP.NET-Anwendung zu einem CDN-Endpunkt]: #upload
  [Konfigurieren des CDN-Cache für die Wiedergabe der erwünschten Inhaltsaktualisierung]: #update
  [Sofortiges Verarbeiten neuer Inhalte mit Abfragezeichenfolgen]: #query
  [Microsoft Azure-Konto]: http://azure.microsoft.com/de-de/account/
  [Azure SDK]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
  [ein Azure-Konto kostenlos erstellen]: http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A261C142F
  [Vorteile für MSDN-Abonnenten aktivieren]: http://azure.microsoft.com/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  []: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG
  [1]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG
  [2]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG
  [3]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG
  [www.ip-address.org]: http://www.ip-address.org
  [4]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG
  [Standorte der Azure-CDN-Knoten (Content Delivery Network)]: http://msdn.microsoft.com/de-de/library/azure/gg680302.aspx
  [5]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG
  [6]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG
  [7]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG
  [8]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG
  [9]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG
  [10]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG
  [11]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG
  [12]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG
  [13]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG
  [14]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG
  [Kontinuierliche Zustellung für Cloud Services in Azure]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Upload Content Files from ASP.NET Application to Azure Blobs]: http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a
  [15]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG
  [Azure Cloud Services]: http://azure.microsoft.com/de-de/services/cloud-services/
  [Stackoverflow]: http://stackoverflow.com/a/13736433
  [Integrieren einer Cloudanwendung in Azure CDN]: http://azure.microsoft.com/de-de/documentation/articles/cloud-services-how-to-create-deploy/
  [Übersicht über das Azure Content Delivery Network (CDN)]: http://msdn.microsoft.com/library/azure/ff919703.aspx
  [16]: http://azure.microsoft.com/de-de/Documentation/Articles/cdn-cloud-service-with-cdn/
  [Verwenden von CDN für Azure]: http://azure.microsoft.com/de-de/documentation/articles/cdn-how-to-use/
