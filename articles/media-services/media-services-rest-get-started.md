<properties 
	pageTitle="Erste Schritte zum Bereitstellen von Video-on-Demand (VoD) mit REST-APIs" 
	description="Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mit Azure Media Services mithilfe von REST-APIs." 
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
	ms.date="04/14/2015" 
	ms.author="juliako"/>


#Erste Schritte zum Bereitstellen von Video-on-Demand (VoD) mit REST-APIs 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Kostenlose Azure-Testversion</a>.

Dieser Schnellstart führt Sie durch die Schritte zum Implementieren einer Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mit Azure Media Services (AMS)-REST-APIs.

Das Lernprogramm stellt den Media Services-Arbeitsfluss und die gängigsten Programmierobjekte und Aufgaben für die Media Services-Entwicklung vor. Nach Abschluss des Lernprogramms sind Sie in der Lage, eine einfache Mediendatei zu streamen oder progressiv herunterzuladen, die Sie hochgeladen, codiert und heruntergeladen haben.

## Voraussetzungen
Für den Einstieg in die Entwicklung mit Media Services mithilfe von REST-APIs gelten die folgenden Voraussetzungen:

- Kenntnisse darüber, wie Sie mit der Media Services-REST-API Code entwickeln. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services REST-API](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- Eine Anwendung Ihrer Wahl, die HTTP-Anforderungen und -Antworten senden kann. In diesem Lernprogramm wird [Fiddler](http://www.telerik.com/download/fiddler) verwendet. 

Die folgenden Aufgaben werden in diesem Schnellstart beschrieben.

1.  Erstellen eines Media Services-Kontos mithilfe des Portals
1.  Herstellen einer Verbindung mit dem Media Services-Konto mit der REST-API
1.  Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei mit der REST-API
1.  Konfigurieren von Streamingeinheiten mit der REST-API
2.  Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate mit der REST-API
1.  Konfigurieren der Übermittlungsrichtlinie für das codierte Medienobjekt mit der REST-API
1.  Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download mit der REST-API 
1.  Wiedergeben Ihrer Inhalte 


## <a id="create_ams"></a>Erstellen eines Media Services-Kontos mithilfe des Portals

1. Klicken Sie im [Verwaltungsportal][] auf **Neu**, klicken Sie auf **Mediendienst** und dann auf **Schnellerfassung**.
   
	![Media Services-Schnellerfassung](./media/media-services-rest-get-started/wams-QuickCreate.png)

2. Geben Sie in das Feld **NAME** den Namen des neuen Kontos ein. Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen.

3. Wählen Sie unter **REGION** die geografische Region aus, in der die Metadaten-Datensätze für Ihr Media Services-Konto gespeichert werden sollen. In der Dropdownliste werden nur die verfügbaren Media Services-Regionen angezeigt.

4. Wählen Sie unter **SPEICHERKONTO** ein Speicherkonto aus, das als Blob-Speicher für die Medieninhalte aus Ihrem Media Services-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in derselben geografischen Region wie Ihr Media Services-Konto auswählen oder ein neues Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt.

5. Wenn Sie ein neues Speicherkonto erstellt haben, geben Sie im Feld **NEUER SPEICHERKONTONAME** einen Namen für das Speicherkonto an. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Media Services-Konten.

6. Klicken Sie unten im Formular auf **Schnellerfassung**.

	Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

	Wenn das Konto erfolgreich erstellt wurde, ändert sich der Status in "Aktiv".
	
	Am unteren Rand der Seite wird die Schaltfläche **SCHLÜSSEL VERWALTEN** angezeigt. Wenn Sie auf diese Schaltfläche klicken, wird ein Dialogfeld mit dem Media Services-Kontonamen und dem primären und sekundären Schlüssel angezeigt. Sie benötigen den Kontonamen und den Primärschlüssel, um programmgesteuert auf das Media Services-Konto zugreifen zu können.

	
	![Seite "Media Services"](./media/media-services-rest-get-started/wams-mediaservices-page.png)

	Wenn Sie auf den Kontonamen doppelklicken, wird standardmäßig die Seite "Schnellstart" angezeigt. Auf dieser Seite können Sie einige Verwaltungsaufgaben ausführen, die auch auf anderen Seiten des Portals verfügbar sind. Sie können beispielsweise eine Videodatei auf dieser Seite oder auf der Seite INHALT hochladen.


## <a id="connect"></a>Herstellen einer Verbindung mit dem Media Services-Konto mit der REST-API

Für den Zugriff auf Azure Media Services benötigen Sie: ein von Azure Access Control Services (ACS) bereitgestelltes Zugriffstoken und den Media Services-URI selbst. Zur Erstellung dieser Anforderungen können Sie eine beliebige Methode verwenden, solange Sie die richtigen Headerwerte angeben und das Zugriffstoken beim Aufruf von Media Services ordnungsgemäß übergeben.

Die folgenden Schritte beschreiben den am häufigsten verwendeten Workflow, der bei der Verbindung mit Media Services über die Media Services-REST-API zur Anwendung kommt:

1. Abrufen eines Zugriffstokens 
2. Herstellen einer Verbindung mit dem Media Services-URI 
	
	>[AZURE.NOTE]Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Nachfolgende Aufrufe müssen an den neuen URI gesendet werden.
	> 
	> Möglicherweise empfangen Sie auch eine HTTP/1.1 200-Antwort, die die Beschreibung der ODATA-API-Metadaten enthält.
3. Senden nachfolgender API-Aufrufe an die neue URL. 
	
	Wenn nach einem Verbindungsversuch folgende Meldung angezeigt wird:
		
		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	Senden Sie nachfolgende API-Aufrufe an https://wamsbayclus001rest-hs.cloudapp.net/api/.

###Abrufen eines Zugriffstokens

Um direkt über die REST-API auf Media Services zuzugreifen, rufen Sie das Zugriffstoken von ACS ab und verwenden es während jeder im Dienst ausgeführten HTTP-Anforderung. Dieses Token ähnelt anderen Token, die von ACS auf der Basis von Zugriffsansprüchen im HTTP-Anforderungsheader und mit dem OAuth v2-Protokoll bereitgestellt werden. Für die direkte Verbindung mit Media Services gelten keine weiteren Voraussetzungen.

Das folgende Beispiel zeigt den HTTP-Anforderungsheader bzw. -text, die zum Abrufen eines Tokens verwendet werden.

**Header**:

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**Text**:

Sie müssen den client\_id-Wert und den client\_secret-Wert im Text dieser Anforderung überprüfen. Die Werte client\_id und client\_secret entsprechen dem AccountName-Wert und dem AccountKey-Wert. Diese Werte werden Ihnen von Media Services bei der Einrichtung Ihres Kontos bereitgestellt.

Beachten Sie, dass der "AccountKey" für Ihr Media Services-Konto URL-codiert sein muss, wenn Sie ihn als client\_secret-Wert in Ihrer Zugriffstokenanforderung verwenden.

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Beispiel:

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


Das folgende Beispiel zeigt die HTTP-Antwort, die das Zugriffstoken im Antworttext enthält.

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]Es wird empfohlen, den access\_token-Wert und den expires\_in-Wert in einem externen Speicher zwischenzuspeichern. Die Tokendaten können später aus dem Speicher abgerufen und in den Media Services-REST-API-Aufrufen wiederverwendet werden. Dies ist besonders in Szenarien sinnvoll, in denen das Token auf sichere Weise von mehreren Prozessen oder Computern gemeinsam verwendet werden kann.

Überwachen Sie den expires\_in-Wert des Zugriffstokens, und aktualisieren Sie Ihre REST-API-Aufrufe nach Bedarf anhand neuer Token.

###Herstellen einer Verbindung mit dem Media Services-URI

Der Stamm-URI für Media Services lautet https://media.windows.net/. Sie sollten zunächst eine Verbindung mit diesem URI herstellen. Falls Sie eine 301 Redirect-Antwort erhalten, senden Sie nachfolgende Aufrufe an den neuen URI. Außerdem sollten Sie keine Logik für die automatische Umleitung/Verfolgung in Ihren Anforderungen verwenden. HTTP-Verben und -Anforderungstexte werden nicht an den neuen URI weitergeleitet.

Beachten Sie, dass der Stamm-URI für das Hochladen und Herunterladen von Medienobjektdateien https://yourstorageaccount.blob.core.windows.net/ lautet. Dabei entspricht der Speicherkontoname dem Namen, den Sie bei der Einrichtung des Media Services-Kontos verwendet haben.

Das folgende Beispiel veranschaulicht die HTTP-Anforderung an den Stamm-URI für Media Services (https://media.windows.net/). Auf die Anforderung wird eine 301 Redirect-Antwort zurückgegeben. Die nachfolgende Anforderung wird mithilfe des neuen URI gesendet (https://wamsbayclus001rest-hs.cloudapp.net/api/).

**HTTP-Anforderung**:
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
	Accept: application/json
	Host: media.windows.net


**HTTP-Antwort**:
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**HTTP-Anforderung** (mit den neuen URI):
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-Antwort**:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE]Ab jetzt wird der neue URI in diesem Lernprogramm verwendet.

## <a id="upload"></a>Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei mit der REST-API

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch. Die **Medienobjekt**-Entität kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die Metadaten zu diesen Dateien) enthalten. Nachdem die Dateien in das Medienobjekt hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert.

