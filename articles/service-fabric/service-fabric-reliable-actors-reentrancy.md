<properties
   pageTitle="Eintrittsinvarianz in Azure Service Fabric Actors"
   description="Einführung in die Eintrittsinvarianz bei Azure Service Fabric Actors"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/09/2015"
   ms.author="amanbha"/>


# Actor-Eintrittsinvarianz
Fabric-Actors erlauben standardmäßig bei logischen Aufrufen eine kontextbasierte Eintrittsinvarianz. Dadurch können Actors eintrittsinvariant sein, wenn sie sich in der selben Aufrufkontextkette befinden. Wenn beispielsweise Actor A eine Nachricht an Actor B sendet und dieser wiederum eine Nachricht an Actor C. Wenn Actor C Actor A aufruft, wird im Rahmen der Nachrichtenverarbeitung zugelassen, dass die Nachricht eintrittsinvariant ist. Alle weiteren Nachrichten, die zu unterschiedlichen Aufrufkontexten gehören, werden auf Actor A blockiert, bis dieser die Verarbeitung abgeschlossen hat.

Actors, die kontextbasierte Eintrittsinvarianz bei logischen Aufrufen nicht zulassen möchten, können diese Option deaktivieren, indem sie der Actor-Klasse `ReentrantAttribute(ReentrancyMode.Disallowed)` hinzufügen.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

Der folgende Code zeigt die Actor-Klasse, die den Eintrittsinvarianz-Modus auf `ReentrancyMode.Disallowed` festlegt. Wenn in diesem Fall ein Actor eine eintrittsinvariante Nachricht an einen anderen Actor sendet, wird eine Ausnahme vom Typ `FabricException` ausgelöst.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```
 

<!---HONumber=July15_HO4-->