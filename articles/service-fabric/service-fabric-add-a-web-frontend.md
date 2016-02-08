<properties
   pageTitle="Erstellen eines Web-Front-End für Ihre Anwendung | Microsoft Azure"
   description="Stellen Sie die Service Fabric-Anwendung mithilfe eines ASP.NET 5-Web-API-Projekts und mithilfe von Kommunikation zwischen Diensten über ServiceProxy für das Web bereit."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/19/2016"
   ms.author="seanmck"/>


# Erstellen eines Web-Front-Ends für Ihre Anwendung

Standardmäßig enthalten Azure Service Fabric-Dienste keine öffentliche Web-Schnittstelle. Um die Funktionalität Ihrer Anwendung für HTTP-Clients verfügbar zu machen, müssen Sie ein Webprojekt als Einstiegspunkt erstellen und dann darüber mit den einzelnen Diensten kommunizieren.

In diesem Tutorial erfahren Sie, wie Sie ein ASP.NET 5 Web API Front-End zu einer Anwendung hinzufügen, die bereits einen Reliable Service umfasst, basierend auf der zustandsbehafteten Dienstprojektvorlage. Lesen Sie vor diesem Lernprogramm [Erstellung Ihrer ersten Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md), falls noch nicht geschehen.


## Hinzufügen eines ASP.NET 5-Dienstes zu Ihrer Anwendung

ASP.NET 5 ist ein einfaches, plattformübergreifendes Webentwicklungsframework, das Sie zur Erstellung von modernen Benutzeroberflächen und Web-APIs verwenden können. Nun fügen wir unserer vorhandenen Anwendung ein ASP.NET Web API-Projekt hinzu.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste im Anwendungsprojekt auf **Dienste**, und wählen Sie **Fabric-Dienst hinzufügen** aus.

	![Hinzufügen eines neuen Dienstes zu einer vorhandenen Anwendung][vs-add-new-service]

2. Wählen Sie „**ASP.NET 5**“ im Dialogfeld „**Erstellen eines Diensts**“ aus, und legen Sie einen Namen fest.

	![Auswählen eines neuen ASP.NET-Webdienstes im Dialogfeld „Neuer Dienst“][vs-new-service-dialog]

3. Die nächste Seite umfasst eine Reihe von ASP.NET 5-Projektvorlagen. Beachten Sie, dass dies die gleichen Vorlagen sind, wie wenn Sie ein ASP.NET 5-Projekts außerhalb einer Service Fabric-Anwendung erstellen. In diesem Tutorial wählen wir „**Web API**“. Sie können jedoch mit denselben Konzepten eine vollständige Webanwendung erstellen.

	![Auswählen des ASP.NET-Projekttyps][vs-new-aspnet-project-dialog]

    Nach Erstellung Ihres Web-API-Projekts stehen Ihnen zwei Dienste in Ihrer Anwendung zur Verfügung. Bei der weiteren Erstellung Ihrer Anwendung fügen Sie weitere Dienste auf die gleiche Weise hinzu. Diese können unabhängig versioniert und aktualisiert werden.

>[AZURE.NOTE] Ab der November Public Preview-Version von Service Fabric bestehen Probleme mit langen Pfaden beim Umgang mit ASP.NET-Projekten. Wenn Sie diese Typen von Projekten erstellen, empfiehlt es sich, kurze Namen für die Anwendungungs- und Diensttypen sowie Code- und Config-Paketnamen auszuwählen, um Probleme zu vermeiden.

## Ausführen der Anwendung

Um einen Eindruck unserer Arbeit zu erhalten, stellen wir die neue Anwendung bereit und sehen uns das Standardverhalten der ASP.NET 5 Web-API-Vorlage an.

1. Drücken Sie in Visual Studio die Taste F5, um die App zu debuggen.

