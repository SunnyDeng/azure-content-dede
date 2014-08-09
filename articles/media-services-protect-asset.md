<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="Encrypt Assets in Media Services" pageTitle="How to Encrypt Assets in Media Services - Azure" metaKeywords="" description="Learn how to use Microsoft PlayReady Protection to encrypt an asset in Media Services. Code samples are written in C# and use the Media Services SDK for .NET. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Protect an Asset with PlayReady Protection" authors="migree" solutions="" manager="" editor="" />

Vorgehensweise: Schützen eines Medienobjekts mit dem PlayReady-Schutz
=====================================================================

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema lautete [Vorgehensweise: Auftragsfortschritt prüfen](http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409).

Mit den Azure-Mediendiensten können Sie einen Auftrag übermitteln, der den Microsoft PlayReady-Schutz zur Verschlüsselung eines Medienobjekts integriert. Der Code in diesem Abschnitt übernimmt mehrere Streamingdateien aus einem Eingabeordner, erstellt eine Aufgabe und verschlüsselt die Dateien mithilfe von PlayReady Protection.

Das folgende Beispiel zeigt, wie ein einfacher Auftrag erstellt wird, um PlayReady Protection bereitzustellen.

1.  Rufen Sie die Konfigurationsdaten ab. Sie können eine Beispielkonfigurationsdatei des Themas [Aufgabenvoreinstellung für Azure Media Encryptor](http://msdn.microsoft.com/de-de/library/hh973610.aspx) erhalten.
2.  Hochladen einer MP4-Eingabedatei
3.  Umwandeln der MP4-Datei in ein Smooth Streaming-Medienobjekt
4.  Verschlüsseln des Medienobjekts mit PlayReady
5.  Übermitteln des Auftrags

Das folgende Codebeispiel zeigt, wie die Schritte implementiert werden:

<pre><code>
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Erstellen Sie ein speicher-verschlüsseltes Medienobjekt, und laden Sie die MP4-Datei hoch. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Deklarieren Sie einen neuen Auftrag, der die Aufgaben enthält.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Richten Sie die erste Aufgabe ein. 

    // Lesen Sie die Aufgabenkonfigurationsdaten in eine Zeichenfolge ein. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Rufen Sie eine Media Processor-Instanz ab
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

    // Erstellen Sie eine Aufgabe mit den Konvertierungsdetails unter Verwendung der Konfigurationsdaten 
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Geben Sie das zu konvertierende Eingabemedienobjekt an.
    task.InputAssets.Add(asset);

    // Fügen Sie ein Ausgabemedienobjekt hinzu, das die Ergebnisse des Auftrags enthält. Es besteht keine Notwendigkeit, 
    // das Ausgabemedienobjekt für die Speicherung beizubehalten; setzen Sie somit den shouldPersistOutputOnCompletion-
    // Parameter auf "false". 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Richten Sie die Verschlüsselungsaufgabe ein. 

    // Lesen Sie die Konfigurationsdaten in eine Zeichenfolge ein. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Rufen Sie eine Media Processor-Instanz ab
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

    // Erstellen Sie eine zweite Aufgabe, und geben Sie einen Aufgabennamen, den Media Processor und die Konfiguration an
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Fügen Sie das Eingabemedienobjekt hinzu, welches das Smooth Streaming-Ausgabemedienobjekt aus der ersten Aufgabe darstellt. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Fügen Sie ein Ausgabemedienobjekt hinzu, das die Ergebnisse des Auftrags enthält. Behalten Sie die Ausgabe bei, indem Sie 
    // den Parameter shouldPersistOutputOnCompletion auf "true" setzen.
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
        AssetCreationOptions.None);

    // Verwenden Sie den folgenden Ereignishandler zum Überprüfen des Auftragsfortschritts. 
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Starten Sie den Auftrag.
    job.Submit();

    // Protokollieren Sie optional die Auftragsdetails. Dadurch werden grundlegende Auftragsdetails
    // auf der Konsole angezeigt und in einer Datei JobDetails-{JobId}.txt 
    // im Ausgabeordner gespeichert.
    LogJobDetails(job.Id);

    // Überprüfen Sie die Auftragsausführung, und warten Sie die Beendigung des Auftrags ab. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // Wenn der Auftragsstatus einem Fehler entspricht, sollte die Event Handling- 
    // Methode zum Auftragsfortschritt etwaige Fehler protokollieren.  Hier erfolgt eine Überprüfung 
    // auf einen Fehlerstatus. Bei Bedarf wird die Anwendung beendet.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }
    // Führen Sie weitere Aufgaben aus. Greifen Sie beispielsweise auf die Medienobjekte zu, welche die Ausgabe eines Auftrags darstellen. 
    // Dies kann entweder durch das Erstellen von URLs für die jeweiligen Medienobjekte auf dem Server oder durch einen Download geschehen. 

    return job;
}
</code></pre>

Weitere Informationen zu PlayReady Protection finden Sie unter:

-   [Schützen von Medienobjekten mit Microsoft PlayReady](http://msdn.microsoft.com/de-de/library/dn189154.aspx)
-   [Microsoft PlayReady](http://www.microsoft.com/PlayReady/)

Nächste Schritte
----------------

Da Sie nun wissen, wie Medienobjekte mit Media Services geschützt werden, können Sie mit dem Thema [Verwalten von Medienobjekten](http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409) fortfahren.

