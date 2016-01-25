<properties 
	pageTitle="Azure Media Services – Übersicht und häufige Szenarios" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/12/2016"
	ms.author="juliako"/>

#Azure Media Services – Übersicht und häufige Szenarios

Microsoft Azure Media Services ist eine erweiterbare, cloudbasierte Plattform, die Entwicklern das Erstellen von skalierbaren Medienverwaltungslösungen und Bereitstellungsanwendungen ermöglicht. Media Services basiert auf REST-APIs, mit denen Sie auf sichere Weise Video- oder Audioinhalte hochladen, speichern, codieren und paketieren können – sowohl für eine bedarfsgesteuerte als auch für eine Livestreaming-basierte Bereitstellung auf verschiedenen Clients (z. B. TV, PC und mobile Geräte).

Sie können mithilfe von Media Services vollständige End-to-End-Workflows erstellen. Es ist auch möglich, Drittanbieterkomponenten für einige Elemente Ihres Workflows zu nutzen. Beispielsweise können Sie die Codierung mit einem Encoder eines Drittanbieters durchführen. Anschließend sorgen Sie mit Media Services für Upload, Schutz, Paketierung und Bereitstellung.

Sie können Ihre Inhalte live streamen oder sie bei Bedarf bereitstellen. In diesem Thema werden allgemeine Szenarien für die Übermittlung von Inhalten [live](media-services-overview.md#live_scenarios) oder [bei Bedarf](media-services-overview.md#vod_scenarios) gezeigt. Das Thema enthält außerdem Links zu anderen relevanten Themen.

## SDKs und Tools 

Zum Entwickeln von Media Services-Lösungen können Sie folgende Komponenten verwenden:

- [Media Services-REST-API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Eines der verfügbaren Client-SDKs: [Azure Media Services-SDK für .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure-SDK für Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services für Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js), [Azure-PHP-SDK](https://github.com/Azure/azure-sdk-for-php)
- Vorhandene Tools: [klassisches Azure-Portal](http://manage.windowsazure.com/) oder [Azure Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming (in englischer Sprache)](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Media Services - on Demand Streaming (in englischer Sprache)](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Poster


[Hier](http://azure.microsoft.com/documentation/infographics/media-services/) können Sie das Azure Media Services-Poster ansehen, das die AMS-Workflows zeigt, von der Medienerstellung bis zur Nutzung.

##Voraussetzungen

Um mit Azure Media Services loszulegen, sollten Sie Folgendes haben:
 
3. Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](azure.microsoft.com).
2. Ein Azure Media Services-Konto. Verwenden Sie das klassische Azure-Portal, .NET oder die REST-API zum Erstellen eines Azure Media Services-Kontos. Weitere Informationen finden Sie unter [Erstellen von Konten](media-services-create-account.md).
3. (Optional) Eine eingerichtete Entwicklungsumgebung. Wählen Sie .NET oder REST-API für Ihre Entwicklungsumgebung. Weitere Informationen finden Sie unter [Einrichten der Umgebung](media-services-dotnet-how-to-use.md). 

	Erfahren Sie darüber hinaus, wie Sie programmgesteuert eine [Verbindung](media-services-dotnet-connect_programmatically.md) herstellen können.
4. (Empfohlen) Eine oder mehrere zugewiesene Skalierungseinheiten. Es wird empfohlen, für Anwendungen in der Produktionsumgebung eine oder mehrere Skalierungseinheiten zu reservieren. Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten](media-services-manage-origins.md).

##Konzepte

Weitere Informationen finden Sie unter [Konzepte](media-services-concepts.md).


##<a id="vod_scenarios"></a>Bereitstellen von Medien-on-Demand mit Azure Media Services: häufige Szenarios und Aufgaben.

Dieser Abschnitt erläutert allgemeine Szenarien und enthält Links zu relevanten Themen. Das folgende Diagramm zeigt die Hauptbestandteile der Media Services-Plattform, die am Bereitstellen von On-Demand-Inhalten beteiligt sind.

![VoD-Workflow][vod-overview]


###Schützen von Inhalte im Speicher und Übermitteln von Streamingmedien ohne Verschlüsselung

1. Laden Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt hoch.
	
	Es wird empfohlen, eine Speicherverschlüsselung auf Medienobjekte anzuwenden, um Ihre Inhalte beim Hochladen und während der Speicherung zu schützen.
 
1. Codieren Sie das Medienobjekt in einen Satz von MP4-Dateien mit adaptiver Bitrate.

	Es wird empfohlen, eine Speicherverschlüsselung das Ausgabemedienobjekt anzuwenden, um Ihre Inhalte während der Speicherung zu schützen.
	
1. Konfigurieren Sie eine Übermittlungsrichtlinie für Medienobjekte (wird zur dynamischen Paketerstellung verwendet).
	
	Wenn Ihr Medienobjekt speicherverschlüsselt ist, **müssen** Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren.

1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand-Locators.

	Vergewissern Sie sich, dass auf dem Streamingendpunkt, von dem aus Inhalte gestreamt werden sollen, mindestens eine reservierte Einheit für das Streaming vorhanden ist.

1. Streamen Sie die veröffentlichten Inhalte.

###Schützen von Inhalten im Speicher, Übermitteln dynamisch verschlüsselter Streamingmedien  

Damit Sie die dynamische Verschlüsselung verwenden können, müssen Sie zunächst mindestens eine reservierte Einheit für das Streaming auf dem Streamingendpunkt abrufen, auf dem Sie verschlüsselte Inhalte streamen möchten.

1. Laden Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt hoch. Wenden Sie die Speicherverschlüsselung auf das Medienobjekt an.
1. Codieren Sie das Medienobjekt in einen Satz von MP4-Dateien mit adaptiver Bitrate. Wenden Sie die Speicherverschlüsselung auf das Ausgabemedienobjekt an.
1. Erstellen Sie einen Inhaltsverschlüsselungsschlüssel für das Medienobjekt, das während der Wiedergabe dynamisch verschlüsselt werden soll.
2. Konfigurieren Sie Autorisierungsrichtlinien für Inhaltsschlüssel.
1. Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte (wird zur dynamischen Paketerstellung und zur dynamischen Verschlüsselung verwendet).
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand-Locators.
1. Streamen Sie die veröffentlichten Inhalte. 

###Indizieren von Inhalten

1. Laden Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt hoch.
1. Indizieren Sie die Inhalte.

	Der Indizierungsauftrag erstellt Dateien, die bei der Videowiedergabe als Untertitel (Closed Captions, CC) verwendet werden können. Er generiert außerdem die Dateien, mit denen Sie im Video suchen und an den genauen Zeitpunkt im Video springen können.

1. Nutzen Sie die indizierten Inhalte.


###Bereitstellen eines progressiven Downloads 

1. Laden Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt hoch.
1. Codieren Sie das Medienobjekt in eine einzelne MP4-Datei.
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand- oder SAS-Locators.

	Vergewissern Sie sich bei Verwendung eines OnDemand-Locators, dass auf dem Streamingendpunkt, von dem aus Inhalte gestreamt werden sollen, mindestens eine reservierte Einheit für das progressive Herunterladen von Inhalten vorhanden ist.

	Wenn Sie einen SAS-Locator verwenden, wird der Inhalt aus dem Azure-Blob-Speicher heruntergeladen. In diesem Fall benötigen Sie keine reservierten Einheiten für das Streaming.
  
1. Führen Sie einen progressiven Download der Inhalte durch.

###Siehe auch

- [Hochladen von Inhalten](media-services-manage-content.md#upload)
- [Abrufen eines Medienprozesses](media-services-get-media-processor.md)
- [Codieren von Inhalten](media-services-manage-content.md#encode)
- [Überwachen von Aufträgen](media-services-portal-check-job-progress.md)
- [Indizieren von Inhalten](media-services-manage-content.md#index)
- [Schützen von Inhalten](media-services-manage-content.md#encrypt)
- [Geschütztes Veröffentlichen](media-services-manage-content.md#publish)
- [Skalieren der Codierung](media-services-portal-encoding-units.md)

##<a id="live_scenarios"></a>Bereitstellen von Livestreamingereignissen mit Azure Media Services

Beim Arbeiten mit Livestreaming werden normalerweise die folgenden Komponenten verwendet:

- Eine Kamera, mit der ein Ereignis übertragen wird.
- Ein Live-Video-Encoder, der Signale von der Kamera in Datenströme konvertiert, die an einen Livestreaming-Dienst gesendet werden. 
  
	Optional mehrere Live-Encoder. Für bestimmte kritische Liveereignisse, die eine sehr hohe Verfügbarkeit und Quality of Experience erfordern, wird der Einsatz redundanter Aktiv-Aktiv-Encoder empfohlen, um ein nahtloses Failover ohne Datenverlust zu erreichen.
- Ein Livestreaming-Dienst, der Ihnen Folgendes ermöglicht: 
	- Erfassen von Liveinhalten über verschiedene Livestreaming-Protokolle (z. B. RTMP oder Smooth Streaming) 
	- Codieren des Datenstroms in einen Datenstrom mit adaptiver Bitrate
	- Vorschau des Livestreams
	- Speichern des erfassten Inhalts für ein späteres Streaming (Video-on-Demand)
	- Übermitteln der Inhalte über häufig verwendete Streaming-Protokolle (z. B. MPEG DASH, Smooth, HLS, HDS) direkt an die Kunden oder an ein Content Delivery Network (CDN) zur weiteren Verteilung 
	
		
Mit **Microsoft Azure Media Services** (AMS) können Sie Livestreaminginhalte erfassen, codieren, in der Vorschau anzeigen, speichern und bereitstellen.

Bei der Übermittlung Ihrer Inhalte für Kunden besteht Ihr Ziel darin, qualitativ hochwertige Videos für unterschiedliche Geräte unter verschiedenen Netzwerkbedingungen bereitzustellen. Damit Qualität und Netzwerkbedingungen sichergestellt werden, codieren Sie den Datenstrom mit Live-Encodern in einen Videodatenstrom mit mehreren Bitraten (adaptive Bitrate). Verwenden Sie die [dynamische Paketerstellung](media-services-dynamic-packaging-overview.md) von Media Services, um den Datenstrom dynamisch erneut in verschiedene Protokolle zu packen. Media Services unterstützt die Übermittlung der folgenden Streamingtechnologien mit adaptiver Bitrate: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

In Azure Media Services verarbeiten die **Kanäle**, **Programme** und **Streamingendpunkte** alle Livestreamingfunktionen, einschließlich Erfassung, Formatierung, DVR, Sicherheit, Skalierbarkeit und Redundanz.

Ein **Kanal** stellt eine Pipeline zum Verarbeiten von Livestreaming-Inhalten dar. Derzeit kann ein Kanal Live-Eingabedatenströme auf folgende Weise empfangen:


- Ein lokaler Liveencoder sendet einen Single-Bitrate-Datenstrom an den Kanal, der zum Ausführen von Livecodierung mit Media Services in einem der folgenden Formate aktiviert wurde: RTP (MPEG-TS), RTMP oder Smooth Streaming (fragmentiertes MP4). Vom Kanal wird dann eine Livecodierung des Single-Bitrate-Eingabedatenstroms in einen Multi-Bitrate-Videodatenstrom (adaptiv) ausgeführt. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.

	Die Codierung eines Livedatenstroms mit Media Services befindet sich in der **Vorschau**.
- Von einem lokalen Live-Encoder wird Multi-Bitrate-**RTMP** oder -**Smooth Streaming** (fragmentiertes MP4) an den Kanal gesendet. Sie können die folgenden Liveencoder verwenden, von denen Multi-Bitrate-Smooth Streaming ausgegeben werden kann: Elemental, Envivio, Cisco. Von den folgenden Liveencodern wird RTMP ausgegeben: Adobe Flash Live, Telestream Wirecast und Tricaster-Transcoder. Die aufgenommenen Datenströme werden ohne weitere Verarbeitung durch die **Kanäle** geleitet. Ihr Liveencoder kann auch einen Single-Bitrate-Datenstrom an einen Kanal senden, der nicht für die Live-Codierung aktiviert ist. Dies wird jedoch nicht empfohlen. Auf Anforderung wird der Datenstrom den Kunden von Media Services bereitgestellt.


###Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden


Das folgende Diagramm zeigt die Hauptkomponenten der AMS-Plattform, die in Livestreaming-Workflows beteiligt sind, wenn ein Kanal zum Ausführen der Livecodierung mit Media Services aktiviert ist.

![Liveworkflow][live-overview1]

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).


###Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen


Das folgende Diagramm zeigt die Hauptkomponenten der AMS-Plattform, die am Livestreaming-Workflow beteiligt sind.

![Liveworkflow][live-overview2]

Weitere Informationen finden Sie unter [Arbeiten mit Kanälen, die Livedatenströme mit mehreren Bitraten von lokalen Encodern empfangen](media-services-manage-channels-overview.md).

##Nutzung von Inhalten

Azure Media Services stellt die Tools zur Verfügung, die zum Erstellen leistungsstarker, dynamischer Clientplayeranwendungen für die gängigsten Plattformen erforderlich sind, darunter: iOS-Geräte, Android-Geräte, Windows, Windows Phone, Xbox und Set-Top-Boxen. Das Thema enthält Links zu SDKs und Player-Frameworks, mit denen Sie Ihre eigenen Clientanwendungen entwickeln können, die Streamingmedien aus Media Services verarbeiten.

[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)

##Aktivieren von Azure CDN

Von Media Services wird die Integration mit Azure CDN unterstützt. Informationen zum Aktivieren von Azure CDN finden Sie unter [Verwalten von Streamingendpunkten in Media Services-Konten](media-services-manage-origins.md#enable_cdn).

##Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl **reservierter Einheiten für das Streaming** und die Anzahl **reservierter Einheiten für die Codierung** angeben, die für Ihr Konto bereitgestellt werden soll.

Außerdem können Sie Ihr Media Services-Konto skalieren, indem Sie Speicherkonten hinzufügen. Jedes Speicherkonto ist auf 500 TB beschränkt. Um den Speicher über die Standardbeschränkungen hinaus zu erweitern, können Sie mehrere Speicherkonten mit einem einzelnen Media Services-Konto verknüpfen.

[Dieses](media-services-how-to-scale.md) Thema enthält Links zu relevanten Themen.

##Support

Der [Azure-Support](http://azure.microsoft.com/support/options/) bietet Supportoptionen für Azure, Media Services eingeschlossen.

##Muster & Leitfäden

[Patterns and practices guidance](https://wamsg.codeplex.com/) [Online documentation](https://msdn.microsoft.com/library/dn735912.aspx) [Downloadable eBook](https://www.microsoft.com/download/details.aspx?id=42629) (alle in englischer Sprache)


##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Vereinbarung zum Servicelevel (SLA)

- Media Services gewährleistet eine Verfügbarkeit von 99,9 % für die REST-API-Transaktionen zur Media Services-Codierung.
- Das Streaming sorgt für eine erfolgreiche Verarbeitung von Dienstanforderungen mit 99,9 % Verfügbarkeitsgarantie für vorhandene Medieninhalte, wenn mindestens eine Streamingeinheit erworben wird.
- Für Livekanäle wird garantiert, dass ausgeführte Kanäle mindestens 99,9 % der Zeit über externe Konnektivität verfügen.
- Für den Schutz von Inhalten garantieren wir, dass Schlüsselanforderungen mindestens 99,9 % der Zeit erfolgreich bearbeitet werden.
- Für Indexer werden Indexer-Aufgabenanforderungen, die mit einer reservierten Einheit für die Codierung verarbeitet werden, 99,9 % der Zeit erfolgreich bearbeitet.

	Weitere Informationen finden Sie im [Microsoft Azure-SLA](http://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0114_2016-->