Zu den Werten, die Sie festlegen müssen, gehören Optionen für die Erstellung von Medienobjekten. Die Eigenschaft **Optionen** ist ein Aufzählungswert, der die Verschlüsselungsoptionen beschreibt, mit denen ein Medienobjekt erstellt werden kann. Gültig sind einzelne Werte aus der folgenden Liste. Es ist jedoch nicht zulässig, Werte aus dieser Liste zu kombinieren:

 
- **None** = **0**: Es wird keine Verschlüsselung verwendet. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind. Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option. 
- **StorageEncrypted** = **1**: Verschlüsselt Ihren lesbaren Inhalt lokal mit der AES-256-Bit-Verschlüsselung und lädt ihn anschließend in Azure Storage hoch, wo er im Ruhezustand verschlüsselt gespeichert wird. Medienobjekte, die durch Storage Encryption geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für Storage Encryption ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.
- **CommonEncryptionProtected** = **2**: Verwenden Sie diese Option, wenn Sie Inhalte hochladen, die bereits verschlüsselt wurden und durch allgemeine Verschlüsselung oder PlayReady-DRM geschützt sind (z. B. mit PlayReady-DRM geschütztes Smooth Streaming).
- **EnvelopeEncryptionProtected** = **4**: Verwenden Sie diese Option, wenn Sie mit AES verschlüsseltes HLS hochladen. Beachten Sie, dass die Dateien durch Transform Manager codiert und verschlüsselt sein müssen.

