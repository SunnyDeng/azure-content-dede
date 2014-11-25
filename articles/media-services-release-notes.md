<properties pageTitle="Versionshinweise zu Media Services" metaKeywords="Azure Media Services" description="Versionshinweise zu Media Services" metaCanonical="" services="media-services" documentationCenter="Media" title="Versionshinweise zu Media Services" authors="juliako" solutions="media" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="media" ms.devlang="dotnet" ms.topic="article" ms.date="10/15/2014" ms.author="juliako" />

# Versionshinweise zu Azure Media Services

In diesen Versionshinweisen werden Änderungen im Vergleich zu früheren Versionen und bekannte Probleme zusammengefasst.

> [AZURE.NOTE] Wir wünschen uns Feedback von unseren Kunden und möchten Probleme beheben, die negative Auswirkungen auf Ihre Arbeit haben. Wenn Sie ein Problem melden oder Fragen stellen möchten, verfassen Sie bitte einen Beitrag im [MSDN-Forum für Azure Media Services][MSDN-Forum für Azure Media Services].

-   [Aktuell bekannte Probleme][Aktuell bekannte Probleme]
-   [REST-API-Versionsverlauf][REST-API-Versionsverlauf]
-   [Version Oktober 2014][Version Oktober 2014]
-   [Version September 2014][Version September 2014]
-   [Version August 2014][Version August 2014]
-   [Version Juli 2014][Version Juli 2014]
-   [Version Mai 2014][Version Mai 2014]
-   [Version April 2014][Version April 2014]
-   [Versionen Januar\Februar 2014][Versionen Januar\Februar 2014]
-   [Version Dezember 2013][Version Dezember 2013]
-   [Version November 2013][Version November 2013]
-   [Version August 2013][Version August 2013]
-   [Version Juni 2013][Version Juni 2013]
-   [Version Dezember 2012][Version Dezember 2012]
-   [Version November 2012][Version November 2012]
-   [Vorschauversion Juni 2012][Vorschauversion Juni 2012]

## <span id="issues"></span></a>Aktuell bekannte Probleme

### <span id="general_issues"></span></a>Allgemeine Media Services-Probleme

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Problem</th>
<th align="left">Beschreibung</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Mehrere gängige HTTP-Header werden in der REST-API nicht bereitgestellt.</td>
<td align="left">Wenn Sie Media Services-Anwendungen mithilfe der REST-API entwickeln, wird Ihnen auffallen, dass einige gängige HTTP-Headerfelder (einschließlich CLIENT-REQUEST-ID, REQUEST-ID und RETURN-CLIENT-REQUEST-ID) nicht unterstützt werden. Diese Header werden in einem späteren Update hinzugefügt.</td>
</tr>
<tr class="even">
<td align="left">Beim Codieren eines Medienobjekts mit einem Dateinamen, der Escapezeichen (z. B. %20) enthält, tritt der Fehler „MediaProcessor: Datei nicht gefunden“ auf.</td>
<td align="left">Die Namen von Dateien, die einem Medienobjekt hinzugefügt und dann codiert werden sollen, sollten nur alphanumerische Zeichen und Leerzeichen enthalten. Dieses Problem wird in einem späteren Update behoben.</td>
</tr>
<tr class="odd">
<td align="left">Die ListBlobs-Methode, die Teil des Azure Storage-SDK Version 3.x ist, schlägt fehl.</td>
<td align="left">Media Services generiert SAS-URLs auf Grundlage der Version <a href="http://msdn.microsoft.com/de-de/library/azure/dn592123.aspx">12.02.2012</a>. Falls Sie das Azure Storage-SDK zur Auflistung von Blobs in einem Blob-Container nutzen möchten, verwenden Sie die<a href="http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a>-Methode, die Teil des Azure Storage-SDK Version 2.x ist. Die ListBlobs-Methode, die Teil des Azure Storage-SDK Version 3.x ist, wird fehlschlagen.</td>
</tr>
<tr class="even">
<td align="left">Der Drosselungsmechanismus von Media Services schränkt die Ressourcenverwendung für Anwendungen ein, die zu viele Anforderungen an den Dienst ausgeben. Der Dienst kann den HTTP-Statuscode „Dienst nicht verfügbar (503)“ zurückgeben.</td>
<td align="left">Weitere Informationen finden Sie in der Beschreibung des HTTP-Statuscodes 503 unter <a href="http://msdn.microsoft.com/de-de/library/azure/dn168949.aspx">Azure Media Services-Fehlercodes</a>.</td>
</tr>
</tbody>
</table>

