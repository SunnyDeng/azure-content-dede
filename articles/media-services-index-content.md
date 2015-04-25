<properties 
	pageTitle="Indizieren von Mediendateien mit Azure Media Indexer" 
	description="Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. In diesem Thema wird die Verwendung von Media Indexer erläutert." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/04/2015" 
	ms.author="juliako"/>


# Indizieren von Mediendateien mit Azure Media Indexer

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md) . 

Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. Sie können eine einzelne Mediendatei oder mehrere Mediendateien in einem Batch verarbeiten. Sie können auch öffentlich im Internet verfügbare Dateien indizieren, indem Sie die URLs der Dateien in der Manifestdatei angeben.

>[AZURE.NOTE] Stellen Sie beim Indizieren von Inhalten sicher, dass Mediendateien verwendet werden, die sehr klare Sprache enthalten (ohne Hintergrundmusik, Lärm, Effekte oder Mikrofonrauschen). Die folgenden Beispiele sind geeignete Inhalte: aufgezeichnete Besprechungen, Vorträge oder Präsentationen. Folgende Inhalte sind für die Indizierung ggf. nicht geeignet: Filme, Fernsehsendungen, Material mit gemischten Audio- und Soundeffekten, schlecht aufgezeichnete Inhalte mit Hintergrundgeräuschen (Rauschen).


Ein Indizierungsauftrag erzeugt vier Ausgaben für jede Indexdatei:

- Geschlossene Untertiteldatei im SAMI-Format.
- Geschlossene Untertiteldatei im TTML-Format (Timed Text Markup Language).

	SAMI und TTML enthalten ein Tag namens "Recognizability", das einen Indizierungsauftrag basierend darauf bewertet, wie gut die Sprache im Quellvideo erkennbar ist.  Sie können den Wert von "Recognizability" nutzen, um Dateien auf ihre Verwendbarkeit zu prüfen. Eine niedrige Bewertung steht für schlechte Indizierungsergebnisse aufgrund der Audioqualität.
- Schlüsselwortdatei (XML).
- AID-Datei (Audio Indexing Blob) zur Verwendung mit SQL Server.
	
	Weitere Informationen finden Sie unter [Verwenden von AIB-Dateien mit Azure Media Indexer und SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Dieses Thema zeigt Ihnen das Erstellen von Indizierungsaufträgen zum **Indizieren eines Medienobjekts**, **Indizieren mehrerer Dateien** und **Indizieren von öffentlich verfügbaren Dateien im Internet**.

Die neuesten Updates zu Azure Media Indexer finden Sie in den [Media Services-Blogs](http://azure.microsoft.com/blog/topics/media-services/).

## Verwenden von Konfigurations- und Manifestdateien für Indizierungsaufgaben

Sie können weitere Details für Ihre Indizierungsaufgaben mithilfe einer Aufgabenkonfiguration angeben. Beispielsweise können Sie angeben, welche Metadaten für Ihre Mediendatei verwendet werden sollen. Diese Metadaten werden durch das Sprachmodul zum Erweitern des Vokabulars verwendet und verbessern erheblich die Spracherkennungsgenauigkeit.

Sie können auch mehrere Mediendateien gleichzeitig mithilfe einer Manifestdatei verarbeiten.

Weitere Informationen finden Sie unter [Aufgabenvoreinstellung für Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).

## Indizieren eines Medienobjekts

Mit der folgenden Methode werden eine Mediendatei als Medienobjekt hochgeladen und ein Auftrag zum Indizieren des Medienobjekts erstellt.

Beachten Sie, dass die Mediendatei mit allen Standardeinstellungen indiziert wird, wenn keine Konfigurationsdatei angegeben ist.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);
	
	    return asset;
	}
	        
	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
	    foreach (IAssetFile file in asset.AssetFiles)
	    {
	        file.Download(Path.Combine(outputDirectory, file.Name));
	    }
	}
	
	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = _context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));
	
	    return processor;
	} 
	
### <a id="output_files"></a>Ausgabedateien

Der Indizierungsauftrag generiert die folgenden Ausgabedateien. Die Dateien werden im ersten Ausgabemedienobjekt gespeichert.


