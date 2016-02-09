<properties
	pageTitle="Erste Schritte zum Bereitstellen von Inhalten nach Bedarf mit dem .NET SDK"
	description="Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Übermittlung von Inhalten nach Bedarf mit Azure Media Services mithilfe von .NET."
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
	ms.topic="hero-article"
	ms.date="01/28/2016"
	ms.author="juliako"/>


# Erste Schritte zum Bereitstellen von Inhalten nach Bedarf mit dem .NET SDK


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](/pricing/free-trial/?WT.mc_id=A261C142F).
 
##Übersicht 

Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mithilfe des Azure Media Services (AMS) SDK für .NET.


Das Lernprogramm stellt den Media Services-Arbeitsfluss und die gängigsten Programmierobjekte und Aufgaben für die Media Services-Entwicklung vor. Nach Abschluss des Lernprogramms sind Sie in der Lage, eine einfache Mediendatei zu streamen oder progressiv herunterzuladen, die Sie hochgeladen, codiert und heruntergeladen haben.

## Sie lernen Folgendes

Dieses Tutorial beschreibt die Durchführung der folgenden Aufgaben:

1.  Erstellen eines Media Services-Kontos (mithilfe des klassischen Azure-Portals)
2.  Konfigurieren von Streamingendpunkten (mithilfe des Portals)
3.  Erstellen und Konfigurieren eines Visual Studio-Projekts
5.  Herstellen einer Verbindung mit dem Media Services-Konto
6.  Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei
7.  Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate
8.  Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download
9.  Testen der Funktionalität durch Wiedergabe von Inhalten.

## Voraussetzungen

Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

- Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. 
	
	Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](/pricing/free-trial/?WT.mc_id=A261C142F). Sie erhalten ein Guthaben, mit dem Sie andere kostenpflichtige Azure-Dienste ausprobieren können. Selbst, nachdem Sie dieses Guthaben aufgebraucht haben, können Sie das Konto behalten und kostenlose Azure-Dienste und -Features nutzen, z. B. das Web-Apps-Feature in Azure App Service.
- Betriebssysteme: Windows 8 oder höher, Windows 2008 R2, Windows 7.
- .NET Framework 4.0 oder höher
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express) oder höhere Versionen.


##Beispiel herunterladen

Laden Sie [hier](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/) ein Beispiel herunter, und führen Sie es aus.

##Erstellen eines Media Services-Kontos mithilfe des Portals

1. Klicken Sie im **klassischen Azure-Portal** auf **Mediendienst** und dann auf **Schnellerfassung**.

