<properties 
	pageTitle="Hochladen von Dateien in ein Media Services-Konto mit der REST-API" 
	description="Erfahren Sie, wie Sie Medieninhalte in Media Services nutzen können, indem Sie Medienobjekte erstellen und hochladen." 
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
	ms.date="10/18/2015"
	ms.author="juliako"/>


#Hochladen von Dateien in ein Media Services-Konto mit der REST-API

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]
 

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch. Die [Medienobjekt](https://msdn.microsoft.com/library/azure/hh974277.aspx)-Entität kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die Metadaten zu diesen Dateien) enthalten. Nachdem die Dateien in das Medienobjekt hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert.


>[AZURE.NOTE]Media Services verwendet beim Erstellen von URLs für den Streaminginhalt den Wert der IAssetFile.Name-Eigenschaft (z. B. http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Aus diesem Grund ist die Prozentcodierung nicht zulässig. Der Wert der **Name**-Eigenschaft darf keines der folgenden [für die Prozentcodierung reservierten Zeichen enthalten](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Darüber hinaus wird für die Dateinamenerweiterung nur ein Punkt (.) unterstützt.

Der grundlegende Workflow zum Hochladen von Medienobjekten ist in folgende Abschnitte unterteilt:

- Erstellen eines Medienobjekts
- Verschlüsseln eines Medienobjekts (optional)
- Hochladen einer Datei in den Blobspeicher

Mit AMS können Sie Medienobjekte auch per Massenvorgang hochladen. Weitere Informationen finden Sie in [diesem](media-services-rest-upload-files.md#upload_in_bulk) Abschnitt.

##Hochladen von Medienobjekten

###Erstellen eines Medienobjekts

>[AZURE.NOTE]Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Entsprechend der Beschreibung unter [Herstellen einer Verbindung mit einem Media Services-Konto über die Media Services-REST-API](media-services-rest-connect_programmatically.md) müssen Sie nachfolgende Aufrufe an den neuen URI senden.
 
Ein Medienobjekt ist ein Container für mehrere Typen oder Gruppen von Objekten in Media Services. Dazu gehören Videos, Audiodateien, Bilder, Miniaturansichtsammlungen, Texttitel und Untertiteldateien. In der REST-API muss beim Erstellen eines Medienobjekts eine POST-Anforderung an Media Services gesendet werden. Dabei müssen alle Eigenschaftsinformationen zum Medienobjekt im Anforderungstext enthalten sein.

Eine der Eigenschaften, die Sie beim Erstellen eines Medienobjekts angeben können, ist **Options**. **Options** ist ein Enumerationswert, der die Verschlüsselungsoptionen beschreibt, mit denen ein Medienobjekt erstellt werden kann. Gültig sind einzelne Werte aus der folgenden Liste, aber keine kombinierten Werte:

- **None** = **0**: Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind. Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option. 

- **StorageEncrypted** = **1**: Geben Sie an, ob Ihre Dateien beim Hochladen und Speichern mit AES-256-Bit-Verschlüsselung verschlüsselt werden sollen.

	Wenn Ihr Medienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: Geben Sie an, ob Sie Dateien hochladen, die mit einer gängigen Verschlüsselungsmethode (z. B. PlayReady) geschützt sind.

- **EnvelopeEncryptionProtected** = **4**: Geben Sie an, ob Sie mit AES-Dateien verschlüsseltes HLS hochladen. Beachten Sie, dass die Dateien durch Transform Manager codiert und verschlüsselt sein müssen.

>[AZURE.NOTE]Wenn Ihr Medienobjekt die Verschlüsselung verwendet, müssen Sie einen **ContentKey** erstellen und mit Ihrem Medienobjekt verknüpfen, wie im folgenden Thema beschrieben: [Erstellen eines ContentKey](media-services-rest-create-contentkey.md). Nachdem Sie die Dateien in das Medienobjekt hochgeladen haben, müssen Sie die Verschlüsselungseigenschaften für die **AssetFile**-Entität anhand der Werte aktualisieren, die Sie während der **Asset**-Verschlüsselung erhalten haben. Verwenden Sie dazu die **MERGE**-HTTP-Anforderung.


Im folgenden Beispiel wird veranschaulicht, wie Sie ein Medienobjekt erstellen.

**HTTP-Anforderung**

	POST https://media.windows.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"BigBuckBunny.mp4"}
	

**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
###Erstellen einer AssetFile

Die [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx)-Entität stellt eine Video- oder Audiodatei dar, die in einem Blobcontainer gespeichert ist. Eine Medienobjektdatei ist immer mit einem Medienobjekt verknüpft, wobei ein Medienobjekt eine oder mehrere Medienobjektdateien enthalten kann. Der Media Services Encoder-Task kann nicht ausgeführt werden, wenn ein Medienobjektdatei-Objekt keiner digitalen Datei in einem Blobcontainer zugeordnet ist.

Die **AssetFile**-Instanz und die eigentliche Mediendatei sind zwei verschiedene Objekte. Die AssetFile-Instanz enthält Metadaten zur Mediendatei, während die Mediendatei die tatsächlichen Medieninhalte enthält.

Nachdem Sie Ihre digitale Mediendatei in einen Blobcontainer hochgeladen haben, verwenden Sie die **MERGE**-HTTP-Anforderung, um die AssetFile anhand von Informationen über Ihre Mediendatei zu aktualisieren (wie später in diesem Thema beschrieben).

**HTTP-Anforderung**

	POST https://media.windows.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP-Antwort**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


### Erstellen der AccessPolicy mit Schreibberechtigung 

Bevor Sie Dateien in den Blobspeicher hochladen, legen Sie die Zugriffsrichtlinienberechtigungen für das Schreiben in ein Medienobjekt fest. Senden Sie dazu eine HTTP POST-Anforderung an die AccessPolicies-Entitätenmenge. Definieren Sie bei der Erstellung einen DurationInMinutes-Wert, da Sie andernfalls eine Antwort mit einer "500 Interner Serverfehler"-Meldung empfangen. Weitere Informationen zu "AccessPolicies" finden Sie unter [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

Im folgenden Beispiel wird veranschaulicht, wie eine AccessPolicy erstellt wird:
		
**HTTP-Anforderung**

	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP-Anforderung**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

###Abrufen der Upload-URL

Um die eigentliche Upload-URL zu empfangen, erstellen Sie einen SAS-Locator. Ein Locator definiert die Startzeit und den Typ des Verbindungsendpunkts für Clients, die auf Dateien in einem Medienobjekt zugreifen möchten. Sie können mehrere Locator-Entitäten für ein bestimmtes AccessPolicy-/ Asset-Paar erstellen, um unterschiedliche Clientanforderungen und -voraussetzungen zu verarbeiten. Jeder dieser Locators verwendet den StartTime-Wert plus den DurationInMinutes-Wert des AccessPolicy-Objekts, um zu bestimmen, für welchen Zeitraum eine URL verwendet werden kann. Weitere Informationen finden Sie unter [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Eine SAS-URL weist das folgende Format auf:

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Folgende Überlegungen sollten berücksichtigt werden:

- Einem bestimmten Medienobjekt können jeweils nicht mehr als fünf eindeutige Locators zugeordnet sein. Weitere Informationen finden Sie unter "Locator".
- Wenn Sie Ihre Dateien sofort hochladen müssen, sollten Sie Ihren StartTime-Wert auf fünf Minuten vor der aktuellen Uhrzeit festlegen. Dies ist erforderlich, weil ggf. eine Uhrzeitabweichung zwischen dem Clientcomputer und Media Services vorliegen kann. Zudem muss der StartTime-Wert das folgende DateTime-Format haben: JJJJ-MM-TTTHH:mm:ssZ (z. B. "2014-05-23T17:53:50Z").	
- Gegebenenfalls tritt eine Verzögerung von 30 bis 40 Sekunden zwischen dem Erstellen eines Locators und seiner Verfügbarkeit auf. Dies gilt für die SAS-URL sowie für Ursprungslocators.

Das folgende Beispiel zeigt, wie Sie einen SAS URL-Locator gemäß der Type-Eigenschaft im Anforderungstext (1 für einen SAS-Locator und 2 für einen On-Demand-Ursprungslocator) erstellen können. Die zurückgegebene **Path**-Eigenschaft enthält die URL, die Sie für den Upload der Datei verwenden müssen.
	
**HTTP-Anforderung**
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

### Hochladen einer Datei in einen Blobspeichercontainer
	
Nachdem Sie AccessPolicy und Locator konfiguriert haben, können Sie die eigentliche Datei mithilfe der Azure Storage-REST-APIs in einen Azure-Blobspeichercontainer hochladen. Sie können Dateien entweder in Seiten- oder Blockblobs hochladen.

>[AZURE.NOTE]Sie müssen den Dateinamen der Uploaddatei in den **Path**-Wert des Locators einfügen, den Sie im vorherigen Abschnitt empfangen haben. Beispiel: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Weitere Informationen zum Arbeiten mit Azure Storage-Blobs finden Sie unter [REST-API für den Blobdienst](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### Aktualisieren der AssetFile 

Nachdem Sie Ihre Datei nun hochgeladen haben, sollten Sie die FileAsset-Größe und andere Informationen aktualisieren. Beispiel:
	
	MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben: HTTP/1.1 204 Kein Inhalt

### Löschen von AccessPolicy und Locator 

**HTTP-Anforderung**


	DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

	
**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

	HTTP/1.1 204 No Content 
	...

**HTTP-Anforderung**

	DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

	HTTP/1.1 204 No Content 
	...

##<a id="upload_in_bulk"></a>Hochladen von Medienobjekten per Massenvorgang

###Erstellen des IngestManifest-Containers

"IngestManifest" ist ein Container für eine Gruppe von Medienobjekten, Medienobjektdateien und statistischen Informationen, die zur Ermittlung des Fortschritts der Massenerfassung für die Gruppe verwendet werden können.


**HTTP-Anforderung**

	POST https:// media.windows.net/API/IngestManifests HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net
	Content-Length: 36
	Expect: 100-continue
	
	{ "Name" : "ExampleManifestREST" }

###Erstellen von Medienobjekten

Bevor Sie das IngestManifestAsset erstellen können, müssen Sie das Medienobjekt erstellen und mithilfe der Massenerfassung verarbeiten. Ein Medienobjekt ist ein Container für mehrere Typen oder Gruppen von Objekten in Media Services. Dazu gehören Videos, Audiodateien, Bilder, Miniaturansichtsammlungen, Texttitel und Untertiteldateien. In der REST-API muss zum Erstellen eines Medienobjekts eine HTTP-POST-Anforderung an Microsoft Azure Media Services gesendet und es müssen alle Eigenschafteninformationen über Ihr Medienobjekt im Anforderungstext platziert werden. In diesem Beispiel wird das Medienobjekt mithilfe der StorageEncrption(1)-Option erstellt, die im Anforderungstext enthalten ist.

**HTTP-Antwort**

	POST https://media.windows.net/API/Assets HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net
	Content-Length: 55
	Expect: 100-continue
	
	{ "Name" : "ExampleManifestREST_Asset", "Options" : 1 }

###Erstellen von IngestManifestAssets

"IngestManifestAssets" repräsentieren Medienobjekte innerhalb eines IngestManifest-Containers, die bei der Massenerfassung verwendet werden. Sie verknüpfen das Medienobjekt mit dem Manifest. Azure Media Services überwacht intern den Dateiupload, basierend auf der mit dem IngestManifestAsset verknüpften IngestManifestFiles-Sammlung. Sobald diese Dateien hochgeladen wurden, ist das Medienobjekt vollständig. Sie können ein neues IngestManifestAsset mit einer HTTP-POST-Anforderung erstellen. Fügen Sie im Anforderungstext die IngestManifest-ID und die Medienobjekt-ID ein, die das IngestManifestAsset zur Massenerfassung miteinander verknüpfen soll.

**HTTP-Antwort**

	POST https://media.windows.net/API/IngestManifestAssets HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net
	Content-Length: 152
	Expect: 100-continue
	{ "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "Asset" : { "Id" : "nb:cid:UUID:b757929a-5a57-430b-b33e-c05c6cbef02e"}}

###(Optional) Erstellen von ContentKeys zur Verschlüsselung

Wenn Ihr Medienobjekt Verschlüsselung verwenden soll, müssen Sie einen ContentKey erstellen, über den die Verschlüsselung erfolgen soll, bevor Sie die IngestManifestFiles für das Medienobjekt erstellen. In diesem Fall sind die folgenden Eigenschaften im Anforderungstext enhalten.
 
Anforderungstexteigenschaft | Beschreibungs-ID | Die ContentKey-ID, die wir selbst in folgendem Format generieren: "nb:kid:UUID:<NEW GUID>". ContentKeyType | Dies ist der Inhaltsschlüsseltyp für diesen Inhaltsschlüssel in Form einer Ganzzahl. Wir übergeben den Wert 1 für die Speicherverschlüsselung. EncryptedContentKey | Wir erstellen einen neuen Inhaltsschlüsselwert mit einer Länge von 256 Bits (32 Bytes). Der Schlüssel wird mithilfe des X.509-Speicherverschlüsselungszertifikats verschlüsselt, das wir von Microsoft Azure Media Services abrufen, indem wir eine HTTP-GET-Anforderung für die Methoden "GetProtectionKeyId" und "GetProtectionKey" ausführen. ProtectionKeyId | Dies ist die Schutzschlüssel-ID für das X.509-Speicherverschlüsselungszertifikat, das zur Verschlüsselung des Inhaltsschlüssels verwendet wurde. ProtectionKeyType | Dies ist der Verschlüsselungstyp für den Schutzschlüssel, der zur Verschlüsselung des Inhaltsschlüssels verwendet wurde. In unserem Beispiel lautet der Wert "StorageEncryption(1)". Checksum | Die per MD5 berechnete Prüfsumme für den Inhaltsschlüssel. Die Berechnung erfolgt durch Verschlüsselung der Inhalts-ID mit dem Inhaltsschlüssel. Der Beispielcode zeigt, wie die Prüfsumme berechnet wird.


**HTTP-Antwort**
	
	POST https://media.windows.net/api/ContentKeys HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net
	Content-Length: 572
	Expect: 100-continue
	
	{"Id" : "nb:kid:UUID:316d14d4-b603-4d90-b8db-0fede8aa48f8", "ContentKeyType" : 1, "EncryptedContentKey" : "Y4NPej7heOFa2vsd8ZEOcjjpu/qOq3RJ6GRfxa8CCwtAM83d6J2mKOeQFUmMyVXUSsBCCOdufmieTKi+hOUtNAbyNM4lY4AXI537b9GaY8oSeje0NGU8+QCOuf7jGdRac5B9uIk7WwD76RAJnqyep6U/OdvQV4RLvvZ9w7nO4bY8RHaUaLxC2u4aIRRaZtLu5rm8GKBPy87OzQVXNgnLM01I8s3Z4wJ3i7jXqkknDy4VkIyLBSQvIvUzxYHeNdMVWDmS+jPN9ScVmolUwGzH1A23td8UWFHOjTjXHLjNm5Yq+7MIOoaxeMlKPYXRFKofRY8Qh5o5tqvycSAJ9KUqfg==", "ProtectionKeyId" : "7D9BB04D9D0A4A24800CADBFEF232689E048F69C", "ProtectionKeyType" : 1, "Checksum" : "TfXtjCIlq1Y=" }

### Verknüpfen des ContentKey mit dem Medienobjekt

Der ContentKey wird durch Senden einer HTTP-POST-Anforderung mit mindestens einem Medienobjekt verknüpft. Folgende Anforderung ist ein Beispiel für die Verknüpfung des Beispiel-ContentKey mit dem Beispielmedienobjekt über die ID.

**HTTP-Antwort**
	
	POST https://media.windows.net/API/Assets('nb:cid:UUID:b3023475-09b4-4647-9d6d-6fc242822e68')/$links/ContentKeys HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net
	Content-Length: 113
	Expect: 100-continue
	
	{ "uri": "https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A32e6efaf-5fba-4538-b115-9d1cefe43510')"}

###Erstellen der IngestManifestFiles für jedes Medienobjekt

Eine IngestManifestFile repräsentiert ein tatsächliches Video- oder Audioblobobjekt, das im Rahmen einer Massenerfassung für ein Medienobjekt hochgeladen wird. Verschlüsselungseigenschaften sind nur erforderlich, wenn das Medienobjekt eine Verschlüsselungsoption verwendet. Das Beispiel in diesem Abschnitt zeigt die Erstellung einer IngestManifestFile, die die Option "StorageEncryption" für das zuvor erstellte Medienobjekt verwendet.


**HTTP-Antwort**

	POST https://media.windows.net/API/IngestManifestFiles HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net
	Content-Length: 367
	Expect: 100-continue
	
	{ "Name" : "REST_Example_File.wmv", "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "ParentIngestManifestAssetId" : "nb:maid:UUID:beed8531-9a03-9043-b1d8-6a6d1044cdda", "IsEncrypted" : "true", "EncryptionScheme" : "StorageEncryption", "EncryptionVersion" : "1.0", "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510" }
	
###Hochladen der Dateien in den Blobspeicher

Sie können eine beliebige hochleistungsfähige Clientanwendung nutzen, die in der Lage ist, die Medienobjektdateien mithilfe des durch die BlobStorageUriForUpload-Eigenschaft von IngestManifest angegebenen URI des Blobspeichercontainers hochzuladen. Der Uploaddienst [Aspera On Demand für Azure](http://go.microsoft.com/fwlink/?LinkId=272001) bietet beispielsweise hohe Geschwindigkeiten.

###Überwachen des Massenerfassungsprozesses

Sie können den Status der Massenerfassung für alle Medienobjekte bestimmen, die einem IngestManifest zugeordnet sind, indem Sie die Statistics-Eigenschaft von IngestManifest abrufen. Diese Eigenschaft weist einen komplexen Typ auf: [IngestManifestStatistics](https://msdn.microsoft.com/library/azure/jj853027.aspx). Zum Abrufen der Statistics-Eigenschaft senden Sie eine HTTP-GET-Anforderung, in der Sie die IngestManifest-ID übergeben.
 

**HTTP-Antwort**

	GET https://media.windows.net/API/IngestManifests('nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048') HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
	Host: media.windows.net


##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)


 
[How to Get a Media Processor]: media-services-get-media-processor.md
 

<!---HONumber=Oct15_HO4-->