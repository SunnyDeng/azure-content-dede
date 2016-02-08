<properties 
	pageTitle="Partitionieren von Daten in DocumentDB mit dem .NET SDK | Microsoft Azure" 
	description="Erfahren Sie, wie Sie das Azure DocumentDB .NET SDK verwenden, um Daten zu partitionieren (Sharding) und Anforderungen über mehrere Sammlungen hinweg weiterleiten." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="arramac"/>

# Partitionieren von Daten in DocumentDB mit dem .NET SDK

Azure DocumentDB ist ein Dokumentdatenbankdienst, der Ihnen durch Bereitstellung von Sammlungen mithilfe von [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx) und [REST-APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) (auch **Sharding** genannt) eine nahtlose Skalierung Ihres Kontos ermöglicht. Um die Entwicklung von partitionierten Anwendungen zu vereinfachen und die Menge der für Partitionierungsaufgaben erforderlichen Codebausteine zu reduzieren, wurden Funktionen in das .NET SDK eingefügt, mit denen sich Anwendungen leichter erstellen lassen, die über mehrere Partitionen horizontal skaliert werden.

In diesem Artikel werden die Klassen und Schnittstellen im .NET SKDK betrachtet, und es wird erläutert, wie Sie diese für die Entwicklung partitionierter Anwendungen verwenden können

## Partitionieren mit dem DocumentDB SDK

Bevor wir genauer auf das Thema Partitionierung eingehen, betrachten wir einige grundlegende DocumentDB-Konzepte in Zusammenhang mit der Partitionierung. Jedes Azure DocumentDB-Datenbankkonto besteht aus einer Reihe von Datenbanken, die jeweils mehrere Sammlungen umfassen, die wiederum gespeicherte Prozeduren, Trigger, UDFs, Dokumente und zugehörige Anhänge enthalten können. Sammlungen können in DocumentDB als Partitionen behandelt werden und weisen folgende Merkmale auf:

- Sammlungen sind physische Partitionen, nicht lediglich logische Container. Deshalb ergibt sich ein Leistungsvorteil beim Abfragen oder Verarbeiten von Dokumenten, die sich in der gleichen Sammlung befinden.
- Sammlungen sind die Grenzen für ACID-Transaktionen, also gespeicherte Prozeduren und Trigger.
- Sammlungen erzwingen kein Schema und können daher für JSON-Dokumente des gleichen Typs oder unterschiedlicher Typen verwendet werden.

Ab Version [1\.1.0 des Azure DocumentDB .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) können Sie Dokumentvorgänge direkt in einer Datenbank durchführen. Intern verwendet der [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) den "PartitionResolver", den Sie angegeben haben, damit die Datenbank Anforderungen an die entsprechende Sammlung weiterleitet.

Jede PartitionResolver-Klasse ist eine konkrete Implementierung einer [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)-Schnittstelle mit drei Methoden: [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) und [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). LINQ-Abfragen und ReadFeed-Iteratoren verwenden die ResolveForRead-Methode intern zum Durchlaufen aller Sammlungen, die dem Partitionsschlüssel für die Anforderung entsprechen. Ebenso wird bei Erstellungsvorgängen die ResolveForCreate-Methode zum Weiterleiten von Erstellungsvorgängen an die richtige Partition. Für Ersetzungs-, Lösch- und Lesevorgänge sind keine Änderungen erforderlich, da diese Dokumente verwenden, die bereits den Verweis auf die entsprechende Sammlung enthalten.

Das SDK enthält außerdem zwei Klassen, die über einen [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) und einen [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) die beiden kanonischen Partitionierungsverfahren unterstützen: Hashing und Bereichslookups. Sie können diese Klassen verwenden, um Ihrer Anwendung ganz einfach Partitionierungslogik hinzuzufügen.

## Hinzufügen von Partitionierungslogik und Registrieren des "PartitionResolver" 

Folgender Codeausschnitt zeigt, wie ein [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) erstellt und beim "DocumentClient" für eine Datenbank registriert wird.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## Erstellen von Dokumenten in einer Partition  

Sobald der "PartitionResolver" registriert wurde, können Sie Erstellungs- und Abfragevorgänge direkt in der Datenbank ausführen, wie unten dargestellt. In diesem Beispiel verwendet das SDK den "PartitionResolver", um die Benutzer-ID zu extrahieren, sie mit einem Hash-Wert zu versehen und diesen Wert zu verwenden, um den Erstellungsvorgang an die richtige Sammlung weiterzuleiten.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## Erstellen von Abfragen für Partitionen  

Sie können Abfragen mit der [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)-Methode ausführen, indem Sie die Datenbank und einen Partitionsschlüssel übergeben. Die Abfrage gibt ein einzelnes Resultset über alle Sammlungen in der Datenbank zurück, die dem Partitionsschlüssel zugeordnet sind.

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## Erstellen von Abfragen für alle Sammlungen in der Datenbank 

