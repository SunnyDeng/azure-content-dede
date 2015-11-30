<properties
   pageTitle="Übersicht über das Reliable Service-Kommunikationsmodell | Microsoft Azure"
   description="Übersicht über das Reliable Service-Kommunikationsmodell, einschließlich Öffnen von Listeners für Dienste, Auflösen von Endpunkten und Kommunikation zwischen Diensten."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Das Reliable Service-Kommunikationsmodell

Service Fabric ist als Plattform vollständig unabhängig von der Kommunikation zwischen Diensten. Alle Protokolle und Stapel von UDP bis HTTP können koexistieren. Es liegt in der Entscheidung des Entwicklers, wie Dienste kommunizieren sollen. Das Reliable Services-Anwendungsframework bietet einige vorgefertigte Kommunikationsstapel und Tools zur Implementierung benutzerdefinierter Kommunikationsstapel, wie z. B. Abstraktionen, die Clients zur Ermittlung von Endpunkten und zur Kommunikation mit diesen verwenden können.

## Einrichten der Dienstkommunikation

Die Reliable Services-API verwendet eine einfache Schnittstelle für die Dienstkommunikation. Um einen Endpunkt für den Dienst zu öffnen, können Sie einfach Sie die folgende Schnittstelle implementieren:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Fügen Sie anschließend Ihre Kommunikationslistener-Implementierung hinzu, indem Sie sie in der Überschreibung einer Dienst-Basisklassenmethode zurückgeben.

Statusfrei:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Statusbehaftet

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

In beiden Fällen wird eine Sammlung von Listenern zurückgegeben, durch die Ihr Dienst problemlos mehrere Listener verwenden kann – z. B. könnten Sie einen HTTP-Listener und einen separaten WebSocket-Listener haben. Jeder Listener erhält einen Namen und die resultierende Auflistung von Name-Adress-Paaren wird als JSON-Objekt dargestellt, wenn ein Client die Listeneradressen für eine Dienstinstanz oder eine Partition anfordert.

Bei einem zustandslosen Dienst gibt die Überschreibung eine Sammlung von ServiceInstanceListeners zurück. Ein ServiceInstanceListener enthält eine Funktion für die ICommunicationListener-Erstellung und weist diesem einen Namen zu. Bei statusbehafteten Diensten gibt die Überschreibung eine ServiceReplicaListener-Sammlung zurück. Dies unterscheidet sich leicht vom statusfreien Gegenstück, da ServiceReplicaListener über eine Option zum Öffnen eines ICommunicationListeners auf sekundären Replikaten verfügt. Dadurch können Sie nicht nur mehrere Kommunikationslistener in einem Dienst verwenden, sondern auch angeben, welche Listener auf sekundären Replikaten Anforderungen akzeptieren und welche nur auf primären Replikaten ausgeführt werden.

Beispielsweise haben wir einen ServiceRemotingListener, der nur RPC-Aufrufe auf primären Replikaten akzeptiert, und einen zweiten, benutzerdefinierten Listener, der Leseanforderungen für sekundäre Replikate akzeptiert:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Abschließend beschreiben Sie die Endpunkte, die für den Dienst erforderlich sind, im [Dienstmanifest](service-fabric-application-model.md) im Abschnitt „Endpoints“.

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

Eine vollständige exemplarische Vorgehensweise zum Schreiben eines `ICommunicationListener` finden Sie unter [Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server](service-fabric-reliable-services-communication-webapi.md).

## Kommunikation zwischen Client und Dienst
Die Reliable Services-API bietet die folgenden Abstraktionen, die das Schreiben von Clients für die Kommunikation mit Diensten einfach machen.

### ServicePartitionResolver
Diese Hilfsklasse hilft dem Client, den Endpunkt eines Service Fabric-Diensts zur Laufzeit zu ermitteln. Das Ermitteln des Endpunkts eines Diensts wird in Verbindung mit Service Fabric als Dienstendpunktauflösung bezeichnet.

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

### CommunicationClientFactory
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
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
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

Ein typisches Nutzungsmuster sieht so aus:

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
* [Remoteprozeduraufruf mit Reliable Services-Remoting](service-fabric-reliable-services-communication-remoting.md)

* [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [WCF-Kommunikation mit Reliable Services](service-fabric-reliable-services-communication-wcf.md)

 

<!---HONumber=Nov15_HO4-->