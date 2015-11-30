<properties 
	pageTitle="Bereitstellen von Widevine-Lizenzen für Azure Media Services mithilfe von Axinom" 
	description="In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Media Services (AMS) einen Stream übermitteln, der von AMS mit PlayReady- und Widevine-DRMs dynamisch verschlüsselt wird. Die PlayReady-Lizenz stammt vom Media Services PlayReady-Lizenzserver, und die Widevine-Lizenz wird vom Axinom-Lizenzserver bereitgestellt." 
	services="media-services" 
	documentationCenter="" 
	authors="willzhan,Mingfeiy,rajputam,Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Bereitstellen von Widevine-Lizenzen für Azure Media Services mithilfe von Axinom  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##Übersicht

Azure Media Services (AMS) bietet nun dynamischen Google Widevine-Schutz (im [Blog von Mingfei](https://azure.microsoft.com/de-DE/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) finden Sie Einzelheiten). Darüber hinaus bietet Azure Media Player (AMP) nun auch Unterstützung für Widevine (Details finden Sie im [AMP-Dokument](http://amp.azure.net/libs/amp/latest/docs/)). Dies ist eine wichtige Neuerung für das Streamen von durch CENC geschützte Inhalte mit Multi-Native-DRM (PlayReady und Widevine) in modernen Browsern, die mit MSE und EME ausgestattet sind.

Ab Media Services .NET SDK, Version 3.5.2, ermöglicht Media Services Ihnen die Konfiguration der Widevine-Lizenzvorlage und das Abrufen von Widevine-Lizenzen. Sie können sich auch von folgenden AMS-Partnern bei der Übermittlung von Widevine-Lizenzen unterstützen lassen: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

In diesem Artikel wird beschrieben, wie von Axinom verwaltete Lizenzserver integriert und getestet werden. Insbesondere wird Folgendes behandelt:

- Konfigurieren der dynamischen Common Encryption (CENC) mit Multi-DRM (PlayReady und Widevine) mit entsprechenden Lizenzerwerbs-URLs
- Generieren eines JWT-Tokens zum Erfüllen der Lizenzserveranforderungen
- Entwickeln einer Azure Media Player-App, die den Lizenzerwerb mithilfe der JWT-Tokenauthentifizierung unterstützt

Das vollständige System und die Übermittlung von Inhaltsschlüssel, Schlüssel-ID, Schlüsselwert, JTW-Token und dessen Ansprüchen können am besten mithilfe des folgenden Diagramms veranschaulicht werden.

![DASH und CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##Inhaltsschutz

Informationen zum Konfigurieren des dynamischen Schutzes und der Hauptbereitstellungsrichtlinie finden Sie im Blog von Mingfei: [How to configure Widevine packaging with Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Sie können dynamischen CENC-Schutz mit Multi-DRM für DASH-Streaming mit den beiden folgenden Merkmalen konfigurieren:

1. PlayReady-Schutz für MS-Edge und IE11, der Einschränkungen für die Autorisierung mittels Token aufweisen kann. Eine Einschränkungsrichtlinie mit Token muss von einem Token begleitet werden, das von einem Sicherheitstokendienst (STS) wie Azure Active Directory ausgestellt wird.
1. Widevine-Schutz für Chrome, für den eine Authentifizierung mittels Token mithilfe eines von einem anderen STS ausgestellten Tokens erfolgen kann. 

Im Abschnitt [Generierung von JWT-Token](media-services-axinom-integration.md#jwt-token-generation) erfahren, warum Azure Active Directory nicht als ein STS für den Widevine-Lizenzserver von Axinom verwendet werden kann.

###Überlegungen

1. Verwenden Sie den von Axinom angegebenen Schlüsselwert (8888000000000000000000000000000000000000) und Ihre generierte oder ausgewählte Schlüssel-ID zum Generieren des Inhaltsschlüssels zum Konfigurieren des Schlüsselbereitstellungsdiensts. Der Axinom-Lizenzserver stellt alle Lizenzen, die Inhaltsschlüssel enthalten, basierend auf demselben Schlüsselwert aus, der für Test- und Produktionsumgebungen gültig ist.
1. Die Widevine-Lizenzerwerbs-URL für Tests: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP und HTTS sind zulässig.

##Vorbereiten von Azure Media Player

AMP 1.4.0 unterstützt die Wiedergabe von AMS-Inhalten, die dynamisch mit sowohl PlayReady als auch Widevine DRM gepackt sind. Wenn der Widevine-Lizenzserver keine Authentifizierung mittels Token verlangt, sind zum Testen von DASH-Inhalten, die mit Widevine geschützt sind, keine weiteren Schritte erforderlich. Das AMP-Team bietet ein einfaches [Beispiel](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), mit dem die Funktionsweise in Edge und IE11 mit PlayReady und in Chrome mit Widevine erläutert wird. Der von Axinom bereitgestellte Widevine-Lizenzserver verlangt die Authentifizierung mittels JWT-Token. Das JWT-Token muss mit der Lizenzanforderung über einen HTTP-Header vom Typ "X-AxDRM-Message" übermittelt werden. Zu diesem Zweck müssen Sie das folgende Javascript auf der Webseite hinzufügen, die AMP hostet, ehe Sie die Quelle festlegen:

	<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Der Rest des AMP-Codes ist eine standardmäßige AMP-API, wie im AMP-Dokument [hier](http://amp.azure.net/libs/amp/latest/docs/) beschrieben.

Beachten Sie, dass das oben genannte Javascript zum Festlegen des benutzerdefinierten Autorisierungsheaders noch immer ein vorübergehender Ansatz ist, ehe der offizielle endgültige Ansatz in AMP freigegeben wird.

##Generierung von JWT-Token

Der Widevine-Lizenzserver von Axinom verlangt die Authentifizierung mittels JWT-Token. Darüber hinaus hat einer der Ansprüche im JWT-Token anstelle eines primitiven Datentyps einen komplexen Objekttyp.

Leider kann Azure AD nur JWT-Token mit primitiven Typen ausstellen. Gleichermaßen lässt die .NET Framework-API (System.IdentityModel.Tokens.SecurityTokenHandler und JwtPayload) nur die Eingabe komplexer Objekttypen als Ansprüche zu. Die Ansprüche werden jedoch weiterhin als Zeichenfolge serialisiert. Daher können wir keines der beiden zum Generieren des JWT-Tokens für die Widevine-Lizenzanforderung verwenden.


Das [NuGet-Paket für JWT](https://www.nuget.org/packages/JWT) von John Sheehan erfüllt die Anforderungen, weshalb wir dieses NuGet-Paket verwenden.

Es folgt der Code zum Generieren des JWT-Tokens mit den erforderlichen Ansprüchen gemäß den Anforderungen des Widevine-Lizenzservers von Axinom für Tests:

	
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.IdentityModel.Tokens;
	using System.IdentityModel.Protocols.WSTrust;
	using System.Security.Claims;
	
	namespace OpenIdConnectWeb.Utils
	{
	    public class JwtUtils
	    {
	        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
	        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
	        {
	            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
	
	            var payload = new Dictionary<string, object>()
	                         {
	                             { "version", 1 },
	                             { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
	                             { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
	                         };
	
	            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
	
	            return token;
	        }
	
	        //convert hex string to byte[]
	        public static byte[] ConvertHexStringToByteArray(string hexString)
	        {
	            if (hexString.Length % 2 != 0)
	            {
	                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
	            }
	
	            byte[] HexAsBytes = new byte[hexString.Length / 2];
	            for (int index = 0; index < HexAsBytes.Length; index++)
	            {
	                string byteValue = hexString.Substring(index * 2, 2);
	                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
	            }
	
	            return HexAsBytes;
	        }
	
	    }  
	
	}  

Widevine-Lizenzserver von Axinom

	<add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
	<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
	<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
	<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###Überlegungen

1.	Obwohl der AMS PlayReady-Lizenzbereitstellungsdienst die Angabe "Bearer=" vor einem Authentifizierungstoken erfordert, wird diese vom Widevine-Lizenzserver von Axinom nicht verwendet.
2.	Der Axinom-Kommunikationsschlüssel wird als Signaturschlüssel verwendet. Beachten Sie, dass der Schlüssel eine hexadezimale Zeichenfolge ist. Er muss jedoch beim Codieren als eine Reihe von Bytes und nicht als Zeichenfolge behandelt werden. Dies wird mithilfe der "ConvertHexStringToByteArray"-Methode erreicht.

##Abrufen der Schlüssel-ID

Sie haben möglicherweise bemerkt, dass im Code zum Generieren eines JWT-Tokens die Schlüssel-ID erforderlich ist. Da das JWT-Token vor dem Laden des AMP-Players bereit sein muss, muss die Schlüssel-ID abgerufen werden, um das JWT-Token zu generieren.

Es stehen mehrere Verfahren zum Abrufen der Schlüssel-ID zur Verfügung. Die Schlüssel-ID kann beispielsweise zusammen mit den Inhaltsmetadaten in einer Datenbank gespeichert werden. Oder Sie können die Schlüssel-ID aus der DASH MPD-Datei (Media Presentation Description) abrufen. Den Code hierfür finden Sie nachstehend.

	//get key_id from DASH MPD
	public static string GetKeyID(string dashUrl)
	{
	    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
	    {
	        dashUrl += "(format=mpd-time-csf)";
	    }
	
	    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
	    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
	    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
	    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
	    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
	    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
	    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
	    objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
	    objXmlNamespaceManager.PushScope();
	
	    XPathNodeIterator objXPathNodeIterator;
	    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
	
	    string key_id = string.Empty;
	    if (objXPathNodeIterator.MoveNext())
	    {
	        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
	    }
	
	    return key_id;
	}

##Zusammenfassung

Dank der jüngsten Hinzufügung der Widevine-Unterstützung in Azure Media Services Content Protection und Azure Media Player können wir das Streaming von DASH und Multi-Native-DRM (PlayReady + Widevine) mit sowohl dem PlayReady-Lizenzdienst in AMS als auch dem Widevine-Lizenzserver von Axinom für die folgenden modernen Browser implementieren:

- Chrome
- Microsoft Edge für Windows 10
- IE 11 für Windows 8.1 und Windows 10
- Sowohl Firefox (Desktop) als auch Safari für Mac (nicht iOS) werden außerdem über Silverlight und dieselbe URL mit Azure Media Player unterstützt.

Die folgenden Parameter sind in der Minilösung erforderlich, die den Widevine-Lizenzserver von Axinom nutzt. Mit Ausnahme der Schlüssel-ID werden die restlichen Parameter von Axinom basierend auf der Einrichtung des Widevine-Servers bereitgestellt.


Parameter|Verwendung
---|---
Kommunikationsschlüssel-ID|Muss als Wert des Anspruchs "com\_key\_id" in das JWT-Token eingeschlossen werden (siehe [diesen](media-services-axinom-integration.md#jwt-token-generation) Abschnitt).
Kommunikationsschlüssel|Muss als Signaturschlüssel des JWT-Tokens verwendet werden (siehe [diesen](media-services-axinom-integration.md#jwt-token-generation) Abschnitt).
Schlüsselwert|Muss verwendet werden, um den Inhaltsschlüssel mit einer angegebenen Inhaltsschlüssel-ID zu generieren (siehe [diesen](media-services-axinom-integration.md#content-protection) Abschnitt).
Widevine-Lizenzerwerbs-URL|Muss beim Konfigurieren der Übermittlungsrichtlinie für Medienobjekte für DASH-Streaming verwendet werden (siehe [diesen](media-services-axinom-integration.md#content-protection) Abschnitt).
Inhaltsschlüssel-ID|Muss als Teil des Werts des Anspruchs "entitlement\_message" des JWT-Tokens eingeschlossen werden (siehe [diesen](media-services-axinom-integration.md#jwt-token-generation) Abschnitt). 


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###Danksagungen 

Wir möchten folgenden Personen für ihre Beiträge zur Erstellung dieses Dokuments danken: Kristjan Jõgi von Axinom, Mingfei Yan und Amit Rajput.

<!---HONumber=Nov15_HO4-->