### <span id="dotnet_issues"></span></a>Probleme in Media Services SDK für .NET

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Problem</th>
<th align="left">Beschreibung</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Media Services-Medienobjekte im SDK können nicht serialisiert werden und funktionieren daher nicht mit Azure Caching.</td>
<td align="left">Wenn Sie versuchen, das AssetCollection-Medienobjekt des SDKs zu serialisieren, um es Azure Caching hinzuzufügen, wird eine Ausnahme ausgelöst.</td>
</tr>
</tbody>
</table>

## <span id="rest_version_history"></span></a>REST-API-Versionsverlauf

Informationen zum Versionsverlauf der Media Services-REST-API finden Sie unter [Azure Media Services – REST-API-Referenz][Azure Media Services – REST-API-Referenz].

## <span id="october_changes_14"></span></a>Version Oktober 2014

### <span id="new_encoder_release"></span></a>Media Services-Encoder-Version

Hiermit kündigen wir die neue Version des Media Services-Azure-Medienencoders an. Beim neuesten Azure-Medienencoder werden Ihnen nur die ausgegebenen GB berechnet. Andererseits ist der neue Encoder mit dem Microsoft Azure-Medienencoder kompatibel. Weitere Informationen finden Sie unter [Mediendienste – Preisdetails][Mediendienste – Preisdetails]).

### <span id="oct_sdk"></span></a>Media Services SDK für .NET

Für Media Services SDK für .NET ist jetzt Version 3.0.0.8 verfügbar.

Für Media Services SDK-Erweiterungen für .NET ist jetzt Version 2.0.0.3 verfügbar.

## <span id="september_changes_14"></span></a>Version September 2014

Für Media Services-REST-Metadaten ist jetzt Version 2.7 verfügbar. Weitere Informationen zu den neuesten REST-Updates finden Sie unter [Azure Media Services – REST-API-Referenz][Azure Media Services – REST-API-Referenz].

Für Media Services SDK für .NET ist jetzt Version 3.0.0.7 verfügbar.

### <span id="sept_14_breaking_changes"></span></a>Wichtige Änderungen

-   **Origin** wurde in [StreamingEndpoint][StreamingEndpoint] umbenannt.
-   Eine Änderung des Standardverhaltens bei Verwendung des **Azure-Verwaltungsportals** zum Codieren und Veröffentlichen von MP4-Dateien.

    Zuvor wurde bei Verwendung des Verwaltungsportals zum Veröffentlichen eines MP4-Videoobjekts mit einer einzelnen Datei eine SAS-URL erstellt (SAS-URLs ermöglichen das Herunterladen des Videos aus einem Blob-Speicher). Derzeit verweist die generierte URL bei Verwendung des Verwaltungsportals zum Codieren und Veröffentlichen eines MP4-Videoobjekts mit einer einzelnen Datei auf einen Azure Media Services-Streaming-Endpunkt. Diese Änderung hat keinerlei Einfluss auf MP4-Videos, die direkt in Media Services hochgeladen und ohne Codierung durch Azure Media Services veröffentlicht werden.

    Derzeit stehen Ihnen die folgenden beiden Optionen zur Verfügung, um das Problem zu beheben.

    -   Aktivieren von Streaming-Einheiten und Verwenden der dynamischen Paketerstellung, um das .mp4-Objekt als Smooth-Streaming-Präsentation zu streamen

    -   Erstellen einer SAS-URL zum Herunterladen (oder schrittweisen Abspielen) der .mp4 Weitere Informationen zum Erstellen eines SAS-Locators finden Sie unter [Bereitstellen von Inhalten][Bereitstellen von Inhalten].

