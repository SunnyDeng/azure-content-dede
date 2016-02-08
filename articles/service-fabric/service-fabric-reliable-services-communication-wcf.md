<properties
   pageTitle="Zuverlässige WCF-Dienste Kommunikationsstapel von Reliable Services | Microsoft Azure"
   description="Der integrierte WCF-Kommunikationsstapel in Service Fabric bietet Clientdienst-WCF-Kommunikation für Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# WCF-basierter Kommunikationsstapel für Reliable Services
Das Reliable Services-Framework ermöglicht Dienstautoren, den Kommunikationsstapel für ihren Dienst zu wählen. Sie können den Kommunikationsstapel ihrer Wahl über den von der [CreateServiceReplicaListeners- oder CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md)-Methode zurückgegebenen **ICommunicationListener** implementieren. Das Framework bietet eine Implementierung des auf WCF (Windows Communication Foundation) basierenden Kommunikationsstapels für Dienstautoren, die eine WCF-basierte Kommunikation verwenden möchten.

## WCF-Kommunikationslistener
Die WCF-spezifische Implementierung von **ICommunicationListener** erfolgt über die **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**-Klasse.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(ServiceInitializationParameters,typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## Schreiben von Clients für den WCF-Kommunikationsstapel
Für das Schreiben von Clients, die über WCF mit Diensten kommunizieren, bietet das Framework **WcfClientCommunicationFactory**. Dies ist die spezielle WCF-Implementierung von [ClientCommunicationFactoryBase](service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Der WCF-Kommunikationskanal ist vom **WcfCommunicationClient** aus zugänglich, der mit **WcfCommunicationClientFactory** erstellt wurde.

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Clientcode kann **WcfCommunicationClientFactory** zusammen mit dem **ServicePartitionClient** verwenden, um den Dienstendpunkt festzulegen und mit dem Dienst zu kommunizieren.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // do not retry Exception types


//
// Create a client for communicating with the calc service that has been created with the
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```

## Nächste Schritte
* [Remoteprozeduraufruf mit Reliable Services-Remoting](service-fabric-reliable-services-communication-remoting.md)

* [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0128_2016-->