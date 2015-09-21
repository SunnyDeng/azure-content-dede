<properties 
	pageTitle="Codieren Ihrer Medien mit Dolby Digital Plus" 
	description="In diesem Thema wird beschrieben, wie Sie Medien mithilfe von Dolby Digital Plus codieren." 
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
	ms.date="09/07/2015"   
	ms.author="juliako"/>

#Codieren Ihrer Medien mit Dolby Digital Plus

Der Azure Media Encoder unterstützt die **Dolby® Digital Plus**-Codierung. Dolby® Digital Plus oder Enhanced AC-3 (E-AC-3) ist ein erweiterter Surround Sound-Audiocodec, der speziell für die neuen Medien entwickelt wurde. Dolby Digital Plus definiert die High-Fidelity-Audioqualität von Heimkino und PCs bis hin zu Mobiltelefonen und Onlinestreaming. Sie erhalten auf allen Mediengeräten immer das Dolby-Kinoerlebnis. Dolby Digital Plus basiert auf den wichtigsten Dolby Digital-Technologien, dem etablierten Standard für Kino, Rundfunk und Surround Sound für das Heimkino. Genau wie die mobilen Geräte einer ständigen Entwicklung unterliegen, entwickelt sich Dolby Digital Plus zum Standard für das mobile Entertainment. Die erweiterten neuen Audiotechnologien bieten eine bessere Soundqualität und zusätzliche Bandbreiteneinsparungen. Sie können qualitativ hochwertige Inhalte mit weniger Unterbrechungen abrufen, selbst wenn die Bandbreite eingeschränkt ist.


##Einrichten von Azure Media Encoder zum Codieren mit Dolby Digital Plus

###Abrufen des Azure Media Encoder-Prozessors 

Dolby Digital Plus wird vom Azure Media Encoder unterstützt. Informationen zum Abrufen eines Verweises auf den **Azure Media Encoder** finden Sie im Thema [Abrufen eines Medienprozessors](media-services-get-media-processor.md).

###<a id="configure_preset"></a>Konfigurieren von Azure Media Encoder-Einstellungen

Beim Konfigurieren der Codierungseinstellungen zur Verwendung mit Azure Media Encoder werden einige vordefinierte Einstellungen durch leicht einprägsame Zeichenfolgen repräsentiert. Der Dolby Digital Plus-Encoder stellt eine umfangreiche Auswahl an Steuerelementen bereit. Weiter Informationen hierzu erhalten Sie unter [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx). Es gibt daher keine vordefinierten Zeichenfolgeneinstellungen, die diesen Codec verwenden. Sie müssen die gewünschten Encoder-Einstellungen in einer XML-Datei angeben und diese Daten mit Ihrer Aufgabe wie im folgenden Codebeispiel gezeigt übermitteln:
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

Im vorliegenden Thema werden verschiedene XML-Voreinstellungen zum Konfigurieren der Encoder-Einstellungen beschrieben. Das Element zum Konfigurieren der Dolby Digital Plus-Codierung ist [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx). Hierbei handelt es sich um einen untergeordneten Knoten des Elements <AudioProfile> in einer Azure Media Encoder-XML-Voreinstellung. Dieses XML-Element enthält eine Reihe von Attributen, die verschiedene Elemente der Codierung steuern.

##Codieren in Dolby Digital Plus 5.1 Multichannel

Legen Sie zum Codieren in Dolby Digital Plus 5.1 Multichannel die Attribute "Codec" und "EncoderMode" auf "DolbyDigitalPlus" fest. Die Anzahl der codierten Kanäle wird mit dem AudioCodingMode-Attribut des <DolbyDigitalPlusAudioProfile>-Elements angegeben. Für eine 5.1 Multichannel-Codierung legen Sie "AudioCodingMode" auf "Mode32" fest.

Die folgende XML-Voreinstellung enthält eine vollständige Azure Media Encoder-XML-Voreinstellung, die eine MP4-Datei mit H264 Broadband 1080p-Video und Dolby Digital Plus 5.1 Multichannel-Audio erzeugt. Sie codiert außerdem einen LFE-Kanal (Low Frequency Effects), der durch Festlegen des Attributs "LFEOn" auf "true" angegeben wird. Für alle nicht angegebenen Attribute werden deren Standardwerte verwendet.