### <span id="sept_14_GA_changes"></span></a>Neue Funktionen/Szenarien, die Teil der GA-Version sind

-   **Indexer-Medienprozessor**. Weitere Informationen finden Sie unter [Indizieren von Mediendateien mit Azure Media Indexer][Indizieren von Mediendateien mit Azure Media Indexer].

-   Mit der [StreamingEndpoint][StreamingEndpoint]-Entität können Sie nun benutzerdefinierte Domänennamen (Hostnamen) hinzufügen.

    Damit ein benutzerdefinierter Domänenname als Name des Media Services-Streaming-Endpunkts verwendet werden kann, müssen Sie Ihrem Streaming-Endpunkt benutzerdefinierte Hostnamen hinzufügen. Verwenden Sie die REST-APIs oder das .NET-SDK von Media Services, um benutzerdefinierte Hostnamen hinzuzufügen.

    Es gelten die folgenden Bedingungen:

    -   Sie müssen Eigentümer des benutzerdefinierten Domänennamens sein.

    -   Sie müssen als Eigentümer des Domänennamens von Azure Media Services validiert werden.

    Weitere Informationen finden Sie unter der **CustomHostNames**-Eigenschaft unter [StreamingEndpoint][StreamingEndpoint] (in englischer Sprache).

### <span id="sept_14_preview_changes"></span></a>Neue Funktionen/Szenarien, die Teil der öffentlichen Vorschauversion sind

-   Live Streaming-Vorschau. Weitere Informationen finden Sie unter [Working with Azure Media Services Live Streaming][Working with Azure Media Services Live Streaming] (in englischer Sprache).

-   Schlüsselübermittlungsdienst. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts][Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts].

-   Dynamische AES-Verschlüsselung. Weitere Informationen finden Sie unter [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts][Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts].

-   PlayReady-Lizenzübermittlungsdienst. Weitere Informationen finden Sie unter [Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts][Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts].

-   Dynamische PlayReady-Verschlüsselung. Weitere Informationen finden Sie unter [Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts][Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts].

-   Media Services PlayReady-Lizenzvorlage. Weitere Informationen finden Sie unter [Media Services PlayReady-Lizenzvorlage – Übersicht][Media Services PlayReady-Lizenzvorlage – Übersicht].

-   Streaming von speicherverschlüsselten Inhalten. Weitere Informationen finden Sie unter [Streaming Storage Encrypted Content][Streaming Storage Encrypted Content] (in englischer Sprache).

## <span id="august_changes_14"></span></a>Version August 2014

Wenn Sie ein Medienobjekt codieren, wird bei Abschluss des Codierungsauftrags ein Ausgabemedienobjekt erstellt. Bis zu dieser Version erstellte Azure Media Services-Encoder Metadaten zu Ausgabemedienobjekten. Ab dieser Version erstellt der Encoder zudem Metadaten zu Eingabe-Medienobjekten. Weitere Informationen finden Sie unter [Eingeben von Metadaten][Eingeben von Metadaten] und [Ausgeben von Metadaten][Ausgeben von Metadaten].

## <span id="july_changes_14"></span></a>Version Juli 2014

Die folgenden Fehlerkorrekturen wurden für Azure Media Services Packager und Encryptor vorgenommen:

-   Beim Transcodieren/Multiplexieren eines Live-Archiv-Objekts in das HTTP Live Streaming-Format wird nur Audio abgespielt – dieses Problem wurde behoben und nun werden Audio und Video abgespielt.

-   Beim Packen eines Objekts in HTTP Live Streaming und 128-Bit-AES-Umschlagverschlüsselung werden die gepackten Streams nicht auf Android-Geräten wiedergegeben – dieses Problem wurde behoben und der gepackte Stream wird auf Android-Geräten wiedergegeben, die HTTP Live Streaming unterstützen.

