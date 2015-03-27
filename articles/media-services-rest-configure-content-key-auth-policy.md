<properties 
	pageTitle="Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel mit REST" 
	description="Erfahren Sie, wie Sie eine Autorisierungsrichtlinie für einen Inhaltsschlüssel konfigurieren." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
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



# Dynamische Verschlüsselung: Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](../media-services-video-on-demand-workflow) und [Media Services: Livestreaming-Workflow](../media-services-live-streaming-workflow) . 

## Übersicht

Mit Microsoft Azure Media Services können Sie Inhalte (dynamisch) verschlüsselt übermitteln, und zwar mit AES (Advanced Encryption Standard unter Verwendung eines 128-Bit-Verschlüsselungsschlüssels) und PlayReady-DRM. Media Services umfasst auch einen Dienst für die Übermittlung von Schlüsseln und PlayReady-Lizenzen an autorisierte Clients. 

Wenn ein Medienobjekt durch Media Services verschlüsselt werden soll, müssen Sie ihm einen Verschlüsselungsschlüssel (**CommonEncryption** oder **EnvelopeEncryption**) zuordnen (wie [hier] beschrieben)(../media-services-rest-create-contentkey/)und zusätzlich Autorisierungsrichtlinien für den Schlüssel konfigurieren (wie in diesem Artikel beschrieben). 

Derzeit können Sie die folgenden Streamingformate verschlüsseln: HLS, MPEG-DASH und Smooth Streaming. Das HDS-Streamingformat oder progressive Downloads können nicht verschlüsselt werden.

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um Ihren Inhalt mit AES- oder PlayReady-Verschlüsselung dynamisch zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu entscheiden, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Autorisierungsrichtlinien aus, die Sie für den Schlüssel angegeben haben.

Media Services unterstützt mehrere Möglichkeiten zur Authentifizierung von Benutzern, die Schlüssel anfordern. Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: **Die Open**-, **Token**- oder **IP**-Einschränkung. Die durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Secure Token Service (STS) ausgestellt wurde. Media Services unterstützt Token im **Simple Web Tokens**-Format ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) und **JSON Web Token**-Format (JWT).  

Secure Token Services werden von Media Services nicht bereitgestellt. Sie können einen benutzerdefinierten STS erstellen oder Microsoft Azure ACS zum Ausstellen von Token nutzen. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausgabe von Ansprüchen konfiguriert sein, die Sie in der Konfiguration der Token-Einschränkung angegeben haben (wie in diesem Artikel beschrieben). Der Schlüsselübermittlungsdienst von Media Services gibt den Verschlüsselungsschlüssel an den Client zurück, wenn das Token gültig ist und die Ansprüche im Token mit den für den Inhaltsschlüssel konfigurierten Ansprüchen übereinstimmen.

Weitere Informationen finden Sie unter 

