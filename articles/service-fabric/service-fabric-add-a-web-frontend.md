<properties
   pageTitle="Erstellen eines Web-Front-Ends für Ihre Anwendung | Microsoft Azure"
   description="Stellen Sie die Service Fabric-Anwendung mithilfe eines ASP.NET 5-Web-API-Projekts und Kommunikation zwischen Diensten über ServiceProxy für das Web bereit."
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
   ms.date="11/21/2015"
   ms.author="seanmck"/>


# Erstellen eines Web-Front-Ends für Ihre Anwendung

Standardmäßig enthalten Service Fabric-Dienste keine öffentliche Web-Schnittstelle. Um die Funktionalität Ihrer Anwendung für HTTP-Clients verfügbar zu machen, müssen Sie ein Webprojekt als Einstiegspunkt erstellen und dann darüber mit den einzelnen Diensten kommunizieren.

In diesem Tutorial erfahren Sie, wie Sie ein ASP.NET 5 Web API Front-End zu einer Anwendung hinzufügen, die bereits einen Reliable Service umfasst, basierend auf der zustandsbehafteten Dienstprojektvorlage. Lesen Sie vor diesem Lernprogramm [Erstellung Ihrer ersten Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md), falls noch nicht geschehen.


## Hinzufügen eines ASP.NET 5-Dienstes zu Ihrer Anwendung

ASP.NET 5 ist ein einfaches, plattformübergreifendes Webentwicklungsframework, das die Erstellung von modernen Benutzeroberflächen und Web-APIs ermöglicht. Nun fügen wir unserer vorhandenen Anwendung ein ASP.NET Web API-Projekt hinzu.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste im Anwendungsprojekt auf **Dienste**, und wählen Sie **Fabric-Dienst hinzufügen** aus.

	![Hinzufügen eines neuen Dienstes zu einer vorhandenen Anwendung][vs-add-new-service]

2. Wählen Sie im Dialogfeld für den neuen Dienst **ASP.NET 5** aus, und legen Sie einen Namen fest.

	![Auswählen eines neuen ASP.NET-Webdienstes im Dialogfeld „Neuer Dienst“][vs-new-service-dialog]

3. Das nächste Dialogfeld umfasst eine Reihe von ASP.NET 5-Projektvorlagen. Beachten Sie, dass die gleichen Vorlagen wie beim Erstellen eines ASP.NET 5-Projekts außerhalb einer Service Fabric-Anwendung angezeigt werden. Für dieses Tutorial wählen wir **Web-API**, Sie können jedoch mit denselben Konzepten eine vollständige Webanwendung erstellen.

	![Auswählen des ASP.NET-Projekttyps][vs-new-aspnet-project-dialog]

    Nach Erstellung Ihres Web-API-Projekts stehen Ihnen zwei Dienste in Ihrer Anwendung zur Verfügung. Bei der weiteren Erstellung Ihrer Anwendung fügen Sie weitere Dienste auf die gleiche Weise hinzu. Diese können unabhängig versioniert und aktualisiert werden.


## Ausführen der Anwendung

Um unsere Arbeit zu überprüfen, stellen wir die neue Anwendung bereit und sehen uns das Standardverhalten der ASP.NET 5 Web-API-Vorlage an.

1. Drücken Sie in Visual Studio die Taste F5, um die Anwendung zu debuggen.

2. Nach Abschluss der Bereitstellung startet Visual Studio den Browser im Stamm des ASP.NET Web API-Diensts, z. B. http://localhost:33003 (die Portnummer wird nach dem Zufallsprinzip zugewiesen und kann auf Ihrem Computer anders lauten). Da die ASP.NET 5 Web-API-Vorlage kein Standardverhalten für den Stamm bereitstellt, wird eine Fehlermeldung im Browser angezeigt.

3. Fügen Sie dem Speicherort im Browser `/api/values` hinzu. Dadurch wird die `Get`-Methode im ValuesController in der Web-API-Vorlage aufgerufen und die Standardantwort von der Vorlage zurückgegeben – ein JSON-Array mit zwei Zeichenfolgen:

    ![Von der ASP.NET 5 Web-API-Vorlage zurückgegebene Standardwerte][browser-aspnet-template-values]

    Am Ende dieses Tutorials werden wir diese Standardwerte mit dem letzten Zählerwert aus unserem zustandsbehafteten Dienst ersetzen.


## Verbinden der Dienste

