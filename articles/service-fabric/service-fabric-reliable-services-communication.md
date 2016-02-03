<properties
   pageTitle="Übersicht über das Reliable Services-Kommunikationsmodell | Microsoft Azure"
   description="Übersicht über das Reliable Services-Kommunikationsmodell, einschließlich Öffnen von Listenern für Dienste, Auflösen von Endpunkten und Kommunikation zwischen Diensten."
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

# Das Reliable Services-Kommunikationsmodell

Azure Service Fabric ist als Plattform vollständig unabhängig von der Kommunikation zwischen Diensten. Alle Protokolle und Stapel von UDP bis HTTP sind zulässig. Es liegt in der Entscheidung des Entwicklers, wie Dienste kommunizieren sollen. Das Reliable Services-Anwendungsframework stellt einige vordefinierte Kommunikationsstapel und Tools bereit, die Sie für die Einführung Ihres benutzerdefinierten Kommunikationsstapels verwenden können. Dazu gehören Abstraktionen, die Clients zum Erkennen von und Kommunizieren mit Dienstendpunkten verwenden können.

## Einrichten der Dienstkommunikation

Die Reliable Services-API verwendet eine einfache Schnittstelle für die Dienstkommunikation. Um einen Endpunkt für den Dienst zu öffnen, implementieren Sie einfach die folgende Schnittstelle:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Sie können anschließend Ihre Kommunikationslistener-Implementierung hinzufügen, indem Sie sie in der Überschreibung einer dienstbasierten Klassenmethode zurückgeben.

Zustandslose Dienste:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Zustandsbehaftete Dienste:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

In beiden Fällen geben Sie eine Sammlung von Listenern zurück. Dies ermöglicht dem Dienst die einfache Verwendung mehrerer Listener. Sie besitzen beispielsweise einen HTTP-Listener und einen separaten WebSocket-Listener. Jeder Listener erhält einen Namen, und die resultierende Sammlung von *name : address*-Paaren wird als JSON-Objekt dargestellt, wenn ein Client die Listeneradressen für eine Dienstinstanz oder eine Partition anfordert.

Bei einem zustandslosen Dienst gibt die Überschreibung eine Sammlung von ServiceInstanceListeners zurück. Ein ServiceInstanceListener enthält eine Funktion für die ICommunicationListener-Erstellung und weist diesem Listener einen Namen zu. Bei statusbehafteten Diensten gibt die Überschreibung eine ServiceReplicaListener-Sammlung zurück. Dies unterscheidet sich leicht vom zustandslosen Gegenstück, da ein ServiceReplicaListener über eine Option zum Öffnen eines ICommunicationListeners auf sekundären Replikaten verfügt. Sie können nicht nur mehrere Kommunikationslistener in einem Dienst verwenden, sondern auch angeben, welche Listener auf sekundären Replikaten Anforderungen akzeptieren und welche nur auf primären Replikaten ausgeführt werden.

Beispielsweise können Sie einen ServiceRemotingListener besitzen, der nur RPC-Aufrufe auf primären Replikaten akzeptiert, und einen zweiten, benutzerdefinierten Listener, der Leseanforderungen für sekundäre Replikate akzeptiert:

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

Abschließend beschreiben Sie die Endpunkte, die für den Dienst erforderlich sind, im [Dienstmanifest](service-fabric-application-model.md) im Abschnitt „Endpunkte“.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

Der Kommunikationslistener kann die ihm zugewiesenen Endpunktressourcen für den Endpunkt über `CodePackageActivationContext` in `ServiceInitializationParameters` aufrufen. Die Überwachung von Anforderungen kann nach dem Öffnen des Listeners beginnen.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Das gesamte Dienstpaket verwendet einheitliche Endpunktressourcen, die von Service Fabric beim Aktivieren des Dienstpakets zugewiesen werden. Alle Replikate, die im gleichen ServiceHost gehostet werden, verwenden den gleichen Port. Dies bedeutet, dass der Kommunikationslistener die Portfreigabe unterstützen sollte. Zu diesem Zweck wird empfohlen, dass der Kommunikationslistener zum Generieren der Überwachungsadresse die Partitions-ID und die Replikat-/Instanz-ID verwendet.

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

Eine ausführliche Anleitung mit den Schritten zum Schreiben eines `ICommunicationListener`-Elements finden Sie unter [Web-API-Dienste von Service Fabric mit selbstgehostetem OWIN](service-fabric-reliable-services-communication-webapi.md).

### Kommunikation zwischen Client und Dienst
Die Reliable Services-API bietet die folgenden Abstraktionen, die das Schreiben von Clients für die Kommunikation mit Diensten erleichtern.

#### ServicePartitionResolver
Diese Hilfsklasse hilft dem Client, den Endpunkt eines Service Fabric-Diensts zur Laufzeit zu ermitteln. Das Ermitteln des Endpunkts eines Diensts wird in Verbindung mit Service Fabric als *Dienstendpunktauflösung* bezeichnet.

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
> [AZURE.NOTE]FabricClient ist das Objekt, das für die Kommunikation mit dem Service Fabric-Cluster für verschiedene Verwaltungsvorgänge im Cluster verwendet wird.

In der Regel muss der Clientcode nicht direkt mit dem `ServicePartitionResolver`-Element funktionieren. Das Element wird erstellt und an andere Hilfsklassen in der Reliable Services-API übergeben. Diese Klassen verwenden den Resolver intern und unterstützen die Kommunikation des Clients mit dem Dienst.

#### CommunicationClientFactory
`ICommunicationClientFactory` definiert die von einer Kommunikationclientfactory implementierte Basisschnittstelle, die Clients erstellt, die mit einem Service Fabric-Dienst kommunizieren können. Die Implementierung von CommunicationClientFactory hängt vom Kommunikationsstapel ab, den der Service Fabric-Dienst verwendet, über den der Client kommunizieren möchte. Die Reliable Services-API stellt das Element `CommunicationClientFactoryBase<TCommunicationClient>` bereit. Dieses Element bietet eine grundlegende Implementierung der `ICommunicationClientFactory`-Schnittstelle und führt Aufgaben aus, die für alle Kommunikationsstapel gelten. (Zu diesen Aufgaben gehört die Verwendung von `ServicePartitionResolver` zum Ermitteln des Dienstendpunkts.) Clients implementieren in der Regel die abstrakte Klasse CommunicationClientFactoryBase, um die für den Kommunikationsstapel spezifische Logik zu verarbeiten.

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

#### ServicePartitionClient
`ServicePartitionClient` verwendet CommunicationClientFactory (und intern ServicePartitionResolver). Der Client unterstützt außerdem die Kommunikation mit dem Dienst, indem er Wiederholungen für gängige Ausnahmen bei der Kommunikation und vorübergehende Service Fabric-Ausnahmen handhabt.

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
* [Remoteprozeduraufrufe mit Reliable Services-Remoting](service-fabric-reliable-services-communication-remoting.md)

* [Web-API, die OWIN in Reliable Services verwendet](service-fabric-reliable-services-communication-webapi.md)

* [WCF-Kommunikation mit Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0121_2016-->