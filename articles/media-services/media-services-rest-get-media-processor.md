<properties 
	pageTitle="Erstellen eines Medienprozessors | Microsoft Azure" 
	description="Erfahren Sie, wie Sie eine Medienprozessorkomponente erstellen können, um Medieninhalte für Azure Media Services zu codieren, zu ver- oder entschlüsseln, und um Formate zu konvertieren." 
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


#Gewusst wie: Abrufen einer Media Processor-Instanz


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##Übersicht

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

Medienprozessorname|Beschreibung|Weitere Informationen
---|---|---
Azure Media Encoder|Zur Ausführung von Codieraufgaben mit dem Azure Media Encoder.|[Azure Media Encoder](media-services-encode-asset.md#azure_media_encoder)
Media Encoder Standard|Zur Ausführung von Codieraufgaben mit dem Media Encoder Standard.|[Azure Media Encoder](media-services-encode-asset.md#media_encoder_standard)
Media Encoder Premium Workflow|Zur Ausführung von Codieraufgaben mit dem Media Encoder Premium Workflow.|[Media Encoder Premium Workflow](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.|[Indizieren von Mediendateien mit Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (Vorschau)|Ermöglicht es Ihnen, die "Unregelmäßigkeiten" in Ihrem Video mithilfe von Videostabilisierung auszugleichen. Darüber hinaus können Sie Ihre Inhalte beschleunigen, um einen verwendbaren Clip zu erhalten.|		[Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden.|N/V
Azure Media Packager|Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.|[Systemvoreinstellungen für den Azure Media Packager](http://msdn.microsoft.com/library/hh973635.aspx)
Azure Media Encryptor|Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.|[Aufgabenvoreinstellungen für den Azure Media Packager](http://msdn.microsoft.com/library/hh973610.aspx)

##Abrufen von MediaProcessor

>[AZURE.NOTE]Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Entsprechend der Beschreibung unter [Herstellen einer Verbindung mit einem Media Services-Konto über die Media Services-REST-API](media-services-rest-connect_programmatically.md) müssen Sie nachfolgende Aufrufe an den neuen URI senden.



Der folgende REST-Aufruf zeigt, wie Sie eine Medienprozessorinstanz nach Namen (in diesem Fall **Azure Media Encoder**) abrufen.

	
Anforderung:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
Antwort:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}




##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Nächste Schritte
Sie sind nun in der Lage, eine Medienprozessor-Instanz zu erstellen, und können mit dem Thema [Gewusst wie: Codieren von Medienobjekten][] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

[Gewusst wie: Codieren von Medienobjekten]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/

<!---HONumber=AcomDC_1210_2015-->