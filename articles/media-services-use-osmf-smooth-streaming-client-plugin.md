<properties linkid="develop-media-services-tutorials-smooth-streaming-plugin-for-open-source-media-framework" urlDisplayName="Smooth Streaming Plugin" pageTitle="Smooth Streaming Plugin for the Open Source Media Framework" metaKeywords="" description="Learn how to use the Azure Media Services Smooth Streaming plugin for the Adobe Open Source Media Framework." metaCanonical="" services="media-services" documentationCenter="" title="How to Use the Microsoft Smooth Streaming Plugin for the Adobe Open Source Media Framework" authors="" solutions="" manager="" editor="" />

Verwenden des Microsoft Smooth Streaming-Plug-Ins für das Adobe Open Source Media-Framework
===========================================================================================

Übersicht
---------

Das Microsoft Smooth Streaming-Plug-In für das Open Source Media-Framework 2.0 (SS für OSMF) erweitert die OSMF-Standardfunktionen und bietet Abspielfunktionen für Microsoft Smooth Streaming-Inhalte für neue und existierende OSMF-Player. Das Plug-In bietet außerdem Smooth Streaming-Wiedergabefähigkeiten für Strobe Media Playback (SMP).

SS für OSMF enthält zwei Versionen des Plug-Ins:

-   Statisches Smooth Streaming-Plug-In für OSMF (.swc)

-   Dynamisches Smooth Streaming-Plug-In für OSMF (.swf)

Dieses Dokument geht davon aus, dass die Leser über allgemeine Kenntnisse zu OSMF und OSMF-Plug-Ins verfügen. Weitere Informationen zu OSMF finden Sie in der Dokumentation auf der [offiziellen OSMF-Website](http://osmf.org/).

### Smooth Streaming-Plug-In für OSMF 2.0

Das Plug-In unterstützt Laden und Wiedergabe von bedarfsgesteuerten Smooth Streaming-Inhalten mit den folgenden Features:

-   Bedarfsgesteuerte Smooth Streaming-Wiedergabe (Wiedergabe, Pause, Positionierung, Beenden)
-   Live Smooth Streaming-Wiedergabe (Wiedergabe)
-   Live DVR-Funktionen (Pause, Positionierung, DVR-Wiedergabe, Wechsel zu Live)
-   Unterstützung für Videocodecs - H.264
-   Unterstützung für Audiocodecs - AAC
-   Umschalten zwischen mehreren Audiosprachen mit eingebauten OSMF-APIs
-   Auswahl der maximalen Wiedergabequalität mit eingebauten OSMF-APIs
-   Sidecar-Untertitel mit OSMF-Untertitel-Plug-In
-   AdobeÂ® FlashÂ® Player 10.2 oder höher.
-   Diese Version unterstützt nur OSMF 2.0.

Die folgenden Features werden nicht unterstützt:

-   VC-1- und WMA-Codecs
-   Inhaltsschutz (PlayReady)
-   Text und Sparse Tracks
-   Spezialwiedergabe (Zeitlupe, Vorlauf und Rücklauf)

Es folgt eine Liste der bekannten Probleme:

-   Bei der Wiedergabe von Smooth Streaming-Inhalten mit 48KHz-Audiospuren treten Probleme auf. In der Flash-Runtime existiert ein bekanntes Problem mit dem Rendering von 48KHz-Audioinhalten. Aufgrund dieses Problems kann es sein, dass Smooth Streaming-Inhalte mit 48KHz-Einstellungen nicht wie gewünscht funktionieren. Siehe auch: [Verwenden von Flash Player](http://forums.adobe.com/message/4483498#4483498) und [Adobe Flash Player 11.3 - Bug 3210964](https://bugbase.adobe.com/index.cfm?event=bug&id=3210964) für weitere Informationen.
-   Bei der Wiedergabe mehrerer Smooth Streaming-Inhalte auf einer Seite können Probleme auftreten. Dies ist ein bekanntes Problem mit OSMF.
-   Bei der Wiedergabe von Stage Video können Probleme auftreten, und auf manchen Maschinen wird kein Video angezeigt. Sie können Hardwarebeschleunigung oder Stage Video deaktivieren, um dieses Problem zu umgehen.

Laden des Plug-Ins
------------------

OSMF-Plug-Ins können statisch (zur Kompilierzeit) oder dynamisch (zur Laufzeit) geladen werden. Die Downloadversion des Smooth Streaming-Plug-Ins für OSMF enthält sowohl die statische als auch die dynamische Version.

-   Statisches Laden: Zum statischen Laden wird eine statische Bibliotheksdatei (SWC) benötigt. Statische Plug-Ins werden als Verweise zu Projekten hinzugefügt und zur Kompilierzeit in die Projekt-Ausgabedatei eingebunden.

-   Dynamisches Laden: Zum dynamischen Laden wird eine vorkompilierte Datei (SWF) benötigt. Dynamische Plug-Ins werden in der Runtime geladen und sind nicht in der Projektausgabe enthalten. (Kompilierte Ausgabe) Dynamische Plug-Ins können mit den HTTP- und FILE-Protokollen geladen werden.

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
			// Container (Sprite) für Anzeige- und Layoutverwaltung erstellen
		    _mediaPlayerSprite = new MediaPlayerSprite();
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);

			// mediafactory-Instanz erstellen
			_mediaFactory = new DefaultMediaFactory();
			
			// Listener für PLUGIN_LOADING hinzufügen
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

			// Plug-In-Klasse laden
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
			// Plug-In wurde erfolgreich geladen.
            // Ihr Webserver muss eine gültige crossdomain.xml-Datei hosten, um dem Plug-In den Download von Smooth Streaming-Dateien zu ermöglichen.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

		}

		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
			// Fehler beim Laden des Plug-Ins ...
        }


		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;

			switch (state)
			{
				case MediaPlayerState.LOADING: 
                    
                    // Neue Quelle wird geladen.
                    
                    break;
             		
				case  MediaPlayerState.READY :   
                    // Code für den Bereitschaftszustand des Players beim ersten Nachladen, nachdem eine Quelle geladen wurde. 
                    
                    break;

                case MediaPlayerState.BUFFERING :
                    
                    break;

				case  MediaPlayerState.PAUSED :
                    
					break;
				// sonstige Zustände ...          
            }
		}

		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Fehler im Media Player.           
        }

		private function loadMediaSource(sourceURL : String):void
		{
			// URL des SmoothStreamingSource-Manifests zur Seite hinzufügen.
            
            var resource:URLResource= new URLResource( sourceURL );

			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;

			// Medienelement hinzufügen
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


	//SWF-Größe angeben

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
			
			// Container (Sprite) für Anzeige- und Layoutverwaltung erstellen
			_mediaPlayerSprite = new MediaPlayerSprite();
		    _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			
			//Container hinzufügen
            addChild(_mediaPlayerSprite);

            // mediafactory-Instanz erstellen
			_mediaFactory = new DefaultMediaFactory();

			// Listener für PLUGIN_LOADING hinzufügen
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
			// Plug-In-Klasse laden
			loadAdaptiveStreamingPlugin( );

		}

		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

			// Ihr dynamischer Plug-In-Webserver muss eine gültige crossdomain.xml-Datei hosten, um das Laden von Plug-Ins zu ermöglichen.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 
		}

		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// Plug-In wurde erfolgreich geladen.

            // Ihr Webserver muss eine gültige crossdomain.xml-Datei hosten, um dem Plug-In den Download von Smooth Streaming-Dateien zu ermöglichen.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}

		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// Fehler beim Laden des Plug-Ins ...
        }

		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;

			state =  event.state;

			switch (state)
			{
				case MediaPlayerState.LOADING: 
                    
                    // Neue Quelle wird geladen.
                    
                    break;
				case  MediaPlayerState.READY :   
                    // Code für den Bereitschaftszustand des Players beim ersten Nachladen, nachdem eine Quelle geladen wurde. 
                    
               		break;

				case MediaPlayerState.BUFFERING :
                    
                    break;
				case  MediaPlayerState.PAUSED :
                    
					break;
				// sonstige Zustände ...          
            }
		}

		private function onPlayerFailed(event:MediaErrorEvent) : void
	    {
			// Fehler im Media Player.           
        }

		private function loadMediaSource(sourceURL : String):void
	    {
			// URL des SmoothStreamingSource-Manifests zur Seite hinzufügen.
            
            var resource:URLResource= new URLResource( sourceURL );

			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Medienelement hinzufügen
			_mediaPlayerSprite.media = element;
		}

	}
}
</code></pre>

