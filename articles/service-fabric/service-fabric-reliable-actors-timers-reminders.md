<properties
   pageTitle="Timer und Erinnerungen für Reliable Actors"
   description="Einführung in Timer und Erinnerungen für Service Fabric Reliable Actors."
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
   ms.date="08/05/2015"
   ms.author="amanbha"/>


# Actor-Timer
Actor-Timer bieten einen einfachen Wrapper um .NET-Timer, wodurch die Rückrufmethoden die zur Actor-Laufzeit bereitgestellten wechselseitigen Parallelitätsgarantien respektieren.

Actor können sich mithilfe der Methoden `RegisterTimer` und `UnregisterTimer` ihrer Basisklasse bei ihren Timern an- und abmelden. Das folgende Beispiel zeigt die Verwendung von Timer-APIs. Die APIs sind dem .NET-Timer sehr ähnlich. Im Beispiel unten wird bei Ablauf des Timers von der Actor-Laufzeit die Methode `MoveObject` aufgerufen. Sie respektiert garantiert die wechselseitige Parallelität, sodass bis zum Abschluss des Rückrufs keine anderen Actor-Methoden oder Timer-/Erinnerungsrückrufe ausgeführt werden.

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return TaskDone.Done;
    }
}
```

Der nächste Timerzeitraum beginnt nach Abschluss des Rückrufs. Dies bedeutet, dass der Timer während des Rückrufs angehalten und nach Abschluss des Rückrufs erneut gestartet wird.

Zur Actor-Laufzeit wird der Actor-Zustand bei Abschluss des Rückrufs gespeichert, wenn er wie im obigen Beispiel zustandsbehaftet ist. Tritt ein Fehler beim Speichern des Zustands auf, wird das Actor-Objekt deaktiviert und eine neue Instanz aktiviert. Eine Rückrufmethode, die den Actor-Zustand nicht ändert, kann als ein schreibgeschützter Timerrückruf registriert werden. Legen Sie hierfür wie im Abschnitt [Schreibschutzmethoden](service-fabric-reliable-actors-introduction.md#readonly-methods) beschrieben, das Attribute "Readonly“ für den Timerrückruf fest.

Wen der Actor im Rahmen der Garbage Collection deaktiviert wird, werden alle Timer angehalten und im Anschluss keine Timerrückrufaktionen ausgeführt. Zur Actor-Laufzeit werden zudem keine Informationen über die Timer gespeichert, die vor der Deaktivierung ausgeführt wurden. Es ist die Aufgabe des Actors, alle für zukünftige Reaktivierungen erforderlichen Timer zu registrieren. Weitere Informationen finden Sie im Abschnitt über die [Actor Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

## Actor-Erinnerungen
Erinnerungen sind ein Mechanismus für persistente Rückrufe in einem Actor zu bestimmten Zeiten. Die Funktionalität ähnelt Timern, wobei Erinnerungen bis zur expliziten Aufhebung durch den Actor unter allen Umständen ausgelöst werden. Erinnerungen werden insbesondere über Actor-Deaktivierungen und Failover ausgelöst, da zur Actor-Laufzeit Informationen über die Erinnerungen des Actors gespeichert werden.

Erinnerungen werden nur für zustandsbehaftete Actor unterstützt. Zustandsfreie Actor können keine Erinnerungen verwenden. Die Zustandsanbieter der Actor sind für das Speichern von Informationen zu den Erinnerungen verantwortlich, die vom jeweiligen Actor registriert wurden.

Zum Registrieren einer Erinnerung ruft ein Actor die in der Basisklasse bereitgestellte Methode `RegisterReminder` auf, wie im folgenden Beispiel gezeigt.

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

Im Beispiel oben ist `"Pay cell phone bill"` der Erinnerungsname, d. h. eine Zeichenfolge, die der Actor zur eindeutigen Identifizierung einer Erinnerung verwendet. `BitConverter.GetBytes(amountInDollars)` ist der mit der Erinnerung verknüpfte Kontext. Sie wird an den Actor als Argument für den Erinnerungsrückruf zurückgegeben, d. h. `IRemindable.ReceiveReminderAsync`.

Actor, die Erinnerungen verwenden, müssen die Schnittstelle `IRemindable` implementieren, wie im folgenden Beispiel gezeigt.

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

Wenn eine Erinnerung ausgelöst wird, ruft die Fabric Actors-Laufzeit auf dem Actor die Methode `ReceiveReminderAsync` auf. Ein Actor kann mehrere Erinnerungen registrieren. Die Methode `ReceiveReminderAsync` wird aufgerufen, sobald eine dieser Erinnerungen ausgelöst wurde. Der Actor kann mithilfe des an die Methode `ReceiveReminderAsync` übergebenen Erinnerungsnamens ermitteln, welche Erinnerung ausgelöst wurde.

Die Actors-Laufzeit speichert den Actor-Zustand nach Abschluss des Aufrufs `ReceiveReminderAsync`. Tritt ein Fehler beim Speichern des Zustands auf, wird das Actor-Objekt deaktiviert und eine neue Instanz aktiviert. Um anzugeben, dass der Zustand nach Abschluss des Erinnerungsrückrufs nicht gespeichert werden muss, kann das Flag `ActorReminderAttributes.ReadOnly` im Parameter `attributes` festgelegt werden, wenn die Methode `RegisterReminder` zum Erstellen der Erinnerung aufgerufen wird.

Zum Aufheben der Registrierung einer Erinnerung sollte die Methode `UnregisterReminder` aufgerufen werden, wie im folgenden Beispiel gezeigt.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Wie oben gezeigt, akzeptiert die Methode `UnregisterReminder` die Schnittstelle `IActorReminder`. Die Basisklasse des Actors unterstützt die Methode `GetReminder`, die zum Abrufen der Schnittstelle `IActorReminder` durch Übergabe des Erinnerungsnamens verwendet werden kann. Dies ist praktisch, da der Actor die Schnittstelle `IActorReminder` nicht beibehalten muss, die bei Aufruf der Methode `RegisterReminder` zurückgegeben wurde.

<!---HONumber=Oct15_HO3-->