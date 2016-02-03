<properties
   pageTitle="Reliable Actors-Lebenszyklus | Microsoft Azure"
   description="Erläutert Lebenszyklus und Garbage Collection für Service Fabric Reliable Actors"
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
   ms.date="01/20/2016"
   ms.author="amanbha"/>


# Actor-Lebenszyklus und Garbage Collection
Ein Actor wird aktiviert, wenn sein erster Aufruf erfolgt. Ein Actor wird deaktiviert (Garbage Collection durch die Actors-Laufzeit), wenn er einige Zeit lang nicht verwendet wird. Informationen zum Konfigurieren dieses Zeitraums finden Sie im Abschnitt „Actor-Garbage Collection“ weiter unten.

Was geschieht bei der Actor-Aktivierung?

- Wenn ein Aufruf für einen Actor eingeht und er nicht bereits aktiv ist, wird ein neuer Actor erstellt.
- Der Status des Actors wird geladen (bei einem statusbehafteten Actor).
- Die Methode `OnActivateAsync` (die bei der Actor-Implementierung überschrieben werden kann) wird aufgerufen.
- Der Actor wird einer Tabelle von aktiven Actors hinzugefügt.

Was geschieht bei der Actor-Deaktivierung?

- Wenn ein Actor einige Zeit lang nicht verwendet wird, wird er aus der Tabelle der aktiven Actors entfernt.
- Die Methode `OnDeactivateAsync` (die bei der Actor-Implementierung überschrieben werden kann) wird aufgerufen. Dadurch werden alle Timer für den Actor gelöscht.

> [AZURE.TIP]Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit der Actor-Aktivierung und -Deaktivierung](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

## Actor-Garbage Collection
Die Actors-Laufzeit sucht in regelmäßigen Abständen nach Actors, die eine Zeitlang nicht verwendet wurden, und deaktiviert sie. Sobald die Actors deaktiviert sind, können sie durch die Common Language Runtime (CLR) mit einer Garbage Collection entfernt werden.

Was zählt für Zwecke der Garbage Collection als "wird verwendet"?

- Empfangen eines Aufrufs
- Aufrufen von Methode `IRemindable.ReceiveReminderAsync` (gilt nur, wenn der Actor Erinnerungen verwendet).

Es ist erwähnenswert, dass wenn der Actor Timer verwendet und der Timer-Rückruf aufgerufen wird, er **nicht** als "wird verwendet" zählt.

Bevor das Thema Garbage Collection ausführlicher behandelt wird, ist es wichtig, die folgenden Begriffe zu definieren:

- *Scanintervall*. Das Intervall, in dem die Actors-Laufzeit ihre Tabelle der aktiven Actors nach Actors durchsucht, für die eine Garbage Collection ausgeführt werden kann. Der Standardwert dafür ist 1 Minute.
- *Leerlauftimeout*. Die Zeitspanne, während der ein Actor inaktiv (im Leerlauf) sein muss, ehe eine Garbage Collection ausgeführt werden kann. Der Standardwert hierfür sind 60 Minuten.

In der Regel müssen Sie diese Standardeinstellungen nicht ändern. Falls jedoch erforderlich, können diese Intervalle auf Assemblyebene für alle Actor-Typen in der Assembly oder auf Actor-Typ-Ebene mithilfe von Attribut `ActorGarbageCollection` geändert werden. Das folgende Beispiel zeigt die Änderung an den Garbage Collection-Intervallen für HelloActor.

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

Um den Standardwert des Attributs `ActorGarbageCollection` auf Assemblyebene zu ändern, fügen Sie `AssemblyInfo.cs` den folgenden Codeausschnitt hinzu.

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

Für jeden Actor in der Tabelle der aktiven Actors verfolgt die Actors-Laufzeit die Zeitspanne, während der er im Leerlauf war (d. h. nicht verwendet wurde). Die Actors-Laufzeit überprüft jeden Actor alle `ScanIntervalInSeconds`, um festzustellen, ob eine Garbage Collection für ihn ausgeführt werden kann, und erfasst ihn, wenn er sich seit `IdleTimeoutInSeconds` im Leerlauf befindet.

Bei jeder Verwendung eines Actors wird seine Leerlaufzeit auf 0 zurückgesetzt. Danach kann für den Actor nur dann eine Garbage Collection ausgeführt werden, wenn er erneut `IdleTimeoutInSeconds` lang im Leerlauf bleibt. Denken Sie daran, dass ein Actor als verwendet gilt, wenn entweder eine Actor-Schnittstellenmethode oder ein Erinnerungs-Rückruf ausgeführt wird. Ein Actor gilt **nicht** als verwendet, wenn der Timer-Rückruf ausgeführt wird.

Das folgende Diagramm enthält ein Beispiel zur Veranschaulichung dieser Konzepte.

![Beispiel für Leerlaufzeit][1]

Das Beispiel setzt voraus, dass nur ein aktiver Actor in der Tabelle der aktiven Actors vorhanden ist. Das Beispiel zeigt die Auswirkung von Actor-Methodenaufrufen, Erinnerungen und Timern auf die Lebensdauer dieses Actors. Die folgenden Punkte im Hinblick auf das Beispiel sind besonders erwähnenswert:

- „ScanInterval“ und „IdleTimeout“ werden auf 5 und 10 festgelegt. (Einheiten sind hier nicht wichtig, da nur das Konzept veranschaulicht werden soll.)
- Der Scanvorgang für Actors, für die eine Garbage Collection ausgeführt werden soll, erfolgt in den Intervallen T = 0, 5, 10, 15, 20, 25, wie durch den Scanintervallwert 5 definiert.
- Ein periodischer Timer wird bei T = 4, 8, 12, 16, 20, 24 ausgelöst, und der Rückruf wird ausgeführt. Auf die Leerlaufzeit des Actors hat dies keine Auswirkungen.
- Ein Actor-Methodenaufruf bei T = 7 setzt die Leerlaufzeit auf 0 zurück und verzögert die Ausführung der Garbage Collection für den Actor.
- Ein Actor-Erinnerungs-Rückruf wird bei T = 14 ausgeführt und verzögert die Garbage Collection des Actors noch weiter.
- Während des Garbage Collection-Scanvorgangs bei T = 25 überschreitet die Leerlaufzeit des Actors schließlich den Leerlauftimeout-Wert von 10, und die Garbage Collection wird für den Actor ausgeführt.

Beachten Sie, dass für einen Actor nie eine Garbage Collection durchgeführt wird, während er eine seiner Methoden ausführt, unabhängig davon, wie viel Zeit die Ausführung dieser Methode beansprucht. Wie bereits erwähnt, verhindert die Ausführung von Actor-Schnittstellenmethoden und Erinnerungs-Rückrufen die Durchführung der Garbage Collection, indem die Leerlaufzeit des Actors auf 0 zurückgesetzt wird. Durch die Ausführung von Timer-Rückrufen wird die Leerlaufzeit nicht auf 0 zurückgesetzt. Allerdings wird die Garbage Collection des Actors verzögert, bis der Timer-Rückruf abgeschlossen ist.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=AcomDC_0121_2016-->