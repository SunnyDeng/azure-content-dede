<properties 
	pageTitle="Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von castLabs" 
	description="In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Media Services (AMS) einen Stream übermitteln, der von AMS mit PlayReady- und Widevine-DRMs dynamisch verschlüsselt wird. Die PlayReady-Lizenz stammt vom Media Services PlayReady-Lizenzserver, und die Widevine-Lizenz wird vom castLabs-Lizenzserver übermittelt." 
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
	ms.date="08/11/2015"  
	ms.author="juliako"/>


#Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von castLabs

##Übersicht

In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Media Services (AMS) einen Stream übermitteln, der von AMS mit PlayReady- und Widevine-DRMs dynamisch verschlüsselt wird. Die PlayReady-Lizenz stammt vom Media Services PlayReady-Lizenzserver, und die Widevine-Lizenz wird vom **castLabs**-Lizenzserver übermittelt.

In der folgenden Abbildung ist eine allgemeine Azure Media Services- und castLabs-Integrationsarchitektur dargestellt.

![Integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##Typisches System-Setup

- Medieninhalte werden in AMS gespeichert.
- Schlüssel-IDs der Inhaltsschlüssel werden in castLabs und AMS gespeichert.
- castLabs sowie AMS verfügen über eine integrierte Tokenauthentifizierung. In den folgenden Abschnitten werden Authentifizierungstoken erörtert. 
- Wenn ein Kunde das Streaming eines Videos anfordert, wird der Inhalt mit **CENC** (Common Encryption, allgemeine Verschlüsselung) dynamisch verschlüsselt und von AMS dynamisch in Smooth Streaming und DASH verpackt. Wir stellen auch eine elementare Streamverschlüsselung per PlayReady M2TS für das HLS-Streamingprotokoll bereit.
- Die PlayReady-Lizenz wird vom AMS-Lizenzserver abgerufen und die Widevine-Lizenz vom castLabs-Lizenzserver. 
- Abhängig von der Plattform des Kunden ruft Media Player automatisch die entsprechende Lizenz ab. 

##Generierung von Authentifizierungstoken zum Abrufen einer Lizenz

castLabs sowie AMS unterstützen das für die Autorisierung einer Lizenz verwendete JWT (JSON Web Token)-Tokenformat.

###JWT-Token in AMS 

In der folgenden Tabelle wird das in AMS verwendete JWT-Token beschrieben.

Aussteller|Ausstellerzeichenfolge aus dem ausgewählten Secure Token Service (STS)
---|---
Zielgruppe|Zielgruppenzeichenfolge aus dem verwendeten STS
Ansprüche|Eine Sammlung von Ansprüchen
NotBefore|Beginn der Gültigkeit des Tokens
Expires|Ende der Gültigkeit des Tokens
SigningCredentials|Der Schlüssel, der vom PlayReady-Lizenzserver, dem castLabs-Lizenzserver und STS gemeinsam genutzt wird. Kann ein symmetrischer oder asymmetrischer Schlüssel sein.

###JWT-Token in castLabs

In der folgenden Tabelle wird das in castLabs verwendete JWT-Token beschrieben.

Name|Beschreibung
---|---
optData|Eine JSON-Zeichenfolge, die Informationen über Sie enthält. 
crt|Eine JSON-Zeichenfolge mit Angaben zum Medienobjekt, den zugehörigen Lizenzinformationen und Wiedergaberechten.
iat|Aktuelles Datum und aktuelle Uhrzeit in der Epoche.
jti|Ein eindeutiger Bezeichner des Tokens (jedes Token kann im castLabs-System nur einmal verwendet werden).

##Setup einer Beispiellösung 

Die [Beispiellösung](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) besteht aus zwei Projekten:

-	Einer Konsolenanwendung, mit der DRM-Einschränkungen für ein bereits eingefügtes Medienobjekt für PlayReady und Widevine festgelegt werden können.
-	Einer Webanwendung, die Token ausgibt und als SEHR VEREINFACHTE Version eines STS aufgefasst werden kann.


So verwenden Sie die Konsolenanwendung

1.	Ändern Sie die Datei "app.config", und richten Sie die AMS-Anmeldeinformationen, die castLabs-Anmeldeinformationen, die STS-Konfiguration und den gemeinsam verwendeten Schlüssel ein.
2.	Laden Sie ein Medienobjekt in AMS hoch.
3.	Rufen Sie die UUID aus dem hochgeladenen Medienobjekt ab, und ändern Sie die Zeile 32 in der Datei "Program.cs":

		 var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.	Verwenden Sie zur Benennung des Medienobjekts im castLabs-System eine AssetId (Zeile 44 in der Datei "Program.cs").

	Für die AssetId für **castLabs** müssen Sie eine eindeutige alphanumerische Zeichenfolge festlegen.

5.	Führen Sie das Programm aus.


So verwenden Sie die Webanwendung (STS)

1.	Ändern Sie die Datei "Web.config", und richten Sie die Anbieter-ID für castLabs, die STS-Konfiguration und den gemeinsam verwendeten Schlüssel ein.
2.	Stellen Sie die Datei auf Azure-Websites bereit.
3.	Navigieren Sie zu der Website.

##Wiedergeben eines Videos

Ein durch allgemeine Verschlüsselung (PlayReady) verschlüsseltes Video können Sie mit dem [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) wiedergeben. Wenn Sie die Konsolenanwendung ausführen, werden die Inhaltsschlüssel-ID und die Manifest-URL ausgegeben.

1.	Öffnen Sie eine neue Registerkarte, und starten Sie das STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.	Wechseln Sie zu [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.	Fügen Sie die Streaming-URL ein.
4.	Klicken Sie auf das Kontrollkästchen **Erweiterte Optionen**.
5.	Wählen Sie in der Dropdownliste **Schutz** die Option "PlayReady" aus.
6.	Fügen Sie im Textfeld "Token" das Token ein, das Sie vom STS abgerufen haben.
7.	Aktualisieren Sie den Player.
8.	Das Video sollte wiedergegeben werden.

Wenn Sie das geschützte Video in HTML5 in Chrome mit dem castLabs-Player wiedergeben möchten, wenden Sie sich an yanmf@microsoft.com, um Zugriff auf den Player zu erhalten. Wenn Sie Zugriff haben, sind zwei Punkte zu berücksichtigen:

1.	Der castLabs-Player benötigt Zugriff auf die MPEG-DASH-Manifestdatei. Hängen Sie daher (format=mpd-time-csf) an Ihre Manifestdatei an, um anstelle der Smooth Streaming-Standarddatei die MPEG-DASH-Manifestdatei abzurufen.

2.	Für den castLabs-Lizenzserver ist das Präfix "Bearer=" vor dem Token nicht erforderlich. Entfernen Sie dieses Präfix vor dem Übermitteln des Tokens.

 

<!---HONumber=August15_HO7-->