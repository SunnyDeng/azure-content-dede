<properties 
	pageTitle="Bereitstellen von Inhalten für Kunden – Übersicht" 
	description="Dieses Thema bietet einen Überblick darüber, was zur Bereitstellung von Inhalten mit Azure Media Services erforderlich ist." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"  
	ms.author="juliako"/>


#Bereitstellen von Inhalten für Kunden – Übersicht

##Übersicht

Bei der Übermittlung Ihrer Inhalte für Kunden \(Streaming von Liveereignissen oder Video-on-Demand\) besteht Ihr Ziel darin, qualitativ hochwertige Videos für unterschiedliche Geräte unter verschiedenen Netzwerkbedingungen zu übermitteln.

So erreichen Sie dieses Ziel:

- Codieren Sie den Stream in einen Videostream mit mehreren Bitraten \(mit adaptiver Bitrate\) \(dies betrifft die Qualität und die Netzwerkbedingungen\) und 
- verwenden Sie [Media Services Dynamic Packaging](media-services-dynamic-packaging-overview.md), um den Stream dynamisch erneut in verschiedene Protokolle zu packen \(dies betrifft das Streaming auf verschiedenen Geräten\). Media Services unterstützt die Übermittlung der folgenden Streamingtechnologien mit adaptiver Bitrate: HTTP Live Streaming \(HLS\), Smooth Streaming, MPEG DASH und HDS \(nur mit Adobe PrimeTime/Access-Lizenz\).

