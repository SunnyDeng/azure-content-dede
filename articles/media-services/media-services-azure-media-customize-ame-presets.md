<properties 
	pageTitle="Ändern von Codierungsaufgaben durch Anpassen von Aufgabenvoreinstellungen" 
	description="Mit dem Azure Media Services Encoder können Sie benutzerdefinierte Voreinstellungsdateien an Azure Media Encoder übergeben. In diesem Thema erfahren Sie, wie Sie Voreinstellungsdateien anpassen, um die folgenden Aufgaben durchzuführen: Überlagern eines bestehenden Videos mit einem Bild, Steuern der Ausgabedateinamen, die der Encoder erstellt, Zusammenfügen von Videos." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015" 
	ms.author="juliako"/>

#Ändern von Codierungsaufgaben durch Anpassen von Aufgabenvoreinstellungen 

Mit dem Azure Media Services Encoder können Sie benutzerdefinierte Voreinstellungsdateien an Azure Media Encoder übergeben. In diesem Thema erfahren Sie, wie Sie Voreinstellungsdateien anpassen, um die folgenden Aufgaben durchzuführen:

- Überlagern eines vorhandenen Videos mit einem Bild 
- Steuern der Ausgabedateinamen, die der Encoder erstellt 
- Zusammenfügen von Videos
- Codieren von sprachlastigen Präsentationen

##Steuern der Ausgabedateinamen des Azure Media Encoder 

Azure Media Encoder erstellt Ausgabedateinamen standardmäßig durch die Kombination verschiedener Attribute des Eingabemedienobjekts und des Codierungsvorgangs. Jedes Attribut wird mithilfe eines Makros identifiziert, wie nachstehend erläutert.

Im Folgenden finden Sie eine vollständige Liste der verfügbaren Makros für die Ausgabedateibenennung: "Audio Bitrate": Die beim Codieren der Audiodatei verwendete Bitrate, angegeben in KBit/s

- "Audio Codec": Der für die Audiocodierung verwendete Codec, gültige Werte sind: AAC, WMA und DDP
- "Channel Count": Die Anzahl der codierten Audiokanäle, gültige Werte sind: 1, 2 und 6
- "Default extension": Die Standarddateierweiterung 
- "Language": Der BCP-47-Sprachcode, der die in der Audiodatei verwendete Sprache anzeigt. Der Standardwert lautet derzeit "und". 
- "Original File Name": Der Name der Datei, die in den Azure-Speicher hochgeladen wird
- "StreamId": Die Datenstrom-ID gemäß Definition durch das StreamID-Attribut des <StreamInfo>-Element in der Voreinstellungsdatei 
- "Video Codec": Der für die Codierung verwendete Codec, gültige Werte sind: H264 und VC1
- "Video Bitrate": Die zum Codieren der Videodatei verwendete Bitrate, angegeben in KBit/s

Diese Makros können in jeder beliebigen Kombination verwendet werden, um die Benennung von mit Media Services Encoder generierten Dateien zu steuern. Beispielsweise lautet die standardmäßige Benennungskonvention:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

