<properties 
	pageTitle="Bereitstellen von Inhalten für Kunden - Übersicht" 
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
	ms.date="04/08/2015" 
	ms.author="juliako"/>


#Bereitstellen von Inhalten für Kunden - Übersicht

##Übersicht

Bei der Arbeit mit Media Services werden die folgenden Schritte häufig ausgeführt:

1. Hochladen einer Eingabedatei (auch Mezzanine-Datei genannt) in ein Medienobjekt, z. B. H.264, MP4 oder WMV.
1. Codieren des Medienobjekts in einen Satz von MP4-Dateien mit adaptiver Bitrate.
1. Veröffentlichen Sie das Medienobjekt. 
2. Verwenden der [dynamischen Paketerstellung](http://msdn.microsoft.com/library/azure/jj889436.aspx) für die Bereitstellung der Inhalte für die Kunden in einem der folgenden Formate: MPEG DASH, Apple HLS oder Smooth Streaming. 

Dieses Thema bietet einen Überblick über die wichtigsten [Konzepte](media-services-deliver-content.md#concepts) sowie Links zu Anleitungen für das Durchführen der für Bereitstellung von Inhalten erforderlichen [Aufgaben](media-services-deliver-content.md#tasks).

##<a id="concepts"></a>Konzepte

In der folgenden Liste werden hilfreiche Begriffe und Konzepte beim Bereitstellen von Medien beschrieben.

###Locator

Um Ihren Benutzern eine URL für das Streaming bzw. Herunterladen des Inhalts angeben zu können, müssen Sie zunächst das Medienobjekt "veröffentlichen", indem Sie einen Locator erstellen.  Locator bieten einen Einstiegspunkt für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien. Media Services unterstützt zwei Arten von Locatorobjekten: 

- **OnDemandOrigin**-Locator, die zum Streaming von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) und zum Herunterladen von Dateien dienen.
-  **SAS**-URL-Locator (Zugriffssignatur), die zum Herunterladen von Mediendateien auf den lokalen Computer dienen. 

Anhand einer **Zugriffsrichtlinie** werden die Berechtigungen eines Clients (z. B. Lesen, Schreiben und Anzeigen) und die Dauer definiert, für die der Client auf eine bestimmte Ressource zugreifen kann. Beachten Sie, dass die Berechtigung zum Auflisten (AccessPermissions.List) beim Erstellen eines OrDemandOrigin-Locators nicht verwendet werden sollte.

Locator haben ein Ablaufdatum. Wenn Sie Medienobjekte über das Portal veröffentlichen, werden Locator mit einem Ablaufdatum von 100 Jahren erstellt. 

>[AZURE.NOTE] Wenn Sie das Portal vor März 2015 zum Erstellen von Locators verwendet haben, wurden diese mit einem Ablaufdatum von zwei Jahren erstellt.  

Verwenden Sie zum Aktualisieren des Ablaufdatums für einen Locator die [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )- oder [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-APIs. Wenn Sie das Ablaufdatum eines SAS-Locators aktualisieren, ändert sich auch die URL. 
 
Locator sind nicht für die Verwaltung der Zugriffssteuerung pro Benutzer konzipiert. Um einzelnen Benutzern verschiedene Zugriffsrechte zu erteilen, verwenden Sie Lösungen zur Verwaltung digitaler Rechte (Digital Rights Management, DRM). Weitere Informationen finden Sie unter [Sichern von Medien](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Beachten Sie, dass beim Erstellen eines Locators möglicherweise eine Verzögerung von 30 Sekunden auftritt, die durch die erforderlichen Speicher- und Weitergabeprozesse in Azure Storage verursacht wird.


###Adaptives Streaming 

Bei adaptiven Bitratentechnologien können Videoplayeranwendungen die Netzwerkbedingungen ermitteln und eine Auswahl aus mehreren Bitraten treffen. Wenn die Netzwerkleistung absinkt, kann der Client eine niedrigere Bitrate auswählen, und der Player kann die Wiedergabe des Videos mit einer geringeren Videoqualität fortsetzen. Verbessert sich die Netzwerkleistung, kann der Client auf eine höhere Bitrate umschalten und eine verbesserte Videoqualität anbieten. Azure Media Services unterstützt die folgenden Technologien mit adaptiven Bitraten: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS.

Um Benutzern Streaming-URLs bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt mit den Inhalten, die Sie streamen möchten. Um diese Inhalte streamen zu können, müssen Sie diesen Pfad jedoch ändern. Zum Erstellen einer vollständigen URL für die Streaming-Manifestdatei müssen Sie den Pfadwert des Locators mit dem Dateinamen des Manifests (dateiname.ism) verketten. Fügen Sie dem Locatorpfad anschließend "/Manifest" und ein geeignetes Format (falls erforderlich) hinzu. 

Sie können auch den Inhalt über eine SSL-Verbindung streamen. Zu diesem Zweck stellen Sie sicher, dass die Streaming-URLs mit HTTPS beginnen. 

Beachten Sie, dass Sie nur über SSL streamen können, wenn der Streaming-Endpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streaming-Endpunkten basieren, die nach dem 10. September erstellt wurden, enthält die URL "streaming.mediaservices.windows.net" (neues Format). Streaming-URLs, die "origin.mediaservices.windows.net" (das alte Format) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, erstellen Sie einen neuen Streamingendpunkt. Verwenden Sie die URLs, die basierend auf dem neuen Streaming-Endpunkt erstellt wurden, um Ihre Inhalte über SSL zu streamen. 


####Streaming-URL-Formate:

**Smooth Streaming-Format**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Beispiel:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


**MPEG DASH-Format**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

Beispiel

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

**Apple HTTP Live Streaming (HLS) V4-Format**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Apple HTTP Live Streaming (HLS) V3-Format**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**HDS (nur für Lizenznehmer von Adobe PrimeTime/Access)**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


###Dynamische Paketerstellung

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen. 

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren Ihrer Zwischendatei (Quelldatei) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate
- Abrufen von mindestens einer On-Demand-Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für bedarfsgesteuertes Streaming](media-services-manage-origins.md#scale_streaming_endpoints/).

Mit der dynamischen Paketerstellung müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client. 

Beachten Sie, dass die reservierten Einheiten für On-Demand-Streaming Ihnen zusätzlich zur dynamischen Paketerstellung auch eine dedizierte Ausgangskapazität bereitstellen, die in Schritten von 200 Mbit/s erworben werden kann. Standardmäßig wird das bedarfsgesteuerte Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Durchsatz des bedarfsgesteuerten Streamings zu erhöhen, sollten Sie die reservierten Einheiten für bedarfsgesteuertes Streaming kaufen.

###Progressiver Download 

Der progressive Download ermöglicht die Wiedergabe von Medien, bevor die gesamte Datei heruntergeladen wurde. Sie können Dateien mit der Endung ".ism*" (ISMV, ISMA, ISMT, ISMC) nicht progressiv herunterladen. 

Verwenden Sie den "OnDemandOrigin"-Typ des Locator zum progressiven Herunterladen von Inhalten. Das folgende Beispiel zeigt die URL, die auf dem "locator.r"-Typ "OnDemandOrigin" basiert:

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

Ein **Streamingendpunkt** stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Netzwerk für die Inhaltsübermittlung (Content Delivery Network, CDN) bereitstellen kann. Der ausgehende Stream des Streamingendpunkt-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. Darüber hinaus können Sie die Kapazität des Streamingendpunkt-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie die reservierten Einheiten für das Streaming anpassen. Anwendungen in einer Produktionsumgebung sollten Sie mindestens eine reservierte Einheit zuweisen. Weitere Informationen finden Sie unter [Skalieren eines Mediendiensts](media-services-manage-origins.md#scale_streaming_endpoints).

##<a id="tasks"></a>Aufgaben für das Bereitstellen von Medienobjekten

###Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

###Veröffentlichen von Medienobjekten

Veröffentlichen Sie Medienobjekte (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mit **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


##Verwandte Themen

[Aktualisieren von Media Services nach dem Austausch der Speicherschlüssel](media-services-roll-storage-access-keys.md)


<!--HONumber=52-->