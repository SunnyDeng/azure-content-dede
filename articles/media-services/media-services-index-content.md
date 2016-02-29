<properties
	pageTitle="Indizieren von Mediendateien mit Azure Media Indexer"
	description="Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. In diesem Thema wird die Verwendung von Media Indexer erläutert."
	services="media-services"
	documentationCenter=""
	authors="Asolanki"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/17/2016"   
	ms.author="juliako;adsolank;johndeu"/>


# Indizieren von Mediendateien mit Azure Media Indexer

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. Sie können eine einzelne Mediendatei oder mehrere Mediendateien in einem Batch verarbeiten.

>[AZURE.IMPORTANT] Stellen Sie beim Indizieren von Inhalten sicher, dass Mediendateien verwendet werden, die sehr klare Sprache enthalten (ohne Hintergrundmusik, Lärm, Effekte oder Mikrofonrauschen). Die folgenden Beispiele sind geeignete Inhalte: aufgezeichnete Besprechungen, Vorträge oder Präsentationen. Folgende Inhalte sind für die Indizierung ggf. nicht geeignet: Filme, Fernsehsendungen, Material mit gemischten Audio- und Soundeffekten, schlecht aufgezeichnete Inhalte mit Hintergrundgeräuschen (Rauschen).


Ein Indizierungsauftrag kann die folgenden Ausgaben generieren:

- Untertiteldateien in den folgenden Formaten: **SAMI**, **TTML** und **WebVTT**.

	Eine Untertiteldatei enthält ein Tag namens „Recognizability“, das einen Indizierungsauftrag basierend darauf bewertet, wie gut die Sprache im Quellvideo erkennbar ist. Sie können den Wert von Recognizability nutzen, um Dateien auf ihre Verwendbarkeit zu prüfen. Eine niedrige Bewertung steht für schlechte Indizierungsergebnisse aufgrund der Audioqualität.
