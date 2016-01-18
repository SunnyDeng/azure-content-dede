<properties
   pageTitle="Dienstremoting in Service Fabric | Microsoft Azure"
   description="Service Fabric-Remoting ermöglicht Clients und Diensten die Kommunikation mit Diensten über einen Remoteprozeduraufruf."
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
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Dienstremoting mit Reliable Services
Für Dienste, die nicht an ein bestimmtes Kommunikationsprotokoll oder einen bestimmten Kommunikationsstapel gebunden sind, wie etwa WebAPI, WCF (Windows Communication Foundation) oder andere Dienste, stellt das Framework einen Remotingmechanismus für das schnelle, einfache Einrichten von Remoteprozeduraufrufen für Dienste bereit.

## Einrichten von Remoting für einen Dienst
Die Einrichtung von Remoting für einen Dienst erfolgt in zwei einfachen Schritten:

1. Erstellen Sie eine Schnittstelle, die vom Dienst implementiert werden soll. Diese Schnittstelle definiert die Methoden, die für den Remoteprozeduraufruf für Ihren Dienst verfügbar sind. Bei den Methoden muss es sich um asynchrone Methoden handeln, die einen Task zurückgeben. Die Schnittstelle muss `Microsoft.ServiceFabric.Services.Remoting.IService` implementieren, um zu signalisieren, dass der Dienst über eine Remotingschnittstelle verfügt.
2. Verwenden Sie `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener` in Ihrem Dienst. Dies ist eine `ICommunicationListener`-Implementierung, die Remotingfunktionen bereitstellt.

Dieser Hello World-Dienst macht beispielsweise eine einzelne Methode verfügbar, um „Hello World“ per Remoteprozeduraufruf abzurufen:

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE]Bei den Argumenten und Rückgabetypen in der Dienstschnittstelle kann es sich um einfache, komplexe oder benutzerdefinierte Typen handeln. Sie müssen jedoch von [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) von .NET serialisiert werden können.


## Aufrufen von Remotedienstmethoden
Methoden für einen Dienst, der den Remotingstapel nutzt, werden mithilfe eines lokalen Proxys für den Dienst über die `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`-Klasse abgerufen. Die `ServiceProxy`-Methode erstellt einen lokalen Proxy mit der gleichen Schnittstelle, die auch der Dienst implementiert. Mit diesem Proxy können Sie Methoden für die Schnittstelle einfach remote aufrufen.


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

Das Remotingframework gibt beim Dienst aufgetretene Ausnahmen an den Client weiter. Somit kann die Ausnahmebehandlungslogik auf dem Client Ausnahmen, die vom Dienst ausgegeben werden, mithilfe von `ServiceProxy` direkt behandeln.

## Nächste Schritte

* [Web-API mit OWIN in Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [WCF-Kommunikation mit Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0107_2016-->