Sie können auch alle Sammlungen in der Datenbank abfragen und die Ergebnisse durch Überspringen des Partitionsschlüsselarguments auflisten, wie unten gezeigt.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## HashPartitionResolver
Bei der Hashpartitionierung werden basierend auf dem Wert einer Hashfunktion Partitionen zugewiesen, sodass Sie Anforderungen und Daten gleichmäßig über eine Anzahl von Partitionen verteilen können. Dieses Verfahren wird häufig zur Partitionierung von Daten verwendet, die aus einer großen Anzahl von unterschiedlichen Clients erzeugt oder genutzt werden, und eignet sich zum Speichern von Benutzerprofilen, Katalogelementen und IoT-Telemetriedaten (Internet of Things, Internet der Dinge).

**Hashpartitionierung:**![Diagramm zur Veranschaulichung, wie die Hashpartitionierung Anforderungen gleichmäßig auf Partitionen verteilt](media/documentdb-sharding/partition-hash.png)

Ein einfaches Hashpartitionierungsschema über *N* Sammlungen könnte darin bestehen, dass für ein beliebiges Dokument *hash(d) mod N* berechnet wird, um zu ermitteln, in welcher Sammlung es sich befindet. Ein Problem mit diesem einfachen Verfahren ist jedoch, dass es nicht gut funktioniert, wenn Sie neue Sammlungen hinzufügen oder Sammlungen entfernen, da dafür fast alle Daten neu zusammengestellt werden müssten. Das [konsistente Hashing](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) ist ein bekannter Algorithmus, der dieses Problem löst, indem ein Hashingschema implementiert wird, das die Anzahl der während des Erstellens oder Entfernens von Sammlungen erforderlichen Datenverschiebungen minimiert.

Die [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)-Klasse implementiert die Logik zum Erstellen eines konsistenten Hashrings über die in der [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx)-Schnittstelle angegebene Hashfunktion. Standardmäßig verwendet der "HashPartitionResolver" eine MD5-Hashfunktion, Sie können diese jedoch durch Ihre eigene Hashingimplementierung ersetzen. Der "HashPartitionResolver" erstellt intern für jede Sammlung 16 Hashes oder "virtuelle Knoten" innerhalb des Hashrings, um eine einheitlichere Verteilung von Dokumenten über die Sammlung zu erreichen. Sie können diese Zahl jedoch variieren, um Datenasymmetrien und die Menge an clientseitigen Berechnungsvorgängen auszubalancieren.

**Konsistentes Hashing mit "HashPartitionResolver":** ![Diagramm zur Veranschaulichung, wie "HashPartitionResolver" einen Hashring erstellt](media/documentdb-sharding/HashPartitionResolver.JPG)

## RangePartitionResolver

In einer Bereichspartitionierung werden Partitionen basierend darauf zugewiesen, ob der Partitionsschlüssel in einem bestimmten Bereich liegt. Dies wird häufig für Partitionierungen mit time stamp-Eigenschaften verwendet (z. B. eventTime zwischen dem 1. April 2015 und dem 14. April 2015). Die [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)-Klasse unterstützt Sie dabei, eine Zuordnung zwischen einem "Range<T>" und einem "self-link" für eine Sammlung zu verwalten.