## <span id="may_changes_14"></span></a>Version Mai 2014

### <span id="may_14_changes"></span></a>Allgemeine Media Services-Updates

Sie können nun die [Dynamische Paketerstellung][Dynamische Paketerstellung] zum Streamen von HTTP Live Streaming (HLS) v3 verwenden. Fügen Sie zum Streamen von HLS v3 das folgende Format zum ursprünglichen Locator-Pfad hinzu: \*.ism/manifest(format=m3u8-aapl-v3). Weitere Informationen finden Sie unter [Nick Drouin's Blog][Nick Drouin's Blog] (in englischer Sprache).

Die dynamische Paketerstellung unterstützt jetzt auch die Bereitstellung von mit PlayReady verschlüsseltem HLS (v3 und v4) basierend auf statisch mit PlayReady verschlüsseltem Smooth Streaming. Informationen zum Verschlüsseln von Smooth Streaming mit PlayReady finden Sie unter [Schützen von Smooth Streaming und MPEG DASH mit PlayReady][Schützen von Smooth Streaming und MPEG DASH mit PlayReady].

### <a name="may_14_donnet_changes"></a>Updates für Media Services SDK für .NET

Das Media Services SDK für .NET Version 3.0.0.5 umfasst die folgenden Verbesserungen:

-   Höhere Geschwindigkeit und bessere Belastbarkeit beim Hochladen/Herunterladen von Medienobjekten.

-   Verbesserungen bei Wiederholungslogik und vorübergehender Ausnahmebehandlung:

    -   Vorübergehende Fehlerermittlung und Wiederholungslogik wurden für Ausnahmen verbessert, die durch Abfragen, Speichern von Änderungen, Hochladen oder Herunterladen von Dateien verursacht werden.

    -   Bei der Ausgabe von Webausnahmen (z. B. während der Abfrage eines ACS-Token) schlagen schwerwiegende Fehler nun schneller fehl.

Weitere Informationen finden Sie unter [Wiederholungslogik im Media Services SDK für .NET][Wiederholungslogik im Media Services SDK für .NET].

## <span id="april_changes_14"></span></a>Encoder-Version April 2014

### <a name="april_14_enocer_changes"></a>Media Services-Encoder-Updates

-   Unterstützung für die Erfassung von mit dem nichtlinearen Grass Valley EDIUS-Editor erstellten AVI-Dateien, wobei das Video leicht mit dem Grass Valley HQ-/HQX-Codec komprimiert ist. Weitere Informationen finden Sie unter [Grass Valley Announces EDIUS 7 Streaming Through the Cloud][Grass Valley Announces EDIUS 7 Streaming Through the Cloud] (in englischer Sprache).

-   Unterstützung für die Festlegung der Namenskonvention für die vom Media Encoder erstellten Dateien. Weitere Informationen finden Sie unter [Steuern von Media Services Encoder-Ausgabedateinamen][Steuern von Media Services Encoder-Ausgabedateinamen].

-   Unterstützung für Video- und/oder Audioüberlagerungen. Weitere Informationen finden Sie unter [Erstellen von Überlagerungen][Erstellen von Überlagerungen].

-   Unterstützung für das Zusammenfügen mehrerer Videosegmente. Weitere Informationen finden Sie unter [Zusammenfügen von Videosegmenten][Zusammenfügen von Videosegmenten].

-   Behebung eines Problems hinsichtlich des Transcodierens von MP4s, bei denen das Audio mit MPEG-1 Audio Layer 3 (alias MP3) codiert wurde.

## <span id="jan_feb_changes_14"></span></a>Versionen Januar\\Februar 2014

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services SDK für .NET 3.0.0.1, 3.0.0.2 und 3.0.0.3

Folgende Änderungen wurden in 3.0.0.1 und 3.0.0.2 vorgenommen:

