<properties
	pageTitle="Erste Schritte mit Azure-Speicher und verbundenen Visual Studio-Diensten (WebJob-Projekte)"
	description="Erfahren Sie etwas über die ersten Schritte mit Azure-Tabellenspeicher in einem Azure WebJobs-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben."
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Erste Schritte mit Azure Storage (Azure WebJob-Projekte)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-webjobs-getting-started-tables.md)
> - [What happened](vs-storage-webjobs-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-webjobs-getting-started-blobs.md)
> - [Queues](vs-storage-webjobs-getting-started-queues.md)
> - [Tables](vs-storage-webjobs-getting-started-tables.md)



## Übersicht

Dieser Artikel enthält C#-Codebeispiele, die zeigen, wie das Azure WebJobs-SDK (Version 1.x) mit dem Azure-Tabellenspeicherdienst verwendet wird. In den Codebeispielen wird Version 1.x des [WebJobs-SDK](websites-dotnet-webjobs-sdk.md) verwendet.

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Weitere Informationen finden Sie unter [Verwenden von Tabellenspeicher aus .NET](storage-dotnet-how-to-use-tables.md/#create-a-table "Verwenden des Tabellenspeichers mit .NET").


Einige der Codeausschnitte zeigen das **Table**-Attribut in Funktionen, die [manuell aufgerufen](vs-storage-webjobs-getting-started-blobs.md#manual) werden, d. h. nicht mit einem der Triggerattribute.

## Hinzufügen von Entitäten zu einer Tabelle

Um einer Tabelle Entitäten hinzuzufügen, verwenden Sie das **Table**-Attribut mit einem **ICollector<T>** oder **IAsyncCollector<T>**-Parameter, wobei **T** das Schema der Entitäten angibt, die Sie hinzufügen möchten. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Tabelle angibt.

Das folgende Codebeispiel fügt einer Tabelle namens *Ingress* **Person**-Entitäten hinzu.

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

In der Regel ist der Typ, den Sie mit **ICollector verwenden**, von **TableEntity** abgeleitet oder implementiert **ITableEntity**, was aber nicht sein muss. Beide der folgenden **Person**-Klassen funktionieren mit dem Code in der vorangehenden **Ingress**-Methode.

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

Wenn Sie direkt mit dem Azure-Speicher-API arbeiten möchten, können Sie der Methodensignatur einen **CloudStorageAccount**-Parameter hinzufügen.

## Überwachung in Echtzeit

Da Dateneingangsfunktionen oft große Datenmengen verarbeiten, bietet das Dashboard des WebJobs-SDK Überwachungsdaten in Echtzeit. Der Abschnitt **Aufrufprotokoll** informiert Sie, ob die Funktion noch ausgeführt wird.

![Eingangsfunktion wird ausgeführt](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Die Seite **Aufrufdetails** meldet den Status der Funktion (Anzahl der geschriebenen Entitäten) während der Ausführung und bietet Ihnen die Möglichkeit, sie abzubrechen.

![Eingangsfunktion wird ausgeführt](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Wenn die Funktion abgeschlossen ist, meldet die Seite **Aufrufdetails** die Anzahl der geschriebenen Zeilen.

![Eingangsfunktion ist abgeschlossen](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## Lesen mehrerer Entitäten aus einer Tabelle

Verwenden Sie zum Lesen einer Tabelle das **Table**-Attribut mit dem **IQueryable<T>**-Parameter, wobei der Typ **T** von **TableEntity** abgeleitet ist oder **ITableEntity** implementiert.

Das folgende Codebeispiel liest und meldet alle Zeilen aus der **Ingress**-Tabelle:

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

### Lesen einer einzelnen Entität aus einer Tabelle

Es gibt einen **Table**-Attributkonstruktor mit zwei zusätzliche Parametern, mit denen Sie den Partitionsschlüssel und Zeilenschlüssel angeben können, wenn Sie eine Bindung mit einer einzelnen Tabellenentität herstellen möchten.

Das folgende Codebeispiel liest eine Tabellenzeile für eine **Person**-Entität anhand der Partitions- und Zeilenschlüsselwerte, die in einer Warteschlange empfangen wurden:

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


Die **Person**-Klasse in diesem Beispiel muss nicht zwingend **ITableEntity** implementieren.

## Verwenden der .NET Storage API für das direkte Arbeiten mit der Tabelle

Sie können auch das **Table**-Attribut mit einem **CloudTable**-Objekt verwenden, um flexibler mit einer Tabelle arbeiten zu können.

Im folgenden Codebeispiel wird ein **CloudTable**-Objekt verwendet, um eine einzelne Entität zur Tabelle *Ingress* hinzuzufügen.

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

Weitere Informationen zur Verwendung des **CloudTable**-Objekts finden Sie unter [Verwenden von Tabellenspeicher aus .NET](../storage-dotnet-how-to-use-tables.md).

## Verwandte in den Artikeln zu Warteschlangen behandelte Themen

Informationen zur Handhabung der durch eine Warteschlangennachricht ausgelösten Tabellenverarbeitung oder zu Szenarien für das WebJobs-SDK, die nicht spezifisch für die Blobverarbeitung sind, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem WebJobs-SDK](vs-storage-webjobs-getting-started-queues.md).



## Nächste Schritte

In diesem Artikel wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Tabellen behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und dem WebJobs-SDK finden Sie unter [Empfohlene Ressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=Sept15_HO4-->