### Erstellen eines Medienobjekts

Ein Medienobjekt ist ein Container für mehrere Typen oder Gruppen von Objekten in Media Services. Dazu gehören Videos, Audiodateien, Bilder, Miniaturansichtsammlungen, Texttitel und Untertiteldateien. In der REST-API muss beim Erstellen eines Medienobjekts eine POST-Anforderung an Media Services gesendet werden. Dabei müssen alle Eigenschaftsinformationen zum Medienobjekt im Anforderungstext enthalten sein.

Im folgenden Beispiel wird veranschaulicht, wie Sie ein Medienobjekt erstellen.

**HTTP-Anforderung**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 45
	
	{"Name":"BigBuckBunny.mp4", "Options":"0"}
	

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
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny2.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
### Erstellen einer AssetFile

Die [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx)-Entität stellt eine Video- oder Audiodatei dar, die in einem Blob-Container gespeichert ist. Eine Medienobjektdatei ist immer mit einem Medienobjekt verknüpft, wobei ein Medienobjekt eine oder mehrere AssetFiles enthalten kann. Der Media Services Encoder-Task schlägt fehl, wenn ein Medienobjektdatei-Objekt keiner digitalen Datei in einem BLOB-Container zugeordnet ist.

Nachdem Sie Ihre digitale Mediendatei in einen BLOB-Container hochgeladen haben, verwenden Sie die HTTP **MERGE**-Anforderung, um AssetFile mit Informationen über Ihre Mediendatei zu aktualisieren (wie später in diesem Thema beschrieben).

**HTTP-Anforderung**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
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

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 74
	
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
	X-Powered-By: ASP.NET
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

### Abrufen der Upload-URL

