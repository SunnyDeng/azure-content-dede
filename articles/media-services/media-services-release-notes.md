<properties 
	pageTitle="Versionshinweise zu Media Services" 
	description="Versionshinweise zu Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="juliako"/>


# Versionshinweise zu Azure Media Services

In diesen Versionshinweisen werden Änderungen im Vergleich zu früheren Versionen und bekannte Probleme zusammengefasst.

>[AZURE.NOTE]Wir wünschen uns Feedback von unseren Kunden und möchten Probleme beheben, die negative Auswirkungen auf Ihre Arbeit haben. Wenn Sie ein Problem melden oder Fragen stellen möchten, verfassen Sie bitte einen Beitrag im [MSDN-Forum für Azure Media Services].

- [Aktuell bekannte Probleme](#issues)
- [REST-API-Versionsverlauf](#rest_version_history)
- [Version Juni 2015](#june_changes_15)
- [Version Mai 2015](#may_changes_15)
- [Version April 2015](#april_changes_15)
- [Version März 2015](#march_changes_15)
- [Version Februar 2015](#february_changes_15)
- [Version Januar 2015](#january_changes_15)
- [Version Dezember 2014](#december_changes_14)
- [Version November 2014](#november_changes_14)
- [Version Oktober 2014](#october_changes_14)
- [Version September 2014](#september_changes_14)
- [Version August 2014](#august_changes_14)
- [Version Juli 2014](#july_changes_14)
- [Version Mai 2014](#may_changes_14)
- [Version April 2014](#april_changes_14) 
- [Versionen Januar\Februar 2014](#jan_feb_changes_14) 
- [Version Dezember 2013](#december_changes_13)
- [Version November 2013](#november_changes_13)
- [Version August 2013](#august_changes_13)
- [Version Juni 2013](#june_changes_13)
- [Version Dezember 2012](#december_changes_12)
- [Version November 2012](#november_changes_12)
- [Vorschauversion Juni 2012](#june_changes_12)


##<a id="issues"></a>Aktuell bekannte Probleme

### <a id="general_issues"></a>Allgemeine Probleme von Media Services

<table border="1"> <tr><th>Problem</th><th>Beschreibung</yt></tr> <tr><td>Verschiedene gängige HTTP-Header werden in der REST-API nicht bereitgestellt. </td><td>Wenn Sie Media Services-Anwendungen mithilfe der REST-API entwickeln, wird Ihnen auffallen, dass einige gängige HTTP-Headerfelder (einschließlich CLIENT-REQUEST-ID, REQUEST-ID und RETURN-CLIENT-REQUEST-ID) nicht unterstützt werden. Diese Header werden in einem späteren Update hinzugefügt.</td></tr> <tr><td>Beim Codieren eines Medienobjekts mit einem Dateinamen, der Escapezeichen (z. B. %20) enthält, tritt der Fehler "MediaProcessor: Datei nicht gefunden."</td><td>Die Namen von Dateien, die einem Medienobjekt hinzugefügt und dann codiert werden sollen, sollten nur alphanumerische Zeichen und Leerzeichen enthalten. Dieses Problem wird in einem späteren Update behoben.</td></tr> <tr><td>Die ListBlobs-Methode, die Teil des Azure Storage-SDK, Version 3.x, ist, kann nicht ausgeführt werden.</td><td>Media Services generiert SAS-URLs auf Grundlage der Version <a href="http://msdn.microsoft.com/library/azure/dn592123.aspx">2012-02-12</a>. Wenn Sie das Azure Storage-SDK zur Auflistung von Blobs in einem Blobcontainer nutzen möchten, verwenden Sie die <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a>-Methode, die Teil des Azure Storage-SDK, Version 2.x, ist. Die ListBlobs-Methode, die Teil des Azure Storage-SDK, Version 3.x, ist, kann nicht ausgeführt werden.</td></tr> <tr><td>Der Drosselungsmechanismus von Media Services schränkt die Ressourcenverwendung für Anwendungen ein, die zu viele Anforderungen an den Dienst ausgeben. Der Dienst kann den HTTP-Statuscode "Dienst nicht verfügbar (503)" zurückgeben.</td><td>Weitere Informationen finden Sie in der Beschreibung des HTTP-Statuscodes 503 unter <a href="http://msdn.microsoft.com/library/azure/dn168949.aspx">Azure Media Services-Fehlercodes</a>.</td></tr> </table><br/>
 
### <a id="dotnet_issues"></a>Probleme in Media Services SDK für .NET

<table border="1"> <tr><th>Problem</th><th>Beschreibung</yt></tr> <tr><td>Media Services-Medienobjekte im SDK können nicht serialisiert werden und funktionieren daher nicht mit Azure Caching.</td><td>Wenn Sie versuchen, das AssetCollection-Objekt des SDK zu serialisieren, um es Azure Caching hinzuzufügen, wird eine Ausnahme ausgelöst.</td></tr> </table><br/>

##<a id="rest_version_history"></a>REST-API-Versionsverlauf

Informationen zum Versionsverlauf der Media Services-REST-API finden Sie unter [Azure Media Services – REST-API-Referenz].

##<a id="june_changes_15"></a>Version Juni 2015

###Media Services .NET SDK-Updates

Azure Media Services .NET SDK ist jetzt in der Version 3.3.0.0 erhältlich. Die folgende Funktionalität wurde in dieser Version hinzugefügt:

- Unterstützung für die OpenId Connect Discovery-Spezifikation
- Unterstützung für die Verarbeitung von Schlüsselrollover auf der Seite des Identitätsanbieters 

Wenn Sie einen Identitätsanbieter verwenden, der das OpenID Connect Discovery-Dokument verfügbar macht (wie die folgenden Anbieter: Azure Active Directory, Google, Salesforce), können Sie Azure Media Services anweisen, Signaturschlüssel zur Überprüfung des JWT-Tokens aus der OpenID Connect Discovery-Spezifikation abzurufen.

Weitere Informationen finden Sie unter [Using Json Web Keys from OpenID Connect discovery spec to work with JWT token authentication in Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/) (in englischer Sprache).


##<a id="may_changes_15"></a>Version Mai 2015

Ankündigung der folgenden neuen Funktionen:

- [Eine Vorschau der Livecodierung mit Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Dynamisches Manifest](media-services-dynamic-manifest-overview.md)
- [Eine Vorschau des Azure Media Hyperlapse-Medienprozessors](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Version April 2015

###Allgemeine Media Services-Updates

- [Ankündigung von Azure Media Player](http://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Beginnend mit Media Services REST 2.10 werden Kanäle, die zur Erfassung eines RTMP-Protokolls konfiguriert sind, mit primären und sekundären Erfassungs-URLs erstellt. Weitere Informationen finden Sie unter [Kanalerfassungskonfigurationen](media-services-manage-channels-overview.md#channel_input)
- Azure Media Indexer-Updates
	- Unterstützung für Spanisch
	- Neues Konfigurations-XML-Format
	
	Weitere Informationen finden Sie in [diesem Blog](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###Media Services .NET SDK-Updates

Azure Media Services .NET SDK ist jetzt in der Version 3.2.0.0 erhältlich.

Im Folgenden werden einige der Updates für Kunden aufgeführt:
 
- **Wichtige Änderung**: **TokenRestrictionTemplate.Issuer** und **TokenRestrictionTemplate.Audience** weisen jetzt einen Zeichenfolgentyp auf. 
- Updates im Zusammenhang mit der Erstellung von benutzerdefinierten Wiederholungsrichtlinien. 
- Fehlerbehebungen im Zusammenhang mit dem Hochladen/Herunterladen von Dateien. 
- Die **MediaServicesCredentials**-Klasse akzeptiert jetzt primäre und sekundäre Endpunkte für die Zugriffssteuerung zum Authentifizieren.



##<a id="march_changes_15"></a>Version März 2015

### Allgemeine Media Services-Updates

- Media Services bietet jetzt eine Integration von Azure CDN. Zur Unterstützung der Integration wurde **StreamingEndpoint** die **CdnEnabled**-Eigenschaft hinzugefügt. **CdnEnabled** kann mit REST-APIs ab Version 2.9 verwendet werden (weitere Informationen finden Sie unter [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)). **CdnEnabled** kann mit dem .NET SDK ab Version 3.1.0.2 verwendet werden (weitere Informationen finden Sie unter [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Ankündigung des **Media Encoder Premium-Workflow**. Weitere Informationen finden Sie unter [Introducing Premium Encoding in Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services) (in englischer Sprache).
 


##<a id="february_changes_15"></a>Version Februar 2015

### Allgemeine Media Services-Updates

Die Media Services-REST-API ist jetzt in Version 2.9 verfügbar. Beginnend mit dieser Version können Sie die Azure CDN-Integration mit Streamingendpunkten aktivieren. Weitere Informationen finden Sie unter [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Version Januar 2015

### Allgemeine Media Services-Updates

Ankündigung der allgemeinen Verfügbarkeit (GA) des Inhaltschutzes mit dynamischer Verschlüsselung. Weitere Informationen finden Sie unter [Azure Media Services enhances streaming security with General Availability of DRM technology](http://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/) (in englischer Sprache).

###Media Services .NET SDK-Updates

Das Azure Media Services .NET SDK ist jetzt in der Version 3.1.0.1 erhältlich.

Durch diese Version wird der Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate-Standardkonstruktor als veraltet eingestuft. Der neue Konstruktor akzeptiert TokenType als Argument.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Version Dezember 2014

###Allgemeine Media Services-Updates

- Einige Updates und neue Funktionen wurden zum Azure Indexer-Medienprozessor hinzugefügt. Weitere Informationen finden Sie unter [Versionshinweise für Azure Media Indexer Version 1.1.6.7](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Eine neue REST-API wurde hinzugefügt, mit der Sie die Codierung reservierter Einheiten aktualisieren können: [EncodingReservedUnitType mit REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS-Unterstützung für wichtigen Übermittlungsdienst wurde hinzugefügt.
- Leistungsverbesserungen für Abfragen von Autorisierungsrichtlinienoptionen wurden vorgenommen.
- Im Datencenter China gilt die [Schlüsselübermittlungs-URL](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) jetzt pro Kunde (genau wie in anderen Datencentern).
- Automatische HLS-Zieldauer hinzugefügt. Bei Livestreaming wird HLS immer dynamisch verpackt. Standardmäßig berechnet Media Services das HLS Segment-Paketerstellungsverhältnis (FragmentsPerSegment) basierend auf dem Keyframe-Intervall (KeyFrameInterval), auch bezeichnet als Gruppe von Bildern – GOP, die vom Live-Encoder empfangen wird. Weitere Informationen finden Sie unter [Working with Azure Media Services Live Streaming] (in englischer Sprache).
 
###Media Services .NET SDK-Updates

- [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) ist jetzt in der Version 3.1.0.0 erhältlich.
- Die Abhängigkeit von .Net-SDK wurde auf das .NET 4.5-Framework aktualisiert.
- Eine neue API wurde hinzugefügt, mit der Sie die Codierung reservierter Einheiten aktualisieren können: Weitere Informationen finden Sie unter [Aktualisieren des reservierten Einheitentyps und Erhöhen der Codierungs-RUs mit .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- JWT-Unterstützung (JSON Web Token) für Token-Authentifizierung hinzugefügt. Weitere Informationen finden Sie unter [JWT-Token-Authentifizierung in Azure Media Services und dynamische Verschlüsselung](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Relative Offsets für "BeginDate" und "ExpirationDate" in der PlayReady-Lizenzvorlage hinzugefügt.


##<a id="november_changes_14"></a>Version November 2014

- Media Services ermöglicht jetzt das Erfassen von Live-Smooth-Streaming (FMP4)-Inhalten über eine SSL-Verbindung. Zur Erfassung über SSL stellen Sie sicher, dass die Erfassungs-URL auf HTTPS aktualisiert wurde. Weitere Informationen zu Live-Streaming finden Sie unter [Arbeiten mit Azure Media Services-Livestreaming].
- Beachten Sie, dass Sie derzeit RTMP-Livestreams nicht über eine SSL-Verbindung erfassen können.
- Sie können auch den Inhalt über eine SSL-Verbindung streamen. Zu diesem Zweck stellen Sie sicher, dass die Streaming-URLs mit HTTPS beginnen.
- Beachten Sie, dass Sie nur über SSL streamen können, wenn der Streaming-Endpunkt, von dem aus Sie Ihre Inhalte übermitteln, nach dem 10. September 2014 erstellt wurde. Wenn die Streaming-URLs auf Streaming-Endpunkten basieren, die nach dem 10. September erstellt wurden, enthält die URL "streaming.mediaservices.windows.net" (neues Format). Streaming-URLs, die "origin.mediaservices.windows.net" (das alte Format) enthalten, unterstützen kein SSL. Wenn die URL im alten Format vorliegt und Sie über SSL streamen möchten, [erstellen Sie einen neuen Streamingendpunkt](media-services-manage-origins.md). Verwenden Sie die URLs, die basierend auf dem neuen Streaming-Endpunkt erstellt wurden, um Ihre Inhalte über SSL zu streamen.
   
##<a id="october_changes_14"></a>Version Oktober 2014

### <a id="new_encoder_release"></a>Media Services-Encoder-Version

Hiermit kündigen wir die neue Version des Media Services-Azure-Medienencoders an. Beim neuesten Azure Media Encoder werden Ihnen nur die ausgegebenen GB berechnet. Ansonsten sind die Funktionen des neuen Encoders mit dem vorherigen Encoder kompatibel. Weitere Informationen finden Sie unter [Mediendienste – Preisdetails]).

### <a id="oct_sdk"></a>Media Services .NET SDK 

Für Media Services SDK-Erweiterungen für .NET ist jetzt Version 2.0.0.3 verfügbar.

Für Media Services SDK für .NET ist jetzt Version 3.0.0.8 verfügbar.

Die folgenden Änderungen wurden vorgenommen:

- Umgestaltung in Wiederholungs-Richtlinienklassen.
- Hinzufügen einer Benutzer-Agent-Zeichenfolge zu Http-Anforderungsheadern.
- Hinzufügen eines Nuget-Build-Wiederherstellungsschritts.
- Beheben von Szenariotests zur Verwendung des x509-Zertifikats aus dem Repository.
- Überprüfen von Einstellungen beim Aktualisieren von Channel und Streaming-Ende.
 

### Neues GitHub-Repository zum Hosten von Media Services-Beispielen

Die Beispiele befinden sich im [GitHub-Repository für Azure Media Services-Beispiele](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Version September 2014

Für Media Services-REST-Metadaten ist jetzt Version 2.7 verfügbar. Weitere Informationen zu den neuesten REST-Updates finden Sie unter [Azure Media Services – REST-API-Referenz].

Für Media Services SDK für .NET ist jetzt Version 3.0.0.7 verfügbar.
 
### <a id="sept_14_breaking_changes"></a>Wichtige Änderungen

* **Origin** wurde in [StreamingEndpoint] umbenannt.
* Eine Änderung des Standardverhaltens bei Verwendung des **Azure-Verwaltungsportals** zum Codieren und Veröffentlichen von MP4-Dateien. 

	Zuvor wurde bei Verwendung des Verwaltungsportals zum Veröffentlichen eines MP4-Videoobjekts mit einer einzelnen Datei eine SAS-URL erstellt (SAS-URLs ermöglichen das Herunterladen des Videos aus einem Blobspeicher). Derzeit verweist die generierte URL bei Verwendung des Verwaltungsportals zum Codieren und Veröffentlichen eines MP4-Videoobjekts mit einer einzelnen Datei auf einen Azure Media Services-Streaming-Endpunkt. Diese Änderung hat keinerlei Einfluss auf MP4-Videos, die direkt in Media Services hochgeladen und ohne Codierung durch Azure Media Services veröffentlicht werden.
	
	Derzeit stehen Ihnen die folgenden beiden Optionen zur Verfügung, um das Problem zu beheben.
	
	* Aktivieren von Streaming-Einheiten und Verwenden der dynamischen Paketerstellung, um das .mp4-Objekt als Smooth-Streaming-Präsentation zu streamen
	
	* Erstellen einer SAS-URL zum Herunterladen (oder schrittweisen Abspielen) der .mp4 Weitere Informationen zum Erstellen eines SAS-Locators finden Sie unter [Bereitstellen von Inhalten].


### <a id="sept_14_GA_changes"></a>Neue Funktionen/Szenarien, die Teil der GA-Version sind

* **Indexer-Medienprozessor**. Weitere Informationen finden Sie unter [Indizieren von Mediendateien mit Azure Media Indexer].

* Mit der [StreamingEndpoint]-Entität können Sie nun benutzerdefinierte Domänennamen (Hostnamen) hinzufügen.

	Damit ein benutzerdefinierter Domänenname als Name des Media Services-Streaming-Endpunkts verwendet werden kann, müssen Sie Ihrem Streaming-Endpunkt benutzerdefinierte Hostnamen hinzufügen. Verwenden Sie die REST-APIs oder das .NET-SDK von Media Services, um benutzerdefinierte Hostnamen hinzuzufügen.
	
	Es gelten die folgenden Bedingungen:
	
	* Sie müssen Eigentümer des benutzerdefinierten Domänennamens sein.
	
	* Sie müssen als Eigentümer des Domänennamens von Azure Media Services validiert werden. Erstellen Sie zum Überprüfen der Domäne einen CName-Datensatz, der <MediaServicesAccountId>.<parent domain> zu verifydns.<mediaservices-dns-zone> zuordnet.
	
	* Sie müssen einen anderen CName-Datensatz erstellen, der den benutzerdefinierten Hostnamen (z. B. sports.contoso.com) dem Hostnamen des Media Services-Streamingendpunkts zuordnet (z. B. amstest.streaming.mediaservices.windows.net).


	Weitere Informationen finden Sie unter der **CustomHostNames**-Eigenschaft unter [StreamingEndpoint] (in englischer Sprache).

### <a id="sept_14_preview_changes"></a>Neue Funktionen/Szenarien, die Teil der öffentlichen Vorschauversion sind

* Live Streaming-Vorschau. Weitere Informationen finden Sie unter [Working with Azure Media Services Live Streaming] (in englischer Sprache).

* Schlüsselübermittlungsdienst. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts].

* Dynamische AES-Verschlüsselung. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts].

* PlayReady-Lizenzübermittlungsdienst. Weitere Informationen finden Sie unter [Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts].

* Dynamische PlayReady-Verschlüsselung. Weitere Informationen finden Sie unter [Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts].

* Media Services PlayReady-Lizenzvorlage. Weitere Informationen finden Sie unter [Media Services PlayReady-Lizenzvorlage – Übersicht].

* Streaming von speicherverschlüsselten Inhalten. Weitere Informationen finden Sie unter [Streaming Storage Encrypted Content] (in englischer Sprache).

##<a id="august_changes_14"></a>Version August 2014

Wenn Sie ein Medienobjekt codieren, wird bei Abschluss des Codierungsauftrags ein Ausgabemedienobjekt erstellt. Bis zu dieser Version erstellte Azure Media Services-Encoder Metadaten zu Ausgabemedienobjekten. Ab dieser Version erstellt der Encoder zudem Metadaten zu Eingabe-Medienobjekten. Weitere Informationen finden Sie unter [Eingeben von Metadaten] und [Ausgeben von Metadaten].


##<a id="july_changes_14"></a>Version Juli 2014

Die folgenden Fehlerkorrekturen wurden für Azure Media Services Packager und Encryptor vorgenommen:

* Beim Transcodieren/Multiplexieren eines Live-Archiv-Objekts in das HTTP Live Streaming-Format wird nur Audio abgespielt – dieses Problem wurde behoben und nun werden Audio und Video abgespielt.

* Beim Packen eines Objekts in HTTP Live Streaming und 128-Bit-AES-Umschlagverschlüsselung werden die gepackten Streams nicht auf Android-Geräten wiedergegeben – dieses Problem wurde behoben und der gepackte Stream wird auf Android-Geräten wiedergegeben, die HTTP Live Streaming unterstützen.

##<a id="may_changes_14"></a>Version Mai 2014

### <a id="may_14_changes"></a>Allgemeine Media Services-Updates

Sie können nun die [Dynamische Paketerstellung] zum Streamen von HTTP Live Streaming (HLS) v3 verwenden. Fügen Sie zum Streamen von HLS v3 das folgende Format zum ursprünglichen Locator-Pfad hinzu: *.ism/manifest(format=m3u8-aapl-v3). Weitere Informationen finden Sie in [Nick Drouins Blog].

Die dynamische Paketerstellung unterstützt jetzt auch die Bereitstellung von mit PlayReady verschlüsseltem HLS (v3 und v4) basierend auf statisch mit PlayReady verschlüsseltem Smooth Streaming. Informationen zum Verschlüsseln von Smooth Streaming mit PlayReady finden Sie unter [Schützen von Smooth Streaming und MPEG DASH mit PlayReady].

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK-Updates

Das Media Services SDK für .NET Version 3.0.0.5 umfasst die folgenden Verbesserungen:

* Höhere Geschwindigkeit und bessere Belastbarkeit beim Hochladen/Herunterladen von Medienobjekten.

* Verbesserungen bei Wiederholungslogik und vorübergehender Ausnahmebehandlung:

	* Vorübergehende Fehlerermittlung und Wiederholungslogik wurden für Ausnahmen verbessert, die durch Abfragen, Speichern von Änderungen, Hochladen oder Herunterladen von Dateien verursacht werden. 
	
	* Bei der Ausgabe von Webausnahmen (z. B. während der Abfrage eines ACS-Token) schlagen schwerwiegende Fehler nun schneller fehl.

Weitere Informationen finden Sie unter [Wiederholungslogik im Media Services SDK für .NET].

##<a id="april_changes_14"></a>Encoder-Version April 2014

### <a name="april_14_enocer_changes"></a>Media Services Encoder-Updates

* Unterstützung für die Erfassung von mit dem nichtlinearen Grass Valley EDIUS-Editor erstellten AVI-Dateien, wobei das Video leicht mit dem Grass Valley HQ-/HQX-Codec komprimiert ist. Weitere Informationen finden Sie unter [Grass Valley Announces EDIUS 7 Streaming Through the Cloud] (in englischer Sprache).

* Unterstützung für die Festlegung der Namenskonvention für die vom Media Encoder erstellten Dateien. Weitere Informationen finden Sie unter [Steuern von Media Services Encoder-Ausgabedateinamen].

* Unterstützung für Video- und/oder Audioüberlagerungen. Weitere Informationen finden Sie unter [Erstellen von Überlagerungen].

* Unterstützung für das Zusammenfügen mehrerer Videosegmente. Weitere Informationen finden Sie unter [Zusammenfügen von Videosegmenten].

* Behebung eines Problems hinsichtlich des Transcodierens von MP4s, bei denen das Audio mit MPEG-1 Audio Layer 3 (alias MP3) codiert wurde.


##<a id="jan_feb_changes_14"></a>Versionen Januar\Februar 2014

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 und 3.0.0.3

Folgende Änderungen wurden in 3.0.0.1 und 3.0.0.2 vorgenommen:

* Behebung von Problemen hinsichtlich der Verwendung von LINQ-Abfragen mit OrderBy-Anweisungen.

* Aufteilen von Testlösungen in [GitHub] in einheitenbasierte Tests und szenarienbasierte Tests.

Weitere Informationen zu den Änderungen finden Sie unter: [Azure Media Services .NET SDK-Versionen 3.0.0.1 und 3.0.0.2].

Die folgenden Änderungen wurden in 3.0.0.3 vorgenommen:

* Upgrade der Azure-Speicherabhängigkeiten für die Verwendung von Version 3.0.3.0. 

* Behebung des Problems mit der Abwärtskompatibilität für Versionen 3.0.*.*.


##<a id="december_changes_13"></a>Version Dezember 2013

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

>[AZURE.NOTE]Versionen 3.0.x.x sind nicht abwärtskompatibel zu Versionen 2.4.x.x.

Die neueste Version des Media Services SDK ist 3.0.0.0. Sie können das neueste Paket aus Nuget herunterladen oder die Bits unter [GitHub] abrufen.

Ab Media Services SDK Version 3.0.0.0 können Sie die Token des [Zugriffssteuerungsdiensts für Azure Active Directory] wiederverwenden. Weitere Informationen finden Sie im Abschnitt "Wiederverwenden von Token des Zugriffssteuerungsdiensts" unter [Herstellen einer Verbindung mit Media Services mit dem Media Services SDK].

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK-Erweiterungen 2.0.0.0

Azure Media Services SDK-Erweiterungen für .NET ist ein Satz von Erweiterungsmethoden und Hilfsfunktionen, die Ihren Code vereinfachen und eine einfachere Entwicklung mit Azure Media Services ermöglichen. Sie können die neuesten Bits unter [Azure Media Services .NET SDK Extensions] (in englischer Sprache) abrufen.

##<a id="november_changes_13"></a>Version November 2013

### <a name="nov_13_donnet_changes"></a>Änderungen im Azure Media Services .NET SDK

Ab dieser Version behandelt Media Services SDK für .NET vorübergehende Fehler, die bei Aufrufen der Media Services-REST-API-Ebene auftreten können.

##<a id="august_changes_13"></a>Version August 2013

### <a name="aug_13_powershell_changes"></a>In Azure-SDK-Tools enthaltene Media Services-PowerShell-Cmdlets

Die folgenden Media Services-PowerShell-Cmdlets sind jetzt in [azure-sdk-tools] (in englischer Sprache) enthalten.

* Get-AzureMediaServices 

	Beispiel: `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount

	Beispiel: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* New-AzureMediaServicesKey

	Beispiel: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount

	Beispiel: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Version Juni 2013

### <a name="june_13_general_changes"></a>Änderungen an Azure Media Services

Die in diesem Abschnitt aufgeführten Änderungen sind Aktualisierungen der Media Services-Versionen für Juni 2013.

* Möglichkeit, mehrere Speicherkonten mit einem Media Service-Konto zu verknüpfen. 

	StorageAccount
	
	Asset.StorageAccountName und Asset.StorageAccount

* Möglichkeit zum Aktualisieren von Job.Priority.

* Benachrichtigungsbezogene Entitäten und Eigenschaften:

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Job

* Asset.Uri

* Locator.Name

### <a name="june_13_dotnet_changes"></a>Änderungen im Azure Media Services .NET SDK

Die folgenden Änderungen sind in Media Services SDK-Versionen von Juni 2013 enthalten. Das neueste Media Services SDK ist auf GitHub verfügbar.

* Ab Version 2.3.0.0 unterstützt das Media Services SDK die Verknüpfung mehrerer Speicherkonten mit einem Media Services-Konto. Die folgenden APIs unterstützen diese Funktion:
	
	Der IStorageAccount-Typ.
	
	Die Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts-Eigenschaft.
	
	Die StorageAccount-Eigenschaft.
	
	Die StorageAccountName-Eigenschaft.
	
	Weitere Informationen finden Sie unter [Verwalten von Media Services-Medienobjekte über mehrere Speicherkonten].

* Benachrichtigungsbezogene APIs. Ab Version 2.2.0.0 können Sie sich Azure-Warteschlangenspeicher-Benachrichtigungen anhören. Weitere Informationen finden Sie unter [Verarbeiten von Media Services-Auftragsbenachrichtigungen].
	
	Die Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions-Eigenschaft.
	
	Der Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint-Typ.
	
	Der Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription-Typ.
	
	Der Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection-Typ.
	
	Der Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType-Typ.
	
	Der Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState-Typ.

* Abhängigkeit vom Azure-Speicherclient-SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Abhängigkeit von OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Version Dezember 2012

### <a name="dec_12_dotnet_changes"></a>Änderungen im Azure Media Services .NET SDK

* Intellisense: Aufnahme fehlender Intellisense-Dokumentation für viele Typen.

* Microsoft.Practices.TransientFaultHandling.Core: Behebung eines Problems, bei dem das SDK immer noch eine Abhängigkeit zu einer alten Version dieser Assembly aufwies. Das SDK verweist jetzt auf Version 5.1.1209.1 dieser Assembly.

Problembehebung im SDK von November 2012:

* IAsset.Locators.Count: Diese Anzahl wird nun korrekt auf neuen IAsset-Oberflächen angegeben, nachdem alle Locators gelöscht wurden.

* IAssetFile.ContentFileSize: Dieser Wert ist nun nach dem Hochladen durch IAssetFile.Upload(filepath) korrekt eingestellt.

* IAssetFile.ContentFileSize: Diese Eigenschaft kann nun bei der Erstellung einer Objektdatei festgelegt werden. Sie war zuvor schreibgeschützt.

* IAssetFile.Upload(filepath): Behebung eines Fehlers, bei dem diese synchrone Uploadmethode beim Hochladen mehrerer Dateien in das Objekt den folgenden Fehler ausgab. Der Fehler lautete "Die Anforderung konnte nicht authentifiziert werden. Stellen Sie sicher, dass der Wert des Autorisierungsheaders, einschließlich der Signatur, korrekt ist."

* IAssetFile.UploadAsync: Behebung eines Fehlers, bei dem höchstens fünf Dateien gleichzeitig hochgeladen werden konnten.

* IAssetFile.UploadProgressChanged: Dieses Ereignis wird nun vom SDK bereitgestellt.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Diese Methodenüberladung wird nun bereitgestellt.

* IAssetFile.DownloadAsync: Behebung eines Fehlers, bei dem höchstens fünf Dateien gleichzeitig heruntergeladen werden konnten.

* IAssetFile.Delete(): Behebung eines Fehlers, bei dem das Aufrufen der Löschfunktion eine Ausnahme auslösen konnte, wenn keine Datei für IAssetFile hochgeladen wurde.

* Aufträge: Behebung eines Fehlers, bei dem durch die Verkettung einer Aufgabe zum Konvertieren von MP4 für Smooth Streams und einer PlayReady-Schutzaufgabe unter Verwendung einer Jobvorlage keine Aufgabe erstellt wurde.

* EncryptionUtils.GetCertificateFromStore(): Diese Methode gibt keine Null-Referenz-Ausnahme mehr zurück, weil bei der Suche nach dem Zertifikat aufgrund von Problemen mit der Zertifikatkonfiguration Fehler auftreten.

##<a id="november_changes_12"></a>Version November 2012

Die in diesem Abschnitt aufgeführten Änderungen sind Updates, die im SDK von November 2012 (Version 2.0.0.0) enthalten waren. Aufgrund dieser Änderungen muss möglicherweise Code, der für die SDK-Vorschauversion von Juni 2012 geschrieben wurde, geändert oder neu geschrieben werden.

* Objekte
	
	IAsset.Create(assetName) ist die EINZIGE Funktion für die Objekterstellung. IAsset.Create lädt im Rahmen des Methodenaufrufs keine Dateien mehr hoch. Verwenden Sie IAssetFile zum Hochladen.
	
	Die IAsset.Publish-Methode und der AssetState.Publish-Aufzählungswert wurden aus dem Services SDK entfernt. Von diesem Wert abhängiger Code muss neu geschrieben werden.

* FileInfo

	Diese Klasse wurde entfernt und durch IAssetFile ersetzt.

	IAssetFiles

	IAssetFile ersetzt FileInfo und weist ein anderes Verhalten auf. Um es verwenden zu können, instanziieren Sie das IAssetFiles-Objekt und laden Sie dann eine Datei über das Media Services SDK oder das Azure-Speicher-SDK hoch. Die folgenden IAssetFile.Upload-Überladungen können verwendet werden:

	* IAssetFile.Upload(filePath): Eine synchrone Methode, die den Thread sperrt und nur beim Hochladen einer einzelnen Datei empfohlen wird.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Eine asynchrone Methode. Das ist der bevorzugte Uploadmechanismus.

	Bekannter Fehler: Durch Verwendung des cancellationToken wird der Upload zwar abgebrochen, für den Abbruchstatus der Aufgaben gibt es jedoch unterschiedliche Möglichkeiten. Sie müssen Ausnahmen korrekt erfassen und behandeln.

* Locators
	
	Die ursprungsspezifischen Versionen wurden entfernt. Der SAS-spezifische context.Locators.CreateSasLocator (asset, accessPolicy) wird als veraltet markiert oder durch GA entfernt. Informationen zum aktualisierten Verhalten finden Sie im Abschnitt "Locator" unter "Neue Funktionalität".


##<a id="june_changes_12"></a>Vorschauversion Juni 2012

Die folgende Funktion war neu in der November-Version des SDK.

* Löschen von Entitäten

	Objekte IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey werden nun auf Objektebene gelöscht (d. h. IObject.Delete()), anstatt in der Sammlung gelöscht werden zu müssen (d. h. cloudMediaContext.ObjCollection.Delete(objInstance)).

* Locators

	Locators müssen nun anhand der CreateLocator-Methode und unter Verwendung der LocatorType.SAS- oder LocatorType.OnDemandOrigin-Aufzählungswerte als Argument für den zu erstellenden, spezifischen Locator-Typ erstellt werden.

	Den Locators wurden neue Eigenschaften hinzugefügt, mit denen einfacher nutzbare URIs für Ihre Inhalte abgerufen werden können. Dieses neue Design der Locators sollte zu mehr Flexibilität für eine zukünftige Erweiterbarkeit durch Dritte sowie zu einer höheren Benutzerfreundlichkeit für Medienclientanwendungen führen.

* Unterstützung der asynchronen Methode

	Für alle Methoden wurde asynchrone Unterstützung hinzugefügt.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[MSDN-Forum für Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services – REST-API-Referenz]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[Mediendienste – Preisdetails]: http://azure.microsoft.com/pricing/details/media-services/
[Eingeben von Metadaten]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Ausgeben von Metadaten]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Bereitstellen von Inhalten]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indizieren von Mediendateien mit Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Working with Azure Media Services Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Arbeiten mit Azure Media Services-Livestreaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady-Lizenzvorlage – Übersicht]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming Storage Encrypted Content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Management Portal]: https://manage.windowsazure.com
[Dynamische Paketerstellung]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouins Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Schützen von Smooth Streaming und MPEG DASH mit PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Wiederholungslogik im Media Services SDK für .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Steuern von Media Services Encoder-Ausgabedateinamen]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Erstellen von Überlagerungen]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Zusammenfügen von Videosegmenten]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK-Versionen 3.0.0.1 und 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Zugriffssteuerungsdiensts für Azure Active Directory]: http://msdn.microsoft.com/library/hh147631.aspx
[Herstellen einer Verbindung mit Media Services mit dem Media Services SDK]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Verwalten von Media Services-Medienobjekte über mehrere Speicherkonten]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Verarbeiten von Media Services-Auftragsbenachrichtigungen]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 

<!---HONumber=July15_HO1-->