[Range<T>](https://msdn.microsoft.com/library/azure/mt126048.aspx) ist eine einfache Klasse, die Bereiche aller Typen verwaltet, die "IComparable<T>" und "IEquatable<T>" wie etwa Zeichenfolgen oder Zahlen implementieren. Für alle Lese- und Erstellungsvorgänge können Sie einen frei wählbaren Bereich übergeben. Der Resolver identifiziert alle Kandidatensammlungen, indem er die Bereiche der Partitionen identifiziert, die sich mit dem angeforderten Bereich überschneiden. Diese Funktion kann bei der Durchführung von Bereichsabfragen in Zeitreihendaten nützlich sein.

**Bereichspartitionierung:**

![Diagramm zur Veranschaulichung, wie die Bereichspartitionierung Anforderungen gleichmäßig auf Partitionen verteilt](media/documentdb-sharding/partition-range.png)

Ein Sonderfall der Bereichspartitionierung liegt vor, wenn es sich bei dem Bereich nur um einzigen diskreten Wert handelt. Dies wird zuweilen auch als "Lookuppartitionierung" bezeichnet. Dies wird häufig zur regionsbasierten Partitionierung (z. B. die Partition für Skandinavien umfasst Norwegen, Dänemark und Schweden) oder zur Partitionierung von Mandanten in einer mehrinstanzenfähigen Anwendung verwendet.

## Beispiele 

Sehen Sie sich das [GitHub-Projekt mit DocumentDB-Partitionierungsbeispielen](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning) mit Codeausschnitten an, die erläutern, wie Sie diese "PartitionResolver" verwenden und erweitern, um Ihre eigenen Resolver für spezielle Anwendungsfälle zu implementieren, wie beispielsweise die folgenden:

* Angeben eines frei wählbaren lambda-Ausdrucks für "GetPartitionKey" und Verwenden des Ausdrucks, um zusammengesetzte Partitionierungsschlüssel zu implementieren oder verschiedene Typen von Objekten auf unterschiedliche Weise zu partitionieren.
* Erstellen eines einfachen [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs), der zur Durchführung der Partitionierung eine manuelle Lookuptabelle verwendet. Diese Muster wird häufig für eine Partitionierung verwendet, die auf diskreten Werten wie Region, Mandanten-ID oder Anwendungsname basiert.
* Erstellen eines [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs), der automatisch Sammlungen basierend auf einer Vorlage erstellt. Diese Vorlage definiert ein Benennungsschema, eine Indizierungsrichtlinie und gespeicherte Prozeduren, die für neue Sammlungen registriert werden müssen.
* Erstellen eines schemalosen [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs), der einfach neue Sammlungen erstellt, wenn die vorhandenen Sammlungen voll sind.
* Serialisieren und Deserialisieren des PartitionResolver-Status als JSON, sodass diese für Prozesse und über Vorgänge zum Herunterfahren hinweg freigegeben werden können. Sie können diese in Konfigurationsdateien oder sogar in einer DocumentDB-Sammlung dauerhaft speichern.
* Eine [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs)-Klasse zum dynamischen Hinzufügen und Entfernen von Partitionen zu einer und aus einer basierend auf konsistentem Hashing partitionierten Datenbank. Intern verwendet die Klasse einen [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs), um während einer Migration Lese- und Schreibvorgänge mithilfe eines von vier Modi weiterzuleiten: Lesen aus dem alten (ReadCurrent) oder dem neuen Partitionierungsschema (ReadNext), Zusammenführen der Ergebnisse aus beiden Vorgängen (ReadBoth) oder Nichtverfügbarkeit während der Migration (None).

Diese Beispiele sind Open-Source-basiert, und wir freuen uns, wenn Sie Pullanforderungen mit Beiträgen senden, von denen andere DocumentDB-Entwickler profitieren können. In den [Anleitungen für Beiträge](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) finden Sie Informationen dazu, wie Sie beitragen können.

>[AZURE.NOTE] Die Übertragungsrate bei der Erstellung von Sammlungen ist durch DocumentDB begrenzt, die Durchführung einiger der hier gezeigten Beispielmethoden kann also einige Minuten in Anspruch nehmen.

##Häufig gestellte Fragen
**Warum unterstützt DocumentDB die clientseitige Partitionierung anstelle einer serverseitigen Partitionierung?**

DocumentDB unterstützt die clientseitige Partitionierung aus verschiedenen Gründen:

- Es ist für Entwickler sehr schwierig, das Konzept einer Sammlung zu abstrahieren, ohne eine der drei grundlegenden Aspekte zu vernachlässigen: konsistente Indizierung/Abfragen, hohe Verfügbarkeit und ACID-Transaktionsgarantien. 
- Dokumentendatenbanken erfordern häufig viel Flexibilität hinsichtlich der Definition von Partitionierungsstrategien, eine Voraussetzung, die sich durch einen serverseitigen Ansatz möglicherweise nicht erfüllen lässt. 

**Warum wird die Partitionierung auf anderen Plattformen (Node.js, Java oder Python) nicht unterstützt?**

Die Partitionierungsunterstützung wird, basierend auf dem Feedback der .NET SDK-Kunden, nach und nach auch für andere Plattformen eingeführt.

**Wie füge ich eine Sammlung zu meinem Partitionierungsschema hinzu oder entferne sie daraus?**

Im Beispielprojekt finden Sie in der Implementierung von "DocumentClientHashPartitioningManager" ein Beispiel für die Implementierung der Neupartitionierung.

**Wie speichere ich meine Partitionskonfiguration dauerhaft oder gebe sie für andere Clients frei?**

Sie können die Partitionierungszustände als JSON serialisieren und in Konfigurationsdateien oder sogar innerhalb von DocumentDB-Sammlungen speichern. Ein Beispiel dafür finden Sie in der RunSerializeDeserializeSample-Methode im Beispielprojekt.

**Wie verkette ich verschiedene Partitionierungsverfahren?**

Sie können "PartitionResolver" verketten, indem Sie Ihren eigenen "IPartitionResolver" implementieren, der intern einen oder mehrere vorhandene Resolver verwendet. Ein Beispiel finden Sie im "TransitionHashPartitionResolver" im Beispielprojekt.

##Referenzen
* [Partitionieren von Codebeispielen in GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/Partitioning)
* [Partitionieren von Daten mit DocumentDB-Konzepten](documentdb-partition-data.md)
* [DocumentDB-Sammlungen und -Leistungsstufen](documentdb-performance-levels.md)
* [DocumentDB .NET SDK-Dokumentation in MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET-Beispiele](https://github.com/Azure/azure-documentdb-net)
* [DocumentDB-Einschränkungen](documentdb-limits.md)
* [DocumentDB-Blog mit Leistungstipps](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
 

<!---HONumber=AcomDC_0128_2016-->