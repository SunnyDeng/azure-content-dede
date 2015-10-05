<properties 
	pageTitle="Verwenden von .NET SDK, um Kanäle zu erstellen, von denen eine Livecodierung von Single-Bitrate- zu Multi-Bitrate-Datenströmen vorgenommen wird" 
	description="In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines Kanals geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Verwenden von .NET SDK, um Kanäle zu erstellen, von denen eine Livecodierung von Single-Bitrate- zu Multi-Bitrate-Datenströmen vorgenommen wird (Vorschau)

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

##Übersicht

In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines **Kanals** geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird.

>[AZURE.NOTE]Weitere konzeptuelle Informationen zu Kanälen, bei denen Livecodierung aktiviert ist, finden Sie unter [Arbeiten mit Kanälen, von denen Livecodierung von Single-Bitrate- in Multi-Bitrate-Livedatenströme ausgeführt wird](media-services-manage-live-encoder-enabled-channels.md).

>[AZURE.NOTE]Sie müssen das Media Services .NET SDK, Version 3.2.0.0 oder höher, verwenden.

##Allgemeines Livestreamingszenario

Im Folgenden werden die Schritte und Aufgaben zum Erstellen allgemeiner Livestreaminganwendungen beschrieben.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Liveencoder, von dem ein Single-Bitrate-Datenstrom in einem der folgenden Protokolle ausgegeben wird: RTMP, Smooth Streaming oder RTP (MPEG-TS). Weitere Informationen finden Sie unter [Windows Azure Media Services RTMP-Support und Liveencoder](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.

1. Erstellen Sie einen Kanal, und starten Sie ihn.

1. Rufen Sie die Erfassungs-URL des Kanals ab.

	Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.
1. Rufen Sie die Vorschau-URL des Kanals ab. 

	Verwenden Sie diese URL, um sicherzustellen, dass der Livedatenstrom ordnungsgemäß vom Kanal empfangen wird.

2. Erstellen Sie ein Medienobjekt.
3. Wenn das Medienobjekt während der Wiedergabe dynamisch verschlüsselt werden soll, führen Sie folgende Schritte aus: 	
	
	1. 	Erstellen Sie einen Inhaltsschlüssel. 
	1. 	Konfigurieren Sie eine Autorisierungsrichtlinie für Inhaltsschlüssel.
1. Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte (wird zur dynamischen Paketerstellung und zur dynamischen Verschlüsselung verwendet).
3. Erstellen Sie ein Programm, und legen Sie fest, dass das erstellte Medienobjekt verwendet werden soll.
1. Veröffentlichen Sie das dem Programm zugeordnete Medienobjekt, indem Sie einen OnDemand-Locator erstellen.  

	Stellen Sie sicher, dass auf dem Streamingendpunkt, von dem Sie Inhalte streamen möchten, mindestens eine für das Streaming reservierte Einheit verfügbar ist.
1. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm.
2. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
1. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
1. Löschen Sie das Programm (und optional das Medienobjekt).   

##In diesem Thema

In diesem Thema wird erläutert, wie mithilfe von Media Services .NET SDK verschiedene Vorgänge bei Kanälen und Programmen ausgeführt werden. Da viele Vorgänge langfristiger Art sind, werden .NET APIs verwendet, von denen langfristige Vorgänge verwaltet werden.

In diesem Thema erfahren Sie, wie Sie die folgenden Aufgaben ausführen:

1. Erstellen Sie einen Kanal, und starten Sie ihn. Verwenden Sie langfristige APIs.
1. Erfassen Sie den Eingabeendpunkt der Kanäle. Dieser Endpunkt muss dem Encoder bereitgestellt werden, von dem ein Single-Bitrate-Livedatenstrom gesendet wird.
1. Rufen Sie den Vorschauendpunkt ab. Dieser Endpunkt wird verwendet, um Ihren Datenstrom in der Vorschau anzuzeigen. 
1. Erstellen Sie ein Medienobjekt, das zum Speichern Ihrer Inhalte verwendet wird. Konfigurieren Sie außerdem Übermittlungsrichtlinien für Medienobjekte wie in diesem Beispiel gezeigt.
1. Erstellen Sie ein Programm, und legen Sie fest, dass das zuvor erstellte Medienobjekt verwendet werden soll. Starten Sie das Programm. Verwenden Sie langfristige APIs.
1. Erstellen Sie einen Locator für das Medienobjekt, damit der Inhalt veröffentlicht wird und an Ihre Kunden gestreamt werden kann.
1. Blenden Sie Slates ein und aus. Starten und Beenden Sie Werbespots. Verwenden Sie langfristige APIs.
1. Bereinigen Sie den Kanal und alle zugehörigen Ressourcen.

>[AZURE.NOTE]Dieses Feature befindet sich in der Vorschauphase. Die maximale empfohlene Dauer einer Live-Veranstaltung ist 8 Stunden.
>
##Voraussetzungen
Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

- Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](azure.microsoft.com).
- Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Konto erstellen](media-services-create-account.md).
- Sie benötigen Visual Studio 2010 SP1 oder höher.
- Sie benötigen eine Webcam und einen Encoder, von dem ein Single-Bitrate-Livedatenstrom gesendet wird.