-   Behebung von Problemen hinsichtlich der Verwendung von LINQ-Abfragen mit OrderBy-Anweisungen.

-   Aufteilen von Testlösungen in [Github][Github] in einheitenbasierte Tests und szenarienbasierte Tests.

Genauere Informationen zu den Änderungen finden Sie unter [Windows Azure Media Services .NET SDK 3.0.0.2 release][Windows Azure Media Services .NET SDK 3.0.0.2 release] (in englischer Sprache).

Die folgenden Änderungen wurden in 3.0.0.3 vorgenommen:

-   Upgrade der Azure-Speicherabhängigkeiten für die Verwendung von Version 3.0.3.0.

-   Behebung des Problems mit der Abwärtskompatibilität für Versionen 3.0.*.*.

## <span id="december_changes_13"></span></a>Version Dezember 2013

### <a name="dec_13_donnet_changes"></a>Azure Media Services SDK für .NET 3.0.0.0

> [AZURE.NOTE] Versionen 3.0.x.x sind nicht abwärtskompatibel zu Versionen 2.4.x.x.

Die neueste Version des Media Services SDK ist 3.0.0.0. Sie können das neueste Paket aus Nuget herunterladen oder die Bits unter [Github][Github] abrufen.

Ab Media Services SDK Version 3.0.0.0 können Sie die Token des [Zugriffssteuerungsdiensts für Azure Active Directory][Zugriffssteuerungsdiensts für Azure Active Directory] wiederverwenden. Weitere Informationen finden Sie im Abschnitt „Wiederverwenden von Token des Zugriffssteuerungsdiensts“ unter [Herstellen einer Verbindung mit Media Services mit dem Media Services SDK][Herstellen einer Verbindung mit Media Services mit dem Media Services SDK].

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services SDK-Erweiterungen für .NET 2.0.0.0

Azure Media Services SDK-Erweiterungen für .NET ist ein Satz von Erweiterungsmethoden und Hilfsfunktionen, die Ihren Code vereinfachen und eine einfachere Entwicklung mit Azure Media Services ermöglichen. Sie können die neuesten Bits unter [Azure Media Services .NET SDK Extensions][Azure Media Services .NET SDK Extensions] (in englischer Sprache) abrufen.

## <span id="november_changes_13"></span></a>Version November 2013

### <a name="nov_13_donnet_changes"></a>Änderungen in Azure Media Services SDK für .NET

Ab dieser Version behandelt Media Services SDK für .NET vorübergehende Fehler, die bei Aufrufen der Media Services-REST-API-Ebene auftreten können.

## <span id="august_changes_13"></span></a>Version August 2013

### <a name="aug_13_powershell_changes"></a>In Azure-SDK-Tools enthaltene Media Services-PowerShell-Cmdlets

Die folgenden Media Services-PowerShell-Cmdlets sind jetzt in [azure-sdk-tools][azure-sdk-tools] (in englischer Sprache) enthalten.

-   Get-AzureMediaServices

    Beispiel: `Get-AzureMediaServicesAccount`.

-   New-AzureMediaServicesAccount

    Beispiel: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

-   New-AzureMediaServicesKey

    Beispiel: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

-   Remove-AzureMediaServicesAccount

    Beispiel: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <span id="june_changes_13"></span></a>Version Juni 2013

### <a name="june_13_general_changes"></a>Änderungen an Azure Media Services

Die in diesem Abschnitt aufgeführten Änderungen sind Aktualisierungen der Media Services-Versionen für Juni 2013.

-   Möglichkeit, mehrere Speicherkonten mit einem Media Service-Konto zu verknüpfen.

    StorageAccount

    Asset.StorageAccountName und Asset.StorageAccount

-   Möglichkeit zum Aktualisieren von Job.Priority.

-   Benachrichtigungsbezogene Entitäten und Eigenschaften:

    JobNotificationSubscription

    NotificationEndPoint

    Job

-   Asset.Uri

-   Locator.Name