<table border="1">
<tr><th>Dateiname</th><th>Beschreibung</th></tr>
<tr><td>InputFileName.aib </td>
<td>AIB (Audio Indexing Blob)-Datei<br/><br/>
Die AIB (Ausgabeindizierungs-Blob)-Datei ist eine binäre Datei, die in Microsoft SQL Server mithilfe der Volltextsuche durchsucht werden kann.  Die AIB-Datei ist leistungsfähiger als einfache Untertiteldateien, da sie Alternativen für jedes Wort enthält, was eine bessere Sucherfahrung ermöglicht.
<br/>
<br/>
Sie erfordert die Installation des Indexer SQL-Add-Ons auf einem Computer unter Microsoft SQL Server 2008 oder höher. Das Durchsuchen der AIB-Datei mithilfe der Microsoft SQL Server-Volltextsuche bietet genauere Suchergebnisse als das Durchsuchen der mittels WAMI generierten Untertiteldateien. Der Grund hierfür ist, dass die AIB-Datei Wortalternativen enthält, die ähnlich klingen, wohingegen Untertiteldateien das höchste Konfidenzwort für jedes Segment der Audiospur enthalten. Wenn die Suche nach gesprochenen Wörtern von größter Wichtigkeit ist, dann empfiehlt sich die Verwendung der AIB-Datei zusammen mit Microsoft SQL Server.
<br/><br/>
Klicken Sie zum Herunterladen des Add-Ons auf <a href="http://aka.ms/indexersql">Azure Media Indexer-SQL-Add-On</a>.
<br/><br/>
Es ist auch möglich, andere Suchmaschinen wie Apache Lucene/Solr zu verwenden, um das Video einfach auf Basis der Untertitel- und Stichwort-XML-Dateien zu indizieren. Dies hat jedoch ungenauere Suchergebnisse zur Folge.</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>Untertiteldateien (Closed Caption, CC) im SAMI- und TTML-Format.
<br/><br/>
Sie können verwendet werden, um Hörgeschädigten Audio- und Videodateien zugänglich zu machen.
<br/><br/>
SAMI und TTML enthalten ein Tag namens <b>Recognizability</b>, das einen Indizierungsauftrag basierend darauf bewertet, wie gut die Sprache im Quellvideo erkennbar ist.  Sie können den Wert von <b>Recognizability</b> nutzen, um Dateien auf ihre Verwendbarkeit zu prüfen. Eine niedrige Bewertung steht für schlechte Indizierungsergebnisse aufgrund der Audioqualität.</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>Stichwortdatei
<br/><br/>
Die Stichwortdatei ist eine XML-Datei, die Stichwörter enthält, welche aus dem Sprachinhalt mit Frequenz- und Abstandsinformationen extrahiert wurden.
<br/><br/>
Die Datei kann für unterschiedliche Zwecke verwendet werden, beispielsweise zum Ausführen einer Sprachanalyse, oder sie kann Suchmaschinen wie Bing, Google oder Microsoft SharePoint zur Verfügung gestellt werden, damit die Mediendateien leichter auffindbar sind, oder sie kann zum Bereitstellen wirkungsvollerer Werbeanzeigen verwendet werden.</td></tr>
</table>

