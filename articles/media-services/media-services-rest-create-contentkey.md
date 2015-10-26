<properties 
	pageTitle="Erstellen von ContentKeys mit REST" 
	description="Erfahren Sie, wie Sie Inhaltsschlüssel erstellen, die den sicheren Zugriff auf Medienobjekte ermöglichen." 
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
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#Erstellen von ContentKeys mit REST


> [AZURE.SELECTOR]
- [REST](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)


Media Services ermöglicht das Erstellen neuer und Übermitteln verschlüsselter Medienobjekte. Ein **ContentKey** ermöglicht den sicheren Zugriff auf Ihre **Medienobjekte**.

Beim Erstellen eines neuen Medienobjekts (z. B. vor dem [Hochladen von Dateien](media-services-rest-upload-files.md)) können Sie die folgenden Verschlüsselungsoptionen angeben: **StorageEncrypted**, **CommonEncryptionProtected** oder **EnvelopeEncryptionProtected**.

Wenn Sie Medienobjekte an Ihre Clients übermitteln, können Sie mithilfe einer der beiden folgenden Verschlüsselungen die [dynamische Verschlüsselung von Medienobjekten konfigurieren](media-services-rest-configure-asset-delivery-policy.md): **DynamicEnvelopeEncryption** oder **DynamicCommonEncryption**.

Verschlüsselte Medienobjekte müssen **ContentKeys** zugeordnet werden. In diesem Artikel wird beschrieben, wie ein Inhaltsschlüssel erstellt wird.

Im Folgenden finden Sie allgemeine Schritte zum Generieren von Inhaltsschlüsseln, die Sie den zu verschlüsselnden Medienobjekten zuordnen.

1. Generieren Sie nach dem Zufallsprinzip einen 16-Byte-AES-Schlüssel (für die allgemeine und Umschlagsverschlüsselung) oder einen 32-Byte-AES-Schlüssel (für die Speicherverschlüsselung). 

	Dies ist der Inhaltsschlüssel für Ihr Medienobjekt. Das bedeutet, dass alle mit diesem Medienobjekt verknüpften Dateien denselben Inhaltsschlüssel zur Entschlüsselung verwenden müssen. 