[JWT-Tokenauthentifizierung](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrieren einer Azure Media Services-OWIN MVC-basierten App in Azure Active Directory und Einschränken der Übermittlung von Inhaltsschlüsseln auf Grundlage von JWT-Ansprüchen](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Ausstellen von Token mithilfe von Azure ACS ](http://mingfeiy.com/acs-with-key-services)

### Folgende Überlegungen sollten berücksichtigt werden:

- Zur Verwendung der dynamischen Paketerstellung und Verschlüsselung müssen Sie über mindestens eine Skalierungseinheit (auch als Streamingeinheit bezeichnet) verfügen. Weitere Informationen finden Sie unter [Skalieren eines Mediendiensts](../media-services-manage-origins#scale_streaming_endpoints). 
- Ihr Medienobjekt muss einen Satz von MP4- oder Smooth Streaming-Dateien mit variablen Bitraten enthalten. Weitere Informationen finden Sie unter [Codieren eines Medienobjekts](../media-services-encode-asset/).  
- Zum Hochladen und Codieren Ihrer Medienobjekte verwenden Sie die Option **AssetCreationOptions.StorageEncrypted**.
- Wenn Sie mehrere Inhaltsschlüssel verwenden möchten, die dieselbe Richtlinienkonfiguration erfordern, wird empfohlen, eine einzelne Autorisierungsrichtlinie zu erstellen und für mehrere Inhaltsschlüssel wiederzuverwenden.
- ContentKeyAuthorizationPolicy und die zugehörigen Objekte (Richtlinienoptionen und Einschränkungen) werden vom Schlüsselübermittlungsdienst für 15 Minuten zwischengespeichert.  Wenn Sie ContentKeyAuthorizationPolicy erstellen und angeben, dass eine "Token"-Einschränkung verwendet werden soll, diese anschließend testen und dann die Richtlinie auf eine "Open"-Einschränkung aktualisieren, dauert es ungefähr 15 Minuten, bis die Richtlinie zur "Open"-Version der Richtlinie wechselt.
- Wenn Sie die Übermittlungsrichtlinie eines Medienobjekts hinzufügen oder aktualisieren, müssen Sie einen vorhandenen Locator (sofern vorhanden) löschen und einen neuen Locator erstellen.


## Dynamische AES-128-Verschlüsselung 

>[AZURE.NOTE] Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Setup für die Entwicklung mit der Media Services REST-API](../media-services-rest-how-to-use).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Wie in [Herstellen einer Verbindung mit Media Services mit der REST-API] beschrieben, müssen Sie nachfolgende Aufrufe an den neuen URI senden(../media-services-rest-connect_programmatically/). 


### Open-Einschränkung

Bei Verwendung einer Open-Einschränkung übermittelt das System den Schlüssel an jeden, der einen Schlüssel anfordert. Diese Einschränkung kann zu Testzwecken nützlich sein.

Im folgenden Beispiel wird eine Open-Autorisierungsrichtlinie erstellt und dem Inhaltsschlüssel hinzugefügt.

#### <a id="ContentKeyAuthorizationPolicies"></a>Erstellen von ContentKeyAuthorizationPolicies

Anforderung:
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423578086&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lZlyQ2%2bvH73qtJsb42%2fH3xF7r7EvQFR3UXyezuDENFU%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 36
	
	{"Name":"Open Authorization Policy"}
	
Antwort:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 211
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
	request-id: aabfa731-e884-4bf3-8314-492b04747ac4
	x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 08:25:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}

#### <a id="ContentKeyAuthorizationPolicyOptions"></a>Erstellen von ContentKeyAuthorizationPolicyOptions
	