Service Fabric bietet absolute Flexibilität bei der Kommunikation mit Reliable Services. In einer einzigen Anwendung verfügen Sie möglicherweise über Dienste, die über TCP, über die HTTP REST-API und über Web-Sockets abrufbar sind. Ausführliche Informationen zu den verfügbaren Optionen und deren Vor- und Nachteile finden Sie unter [Kommunizieren mit Diensten](service-fabric-connect-and-communicate-with-services.md). In diesem Tutorial greifen wir auf einen einfacheren Ansatz zurück und verwenden die im SDK bereitgestellten `ServiceProxy`/`ServiceRemotingListener`-Klassen.

Im `ServiceProxy`-Ansatz (in Remoteprozeduraufrufen (RPC) entwickelt) definieren Sie eine Schnittstelle, die als öffentlicher Vertrag für den Dienst fungiert, und verwenden diese Schnittstelle, um eine Proxyklasse für die Interaktion mit dem Dienst zu generieren.


### Erstellen der Benutzeroberfläche

Wir erstellen zunächst die Benutzeroberfläche, die als Vertrag zwischen dem zustandsbehafteten Dienst und dessen Clients fungiert, einschließlich des ASP.NET 5-Projekts.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe und dann auf **Hinzufügen > Neues Projekt**.

2. Wählen Sie im linken Navigationsbereich den Visual C#-Eintrag und dann die **Klassenbibliothek**-Vorlage aus. Stellen Sie sicher, dass Sie die .NET-Frameworkversion 4.5.1 verwenden.

    ![Erstellen eines Schnittstellenprojekts für Ihren zustandsbehafteten Dienst][vs-add-class-library-project]

3. Damit eine Schnittstelle von `ServiceProxy` verwendet werden kann, muss sie von der IService-Schnittstelle abgeleitet werden, die in einem der Service Fabric NuGet-Pakete enthalten ist. Klicken Sie zum Hinzufügen des Pakets mit der rechten Maustaste auf das neue Klassenbibliotheksprojekt, und wählen Sie **NuGet-Pakete verwalten** aus.

4. Stellen Sie sicher, dass das Kontrollkästchen **Vorversion einschließen** aktiviert ist, suchen Sie nach dem **Microsoft.ServiceFabric.Services**-Paket, und installieren Sie es.

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

Dach dem Definieren der Schnittstelle müssen wir sie in unseren zustandsbehafteten Dienst implementieren.

1. Fügen Sie Ihrem zustandsbehafteten Dienst einen Verweis auf das Klassenbibliotheksprojekt mit der Schnittstelle hinzu.

    ![Hinzufügen eines Verweises auf das Klassenbibliotheksprojekt im zustandsbehafteten Dienst][vs-add-class-library-reference]

2. Suchen Sie die Klasse, die mit `StatefulService` verknüpft ist, z. B. `MyStatefulService`, und erweitern Sie sie zum Implementieren der `ICounter`-Schnittstelle.

    ```c#
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Implementieren Sie jetzt die einzige Methode, die die `ICounter`-Schnittstelle definiert, `GetCountAsync`.

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

Nach dem Implementieren der `ICounter`-Schnittstelle besteht der letzte Schritt bei der Aktivierung des zustandsbehafteten Diensts darin, von anderen Diensten aufgerufen zu werden, um einen Kommunikationskanal zu öffnen. Für zustandsbehaftete Dienste stellt Service Fabric die überschreibbare Methode `CreateServiceReplicaListeners` bereit. Hier können Sie einen oder mehrere Kommunikationslistener angeben, die auf dem Kommunikationstyp basieren, den Sie für Ihren Dienst aktivieren möchten.

>[AZURE.NOTE]Die entsprechende Methode zum Öffnen eines Kommunikationskanals für zustandslose Dienste lautet `CreateServiceInstanceListeners`.

In diesem Fall stellen wir einen `ServiceRemotingListener` bereit, der einen RPC-Endpunkt erstellt, der von Clients aufgerufen werden kann, die `ServiceProxy` verwenden.

```c#
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


### Verwenden von ServiceProxy zum Interagieren mit dem Dienst

Unser zustandsbehafteter Dienst kann nun Datenverkehr von anderen Diensten empfangen. Jetzt muss nur noch der Code hinzugefügt werden, der vom ASP.NET-Webdienst damit kommunizieren soll.

1. Fügen Sie Ihrem ASP.NET-Projekt einen Verweis auf die Klassenbibliothek mit der `ICounter`-Schnittstelle hinzu.

2. Fügen Sie dem ASP.NET-Projekt das Microsoft.ServiceFabric.Services-Paket auf dieselbe Weise hinzu wie zuvor beim Klassenbibliotheksprojekt. Dadurch wird die `ServiceProxy`-Klasse bereitgestellt.