Diese XML-Voreinstellung sollte an den **Azure Media Encoder** übergeben werden, um wie [hier](media-services-dotnet-encode-asset.md) beschrieben einen Codierungsauftrag zu erstellen (anstelle einer vordefinierten Voreinstellungszeichenfolge übergeben Sie jedoch die komplette XML-Voreinstellung wie [hier](#configure_preset) beschrieben).


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codieren in Dolby Digital Plus Stereo

Legen Sie zum Codieren in Dolby Digital Plus Stereo die Attribute "Codec" und "EncoderMode" auf "DolbyDigitalPlus" fest. Die Anzahl der codierten Kanäle wird mit dem AudioCodingMode-Attribut angegeben. Für eine Stereocodierung legen Sie "AudioCodingMode" auf "Mode20" fest. Das folgende vordefinierte XML-Beispiel zeigt, wie <DolbyDigitalPlusAudioProfile> zur Codierung in 5.1-Audio verwendet wird. Für alle nicht angegebenen Attribute werden deren Standardwerte verwendet.

Diese XML-Voreinstellung sollte an den **Azure Media Encoder** übergeben werden, um wie [hier](media-services-dotnet-encode-asset.md) beschrieben einen Codierungsauftrag zu erstellen (anstelle einer vordefinierten Voreinstellungszeichenfolge übergeben Sie jedoch die komplette XML-Voreinstellung wie [hier](#configure_preset) beschrieben).

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codierung in mehrere MP4-Dateien 

Sie können die Codierung in mehrere MP4-Dateien in einer einzelnen XML-Voreinstellung ausführen. Fügen Sie für jede MP4-Datei, die generiert werden soll, ein Element <Preset> in die Konfiguration ein. Jedes Element <Preset> kann Konfigurationsinformationen für Video, Audio oder beides enthalten.

###Konfiguration

Die folgende Konfiguration erzeugt die folgenden Ausgaben:

- 8 Nur-Video-MP4-Dateien
	- 1080p Video mit 6000 KBit/s
	- 1080p Video mit 4700 KBit/s
	- 720p Video mit 3400 KBit/s
	- 960 x 540 Video mit 2250 KBit/s
	- 960 x 540 Video mit 1500 KBit/s
	- 640 x 380 Video mit 1000 KBit/s
	- 640 x 380 Video mit 650 KBit/s
	- 320 x 180 Video mit 400 KBit/s

- 5 Nur-Audio-MP4-Dateien
	- AAC Audio Stereo mit 128 KBit/s
	- AAC Audio 5.1 mit 512 KBit/s
	- Dolby Digital Plus Stereo mit 128 KBit/s
	- Dolby Digital Plus 5.1 Multichannel mit 512 KBit/s
	- AAC Stereo mit 56 KBit/s
- Ein ISM-Manifest
- Eine XML-Datei, welche die Eigenschaften der generierten MP4-Dateien auflistet.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##Erstellen kommerzieller Codierungsdienste

Einige Kunden möchten ggf. einen kommerziellen Codierungsdienst basierend auf Azure Media Services aufbauen. Wenn Sie einen solchen "aufbauenden" Dienst erstellen möchten, müssen unbedingt alle Dolby Digital Plus-Codierungsparameter verfügbar sein. Stellen Sie daher sicher, dass alle Parameter im Tag <DolbyDigitalPlusAudioProfile> für den Endbenutzer verfügbar und konfigurierbar sind. Wenden Sie sich an prolicensingsupport@dolby.com, wenn Sie Unterstützung beim Bereitstellen dieser Parameter benötigen.

##Verwenden von Dolby Professional Loudness Metering-Unterstützung (DPLM)

Der Azure Media Encoder kann das DPLM-SDK verwenden, um die Lautstärke von Dialogen in den Eingabeaudiodaten zu messen und den richtigen Wert für "DialogNormalization" festzulegen. Diese Funktion wird nur aktiviert, wenn die Audiodaten in Dolby Digital Plus codiert werden. DPLM wird in einer vordefinierten Konfigurationsdatei eingerichtet. Hierbei wird das Element <LoudnessMetering> verwendet, bei dem es sich um ein untergeordnetes Element des Elements <DolbyDigitalPlusAudioProfile> handelt. Das folgende Beispiel zeigt, wie DPLM konfiguriert wird:
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

Das Element <LoudnessMetering> kann nur in einem Element <DolbyDigitalPlusAudioProfile> angegeben werden. Wenn das Element <LoudnessMetering> verwendet wird, darf das Attribut "DialogNormalization" nicht verwendet werden. Der Encoder generiert einen Fehler, wenn das Element <LoudnessMetering> und das DialogNormalization-Attribut verwendet werden. Alle LoudnessMetering-Attribute sind optional, und der Encoder verwendet per Voreinstellung die von Dolby Laboratories, Inc. empfohlenen Werte.

Jedes Attribut wird in den folgenden Abschnitten beschrieben.

###Mode-Attribut

Dieses Attribut bestimmt den Loudness Metering-Modus. Zulässige Werte sind:

 
**ITU\_R\_BS\_1770\_2\_DI** (Standardwert): Gibt ITU-R BS.1770-2 und Dialogue Intelligence an

**ITU\_R\_BS\_1770\_1\_DI**: Gibt ITU-R BS.1770-1 und Dialogue Intelligence an

**ITU\_R\_BS\_1770\_2**: Gibt ITU-R BS.1770-2 an

**LEQA\_DI**: Gibt Leq(A) und Dialogue Intelligence an

**Hinweis:**

Der **EBU R128**-Modus kann mit **ITU\_R\_BS\_1770\_2\_DI** erzielt werden.

**Leq(A)** wurde aus Kompatibilitätsgründen aufgenommen und sollte nur in bestimmten älteren Workflows verwendet werden.

Die **ITU** hat vor kurzem eine Aktualisierung namens BS.1770-3 veröffentlicht, die BS.1770-2 mit der Einstellung "false" für "TruePeakDCBlock" und "TruePeakEmphasis" entspricht

###SpeechThreshold-Attribut

Gibt die von DPLM verwendete Sprachschwelle zur Erzeugung eines integrierten Lautheitergebnisses an (z. B. Auswahl zwischen Sprach-Gating, Lautstärke-Gating und kein Gating). Die Sprachschwelleneinstellung reicht von 0 % bis 100 % in Schritten von 1 %. Dieser Parameter ist nur dann wirksam, wenn DPLM in einem Modus konfiguriert ist, der Dialogue Intelligence verwendet. Dies bedeutet, er kann nur dann angegeben werden, wenn der Modus auf ITU\_R\_BS\_1770\_2\_DI oder ITU\_R\_BS\_1770\_1\_DI festgelegt ist. Der Standardwert ist 20 %, wenn der Modus ITU\_R\_BS\_1770\_2\_DI oder ITU\_R\_BS\_1770\_1\_DI lautet. Die Werte für dieses Attribut müssen im Bereich 0, 1 bis 100 festgelegt werden.

###TruePeakDCBlock-Attribut

Dieser Eingabeparameter gibt an, ob der DC-Block mit True Peak-Messung aktiviert (true) oder deaktiviert (false) ist. Ausführliche Informationen zum DC-Block finden Sie unter ITU‐R BS.1770‐2. Der Standardwert ist "false".

###TruePeakEmphasis-Attribut

Gibt an, ob der Verzerrungsfilter mit True Peak-Messung aktiviert (true) oder deaktiviert (false) ist. Ausführliche Informationen zum Verzerrungsfilter finden Sie unter ITU‐R BS.1770‐2. Der Standardwert ist "false".


###DPLM-Ergebnisse

Wenn eine Codierungsaufgabe die Verwendung von DPLM angibt, werden die Ergebnisse der Lautheitsmessung in die Metadaten-XML des Ausgabeobjekts einbezogen Dies ist ein Beispiel.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

Alle Attribute werden im Folgenden beschrieben.

**DPLMVersionInformation**: Eine Zeichenfolge, die die Version des verwendeten DPLM-SDKs darstellt.

**DialogNormalization**: Der für die Eingabeaudiodaten gemessene Wert von "DialNorm" (in Dezibel), der in den DD+-Ausgabedatenstrom eingebettet wird, im Bereich von {-31, -30, …, -1} dB.

**IntegratedLoudness**: Die von DPLM gemessene integrierte Lautstärke, im Bereich von -70 bis +10 LKFS/dBFS (wobei dBFS nur verwendet wird, wenn der Modus auf LEQA\_DI festgelegt ist).

**IntegratedLoudnessGatingMethod**: Gültige Werte sind: 0 – None/Ungated; 1 – Sprache Gated; 2 – Lautstärke-Gating.

**IntegratedLoudnessSpeechPercentage**: Dieses Ergebnis enthält den Prozentsatz der Zeitachse des Eingabemediums, bei dem Sprache erkannt wird. Die Werte liegen im Bereich von 0 % bis 100 %.

**SamplePeak**: Dieses Ergebnis enthält den größten absoluten Abtastwert in jedem Kanal, seit die Messung zurückgesetzt wurde, und liegt im Bereich von -70 bis +10 dBFS.

**TruePeak**: Dieses Ergebnis enthält den größten absoluten True Peak-Wert in jedem Kanal, seit die Messung zurückgesetzt wurde. Eine Beschreibung von True Peak finden Sie unter ITU‐R BS.1770‐2. Die Werte liegen im Bereich von -70 bis 12.04 dBTP.
 

##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

<!---HONumber=Sept15_HO2-->