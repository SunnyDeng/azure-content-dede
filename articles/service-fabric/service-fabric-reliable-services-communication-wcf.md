<properties
   pageTitle="WCF-basierter Kommunikationsstapel der Reliable Services-API"
   description="Dieser Artikel beschreibt den WCF-basierten Kommunikationsstapel, der von der Reliable Services-API bereitgestellt wird."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# WCF-basierter Kommunikationsstapel für Reliable Services
Das Reliable Services-Framework ermöglicht Dienstautoren, den Kommunikationsstapel für ihren Dienst zu wählen. Sie können den gewünschten Kommunikationsstapel als Plug-In über `ICommunicationListener` verwenden, der von der Methode [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md) zurückgegeben wird. Das Framework bietet eine WCF-basierten Implementierung des Kommunikationstapels für Dienstautoren, die eine WCF-basierte Kommunikation verwenden möchten.

## WCF-Kommunikationslistener
Die spezielle WCF-Implementierung von `ICommunicationListener` erfolgt über die Klasse `WcfCommunicationListener`.

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        };

        return communicationListener;
    }

```

## Schreiben von Clients für WCF-Kommunikationsstapel
Für das Schreiben von Clients, die über WCF mit Diensten kommunizieren, bietet das Framework `WcfClientCommunicationFactory`. Dies ist die spezielle WCF-Implementierung von [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Auf den WCF-Kommunikationskanal kann von `WcfCommunicationClient` aus zugegriffen werden. Der Client wurde von `WcfCommunicationClientFactory` erstellt.

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Der Clientcode kann `WcfCommunicationClientFactory` zusammen mit `ServicePartitionClient` verwenden, um den Dienstendpunkt zu bestimmen und mit dem Dienst zu kommunizieren.

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
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
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
 

<!---HONumber=August15_HO6-->