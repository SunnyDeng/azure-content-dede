<properties 
	pageTitle="Erstellen eines Medienprozessors | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016"  
	ms.author="juliako"/>


#Gewusst wie: Abrufen einer Media Processor-Instanz


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##Übersicht

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

Medienprozessorname|Beschreibung|Weitere Informationen
---|---|---
Media Encoder Standard|Umfasst Standardfunktionen für bedarfsgesteuerte Codierung. |[Azure On-Demand Media Encoder – Überblick und Vergleich](media-services-encode-asset.md)
Media Encoder Premium Workflow|Zur Ausführung von Codieraufgaben mit dem Media Encoder Premium Workflow.|[Azure On-Demand Media Encoder – Überblick und Vergleich](media-services-encode-asset.md)
Azure Media Indexer| Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (Vorschau)|Ermöglicht es Ihnen, die "Unregelmäßigkeiten" in Ihrem Video mithilfe von Videostabilisierung auszugleichen. Darüber hinaus können Sie Ihre Inhalte beschleunigen, um einen verwendbaren Clip zu erhalten.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Veraltet
Storage Decryption| Veraltet|
Azure Media Packager|Veraltet|
Azure Media Encryptor|Veraltet|

##Abrufen von MediaProcessor

>[AZURE.NOTE] Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Entsprechend der Beschreibung unter [Herstellen einer Verbindung mit einem Media Services-Konto über die Media Services-REST-API](media-services-rest-connect_programmatically.md) müssen Sie nachfolgende Aufrufe an den neuen URI senden.


Der folgende REST-Aufruf zeigt, wie Sie eine Medienprozessorinstanz nach Namen (in diesem Fall **Media Encoder Standard**) abrufen.



	
Anforderung:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer <token>
	x-ms-version: 2.11
	Host: media.windows.net
	
Antwort:
		
	. . .
	
	{  
	   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "Description":"Media Encoder Standard",
	         "Name":"Media Encoder Standard",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"1.1"
	      }
	   ]
	}


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Nächste Schritte

Sie sind nun in der Lage, eine Medienprozessorinstanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Assets](media-services-rest-get-started.md) fortfahren. Dort lernen Sie, wie Sie Assets mit dem Media Encoder Standard codieren können.

<!---HONumber=AcomDC_0302_2016-->