<properties 
	pageTitle="Verwenden von Azure-Tabellenspeicher mit dem WebJobs-SDK" 
	description="Erfahren Sie, wie Sie Azure-Tabellenspeicher mit dem WebJobs-SDK nutzen. Erstellen von Tabellen, Entitäten zu Tabellen hinzufügen und vorhandene Tabellen lesen." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Verwenden von Azure-Tabellenspeicher mit dem WebJobs-SDK

## Übersicht

Diese Anleitung enthält C#-Codebeispiele, die zeigen, wie Azure-Speichertabellen mithilfe des [WebJobs-SDK](websites-dotnet-webjobs-sdk.md) (Version 1.x) gelesen und geschrieben werden.

Im Handbuch wird davon ausgegangen, dass Sie wissen, [wie ein WebJobs-Projekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto verweisen](websites-dotnet-webjobs-sdk-get-started.md).
		
Einige der Codeausschnitte zeigen das `Table`-Attribut in Funktionen, die [manuell aufgerufen](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) werden, d. h. nicht mit einem der Triggerattribute.

## <a id="ingress"></a> Hinzufügen von Entitäten zu einer Tabelle

Um einer Tabelle Entitäten hinzuzufügen, verwenden Sie das `Table`-Attribut mit einem `ICollector<T>`- oder `IAsyncCollector<T>`-Parameter, wobei `T` das Schema der Entitäten angibt, die Sie hinzufügen möchten. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Tabelle angibt.

Das folgende Codebeispiel fügt `Person`-Entitäten zu einer Tabelle namens *Ingress* hinzu.

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

In der Regel ist der Typ, den Sie mit `ICollector` verwenden, von `TableEntity` abgeleitet oder implementiert `ITableEntity`, was aber nicht sein muss. Beide der folgenden `Person`-Klassen funktionieren mit dem Code in der vorangehenden `Ingress`-Methode.

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

Wenn Sie direkt mit der Azure Storage-API arbeiten möchten, können Sie der Methodensignatur einen `CloudStorageAccount`-Parameter hinzufügen.

## <a id="monitor"></a> Überwachung in Echtzeit

Da Dateneingangsfunktionen oft große Datenmengen verarbeiten, bietet das Dashboard des WebJobs-SDK Überwachungsdaten in Echtzeit. Der Abschnitt **Aufrufprotokoll** informiert Sie, ob die Funktion noch ausgeführt wird.

![Eingangsfunktion wird ausgeführt](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

Die Seite **Aufrufdetails** meldet den Status der Funktion (Anzahl der geschriebenen Entitäten) während der Ausführung und bietet Ihnen die Möglichkeit, sie abzubrechen.

![Eingangsfunktion wird ausgeführt](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Wenn die Funktion abgeschlossen ist, meldet die Seite **Aufrufdetails** die Anzahl der geschriebenen Zeilen.

![Eingangsfunktion ist abgeschlossen](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Lesen mehrerer Entitäten aus einer Tabelle

Verwenden Sie zum Lesen einer Tabelle das `Table`-Attribut mit dem `IQueryable<T>`-Parameter, wobei der Typ `T` von `TableEntity` abgeleitet ist oder `ITableEntity` implementiert.

Das folgende Codebeispiel liest und meldet alle Zeilen aus der `Ingress`-Tabelle:
 
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

Es gibt einen `Table`-Attributkonstruktor mit zwei zusätzliche Parametern, mit denen Sie den Partitionsschlüssel und Zeilenschlüssel angeben können, wenn Sie eine Bindung mit einer einzelnen Tabellenentität herstellen möchten.

Das folgende Codebeispiel liest eine Tabellenzeile für eine `Person`-Entität anhand der Partitions- und Zeilenschlüsselwerte, die in einer Warteschlange empfangen wurden:

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


Die `Person`-Klasse in diesem Beispiel muss nicht zwingend `ITableEntity` implementieren.

## <a id="storageapi"></a> Verwenden der .NET Storage-API für das direkte Arbeiten mit der Tabelle

Sie können auch das `Table`-Attribut mit einem `CloudTable`-Objekt verwenden, um flexibler mit einer Tabelle arbeiten zu können.

Im folgenden Codebeispiel wird ein `CloudTable`-Objekt verwendet, um eine einzelne Entität zur Tabelle *Ingress* hinzuzufügen.
 
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

Weitere Informationen zur Verwendung des `CloudTable`-Objekts finden Sie unter [Verwenden von Tabellenspeicher aus .NET](../storage-dotnet-how-to-use-tables.md).

## <a id="queues"></a>Verwandte Themen, die im Artikel zu Warteschlangen behandelt werden

Informationen zur Handhabung der durch eine Warteschlangennachricht ausgelösten Tabellenverarbeitung oder zu Szenarien für das WebJobs-SDK, die nicht spezifisch für die Blobverarbeitung sind, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von WebJobs-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Tabellen behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und dem WebJobs-SDK finden Sie unter [Empfohlene Ressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=Oct15_HO1-->