### <a name="june_13_dotnet_changes"></a>Änderungen in Azure Media Services SDK für .NET

Die folgenden Änderungen sind in Media Services SDK-Versionen von Juni 2013 enthalten. Das neueste Media Services SDK ist auf GitHub verfügbar.

-   Ab Version 2.3.0.0 unterstützt das Media Services SDK die Verknüpfung mehrerer Speicherkonten mit einem Media Services-Konto. Die folgenden APIs unterstützen diese Funktion:

    Der IStorageAccount-Typ.

    Die Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts-Eigenschaft.

    Die StorageAccount-Eigenschaft.

    Die StorageAccountName-Eigenschaft.

    Weitere Informationen finden Sie unter [Verwalten von Media Services-Medienobjekte über mehrere Speicherkonten][Verwalten von Media Services-Medienobjekte über mehrere Speicherkonten].

-   Benachrichtigungsbezogene APIs. Ab Version 2.2.0.0 können Sie sich Azure-Warteschlangenspeicher-Benachrichtigungen anhören. Weitere Informationen finden Sie unter [Verarbeiten von Media Services-Auftragsbenachrichtigungen][Verarbeiten von Media Services-Auftragsbenachrichtigungen].

    Die Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions-Eigenschaft.

    Der Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint-Typ.

    Der Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription-Typ.

    Der Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection-Typ.

    Der Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType-Typ.

    Der Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState-Typ.

-   Abhängigkeit vom Azure-Speicherclient-SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

-   Abhängigkeit von OData 5.5 (Microsoft.Data.OData.dll).

## <span id="december_changes_12"></span></a>Version Dezember 2012

### <a name="dec_12_dotnet_changes"></a>Änderungen in Azure Media Services SDK für .NET

-   Intellisense: Aufnahme fehlender Intellisense-Dokumentation für viele Typen.

-   Microsoft.Practices.TransientFaultHandling.Core: Behebung eines Problems, bei dem das SDK immer noch eine Abhängigkeit zu einer alten Version dieser Assembly aufwies. Das SDK verweist jetzt auf Version 5.1.1209.1 dieser Assembly.

Problembehebung im SDK von November 2012:

-   IAsset.Locators.Count: Diese Anzahl wird nun korrekt auf neuen IAsset-Oberflächen angegeben, nachdem alle Locators gelöscht wurden.

-   IAssetFile.ContentFileSize: Dieser Wert ist nun nach dem Hochladen durch IAssetFile.Upload(filepath) korrekt eingestellt.

-   IAssetFile.ContentFileSize: Diese Eigenschaft kann nun bei der Erstellung einer Objektdatei festgelegt werden. Sie war zuvor schreibgeschützt.

-   IAssetFile.Upload(filepath): Behebung eines Fehlers, bei dem diese synchrone Uploadmethode beim Hochladen mehrerer Dateien in das Objekt den folgenden Fehler ausgab. Der Fehler lautete „Die Anforderung konnte nicht authentifiziert werden. Stellen Sie sicher, dass der Wert des Autorisierungsheaders, einschließlich der Signatur, korrekt ist.“

-   IAssetFile.UploadAsync: Behebung eines Fehlers, bei dem höchstens 5 Dateien gleichzeitig hochgeladen werden konnten.

-   IAssetFile.UploadProgressChanged: Dieses Ereignis wird nun vom SDK bereitgestellt.

-   IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Diese Methodenüberladung wird jetzt bereitgestellt.

-   IAssetFile.DownloadAsync: Behebung eines Fehlers, bei dem höchstens 5 Dateien gleichzeitig heruntergeladen werden konnten.

-   IAssetFile.Delete(): Behebung eines Fehlers, bei dem das Aufrufen der Löschfunktion eine Ausnahme auslösen konnte, wenn keine Datei für IAssetFile hochgeladen wurde.