Die Dateinamenkonvention wird mithilfe des DefaultMediaOutputFileName-Attributs des [<Voreinstellungen>](https://msdn.microsoft.com/library/azure/dn554334.aspx)-Elements angegeben. Beispiel:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

Der Encoder fügt Unterstriche zwischen den einzelnen Makros ein, z. B. ergibt die Konfiguration oben einen Dateinamen wie "MyVideo\_H264\_4500kpbs\_AAC\_und\_ch2\_128kbps.mp4".


##Erstellen von Überlagerungen

Mit dem Azure Media Services Encoder können Sie ein vorhandenes Video mit einem Bild (JPG, BMP, GIF, TIF), einem Video oder einer Audiodatei (WMA, MP3, WAV) überlagern. Diese Funktion entspricht in etwa dem Expression Encoder 4 (Service Pack 2).

###Überlagerungen mit dem Media Services Encoder

Sie können angeben, wann die Überlagerung gezeigt wird, wie lange die Überlagerung angezeigt wird und bei Bild-/Video-Überlagerungen an welcher Stelle die Überlagerung auf dem Bildschirm angezeigt wird. Außerdem können Sie die Überlagerungen ein- und ausblenden. Die Audio- und Videodateien für die Überlagerung können in mehreren Medienobjekten oder in einem einzelnen Medienobjekt enthalten sein. Überlagerungen werden durch eine Voreinstellungs-XML gesteuert, die an den Encoder übergeben wird. Eine vollständige Beschreibung des Voreinstellungsschemas finden Sie unter "Azure Media Encoder-Schemas". Überlagerungen werden wie im folgenden Voreinstellungsausschnitt gezeigt im <MediaFile>-Element angegeben:

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###Voreinstellungen für Video- oder Bildüberlagerungen

Überlagerungen können aus einem einzelnen oder mehreren Medienobjekten stammen. Beim Erstellen von Videoüberlagerungen mit mehreren Medienobjekten wird der Überlagerungsdateiname im OverlayFileName-Attribut mit der %n%-Syntax angegeben, wobei n der nullbasierte Index des Eingabemedienobjekts für die Codierungsaufgabe ist. Beim Erstellen von Videoüberlagerungen mit einem einzelnen Medienobjekt wird der Überlagerungsdateiname direkt im OverlayFileName-Attribut angegeben, wie im folgenden Voreinstellungsausschnitt gezeigt:

Beispiel 1:

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Beispiel 2:

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Die Position und Größe der Videoüberlagerung wird durch das OverlayRect-Attribut gesteuert. Die Größe des zu überlagernden Inhalts wird an dieses Rechteck angepasst. Die Deckkraft wird durch das OverlayOpacity-Attribut gesteuert. Gültige Werte sind 0,0 bis 1,0, wobei 1,0 für eine 100%ige Deckkraft steht. Die Überlagerung wird an dem durch das OverlayStartTime-Attribut festgelegten Zeitpunkt angezeigt und an dem durch das OverlayEndTime-Attribut festgelegten Zeitpunkt ausgeblendet. Sowohl OverlayStartTime als auch OverlayEndTime müssen innerhalb der Zeitleiste des Quellvideos liegen. Weitere Informationen zu den einzelnen überlagerungsspezifischen Attributen finden Sie unter "Azure Media Encoder-Schemas".

###Voreinstellungen für Audioüberlagerungen

Als Audioüberlagerungen können beispielsweise alle unterstützten Audioformate verwendet werden. Eine vollständige Liste der unterstützten Audiodateiformate finden Sie unter "Vom Media Services Encoder unterstützte Formate". Audioüberlagerungen werden ebenfalls wie im folgenden Voreinstellungsausschnitt gezeigt im <MediaFile>-Element angegeben:

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

Wenn Audioüberlagerungen in mehreren Medienobjekten gespeichert sind, wird der Audioüberlagerungsdateiname im AudioOverlayFileName-Attribut mit der %n%-Syntax angegeben, wobei n der nullbasierte Index der Auflistung an Eingabemedienobjekten für die Codierungsaufgabe ist. Bei Audioüberlagerungen in einem einzelnen Medienobjekt wird der Überlagerungsdateiname direkt im AudioOverlayFileName-Attribut angegeben. AudioOverlayLayoutMode legt fest, wann die Audioüberlagerung eingespielt wird. Beim Wert "WholeSequence" wird die Audiodatei während der gesamten Dauer des Videos abgespielt. Beim Wert "Custom" bestimmen die AudioOverlayStartTime- und AudioOverlayEndTime-Attribute, wann die Audioüberlagerung beginnt und endet. Sowohl OverlayStartTime als auch OverlayEndTime müssen innerhalb der Zeitleiste des Quellvideos liegen. Weitere Informationen zu allen Audioüberlagerungsattributen finden Sie unter "Azure Media Encoder-Schemas". Audioüberlagerungen können mit Videoüberlagerungen kombiniert werden, wie im folgenden Voreinstellungsausschnitt gezeigt:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###Übermitteln von Aufgaben mit Überlagerungen

Nach dem Erstellen einer Voreinstellungsdatei müssen Sie die folgenden Schritte ausführen:

- Hochladen von Medienobjekten
- Laden der Voreinstellungskonfiguration (Hinweis: Im folgenden Code wird die oben stehende Voreinstellung verwendet.)
- Erstellen eines Auftrags
- Abrufen eines Verweises auf den Media Services Encoder
- Erstellen einer Aufgabe zur Angaben der Auflistung der Eingabemedienobjekte, der Voreinstellungskonfiguration, des Media Encoder und des Ausgabemedienobjekts
- Übermitteln des Auftrags

Der folgende Codeausschnitt zeigt, wie Sie diese Schritte durchführen können:

	static public void CreateOverlayJob()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);


		// Upload assets to overlay
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
		
		// Load the preset configuration
		string presetFileName = "OverlayPreset.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME overlay job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
	}