![Media Services-Schnellerfassung](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. Geben Sie in das Feld **NAME** den Namen des neuen Kontos ein. Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen.

3. Wählen Sie unter **REGION** die geografische Region aus, in der die Metadaten-Datensätze für Ihr Media Services-Konto gespeichert werden sollen. In der Dropdownliste werden nur die verfügbaren Media Services-Regionen angezeigt.

4. Wählen Sie unter **SPEICHERKONTO** ein Speicherkonto aus, das als BLOB-Speicher für die Medieninhalte aus Ihrem Media Services-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in derselben geografischen Region wie Ihr Media Services-Konto auswählen oder ein neues Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt.

5. Wenn Sie ein neues Speicherkonto erstellt haben, geben Sie im Feld **NEUER SPEICHERKONTONAME** einen Namen für das Speicherkonto an. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Media Services-Konten.

6. Klicken Sie unten im Formular auf **Schnellerfassung**.

Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

Wenn das Konto erfolgreich erstellt wurde, ändert sich der Status in **Aktiv**.

Am unteren Rand der Seite wird die Schaltfläche **SCHLÜSSEL VERWALTEN** angezeigt. Wenn Sie auf diese Schaltfläche klicken, wird ein Dialogfeld mit dem Media Services-Kontonamen und dem primären und sekundären Schlüssel angezeigt. Sie benötigen den Kontonamen und den Primärschlüssel, um programmgesteuert auf das Media Services-Konto zugreifen zu können.

![Seite "Media Services"](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

Wenn Sie auf den Kontonamen doppelklicken, wird standardmäßig die Seite **Schnellstart** angezeigt. Auf dieser Seite können Sie einige Verwaltungsaufgaben ausführen, die auch auf anderen Seiten des Portals verfügbar sind. Sie können beispielsweise eine Videodatei von dieser Seite oder der Seite INHALT hochladen.

##Konfigurieren von Streamingendpunkten mithilfe des Portals

Wenn Sie mit Azure Media Services arbeiten, besteht eines der häufigsten Szenarios darin, Adaptive Bitrate Streaming an Ihre Clients zu übermitteln. Mit Adaptive Bitrate Streaming kann der Client während der Videodarstellung auf einen höheren oder niedrigeren Bitraten-Stream wechseln, basierend auf der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren. Von Media Services werden die folgenden Streamingtechnologien mit adaptiver Bitrate unterstützt: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4-Dateien mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streamingformate erneut verpacken müssen.

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren oder Transcodieren Ihrer Zwischendatei (Quelle) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte werden weiter unten in diesem Lernprogramm veranschaulicht)
- Abrufen von mindestens einer Streamingeinheit für den **Streamingendpunkt**, von dem aus Sie die Bereitstellung Ihrer Inhalte planen

Mit der dynamischen Paketerstellung müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client.

Um die Anzahl der reservierten Einheiten für das Streaming zu ändern, gehen Sie folgendermaßen vor:

1. Klicken Sie im [Portal](https://manage.windowsazure.com/) auf **Media Services**. Klicken Sie anschließend auf den Namen des Mediendienstes.

2. Wählen Sie die Seite STREAMING-ENDPUNKTE aus. Klicken Sie anschließend auf den Streaming-Endpunkt, den Sie ändern möchten.

3. Um die Anzahl der Streaming-Einheiten anzugeben, klicken Sie die auf Registerkarte "SKALIERUNG" und verschieben anschließend den Schieberegler für die **reservierte Kapazität**.

	![Skalierungsseite](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. Klicken Sie zum Speichern der Änderungen auf **SPEICHERN**.

Das Zuordnen neuer Einheiten dauert etwa 20 Minuten.

>[AZURE.NOTE] Aktuell kann das Streaming bis zu eine Stunde lang deaktiviert werden, wenn Sie einen positiven Wert für die Streamingeinheiten zurück auf null setzen.
>
> Die höchste für den 24-Stunden-Zeitraum angegebene Anzahl an Einheiten wird zum Berechnen der Kosten verwendet. Eine Preisübersicht finden Sie unter [Azure-Preise](http://go.microsoft.com/fwlink/?LinkId=275107).



##Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 SP1. Geben Sie **Name**, **Ort** und **Lösungsname** ein und klicken Sie auf **OK**.

2. Verwenden Sie das NuGet-Paket [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), um die **Azure Media Services .NET SDK-Erweiterungen** zu installieren. Media Services SDK-Erweiterungen für .NET ist ein Satz von Erweiterungsmethoden und Hilfsfunktionen, die Ihren Code vereinfachen und eine einfachere Entwicklung mit Media Services ermöglichen. Durch Installieren dieses Pakets werden auch das **Media Services .NET SDK** installiert und alle anderen erforderlichen Abhängigkeiten hinzugefügt.

3. Fügen Sie einen Verweis auf die Assembly "System.Configuration" hinzu. Diese Assembly enthält die **System.Configuration.ConfigurationManager**-Klasse, die zum Zugriff auf die Konfigurationsdateien (z. B. "App.config") verwendet wird.

4. Öffnen Sie die Datei "App.config" (fügen Sie die Datei dem Projekt hinzu, wenn sie nicht standardmäßig hinzugefügt wurde), und fügen Sie einen *appSettings*-Abschnitt in der Datei hinzu. Legen Sie die Werte für Ihren Azure Media Services-Kontonamen und Schlüssel fest, wie im folgenden Beispiel gezeigt. Um den Kontonamen und wichtige Informationen zu erhalten, öffnen Sie das klassische Azure-Portal, wählen Sie Ihr Media Services-Konto aus, und klicken Sie anschließend auf die Schaltfläche **Schlüssel verwalten**.

		<configuration>
		...
		  <appSettings>
		    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
		    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
		  </appSettings>
		  
		</configuration>

5. Überschreiben Sie die existierenden **using**-Anweisungen am Anfang der Datei "Program.cs" durch den folgenden Code.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		

6. Erstellen Sie einen neuen Ordner unter dem Verzeichnis Projekte, und kopieren Sie eine MP4 oder WMV-Datei, die Sie codieren und streamen oder progressiv herunterladen möchten. In diesem Beispiel wird der Pfad "C:\VideoFiles" verwendet.

##Herstellen einer Verbindung mit dem Media Services-Konto

Wenn Sie Media Services mit .NET verwenden, müssen Sie die **CloudMediaContext**-Klasse für die meisten Media Services-Programmieraufgaben verwenden, z. B. für das Herstellen einer Verbindung mit dem Media Services-Konto, das Erstellen, Aktualisieren, Zugreifen auf und Löschen der folgenden Objekte: Medienobjekte, Objektdateien, Aufträge, Zugriffsrichtlinien, Locators usw.

Überschreiben Sie die Standardklasse des Programms durch den folgenden Code. Im Code wird veranschaulicht, wie die Werte aus der Datei "App.config" gelesen werden und das **CloudMediaContext**-Objekt erstellt wird, um eine Verbindung zu Media Services herzustellen. Weitere Informationen zum Herstellen einer Verbindung mit Media Services finden Sie unter [Herstellen einer Verbindung zu Media Services mit dem Media Services SDK für .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Die **Main**-Funktion ruft Methoden auf, die weiter unten in diesem Abschnitt definiert werden.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch oder erfassen sie auf diese Weise. Die **Medienobjekt**-Entität kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (sowie die Metadaten zu diesen Dateien) enthalten. Nachdem die Dateien hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert. Die Dateien im Medienobjekt heißen **Medienobjektdateien**.

Die unten definierte **UploadFile**-Methode ruft **CreateFromFile** auf (in den .NET SDK-Erweiterungen definiert). **CreateFromFile** erstellt ein neues Medienobjekt, in das die angegebene Quelldatei hochgeladen wird.

Die **CreateFromFile**-Methode übernimmt **AssetCreationOptions**, sodass Sie eine der folgenden Erstellungsoptionen für Medienobjekte angeben können:

- **None:** Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind. Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option.
- **StorageEncrypted**: Verwenden Sie diese Option, um Ihre unverschlüsselten Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung (Advanced Encryption Standard) zu verschlüsseln. Anschließend werden sie in Azure Storage hochgeladen, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Storage Encryption geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für Storage Encryption ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.
- **CommonEncryptionProtected**: Verwenden Sie diese Option, wenn Sie Inhalte hochladen, die bereits verschlüsselt wurden und durch allgemeine Verschlüsselung oder PlayReady-DRM geschützt sind (z. B. mit PlayReady-DRM geschütztem Smooth Streaming).
- **EnvelopeEncryptionProtected**: Verwenden Sie diese Option, wenn Sie mit AES verschlüsseltes HLS hochladen. Beachten Sie, dass die Dateien durch Transform Manager codiert und verschlüsselt sein müssen.

Mit der **CreateFromFile**-Methode können Sie auch einen Rückruf angeben, um den Fortschritt des Dateiuploads zu melden.

Im folgenden Beispiel wird **Kein** für die Medienobjektoptionen angegeben.

Fügen Sie die folgende Methode zur Program-Klasse hinzu.

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });

	    Console.WriteLine("Asset {0} created.", inputAsset.Id);

	    return inputAsset;
	}


##Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate

Nach dem Erfassen der Medienobjekte in Media Services können die Medien u. a. codiert, transcodiert/multiplexiert und mit einem Wasserzeichen versehen werden, bevor sie an die Clients übermittelt werden. Diese Aktivitäten werden geplant und für mehrere Hintergrundrolleninstanzen ausgeführt, um hohe Leistung und Verfügbarkeit zu gewährleisten. Diese Aktivitäten werden als Aufträge bezeichnet, und jeder Auftrag besteht aus atomaren Aufgaben, welche die eigentliche Arbeit für die Medienobjektdatei leisten.

Wie bereits erwähnt, besteht beim Arbeiten mit Azure Media Services eines der häufigsten Szenarios darin, Adaptive Bitrate Streaming an Ihre Clients zu übermitteln. Media Services können eine Reihe von MP4-Dateien mit adaptiver Bitrate dynamisch in eines der folgenden Formate verpacken: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH und HDS (nur mit Adobe PrimeTime/Access-Lizenz).

Um die dynamische Paketerstellung nutzen zu können, müssen Sie folgende Schritte ausführen:

- Codieren oder Transcodieren Ihrer Zwischendatei (Quelldatei) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate  
- Abrufen von mindestens einer Streamingeinheit für den Streamingendpunkt, von dem aus Sie die Bereitstellung Ihrer Inhalte planen

Der folgende Code zeigt, wie Sie einen Codierungsauftrag senden. Der Auftrag enthält einen Task zum Transcodieren der Zwischendatei in eine Reihe von MP4-Dateien mit adaptiver Bitrate mithilfe von **Media Encoder Standard**. Der Code sendet den Auftrag und wartet, bis er abgeschlossen ist.

Wenn der Auftrag abgeschlossen ist, sind Sie in der Lage, das Medienobjekt zu streamen oder MP4-Dateien, die als Ergebnis der Transcodierung erstellt wurden, progressiv herunterzuladen. Beachten Sie, dass Sie keine Streamingeinheiten abrufen müssen, um MP4-Dateien progressiv herunterzuladen.

Fügen Sie die folgende Methode zur Program-Klasse hinzu.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
	
	    // Prepare a job with a single task to transcode the specified asset
	    // into a multi-bitrate asset.
	
	    IJob job = _context.Jobs.CreateWithSingleTask(
	        "Media Encoder Standard",
	        "H264 Multiple Bitrate 720p",
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
	    Console.WriteLine("Submitting transcoding job...");
	
	
	    // Submit the job and wait until it is completed.
	    job.Submit();
	
	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;
	
	    Console.WriteLine("Transcoding job finished.");
	
	    IAsset outputAsset = job.OutputMediaAssets[0];
	
	    return outputAsset;
	}

##Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download

Um ein Medienobjekt zu streamen oder herunterzuladen, müssen Sie es zunächst durch Erstellen eines Locators "veröffentlichen". Locator ermöglichen den Zugriff auf Dateien im Medienobjekt. Media Services unterstützt zwei Locator-Typen: OnDemandOrigin-Locator zum Streamen von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) und Access Signature (SAS)-Locator zum Herunterladen von Mediendateien.

Nachdem Sie die Locator erstellt haben, können Sie die URLs erstellen, mit denen Sie die Dateien streamen oder herunterladen möchten.


Eine URL für Smooth Streaming hat das folgende Format:

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Eine Streaming-URL für HLS hat das folgende Format:

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Eine Streaming-URL für MPEG DASH hat das folgende Format:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Eine SAS-URL zum Herunterladen von Dateien hat das folgende Format:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK-Erweiterungen bieten praktische Hilfsmethoden, die formatierte URLs für das veröffentlichte Medienobjekt zurückgeben.

Der folgende Code verwendet .NET SDK-Erweiterungen, um Locators zu erstellen sowie Streaming-URLs und URLs für progressive Downloads abzurufen. Der Code zeigt auch das Herunterladen von Dateien in einen lokalen Ordner.

Fügen Sie die folgende Methode zur Program-Klasse hinzu.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##Testen der Funktionalität durch Wiedergabe von Inhalten  

Nachdem Sie das im vorherigen Abschnitt definierte Programm ausgeführt haben, werden ähnliche URLs wie die folgenden im Konsolenfenster angezeigt.

Adaptive Streaming-URLs:

Smooth Streaming

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs für progressiven Download (Audio und Video).

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Verwenden Sie zum Streamen von Videos [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Fügen Sie zum Testen des progressiven Downloads eine URL in einen Browser ein (z. B. Internet Explorer, Chrome oder Safari).


##Nächste Schritte: Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### Suchen Sie etwas anderes?

Wenn dieses Thema nicht die erwarteten Informationen enthält, Informationen fehlen oder auf andere Weise Ihre Erwartungen nicht erfüllt wurden, senden Sie uns bitte über den Disqus-Thread unten Ihr Feedback.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/

<!---HONumber=AcomDC_0204_2016-->