-   Jobs: Behebung eines Fehlers, bei dem durch die Verkettung einer Aufgabe zum Konvertieren von MP4 für Smooth Streams und einer PlayReady-Schutzaufgabe unter Verwendung einer Jobvorlage keine Aufgabe erstellt wurde.

-   EncryptionUtils.GetCertificateFromStore(): Diese Methode gibt keine Null-Referenz-Ausnahme mehr zurück, weil die Suche nach dem Zertifikat aufgrund von Problemen mit der Zertifikatkonfiguration fehlschlägt.

## <span id="november_changes_12"></span></a>Version November 2012

Die in diesem Abschnitt aufgeführten Änderungen sind Updates, die im SDK von November 2012 (Version 2.0.0.0) enthalten waren. Aufgrund dieser Änderungen muss möglicherweise Code, der für die SDK-Vorschauversion von Juni 2012 geschrieben wurde, geändert oder neu geschrieben werden.

-   Objekte

    IAsset.Create(assetName) ist die EINZIGE Funktion für die Objekterstellung. IAsset.Create lädt im Rahmen des Methodenaufrufs keine Dateien mehr hoch. Verwenden Sie IAssetFile zum Hochladen.

    Die IAsset.Publish-Methode und der AssetState.Publish-Aufzählungswert wurden aus dem Services SDK entfernt. Von diesem Wert abhängiger Code muss neu geschrieben werden.

-   FileInfo

    Diese Klasse wurde entfernt und durch IAssetFile ersetzt.

    IAssetFiles

    IAssetFile ersetzt FileInfo und weist ein anderes Verhalten auf. Um es verwenden zu können, instanziieren Sie das IAssetFiles-Objekt und laden Sie dann eine Datei über das Media Services SDK oder das Azure-Speicher-SDK hoch. Die folgenden IAssetFile.Upload-Überladungen können verwendet werden:

    -   IAssetFile.Upload(filePath): Eine synchrone Methode, die den Thread sperrt und nur beim Hochladen einer einzelnen Datei empfohlen wird.

    -   IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Eine asynchrone Methode. Das ist der bevorzugte Uploadmechanismus.

    Bekannter Fehler: Durch Verwendung des cancellationToken wird der Upload zwar abgebrochen, für den Abbruchstatus der Aufgaben gibt es jedoch unterschiedliche Möglichkeiten. Sie müssen Ausnahmen korrekt erfassen und behandeln.

-   Locators

    Die ursprungsspezifischen Versionen wurden entfernt. Der SAS-spezifische context.Locators.CreateSasLocator(asset, accessPolicy) wird als veraltet gekennzeichnet oder von GA entfernt. Informationen zum aktualisierten Verhalten finden Sie im Abschnitt zu Locators unter „Vorschauversion Juni 2012“.

## <span id="june_changes_12"></span></a>Vorschauversion Juni 2012

Die folgende Funktion war neu in der November-Version des SDK.

-   Löschen von Entitäten

    Objekte IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey werden nun auf Objektebene gelöscht (d. h. IObject.Delete()), anstatt in der Sammlung gelöscht werden zu müssen (d. h. cloudMediaContext.ObjCollection.Delete(objInstance)).

-   Locators

    Locators müssen nun anhand der CreateLocator-Methode und unter Verwendung der LocatorType.SAS- oder LocatorType.OnDemandOrigin-Aufzählungswerte als Argument für den zu erstellenden, spezifischen Locator-Typ erstellt werden.

    Den Locators wurden neue Eigenschaften hinzugefügt, mit denen einfacher nutzbare URIs für Ihre Inhalte abgerufen werden können. Dieses neue Design der Locators sollte zu mehr Flexibilität für eine zukünftige Erweiterbarkeit durch Dritte sowie zu einer höheren Benutzerfreundlichkeit für Medienclientanwendungen führen.

