<properties pageTitle="Smooth Streaming-Plug-In für das Open Source Media-Framework" description="Erfahren Sie, wie Sie das Smooth Streaming-Plug-In von Azure Media Services für das Adobe Open Source Media-Framework verwenden." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>



# Verwenden des Microsoft Smooth Streaming-Plug-Ins für das Adobe Open Source Media-Framework #

## Übersicht ##
Das Microsoft Smooth Streaming-Plug-In für das Open Source Media-Framework 2.0 (SS für OSMF) erweitert die OSMF-Standardfunktionen und bietet Abspielfunktionen für Microsoft Smooth Streaming-Inhalte für neue und existierende OSMF-Player. Das Plug-In bietet außerdem Smooth Streaming-Wiedergabefähigkeiten für Strobe Media Playback (SMP).

SS für OSMF enthält zwei Versionen des Plug-Ins:

- Statisches Smooth Streaming-Plug-In für OSMF (.swc)

- Dynamisches Smooth Streaming-Plug-In für OSMF (.swf)

Dieses Dokument geht davon aus, dass die Leser über allgemeine Kenntnisse zu OSMF und OSMF-Plug-Ins verfügen. Weitere Informationen zu OSMF finden Sie in der [Dokumentation auf der offiziellen OSMF-Website](http://osmf.org/).

### Smooth Streaming-Plug-In für OSMF 2.0

Das Plug-In unterstützt Laden und Wiedergabe von bedarfsgesteuerten Smooth Streaming-Inhalten mit den folgenden Features:

- Bedarfsgesteuerte Smooth Streaming-Wiedergabe (Wiedergabe, Pause, Positionierung, Beenden)
- Live Smooth Streaming-Wiedergabe (Wiedergabe)
- Live DVR-Funktionen (Pause, Positionierung, DVR-Wiedergabe, Wechsel zu Live)
- Unterstützung für Videocodecs - H.264
- Unterstützung für Audiocodecs - AAC
- Umschalten zwischen mehreren Audiosprachen mit eingebauten OSMF-APIs
- Auswahl der maximalen Wiedergabequalität mit eingebauten OSMF-APIs
- Sidecar-Untertitel mit OSMF-Untertitel-Plug-In
- Adobe&reg; Flash&reg; Player 10.2 oder höher.
- Diese Version unterstützt nur OSMF 2.0.

Die folgenden Features werden nicht unterstützt:

- VC-1- und WMA-Codecs
- Inhaltsschutz (PlayReady)
- Text und Sparse Tracks
- Spezialwiedergabe (Zeitlupe, Vorlauf und Rücklauf)

Es folgt eine Liste der bekannten Probleme:

- Bei der Wiedergabe von Smooth Streaming-Inhalten mit 48KHz-Audiospuren treten Probleme auf. In der Flash-Runtime existiert ein bekanntes Problem mit dem Rendering von 48KHz-Audioinhalten. Aufgrund dieses Problems kann es sein, dass Smooth Streaming-Inhalte mit 48KHz-Einstellungen nicht wie gewünscht funktionieren. Siehe auch: [Verwenden von Flash Player](http://forums.adobe.com/message/4483498#4483498) und [Adobe Flash Player 11.3  -  Fehler 3210964](https://bugbase.adobe.com/index.cfm?event=bug&id=3210964).
- Bei der Wiedergabe mehrerer Smooth Streaming-Inhalte auf einer Seite können Probleme auftreten. Dies ist ein bekanntes Problem mit OSMF.
- Bei der Wiedergabe von Stage Video können Probleme auftreten, und auf manchen Maschinen wird kein Video angezeigt. Sie können Hardwarebeschleunigung oder Stage Video deaktivieren, um dieses Problem zu umgehen.

## Laden des Plug-Ins
OSMF-Plug-Ins können statisch (zur Kompilierzeit) oder dynamisch (zur Laufzeit) geladen werden. Die Downloadversion des Smooth Streaming-Plug-Ins für OSMF enthält sowohl die statische als auch die dynamische Version.

- Statisches Laden: Zum statischen Laden wird eine statische Bibliotheksdatei (SWC) benötigt. Statische Plug-Ins werden als Verweise zu Projekten hinzugefügt und zur Kompilierzeit in die Projekt-Ausgabedatei eingebunden.

- Dynamisches Laden: Zum dynamischen Laden wird eine vorkompilierte Datei (SWF) benötigt. Dynamische Plug-Ins werden in der Runtime geladen und sind nicht in der Projektausgabe enthalten. (Kompilierte Ausgabe) Dynamische Plug-Ins können mit den HTTP- und FILE-Protokollen geladen werden.

Weitere Informationen zum statischen und dynamischen Laden finden Sie auf der offiziellen [OSMF-Plug-In-Website](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### SS für OSMF statisches Laden
Der folgende Codeausschnitt zeigt, wie Sie das SS-Plug-In für OSMF statisch laden und ein einfaches Video mit der OSMF MediaFactory-Klasse abspielen können. Bevor Sie den SS für OSMF-Code hinzufügen, sollten Sie sicherstellen, dass das statische Plug-In "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" in den Projektverweisen enthalten ist.

<pre><code>
package 
{
	
	import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	
	
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		

		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;

			initMediaPlayer();

		}
	
		private function initMediaPlayer():void
		{
		
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			
			pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
			_mediaFactory.loadPlugin( pluginResource ); 
		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.
			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
		loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>


### SS für OSMF dynamisches Laden

Der folgende Codeausschnitt zeigt, wie Sie das SS-Plug-In für OSMF dynamisch laden und ein einfaches Video mit der OSMF MediaFactory-Klasse abspielen können. Bevor Sie den SS für OSMF-Code hinzufügen, sollten Sie das dynamische Plug-In "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" in den Projektordner kopieren, falls Sie das FILE-Protokoll verwenden möchten, bzw. in einen Webserver kopieren, falls Sie das HTTP-Protokoll verwenden möchten. "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" muss nicht in die Projektverweise eingebunden werden.

<pre><code>
package 
{
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	import flash.events.Event;
	import flash.system.Capabilities;

	
	//Sets the size of the SWF
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		
		
		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;
			initMediaPlayer();
		}
		
		private function initMediaPlayer():void
		{
			
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			var adaptiveStreamingPluginUrl:String;

			// Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

			adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 

		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.

			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>

## Strobe Media  Playback mit dem dynamischen SS ODMF-Plug-In
Das dynamische Smooth Streaming für OSMF-Plug-In unterstützt [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Sie können das Smooth Streaming für OSMF-Plug-In für die Smooth Streaming-Wiedergabe von SMP verwenden. Kopieren Sie dazu "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" in einen Webserver für die HTTP-Funktion, indem Sie die folgenden Schritte ausführen:

1.	Öffnen Sie die [Strobe Media Playback-Einrichtungsseite](http://osmf.org/dev/2.0gm/setup.html). 
2.	Wählen Sie eine Smooth Streaming-Quelle als Quelle, (z. B. http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.	Nehmen Sie die gewünschten Konfigurationsänderungen vor und klicken Sie auf Vorschau und Aktualisieren.
 
	**Hinweis** Ihr Webserver benötigt eine gültige crossdomain.xml-Datei. 
4.	Fügen Sie den folgenden Code mit einem Text-Editor Ihrer Wahl in eine einfache HTML-Seite ein, wie z. B. im folgenden Beispiel:



		<html>
		<body>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
		</embed>
		</object>
		</body>
		</html>



5. Fügen Sie das Smooth Streaming OSMF-Plug-In zum Einbindungscode hinzu und speichern Sie die Datei.

		<html>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
		</embed>
		</object>
		</html>


6. 	Speichern Sie Ihre HTML-Seite und veröffentlichen Sie die Seite auf einem Webserver. Öffnen Sie die veröffentlichte Webseite in einem Internetbrowser Ihrer Wahl, der den Flash&reg; Player unterstützt (Internet Explorer, Chrome, Firefox usw.).
7. 	Betrachten Sie den Smooth Streaming-Inhalt in Ihrem Adobe&reg; Flash&reg; Player.

Weitere allgemeine Informationen zu OSMF-Entwicklung finden Sie auf der offiziellen [OSMF-Entwicklungswebsite](http://osmf.org/resources.html).

<!--HONumber=42-->