- Schlüsselwortdatei (XML).
- AID-Datei (Audio Indexing Blob) zur Verwendung mit SQL Server.

	Weitere Informationen finden Sie unter [Verwenden von AIB-Dateien mit Azure Media Indexer und SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


In diesem Thema wird das Erstellen von Indizierungsaufträgen zum **Indizieren eines Medienobjekts** und zum **Indizieren mehrerer Dateien** beschrieben.

Die neuesten Updates zu Azure Media Indexer finden Sie in den [Media Services-Blogs](#preset).

## Verwenden von Konfigurations- und Manifestdateien für Indizierungsaufgaben

Sie können weitere Details für Ihre Indizierungsaufgaben mithilfe einer Aufgabenkonfiguration angeben. Beispielsweise können Sie angeben, welche Metadaten für Ihre Mediendatei verwendet werden sollen. Diese Metadaten werden durch das Sprachmodul zum Erweitern des Vokabulars verwendet und verbessern erheblich die Spracherkennungsgenauigkeit. Sie können zudem die gewünschten Ausgabedateien angeben.

Sie können auch mehrere Mediendateien gleichzeitig mithilfe einer Manifestdatei verarbeiten.

Weitere Informationen finden Sie unter [Aufgabenvoreinstellung für Azure Media Indexer](#).

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
<!-- __ -->
### <a id="output_files"></a>Ausgabedateien

Ein Indizierungsauftrag generiert standardmäßig die folgenden Ausgabedateien. Die Dateien werden im ersten Ausgabemedienobjekt gespeichert.

Wenn mehrere Eingabemediendateien vorhanden sind, generiert Indexer die Manifestdatei „JobResult.txt“ für die Auftragsausgaben. Die sich daraus ergebenden AIB-, SAMI-, TTML-, WebVTT- und Stichwortdateien werden für jede Eingabemediendatei fortlaufend nummeriert und mithilfe des „Alias“ benannt.

Dateiname | Beschreibung
----------|------------
__InputFileName.aib__ | AIB (Audio Indexing Blob)-Datei <br /><br /> Die Ausgabeindizierungsblob-Datei (Audio Indexing Blob, AIB) ist eine binäre Datei, die in Microsoft SQL Server mithilfe der Volltextsuche durchsucht werden kann. Die AIB-Datei ist leistungsfähiger als einfache Untertiteldateien, da sie Alternativen für jedes Wort enthält, was eine bessere Sucherfahrung ermöglicht. <br/> <br/>Sie erfordert die Installation des Indexer SQL-Add-Ons auf einem Computer unter Microsoft SQL Server 2008 oder höher. Das Durchsuchen der AIB-Datei mithilfe der Microsoft SQL Server-Volltextsuche bietet genauere Suchergebnisse als das Durchsuchen der mittels WAMI generierten Untertiteldateien. Der Grund hierfür ist, dass die AIB-Datei Wortalternativen enthält, die ähnlich klingen, wohingegen Untertiteldateien das höchste Konfidenzwort für jedes Segment der Audiospur enthalten. Wenn die Suche nach gesprochenen Wörtern sehr wichtig ist, dann empfiehlt sich die Verwendung der AIB-Datei zusammen mit Microsoft SQL Server.<br/><br/> Klicken Sie zum Herunterladen des Add-Ons auf <a href="http://aka.ms/indexersql">Azure Media Indexer-SQL-Add-On</a>. <br/><br/>Es ist auch möglich, andere Suchmaschinen wie Apache Lucene/Solr zu verwenden, um das Video einfach auf Basis der Untertitel- und Stichwort-XML-Dateien zu indizieren. Dies hat jedoch ungenauere Suchergebnisse zur Folge.
__InputFileName.smi__<br />\_\_InputFileName.ttml\_\_<br />\_\_InputFileName.vtt\_\_ |Untertiteldateien (Closed Caption, CC) im SAMI-, TTML-, und WebVTT-Format.<br/><br/>Sie können verwendet werden, um Hörgeschädigten Audio- und Videodateien zugänglich zu machen.<br/><br/>Untertiteldateien enthalten ein Tag namens <b>Recognizability</b>, das einen Indizierungsauftrag basierend darauf bewertet, wie gut die Sprache im Quellvideo erkennbar ist. Sie können den Wert von <b>Recognizability</b> nutzen, um Ausgabedateien auf ihre Verwendbarkeit zu prüfen. Eine niedrige Bewertung steht für schlechte Indizierungsergebnisse aufgrund der Audioqualität.
__InputFileName.kw.xml<br />InputFileName.info__ |Stichwort- und Informationsdateien. <br/><br/>Die Stichwortdatei ist eine XML-Datei, die Stichwörter enthält, welche aus dem Sprachinhalt mit Frequenz- und Abstandsinformationen extrahiert wurden. <br/><br/>Die Informationsdatei ist eine Nur-Text-Datei und enthält detaillierte Informationen zu jedem erkannten Begriff. Die erste Zeile unterscheidet sich von den anderen Zeilen und enthält die Bewertung für „Recognizability“. Jede nachfolgende Zeile ist eine tabstoppgetrennte Auflistung der folgenden Daten: Startzeit, Endzeit, Wort/Ausdruck, Konfidenz. Die Zeiten sind in Sekunden und die Konfidenzwerte als Zahl zwischen 0 und 1 angegeben. <br/><br/>Beispielzeile: „1.20 1.45 word 0.67“ <br/><br/>Diese Dateien können für unterschiedliche Zwecke verwendet werden, beispielsweise zum Ausführen einer Sprachanalyse. Zudem können sie Suchmaschinen wie Bing, Google oder Microsoft SharePoint zur Verfügung gestellt werden, damit die Mediendateien leichter auffindbar sind, oder auch zum Bereitstellen wirkungsvollerer Werbeanzeigen verwendet werden.
__JobResult.txt__ |Ausgabemanifest, nur beim Indizieren mehrerer Dateien vorhanden, enthält die folgenden Informationen:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Error</th></tr><tr><td>a.mp4</td><td>Media\_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media\_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media\_3</td><td>600</td><td>0</td></tr></table><br/>



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

### Teilweise erfolgreicher Auftrag

Wenn nicht alle Eingabemediendateien erfolgreich indiziert werden, verursacht der Indizierungsauftrag einen Fehler mit dem Fehlercode 4000. Weitere Informationen finden Sie unter [Fehlercodes](#error_codes).


Es werden die gleichen Ausgaben (wie bei erfolgreichen Aufträgen) generiert. Sie können anhand der Ausgabemanifestdatei feststellen, welche Eingabedateien gemäß den Werten in der Fehlerspalte fehlerhaft sind. Bei fehlerhaften Eingabedateien werden KEINE AIB-, SAMI-, TTML-, WebVTT- und Stichwortdateien generiert.

### <a id="preset"></a> Aufgabenvoreinstellungen für Azure Media Indexer

Die Verarbeitung von Azure Media Indexer kann angepasst werden, indem zusätzlich zur Aufgabe eine optionale Aufgabenvoreinstellung bereitgestellt wird. Im Folgenden wird das Format dieser XML-Konfigurationsdatei beschrieben.

Name | Anforderung | Beschreibung
----|----|---
__input__ | false | Medienobjektdateien, die Sie indizieren möchten.</p><p>Azure Media Indexer unterstützt die folgenden Mediendateiformate: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Sie können Sie den Dateinamen im Attribut **name** oder **list** des Elements **input** (siehe unten) spezifizieren. Wenn keine Medienobjektdatei zum Indizieren festgelegt ist, wird automatisch die Primärdatei ausgewählt. Wenn keine primäre Medienobjektdatei festgelegt ist, wird die erste Datei im Eingabemedienobjekt indiziert.</p><p>Den Namen der Medienobjektdatei geben Sie folgendermaßen explizit an:<br />`<input name="TestFile.wmv">`<br /><br />Sie können Sie auch mehrere (bis zu 10) Medienobjektdateien gleichzeitig indizieren. Gehen Sie hierfür folgendermaßen vor:<br /><br /><ol class="ordered"><li><p>Erstellen Sie eine Textdatei (Manifestdatei), und geben Sie ihr die Erweiterung .LST. </p></li><li><p>Fügen Sie dieser Manifestdatei eine Liste aller Medienobjektdateinamen im Eingabemedienobjekt hinzu. </p></li><li><p>Fügen Sie die Manifestdatei dem Medienobjekt hinzu (laden Sie sie hoch). </p></li><li><p>Geben Sie im Eingabelistenattribut den Namen der Manifestdatei an.<br />`<input list="input.lst">`</li></ol><br /><br />Hinweis: Wenn Sie der Manifestdatei mehr als 10 Dateien hinzufügen, schlägt der Indizierungsauftrag fehl, und der Fehlercode „2006“ wird ausgegeben.
__metadata__ | false | Metadaten für die festgelegten Medienobjektdateien zur Vokabularanpassung. So kann der Indexer nicht standardmäßiges Vokabular wie z. B. Eigennamen erkennen.<br />`<metadata key="..." value="..."/>` <br /><br />Sie können __Werte__ für vordefinierte __Schlüssel__ angeben. Derzeit werden die folgenden Schlüssel unterstützt:<br /><br />„title“ und „description“. Diese dienen der Vokabularanpassung zum Optimieren des Sprachmodells für Ihren Auftrag und zum Verbessern der Spracherkennungsgenauigkeit. Über Internetsuchen finden die Werte kontextrelevante Textdokumente und nutzen deren Inhalte zur Vergrößern des internen Wörterbuchs während der Indizierungsaufgabe.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__features__ <br /><br /> Wurde in Version 1.2 hinzugefügt. Das einzige unterstützte Feature ist derzeit die Spracherkennung („ASR“).| false | Die Spracherkennung verfügt über die folgenden Einstellungsschlüssel:<table><tr><th><p>Schlüssel</p></th> <th><p>Beschreibung</p></th><th><p>Beispielwert</p></th></tr><tr><td><p>Language</p></td><td><p>Die in der Multimediadatei zu erkennende natürliche Sprache</p></td><td><p>Englisch, Spanisch</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>Eine durch Semikolon getrennte Liste der gewünschten ausgegebenen Untertitelformate (sofern vorhanden)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Ein boolesches Flag, das angibt, ob eine AIB-Datei erforderlich ist (zur Verwendung mit SQL Server und dem Indexerfilter des Kunden) Weitere Informationen finden Sie unter <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Verwenden von AIB-Dateien mit Azure Media Indexer und SQL Server</a>.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Ein boolesches Flag, das angibt, ob eine XML-Datei mit Stichwörtern erforderlich ist</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Ein boolesches Flag, das angibt, ob vollständige Untertitel erzwungen werden sollen (unabhängig vom Grad der Zuverlässigkeit) </p><p>Standardwert ist „false“. In dem Fall werden Wörter und Sätze, deren Zuverlässigkeit unter 50 % liegt, in den final ausgegebenen Untertiteln durch Auslassungspunkte („...“) ersetzt. Die Auslassungspunkte eignen sich für die Qualitätskontrolle der Untertitel und für die Überwachung.</p></td><td><p>True; False. </p></td></tr></table>

### <a id="error_codes"></a>Fehlercodes

Wenn ein Fehler auftritt, gibt Azure Media Indexer einen der folgenden Fehlercodes aus:

Code | Name | Mögliche Ursachen
-----|------|------------------
2000 | Ungültige Konfiguration | Ungültige Konfiguration
2001 | Ungültige Eingabemedienobjekte | Fehlende Eingabemedienobjekte oder leeres Medienobjekt
2002 | Ungültiges Manifest | Manifest ist leer oder enthält ungültige Elemente
2003 | Fehler beim Herunterladen der Mediendatei | Ungültige URL in Manifestdatei
2004 | Nicht unterstütztes Protokoll | Protokoll der Medien-URL wird nicht unterstützt
2005 | Nicht unterstützter Dateityp | Typ der Eingabemediendatei wird nicht unterstützt
2006 | Zu viele Eingabedateien | Es sind mehr als 10 Dateien im Eingabemanifest vorhanden
3000 | Fehler beim Decodieren der Mediendatei | Nicht unterstützter Mediencodec <br/>oder<br/> Beschädigte Mediendatei <br/>oder<br/> Kein Audiostream in den Eingabemedien
4000 | Batchindizierung teilweise erfolgreich | Bei einigen Eingabemediendateien ist beim Indizieren ein Fehler aufgetreten Weitere Informationen finden Sie unter <a href="output_files">Ausgabedateien</a>.
Sonstige | Interne Fehler | Wenden Sie sich an das Supportteam. indexer@microsoft.com


## <a id="supported_languages"></a>Unterstützte Sprachen

Derzeit werden die Sprachen Englisch und Spanisch unterstützt. Weitere Informationen finden Sie im [Blogbeitrag zu Version 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



## Verwandte Links

[Verwenden von AIB-Dateien mit Azure Media Indexer und SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0218_2016-->