Um die eigentliche Upload-URL zu empfangen, erstellen Sie einen SAS-Locator. Ein Locator definiert die Startzeit und den Typ des Verbindungsendpunkts für Clients, die auf Dateien in einem Medienobjekt zugreifen möchten. Sie können mehrere Locator-Entitäten für ein bestimmtes AccessPolicy-/ Asset-Paar erstellen, um unterschiedliche Clientanforderungen und -voraussetzungen zu verarbeiten. Jeder dieser Locators verwendet den StartTime-Wert plus den DurationInMinutes-Wert des AccessPolicy-Objekts, um zu bestimmen, für welchen Zeitraum eine URL verwendet werden kann. Weitere Informationen finden Sie unter [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Eine SAS-URL weist das folgende Format auf:

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Folgende Überlegungen sollten berücksichtigt werden:

- Einem bestimmten Medienobjekt können jeweils nicht mehr als fünf eindeutige Locators zugeordnet sein. Weitere Informationen finden Sie unter "Locator".
- Wenn Sie Ihre Dateien sofort hochladen müssen, sollten Sie Ihren StartTime-Wert auf fünf Minuten vor der aktuellen Uhrzeit festlegen. Dies ist erforderlich, weil ggf. eine Uhrzeitabweichung zwischen dem Clientcomputer und Media Services vorliegen kann. Zudem muss der StartTime-Wert das folgende DateTime-Format haben: JJJJ-MM-TTTHH:mm:ssZ (z. B. "2014-05-23T17:53:50Z").	
- Gegebenenfalls tritt eine Verzögerung von 30 bis 40 Sekunden zwischen dem Erstellen eines Locators und seiner Verfügbarkeit auf. Dies gilt für die SAS-URL sowie für Ursprungslocators.

Das folgende Beispiel zeigt, wie Sie einen SAS URL-Locator gemäß der Type-Eigenschaft im Anforderungstext (1 für einen SAS-Locator und 2 für einen On-Demand-Ursprungslocator) erstellen können. Die zurückgegebene **Path**-Eigenschaft enthält die URL, die Sie für den Upload der Datei verwenden müssen.
	
**HTTP-Anforderung**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 178
	
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
	
	MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben: HTTP/1.1 204 Kein Inhalt

## Löschen von AccessPolicy und Locator 

**HTTP-Anforderung**


	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

	
**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

	HTTP/1.1 204 No Content 
	...

**HTTP-Anforderung**

	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

	HTTP/1.1 204 No Content 
	...

 
## <a id="configure_streaming_units"></a>Konfigurieren von Streamingeinheiten mit der REST-API

Bei der Arbeit mit Azure Media Services ist eines der häufigsten Szenarios das Streaming mit adaptiver Bitrate an Clients. Beim Streaming mit adaptiver Bitrate kann der Client während der Videodarstellung abhängig von der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren auf einen Stream mit höherer oder niedrigerer Bitrate wechseln. Von Media Services werden die folgenden Streamingtechnologien mit adaptiver Bitrate unterstützt: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen.

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Abrufen von mindestens einer Streamingeinheit für den \*\*Streamingendpunkt\*\*, von dem aus Sie die Bereitstellung Ihrer Inhalte (wie in diesem Abschnitt beschrieben) planen.
- Codieren oder Transcodieren Ihrer Zwischendatei (Quelle) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte werden weiter unten in diesem Lernprogramm veranschaulicht).  

Mit der dynamischen Paketerstellung müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client.


>[AZURE.NOTE]Informationen zu den Preisen finden Sie unter [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107).

Um die Anzahl der reservierten Einheiten für das Streaming zu ändern, gehen Sie folgendermaßen vor:
	
### Abrufen des zu aktualisierenden Streamingendpunkts

Hier rufen wir z. B. den ersten Streamingendpunkt in Ihrem Konto ab (es können bis zu zwei Streamingendpunkte im Ausführungsstatus gleichzeitig vorhanden sein).

**HTTP-Anforderung**:

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-Antwort**
	
Im Erfolgsfall wird Folgendes zurückgegeben:
	
	HTTP/1.1 200 OK
	. . . 
	
### Skalieren des Streamingendpunkts
 
**HTTP-Anforderung**:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
	Content-Type: application/json;odata=verbose
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{"scaleUnits":1}

**HTTP-Antwort**

	HTTP/1.1 202 Accepted
	Cache-Control: no-cache
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:16:43 GMT
	Content-Length: 0

	
### <a id="long_running_op_status"></a> Überprüfen des Status eines Vorgangs mit langer Ausführungsdauer

Die Zuordnung neuer Einheiten dauert ca. 20 Minuten. Sie überprüfen den Vorgangsstatus mithilfe der **Operations**-Methode, indem Sie die ID des Vorgangs angeben. Die Vorgangs-ID wurde in der Antwort auf die **Scale**-Anforderung zurückgegeben.

	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**HTTP-Anforderung**:
	
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
**HTTP-Antwort**
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 515
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
	request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:57:39 GMT
	
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
	      },
	      "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
	      "State":"Succeeded",
	      "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
	      "ErrorCode":null,
	      "ErrorMessage":null
	   }
	}


## <a id="encode"></a>Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate

