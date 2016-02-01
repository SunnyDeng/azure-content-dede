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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Reliable Actors: Das Szenario für die kanonische HelloWorld-Vorgehensweise
Dieser Artikel erläutert die Grundlagen von Azure Service Fabric Reliable Actors und führt Sie durch das Erstellen, Debuggen und Bereitstellen einer einfachen HelloWorld-Anwendung in Visual Studio.

## Installation und Setup
Bevor Sie beginnen, vergewissern Sie sich, dass sich das Setup der Entwicklungsumgebung von Service Fabric auf Ihrem Computer befindet. Wenn Sie die Umgebung einrichten müssen, finden Sie ausführliche Anweisungen unter [Einrichten der Entwicklungsumgebung](service-fabric-get-started.md).

## Grundlegende Konzepte
Um mit Reliable Actors zu beginnen, müssen Sie lediglich vier grundlegende Konzepte verstehen:

* **Actor-Dienst**. Reliable Actors sind in Dienste gepackt, die in der Service Fabric-Infrastruktur bereitgestellt werden können. Ein Dienst kann einen oder mehrere Actors hosten. Die Vor- und Nachteile von einem oder mehreren Actors pro Dienst werden weiter unten ausführlicher behandelt. Zunächst gehen wir davon aus, dass nur ein Actor implementiert werden soll.
* **Actor-Schnittstelle**. Die Actor-Schnittstelle wird zum Definieren der öffentlichen Schnittstelle eines Actors verwendet. In der Terminologie von Reliable Actor-Modellen definiert die Actor-Schnittstelle die Nachrichtentypen, die der Actor verstehen und verarbeiten kann. Die Actor-Schnittstelle wird von anderen Actors oder von Clientanwendungen zum (asynchronen) „Senden“ von Nachrichten an den Actor verwendet. Reliable Actors können mehrere Schnittstellen implementieren. Wie noch zu sehen sein wird, kann ein HelloWorld-Actor die IHelloWorld-Schnittstelle implementieren, jedoch auch eine ILogging-Schnittstelle, die verschiedene Nachrichten und/oder Funktionalitäten definiert.
* **Actor-Registrierung**. Im Reliable Actors-Dienst muss der Actor-Typ registriert werden. Auf diese Weise ist der neue Typ in Service Fabric bekannt und kann zum Erstellen neuer Actors verwendet werden.
* **ActorProxy-Klasse**. Die ActorProxy-Klasse dient zum Binden an einen Actor und zum Aufrufen der Methoden, die über seine Schnittstellen verfügbar gemacht werden. Die ActorProxy-Klasse bietet zwei wichtige Funktionen:
	* Namensauflösung. Kann zum Lokalisieren des Actors im Cluster verwendet werden (den Knoten des Clusters suchen, auf dem er gehostet wird).
	* Behandeln von Fehlern. Kann Methodenaufrufe erneut versuchen und den Actor-Speicherort erneut bestimmen, z. B. nach einem Fehler, der erfordert, dass der Actor auf einen anderen Knoten im Cluster verschoben wird.

## Erstellen eines neuen Projekts in Visual Studio
Nachdem Sie die Service Fabric-Tools für Visual Studio installiert haben, können Sie neue Projekttypen erstellen. Die neuen Projekttypen befinden sich unter der Kategorie **Cloud** des Dialogfelds **Neues Projekt**.


![Service Fabric-Tools für Visual Studio – Neues Projekt][1]

Im nächsten Dialogfeld können Sie den Typ des Projekts auswählen, das Sie erstellen möchten.

![Service Fabric-Projektvorlagen][5]

Für das HelloWorld-Projekt wird der Service Fabric Reliable Actors-Dienst verwendet.

Nachdem die Projektmappe erstellt wurde, sollte die folgende Struktur angezeigt werden:

![Service Fabric-Projektstruktur][2]

## Grundlegende Bausteine von Reliable Actors

Eine typische Reliable Actors-Projektmappe besteht aus drei Projekten:

* **Anwendungsprojekt (HelloWorldApplication)**. Dies ist das Projekt, das alle Dienste für die Bereitstellung zusammenfasst. Es enthält die Datei **ApplicationManifest.xml** und PowerShell-Skripts zum Verwalten der Anwendung.

* **Schnittstellenprojekt (HelloWorld.Interfaces)**. Dies ist das Projekt, das die Schnittstellendefinition für den Actor enthält. Im Projekt „HelloWorld.Interfaces“ können Sie die Schnittstellen definieren, die von den Actors in der Projektmappe verwendet werden.

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* **Dienstprojekt (HelloWorld)**. Dies ist das Projekt, mit dem der Service Fabric-Dienst zum Hosten des Actors definiert wird. Es enthält einige Codebausteine, die in den meisten Fällen nicht bearbeitet werden müssen (ServiceHost.cs), und die Implementierung des Actors. Die Implementierung des Actors umfasst die Implementierung einer Klasse, die von einem Basistyp (Actor) abgeleitet wird. Außerdem werden die Schnittstellen implementiert, die im Projekt „HelloWorld.Interfaces“ definiert sind.

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

Das Reliable Actors-Dienstprojekt enthält den Code zum Erstellen eines Service Fabric-Diensts. In der Dienstdefinition werden die Actor-Typen registriert, sodass sie zum Instanziieren neuer Actors verwendet werden können.

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

Wenn Sie ein neues Projekt in Visual Studio beginnen und nur eine Actor-Definition haben, wird die Registrierung standardmäßig in den von Visual Studio generierten Code eingefügt. Wenn Sie in dem Dienst weitere Actors definieren, müssen Sie die Actor-Registrierung folgendermaßen hinzufügen:

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## Debuggen

Die Service Fabric-Tools für Visual Studio unterstützen das Debuggen auf dem lokalen Computer. Sie können eine Debugsitzung starten, indem Sie F5 drücken. Visual Studio erstellt Pakete (falls erforderlich). Es stellt außerdem die Anwendung auf dem lokalen Service Fabric-Cluster bereit und fügt den Debugger an. Die Erfahrung ist vergleichbar mit dem Debuggen einer ASP.NET-Anwendung.

Während der Bereitstellung wird der Fortschritt im Fenster **Ausgabe** angezeigt.

![Service Fabric-Debugging – Ausgabefenster][3]


## Nächste Schritte

- [Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Referenzdokumentation zu Actors-APIs](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Beispielcode](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0121_2016-->