3. Öffnen Sie im Controllers-Ordner die `ValuesController`-Klasse. Beachten Sie, dass derzeit nur die `Get`-Methode ein hartcodiertes Zeichenfolgenarray von „Wert1“ und „Wert2“ zurückgibt, das dem vorherigen Browserverhalten entspricht. Ersetzen Sie diese Implementierung durch den folgenden Code:

    ```c#
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    Die erste Codezeile ist der Schlüssel. Zum Erstellen des ICounter-Proxys für den zustandsbehafteten Dienst müssen Sie zwei Informationen angeben: die Partitions-ID und den Namen des Dienstes.

    Durch die Partitionierung können Sie zustandsbehaftete Dienste skalieren, indem Sie ihren Status in verschiedene Buckets aufteilen, die auf einem von Ihnen definierten Schlüssel definieren, z. B. Kunden-ID oder PLZ. In unserer einfachen Anwendung verfügt der zustandsbehaftete Dienst nur über eine Partition, sodass der Schlüssel keine Rolle spielt – jeder von Ihnen bereitgestellte Schlüssel führt zur gleichen Partition. Weitere Informationen zum Partitionieren von Diensten finden Sie unter [Partitionieren von Service Fabric Reliable Services](service-fabric-concepts-partitioning).

    Der Dienstname ist eine URI der Formular-Fabric: /&lt;application\_name&gt;/&lt;service\_name&gt;.

    Mit diesen zwei Angaben kann Service Fabric den Computer eindeutig identifizieren, an den Anforderungen gesendet werden sollen. Der `ServiceProxy` wird auch bei Ausfall des Computers angewendet, der die Partition des zustandsbehafteten Diensts hostet, und ein anderer Computer muss dessen Stelle einnehmen. Diese Abstraktion vereinfacht das Schreiben des Clientcodes für den Umgang mit anderen Diensten beträchtlich.

    Sobald wir den Proxy haben, rufen wir einfach die `GetCountAsync`-Methode auf und geben das Ergebnis zurück.

4. Drücken Sie erneut F5, um die geänderte Anwendung auszuführen. Wie zuvor startet Visual Studio automatisch den Browser zum Stamm des Webprojekts. Bei Hinzufügen des „api/Values“-Pfads sollte der aktuelle Zählerwert zurückgegeben werden.

    ![Anzeige des zustandsbehafteten Zählerwerts im Browser][browser-aspnet-counter-value]

    Aktualisieren Sie den Browser regelmäßig, um den aktuellen Zählerwert anzuzeigen.


## Was wird mit Akteuren verfahren?

Dieses Tutorial konzentriert sich auf das Hinzufügen des Web-Front-Ends, das mit einem zustandsbehafteten Dienst kommuniziert. Sie können aber mit einem sehr ähnlichen Modell mit Akteuren kommunizieren. Das ist sogar einfacher.

Wenn Sie ein Akteur-Projekt erstellen, generiert Visual Studio automatisch ein Schnittstellenprojekt für Sie. Diese Schnittstelle können Sie verwenden, um einen Akteur-Proxy im Webprojekt für die Kommunikation mit den Akteur zu generieren. Der Kommunikationskanal wird automatisch bereitgestellt. Vorgänge wie die Einrichtung eines `ServiceRemotingListener` wie beim zustandsbehafteten Dienst in diesem Tutorial sind also nicht erforderlich.

## Ausführen von Webdiensten auf einem lokalen Cluster

Im Allgemeinen können Sie genau die gleiche Service Fabric-Anwendung in einem Cluster mit mehreren Computern bereitstellen, den Sie auf dem lokalen Cluster bereitgestellt haben, und sicher sein, dass es wie erwartet funktioniert, da der lokale Cluster einfach eine auf einen einzelnen Computer reduzierte Fünf-Knoten-Konfiguration ist.

Bei Webdiensten gibt es jedoch eine wichtige Nuance. Wenn sich Ihr Cluster wie in Azure hinter einem Load Balancer befindet, müssen Sie sicherstellen, dass Ihre Webdienste auf jedem Computer bereitgestellt werden, da der Load Balancer den Datenverkehr einfach auf die Computer verteilt. Dafür legen Sie den Sonderwert „-1“ für den `InstanceCount` des Diensts fest. Beim lokalen Ausführen müssen Sie dagegen sicherstellen, dass nur eine Instanz des Dienstes ausgeführt wird. Andernfalls führt das zu Konflikten aus mehreren Prozessen, die den Pfad und den Port überwachen. Daher sollte bei lokalen Bereitstellungen der Wert für die Webdienstinstanzen „1“ lauten.

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

<!---HONumber=AcomDC_1217_2015-->