Nach dem Erfassen der Medienobjekte in Media Services können die Medien u. a. codiert, transcodiert/multiplexiert und mit einem Wasserzeichen versehen werden, bevor sie an die Clients übermittelt werden. Diese Aktivitäten werden geplant und für mehrere Hintergrundrolleninstanzen ausgeführt, um hohe Leistung und Verfügbarkeit zu gewährleisten. Diese Aktivitäten werden als Aufträge bezeichnet, und jeder [Auftrag](http://msdn.microsoft.com/library/azure/hh974289.aspx) besteht aus atomaren Aufgaben, die die eigentliche Arbeit für die Medienobjektdatei leisten.

Wie bereits erwähnt, besteht beim Arbeiten mit Azure Media Services eines der häufigsten Szenarios darin, Streaming mit adaptiver Bitrate an Ihre Clients zu übermitteln. Media Services können eine Reihe von MP4-Dateien mit adaptiver Bitrate dynamisch in eines der folgenden Formate verpacken: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren oder Transcodieren Ihrer Zwischendatei (Quelldatei) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate  
- Abrufen von mindestens einer Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen. 

Der folgende Abschnitt veranschaulicht die Erstellung eines Auftrags, der eine Codierungsaufgabe enthält. Die Aufgabe gibt an, dass die Zwischendatei mit **Azure Media Encoder** in einen MP4-Dateisatz mit adaptiver Bitrate transcodiert werden soll. Außerdem wird in diesem Abschnitt erläutert, wie Sie den Fortschritt der Auftragsverarbeitung überwachen. Nachdem Sie den Auftrag abgeschlossen haben, können Sie Locators erstellen, die für den Zugriff auf Ihre Medienobjekte erforderlich sind.

### Abrufen eines Medienprozessors

Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Sie verwenden den Azure Media Encoder, um die in diesem Lernprogramm beschriebene Codierungsaufgabe durchzuführen.

Durch den folgenden Code wird die Encoder-ID angefordert.

**HTTP-Anforderung**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP-Antwort**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 07:54:09 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "Description":"Azure Media Encoder",
	         "Name":"Azure Media Encoder",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"4.4"
	      }
	   ]
	}

### Erstellen eines Auftrags

Je nach Art der Verarbeitung, die Sie durchführen möchten, können einem Auftrag eine oder mehrere Aufgaben zugeordnet sein. Über die REST-API können Sie Aufträge und die zugehörigen Aufgaben auf folgende zwei Arten erstellen: Aufgaben können Inline über die Aufgabennavigationseigenschaft von Auftragsentitäten oder über die OData-Batchverarbeitung definiert werden. Das Media Services SDK verwendet Batchverarbeitung. Für die einfache Lesbarkeit der Codebeispiele in diesem Thema werden Aufgaben jedoch inline definiert. Weitere Informationen zur Batchverarbeitung finden Sie unter [Open Data Protocol (OData) Batch Processing](http://www.odata.org/documentation/odata-version-3-0/batch-processing/) (in englischer Sprache).

Das folgende Beispiel zeigt, wie Sie einen Auftrag mit einer Aufgabe erstellen und bereitstellen, die für die Codierung eines Videos mit einer bestimmten Auflösung und Qualität konfiguriert wurde. Der folgende Abschnitt dieser Dokumentation enthält eine Liste sämtlicher [Aufgabenvoreinstellungen](http://msdn.microsoft.com/library/azure/dn619392.aspx), die vom Azure-Medienprozessor unterstützt werden.

**HTTP-Anforderung**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: application/json
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 482
	
	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
				<outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      }
	   ]
	}

**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1215
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
	Server: Microsoft-IIS/8.5
	request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:04:35 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
	      },
	      "Tasks":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
	         }
	      },
	      "OutputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
	         }
	      },
	      "InputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
	         }
	      },
	      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "Name":"NewTestJob",
	      "Created":"2015-01-19T08:04:34.3287057Z",
	      "LastModified":"2015-01-19T08:04:34.3287057Z",
	      "EndTime":null,
	      "Priority":0,
	      "RunningDuration":0,
	      "StartTime":null,
	      "State":0,
	      "TemplateId":null,
	      "JobNotificationSubscriptions":{  
	         "__metadata":{  
	            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
	         },
	         "results":[  
	
	         ]
	      }
	   }
	}


Bei jeder Auftragsanforderung sind einige wichtige Punkte zu beachten:

- TaskBody-Eigenschaften MÜSSEN literalen XML-Code verwenden, um die Anzahl der von der Aufgabe verwendeten Eingabe- oder Ausgabemedienobjekte zu definieren. Das Thema „Aufgabe“ enthält die XML-Schemadefinition für den XML-Code.
- In der "TaskBody"-Definition muss jeder interne Wert für <inputAsset> und <outputAsset> als "JobInputAsset(value)" oder "JobOutputAsset(value)" festgelegt werden.
- Eine Aufgabe kann mehrere Ausgabemedienobjekte besitzen. Ein JobOutputAsset(x)-Objekt kann nur ein Mal als Ausgabe einer Aufgabe in einem Auftrag verwendet werden.
- Sie können JobInputAsset oder JobOutputAsset als Eingabemedienobjekt einer Aufgabe angeben.
- Aufgaben dürfen keine Schleife bilden.
- Der Value-Parameter, den Sie an JobInputAsset oder JobOutputAsset übergeben, stellt den Indexwert für ein Medienobjekt dar. Die tatsächlichen Medienobjekte werden in den Navigationseigenschaften InputMediaAssets und OutputMediaAssets für die Auftragsentitätsdefinition definiert. 

