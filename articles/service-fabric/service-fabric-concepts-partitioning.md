<properties
   pageTitle="Partitionieren von Service Fabric-Diensten | Microsoft Azure"
   description="Beschreibt, wie Sie die Service Fabric-Dienste partitionieren."
   services="service-fabric"
   documentationCenter=".net"
   authors="bscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="bscholl"/>

# So wird‘s gemacht: Partitionieren von Service Fabric Reliable Services
Dieser Artikel enthält eine Einführung in die grundlegenden Konzepte der Partitionierung von Service Fabric Reliable Services. Der in diesem Artikel verwendete Quellcode ist auch unter [GitHub (add final link)](http://Github.com) verfügbar.

## Was ist die Partitionierung?
Die Partitionierung ist nicht auf Service Fabric beschränkt, sondern ein wichtiges Muster bei der Erstellung skalierbarer Dienste. In einem weiteren Sinne können wir uns die Partitionierung als ein Konzept vorstellen, bei dem der Zustand (Daten) und die Berechnung (Compute) in kleinere zugängliche Einheiten unterteilt werden, um die Skalierbarkeit und Leistung zu verbessern. Eine bekannte Form der Partitionierung ist die [Datenpartitionierung] (https://en.wikipedia.org/wiki/Partition_(database)), die auch als Sharding bezeichnet wird.


### Partitionieren von zustandslosen Service Fabric-Diensten
Bei zustandslosen Diensten können Sie sich eine Partition wie eine logische Einheit vorstellen, die mindestens eine Instanz eines Diensts enthält. Abbildung 1 zeigt einen zustandslosen Dienst mit fünf Instanzen, die mit einer Partition auf einen Cluster verteilt sind.

![Zustandsloser Dienst](./media/service-fabric-concepts-partitioning/statelessservice.png)

Es gibt im Grunde zwei Arten von Lösungen für zustandslose Dienste. Die erste ist ein Dienst, für den der Zustand extern beibehalten wird, z. B. in Azure SQL-Datenbank (etwa eine Website, auf der die Sitzungsinformationen und Daten gespeichert sind). Die zweite umfasst Dienste, die ausschließlich für die Berechnung bestimmt sind (etwa ein Rechner oder Bildminiaturansichten) und keinen beständigen Zustand verwalten. In beiden Fällen ist das Partitionieren eines zustandslosen Diensts ein sehr seltenes Szenario, und die Skalierbarkeit und Verfügbarkeit wird normalerweise erreicht, indem weitere Instanzen hinzugefügt werden. Sie sollten die Verwendung mehrerer Partitionen für zustandslose Dienstinstanzen nur erwägen, wenn Sie spezielle Routinganforderungen erfüllen müssen. Stellen Sie sich beispielsweise einen Fall vor, in dem Benutzer mit IDs in einem bestimmten Bereich nur von einer bestimmten Dienstinstanz versorgt werden sollen. Ein weiteres Beispiel für die mögliche Partitionierung eines zustandslosen Diensts ist, wenn Sie ein Back-End mit echter Partitionierung verwenden, z. B. eine SQL-Datenbank mit Sharding. Sie möchten steuern, welche Dienstinstanz in die Datenbankshard schreiben soll, oder andere Vorbereitungsschritte im zustandslosen Dienst ausführen, für die die gleichen Partitionierungsinformationen wie im Back-End benötigt werden. Diese Arten von Szenarios können auch auf unterschiedliche Arten gelöst werden, und eine Dienstpartitionierung ist nicht unbedingt erforderlich.

Im restlichen Teil dieser Vorgehensweise geht es um zustandsbehaftete Dienste.

### Partitionieren von zustandsbehafteten Service Fabric-Diensten
Mit Service Fabric ist es einfach, skalierbare zustandsbehaftete Dienste zu entwickeln, indem ein erstklassiger Weg zum Partitionszustand (Daten) angeboten wird. Vom Konzept her können Sie sich eine Partition eines zustandsbehafteten Diensts als Skalierungseinheit vorstellen, die aufgrund von [Replikaten](service-fabric-availability-services.md) hohe Verfügbarkeit bietet. Die Replikate sind gleichmäßig auf die Knoten im Cluster verteilt. Die Partitionierung im Kontext von zustandsbehafteten Service Fabric-Diensten bezieht sich auf den Prozess der Ermittlung, dass eine bestimmte Dienstpartition (wie bereits erwähnt besteht eine Partition aus einer Gruppe von [Replikaten](service-fabric-availability-services.md)) für einen Teil des vollständigen Zustands des Diensts zuständig ist. Der Vorteil von Service Fabric ist, dass die Partitionen auf unterschiedlichen Knoten angeordnet werden, sodass sie bis zum Ressourcenlimit eines Knotens anwachsen können. Wenn sich die Datenanforderungen erhöhen, werden die Partitionen größer, und Service Fabric verteilt die Partitionen neu auf die Knoten. So wird sichergestellt, dass die Hardwareressourcen weiterhin effizient genutzt werden.

Beispiel: Sie beginnen mit einem Cluster mit fünf Knoten und einem Dienst, der für zehn Partitionen und einem Ziel von drei Replikaten konfiguriert ist. In diesem Fall verteilt Service Fabric die Replikate gleichmäßig im Cluster, und es werden zwei primäre [Replikate](service-fabric-availability-services.md) pro Knoten verwendet. Wenn Sie den Cluster nun auf zehn Knoten horizontal hochskalieren müssen, verteilt Service Fabric die primären [Replikate](service-fabric-availability-services.md) neu auf alle zehn Knoten. Falls Sie wieder auf fünf Knoten herunterskalieren, verteilt Service Fabric alle Replikate neu auf die fünf Knoten.

Abbildung 2 zeigt die Verteilung von zehn Partitionen vor und nach dem Skalieren des Clusters.

![Zustandsbehafteter Dienst](./media/service-fabric-concepts-partitioning/scaledcluster.png)

Das horizontale Hochskalieren wird daher erreicht, indem Anforderungen von Clients über die Computer hinweg verteilt werden, die Gesamtleistung der Anwendung verbessert wird und Konflikte beim Zugreifen auf Datenblöcke verringert werden.

## Planen der Partitionierung
Vor der Implementierung eines Diensts sollten Sie immer die Partitionierungsstrategie bedenken, die für das horizontale Hochskalieren erforderlich ist. Es gibt unterschiedliche Ansätze, bei denen aber immer die Frage im Mittelpunkt steht, was mit der Anwendung erreicht werden soll. Im Rahmen dieses Artikels betrachten wir einige der wichtigeren Aspekte.

Ein guter Ansatz besteht darin, sich die Struktur des Zustands anzusehen, die im ersten Schritt partitioniert werden soll.

Wir sehen uns ein einfaches Beispiel an. Wenn Sie einen Dienst für eine Umfrage in einem Bezirk erstellen, können Sie eine Partition für jeden Ort des Bezirks erstellen und die Stimmabgabe für jede Person des Orts in der Partition für diesen Ort speichern. Abbildung 3 zeigt eine Gruppe von Personen und den Ort, in dem sie wohnen.

![Einfache Partition](./media/service-fabric-concepts-partitioning/cities.png)

Da die Einwohnerzahl der Orte stark variiert, verfügen Sie über einige Partitionen mit vielen Daten (z. B. Seattle) und andere Partitionen mit sehr wenigen Zuständen (z. B. Kirkland). Welche Auswirkungen hat es, wenn Partitionen mit ungleichen Zustandsmengen verwendet werden?

Wenn Sie sich das Beispiel erneut ansehen, fällt Ihnen auf, dass die Partition mit den Stimmabgaben für Seattle mehr Datenverkehr als die Partition für Kirkland aufweist. Standardmäßig wird von Service Fabric sichergestellt, dass für jeden Knoten ungefähr die gleiche Anzahl von primären und sekundären Replikaten vorhanden ist. Sie haben also unter Umständen einige Knoten mit Replikaten, für die mehr Datenverkehr anfällt, und einige Knoten, für die weniger Datenverkehr anfällt. Nach Möglichkeit sollten Sie „Hotspots“ und „Coldspots“ dieser Art in einem Cluster vermeiden.

Um dies zu erreichen, sollten Sie aus Sicht der Partitionierung zwei Dinge tun:

- Versuchen Sie, den Zustand so zu partitionieren, dass er gleichmäßig auf alle Partitionen verteilt ist.
- [Verfolgen Sie die Metriken für alle Replikate für den Dienst](service-fabric-reliable-services-advanced-usage.md). Service Fabric enthält eine Funktion zum Melden von Metriken für einen Dienst, z. B. die Arbeitsspeichermenge oder die Anzahl von Datensätzen. Anhand der gemeldeten Metriken erkennt Service Fabric, dass einige Partitionen über eine höhere Auslastung als andere Partitionen verfügen. Es wird eine neue Verteilung für den Cluster durchgeführt, indem Replikate auf besser geeignete Knoten verschoben werden.

Es kann vorkommen, dass Sie nicht wissen, wie viele Daten sich auf einer bestimmten Partition befinden. Also lautet die allgemeine Empfehlung, zweigleisig zu fahren: Verwenden einer Partitionierungsstrategie, bei der die Daten gleichmäßig auf die Partitionen verteilt werden, und Melden der Auslastung. Mit der ersten Methode werden Situationen verhindert, die im Beispiel mit der Umfrage beschrieben sind, und mit der zweiten Methode werden vorübergehende Unterschiede beim Zugriff oder der Auslastung in Abhängigkeit der Zeit ausgeglichen.

Ein weiterer Aspekt der Partitionsplanung ist das Wählen der richtigen Anzahl von Partitionen zu Beginn der Planungsphase. Aus Sicht von Service Fabric spricht nichts dagegen, mit einer höheren Anzahl von Partitionen zu beginnen, als Sie für Ihr Szenario erwarten. Es ist ein guter Ansatz, eine Annahme über die maximale Anzahl von Partitionen zu treffen.

In seltenen Fällen kann es vorkommen, dass Sie mehr Partitionen benötigen, als Sie zu Anfang gewählt haben. Da Sie die Partitionsanzahl später nicht mehr ändern können, müssen Sie andere Partitionsansätze anwenden, z. B. das Erstellen einer neuen Dienstinstanz desselben Diensttyps. Sie müssen eine clientseitige Logik implementieren, mit der die Anforderungen basierend auf clientseitigem Wissen, das von Ihrem Clientcode verwaltet werden muss, an die richtige Dienstinstanz geleitet werden.

Auch die verfügbaren Computerressourcen müssen bei der Partitionierungsplanung berücksichtigt werden. Da auf den Zustand zugegriffen und der Zustand gespeichert werden muss, sind Sie an Folgendes gebunden:

- Grenzen der Netzwerkbandbreite
- Grenzen des Systemarbeitsspeichers
- Grenzen des Datenträger-Speicherplatzes

Was passiert, wenn es auf einem ausgeführten Cluster zu Ressourceneinschränkungen kommt? Die Antwort lautet, dass Sie den Cluster einfach horizontal hochskalieren können, um die neuen Anforderungen abzudecken.

Der [Leitfaden zur Kapazitätsplanung](manisdoc.md) enthält eine Anleitung, wie Sie ermitteln, wie viele Knoten für Ihren Cluster benötigt werden.

## Durchführen der Partitionierung
In diesem Abschnitt wird beschrieben, wie Sie mit der Partitionierung Ihres Diensts beginnen.

Service Fabric verfügt über drei Partitionsschemas.

- Bereichsbasierte Partitionierung (auch als UniformInt64Partition bezeichnet)
- Namensbasierte Partitionierung: Anwendungen, für die dieses Modell verwendet wird, verfügen normalerweise über Daten, für die Buckets genutzt werden können (in einem gebundenen Satz). Einige häufige Beispiele für Datenfelder, die als benannte Partitionsschlüssel verwendet werden, sind Regionen, Postleitzahlen, Kundengruppen oder andere geschäftliche Grenzen.
- Singleton-Partitionierung: Singleton-Partitionen werden normalerweise verwendet, wenn für den Dienst kein zusätzliches Routing benötigt wird. Für zustandslose Dienste wird dieses Partitionierungsschema beispielsweise standardmäßig verwendet.

Bei namensbasierten und Singleton-Partitionierungsschemas handelt es sich um besondere Formen von bereichsbasierten Partitionen. Standardmäßig wird für Visual Studio-Vorlagen für Service Fabric die bereichsbasierte Partitionierung verwendet, da dies die häufigste und nützlichste Partitionierung ist. Im restlichen Teil dieses Artikels geht es um das bereichsbasierte Partitionierungsschema.

### Bereichsbasiertes Partitionierungsschema
Dieses Schema wird verwendet, um einen Integer-Bereich (identifiziert durch einen niedrigen und hohen Schlüssel) und eine Anzahl von Partitionen (n) anzugeben. Es werden „n“ Partitionen erstellt, die jeweils für einen nicht überlappenden Unterbereich des gesamten Partitionsschlüsselbereichs zuständig sind. Beispiel: Ein bereichsbasiertes Partitionierungsschema mit einem niedrigen Schlüssel 0, einem hohen Schlüssel 99 und einer Anzahl von 4 erstellt 4 Partitionen. Dies ist unten dargestellt.

![Bereichspartitionierung](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Ein gängiger Ansatz besteht darin, im Dataset einen Hashwert basierend auf einem eindeutigen Schlüssel zu erstellen. Ein Schlüssel kann beispielsweise eine Fahrgestellnummer, Mitarbeiter-ID oder eine eindeutige Zeichenfolge sein. Mit dem eindeutigen Schlüssel generieren Sie dann einen Hashcode (Modulo-Vorgang zum Berechnen des Schlüsselbereichs), den Sie als Ihren Schlüssel verwenden. Sie können den obere und untere Grenzwert des zulässigen Schlüsselbereichs angeben.


### Auswählen eines Hashalgorithmus
Beim Hashing spielt die Wahl des Hashalgorithmus eine wichtige Rolle. Ausschlaggebend ist, ob ähnliche nah beieinanderliegende Schlüssel gruppiert werden sollen (lokalitätsbasiertes Hashing), oder ob die Aktivität über alle Partitionen weitläufig verteilt werden soll (verteiltes Hashing). Letzteres wird häufiger verwendet.

Merkmale eines guten Hashalgorithmus für die Verteilung sind, dass er einfach zu berechnen ist, wenig Konflikte aufweist und die Schlüssel gleichmäßig verteilt. Ein gutes Beispiel für einen effizienten Hashalgorithmus ist der Hashalgorithmus [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function).


Hilfreiche Informationen zur Wahl des Hashalgorithmus finden Sie auf der [Wikipedia-Seite über Hashfunktionen](http://en.wikipedia.org/wiki/Hash_function).

## Erstellen eines zustandsbehafteten Diensts mit mehreren Partitionen
Sie erstellen jetzt Ihren ersten zuverlässigen zustandsbehafteten Dienst mit mehreren Partitionen. In diesem Beispiel erstellen Sie eine sehr einfache Anwendung, unter der Sie alle Nachnamen, die mit dem gleichen Buchstaben beginnen, in derselben Partition speichern möchten.

Bevor Sie Code schreiben, müssen Sie sich überlegen, welche Partitionen und Partitionsschlüssel verwendet werden sollen. Sie benötigen 26 Partitionen – eine für jeden Buchstaben des Alphabets. Aber was ist mit dem niedrigen und hohen Schlüssel? Da wir eine Partition pro Buchstabe benötigen, können wir 0 als niedrigen Schlüssel und 25 als hohen Schlüssel verwenden. So hat jeder Buchstabe seinen eigenen Schlüssel.


>[AZURE.NOTE]Dies ist ein vereinfachtes Szenario, da die Verteilung in der Praxis ungleichmäßig wäre. Nachnamen, die mit dem Buchstaben S oder M beginnen, treten häufiger als Nachnamen mit X oder Y auf.


1. Öffnen Sie „Visual Studio“ -> „Datei“ -> „Neu“ -> „Projekt“.
2. Wählen Sie im Dialogfeld „Neues Projekt“ die Service Fabric-Anwendung aus.
3. Geben Sie dem Projekt den Namen „AlphabetPartitions“.
4. Wählen Sie im Dialogfeld „Dienst erstellen“ den zustandsbehafteten Dienst aus, und geben Sie ihm den Namen „Alphabet.Processing“. Dies ist unten in der Abbildung dargestellt. ![alphabetstateful](./media/service-fabric-concepts-partitioning/alphabetstatefulnew.png)
5. Legen Sie die Anzahl der Partitionen fest. Öffnen Sie im Projekt „AlphabetPartitions“ die Datei „ApplicationManifest.xml“, und aktualisieren Sie den Parameter „Processing\_PartitionCount“ wie unten gezeigt auf 26.

    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
    Außerdem müssen Sie wie unten gezeigt die Eigenschaften „LowKey“ und „HighKey“ des StatefulService-Elements aktualisieren. ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```

6. Damit der Dienst zugänglich ist, öffnen Sie einen Endpunkt auf einem Port. Aktualisieren Sie hierzu wie folgt das Endpunktelement der Datei „ServiceManifest.xml“ (im Ordner „PackageRoot“) für den Dienst „Alphabet.Processing“:

    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Protocol="http" Type="Internal" />
    ```

    Der Dienst ist jetzt für das Lauschen über einen internen Endpunkt mit 26 Partitionen konfiguriert.

7. Als Nächstes müssen Sie die `CreateServiceReplicaListeners()`-Methode der Processing-Klasse überschreiben.

    >[AZURE.NOTE]In diesem Beispiel wird davon ausgegangen, dass Sie ein einfaches HttpCommunicationListener-Element verwenden. Weitere Informationen zur Reliable Services-Kommunikation finden Sie [hier](service-fabric-reliable-services-communication.md).

8. Ein empfohlenes Muster für die URL, unter der ein Replikat lauscht, ist das folgende Format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`. Es ist also ratsam, Ihren Kommunikationslistener an den richtigen Endpunkten lauschen zu lassen und dieses Muster zu verwenden. Mehrere Replikate dieses Diensts können auf demselben Computer gehostet werden. Diese Adresse muss für das Replikat also eindeutig sein, sodass in der URL die Partitions-ID und die Replikat-ID verwendet werden. HttpListener kann unter mehreren Adressen auf demselben Port lauschen, solange das URL-Präfix eindeutig ist. Die zusätzliche GUID ist für einen erweiterten Fall vorhanden, in dem sekundäre Replikate auch auf schreibgeschützte Anforderungen lauschen. In diesem Fall sollten Sie sicherstellen, dass beim Übergang von primären zu sekundären Replikaten eine neue eindeutige Adresse verwendet wird, um Clients zum Auflösen der Adresse zu zwingen. Hier wird „+“ als Adresse verwendet, damit das Replikat auf allen verfügbaren Hosts lauscht (IP, FQDN, localhost usw.). Mit dem folgenden Code wird ein Beispiel veranschaulicht.

    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
            return new[] { new ServiceReplicaListener(CreateInternalListener, "Internal", false) };
    }
    private ICommunicationListener CreateInternalListener(StatefulServiceInitializationParameters args)
    {
        EndpointResourceDescription internalEndpoint = args.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");

        string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                this.ServiceInitializationParameters.PartitionId,
                this.ServiceInitializationParameters.ReplicaId,
                Guid.NewGuid());

        string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
        string uriPublished = uriPrefix.Replace("+", nodeIP);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
Beachten Sie auch, dass sich die veröffentlichte URL leicht vom Überwachungs-URL-Präfix unterscheidet. Die Überwachungs-URL wird für HttpListener vergeben. Die veröffentlichte URL ist die URL, die für den Service Fabric Naming Service veröffentlicht wird. Dieser Dienst wird für die Diensterkennung verwendet. Clients fragen diese Adresse über den Ermittlungsdienst ab. Die Adresse, die Clients erhalten, muss über die tatsächliche IP oder den FQDN des Knotens verfügen, um eine Verbindung herstellen zu können. Sie müssen also wie oben gezeigt „+“ durch die IP oder den FQDN des Knotens ersetzen.
9. Der letzte Schritt ist das Hinzufügen der Verarbeitungslogik zum Dienst. Dies ist unten dargestellt.

    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
          string output = null;
          string user = context.Request.QueryString["lastname"].ToString();

          try
          {
              output = await this.AddUserAsync(user);
          }
          catch (Exception ex)
          {
              output = ex.Message;
          }

          using (HttpListenerResponse response = context.Response)
          {
              if (output != null)
              {
                  byte[] outBytes = Encoding.UTF8.GetBytes(output);
                  response.OutputStream.Write(outBytes, 0, outBytes.Length);
              }
          }
      }
      private async Task<string> AddUserAsync(string user)
      {
          IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");

          using (ITransaction tx = this.StateManager.CreateTransaction())
          {
              bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);

              await tx.CommitAsync();

              return String.Format(
                  "User {0} {1}",
                  user,
                  addResult ? "sucessfully added" : "already exists");
          }
      }
    ```

    `ProcessInternalRequest` liest die Werte des Abfragezeichenfolgenparameters, der zum Aufrufen der Partition verwendet wird, und ruft `AddUserAsync` auf, um den Nachnamen dem zuverlässigen Wörterbuch `m_name` hinzuzufügen.

10. Wir fügen dem Projekt nun einen zustandslosen Dienst hinzu, um zu verdeutlichen, wie Sie eine bestimmte Partition aufrufen können. Dieser Dienst dient als einfache Webschnittstelle, die den Nachnamen als Abfragezeichenfolgenparameter akzeptiert, den Partitionsschlüssel bestimmt und diesen zur Verarbeitung an den Alphabet.Processing-Dienst sendet.
11. Wählen Sie im Dialogfeld „Dienst erstellen“ die Option „Zustandsloser Dienst“, und vergeben Sie wie unten gezeigt den Namen „Alphabet.WebApi“. ![alphabetstateless](./media/service-fabric-concepts-partitioning/alphabetstatelessnew.png).
12. Aktualisieren Sie die Endpunktinformationen in der Datei „ServiceManifest.xml“ des Alphabet.WebApi-Diensts, um wie unten gezeigt einen Port zu öffnen.

    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8090"/>
    ```

13. Sie müssen eine Sammlung mit ServiceInstanceListener-Elementen zurückgeben. Auch hier können Sie wieder einen einfachen HttpCommunicationListener implementieren.

    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
           return new[] {new ServiceInstanceListener(this.CreateInputListener, "Input")};
    }
    private ICommunicationListener CreateInputListener(StatelessServiceInitializationParameters args)
    {
           // Service instance's URL is the node's IP & desired port
           EndpointResourceDescription inputEndpoint = args.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
           string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
           var uriPublished = uriPrefix.Replace("+", m_nodeIP);
           return new HttpCommunicationListener(uriPrefix, uriPublished, ProcessInputRequest);
     }
     ```
14. Als Nächstes müssen Sie die Verarbeitungslogik implementieren. Der HttpCommunicationListener ruft `ProcessInputRequest` auf, wenn eine Anforderung eingeht. Wir fügen unten jetzt also den Code hinzu.

    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
           String output = null;
           try
           {
               string lastname = context.Request.QueryString["lastname"];
               char firstLetterOfLastName = lastname.First();
               int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A';

               ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
               ResolvedServiceEndpoint ep = partition.GetEndpoint();
               JObject addresses = JObject.Parse(ep.Address);
               string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

               UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
               primaryReplicaUriBuilder.Query = "lastname=" + lastname;

               string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);

               output = String.Format(
               "Result: {0}. Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. Processing service partition ID: {4}. Processing service replica address: {5}",
               result,
               partitionKey,
               firstLetterOfLastName,
               lastname,
               partition.Info.Id,
               primaryReplicaAddress);
    }
    catch (Exception ex) { output = ex.Message; }
    using (var response = context.Response)
    {
               if (output != null)
               {
                   output = output + "added to Partition: " + primaryReplicaAddress;
                   byte[] outBytes = Encoding.UTF8.GetBytes(output);
                   response.OutputStream.Write(outBytes, 0, outBytes.Length);
               }
           }
      }
      ```

    Let's walk through it step by step. The code reads the first letter of the query string parameter `lastname` into a char. Then it determines the partition key for this letter by subtracting the hex value of `A` from the hex value of the last names' first letter.

    ```CSharp
    string lastname = context.Request.QueryString["lastname"]; char firstLetterOfLastName = lastname.First(); int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A'; ```

    Bedenken Sie, dass wir für dieses Beispiel 26 Partitionen mit einem Partitionsschlüssel pro Partition verwenden. Als Nächstes rufen wir die Dienstpartition `partition` für diesen Schlüssel ab, indem wir die `ResolveAsync`-Methode für das `servicePartitionResolver`-Objekt verwenden. `servicePartitionResolver` ist wie folgt definiert:

    ```CSharp
    private static readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```

    Für die `ResolveAsync`-Methode werden der Dienst-URI, der Partitionsschlüssel und ein Abbruchtoken als Parameter verwendet. Der Dienst-URI für den Verarbeitungsdienst lautet `fabric:/AlphabetPartitions/Processing`. Als Nächstes rufen wir den Endpunkt der Partition ab.

    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```

    Als Letztes erstellt wir die Endpunkt-URL sowie die Abfragezeichenfolge und rufen den Verarbeitungsdienst auf.

    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;

    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```

    Nachdem die Verarbeitung abgeschlossen ist, schreiben wir die Ausgabe wieder zurück.

15. Der letzte Schritt besteht darin, den Dienst zu testen. In Visual Studio werden Anwendungsparameter für die lokale Bereitstellung und die Cloudbereitstellung verwendet. Zum lokalen Testen des Diensts mit 26 Partitionen müssen Sie die Datei `Local.xml` im Ordner „ApplicationParameters“ des Projekts „AlphabetPartitions“ aktualisieren. Dies ist hier dargestellt:

    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
  </Parameters>
  ```

16. Nach der Bereitstellung können Sie den Dienst und alle Partitionen im Service Fabric Explorer überprüfen. ![Dienst](./media/service-fabric-concepts-partitioning/alphabetservicerunning.png)
17. In einem Browser können Sie die Partitionierungslogik testen, indem Sie `http://localhost:8090/?lastname=somename` eingeben. Sie sehen, dass alle Nachnamen mit dem gleichen Anfangsbuchstaben in derselben Partition gespeichert werden. !["Browser"](./media/service-fabric-concepts-partitioning/alphabetinbrowser.png)

Den gesamten Quellcode des Beispiels finden Sie bei [GitHub](www.github.com).

## Nächste Schritte

Informationen zu den Service Fabric-Konzepten finden Sie hier:

- [Verfügbarkeit der Service Fabric-Dienste](service-fabric-availability-services.md)

- [Skalierbarkeit der Service Fabric-Dienste](service-fabric-concepts-scalability.md)

<!---HONumber=Nov15_HO4-->