2. Nach Abschluss der Bereitstellung startet Visual Studio den Browser im Stamm des ASP.NET Web API-Diensts, z. B. http://localhost:33003. Die Portnummer wird nach dem Zufallsprinzip zugewiesen und kann auf Ihrem Computer anders sein. Da die ASP.NET 5 Web-API-Vorlage kein Standardverhalten für den Stamm bereitstellt, wird eine Fehlermeldung im Browser angezeigt.

3. Fügen Sie dem Speicherort im Browser `/api/values` hinzu. Dies ruft die `Get` Methode im ValuesController in der Web-API-Vorlage auf. Dieser gibt die standardmäßige Antwort, die von der Vorlage bereitgestellt wird – ein JSON-Array, das zwei Zeichenfolgen enthält:

    ![Von der ASP.NET 5 Web-API-Vorlage zurückgegebene Standardwerte][browser-aspnet-template-values]

    Am Ende dieses Tutorials werden wir diese Standardwerte mit dem letzten Zählerwert aus unserem zustandsbehafteten Dienst ersetzen.


## Verbinden der Dienste

Service Fabric bietet absolute Flexibilität bei der Kommunikation mit Reliable Services. Innerhalb einer einzelnen Anwendung haben Sie womöglich Dienste, die über TCP zugänglich sind, andere Dienste, auf die über eine HTTP-REST-API zugegriffen werden kann und wiederum andere Dienste, auf die über WebSockets zugegriffen werden kann. Ausführliche Informationen zu den verfügbaren Optionen und deren Vor- und Nachteilen finden Sie unter [Kommunizieren mit Diensten](service-fabric-connect-and-communicate-with-services.md). In diesem Tutorial greifen wir auf einen einfacheren Ansatz zurück und verwenden die im SDK bereitgestellten `ServiceProxy`/`ServiceRemotingListener`-Klassen.

In dem `ServiceProxy` Ansatz (modelliert basierend auf Remoteprozeduraufrufen oder RPCs), definieren Sie eine Schnittstelle, die als der öffentliche Vertrag für den Dienst fungiert. Anschließend verwenden Sie diese Schnittstelle zum Generieren einer Proxyklasse für die Interaktion mit dem Dienst.


### Erstellen der Benutzeroberfläche

Wir erstellen zunächst die Benutzeroberfläche, die als Vertrag zwischen dem zustandsbehafteten Dienst und dessen Clients fungiert, einschließlich des ASP.NET 5-Projekts.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe und dann auf „**Hinzufügen**“ > „**Neues Projekt**“.

2. Wählen Sie im linken Navigationsbereich den **Visual C#**-Eintrag und dann die **Klassenbibliothek**-Vorlage aus. Stellen Sie sicher, dass Sie die .NET-Frameworkversion **4.5.1** verwenden.

    ![Erstellen eines Schnittstellenprojekts für Ihren zustandsbehafteten Dienst][vs-add-class-library-project]

3. Damit eine Schnittstelle von `ServiceProxy` verwendet werden kann, muss diese von der IService-Schnittstelle abgeleitet werden. Diese Schnittstelle ist in einem der Service Fabric-NuGet-Pakete enthalten. Klicken Sie zum Hinzufügen des Pakets mit der rechten Maustaste auf das neue Klassenbibliotheksprojekt, und wählen Sie **NuGet-Pakete verwalten** aus.

4. Stellen Sie sicher, dass das Kontrollkästchen „**Vorversion einschließen**“ aktiviert ist, suchen Sie nach dem **Microsoft.ServiceFabric.Services**-Paket, und installieren Sie es.

    ![Abrufen des Services NuGet-Pakets][vs-services-nuget-package]

5. Erstellen Sie in der Klassenbibliothek eine Schnittstelle mit einer einzelnen Methode, `GetCountAsync`, und erweitern Sie die Schnittstelle von IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### Implementieren der Schnittstelle in Ihrem zustandsbehafteten Dienst

Nach dem Definieren der Schnittstelle müssen wir sie in den zustandsbehafteten Dienst implementieren.