Dieses Thema bietet einen Überblick über die [Konzepte für die Inhaltsbereitstellung](media-services-deliver-content-overview.md#concepts) sowie Links zu Themen, in denen erläutert wird, wie Sie die für die Bereitstellung von Inhalten erforderlichen [Aufgaben](media-services-deliver-content-overview.md#tasks) durchführen.

##<a id="concepts"></a>Konzepte

In der folgenden Liste werden hilfreiche Begriffe und Konzepte beim Bereitstellen von Medien beschrieben.

###Dynamische Paketerstellung

Es wird empfohlen, eine dynamische Paketerstellung zu verwenden, um die Inhalte bereitzustellen. Weitere Informationen finden Sie unter [Dynamische Paketerstellung](media-services-dynamic-packaging-overview.md).

Um dynamische Paketerstellung nutzen zu können, ist mindestens eine bedarfsgesteuerte Streamingeinheit für den Streamingendpunkt erforderlich, aus dem die Inhalte bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Skalieren von Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

###Filter und dynamische Manifeste

Mit Media Services können Sie Filter für Ihre Medienobjekte definieren. Diese Filter sind serverseitige Regeln, mit denen Ihre Kunden verschiedene Aktionen ausführen können, z. B. Wiedergabe bestimmter Videoabschnitte \(anstelle des gesamten Videos\). Sie können zudem nur eine Teilmenge von Audio- und Videowiedergaben \(anstelle von allen mit dem Medienobjekt verknüpften Wiedergaben\) angeben, die für das Gerät eines Kunden geeignet sind. Diese Filterung der Medienobjekte erfolgt über **dynamische Manifeste**, die auf Anfrage des Kunden zum Streamen von Videos basierend auf bestimmten Filtern erstellt werden.

Weitere Informationen finden Sie unter [Filter und dynamische Manifeste](media-services-dynamic-manifest-overview.md).

###Locators

Um Ihren Benutzern eine URL für das Streaming bzw. Herunterladen des Inhalts angeben zu können, müssen Sie zunächst das Medienobjekt "veröffentlichen", indem Sie einen Locator erstellen. Locator bieten einen Einstiegspunkt für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien. Media Services unterstützt zwei Arten von Locatorobjekten:

- **OnDemandOrigin**-Locators, die zum Streaming von Medien \(z. B. MPEG DASH, HLS oder Smooth Streaming\) und zum Herunterladen von Dateien dienen.
-  **SAS**-URL-Locators \(Zugriffssignatur\), die zum Herunterladen von Mediendateien auf den lokalen Computer dienen. 

Anhand einer **Zugriffsrichtlinie** werden die Berechtigungen eines Clients \(z. B. Lesen, Schreiben und Anzeigen\) und die Dauer definiert, für die der Client auf eine bestimmte Ressource zugreifen kann. Beachten Sie, dass die Berechtigung zum Auflisten \(AccessPermissions.List\) beim Erstellen eines OrDemandOrigin-Locators nicht verwendet werden sollte.

Locator haben ein Ablaufdatum. Wenn Sie Medienobjekte über das Portal veröffentlichen, werden Locator mit einem Ablaufdatum von 100 Jahren erstellt.

>[AZURE.NOTE]Wenn Sie das Portal vor März 2015 zum Erstellen von Locators verwendet haben, wurden diese mit einem Ablaufdatum von zwei Jahren erstellt.

Verwenden Sie zum Aktualisieren des Ablaufdatums für einen Locator die [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator)- oder [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-APIs. Wenn Sie das Ablaufdatum eines SAS-Locators aktualisieren, ändert sich auch die URL.
 
Locator sind nicht für die Verwaltung der Zugriffssteuerung pro Benutzer konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte \(Digital Rights Management, DRM\). Weitere Informationen finden Sie unter [Sichern von Medien](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Beachten Sie, dass beim Erstellen eines Locators möglicherweise eine Verzögerung von 30 Sekunden auftritt, die durch die erforderlichen Speicher- und Weitergabeprozesse in Azure Storage verursacht wird.


###Adaptives Streaming 

Bei adaptiven Bitratentechnologien können Videoplayeranwendungen die Netzwerkbedingungen ermitteln und eine Auswahl aus mehreren Bitraten treffen. Wenn die Netzwerkleistung absinkt, kann der Client eine niedrigere Bitrate auswählen, und der Player kann die Wiedergabe des Videos mit einer geringeren Videoqualität fortsetzen. Verbessert sich die Netzwerkleistung, kann der Client auf eine höhere Bitrate umschalten und eine verbesserte Videoqualität anbieten. Von Azure Media Services werden die folgenden Technologien mit adaptiver Bitrate unterstützt: HTTP Live Streaming \(HLS\), Smooth Streaming, MPEG DASH und HDS.

Um Benutzern Streaming-URLs bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt mit den Inhalten, die Sie streamen möchten. Um diese Inhalte streamen zu können, müssen Sie diesen Pfad jedoch ändern. Zum Erstellen einer vollständigen URL für die Streaming-Manifestdatei müssen Sie den Pfadwert des Locators mit dem Dateinamen des Manifests \(dateiname.ism\) verketten. Fügen Sie dem Locatorpfad anschließend "/Manifest" und ein geeignetes Format \(falls erforderlich\) hinzu.

>[AZURE.NOTE]Sie können auch den Inhalt über eine SSL-Verbindung streamen. Zu diesem Zweck stellen Sie sicher, dass die Streaming-URLs mit HTTPS beginnen.

Beachten Sie, dass Sie nur über SSL streamen können, wenn der Streaming-Endpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streaming-Endpunkten basieren, die nach dem 10. September erstellt wurden, enthält die URL "streaming.mediaservices.windows.net" \(neues Format\). Streaming-URLs, die "origin.mediaservices.windows.net" \(das alte Format\) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, erstellen Sie einen neuen Streamingendpunkt. Verwenden Sie die URLs, die basierend auf dem neuen Streaming-Endpunkt erstellt wurden, um Ihre Inhalte über SSL zu streamen.


####Streaming-URL-Formate:

**MPEG DASH-Format**

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest\(Format=mpd-time-csf\)

Beispiel

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Apple HTTP Live Streaming \(HLS\) V4-Format**

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest\(Format=m3u8-aapl\)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Apple HTTP Live Streaming \(HLS\) V3-Format**

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest\(Format=m3u8-aapl-v3\)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)


**Smooth Streaming-Format**

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest

Beispiel:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

**Smooth Streaming 2.0-Manifest \(Legacymanifest\)**

Das Smooth Streaming-Manifestformat enthält standardmäßig das repeat-Tag \(r-tag\). Einige Player unterstützen das r-Tag jedoch nicht. Für diese Clients kann ein Format verwendet werden, mit dem das r-Tag deaktiviert wird:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest\(Format=fmp4-v20\)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

**HDS \(nur für Lizenznehmer von Adobe PrimeTime/Access\)**

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest\(Format=f4m-f4f\)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


###Dynamische Paketerstellung

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden \(MPEG DASH, HLS, Smooth Streaming, HDS\), ohne dass Sie diese Streamingformate erneut verpacken müssen.

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren Ihrer Zwischendatei \(Quelldatei\) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate
- Abrufen von mindestens einer On-Demand-Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für bedarfsgesteuertes Streaming](media-services-manage-origins.md#scale_streaming_endpoints). 

Mit der dynamischen Paketerstellung müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client.

Beachten Sie, dass die reservierten Einheiten für On-Demand-Streaming Ihnen zusätzlich zur dynamischen Paketerstellung auch eine dedizierte Ausgangskapazität bereitstellen, die in Schritten von 200 Mbit/s erworben werden kann. Standardmäßig wird das bedarfsgesteuerte Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen \(z. B. Rechen- und Ausgangskapazität usw.\) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Durchsatz des bedarfsgesteuerten Streamings zu erhöhen, sollten Sie reservierte Einheiten für On-Demand Streaming kaufen.

###Progressiver Download 

Der progressive Download ermöglicht die Wiedergabe von Medien, bevor die gesamte Datei heruntergeladen wurde. Sie können Dateien mit der Endung ".ism*" \(ISMV, ISMA, ISMT, ISMC\) nicht progressiv herunterladen.

Verwenden Sie den "OnDemandOrigin"-Typ des Locator zum progressiven Herunterladen von Inhalten. Das folgende Beispiel zeigt die URL, die auf dem OnDemandOrigin-Typ des Locators basiert:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Es gelten die folgenden Bedingungen:

- Sie müssen alle speicherverschlüsselten Medienobjekte entschlüsseln, die Sie aus dem ursprünglichen Dienst für den progressiven Download streamen möchten.


###Herunterladen

Um Ihre Inhalte auf ein Clientgerät herunterzuladen, müssen Sie einen SAS-Locator erstellen. Die SAS-Locator ermöglicht den Zugriff auf den Azure Storage-Container, in dem sich die Datei befindet. Zum Erstellen der Download-URL müssen Sie den Dateinamen zwischen dem Host und der SAS-Signatur einbetten.

Das folgende Beispiel zeigt die URL, die auf dem SAS-Locator basiert:

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Es gelten die folgenden Bedingungen:

- Sie müssen alle speicherverschlüsselten Medienobjekte entschlüsseln, die Sie aus dem ursprünglichen Dienst für den progressiven Download streamen möchten.
- Ein Download, der nicht innerhalb von zwölf Stunden abgeschlossen wird, schlägt fehl.



###Streamingendpunkte

Ein **Streamingendpunkt** stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Clientwiedergabeanwendung oder einem Content Delivery Network \(CDN\) bereitstellen kann. Der ausgehende Stream des Streamingendpunkt-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. Darüber hinaus können Sie die Kapazität des Streamingendpunkt-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie die reservierten Einheiten für das Streaming anpassen. Anwendungen in einer Produktionsumgebung sollten Sie mindestens eine reservierte Einheit zuweisen. Weitere Informationen finden Sie unter [Skalieren eines Mediendiensts](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="tasks"></a>Aufgaben für das Bereitstellen von Medienobjekten


###Konfigurieren von Streamingendpunkten

Eine Übersicht über Streamingendpunkte sowie Informationen zu deren Verwaltung finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md).

###Hochladen von Medien 

Laden Sie Ihre Dateien mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET** oder **REST-API** hoch.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###Codieren von Medienobjekten

Führen Sie die Codierung mit **Azure Media Encoder** mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET**, oder **REST-API** aus.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

###Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

###Veröffentlichen von Medienobjekten

Veröffentlichen von Medienobjekten \(durch Locator-Erstellung\) mithilfe des **Azure-Verwaltungsportals** oder mithilfe von **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Verwandte Themen

[Aktualisieren von Media Services nach dem Austausch der Speicherschlüssel](media-services-roll-storage-access-keys.md)
 

<!---HONumber=August15_HO7-->