Anforderung:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580006&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Ref3EsonGF7fUKCwGwGgiMnZitzIzsDOvvMTeVrVVPg%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 168
	
	{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

Antwort:	
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 349
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
	request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
	x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 08:56:40 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

#### <a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen

Anforderung:
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580006&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Ref3EsonGF7fUKCwGwGgiMnZitzIzsDOvvMTeVrVVPg%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 154
	
	{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}

Antwort:

	HTTP/1.1 204 No Content

#### <a id="AddAuthorizationPolicyToKey"></a>Hinzufügen der Autorisierungsrichtlinie zum Inhaltsschlüssel

Anforderung:

	PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 78
	
	{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}

Antwort:

	HTTP/1.1 204 No Content

### Token-Einschränkung

In diesem Abschnitt wird beschrieben, wie eine Autorisierungsrichtlinie für Inhaltsschlüssel erstellt und dem Inhaltsschlüssel zugeordnet wird. Durch die Autorisierungsrichtlinie wird beschrieben, welche Autorisierungsanforderungen der Benutzer erfüllen muss, um den Schlüssel zu erhalten (beispielsweise wird überprüft, ob der Schlüssel, mit dem das Token signiert wurde, in der Liste der Überprüfungsschlüssel enthalten ist).

Zur Konfiguration der Token-Einschränkungsoption müssen die Autorisierungsanforderungen des Tokens in XML beschrieben werden. Die XML für die Konfiguration der Token-Einschränkung muss folgendem XML-Schema entsprechen.

#### <a id="schema"></a>Schema für die Tokeneinschränkung
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

Bei der Konfiguration der Richtlinie mit **Token**-Einschränkung müssen die Parameter **PrimaryVerificationKey**, **Issuer** und **Audience** angegeben werden. **PrimaryVerificationKey** enthält den Schlüssel, mit dem das Token signiert wurde, und **Issuer** ist der STS (Secure Token Service), von dem das Token ausgestellt wurde. **Audience** (manchmal auch **Scope**) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Schlüsselübermittlungsdienst von Media Services überprüft, ob die Werte im Token mit den Werten in der Vorlage übereinstimmen. 

Im folgenden Beispiel wird eine Autorisierungsrichtlinie mit einer Token-Einschränkung erstellt. In diesem Beispiel müsste der Client ein Token vorlegen, das einen Signaturschlüssel (VerificationKey), einen Tokenherausgeber und erforderliche Ansprüche enthält.
	
### Erstellen von ContentKeyAuthorizationPolicies

Erstellen der durch Token eingeschränkten Richtlinie, wie [hier](#ContentKeyAuthorizationPolicies)beschrieben.


### Erstellen von ContentKeyAuthorizationPolicyOptions

Anforderung:
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423580720&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=5LsNu%2b0D4eD3UOP3BviTLDkUjaErdUx0ekJ8402xidQ%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 1079
	
	{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

Antwort:	
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1260
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
	request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
	x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:10:37 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
	
#### Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen

Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen wie [hier](#ContentKeyAuthorizationPolicies)beschrieben.

#### Hinzufügen der Autorisierungsrichtlinie zum Inhaltsschlüssel

Hinzufügen der AuthorizationPolicy zum ContentKey wie [hier](#AddAuthorizationPolicyToKey)beschrieben.


## Dynamische PlayReady-Verschlüsselung 

Mithilfe von Media Services können Sie die Rechte und Einschränkungen konfigurieren, die für die PlayReady-DRM-Laufzeit erzwungen werden sollen, wenn ein Benutzer versucht, geschützte Inhalte wiederzugeben. 

Wenn Sie Inhalte mit PlayReady schützen, müssen Sie in Ihrer Autorisierungsrichtlinie u. a. eine XML-Zeichenfolge zur Definition der [PlayReady-Lizenzvorlage](https://msdn.microsoft.com/library/azure/dn783459.aspx) angeben. 

### Open-Einschränkung
	
Bei Verwendung einer Open-Einschränkung übermittelt das System den Schlüssel an jeden, der einen Schlüssel anfordert. Diese Einschränkung kann zu Testzwecken nützlich sein.

Im folgenden Beispiel wird eine Open-Autorisierungsrichtlinie erstellt und dem Inhaltsschlüssel hinzugefügt.
	
#### <a id="ContentKeyAuthorizationPolicies2"></a>Erstellen von ContentKeyAuthorizationPolicies

Anforderung:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 58
	
	{"Name":"Deliver Common Content Key"}
	
Antwort:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 233
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
	request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
	x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:26:00 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
	

#### Erstellen von ContentKeyAuthorizationPolicyOptions

Anforderung:
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423581565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=JiNSG3w6r2C0nIyfKvTZj1uPJGjuitD%2b0sbfZ%2b2JDZI%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 593
	
	{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
	
Antwort:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 774
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
	request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
	x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:23:24 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

#### Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen

Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen wie [hier](#ContentKeyAuthorizationPolicies)beschrieben.

#### Hinzufügen der Autorisierungsrichtlinie zum Inhaltsschlüssel

Hinzufügen der AuthorizationPolicy zum ContentKey wie [hier](#AddAuthorizationPolicyToKey)beschrieben.


### Token-Einschränkung

Zur Konfiguration der Token-Einschränkungsoption müssen die Autorisierungsanforderungen des Tokens in XML beschrieben werden. Die XML für die Konfiguration der Token-Einschränkung muss dem in [diesem] Abschnitt beschriebenen XML-Schema entsprechen(#schema) .
	
#### Erstellen von ContentKeyAuthorizationPolicies
	
Erstellen von ContentKeyAuthorizationPolicies wie [hier](#ContentKeyAuthorizationPolicies2)beschrieben.

#### Erstellen von ContentKeyAuthorizationPolicyOptions
	
Anforderung:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 3.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423583561&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=5eZnkOsSv%2fLLEKmS%2bWObBlsNYyee8BQlp%2bUYbjugcJg%3d
	x-ms-version: 2.8
	UserAgent: Azure Media Services .NET SDK v3.1.0.1
	x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 1525
	
	{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

Antwort:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1706
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
	request-id: ccf8a4ba-731e-4124-8192-079592c251cc
	x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Tue, 10 Feb 2015 09:58:47 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testacs.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

#### Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen

Verknüpfen von ContentKeyAuthorizationPolicies mit Optionen wie [hier](#ContentKeyAuthorizationPolicies)beschrieben.

#### Hinzufügen der Autorisierungsrichtlinie zum Inhaltsschlüssel

Hinzufügen der AuthorizationPolicy zum ContentKey wie [hier](#AddAuthorizationPolicyToKey)beschrieben.


## <a id="types"></a>Beim Definieren von ContentKeyAuthorizationPolicy verwendete Typen

### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }


## Nächste Schritte
Nachdem Sie eine Autorisierungsrichtlinie für einen Inhaltsschlüssel konfiguriert haben, fahren Sie mit dem Thema [Konfigurieren einer Übermittlungsrichtlinie für Medienobjekte](../media-services-rest-configure-asset-delivery-policy/) fort.


<!--HONumber=47-->
