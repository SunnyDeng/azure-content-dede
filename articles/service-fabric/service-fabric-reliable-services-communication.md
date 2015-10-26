<properties
   pageTitle="Übersicht über das Dienstkommunikationsmodell"
   description="Dieser Artikel beschreibt die Grundlagen des von der Reliable Services-API unterstützten Kommunikationsmodells."
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
   ms.date="08/27/2015"
   ms.author="bharatn@microsoft.com"/>

# Dienstkommunikationsmodell

Das Reliable Services-Programmiermodell ermöglicht es Dienstautoren, den gewünschten Kommunikationsmechanismus zum Bereitstellen ihrer Dienstendpunkte festzulegen. Zudem bietet das Modell Abstraktionen, mit deren Hilfe Clients Dienstendpunkte ermitteln und mit diesen kommunizieren können.

## Einrichten des Dienstkommunikationsstapels

Die Reliable Services-API ermöglicht es Autoren, den gewünschten Kommunikationsstapel als Plug-In im Dienst zu verwenden. Sie implementieren zu diesem Zweck die folgende Methode in den Dienst:

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

Die Schnittstelle `ICommunicationListener` definiert die Schnittstelle, die vom Kommunikationslistener für einen Dienst implementiert werden muss.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
Die Endpunkte, die für den Dienst erforderlich sind, werden im [Dienstmanifest](service-fabric-application-model.md) im Abschnitt über Endpunkte erläutert.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

Der Kommunikationslistener kann die ihm zugewiesenen Endpunktressourcen für den Endpunkt über `CodePackageActivationContext` in `ServiceInitializationParameters` aufrufen. Die Überwachung von Anforderungen kann nach dem Öffnen des Kommunikationslisteners beginnen.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Das gesamte Dienstpaket verwendet einheitliche Endpunktressourcen, die von Service Fabric beim Aktivieren des Dienstpakets zugewiesen werden. Somit verwenden alle Replikate, die im gleichen ServiceHost gehostet sind, den gleichen Port. Dies bedeutet, dass der Kommunikationslistener die Portfreigabe unterstützen sollte. Zu diesem Zweck wird empfohlen, dass der Kommunikationslistener zum Generieren der Überwachungsadresse die Partitions-ID und die Replikat-/Instanz-ID verwendet.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
  replicaOrInstanceId = parameters.InstanceId;
}
else
{
  replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

## Kommunikation zwischen Client und Dienst
Die Reliable Services-API bietet die folgenden Abstraktionen, die das Schreiben von Clients für die Kommunikation mit Diensten einfach machen.

## ServicePartitionResolver
Die Klasse ServicePartitionResolver hilft dem Client, den Endpunkt eines Service Fabric-Diensts zur Laufzeit zu ermitteln.

> [AZURE.TIP]Das Ermitteln des Endpunkts eines Diensts wird in Verbindung mit Service Fabric als Dienstendpunktauflösung bezeichnet.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
    long partitionKey,
    CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
    CancellationToken cancellationToken);


```
> [AZURE.NOTE]"FabricClient" ist das Objekt, das für die Kommunikation mit dem Service Fabric-Cluster für verschiedene Verwaltungsvorgänge auf dem Service Fabric-Cluster verwendet wird.

In der Regel muss der Clientcode nicht direkt mit `ServicePartitionResolver` funktionieren. Der erstellte Code wird an andere Hilfsklassen in der Reliable Services-API übergeben. Diese verwenden intern den Resolver und unterstützen den Client bei der Kommunikation mit dem Dienst.

## CommunicationClientFactory
`ICommunicationClientFactory` definiert die von einer Kommunikationclientfactory implementierte Basisschnittstelle, die Clients erstellt, die mit einem ServiceFabric-Dienst kommunizieren können. Die Implementierung von CommunicationClientFactory hängt vom Kommunikationsstapel ab, den der Service Fabric-Dienst verwendet, mit dem der Client kommunizieren möchte. Die Reliable Services-API bietet die Klasse CommunicationClientFactoryBase<TCommunicationClient>, die eine Basisimplementierung der Schnittstelle `ICommunicationClientFactory` implementiert und für alle Kommunikationsstapel gängige Aufgaben ausführt (wie die Verwendung von `ServicePartitionResolver` zum Ermitteln des Dienstendpunkts). Clients implementieren in der Regel die abstrakte Klasse CommunicationClientFactoryBase, um die für Kommunikationsstapel spezifische Logik zu verarbeiten.

```csharp

protected CommunicationClientFactoryBase(
    ServicePartitionResolver servicePartitionResolver = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
    public MyCommunicationClient(MyCommunicationChannel communicationChannel)
    {
      this.CommunicationChannel = communicationChannel;
    }
    public MyCommunicationChannel CommunicationChannel { get; private set; }
    public ResolvedServicePartition ResolvedServicePartition;

}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
`ServicePartitionClient` verwendet CommunicationClientFactory (und intern ServicePartitionResolver). Der Client unterstützt die Kommunikation mit dem Dienst, indem er Wiederholungen für gängige Ausnahmen bei der Kommunikation und vorübergehende Service Fabric-Ausnahmen handhabt.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Ein typisches Nutzungsmuster würde so aussehen:

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

  var result = await myServicePartitionClient.InvokeWithRetryAsync(
      client =>
      {
        // Communicate with the service using the client.
        throw new NotImplementedException();
      },
      CancellationToken.None);


... other client code ...

```

## Nächste Schritte
* [Standardkommunikationsstapel des Reliable Services-Frameworks](service-fabric-reliable-services-communication-default.md)

* [WCF-basierter Kommunikationsstapel des Reliable Services-Frameworks](service-fabric-reliable-services-communication-wcf.md)

* [Schreiben von Diensten mit der Reliable Services-API und dem WebAPI-Kommunikationsstapel](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=Oct15_HO3-->