>[AZURE.NOTE]Da Media Services als Grundlage OData v3 verwendet, wird auf die einzelnen Medienobjekte in den Navigationseigenschaftenauflistungen InputMediaAssets und OutputMediaAssets durch das Name-Wert-Paar „\_\_metadata: uri“ verwiesen.

- InputMediaAssets ist mindestens einem Medienobjekt zugeordnet, das Sie in Media Services erstellt haben. OutputMediaAssets werden vom System erstellt. Sie verweisen nicht auf ein vorhandenes Medienobjekt.
- OutputMediaAssets können mithilfe des assetName-Attributs benannt werden. Wenn dieses Attribut nicht vorhanden ist, wird als Name von OutputMediaAsset der interne Textwert des <outputAsset>-Elements mit dem Wert des Auftragsnamens oder der Auftrags-ID (falls die Name-Eigenschaft nicht definiert ist) als Suffix verwendet. Wenn Sie für assetName z. B. den Wert „Sample“ festlegen, wird die Name-Eigenschaft von OutputMediaAsset auf „Sample“ festgelegt. Wenn Sie jedoch keinen Wert für assetName festgelegt haben, der Auftragsname jedoch auf NewJob festgelegt wurde, wird OutputMediaAsset der Name JobOutputAsset(Wert)\_NewJob zugewiesen.

	Im folgenden Beispiel wird gezeigt, wie das AssetName-Attribut festgelegt wird:
	
		"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"


- So aktivieren Sie die Aufgabenverkettung:

	- Ein Auftrag muss mindestens zwei Aufgaben aufweisen.
	- Es muss mindestens eine Aufgabe vorhanden sein, deren Eingabe die Ausgabe einer anderen Aufgabe im Auftrag ist.

Weitere Informationen finden Sie unter [Erstellen eines Codierungsauftrags mit der Media Services REST-API](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### Überwachen des Verarbeitungsstatus

Sie können den Auftragsstatus mithilfe der State-Eigenschaft abrufen, wie im folgenden Beispiel veranschaulicht.

**HTTP-Anforderung**

	GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 0


**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 17
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Sun, 13 May 2012 05:16:53 GMT
	
	{"d":{"State":2}}


### Abbrechen eines Auftrags

Media Services ermöglicht das Abbrechen aktuell ausgeführter Aufträge über die CancelJob-Funktion. Dieser Aufruf gibt den Fehlercode 400 zurück, wenn Sie versuchen, einen Auftrag abzubrechen, während sein Status „Abgebrochen“, „Wird abgebrochen“, „Fehler“ oder „Abgeschlossen“ lautet.

Das folgende Beispiel zeigt, wie Sie CancelJob aufrufen.


**HTTP-Anforderung**


	GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.2
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
	Host: wamsbayclus001rest-hs.net


Bei Erfolg wird ein Antwortcode 204 ohne Meldungstext zurückgegeben.

>[AZURE.NOTE]Sie müssen die Auftrags-ID URL-codieren (normalerweise nb:jid:UUID:Wert), wenn Sie sie als Parameter an CancelJob übergeben.


### Abrufen des Ausgabemedienobjekts 

Im nächsten Abschnitt konfigurieren wir die Übermittlungsrichtlinie für das Ausgabemedienobjekt (das codierte Medienobjekt) des Auftrags. Der folgende Code zeigt, wie Sie die ID des Ausgabemedienobjekts anfordern.


**HTTP-Anforderung**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP-Antwort**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 445
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:28:13 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
	   "value":[  
	      {  
	         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "State":0,
	         "Created":"2015-01-19T07:52:15.603",
	         "LastModified":"2015-01-19T07:52:15.603",
	         "AlternateId":null,
	         "Name":"Multibitrate MP4s",
	         "Options":0,
	         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "StorageAccountName":"storagetestaccount001"
	      }
	   ]
	}


## <a id="configure_delivery_method"></a>Konfigurieren der Übermittlungsrichtlinie für das codierte Medienobjekt

Einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services ist das Konfigurieren von Übermittlungsrichtlinien für das Medienobjekt. Die Konfiguration der Übermittlungsrichtlinien für das Medienobjekt umfasst u. a. Folgendes: die Protokolle, die verwendet werden können (z. B. MPEG DASH, HLS, HDS, Smooth Streaming oder alle), ob das Medienobjekt dynamisch verschlüsselt werden soll und wie dies geschehen soll (Umschlagverschlüsselung oder allgemeine Verschlüsselung).