2.	Rufen Sie die [GetProtectionKeyId](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkeyid)-Methode und die [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey)-Methode auf, um das richtige X.509-Zertifikat zur Verschlüsselung Ihres Inhaltsschlüssels abzurufen.
3.	Verschlüsseln Sie Ihren Inhaltsschlüssel mit dem öffentlichen Schlüssel des X.509-Zertifikats. 

	Das Media Services .NET SDK verwendet RSA mit OAEP zur Verschlüsselung. Ein Beispiel finden Sie in der [EncryptSymmetricKeyData-Funktion](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4.	Erstellen Sie einen Prüfsummenwert (basierend auf den Prüfsummenalgorithmus des PlayReady AES-Schlüssels), der anhand der Schlüsselkennung und des Inhaltsschlüssels berechnet wird. Weitere Informationen finden Sie [hier](http://www.microsoft.com/playready/documents/) im Abschnitt "Prüfsummenalgorithmus für den PlayReady AES-Schlüssel" zum PlayReady-Headerobjekt.

	Im folgenden .NET-Beispiel wird die Prüfsumme anhand des GUID-Abschnitts der Schlüsselkennung und des unverschlüsselten Inhaltsschlüssels berechnet.
	
		public static string CalculateChecksum(byte[] contentKey, Guid keyId)
		{
		    byte[] array = null;
		    using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
		    {
		        aesCryptoServiceProvider.Mode = CipherMode.ECB;
		        aesCryptoServiceProvider.Key = contentKey;
		        aesCryptoServiceProvider.Padding = PaddingMode.None;
		        ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
		        array = new byte[16];
		        cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
		    }
		    byte[] array2 = new byte[8];
		    Array.Copy(array, array2, 8);
		    return Convert.ToBase64String(array2);
		}

5. Erstellen Sie den Inhaltsschlüssel mit dem (in eine base64-codierte Zeichenfolge konvertierten) **EncryptedContentKey** und den Werten für **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** und **Checksum**, die Sie in den vorherigen Schritten erhalten haben.
6. Verwenden Sie den $links-Vorgang, um die **ContentKey**-Entität mit der **Asset**-Entität zu verknüpfen.

In diesem Thema wurden die Beispiele zum Generieren eines AES-Schlüssels, zur Schlüsselverschlüsselung und zur Berechnung der Prüfsumme ausgelassen. Es wird nur auf Beispiele eingegangen, die die Interaktion mit Media Services betreffen.


>[AZURE.NOTE]Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Entsprechend der Beschreibung unter [Herstellen einer Verbindung mit einem Media Services-Konto über die Media Services-REST-API](media-services-rest-connect_programmatically.md) müssen Sie nachfolgende Aufrufe an den neuen URI senden.

##Abrufen der ProtectionKeyId 
 

Im folgenden Beispiel wird veranschaulicht, wie Sie die ProtectionKeyId abrufen. Dabei handelt es sich um einen Zertifikatfingerabdruck für das Zertifikat, das Sie zur Verschlüsselung Ihres Inhaltsschlüssels verwenden. Führen Sie den folgenden Schritt aus, um sicherzustellen, dass das entsprechende Zertifikat bereits auf Ihrem Computer vorhanden ist.


Anforderung:
	
	
	GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
	x-ms-version: 2.8
	Host: media.windows.net
	

Antwort:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 139
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
	x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 04 Feb 2015 02:42:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

##Abrufen des ProtectionKey für die ProtectionKeyId

Das folgende Beispiel zeigt, wie Sie das X.509-Zertifikat mithilfe der im vorherigen Schritt abgerufenen ProtectionKeyId abrufen.

Anforderung:
		
	GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
	Host: media.windows.net
	


Antwort:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1227
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
	request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
	x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 05 Feb 2015 07:52:30 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
	"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

##Erstellen des ContentKey 

Nachdem Sie das X.509-Zertifikat abgerufen und dessen öffentlichen Schlüssel zum Verschlüsseln Ihres Inhaltsschlüssels verwendet haben, erstellen Sie eine **ContentKey**-Entität und legen die entsprechenden Eigenschaftswerte fest.

Beim Erstellen des Inhaltsschlüssels muss neben anderen Werten auch der Typ festgelegt werden. Wählen Sie unter folgenden Werten aus.

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,
    }


Das folgende Beispiel zeigt, wie Sie einen **ContentKey** mit einem **ContentKeyType** erstellen, für den die Speicherverschlüsselung festgelegt ("1") und **ProtectionKeyType** auf "0" festgelegt wurde. Dies zeigt an, dass es sich bei der Schutzschlüssel-ID um den X. 509-Zertifikatfingerabdruck handelt.


Anforderung

	POST https://media.windows.net/api/ContentKeys HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
	x-ms-version: 2.8
	Host: media.windows.net
	{
	"Name":"ContentKey",
	"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
	"ContentKeyType":"1", 
	"ProtectionKeyType":"0",
	"EncryptedContentKey":"your encrypted content key",
	"Checksum":"calculated checksum"
	}


Antwort:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 777
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
	Server: Microsoft-IIS/8.5
	request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
	x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 04 Feb 2015 02:37:46 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
	"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
	"LastModified":"2015-02-04T02:37:46.9684379Z",
	"ContentKeyType":1,
	"EncryptedContentKey":"your encrypted content key",
	"Name":"ContentKey",
	"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
	"ProtectionKeyType":0,
	"Checksum":"calculated checksum"}

##Zuordnen des ContentKey zu einem Medienobjekt

Nachdem Sie den ContentKey erstellt haben, ordnen Sie ihn mithilfe des $links-Vorgangs Ihrem Medienobjekt zu, wie im folgenden Beispiel beschrieben:
	
Anforderung:
	
	POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
	x-ms-version: 2.8
	Host: media.windows.net

	
	{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Antwort:

	HTTP/1.1 204 No Content 



##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

<!---HONumber=Oct15_HO3-->