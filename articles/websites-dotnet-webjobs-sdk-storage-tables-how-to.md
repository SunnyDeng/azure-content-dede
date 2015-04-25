<properties 
	pageTitle="Verwenden von Azure-Tabellenspeicher mit dem Webaufträge-SDK" 
	description="Erfahren Sie, wie Sie Azure-Tabellenspeicher mit dem Webaufträge-SDK nutzen. Erstellen von Tabellen, Entitäten zu Tabellen hinzufügen und vorhandene Tabellen lesen." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Verwenden von Azure-Tabellenspeicher mit dem Webaufträge-SDK

Diese Anleitung enthält C#-Codebeispiele, die zeigen, wie Sie Azure-Speichertabellen mithilfe des [Webaufträge-SDK](websites-dotnet-webjobs-sdk.md),Version 1.x, gelesen und geschrieben werden.

In der Anleitung wird davon ausgegangen, dass Sie wissen, [wie ein Webauftragsprojekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto zeigen](websites-dotnet-webjobs-sdk-get-started.md)..
		
Einige der Codeausschnitte zeigen das  `Table`-Attribut in Funktionen, die  [manuell aufgerufen werden](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual), d. h. nicht mithilfe eines der Auslöserattribute. 

## Inhaltsverzeichnis

-   [Hinzufügen von Entitäten zu einer Tabelle](#ingress)
-   [Überwachung in Echtzeit](#monitor)
-   [Lesen mehrerer Entitäten aus einer Tabelle](#multiple)
-   [Lesen einer einzelnen Entität aus einer Tabelle](#readone)
-   [Verwenden der .NET Storage API für das direkte Arbeiten mit der Tabelle](#readone)
-   [Verwandte in den Artikeln zu Warteschlangen behandelte Themen](#queues)
-   [Nächste Schritte](#nextsteps)

## <a id="ingress"></a>Hinzufügen von Entitäten zu einer Tabelle

Verwenden zum Hinzufügen von Entitäten zu einer Tabelle das  `Table`-Attribut mit einem  `ICollector<T>`- oder  `IAsyncCollector<T>`-Parameter, wobei das Schema der Entitäten angibt, die Sie hinzufügen möchten. ( `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table. )

(The following code sample adds `Person`)Das folgende Codebespiel fügt Entitäten der Tabelle  *Ingress* hinzu.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

In der Regel ist der Typ,den Sie mit  `ICollector` verwenden,  von  `TableEntity` abgeleitet oder implementiert `ITableEntity`, was aber nicht sein muss. Beide der folgenden  `Person`-Klassen funktionieren mit dem Code in der vorangehenden  `Ingress`-Methode.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Wenn Sie direkt mit dem Azure-Speicher-API arbeiten möchten, können Sie der Methodensignatur einen  `CloudStorageAccount`-Parameter hinzufügen.

## <a id="monitor"></a> Überwachung in Echtzeit

Da Dateneingangsfunktionen oft große Datenmengen verarbeiten, bietet das Dashboard des Webaufträge-SDK Überwachungsdaten in Echtzeit. Der Abschnitt **Aufrufprotokoll** informiert Sie, ob die Funktion noch ausgeführt wird.

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

Die Seite **Aufrufdetails** meldet den Status der Funktion (Anzahl der geschriebenen Entitäten ) während der Ausführung und bietet Ihnen die Möglichkeit, sie abzubrechen. 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Wenn die Funktion abgeschlossen ist, meldet die Seite **Aufrufdetails** die Anzahl der geschriebenen Zeilen.

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Lesen mehrerer Entitäten aus einer Tabelle

Verwenden Sie zum Lesen einer Tabelle das  `Table`-Attribut mit dem  `IQueryable<T>`-Parameter, wobei der Typ von  `T` derives from `TableEntity` abgeleitet ist oder  `ITableEntity` implementiert wird.

Das folgende Codebeispiel liest und meldet alle Zeilen aus der Tabelle  `Ingress`:
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a> Lesen einer einzelnen Entität aus einer Tabelle

Es gibt einen  `Table`-Attributkonstruktor mit zwei zusätzliche Parametern, mit denen Sie den Partitionsschlüssel und Zeilenschlüssel angeben können, wenn Sie eine Bindung mit einer einzelnen Tabellenentität herstellen möchten.

Das folgende Codebeispiel liest eine Tabellenzeile für eine  `Person`-Entität anhand der Partition- und Zeilenschlüsselwerte, die in einer Warteschlange empfangen wurden:  

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


Die  `Person`-Klasse in diesem Beispiel muss  `ITableEntity` nicht implementieren.

## <a id="storageapi"></a> Verwenden der .NET Storage API für das direkte Arbeiten mit der Tabelle

Sie können auch das  `Table` -Attribut mit einem  `CloudTable`-Objekt verwenden, um flexibler mit einer Tabelle arbeiten zu können.

Im folgenden Codebeispiel wird ein  `CloudTable`-Objekt verwendet, um eine einzelne Entität zum zur Tabelle  *Ingress* hinzuzufügen. 
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Weitere Informationen zur Verwendung des  `CloudTable`-Objekts finden Sie unter [Verwenden von Tabellenspeicher aus .NET](storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>Verwandte in den Artikeln zu Warteschlangen behandelte Themen

Informationen zur Handhabung der Tabellenverarbeitung, die durch eine Warteschlangennachricht ausgelöst wird, oder zu Szenarien für das Webaufträge-SDK, die nicht spezifisch für die Tabellenverarbeitung sind, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). .

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für Konstruktorparameter im Webaufträge-SDK im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a>Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Tabellen behandelt werden. Weitere Informationen zur Verwendung von Azure-Webaufträgen und dem Webaufträge-SDK finden Sie unter [Empfohlene Ressourcen für Azure-Webaufträge](http://go.microsoft.com/fwlink/?linkid=390226).





<!--HONumber=42-->