Mit der folgenden HTTP-Anforderung **AssetDeliveryPolicies** wird angegeben, dass keine dynamische Verschlüsselung angewendet werden soll (AssetDeliveryPolicyType kann einen der folgenden Werte haben: None = 0, Blocked = 1, NoDynamicEncryption = 2, DynamicEnvelopeEncryption = 3, DynamicCommonEncryption = 4) und dass der Stream in einem der folgenden Protokolle bereitgestellt werden soll: MPEG DASH, HLS oder Smooth Streaming (AssetDeliveryProtocol kann eine Kombination der folgenden Werte sein: None = 0, SmoothStreaming = 1, Dash = 2, HLS = 4, Hds = 8, All = 65535).


### Erstellen von AssetDeliveryPolicies


**HTTP-Anforderung**
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 83
	
	{"Name":"Clear Policy", "AssetDeliveryPolicyType":"2","AssetDeliveryProtocol":"7"} 


**HTTP-Antwort**
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 361
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')
	Server: Microsoft-IIS/8.5
	request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	x-ms-request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 09:13:18 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AssetDeliveryPolicies/@Element",
	   "Id":"nb:adpid:UUID:ef97ae36-b898-4b8a-b427-7819ee726276",
	   "Name":"Clear Policy",
	   "AssetDeliveryProtocol":7,
	   "AssetDeliveryPolicyType":2,
	   "AssetDeliveryConfiguration":null,
	   "Created":"2015-01-19T09:13:18.911615Z",
	   "LastModified":"2015-01-19T09:13:18.911615Z"
	}
    

### Verknüpfen der Übermittlungsrichtlinie für Medienobjekte mit einem Medienobjekt

Die folgende HTTP-Anforderung verknüpft die angegebene Übermittlungsrichtlinie mit dem angegebenen Medienobjekt.

**HTTP-Anforderung**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/$links/DeliveryPolicies HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 140
	
	{ "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')" }


**HTTP-Antwort**

	HTTP/1.1 204 No Content
    . . . 


## <a id="publish_get_urls"></a>Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download mit der REST-API

Um ein Medienobjekt zu streamen oder herunterzuladen, müssen Sie es zunächst durch Erstellen eines Locators „veröffentlichen“. Locator ermöglichen den Zugriff auf Dateien im Medienobjekt. Media Services unterstützt zwei Locator-Typen: OnDemandOrigin-Locator zum Streamen von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) und Access Signature (SAS)-Locator zum Herunterladen von Mediendateien.

Nachdem Sie die Locator erstellt haben, können Sie die URLs erstellen, mit denen Sie die Dateien streamen oder herunterladen möchten.


Eine URL für Smooth Streaming hat das folgende Format:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Eine Streaming-URL für HLS hat das folgende Format:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Eine Streaming-URL für MPEG DASH hat das folgende Format:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Eine SAS-URL zum Herunterladen von Dateien hat das folgende Format:

	{blob container name}/{asset name}/{file name}/{SAS signature}

In diesen Abschnitten wird gezeigt, wie Medienobjekte anhand der folgenden Aufgaben „veröffentlicht“ werden.

- Erstellen der AccessPolicy mit Leseberechtigung 
- Erstellen eines SAS-URLs zum Herunterladen von Inhalten 
- Erstellen einer Ursprungs-URL für das Streamen von Inhalten 

###Erstellen der AccessPolicy mit Leseberechtigung

Vor dem Herunterladen oder Streamen von Medieninhalten definieren Sie zunächst eine AccessPolicy mit Leseberechtigungen und erstellen die entsprechende Locator-Entität, die den Typ des Übermittlungsmechanismus angibt, den Sie für Clients aktivieren möchten. Weitere Informationen zu den verfügbaren Eigenschaften finden Sie unter [AccessPolicy-Entitätseigenschaften](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

Das folgende Beispiel zeigt, wie die AccessPolicy für Leseberechtigungen eines bestimmten Medienobjekts angegeben wird.

	POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 74
	Expect: 100-continue
	
	{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Im Erfolgsfall wird ein 201 Erfolgscode zurückgegeben, der die erstellte AccessPolicy-Entität beschreibt. Anschließend verwenden Sie die AccessPolicy-ID zusammen mit der ID des Medienobjekts, das die zu übermittelnde Datei (z. B. ein Ausgabemedienobjekt) enthält, um die Locator-Entität zu erstellen.

>[AZURE.NOTE]Dieser grundlegende Workflow entspricht dem Hochladen einer Datei bei der Sammelerfassung eines Medienobjekts (wie oben in diesem Thema beschrieben). Wie beim Hochladen von Dateien gilt Folgendes: Wenn Sie (oder die Clients) sofortigen Zugriff auf Ihre Dateien benötigen, legen Sie den StartTime-Wert auf fünf Minuten vor der aktuellen Zeit fest. Dies ist erforderlich, weil ggf. eine Uhrzeitabweichung zwischen dem Clientcomputer und Media Services vorliegen kann. Zudem muss der StartTime-Wert das folgende DateTime-Format haben: JJJJ-MM-TTTHH:mm:ssZ (z. B. "2014-05-23T17:53:50Z").


###Erstellen eines SAS-URLs zum Herunterladen von Inhalten 

Der folgende Code zeigt, wie Sie eine URL abrufen, die zum Herunterladen einer zuvor erstellten und hochgeladenen Mediendatei verwendet werden kann. Für die AccessPolicy wurden Leseberechtigungen festgelegt, und der Locator-Pfad verweist auf eine SAS-URL zum Herunterladen von Inhalten.

	POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1150
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:32 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
	      "ExpirationDateTime":"\/Date(1337049393000)\/",
	      "Type":1,
	      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "StartTime":"\/Date(1337031393000)\/"
	   }
	}