Strobe Media Playback mit dem dynamischen SS ODMF-Plug-In
---------------------------------------------------------

Das dynamische Smooth Streaming für OSMF-Plug-In unterstützt [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Sie können das Smooth Streaming für OSMF-Plug-In für die Smooth Streaming-Wiedergabe von SMP verwenden. Kopieren Sie dazu "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" in einen Webserver für die HTTP-Funktion, indem Sie die folgenden Schritte ausführen:

1.  Öffnen Sie die [Strobe Media Playback-Einrichtungsseite](http://osmf.org/dev/2.0gm/setup.html).
2.  Wählen Sie eine Smooth Streaming-Quelle als Quelle, (z. B. http://devplatem.vo.msecnd.net/Sintel/Sintel\_H264.ism/manifest)
3.  Nehmen Sie die gewünschten Konfigurationsänderungen vor und klicken Sie auf Vorschau und Aktualisieren.

    **Hinweis** Ihr Webserver benötigt eine gültige crossdomain.xml-Datei.

4.  Fügen Sie den folgenden Code mit einem Text-Editor Ihrer Wahl in eine einfache HTML-Seite ein, wie z. B. im folgenden Beispiel:

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

5.  Fügen Sie das Smooth Streaming OSMF-Plug-In zum Einbindungscode hinzu und speichern Sie die Datei.

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

6.  Speichern Sie Ihre HTML-Seite und veröffentlichen Sie die Seite auf einem Webserver. Öffnen Sie die veröffentlichte Webseite mit einem FlashÂ® Player-fähigen Internetbrowser Ihrer Wahl (Internet Explorer, Chrome, Firefox usw).
7.  Testen Sie die Smooth Streaming-Inhalte im AdobeÂ® FlashÂ® Player.

Weitere allgemeine Informationen zu OSMF-Entwicklung finden Sie auf der offiziellen [OSMF-Entwicklungswebsite](http://osmf.org/resources.html).

