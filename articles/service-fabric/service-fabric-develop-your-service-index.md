<properties
   pageTitle="Entwickeln von Service Fabric-Diensten"
   description="Enthält grundlegende Informationen und Lernprogramme zum Entwickeln eines Service Fabric-Diensts mit dem Reliable Actors- oder Reliable Services-Programmiermodell."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2015"
   ms.author="ryanwi"/>

# Entwickeln von Service Fabric-Diensten
Diese Seite enthält Links zur Übersicht, zu grundlegenden Artikeln und zum Lernprogramm für das Entwickeln von Service Fabric-Diensten. Service Fabric bietet zwei High-Level-Programmiermodelle zum Erstellen von Diensten: Reliable Actors-APIs und Reliable Services-APIs. Beide Programmiermodelle setzen auf dem gleichen Service Fabric-Kern auf, sie unterscheiden sich jedoch durch unterschiedliche Kompromisse zwischen Einfachheit und Flexibilität im Hinblick auf Parallelität, Partitionierung und Kommunikation. Um das richtige Framework für einen bestimmten Dienst in Ihrer Anwendung zu wählen, sollten Sie beide Modelle kennen.

- [Auswählen des Programmiermodells](service-fabric-choose-framework.md)
- [Einführung in das Azure Service Fabric-Actors-Modell](service-fabric-reliable-actors-introduction.md)
- [Einführung in das Reliable Services-Programmiermodell](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## Reliable Actors-Programmiermodell
 Reliable Actors bietet ein asynchrones Singlethread-Actors-Modell. Actors stellen die Einheit von Status und Berechnung dar, die im Cluster verteilt werden, um hohe Skalierbarkeit zu erzielen. Das Reliable Actors-Modell nutzt den verteilten Speicher, der von der zugrunde liegenden Service Fabric-Plattform bereitgestellt wird, um hohe Verfügbarkeit und konsistente Zustandsverwaltung für Anwendungsentwickler zu ermöglichen. Weitere Informationen finden Sie unter:

- [Erste Schritte mit Reliable Actors](service-fabric-reliable-actors-get-started.md)
- [Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
- [Verwendung der Service Fabric-Plattform durch Fabric Actors](service-fabric-reliable-actors-platform.md)
- [Hinweise zur Typserialisierung von Azure Service Fabric Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
- [Node.js und Reliable Actors](service-fabric-node-and-reliable-actors-an-winning-combination.md)

Die Kommunikation mit Actors wird in folgenden Artikeln beschrieben:

- [Einführung in das Azure Service Fabric-Actors-Modell](service-fabric-reliable-actors-introduction.md#actor-communication)
- [Kommunizieren mit Diensten](service-fabric-connect-and-communicate-with-services.md)

Diese Artikel befasst sich mit hilfreichen Entwurfsmustern und Szenarien:

- [Entwurfsmuster für das Actor-Modell](service-fabric-reliable-actors-patterns-introduction.md)  
- [Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)
- [Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)
- [Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

Für Reliable Actors-Methoden wird eine einfache Turn-basierte Parallelität bereitgestellt. In den folgenden Artikeln werden Parallelität, Timer, Erinnerungen und Eintrittsvarianz beschrieben:

- [Parallelität](service-fabric-reliable-actors-introduction.md#concurrency)
- [Parallelitätsbezogene Ereignisse und Leistungsindikatoren](service-fabric-reliable-actors-diagnostics.md)
- [Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
- [Actor-Timer](service-fabric-reliable-actors-timers-reminders.md)

Informationen zum Konfigurieren von Reliable Actors finden Sie hier:

- [KVSActorStateProvider-Konfiguration](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [Konfigurieren von Reliable Actors: ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

Reliable Actors geben Ereignisse und Leistungsindikatoren aus, die zur Diagnose und Überwachung von Diensten verwendet werden können:

- [Actor-Diagnose](service-fabric-reliable-actors-diagnostics.md)
- [Actor-Ereignisse](service-fabric-reliable-actors-events.md)


## Reliable Services-Programmiermodell
Reliable Services bietet ein einfaches, leistungsfähiges High-Level-Programmiermodell, mit dem Sie alles Wichtige in Ihrer Anwendung programmieren können. Weitere Informationen finden Sie unter:

- [Erste Schritte mit Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Architektur](service-fabric-reliable-services-platform-architecture.md)
- [Zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)
- [Konfigurieren zustandsbehafteter zuverlässiger Dienste](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [Erweiterte Verwendung des Programmiermodells Reliable Services](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

Die folgenden Artikel beschreiben die Kommunikation mit Reliable Services und die Abstraktionen, die von Clients zum Erkennen und Kommunizieren mit Dienstendpunkten verwendet werden können:

- [Kommunizieren mit Diensten](service-fabric-connect-and-communicate-with-services.md)
- [Dienstkommunikationsmodell](service-fabric-reliable-services-communication.md)
- [Standardkommunikationsstapel des Reliable Services-Framework](service-fabric-reliable-services-communication-default.md)
- [WCF-basierter Kommunikationsstapel für Reliable Services](service-fabric-reliable-services-communication-wcf.md)
- [Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

Reliable Services geben Ereignisse und Leistungsindikatoren aus, die zur Diagnose und Überwachung von Diensten verwendet werden können:

- [Diagnose zustandsbehafteter zuverlässiger Dienste](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=Oct15_HO1-->