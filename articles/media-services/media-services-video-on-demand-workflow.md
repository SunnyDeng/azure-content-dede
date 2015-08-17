<properties 
	pageTitle="Bereitstellen von Medien-on-Demand mit Azure Media Services" 
	description="In diesem Thema werden häufige Szenarios bei der Bereitstellung von Medien-on-Demand mit Azure Media Services beschrieben." 
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
	ms.date="06/08/2015" 
	ms.author="juliako"/>


#Bereitstellen von Medien-on-Demand mit Azure Media Services

##Übersicht

In diesem Thema werden die Schritte eines typischen Azure Media Services-VoD-Workflows (Video on Demand) beschrieben. Jeder Schritt enthält Links zu relevanten Themen. Für Aufgaben, die mithilfe verschiedener Technologien erledigt werden können, stehen Schaltflächen zur Technologie Ihrer Wahl (z. B. .NET oder REST) zur Verfügung.

Beachten Sie, dass Sie Media Services in Ihre vorhandenen Tools und Prozesse integrieren können. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über das Azure CDN oder ein Drittanbieter-CDN zu übermitteln.

Das folgende Diagramm zeigt die Hauptbestandteile der Media Services-Plattform, die am Video-on-Demand-Workflow beteiligt sind. ![VoD-Workflow][vod-overview]

##<a id="vod_scenarios"></a>Allgemeine Szenarien: Bereitstellen von Medien-On-Demand. 

###Schützen von Inhalte im Speicher und Übermitteln von Streamingmedien ohne Verschlüsselung

1. Laden Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt hoch.
	
	Es wird empfohlen, eine Speicherverschlüsselung auf Medienobjekte anzuwenden, um Ihre Inhalte beim Hochladen und während der Speicherung zu schützen. 
1. Führen Sie eine Codierung in einen MP4-Satz mit adaptiver Bitrate durch. 

	Es wird empfohlen, eine Speicherverschlüsselung das Ausgabemedienobjekt anzuwenden, um Ihre Inhalte während der Speicherung zu schützen.
	
1. Konfigurieren Sie eine Übermittlungsrichtlinie für Medienobjekte (wird zur dynamischen Paketerstellung verwendet).
	
	Wenn Ihr Medienobjekt speicherverschlüsselt ist, **müssen** Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren.

1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand-Locators.

	Vergewissern Sie sich, dass auf dem Streamingendpunkt, von dem aus Inhalte gestreamt werden sollen, mindestens eine reservierte Einheit für das Streaming vorhanden ist.

1. Streamen Sie die veröffentlichten Inhalte.

###Schützen von Inhalten im Speicher, Übermitteln dynamisch verschlüsselter Streamingmedien  

Damit Sie die dynamische Verschlüsselung verwenden können, müssen Sie zunächst mindestens eine reservierte Einheit für das Streaming auf dem Streamingendpunkt abrufen, auf dem Sie verschlüsselte Inhalte streamen möchten.

1. Laden Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt hoch. Wenden Sie die Speicherverschlüsselung auf das Medienobjekt an.
1. Führen Sie eine Codierung in einen MP4-Satz mit adaptiver Bitrate durch. Wenden Sie die Speicherverschlüsselung auf das Ausgabemedienobjekt an.
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
1. Führen Sie eine Codierung in einen MP4-Satz mit adaptiver Bitrate oder eine einzelne MP4-Datei durch.
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand- oder SAS-Locators.

	Vergewissern Sie sich bei Verwendung eines OnDemand-Locators, dass auf dem Streamingendpunkt, von dem aus Inhalte gestreamt werden sollen, mindestens eine reservierte Einheit für das progressive Herunterladen von Inhalten vorhanden ist.

	Wenn Sie einen SAS-Locator verwenden, wird der Inhalt aus dem Azure-Blob-Speicher heruntergeladen. In diesem Fall benötigen Sie keine reservierten Einheiten für das Streaming.
  
1. Führen Sie einen progressiven Download der Inhalte durch.

Dieser Artikel enthält Links zu Themen, in denen das Einrichten der Entwicklungsumgebung und das Ausführen der oben genannten Aufgaben beschrieben wird.


##Konzepte

Konzepte im Zusammenhang mit der Übermittlung von Inhalten On-Demand finden Sie unter [Media Services – Konzepte](media-services-concepts.md).

##Allgemeine Aufgaben: Bereitstellen von Medien-On-Demand.

###Erstellen eines Media Services-Kontos

Verwenden Sie das **Azure-Verwaltungsportal** zum [Erstellen eines Azure Media Services-Kontos](media-services-create-account.md).

###Einrichten der Entwicklungsumgebung  

