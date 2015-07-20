<properties 
	pageTitle="Schützen von Inhalten – Übersicht" 
	description="In diesem Artikel finden Sie eine Übersicht über den Schutz von Inhalten mit Media Services." 
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
	ms.date="07/07/2015" 
	ms.author="juliako"/>

#Schützen von Inhalten – Übersicht


Microsoft Azure Media Services ermöglicht die Sicherung Ihrer Medien ab dem Zeitpunkt, an dem sie Ihren Computer durch Speicherung, Verarbeitung und Übermittlung verlassen. Mit Media Services können Sie Inhalte dynamisch verschlüsselt übermitteln, und zwar mit AES (Advanced Encryption Standard unter Verwendung eines 128-Bit-Verschlüsselungsschlüssels) und PlayReady-DRM. Media Services umfasst auch einen Dienst für die Übermittlung von Schlüsseln und PlayReady-Lizenzen an autorisierte Clients.

![content-protection][content-protection]

Damit Sie die dynamische Verschlüsselung verwenden können, müssen Sie zunächst mindestens eine reservierte Einheit für das Streaming auf dem Streamingendpunkt abrufen, auf dem Sie verschlüsselte Inhalte streamen möchten.

##Konzepte

###Optionen zur Medienobjektverschlüsselung

Je nach Art der Inhalte, die Sie hochladen, speichern und bereitstellen möchten, stellt Media Services verschiedene Verschlüsselungsoptionen zur Verfügung, aus denen Sie auswählen können.

**None** - Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind.

Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option zum Hochladen der Inhalte.

**StorageEncrypted** – Verwenden Sie diese Option, um Ihre unverschlüsselten Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung zu verschlüsseln und sie dann in Azure Storage hochzuladen, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Speicherverschlüsselung geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für die Speicherverschlüsselung ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.

Um ein speicherverschlüsseltes Medienobjekt zu übermitteln, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren und Media Services so mitteilen, wie die Inhalte bereitgestellt werden sollen. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie (AES, PlayReady oder unverschlüsselt) gestreamt.

**CommonEncryptionProtected** - Verwenden Sie diese Option, wenn Sie Inhalte mit Common Encryption oder PlayReady-DRM (z. B. mit PlayReady-DRM geschütztes Smooth Streaming) verschlüsseln (oder bereits verschlüsselte Inhalte hochladen) möchten.

**EnvelopeEncryptionProtected** – Verwenden Sie diese Option, wenn Sie HTTP Live Streaming (HLS) schützen (oder bereits geschütztes HLS hochladen) möchten, das mit dem Advanced Encryption Standard (AES) verschlüsselt ist. Beachten Sie, dass beim Hochladen von bereits mit AES verschlüsseltem HLS die Verschlüsselung mithilfe von Transform Manager erfolgt sein muss.



###Dynamische Verschlüsselung

Mit Microsoft Azure Media Services können Sie Inhalte dynamisch verschlüsselt übermitteln, und zwar mit AES (Advanced Encryption Standard unter Verwendung eines 128-Bit-Verschlüsselungsschlüssels) und PlayReady-DRM.

Zur Zeit können Sie die folgenden Streamingformate verschlüsseln: HLS, MPEG DASH und Smooth Streaming. Das HDS-Streamingformat oder progressive Downloads können nicht verschlüsselt werden.

Wenn ein Medienobjekt durch Media Services verschlüsselt werden soll, müssen Sie dem Medienobjekt einen Verschlüsselungsschlüssel (CommonEncryption oder EnvelopeEncryption) zuordnen und zusätzlich Autorisierungsrichtlinien für den Schlüssel konfigurieren.

Außerdem müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren. Wenn Sie ein speicherverschlüsseltes Medienobjekt streamen möchten, geben Sie durch Konfigurieren der Übermittlungsrichtlinie an, wie die Übermittlung erfolgen soll.

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um Ihren Inhalt mit AES- oder PlayReady-Verschlüsselung dynamisch zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu entscheiden, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Autorisierungsrichtlinien aus, die Sie für den Schlüssel angegeben haben.

>[AZURE.NOTE]Um die dynamische Verschlüsselung nutzen zu können, ist mindestens eine bedarfsgesteuerte Streamingeinheit für den Streamingendpunkt erforderlich, aus dem die verschlüsselten Inhalte bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Skalieren von Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

###PlayReady-DRM-Lizenzen und AES-Übermittlungsdienste mit unverschlüsselten Schlüsseln

Media Services bietet einen Dienst zum Übermitteln von PlayReady-Lizenzen und unverschlüsselten AES-Schlüsseln für autorisierte Clients. Sie können das Azure Management Portal, REST API oder das Media Services-SDK für .NET zum Konfigurieren von Autorisierungs- und Authentifizierungsrichtlinien für Ihre Lizenzen und Schlüssel verwenden.

Beachten Sie, dass Sie über das Portal eine AES-Richtlinie (zur Anwendung auf alle AES-verschlüsselten Inhalte) und eine PlayReady-Richtlinie (zur Anwendung auf alle PlayReady-verschlüsselten Inhalte) konfigurieren können. Verwenden Sie das Media Services-SDK für .NET, wenn Sie mehr Kontrolle über die Konfigurationen benötigen.

###PlayReady-Lizenzvorlage

Media Services bietet einen Dienst für die Übermittlung von PlayReady-Lizenzen. Wenn der Endbenutzer-Player (z. B. Silverlight) versucht, Ihre durch PlayReady geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzübermittlungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, wird die Lizenz ausgegeben. Diese wird an den Client gesendet und kann zum Entschlüsseln und Wiedergeben des angegebenen Inhalts verwendet werden.

