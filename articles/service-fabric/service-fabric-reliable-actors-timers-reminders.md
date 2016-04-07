<properties
	pageTitle="Timer und Erinnerungen für Reliable Actors | Microsoft Azure"
	description="Einführung in Timer und Erinnerungen für Service Fabric Reliable Actors."
	services="service-fabric"
	documentationCenter=".net"
	authors="myamanbh"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="03/15/2016"
	ms.author="amanbha"/>


# Actor-Timer
Actor-Timer bieten einen einfachen Wrapper um .NET-Timer, wodurch die Rückrufmethoden die von der Actor-Laufzeit bereitgestellten wechselseitigen Parallelitätsgarantien respektieren.

Actor können sich mithilfe der Methoden `RegisterTimer` und `UnregisterTimer` ihrer Basisklasse bei ihren Timern an- und abmelden. Das folgende Beispiel zeigt die Verwendung von Timer-APIs. Die APIs sind dem .NET-Timer sehr ähnlich. Im Beispiel wird die `MoveObject`-Methode von der Actors-Laufzeit aufgerufen, wenn der Timer abgelaufen ist. Es ist sichergestellt, dass die Methode die wechselseitige Parallelität respektiert. Dies bedeutet, dass keine anderen Actor-Methoden oder Timer-/Erinnerungsrückrufe ausgeführt werden, bis dieser Rückruf die Ausführung abgeschlossen hat.

```csharp
class VisualObjectActor : StatefulActor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            state,                          // State to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
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
        return Task.FromResult(true);
    }
}
```

Der nächste Timerzeitraum beginnt nach Abschluss des Rückrufs. Dies bedeutet, dass der Timer während des Rückrufs angehalten und nach Abschluss des Rückrufs erneut gestartet wird.

Zur Actor-Laufzeit wird der Actor-Zustand bei Abschluss des Rückrufs gespeichert, wenn er wie im obigen Beispiel zustandsbehaftet ist. Tritt ein Fehler beim Speichern des Zustands auf, wird das Actor-Objekt deaktiviert und eine neue Instanz aktiviert. Eine Rückrufmethode, die den Actor-Zustand nicht ändert, kann als schreibgeschützter Timerrückruf registriert werden. Legen Sie hierfür das Attribut „Readonly“ für den Timerrückruf fest. Dies wird im Abschnitt zu [Readonly-Methoden](service-fabric-reliable-actors-introduction.md#readonly-methods) beschrieben.

Alle Timer werden angehalten, wenn der Actor im Rahmen einer Garbage Collection deaktiviert wird. Danach werden keine Timerrückrufe mehr aufgerufen. Zur Actor-Laufzeit werden zudem keine Informationen über die Timer gespeichert, die vor der Deaktivierung ausgeführt wurden. Es ist die Aufgabe des Actors, alle für zukünftige Reaktivierungen erforderlichen Timer zu registrieren. Weitere Informationen finden Sie im Abschnitt zur [Actor Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

## Actor-Erinnerungen
Erinnerungen sind ein Mechanismus zum Auslösen persistenter Rückrufe für einen Actor zu bestimmten Zeiten. Die Funktionalität ähnelt dabei Timern. Im Gegensatz zu Timern werden Erinnerungen aber unter allen Umständen ausgelöst, bis ihre Registrierung vom Actor explizit aufgehoben wird. Erinnerungen werden insbesondere über Actor-Deaktivierungen und Failover ausgelöst, da zur Actor-Laufzeit Informationen über die Erinnerungen des Actors gespeichert werden.

Erinnerungen werden nur für zustandsbehaftete Actors unterstützt. Zustandsfreie Actors können keine Erinnerungen verwenden. Die Zustandsanbieter der Actors sind für das Speichern von Informationen zu den Erinnerungen verantwortlich, die von Actors registriert wurden.

Zum Registrieren einer Erinnerung ruft ein Actor die in der Basisklasse bereitgestellte `RegisterReminder`-Methode auf, wie im folgenden Beispiel gezeigt.

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

Im obigen Beispiel ist `"Pay cell phone bill"` der Name der Erinnerung. Dies ist eine Zeichenfolge, die der Actor zur eindeutigen Identifizierung einer Erinnerung verwendet. `BitConverter.GetBytes(amountInDollars)` ist der mit der Erinnerung verknüpfte Kontext. Sie wird an den Actor als Argument für den Erinnerungsrückruf zurückgegeben, d. h. `IRemindable.ReceiveReminderAsync`.

Actors, die Erinnerungen verwenden, müssen die `IRemindable`-Schnittstelle implementieren, wie im folgenden Beispiel gezeigt.

```csharp
public class ToDoListActor : StatefulActor<ToDoList>, IToDoListActor, IRemindable
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

Wenn eine Erinnerung ausgelöst wird, ruft die Fabric Actors-Laufzeit auf dem Actor die `ReceiveReminderAsync`-Methode auf. Ein Actor kann mehrere Erinnerungen registrieren. Die `ReceiveReminderAsync`-Methode wird aufgerufen, sobald eine dieser Erinnerungen ausgelöst wurde. Der Actor kann mithilfe des an die Methode `ReceiveReminderAsync` übergebenen Erinnerungsnamens ermitteln, welche Erinnerung ausgelöst wurde.

Die Actors-Laufzeit speichert den Actor-Zustand nach Abschluss des Aufrufs `ReceiveReminderAsync`. Tritt ein Fehler beim Speichern des Zustands auf, wird das Actor-Objekt deaktiviert und eine neue Instanz aktiviert. Um anzugeben, dass der Zustand nach Abschluss des Erinnerungsrückrufs nicht gespeichert werden muss, kann das Flag `ActorReminderAttributes.ReadOnly` im Parameter `attributes` festgelegt werden, wenn die Methode `RegisterReminder` zum Erstellen der Erinnerung aufgerufen wird.

Zum Aufheben der Registrierung einer Erinnerung ruft ein Actor die `UnregisterReminder`-Methode auf, wie im folgenden Beispiel gezeigt.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Wie oben gezeigt, akzeptiert die Methode `UnregisterReminder` die Schnittstelle `IActorReminder`. Die Basisklasse des Actors unterstützt die Methode `GetReminder`, die zum Abrufen der Schnittstelle `IActorReminder` durch Übergabe des Erinnerungsnamens verwendet werden kann. Dies ist praktisch, da der Actor die Schnittstelle `IActorReminder` nicht beibehalten muss, die bei Aufruf der Methode `RegisterReminder` zurückgegeben wurde.

<!------HONumber=AcomDC_0316_2016-->