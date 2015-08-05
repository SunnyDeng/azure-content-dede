<properties
   pageTitle="Standardkommunikationsstapel des Reliable Services-Frameworks"
   description="Dieser Artikel beschreibt den vom Reliable Services-Framework bereitgestellten Standardkommunikationsstapel für die Kommunikation zwischen Diensten und Clients."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Standardkommunikationsstapel des Reliable Services-Frameworks
Für Dienstautoren, die nicht an eine bestimmte Implementierung der Kommunikationsstapel (WebAPI, WCF usw.) gebunden sind, stellt das Framework auf der Client- und Dienstseite Kommunikationsmittel zum Einrichten der Kommunikation zwischen dem Dienst und dem Client bereit.

> [AZURE.NOTE]Aktualisieren Sie auf die neuesten NuGet-Pakete, um die unten genannten Funktionen zu erhalten.

## Dienstkommunikationslistener
Der Standardkommunikationslistener für den Dienst ist in die Klasse `ServiceCommunicationListener` implementiert.

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
Die Methoden, die der Dienst implementiert und seinen Clients zur Verfügung stellt, werden als asynchrone Methoden in einer Schnittstelle definiert, die Daten von der Schnittstelle `IService` erbt. Der Dienst kann das Objekt `ServiceCommunicationListener` anschließend einfach instanziieren und an die Methode [`CreateCommunicationListener` zurückgeben](service-fabric-reliable-services-communication.md). Der Dienstcode "HelloWorld" zum Einrichten dieses Kommunikationsstapels kann beispielsweise wie folgt definiert werden.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]Bei den Argumenten und Rückgabetypen in der Dienstschnittstelle (z. B. der oben genannten Message-Klasse) wird erwartet, dass sie vom [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) von .NET serialisiert werden können.


## Schreiben von Clients für die Kommunikation mit ServiceCommunicationListener
Damit Clients mithilfe von `ServiceCommunicationListener` mit Diensten kommunizieren können, bietet das Framework die Klasse `ServiceProxy`.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

Clients können ein Dienstproxyobjekt instanziieren, das auf dem Proxyobjekt die entsprechenden Dienstschnittstellen- und Aufrufmethoden implementiert.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]Das Kommunikationsframework gibt vom Dienst ausgegebenen Ausnahmen an den Client weiter. Somit kann die Ausnahmebehandlungslogik auf dem Client, der ServiceProxy verwendet, gegebenenfalls vom Dienst ausgegebene Ausnahmen direkt behandeln.
 

<!---HONumber=July15_HO4-->