Lizenzen enthalten die Rechte und Einschränkungen, die von der PlayReady-DRM-Laufzeit durchgesetzt werden sollen, wenn ein Benutzer versucht, geschützte Inhalte wiederzugeben. Media Services bietet APIs, mit denen Sie Ihre PlayReady-Lizenzen konfigurieren können. Weitere Informationen finden Sie unter [Media Services PlayReady-Lizenzvorlage – Übersicht](https://msdn.microsoft.com/library/azure/dn783459.aspx)

###Token-Einschränkung

Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen, Tokeneinschränkung oder IP-Einschränkung. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Secure Token Service (STS) ausgestellt wurde. Media Services unterstützt Token im Simple Web Tokens (SWT)-Format und JSON Web Token (JWT)-Format. Secure Token Services werden von Media Services nicht bereitgestellt. Sie können einen benutzerdefinierten STS erstellen oder Microsoft Azure ACS zum Ausstellen von Token nutzen. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausstellungsansprüche konfiguriert sein, die Sie in der Konfiguration der Tokeneinschränkung angegeben haben. Der Schlüsselübermittlungsdienst von Media Services gibt den angeforderten Schlüssel (oder die Lizenz) an den Client zurück, wenn das Token gültig ist und die Ansprüche im Token mit den für den Schlüssel (oder die Lizenz) konfigurierten Ansprüchen übereinstimmen. Bei der Konfiguration der Richtlinie mit Token-Einschränkung müssen die Parameter PrimaryVerificationKey, Issuer und Audience angegeben werden. PrimaryVerificationKey enthält den Schlüssel, mit dem das Token signiert wurde, und Issuer ist der STS (Secure Token Service), von dem das Token ausgestellt wurde. Audience (manchmal auch Scope) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Schlüsselübermittlungsdienst von Media Services überprüft, ob die Werte im Token mit den Werten in der Vorlage übereinstimmen.

##Häufige Szenarios

###Schützen von Inhalten im Speicher, Übermitteln dynamisch verschlüsselter Streamingmedien, Verwenden des AMS-Schlüssels\\Lizenzbereitstellungsdienstes  

1. Nehmen Sie eine Zwischendatei (Mezzanine File) hoher Qualität in ein Medienobjekt auf. Wenden Sie die Speicherverschlüsselung auf das Medienobjekt an.
2. Konfigurieren von Streamingendpunkten
1. Führen Sie eine Codierung in einen MP4-Satz mit adaptiver Bitrate durch. Wenden Sie die Speicherverschlüsselung auf das Ausgabemedienobjekt an.
1. Erstellen Sie einen Inhaltsverschlüsselungsschlüssel für das Medienobjekt, das während der Wiedergabe dynamisch verschlüsselt werden soll.
2. Konfigurieren Sie Autorisierungsrichtlinien für Inhaltsschlüssel.
1. Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte (wird zur dynamischen Paketerstellung und zur dynamischen Verschlüsselung verwendet).
1. Veröffentlichen Sie das Medienobjekt durch Erstellen eines OnDemand-Locators.
1. Streamen Sie die veröffentlichten Inhalte.

###Verwenden von Media Service-Schlüssel und Lizenzübermittlungsdienst mit eigenen Verschlüsselungs- und Streamingdiensten

Weitere Informationen finden Sie unter [How to integrate Azure PlayReady License service with your own encryptor/streaming server](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server) ("Integrieren des Azure PlayReady-Lizenzierungsdiensts auf einem Verschlüsselungs-/Streamingserver", in englischer Sprache).

###Integrieren in Partneranwendungen

[Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von castLabs](media-services-castlabs-integration.md)


##Häufige Aufgaben bei der Inhaltsverschlüsselung

>[AZURE.NOTE]Wenn Ihr Inhalt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren.

###Konfigurieren von Streamingendpunkten

Eine Übersicht über Streamingendpunkte sowie Informationen zu deren Verwaltung finden Sie unter [Verwalten von Streamingendpunkten in einem Media Services-Konto](media-services-manage-origins.md).

###Erstellen von Inhaltsschlüsseln

Erstellen Sie einen Inhaltsschlüssel für das Verschlüsseln Ihres Medienobjekts mit **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

###Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel 

Konfigurieren Sie Inhaltsschutz und Autorisierungsrichtlinien für Inhaltsschlüssel mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

###Konfigurieren von Übermittlungsrichtlinien für Medienobjekte

Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]


##Verwandte Links

[Announcing PlayReady as a service and AES dynamic encryption with Azure Media Services](http://mingfeiy.com/playready) ("Ankündigen von PlayReady als Dienst und dynamische AES-Verschlüsselung mit Azure Media Services", in englischer Sprache)

[Azure Media Services PlayReady license delivery pricing explained](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services) ("Erläuterung der Preisgestaltung bei der Azure Media Services PlayReady-Lizenzübermittlung", in englischer Sprache)

[How to debug for AES encrypted stream in Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services) ("Debuggen AES-verschlüsselter Streams in Azure Media Services", in englischer Sprache)

[JWT-Tokenauthentifizierung](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrieren einer Azure Media Services-OWIN MVC-basierten App in Azure Active Directory und Einschränken der Übermittlung von Inhaltsschlüsseln auf Grundlage von JWT-Ansprüchen](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Ausstellen von Token mithilfe von Azure ACS](http://mingfeiy.com/acs-with-key-services)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
 

<!---HONumber=July15_HO2-->