Wählen Sie **.NET** oder **REST API** für Ihre Entwicklungsumgebung.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Programmgesteuerte Verbindung  

Wählen Sie **.NET** oder **REST API**, um eine programmgesteuerte Verbindung mit Azure Media Services herzustellen.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


###Konfigurieren von Streamingendpunkten

Eine Übersicht über Streamingendpunkte sowie Informationen zu deren Verwaltung finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md).

###Hochladen von Medien 

Laden Sie Ihre Dateien mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET** oder **REST-API** hoch.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

###Erstellen von Aufträgen/Aufgaben

Ein Auftrag ist eine Entität, die Metadaten zu einer Reihe von Aufgaben enthält (z. B. Codierung oder Indizierung). Jede Aufgabe führt einen unteilbaren Vorgang für das (oder die) Eingangsmedienobjekt(e) durch. Ein Beispiel zum Erstellen von Codierungsaufträgen finden Sie hier:

Eine Übersicht finden Sie unter [Arbeiten mit Azure Media Services-Aufträgen](media-services-jobs.md).

Rufen Sie mit **.NET** oder der **REST API** einen für Ihre Aufgabe geeigneten Medienprozessor ab.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

In den folgenden Beispielen werden Codierungsaufträge mit dem **Azure-Verwaltungsportal**, **.NET** oder der **REST-API** erstellt.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Indizieren

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

####Codieren 

**Übersicht**:

- [Dynamische Paketerstellung – Übersicht](media-services-dynamic-packaging-overview.md)
- [Codieren von On-Demand-Inhalten mit Azure Media Services](media-services-encode-asset.md)

Führen Sie die Codierung mit **Azure Media Encoder** mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET**, oder **REST-API** aus.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

Erweiterte Codierung mit dem **Media Encoder Premium Workflow** mit **.NET**.

[AZURE.INCLUDE [media-services-selector-advanced-encoding](../../includes/media-services-selector-advanced-encoding.md)]

####Überwachen des Auftragsfortschritts

Überwachen Sie den Auftragsfortschritt mit dem **Azure-Verwaltungsportal**, **.NET** oder der **REST-API**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

###Schützen von Inhalten 

**Übersicht**:

[Inhaltsschutz – Übersicht](media-services-content-protection-overview.md)

Wenn Sie ein Medienobjekt mit AES (Advanced Encryption Standard, mit 128-Bit-Verschlüsselungsschlüsseln) oder PlayReady-DRM verschlüsseln möchten, müssen Sie einen Inhaltsschlüssel erstellen.

Verwenden Sie **.NET** oder **REST API**, um Schlüssel zu erstellen.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Nach dem Erstellen des Inhaltsschlüssels können Sie die Autorisierungsrichtlinie für Schlüssel mithilfe von **.NET** oder **REST API** konfigurieren.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


####Integrieren in Partneranwendungen

[Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von castLabs](media-services-castlabs-integration.md)

###Veröffentlichen und Bereitstellen von Medienobjekten

Dynamische Paketerstellung – Übersicht

> [AZURE.SELECTOR]
- [Overview](media-services-dynamic-packaging-overview.md)


Bereitstellen von Inhalten – Übersicht

> [AZURE.SELECTOR]
- [Overview](media-services-deliver-content-overview.md)

Veröffentlichen Sie Medienobjekte (durch Locator-Erstellung) mit dem **Azure-Verwaltungsportal**, **.NET** oder der **REST-API**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]

###Aktivieren von Azure CDN

Von Media Services wird die Integration mit Azure CDN unterstützt. Informationen zum Aktivieren von Azure CDN finden Sie unter [Verwalten von Streamingendpunkten in Media Services-Konten](media-services-manage-origins.md#enable_cdn).

###Skalieren eines Media Services-Kontos

Sie können **Media Services** skalieren, indem Sie die Anzahl **reservierter Einheiten für das Streaming** und die Anzahl **reservierter Einheiten für die Codierung** angeben, die für Ihr Konto bereitgestellt werden soll.

Außerdem können Sie Ihr Media Services-Konto skalieren, indem Sie Speicherkonten hinzufügen. Jedes Speicherkonto ist auf 500 TB beschränkt. Um den Speicher über die Standardbeschränkungen hinaus zu erweitern, können Sie mehrere Speicherkonten mit einem einzelnen Media Services-Konto verknüpfen.

[Dieses](media-services-how-to-scale.md) Thema enthält Links zu relevanten Themen.

###Wiedergabe Ihrer Inhalte mit vorhandenen Playern

Weitere Informationen finden Sie unter [Wiedergabe Ihrer Inhalte mit vorhandenen Playern](media-services-playback-content-with-existing-players.md).


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=August15_HO6-->