>[AZURE.NOTE]In diesem Codeausschnitt wird einfachheitshalber jedes Medienobjekt sequenziell geladen. In Produktionsumgebungen werden Medienobjekte in einem Massenladevorgang geladen. Weitere Informationen zum Hochladen mehrerer Medienobjekte in einem Massenladevorgang finden Sie unter [Sammelerfassung von Medienobjekten mit dem Media Services SDK für .NET](media-services-dotnet-upload-files.md#ingest_in_bulk).

Einen vollständigen Beispielcode finden Sie unter [Erstellen von Überlagerungen mit dem Media Services Encoder](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47).

###Fehlerbedingungen

Beim Bearbeiten der Voreinstellungszeichenfolge müssen Sie Folgendes sicherstellen:

- Bei Video- und Bildüberlagerungen muss das Überlagerungsrechteck vollständig in die Abmessungen des Quellvideos passen.
- Die Start- und Endzeit der Überlagerungen sollten in der Zeitleiste des Quellvideos liegen.
- Wenn die Voreinstellungs-XML einen Verweis auf ?OverlayFileName="%n%" enthält, dann sollte die InputAssets-Auflistung für die Aufgaben mindestens n+1 Medienobjekte enthalten



##Zusammenfügen von Videosegmenten

Der Media Services Encoder bietet Unterstützung für das Zusammenfügen mehrerer Videos. Videos können hintereinander zusammengefügt werden, oder Sie geben Teile eines oder beider Videos an, die zusammengefügt werden sollen. Das Ergebnis des Zusammenfügens ist ein einzelnes Ausgabemedienobjekt, das das angegebene Video aus den Eingabemedienobjekten enthält. Die Videos, die zusammengefügt werden sollen, können in mehreren Medienobjekten oder in einem einzelnen Medienobjekt enthalten sein. Das Zusammenfügen wird von der Voreinstellungs-XML gesteuert, die an den Encoder übergeben wird. Eine vollständige Beschreibung des Voreinstellungsschemas finden Sie unter [Azure Media Encoder-Schemas](https://msdn.microsoft.com/library/azure/dn584702.aspx).

###Zusammenfügen mit dem Media Services Encoder

Das Zusammenfügen wird, wie in der folgenden Voreinstellung gezeigt, über das <MediaFile>-Element gesteuert:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Für jedes Video, das zusammengefügt werden soll, wird dem <Sources>-Element ein <Source>-Element hinzugefügt. Jedes <Source>-Element enthält ein <Clips>-Element. Jedes <Clips>-Element enthält ein oder mehrere <Clip>-Elemente, die angeben, welcher Anteil des Videos zum Ausgabemedienobjekt zusammengefügt wird. Dazu müssen eine Start- und Endzeit angegeben werden. Das <Source>-Element verweist auf das zu verwendende Medienobjekt. Das Format des Verweises ist davon abhängig, ob sich die Videos, die zusammengefügt werden sollen, in separaten Medienobjekten oder in einem einzelnen Medienobjekt befinden. Wenn Sie ein gesamtes Video zusammenfügen möchten, lassen Sie das <Clips>-Element einfach weg.

###Zusammenfügen von Videos aus mehreren Medienobjekten

Beim Zusammenfügen von Videos aus mehreren Medienobjekten wird ein nullbasierter Index für das MediaFile-Attribut des <Source>-Elements verwendet, um zu ermitteln, welchem Medienobjekt das <Source>-Element entspricht. Der Nullindex ist nicht angegeben, das <Source>-Element, das kein MediaFile-Attribut angibt, verweist auf das erste Eingabemedienobjekt. Alle anderen <Source>-Elemente müssen den nullbasierten Index des Eingabemedienobjekts, auf den es verweist, mithilfe einer %n%-Syntax angeben, wobei n der nullbasierte Index des Eingabemedienobjekts ist. Im vorherigen Beispiel gibt das erste <Source>-Element das erste Eingabemedienobjekt an, das zweite <Source>-Element gibt das zweite Eingabemedienobjekt an usw. Es ist nicht erforderlich, dass der Reihe nach auf die Eingabemedienobjekte verwiesen wird. Beispiel:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

In diesem Beispiel werden Teile des zweiten, des ersten und des dritten Eingabemedienobjekts zusammengefügt. Beachten Sie, dass es möglich ist, zwei Videos mit demselben Namen zusammenzufügen, da auf jedes Video durch einen nullbasierten Index verwiesen wird. Nach dem Erstellen einer Voreinstellungsdatei müssen Sie die folgenden Schritte ausführen:
 
- Hochladen von Medienobjekten
- Laden der Voreinstellungskonfiguration
- Erstellen eines Auftrags
- Abrufen eines Verweises auf den Media Services Encoder
- Erstellen einer Aufgabe durch Angabe der Eingabemedienobjekte, der Voreinstellungskonfiguration, des Media Encoders und des Ausgabemedienobjekts
- Übermitteln des Auftrags

Der folgende Codeausschnitt zeigt, wie Sie diese Schritte durchführen können:
	
	static public void StitchWithMultipleAssets()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
		
		// Upload assets to stitch
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
		
		// Load the preset configuration
		string presetFileName = "StitchingWithMultipleAssets.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
		} 


In diesem Codeausschnitt wird einfachheitshalber jedes Medienobjekt sequenziell geladen. In Produktionsumgebungen werden Medienobjekte in einem Massenladevorgang geladen. Weitere Informationen zum Hochladen mehrerer Medienobjekte in einem Massenladevorgang finden Sie unter [Sammelerfassung von Medienobjekten mit dem Media Services SDK für .NET](media-services-dotnet-upload-files.md#ingest_in_bulk). Den vollständigen Beispielcode finden Sie unter [Zusammenfügen mit Media Services Encoder](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203).

###Zusammenfügen von Videos aus einem Medienobjekt

Beim Zusammenfügen von Videos in einem einzelnen Medienobjekt muss jedes Video einen eindeutigen Namen haben. Die Videos werden mit dem MediaFile-Attribut angegeben, wobei der Dateiname als Wert des Attributs verwendet wird. Beispiel:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Diese Voreinstellung fügt Teile von video1.mp4, video2.wmv und video3.wmv im Ausgabemedienobjekt zusammen. Das Erstellen des Auftrags und der Aufgaben entspricht dem Zusammenfügen von Videos aus mehreren Medienobjekten; Sie müssen nur ein einzelnes Medienobjekt hochladen, wie im folgenden Code gezeigt:

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##Codieren von sprachlastigen Präsentationen oder Audiostreams
 
Wenn Sie Videos codieren, deren Audiospur sprachlastig ist, werden Hintergrundgeräusche im codierten Medienobjekt möglicherweise durch die standardmäßigen Encoder-Voreinstellungen verstärkt. Dies ist dann der Fall, wenn das NormalizeAudio-Attribut auf "true" festgelegt wurde.

###Codieren von sprachlastigen Präsentationen

Um eine Verstärkung der Hintergrundgeräusche zu verhindern, führen Sie folgende Schritte aus:

1. Kopieren Sie den Inhalt der Encoder-Voreinstellung in eine XML-Datei. Die Encoder-Voreinstellungen finden Sie unter „Azure Media Encoder-Schemas“.
1. Löschen Sie das NormalizeAudio-Attribut, das sich im oberen Bereich der Voreinstellungsdatei unter dem <MediaFile>-Element befindet:
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. Speichern Sie die geänderte Voreinstellungsdatei auf Ihrer lokalen Festplatte, und verwenden Sie einen Code wie den folgenden, um die Codierung mit diesen Voreinstellungen durchzuführen:
		
		// Upload file and create asset
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
		 
		string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml";
		string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
		 
		IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " +  presetName);
		
		Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
		
		// Get a media processor reference, and pass to it the name of the processor to use for the specific task.
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
		 
		// Read the configuration data into a string. 
		string configuration = File.ReadAllText(inputPresetFile);
		 
		// Create a task with the encoding details, using a string preset.
		ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration,
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
		 
		// Specify the input asset to be encoded.
		task.InputAssets.Add(asset);
		 
		// Add an output asset to contain the results of the job.
		task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
		 
		// Launch the job. 
		job.Submit();


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Weitere Informationen

[Azure Media Encoder XML-Schema](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=AcomDC_1210_2015-->