-   Unterstützung der asynchronen Methode

    Für alle Methoden wurde asynchrone Unterstützung hinzugefügt.

  

  [MSDN-Forum für Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/de-de/home?forum=MediaServices
  [Aktuell bekannte Probleme]: #issues
  [REST-API-Versionsverlauf]: #rest_version_history
  [Version Oktober 2014]: #october_changes_14
  [Version September 2014]: #september_changes_14
  [Version August 2014]: #august_changes_14
  [Version Juli 2014]: #july_changes_14
  [Version Mai 2014]: #may_changes_14
  [Version April 2014]: #april_changes_14
  [Version Dezember 2013]: #december_changes_13
  [Version November 2013]: #november_changes_13
  [Version August 2013]: #august_changes_13
  [Version Juni 2013]: #june_changes_13
  [Version Dezember 2012]: #december_changes_12
  [Version November 2012]: #november_changes_12
  [Vorschauversion Juni 2012]: #june_changes_12
  [Azure Media Services – REST-API-Referenz]: http://msdn.microsoft.com/de-de/library/azure/hh973617.aspx
  [Mediendienste – Preisdetails]: http://azure.microsoft.com/de-de/pricing/details/media-services/
  [StreamingEndpoint]: http://msdn.microsoft.com/de-de/library/azure/dn783468.aspx
  [Bereitstellen von Inhalten]: http://msdn.microsoft.com/de-de/library/azure/hh973618.aspx
  [Indizieren von Mediendateien mit Azure Media Indexer]: http://msdn.microsoft.com/de-de/library/azure/dn783455.aspx
  [Working with Azure Media Services Live Streaming]: http://msdn.microsoft.com/de-de/library/azure/dn783466.aspx
  [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts]: http://msdn.microsoft.com/de-de/library/azure/dn783457.aspx
  [Verwenden der dynamischen PlayReady-Verschlüsselung und des Lizenzübermittlungsdiensts]: http://msdn.microsoft.com/de-de/library/azure/dn783467.aspx
  [Media Services PlayReady-Lizenzvorlage – Übersicht]: http://msdn.microsoft.com/de-de/library/azure/dn783459.aspx
  [Streaming Storage Encrypted Content]: http://msdn.microsoft.com/de-de/library/azure/dn783451.aspx
  [Eingeben von Metadaten]: http://msdn.microsoft.com/de-de/library/azure/dn783120.aspx
  [Ausgeben von Metadaten]: http://msdn.microsoft.com/de-de/library/azure/dn783217.aspx
  [Dynamische Paketerstellung]: http://msdn.microsoft.com/de-de/library/azure/jj889436.aspx
  [Nick Drouin's Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
  [Schützen von Smooth Streaming und MPEG DASH mit PlayReady]: http://msdn.microsoft.com/de-de/library/azure/dn189154.aspx
  [Wiederholungslogik im Media Services SDK für .NET]: http://msdn.microsoft.com/de-de/library/azure/dn745650.aspx
  [Grass Valley Announces EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
  [Steuern von Media Services Encoder-Ausgabedateinamen]: http://msdn.microsoft.com/de-de/library/azure/dn303341.aspx
  [Erstellen von Überlagerungen]: http://msdn.microsoft.com/de-de/library/azure/dn640496.aspx
  [Zusammenfügen von Videosegmenten]: http://msdn.microsoft.com/de-de/library/azure/dn640504.aspx
  [Github]: https://github.com/Azure/azure-sdk-for-media-services
  [Windows Azure Media Services .NET SDK 3.0.0.2 release]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
  [Zugriffssteuerungsdiensts für Azure Active Directory]: http://msdn.microsoft.com/de-de/library/hh147631.aspx
  [Herstellen einer Verbindung mit Media Services mit dem Media Services SDK]: http://msdn.microsoft.com/de-de/library/azure/jj129571.aspx
  [Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
  [azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
  [Verwalten von Media Services-Medienobjekte über mehrere Speicherkonten]: http://msdn.microsoft.com/de-de/library/azure/dn271889.aspx
  [Verarbeiten von Media Services-Auftragsbenachrichtigungen]: http://msdn.microsoft.com/de-de/library/azure/dn261241.aspx