Die zurückgegebene **Path**-Eigenschaft enthält die SAS-URL.

>[AZURE.NOTE]Wenn Sie speicherverschlüsselten Inhalt herunterladen, müssen Sie ihn vor dem Rendern manuell entschlüsseln. Alternativ können Sie den MediaProcessor für die Speicherverschlüsselung in einer Verarbeitungsaufgabe verwenden, um verarbeitete Dateien unverschlüsselt an ein OutputAsset auszugeben und dann von diesem Medienobjekt herunterzuladen. Weitere Informationen zur Verarbeitung finden Sie unter „Erstellen und Verschlüsseln von Aufträgen mit der Media Services-REST-API“. Nachdem die SAS URL-Locators erstellt wurden, können sie nicht mehr aktualisiert werden. Beispielsweise kann derselbe Locator nicht mit einem aktualisierten StartTime-Wert wiederverwendet werden. Dies liegt an der Art, wie SAS-URLs erstellt werden. Wenn ein Locator abgelaufen ist und Sie auf ein Medienobjekt zugreifen möchten, um es herunterzuladen, müssen Sie einen neuen Locator mit einem neuen StartTime-Wert erstellen.

###Herunterladen von Dateien

Nachdem Sie AccessPolicy und Locator konfiguriert haben, können Sie Dateien mithilfe der Azure Storage-REST-APIs herunterladen.

>[AZURE.NOTE]Sie müssen den Dateinamen der Downloaddatei in den **Path**-Wert des Locators einfügen, den Sie im vorherigen Abschnitt empfangen haben. Beispiel: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Weitere Informationen zum Arbeiten mit Azure Storage-BLOBs finden Sie unter [REST-API des Blob-Diensts](http://msdn.microsoft.com/library/azure/dd135733.aspx).

Als Ergebnis des zuvor ausgeführten Codierungsauftrags (Codierung in einen MP4-Satz mit adaptiver Bitrate) verfügen Sie über mehrere MP4-Dateien, die Sie progressiv herunterladen können. Beispiel:
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


###Erstellen einer Streaming-URL für das Streamen von Inhalten


Der folgende Code zeigt, wie Sie einen Streaming-URL-Locator erstellen:

	POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 981
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:39 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
	      "ExpirationDateTime":"\/Date(1337049395000)\/",
	      "Type":2,
	      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
	      "StartTime":"\/Date(1337031395000)\/"
	   }
	}

Um eine Smooth Streaming-Ursprungs-URL in einen Streaming Media Player zu streamen, müssen Sie die Path-Eigenschaft mit dem Namen der Smooth Streaming-Manifestdatei gefolgt von „/ manifest“ anfügen.

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Zum Streamen von HLS fügen Sie (format=m3u8-aapl) nach „/manifest“ an.

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Zum Streamen von MPEG DASH fügen Sie (format=mpd-time-csf) nach „/manifest“ an.

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Wiedergeben Ihrer Inhalte  

Verwenden Sie zum Streamen von Videos [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Fügen Sie zum Testen des progressiven Downloads eine URL in einen Browser ein (z. B. Internet Explorer, Chrome, Safari).

##Nächster Schritt

Weitere Informationen zum Erstellen von Video-on-Demand-Anwendungen erhalten Sie unter [Erstellen von VoD-Anwendungen](media-services-video-on-demand-workflow.md).


##Zusätzliche Ressourcen
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Stellen Sie Ihr Video jetzt online!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamische Pakete und mobile Geräte</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Verwaltungsportal]: http://manage.windowsazure.com/


 

<!---HONumber=August15_HO6-->