1. Fügen Sie Ihrem zustandsbehafteten Dienst einen Verweis auf das Klassenbibliotheksprojekt zu, das die Schnittstelle enthält.

    ![Hinzufügen eines Verweises auf das Klassenbibliotheksprojekt im zustandsbehafteten Dienst][vs-add-class-library-reference]

2. Suchen Sie die Klasse, die mit `StatefulService` verknüpft ist, z. B. `MyStatefulService`, und erweitern Sie sie zum Implementieren der `ICounter`-Schnittstelle.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Implementieren Sie jetzt die einzige Methode, die in der `ICounter`-Schnittstelle definiert ist, „`GetCountAsync`“.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### Bereitstellen des zustandsbehafteten Diensts mit „ServiceRemotingListener“

Nach dem Implementieren der `ICounter`-Schnittstelle besteht der letzte Schritt bei der Aktivierung des zustandsbehafteten Diensts darin, von anderen Diensten aufgerufen zu werden, um einen Kommunikationskanal zu öffnen. Für zustandsbehaftete Dienste bietet Service Fabric eine überschreibbare Methode namens „`CreateServiceReplicaListeners`“. Mit dieser Methode können Sie eine oder mehrere Kommunikationslistener angeben, basierend auf der Art der Kommunikation, die Sie in ihrem Dienst aktivieren möchten.

>[AZURE.NOTE] Die entsprechende Methode zum Öffnen eines Kommunikationskanals für zustandslose Dienste lautet `CreateServiceInstanceListeners`.

