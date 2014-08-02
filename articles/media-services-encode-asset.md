<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="How to Encode an Asset" pageTitle="How to Encode an Asset for Media Services - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="migree" solutions="" manager="" editor="" />

Gewusst wie: Codieren von Medienobjekten
========================================

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Gewusst wie: Abrufen eines Medienprozessors](http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409).

Sie können Medieninhalte auf dem Server mit dem Azure Media Encoder in verschiedenen Medien-Codierungen und Formaten codieren. Außerdem können Sie Codierer von Mediendienste-Partnern verwenden. Externe Codierer finden Sie im [Azure Marketplace](https://datamarket.azure.com/). Sie können die Details von Codierungsaufgaben entweder mit [Voreinstellungs](http://msdn.microsoft.com/en-us/library/hh973610.aspx)-Zeichenfolgen oder über Konfigurationsdateien angeben.

MP4-Codierung
-------------

Mit der folgenden Methode können Sie ein einzelnes Medienobjekt hochladen und eine Aufgabe zur Codierung des Objekts in MP4 mit der Voreinstellung "H264 Broadband 720p" erstellen. Das Resultat ist eine einzelne MP4-Datei mit H264-Codierung und einer Auflösung von 720p:

<pre><code>
    static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
    {
        //Verschlüsseltes Medienobjekt erstellen und in den Speicher hochladen.
        IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
            inputMediaFilePath);

        // Neue Aufgabe erstellen.

        IJob job = _context.Jobs.Create("My encoding job");
  
        // Verweis auf den Azure Media Encoder abrufen
	    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		
		// Aufgabe mit Codierungsdetails mithilfe einer Voreinstellungs-Zeichenfolge erstellen.
        ITask task = job.Tasks.AddNew("My encoding task",
	        processor,
            "H264 Broadband 720p",
           _protectedConfig);
		
	    // Angabe des zu codierenden Eingabe-Medienobjekts.
        task.InputAssets.Add(asset);

        // Hinzufügen eines Ausgabe-Medienobjekts für die Resultate des Auftrags. 
        // Diese Ausgabe ist als AssetCreationOptions.None deklariert, d. h.
	    // das Ausgabe-Medienobjekt ist im Klartext (nicht verschlüsselt). 
        task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);

		// Mit dem folgenden Ereignishandler können Sie den Auftragsfortschritt prüfen.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs>(StateChanged);

		// Starten des Auftrags.
        job.Submit();

		// Optionale Protokollierung von Auftragsdetails. Gibt die Auftragsdetails
	    // in der Konsole aus und speichert diese in einer JobDetails-{JobId}.txt-Datei
        // in Ihrem Ausgabeordner.
        LogJobDetails(job.Id);

		// Auftragsausführung prüfen und auf Abschluss warten. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		progressJobTask.Wait();

		// Falls Jobstatus gleich Error ist, sollte die
	    // Fehlerbehandlungsmethode die Fehler protokollieren.  Hier prüfen wir
	    // auf den Fehlerstatus und beenden die Ausführung bei Bedarf.
        if (job.State == JobState.Error)
		{
			Console.WriteLine("\nExiting method due to job error.");
			return job;
		}
		
		// Ausführen anderer Aufgaben. Zum Beispiel Zugriff auf die Ausgabe-Medienobjekte
		// des Auftrags, entweder in Form von URLs der Objekte 
		// auf dem Server oder in Form eines Downloads. 
        return job;
    }
	
	private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
		Console.WriteLine("Job state changed event:");
		Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
	    switch (e.CurrentState)
        {
			case JobState.Finished:
            Console.WriteLine();
			Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");            break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
				break;
	        case JobState.Canceled:
            case JobState.Error:

                // Cast sender as a job.
                IJob job = (IJob)sender;
	            // Fehlerdetails bei Bedarf anzeigen oder protokollieren.
                LogJobStop(job.Id);
                break;
	        default:
                break;
		}
	}
</code></pre>

Codierung für Smooth Streaming
------------------------------

Sie haben zwei Optionen, um Videos für Smooth Streaming zu codieren:

-   Direkte Codierung für Smooth Streaming
-   Codierung in MP4 und anschließend codieren für Smooth Streaming

Direkte Codierung für Smooth Streaming mit dem obigen Code, jedoch mithilfe einer der Smooth Streaming Codierungs-Voreinstellungen. Eine vollständige Liste der Codierungs-Voreinstellungen finden Sie unter [Zeichenfolgen für vordefinierte Einstellungen für Aufgaben für Azure Media Encoder](http://msdn.microsoft.com/en-us/library/jj129582.aspx).

Verwenden Sie den Azure Media Packager, um eine MP4-Datei für Smooth Streaming zu konvertieren. Der Azure Media Packager unterstützt keine Voreinstellungen, und Sie müssen die Konfigurationsoptionen im XML angeben. Sie finden das entsprechende XML zum Konvertieren von MP4 für Smooth Streaming unter [Voreinstellungen für Aufgaben für Azure Media Packager](http://msdn.microsoft.com/en-us/library/windowsazure/hh973635.aspx). Fügen Sie das XML in eine Datei mit dem Namen MediaPackager\_MP4ToSmooth.xml in Ihrem Projekt ein. Der folgende Code demonstriert die Konvertierung eines MP4-Medienobjekts für Smooth Streaming. Die folgende Methode nimmt ein existierendes Medienobjekt entgegen und konvertiert es.

<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
  {    
	// Deklarieren eines neuen Auftrags für die Aufgaben
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Einrichten der ersten Aufgabe für die Konvertierung von MP4 für Smooth Streaming. 
    // Lesen des XML mit der Aufgabenkonfiguration?    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Abrufen eines Media Packager-Verweises
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Erstellen eines Auftrags mit den Konvertierungsdetails unter Verwendung der Konfigurationsdaten
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Angabe des zu konvertierenden Eingangs-Medienobjekts.
    task.InputAssets.Add(assetToConvert);
    // Hinzufügen eines Ausgabe-Medienobjekts für die Resultate des Auftrags.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Mit dem folgenden Ereignishandler können Sie den Auftragsfortschritt prüfen. 
    // Die StateChange ist gleich wie im vorherigen Beispiel
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs>(StateChanged);
    // Starten des Auftrags.
    job.Submit();
    // Auftragsausführung prüfen und auf Abschluss warten. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Abrufen eines aktualisierten Auftragsverweises, nachdem auf einen Thread gewartet wurde.
    job = GetJob(job.Id);
    // Prüfen auf Fehler
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

Weitere Informationen zur Verarbeitung von Medienobjekten finden Sie unter:

-   [Verarbeiten von Medienobjekten mit dem Mediendienste-SDK für .NET](http://msdn.microsoft.com/en-us/library/jj129580.aspx)
-   [Verarbeiten von Medienobjekten mit der Mediendienste-REST-API](http://msdn.microsoft.com/en-us/library/jj129574.aspx)

Nächste Schritte
----------------

Sie sind nun in der Lage, einen Auftrag zur Codierung von Medienobjekten zu erstellen und können mit dem Thema [How To Check Job Progress with Media Services](http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409) (Prüfen des Auftragsfortschritts mit Mediendiensten, in englischer Sprache) fortfahren.

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
[How to: Get a Media Processor Instance]:http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]:http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]:http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]:http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]:http://msdn.microsoft.com/en-us/library/windowsazure/hh973635.aspx
