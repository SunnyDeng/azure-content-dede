<properties 
	pageTitle="Erstellen eines Medienprozessors - Azure" 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Gewusst wie: Abrufen einer Media Processor-Instanz

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md). 


##Übersicht

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Normalerweise erstellen Sie einen Medienprozessor, wenn Sie eine Aufgabe zur Codierung, Verschlüsselung oder Formatumwandlung von Medieninhalten erstellen.

Die folgende Tabelle enthält Name und Beschreibung der verfügbaren Medienprozessoren.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Medienprozessorname</th>
       <th>Beschreibung</th>
	<th>Weitere Informationen</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>Zur Ausführung von Codieraufgaben mit dem Azure Media Encoder.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Aufgabenvoreinstellungen für den Azure Media Encoder</a></td>
    </tr>
    <tr>
       <td>Media Encoder Premium Workflow</td>
       <td>Zur Ausführung von Codieraufgaben mit dem Media Encoder Premium Workflow.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Codierung mit dem Media Encoder Premium Workflow</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Macht Mediendateien und Inhalte durchsuchbar und generiert Untertitelspuren und Schlüsselwörter.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Indizieren von Mediendateien mit Azure Media Indexer</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Konvertiert Medienobjekte von .mp4 in das Smooth Streaming-Format. Konvertiert außerdem Medienobjekte vom Smooth Streaming-Format in das Apple HTTP Live Streaming (HLS)-Format.</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Aufgabenvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Verschlüsselt Medienobjekte mit dem PlayReady-Schutz.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Aufgabenvoreinstellungen für den Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Entschlüsselt Medienobjekte, die mit der Speicherverschlüsselung verschlüsselt wurden.</td>
		<td>-</td>
    </tr>  </tbody>
</table>

<br />

##Abrufen von MediaProcessor

>[AZURE.NOTE] Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Setup für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Wie in [Herstellen einer Verbindung mit Media Services mit der REST-API](media-services-rest-connect_programmatically.md) beschrieben müssen Sie nachfolgende Aufrufe an den neuen URI senden. 



Der folgende REST-Aufruf zeigt, wie Sie eine Medienprozessorinstanz nach Namen (in diesem Fall **Azure Media Encoder**) abrufen. 

	
Anforderung:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
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


##Nächste Schritte
Sie sind nun in der Lage, eine Medienprozessorinstanz abzurufen, und können mit dem Thema [Codieren von Medienobjekten][] fortfahren. Dort lernen Sie, wie Sie Medienobjekte mit dem Azure Media Encoder codieren können.

[Codieren von Medienobjekten]: media-services-rest-encode-asset.md
[Aufgabenvoreinstellungen für den Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Vorgehensweise: Programmgesteuertes Verbinden mit Mediendiensten]: ../media-services-rest-connect_programmatically/


<!--HONumber=52--> 