##Vorbereiten der Entwicklung mit Media Services SDK für .NET
 
1. Erstellen Sie mithilfe von Visual Studio eine Konsolenanwendung.
1. Fügen Sie Ihrer Konsolenanwendung mithilfe des Media Services NuGet-Pakets das Media Services SDK für .NET hinzu.

##Verbinden mit Mediendiensten
Als bewährte Methode sollten Sie eine app.config-Datei zum Speichern von Namen und Kontoschlüssel von Media Services verwenden.

>[AZURE.NOTE]Sie finden Werte für Namen und Schlüssel im Azure-Portal: Wählen Sie Ihr Media Services-Konto aus, und klicken Sie unten im Portalfenster auf das Symbol SCHLÜSSEL VERWALTEN. Klicken Sie auf die Symbole neben den Textfeldern, um den jeweiligen Wert in die Systemzwischenablage zu kopieren.
 
Fügen Sie der app.config-Datei den Bereich „appSettings“ hinzu, und geben Sie Ihren Kontonamen und Kontoschlüssel für Media Services ein.


	<?xml version="1.0"?>
	<configuration>
	  <appSettings>
	      <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
	      <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
	  </appSettings>
	</configuration>
	 
	

##Codebeispiel

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Newtonsoft.Json.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        private const string ChannelName = "channel001";
	        private const string AssetlName = "asset001";
	        private const string ProgramlName = "program001";
	
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
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            IChannel channel = CreateAndStartChannel();
	
	            // The channel's input endpoint:
	            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Intest URL: {0}", ingestUrl);
	
	
	            // Use the previewEndpoint to preview and verify 
	            // that the input from the encoder is actually reaching the Channel. 
	            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Preview URL: {0}", previewEndpoint);
	
	            // Get a thumbnail preview of a live feed.
	            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
	            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
	
	            string thumbnailUri = new UriBuilder
	            {
	                Scheme = Uri.UriSchemeHttps,
	                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
	                Path = "thumbnails/input.jpg"
	            }.Uri.ToString();
	
	            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
	
	            // Once you previewed your stream and verified that it is flowing into your Channel, 
	            // you can create an event by creating an Asset, Program, and Streaming Locator. 
	            IAsset asset = CreateAndConfigureAsset();
	
	            IProgram program = CreateAndStartProgram(channel, asset);
	
	            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
	
	            // You can use slates and ads only if the channel type is Standard.  
	            StartStopAdsSlates(channel);
	
	            // Once you are done streaming, clean up your resources.
	            Cleanup(channel);
	
	        }
	
	        public static IChannel CreateAndStartChannel()
	        {
	            ChannelCreationOptions options = new ChannelCreationOptions
	            {
	                EncodingType = ChannelEncodingType.Standard,
	                Name = ChannelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Encoding = CreateChannelEncoding()
	            };
	
	            Log("Creating channel");
	            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
	            string channelId = TrackOperation(channelCreateOperation, "Channel create");
	
	            IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
	
	            Log("Starting channel");
	            var channelStartOperation = channel.SendStartOperation();
	            TrackOperation(channelStartOperation, "Channel start");
	
	            return channel;
	        }
	
	        /// <summary>
	        /// Create channel input, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelInput CreateChannelInput()
	        {
	            return new ChannelInput
	            {
	                StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelInput001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel preview, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelPreview CreateChannelPreview()
	        {
	            return new ChannelPreview
	            {
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelPreview001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel encoding, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelEncoding CreateChannelEncoding()
	        {
	            return new ChannelEncoding
	            {
	                SystemPreset = "Default720p",
	                IgnoreCea708ClosedCaptions = false,
	                AdMarkerSource = AdMarkerSource.Api,
	                // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
	                AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
	            };
	        }
	
	        /// <summary>
	        /// Create an asset and configure asset delivery policies.
	        /// </summary>
	        /// <returns></returns>
	        public static IAsset CreateAndConfigureAsset()
	        {
	            IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
	
	            IAssetDeliveryPolicy policy =
	                _context.AssetDeliveryPolicies.Create("Clear Policy",
	                AssetDeliveryPolicyType.NoDynamicEncryption,
	                AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
	
	            asset.DeliveryPolicies.Add(policy);
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
	        /// however each Program must have a unique name within your Media Services account.
	        /// </summary>
	        /// <param name="channel"></param>
	        /// <param name="asset"></param>
	        /// <returns></returns>
	        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
	        {
	            IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
	            Log("Program created", program.Id);
	
	            Log("Starting program");
	            var programStartOperation = program.SendStartOperation();
	            TrackOperation(programStartOperation, "Program start");
	
	            return program;
	        }
	
	        /// <summary>
	        /// Create locators in order to be able to publish and stream the video.
	        /// </summary>
	        /// <param name="asset"></param>
	        /// <param name="ArchiveWindowLength"></param>
	        /// <returns></returns>
	        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
	        {
	            var locator = _context.Locators.CreateLocator
	                (
	                    LocatorType.OnDemandOrigin,
	                    asset,
	                    _context.AccessPolicies.Create
	                        (
	                            "Live Stream Policy",
	                            ArchiveWindowLength,
	                            AccessPermissions.Read
	                        )
	                );
	
	            return locator;
	        }
	
	        /// <summary>
	        /// Perform operations on slates.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void StartStopAdsSlates(IChannel channel)
	        {
	            Log("Creating asset");
	            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
	            Log("Slate asset created", slateAsset.Id);
	
	            Log("Uploading file");
	            var assetFile = slateAsset.AssetFiles.Create("SlateTest.jpg");
	            assetFile.Upload("SlateTest.jpg");
	            assetFile.IsPrimary = true;
	            assetFile.Update();
	
	            Log("Showing slate");
	            var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
	            TrackOperation(showSlateOpeartion, "Show slate");
	
	            Log("Hiding slate");
	            var hideSlateOperation = channel.SendHideSlateOperation();
	            TrackOperation(hideSlateOperation, "Hide slate");
	
	            Log("Starting ad");
	            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), 0, false);
	            TrackOperation(startAdOperation, "Start ad");
	
	            Log("Ending ad");
	            var endAdOperation = channel.SendEndAdvertisementOperation();
	            TrackOperation(endAdOperation, "End ad");
	
	            Log("Deleting slate asset");
	            slateAsset.Delete();
	        }
	
	        /// <summary>
	        /// Clean up resources associated with the channel.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void Cleanup(IChannel channel)
	        {
	            IAsset asset;
	            if (channel != null)
	            {
	                foreach (var program in channel.Programs)
	                {
	                    asset = _context.Assets.Where(se => se.Id == program.AssetId)
	                                            .FirstOrDefault();
	
	                    Log("Stopping program");
	                    var programStopOperation = program.SendStopOperation();
	                    TrackOperation(programStopOperation, "Program stop");
	
	                    program.Delete();
	
	                    if (asset != null)
	                    {
	                        Log("Deleting locators");
	                        foreach (var l in asset.Locators)
	                            l.Delete();
	
	                        Log("Deleting asset");
	                        asset.Delete();
	                    }
	                }
	
	                Log("Stopping channel");
	                var channelStopOperation = channel.SendStopOperation();
	                TrackOperation(channelStopOperation, "Channel stop");
	
	                Log("Deleting channel");
	                var channelDeleteOperation = channel.SendDeleteOperation();
	                TrackOperation(channelDeleteOperation, "Channel delete");
	            }
	        }
	
	
	        /// <summary>
	        /// Track long running operations.
	        /// </summary>
	        /// <param name="operation"></param>
	        /// <param name="description"></param>
	        /// <returns></returns>
	        public static string TrackOperation(IOperation operation, string description)
	        {
	            string entityId = null;
	            bool isCompleted = false;
	
	            Log("starting to track ", null, operation.Id);
	            while (isCompleted == false)
	            {
	                operation = _context.Operations.GetOperation(operation.Id);
	                isCompleted = IsCompleted(operation, out entityId);
	                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	            }
	            // If we got here, the operation succeeded.
	            Log(description + " in completed", operation.TargetEntityId, operation.Id);
	
	            return entityId;
	        }
	
	        /// <summary> 
	        /// Checks if the operation has been completed. 
	        /// If the operation succeeded, the created entity Id is returned in the out parameter.
	        /// </summary> 
	        /// <param name="operationId">The operation Id.</param> 
	        /// <param name="channel">
	        /// If the operation succeeded, 
	        /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
	        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	        private static bool IsCompleted(IOperation operation, out string entityId)
	        {
	
	            bool completed = false;
	
	            entityId = null;
	
	            switch (operation.State)
	            {
	                case OperationState.Failed:
	                    // Handle the failure. 
	                    // For example, throw an exception. 
	                    // Use the following information in the exception: operationId, operation.ErrorMessage.
	                    Log("operation failed", operation.TargetEntityId, operation.Id);
	                    break;
	                case OperationState.Succeeded:
	                    completed = true;
	                    entityId = operation.TargetEntityId;
	                    break;
	                case OperationState.InProgress:
	                    completed = false;
	                    Log("operation in progress", operation.TargetEntityId, operation.Id);
	                    break;
	            }
	            return completed;
	        }
	
	
	        private static void Log(string action, string entityId = null, string operationId = null)
	        {
	            Console.WriteLine(
	                "{0,-21}{1,-51}{2,-51}{3,-51}",
	                DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
	                action,
	                entityId ?? string.Empty,
	                operationId ?? string.Empty);
	        }
	    }
	}
	



##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

 

<!---HONumber=Sept15_HO2-->