In diesem Fall ersetzen wir die vorhandene `CreateServiceReplicaListeners`-Methode und stellen eine Instanz des `ServiceRemotingListener` bereit. Dieser erstellt einen RPC-Endpunkt, der von Clients über `ServiceProxy` aufgerufen werden kann.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceRemotingListener<ICounter>(initParams, this))
    };
}
```


### Verwenden der ServiceProxy-Klasse für die Interaktion mit dem Dienst

Der zustandsbehaftete Dienst ist nun bereit, Datenverkehr von anderen Diensten zu empfangen. Es fehlt also nur noch das Hinzufügen des Codes, um durch ASP.NET Web Service mit dem Dienst zu kommunizieren.

1. Fügen Sie Ihrem ASP.NET-Projekt einen Verweis auf die Klassenbibliothek hinzu, welche `ICounter`-Schnittstelle enthält.

2. Fügen Sie dem ASP.NET-Projekt das Microsoft.ServiceFabric.Services-Paket auf dieselbe Weise hinzu wie zuvor beim Klassenbibliotheksprojekt. Dadurch wird die `ServiceProxy`-Klasse bereitgestellt.

3. Öffnen Sie im Ordner „**Controllers**“ die `ValuesController`-Klasse. Beachten Sie, dass derzeit nur die `Get`-Methode ein hartcodiertes Zeichenfolgenarray von „Wert1“ und „Wert2“ zurückgibt, das wir im vorherigen Browserverhalten gesehen haben. Ersetzen Sie diese Implementierung durch den folgenden Code:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    Die erste Codezeile ist der Schlüssel. Zum Erstellen des ICounter-Proxys für den zustandsbehafteten Dienst müssen Sie zwei Informationen angeben: die Partitions-ID und den Namen des Dienstes.

    Sie können durch die Partitionierung zustandsbehaftete Dienste skalieren, indem Sie ihren Status in verschiedene Buckets aufteilen, die auf einem von Ihnen definierten Schlüssel basieren, z. B. „Kunden-ID“ oder „PLZ“. In unserer einfachen Anwendung verfügt der zustandsbehaftete Dienst nur über eine Partition, sodass der Schlüssel keine Rolle spielt. Jeder Schlüssel, den Sie bereitstellen, wird auf dieselbe Partition führen. Weitere Informationen zum Partitionieren von Diensten finden Sie unter [Partitionieren von Service Fabric Reliable Services](service-fabric-concepts-partitioning.md).

    Der Dienstname ist eine URI der Formular-Fabric: /&lt;application\_name&gt;/&lt;service\_name&gt;.

    Mit diesen zwei Angaben kann Service Fabric den Computer eindeutig identifizieren, an den Anforderungen gesendet werden sollen. Die „`ServiceProxy`“-Klasse greift auch dann ein, wenn der Computers ausfällt, der die Partition des zustandsbehafteten Diensts hostet, und ein anderer Computer dessen Stelle einnehmen muss. Diese Abstraktion vereinfacht das Schreiben des Clientcodes für den Umgang mit anderen Diensten beträchtlich.

    Sobald wir den Proxy haben, rufen wir einfach die `GetCountAsync`-Methode auf und geben das Ergebnis zurück.

4. Drücken Sie erneut F5, um die geänderte Anwendung auszuführen. Wie zuvor startet Visual Studio automatisch den Browser zum Stamm des Webprojekts. Fügen Sie den „api/Values“-Pfad hinzu und der aktuelle Zählerwert sollte zurückgegeben werden.

    ![Anzeige des zustandsbehafteten Zählerwerts im Browser][browser-aspnet-counter-value]

    Aktualisieren Sie den Browser regelmäßig, um den aktuellen Zählerwert anzuzeigen.


## Was wird mit Akteuren verfahren?

Dieses Tutorial konzentriert sich auf das Hinzufügen des Web-Front-Ends, das mit einem zustandsbehafteten Dienst kommuniziert. Allerdings können Sie ein sehr ähnliches Modell befolgen, um mit Akteuren zu sprechen. Das ist sogar einfacher.

Wenn Sie ein Akteur-Projekt erstellen, generiert Visual Studio automatisch ein Schnittstellenprojekt für Sie. Diese Schnittstelle können Sie verwenden, um einen Akteur-Proxy im Webprojekt für die Kommunikation mit den Akteur zu generieren. Der Kommunikationskanal wird automatisch bereitgestellt. Es ist also nicht erforderlich, Vorgänge wie die Einrichtung eines `ServiceRemotingListener` wie beim zustandsbehafteten Dienst in diesem Tutorial durchzuführen.

## Funktionsweise von Webdiensten auf Ihrem lokalen Cluster

Im Allgemeinen können Sie genau die gleiche Service Fabric-Anwendung in einem Cluster mit mehreren Computern bereitstellen, die Sie auf dem lokalen Cluster bereitgestellt haben, und sicher sein, dass es wie erwartet funktioniert. Dies ist der Fall, da der lokale Cluster einfach eine Konfiguration mit fünf Knoten ist, die auf einen einzelnen Computer reduziert ist.

Bei Webdiensten gibt es jedoch eine wichtige Nuance. Wenn sich Ihr Cluster wie in Azure hinter einem Load Balancer befindet, müssen Sie sicherstellen, dass Ihre Webdienste auf jedem Computer bereitgestellt werden, da der Load Balancer den Datenverkehr einfach auf die Computer verteilt. Dafür legen Sie den Sonderwert „-1“ für den `InstanceCount` des Diensts fest.

Im Gegensatz dazu müssen Sie, wenn Sie einen Webdienst lokal ausführen, sicherstellen, dass nur eine Instanz des Diensts ausgeführt wird. Andernfalls entstehen Konflikten aus mehreren Prozessen, die den Pfad und den Port überwachen. Daher sollte bei lokalen Bereitstellungen der Wert für die Webdienstinstanzen „1“ lauten.

Informationen zum Konfigurieren verschiedener Werte für andere Umgebungen finden Sie unter [Verwalten von Anwendungsparametern für mehrere Umgebungen](service-fabric-manage-multiple-environment-app-configuration.md).

## Nächste Schritte

- [Erstellen eines Cluster in Azure zum Bereitstellen der Anwendung in der Cloud](service-fabric-cluster-creation-via-portal.md)
- [Weitere Informationen zum Kommunizieren mit Diensten](service-fabric-connect-and-communicate-with-services.md)
- [Weitere Informationen über die Partitionierung zustandsbehafteter Dienste](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=AcomDC_0128_2016-->