<properties 
	pageTitle="Herstellen einer Verbindung mit einem Media Services-Konto mit der REST-API" 
	description="In diesem Thema wird veranschaulicht, wie Sie über die REST-API eine Verbindung mit Media Services herstellen." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


# Herstellen einer Verbindung mit einem Media Services-Konto über die Media Services-REST-API

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect_programmatically.md)
- [REST](media-services-rest-connect_programmatically.md)

In diesem Thema wird beschrieben, wie bei der Programmierung mit der Media Services-REST-API eine programmgesteuerte Verbindung mit Microsoft Azure Media Services hergestellt wird.

Für den Zugriff auf Microsoft Azure Media Services benötigen Sie: ein von Azure Access Control Services (ACS) bereitgestelltes Zugriffstoken und den Media Services-URI selbst. Zur Erstellung dieser Anforderungen können Sie eine beliebige Methode verwenden, solange Sie die richtigen Headerwerte angeben und das Zugriffstoken beim Aufruf von Media Services ordnungsgemäß übergeben.

Die folgenden Schritte beschreiben den am häufigsten verwendeten Workflow, der bei der Verbindung mit Media Services über die Media Services-REST-API zur Anwendung kommt:

1. Abrufen eines Zugriffstokens 
2. Herstellen einer Verbindung mit dem Media Services-URI 

	>[AZURE.NOTE]Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Nachfolgende Aufrufe müssen an den neuen URI gesendet werden. Möglicherweise empfangen Sie auch eine HTTP/1.1 200-Antwort, die die Beschreibung der ODATA-API-Metadaten enthält.

3. Senden Sie nachfolgende API-Aufrufe an die neue URL.

	Wenn nach einem Verbindungsversuch folgende Meldung angezeigt wird:

		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	Senden Sie nachfolgende API-Aufrufe an https://wamsbayclus001rest-hs.cloudapp.net/api/.

##Abrufen eines Zugriffstokens

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

Sie müssen den "client\_id"-Wert und den "client\_secret"-Wert im Text dieser Anforderung überprüfen. Die Werte "client\_id" und "client\_secret" entsprechen dem "AccountName"-Wert und dem "AccountKey"-Wert. Diese Werte werden Ihnen von Media Services bei der Einrichtung Ihres Kontos bereitgestellt.

Beachten Sie, dass der "AccountKey" für Ihr Media Services-Konto URL-codiert sein muss (siehe [Prozentcodierung](http://tools.ietf.org/html/rfc3986#section-2.1)), wenn Sie ihn als "client\_secret"-Wert in Ihrer Zugriffstokenanforderung verwenden.

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
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
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
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.9
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
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.9
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
	 


>[AZURE.NOTE]Nachdem Sie den neuen URI abgerufen haben, verwenden Sie ihn für die Kommunikation mit Media Services.



##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)


<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=Oct15_HO3-->