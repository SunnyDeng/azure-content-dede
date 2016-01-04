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
	ms.date="12/05/2015"   
	ms.author="juliako"/>


#Bereitstellen von Medien-on-Demand mit Azure Media Services

##Übersicht

In diesem Thema werden die Schritte eines typischen Azure Media Services-VoD-Workflows (Video on Demand) beschrieben. Jeder Schritt enthält Links zu relevanten Themen. Für Aufgaben, die mithilfe verschiedener Technologien erledigt werden können, stehen Schaltflächen zur Technologie Ihrer Wahl (z. B. .NET oder REST) zur Verfügung.

Beachten Sie, dass Sie Media Services in Ihre vorhandenen Tools und Prozesse integrieren können. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über das Azure CDN oder ein Drittanbieter-CDN zu übermitteln.

Das folgende Diagramm zeigt die Hauptbestandteile der Media Services-Plattform, die am Video-on-Demand-Workflow beteiligt sind. ![VoD-Workflow][vod-overview]

##<a id="vod_scenarios"></a>Allgemeine Szenarien: Bereitstellen von Medien-On-Demand

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

##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
 

<!---HONumber=AcomDC_1210_2015-->