Wenn nicht alle Eingabemediendateien erfolgreich indiziert werden, verursacht der Indizierungsauftrag einen Fehler mit dem Fehlercode 4000. Weitere Informationen finden Sie unter [Fehlercodes](#error_codes).

## Indizieren mehrerer Dateien

Mit der folgenden Methode werden mehrere Mediendateien als Medienobjekt hochgeladen und ein Auftrag zum Indizieren all dieser Dateien in einem Batch erstellt.

Es wird eine Manifestdatei mit der Erweiterung LST erstellt und in das Medienobjekt hochgeladen. Die Manifestdatei enthält die Liste sämtlicher Medienobjektdateien. Weitere Informationen finden Sie unter [Aufgabenvoreinstellung für Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


### Ausgabedateien

Wenn mehr als eine Eingabemediendatei vorhanden ist, generiert WAMI die Manifestdatei "JobResult.txt" für die Auftragsausgaben. Die sich daraus ergebenden AIB-, SAMI-, TTML- und Stichwortdateien werden für jede Eingabemediendatei sequenziell nummeriert, wie dies im Folgenden aufgelistet ist.

Beschreibungen der Ausgabedateien, finden Sie unter [Ausgabedateien](#output_files). 


<table border="1">
<tr><th>Dateiname</th><th>Beschreibung</th></tr>
<tr><td>JobResult.txt</td>
<td>Ausgabemanifest
<br/><br/>Im Folgenden finden Sie das Format der Ausgabemanifestdatei ("JobResult.txt").
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Fehler</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
Jede Zeile stellt eine Eingabemediendatei dar:
<br/><br/>
InputFile: Medienobjekt-Dateiname oder URL der Eingabemediendatei.
<br/><br/>
Alias: Entsprechender Ausgabedateiname.
<br/><br/>
MediaLength: Länge der Eingabemediendatei in Sekunden. Kann 0 sein, wenn ein Fehler bei dieser Eingabe auftritt.
<br/><br/>
Fehler: Gibt an, ob diese Mediendatei erfolgreich indiziert wurde. 0 für Erfolg, ansonsten fehlerhaft. Informationen zu einzelnen Fehlern finden Sie unter <a href="#error_codes">Fehlercodes</a>.
</td></tr>
<tr><td>Media_1.aib </td>
<td>Datei #0 - AIB-Datei</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>Datei #0 - Untertiteldateien (Closed Caption, CC) im SAMI- und TTML-Format</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>Datei #0 - Stichwortdatei</td></tr>
<tr><td>Media_2.aib </td>
<td>Datei #1 - AIB-Datei</td></tr>
</table>

Wenn nicht alle Eingabemediendateien erfolgreich indiziert werden, verursacht der Indizierungsauftrag einen Fehler mit dem Fehlercode 4000. Weitere Informationen finden Sie unter [Fehlercodes](#error_codes).

### Teilweise erfolgreicher Auftrag

Wenn nicht alle Eingabemediendateien erfolgreich indiziert werden, verursacht der Indizierungsauftrag einen Fehler mit dem Fehlercode 4000. Weitere Informationen finden Sie unter [Fehlercodes](#error_codes).


Es werden die gleichen Ausgaben (wie bei erfolgreichen Aufträgen) generiert. Sie können anhand der Ausgabemanifestdatei feststellen, welche Eingabedateien gemäß den Werten in der Fehlerspalte fehlerhaft sind. Bei fehlerhaften Eingabedateien werden KEINE AIB-, SAMI-, TTML- und Stichwortdateien generiert.

## Indizieren von Dateien aus dem Internet

Sie können auch öffentlich verfügbare Mediendateien im Internet indizieren, ohne sie in Azure Storage zu kopieren. Sie können die Manifestdatei verwenden, um die URLs der Mediendateien anzugeben. Weitere Informationen finden Sie unter [Aufgabenvoreinstellung für Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).

Beachten Sie, dass HTTP- und HTTPS-URL-Protokolle unterstützt werden.

Mit der folgenden Methode und Konfiguration wird ein Auftrag zum Indizieren einer Mediendatei im Internet erstellt.
	
	static bool RunIndexingJobWithPublicUrl(string inputMediaUrl, string outputFolder)
	{
	    // Create the manifest file that contains the input media URL
	    string manifestFile = "input.lst";
	    File.WriteAllLines(manifestFile, new string[] { inputMediaUrl });
	
	    // Create an asset and upload the manifest file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(manifestFile,
	        "My Indexing Input Asset - Public URL",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Public URL");
	
	    // Get a reference to the Azure Media Indexer.
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("public.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Public URL",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Public URL", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}

### Ausgabedateien

Beschreibungen der Ausgabedateien, finden Sie unter [Ausgabedateien](#output_files). 


## Verarbeiten geschützter Dateien

Der Indexer unterstützt die Standardauthentifizierung mit Benutzernamen und Kennwort beim Herunterladen von Internetdateien über HTTP oder HTTPS.

Sie können in der Aufgabenkonfiguration Werte für **Benutzername** und **Kennwort** festlegen, wie beschrieben unter [Aufgabenvoreinstellung für Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).

### <a id="error_codes"></a>Fehlercodes


<table border="1">
<tr><th>Code</th><th>Name</th><th>Mögliche Ursachen</th></tr>
<tr><td>2000</td><td>Ungültige Konfiguration</td><td>Ungültige Konfiguration</td></tr>
<tr><td>2001</td><td>Ungültige Eingabemedienobjekte</td><td>Fehlende Eingabemedienobjekte oder leeres Medienobjekt</td></tr>
<tr><td>2002</td><td>Ungültiges Manifest</td><td>Manifest ist leer oder enthält ungültige Elemente</td></tr>
<tr><td>2003</td><td>Fehler beim Herunterladen der Mediendatei</td><td>Ungültige URL in Manifestdatei</td></tr>
<tr><td>2004</td><td>Nicht unterstütztes Protokoll</td><td>Protokoll der Medien-URL wird nicht unterstützt</td></tr>
<tr><td>2005</td><td>Nicht unterstützter Dateityp</td><td>Typ der Eingabemediendatei wird nicht unterstützt</td></tr>
<tr><td>2006</td><td>Zu viele Eingabedateien</td><td>Es sind mehr als 10 Dateien im Eingabemanifest vorhanden </td></tr>
<tr><td>3000</td><td>Fehler beim Decodieren der Mediendatei</td>
<td>Nicht unterstützter Mediencodec
<br/>oder<br/>
Beschädigte Mediendatei
<br/>oder<br/>
Kein Audiostream in den Eingabemedien</td></tr>
<tr><td>4000</td><td>Batchindizierung teilweise erfolgreich</td><td>Bei einigen Eingabemediendateien ist beim Indizieren ein Fehler aufgetreten Weitere Informationen finden Sie unter <a href="output_files">Ausgabedateien</a>.</td></tr>
<tr><td>Sonstige</td><td>Interne Fehler</td><td>Wenden Sie sich an das Supportteam</td></tr>
</table>


## <a id="supported_languages"></a>Unterstützte Sprachen

Derzeit wird nur Englisch unterstützt.

## Verwandte Links

[Verwenden von AIB-Dateien mit Azure Media Indexer und SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!--HONumber=47-->
