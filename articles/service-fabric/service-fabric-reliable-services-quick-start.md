<properties
   pageTitle="Erste Schritte mit Microsoft Azure Service Fabric Reliable Services"
   description="Erstellen einer Service Fabric-Anwendung mit zustandslosen und zustandsbehafteten Diensten."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/23/2015"
   ms.author="vturecek"/>

# Erste Schritte mit Microsoft Azure Service Fabric Reliable Services

Eine Service Fabric-Anwendung enthält einen oder mehrere Dienste zum Ausführen Ihres Codes. Dieses Lernprogramm führt Sie durch die Schritte zum Erstellen der zustandslosen und zustandsbehafteten Service Fabric-Anwendungen "Hello World" mithilfe des [*Reliable Services*-Programmiermodell](../Service-Fabric/service-fabric-reliable-services-introduction.md).

Zustandslose Dienste werden heute überwiegend in Cloudanwendungen eingesetzt. Diese Dienste gelten als zustandslos, da sie selbst keine Daten enthalten, die zuverlässig gespeichert werden oder hoch verfügbar sein müssen – mit anderen Worten, wenn eine Instanz eines zustandslosen Diensts heruntergefahren wird, geht deren gesamter interner Zustand verloren. Damit der Zustand dieser Dienste hoch verfügbar und zuverlässig ist, muss er extern gespeichert werden, z. B. in Azure-Tabellen oder in einer SQL-Datenbank.

Service Fabric führt eine neue Art von zustandsbehaftetem Dienst ein: Dieser Dienst kann seinen Zustand zuverlässig innerhalb des Diensts beibehalten. Er wird dort zusammen mit dem Code gespeichert, der ihn verwendet. Service Fabric stellt die hohe Verfügbarkeit des Zustands sicher, ohne dass dieser extern gespeichert werden muss.

In diesem Lernprogramm implementieren Sie sowohl einen zustandslosen als auch einen zustandsbehafteten Dienst, letzteren mit einem internen Zähler. Im zustandslosen Dienst geht der Wert des Zählers verloren, wenn der Dienst neu gestartet bzw. verschoben wird. Im zustandsbehafteten Dienst hingegen gewährleistet Service Fabric einen zuverlässigen Zählerzustand. Wird die Dienstausführung aus irgendeinem Grund während der Zählung unterbrochen, kann sie am Unterbrechungspunkt fortgesetzt werden.

## Erstellen eines zustandslosen Diensts

Beginnen wir mit einem zustandslosen Dienst.

Starten Sie Visual Studio 2015 RC als **Administrator**, und erstellen Sie eine neue **Service Fabric-Anwendung** mit dem Namen *HelloWorld*:

