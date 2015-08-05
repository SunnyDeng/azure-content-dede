<properties 
	pageTitle="Azure Media Services-Konzepte" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Services-Konzepte." 
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
	ms.date="04/15/2015"
	ms.author="juliako"/>

#Azure Media Services-Konzepte 

Dieses Thema bietet eine Übersicht über die wichtigsten Konzepte von Media Services.

##<a id="assets"></a>Medienobjekte\Speicher

###Medienobjekte

Ein [Medienobjekt](https://msdn.microsoft.com/library/azure/hh974277.aspx) enthält digitale Dateien (z. B. Video, Audio, Bilder, Sammlungen von Miniaturansichten, Textspuren und Untertiteldateien) sowie die Metadaten zu diesen Dateien. Nachdem die digitalen Dateien in ein Medienobjekt geladen wurden, können Sie in den Codierungs- und Streaming-Workflows der Media Services verwendet werden.

Ein Medienobjekt wird einem BLOB-Container im Azure Storage-Konto zugeordnet, und die im Medienobjekt enthaltenen Dateien werden als BLOBs in diesem Container gespeichert.

Bei der Entscheidung, welche Medieninhalte in einem Medienobjekt hochgeladen und gespeichert werden sollen, ist Folgendes zu beachten:

- Ein Medienobjekt sollte nur eine einzige, eindeutige Instanz des Medieninhalts enthalten, beispielsweise eine einzige Bearbeitung einer TV-Folge, eines Films oder einer Werbung.
- Ein Medienobjekt sollte nicht mehrere Varianten oder Bearbeitungen einer audiovisuellen Datei enthalten. Ein Beispiel für eine nicht ordnungsgemäße Verwendung eines Medienobjekts wäre der Versuch, mehrere TV-Folgen, Werbungen oder mehrere Kameraeinstellungen aus einer einzigen Produktion in einem Medienobjekt zu speichern. Das Speichern mehrerer Varianten oder Bearbeitungen einer audiovisuellen Datei in einem Medienobjekt kann zu Schwierigkeiten beim Übermitteln von Codierungsaufträgen, beim Streamen und beim Sichern der Übermittlung des Medienobjekts zu einem späteren Zeitpunkt im Workflow führen.  

###Medienobjektdatei 
Die [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx)-Entität stellt eine tatsächliche Video- oder Audiodatei dar, die in einem Blob-Container gespeichert ist. Eine Medienobjektdatei ist immer mit einem Medienobjekt verknüpft, wobei ein Medienobjekt eine oder mehrere Dateien enthalten kann. Der Media Services Encoder-Task schlägt fehl, wenn ein Medienobjektdatei-Objekt keiner digitalen Datei in einem BLOB-Container zugeordnet ist.

Die **AssetFile**-Instanz und die eigentliche Mediendatei sind zwei verschiedene Objekte. Die AssetFile-Instanz enthält Metadaten zur Mediendatei, während die Mediendatei die tatsächlichen Medieninhalte enthält.

Sie sollten nicht versuchen, den Inhalt von BLOB-Containern, die mit Media Services generiert wurden, ohne die Verwendung von Media Service-APIs zu ändern.

###Optionen zur Medienobjektverschlüsselung

Je nach Art der Inhalte, die Sie hochladen, speichern und bereitstellen möchten, stellt Media Services verschiedene Verschlüsselungsoptionen zur Verfügung, aus denen Sie auswählen können.

**None** - Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind.

Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option zum Hochladen der Inhalte.

**StorageEncrypted** – Verwenden Sie diese Option, um Ihre unverschlüsselten Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung zu verschlüsseln und sie dann in Azure Storage hochzuladen, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Speicherverschlüsselung geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für die Speicherverschlüsselung ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.

Um ein speicherverschlüsseltes Medienobjekt zu übermitteln, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren und Media Services so mitteilen, wie die Inhalte bereitgestellt werden sollen. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie (AES, PlayReady oder unverschlüsselt) gestreamt.

**CommonEncryptionProtected** - Verwenden Sie diese Option, wenn Sie Inhalte mit Common Encryption oder PlayReady-DRM (z. B. mit PlayReady-DRM geschütztes Smooth Streaming) verschlüsseln (oder bereits verschlüsselte Inhalte hochladen) möchten.

**EnvelopeEncryptionProtected** – Verwenden Sie diese Option, wenn Sie HTTP Live Streaming (HLS) schützen (oder bereits geschütztes HLS hochladen) möchten, das mit dem Advanced Encryption Standard (AES) verschlüsselt ist. Beachten Sie, dass beim Hochladen von bereits mit AES verschlüsseltem HLS die Verschlüsselung mithilfe von Transform Manager erfolgt sein muss.

###Zugriffsrichtlinie 

Eine [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) definiert Berechtigungen (wie Lesen, Schreiben und Auflisten) sowie die Dauer des Zugriffs auf ein Medienobjekt. Ein AccessPolicy-Objekt wird normalerweise an einen Locator übergeben, der anschließend für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien verwendet wird.


###BLOB-Container

Ein BLOB-Container dient zur Gruppierung eines Satzes von BLOBs. BLOB-Container werden in Media Services als Grenzpunkt für die Zugriffssteuerung und SAS-Locators (Shared Access Signature) für Medienobjekte verwendet. Ein Azure Storage-Konto kann eine unbegrenzte Anzahl von BLOB-Containern enthalten. In einem Container kann eine beliebige Anzahl von BLOBs gespeichert sein.

>[AZURE.NOTE]Sie sollten nicht versuchen, den Inhalt von BLOB-Containern, die mit Media Services generiert wurden, ohne die Verwendung von Media Service-APIs zu ändern.

###<a id="locators"></a>Locator

[Locator](https://msdn.microsoft.com/library/azure/hh974308.aspx) bieten einen Einstiegspunkt für den Zugriff auf die in einem Medienobjekt enthaltenen Dateien. Anhand einer Zugriffsrichtlinie werden die Berechtigungen eines Clients und die Dauer definiert, für die der Client auf eine bestimmte Ressource zugreifen kann. Locator können eine n:1-Beziehung mit einer Zugriffsrichtlinie aufweisen, wobei verschiedene Locator verschiedenen Clients verschiedene Startzeiten und Verbindungstypen bereitstellen können, wobei alle dieselben Berechtigungen und Einstellungen für die Dauer verwenden; aufgrund einer Einschränkung der Richtlinie für den gemeinsamen Zugriff, die von Azure Storage Services festgelegt wird, können jedoch nicht mehr als fünf eindeutige Locator mit einem bestimmten Medienobjekt gleichzeitig verknüpft sein.

Media Services unterstützt zwei Locator-Typen: OnDemandOrigin-Locator, die zum Streamen von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) oder für den progressiven Download von Medien verwendet werden, und SAS-URL-Locator, die zum Hochladen oder Herunterladen von Mediendateien auf/von Azure Storage verwendet werden.

Beachten Sie, dass die Berechtigung zum Auflisten (AccessPermissions.List) beim Erstellen eines OrDemandOrigin-Locators nicht verwendet werden sollte.

###Speicherkonto

Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Ein Media Service-Konto kann mit einem oder mehreren Speicherkonten verknüpft werden. Ein Konto kann eine unbegrenzte Anzahl von Containern enthalten, solange die Gesamtgröße unter 500 TB pro Speicherkonto bleibt. Media Services enthält Tools auf SDK-Ebene, mit denen Sie mehrere Speicherkonten verwalten und bei der Verteilung Ihrer Medienobjekte einen Lastenausgleich durchführen können. Beim Hochladen der Objekte in diese Konten kann dieser Ausgleich basierend auf Kennzahlen oder anhand einer zufälligen Verteilung erfolgen. Weitere Informationen finden Sie unter [Arbeiten mit Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx).

##Aufträge \ Aufgaben

Ein [Auftrag ](https://msdn.microsoft.com/library/azure/hh974289.aspx) wird normalerweise zum Verarbeiten (beispielsweise zum Indizieren oder Codieren) einer einzelnen Audio-/Videopräsentation verwendet. Wenn Sie mehrere Videos verarbeiten, erstellen Sie einen Auftrag für jedes zu verschlüsselnde Video.

Ein Auftrag enthält Metadaten zur auszuführenden Verarbeitung. Jeder Auftrag enthält eine oder mehrere [Aufgaben](https://msdn.microsoft.com/library/azure/hh974286.aspx). Diese geben eine unteilbare Verarbeitungsaufgabe, die zugehörigen Eingabe- und Ausgabemedienobjekte, einen Medienprozessor und die zugehörigen Einstellungen an. Aufgaben innerhalb eines Auftrags können verkettet werden, wobei das Ausgabemedienobjekt als Eingabemedienobjekt für die nächste Aufgabe dient. Auf diese Weise kann ein Auftrag die gesamten Verarbeitungsschritte für eine Medienpräsentation umfassen.

##<a id="encoding"></a>Codierung\Paketerstellung

###Codieren

Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Digitale Videodateien sind sehr umfangreich und möglicherweise zu groß, um sie über das Internet zu übermitteln oder auf den Geräten Ihrer Kunden ordnungsgemäß wiederzugeben. Benutzer sehen Sie sich Videos auf einer Vielzahl von Geräten an, beispielsweise auf Fernsehern mit Set-Top-Boxen, Desktop-PCs, Tablet und Smartphones. Jedes dieser Geräte besitzt unterschiedliche Anforderungen an Bandbreite und Komprimierung. Codierung ist das Verfahren zur Komprimierung von Video- und Audiodaten mithilfe von Komprimierungs- und Dekomprimierungsprogrammen oder Codecs.

Bei der Transcodierung wird ein bereits codiertes Video erneut in einem anderen Codierungsformat codiert. Da Videos auf den meisten Kameras in gewissem Maß codiert werden, handelt es sich bei der Codierungsarbeit in Azure Media Services streng genommen um eine Transcodierung.

###Codecs und Dateiformate 

Codecs verfügen über zwei Komponenten: eine zum Komprimieren digitaler Mediendateien für die Übertragung und die andere zum Dekomprimieren digitaler Mediendateien für die Wiedergabe. Für die Komprimierung und Dekomprimierung von Audiodaten existieren Audiocodecs, und für Videodaten entsprechend Videocodecs. Codecs können entweder verlustfreie oder verlustbehaftete Komprimierung verwenden. Verlustfreie Codecs erhalten bei der Komprimierung alle Informationen. Das Ergebnis der Dekomprimierung ist eine Datei, die identisch mit dem Eingabemedium ist. Verlustfreie Codecs eignen sich daher gut für Archivierung und Speicherung. Verlustbehaftete Codecs verlieren beim Komprimieren einen Teil der Informationen, produzieren kleinere Dateien (im Vergleich zum Original) auf Kosten der Videoqualität und eignen sich gut zum Streaming über das Internet. Die zwei Hauptcodecs, die vom Azure Media Encoder zum Codieren verwendet werden, sind H.264 und VC-1. Andere Codecs sind möglicherweise über unser Partner-Ökosystem von Encodern verfügbar

###Media Services-Encoder

Informationen zu unterstützten Encodern finden Sie unter [Encoder](media-services-encode-asset.md).


##Livestreaming

###Lokaler Live-Encoder (Drittanbieter)

Ein lokaler Live-Encoder (oder -Transcoder) konvertiert die von Ihrer Kamera gestreamten Audio- und/oder Videodaten in ein RTMP- oder Smooth Streaming-Format mit variablen Bitraten. Der Transcoder überträgt dann die RTMP- oder Smooth Streams mit adaptiver Bitrate in einen Media Services-Kanal. Media Services sendet das Ereignis anschließend live.

###Kanal

In Media Services sind [Kanäle](https://msdn.microsoft.com/library/azure/dn783458.aspx) für die Verarbeitung von Livestreaminginhalten zuständig. Ein Kanal stellt einen Eingabeendpunkt (Erfassungs-URL) bereit, den Sie dann einem Live-Transcoder vorlegen. Der Kanal empfängt Live-Eingabestreams aus dem Live-Transcoder und stellt diese zum Streamen durch einen oder mehrere StreamingEndpoints zur Verfügung. Zudem bieten Kanäle einen Vorschauendpunkt (Vorschau-URL), mit dem Sie eine Vorschau des Streams anzeigen und überprüfen können, bevor Sie diesen weiter verarbeiten und übermitteln.

Beim Erstellen des Kanals können Sie die Erfassungs-URL und die Vorschau-URL erhalten. Zum Abfragen dieser URLs muss sich der Kanal nicht im gestarteten Zustand befinden. Wenn Sie zum Übermitteln von Daten aus einem Live-Transcoder an den Kanal bereit sind, muss der Kanal gestartet werden. Sobald der Live-Transcoder mit dem Erfassen von Daten beginnt, können Sie eine Vorschau des Streams anzeigen.

Jedes Media Services-Konto kann mehrere Kanäle, mehrere Programme und mehrere StreamingEndpoints enthalten. Je nach den Anforderungen an Bandbreite und Sicherheit können StreamingEndpoint-Dienste einem oder mehreren Kanälen zugeordnet werden. Jeder StreamingEndpoint kann von jedem Kanal abrufen.

In der Standardeinstellung können Sie Ihrem Media Services-Konto bis zu fünf Kanäle hinzufügen. Informationen zum Anfordern eines höheren Kontingents finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

Eine Abrechnung erfolgt nur für ausgeführte Kanäle.

###Programm 

Mit einem [Programm](https://msdn.microsoft.com/library/azure/dn783463.aspx) können Sie die Veröffentlichung und Speicherung von Segmenten in einem Livestream steuern. Kanäle verwalten Programme. Die Beziehung zwischen Kanal und Programm ähnelt herkömmlichen Medien, bei denen ein Kanal einen konstanten Stream von Inhalten aufweist und ein Programm auf ein zeitlich festgelegtes Ereignis in diesem Kanal ausgerichtet ist. Über die Eigenschaft **ArchiveWindowLength** können Sie die Anzahl der Stunden festlegen, für die Sie den aufgezeichneten Inhalt für das Programm beibehalten möchten. Dieser Wert kann von mindestens 5 Minuten bis zu einem Höchstwert von 25 Stunden eingestellt werden.

ArchiveWindowLength bestimmt außerdem die maximale Dauer, für die Clients von der aktuellen Liveposition aus rückwärts suchen können. Programme können über die angegebene Zeitspanne laufen. Inhalte, die über das Zeitfenster hinausgehen, werden jedoch fortlaufend verworfen. Durch den Wert dieser Eigenschaft wird außerdem festgelegt, wie lange Clientmanifeste wachsen können.

Jedes Programm ist mit einem Medienobjekt verknüpft. Zum Veröffentlichen des Programms müssen Sie einen Locator für das zugehörige Medienobjekt erstellen. Mithilfe dieses Locators können Sie eine Streaming-URL erstellen, die Sie Ihren Clients bereitstellen können.

Ein Kanal unterstützt bis zu drei gleichzeitig ausgeführte Programme, sodass Sie mehrere Archive desselben eingehenden Datenstroms erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Beispielsweise könnte Ihre Geschäftsanforderung darin bestehen, 6 Stunden eines Programms zu archivieren, jedoch nur die letzten 10 Minuten zu senden. Dazu müssen Sie zwei Programme erstellen, die gleichzeitig ausgeführt werden. Ein Programm wird auf die Archivierung von 6 Stunden des Ereignisses festgelegt. Dieses Programm wird jedoch nicht veröffentlicht. Das andere Programm wird auf die Archivierung von 10 Minuten festgelegt. Dieses Programm wird veröffentlicht.

##Schützen von Inhalten

###Dynamische Verschlüsselung

Mit Microsoft Azure Media Services können Sie Inhalte dynamisch verschlüsselt übermitteln, und zwar mit AES (Advanced Encryption Standard unter Verwendung eines 128-Bit-Verschlüsselungsschlüssels) und PlayReady-DRM.

Zur Zeit können Sie die folgenden Streamingformate verschlüsseln: HLS, MPEG DASH und Smooth Streaming. Das HDS-Streamingformat oder progressive Downloads können nicht verschlüsselt werden.

Wenn ein Medienobjekt durch Media Services verschlüsselt werden soll, müssen Sie dem Medienobjekt einen Verschlüsselungsschlüssel (CommonEncryption oder EnvelopeEncryption) zuordnen und zusätzlich Autorisierungsrichtlinien für den Schlüssel konfigurieren.

Außerdem müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren. Wenn Sie ein speicherverschlüsseltes Medienobjekt streamen möchten, geben Sie durch Konfigurieren der Übermittlungsrichtlinie an, wie die Übermittlung erfolgen soll.

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um Ihren Inhalt mit AES- oder PlayReady-Verschlüsselung dynamisch zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu entscheiden, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Autorisierungsrichtlinien aus, die Sie für den Schlüssel angegeben haben.

###PlayReady-DRM-Lizenzen und AES-Übermittlungsdienste mit unverschlüsselten Schlüsseln

Media Services bietet einen Dienst zum Übermitteln von PlayReady-Lizenzen und unverschlüsselten AES-Schlüsseln für autorisierte Clients. Sie können das Azure Management Portal, REST API oder das Media Services-SDK für .NET zum Konfigurieren von Autorisierungs- und Authentifizierungsrichtlinien für Ihre Lizenzen und Schlüssel verwenden.

Beachten Sie, dass Sie über das Portal eine AES-Richtlinie (zur Anwendung auf alle AES-verschlüsselten Inhalte) und eine PlayReady-Richtlinie (zur Anwendung auf alle PlayReady-verschlüsselten Inhalte) konfigurieren können. Verwenden Sie das Media Services-SDK für .NET, wenn Sie mehr Kontrolle über die Konfigurationen benötigen.

###PlayReady-Lizenzvorlage

Media Services bietet einen Dienst für die Übermittlung von PlayReady-Lizenzen. Wenn der Endbenutzer-Player (z. B. Silverlight) versucht, Ihre durch PlayReady geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzübermittlungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, wird die Lizenz ausgegeben. Diese wird an den Client gesendet und kann zum Entschlüsseln und Wiedergeben des angegebenen Inhalts verwendet werden.

Lizenzen enthalten die Rechte und Einschränkungen, die von der PlayReady-DRM-Laufzeit durchgesetzt werden sollen, wenn ein Benutzer versucht, geschützte Inhalte wiederzugeben. Media Services bietet APIs, mit denen Sie Ihre PlayReady-Lizenzen konfigurieren können. Weitere Informationen finden Sie unter [Media Services PlayReady-Lizenzvorlage – Übersicht](https://msdn.microsoft.com/library/azure/dn783459.aspx)

###Token-Einschränkung

Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen: offen, Tokeneinschränkung oder IP-Einschränkung. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Secure Token Service (STS) ausgestellt wurde. Media Services unterstützt Token im Simple Web Tokens (SWT)-Format und JSON Web Token (JWT)-Format. Secure Token Services werden von Media Services nicht bereitgestellt. Sie können einen benutzerdefinierten STS erstellen oder Microsoft Azure ACS zum Ausstellen von Token nutzen. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausstellungsansprüche konfiguriert sein, die Sie in der Konfiguration der Tokeneinschränkung angegeben haben. Der Schlüsselübermittlungsdienst von Media Services gibt den angeforderten Schlüssel (oder die Lizenz) an den Client zurück, wenn das Token gültig ist und die Ansprüche im Token mit den für den Schlüssel (oder die Lizenz) konfigurierten Ansprüchen übereinstimmen.

Bei der Konfiguration der Richtlinie mit Token-Einschränkung müssen die Parameter PrimaryVerificationKey, Issuer und Audience angegeben werden. PrimaryVerificationKey enthält den Schlüssel, mit dem das Token signiert wurde, und Issuer ist der STS (Secure Token Service), von dem das Token ausgestellt wurde. Audience (manchmal auch Scope) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Schlüsselübermittlungsdienst von Media Services überprüft, ob die Werte im Token mit den Werten in der Vorlage übereinstimmen.

##Übermitteln

###<a id="dynamic_packaging"></a>Dynamische Paketerstellung

Bei der Arbeit mit Media Services empfiehlt es sich, Zwischendateien immer in einen MP4-Satz mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung ](media-services-dynamic-packaging-overview.md) in das gewünschte Format zu konvertieren.


###Streamingendpunkt

Ein StreamingEndpoint stellt einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt in einer Client-Player-Anwendung oder einem Content Delivery Network (CDN) bereitstellen kann. (Azure Media Services bietet jetzt die Azure-CDN-Integration.) Der ausgehende Stream des StreamingEndpoint-Diensts kann ein Livestream oder ein Video on Demand-Medienobjekt in Ihrem Media Services-Konto sein. Darüber hinaus können Sie die Kapazität des StreamingEndpoint-Diensts für die Verarbeitung wachsender Bandbreitenanforderungen steuern, indem Sie Skalierungseinheiten (auch als Streamingeinheiten bezeichnet)anpassen. Es wird empfohlen, für Anwendungen in der Produktionsumgebung eine oder mehrere Skalierungseinheiten zu reservieren. Skalierungseinheiten stellen Ihnen dedizierte Ausgangskapazitäten bereit, die zu jeweils 200 Mbit/s erworben werden können, und zusätzliche Funktionen, die aktuell dynamische Paketfunktionen umfassen.

Es wird empfohlen, eine dynamische Paketerstellung und/oder die dynamische Verschlüsselung zu verwenden. Wenn Sie diese Funktionen verwenden möchten, müssen Sie über mindestens eine Streamingeinheit für den Endpunkt verfügen, von dem Sie streamen möchten. Weitere Informationen finden Sie unter [Skalieren von Streamingeinheiten](media-services-manage-origins.md#scale_streaming_endpoints).

In der Standardeinstellung können Sie Ihrem Media Services-Konto bis zu zwei Streaming-Endpunkte hinzufügen. Informationen zum Anfordern eines höheren Kontingents finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

Eine Abrechnung erfolgt nur für ausgeführte StreamingEndpoints.

###Übermittlungsrichtlinie für Medienobjekte

Einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services ist das Konfigurieren von [Übermittlungsrichtlinien für Medienobjekte](https://msdn.microsoft.com/library/azure/dn799055.aspx), die gestreamt werden sollen. Anhand der Übermittlungsrichtlinie für Medienobjekte kann Media Services ermitteln, wie das Medienobjekt übermittelt werden soll, also mit welchem Streamingprotokoll das Medienobjekt dynamisch verpackt werden soll (z. B. MPEG-DASH, HLS, Smooth Streaming oder alle) und ob und wie das Medienobjekt ggf. dynamisch verschlüsselt werden soll (Umschlag- oder allgemeine Verschlüsselung).

Damit ein speicherverschlüsseltes Medienobjekt gestreamt werden kann, entfernt der Streamingserver die Speicherverschlüsselung und streamt den Inhalt anhand der angegebenen Übermittlungsrichtlinie. Wenn Sie ein Medienobjekt für die Übermittlung beispielsweise mit einem Schlüssel für die AES-Verschlüsselung (Advanced Encryption Standard) verschlüsseln möchten, legen Sie den Richtlinientyp auf DynamicEnvelopeEncryption fest. Um die Speicherverschlüsselung zu entfernen und das Medienobjekt unverschlüsselt zu streamen, legen Sie den Richtlinientyp auf NoDynamicEncryption fest.

###Progressiver Download 

Der progressive Download ermöglicht die Wiedergabe von Medien, bevor die gesamte Datei heruntergeladen wurde. Sie können nur eine MP4-Datei progressiv herunterladen.

Beachten Sie, dass verschlüsselte Medienobjekte entschlüsselt werden müssen, wenn diese für den progressiven Download verfügbar sein sollen.

Um Benutzern URLs für den progressiven Download bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt. Anschließend müssen Sie den Namen der MP4-Datei anhängen. Beispiel:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###Streaming-URLs

Streamen Ihre Inhalte auf Clients. Um Benutzern Streaming-URLs bereitzustellen, müssen Sie zuerst einen OnDemandOrigin-Locator erstellen. Beim Erstellen des Locators erhalten Sie den Basispfad für das Medienobjekt mit den Inhalten, die Sie streamen möchten. Um diese Inhalte streamen zu können, müssen Sie diesen Pfad jedoch ändern. Zum Erstellen einer vollständigen URL für die Streaming-Manifestdatei müssen Sie den Pfadwert des Locators mit dem Dateinamen des Manifests (dateiname.ism) verketten. Fügen Sie dem Locatorpfad anschließend "/Manifest" und ein geeignetes Format (falls erforderlich) hinzu.

Sie können auch den Inhalt über eine SSL-Verbindung streamen. Zu diesem Zweck stellen Sie sicher, dass die Streaming-URLs mit HTTPS beginnen.

Beachten Sie, dass Sie nur über SSL streamen können, wenn der Streaming-Endpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streaming-Endpunkten basieren, die nach dem 10. September erstellt wurden, enthält die URL "streaming.mediaservices.windows.net" (neues Format). Streaming-URLs, die "origin.mediaservices.windows.net" (das alte Format) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, erstellen Sie einen neuen Streamingendpunkt. Verwenden Sie die URLs, die basierend auf dem neuen Streaming-Endpunkt erstellt wurden, um Ihre Inhalte über SSL zu streamen.

In der folgenden Liste werden verschiedene Streamingformate beschrieben und Beispiele angegeben:

- Smooth Streaming

	{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest
		
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG DASH

	{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=mpd-time-csf)
 
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP Live Streaming (HLS) V4

	{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP Live Streaming (HLS) V3

	{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=m3u8-aapl-v3)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS (nur für Lizenznehmer von Adobe PrimeTime/Access)

	{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest(Format=f4m-f4f)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f) 

 

<!---HONumber=July15_HO4-->