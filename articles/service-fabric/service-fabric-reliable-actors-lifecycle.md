<properties
   pageTitle="Lebenszyklus von Azure Service Fabric Actors"
   description="Erläutert Lebenszyklus und Garbage Collection für Azure Service Fabric Actors"
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


#Actor-Lebenszyklus und Garbage Collection
Ein Actor wird aktiviert, wenn der erste Aufruf erfolgt. Er wird deaktiviert (Garbage Collection durch die Actors-Laufzeit), wenn er einige Zeit lang nicht verwendet wird. Informationen zum Konfigurieren dieses Zeitraums finden Sie im Abschnitt "Actor Garbage Collection" weiter unten.

Was geschieht bei der Actor-Aktivierung?

- Wenn ein Aufruf für einen Actor eingeht und er nicht bereits aktiv ist, wird ein neuer Actor erstellt.
- Sein Status wird geladen (bei einem statusbehafteten Actor)
- Methode `OnActivateAsync` (die bei der Actor-Implementierung überschrieben werden kann) wird aufgerufen.
- Sie wird einer Tabelle von aktiven Actors hinzugefügt.

Was geschieht bei der Actor-Deaktivierung?

- Wenn ein Actor einige Zeit lang nicht verwendet wird, wird er aus der Tabelle der aktiven Actors entfernt.
- Methode `OnDeactivateAsync` (die bei der Actor-Implementierung überschrieben werden kann) wird aufgerufen, wodurch alle Timer für den Actor gelöscht werden.

> [AZURE.TIP]Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit der Actor-Aktivierung und -Deaktivierung](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

## Actor Garbage Collection
Die Actors-Laufzeit sucht in regelmäßigen Abständen nach Actors, die eine Zeitlang nicht verwendet wurden, und deaktiviert sie. Nachdem sie deaktiviert wurden, kann CLR eine Garbage Collection für sie durchführen.

Was zählt für Zwecke der Garbage Collection als "wird verwendet"?

- Empfangen eines Aufrufs.
- Aufrufen von Methode `IRemindable.ReceiveReminderAsync` (gilt nur, wenn der Actor Erinnerungen verwendet).

Es ist erwähnenswert, dass wenn der Actor Timer verwendet und der Timer-Rückruf aufgerufen wird, er **nicht** als "wird verwendet" zählt.

Bevor das Thema Garbage Collection ausführlicher behandelt wird, ist es wichtig, die folgenden Begriffe zu definieren:

- *Scanintervall* - Das Intervall, in dem die Actors-Laufzeit ihre Tabelle der aktiven Actors nach Actors durchsucht, für die eine Garbage Collection durchgeführt werden kann. Der Standardwert dafür ist 1 Minute.
- *Leerlaufzeitlimit* - Die Zeitspanne, während der ein Actor inaktiv (im Leerlauf) sein muss, ehe eine Garbage Collection durchgeführt werden kann. Der Standardwert hierfür sind 60 Minuten.

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

Für jeden Actor in der Tabelle der aktiven Actors verfolgt die Actors-Laufzeit die Zeitspanne, während der er im Leerlauf war (d. h. nicht verwendet wurde). Die Actors-Laufzeit überprüft jeden Actor alle `ScanIntervalInSeconds`, um festzustellen, ob eine Garbage Collection für ihn durchgeführt werden kann, und erfasst ihn, wenn er sich seit `IdleTimeoutInSeconds` im Leerlauf befindet.

Bei jeder Verwendung eines Actors wird seine Leerlaufzeit auf 0 zurückgesetzt. Danach kann für den Actor nur dann eine Garbage Collection durchgeführt werden, wenn er erneut `IdleTimeoutInSeconds` lang im Leerlauf bleibt. Denken Sie daran, dass ein Actor als verwendet gilt, wenn entweder eine Actor-Schnittstellenmethode oder ein Erinnerungs-Rückruf ausgeführt wird. Ein Actor gilt **nicht** als verwendet, wenn der Timer-Rückruf ausgeführt wird.

Das folgende Diagramm enthält ein Beispiel zur Veranschaulichung dieser Konzepte.

![][1]

Das Beispiel geht davon aus, dass nur ein aktiver Actor in der Tabelle der aktiven Actors enthalten ist, und zeigt die Auswirkungen von Actor-Methodenaufrufen, Erinnerungen und Timer für die Lebensdauer dieses Actors. Die folgenden Punkte im Hinblick auf das Beispiel sind besonders erwähnenswert:

- "Scanintervall" und "IdleTimeout" werden im Beispiel auf 5 bzw. 10 festgelegt (Einheiten sind hier nicht wichtig, da es nur um die Veranschaulichung des Konzepts geht).
- Der Scanvorgang für Actors, für die eine Garbage Collection durchgeführt werden soll, erfolgt in den Intervallen T = 0,5, 10, 15, 20, 25, wie durch den ScanInterval-Wert 5 definiert.
- Ein periodischer Timer wird bei T = 4, 8, 12, 16, 20, 24 ausgelöst, und der Rückruf wird ausgeführt. Auf die Leerlaufzeit des Actors hat dies keine Auswirkungen.
- Ein Actor-Methodenaufruf bei T = 7 setzt die Leerlaufzeit auf 0 zurück und verzögert die Durchführung der Garbage Collection für den Actor.
- Ein Actor-Erinnerungs-Rückruf wird bei T = 14 ausgeführt und verzögert die Garbage Collection des Actors noch weiter.
- Während des Garbage Collection-Scanvorgangs bei T = 25 überschreitet die Leerlaufzeit des Actors schließlich den IdleTimeout-Wert von 10, und die Garbage Collection wird für den Actor durchgeführt.

Bitte beachten Sie, dass für einen Actor nie eine Garbage Collection durchgeführt wird, während er eine seiner Methoden ausführt, unabhängig davon, wie viel Zeit die Ausführung dieser Methode beansprucht. Wie bereits erwähnt, verhindert die Ausführung von Actor-Schnittstellenmethoden und Erinnerungs-Rückrufen die Durchführung der Garbage Collection, indem die Leerlaufzeit des Actors auf 0 zurückgesetzt wird. Durch die Ausführung von Timer-Rückrufen wird die Leerlaufzeit nicht auf 0 zurückgesetzt. Allerdings wird die Garbage Collection des Actors verzögert, bis der Timer-Rückruf abgeschlossen ist.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
 

<!---HONumber=July15_HO4-->