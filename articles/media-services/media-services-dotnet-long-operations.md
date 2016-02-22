<properties 
	pageTitle="Abrufen von Vorgängen mit langen Laufzeiten" 
	description="In diesem Thema wird gezeigt, wie Vorgänge mit langen Laufzeiten abgerufen werden." 
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
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Bereitstellen von Livestreaming mit Azure Media Services

##Übersicht

Microsoft Azure Media Services bietet APIs, die Anforderungen zum Starten von Vorgängen an Media Services senden (beispielsweise zum Erstellen, Starten, Beenden oder Löschen eines Kanals). Diese Vorgänge haben eine lange Laufzeit.

Das Media Services .NET SDK stellt APIs bereit, die die Anforderung senden und auf den Abschluss des Vorgangs warten (intern fragen die APIs in regelmäßigen Intervallen den Vorgangsfortschritt ab). Wenn Sie beispielsweise "channel.Start()" aufrufen, wird die Methode nach dem Start des Kanals zurückgegeben. Sie können auch die asynchrone Version "await channel.StartAsync()" verwenden. (Informationen zu aufgabenbasierten asynchronen Mustern finden Sie unter [TAP](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx). APIs, die eine Vorgangsanforderung senden und den Status abfragen, bis der Vorgang abgeschlossen ist, werden als "Abfragemethoden" bezeichnet. Diese Methoden (insbesondere die asynchrone Version) werden für Rich Client-Anwendungen und/oder für zustandsbehaftete Dienste empfohlen.

Es gibt Szenarien, in denen eine Anwendung nicht auf eine HTTP-Anforderung mit langer Laufzeit warten kann und der Vorgangsfortschritt manuell abgerufen werden soll. Ein typisches Beispiel wäre die Interaktion mit einem zustandsloser Webdienst: Wenn der Browser die Erstellung eines Kanals anfordert, startet der Webdienst einen Vorgang mit langer Laufzeit und gibt die Vorgangs-ID an den Browser zurück. Der Browser kann dann beim Webdienst den Abruf des Vorgangsstatus basierend auf der ID anfordern. Das Media Services .NET SDK stellt APIs bereit, die für dieses Szenario von Nutzen sind. Diese APIs werden als "Methoden ohne Abfrage" bezeichnet. Methoden ohne Abfrage weisen das folgende Namensmuster auf: Send*Vorgangsname*Operation (z. B. SendCreateOperation). Send*Vorgangsname*Operation-Methoden geben das **IOperation**-Objekt zurück. Das zurückgegebene Objekt enthält Informationen, die zum Nachverfolgen des Vorgangs verwendet werden können. Die Send*Vorgangsname*OperationAsync-Methoden geben **Task<IOperation>** zurück.

Derzeit unterstützen die folgenden Klassen Methoden ohne Abfrage: **Channel**, **StreamingEndpoint** und **Program**.

Verwenden Sie zum Abfragen des Vorgangsstatus die **GetOperation**-Methode in der **OperationBaseCollection**-Klasse. Verwenden Sie die folgenden Intervalle, um den Vorgangsstatus abzufragen: für **Channel**- und **StreamingEndpoint**-Vorgänge 30 Sekunden, für **Program**-Vorgänge 10 Sekunden.


##Beispiel

Das folgende Beispiel definiert eine Klasse mit dem Namen **ChannelOperations**. Diese Klassendefinition könnte als Startpunkt für die Klassendefinition Ihres Webdiensts verwendet werden. Der Einfachheit halber werden in den folgenden Beispielen die nicht asynchronen Versionen der Methoden verwendet.

Das Beispiel zeigt außerdem, wie der Client diese Klasse verwenden kann.

###Definition der ChannelOperations-Klasse

	/// <summary> 
	/// The ChannelOperations class only implements 
	/// the Channel’s creation operation. 
	/// </summary> 
	public class ChannelOperations
	{
	    // Read values from the App.config file.
	    private static readonly string _mediaServicesAccountName =
	        ConfigurationManager.AppSettings["MediaServicesAccountName"];
	    private static readonly string _mediaServicesAccountKey =
	        ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	    // Field for service context.
	    private static CloudMediaContext _context = null;
	    private static MediaServicesCredentials _cachedCredentials = null;
	
	    public ChannelOperations()
	    {
	            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	            _context = new CloudMediaContext(_cachedCredentials);    }
	
	    /// <summary>  
	    /// Initiates the creation of a new channel.  
	    /// </summary>  
	    /// <param name="channelName">Name to be given to the new channel</param>  
	    /// <returns>  
	    /// Operation Id for the long running operation being executed by Media Services. 
	    /// Use this operation Id to poll for the channel creation status. 
	    /// </returns> 
	    public string StartChannelCreation(string channelName)
	    {
	        var operation = _context.Channels.SendCreateOperation(
	            new ChannelCreationOptions
	            {
	                Name = channelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Output = CreateChannelOutput()
	            });
	
	        return operation.Id;
	    }
	
	    /// <summary> 
	    /// Checks if the operation has been completed. 
	    /// If the operation succeeded, the created channel Id is returned in the out parameter.
	    /// </summary> 
	    /// <param name="operationId">The operation Id.</param> 
	    /// <param name="channel">
	    /// If the operation succeeded, 
	    /// the created channel Id is returned in the out parameter.</param>
	    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	    public bool IsCompleted(string operationId, out string channelId)
	    {
	        IOperation operation = _context.Operations.GetOperation(operationId);
	        bool completed = false;
	
	        channelId = null;
	
	        switch (operation.State)
	        {
	            case OperationState.Failed:
	                // Handle the failure. 
	                // For example, throw an exception. 
					// Use the following information in the exception: operationId, operation.ErrorMessage.
	                break;
	            case OperationState.Succeeded:
	                completed = true;
	                channelId = operation.TargetEntityId;
	                break;
	            case OperationState.InProgress:
	                completed = false;
	                break;
	        }
	        return completed;
	    }
	
	
	    private static ChannelInput CreateChannelInput()
	    {
	        return new ChannelInput
	        {
	            StreamingProtocol = StreamingProtocol.RTMP,
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
	
	    private static ChannelOutput CreateChannelOutput()
	    {
	        return new ChannelOutput
	        {
	            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
	        };
	    }
	}

###Der Clientcode

	ChannelOperations channelOperations = new ChannelOperations();
	string opId = channelOperations.StartChannelCreation("MyChannel001");
	
	string channelId = null;
	bool isCompleted = false;
	
	while (isCompleted == false)
	{
	    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	    isCompleted = channelOperations.IsCompleted(opId, out channelId);
	}
	
	// If we got here, we should have the newly created channel id.
	Console.WriteLine(channelId);
 


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->