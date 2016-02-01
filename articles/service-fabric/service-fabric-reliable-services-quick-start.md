<properties
   pageTitle="Erste Schritte mit Reliable Services | Microsoft Azure"
   description="Einführung in das Erstellen einer Microsoft Azure Service Fabric-Anwendung mit zustandslosen und zustandsbehafteten Diensten."
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
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# Erste Schritte mit Service Fabric Reliable Services

Eine Azure Service Fabric-Anwendung enthält einen oder mehrere Dienste zum Ausführen von Code. In diesem Leitfaden wird veranschaulicht, wie Sie zustandslose und zustandsbehaftete Service Fabric-Anwendungen mit [Reliable Services](service-fabric-reliable-services-introduction.md) erstellen.

## Erstellen eines zustandslosen Diensts

Ein zustandsloser Dienst ist eine Art von Dienst, der in Cloudanwendungen derzeit die Norm ist. Er wird als zustandslos angesehen, weil der Dienst selbst keine Daten enthält, die zuverlässig gespeichert werden oder hoch verfügbar sein müssen. Wenn eine Instanz eines zustandslosen Diensts heruntergefahren wird, geht sein gesamter interner Zustand verloren. Damit der Zustand dieser Dienste hoch verfügbar und zuverlässig ist, muss er extern gespeichert werden, z. B. in Azure-Tabellen oder in einer SQL-Datenbank.

Starten Sie Visual Studio 2015 RC als Administrator, und erstellen Sie ein neues Projekt mit einer Service Fabric-Anwendung, das den Namen *HelloWorld* trägt:

![Erstellen Sie über das Dialogfeld „Neues Projekt“ eine neue Service Fabric-Anwendung.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Erstellen Sie anschließend ein Projekt für einen zustandslosen Dienst mit dem Namen *HelloWorldStateless*:

![Erstellen Sie im zweiten Dialogfeld ein Projekt für einen zustandslosen Dienst.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Die Projektmappe enthält jetzt zwei Projekte:

 - *HelloWorld*: Dies ist das *Anwendung*sprojekt, das Ihre *Dienste* enthält. Darüber hinaus enthält es das Anwendungsmanifest zum Beschreiben der Anwendung sowie eine Reihe von PowerShell-Skripts, mit deren Hilfe Sie die Anwendung bereitstellen.
 - *HelloWorldStateless*: Dies ist das Dienstprojekt. Es enthält die Implementierung des zustandslosen Diensts.


## Implementieren des Diensts

Öffnen Sie im Dienstprojekt die Datei **HelloWorld.cs**. In Service Fabric kann mit einem Dienst jegliche Art von Geschäftslogik ausgeführt werden. Die Dienst-API bietet zwei Einstiegspunkte für den Code:

 - Eine Einstiegspunktmethode mit offenem Ende, die den Namen *RunAsync* hat und mit der Sie mit der Ausführung von Workloads beginnen können. Dies können auch Compute-Workloads mit langer Ausführungsdauer sein.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Einen Einstiegspunkt für die Kommunikation, den Sie mit einem beliebigen Kommunikationsstapel verbinden können, z. B. der ASP.NET Web-API. Dies ist der Punkt, an dem Sie mit dem Empfangen der Anforderungen von Benutzern und anderen Diensten beginnen können.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

In diesem Tutorial geht es um die Einstiegspunktmethode `RunAsync()`. Hiermit können Sie sofort mit der Ausführung des Codes beginnen. Die Projektvorlage enthält ein Beispiel für eine Implementierung von `RunAsync()`, die einen rollierenden Zähler schrittweise erhöht.

> [AZURE.NOTE]Ausführliche Informationen zur Verwendung eines Kommunikationsstapels finden Sie unter [Web-API-Dienste von Service Fabric mit selbstgehostetem OWIN](service-fabric-reliable-services-communication-webapi.md).


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

Die Plattform ruft diese Methode auf, wenn eine Instanz des Diensts platziert wurde und zur Ausführung bereit ist. Bei zustandslosen Diensten ist dies einfach der Fall, wenn die Dienstinstanz geöffnet wird. Ein Abbruchtoken koordiniert, wann die Dienstinstanz geschlossen werden muss. In Service Fabric kann dieser Offen/Geschlossen-Zyklus einer Dienstinstanz über die gesamte Lebensdauer des Diensts häufig auftreten. Dies kann aus unterschiedlichen Gründen geschehen, z. B.:

- Das System verschiebt Ihre Dienstinstanzen, um einen Lastenausgleich für Ressourcen durchzuführen.
- In Ihrem Code treten Fehler auf.
- Die Anwendung oder das System werden aktualisiert.
- Die zugrunde liegende Hardware fällt aus.

Diese Orchestrierung wird vom System verwaltet, um sicherzustellen, dass der Dienst hoch verfügbar bleibt und die Lasten richtig verteilt sind.

`RunAsync()` wird in einer eigenen Aufgabe ausgeführt. Beachten Sie, dass wir im obigen Codeausschnitt direkt in eine *while*-Schleife gesprungen sind. Es ist nicht erforderlich, für Ihre Workload eine separate Aufgabe einzuplanen. Zum Abbrechen der Arbeitsauslastung ist das Zusammenspiel verschiedener Aktionen erforderlich, die vom bereitgestellten Abbruchtoken orchestriert werden. Das System wartet, bis Ihre Aufgabe beendet wurde (erfolgreicher Abschluss, Abbruch oder Fehler), bevor der Vorgang fortgesetzt wird. Es ist wichtig, das Abbruchtoken zu berücksichtigen, etwaige Arbeiten abzuschließen und `RunAsync()` so schnell wie möglich zu beenden, wenn vom System ein Abbruch angefordert wird.

In diesem Beispiel eines zustandslosen Diensts wird die Anzahl in einer lokalen Variablen gespeichert. Da es sich aber um einen zustandslosen Dienst handelt, existiert der gespeicherte Wert nur für den aktuellen Lebenszyklus der Dienstinstanz. Wenn der Dienst verschoben oder neu gestartet wird, geht der Wert verloren.

## Erstellen eines zustandsbehafteten Diensts

Mit Service Fabric wird eine neue Art von zustandsbehaftetem Dienst eingeführt. Bei einem zustandsbehafteten Dienst kann der Zustand zuverlässig innerhalb des Diensts selbst verwaltet und dem Code zugeordnet werden, in dem er verwendet wird. Service Fabric stellt die hohe Verfügbarkeit des Zustands sicher, ohne dass dieser extern gespeichert werden muss.

Um einen Zählerwert selbst bei einer Verschiebung oder einem Neustart des Diensts von zustandslos zu hoch verfügbar und persistent zu konvertieren, benötigen Sie einen zustandsbehafteten Dienst.

Sie können der Anwendung *HelloWorld* einen neuen Dienst hinzufügen, indem Sie mit der rechten Maustaste auf das Anwendungsprojekt klicken und die Option **Fabric-Dienst hinzufügen** wählen.

![Fügen Sie der Service Fabric-Anwendung einen Dienst hinzu.](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Wählen Sie **Zustandsbehafteter Dienst**, und geben Sie ihm den Namen *HelloWorldStateful*. Klicken Sie auf **OK**.

![Verwenden Sie das Dialogfeld „Neues Projekt“, um einen zustandsbehafteten Service Fabric-Dienst zu erstellen.](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Ihre Anwendung sollte nun über zwei Dienste verfügen: den zustandslosen Dienst *HelloWorld* und den zustandsbehafteten Dienst *HelloWorldStateful*.

Öffnen Sie **HelloWorldStateful.cs** in *HelloWorldStateful*. Darin ist die folgende RunAsync-Methode enthalten:

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

Ein zustandsbehafteter Dienst hat die gleichen Einstiegspunkte wie ein zustandsloser Dienst. Der Hauptunterschied ist die Verfügbarkeit von Reliable Collections und des Zustands-Managers. Die Ausführung von `RunAsync()` ist für zustandsbehaftete und zustandslose Dienste ähnlich. Bei einem zustandsbehafteten Dienst werden von der Plattform aber noch weitere Schritte in Ihrem Namen ausgeführt, bevor `RunAsync()` ausgeführt wird. Hierzu kann auch die Sicherstellung gehören, dass der Zustands-Manager und Reliable Collections bereit für die Verwendung sind.

### Reliable Collections und der Zustands-Manager

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

*IReliableDictionary* ist eine Wörterbuchimplementierung, die Sie nutzen können, um den Zustand im Dienst zuverlässig zu speichern. Dies ist Teil der in Service Fabric integrierten [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Mit Service Fabric und Reliable Collections können Sie Daten direkt in Ihrem Dienst speichern. Ein externer persistenter Speicher ist nicht erforderlich. Dieser Ansatz sorgt dafür, dass Ihre Daten hoch verfügbar sind. Service Fabric erreicht dies, indem mehrere *Replikate* des Diensts für Sie erstellt und verwaltet werden. Außerdem wird eine API bereitgestellt, mit der die komplexen Verwaltungsanforderungen dieser Replikate und der damit verbundenen Zustandsübergänge beseitigt werden.

Reliable Collections können mit gewissen Einschränkungen beliebige .NET-Typen – einschließlich benutzerdefinierten Typen – speichern:

 - Service Fabric macht den Zustand hoch verfügbar, indem er auf verschiedenen Knoten *repliziert* und auf einem lokalen Datenträger gespeichert wird. Dies bedeutet, dass alle Elemente, die in Reliable Collections gespeichert werden, *serialisierbar* sein müssen. Reliable Collections verwenden standardmäßig [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) für die Serialisierung. Stellen Sie daher bei Verwendung des Standardserialisierers unbedingt sicher, dass die Typen [vom Datenvertragsserialisierer unterstützt](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) werden.

 - Objekte werden zum Zweck einer hohen Verfügbarkeit repliziert, wenn Sie Transaktionen auf Reliable Collections anwenden. In Reliable Collections gespeicherte Objekte werden in Ihrem Dienst im lokalen Speicher vorgehalten. Dies bedeutet, dass Sie über einen lokalen Verweis auf das Objekt verfügen.

    Es ist wichtig, dass Sie lokale Instanzen dieser Objekte nicht ändern, ohne ein Update für die Reliable Collection in einer Transaktion durchzuführen. Der Grund ist, dass diese Änderungen nicht automatisch repliziert werden.

Mit dem Zustands-Manager werden Reliable Collections für Sie verwaltet. Sie können über den Zustands-Manager jederzeit und von jedem Ort aus anhand des Namens eine Reliable Collection anfordern. Der Zustands-Manager stellt sicher, dass Sie einen Verweis zurückerhalten. Es ist nicht ratsam, Verweise auf Reliable Collection-Instanzen in Klassenmembervariablen oder -eigenschaften zu speichern. Achten Sie besonders darauf sicherzustellen, dass der Verweis während des Dienstlebenszyklus jederzeit auf eine Instanz festgelegt ist. Der Zustands-Manager übernimmt diesen Schritt für Sie, und der Vorgang ist für wiederholte Besuche optimiert.

### Transaktionale und asynchrone Vorgänge

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collections verfügen über viele Vorgänge, die auch für `System.Collections.Generic` und `System.Collections.Concurrent` möglich sind, z. B. LINQ. Vorgänge für zuverlässige Auflistungen sind jedoch asynchron. Der Grund ist, dass Schreibvorgänge bei Reliable Collections *repliziert* werden. Zur Sicherstellung der hohen Verfügbarkeit werden diese Vorgänge an andere Replikate des Diensts auf unterschiedlichen Knoten gesendet.

Sie unterstützen auch *Transaktionen*, damit Sie den Zustand zwischen mehreren Reliable Collections konsistent halten können. Sie können beispielsweise eine Arbeitsaufgabe aus einer zuverlässigen Warteschlange entfernen, einen Vorgang dafür ausführen und das Ergebnis in einem zuverlässigen Wörterbuch speichern – alles innerhalb einer Transaktion. Dies wird wie ein atomarischer Vorgang behandelt, und es ist garantiert, dass entweder der gesamte Vorgang erfolgreich ist oder kein Teil des Vorgangs erfolgreich ist. Wenn nach dem Entfernen des Elements aus der Warteschlange und vor dem Speichern des Ergebnisses ein Fehler auftritt, wird für die gesamte Transaktion ein Rollback ausgeführt, und das Element bleibt zur Verarbeitung in der Warteschlange enthalten.

## Ausführen der Anwendung

Wir kehren nun zur Anwendung *HelloWorld* zurück. Sie können jetzt Dienste erstellen und bereitstellen. Wenn Sie **F5** drücken, wird die Anwendung erstellt und im lokalen Cluster bereitgestellt.

Nach dem Beginn der Dienstausführung können Sie die generierten ETW-Ereignisse (Event Tracing for Windows, Ereignisablaufverfolgung für Windows) im Fenster **Diagnoseereignisse** anzeigen. Beachten Sie, dass sowohl Ereignisse des zustandslosen Diensts als auch des zustandsbehafteten Diensts in der Anwendung angezeigt werden. Sie können den Datenstrom anhalten, indem Sie auf die Schaltfläche **Anhalten** klicken. Sie können die Details einer Nachricht dann prüfen, indem Sie sie erweitern.

>[AZURE.NOTE]Stellen Sie vor dem Ausführen der Anwendung sicher, dass ein lokaler Entwicklungscluster ausgeführt wird. Informationen zum Einrichten Ihrer lokalen Umgebung finden Sie im [Leitfaden zu den ersten Schritten](service-fabric-get-started.md).

![Zeigen Sie Diagnoseereignisse in Visual Studio an.](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Nächste Schritte

[Debuggen einer Service Fabric-Anwendung in Visual Studio](service-fabric-debugging-your-application.md)

[Erste Schritte: Web-API-Dienste von Service Fabric mit selbstgehostetem OWIN](service-fabric-reliable-services-communication-webapi.md)

[Erfahren Sie mehr über zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)

[Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)

[Anwendungsupgrade](service-fabric-application-upgrade.md)

[Entwicklerreferenz für zuverlässige Dienste](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->