![Erstellen Sie im Dialogfeld „Neues Projekt“ eine neue Service Fabric-Anwendung.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Erstellen Sie anschließend ein Projekt für einen **zustandslosen Dienst** mit dem Namen *HelloWorldStateless*:

![Erstellen Sie im zweiten Dialogfeld einen zustandslosen Dienst.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Die Projektmappe enthält jetzt zwei Projekte:

 + **HelloWorld**: Dies ist das *Anwendungsprojekt*, das Ihre *Dienste* enthält. Darüber hinaus enthält es das Anwendungsmanifest, das die Anwendung und einer Reihe von PowerShell-Skripts beschreibt, mit deren Hilfe Sie die Anwendung bereitstellen können.
 + **HelloWorldStateless**: Dies ist das Dienstprojekt, das die Implementierung des zustandslosen Diensts enthält.


## Implementieren des Diensts

Öffnen Sie die Datei **HelloWorld.cs** im Dienstprojekt. In Service Fabric kann ein Dienst jegliche Art von Geschäftslogik ausführen. Die Dienst-API bietet zwei Einstiegspunkte für den Code:

 - Eine Einstiegspunktmethode mit offenem Ende namens *RunAsync*, mit der Sie eine beliebige Arbeitsauslastung verwenden können, wie etwa lang andauernde Computing-Arbeitsauslastungen.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Einen Einstiegspunkt für die Kommunikation, in den Sie den gewünschten Kommunikationsstapel \(z. B. Web-API\) einbinden können, um Anforderungen von Benutzern oder anderen Diensten zu empfangen.

```C#
protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}
```

In diesem Lernprogramm konzentrieren wir uns auf die Einstiegspunktmethode `RunAsync()`, mit der Sie Ihren Code sofort ausführen können. Die Projektvorlage enthält ein Beispiel für eine Implementierung von `RunAsync()`, die einen rollierenden Zähler schrittweise erhöht.

> [AZURE.NOTE]Weitere Informationen zum Arbeiten mit einem Kommunikationsstapel finden Sie unter [Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Die Plattform ruft diese Methode auf, wenn eine Instanz des Diensts platziert wurde und zur Ausführung bereit ist. Bei zustandslosen Diensten ist dies einfach der Fall, wenn die Dienstinstanz geöffnet wird. Ein Abbruchtoken koordiniert, wann die Dienstinstanz geschlossen werden muss. In Service Fabric kann dieser Offen-geschlossen-Zyklus einer Dienstinstanz oft über die Lebensdauer des Diensts als Ganzes aus verschiedenen Gründen und in verschiedenen Situationen auftreten, einschließlich:

- Das System kann die Dienstinstanzen für den Ressourcenausgleich verschieben.
- Innerhalb des Codes sind Fehler aufgetreten.
- Während Anwendungs- oder Systemaktualisierungen.
- Wenn die zugrunde liegende Hardware ausfällt.

Diese Orchestrierung wird vom System so verwaltet, dass der Dienst hoch verfügbar bleibt und die Lasten ordnungsgemäß verteilt sind.

`RunAsync()` wird in einem eigenen **Task** ausgeführt. Im Codeausschnitt oben verwenden wir direkt eine **while**-Schleife, da für die Arbeitsauslastung keine separate Aufgabe geplant werden muss. Zum Abbrechen der Arbeitsauslastung ist das Zusammenspiel verschiedener Aktionen erforderlich, die vom bereitgestellten Abbruchtoken orchestriert werden. Das System wartet darauf, dass der Task beendet wird \(entweder durch erfolgreichen Abschluss, durch Abbruch oder Fehler\), bevor es fortfährt. Es ist **wichtig**, das Abbruchtoken zu berücksichtigen, etwaige Arbeiten abzuschließen und `RunAsync()` so schnell wie möglich zu beenden, wenn vom System ein Abbruch angefordert wird.

In diesem Beispiel eines zustandslosen Diensts wird die Anzahl in einer lokalen Variablen gespeichert. Da es sich jedoch um einen zustandslosen Dienst handelt, existiert der gespeicherte Wert nur für den aktuellen Lebenszyklus der Dienstinstanz, in der er sich befindet. Wenn der Dienst verschoben oder neu gestartet wird, geht der Wert verloren.

## Erstellen eines zustandsbehafteten Diensts

Um den Zählerwert selbst bei einer Verschiebung oder einem Neustart des Diensts von zustandslos zu hoch verfügbar und persistent zu konvertieren, benötigen wir einen zustandsbehafteten Dienst.

Fügen Sie der Anwendung **HelloWorld** einen neuen Dienst hinzu, indem Sie mit der rechten Maustaste auf das Anwendungsprojekt klicken und die Option **Neuer Fabric-Dienst** auswählen.

![Fügen Sie der Service Fabric-Anwendung einen Dienst hinzu.](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Wählen Sie die Option für einen zustandsbehafteten Service Fabric-Dienst, und nennen Sie ihn "HelloWorldStateful". Klicken Sie auf **Hinzufügen**.

![Erstellen Sie im Dialogfeld "Neues Projekt" einen neuen zustandsbehafteten Service Fabric-Dienst.](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Ihre Anwendung sollte nun über zwei Dienste verfügen: den zustandslosen Dienst *HelloWorld* und den zustandsbehafteten Dienst *HelloWorldStateful*.

Öffnen Sie **HelloWorldStateful.cs** in *HelloWorldStateful*, das die folgende `RunAsync`-Methode enthält:

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

### RunAsync

Ein zustandsbehafteter Dienst hat die gleichen Einstiegspunkte wie ein zustandsloser Dienst. Der Hauptunterschied besteht in der Verfügbarkeit von *zuverlässigen Auflistungen* und des *Zustands-Managers*. `RunAsync()` agiert in einem zustandsbehafteten Dienst ähnlich wie ein zustandsloser Dienst, nur dass in einem zustandsbehafteten Dienst die Plattform vor dem Ausführen von `RunAsync()` zusätzliche Arbeit für Sie ausführt. Sie stellt beispielsweise sicher, dass der *Zustands-Manager* und die *zuverlässigen Auflistungen* verwendet werden können.

### Zuverlässige Auflistungen und Zustands-Manager

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** ist eine Wörterbuchimplementierung, mit der Sie zuverlässig den Zustand im Dienst speichern können. Dies ist Teil der in Service Fabric integrierten [zuverlässigen Auflistungen](service-fabric-reliable-services-reliable-collections.md). Mit Service Fabric und zuverlässigen Auflistungen können Sie Daten jetzt direkt in Ihrem Dienst speichern. Ein persistentes externes Speichern ist nicht erforderlich, sodass Ihre Daten hoch verfügbar sind. Service Fabric erreicht dies durch Erstellen und Verwalten mehrerer *Replikate* des Diensts. Gleichzeitig wird eine API bereitgestellt, welche die Verwaltung dieser Replikate und ihrer Zustandsübergänge abstrahiert.

Zuverlässige Auflistungen können mit gewissen Einschränkungen beliebige .NET-Typen – einschließlich benutzerdefinierten Typen – speichern:

 1. Service Fabric macht den Zustand hochverfügbar, indem es ihn auf verschiedenen Knoten *repliziert* und auf einem lokalen Datenträger speichert. Dies bedeutet, dass alle Elemente, die in einer zuverlässigen Auflistung gespeichert werden, *serialisierbar* sein müssen. Zuverlässige Auflistungen verwenden standardmäßig [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) für die Serialisierung. Stellen Sie daher bei Verwendung des Standardserialisierers unbedingt sicher, dass die Typen [vom Datenvertragsserialisierer unterstützt](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) werden.

 2. Objekte werden zum Zweck einer hohen Verfügbarkeit repliziert, wenn Sie eine Transaktion auf eine zuverlässige Auflistung anwenden. In zuverlässigen Auflistungen gespeicherte Objekte bleiben im lokalen Speicher in Ihrem Dienst erhalten. Sie verfügen somit über einen lokalen Verweis auf das Objekt.

    Es ist wichtig, dass Sie lokale Instanzen dieser Objekte nur ändern, nachdem Sie ein Update für die zuverlässige Auflistung in einer Transaktion durchgeführt haben, da diese Änderungen nicht automatisch repliziert werden.

Der *Zustands-Manager* verwaltet zuverlässige Auflistungen für Sie. Sie können vom Zustands-Manager jederzeit und überall im Dienst anhand des Namens eine zuverlässige Auflistung anfordern. Der Manager stellt sicher, dass Sie einen Verweis zurück erhalten. Es wird davon abgeraten, Verweise auf Instanzen zuverlässiger Auflistungen in Klassenmembervariablen oder -eigenschaften zu speichern. Es muss sonst ausdrücklich darauf geachtet werden, dass der Verweis während des gesamten Dienstlebenszyklus eine Instanz referenziert. Der Zustands-Manager übernimmt diese für wiederholte Besuche optimierte Arbeit für Sie.

### Transaktional und asynchron

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Zuverlässige Auflistungen verfügen über viele der gleichen Vorgänge wie ihre `System.Collections.Generic`- und `System.Collections.Concurrent`-Gegenstücke, einschließlich LINQ. Vorgänge für zuverlässige Auflistungen sind jedoch asynchron. Der Grund dafür liegt darin, dass Schreibvorgänge für zuverlässige Auflistungen *repliziert* werden. Dabei wird der Vorgang zum Zweck einer hohen Verfügbarkeit an andere Replikate des Diensts auf verschiedenen Knoten gesendet.

Sie unterstützen auch *Transaktionen*, damit Sie den Zustand zwischen mehreren zuverlässigen Auflistungen konsistent halten können. Sie können beispielsweise eine Arbeitsaufgabe aus einer zuverlässigen Warteschlange entfernen, einen Vorgang daran ausführen und das Ergebnis in einem zuverlässigen Wörterbuch speichern – alles in einer Transaktion. Dieser Vorgang wird als unteilbar behandelt, was sicherstellt, dass entweder der gesamte Vorgang erfolgreich ausgeführt wird oder gar nichts. Wenn ein Fehler auftritt, nachdem Sie das Element aus der Warteschlange entfernt haben, aber bevor Sie das Ergebnis speichern können, wird ein Rollback für die gesamte Transaktion durchgeführt, und das Element bleibt zur Verarbeitung in der Warteschlange.

## Ausführen der Anwendung

Zurück zur *HelloWorld*-Anwendung. Sie können jetzt Dienste erstellen und bereitstellen. Indem Sie **F5** drücken, wird die Anwendung erstellt und im lokalen Cluster bereitgestellt.

Sobald die Dienste ausgeführt werden, sehen Sie die generierten ETW-Ereignisse in einem Fenster mit Diagnoseereignissen. Beachten Sie, dass sowohl Ereignisse des zustandslosen Diensts als auch des zustandsbehafteten Diensts in der Anwendung angezeigt werden. Sie können den Stream anhalten, indem Sie auf die Schaltfläche *Pause* klicken. Zum Überprüfen der Nachrichtendetails erweitern Sie die Nachricht.

>[AZURE.NOTE]Stellen Sie vor dem Ausführen der Anwendung sicher, dass ein lokaler Entwicklungscluster ausgeführt wird. Informationen zum Einrichten Ihrer lokalen Umgebung erhalten Sie unter [Erste Schritte](service-fabric-get-started.md).

![Zeigen Sie Diagnoseereignisse in Visual Studio an.](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Nächste Schritte

[Debuggen einer Service Fabric-Anwendung in Visual Studio](service-fabric-debugging-your-application.md)

[Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server](service-fabric-reliable-services-communication-webapi.md)

[Erfahren Sie mehr über zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)

[Verwalten von Service Fabric-Diensten](service-fabric-manage-your-service-index.md)

[Entwicklerreferenz für zuverlässige Dienste](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=July15_HO5-->