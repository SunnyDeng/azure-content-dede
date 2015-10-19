<properties
   pageTitle="Erste Schritte mit Reliable Actors | Microsoft Azure"
   description="Dieses Lernprogramm führt Sie durch die Schritte zum Erstellen, Debuggen und Bereitstellen eines kanonischen HelloWorld-Diensts mithilfe von Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Reliable Actors: Das Szenario für die kanonische HelloWorld-Vorgehensweise
Dieser Artikel erläutert die Grundlagen von Service Fabric Reliable Actors und führt Sie durch das Erstellen, Debuggen und Bereitstellen einer einfachen HelloWorld-Anwendung in Visual Studio.

## Installation und Setup
Bevor Sie beginnen, vergewissern Sie sich, dass sich das Setup der Entwicklungsumgebung von Service Fabric auf Ihrem Computer befindet. Ausführliche Anweisungen zum Einrichten der Entwicklungsumgebung finden Sie [hier](service-fabric-get-started.md).

## Grundlegende Konzepte
Um mit Reliable Actors zu beginnen, müssen Sie lediglich 4 grundlegende Konzepte verstehen:

* **Actor-Dienst**. Reliable Actors sind in Dienste gepackt, die in der Service Fabric-Infrastruktur bereitgestellt werden können. Ein Dienst kann einen oder mehrere Actors hosten. Die Vor- und Nachteile von einem oder mehreren Actors pro Dienst werden zu einem späteren Zeitpunkt ausführlicher behandelt. Hier wird davon ausgegangen, dass nur ein Actor implementiert werden soll.
* **Actor-Schnittstelle**. Die Actor-Schnittstelle wird zum Definieren der öffentlichen Schnittstelle eines Actors verwendet. In der Actor-Modell-Terminologie definiert sie den Typ der Nachrichten, die der Actor verarbeiten kann. Die Actor-Schnittstelle wird von anderen Actors oder von Clientanwendungen zum (asynchronen) "Senden" von Nachrichten an den Actor verwendet. Mithilfe von Reliable Actors können mehrere Schnittstellen implementiert werden, wie noch zu sehen sein wird, ein HelloWorld-Actor kann die IHelloWorld-Schnittstelle implementieren, jedoch auch eine ILogging-Schnittstelle, die verschiedene Nachrichten/Funktionalitäten definiert.
* **Actor-Registrierung**. Der Actor-Typ muss im Actor-Dienst registriert werden, damit Service Fabric den neuen Typ erkennt und ihn zum Erstellen von neuen Actors verwenden kann.
* **ActorProxy--Klasse**. Die ActorProxy-Klasse dient zum Binden an einen Actor und zum Aufrufen der Methoden, die über seine Schnittstellen verfügbar gemacht werden. Die ActorProxy-Klasse bietet zwei wichtige Funktionen:
	* Namensauflösung: Kann zum Lokalisieren des Actors im Cluster verwendet werden (suchen, in welchem Knoten des Clusters er sich befindet).
	* Behandeln von Fehlern: Kann Methodenaufrufe erneut versuchen und den Actor-Speicherort erneut bestimmen, z. B. nach einem Fehler, der erfordert, dass der Actor auf einen anderen Knoten im Cluster verschoben wird.

## Erstellen eines neuen Projekts in Visual Studio
Nachdem Sie die Service Fabric-Tools für Visual Studio installiert haben, können Sie neue Projekttypen erstellen. Die neuen Projekttypen befinden sich unter der Kategorie "Cloud" des Dialogfelds "Neues Projekt".


![Service Fabric-Tools für Visual Studio – Neues Projekt][1]

Im nächsten Dialogfeld können Sie den Typ des Projekts auswählen, das Sie erstellen möchten.

![Service Fabric-Projektvorlagen][5]

Für das HelloWorld-Projekt wird der Service Fabric Actor-Dienst verwendet.

Nachdem die Projektmappe erstellt wurde, sollte die folgende Struktur angezeigt werden:

![Service Fabric-Projektstruktur][2]

## Grundlegende Bausteine von Reliable Actors

Eine typische Reliable Actors-Projektmappe aus drei Projekten:

* Das Anwendungsprojekt (HelloWorldApplication). Dies ist das Projekt, das alle Dienste für die Bereitstellung zusammenpackt. Es enthält die ApplicationManifest.xml und PowerShell-Skripts zum Verwalten der Anwendung.

* Das Schnittstellenprojekt (HelloWorld.Interfaces). Dies ist das Projekt, das die Schnittstellendefinition für den Actor enthält. Im Schnittstellenprojekt können Sie die Schnittstellen definieren, die von den Actors in der Projektmappe verwendet werden.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* Das Dienstprojekt ("HelloWorld"). Dies ist das Projekt, mit dem der Service Fabric-Dienst zum Hosten des Actors definiert wird. Es enthält einige Codebausteine, die in den meisten Fällen (ServiceHost.cs) nicht bearbeitet werden müssen, und die Implementierung des Actors. Die Implementierung des Actors umfasst die Implementierung einer Klasse, die von einem Basistyp (Actor) abgeleitet wird und die im .Interfaces-Projekt definierte(n) Schnittstelle(n) implementiert.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

Das Actor-Dienst-Projekt enthält den Code zum Erstellen eines Service Fabric-Dienstes. In der Dienstdefinition sind Actor-Typen registriert, damit sie zum Instanziieren von neuen Actors verwendet werden können.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

Wenn Sie ein neues Projekt in Visual Studio beginnen und nur eine Actor-Definition haben, wird die Registrierung standardmäßig in den von Visual Studio generierten Code eingefügt. Wenn Sie in dem Dienst weitere Actors definieren, müssen Sie die Actor-Registrierung hinzufügen mit:

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## Debuggen

Service Fabric-Tools für Visual Studio unterstützen das Debuggen auf dem lokalen Computer. Sie können eine Debugsitzung starten, indem Sie F5 drücken. Visual Studio erstellt (falls erforderlich) Pakete, stellt die Anwendung auf dem lokalen Service Fabric-Cluster bereit und fügt den Debugger an. Die Erfahrung ist vergleichbar mit dem Debuggen einer ASP.NET-Anwendung. Während der Bereitstellung wird der Fortschritt im Ausgabefenster angezeigt.

![Service Fabric-Debugging – Ausgabefenster][3]


## Nächste Schritte

- [Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Actors-APIS-Referenzdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Beispielcode](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=Oct15_HO2-->